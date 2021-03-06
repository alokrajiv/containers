---

copyright:
  years: 2014, 2018
lastupdated: "2018-10-25"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:download: .download}


# Rimozione dell'archiviazione persistente da un cluster
{: #cleanup}

Quando configuri l'archiviazione persistente nel tuo cluster, hai tre componenti principali: l'attestazione del volume persistente (o PVC, persistent volume claim) Kubernetes che richiede l'archiviazione, il volume persistente (o PV, persistent volume) montato in un pod e descritto nella PVC e l'istanza dell'infrastruttura IBM Cloud (SoftLayer), come l'archiviazione blocchi o file NFS. A seconda di come ne hai eseguito la creazione, potresti dover eliminare tutti i tre separatamente.
{:shortdesc}

## Ripulitura dell'archiviazione persistente
{: #storage_remove}

Descrizione delle tue opzioni di eliminazione:

**Ho eliminato il mio cluster. Devo eliminare qualcos'altro per rimuovere l'archiviazione persistente?**</br>
Dipende. Quando elimini un cluster, la PVC e il PV vengono eliminati. Tuttavia, scegli se rimuovere l'istanza di archiviazione associata nell'infrastruttura IBM Cloud (SoftLayer). Se scegli di non rimuoverla, l'istanza di archiviazione permane. Tuttavia, se hai eliminato il tuo cluster in uno stato non integro, l'archiviazione potrebbe permanere anche se hai scelto di rimuoverla. Attieniti alle istruzioni, in particolare il passo per [eliminare la tua istanza di archiviazione](#sl_delete_storage) nell'infrastruttura IBM Cloud (SoftLayer).

**Posso eliminare la PVC per rimuovere tutta la mia archiviazione?**</br>
In alcuni casi. Se [crei l'archiviazione persistente in modo dinamico](cs_storage_basics.html#dynamic_provisioning) e selezioni una classe di archiviazione senza `retain` nel suo nome, quando elimini la PVC, vengono eliminati anche il PV e l'istanza di archiviazione dell'infrastruttura IBM Cloud (SoftLayer).

In tutti gli altri casi, attieniti alle istruzioni per controllare lo stato della PVC, del PV e del dispositivo di archiviazione fisico ed eliminali separatamente, se necessario. 

**Continuo a incorrere in addebiti per l'archiviazione dopo che l'ho eliminata?**</br>
Dipende da cosa elimini e dal tipo di fatturazione. Se elimini la PVC e il PV ma non l'istanza nel tuo account dell'infrastruttura IBM Cloud (SoftLayer), tale istanza permane e incorri in addebiti per essa. Devi eliminare tutto, per evitare addebiti. Inoltre, quando specifichi il tipo di fatturazione (`billingType`) nella PVC, puoi scegliere `hourly` o `monthly`. Se hai scelto `monthly`, la tua istanza viene fatturata mensilmente. Quando elimini l'istanza, ti viene addebitato il resto del mese.


**Importante**:
* Quando ripulisci l'archiviazione persistente, elimini tutti i dati in essa archiviati. Se hai bisogno di una copia dei dati, crea un backup per l'[archiviazione file](cs_storage_file.html#backup_restore) o l'[archiviazione blocchi](cs_storage_block.html#backup_restore).
* Se utilizzi un account {{site.data.keyword.Bluemix_dedicated}}, devi richiedere l'eliminazione del volume [aprendo un ticket di supporto](/docs/get-support/howtogetsupport.html#getting-customer-support).

Prima di iniziare: [accedi al tuo account. Specifica la regione appropriata e, se applicabile, il gruppo di risorse. Imposta il contesto per il tuo cluster](cs_cli_install.html#cs_cli_configure).

Per ripulire i dati persistenti:

1.  Elenca le PVC nel tuo cluster e prendi nota del **NAME** della PVC, della **STORAGECLASS** e del nome del PV associato alla PVC e visualizzato come **VOLUME**.
    ```
    kubectl get pvc
    ```
    {: pre}

    Output di esempio:
    ```
    NAME                  STATUS    VOLUME                                     CAPACITY   ACCESSMODES   STORAGECLASS            AGE
    claim1-block-bronze   Bound     pvc-06886b77-102b-11e8-968a-f6612bb731fb   20Gi       RWO           ibmc-block-bronze       78d
    claim-file-bronze     Bound     pvc-457a2b96-fafc-11e7-8ff9-b6c8f770356c   4Gi        RWX           ibmc-file-bronze-retain 105d
    claim-file-silve      Bound     pvc-1efef0ba-0c48-11e8-968a-f6612bb731fb   24Gi       RWX           ibmc-file-silver        83d
    ```
    {: screen}
    
2. Esamina **ReclaimPolicy** e **billingType** per la classe di archiviazione. 
   ```
   kubectl describe storageclass <storageclass_name>
   ```
   {: pre}
   
   Se la politica di riacquisizione indica `Delete`, il tuo PV e l'archiviazione fisica vengono rimossi quando rimuovi la PVC. Se la politica di riacquisizione indica `Retain`, o se hai eseguito il provisioning della tua archiviazione senza una classe di archiviazione, il tuo PV e l'archiviazione fisica non vengono rimossi quando rimuovi la PVC. Devi rimuovere la PVC, il PV e l'archiviazione fisica separatamente. 
   
   **Importante:** se l'addebito a tuo carico per la tua archiviazione è a base mensile, ti verrà comunque addebitato l'intero mese, anche se rimuovi l'archiviazione prima della fine del ciclo di fatturazione. 
   
3. Rimuovi gli eventuali pod che montano la PVC. 
   1. Elenca i pod che montano la PVC. 
      ```
      kubectl get pods --all-namespaces -o=jsonpath='{range .items[*]}{"\n"}{.metadata.name}{":\t"}{range .spec.volumes[*]}{.persistentVolumeClaim.claimName}{" "}{end}{end}' | grep "<pvc_name>"
      ```
      {: pre}
    
      Output di esempio:
      ```
      blockdepl-12345-prz7b:	claim1-block-bronze  
      ```
      {: screen}
    
      Se non viene restituito alcun pod nel tuo output della CLI, non hai alcun pod che utilizza la PVC. 
    
   2. Rimuovi il pod che utilizza la PVC. Se il pod fa parte di una distribuzione, rimuovi la distribuzione. 
      ```
      kubectl delete pod <pod_name>
      ```
      {: pre}
      
   3. Verifica che il pod venga rimosso. 
      ```
      kubectl get pods
      ```
      {: pre}
   
4. Rimuovi la PVC. 
   ```
   kubectl delete pvc <pvc_name>
   ```
   {: pre}
   
5. Esamina lo stato del tuo PV. Utilizza il nome del PV che hai richiamato in precedenza come **VOLUME**. 
   ```
   kubectl get pv <pv_name>
   ```
   {: pre}
   
   Quando rimuovi la PVC, il PV ad essa associato viene rilasciato. A seconda di come hai eseguito il provisioning della tua archiviazione, il tuo PV passa a uno stato di `Deleting`, se il PV viene eliminato automaticamente, oppure a uno stato di `Released`, se devi eliminarlo manualmente. 
   
   **Nota:**: per i PV eliminati automaticamente, lo stato potrebbe brevemente indicare `Released` prima che venga eliminato. Riesegui il comando dopo qualche minuto per appurare se il PV viene rimosso.
   
6. Se il tuo PV non viene eliminato, rimuovilo manualmente. 
   ```
   kubectl delete pv <pv_name>
   ```
   {: pre}
   
7. Verifica che il PV venga rimosso. 
   ```
   kubectl get pv
   ```
   {: pre}
   
8. {: #sl_delete_storage}Elenca l'istanza di archiviazione fisica a cui puntava il tuo PV e prendi nota del suo **id**. 

   **Archiviazione file:** 
   ```
   ibmcloud sl file volume-list --columns id  --columns notes | grep <pv_name>
   ```
   {: pre}
   **Archiviazione blocchi:**
   ```
   ibmcloud sl block volume-list --columns id --columns notes | grep <pv_name>
   ```
   {: pre}
     
   Se hai rimosso il tuo cluster e non riesci a richiamare il nome del PV, sostituisci `grep <pv_name>` con `grep <cluster_id>` per elencare tutti i dispositivi di archiviazione associati al tuo cluster. 
   {: tip}
     
   Output di esempio: 
   ```
   id         notes
   12345678   ibmcloud-block-storage-plugin-7566ccb8d-44nff:us-south:aa1a11a1a11b2b2bb22b22222c3c3333:Performance:mypvc:pvc-457a2b96-fafc-11e7-8ff9-b6c8f770356z 
   ```
   {: screen}
     
   Descrizione delle informazioni del campo **Note**.
   *  **`:`**: un carattere due punti (`:`) separa le informazioni.
   *  **` ibmcloud-block-storage-plugin-7566ccb8d-44nff`**: il plug-in di archiviazione utilizzato dal cluster.
   *  **`us-south`**: la regione in cui si trova il tuo cluster.
   *  **`aa1a11a1a11b2b2bb22b22222c3c3333`**: l'ID cluster associato all'istanza di archiviazione.
   *  **`Performance`**: il tipo di archiviazione file o blocchi, `Endurance` o `Performance`.
   *  **`mypvc`**: il nome della PVC associata all'istanza di archiviazione.
   *  **`pvc-457a2b96-fafc-11e7-8ff9-b6c8f770356z`**: il PV associato all'istanza di archiviazione.
     
9. Rimuovi l'istanza di archiviazione fisica. 
   
   **Archiviazione file:**
   ```
   ibmcloud sl file volume-cancel <filestorage_id>
   ```
   {: pre}
   
   **Archiviazione blocchi:**
   ```
   ibmcloud sl block volume-cancel <blockstorage_id>
   ```
   {: pre}
     
9. Verifica che l'istanza di archiviazione fisica venga rimossa. **Nota**: il completamento del processo di eliminazione potrebbe richiedere alcuni giorni. 

   **Archiviazione file:** 
   ```
   ibmcloud sl file volume-list 
   ```
   {: pre}
   **Archiviazione blocchi:**
   ```
   ibmcloud sl block volume-list 
   ```
   {: pre}
 

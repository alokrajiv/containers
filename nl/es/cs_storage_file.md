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




# Almacenamiento de datos en IBM File Storage for IBM Cloud
{: #file_storage}


## Cómo decidir la configuración del almacenamiento de archivos
{: #predefined_storageclass}

{{site.data.keyword.containerlong}} proporciona clases de almacenamiento predefinidas para el almacenamiento de archivos que puede utilizar para suministrar almacenamiento de archivos con una configuración específica.
{: shortdesc}

Cada clase de almacenamiento especifica el tipo de almacenamiento de archivos que suministra, incluidos tamaño disponible, IOPS, sistema de archivos y política de retención.  

**Importante:** asegúrese de elegir la configuración de almacenamiento cuidadosamente para tener suficiente capacidad para almacenar los datos. Después de suministrar un tipo específico de almacenamiento utilizando una clase de almacenamiento, no puede cambiar tamaño, tipo, IOPS ni política de retención del dispositivo de almacenamiento. Si necesita más almacenamiento o almacenamiento con otra configuración, debe [crear una nueva instancia de almacenamiento y copiar los datos](cs_storage_basics.html#update_storageclass) de la instancia de almacenamiento antigua en la nueva.

1. Obtenga una lista de las clases de almacenamiento disponibles en {{site.data.keyword.containerlong}}.
    ```
    kubectl get storageclasses | grep file
    ```
    {: pre}

    Salida de ejemplo:
    ```
    $ kubectl get storageclasses
    NAME                         TYPE
    ibmc-file-bronze (default)   ibm.io/ibmc-file
    ibmc-file-custom             ibm.io/ibmc-file
    ibmc-file-gold               ibm.io/ibmc-file
    ibmc-file-retain-bronze      ibm.io/ibmc-file
    ibmc-file-retain-custom      ibm.io/ibmc-file
    ibmc-file-retain-gold        ibm.io/ibmc-file
    ibmc-file-retain-silver      ibm.io/ibmc-file
    ibmc-file-silver             ibm.io/ibmc-file
    ```
    {: screen}

2. Revise la configuración de una clase de almacenamiento.
   ```
   kubectl describe storageclass <storageclass_name>
   ```
   {: pre}

   Para obtener más información sobre cada clase de almacenamiento, consulte la [referencia de clases de almacenamiento](#storageclass_reference). Si no encuentra lo que está buscando, considere la posibilidad de crear su propia clase de almacenamiento personalizada. Para empezar, compruebe los [ejemplos de clase de almacenamiento personalizada](#custom_storageclass).
   {: tip}

3. Elija el tipo de almacenamiento de archivos que desea suministrar.
   - **Clases de almacenamiento de bronce, plata y oro:** estas clases de almacenamiento suministran [almacenamiento resistente ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo") ](https://knowledgelayer.softlayer.com/topic/endurance-storage). El almacenamiento resistente le permite elegir el tamaño del almacenamiento en gigabytes en los niveles de IOPS predefinidos.
   - **Clase de almacenamiento personalizada:** esta clase de almacenamiento contiene [almacenamiento de rendimiento ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo") ](https://knowledgelayer.softlayer.com/topic/performance-storage). Con el almacenamiento de rendimiento, tiene más control sobre el tamaño del almacenamiento y de IOPS.

4. Elija el tamaño e IOPS para el almacenamiento de archivos. El tamaño y el número de IOPS definen el número total de IOPS (operaciones de entrada/salida por segundo), lo que sirve como indicador de la rapidez del almacenamiento. Cuantas más IOPS tenga el almacenamiento, más rápido se procesarán las operaciones de entrada y salida.
   - **Clases de almacenamiento de bronce, plata y oro:** estas clases de almacenamiento se suministran con un número fijo de IOPS por gigabytes y se suministran en discos duros SSD. El número total de IOPS depende del tamaño del almacenamiento que elija. Puede seleccionar cualquier número entero de gigabytes comprendido dentro del rango de tamaño permitido, como por ejemplo 20 Gi, 256 Gi o 11854 Gi. Para determinar el número total de IOPS, debe multiplicar IOPS por el tamaño seleccionado. Por ejemplo, si selecciona un tamaño de almacenamiento de archivos de 1000 Gi en la clase de almacenamiento de plata que se suministra con 4 IOPS por GB, el almacenamiento tendrá un total de 4000 IOPS.
     <table>
         <caption>Tabla de IOPS y rangos de tamaño de clase de almacenamiento por gigabyte</caption>
         <thead>
         <th>Clase de almacenamiento</th>
         <th>IOPS por gigabyte</th>
         <th>Rango de tamaño en gigabytes</th>
         </thead>
         <tbody>
         <tr>
         <td>Bronce</td>
         <td>2 IOPS/GB</td>
         <td>20-12000 Gi</td>
         </tr>
         <tr>
         <td>Plata</td>
         <td>4 IOPS/GB</td>
         <td>20-12000 Gi</td>
         </tr>
         <tr>
         <td>Oro</td>
         <td>10 IOPS/GB</td>
         <td>20-4000 Gi</td>
         </tr>
         </tbody></table>
   - **Clase de almacenamiento personalizada:** cuando elige esta clase de almacenamiento, tiene más control sobre el tamaño y el número de IOPS que desea. Para el tamaño, puede seleccionar cualquier número entero de gigabytes comprendido dentro del rango de tamaño permitido. El tamaño que elija determina el rango de IOPS que tendrá a su disponibilidad. Puede elegir un tamaño de IOPS que sea un múltiplo de 100 y que esté en el rango especificado. Las IOPS que elige son estáticas y no se escalan con el tamaño del almacenamiento. Por ejemplo, si elige 40 Gi con 100 IOPS, el número total de IOPS seguirá siendo 100. </br></br> La proporción entre IOPS y gigabytes también determina el tipo de disco duro que se suministra. Por ejemplo, si tiene 500 Gi a 100 IOPS, la proporción entre IOPS y gigabyte será de 0,2. Si la proporción es menor o igual a 0,3, el almacenamiento se suministra en discos duros SATA. Si la proporción es mayor que 0,3, el almacenamiento se suministran en los discos duros SSD.  
     <table>
         <caption>Tabla de IOPS y rangos de tamaño de clase de almacenamiento personalizadas</caption>
         <thead>
         <th>Rango de tamaño en gigabytes</th>
         <th>Rango de IOPS en múltiplos de 100</th>
         </thead>
         <tbody>
         <tr>
         <td>20-39 Gi</td>
         <td>100-1000 IOPS</td>
         </tr>
         <tr>
         <td>40-79 Gi</td>
         <td>100-2000 IOPS</td>
         </tr>
         <tr>
         <td>80-99 Gi</td>
         <td>100-4000 IOPS</td>
         </tr>
         <tr>
         <td>100-499 Gi</td>
         <td>100-6000 IOPS</td>
         </tr>
         <tr>
         <td>500-999 Gi</td>
         <td>100-10000 IOPS</td>
         </tr>
         <tr>
         <td>1000-1999 Gi</td>
         <td>100-20000 IOPS</td>
         </tr>
         <tr>
         <td>2000-2999 Gi</td>
         <td>200-40000 IOPS</td>
         </tr>
         <tr>
         <td>3000-3999 Gi</td>
         <td>200-48000 IOPS</td>
         </tr>
         <tr>
         <td>4000-7999 Gi</td>
         <td>300-48000 IOPS</td>
         </tr>
         <tr>
         <td>8000-9999 Gi</td>
         <td>500-48000 IOPS</td>
         </tr>
         <tr>
         <td>10000-12000 Gi</td>
         <td>1000-48000 IOPS</td>
         </tr>
         </tbody></table>

5. Decida si desea conservar los datos después de que se suprima el clúster o la reclamación de volumen persistente (PVC).
   - Si desea conservar los datos, seleccione la clase de almacenamiento `retain`. Cuando se suprime la PVC, únicamente ésta se suprime. El PV, el dispositivo de almacenamiento físico de la cuenta de infraestructura de IBM Cloud (SoftLayer) y los datos seguirán existiendo. Para reclamar el almacenamiento y volverlo a utilizar en el clúster, debe eliminar el PV y seguir los pasos de [utilización de almacenamiento de archivos existente](#existing_file).
   - Si desea que el PV, los datos y el dispositivo físico de almacenamiento de archivos se supriman cuando suprima la PVC, elija una clase de almacenamiento sin la opción `retain`. **Nota**: si tiene una cuenta dedicada, seleccione una clase de almacenamiento sin la opción `retain` para evitar volúmenes huérfanos en la infraestructura de IBM Cloud (SoftLayer).

6. Decida si desea que se le facture por horas o por meses. Consulte las [tarifas ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo")](https://www.ibm.com/cloud/file-storage/pricing) para obtener más información. De forma predeterminada, todos los dispositivos de almacenamiento de archivos se suministran con un tipo de facturación por hora.
   **Nota: ** si selecciona el tipo de facturación mensual, cuando elimine el almacenamiento persistente seguirá pagando el cargo mensual por el mismo, aunque solo lo haya utilizado durante un breve periodo de tiempo.

<br />



## Adición de almacenamiento de archivos a apps
{: #add_file}

Cree una reclamación de volumen persistente (PVC) para [suministrar de forma dinámica](cs_storage_basics.html#dynamic_provisioning) almacenamiento de archivos para el clúster. El suministro dinámico crea automáticamente el volumen persistente (PV) adecuado y solicita el dispositivo de almacenamiento físico en la cuenta de infraestructura de IBM Cloud (SoftLayer).
{:shortdesc}

Antes de empezar:
- Si tiene un cortafuegos, [permita el acceso de salida](cs_firewall.html#pvc) para los rangos de IP de la infraestructura de IBM Cloud (SoftLayer) de las zonas en las que están en los clústeres, de modo que pueda crear las PVC.
- [Decida si desea utilizar una clase de almacenamiento predefinida](#predefined_storageclass) o crear una [clase de almacenamiento personalizada](#custom_storageclass).

  **Nota:** si tiene un clúster multizona, la zona en la que se suministra el almacenamiento se selecciona en una iteración cíclica para equilibrar las solicitudes de volumen de forma uniforme entre todas las zonas. Si desea especificar la zona para el almacenamiento, cree en primer lugar una [clase de almacenamiento personalizada](#multizone_yaml). A continuación, siga los pasos de este tema para suministrar almacenamiento utilizando la clase de almacenamiento personalizada.

¿Desea desplegar almacenamiento de archivos en un conjunto con estado? Consulte [Utilización del almacenamiento de archivos en un conjunto con estado](#file_statefulset) para obtener más información.
{: tip}

Para añadir almacenamiento de archivos:

1.  Cree un archivo de configuración para definir su reclamación de volumen persistente (PVC) y guarde la configuración como archivo `.yaml`.

    - **Ejemplo de clases de almacenamiento bronce, plata y oro**:
       El siguiente archivo `.yaml` crea una reclamación que se denomina `mypvc` de la clase de almacenamiento `"ibmc-file-silver"`, que se factura con el valor `"monthly"`, con un tamaño de gigabyte de `24Gi`.

       ```
       apiVersion: v1
       kind: PersistentVolumeClaim
       metadata:
         name: mypvc
         annotations:
           volume.beta.kubernetes.io/storage-class: "ibmc-file-silver"
         labels:
           billingType: "monthly"
       spec:
         accessModes:
           - ReadWriteMany
         resources:
           requests:
             storage: 24Gi
       ```
       {: codeblock}

    -  **Ejemplo de utilización de una clase de almacenamiento personalizada**:
       El siguiente archivo `.yaml` crea una reclamación denominada `mypvc` de la clase de almacenamiento `ibmc-file-retain-custom`, con una facturación de tipo `"hourly"`, un tamaño en gigabytes de `45Gi` y `"300"` como número de IOPS.

       ```
       apiVersion: v1
       kind: PersistentVolumeClaim
       metadata:
         name: mypvc
         annotations:
           volume.beta.kubernetes.io/storage-class: "ibmc-file-retain-custom"
         labels:
           billingType: "hourly"
       spec:
         accessModes:
           - ReadWriteMany
         resources:
           requests:
             storage: 45Gi
             iops: "300"
       ```
       {: codeblock}

       <table>
       <caption>Visión general de los componentes del archivo YAML</caption>
       <thead>
       <th colspan=2><img src="images/idea.png" alt="Icono Idea"/> Visión general de los componentes del archivo YAML</th>
       </thead>
       <tbody>
       <tr>
       <td><code>metadata.name</code></td>
       <td>Escriba el nombre de la PVC.</td>
       </tr>
       <tr>
       <td><code>metadata.annotations</code></td>
       <td>El nombre de la clase de almacenamiento que desea utilizar para suministrar almacenamiento de archivos. </br> Si no especifica ninguna clase de almacenamiento, el PV se crea con la clase de almacenamiento predeterminada <code>ibmc-file-bronze</code><p>**Sugerencia:** Si desea cambiar la clase de almacenamiento predeterminada, ejecute <code>kubectl patch storageclass &lt;storageclass&gt; -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'</code> y sustituya <code>&lt;storageclass&gt;</code> con el nombre de la clase de almacenamiento.</p></td>
       </tr>
       <tr>
         <td><code>metadata.labels.billingType</code></td>
          <td>Especifique la frecuencia con la que desea que se calcule la factura de almacenamiento, los valores son "monthly" o "hourly". Si no especifica ningún tipo de facturación, el almacenamiento se suministre con el tipo de facturación por hora. </td>
       </tr>
       <tr>
       <td><code>spec.accessMode</code></td>
       <td>Especifique una de las opciones siguientes: <ul><li><strong>ReadWriteMany: </strong> la PVC se puede montar mediante varios pods. Todos los pods pueden leer y escribir en el volumen. </li><li><strong>ReadOnlyMany: </strong> la PVC se puede montar mediante varios pods. Todos los pods tienen acceso de sólo lectura. <li><strong>ReadWriteOnce: </strong> la PVC se puede montar mediante un solo pod. Este pod puede leer y escribir en el volumen. </li></ul></td>
       </tr>
       <tr>
       <td><code>spec.resources.requests.storage</code></td>
       <td>Indique el tamaño del almacenamiento de archivos, en gigabytes (Gi). </br></br><strong>Nota:</strong> una vez suministrado el almacenamiento, no puede cambiar el tamaño del almacenamiento de archivos. Asegúrese de especificar un tamaño que coincida con la cantidad de datos que desea almacenar. </td>
       </tr>
       <tr>
       <td><code>spec.resources.requests.iops</code></td>
       <td>Esta opción solo está disponible para las clases de almacenamiento personalizadas (`ibmc-file-custom/ibmc-file-retain-custom`). Especifique el IOPS total para el almacenamiento seleccionando un múltiplo de 100 dentro del rango permitido. Si elige un IOPS distinto del que aparece en la lista, el IOPS se redondea.</td>
       </tr>
       </tbody></table>

    Si desea utilizar una clase de almacenamiento personalizada, cree su PVC con el nombre de clase de almacenamiento correspondiente, unas IOPS válidas y un tamaño.   
    {: tip}

2.  Cree la PVC.

    ```
    kubectl apply -f mypvc.yaml
    ```
    {: pre}

3.  Verifique que la PVC se ha creado y se ha vinculado al PV. Este proceso puede tardar unos minutos.

    ```
    kubectl describe pvc mypvc
    ```
    {: pre}

    Salida de ejemplo:

    ```
    Name: mypvc
    Namespace: default
    StorageClass: ""
    Status:		Bound
    Volume:		pvc-0d787071-3a67-11e7-aafc-eef80dd2dea2
    Labels:		<none>
    Capacity:	20Gi
    Access Modes:	RWX
    Events:
      FirstSeen	LastSeen	Count	From								SubObjectPath	Type		Reason			Message
      ---------	--------	-----	----								-------------	--------	------			-------
      3m		3m		1	{ibm.io/ibmc-file 31898035-3011-11e7-a6a4-7a08779efd33 }			Normal		Provisioning		External provisioner is provisioning volume for claim "default/my-persistent-volume-claim"
      3m		1m		10	{persistentvolume-controller }							Normal		ExternalProvisioning	cannot find provisioner "ibm.io/ibmc-file", expecting that a volume for the claim is provisioned either manually or via external software
      1m		1m		1	{ibm.io/ibmc-file 31898035-3011-11e7-a6a4-7a08779efd33 }			Normal		ProvisioningSucceeded	Successfully provisioned volume pvc-0d787071-3a67-11e7-aafc-eef80dd2dea2

    ```
    {: screen}

4.  {: #app_volume_mount}Para montar el almacenamiento en el despliegue, cree un archivo `.yaml` de configuración y especifique la PVC que enlaza el PV.

    Si tiene una app que necesita que un usuario que no sea root escriba en el almacenamiento persistente, o una app que necesita que la vía de acceso de montaje sea propiedad del usuario root, consulte los apartados [Adición de acceso de usuarios no root al almacenamiento de archivos NFS](cs_troubleshoot_storage.html#nonroot) o [Habilitación del permiso root para almacenamiento de archivos NFS](cs_troubleshoot_storage.html#nonroot).
    {: tip}

    ```
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: <deployment_name>
      labels:
        app: <deployment_label>
    spec:
      selector:
        matchLabels:
          app: <app_name>
      template:
        metadata:
          labels:
            app: <app_name>
        spec:
          containers:
          - image: <image_name>
            name: <container_name>
            volumeMounts:
            - name: <volume_name>
              mountPath: /<file_path>
          volumes:
          - name: <volume_name>
            persistentVolumeClaim:
              claimName: <pvc_name>
    ```
    {: codeblock}

    <table>
    <caption>Visión general de los componentes del archivo YAML</caption>
    <thead>
    <th colspan=2><img src="images/idea.png" alt="Icono Idea"/> Visión general de los componentes del archivo YAML</th>
    </thead>
    <tbody>
        <tr>
    <td><code>metadata.labels.app</code></td>
    <td>Una etiqueta para el despliegue.</td>
      </tr>
      <tr>
        <td><code>spec.selector.matchLabels.app</code> <br/> <code>spec.template.metadata.labels.app</code></td>
        <td>Una etiqueta para la app.</td>
      </tr>
    <tr>
    <td><code>template.metadata.labels.app</code></td>
    <td>Una etiqueta para el despliegue.</td>
      </tr>
    <tr>
    <td><code>spec.containers.image</code></td>
    <td>El nombre del imagen que desea utilizar. Para ver una lista de todas las imágenes disponibles en su cuenta de {{site.data.keyword.registryshort_notm}}, ejecute `ibmcloud cr image-list`.</td>
    </tr>
    <tr>
    <td><code>spec.containers.name</code></td>
    <td>El nombre del contenedor que desea desplegar en el clúster.</td>
    </tr>
    <tr>
    <td><code>spec.containers.volumeMounts.mountPath</code></td>
    <td>La vía de acceso absoluta del directorio en el que el que está montado el volumen dentro del contenedor. Los datos que se graban en la vía de acceso de montaje se almacenan en el directorio <code>root</code> de la instancia de almacenamiento de archivos físicos. Para crear directorios en la instancia de almacenamiento de archivos físicos, debe crear subdirectorios en la vía de acceso de montaje. </td>
    </tr>
    <tr>
    <td><code>spec.containers.volumeMounts.name</code></td>
    <td>El nombre del volumen que va a montar en el pod.</td>
    </tr>
    <tr>
    <td><code>volumes.name</code></td>
    <td>El nombre del volumen que va a montar en el pod. Normalmente, este nombre es el mismo que <code>volumeMounts.name</code>.</td>
    </tr>
    <tr>
    <td><code>volumes.persistentVolumeClaim.claimName</code></td>
    <td>El nombre de la PVC que enlaza el PV que desea utilizar. </td>
    </tr>
    </tbody></table>

5.  Cree el despliegue.
     ```
     kubectl apply -f <local_yaml_path>
     ```
     {: pre}

6.  Verifique que el PV se ha montado correctamente.

     ```
     kubectl describe deployment <deployment_name>
     ```
     {: pre}

     El punto de montaje se muestra en el campo **Volume Mounts** y el volumen se muestra en el campo **Volumes**.

     ```
      Volume Mounts:
          /var/run/secrets/kubernetes.io/serviceaccount from default-token-tqp61 (ro)
          /volumemount from myvol (rw)
     ...
     Volumes:
      myvol:
        Type: PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
        ClaimName: mypvc
        ReadOnly: false
     ```
     {: screen}

<br />


## Utilización de almacenamiento de archivos existente en el clúster
{: #existing_file}

Si dispone de un dispositivo de almacenamiento físico existente que desea utilizar en el clúster, puede crear manualmente el PV y la PVC para [suministrar de forma estática](cs_storage_basics.html#static_provisioning) el almacenamiento.

Antes de empezar, asegúrese de que tiene al menos un nodo trabajador en la misma zona que la instancia de almacenamiento de archivos existente.

### Paso 1: Preparación del almacenamiento existente.

Antes de empezar a montar el almacenamiento existente en una app, debe recuperar toda la información necesaria para su PV y debe preparar el almacenamiento para que el clúster pueda acceder al mismo.  

**Para el almacenamiento suministrado con la clase de almacenamiento `retain`:** </br>
Si ha suministrado almacenamiento con la clase de almacenamiento `retain` y elimina la PVC, el PV y el dispositivo de almacenamiento físico no se eliminan de forma automática. Para volver a utilizar el almacenamiento en el clúster, primero debe eliminar el PV restante.

Para utilizar el almacenamiento existente en un clúster distinto de aquel en el que se ha suministrado, siga los pasos para [almacenamiento que se ha creado fuera del clúster](#external_storage) para añadir el almacenamiento a la subred del nodo trabajador.
{: tip}

1. Obtenga una lista de los PV existentes.
   ```
   kubectl get pv
   ```
   {: pre}

   Busque el PV perteneciente a su almacenamiento persistente. El PV está en el estado `released`.

2. Obtenga los detalles del PV.
   ```
   kubectl describe pv <pv_name>
   ```
   {: pre}

3. Anote los valores de `CapacityGb`, `storageClass`, `failure-domain.beta.kubernetes.io/region`, `failure-domain.beta.kubernetes.io/zone`, `server` y `path`.

4. Elimine el PV.
   ```
   kubectl delete pv <pv_name>
   ```
   {: pre}

5. Verifique que el PV se ha eliminado.
   ```
   kubectl get pv
   ```
   {: pre}

</br>

**Para el almacenamiento persistente que se ha suministrado fuera del clúster:** </br>
Si desea utilizar el almacenamiento existente que ha suministrado anteriormente, pero nunca antes se ha utilizado en el clúster, debe hacer que el almacenamiento esté disponible en la misma subred que los nodos trabajadores.

**Nota**: si tiene una cuenta dedicada, debe [abrir una incidencia de soporte](/docs/get-support/howtogetsupport.html#getting-customer-support).

1.  {: #external_storage}En el [portal de la infraestructura de IBM Cloud (SoftLayer)![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo")](https://control.bluemix.net/), pulse **Almacenamiento**.
2.  Pulse **Almacenamiento de archivos** y, en el menú **Acciones**, seleccione **Autorizar host**.
3.  Seleccione **Subredes**.
4.  En la lista desplegable, seleccione la subred VLAN privada a la que está conectado el nodo trabajador. Para buscar la subred del nodo trabajador, ejecute `ibmcloud ks workers <cluster_name>` y compare la `IP privada` del nodo trabajador con la subred que ha encontrado en la lista desplegable.
5.  Pulse **Enviar**.
6.  Pulse el nombre del almacenamiento de archivos.
7.  Anote los valores de los campos `Mount Point`, `size` y `Location`. El campo `Mount Point` se muestra como `<nfs_server>:<file_storage_path>`.

### Paso 2: Creación de un volumen persistente (PV) y de una reclamación de volumen persistente (PVC) coincidente

1.  Cree un archivo de configuración de almacenamiento para el PV. Incluya los valores que ha recuperado anteriormente.

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
     name: mypv
     labels:
        failure-domain.beta.kubernetes.io/region: <region>
        failure-domain.beta.kubernetes.io/zone: <zone>
    spec:
     capacity:
       storage: "<size>"
     accessModes:
       - ReadWriteMany
     nfs:
       server: "<nfs_server>"
       path: "<file_storage_path>"
    ```
    {: codeblock}

    <table>
    <caption>Visión general de los componentes del archivo YAML</caption>
    <thead>
    <th colspan=2><img src="images/idea.png" alt="Icono Idea"/> Visión general de los componentes del archivo YAML</th>
    </thead>
    <tbody>
    <tr>
    <td><code>name</code></td>
    <td>Especifique el nombre del objeto de PV que crear.</td>
    </tr>
    <tr>
    <td><code>metadata.labels</code></td>
    <td>Especifique la región y la zona que ha recuperado anteriormente. Debe tener al menos un nodo trabajador en la misma región y zona que el almacenamiento persistente para montar el almacenamiento en el clúster. Si ya existe un PV para el almacenamiento, [añada la etiqueta de zona y región](cs_storage_basics.html#multizone) a su PV.
    </tr>
    <tr>
    <td><code>spec.capacity.storage</code></td>
    <td>Especifique el tamaño de almacenamiento del recurso compartido de archivos NFS existente que ha recuperado anteriormente. El tamaño de almacenamiento se debe especificar en gigabytes, por ejemplo, 20Gi (20 GB) o 1000Gi (1 TB), y el tamaño debe coincidir con el tamaño del recurso compartido de archivos existente.</td>
    </tr>
    <tr>
    <td><code>spec.accessMode</code></td>
    <td>Especifique una de las opciones siguientes: <ul><li><strong>ReadWriteMany: </strong> la PVC se puede montar mediante varios pods. Todos los pods pueden leer y escribir en el volumen. </li><li><strong>ReadOnlyMany: </strong> la PVC se puede montar mediante varios pods. Todos los pods tienen acceso de sólo lectura. <li><strong>ReadWriteOnce: </strong> la PVC se puede montar mediante un solo pod. Este pod puede leer y escribir en el volumen. </li></ul></td>
    </tr>
    <tr>
    <td><code>spec.nfs.server</code></td>
    <td>Escriba el ID del servidor de recursos compartidos de archivos NFS que ha recuperado anteriormente.</td>
    </tr>
    <tr>
    <td><code>path</code></td>
    <td>Especifique la vía de acceso al recurso compartido de archivos NFS existente que ha recuperado anteriormente.</td>
    </tr>
    </tbody></table>

3.  Cree el PV en el clúster.

    ```
    kubectl apply -f mypv.yaml
    ```
    {: pre}

4.  Verifique que se ha creado el PV.

    ```
    kubectl get pv
    ```
    {: pre}

5.  Cree otro archivo de configuración para crear la PVC. Para que la PVC coincida con el PV que ha creado anteriormente, debe elegir el mismo valor para `storage` y `accessMode`. El campo `storage-class` debe estar vacío. Si alguno de estos campos no coincide con el PV, se [suministran de forma dinámica](cs_storage_basics.html#dynamic_provisioning) un nuevo PV y una nueva instancia de almacenamiento físico.

    ```
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
     name: mypvc
     annotations:
       volume.beta.kubernetes.io/storage-class: ""
    spec:
     accessModes:
       - ReadWriteMany
     resources:
       requests:
         storage: "<size>"
    ```
    {: codeblock}

6.  Cree la PVC.

    ```
    kubectl apply -f mypvc.yaml
    ```
    {: pre}

7.  Verifique que la PVC se ha creado y se ha vinculado al PV. Este proceso puede tardar unos minutos.

    ```
    kubectl describe pvc mypvc
    ```
    {: pre}

    Salida de ejemplo:

    ```
    Name: mypvc
    Namespace: default
    StorageClass: ""
    Status: Bound
    Volume: pvc-0d787071-3a67-11e7-aafc-eef80dd2dea2
    Labels: <none>
    Capacity: 20Gi
    Access Modes: RWX
    Events:
      FirstSeen LastSeen Count From        SubObjectPath Type Reason Message
      --------- -------- ----- ----        ------------- -------- ------ -------
      3m 3m 1 {ibm.io/ibmc-file 31898035-3011-11e7-a6a4-7a08779efd33 } Normal Provisioning External provisioner is provisioning volume for claim "default/my-persistent-volume-claim"
      3m 1m	 10 {persistentvolume-controller } Normal ExternalProvisioning cannot find provisioner "ibm.io/ibmc-file", expecting that a volume for the claim is provisioned either manually or via external software
      1m 1m 1 {ibm.io/ibmc-file 31898035-3011-11e7-a6a4-7a08779efd33 } Normal ProvisioningSucceeded	Successfully provisioned volume pvc-0d787071-3a67-11e7-aafc-eef80dd2dea2
    ```
    {: screen}


Ha creado correctamente un PV y lo ha enlazado a una PVC. Ahora los usuarios del clúster pueden [montar la PVC](#app_volume_mount) en sus despliegues y empezar a leer el objeto de PV y a grabar en el mismo.

<br />



## Utilización del almacenamiento de archivos en un conjunto con estado
{: #file_statefulset}

Si tiene una aplicación con estado como, por ejemplo, una base de datos, puede crear conjuntos con estado que utilicen el almacenamiento de archivos para almacenar los datos de la aplicación. Como alternativa, puede utilizar una base de datos como servicio de {{site.data.keyword.Bluemix_notm}} y almacenar los datos en la nube.
{: shortdesc}

**¿Qué debo tener en cuenta cuando añada almacenamiento de archivos a un conjunto con estado?** </br>
Para añadir almacenamiento a un conjunto con estado, debe especificar la configuración del almacenamiento en la sección `volumeClaimTemplates` del archivo YAML del conjunto con estado. `volumeClaimTemplates` constituye la base para el PVC y puede incluir la clase de almacenamiento y el tamaño o IOPS del almacenamiento de archivos que desea suministrar. Sin embargo, si desea incluir etiquetas en `volumeClaimTemplates`, Kubernetes no incluye estas etiquetas al crear el PVC. En su lugar, debe añadir las etiquetas directamente al conjunto con estado.

**Importante:** no se pueden desplegar dos conjuntos con estado al mismo tiempo. Si intenta crear un conjunto con estado antes de que otro se despliegue por completo, el despliegue de su conjunto con estado puede dar lugar a resultados inesperados.

**¿Cómo se crea un conjunto con estado en una zona específica?** </br>
En un clúster multizona, puede especificar la zona y la región en las que desea crear el conjunto con estado en la sección `spec.selector.matchLabels` y en la sección `spec.template.metadata.labels` del archivo YAML del conjunto con estado. Como alternativa, puede añadir estas etiquetas a una [clase de almacenamiento personalizada](cs_storage_basics.html#customized_storageclass) y utilizar esta clase de almacenamiento en la sección `volumeClaimTemplates` de su conjunto con estado. 

**¿Qué opciones tengo para añadir almacenamiento de archivos a un conjunto con estado?** </br>
Si desea crear automáticamente el PVC al crear el conjunto con estado, utilice el [suministro dinámico](#dynamic_statefulset). También puede optar por [realizar un suministro previo de los PVC o utilizar PVC existentes](#static_statefulset) con su conjunto con estado.  

### Suministro dinámico del PVC al crear un conjunto con estado
{: #dynamic_statefulset}

Utilice esta opción si desea crear automáticamente el PVC al crear el conjunto con estado.
{: shortdesc}

Antes de empezar: [Inicie la sesión en su cuenta. Elija como destino la región adecuada y, si procede, el grupo de recursos. Establezca el contexto para el clúster](cs_cli_install.html#cs_cli_configure).

1. Verifique que todos los conjuntos con estado existentes del clúster estén totalmente desplegados. Si todavía se está desplegando un conjunto con estado, no puede empezar a crear su conjunto con estado. Debe esperar a que todos los conjuntos con estado del clúster se hayan desplegado por completo para evitar resultados inesperados.
   1. Obtenga una lista de los conjuntos con estado existentes en el clúster.
      ```
      kubectl get statefulset --all-namespaces
      ```
      {: pre}

      Salida de ejemplo:
      ```
      NAME              DESIRED   CURRENT   AGE
      mystatefulset     3         3         6s
      ```
      {: screen}

   2. Visualice el **estado de los pods** de cada conjunto con estado para asegurarse de que el despliegue del conjunto con estado haya finalizado.  
      ```
      kubectl describe statefulset <statefulset_name>
      ```
      {: pre}

      Salida de ejemplo:
      ```
      Name:               nginx
      Namespace:          default
      CreationTimestamp:  Fri, 05 Oct 2018 13:22:41 -0400
      Selector:           app=nginx,billingType=hourly,region=us-south,zone=dal10
      Labels:             app=nginx
                          billingType=hourly
                          region=us-south
                          zone=dal10
      Annotations:        kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"apps/v1beta1","kind":"StatefulSet","metadata":{"annotations":{},"name":"nginx","namespace":"default"},"spec":{"podManagementPolicy":"Par...
      Replicas:           3 desired | 3 total
      Pods Status:        0 Running / 3 Waiting / 0 Succeeded / 0 Failed
      Pod Template:
        Labels:  app=nginx
                 billingType=hourly
                 region=us-south
                 zone=dal10
      ...
      ```
      {: screen}

      Un conjunto con estado se ha desplegado por completo cuando el número de réplicas que encuentra en la sección **Replicas** de la salida de CLI es igual al número de pods con el estado **Running** en la sección **Pods Status**. Si un conjunto con estado aún no se ha desplegado por completo, espere hasta que el despliegue haya finalizado antes de continuar.

3. Cree un archivo de configuración para el conjunto con estado y el servicio que utiliza para exponer el conjunto con estado. En el ejemplo siguiente se muestra cómo desplegar nginx como un conjunto con estado con 3 réplicas. Para cada réplica, se proporciona un dispositivo de almacenamiento de archivos de 20 gigabytes en función de las especificaciones definidas en la clase de almacenamiento `ibmc-file-retain-bronze`. Todos los dispositivos de almacenamiento se suministran en la zona `dal10`. Puesto que no se puede acceder al almacenamiento de archivos desde otras zonas, todas las réplicas del conjunto de estado también se despliegan en un nodo trabajador que se encuentra en `dal10`.

   ```
   apiVersion: v1
   kind: Service
   metadata:
    name: nginx
    labels:
      app: nginx
   spec:
    ports:
    - port: 80
      name: web
    clusterIP: None
    selector:
      app: nginx
   ---
   apiVersion: apps/v1beta1
   kind: StatefulSet
   metadata:
    name: nginx
   spec:
    serviceName: "nginx"
    replicas: 3
    podManagementPolicy: Parallel
    selector:
      matchLabels:
        app: nginx
        billingType: "hourly"
        region: "us-south"
        zone: "dal10"
    template:
      metadata:
        labels:
          app: nginx
          billingType: "hourly"
          region: "us-south"
          zone: "dal10"
      spec:
        containers:
        - name: nginx
          image: k8s.gcr.io/nginx-slim:0.8
          ports:
          - containerPort: 80
            name: web
          volumeMounts:
          - name: myvol
            mountPath: /usr/share/nginx/html
    volumeClaimTemplates:
    - metadata:
        annotations:
          volume.beta.kubernetes.io/storage-class: ibmc-file-retain-bronze
        name: myvol
      spec:
        accessModes:
        - ReadWriteOnce
        resources:
          requests:
            storage: 20Gi
            iops: "300" #required only for performance storage
   ```
   {: codeblock}

   <table>
    <caption>Visión general de los componentes del archivo YAML de un conjunto con estado</caption>
    <thead>
    <th colspan=2><img src="images/idea.png" alt="Icono de idea"/> Visión general de los componentes del archivo YAML de un conjunto con estado</th>
    </thead>
    <tbody>
    <tr>
    <td style="text-align:left"><code>metadata.name</code></td>
    <td style="text-align:left">Especifique un nombre para el conjunto con estado. El nombre que escriba se utiliza para crear el nombre del PVC con el siguiente formato: <code>&lt;nombre_volumen&gt;-&lt;nombre_conjuntoconestado&gt;-&lt;número_réplica&gt;</code>. </td>
    </tr>
    <tr>
    <td style="text-align:left"><code>spec.serviceName</code></td>
    <td style="text-align:left">Especifique el nombre del servicio que desea utilizar para exponer el conjunto con estado. </td>
    </tr>
    <tr>
    <td style="text-align:left"><code>spec.replicas</code></td>
    <td style="text-align:left">Especifique el número de réplicas para el conjunto con estado. </td>
    </tr>
    <tr>
    <td style="text-align:left"><code>spec.podManagementPolicy</code></td>
    <td style="text-align:left">Especifique la política de gestión de pod que desea utilizar para su conjunto con estado. Seleccione una de las opciones siguientes: <ul><li><strong>OrderedReady: </strong>con esta opción, las réplicas del conjunto con estado se despliegan una después de otra. Por ejemplo, si ha especificado 3 réplicas, Kubernetes crea el PVC para la primera réplica, espera hasta que el PVC esté enlazado, despliegue la réplica del conjunto con estado y monta el PVC en la réplica. Una vez que haya finalizado el despliegue, se despliega la segunda réplica. Para obtener más información acerca de esta opción, consulte [Gestión de podsOrderedReady![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/#orderedready-pod-management). </li><li><strong>Parallel: </strong>con esta opción, el despliegue de todas las réplicas del conjunto con estado comienza a la vez. Si la app da soporte al despliegue de réplicas en paralelo, utilice esta opción para ahorrar tiempo de despliegue para los PVC y las réplicas de conjuntos con estado. </li></ul></td>
    </tr>
    <tr>
    <td style="text-align:left"><code>spec.selector.matchLabels</code></td>
    <td style="text-align:left">Especifique todas las etiquetas que desee incluir en el conjunto con estado y en el PVC. Kubernetes no reconoce las etiquetas que se incluyen en <code>volumeClaimTemplates</code> del conjunto con estado. Ejemplos de etiquetas que quizás desee incluir: <ul><li><code><strong>region</strong></code> y <code><strong>zone</strong></code>: si desea que todas las réplicas y PVC del conjunto con estado se creen en una zona específica, añada ambas etiquetas. También puede especificar la zona y la región en la clase de almacenamiento que utiliza. Si no especifica una zona y región y tiene un clúster multizona, la zona en la que se suministra el almacenamiento se selecciona en una iteración cíclica para equilibrar las solicitudes de volumen de forma uniforme entre todas las zonas.</li><li><code><strong>billingType</strong></code>: escriba el tipo de facturación que desea utilizar para los PVC. Elija entre <code>hourly</code> o <code>monthly</code>. Si no especifica esta etiqueta, todos los PVC se crean con un tipo de facturación por hora. </li></ul></td>
    </tr>
    <tr>
    <td style="text-align:left"><code>spec.template.metadata.labels</code></td>
    <td style="text-align:left">Especifique las mismas etiquetas que ha añadido a la sección <code>spec.selector.matchLabels</code>. </td>
    </tr>
    <tr>
    <td style="text-align:left"><code>spec.volumeClaimTemplates.metadata.</code></br><code>annotations.volume.beta.</code></br><code>kubernetes.io/storage-class</code></td>
    <td style="text-align:left">Especifique la clase de almacenamiento que desea utilizar. Para ver una lista de las clases de almacenamiento existentes, ejecute <code>kubectl get storageclasses | grep file</code>. Si no especifica una clase de almacenamiento, el PVC se crea con la clase de almacenamiento predeterminada establecida en el clúster. Asegúrese de que la clase de almacenamiento predeterminada utiliza el suministrador <code>ibm.io/ibmc-file</code> para que el conjunto con estado se suministre con almacenamiento de archivos.</td>
    </tr>
    <tr>
    <td style="text-align:left"><code>spec.volumeClaimTemplates.metadata.name</code></td>
    <td style="text-align:left">Especifique un nombre para el volumen. Utilice el mismo nombre que ha definido en la sección <code>spec.containers.volumeMount.name</code>. El nombre que escriba aquí se utiliza para crear el nombre del PVC con el siguiente formato: <code>&lt;nombre_volumen&gt;-&lt;nombre_conjuntoconestado&gt;-&lt;número_réplica&gt;</code>. </td>
    </tr>
    <tr>
    <td style="text-align:left"><code>spec.volumeClaimTemplates.spec.resources.</code></br><code>requests.storage</code></td>
    <td style="text-align:left">Indique el tamaño del almacenamiento de archivos, en gigabytes (Gi).</td>
    </tr>
    <tr>
    <td style="text-align:left"><code>spec.volumeClaimTemplates.spec.resources.</code></br><code>requests.iops</code></td>
    <td style="text-align:left">Si desea suministrar [almacenamiento de rendimiento](#predefined_storageclass), especifique el número de IOPS. Si utiliza una clase de almacenamiento de resistencia y especifica un número de IOPS, se pasa por alto el número de IOPS. En su lugar se utiliza el número de IOPS especificado en la clase de almacenamiento.  </td>
    </tr>
    </tbody></table>

4. Cree su conjunto con estado.
   ```
   kubectl apply -f statefulset.yaml
   ```
   {: pre}

5. Espere a que se despliegue el conjunto con estado.
   ```
   kubectl describe statefulset <statefulset_name>
   ```
   {: pre}

   Para ver el estado actual de los PVC, ejecute `kubectl get pvc`. El nombre del PVC tiene el formato `<volume_name>-<statefulset_name>-<replica_number>`.
   {: tip}

### Realice un suministro previo del PVC antes de crear el conjunto con estado
{: #static_statefulset}

Puede realizar un suministro previo de los PVC antes de crear el conjunto con estado o utilizar PVC existentes con su conjunto con estado.
{: shortdesc}

Cuando [suministre dinámicamente los PVC al crear el conjunto con estado](#dynamic_statefulset), el nombre del PVC se asigna en función de los valores que ha utilizado en el archivo YAML de conjunto con estado. Para que el conjunto con estado utilice los PVC existentes, el nombre de los PVC debe coincidir con el nombre que se crearía automáticamente si se utilizara el suministro dinámico.

Antes de empezar: [Inicie la sesión en su cuenta. Elija como destino la región adecuada y, si procede, el grupo de recursos. Establezca el contexto para el clúster](cs_cli_install.html#cs_cli_configure).

1. Siga los pasos del 1 al 3 de la sección [Adición de almacenamiento de archivos a aplicaciones](#add_file) para crear un PVC para cada réplica del conjunto con estado. Asegúrese de crear el PVC con un nombre que siga el formato siguiente: `<volume_name>-<statefulset_name>-<replica_number>`.
   - **`<volume_name>`**: utilice el nombre que desea especificar en la sección `spec.volumeClaimTemplates.metadata.name`
del conjunto con estado, como por ejemplo `nginxvol`.
   - **`<statefulset_name>`**: utilice el nombre que desea especificar en la sección ` metadata.name` del conjunto con estado, como por ejemplo `nginx_statefulset`.
   - **`<replica_number>`**: especifique el número de la réplica empezando por 0.

   Por ejemplo, si tiene que crear 3 réplicas del conjunto con estado, cree 3 PVC con los siguientes nombres: `nginxvol-nginx_statefulset-0`, `nginxvol-nginx_statefulset-1` y `nginxvol-nginx_statefulset-2`.  

2. Siga los pasos de [Suministro dinámico del PVC al crear un conjunto con estado](#dynamic_statefulset) para crear el conjunto con estado. Asegúrese de utilizar los valores de los nombres de PVC en la especificación del conjunto con estado:
   - **`spec.volumeClaimTemplates.metadata.name`**: especifique el `<volume_name>` que ha utilizado en el paso anterior.
   - **`metadata.name`**: utilice el `<statefulset_name>` que ha utilizado en el paso anterior.
   - **`spec.replicas`**: escriba el número de réplicas que desea crear para el conjunto con estado. El número de réplicas debe ser igual al número de PVC que ha creado anteriormente.

   **Nota:** si ha creado los PVC en diferentes zonas, no incluya una etiqueta de región o zona en el conjunto con estado.

3. Verifique que los PVC se utilizan en los pods de réplica del conjunto con estado.
   1. Obtenga una lista de pods en el clúster. Identifique los pods que pertenecen a su conjunto con estado.
      ```
      kubectl get pods
      ```
      {: pre}

   2. Verifique que el PVC existente esté montado en la réplica del conjunto con estado. Revise el valor de **ClaimName** en la sección **Volumes** de la salida de la CLI.
      ```
      kubectl describe pod <pod_name>
      ```
      {: pre}

      Salida de ejemplo:
      ```
      Name:           nginx-0
      Namespace:      default
      Node:           10.xxx.xx.xxx/10.xxx.xx.xxx
      Start Time:     Fri, 05 Oct 2018 13:24:59 -0400
      ...
      Volumes:
        myvol:
          Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
          ClaimName:  myvol-nginx-0
     ...
      ```
      {: screen}

<br />


## Cambio de la versión predeterminada de NFS
{: #nfs_version}

La versión del almacenamiento de archivos determina el protocolo que se utiliza para comunicarse con el servidor de almacenamiento de archivos de {{site.data.keyword.Bluemix_notm}}. De forma predeterminada, todas las instancias de almacenamiento de archivos se configuran con la versión 4 de NFS. Puede cambiar su PV existente a una versión anterior de NFS si una app requiere una versión específica para funcionar correctamente.
{: shortdesc}

Para cambiar la versión predeterminada de NFS, puede crear una nueva clase de almacenamiento para suministrar de forma dinámica el almacenamiento de archivos en su clúster o elegir cambiar un PV existente que esté montado en el pod.

**Importante:** para aplicar las últimas actualizaciones de seguridad y para un mejor rendimiento, utilice la versión de NFS predeterminada y no cambie a una versión anterior de NFS.

**Para crear una clase de almacenamiento personalizada con la versión deseada de NFS: **
1. Cree una [clase de almacenamiento personalizada](#nfs_version_class) con la versión de NFS que desea suministrar.
2. Cree una clase de almacenamiento en su clúster.
   ```
   kubectl apply -f nfsversion_storageclass.yaml
   ```
   {: pre}

3. Verifique que se haya creado la clase de almacenamiento personalizada.
   ```
   kubectl get storageclasses
   ```
   {: pre}

4. Suministre [almacenamiento de archivos](#add_file) con su clase de almacenamiento personalizada.

**Para hacer que el PV existente utilice una versión diferente NFS:**

1. Obtenga el PV del almacenamiento de archivos cuya versión de NFS desee cambiar y anote el nombre del PV.
   ```
   kubectl get pv
   ```
   {: pre}

2. Añada una anotación a su PV. Sustituya `<version_number>` con la versión de NFS que desee utilizar. Por ejemplo, para cambiar a NFS versión 3.0, especifique **3**.  
   ```
   kubectl patch pv <pv_name> -p '{"metadata": {"annotations":{"volume.beta.kubernetes.io/mount-options":"vers=<version_number>"}}}'
   ```
   {: pre}

3. Suprima el pod que utiliza el almacenamiento de archivos y vuelva a crear el pod.
   1. Guarde el yaml del pod en su máquina local.
      ```
      kubect get pod <pod_name> -o yaml > <filepath/pod.yaml>
      ```
      {: pre}

   2. Suprima el pod.
      ```
      kubectl deleted pod <pod_name>
      ```
      {: pre}

   3. Vuelva a crear el pod.
      ```
      kubectl apply -f pod.yaml
      ```
      {: pre}

4. Espere a que se haya desplegado el pod.
   ```
   kubectl get pods
   ```
   {: pre}

   El pod quedará completamente desplegado cuando el estado cambie a `En ejecución`.

5. Inicie una sesión en el pod.
   ```
   kubectl exec -it <pod_name> sh
   ```
   {: pre}

6. Verifique que el almacenamiento de archivos se ha montado con la versión de NFS que ha especificado anteriormente.
   ```
   mount | grep "nfs" | awk -F" |," '{ print $5, $8 }'
   ```
   {: pre}

   Salida de ejemplo:
   ```
   nfs vers=3.0
   ```
   {: screen}

<br />


## Copia de seguridad y restauración de datos
{: #backup_restore}

El almacenamiento de archivos se suministra en la misma ubicación que los nodos trabajadores del clúster. IBM aloja el almacenamiento en servidores en clúster para proporcionar disponibilidad en el caso de que un servidor deje de funcionar. Sin embargo, no se realizan automáticamente copias de seguridad del almacenamiento de archivos, y puede dejar de ser accesible si falla toda la ubicación. Para evitar que los datos que se pierdan o se dañen, puede configurar copias de seguridad periódicas que puede utilizar para restaurar los datos cuando sea necesario.
{: shortdesc}

Consulte las opciones siguientes de copia de seguridad y restauración para el almacenamiento de archivos:

<dl>
  <dt>Configurar instantáneas periódicas</dt>
  <dd><p>Puede [configurar instantáneas periódicas para el almacenamiento de archivos](/docs/infrastructure/FileStorage/snapshots.html), que son imágenes de solo lectura que capturan el estado de la instancia en un punto en el tiempo. Para almacenar la instantánea, debe solicitar espacio de instantáneas en el almacenamiento de archivos. Las instantáneas se almacenan en la instancia de almacenamiento existente dentro de la misma zona. Puede restaurar datos desde una instantánea si un usuario elimina accidentalmente datos importantes del volumen. <strong>Nota</strong>: si tiene una cuenta dedicada, debe [abrir una incidencia de soporte](/docs/get-support/howtogetsupport.html#getting-customer-support).</br></br> <strong>Para crear una instantánea para su volumen: </strong><ol><li>[Inicie una sesión en su cuenta. Elija como destino la región adecuada y, si procede, el grupo de recursos. Establezca el contexto para el clúster](cs_cli_install.html#cs_cli_configure).</li><li>Inicie una sesión en la CLI de `ibmcloud sl`. <pre class="pre"><code>    ibmcloud sl init
    </code></pre></li><li>Liste los PV en su clúster. <pre class="pre"><code>kubectl get pv</code></pre></li><li>Obtenga los detalles de los PV para los que desea crear espacio de instantáneas y anote el ID de volumen, el tamaño y las IOPS. <pre class="pre"><code>kubectl describe pv &lt;pv_name&gt;</code></pre> Encontrará el ID de volumen, el tamaño y las IOPS en la sección <strong>Labels</strong> de la salida de la CLI. </li><li>Cree el tamaño de instantánea para el volumen existente con los parámetros que ha recuperado en el paso anterior. <pre class="pre"><code>ibmcloud sl file snapshot-order &lt;volume_ID&gt; --size &lt;size&gt; --tier &lt;iops&gt;</code></pre></li><li>Espere a que se haya creado el tamaño de la instantánea. <pre class="pre"><code>ibmcloud sl file volume-detail &lt;volume_ID&gt;</code></pre>El tamaño de la instantánea se suministra de forma correcta cuando el valor de <strong>Snapshot Size (GB)</strong> en la salida de la CLI pasa de 0 al tamaño solicitado. </li><li>Cree la instantánea para el volumen y anote el ID de la instantánea que se crea para usted. <pre class="pre"><code>ibmcloud sl file snapshot-create &lt;volume_ID&gt;</code></pre></li><li>Verifique que la instantánea se haya creado correctamente. <pre class="pre"><code>ibmcloud sl file snapshot-list &lt;volume_ID&gt;</code></pre></li></ol></br><strong>Para restaurar los datos desde una instantánea en un volumen existente: </strong><pre class="pre"><code>ibmcloud sl file snapshot-restore &lt;volume_ID&gt; &lt;snapshot_ID&gt;</code></pre></p></dd>
  <dt>Realice una réplica de las instantáneas en otra zona</dt>
 <dd><p>Para proteger los datos ante un error de la zona, puede [replicar instantáneas](/docs/infrastructure/FileStorage/replication.html#replicating-data) en una instancia de almacenamiento de archivos configurada en otra zona. Los datos únicamente se pueden replicar desde el almacenamiento primario al almacenamiento de copia de seguridad. No puede montar una instancia replicada de almacenamiento de archivos en un clúster. Cuando el almacenamiento primario falla, puede establecer de forma manual el almacenamiento de copia de seguridad replicado para que sea el primario. A continuación, puede montarla en el clúster. Una vez restaurado el almacenamiento primario, puede restaurar los datos del almacenamiento de copia de seguridad. <strong>Nota</strong>: si tiene una cuenta dedicada, no puede replicar instantáneas en otra zona.</p></dd>
 <dt>Duplicar almacenamiento</dt>
 <dd><p>Puede [duplicar la instancia de almacenamiento de archivos](/docs/infrastructure/FileStorage/how-to-create-duplicate-volume.html#creating-a-duplicate-file-storage) en la misma zona que la instancia de almacenamiento original. La instancia duplicada tiene los mismos datos que la instancia de almacenamiento original en el momento de duplicarla. A diferencia de las réplicas, utilice los duplicados como una instancia de almacenamiento independiente de la original. Para duplicar, primero [configure instantáneas para el volumen](/docs/infrastructure/FileStorage/snapshots.html). <strong>Nota</strong>: si tiene una cuenta dedicada, debe <a href="/docs/get-support/howtogetsupport.html#getting-customer-support">abrir una incidencia de soporte</a>.</p></dd>
  <dt>Haga copia de seguridad de los datos en {{site.data.keyword.cos_full}}</dt>
  <dd><p>Puede utilizar la [**imagen ibm-backup-restore**](/docs/services/RegistryImages/ibm-backup-restore/index.html#ibmbackup_restore_starter) para utilizar un pod de copia de seguridad y restauración en el clúster. Este pod contiene un script para ejecutar una copia de seguridad puntual o periódico para cualquier reclamación de volumen persistente (PVC) en el clúster. Los datos se almacenan en la instancia de {{site.data.keyword.cos_full}} que ha configurado en una zona.</p>
  <p>Para aumentar la alta disponibilidad de los datos y proteger la app ante un error de la zona, configure una segunda instancia de {{site.data.keyword.cos_full}} y replique los datos entre las zonas. Si necesita restaurar datos desde la instancia de {{site.data.keyword.cos_full}}, utilice el script de restauración que se proporciona con la imagen.</p></dd>
<dt>Copiar datos a y desde pods y contenedores</dt>
<dd><p>Utilice el [mandato ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo")](https://kubernetes.io/docs/reference/kubectl/overview/#cp) `kubectl cp` para copiar archivos y directorios a y desde pods o contenedores específicos en el clúster.</p>
<p>Antes de empezar: [Inicie la sesión en su cuenta. Elija como destino la región adecuada y, si procede, el grupo de recursos. Establezca el contexto para el clúster](cs_cli_install.html#cs_cli_configure). Si no especifica un contenedor con <code>-c</code>, el mandato utiliza el primer contenedor disponible en el pod.</p>
<p>El mandato se puede utilizar de varias maneras:</p>
<ul>
<li>Copiar datos desde su máquina local a un pod en su clúster: <pre class="pre"><code>kubectl cp <var>&lt;local_filepath&gt;/&lt;filename&gt;</var> <var>&lt;namespace&gt;/&lt;pod&gt;:&lt;pod_filepath&gt;</var></code></pre></li>
<li>Copiar datos desde un pod en su clúster a su máquina local: <pre class="pre"><code>kubectl cp <var>&lt;namespace&gt;/&lt;pod&gt;:&lt;pod_filepath&gt;/&lt;filename&gt;</var> <var>&lt;local_filepath&gt;/&lt;filename&gt;</var></code></pre></li>
<li>Copiar datos desde su máquina local a un contenedor específico que se ejecuta en un pod en el clúster: <pre class="pre"><code>kubectl cp <var>&lt;local_filepath&gt;/&lt;filename&gt;</var> <var>&lt;namespace&gt;/&lt;pod&gt;:&lt;pod_filepath&gt;</var> -c <var>&lt;container></var></code></pre></li>
</ul></dd>
  </dl>

<br />


## Referencia de clases de almacenamiento
{: #storageclass_reference}

### Bronce
{: #bronze}

<table>
<caption>Clase de almacenamiento de archivos: bronce</caption>
<thead>
<th>Características</th>
<th>Valor</th>
</thead>
<tbody>
<tr>
<td>Nombre</td>
<td><code>ibmc-file-bronze</code></br><code>ibmc-file-retain-bronze</code></td>
</tr>
<tr>
<td>Tipo</td>
<td>[Almacenamiento resistente ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo")](https://knowledgelayer.softlayer.com/topic/endurance-storage)</td>
</tr>
<tr>
<td>Sistema de archivos</td>
<td>NFS</td>
</tr>
<tr>
<td>IOPS por gigabyte</td>
<td>2</td>
</tr>
<tr>
<td>Rango de tamaño en gigabytes</td>
<td>20-12000 Gi</td>
</tr>
<tr>
<td>Disco duro</td>
<td>SSD</td>
</tr>
<tr>
<td>Facturación</td>
<td>Por hora</td>
</tr>
<tr>
<td>Tarifas</td>
<td>[Información sobre tarifas ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo")](https://www.ibm.com/cloud/file-storage/pricing)</td>
</tr>
</tbody>
</table>


### Plata
{: #silver}

<table>
<caption>Clase de almacenamiento de archivos: plata</caption>
<thead>
<th>Características</th>
<th>Valor</th>
</thead>
<tbody>
<tr>
<td>Nombre</td>
<td><code>ibmc-file-silver</code></br><code>ibmc-file-retain-silver</code></td>
</tr>
<tr>
<td>Tipo</td>
<td>[Almacenamiento resistente ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo")](https://knowledgelayer.softlayer.com/topic/endurance-storage)</td>
</tr>
<tr>
<td>Sistema de archivos</td>
<td>NFS</td>
</tr>
<tr>
<td>IOPS por gigabyte</td>
<td>4</td>
</tr>
<tr>
<td>Rango de tamaño en gigabytes</td>
<td>20-12000 Gi</td>
</tr>
<tr>
<td>Disco duro</td>
<td>SSD</td>
</tr>
<tr>
<td>Facturación</td>
<td>Por hora</li></ul></td>
</tr>
<tr>
<td>Tarifas</td>
<td>[Información sobre tarifas ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo")](https://www.ibm.com/cloud/file-storage/pricing)</td>
</tr>
</tbody>
</table>

### Oro
{: #gold}

<table>
<caption>Clase de almacenamiento de archivos: oro</caption>
<thead>
<th>Características</th>
<th>Valor</th>
</thead>
<tbody>
<tr>
<td>Nombre</td>
<td><code>ibmc-file-gold</code></br><code>ibmc-file-retain-gold</code></td>
</tr>
<tr>
<td>Tipo</td>
<td>[Almacenamiento resistente ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo")](https://knowledgelayer.softlayer.com/topic/endurance-storage)</td>
</tr>
<tr>
<td>Sistema de archivos</td>
<td>NFS</td>
</tr>
<tr>
<td>IOPS por gigabyte</td>
<td>10</td>
</tr>
<tr>
<td>Rango de tamaño en gigabytes</td>
<td>20-4000 Gi</td>
</tr>
<tr>
<td>Disco duro</td>
<td>SSD</td>
</tr>
<tr>
<td>Facturación</td>
<td>Por hora</li></ul></td>
</tr>
<tr>
<td>Tarifas</td>
<td>[Información sobre tarifas ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo")](https://www.ibm.com/cloud/file-storage/pricing)</td>
</tr>
</tbody>
</table>

### Personalizada
{: #custom}

<table>
<caption>Clase de almacenamiento de archivos: personalizada</caption>
<thead>
<th>Características</th>
<th>Valor</th>
</thead>
<tbody>
<tr>
<td>Nombre</td>
<td><code>ibmc-file-custom</code></br><code>ibmc-file-retain-custom</code></td>
</tr>
<tr>
<td>Tipo</td>
<td>[Rendimiento ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo")](https://knowledgelayer.softlayer.com/topic/performance-storage)</td>
</tr>
<tr>
<td>Sistema de archivos</td>
<td>NFS</td>
</tr>
<tr>
<td>IOPS y tamaño</td>
<td><p><strong>Rango de tamaños en gigabytes / Rango de IOPS en múltiplos de 100</strong></p><ul><li>20-39 Gi / 100-1000 IOPS</li><li>40-79 Gi / 100-2000 IOPS</li><li>80-99 Gi / 100-4000 IOPS</li><li>100-499 Gi / 100-6000 IOPS</li><li>500-999 Gi / 100-10000 IOPS</li><li>1000-1999 Gi / 100-20000 IOPS</li><li>2000-2999 Gi / 200-40000 IOPS</li><li>3000-3999 Gi / 200-48000 IOPS</li><li>4000-7999 Gi / 300-48000 IOPS</li><li>8000-9999 Gi / 500-48000 IOPS</li><li>10000-12000 Gi / 1000-48000 IOPS</li></ul></td>
</tr>
<tr>
<td>Disco duro</td>
<td>La proporción entre IOPS y gigabytes determina el tipo de disco duro que se suministra. Para determinar la proporción entre IOPS y gigabytes, divida el número de IOPS por el tamaño de su almacenamiento. </br></br>Ejemplo: </br>Supongamos que elige 500 Gi de almacenamiento con 100 IOPS. La proporción es de 0,2 (100 IOPS/500 Gi). </br></br><strong>Visión general de los tipos de disco duro por proporción:</strong><ul><li>Menor o igual que 0,3: SATA</li><li>Mayor que 0,3: SSD</li></ul></td>
</tr>
<tr>
<td>Facturación</td>
<td>Por hora</li></ul></td>
</tr>
<tr>
<td>Tarifas</td>
<td>[Información sobre tarifas ![Icono de enlace externo](../icons/launch-glyph.svg "Icono de enlace externo")](https://www.ibm.com/cloud/file-storage/pricing)</td>
</tr>
</tbody>
</table>

<br />


## Clases de almacenamiento personalizadas de ejemplo
{: #custom_storageclass}

Puede crear una clase de almacenamiento personalizada y utilizar la clase de almacenamiento en el PVC.
{: shortdesc}

{{site.data.keyword.containerlong_notm}} proporciona [clases de almacenamiento predefinidas](#storageclass_reference) para
suministrar almacenamiento de archivos con un nivel y una configuración determinados. En algunos casos, es posible que desee suministrar almacenamiento con otra configuración que no esté cubierta en las clases de almacenamiento predefinidas. Puede utilizar los ejemplos de este tema para encontrar clases de almacenamiento personalizadas de ejemplo.

Para crear la clase de almacenamiento personalizada, consulte [Personalización de una clase de almacenamiento](cs_storage_basics.html#customized_storageclass). A continuación, [utilice la clase de almacenamiento personalizada en el PVC](#add_file).

### Especificación de la zona para clústeres multizona
{: #multizone_yaml}

El siguiente archivo `.yaml` personaliza una clase de almacenamiento que se basa en la clase de almacenamiento sin retención `ibm-file-silver`: el valor de `type` es `"Endurance"`, el valor de `iopsPerGB` es `4`, el valor de `sizeRange` es `"[20-12000]Gi"` y el valor de `reclaimPolicy` está establecido en `"Delete"`. La zona especificada es `dal12`. Puede revisar la información anterior en las clases de almacenamiento `ibmc` como ayuda para elegir valores aceptables para estos </br>

```
apiVersion: storage.k8s.io/v1beta1
kind: StorageClass
metadata:
  name: ibmc-file-silver-mycustom-storageclass
  labels:
    kubernetes.io/cluster-service: "true"
provisioner: ibm.io/ibmc-file
parameters:
  zone: "dal12"
  region: "us-south"
  type: "Endurance"
  iopsPerGB: "4"
  sizeRange: "[20-12000]Gi"
  reclaimPolicy: "Delete"
  classVersion: "2"
```
{: codeblock}

### Cambio de la versión predeterminada de NFS
{: #nfs_version_class}

La siguiente clase de almacenamiento personalizada se basa en la [clase de almacenamiento `ibmc-file-bronze`](#bronze) y le permite definir la versión de NFS que desea suministrar. Por ejemplo, para suministrar NFS versión 3.0, sustituya `<nfs_version>` por **3.0**.
```
apiVersion: storage.k8s.io/v1
   kind: StorageClass
   metadata:
     name: ibmc-file-mount
     #annotations:
     #  storageclass.beta.kubernetes.io/is-default-class: "true"
     labels:
       kubernetes.io/cluster-service: "true"
   provisioner: ibm.io/ibmc-file
   parameters:
     type: "Endurance"
     iopsPerGB: "2"
     sizeRange: "[1-12000]Gi"
     reclaimPolicy: "Delete"
     classVersion: "2"
     mountOptions: nfsvers=<nfs_version>
```
{: codeblock}

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

# Configuration de politiques de sécurité de pod
{: #psp}

Avec les [politiques de sécurité de pod ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/policy/pod-security-policy/), vous pouvez
configurer des politiques pour déterminer quelles sont les personnes autorisées à créer et mettre à jour des pods dans {{site.data.keyword.containerlong}}. Les clusters qui exécutent Kubernetes versions 1.10.3, 1.9.8 t 1.8.13 ou des groupes de correctifs ultérieurs, prennent en charge le contrôleur d'admission `PodSecurityPolicy` qui applique ces politiques. 
{: shortdesc}

Vous utilisez une version plus ancienne de Kubernetes ? [Mettez à jour votre cluster](cs_cluster_update.html) dès aujourd'hui.
{: tip}

**Pourquoi définir des politiques de sécurité de pod ?**</br>
En tant qu'administrateur de cluster, vous désirez contrôler ce qui se passe dans votre cluster, notamment les actions qui affectent la sécurité ou la réactivité du cluster. Ces politiques peuvent vous aider à contrôler l'utilisation des conteneurs privilégiés, des espaces de nom racine, des réseaux et ports d'hôte, des types de volume, des systèmes de fichiers hôte, des droits Linux, tels que lecture seule ou ID de groupes, etc.

Avec le contrôleur d'admission `PodSecurityPolicy`, aucun pod ne peut être créé tant que vous n'avez pas [autorisé de politiques](#customize_psp). La configuration des politiques de sécurité de pod peut avoir des effets secondaires indésirables, par conséquent veillez à tester un déploiement après avoir modifié une politique. Pour déployer des applications, les comptes utilisateur et les comptes de service doivent tous être autorisés par les politiques de sécurité de pod requises à déployer des pods. Par exemple, si vous installez des applications en utilisant [Helm](cs_integrations.html#helm_links), le composant Helm tiller crée des pods, et vous devez donc disposer de l'autorisation de politique de sécurité adéquate.

Vous essayez de contrôler quels sont les utilisateurs pouvant accéder à {{site.data.keyword.containerlong_notm}} ? Voir [Affectation d'accès au cluster](cs_users.html#users) pour définir les droits IAM et les droits de l'infrastructure.
{: tip}

**Existent-ils des politiques définies par défaut ? Que puis-je ajouter ?**</br>
Par défaut, {{site.data.keyword.containerlong_notm}} configure le contrôleur d'admission `PodSecurityPolicy` avec des [ressources de gestion de cluster {{site.data.keyword.IBM_notm}}](#ibm_psp) que vous ne pouvez pas supprimer ou modifier. Vous ne pouvez pas non plus désactiver le contrôleur d'admission. 

Les actions de pod ne sont pas verrouillées par défaut. A la place, deux ressources RBAC (Role-Based Access Control) figurant dans le cluster autorisent tous les administrateurs, utilisateurs, services et noeuds à créer des pods privilégiés et non privilégiés. Pour empêcher certains utilisateurs de créer ou de mettre à jour des pods, vous pouvez [modifier ces ressources RBAC ou créer vos propres ressources](#customize_psp).

**Comment fonctionne une autorisation de politique ?**</br>
Lorsqu'en tant qu'utilisateur vous créez un pod directement sans passer par un contrôleur, tel qu'un déploiement, vos données d'identification sont validées par rapport aux politiques de sécurité de pod que vous êtes autorisé à utiliser. Si aucune politique n'est compatible avec les exigences en matière de sécurité du pod, le pod n'est pas créé.

Lorsque vous créez un pod en utilisant un contrôleur de ressource, tel qu'un déploiement, Kubernetes valide les données d'identification du compte de service par rapport aux politiques de sécurité de pod que le compte de service est autorisé à utiliser. Si aucune politique n'est compatible avec les exigences en matière de sécurité du pod, le contrôleur aboutit mais le pod n'est pas créé.

Pour connaître les messages d'erreur courants, voir la rubrique [Les pods ne parviennent pas à se déployer en raison d'une politique de sécurité de pod](cs_troubleshoot_clusters.html#cs_psp).

## Personnalisation des politiques de sécurité de pod
{: #customize_psp}

Pour bloquer toute action non autorisée sur les pods, vous pouvez modifier les ressources de politique de sécurité de pod existantes ou créer vos propres ressources. Vous devez être administrateur d'un cluster pour personnaliser des politiques.
{: shortdesc}

**Quelles sont les politiques existantes que je peux modifier ?**</br>
Par défaut, votre cluster renferme les ressources RBAC suivantes qui permettent aux administrateurs
de cluster, aux utilisateurs autorisés, aux comptes de service et aux noeuds d'utiliser les politiques
de sécurité de pod `ibm-privileged-psp` et `ibm-restricted-psp`. Ces
politiques permettent aux utilisateurs de créer et mettre à jour des pods privilégiés et des pods (restreints) non privilégiés.

| Nom | Espace de nom | Type | Fonction |
|---|---|---|---|
| `privileged-psp-user` | cluster | `ClusterRoleBinding` | Permet aux administrateurs de cluster, aux utilisateurs authentifiés, aux comptes de service et aux noeuds d'utiliser la politique de sécurité de pod `ibm-privileged-psp`. |
| `restricted-psp-user` | cluster | `ClusterRoleBinding` | Permet aux administrateurs de cluster, aux utilisateurs authentifiés, aux comptes de service et aux noeuds d'utiliser la politique de sécurité de pod `ibm-restricted-psp`. |
{: caption="Ressources RBAC par défaut que vous pouvez modifier" caption-side="top"}

Vous pouvez modifier ces rôles RBAC pour retirer ou ajouter des administrateurs, des utilisateurs, des services ou des noeuds dans une politique.

Avant de commencer : 
*  [Connectez-vous à votre compte. Ciblez la région appropriée et, le cas échéant, le groupe de ressources. Définissez le contexte de votre cluster](cs_cli_install.html#cs_cli_configure).
*  Familiarisez-vous avec l'utilisation des rôles RBAC. Pour plus d'informations, voir [Autorisation des utilisateurs avec des rôles RBAC Kubernetes personnalisés](cs_users.html#rbac) ou la [documentation de Kubernetes ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe")](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#api-overview).
*  **Remarque** : lorsque vous modifiez la configuration par défaut, vous pouvez empêcher l'exécution d'actions importantes sur le cluster, comme par exemple les déploiements de pod ou les mises à jour de cluster. Testez vos modifications dans un cluster hors production qui n'est pas utilisé par d'autres équipes.

**Pour modifier les ressources RBAC** :
1.  Obtenez le nom de la liaison de rôle de cluster RBAC.
    ```
    kubectl get clusterrolebinding
    ```
    {: pre}
    
2.  Téléchargez cette liaison sous forme de fichier `.yaml` que vous pourrez éditer localement.
    
    ```
    kubectl get clusterrolebinding privileged-psp-user -o yaml > privileged-psp-user.yaml
    ```
    {: pre}
    
    Vous souhaitez peut-être sauvegarder une copie de la politique existante de sorte à pouvoir la rétablir si la politique modifiée produit des résultats inattendus.
    {: tip}
    
    **Exemple de fichier de liaison de rôle de cluster**:
    
    ```yaml
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRoleBinding
    metadata:
      creationTimestamp: 2018-06-20T21:19:50Z
      name: privileged-psp-user
      resourceVersion: "1234567"
      selfLink: /apis/rbac.authorization.k8s.io/v1/clusterrolebindings/privileged-psp-user
      uid: aa1a1111-11aa-11a1-aaaa-1a1111aa11a1
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: ClusterRole
      name: ibm-privileged-psp-user
    subjects:
    - apiGroup: rbac.authorization.k8s.io
      kind: Group
      name: system:masters
    - apiGroup: rbac.authorization.k8s.io
      kind: Group
      name: system:nodes
    - apiGroup: rbac.authorization.k8s.io
      kind: Group
      name: system:serviceaccounts
    - apiGroup: rbac.authorization.k8s.io
      kind: Group
      name: system:authenticated
    ```
    {: codeblock}
    
3.  Editez le fichier `.yaml` de liaison de rôle de cluster. Pour comprendre ce que vous pouvez éditer, consultez la [documentation Kubernetes ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/policy/pod-security-policy/). Exemples d'actions :
    
    *   **Compte de service** : vous envisagez peut-être d'autoriser des compte de services pour que les déploiements soient possibles uniquement dans des espaces de nom particuliers. Par exemple, si vous délimitez la politique pour autoriser des actions dans l'espace de nom `kube-system`, de nombreuses actions importantes, telles que les mises à jour de cluster sont possibles. Toutefois, les actions intervenant dans d'autres espaces de nom ne sont plus autorisées. 
    
        Pour que la politique autorise des actions dans un espace de nom spécifique, remplacez `system:serviceaccounts` par `system:serviceaccount:<namespace>`.
        ```yaml
        - apiGroup: rbac.authorization.k8s.io
          kind: Group
          name: system:serviceaccount:kube-system
        ```
        {: codeblock}
  
    *   **Utilisateurs** : vous pouvez être amené à retirer à tous les utilisateurs authentifiés l'autorisation de déployer des pods à accès privilégié. Supprimez l'entrée `system:authenticated`.
        ```yaml
        - apiGroup: rbac.authorization.k8s.io
          kind: Group
          name: system:authenticated
        ```
        {: codeblock}

4.  Créez la ressource de liaison de rôle de cluster modifiée dans votre cluster.

    ```
    kubectl apply -f privileged-psp-user.yaml
    ```
    {: pre}
    
5.  Vérifiez que la ressource a été modifiée.

    ```
    kubectl get clusterrolebinding privileged-psp-user -o yaml
    ```
    {: pre}

</br>
**Pour supprimer les ressources RBAC** :
1.  Obtenez le nom de la liaison de rôle de cluster RBAC.
    ```
    kubectl get clusterrolebinding
    ```
    {: pre}

2.  Supprimez le rôle RBAC que vous désirez retirer.
    ```
    kubectl delete clusterrolebinding privileged-psp-user
    ```
    {: pre}
    
3.  Vérifiez que la liaison de rôle de cluster RBAC ne figure plus dans votre cluster.
    ```
    kubectl get clusterrolebinding
    ```
    {: pre}

</br>
**Pour créer votre propre politique de sécurité de pod** :</br>
Pour créer votre propre ressource de politique de sécurité de pod et autoriser les utilisateurs avec le contrôle d'accès RBAC, consultez la [documentation de Kubernetes  ![Icône de lien externe](../icons/launch-glyph.svg "Icône de lien externe")](https://kubernetes.io/docs/concepts/policy/pod-security-policy/). 

Veillez lorsque vous avez modifié des politiques existantes à ce que la nouvelle politique que vous créez n'entre pas en conflit avec une politique existante. Prenons l'exemple d'une politique existante autorisant les utilisateurs à créer et à mettre à jour des pods privilégiés : si vous créez une politique qui n'autorise pas les utilisateurs à créer ou mettre à jour des pods privilégiés, le conflit entre la politique existante et la nouvelle politique peut provoquer des résultats inattendus.

## Description des ressources par défaut pour la gestion de cluster {{site.data.keyword.IBM_notm}}
{: #ibm_psp}

Votre cluster Kubernetes dans {{site.data.keyword.containerlong_notm}} contient les politiques
de sécurité de pod et des ressources RBAC associées pour permettre à {{site.data.keyword.IBM_notm}} gérer correctement votre cluster.
{: shortdesc}

Les ressources par défaut RBAC `privileged-psp-user` et `restricted-psp-user` font référence à des politiques de sécurité de pod définies par {{site.data.keyword.IBM_notm}}. 

**Attention** : vous ne devez pas supprimer ou modifier ces ressources.

| Nom | Espace de nom | Type | Fonction |
|---|---|---|---|
| `ibm-privileged-psp` | cluster | `PodSecurityPolicy` | Politique permettant la création de pods privilégiés. |
| `ibm-privileged-psp-user` | cluster | `ClusterRole` | Rôle de cluster autorisant l'utilisation de la politique de sécurité de pod `ibm-privileged-psp`. |
| `ibm-privileged-psp-user` | `kube-system` | `RoleBinding` | Permet aux administrateurs de cluster, aux comptes de service et aux noeuds d'utiliser la politique de sécurité de pod `ibm-privileged-psp` dans l'espace de nom `kube-system`. |
| `ibm-privileged-psp-user` | `ibm-system` | `RoleBinding` | Permet aux administrateurs de cluster, aux comptes de service et aux noeuds d'utiliser la politique de sécurité de pod `ibm-privileged-psp` dans l'espace de nom `ibm-system`. |
| `ibm-privileged-psp-user` | `kubx-cit` | `RoleBinding` | Permet aux administrateurs de cluster, aux comptes de service et aux noeuds d'utiliser la politique de sécurité de pod `ibm-privileged-psp` dans l'espace de nom `kubx-cit`. |
| `ibm-restricted-psp` | cluster | `PodSecurityPolicy` | Politique permettant la création de pod non privilégiés ou restreints. |
| `ibm-restricted-psp-user` | cluster | `ClusterRole` | Rôle de cluster autorisant l'utilisation de la politique de sécurité de pod `ibm-restricted-psp`. |
{: caption="Ressources de politique de sécurité de pod IBM que vous ne pouvez pas modifier" caption-side="top"}

---
title: Limitare l'accesso a kubeconfig nel servizio Azure Kubernetes
description: Informazioni su come controllare l'accesso al file di configurazione di Kubernetes (kubeconfig) per gli amministratori e gli utenti di cluster
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: b55cc226cfbb462cdccd73b3b80cfb0d56c10711
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475605"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Usare il controllo degli accessi in base al ruolo di Azure per definire l'accesso al file di configurazione di Kubernetes nel servizio Azure Kubernetes

È possibile interagire con i cluster Kubernetes tramite lo strumento `kubectl`. L'interfaccia della riga di comando di Azure offre un modo semplice per ottenere le credenziali di accesso e le informazioni di configurazione per la connessione ai cluster del servizio Azure Kubernetes tramite `kubectl`. Per limitare gli utenti che possono ottenere tali informazioni di configurazione di Kubernetes (*kubeconfig*) e le autorizzazioni assegnate, è possibile usare il controllo degli accessi in base al ruolo di Azure (RBAC).

Questo articolo illustra come assegnare ruoli di controllo degli accessi in base al ruolo che limitano gli utenti in grado di ottenere le informazioni di configurazione per un cluster del servizio Azure Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster AKS esistente. Se è necessario un cluster servizio Azure Kubernetes, vedere la Guida introduttiva su servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

Questo articolo richiede anche di eseguire il comando di Azure versione 2.0.65 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Autorizzazioni per i ruoli del cluster disponibili

Quando si interagisce con un cluster del servizio Azure Kubernetes tramite lo strumento `kubectl`, viene usato un file di configurazione che definisce le informazioni di connessione del cluster. Questo file di configurazione è in genere archiviato in *~/.kube/config*. In questo file *kubeconfig* possono essere definiti più cluster. Per passare da un cluster a un altro, si usa il comando [kubectl config use-context][kubectl-config-use-context].

Il comando [az aks get-credentials][az-aks-get-credentials] consente di ottenere le credenziali di accesso per un cluster del servizio Azure Kubernetes e di unirle nel file *kubeconfig*. È possibile usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso a queste credenziali. Questi ruoli del controllo degli accessi in base al ruolo di Azure consentono di definire chi può recuperare il file *kubeconfig* e le autorizzazioni disponibili all'interno del cluster.

I due ruoli predefiniti sono:

* **Ruolo di amministratore del cluster del servizio Azure Kubernetes**  
    * Consente l'accesso alla chiamata API *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action*. Questa chiamata API [elenca le credenziali di amministratore del cluster][api-cluster-admin].
    * Scarica *kubeconfig* per il ruolo *clusterAdmin*.
* **Ruolo di utente del cluster del servizio Azure Kubernetes**
    * Consente l'accesso alla chiamata API *Microsoft.ContainerService/managedClusters/listClusterUserCredential/action*. Questa chiamata API [elenca le credenziali di utente del cluster][api-cluster-user].
    * Scarica *kubeconfig* per il ruolo *clusterUser*.

Questi ruoli RBAC possono essere applicati a un utente di Azure Active Directory (AD) o un gruppo.

## <a name="assign-role-permissions-to-a-user-or-group"></a>Assegnare le autorizzazioni del ruolo a un utente o gruppo

Per assegnare uno dei ruoli disponibili, è necessario ottenere l'ID risorsa del cluster servizio contenitore di AZURE e l'ID dell'account utente Azure AD o del gruppo. I comandi nell'esempio seguente:

* Ottenere l'ID di risorsa cluster con il [show di az aks] [ az-aks-show] comando per il cluster denominato *myAKSCluster* nel *myResourceGroup* gruppo di risorse. Specificare il nome del cluster e del gruppo di risorse in base alle esigenze.
* Usa il [show account az] [ az-account-show] e [show utente di ad az] [ az-ad-user-show] comandi per ottenere l'ID utente.
* Infine, assegnare un ruolo tramite il comando [az role assignment create][az-role-assignment-create].

L'esempio seguente assegna il *ruolo di amministratore Cluster Azure Kubernetes Service* per un singolo account utente:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --upn-or-object-id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!TIP]
> Se si desidera assegnare autorizzazioni a un gruppo di Azure AD, aggiornare il `--assignee` parametro illustrato nell'esempio precedente con l'ID oggetto per il *gruppo* anziché un *utente*. Per ottenere l'ID oggetto per un gruppo, usare il [show di gruppo ad az] [ az-ad-group-show] comando. L'esempio seguente ottiene l'ID oggetto per il gruppo di Azure AD denominato *appdev*: `az ad group show --group appdev --query objectId -o tsv`

È possibile modificare l'assegnazione precedente specificando il *Ruolo utente del cluster* in base alle esigenze.

L'output di esempio seguente mostra che l'assegnazione di ruolo è stata creata correttamente:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>Ottenere e verificare le informazioni di configurazione

Dopo aver assegnato i ruoli di controllo degli accessi in base al ruolo, usare il comando [az aks get-credentials][az-aks-get-credentials] per ottenere la definizione *kubeconfig* per il cluster del servizio Azure Kubernetes. L'esempio seguente ottiene le credenziali *--admin*, che funzionano correttamente se all'utente è stato assegnato il *Ruolo amministratore del cluster*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

È quindi possibile usare il comando [kubectl config view][kubectl-config-view] per verificare che le informazioni *context* per il cluster indichino che sono state applicate le informazioni di configurazione per l'amministratore:

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>Rimuovere le autorizzazioni per i ruoli

Per rimuovere le assegnazioni di ruolo, usare il comando [az role assignment delete][az-role-assignment-delete]. Specificare l'ID account e ID risorsa del cluster ottenute nei comandi precedenti. Se è stato assegnato il ruolo a un gruppo anziché a un utente, specificare l'oggetto di gruppo appropriato ID anziché account ID oggetto per il `--assignee` parametro:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Passaggi successivi

Per aumentare il livello di sicurezza dell'accesso ai cluster del servizio Azure Kubernetes, [integrare l'autenticazione di Azure Active Directory][aad-integration].

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: azure-ad-integration.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show

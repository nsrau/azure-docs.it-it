---
title: Controllare le risorse cluster con RBAC e Azure AD nel servizio Azure KubernetesControl cluster resources with RBAC and Azure AD in Azure Kubernetes Service
description: Informazioni su come usare l'appartenenza ai gruppi di Azure Active Directory per limitare l'accesso alle risorse del cluster usando il controllo degli accessi in base al ruolo nel servizio Azure Kubernetes (AKS)Learn how to use Azure Active Directory group membership to restrict access to cluster resources using role-based access control (RBAC) in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: 456b6dcdd590b48e06c830db85b726d4bebb69e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596522"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Control access to cluster resources using role-based access control and Azure Active Directory identities in Azure Kubernetes Service

Il servizio Azure Kubernetes può essere configurato per usare Azure Active Directory (AD) per l'autenticazione utente. In questa configurazione si accede a un cluster AKS usando un token di autenticazione di Azure AD. È inoltre possibile configurare il controllo degli accessi in base al ruolo Kubernetes per limitare l'accesso alle risorse del cluster in base all'identità o all'appartenenza al gruppo di un utente.

Questo articolo illustra come usare l'appartenenza ai gruppi di Azure AD per controllare l'accesso agli spazi dei nomi e alle risorse cluster usando il controllo degli accessi in base al ruolo Kubernetes in un cluster AKS. I gruppi e gli utenti di esempio vengono creati in Azure AD, quindi I ruoli e i RoleBindings vengono creati nel cluster AKS per concedere le autorizzazioni appropriate per creare e visualizzare le risorse.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che tu abbia un cluster AKS esistente abilitato con l'integrazione di Azure AD. Se è necessario un cluster AKS, vedere [Integrare Azure Active Directory con AKS][azure-ad-aks-cli].

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.61 o successiva installata e configurata. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If][install-azure-cli]you need to install or upgrade, see Install Azure CLI.

## <a name="create-demo-groups-in-azure-ad"></a>Creare gruppi dimostrativi in Azure ADCreate demo groups in Azure AD

In questo articolo vengono creati due ruoli utente che possono essere usati per mostrare in che modo Kubernetes RBAC e Azure AD controllano l'accesso alle risorse del cluster. Vengono utilizzati i due ruoli di esempio seguenti:The following two example roles are used:

* **Sviluppatore di applicazioni**
    * Un utente denominato *aksdev* che fa parte del gruppo *appdev.*
* **Tecnico dell'affidabilità del sito**
    * Un utente denominato *akssre* che fa parte del gruppo *opssre.*

Negli ambienti di produzione è possibile usare utenti e gruppi esistenti all'interno di un tenant di Azure AD.

Ottenere innanzitutto l'ID risorsa del cluster AKS usando il comando [az aks show.][az-aks-show] Assegnare l'ID risorsa a una variabile denominata *AKS_ID* in modo che sia possibile farvi riferimento nei comandi aggiuntivi.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Creare il primo gruppo di esempio in Azure AD per gli sviluppatori di applicazioni usando il comando [az ad group create.][az-ad-group-create] Nell'esempio seguente viene creato un gruppo denominato *appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

A questo punto, creare un'assegnazione di ruolo di Azure per il gruppo *appdev* usando il comando [az role assignment create.][az-role-assignment-create] Questa assegnazione consente a `kubectl` qualsiasi membro del gruppo di interagire con un cluster AKS concedendo loro il ruolo utente del cluster di servizi di *Azure Kubernetes*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Se viene visualizzato un `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`errore, ad esempio , attendere alcuni secondi per la propagazione dell'ID oggetto gruppo di Azure AD nella directory, quindi riprovare a eseguire il `az role assignment create` comando.

Creare un secondo gruppo di esempio, questo per gli SRE denominati *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Creare nuovamente un'assegnazione di ruolo di Azure per concedere ai membri del gruppo il ruolo utente del cluster di servizi di *Azure Kubernetes:*

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Creare utenti demo in Azure ADCreate demo users in Azure AD

Con due gruppi di esempio creati in Azure AD per gli sviluppatori di applicazioni e sRE, ora consente di creare due utenti di esempio. Per testare l'integrazione RBAC alla fine dell'articolo, accedere al cluster AKS con questi account.

Creare il primo account utente in Azure AD usando il comando [az ad user create.][az-ad-user-create]

Nell'esempio riportato di seguito viene creato un utente con il `aksdev@contoso.com`nome visualizzato *AKS Dev* e il nome dell'entità utente (UPN) di . Aggiornare l'UPN per includere un dominio verificato per il tenant di Azure `--password` AD (sostituire contoso.com con il proprio dominio) e fornire le proprie credenziali sicure:Update the UPN to include a verified domain for your Azure AD tenant (replace *contoso.com* with your own domain), and provide your own secure credential:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Ora aggiungi l'utente al gruppo *appdev* creato nella sezione precedente utilizzando il comando [az ad group add:][az-ad-group-member-add]

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Creare un secondo account utente. Nell'esempio seguente viene creato un utente con il nome visualizzato *AKS SRE* e il nome dell'entità utente (UPN) di `akssre@contoso.com`. Anche in questo caso, aggiornare l'UPN per includere un dominio verificato per `--password` il tenant di Azure AD (sostituire contoso.com con il proprio dominio) e fornire le proprie credenziali sicure:Again, update the UPN to include a verified domain for your Azure AD tenant (replace *contoso.com* with your own domain), and provide your own secure credential:

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name akssre@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Creare le risorse cluster AKS per gli sviluppatori di appCreate the AKS cluster resources for app devs

I gruppi e gli utenti di Azure AD sono stati creati. Sono state create assegnazioni di ruolo di Azure per i membri del gruppo per connettersi a un cluster AKS come utente normale. A questo punto, è possibile configurare il cluster AKS per consentire a questi diversi gruppi l'accesso a risorse specifiche.

Ottenere innanzitutto le credenziali di amministratore del cluster usando il comando [az aks get-credentials.][az-aks-get-credentials] In una delle sezioni seguenti si ottengono le normali credenziali del cluster *di utenti* per visualizzare il flusso di autenticazione di Azure AD in azione.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Creare uno spazio dei nomi nel cluster AKS usando il comando [kubectl create namespace.][kubectl-create] L'esempio seguente crea un nome dello spazio dei nomi *dev:*

```console
kubectl create namespace dev
```

In Kubernetes, *i ruoli* definiscono le autorizzazioni da concedere e *RoleBindings* le applica agli utenti o ai gruppi desiderati. Queste assegnazioni possono essere applicate a uno spazio dei nomi specifico o all'intero cluster. Per altre informazioni, vedere [Uso di autorizzazioni del controllo degli accessi in base al ruolo][rbac-authorization].

Creare innanzitutto un ruolo per lo spazio dei nomi *dev.* Questo ruolo concede autorizzazioni complete allo spazio dei nomi. Negli ambienti di produzione, è possibile specificare autorizzazioni più granulari per utenti o gruppi diversi.

Creare un `role-dev-namespace.yaml` file denominato e incollare il seguente manifesto YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Creare il ruolo utilizzando il comando [kubectl apply][kubectl-apply] e specificare il nome del file del manifesto YAML:

```console
kubectl apply -f role-dev-namespace.yaml
```

Successivamente, ottieni l'ID risorsa per il gruppo *appdev* utilizzando il comando [az ad group show.][az-ad-group-show] Questo gruppo viene impostato come oggetto di un RoleBinding nel passaggio successivo.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

A questo punto, creare un RoleBinding per il gruppo *appdev* per utilizzare il ruolo creato in precedenza per l'accesso allo spazio dei nomi. Creare un file denominato `rolebinding-dev-namespace.yaml` e incollare il manifesto YAML seguente. Nell'ultima riga sostituire *groupObjectId* con l'output dell'ID oggetto gruppo del comando precedente:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

Creare RoleBinding utilizzando il comando [kubectl apply][kubectl-apply] e specificare il nome del file del manifesto YAML:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Creare le risorse cluster AKS per le sRI

Ripetere ora i passaggi precedenti per creare uno spazio dei nomi, Role e RoleBinding per gli SRE.

Creare innanzitutto uno spazio dei nomi per *sre* usando il comando kubectl create namespace:First, create a namespace for sre using the [kubectl create namespace][kubectl-create] command:

```console
kubectl create namespace sre
```

Creare un `role-sre-namespace.yaml` file denominato e incollare il seguente manifesto YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Creare il ruolo utilizzando il comando [kubectl apply][kubectl-apply] e specificare il nome del file del manifesto YAML:

```console
kubectl apply -f role-sre-namespace.yaml
```

Ottenere l'ID risorsa per il gruppo *opssre* utilizzando il comando [az ad group show:][az-ad-group-show]

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Creare un RoleBinding per il gruppo *opssre* per utilizzare il ruolo creato in precedenza per l'accesso allo spazio dei nomi. Creare un file denominato `rolebinding-sre-namespace.yaml` e incollare il manifesto YAML seguente. Nell'ultima riga sostituire *groupObjectId* con l'output dell'ID oggetto gruppo del comando precedente:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

Creare RoleBinding utilizzando il comando [kubectl apply][kubectl-apply] e specificare il nome del file del manifesto YAML:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interagire con le risorse del cluster usando le identità di Azure ADInteract with cluster resources using Azure AD identities

A questo punto, è possibile testare il lavoro delle autorizzazioni previste quando si creano e si gestiscono le risorse in un cluster AKS. In questi esempi si pianificano e si visualizzano i pod nello spazio dei nomi assegnato dall'utente. Quindi, si tenta di pianificare e visualizzare i pod al di fuori dello spazio dei nomi assegnato.

In primo luogo, reimpostare il contesto *kubeconfig* utilizzando il comando [az aks get-credentials.][az-aks-get-credentials] In una sezione precedente si imposta il contesto utilizzando le credenziali di amministratore del cluster. L'utente amministratore ignora le richieste di accesso di Azure AD. Senza `--admin` il parametro, viene applicato il contesto utente che richiede l'autenticazione di tutte le richieste tramite Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Pianificate un pod NGINX di base usando il comando [kubectl run][kubectl-run] nello spazio dei nomi *dev:*

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

Come prompt di accesso, immettere le `appdev@contoso.com` credenziali per il proprio account creato all'inizio dell'articolo. Una volta effettuato l'accesso, il token `kubectl` dell'account viene memorizzato nella cache per i comandi futuri. Il NGINX è pianificato correttamente, come illustrato nell'output di esempio seguente:The NGINX is successfully schedule, as shown in the following example output:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Ora usate il comando [kubectl get pods][kubectl-get] per visualizzare i pod nello spazio dei nomi *dev.*

```console
kubectl get pods --namespace dev
```

Come illustrato nell'output di esempio seguente, il pod NGINX è *in esecuzione correttamente:*

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Creare e visualizzare le risorse cluster all'esterno dello spazio dei nomi assegnatoCreate and view cluster resources outside of the assigned namespace

A questo punto provare a visualizzare i pod all'esterno dello spazio dei nomi *dev.* Usate di nuovo il comando [kubectl get pods,][kubectl-get] questa volta per vedere `--all-namespaces` come segue:

```console
kubectl get pods --all-namespaces
```

L'appartenenza al gruppo dell'utente non dispone di un ruolo Kubernetes che consente questa azione, come illustrato nell'output di esempio seguente:The user's group membership does not have a Kubernetes Role that allows this action, as shown in the following example output:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Allo stesso modo, provare a pianificare un pod in uno spazio dei nomi diverso, ad esempio lo spazio dei nomi *sre.* L'appartenenza al gruppo dell'utente non è allineata con un ruolo Kubernetes e RoleBinding per concedere queste autorizzazioni, come illustrato nell'output di esempio seguente:The user's group membership does not align with a Kubernetes Role and RoleBinding to grant these permissions, as shown in the following example output:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Testare l'accesso SRE alle risorse cluster AKSTest the SRE access to the AKS cluster resources

Per verificare che l'appartenenza al gruppo di Azure AD e il controllo degli accessi in base al ruolo Kubernetes funzionino correttamente tra utenti e gruppi diversi, provare i comandi precedenti dopo aver eseguito l'accesso come utente *opssre.*

Reimpostare il contesto *kubeconfig* usando il comando [az aks get-credentials][az-aks-get-credentials] che cancella il token di autenticazione precedentemente memorizzato nella cache per l'utente *aksdev:*

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Provare a pianificare e visualizzare i pod nello spazio dei nomi *sre* assegnato. Quando richiesto, accedere con `opssre@contoso.com` le proprie credenziali create all'inizio dell'articolo:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Come illustrato nell'output di esempio seguente, potete creare e visualizzare correttamente i pod:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Ora, provate a visualizzare o pianificare i pod all'esterno dello spazio dei nomi SRE assegnato:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

Questi `kubectl` comandi hanno esito negativo, come illustrato nell'output di esempio seguente. L'appartenenza al gruppo dell'utente e Kubernetes Role e RoleBindings non concedono autorizzazioni per creare o gestire risorse in altri spazi dei nomi:The user's group membership and Kubernetes Role and RoleBindings don't grant permissions to create or manager resources in other namespaces:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Pulire le risorse

In questo articolo sono state create risorse nel cluster AKS e utenti e gruppi in Azure AD. Per pulire tutte queste risorse, eseguire i comandi seguenti:To clean up all these resources, run the following commands:

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su come proteggere i cluster Kubernetes, vedere Opzioni di [accesso e identità per AKS).][rbac-authorization]

Per le procedure consigliate per il controllo delle identità e delle risorse, vedere [Procedure consigliate per l'autenticazione e l'autorizzazione in AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md

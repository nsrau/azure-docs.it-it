---
title: Controllo delle risorse cluster con RBAC e Azure AD in Azure Kubernetes Service
description: Informazioni su come usare l'appartenenza al gruppo di Azure Active Directory per limitare l'accesso alle risorse del cluster usando il controllo di accesso basato sui ruoli (RBAC) in Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: e974c47d1dfb04f66b622c64a7143d00de87c4cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467545"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Controllare l'accesso alle risorse del cluster con controllo degli accessi basata sui ruoli e le identità di Azure Active Directory in Azure Kubernetes Service

Il servizio Azure Kubernetes può essere configurato per usare Azure Active Directory (AD) per l'autenticazione utente. In questa configurazione, accedi a un cluster AKS usando un token di autenticazione di Azure AD. È anche possibile configurare Kubernetes il controllo di accesso basato sui ruoli (RBAC) per limitare l'accesso alle risorse del cluster in base a un utente identità o appartenenza al gruppo.

Questo articolo illustra come usare l'appartenenza al gruppo di Azure AD per controllare l'accesso agli spazi dei nomi e le risorse tramite RBAC Kubernetes in un cluster servizio contenitore di AZURE del cluster. Utenti e gruppi di esempio vengono creati in Azure AD, quindi vengono creati i ruoli e RoleBindings del cluster servizio contenitore di AZURE per concedere le autorizzazioni appropriate per creare e visualizzare le risorse.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si dispone di un cluster servizio contenitore di AZURE esistente abilitato con l'integrazione di Azure AD. Se è necessario un cluster del servizio contenitore di AZURE, vedere [integrare Azure Active Directory con AKS][azure-ad-aks-cli].

È necessario la CLI di Azure versione 2.0.61 o versione successiva installato e configurato. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Creare gruppi di demo di Azure AD

In questo articolo, è possibile creare due ruoli utente che possono essere utilizzati per mostrare come Kubernetes RBAC e Azure AD controllare l'accesso alle risorse del cluster. Vengono usati i ruoli di due esempi seguenti:

* **Sviluppatore di applicazioni**
    * Un utente denominato *aksdev* che fa parte delle *appdev* gruppo.
* **Tecnico affidabilità del sito**
    * Un utente denominato *akssre* che fa parte delle *opssre* gruppo.

Negli ambienti di produzione, è possibile utilizzare utenti e gruppi all'interno di un tenant di Azure AD esistenti.

Innanzitutto, ottenere l'ID risorsa del cluster AKS usando il [show di az aks] [ az-aks-show] comando. Assegnare l'ID di risorsa a una variabile denominata *AKS_ID* in modo che è possibile farvi riferimento in altri comandi.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Creare il primo gruppo di esempio in Azure AD per gli sviluppatori di applicazioni usando il [creare il gruppo di Active Directory az] [ az-ad-group-create] comando. L'esempio seguente crea un gruppo denominato *appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

A questo punto, creare un'assegnazione di ruolo di Azure per il *appdev* raggruppare utilizzando il [creazione dell'assegnazione di ruolo az] [ az-role-assignment-create] comando. Questa assegnazione consente a qualsiasi membro del gruppo di usare `kubectl` per interagire con un cluster del servizio contenitore di AZURE concedendo il *il ruolo utente del Cluster di Azure Kubernetes Service*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Se si riceve un errore, ad esempio `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`, attendere alcuni secondi per l'ID oggetto gruppo di Azure AD per propagarsi attraverso la directory, quindi ripetere il `az role assignment create` nuovo il comando.

Creare un secondo gruppo di esempio, denominato questo uno per SREs *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Anche in questo caso, creare un'assegnazione di ruolo di Azure per consentire ai membri del gruppo di *ruolo di utente del Cluster di Azure Kubernetes Service*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Creare utenti demo di Azure AD

Con due gruppi di esempio creati in Azure AD per gli sviluppatori di applicazioni e SREs, ora consente di creare due utenti di esempio. Per testare l'integrazione di RBAC alla fine dell'articolo, è accedere al cluster AKS con questi account.

Creare il primo account utente in Azure AD usando la [Crea utente ad az] [ az-ad-user-create] comando.

L'esempio seguente crea un utente con il nome visualizzato *AKS Dev* e il nome dell'entità utente (UPN) del `aksdev@contoso.com`. Aggiornare il nome UPN per includere un dominio verificato per il tenant di Azure AD (sostituire *contoso.com* con il proprio dominio) e fornire il proprio sicura `--password` credential:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Ora aggiungere l'utente per il *appdev* gruppo creato nella sezione precedente usando la [Aggiungi membro del gruppo ad az] [ az-ad-group-member-add] comando:

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Creare un secondo account utente. L'esempio seguente crea un utente con il nome visualizzato *AKS SRE* e il nome dell'entità utente (UPN) del `akssre@contoso.com`. Anche in questo caso, aggiornare il nome UPN per includere un dominio verificato per il tenant di Azure AD (sostituire *contoso.com* con il proprio dominio) e fornire il proprio sicura `--password` credential:

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

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Creare le risorse del cluster servizio contenitore di AZURE per gli sviluppatori di app

I gruppi di Azure AD e gli utenti vengono ora creati. Le assegnazioni di ruolo di Azure sono state create per i membri del gruppo per la connessione a un cluster del servizio contenitore di AZURE come utente normale. A questo punto, è possibile configurare il cluster servizio contenitore di AZURE per consentire a questi gruppi diversi l'accesso a risorse specifiche.

Innanzitutto, ottenere il cluster usando le credenziali di amministratore di [az aks get-credentials] [ az-aks-get-credentials] comando. In una delle sezioni seguenti, si ottiene normali *utente* cluster le credenziali per l'autenticazione di Azure AD di visualizzare il flusso in azione.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Creare uno spazio dei nomi nel cluster AKS usando il [kubectl create dello spazio dei nomi] [ kubectl-create] comando. L'esempio seguente crea uno spazio dei nomi *dev*:

```console
kubectl create namespace dev
```

In Kubernetes *ruoli* definire le autorizzazioni da concedere, e *RoleBindings* applicarle a gruppi o utenti desiderati. Queste assegnazioni possono essere applicate a uno spazio dei nomi specifico o all'intero cluster. Per altre informazioni, vedere [Uso di autorizzazioni del controllo degli accessi in base al ruolo][rbac-authorization].

In primo luogo, creare un ruolo per il *dev* dello spazio dei nomi. Questo ruolo concede le autorizzazioni complete per lo spazio dei nomi. Negli ambienti di produzione, è possibile specificare autorizzazioni più granulari per diversi utenti o gruppi.

Creare un file denominato `role-dev-namespace.yaml` e incollare il manifesto YAML seguente:

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

Creare il ruolo tramite il [kubectl applicare] [ kubectl-apply] comando e specificare il nome del file del manifesto del YAML:

```console
kubectl apply -f role-dev-namespace.yaml
```

Successivamente, ottenere l'ID di risorsa per il *appdev* gruppo usando la [show di gruppo ad az] [ az-ad-group-show] comando. Questo gruppo viene impostato come oggetto di un RoleBinding nel passaggio successivo.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

A questo punto, creare un RoleBinding per il *appdev* gruppo da usare il ruolo creato in precedenza per l'accesso dello spazio dei nomi. Creare un file denominato `rolebinding-dev-namespace.yaml` e incollare il manifesto YAML seguente. L'ultima riga, sostituire *groupObjectId* con l'ID di oggetto gruppo restituito dal comando precedente:

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

Creare il RoleBinding usando il [kubectl applicare] [ kubectl-apply] comando e specificare il nome del file del manifesto del YAML:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Creare le risorse del cluster servizio contenitore di AZURE per SREs

A questo punto, ripetere i passaggi precedenti per creare un spazio dei nomi, il ruolo e RoleBinding per il SREs.

Innanzitutto, creare uno spazio dei nomi per *sre* usando la [kubectl creare lo spazio dei nomi] [ kubectl-create] comando:

```console
kubectl create namespace sre
```

Creare un file denominato `role-sre-namespace.yaml` e incollare il manifesto YAML seguente:

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

Creare il ruolo tramite il [kubectl applicare] [ kubectl-apply] comando e specificare il nome del file del manifesto del YAML:

```console
kubectl apply -f role-sre-namespace.yaml
```

Ottenere l'ID di risorsa per il *opssre* gruppo usando la [show di gruppo ad az] [ az-ad-group-show] comando:

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Creare un RoleBinding per il *opssre* gruppo da usare il ruolo creato in precedenza per l'accesso dello spazio dei nomi. Creare un file denominato `rolebinding-sre-namespace.yaml` e incollare il manifesto YAML seguente. L'ultima riga, sostituire *groupObjectId* con l'ID di oggetto gruppo restituito dal comando precedente:

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

Creare il RoleBinding usando il [kubectl applicare] [ kubectl-apply] comando e specificare il nome del file del manifesto del YAML:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interagire con le risorse del cluster usando le identità di Azure AD

A questo punto, è possibile testare il lavoro delle autorizzazioni previste quando si crea e gestiscono le risorse in un cluster AKS. In questi esempi, si pianifica e visualizzare i POD nello spazio dei nomi assegnati dell'utente. Quindi, si tenta di pianificare e visualizzare i POD all'esterno dello spazio dei nomi assegnato.

In primo luogo, reimpostare il *kubeconfig* contesto utilizzando il [az aks get-credentials] [ az-aks-get-credentials] comando. Nella sezione precedente, impostare il contesto utilizzando le credenziali di amministratore cluster. L'utente amministratore ignora la richieste di accesso Azure AD. Senza il `--admin` applicato parametro, il contesto dell'utente che richiede tutte le richieste vengano autenticate tramite Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Pianificazione di un pod NGINX base mediante il [kubectl eseguiti] [ kubectl-run] comando il *dev* dello spazio dei nomi:

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

Come la richiesta di accesso, immettere le credenziali per il proprio `appdev@contoso.com` account creato all'inizio dell'articolo. Dopo che l'accesso, viene memorizzato nella cache il token dell'account per futuro `kubectl` comandi. Il NGINX sia pianificare correttamente, come illustrato nell'output di esempio seguente:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

A questo punto usare il [kubectl get pods] [ kubectl-get] comando per visualizzare i POD nel *dev* dello spazio dei nomi.

```console
kubectl get pods --namespace dev
```

Come illustrato nell'output di esempio seguente, il pod NGINX è stata *in esecuzione*:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Creazione e visualizzazione di risorse del cluster all'esterno dello spazio dei nomi assegnato

Ora provare a visualizzare i POD di fuori del *dev* dello spazio dei nomi. Usare la [kubectl get pods] [ kubectl-get] questa fase per visualizzare di nuovo, il comando `--all-namespaces` come indicato di seguito:

```console
kubectl get pods --all-namespaces
```

L'appartenenza al gruppo dell'utente non ha un ruolo di Kubernetes che consenta di questa azione, come illustrato nell'output di esempio seguente:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Nello stesso modo, provare a pianificare un pod nello spazio dei nomi diversi, ad esempio la *sre* dello spazio dei nomi. L'appartenenza al gruppo dell'utente non è coerente con un ruolo di Kubernetes e RoleBinding concedere queste autorizzazioni, come illustrato nell'output di esempio seguente:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Testare l'accesso SRE per le risorse del cluster servizio contenitore di AZURE

Per verificare che l'appartenenza al gruppo di Windows Azure e Kubernetes RBAC funzionino correttamente tra diversi utenti e gruppi, provare i comandi precedenti quando è connesso come il *opssre* utente.

Reimpostare il *kubeconfig* contesto utilizzando il [az aks get-credentials] [ az-aks-get-credentials] comando che consente di cancellare il token di autenticazione precedentemente memorizzata nella cache per il *aksdev*  utente:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Provare a pianificare e visualizzare i POD nell'oggetto assegnato *sre* dello spazio dei nomi. Quando richiesto, accedere con il proprio `opssre@contoso.com` credenziali create all'inizio dell'articolo:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Come illustrato nell'output di esempio seguente, è possibile creare correttamente e visualizza il numero di POD:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

A questo punto, provare a visualizzare o pianificare i POD all'esterno di spazio dei nomi SRE assegnato:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

Questi `kubectl` comandi hanno esito negativo, come illustrato nell'output di esempio seguente. L'appartenenza al gruppo dell'utente e ruolo di Kubernetes e RoleBindings non concedere autorizzazioni per la creazione o Gestione risorse in altri spazi dei nomi:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Pulire le risorse

In questo articolo, creato le risorse nel cluster AKS e gli utenti e gruppi in Azure AD. Per pulire tutte queste risorse, eseguire i comandi seguenti:

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

Per altre informazioni su come proteggere i cluster Kubernetes, vedere [opzioni di accesso e identità per AKS)][rbac-authorization].

Per le procedure consigliate sul controllo delle identità e risorse, vedere [procedure consigliate per l'autenticazione e autorizzazione nel servizio contenitore di AZURE][operator-best-practices-identity].

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

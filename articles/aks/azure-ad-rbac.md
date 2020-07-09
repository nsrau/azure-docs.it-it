---
title: Usare Azure AD e RBAC per i cluster
titleSuffix: Azure Kubernetes Service
description: Informazioni su come usare l'appartenenza a un gruppo Azure Active Directory per limitare l'accesso alle risorse del cluster usando il controllo degli accessi in base al ruolo (RBAC) in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: bb48e4f72506a69969cae39810640d23d771bde3
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86106085"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Controllare l'accesso alle risorse cluster usando il controllo degli accessi in base al ruolo e le identità del Azure Active Directory nel servizio Azure Kubernetes

Il servizio Azure Kubernetes può essere configurato per usare Azure Active Directory (AD) per l'autenticazione utente. In questa configurazione si accede a un cluster AKS usando un token di autenticazione Azure AD. È anche possibile configurare il controllo degli accessi in base al ruolo Kubernetes (RBAC) per limitare l'accesso alle risorse cluster in base all'identità o all'appartenenza a un gruppo di un utente.

Questo articolo illustra come usare l'appartenenza a un gruppo Azure AD per controllare l'accesso agli spazi dei nomi e alle risorse cluster usando Kubernetes RBAC in un cluster AKS. I gruppi e gli utenti di esempio vengono creati in Azure AD, quindi i ruoli e RoleBindings vengono creati nel cluster AKS per concedere le autorizzazioni appropriate per creare e visualizzare le risorse.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che sia disponibile un cluster AKS esistente abilitato con l'integrazione Azure AD. Se è necessario un cluster AKS, vedere [integrare Azure Active Directory con AKS][azure-ad-aks-cli].

È necessario che sia installata e configurata l'interfaccia della riga di comando di Azure 2.0.61 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Creare gruppi dimostrativi in Azure AD

In questo articolo verranno creati due ruoli utente che possono essere usati per illustrare il modo in cui Kubernetes RBAC e Azure AD controllare l'accesso alle risorse del cluster. Vengono utilizzati i due ruoli di esempio seguenti:

* **Sviluppatore di applicazioni**
    * Un utente denominato *aksdev* che fa parte del gruppo *AppDev* .
* **Ingegnere di affidabilità del sito**
    * Un utente denominato *akssre* che fa parte del gruppo *opssre* .

Negli ambienti di produzione è possibile usare utenti e gruppi esistenti in un tenant di Azure AD.

Per prima cosa, ottenere l'ID risorsa del cluster AKS usando il comando [AZ AKS Show][az-aks-show] . Assegnare l'ID risorsa a una variabile denominata *AKS_ID* in modo che sia possibile farvi riferimento in altri comandi.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Creare il primo gruppo di esempio in Azure AD per gli sviluppatori di applicazioni usando il comando [AZ ad Group create][az-ad-group-create] . Nell'esempio seguente viene creato un gruppo denominato *AppDev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

A questo punto, creare un'assegnazione di ruolo di Azure per il gruppo *AppDev* usando il comando [AZ Role Assignment create][az-role-assignment-create] . Questa assegnazione consente a tutti i membri del gruppo di `kubectl` interagire con un cluster del servizio contenitore di Azure concedendo loro il *ruolo utente cluster del servizio Azure Kubernetes*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Se viene visualizzato un errore, ad esempio `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.` , attendere alcuni secondi affinché l'ID oggetto del gruppo Azure ad propaghi attraverso la directory, quindi riprovare a eseguire il `az role assignment create` comando.

Creare un secondo gruppo di esempio, quello per SRE denominato *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Anche in questo caso, creare un'assegnazione di ruolo di Azure per concedere ai membri del gruppo il *ruolo utente cluster del servizio Azure Kubernetes*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Creare utenti demo in Azure AD

Con due gruppi di esempio creati in Azure AD per gli sviluppatori di applicazioni e SRE, ora è possibile creare due utenti di esempio. Per testare l'integrazione RBAC alla fine dell'articolo, si accede al cluster AKS con questi account.

Creare il primo account utente in Azure AD usando il comando [AZ ad User create][az-ad-user-create] .

Nell'esempio seguente viene creato un utente con il nome visualizzato *AKS dev* e il nome dell'entità utente (UPN) di `aksdev@contoso.com` . Aggiornare l'UPN per includere un dominio verificato per il tenant di Azure AD (sostituire *contoso.com* con il proprio dominio) e fornire le proprie credenziali sicure `--password` :

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

A questo punto, aggiungere l'utente al gruppo *AppDev* creato nella sezione precedente usando il comando [AZ ad Group member Add][az-ad-group-member-add] :

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Creare un secondo account utente. Nell'esempio seguente viene creato un utente con il nome visualizzato *AKS SRE* e il nome dell'entità utente (UPN) di `akssre@contoso.com` . Aggiornare l'UPN in modo da includere un dominio verificato per il tenant di Azure AD (sostituire *contoso.com* con il proprio dominio) e fornire le proprie credenziali sicure `--password` :

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

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Creare le risorse del cluster AKS per gli sviluppatori di app

Vengono ora creati i gruppi di Azure AD e gli utenti. Le assegnazioni di ruolo di Azure sono state create per i membri del gruppo per connettersi a un cluster AKS come utente normale. A questo punto, configurare il cluster AKS per consentire a questi gruppi diversi di accedere a risorse specifiche.

Per prima cosa, ottenere le credenziali di amministratore del cluster usando il comando [AZ AKS Get-credentials][az-aks-get-credentials] . In una delle sezioni seguenti si ottengono le credenziali del cluster *utente* normale per visualizzare il flusso di autenticazione Azure ad in azione.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Creare uno spazio dei nomi nel cluster AKS usando il comando [kubectl create namespace][kubectl-create] . Nell'esempio seguente viene creato uno spazio dei nomi *dev*:

```console
kubectl create namespace dev
```

In Kubernetes i *ruoli* definiscono le autorizzazioni da concedere e *RoleBindings* li applicano agli utenti o ai gruppi desiderati. Queste assegnazioni possono essere applicate a uno spazio dei nomi specifico o all'intero cluster. Per altre informazioni, vedere [Uso di autorizzazioni del controllo degli accessi in base al ruolo][rbac-authorization].

Per prima cosa, creare un ruolo per lo spazio dei nomi *dev* . Questo ruolo concede le autorizzazioni complete allo spazio dei nomi. Negli ambienti di produzione è possibile specificare autorizzazioni più granulari per utenti o gruppi diversi.

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

Creare il ruolo usando il comando [kubectl Apply][kubectl-apply] e specificare il nome file del manifesto YAML:

```console
kubectl apply -f role-dev-namespace.yaml
```

Ottenere quindi l'ID di risorsa per il gruppo *AppDev* usando il comando [AZ ad Group Show][az-ad-group-show] . Il gruppo viene impostato come soggetto di un ruolo nel passaggio successivo.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

A questo punto, creare un oggetto Role per il gruppo *AppDev* per usare il ruolo creato in precedenza per l'accesso allo spazio dei nomi. Creare un file denominato `rolebinding-dev-namespace.yaml` e incollare il manifesto YAML seguente. Nell'ultima riga sostituire *groupObjectId* con l'output ID oggetto gruppo del comando precedente:

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

Creare l'oggetto Roleing usando il comando [kubectl Apply][kubectl-apply] e specificare il nome file del manifesto YAML:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Creare le risorse del cluster AKS per SRE

A questo punto, ripetere i passaggi precedenti per creare uno spazio dei nomi, un ruolo e un ruolo per SRE.

Per prima cosa, creare uno spazio dei nomi per *SRE* usando il comando [kubectl create namespace][kubectl-create] :

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

Creare il ruolo usando il comando [kubectl Apply][kubectl-apply] e specificare il nome file del manifesto YAML:

```console
kubectl apply -f role-sre-namespace.yaml
```

Ottenere l'ID risorsa per il gruppo *opssre* usando il comando [AZ ad Group Show][az-ad-group-show] :

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Creare un oggetto Role per il gruppo *opssre* per usare il ruolo creato in precedenza per l'accesso allo spazio dei nomi. Creare un file denominato `rolebinding-sre-namespace.yaml` e incollare il manifesto YAML seguente. Nell'ultima riga sostituire *groupObjectId* con l'output ID oggetto gruppo del comando precedente:

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

Creare l'oggetto Roleing usando il comando [kubectl Apply][kubectl-apply] e specificare il nome file del manifesto YAML:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interagire con le risorse del cluster usando identità Azure AD

A questo punto, è possibile testare le autorizzazioni previste quando si creano e si gestiscono le risorse in un cluster AKS. In questi esempi si pianificano e si visualizzano i pod nello spazio dei nomi assegnato dall'utente. Si tenta quindi di pianificare e visualizzare i pod all'esterno dello spazio dei nomi assegnato.

Per prima cosa, reimpostare il contesto *kubeconfig* usando il comando [AZ AKS Get-credentials][az-aks-get-credentials] . In una sezione precedente si imposta il contesto usando le credenziali di amministratore del cluster. L'utente amministratore ignora le richieste di accesso Azure AD. Senza il `--admin` parametro, viene applicato il contesto utente che richiede l'autenticazione di tutte le richieste con Azure ad.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Pianificare un pod NGINX di base usando il comando [kubectl Run][kubectl-run] nello spazio dei nomi *dev* :

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

Al prompt di accesso immettere le credenziali per il proprio `appdev@contoso.com` account creato all'inizio dell'articolo. Una volta eseguito l'accesso, il token dell'account viene memorizzato nella cache per i `kubectl` comandi successivi. La pianificazione di NGINX è stata completata, come illustrato nell'output di esempio seguente:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

A questo punto usare il comando [kubectl Get Pod][kubectl-get] per visualizzare i pod nello spazio dei nomi *dev* .

```console
kubectl get pods --namespace dev
```

Come illustrato nell'output di esempio seguente, il Pod NGINX viene *eseguito*correttamente:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Creare e visualizzare risorse cluster al di fuori dello spazio dei nomi assegnato

Ora provare a visualizzare i pod all'esterno dello spazio dei nomi *dev* . Usare di nuovo il comando [kubectl Get Pod][kubectl-get] , questa volta per vedere `--all-namespaces` come segue:

```console
kubectl get pods --all-namespaces
```

L'appartenenza al gruppo dell'utente non dispone di un ruolo Kubernetes che consente questa azione, come illustrato nell'output di esempio seguente:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Allo stesso modo, provare a pianificare un pod in uno spazio dei nomi diverso, ad esempio lo spazio dei nomi *SRE* . L'appartenenza al gruppo dell'utente non è allineata a un ruolo Kubernetes e a un ruolo per concedere queste autorizzazioni, come illustrato nell'output di esempio seguente:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Testare l'accesso SRE alle risorse del cluster AKS

Per verificare che l'appartenenza al gruppo Azure AD e il controllo degli accessi in base al ruolo Kubernetes funzionino correttamente tra utenti e gruppi diversi, provare a eseguire i comandi precedenti quando si è connessi come utente di *opssre* .

Reimpostare il contesto *kubeconfig* usando il comando [AZ AKS Get-credentials][az-aks-get-credentials] che cancella il token di autenticazione precedentemente memorizzato nella cache per l'utente *aksdev* :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Provare a pianificare e visualizzare i pod nello spazio dei nomi *SRE* assegnato. Quando richiesto, accedere con le proprie `opssre@contoso.com` credenziali create all'inizio dell'articolo:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Come illustrato nell'output di esempio seguente, è possibile creare e visualizzare i pod correttamente:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

A questo punto, provare a visualizzare o pianificare i pod al di fuori dello spazio dei nomi SRE assegnato:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

Questi `kubectl` comandi hanno esito negativo, come illustrato nell'output di esempio seguente. L'appartenenza a gruppi dell'utente e il ruolo Kubernetes e RoleBindings non concedono le autorizzazioni per la creazione o la gestione delle risorse in altri spazi dei nomi:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Pulire le risorse

In questo articolo sono state create risorse nel cluster AKS e utenti e gruppi in Azure AD. Per pulire tutte le risorse, eseguire i comandi seguenti:

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

Per altre informazioni su come proteggere i cluster Kubernetes, vedere [Opzioni di accesso e identità per AKS)][rbac-authorization].

Per le procedure consigliate sull'identità e sul controllo delle risorse, vedere procedure consigliate [per l'autenticazione e l'autorizzazione in AKS][operator-best-practices-identity].

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
[rbac-authorization]: concepts-identity.md#kubernetes-role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md

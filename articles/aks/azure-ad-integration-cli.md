---
title: Integrare Azure Active Directory con il servizio Azure Kubernetes
description: Informazioni su come usare il comando di Azure per creare e il cluster abilitato Azure Active Directory di Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: d80ad5abecc968a9fe3c82d62ddd8577856a3c54
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835194"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Integrare Azure Active Directory con Azure Kubernetes Service tramite la CLI di Azure

Il servizio Azure Kubernetes può essere configurato per usare Azure Active Directory (AD) per l'autenticazione utente. In questa configurazione, è possibile accedere in un cluster AKS usando un token di autenticazione di Azure AD. Operatori di cluster è anche possono configurare Kubernetes accesso basato sui ruoli controllo degli accessi in base all'appartenenza identità o una directory dell'utente.

Questo articolo illustra come creare i necessari i componenti di Azure AD, quindi distribuire un cluster di Azure Active Directory-abilitata e creare un ruolo RBAC di base del cluster servizio contenitore di AZURE. È anche possibile [completare questi passaggi tramite il portale di Azure][azure-ad-portal].

Per lo script di esempio completo usato in questo articolo, vedere [esempi di comando di Azure - Integrazione servizio contenitore di AZURE con Azure AD][complete-script].

Si applicano le limitazioni seguenti:

- Azure AD può essere abilitato solo quando si crea un nuovo cluster abilitato per il controllo degli accessi in base al ruolo. Non è possibile abilitare Azure AD in un cluster del servizio Azure Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

È necessario la CLI di Azure versione 2.0.61 o versione successiva installato e configurato. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

Per coerenza e consentono di eseguire i comandi in questo articolo, creare una variabile per il nome del cluster servizio contenitore di AZURE desiderato. L'esempio seguente usa il nome *myakscluster*:

```azurecli-interactive
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Panoramica dell'autenticazione AD Azure

L'autenticazione di Azure AD è disponibile per i cluster di servizio Azure Kubernetes con OpenID Connect. OpenID Connect è un livello di gestione delle identità basato sul protocollo OAuth 2.0. Per altre informazioni su OpenID Connect, consulta [documentazione di OpenID Connect][open-id-connect].

Dall'interno del cluster Kubernetes viene usata l'autenticazione del token del webhook per verificare i token di autenticazione. L'autenticazione del token del webhook viene configurata e gestita come parte del cluster servizio Azure Kubernetes. Per altre informazioni sull'autenticazione del token del webhook, consulta [documentazione dell'autenticazione del webhook][kubernetes-webhook].

> [!NOTE]
> Quando si configura Azure AD per l'autenticazione servizio Azure Kubernetes, vengono configurate due applicazioni di Azure AD. Questa operazione deve essere completata da un amministratore tenant di Azure.

## <a name="create-azure-ad-server-component"></a>Creare il componente server di Azure AD

Per l'integrazione con servizio contenitore di AZURE, creare e usare un'applicazione di Azure AD che agisce come un endpoint per le richieste di identità. La prima applicazione di Azure AD che è necessario otterrà l'appartenenza al gruppo di Azure AD un utente.

Creare il componente dell'applicazione server tramite il [az ad app creare] [ az-ad-app-create] comandi, quindi aggiorna l'appartenenza al gruppo di attestazioni utilizzando il [aggiornamento dell'app ad az] [ az-ad-app-update] comando. L'esempio seguente usa il *aksname* variabile definita nel [prima di iniziare](#before-you-begin) sezione e crea una variabile

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Ora creare un'entità servizio per l'app server usando il [az ad sp creare] [ az-ad-sp-create] comando. Questa entità servizio viene usata per autenticare se stesso all'interno della piattaforma Azure. Quindi, ottenere l'entità servizio privata usando il [reimpostare le credenziali di az ad sp] [ az-ad-sp-credential-reset] comando e assegnare alla variabile denominata *serverApplicationSecret* da usare in uno del Procedura:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Azure AD necessita delle autorizzazioni per eseguire le azioni seguenti:

* Leggi i dati della directory
* Accedi e leggi il profilo di un altro utente

Assegnare queste autorizzazioni seguendo la [aggiungere l'autorizzazione dell'app ad az] [ az-ad-app-permission-add] comando:

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Infine, concedere le autorizzazioni assegnate nel passaggio precedente per l'applicazione server usando il [concessione delle autorizzazioni di az ad app] [ az-ad-app-permission-grant] comando. Questo passaggio ha esito negativo se l'account corrente non è un amministratore del tenant. È inoltre necessario aggiungere le autorizzazioni per l'applicazione di Azure AD richiedere informazioni che in caso contrario, potrebbero richiedere il consenso dell'amministratore tramite il [az ad app l'autorizzazione amministratore-consent][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Creare il componente client di Azure AD

La seconda applicazione di Azure AD viene usata quando un utente accede al cluster AKS con la CLI di Kubernetes (`kubectl`). Questa applicazione client accetta la richiesta di autenticazione dell'utente e verifica le credenziali e autorizzazioni. Creare l'app di Azure AD per il componente client usando il [az ad app creare] [ az-ad-app-create] comando:

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Creare un'entità servizio dell'applicazione client usando il [az ad sp creare] [ az-ad-sp-create] comando:

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Ottenere l'ID di oAuth2 per l'app server per consentire il flusso di autenticazione tra i componenti di due app tramite il [show di az ad app] [ az-ad-app-show] comando. Questo ID oAuth2 viene usato nel passaggio successivo.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Aggiungere le autorizzazioni per l'applicazione client e componenti dell'applicazione server per usare la comunicazione oAuth2 flusso mediante la [aggiungere l'autorizzazione dell'app ad az] [ az-ad-app-permission-add] comando. Quindi, concedere le autorizzazioni per l'applicazione client per la comunicazione con l'applicazione server usando il [concessione delle autorizzazioni di az ad app] [ az-ad-app-permission-grant] comando:

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions $oAuthPermissionId=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Distribuire il cluster

Con le due applicazioni di Azure AD create, a questo punto creare il cluster di AKS. In primo luogo, creare un gruppo di risorse usando il [creare il gruppo di az] [ az-group-create] comando. L'esempio seguente crea il gruppo di risorse nel *EastUS* area:

Creare un gruppo di risorse per il cluster:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Ottenere l'ID tenant della sottoscrizione di Azure con il [show account az] [ az-account-show] comando. Quindi, creare il cluster AKS usando il [az aks create] [ az-aks-create] comando. Il comando per creare il cluster AKS fornisce il server e client ID applicazione e segreto dell'entità servizio dell'applicazione server l'ID del tenant:

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

Infine, ottenere il cluster usando le credenziali di amministratore di [az aks get-credentials] [ az-aks-get-credentials] comando. In uno dei passaggi seguenti, si ottiene normali *utente* cluster le credenziali per l'autenticazione di Azure AD di visualizzare il flusso in azione.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>Creare l'associazione RBAC

Prima di poter usare un account di Azure Active Directory con il cluster servizio Azure Kubernetes, è necessario creare un'associazione di ruolo o un'associazione di ruolo del cluster. I *ruoli* definiscono le autorizzazioni da concedere e le *associazioni* le applicano agli utenti desiderati. Queste assegnazioni possono essere applicate a uno spazio dei nomi specifico o all'intero cluster. Per altre informazioni, vedere [Uso di autorizzazioni del controllo degli accessi in base al ruolo][rbac-authorization].

Ottenere il nome dell'entità utente (UPN) per l'utente attualmente connesso usando il [show eseguito l'accesso in utente di ad az] [ az-ad-signed-in-user-show] comando. Questo account utente è abilitato per l'integrazione di Azure AD nel passaggio successivo.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Se l'utente si concede l'associazione di RBAC per è nello stesso tenant di Azure AD, assegnare le autorizzazioni in base il *userPrincipalName*. Se l'utente è in un Azure AD diversi tenant, eseguire una query per e usare la *objectId* proprietà invece.

Creare un manifesto YAML denominato `basic-azure-ad-binding.yaml` e incollare il contenuto seguente. L'ultima riga, sostituire *userPrincipalName_or_objectId* con il nome UPN o un oggetto di ID restituito dal comando precedente:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Creare il ClusterRoleBinding usando il [kubectl applicare] [ kubectl-apply] comando e specificare il nome del file del manifesto del YAML:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Accedere al cluster con Azure AD

A questo punto è possibile testare l'integrazione dell'autenticazione di Azure AD per il cluster AKS. Impostare il `kubectl` contesto di configurazione per utilizzare le credenziali di utente normale. In questo contesto passa tutte le richieste di autenticazione tramite Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

A questo punto usare il [kubectl get pods] [ kubectl-get] comando per visualizzare i POD tra tutti gli spazi dei nomi:

```console
kubectl get pods --all-namespaces
```

Viene visualizzato un segno di nel prompt dei comandi eseguire l'autenticazione con credenziali di Azure AD usando un web browser. Dopo aver eseguito l'autenticazione, il `kubectl` comando Visualizza il numero di POD nel cluster AKS, come illustrato nell'output di esempio seguente:

```console
$ kubectl get pods --all-namespaces

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

Il token di autenticazione ricevute per `kubectl` viene memorizzato nella cache. Sono solo reinserire accedere quando il token è scaduto o il file di configurazione di Kubernetes non verrà ricreato.

Se viene visualizzato un messaggio di errore di autorizzazione dopo aver completato l'accesso usando un web browser come output di esempio seguente, controllare i possibili problemi seguenti:

```console
error: You must be logged in to the server (Unauthorized)
```

* L'ID di oggetto appropriato o UPN, a seconda se l'account utente è nello stesso tenant di Azure AD o non definito.
* L'utente non deve essere membro di più di 200 gruppi.
* Segreto definito nella registrazione dell'applicazione per server corrisponde al valore configurato con `--aad-server-app-secret`

## <a name="next-steps"></a>Passaggi successivi

Per lo script completo che contiene i comandi illustrati in questo articolo, vedere la [repository di esempi di script di integrazione di Azure AD nel servizio contenitore di AZURE][complete-script].

Per usare i gruppi e utenti di Azure AD per controllare l'accesso alle risorse del cluster, vedere [controllare l'accesso alle risorse del cluster con controllo degli accessi basata sui ruoli e le identità di Azure AD in AKS][azure-ad-rbac].

Per altre informazioni su come proteggere i cluster Kubernetes, vedere [opzioni di accesso e identità per AKS)][rbac-authorization].

Per le procedure consigliate sul controllo delle identità e risorse, vedere [procedure consigliate per l'autenticazione e autorizzazione nel servizio contenitore di AZURE][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[azure-ad-portal]: azure-ad-integration.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md

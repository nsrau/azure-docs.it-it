---
title: Integrare Azure Active Directory con il servizio Azure Kubernetes
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per creare un cluster Azure Kubernetes Service (AKS) abilitato per Azure Active Directory
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: mlearned
ms.openlocfilehash: 5b99d76ef20c288d6ae0bd33e1e2b6a75a359d3a
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "67616288"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Integrare Azure Active Directory con il servizio Azure Kubernetes usando l'interfaccia della riga di comando di Azure

Il servizio Azure Kubernetes può essere configurato per usare Azure Active Directory (AD) per l'autenticazione utente. In questa configurazione è possibile accedere a un cluster AKS usando un token di autenticazione Azure AD. Gli operatori del cluster possono anche configurare il controllo degli accessi in base al ruolo Kubernetes in base all'identità dell'utente o al gruppo di directory.

Questo articolo illustra come creare i componenti Azure AD necessari, quindi distribuire un cluster abilitato per Azure AD e creare un ruolo di base RBAC nel cluster AKS. È anche possibile [completare questi passaggi usando il portale di Azure][azure-ad-portal].

Per lo script di esempio completo usato in questo articolo, vedere esempi dell'interfaccia della riga di comando di [Azure-integrazione di AKS con Azure ad][complete-script].

Si applicano le limitazioni seguenti:

- Azure AD può essere abilitato solo quando si crea un nuovo cluster abilitato per il controllo degli accessi in base al ruolo. Non è possibile abilitare Azure AD in un cluster del servizio Azure Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.61 o successiva installata e configurata. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

Per coerenza e per semplificare l'esecuzione dei comandi in questo articolo, creare una variabile per il nome del cluster AKS desiderato. Nell'esempio seguente viene usato il nome *myakscluster*:

```azurecli-interactive
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Panoramica dell'autenticazione Azure AD

L'autenticazione di Azure AD è disponibile per i cluster di servizio Azure Kubernetes con OpenID Connect. OpenID Connect è un livello di gestione delle identità basato sul protocollo OAuth 2.0. Per ulteriori informazioni su OpenID Connect, vedere la [documentazione relativa a Open ID Connect][open-id-connect].

Dall'interno del cluster Kubernetes viene usata l'autenticazione del token del webhook per verificare i token di autenticazione. L'autenticazione del token del webhook viene configurata e gestita come parte del cluster servizio Azure Kubernetes. Per ulteriori informazioni sull'autenticazione dei token webhook, vedere la [documentazione sull'autenticazione webhook][kubernetes-webhook].

> [!NOTE]
> Quando si configura Azure AD per l'autenticazione del servizio Azure Kubernetes, vengono configurate due applicazioni di Azure AD. Questa operazione deve essere completata da un amministratore tenant di Azure.

## <a name="create-azure-ad-server-component"></a>Creare Azure AD componente server

Per l'integrazione con AKS, è possibile creare e usare un'applicazione Azure AD che funga da endpoint per le richieste di identità. Il primo Azure AD applicazione necessario ottiene Azure AD appartenenza a un gruppo per un utente.

Creare il componente applicazione server usando il comando [AZ ad app create][az-ad-app-create] , quindi aggiornare le attestazioni di appartenenza a gruppi usando il comando [AZ ad App Update][az-ad-app-update] . Nell'esempio seguente viene usata la variabile *aksname* definita nella sezione [prima di iniziare](#before-you-begin) e viene creata una variabile

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Creare ora un'entità servizio per l'app server usando il comando [AZ ad SP create][az-ad-sp-create] . Questa entità servizio viene utilizzata per l'autenticazione all'interno della piattaforma Azure. Ottenere quindi il segreto dell'entità servizio usando il comando [AZ ad SP Credential Reset][az-ad-sp-credential-reset] e assegnarlo alla variabile denominata *serverApplicationSecret* per l'uso in uno dei passaggi seguenti:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Il Azure AD richiede le autorizzazioni per eseguire le azioni seguenti:

* Leggi i dati della directory
* Accedi e leggi il profilo di un altro utente

Assegnare queste autorizzazioni usando il comando [AZ ad app permission Add][az-ad-app-permission-add] :

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Infine, concedere le autorizzazioni assegnate nel passaggio precedente per l'applicazione server usando il comando [AZ ad app permission Grant][az-ad-app-permission-grant] . Questo passaggio ha esito negativo se l'account corrente non è un amministratore tenant. È anche necessario aggiungere autorizzazioni per Azure AD applicazione per richiedere informazioni che potrebbero altrimenti richiedere il consenso amministrativo usando il comando [AZ ad app permission admin-acconsenti][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Creare Azure AD componente client

La seconda applicazione Azure AD viene usata quando un utente accede al cluster AKS con l'interfaccia della riga di`kubectl`comando di Kubernetes (). Questa applicazione client accetta la richiesta di autenticazione dall'utente e verifica le credenziali e le autorizzazioni. Creare l'app Azure AD per il componente client usando il comando [AZ ad app create][az-ad-app-create] :

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Creare un'entità servizio per l'applicazione client usando il comando [AZ ad SP create][az-ad-sp-create] :

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Ottenere l'ID oAuth2 per l'app Server per consentire il flusso di autenticazione tra i due componenti dell'app usando il comando [AZ ad App Show][az-ad-app-show] . Questo ID oAuth2 viene usato nel passaggio successivo.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Aggiungere le autorizzazioni per l'applicazione client e i componenti dell'applicazione server per usare il flusso di comunicazione oAuth2 usando il comando [AZ ad app permission Add][az-ad-app-permission-add] . Concedere quindi le autorizzazioni per l'applicazione client per la comunicazione con l'applicazione server usando il comando [AZ ad app permission Grant][az-ad-app-permission-grant] :

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions $oAuthPermissionId=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Distribuire il cluster

Con le due applicazioni Azure AD create, è ora possibile creare il cluster AKS. Per prima cosa, creare un gruppo di risorse usando il comando [AZ Group create][az-group-create] . Nell'esempio seguente viene creato il gruppo di risorse  nell'area eastus:

Creare un gruppo di risorse per il cluster:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Ottenere l'ID tenant della sottoscrizione di Azure usando il comando [AZ account Show][az-account-show] . Quindi, creare il cluster AKS usando il comando [AZ AKS create][az-aks-create] . Il comando per creare il cluster AKS fornisce gli ID dell'applicazione server e client, il segreto dell'entità servizio dell'applicazione server e l'ID tenant:

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

Ottenere infine le credenziali di amministratore del cluster usando il comando [AZ AKS Get-credentials][az-aks-get-credentials] . In uno dei passaggi seguenti si ottengono le credenziali del cluster *utente* normale per vedere il flusso di autenticazione del Azure ad in azione.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>Creare l'associazione RBAC

Prima di poter usare un account di Azure Active Directory con il cluster servizio Azure Kubernetes, è necessario creare un'associazione di ruolo o un'associazione di ruolo del cluster. I *ruoli* definiscono le autorizzazioni da concedere e le *associazioni* le applicano agli utenti desiderati. Queste assegnazioni possono essere applicate a uno spazio dei nomi specifico o all'intero cluster. Per ulteriori informazioni, vedere [utilizzo dell'autorizzazione RBAC][rbac-authorization].

Ottenere il nome dell'entità utente (UPN) per l'utente attualmente connesso usando il comando [AZ ad signed-in-User Show][az-ad-signed-in-user-show] . Questo account utente è abilitato per l'integrazione Azure AD nel passaggio successivo.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Se l'utente che si concede l'associazione RBAC per si trova nello stesso tenant Azure AD, assegnare le autorizzazioni in base a *userPrincipalName*. Se l'utente si trova in un tenant Azure AD diverso, eseguire una query per e utilizzare la proprietà *ObjectID* .

Creare un manifesto YAML denominato `basic-azure-ad-binding.yaml` e incollare il contenuto seguente. Nell'ultima riga sostituire *userPrincipalName_or_objectId* con l'output UPN o ID oggetto del comando precedente:

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

Creare il ClusterRoleBinding usando il comando [kubectl Apply][kubectl-apply] e specificare il nome del file del manifesto YAML:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Accedere al cluster con Azure AD

A questo punto è possibile testare l'integrazione di Azure AD autenticazione per il cluster AKS. Impostare il `kubectl` contesto di configurazione per l'utilizzo delle credenziali utente normali. Questo contesto passa tutte le richieste di autenticazione tramite Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

A questo punto usare il comando [kubectl Get Pod][kubectl-get] per visualizzare i pod in tutti gli spazi dei nomi:

```console
kubectl get pods --all-namespaces
```

Si riceverà una richiesta di accesso per l'autenticazione usando le credenziali Azure AD usando un Web browser. Una volta eseguita l'autenticazione, il `kubectl` comando Visualizza i Pod nel cluster AKS, come illustrato nell'output di esempio seguente:

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

Il token di autenticazione ricevuto `kubectl` per viene memorizzato nella cache. Viene richiesto di eseguire l'accesso solo quando il token è scaduto o il file di configurazione Kubernetes viene ricreato.

Se viene visualizzato un messaggio di errore di autorizzazione dopo aver eseguito l'accesso con un Web browser come nell'output di esempio seguente, verificare i possibili problemi seguenti:

```console
error: You must be logged in to the server (Unauthorized)
```

* È stato definito l'ID oggetto o UPN appropriato, a seconda che l'account utente si trovi nello stesso tenant Azure AD o meno.
* L'utente non deve essere membro di più di 200 gruppi.
* Il segreto definito nella registrazione dell'applicazione per il server corrisponde al valore configurato usando`--aad-server-app-secret`

## <a name="next-steps"></a>Passaggi successivi

Per lo script completo che contiene i comandi illustrati in questo articolo, vedere lo [script di integrazione di Azure ad nel repository degli esempi AKS][complete-script].

Per usare Azure AD utenti e gruppi per controllare l'accesso alle risorse del cluster, vedere [controllare l'accesso alle risorse del cluster usando il controllo degli accessi in base al ruolo e le identità di Azure ad in AKS][azure-ad-rbac].

Per altre informazioni su come proteggere i cluster Kubernetes, vedere [Opzioni di accesso e identità per AKS)][rbac-authorization].

Per le procedure consigliate sull'identità e sul controllo delle risorse, vedere procedure consigliate [per l'autenticazione e l'autorizzazione in AKS][operator-best-practices-identity].

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

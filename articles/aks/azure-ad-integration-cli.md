---
title: Integrare Azure Active Directory con il servizio Azure Kubernetes
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per creare e il cluster di servizio (AKS) abilitato per Azure Active Directory
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: d17ae12beecf9d83ef6d688af799787c5ccf322b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253052"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Integrare Azure Active Directory con il servizio Azure Kubernetes usando l'interfaccia della riga di comando di AzureIntegrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI

Il servizio Azure Kubernetes può essere configurato per usare Azure Active Directory (AD) per l'autenticazione utente. In questa configurazione è possibile accedere a un cluster AKS usando un token di autenticazione di Azure AD. Gli operatori cluster possono inoltre configurare il controllo degli accessi in base al ruolo Kubernetes in base all'identità o all'appartenenza al gruppo di directory di un utente.

Questo articolo illustra come creare i componenti di Azure AD necessari, quindi distribuire un cluster abilitato per Azure AD e creare un ruolo RBAC di base nel cluster AKS. È anche possibile [completare questi passaggi usando il portale][azure-ad-portal]di Azure.You can also complete these steps using the Azure portal .

Per lo script di esempio completo usato in questo articolo, vedere Esempi di interfaccia della riga di comando di [Azure - Integrazione di AKS con Azure AD.][complete-script]

Si applicano le limitazioni seguenti:

- Azure AD può essere abilitato solo quando si crea un nuovo cluster abilitato per il controllo degli accessi in base al ruolo. Non è possibile abilitare Azure AD in un cluster del servizio Azure Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.61 o successiva installata e configurata. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If][install-azure-cli]you need to install or upgrade, see Install Azure CLI.

Vai [https://shell.azure.com](https://shell.azure.com) a per aprire Cloud Shell nel browser.

Per coerenza e per facilitare l'esecuzione dei comandi in questo articolo, creare una variabile per il nome del cluster AKS desiderato. Nell'esempio seguente viene utilizzato il nome *myakscluster*:

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Panoramica dell'autenticazione di Azure ADAzure AD authentication overview

L'autenticazione di Azure AD è disponibile per i cluster di servizio Azure Kubernetes con OpenID Connect. OpenID Connect è un livello di gestione delle identità basato sul protocollo OAuth 2.0. Per ulteriori informazioni su OpenID Connect, vedere la [documentazione relativa][open-id-connect]alla connessione di Open ID .

Dall'interno del cluster Kubernetes viene usata l'autenticazione del token del webhook per verificare i token di autenticazione. L'autenticazione del token del webhook viene configurata e gestita come parte del cluster servizio Azure Kubernetes. Per altre informazioni sull'autenticazione del token del webhook, consulta [documentazione dell'autenticazione del webhook][kubernetes-webhook].

> [!NOTE]
> Quando si configura Azure AD per l'autenticazione del servizio Azure Kubernetes, vengono configurate due applicazioni di Azure AD. Questa operazione deve essere completata da un amministratore tenant di Azure.

## <a name="create-azure-ad-server-component"></a>Creare un componente server di Azure ADCreate Azure AD server component

Per l'integrazione con AKS, creare e usare un'applicazione Azure AD che funge da endpoint per le richieste di identità. La prima applicazione Azure AD necessaria ottiene l'appartenenza al gruppo di Azure AD per un utente.

Creare il componente dell'applicazione server utilizzando il comando [az ad app create,][az-ad-app-create] quindi aggiornare le attestazioni di appartenenza al gruppo utilizzando il comando [az ad app update.][az-ad-app-update] L'esempio seguente usa la variabile *aksname* definita nella sezione [Before you begin](#before-you-begin) e crea una variabile

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Creare ora un'entità servizio per l'app server usando il comando [az ad sp create.][az-ad-sp-create] Questa entità servizio viene usata per autenticarsi all'interno della piattaforma Azure.This service principal is used to authenticate self within the Azure platform. Quindi, ottenere il segreto dell'entità servizio utilizzando il comando [az ad sp credenz reset][az-ad-sp-credential-reset] e assegnare alla variabile denominata *serverApplicationSecret* per l'utilizzo in uno dei passaggi seguenti:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Azure AD richiede le autorizzazioni per eseguire le azioni seguenti:The Azure AD needs permissions to perform the following actions:

* Leggi i dati della directory
* Eseguire l'accesso e leggere il profilo utente

Assegna queste autorizzazioni utilizzando il comando [az ad app add:][az-ad-app-permission-add]

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Infine, concedere le autorizzazioni assegnate nel passaggio precedente per l'applicazione server utilizzando il comando [az ad app permission grant.][az-ad-app-permission-grant] Questo passaggio ha esito negativo se l'account corrente non è un amministratore tenant. È inoltre necessario aggiungere autorizzazioni per l'applicazione Azure AD per richiedere informazioni che potrebbero altrimenti richiedere il consenso amministrativo usando [l'autorizzazione dell'app az admin-consent:][az-ad-app-permission-admin-consent]

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Creare un componente client di Azure ADCreate Azure AD client component

La seconda applicazione Azure AD viene usata quando un utente accede al`kubectl`cluster AKS con l'interfaccia della riga di comando di Kubernetes ( ). Questa applicazione client accetta la richiesta di autenticazione dall'utente e verifica le credenziali e le autorizzazioni. Creare l'app Azure AD per il componente client usando il comando [az ad app create:Create][az-ad-app-create] the Azure AD app for the client component using the az ad app create command:

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Creare un'entità servizio per l'applicazione client usando il comando [az ad sp create:Create][az-ad-sp-create] a service principal for the client application using the az ad sp create command:

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Ottenere l'ID oAuth2 per l'app server per consentire il flusso di autenticazione tra i due componenti dell'app usando il comando [az ad app show.][az-ad-app-show] Questo ID oAuth2 viene utilizzato nel passaggio successivo.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Aggiungere le autorizzazioni per l'applicazione client e i componenti dell'applicazione server per l'utilizzo del flusso di comunicazione oAuth2 utilizzando il comando [az ad app permission add.][az-ad-app-permission-add] Quindi, concedere le autorizzazioni per l'applicazione client per la comunicazione con l'applicazione server utilizzando il comando [az ad app permission grant:][az-ad-app-permission-grant]

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Distribuire il clusterDeploy the cluster

Dopo aver creato le due applicazioni di Azure AD, creare ora il cluster AKS stesso. Creare innanzitutto un gruppo di risorse usando il comando [az group create.][az-group-create] Nell'esempio seguente viene creato il gruppo di risorse nell'area *EastUS:The following* example creates the resource group in the EastUS region:

Creare un gruppo di risorse per il cluster:Create a resource group for the cluster:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Ottenere l'ID tenant della sottoscrizione di Azure usando il comando [az account show.][az-account-show] Quindi, creare il cluster AKS utilizzando il comando [az aks create.][az-aks-create] Il comando per creare il cluster AKS fornisce gli ID dell'applicazione client e server, il segreto dell'entità servizio dell'applicazione server e l'ID tenant:

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

Infine, ottenere le credenziali di amministratore del cluster utilizzando il comando [az aks get-credentials.][az-aks-get-credentials] In uno dei passaggi seguenti si ottengono le normali credenziali del cluster *di utenti* per visualizzare il flusso di autenticazione di Azure AD in azione.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>Creare l'associazione RBAC

Prima di poter usare un account di Azure Active Directory con il cluster servizio Azure Kubernetes, è necessario creare un'associazione di ruolo o un'associazione di ruolo del cluster. I *ruoli* definiscono le autorizzazioni da concedere e le *associazioni* le applicano agli utenti desiderati. Queste assegnazioni possono essere applicate a uno spazio dei nomi specifico o all'intero cluster. Per altre informazioni, vedere [Uso di autorizzazioni del controllo degli accessi in base al ruolo][rbac-authorization].

Ottenere il nome dell'entità utente (UPN) per l'utente attualmente connesso utilizzando il comando [az ad-signed-in-user show.][az-ad-signed-in-user-show] Questo account utente è abilitato per l'integrazione di Azure AD nel passaggio successivo.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Se l'utente per cui si concede l'associazione RBAC si trova nello stesso tenant di Azure AD, assegnare le autorizzazioni in base a *userPrincipalName*. Se l'utente si trova in un tenant di Azure AD diverso, eseguire una query per e usare invece la proprietà *objectId.*

Creare un manifesto YAML denominato `basic-azure-ad-binding.yaml` e incollare il contenuto seguente. Nell'ultima riga, sostituire *userPrincipalName_or_objectId* con l'UPN o l'output dell'ID oggetto del comando precedente:

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

Creare ClusterRoleBinding utilizzando il comando [kubectl apply][kubectl-apply] e specificare il nome del file del manifesto YAML:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Accedere al cluster con Azure AD

Esaminiamo ora l'integrazione dell'autenticazione di Azure AD per il cluster AKS. Impostare `kubectl` il contesto di configurazione per utilizzare le credenziali utente normali. Questo contesto passa tutte le richieste di autenticazione tramite Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Ora usate il comando [kubectl get pods][kubectl-get] per visualizzare i pod in tutti gli spazi dei nomi:

```console
kubectl get pods --all-namespaces
```

Viene visualizzato un prompt di accesso per l'autenticazione usando le credenziali di Azure AD usando un Web browser. Dopo aver eseguito correttamente `kubectl` l'autenticazione, il comando visualizza i pod nel cluster AKS, come illustrato nell'output di esempio seguente:

```console
kubectl get pods --all-namespaces
```

```output
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

Il token di `kubectl` autenticazione ricevuto per viene memorizzato nella cache. Viene richiesto di eseguire nuovamente l'accesso solo quando il token è scaduto o il file di configurazione Kubernetes viene ricreato.

Se viene visualizzato un messaggio di errore di autorizzazione dopo aver eseguito correttamente l'accesso utilizzando un browser Web come nell'output di esempio seguente, verificare i seguenti possibili problemi:

```output
error: You must be logged in to the server (Unauthorized)
```

* L'ID oggetto o l'UPN appropriati sono stati definiti, a seconda che l'account utente si trovi o meno nello stesso tenant di Azure AD.
* L'utente non deve essere membro di più di 200 gruppi.
* Il segreto definito nella registrazione dell'applicazione per il server corrisponde al valore configurato utilizzando`--aad-server-app-secret`

## <a name="next-steps"></a>Passaggi successivi

Per lo script completo che contiene i comandi illustrati in questo articolo, vedere lo script di integrazione di Azure AD nel repository degli [esempi AKS][complete-script].

Per usare gli utenti e i gruppi di Azure AD per controllare l'accesso alle risorse del cluster, vedere [Controllare l'accesso alle risorse del cluster usando][azure-ad-rbac]il controllo degli accessi in base al ruolo e le identità di Azure AD in AKS.

Per ulteriori informazioni su come proteggere i cluster Kubernetes, vedere Opzioni di [accesso e identità per AKS).][rbac-authorization]

Per le procedure consigliate per il controllo delle identità e delle risorse, vedere [Procedure consigliate per l'autenticazione e l'autorizzazione in AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
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

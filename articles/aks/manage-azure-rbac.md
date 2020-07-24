---
title: Gestisci RBAC in Kubernetes da Azure
titleSuffix: Azure Kubernetes Service
description: Informazioni su come usare il controllo degli accessi in base al ruolo di Azure per l'autorizzazione Kubernetes con il servizio Azure Kubernetes.
services: container-service
ms.topic: article
ms.date: 07/07/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: bb4c689da38606561c657a3e4d85fd9e391267bf
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87056736"
---
# <a name="use-azure-rbac-for-kubernetes-authorization-preview"></a>Usare il controllo degli accessi in base al ruolo di Azure per l'autorizzazione di Kubernetes (anteprima)

Oggi è già possibile sfruttare [l'autenticazione integrata tra Azure Active Directory (Azure ad) e AKS](managed-aad.md). Se abilitata, questa integrazione consente ai clienti di usare Azure AD utenti, gruppi o entità servizio come argomenti in Kubernetes RBAC. vedere altre informazioni [qui](azure-ad-rbac.md).
Questa funzionalità non consente di gestire separatamente le identità e le credenziali degli utenti per Kubernetes. Tuttavia, è comunque necessario configurare e gestire il controllo degli accessi in base al ruolo di Azure e Kubernetes RBAC separatamente. Per ulteriori informazioni sull'autenticazione, l'autorizzazione e il controllo degli accessi in base al ruolo in AKS vedere [qui](concepts-identity.md)

Questo documento illustra un nuovo approccio che consente il controllo di accesso e gestione unificato tra risorse di Azure, AKS e risorse Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

La possibilità di gestire il controllo degli accessi in base al ruolo per le risorse Kubernetes da Azure consente di gestire il controllo degli accessi in base al ruolo per le risorse del cluster usando i meccanismi Kubernetes nativi Quando questa funzionalità è abilitata, le entità Azure AD verranno convalidate esclusivamente da controllo degli accessi in base al ruolo di Azure, mentre gli utenti Kubernetes e gli account del servizio normali vengono convalidati Per ulteriori informazioni sull'autenticazione, l'autorizzazione e il controllo degli accessi in base al ruolo in AKS vedere [qui](concepts-identity.md#azure-rbac-for-kubernetes-authorization-preview)

> [!IMPORTANT]
> Le funzionalità di anteprima del servizio contenitore di servizi sono disponibili in base al consenso esplicito. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto tecnico del cliente in base al massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per altre informazioni, vedere gli articoli di supporto seguenti:
>
> - [Criteri di supporto del servizio Azure Kubernetes](support-policies.md)
> - [Domande frequenti relative al supporto tecnico Azure](faq.md)

### <a name="prerequisites"></a>Prerequisiti 
- Iscriversi per l'anteprima <https://aka.ms/aad-rbac-sign-up-form> .
- Assicurarsi di avere `EnableAzureRBACPreview` abilitato il flag funzionalità.
- Assicurarsi di avere `AAD-V2` abilitato il flag funzionalità.
- Assicurarsi che sia installata l' `aks-preview` [estensione CLI][az-extension-add] v 0.4.55 o versione successiva
- Assicurarsi di aver installato [kubectl v 1.18.3 +][az-aks-install-cli].

#### <a name="register-enableazurerbacpreview-and-aad-v2-preview-features"></a>`EnableAzureRBACPreview`Funzionalità di registrazione e `AAD-V2` anteprima

Per creare un cluster AKS che usa il controllo degli accessi in base al ruolo di Azure per l'autorizzazione Kubernetes, è necessario abilitare i `EnableAzureRBACPreview` `AAD-V2` flag di funzionalità e nella sottoscrizione.

Registrare i `EnableAzureRBACPreview` `AAD-V2` flag della funzionalità e usando il comando [AZ feature Register][az-feature-register] , come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureRBACPreview"

az feature register --namespace "Microsoft.ContainerService"  --name "AAD-V2"
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllare lo stato di registrazione usando il comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureRBACPreview')].{Name:name,State:properties.state}"

az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft. servizio contenitore* usando il comando [AZ provider Register] [AZ-provider-Register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

#### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando aks-preview

Per creare un cluster del servizio contenitore di Azure che usa il controllo degli accessi in *base al ruolo* di Azure, è necessaria la versione 0.4.55 o successiva dell'estensione CLI Installare l'estensione dell'interfaccia della riga di comando di Azure *AKS-Preview* usando il comando [AZ Extension Add][az-extension-add] oppure installare gli aggiornamenti disponibili usando il comando [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Limitazioni

- Richiede l' [integrazione di Azure ad gestiti](managed-aad.md).
- Non è possibile integrare il controllo degli accessi in base al ruolo di Azure per l'autorizzazione Kubernetes nei cluster esistenti durante la fase di anteprima, ma sarà disponibile a livello generale (GA).
- Usare [kubectl v 1.18.3 +][az-aks-install-cli].
- Durante l'anteprima è possibile aggiungere autorizzazioni a *livello di spazio dei nomi* solo tramite l'interfaccia della riga di comando di Azure
- Se si dispone di CRD e si stanno creando definizioni di ruolo personalizzate, l'unico modo per coprire CRD oggi è fornire `Microsoft.ContainerService/managedClusters/*/read` . AKS sta lavorando per fornire autorizzazioni più granulari per CRD. Per gli oggetti rimanenti è possibile usare i gruppi di API specifici, ad esempio: `Microsoft.ContainerService/apps/deployments/read` .
- Le nuove assegnazioni di ruolo possono richiedere fino a 5 minuti per la propagazione e l'aggiornamento da parte del server di autorizzazione.

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>Creare un nuovo cluster usando il controllo degli accessi in base al ruolo e l'integrazione Azure AD

Creare un cluster AKS usando i comandi dell'interfaccia della riga di comando seguenti.

Creare un gruppo di risorse di Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Creare il cluster del servizio contenitore di Azure con l'integrazione Azure AD gestita e il controllo degli accessi in base al ruolo

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

Una corretta creazione di un cluster con integrazione Azure AD e controllo degli accessi in base al ruolo di Azure per l'autorizzazione Kubernetes presenta la sezione seguente nel corpo della risposta:

```json
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "enableAzureRbac": true,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "****-****-****-****-****"
  }
```

## <a name="create-role-assignments-for-users-to-access-cluster"></a>Creare assegnazioni di ruolo per consentire agli utenti di accedere al cluster

AKS fornisce i quattro ruoli predefiniti seguenti:


| Ruolo                                | Descrizione  |
|-------------------------------------|--------------|
| Visualizzatore RBAC del servizio Kubernetes di Azure  | Consente l'accesso in sola lettura per visualizzare la maggior parte degli oggetti in uno spazio dei nomi. Non consente la visualizzazione di ruoli o associazioni di ruolo. Questo ruolo non consente la visualizzazione `Secrets` perché la lettura del contenuto dei segreti consente l'accesso alle credenziali ServiceAccount nello spazio dei nomi, che consente l'accesso all'API come qualsiasi ServiceAccount nello spazio dei nomi (un tipo di escalation dei privilegi)  |
| Writer RBAC del servizio Kubernetes di Azure | Consente l'accesso in lettura/scrittura alla maggior parte degli oggetti in uno spazio dei nomi. Questo ruolo non consente la visualizzazione o la modifica di ruoli o associazioni di ruolo. Tuttavia, questo ruolo consente l'accesso `Secrets` e l'esecuzione di pod come qualsiasi ServiceAccount nello spazio dei nomi, quindi può essere usato per ottenere i livelli di accesso all'API di qualsiasi ServiceAccount nello spazio dei nomi. |
| Amministratore RBAC del servizio Kubernetes di Azure  | Consente l'accesso dell'amministratore, che deve essere concesso all'interno di uno spazio dei nomi. Consente l'accesso in lettura/scrittura alla maggior parte delle risorse in uno spazio dei nomi (o ambito del cluster), inclusa la possibilità di creare ruoli e associazioni di ruolo all'interno dello spazio dei nomi. Questo ruolo non consente l'accesso in scrittura alla quota di risorse o allo spazio dei nomi stesso. |
| Amministrazione del cluster RBAC del servizio Kubernetes di Azure  | Consente l'accesso con privilegi avanzati per eseguire qualsiasi azione su qualsiasi risorsa. Fornisce il controllo completo su tutte le risorse nel cluster e in tutti gli spazi dei nomi. |


Le assegnazioni di ruoli con ambito all' **intero cluster AKS** possono essere eseguite nel pannello controllo di accesso (IAM) della risorsa cluster in portale di Azure o usando i comandi dell'interfaccia della riga di comando di Azure, come illustrato di seguito:

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

dove `<AAD-ENTITY-ID>` potrebbe essere un nome utente (ad esempio, user@contoso.com ) o anche il ClientID di un'entità servizio.

È anche possibile creare assegnazioni di ruolo nell'ambito di uno **spazio dei nomi** specifico all'interno del cluster:

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

Attualmente, è necessario configurare le assegnazioni di ruolo con ambito per gli spazi dei nomi tramite l'interfaccia della riga di comando di Azure.


### <a name="create-custom-roles-definitions"></a>Creare definizioni di ruoli personalizzati

Facoltativamente, è possibile scegliere di creare una definizione di ruolo personalizzata e quindi assegnarla come sopra.

Di seguito è riportato un esempio di definizione di ruolo che consente a un utente di leggere solo le distribuzioni e nient'altro. È possibile controllare l'elenco completo delle possibili azioni [qui](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice).


Copiare il codice JSON seguente in un file denominato `deploy-view.json` .

```json
{
    "Name": "AKS Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.ContainerService/managedClusters/apps/deployments/read"  
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<YOUR SUBSCRIPTION ID>"
    ]   
}
```

Sostituire `<YOUR SUBSCRIPTION ID>` con l'ID della sottoscrizione, che è possibile ottenere eseguendo:

```azurecli-interactive
az account show --query id -o tsv
```


A questo punto è possibile creare la definizione del ruolo eseguendo il comando seguente dalla cartella in cui è stato salvato `deploy-view.json` :

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

Ora che si dispone della definizione di ruolo, è possibile assegnarla a un utente o a un'altra identità eseguendo:

```azurecli-interactive
az role assignment create --role "AKS Deployment Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>Usa RBAC di Azure per l'autorizzazione Kubernetes con`kubectl`

> [!NOTE]
> Assicurarsi di disporre della versione più recente di kubectl eseguendo il comando seguente:
>
> ```azurecli-interactive
> az aks install-cli
> ```
> Potrebbe essere necessario eseguire l'operazione con `sudo` privilegi. 

A questo punto è stato assegnato il ruolo e le autorizzazioni desiderate. È possibile iniziare a chiamare l'API Kubernetes, ad esempio da `kubectl` .

A questo scopo, è necessario ottenere il kubeconfig del cluster usando il comando seguente:

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> Per eseguire il passaggio precedente, è necessario il ruolo predefinito [utente del cluster di servizi di Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) .

A questo punto, è possibile usare kubectl per elencare, ad esempio, i nodi nel cluster. La prima volta che si esegue questa operazione, è necessario eseguire l'accesso e i comandi successivi utilizzeranno il rispettivo token di accesso.

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>Usa RBAC di Azure per l'autorizzazione Kubernetes con`kubelogin`

Per sbloccare altri scenari, ad esempio accessi non interattivi, `kubectl` versioni precedenti o uso di SSO in più cluster senza la necessità di accedere a un nuovo cluster, che il token sia ancora valido, AKS ha creato un plug-in exec denominato [`kubelogin`](https://github.com/Azure/kubelogin) .

È possibile utilizzarlo eseguendo:

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

Per la prima volta, è necessario eseguire l'accesso in modo interattivo, ad esempio con kubectl normali, ma in seguito non sarà più necessario, neanche per i nuovi cluster di Azure AD (purché il token sia ancora valido).

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="clean-up"></a>Eseguire la pulizia

### <a name="clean-role-assignment"></a>Pulisci assegnazione di ruolo

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```
Copiare l'ID o gli ID da tutte le assegnazioni effettuate e quindi.

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>Pulisci definizione di ruolo

```azurecli-interactive
az role definition delete -n "AKS Deployment Viewer"
```

### <a name="delete-cluster-and-resource-group"></a>Elimina cluster e gruppo di risorse

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere autenticazione, autorizzazione e controllo degli accessi in base al [ruolo.](concepts-identity.md)
- Scopri di più sul controllo [here](../role-based-access-control/overview.md)degli accessi in base al ruolo
- Altre informazioni su tutte le azioni che è possibile usare per definire in modo granulare i ruoli personalizzati di controllo degli accessi in base al ruolo per [l'autorizzazione Kubernetes](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice).


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli

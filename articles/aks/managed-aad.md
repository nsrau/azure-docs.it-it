---
title: Usare Azure AD nel servizio Azure Kubernetes
description: Informazioni su come usare Azure AD in Azure Kubernetes Service (AKS)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 07/20/2020
ms.author: thomasge
ms.openlocfilehash: 06a97126df449b77bf3fcc48bd23231512c9dff2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87056657"
---
# <a name="aks-managed-azure-active-directory-integration"></a>Integrazione Azure Active Directory gestita da AKS

L'integrazione di Azure AD gestita da AKS è progettata per semplificare l'esperienza di integrazione Azure AD, in cui gli utenti erano in precedenza necessari per creare un'app client, un'app Server e hanno richiesto il tenant Azure AD per concedere le autorizzazioni di lettura directory. Nella nuova versione, il provider di risorse AKS gestisce automaticamente le app client e server.

## <a name="azure-ad-authentication-overview"></a>Panoramica dell'autenticazione Azure AD

Gli amministratori del cluster possono configurare il controllo degli accessi in base al ruolo Kubernetes (RBAC) in base all'identità dell'utente o al gruppo di directory. L'autenticazione di Azure AD è disponibile per i cluster di servizio Azure Kubernetes con OpenID Connect. OpenID Connect è un livello di gestione delle identità basato sul protocollo OAuth 2.0. Per ulteriori informazioni su OpenID Connect, vedere la [documentazione relativa a Open ID Connect][open-id-connect].

Per altre informazioni sul flusso di integrazione di AAD, vedere la [documentazione sui concetti relativi all'integrazione di Azure Active Directory](concepts-identity.md#azure-active-directory-integration).

## <a name="region-availability"></a>Aree di disponibilità

L'integrazione Azure Active Directory gestita da AKS è disponibile nelle aree pubbliche in cui [è supportato AKS](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).

* Azure Government attualmente non è supportato.
* Azure Cina 21Vianet attualmente non è supportato.

## <a name="limitations"></a>Limitazioni 

* L'integrazione di Azure AD gestiti da AKS non può essere disabilitata
* i cluster non abilitati per RBAC non sono supportati per l'integrazione AAD gestita da AKS
* La modifica del tenant di Azure AD associato all'integrazione AAD gestita da AKS non è supportata

> [!IMPORTANT]
> Le funzionalità di anteprima del servizio contenitore di servizi sono disponibili in base al consenso esplicito. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto tecnico del cliente in base al massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per altre informazioni, vedere gli articoli di supporto seguenti: 
> - [Criteri di supporto del servizio Azure Kubernetes](support-policies.md) 
> - [Domande frequenti relative al supporto tecnico Azure](faq.md)

## <a name="prerequisites"></a>Prerequisiti

* L'interfaccia della riga di comando di Azure versione 2.9.0 o successiva
* Kubectl con una versione minima di [1,18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180)

> [!Important]
> È necessario usare Kubectl con una versione minima di 1,18

Per installare kubectl, usare i comandi seguenti:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
```

Usare [queste istruzioni](https://kubernetes.io/docs/tasks/tools/install-kubectl/) per altri sistemi operativi.

```azurecli-interactive 
az feature register --name AAD-V2 --namespace Microsoft.ContainerService    
``` 

Potrebbero essere necessari alcuni minuti prima che lo stato venga visualizzato come **Registrato**. È possibile controllare lo stato di registrazione con il comando [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list): 

```azurecli-interactive 
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"    
``` 

Quando lo stato diventa Registrato, aggiornare la registrazione del provider di risorse `Microsoft.ContainerService` usando il comando [ az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register):    

```azurecli-interactive 
az provider register --namespace Microsoft.ContainerService 
``` 


## <a name="before-you-begin"></a>Prima di iniziare

Per il cluster, è necessario un gruppo di Azure AD. Questo gruppo è necessario come gruppo di amministrazione per il cluster per concedere le autorizzazioni di amministratore del cluster. È possibile usare un gruppo di Azure AD esistente o crearne uno nuovo. Registrare l'ID oggetto del gruppo di Azure AD.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Per creare un nuovo gruppo di Azure AD per gli amministratori del cluster, usare il comando seguente:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Creare un cluster AKS con Azure AD abilitato

Creare un cluster AKS usando i comandi dell'interfaccia della riga di comando seguenti.

Creare un gruppo di risorse di Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Creare un cluster AKS e abilitare l'accesso di amministrazione per il gruppo di Azure AD

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Una corretta creazione di un cluster Azure AD gestito da AKS ha la sezione seguente nel corpo della risposta
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Una volta creato il cluster, è possibile iniziare ad accedervi.

## <a name="access-an-azure-ad-enabled-cluster"></a>Accedere a un cluster Azure AD abilitato

Per eseguire la procedura seguente, è necessario il ruolo predefinito [utente del cluster di servizi di Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) .

Ottenere le credenziali utente per accedere al cluster:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
Seguire le istruzioni per l'accesso.

Usare il comando kubectl Get Nodes per visualizzare i nodi nel cluster:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Configurare il [controllo degli accessi in base al ruolo (RBAC)](./azure-ad-rbac.md) per configurare gruppi di sicurezza aggiuntivi per i cluster.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Risoluzione dei problemi di accesso con Azure AD

> [!Important]
> La procedura descritta di seguito sta ignorando la normale autenticazione del gruppo di Azure AD. Utilizzarle solo in caso di emergenza.

Se non si dispone dell'accesso a un gruppo di Azure AD valido con accesso al cluster, è comunque possibile ottenere le credenziali di amministratore per accedere direttamente al cluster.

Per eseguire questa procedura, è necessario avere accesso al ruolo predefinito [Amministrazione cluster del servizio Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) .

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Aggiornamento all'integrazione Azure AD gestita da AKS

Se il cluster usa l'integrazione Azure AD legacy, è possibile eseguire l'aggiornamento all'integrazione Azure AD gestita da AKS.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Una migrazione riuscita di un cluster di Azure AD gestito da AKS ha la sezione seguente nel corpo della risposta

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Se si vuole accedere al cluster, seguire [questa procedura.][access-cluster]

## <a name="non-interactive-sign-in-with-kubelogin"></a>Accesso non interattivo con kubelogin

Esistono alcuni scenari non interattivi, ad esempio le pipeline di integrazione continua, che non sono attualmente disponibili con kubectl. È possibile usare [`kubelogin`](https://github.com/Azure/kubelogin) per accedere al cluster con l'accesso all'entità servizio non interattivo.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sull' [integrazione RBAC di Azure per l'autorizzazione Kubernetes][azure-rbac-integration]
* Informazioni sull' [integrazione di Azure ad con KUBERNETES RBAC][azure-ad-rbac].
* Usare [kubelogin](https://github.com/Azure/kubelogin) per accedere alle funzionalità di autenticazione di Azure non disponibili in kubectl.
* Altre informazioni sui [concetti relativi all'identità AKS e Kubernetes][aks-concepts-identity].
* Usare i [modelli di Azure Resource Manager (ARM)][aks-arm-template] per creare cluster gestiti da AKS Azure ad abilitati.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration

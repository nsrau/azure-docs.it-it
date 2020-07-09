---
title: Usare Azure AD nel servizio Azure Kubernetes
description: Informazioni su come usare Azure AD in Azure Kubernetes Service (AKS)
services: container-service
manager: gwallace
author: mlearned
ms.topic: article
ms.date: 06/25/2020
ms.author: mlearned
ms.openlocfilehash: f22b79cb8a730fb9c28dd1a208ab672473218b79
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105949"
---
# <a name="aks-managed-azure-active-directory-integration-preview"></a>Integrazione Azure Active Directory gestita da AKS (anteprima)

> [!NOTE]
> I cluster del servizio contenitore di Azure (Kubernetes) esistenti con l'integrazione di Azure Active Directory (Azure AD) non sono interessati dalla nuova esperienza Azure AD gestita da AKS.

L'integrazione di Azure AD gestita da AKS è progettata per semplificare l'esperienza di integrazione Azure AD, in cui gli utenti erano in precedenza necessari per creare un'app client, un'app Server e hanno richiesto il tenant Azure AD per concedere le autorizzazioni di lettura directory. Nella nuova versione, il provider di risorse AKS gestisce automaticamente le app client e server.

## <a name="azure-ad-authentication-overview"></a>Panoramica dell'autenticazione Azure AD

Gli amministratori del cluster possono configurare il controllo degli accessi in base al ruolo Kubernetes (RBAC) in base all'identità dell'utente o al gruppo di directory. L'autenticazione di Azure AD è disponibile per i cluster di servizio Azure Kubernetes con OpenID Connect. OpenID Connect è un livello di gestione delle identità basato sul protocollo OAuth 2.0. Per ulteriori informazioni su OpenID Connect, vedere la [documentazione relativa a Open ID Connect][open-id-connect].

Per altre informazioni sul flusso di integrazione di AAD, vedere la [documentazione sui concetti relativi all'integrazione di Azure Active Directory](concepts-identity.md#azure-active-directory-integration).

## <a name="limitations"></a>Limitazioni

* Attualmente non è possibile aggiornare un cluster integrato con Azure AD AKS esistente alla nuova esperienza Azure AD gestita da AKS.

> [!IMPORTANT]
> Le funzionalità di anteprima del servizio contenitore di servizi sono disponibili in base al consenso esplicito. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto tecnico del cliente in base al massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per altre informazioni, vedere gli articoli di supporto seguenti:
>
> - [Criteri di supporto del servizio Azure Kubernetes](support-policies.md)
> - [Domande frequenti relative al supporto tecnico Azure](faq.md)

## <a name="before-you-begin"></a>Prima di iniziare

* Individuare l'ID del tenant dell'account Azure passando al portale di Azure e selezionare Azure Active Directory proprietà > > ID directory

> [!Important]
> È necessario usare Kubectl con una versione minima di 1,18

Devono essere installate le risorse seguenti:

- INTERFACCIA della riga di comando di Azure, versione 2.5.1 o successiva
- Estensione 0.4.38 AKS-Preview
- Kubectl con una versione minima di [1,18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180)

Per installare o aggiornare l'estensione AKS-Preview o versioni successive, usare i seguenti comandi dell'interfaccia della riga di comando di Azure:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Per installare kubectl, usare i comandi seguenti:

```azurecli
sudo az aks install-cli
kubectl version --client
```

Usare [queste istruzioni](https://kubernetes.io/docs/tasks/tools/install-kubectl/) per altri sistemi operativi.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Potrebbero essere necessari alcuni minuti prima che lo stato venga visualizzato come **Registrato**. È possibile controllare lo stato di registrazione con il comando [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list):

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Quando lo stato diventa Registrato, aggiornare la registrazione del provider di risorse `Microsoft.ContainerService` usando il comando [ az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register):

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Creare un cluster AKS con Azure AD abilitato

Creare un cluster AKS usando i comandi dell'interfaccia della riga di comando seguenti.

Creare un gruppo di risorse di Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

È possibile usare un gruppo di Azure AD esistente o crearne uno nuovo. È necessario l'ID oggetto per il gruppo di Azure AD.

```azurecli-interactive
# List existing groups in the directory
az ad group list
```

Per creare un nuovo gruppo di Azure AD per gli amministratori del cluster, usare il comando seguente:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name MyDisplay --mail-nickname MyDisplay
```

Creare un cluster AKS e abilitare l'accesso di amministrazione per il gruppo di Azure AD

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

Una corretta creazione di un cluster Azure AD gestito da AKS ha la sezione seguente nel corpo della risposta
```
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Il cluster viene creato entro pochi minuti.

## <a name="access-an-azure-ad-enabled-cluster"></a>Accedere a un cluster Azure AD abilitato

Per eseguire la procedura seguente, è necessario il ruolo predefinito [utente del cluster di servizi di Azure Kubernetes](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-user-role) .

Ottenere le credenziali utente per accedere al cluster:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
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
Configurare il [controllo degli accessi in base al ruolo (RBAC)](https://docs.microsoft.com/azure/aks/azure-ad-rbac) per configurare gruppi di sicurezza aggiuntivi per i cluster.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Risoluzione dei problemi di accesso con Azure AD

> [!Important]
> La procedura descritta di seguito sta ignorando la normale autenticazione del gruppo di Azure AD. Utilizzarle solo in caso di emergenza.

Se non si dispone dell'accesso a un gruppo di Azure AD valido con accesso al cluster, è comunque possibile ottenere le credenziali di amministratore per accedere direttamente al cluster.

Per eseguire questa procedura, è necessario avere accesso al ruolo predefinito [Amministrazione cluster del servizio Azure Kubernetes](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-admin-role) .

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```

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
[aks-arm-template]: https://docs.microsoft.com/azure/templates/microsoft.containerservice/managedclusters

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


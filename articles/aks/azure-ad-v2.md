---
title: Usare Azure AD nel servizio Azure KubernetesUse Azure AD in Azure Kubernetes Service
description: Informazioni su come usare Azure AD nel servizio Azure Kubernetes (AKS)Learn how to use Azure AD in Azure Kubernetes Service (AKS)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: 430df504c677b005f5ff5e7fdd9346aed3e168af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294462"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Integrare Azure AD nel servizio Azure Kubernetes (anteprima)Integrate Azure AD in Azure Kubernetes Service (Preview)

> [!Note]
> I cluster AKS v1 esistenti con l'integrazione di Active Directory non sono interessati dalla nuova esperienza AKS v2.

L'integrazione di Azure AD con AKS v2 è progettata per semplificare l'integrazione di Azure AD con l'esperienza AKS v1, in cui agli utenti è stato richiesto di creare un'app client, un'app server e il tenant di Azure AD per concedere le autorizzazioni di lettura della directory. Nella nuova versione, il provider di risorse AKS gestisce le app client e server per l'utente.

## <a name="limitations"></a>Limitazioni

* Al momento non è possibile aggiornare un cluster AKS v1 abilitato di Azure AD esistente all'esperienza v2.

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono disponibili in modalità self-service e con consenso esplicito. Le anteprime sono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dall'assistenza clienti in base al massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per altre informazioni, vedere i seguenti articoli di supporto:
>
> - [Criteri di supporto AKS](support-policies.md)
> - [Domande frequenti relative al supporto tecnico Azure](faq.md)

## <a name="before-you-begin"></a>Prima di iniziare

È necessario disporre delle seguenti risorse installate:

- Interfaccia della riga di comando di Azure, versione 2.2.0 o successiva
- L'estensione aks-preview 0.4.38
- Kubectl con una versione minima di [1.18 beta](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#client-binaries)

Per installare/aggiornare l'estensione aks-preview o versione successiva, usare i comandi dell'interfaccia della riga di comando di Azure seguenti:To install/update the aks-preview extension or later, use the following Azure CLI commands:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Per installare kubectl, utilizzare i seguenti
```azurecli
curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.18.0-beta.2/bin/darwin/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
```

Utilizzare [queste istruzioni](https://kubernetes.io/docs/tasks/tools/install-kubectl/) per altri sistemi operativi.

> [!CAUTION]
> Dopo aver registrato una funzionalità in una sottoscrizione, al momento non è possibile annullare la registrazione di tale funzionalità. Quando si abilitano alcune funzionalità di anteprima, le impostazioni predefinite potrebbero essere usate per tutti i cluster AKS creati successivamente nella sottoscrizione. Non abilitare le funzionalità di anteprima nelle sottoscrizioni di produzione. Usare invece una sottoscrizione separata per testare le funzionalità di anteprima e raccogliere commenti e suggerimenti.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

La visualizzazione dello stato come **Registered**potrebbe richiedere alcuni minuti. È possibile controllare lo stato della registrazione utilizzando il comando [elenco funzionalità az:](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Quando lo stato viene visualizzato come `Microsoft.ContainerService` registrato, aggiornare la registrazione del provider di risorse utilizzando il comando [az provider register:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Creare un cluster AKS con Azure AD abilitatoCreate an AKS cluster with Azure AD enabled

È ora possibile creare un cluster AKS utilizzando i comandi dell'interfaccia della riga di comando seguenti.

Creare innanzitutto un gruppo di risorse di Azure innanzitutto:First, create an Azure resource group:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Quindi, creare un cluster AKS:Then, create an AKS cluster:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
Il comando precedente crea un cluster AKS a tre nodi, ma l'utente, che ha creato il cluster, per impostazione predefinita, non è un membro di un gruppo che ha accesso a questo cluster. Questo utente deve creare un gruppo di Azure AD, aggiungersi come membro del gruppo e quindi aggiornare il cluster come illustrato di seguito. Segui le istruzioni [qui](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)

Dopo aver creato un gruppo e aggiunto se stessi (e altri) come membro, è possibile aggiornare il cluster con il gruppo di Azure AD usando il comando seguenteOnce you've created a group and added yourself (and others) as a member, you can update the cluster with the Azure AD group using the following command

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```
In alternativa, se si crea prima un gruppo e si aggiungono membri, è possibile abilitare il gruppo di Azure AD in fase di creazione usando il comando seguente,

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```

Una corretta creazione di un cluster di Azure AD v2 include la sezione seguente nel corpo della risposta
```
"Azure ADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Il cluster viene creato in pochi minuti.

## <a name="access-an-azure-ad-enabled-cluster"></a>Accedere a un cluster abilitato di Azure ADAccess an Azure AD enabled cluster
Per ottenere le credenziali di amministratore per accedere al cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
Ora usare il comando kubectl get nodes per visualizzare i nodi nel cluster:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

Per ottenere le credenziali utente per accedere al cluster:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Segui le istruzioni per accedere.

Si riceve: **È necessario effettuare l'accesso al server (Non autorizzato)**

L'utente precedente riceve un errore perché l'utente non fa parte di un gruppo che ha accesso al cluster.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul controllo degli [accessi in base al ruolo][azure-ad-rbac]di Azure AD .

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
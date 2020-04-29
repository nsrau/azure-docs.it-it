---
title: Usare Azure AD nel servizio Azure Kubernetes
description: Informazioni su come usare Azure AD in Azure Kubernetes Service (AKS)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: b121830192a2b88185bbbbc9a92934e51b32a61c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81114656"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Integrare Azure AD nel servizio Azure Kubernetes (anteprima)

> [!Note]
> I cluster AKS V1 esistenti con l'integrazione di AD non sono interessati dalla nuova esperienza AKS V2.

Azure AD integrazione con AKS V2 è progettata per semplificare l'integrazione Azure AD con l'esperienza AKS V1, in cui gli utenti erano necessari per creare un'app client, un'app Server e il tenant Azure AD necessario per concedere le autorizzazioni di lettura directory. Nella nuova versione, il provider di risorse AKS gestisce automaticamente le app client e server.

## <a name="limitations"></a>Limitazioni

* Attualmente non è possibile aggiornare un cluster AKS V1 Azure AD abilitato all'esperienza V2.

> [!IMPORTANT]
> Le funzionalità di anteprima del servizio contenitore di servizi sono disponibili in base al consenso esplicito. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto tecnico del cliente in base al massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per ulteriori informazioni, vedere gli articoli di supporto seguenti:
>
> - [Criteri di supporto AKS](support-policies.md)
> - [Domande frequenti relative al supporto tecnico Azure](faq.md)

## <a name="before-you-begin"></a>Prima di iniziare

È necessario che siano installate le risorse seguenti:

- INTERFACCIA della riga di comando di Azure, versione 2.2.0 o successiva
- Estensione 0.4.38 AKS-Preview
- Kubectl con una versione minima di [1,18 beta](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#client-binaries)

Per installare o aggiornare l'estensione AKS-Preview o versioni successive, usare i seguenti comandi dell'interfaccia della riga di comando di Azure:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Per installare kubectl, usare quanto segue:

```azurecli
sudo az aks install-cli
kubectl version --client
```

Usare [queste istruzioni](https://kubernetes.io/docs/tasks/tools/install-kubectl/) per altri sistemi operativi.

> [!CAUTION]
> Al termine della registrazione di una funzionalità in una sottoscrizione, non è possibile annullare la registrazione di tale funzionalità. Quando si abilitano alcune funzionalità di anteprima, è possibile usare i valori predefiniti per tutti i cluster AKS creati successivamente nella sottoscrizione. Non abilitare le funzionalità di anteprima nelle sottoscrizioni di produzione. Usare invece una sottoscrizione separata per testare le funzionalità di anteprima e raccogliere commenti e suggerimenti.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Potrebbero essere necessari alcuni minuti prima che lo stato venga visualizzato come **registrato**. È possibile controllare lo stato della registrazione usando il comando [AZ feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Quando lo stato viene visualizzato come registrato, aggiornare la registrazione del `Microsoft.ContainerService` provider di risorse usando il comando [AZ provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Creare un cluster AKS con Azure AD abilitato

È ora possibile creare un cluster AKS usando i comandi dell'interfaccia della riga di comando seguenti.

Creare prima di tutto un gruppo di risorse di Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Quindi, creare un cluster AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
Il comando precedente crea un cluster AKS a tre nodi, ma l'utente che ha creato il cluster, per impostazione predefinita, non è un membro di un gruppo che ha accesso a questo cluster. Questo utente deve creare un gruppo di Azure AD, aggiungersi come membro del gruppo e quindi aggiornare il cluster come illustrato di seguito. Seguire le istruzioni riportate [qui](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)

Una volta creato un gruppo che è stato aggiunto (e altri) come membro, è possibile aggiornare il cluster con il gruppo Azure AD usando il comando seguente

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```
In alternativa, se si crea prima un gruppo e si aggiungono membri, è possibile abilitare il gruppo di Azure AD in fase di creazione usando il comando seguente:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

Una corretta creazione di un cluster Azure AD V2 ha la sezione seguente nel corpo della risposta
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

Il cluster viene creato entro pochi minuti.

## <a name="access-an-azure-ad-enabled-cluster"></a>Accedere a un cluster Azure AD abilitato
Per ottenere le credenziali di amministratore per accedere al cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
A questo punto usare il comando kubectl Get Nodes per visualizzare i nodi nel cluster:

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
Seguire le istruzioni per l'accesso.

Si riceve: **è necessario essere connessi al server (non autorizzato)**

L'utente precedente genera un errore perché l'utente non fa parte di un gruppo che ha accesso al cluster.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul [controllo degli accessi in base al ruolo Azure ad][azure-ad-rbac].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md

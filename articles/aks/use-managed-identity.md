---
title: Usare identità gestite in Azure Kubernetes Service
description: Informazioni su come usare le identità gestite in Azure Kubernetes Service (AKS)
services: container-service
author: saudas
manager: saudas
ms.service: container-service
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.openlocfilehash: 16a1f3b8f9ef036bcdc9af122243d313186f99f1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885327"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>Anteprima: usare identità gestite in Azure Kubernetes Service

Attualmente, un cluster Azure Kubernetes Service (AKS) (in particolare, il provider di cloud Kubernetes) richiede un' *entità servizio* per creare risorse aggiuntive, ad esempio servizi di bilanciamento del carico e Managed disks in Azure. È necessario fornire un'entità servizio o AKS crearne uno per conto dell'utente. Le entità servizio in genere hanno una data di scadenza. I cluster raggiungono infine uno stato in cui è necessario rinnovare l'entità servizio per far funzionare il cluster. La gestione delle entità servizio aggiunge complessità.

Le *identità gestite* sono essenzialmente un wrapper per le entità servizio e semplificano la gestione. Per altre informazioni, vedere informazioni sulle [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

AKS crea due identità gestite:

- **Identità gestita assegnata dal sistema**: identità usata dal provider di servizi cloud Kubernetes per creare risorse di Azure per conto dell'utente. Il ciclo di vita dell'identità assegnata dal sistema è associato a quello del cluster. L'identità viene eliminata quando il cluster viene eliminato.
- **Identità gestita assegnata dall'utente**: identità usata per l'autorizzazione nel cluster. Ad esempio, l'identità assegnata dall'utente viene usata per autorizzare AKS a usare i record di controllo di accesso (ACRs) o per autorizzare il kubelet a ottenere i metadati da Azure.

In questo periodo di anteprima, è ancora necessaria un'entità servizio. Viene usato per l'autorizzazione dei componenti aggiuntivi, ad esempio il monitoraggio, i nodi virtuali, i criteri di Azure e il routing dell'applicazione HTTP. Il lavoro è in corso per rimuovere la dipendenza dei componenti aggiuntivi sul nome dell'entità servizio (SPN). Infine, il requisito di un nome SPN in AKS verrà rimosso completamente.

> [!IMPORTANT]
> Le funzionalità di anteprima del servizio contenitore di servizi sono disponibili in base al consenso esplicito. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto tecnico per il massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per ulteriori informazioni, vedere gli articoli di supporto seguenti:
>
> - [Criteri di supporto AKS](support-policies.md)
> - [Domande frequenti relative al supporto tecnico Azure](faq.md)

## <a name="before-you-begin"></a>Prima di iniziare

È necessario che siano installate le risorse seguenti:

- INTERFACCIA della riga di comando di Azure, versione 2.0.70 o successiva
- Estensione 0.4.14 AKS-Preview

Per installare l'estensione AKS-Preview 0.4.14 o versione successiva, usare i seguenti comandi dell'interfaccia della riga di comando di Azure:

```azurecli
az extension update --name aks-preview
az extension list
```

> [!CAUTION]
> Al termine della registrazione di una funzionalità in una sottoscrizione, non è possibile annullare la registrazione di tale funzionalità. Quando si abilitano alcune funzionalità di anteprima, è possibile usare i valori predefiniti per tutti i cluster AKS creati successivamente nella sottoscrizione. Non abilitare le funzionalità di anteprima nelle sottoscrizioni di produzione. Usare invece una sottoscrizione separata per testare le funzionalità di anteprima e raccogliere commenti e suggerimenti.

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

Potrebbero essere necessari alcuni minuti prima che lo stato venga visualizzato come **registrato**. È possibile controllare lo stato della registrazione usando il comando [AZ feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

Quando lo stato viene visualizzato come registrato, aggiornare la registrazione del provider di risorse `Microsoft.ContainerService` usando il comando [AZ provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>Creare un cluster AKS con identità gestite

È ora possibile creare un cluster AKS con identità gestite usando i comandi dell'interfaccia della riga di comando seguenti.

Creare prima di tutto un gruppo di risorse di Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Quindi, creare un cluster AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

Ottenere infine le credenziali per accedere al cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Il cluster verrà creato in pochi minuti. È quindi possibile distribuire i carichi di lavoro dell'applicazione nel nuovo cluster e interagire con esso esattamente come è stato fatto con i cluster AKS basati su entità servizio.

> [!IMPORTANT]
>
> - I cluster AKS con identità gestite possono essere abilitati solo durante la creazione del cluster.
> - Non è possibile aggiornare o aggiornare i cluster AKS esistenti per abilitare le identità gestite.

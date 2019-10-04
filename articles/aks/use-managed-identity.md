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
ms.openlocfilehash: a5717d8ee44e4d2e086a6e7bc1b7c3d0deb614c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827535"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>Anteprima: usare identità gestite in Azure Kubernetes Service

Attualmente, gli utenti devono fornire un'entità servizio o AKS ne crea uno per conto dell'utente, in modo che il cluster AKS (in particolare il provider di servizi cloud Kubernetes) crei risorse aggiuntive, ad esempio i servizi di bilanciamento del carico e i dischi gestiti in Azure. Le entità servizio vengono in genere create con una data di scadenza. I cluster raggiungeranno infine uno stato in cui l'entità servizio dovrà essere rinnovata in caso contrario, il cluster non funzionerà. La gestione delle entità servizio aggiunge complessità. Le identità gestite sono essenzialmente un wrapper per le entità servizio e semplificano la gestione. Per altre informazioni, vedere l'articolo [identità gestite](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) .

AKS crea due identità gestite una a un'identità gestita assegnata dal sistema e l'altra identità assegnata dall'utente. Un'identità gestita assegnata dal sistema viene usata dal provider di servizi cloud kubernetes per creare risorse di Azure per conto dell'utente. Il ciclo di vita di questa identità gestita assegnata dal sistema è associato a quello del cluster e viene eliminato quando il cluster viene eliminato. AKS crea anche un'identità gestita assegnata dall'utente usata nel cluster per autorizzare AKS ad accedere a ACRs, kubelet per ottenere i metadati da Azure e così via.

In questo periodo di anteprima, è ancora necessaria un'entità servizio. Verrà usato per l'autorizzazione dei componenti aggiuntivi, ad esempio il monitoraggio, il nodo virtuale, i criteri di Azure e il routing dell'applicazione http. È in corso il lavoro per rimuovere la dipendenza dell'addons sul nome SPN e, successivamente, il requisito SPN in AKS verrà rimosso completamente.

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono il consenso esplicito self-service. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto tecnico per il massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per ulteriori informazioni, vedere gli articoli di supporto seguenti:
>
> * [Criteri di supporto AKS](support-policies.md)
> * [Domande frequenti relative al supporto tecnico Azure](faq.md)

## <a name="before-you-begin"></a>Prima di iniziare

È necessario disporre di quanto segue:

* È necessaria anche l'interfaccia della riga di comando di Azure versione 2.0.70 o successiva e l'estensione AKS-Preview 0.4.14

## <a name="install-latest-aks-cli-preview-extension"></a>Installare la versione di anteprima dell'interfaccia della riga di comando AKS

È necessaria l'estensione **0.4.14 di AKS-Preview** o successiva.

```azurecli
az extension update --name aks-preview 
az extension list
```

> [!CAUTION]
> Quando si registra una funzionalità in una sottoscrizione, attualmente non è possibile annullare la registrazione di tale funzionalità. Dopo aver abilitato alcune funzionalità di anteprima, è possibile usare i valori predefiniti per tutti i cluster AKS, quindi creati nella sottoscrizione. Non abilitare le funzionalità di anteprima nelle sottoscrizioni di produzione. Usare una sottoscrizione separata per testare le funzionalità di anteprima e raccogliere commenti e suggerimenti.

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

Potrebbero essere necessari alcuni minuti prima che lo stato venga visualizzato *registrato*. È possibile controllare lo stato della registrazione usando il comando [AZ feature list] [AZ-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

Quando lo stato è registrato, aggiornare la registrazione del provider di risorse *Microsoft. servizio contenitore* usando il comando [AZ provider Register] [AZ-provider-Register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identity"></a>Creare un cluster AKS con identità gestita

È ora possibile creare un cluster AKS con identità gestite usando il comando CLI seguente
```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

## <a name="create-an-aks-cluster"></a>Creare un cluster del servizio Azure Container
```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

## <a name="get-credentials-to-access-the-cluster"></a>Ottenere le credenziali per accedere al cluster
```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Una volta creato il cluster in pochi minuti, è possibile distribuire i carichi di lavoro dell'applicazione e interagire con esso così come sono stati i cluster AKS basati su entità servizio. 

> [!IMPORTANT]
> * I cluster AKS con identità gestite possono essere abilitati solo durante la creazione del cluster
> * Non è possibile aggiornare o aggiornare i cluster AKS esistenti per abilitare le identità gestite
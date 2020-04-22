---
title: Usare le identità gestite nel servizio Kubernetes di AzureUse managed identities in Azure Kubernetes Service
description: Informazioni su come usare le identità gestite nel servizio Azure Kubernetes (AKS)Learn how to use managed identities in Azure Kubernetes Service (AKS)
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 04/02/2020
ms.author: saudas
ms.openlocfilehash: 907aa83bc293aacd9920d8fd79a1b3184dd1d5dc
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767589"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Usare le identità gestite nel servizio Kubernetes di AzureUse managed identities in Azure Kubernetes Service

Attualmente, un cluster del servizio Azure Kubernetes (AKS) (in particolare, il provider cloud Kubernetes) richiede un'identità per creare risorse aggiuntive, ad esempio servizi di bilanciamento del carico e dischi gestiti in Azure, questa identità può essere *un'identità gestita* o un'entità *servizio.* Se si utilizza [un'entità servizio](kubernetes-service-principal.md), è necessario specificarne una o AKS ne crea una per conto dell'utente. Se si utilizza l'identità gestita, questa verrà creata automaticamente da AKS. I cluster che utilizzano entità servizio raggiungono infine uno stato in cui l'entità servizio deve essere rinnovata per mantenere il funzionamento del cluster. La gestione delle entità servizio aggiunge complessità ed è per questo che è più semplice usare le identità gestite. Gli stessi requisiti di autorizzazione si applicano sia alle entità servizio che alle identità gestite.

*Le identità gestite* sono essenzialmente un wrapper per le entità servizio e semplificano la gestione. Per altre informazioni, vedere Identità gestite per le risorse di Azure.To learn more, read about [managed identities for Azure resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

AKS crea due identità gestite:

- **Identità gestita assegnata dal sistema:** identità usata dal provider di cloud Kubernetes per creare risorse di Azure per conto dell'utente. Il ciclo di vita dell'identità assegnata dal sistema è legato a quello del cluster. L'identità viene eliminata quando il cluster viene eliminato.
- **Identità gestita assegnata**dall'utente: identità utilizzata per l'autorizzazione nel cluster. Ad esempio, l'identità assegnata dall'utente viene usata per autorizzare AKS a usare i registri contenitori di Azure (ACR) o per autorizzare il kubelet a ottenere metadati da Azure.For example, the user-assigned identity is used to authorize AKS to use Azure Container Registries (ACR) and to authorize the kubelet to get metadata from Azure.

I componenti aggiuntivi eseguono anche l'autenticazione utilizzando un'identità gestita. Per ogni componente aggiuntivo, un'identità gestita viene creata da AKS e dura per tutta la durata del componente aggiuntivo. Per creare e usare la propria rete virtuale, l'indirizzo IP statico o il disco di Azure collegato in cui le risorse si trovano all'esterno del gruppo di risorse MC_, usare il PrincipalID del cluster per eseguire un'assegnazione di ruolo. Per altre informazioni sull'assegnazione di ruolo, vedere [Delegare l'accesso ad altre risorse](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)di Azure.For more information on role assignment, see Delegate access to other Azure resources.

## <a name="before-you-begin"></a>Prima di iniziare

È necessario disporre della seguente risorsa installata:

- Interfaccia della riga di comando di Azure, versione 2.2.0 o successiva

## <a name="create-an-aks-cluster-with-managed-identities"></a>Creare un cluster AKS con identità gestiteCreate an AKS cluster with managed identities

È ora possibile creare un cluster AKS con identità gestite usando i comandi dell'interfaccia della riga di comando seguenti.

Creare innanzitutto un gruppo di risorse di Azure innanzitutto:First, create an Azure resource group:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Quindi, creare un cluster AKS:Then, create an AKS cluster:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

Una corretta creazione del cluster tramite identità gestite contiene le informazioni sul profilo dell'entità servizio:A successful cluster creation using managed identities contains this service principal profile information:

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

Infine, ottenere le credenziali per accedere al cluster:Finally, get credentials to access the cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Il cluster verrà creato in pochi minuti. È quindi possibile distribuire i carichi di lavoro dell'applicazione nel nuovo cluster e interagire con esso come è stato eseguito con i cluster AKS basati su entità servizio.

> [!IMPORTANT]
>
> - I cluster AKS con identità gestite possono essere abilitati solo durante la creazione del cluster.
> - I cluster AKS esistenti non possono essere aggiornati per abilitare le identità gestite.

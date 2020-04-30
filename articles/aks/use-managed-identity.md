---
title: Usare identità gestite in Azure Kubernetes Service
description: Informazioni su come usare le identità gestite in Azure Kubernetes Service (AKS)
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 04/02/2020
ms.author: saudas
ms.openlocfilehash: 00ecc077ba55ab9f91fc58f8a47fcdf7440deea6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82112967"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Usare identità gestite in Azure Kubernetes Service

Attualmente, un cluster Azure Kubernetes Service (AKS) (in particolare, il provider di cloud Kubernetes) richiede un'identità per creare risorse aggiuntive, ad esempio servizi di bilanciamento del carico e dischi gestiti in Azure, questa identità può essere un' *identità gestita* o un' *entità servizio*. Se si usa un' [entità servizio](kubernetes-service-principal.md), è necessario specificare una o AKS per crearne una per conto dell'utente. Se si usa l'identità gestita, questa verrà creata automaticamente da AKS. I cluster che usano entità servizio raggiungono infine uno stato in cui l'entità servizio deve essere rinnovata per tenere il cluster funzionante. La gestione delle entità servizio aggiunge complessità, motivo per cui è più facile usare le identità gestite. Gli stessi requisiti di autorizzazione si applicano sia per le entità servizio sia per le identità gestite.

Le *identità gestite* sono essenzialmente un wrapper per le entità servizio e semplificano la gestione. Per altre informazioni, vedere informazioni sulle [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

AKS crea due identità gestite:

- **Identità gestita assegnata dal sistema**: identità usata dal provider di servizi cloud Kubernetes per creare risorse di Azure per conto dell'utente. Il ciclo di vita dell'identità assegnata dal sistema è associato a quello del cluster. L'identità viene eliminata quando il cluster viene eliminato.
- **Identità gestita assegnata dall'utente**: identità usata per l'autorizzazione nel cluster. Ad esempio, l'identità assegnata dall'utente viene usata per autorizzare AKS a usare i registri contenitori di Azure (ACRs) o per autorizzare il kubelet per ottenere i metadati da Azure.

Anche i componenti aggiuntivi eseguono l'autenticazione usando un'identità gestita. Per ogni componente aggiuntivo, un'identità gestita viene creata da AKS e dura per la durata del componente aggiuntivo. 

## <a name="before-you-begin"></a>Prima di iniziare

È necessario che sia installata la seguente risorsa:

- INTERFACCIA della riga di comando di Azure, versione 2.2.0 o successiva

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

La creazione di un cluster con identità gestite contiene le informazioni sul profilo dell'entità servizio seguenti:

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

> [!NOTE]
> Per creare e usare il proprio VNet, un indirizzo IP statico o un disco di Azure collegato dove le risorse si trovano all'esterno del gruppo di risorse MC_ *, usare il PrincipalID dell'identità gestita assegnata dal sistema cluster per eseguire un'assegnazione di ruolo. Per altre informazioni sull'assegnazione di ruolo, vedere [delegare l'accesso ad altre risorse di Azure](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> Le concessioni di autorizzazioni per l'identità gestita del cluster usata dal provider di servizi cloud di Azure possono richiedere fino a 60 minuti.

Ottenere infine le credenziali per accedere al cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Il cluster verrà creato in pochi minuti. È quindi possibile distribuire i carichi di lavoro dell'applicazione nel nuovo cluster e interagire con esso esattamente come è stato fatto con i cluster AKS basati su entità servizio.

> [!IMPORTANT]
>
> - I cluster AKS con identità gestite possono essere abilitati solo durante la creazione del cluster.
> - Non è possibile aggiornare o aggiornare i cluster AKS esistenti per abilitare le identità gestite.

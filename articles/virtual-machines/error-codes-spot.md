---
title: Codici di errore per le istanze di macchine virtuali e set di scalabilità di Azure SpotError codes for Azure Spot VMs and scale set instances
description: Informazioni sui codici di errore che è possibile visualizzare quando si usano macchine virtuali Spot e istanze del set di scalabilità.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 5a34dc2b9468c6c5af4af0e0addfd8b9ebb7e792
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547810"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>Messaggi di errore per macchine virtuali spot e set di scalabilitàError messages for Spot VMs and scale sets

Ecco alcuni possibili codici di errore che è possibile ricevere quando si usano macchine virtuali Spot e set di scalabilità.


| Chiave | Message | Descrizione |
|-----|---------|-------------|
| SkuNotAvailable | Il livello richiesto\<per\>la risorsa ' risorsa\<\>' non\<è\>attualmente disponibile nella posizione ' posizione ' per la sottoscrizione ' subscriptionID '. Provare un altro livello o distribuirlo in un percorso diverso. | La capacità di Azure Spot in questa posizione non è sufficiente per creare la macchina virtuale o l'istanza del set di scalabilità. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  I criteri di rimozione possono essere impostati solo nelle macchine virtuali di Azure Spot.Eviction policy can be set only on Azure Spot Virtual Machines. | Questa macchina virtuale non è una macchina virtuale Spot, pertanto non è possibile impostare i criteri di rimozione. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  La macchina virtuale di Azure Spot non è supportata nel set di disponibilità. | È necessario scegliere di usare una macchina virtuale Spot o una macchina virtuale in un set di disponibilità, non è possibile scegliere entrambi. |
| AzureSpotFeatureNotEnabledForSubscription  |  Sottoscrizione non abilitata con la funzionalità Azure Spot.Subscription not enabled with Azure Spot feature. | Usare una sottoscrizione che supporti le macchine virtuali Spot.Use a subscription that supports Spot VMs. |
| VMPriorityCannotBeApplied  |  Il valore di{0}priorità specificato ' '{1}non può essere applicato alla macchina virtuale ' poiché non è stata specificata alcuna priorità al momento della creazione della macchina virtuale. | Specificare la priorità quando viene creata la macchina virtuale. |
| Prezzo spotPriceGreaterThanProvidedMaxPrice  |  Impossibile eseguire{0}l'operazione ' '{1} poiché il prezzo massimo{2} fornito ' USD' è{3}inferiore al prezzo spot corrente ' USD' per la dimensione di Azure Spot VM ' '. | Selezionare un prezzo massimo più alto. Per ulteriori informazioni, vedere informazioni sui prezzi per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) o [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| MaxPriceValueInvalid  |  Valore di prezzo massimo non valido. Gli unici valori supportati per il prezzo massimo sono -1 o un decimale maggiore di zero. Il valore massimo del prezzo pari a -1 indica che la macchina virtuale Azure Spot non verrà rimossa per motivi di prezzo. | Immettere un prezzo massimo valido. Per ulteriori informazioni, vedere Prezzi per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) o [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVMs | La modifica massima del prezzo{0}non è consentita quando la macchina virtuale ' ' è attualmente allocata. Si prega di deallocare e riprovare. | Arrestare: deallocare la macchina virtuale in modo da poter modificare il prezzo massimo. |
| MaxPriceChangeNotAllowed | La variazione di prezzo massima non è consentita. | Non è possibile modificare il prezzo massimo per questa macchina virtuale. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Azure Spot non è supportato per questa versione dell'API. | La versione dell'API deve essere 2019-03-01.The API version needs to be 2019-03-01. |
| AzureSpotIsNotSupportedForThisVMSize  |  Azure Spot non è supportato per questa dimensione {0}della macchina virtuale. | Selezionare un'altra dimensione della macchina virtuale. Per ulteriori informazioni, vedere [Macchine virtuali spot](./linux/spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  Il prezzo massimo è supportato solo per le macchine virtuali di Azure Spot.Max price is supported only for Azure Spot Virtual Machines. | Per ulteriori informazioni, vedere [Macchine virtuali spot](./linux/spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  La richiesta Sposta risorse contiene una macchina virtuale Azure Spot.The Move resources request contains an Azure Spot virtual machine. Non supportato attualmente. Controllare i dettagli dell'errore per gli ID della macchina virtuale. | Non è possibile spostare le macchine virtuali Spot. |
| MoveResourcesWithAzureSpotVmsSNotSupported  |  La richiesta Sposta risorse contiene un set di scalabilità di macchine virtuali di Azure Spot.The Move resources request contains an Azure Spot virtual machine scale set. Non supportato attualmente. Controllare i dettagli dell'errore per gli ID del set di scalabilità delle macchine virtuali. | Non è possibile spostare le istanze del gruppo di scale Spot. |
| EphemeralOSDisksNotSupportedForSpotVMs | I dischi del sistema operativo effimeri non sono supportati per le macchine virtuali Spot. | Usare un normale disco del sistema operativo per la macchina virtuale Spot.Use a regular OS disk for your Spot VM. |
| Modalitàdi Orchestrazione di AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | La macchina virtuale di Azure Spot non è supportata nel set di scalabilità di macchine virtuali con la modalità di orchestrazione della macchina virtuale. | Impostare la modalità di orchestrazione sul set di scalabilità della macchina virtuale per usare le istanze Spot.Set the orchestration mode to virtual machine scale set in order to use Spot instances. |


**Passaggi successivi** Per ulteriori informazioni, vedere [Macchine virtuali spot](./linux/spot-vms.md).
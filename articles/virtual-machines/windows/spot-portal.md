---
title: Usare il portale per distribuire le macchine virtuali di Azure SpotUse the portal to deploy Azure Spot VMs
description: Informazioni su come usare Azure PowerShell per distribuire macchine virtuali Spot per risparmiare sui costi.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 045cec080b9b1b8f2e4cb589b053c421897db5be
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547374"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Distribuire macchine virtuali spot tramite il portale di AzureDeploy Spot VMs using the Azure portal

L'uso di [Spot VMs](spot-vms.md) consente di sfruttare la nostra capacità inutilizzata con un notevole risparmio sui costi. In qualsiasi momento in cui Azure richiede la capacità, l'infrastruttura di Azure rimuovere le macchine virtuali Spot.At any in time when Azure needs the capacity back, the Azure infrastructure will evict Spot VMs. Pertanto, le macchine virtuali Spot sono ideali per i carichi di lavoro in grado di gestire interruzioni come processi di elaborazione batch, ambienti di sviluppo/test, carichi di lavoro di elaborazione di grandi dimensioni e altro ancora.

I prezzi per le macchine virtuali Spot sono variabili, in base all'area e all'SKU. Per altre informazioni, vedere Prezzi delle macchine virtuali per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows.](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) Per altre informazioni sull'impostazione del prezzo massimo, vedere [Spot VMs - Pricing](spot-vms.md#pricing).

È possibile impostare un prezzo massimo che si è disposti a pagare, all'ora, per la macchina virtuale. Il prezzo massimo per una macchina virtuale Spot può essere impostato in dollari USA (USD), usando fino a 5 cifre decimali. Ad esempio, `0.05701`il valore sarebbe un prezzo massimo di 0,05701 USD all'ora. Se si imposta il `-1`prezzo massimo su , la macchina virtuale non verrà rimossa in base al prezzo. Il prezzo per la macchina virtuale sarà il prezzo corrente per spot o il prezzo per una macchina virtuale standard, che è sempre inferiore, purché siano disponibili capacità e quote.


## <a name="create-the-vm"></a>Creare la VM

Il processo di creazione di una macchina virtuale che usa macchine virtuali Spot è lo stesso descritto nella [guida introduttiva.](quick-create-portal.md) Quando si distribuisce una macchina virtuale, è possibile scegliere di usare un'istanza di Azure spot.When you are deploying a VM, you can choose to use an Azure spot instance.


Nella sezione Dettagli istanza della scheda **Nozioni di base** No è l'impostazione predefinita per l'uso di un'istanza di Azure spot.On the Basics tab, in the **Instance details** section, **No** is the default for using an Azure spot instance.

![Cattura di schermata per la scelta di no, non usare un'istanza spot di Azure](media/spot-portal/no.png)

Si seleziona **Sì**, la sezione si espande ed è possibile scegliere il tipo di [sfratto e la politica di sfratto](spot-vms.md#eviction-policy). 

![Cattura di schermata per la scelta di sì, usare un'istanza spot di AzureScreen capture for choosing yes, use an Azure spot instance](media/spot-portal/yes.png)


## <a name="next-steps"></a>Passaggi successivi

È anche possibile creare macchine virtuali Spot usando [PowerShell](spot-powershell.md).
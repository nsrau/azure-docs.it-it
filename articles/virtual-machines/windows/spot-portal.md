---
title: Usare il portale per distribuire le VM di Azure spot
description: Informazioni su come usare Azure PowerShell per distribuire macchine virtuali spot per risparmiare sui costi.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/20/2019
ms.author: cynthn
ms.openlocfilehash: 682ee86c373c715080ef1baf82b473242a2e43db
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782047"
---
# <a name="preview-deploy-spot-vms-using-the-azure-portal"></a>Anteprima: distribuire le VM spot usando il portale di Azure

L'uso di [macchine virtuali con spot](spot-vms.md) consente di sfruttare la capacità inutilizzata con un notevole risparmio sui costi. In qualsiasi momento, quando Azure necessita della capacità, l'infrastruttura di Azure eliminerà le VM spot. Quindi, le VM spot sono ottime per i carichi di lavoro in grado di gestire le interruzioni, ad esempio processi di elaborazione batch, ambienti di sviluppo/test, carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

I prezzi per le VM spot sono variabili in base all'area e allo SKU. Per altre informazioni, vedere prezzi delle VM per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Per altre informazioni sull'impostazione del prezzo massimo, vedere [spot VM-prezzi](spot-vms.md#pricing).

È possibile impostare un prezzo massimo che si è disposti a pagare, per ora, per la macchina virtuale. Il prezzo massimo per una VM spot può essere impostato in dollari USA (USD), usando un massimo di 5 cifre decimali. Ad esempio, il valore `0.05701`sarebbe un prezzo massimo di $0,05701 USD all'ora. Se si imposta il prezzo massimo da `-1`, la macchina virtuale non verrà rimossa in base al prezzo. Il prezzo della macchina virtuale corrisponderà al prezzo corrente per spot o al prezzo di una macchina virtuale standard, che sempre è inferiore, purché siano disponibili capacità e quota.

> [!IMPORTANT]
> Le istanze di spot sono attualmente in anteprima pubblica.
> Questa versione di anteprima non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Per la prima parte dell'anteprima pubblica, le istanze di spot avranno un prezzo fisso, quindi non vi saranno eliminazioni basate sul prezzo.

## <a name="create-the-vm"></a>Creare la VM

Il processo di creazione di una macchina virtuale che usa le VM spot è identico a quello illustrato in dettaglio nella [Guida introduttiva](quick-create-portal.md). Quando si distribuisce una macchina virtuale, è possibile scegliere di usare un'istanza di Azure spot.


Nella scheda **nozioni di base** , nella sezione **Dettagli istanza** , **non** è il valore predefinito per l'uso di un'istanza di Azure spot.

![Acquisizione schermo per la scelta di no, non usare un'istanza di Azure spot](media/spot-portal/no.png)

Selezionando **Sì**, la sezione si espande ed è possibile scegliere il [tipo di rimozione e i criteri di rimozione](spot-vms.md#eviction-policy). 

![Acquisizione schermo per la scelta di sì, usare un'istanza di Azure spot](media/spot-portal/yes.png)


## <a name="next-steps"></a>Passaggi successivi

È anche possibile creare macchine virtuali spot usando [PowerShell](spot-powershell.md).
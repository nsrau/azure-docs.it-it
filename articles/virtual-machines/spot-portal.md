---
title: Usare il portale per distribuire le VM di Azure spot
description: Come usare Azure PowerShell per distribuire le VM spot per risparmiare sui costi.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/14/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 0da650646c35a9a663dd29589f963d23cbe552cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91828489"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Distribuire le VM spot usando il portale di Azure

L'uso di [macchine virtuali con spot](spot-vms.md) consente di sfruttare la capacità inutilizzata con un notevole risparmio sui costi. Quando, in qualsiasi momento, Azure avrà di nuovo bisogno di quella capacità, l'infrastruttura di Azure rimuoverà le macchine virtuali spot. Le macchine virtuali spot sono pertanto ideali per i carichi di lavoro in grado di gestire le interruzioni, come i processi di elaborazione batch, gli ambienti di sviluppo/test, i carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

I prezzi per le macchine virtuali spot variano in base all'area e allo SKU. Per altre informazioni, vedere i prezzi delle macchine virtuali per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Per altre informazioni sull'impostazione del prezzo massimo, vedere [spot VM-prezzi](spot-vms.md#pricing).

È possibile impostare un prezzo massimo che si è disposti a pagare, per ora, per la macchina virtuale. Il prezzo massimo per una VM spot può essere impostato in dollari USA (USD), usando un massimo di 5 cifre decimali. Ad esempio, il valore `0.05701` è un prezzo massimo di $0,05701 USD all'ora. Se si imposta il prezzo massimo su `-1`, la macchina virtuale non verrà eliminata in base al prezzo. Il prezzo della macchina virtuale corrisponderà al prezzo corrente per spot o al prezzo di una macchina virtuale standard, a seconda di quale sia il più basso, purché siano disponibili capacità e quota.

Quando la macchina virtuale viene eliminata, è possibile scegliere se eliminare la macchina virtuale e il disco sottostante o deallocare la macchina virtuale in modo che possa essere riavviata in un secondo momento.


## <a name="create-the-vm"></a>Creare la VM

Quando si distribuisce una macchina virtuale, è possibile scegliere di usare un'istanza di Azure spot.


Nella scheda **nozioni di base** , nella sezione **Dettagli istanza** , **non** è il valore predefinito per l'uso di un'istanza di Azure spot.

![Acquisizione schermo per la scelta di no, non usare un'istanza di Azure spot](./media/spot-portal/no.png)

Se si seleziona **Sì**, la sezione si espande ed è possibile scegliere il [tipo di rimozione e i criteri di rimozione](spot-vms.md#eviction-policy). 

![Acquisizione schermo per la scelta di sì, usare un'istanza di Azure spot](./media/spot-portal/yes.png)

È anche possibile confrontare i prezzi e le tariffe di rimozione con altre aree simili selezionando **Visualizza cronologia prezzi e confronta prezzi nelle aree vicine**.

In questo esempio, l'area Canada centrale è meno costosa e ha una velocità di eliminazione inferiore rispetto all'area Stati Uniti orientali.

:::image type="content" source="./media/spot-portal/pricing.png" alt-text="Screenshot delle opzioni relative alle aree con la differenza di prezzi e frequenze di rimozione.":::

È possibile modificare l'area selezionando la scelta più adatta alle proprie esigenze e quindi selezionando **OK**.

## <a name="simulate-an-eviction"></a>Simulare un'eliminazione

È possibile [simulare un'eliminazione](/rest/api/compute/virtualmachines/simulateeviction) di una macchina virtuale spot, per testare il modo in cui l'applicazione effettuerà il ristagno a una rimozione improvvisa. 

Sostituire quanto segue con le informazioni: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

## <a name="next-steps"></a>Passaggi successivi

È anche possibile creare VM spot usando [PowerShell](./windows/spot-powershell.md), l' [interfaccia](./linux/spot-cli.md)della riga di comando o un [modello](./linux/spot-template.md).

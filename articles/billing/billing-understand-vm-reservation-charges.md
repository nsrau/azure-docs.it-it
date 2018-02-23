---
title: Informazioni sull'applicazione dello sconto per le istanze di macchina virtuale riservate di Azure | Microsoft Docs
description: Informazioni su come viene applicato lo sconto per le istanze di macchine virtuali riservate di Azure alle VM in esecuzione.
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikdesai
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: vikdesai
ms.openlocfilehash: 2a3854077c7c8bdb20804c6b3e77500659c3c484
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="understand-how-the-reserved-virtual-machine-instance-discount-is-applied"></a>Informazioni su come viene applicato lo sconto relativo alle istanze di macchine virtuali riservate
Dopo avere acquistato un'istanza di macchina virtuale riservata, lo sconto relativo alla prenotazione viene automaticamente applicato alle macchine virtuali corrispondenti agli attributi e alla quantità della prenotazione. Una prenotazione copre i costi di infrastruttura delle macchine virtuali. La tabella seguente illustra i costi della macchina virtuale dopo l'acquisto di una prenotazione. In tutti i casi, vengono addebitate le normali tariffe di archiviazione e di rete.

| Tipo di macchina virtuale  | Costi con la prenotazione |    
|-----------------------|--------------------------------------------| 
|VM Linux senza software aggiuntivo | La prenotazione copre i costi di infrastruttura delle VM.|
|VM Linux con costi relativi a software (ad esempio, Red Hat) | La prenotazione copre i costi di infrastruttura. Viene addebitato il software aggiuntivo.|
|VM Windows senza software aggiuntivo |La prenotazione copre i costi di infrastruttura. Viene addebitato il software Windows.|
|VM Windows con software aggiuntivo (ad esempio, SQL server) | La prenotazione copre i costi di infrastruttura. Vengono addebitati il software Windows e il software aggiuntivo.|
|VM Windows con [Vantaggio Azure Hybrid](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | La prenotazione copre i costi di infrastruttura. I costi del software Windows sono coperti dal vantaggio Azure Hybrid. Eventuali software aggiuntivi vengono addebitati separatamente.| 

## <a name="application-of-reservation-discount-to-non-windows-vms"></a>Applicazione dello sconto relativo alla prenotazione a VM non Windows
 Lo sconto relativo alla prenotazione viene applicato alle istanze di macchine virtuali in esecuzione su base oraria. Le prenotazioni acquistate vengono associate all'utilizzo emesso dalle VM in esecuzione per applicare lo sconto relativo a una prenotazione. In caso di macchine virtuali in esecuzione per meno di un'ora, la prenotazione verrà integrata con altre istanze di macchine virtuali che non utilizzano una prenotazione, tra cui macchine virtuali in esecuzione simultanea. Allo scadere dell'ora, viene bloccata l'applicazione della prenotazione per le macchine virtuali. Qualora una macchina virtuale non venga eseguita per un'ora o più macchine virtuali in esecuzione simultanea non raggiungano l'ora della prenotazione, tale prenotazione sarà sottoutilizzata per quell'ora. Il grafico seguente illustra l'applicazione di una prenotazione all'utilizzo fatturabile delle VM. L'illustrazione si basa sull'acquisto di una prenotazione e su due istanze di VM corrispondenti.

![Applicazione di istanze di macchine virtuali riservate](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  Per l'utilizzo al di sopra della linea dell'istanza di macchina virtuale riservata vengono addebitate le normali tariffe con pagamento in base al consumo. L'utilizzo al di sotto della linea dell'istanza di macchina virtuale riservata non viene addebitato perché è già stato pagato con l'acquisto della prenotazione.
2.  Nell'ora 1 l'istanza 1 viene eseguita per 0,75 ore e l'istanza 2 per 0,5 ore. L'utilizzo totale per l'ora 1 è di 1,25 ore. Vengono addebitate le tariffe con pagamento in base al consumo per le rimanenti 0,25 ore.
3.  Nell'ora 2 e nell'ora 3 ognuna delle istanze è stata eseguita per 1 ora. Un'istanza è coperta dalla prenotazione e per l'altra vengono addebitate le tariffe con pagamento in base al consumo.
4.  Nell'ora 4 l'istanza 1 viene eseguita per 0,5 ore e l'istanza 2 per 1 ora. L'istanza 1 è completamente coperta dalla prenotazione, come anche 0,5 ore dell'istanza 2. Viene addebitata la tariffa con pagamento in base al consumo per le rimanenti 0,5 ore.

Per informazioni sull'applicazione delle prenotazioni nei report sull'utilizzo per la fatturazione, vedere [Informazioni sull'utilizzo di istanze di macchine virtuali riservate](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reservation-discount-to-windows-vms"></a>Applicazione dello sconto relativo alla prenotazione a VM Windows
Quando si eseguono istanze di VM Windows, viene applicata la prenotazione per coprire i costi di infrastruttura. L'applicazione della prenotazione ai costi di infrastruttura per le VM Windows è uguale a quella per le VM non Windows. Il software Windows viene addebitato separatamente per ogni singola vCPU. Vedere [Costi del software Windows con le prenotazioni](https://go.microsoft.com/fwlink/?linkid=862756). È possibile coprire i costi di licenza di Windows con [Vantaggio Azure Hybrid per Windows Server] (https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle istanze di macchine virtuali riservate, vedere gli articoli seguenti.

- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gestire le istanze di macchina virtuale riservate](billing-manage-reserved-vm-instance.md)
- [Risparmiare sui costi delle macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](billing-save-compute-costs-reservations.md)
- [Informazioni su Utilizzo istanze riservate per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Informazioni su Utilizzo istanze riservate per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Costi del software Windows non inclusi nelle istanze riservate](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

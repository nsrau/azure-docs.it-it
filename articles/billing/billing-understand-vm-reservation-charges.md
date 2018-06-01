---
title: Informazioni sullo sconto delle istanze riservate di Azure - Fatturazione di Azure | Microsoft Docs
description: Informazioni su come viene applicato lo sconto per le istanze di macchine virtuali riservate di Azure alle macchine virtuali in esecuzione.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: a0800bafc3d6b858387e28a3b75bc7b3a6bfe6e8
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301414"
---
# <a name="understand-how-the-reserved-instance-discount-is-applied"></a>Informazioni su come viene applicato lo sconto delle istanze riservate
Dopo avere acquistato un'istanza di macchina virtuale riservata di Azure, il relativo sconto viene automaticamente applicato alle macchine virtuali corrispondenti agli attributi e alla quantità dell'istanza riservata. Un'istanza riservata copre i costi di infrastruttura delle macchine virtuali. La tabella seguente illustra i costi della macchina virtuale dopo l'acquisto di un'istanza riservata. In tutti i casi, vengono addebitate le normali tariffe di archiviazione e di rete.

| Tipo di macchina virtuale  | Addebiti con l'istanza riservata |    
|-----------------------|--------------------------------------------|
|VM Linux senza software aggiuntivo | L'istanza riservata copre i costi di infrastruttura delle VM.|
|VM Linux con costi relativi a software (ad esempio, Red Hat) | L'istanza riservata copre i costi di infrastruttura. Viene addebitato il software aggiuntivo.|
|VM Windows senza software aggiuntivo |L'istanza riservata copre i costi di infrastruttura. Viene addebitato il software Windows.|
|VM Windows con software aggiuntivo (ad esempio, SQL server) | L'istanza riservata copre i costi di infrastruttura. Vengono addebitati il software Windows e il software aggiuntivo.|
|VM Windows con [Vantaggio Azure Hybrid](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | L'istanza riservata copre i costi di infrastruttura. I costi del software Windows sono coperti dal vantaggio Azure Hybrid. Eventuali software aggiuntivi vengono addebitati separatamente.| 

## <a name="application-of-reserved-instance-discount-to-non-windows-vms"></a>Applicazione dello sconto relativo all'istanza riservata a VM non Windows
 Lo sconto relativo all'istanza riservata viene applicato alle istanze di macchine virtuali in esecuzione su base oraria. Le istanze riservate acquistate vengono associate all'utilizzo emesso dalle VM in esecuzione per applicare lo sconto relativo all'istanza riservata. In caso di macchine virtuali in esecuzione per meno di un'ora, l'istanza riservata verrà integrata con altre istanze di macchine virtuali che non utilizzano un'istanza riservata, tra cui macchine virtuali in esecuzione simultanea. Allo scadere dell'ora, viene bloccata l'applicazione dell'istanza riservata per le macchine virtuali. Qualora una macchina virtuale non venga eseguita per un'ora o più macchine virtuali in esecuzione simultanea non raggiungano l'ora dell'istanza riservata, questa sarà sottoutilizzata per quell'ora. Il grafico seguente illustra l'applicazione di un'istanza riservata all'uso fatturabile delle VM. L'illustrazione si basa sull'acquisto di un'istanza riservata e su due istanze di VM corrispondenti.

![Screenshot di un'istanza riservata applicata e di due istanze di macchina virtuale corrispondenti](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  Per l'uso al di sopra della linea dell'istanza riservata vengono addebitate le normali tariffe con pagamento in base al consumo. L'uso al di sotto della linea dell'istanza riservata non viene addebitato perché è già stato pagato con l'acquisto dell'istanza riservata.
2.  Nell'ora 1 l'istanza 1 viene eseguita per 0,75 ore e l'istanza 2 per 0,5 ore. L'utilizzo totale per l'ora 1 è di 1,25 ore. Vengono addebitate le tariffe con pagamento in base al consumo per le rimanenti 0,25 ore.
3.  Nell'ora 2 e nell'ora 3 ognuna delle istanze è stata eseguita per 1 ora. Un'istanza è coperta dall'istanza riservata e per l'altra vengono addebitate le tariffe con pagamento in base al consumo.
4.  Nell'ora 4 l'istanza 1 viene eseguita per 0,5 ore e l'istanza 2 per 1 ora. L'istanza 1 è completamente coperta dall'istanza riservata, come anche 0,5 ore dell'istanza 2. Viene addebitata la tariffa con pagamento in base al consumo per le rimanenti 0,5 ore.

Per informazioni sull'applicazione delle istanze riservate nei report sull'utilizzo per la fatturazione, vedere le [informazioni sull'utilizzo di istanze riservate](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reserved-instance-discount-to-windows-vms"></a>Applicazione dello sconto relativo all'istanza riservata a VM Windows
Quando si eseguono istanze di VM Windows, viene applicata l'istanza riservata per coprire i costi di infrastruttura. L'applicazione dell'istanza riservata ai costi di infrastruttura delle macchine virtuali per le VM Windows è uguale a quella per le VM non Windows. Il software Windows viene addebitato separatamente per ogni singola vCPU. Vedere [Costi del software Windows con le istanze riservate](https://go.microsoft.com/fwlink/?linkid=862756). È possibile coprire i costi di licenza di Windows con [Vantaggio Azure Hybrid per Windows Server] (https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle istanze riservate, vedere gli articoli seguenti:

- [Risparmiare sui costi delle macchine virtuali tramite le istanze riservate di Azure](billing-save-compute-costs-reservations.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze riservate](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gestire le istanze riservate](billing-manage-reserved-vm-instance.md)
- [Informazioni su come viene applicato lo sconto relativo alle istanze riservate](billing-understand-vm-reservation-charges.md)
- [Informazioni su Utilizzo istanze riservate per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Informazioni su Utilizzo istanze riservate per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Informazioni sull'utilizzo di istanze riservate per sottoscrizioni CSP](https://docs.microsoft.com/partner-center/azure-reservations)
- [Costi del software Windows non inclusi nelle istanze riservate](billing-reserved-instance-windows-software-costs.md)


## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

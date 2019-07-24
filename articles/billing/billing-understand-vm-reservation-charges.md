---
title: Informazioni sullo sconto delle istanze di macchina virtuale riservate di Azure | Microsoft Docs
description: Informazioni su come viene applicato lo sconto per le istanze di macchine virtuali riservate di Azure alle macchine virtuali in esecuzione.
author: yashesvi
manager: yashar
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2019
ms.author: banders
ms.openlocfilehash: 191160035f516d818d5537c5c47f9604998c46f7
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849982"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Come viene applicato lo sconto di prenotazione di Azure alle macchine virtuali

Dopo avere acquistato un'istanza di macchina virtuale riservata di Azure, lo sconto sulla prenotazione viene automaticamente applicato alle macchine virtuali corrispondenti agli attributi e alla quantità della prenotazione. Una prenotazione copre i costi di calcolo delle macchine virtuali.

Uno sconto sulla prenotazione si applica alle macchine virtuali di base acquistate da Azure Marketplace.

Per la capacità riservata del database SQL, vedere [Informazioni sullo sconto delle istanze riservate di Azure](billing-understand-reservation-charges.md).

La tabella seguente illustra i costi della macchina virtuale dopo l'acquisto di un'istanza di macchina virtuale riservata. In tutti i casi, vengono addebitate le normali tariffe di archiviazione e di rete.

| Tipo di macchina virtuale  | Addebiti con l'istanza di macchina virtuale riservata |
|-----------------------|--------------------------------------------|
|VM Linux senza software aggiuntivo | La prenotazione copre i costi di infrastruttura delle macchine virtuali.|
|VM Linux con costi relativi a software (ad esempio, Red Hat) | La prenotazione copre i costi di infrastruttura. Viene addebitato il software aggiuntivo.|
|VM Windows senza software aggiuntivo |La prenotazione copre i costi di infrastruttura. Viene addebitato il software Windows.|
|VM Windows con software aggiuntivo (ad esempio, SQL server) | La prenotazione copre i costi di infrastruttura. Vengono addebitati il software Windows e il software aggiuntivo.|
|VM Windows con [Vantaggio Azure Hybrid](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | La prenotazione copre i costi di infrastruttura. I costi del software Windows sono coperti dal vantaggio Azure Hybrid. Eventuali software aggiuntivi vengono addebitati separatamente.|

## <a name="how-reservation-discount-is-applied"></a>Come viene applicato lo sconto della prenotazione

Uno sconto sulla prenotazione è "*use-it-or-lose-it*". Quindi, se non si hanno risorse corrispondenti per un'ora, si perderà la quantità di prenotazione per quell'ora. Non è possibile portare in avanti ore riservate inutilizzate.

Quando si arresta una risorsa, lo sconto della prenotazione si applica automaticamente a un'altra risorsa corrispondente nell'ambito specificato. Se non vengono trovate risorse corrispondenti nell'ambito specificato, le ore riservate vengono *perse*.

## <a name="reservation-discount-for-non-windows-vms"></a>Sconto di prenotazione per macchine virtuali non Windows

 Lo sconto relativo alla prenotazione di Azure viene applicato alle istanze di macchine virtuali in esecuzione su base oraria. Le prenotazioni acquistate vengono associate all'utilizzo emesso dalle VM in esecuzione per applicare lo sconto relativo a una prenotazione. In caso di macchine virtuali in esecuzione per meno di un'ora, la prenotazione verrà integrata con altre istanze di macchine virtuali che non utilizzano una prenotazione, tra cui macchine virtuali in esecuzione simultanea. Allo scadere dell'ora, viene bloccata l'applicazione della prenotazione per le macchine virtuali. Qualora una macchina virtuale non venga eseguita per un'ora o più macchine virtuali in esecuzione simultanea non raggiungano l'ora della prenotazione, tale prenotazione sarà sottoutilizzata per quell'ora. Il grafico seguente illustra l'applicazione di una prenotazione all'utilizzo fatturabile delle VM. L'illustrazione si basa sull'acquisto di una prenotazione e su due istanze di VM corrispondenti.

![Screenshot di una prenotazione applicata e di due istanze di macchina virtuale corrispondenti](./media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. Qualsiasi utilizzo sopra la linea di prenotazione viene addebitato alle normali tariffe con pagamento in base al consumo. L'utilizzo al di sotto della linea della prenotazione non viene addebitato perché è già stato pagato con l'acquisto della prenotazione.
2. Nell'ora 1 l'istanza 1 viene eseguita per 0,75 ore e l'istanza 2 per 0,5 ore. L'utilizzo totale per l'ora 1 è di 1,25 ore. Vengono addebitate le tariffe con pagamento in base al consumo per le rimanenti 0,25 ore.
3. Nell'ora 2 e nell'ora 3 ognuna delle istanze è stata eseguita per 1 ora. Un'istanza è coperta dalla prenotazione e per l'altra vengono addebitate le tariffe con pagamento in base al consumo.
4. Nell'ora 4 l'istanza 1 viene eseguita per 0,5 ore e l'istanza 2 per 1 ora. L'istanza 1 è completamente coperta dalla prenotazione, come anche 0,5 ore dell'istanza 2. Viene addebitata la tariffa con pagamento in base al consumo per le rimanenti 0,5 ore.

Per informazioni sull'applicazione delle prenotazioni di Azure nei report sull'utilizzo per la fatturazione, vedere [Understand reservation usage](billing-understand-reserved-instance-usage-ea.md) (Informazioni sull'utilizzo della prenotazione).

## <a name="reservation-discount-for-windows-vms"></a>Sconto di prenotazione per le macchine virtuali Windows

Quando si eseguono istanze di macchine virtuali Windows, viene applicata la prenotazione per coprire i costi di infrastruttura. L'applicazione della prenotazione ai costi di infrastruttura per le VM Windows è uguale a quella per le VM non Windows. Il software Windows viene addebitato separatamente per ogni singola vCPU. Vedere [Costi del software Windows con le prenotazioni](billing-reserved-Instance-windows-software-costs.md). È possibile coprire i costi di licenza di Windows con [Vantaggio Azure Hybrid per Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

## <a name="discount-can-apply-to-different-sizes"></a>Lo sconto può essere applicato a dimensioni diverse

Quando si acquista un'istanza di macchina virtuale riservata, se si seleziona **Ottimizzato per**: **flessibilità delle dimensioni istanza**, la copertura degli sconti dipende dalle dimensioni della macchina virtuale selezionata. La prenotazione può essere applicata alle dimensioni delle macchine virtuali (VM) nello stesso gruppo di serie di dimensioni. Per altre informazioni vedere [Flessibilità di dimensioni delle macchine virtuali con le istanze di macchina virtuale riservate](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

## <a name="discount-applies-to-matching-servicetype-only"></a>Lo sconto si applica solo al ServiceType corrispondente

Uno sconto sulla prenotazione si applica solo all'utilizzo della macchina `ServiceType` virtuale in `AdditionalInfo` cui il valore in corrisponde alla prenotazione acquistata. L'applicazione di sconto della prenotazione ignora il contatore usato per le macchine virtuali `ServiceType`e valuta solo. Informazioni sul tipo di servizio per cui è stata acquistata la macchina virtuale. È possibile scambiare una prenotazione di macchine virtuali di archiviazione non Premium per una prenotazione di archiviazione Premium o in modo contrario.

## <a name="services-that-get-vm-reservation-discounts"></a>Servizi che ricevono sconti per la prenotazione di macchine virtuali

Le prenotazioni della macchina virtuale possono essere applicate all'utilizzo della macchina virtuale emesso da più servizi, non solo per le distribuzioni di macchine virtuali. Le risorse che ricevono sconti per la prenotazione cambiano a seconda dell'impostazione della flessibilità delle dimensioni dell'istanza.

### <a name="instance-size-flexibility-setting"></a>Impostazione della flessibilità delle dimensioni dell'istanza

L'impostazione della flessibilità delle dimensioni dell'istanza determina quali servizi ottengono gli sconti per le istanze riservate.

Se l'impostazione è on o off, gli sconti per la prenotazione si applicano automaticamente a qualsiasi  utilizzo di `Microsoft.Compute`VM corrispondente quando il ConsumedService è. Controllare quindi i dati di utilizzo per il valore *ConsumedService* . Di seguito sono riportati alcuni esempi:

- Macchine virtuali
- set di scalabilità di macchine virtuali
- Servizio contenitore
- Distribuzioni di Azure Batch (in modalità sottoscrizioni utente)
- Servizio Azure Kubernetes
- Service Fabric

Quando l'impostazione è impostata su on, gli sconti per le prenotazioni si applicano automaticamente all'utilizzo della macchina virtuale corrispondente quando *ConsumedService* è uno degli elementi seguenti:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Controllare il valore di *ConsumedService* nei dati di utilizzo per determinare se l'utilizzo è idoneo per gli sconti di prenotazione.

Per altre informazioni sulla flessibilità delle dimensioni delle istanze, vedere [flessibilità delle dimensioni delle macchine virtuali con istanze di VM riservate](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).


## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

Per eventuali domande o per richiedere assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Che cosa sono le prenotazioni per Azure?](billing-save-compute-costs-reservations.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagare in anticipo le risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../sql-database/sql-database-reserved-capacity.md)
- [Gestisci prenotazioni per Azure](billing-manage-reserved-vm-instance.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Informazioni sull'utilizzo della prenotazione per sottoscrizioni CSP](/partner-center/azure-reservations)
- [Costi del software Windows non inclusi nelle prenotazioni](billing-reserved-instance-windows-software-costs.md)

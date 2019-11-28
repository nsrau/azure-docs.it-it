---
title: Informazioni sullo sconto delle istanze di macchina virtuale riservate di Azure | Microsoft Docs
description: Informazioni su come viene applicato lo sconto per le istanze di macchine virtuali riservate di Azure alle macchine virtuali in esecuzione.
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 622960472832d3ebbdfe42d9b3c82e8ede328a5e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225704"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Come viene applicato lo sconto per la prenotazione di Azure alle macchine virtuali

Dopo avere acquistato un'istanza di macchina virtuale riservata di Azure, lo sconto sulla prenotazione viene automaticamente applicato alle macchine virtuali corrispondenti agli attributi e alla quantità della prenotazione. Una prenotazione copre i costi di calcolo delle macchine virtuali.

Lo sconto per la prenotazione si applica alle macchine virtuali di base acquistate da Azure Marketplace.

Per la capacità riservata del database SQL, vedere [Informazioni sullo sconto delle istanze riservate di Azure](billing-understand-reservation-charges.md).

La tabella seguente illustra i costi della macchina virtuale dopo l'acquisto di un'istanza di macchina virtuale riservata. In tutti i casi, vengono addebitate le normali tariffe di archiviazione e di rete.

| Tipo di macchina virtuale  | Addebiti con l'istanza di macchina virtuale riservata |
|-----------------------|--------------------------------------------|
|VM Linux senza software aggiuntivo | La prenotazione copre i costi di infrastruttura delle macchine virtuali.|
|VM Linux con costi relativi a software (ad esempio, Red Hat) | La prenotazione copre i costi di infrastruttura. Viene addebitato il software aggiuntivo.|
|VM Windows senza software aggiuntivo |La prenotazione copre i costi di infrastruttura. Viene addebitato il software Windows.|
|VM Windows con software aggiuntivo (ad esempio, SQL server) | La prenotazione copre i costi di infrastruttura. Vengono addebitati il software Windows e il software aggiuntivo.|
|VM Windows con [Vantaggio Azure Hybrid](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | La prenotazione copre i costi di infrastruttura. I costi del software Windows sono coperti dal vantaggio Azure Hybrid. Eventuali software aggiuntivi vengono addebitati separatamente.|

## <a name="how-reservation-discount-is-applied"></a>Come viene applicato lo sconto per la prenotazione

Lo sconto per la prenotazione si basa sul principio "*use-it-or-lose-it*", ossia se non si usa si perde. Quindi, se non si hanno risorse corrispondenti per un'ora, si perderà la quantità di prenotazione per quell'ora. Non è possibile riportare le ore prenotate inutilizzate.

Quando si arresta una risorsa, lo sconto per la prenotazione si applica automaticamente a un'altra risorsa corrispondente nell'ambito specificato. Se non vengono trovate risorse corrispondenti nell'ambito specificato, le ore prenotate vanno *perse*.

## <a name="reservation-discount-for-non-windows-vms"></a>Sconto per la prenotazione di macchine virtuali non Windows

 Lo sconto relativo alla prenotazione di Azure viene applicato alle istanze di macchine virtuali in esecuzione su base oraria. Le prenotazioni acquistate vengono associate all'utilizzo emesso dalle VM in esecuzione per applicare lo sconto relativo a una prenotazione. In caso di macchine virtuali in esecuzione per meno di un'ora, la prenotazione verrà integrata con altre istanze di macchine virtuali che non utilizzano una prenotazione, tra cui macchine virtuali in esecuzione simultanea. Allo scadere dell'ora, viene bloccata l'applicazione della prenotazione per le macchine virtuali. Qualora una macchina virtuale non venga eseguita per un'ora o più macchine virtuali in esecuzione simultanea non raggiungano l'ora della prenotazione, tale prenotazione sarà sottoutilizzata per quell'ora. Il grafico seguente illustra l'applicazione di una prenotazione all'utilizzo fatturabile delle VM. L'illustrazione si basa sull'acquisto di una prenotazione e su due istanze di VM corrispondenti.

![Screenshot di una prenotazione applicata e di due istanze di macchina virtuale corrispondenti](./media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. Per l'uso al di sopra della linea della prenotazione vengono addebitate le normali tariffe con pagamento in base al consumo. L'utilizzo al di sotto della linea della prenotazione non viene addebitato perché è già stato pagato con l'acquisto della prenotazione.
2. Nell'ora 1 l'istanza 1 viene eseguita per 0,75 ore e l'istanza 2 per 0,5 ore. L'utilizzo totale per l'ora 1 è di 1,25 ore. Vengono addebitate le tariffe con pagamento in base al consumo per le rimanenti 0,25 ore.
3. Nell'ora 2 e nell'ora 3 ognuna delle istanze è stata eseguita per 1 ora. Un'istanza è coperta dalla prenotazione e per l'altra vengono addebitate le tariffe con pagamento in base al consumo.
4. Nell'ora 4 l'istanza 1 viene eseguita per 0,5 ore e l'istanza 2 per 1 ora. L'istanza 1 è completamente coperta dalla prenotazione, come anche 0,5 ore dell'istanza 2. Viene addebitata la tariffa con pagamento in base al consumo per le rimanenti 0,5 ore.

Per informazioni sull'applicazione delle prenotazioni di Azure nei report sull'utilizzo per la fatturazione, vedere [Understand reservation usage](billing-understand-reserved-instance-usage-ea.md) (Informazioni sull'utilizzo della prenotazione).

## <a name="reservation-discount-for-windows-vms"></a>Sconto per la prenotazione di macchine virtuali Windows

Quando si eseguono istanze di macchine virtuali Windows, viene applicata la prenotazione per coprire i costi di infrastruttura. L'applicazione della prenotazione ai costi di infrastruttura per le VM Windows è uguale a quella per le VM non Windows. Il software Windows viene addebitato separatamente per ogni singola vCPU. Vedere [Costi del software Windows con le prenotazioni](billing-reserved-Instance-windows-software-costs.md). È possibile coprire i costi di licenza di Windows con [Vantaggio Azure Hybrid per Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

## <a name="discount-can-apply-to-different-sizes"></a>Lo sconto può essere applicato a diverse dimensioni

Quando si acquista un'istanza di macchina virtuale riservata, se si seleziona **Ottimizzato per**: **flessibilità delle dimensioni istanza**, la copertura degli sconti dipende dalle dimensioni della macchina virtuale selezionata. La prenotazione può essere applicata alle dimensioni delle macchine virtuali (VM) nello stesso gruppo di serie di dimensioni. Per altre informazioni vedere [Flessibilità di dimensioni delle macchine virtuali con le istanze di macchina virtuale riservate](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

## <a name="discount-applies-to-matching-servicetype-only"></a>Lo sconto si applica solo al valore ServiceType corrispondente

Lo sconto per la prenotazione si applica solo all'utilizzo delle macchine virtuali in cui il valore `ServiceType` in `AdditionalInfo` corrisponde alla prenotazione acquistata. L'applicazione dello sconto per la prenotazione ignora il contatore usato per le macchine virtuali e valuta solo il valore `ServiceType`. Conoscere per quale tipo di servizio è stata acquistata la macchina virtuale. È possibile scambiare una prenotazione di macchina virtuale di archiviazione non Premium con una prenotazione di archiviazione Premium o viceversa.

## <a name="services-that-get-vm-reservation-discounts"></a>Servizi che ottengono sconti per la prenotazione di macchine virtuali

Le prenotazioni di macchine virtuali possono essere applicate all'utilizzo di macchine virtuali generato da più servizi, non solo per le proprie distribuzioni di macchine virtuali. Le risorse che ottengono sconti per la prenotazione cambiano a seconda dell'impostazione della flessibilità delle dimensioni dell'istanza.

### <a name="instance-size-flexibility-setting"></a>Impostazione della flessibilità delle dimensioni istanza

L'impostazione della flessibilità delle dimensioni dell'istanza determina quali servizi ottengono gli sconti per le istanze riservate.

Indipendentemente dal fatto che l'impostazione sia attivata o disattivata, gli sconti per la prenotazione vengono applicati automaticamente a qualsiasi utilizzo di macchine virtuali corrispondente quando *ConsumedService* è `Microsoft.Compute`. Controllare quindi i dati di utilizzo per il valore *ConsumedService*. Di seguito sono riportati alcuni esempi:

- Macchine virtuali
- set di scalabilità di macchine virtuali
- Servizio contenitore
- Distribuzioni di Azure Batch (in modalità sottoscrizioni utente)
- Servizio Azure Kubernetes
- Service Fabric

Quando l'impostazione è attivata, gli sconti per la prenotazione vengono applicati automaticamente all'utilizzo di macchine virtuali corrispondente quando *ConsumedService* è uno degli elementi seguenti:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Controllare il valore *ConsumedService* nei dati di utilizzo per determinare se l'utilizzo è idoneo per gli sconti per la prenotazione.

Per altre informazioni sulla flessibilità delle dimensioni istanza, vedere [Flessibilità di dimensioni delle macchine virtuali con le istanze di macchina virtuale riservate](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).


## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Cosa sono le prenotazioni di Azure?](billing-save-compute-costs-reservations.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagare in anticipo le risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../sql-database/sql-database-reserved-capacity.md)
- [Gestire le prenotazioni per Azure](billing-manage-reserved-vm-instance.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Informazioni sull'utilizzo della prenotazione per sottoscrizioni CSP](/partner-center/azure-reservations)
- [Costi del software Windows non inclusi nelle prenotazioni](billing-reserved-instance-windows-software-costs.md)

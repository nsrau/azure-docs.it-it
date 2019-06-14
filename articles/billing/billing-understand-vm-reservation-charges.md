---
title: Informazioni sullo sconto delle istanze di macchina virtuale riservate di Azure | Microsoft Docs
description: Informazioni su come viene applicato lo sconto per le istanze di macchine virtuali riservate di Azure alle macchine virtuali in esecuzione.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: b112dd881d4b2e87e617111d00bc82c6151d7750
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370076"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Modo in cui viene applicato lo sconto di prenotazione Azure alle macchine virtuali

Dopo avere acquistato un'istanza di macchina virtuale riservata di Azure, lo sconto sulla prenotazione viene automaticamente applicato alle macchine virtuali corrispondenti agli attributi e alla quantità della prenotazione. Una prenotazione copre i costi di calcolo delle macchine virtuali.

Uno sconto di prenotazione si applica alle macchine virtuali di base che puoi acquistare da Azure Marketplace.

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

È uno sconto di prenotazione "*utilizzare-it-o-perdere-it*". Pertanto, se non si dispone di risorse corrispondente per ogni ora, quindi si perde una quantità di prenotazione per quell'ora. Non sarà possibile eseguire inoltrare ore riservate non usate.

Quando si arresta una risorsa, lo sconto della prenotazione viene applicato automaticamente a un'altra risorsa corrisponda nell'ambito specificato. Se viene trovata alcuna risorsa corrisponda nell'ambito specificato, quindi sono le ore riservate *persi*.

## <a name="reservation-discount-for-non-windows-vms"></a>Sconto della prenotazione per le macchine virtuali non Windows

 Lo sconto relativo alla prenotazione di Azure viene applicato alle istanze di macchine virtuali in esecuzione su base oraria. Le prenotazioni acquistate vengono associate all'utilizzo emesso dalle VM in esecuzione per applicare lo sconto relativo a una prenotazione. In caso di macchine virtuali in esecuzione per meno di un'ora, la prenotazione verrà integrata con altre istanze di macchine virtuali che non utilizzano una prenotazione, tra cui macchine virtuali in esecuzione simultanea. Allo scadere dell'ora, viene bloccata l'applicazione della prenotazione per le macchine virtuali. Qualora una macchina virtuale non venga eseguita per un'ora o più macchine virtuali in esecuzione simultanea non raggiungano l'ora della prenotazione, tale prenotazione sarà sottoutilizzata per quell'ora. Il grafico seguente illustra l'applicazione di una prenotazione all'utilizzo fatturabile delle VM. L'illustrazione si basa sull'acquisto di una prenotazione e su due istanze di VM corrispondenti.

![Screenshot di una prenotazione applicata e di due istanze di macchina virtuale corrispondenti](./media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. Qualsiasi utilizzo che si trova sopra la linea di prenotazione Ottiene addebitato alle normali tariffe con pagamento a consumo. L'utilizzo al di sotto della linea della prenotazione non viene addebitato perché è già stato pagato con l'acquisto della prenotazione.
2. Nell'ora 1 l'istanza 1 viene eseguita per 0,75 ore e l'istanza 2 per 0,5 ore. L'utilizzo totale per l'ora 1 è di 1,25 ore. Vengono addebitate le tariffe con pagamento in base al consumo per le rimanenti 0,25 ore.
3. Nell'ora 2 e nell'ora 3 ognuna delle istanze è stata eseguita per 1 ora. Un'istanza è coperta dalla prenotazione e per l'altra vengono addebitate le tariffe con pagamento in base al consumo.
4. Nell'ora 4 l'istanza 1 viene eseguita per 0,5 ore e l'istanza 2 per 1 ora. L'istanza 1 è completamente coperta dalla prenotazione, come anche 0,5 ore dell'istanza 2. Viene addebitata la tariffa con pagamento in base al consumo per le rimanenti 0,5 ore.

Per informazioni sull'applicazione delle prenotazioni di Azure nei report sull'utilizzo per la fatturazione, vedere [Understand reservation usage](billing-understand-reserved-instance-usage-ea.md) (Informazioni sull'utilizzo della prenotazione).

## <a name="reservation-discount-for-windows-vms"></a>Sconto della prenotazione per le macchine virtuali Windows

Quando si eseguono istanze di macchine virtuali Windows, viene applicata la prenotazione per coprire i costi di infrastruttura. L'applicazione della prenotazione ai costi di infrastruttura per le VM Windows è uguale a quella per le VM non Windows. Il software Windows viene addebitato separatamente per ogni singola vCPU. Vedere [Costi del software Windows con le prenotazioni](billing-reserved-Instance-windows-software-costs.md). È possibile coprire i costi di licenza di Windows con [Vantaggio Azure Hybrid per Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

## <a name="discount-can-apply-to-different-sizes"></a>Lo sconto è possibile applicare a dimensioni diverse

Quando si acquista un'istanza di macchina virtuale riservata, se si seleziona **Ottimizzato per**: **flessibilità delle dimensioni istanza**, la copertura degli sconti dipende dalle dimensioni della macchina virtuale selezionata. La prenotazione può essere applicata alle dimensioni delle macchine virtuali (VM) nello stesso gruppo di serie di dimensioni. Per altre informazioni vedere [Flessibilità di dimensioni delle macchine virtuali con le istanze di macchina virtuale riservate](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

## <a name="discount-applies-to-matching-servicetype-only"></a>Lo sconto si applica ai corrispondenti solo ServiceType

Uno sconto di prenotazione si applica solo all'utilizzo della macchina virtuale in cui il `ServiceType` valore `AdditionalInfo` corrisponde la prenotazione acquistata. Applicazione dello sconto di prenotazione Ignora riportata dal contatore usata per le macchine virtuali e valuta solo `ServiceType`. Conosce quale servizio che è stato acquistato la macchina virtuale per il tipo. È possibile scambiare una prenotazione della macchina virtuale per una prenotazione di archiviazione premium o in modo inverso di archiviazione non premium.

## <a name="classic-vms-and-cloud-services"></a>Servizi cloud e macchine virtuali classiche

Le istanze di VM riservate automaticamente si applicano a entrambe le macchine virtuali classiche e servizi cloud quando è abilitata la flessibilità di dimensioni di istanza. Per i servizi cloud, lo sconto di prenotazione si applica solo il costo di calcolo. Quando lo sconto della prenotazione viene applicato ai servizi cloud, gli addebiti di utilizzo vengono suddivise in costi di calcolo (misurazione di Linux) e gli addebiti (misurazione di gestione dei servizi cloud) di servizi cloud. Per altre informazioni, vedere [modo in cui lo sconto di prenotazione si applica ai servizi Cloud](billing-reserved-instance-windows-software-costs.md#cloud-services-software-meters-not-included-in-reservation-cost).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Quali sono le prenotazioni per Azure?](billing-save-compute-costs-reservations.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagare in anticipo le risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../sql-database/sql-database-reserved-capacity.md)
- [Gestire le prenotazioni per Azure](billing-manage-reserved-vm-instance.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Informazioni sull'utilizzo della prenotazione per sottoscrizioni CSP](/partner-center/azure-reservations)
- [Costi del software Windows non inclusi nelle prenotazioni](billing-reserved-instance-windows-software-costs.md)

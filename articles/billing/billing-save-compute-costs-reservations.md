---
title: Informazioni sulle prenotazioni di Azure | Microsoft Docs
description: Informazioni sulle prenotazioni e sui prezzi di Azure per risparmiare sui costi di macchine virtuali, database SQL, Azure Cosmos DB e altre risorse.
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
ms.date: 02/06/2019
ms.author: banders
ms.openlocfilehash: a4ad3574849d3c80f671abc93e463b06454289e4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57904168"
---
# <a name="what-are-azure-reservations"></a>Informazioni sulle prenotazioni di Azure

Le prenotazioni di Azure consentono di risparmiare effettuando il pagamento anticipato della capacità di calcolo delle macchine virtuali o dei database SQL, la velocità effettiva di Azure Cosmos DB o altre risorse di Azure per un periodo di uno o tre anni. Il pagamento anticipato consente di ottenere uno sconto sulle risorse usate. Le prenotazioni possono ridurre notevolmente i costi di macchina virtuale, calcolo dei database SQL, Azure Cosmos DB o altre risorse fino al 72% sui prezzi con pagamento a consumo. Le prenotazioni offrono uno sconto a livello di fatturazione e non hanno alcuna ripercussione sullo stato di runtime delle risorse.

È possibile acquistare una prenotazione nel [portale di Azure](https://aka.ms/reservations). Per ulteriori informazioni, vedere gli argomenti seguenti:

- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagare in anticipo le risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../sql-database/sql-database-reserved-capacity.md)
- [Pagare in anticipo le risorse di Azure Cosmos DB con capacità riservata di Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>Perché acquistare una prenotazione?

Se si hanno macchine virtuali, Azure Cosmos DB o database SQL in esecuzione per lunghi periodi di tempo, l'acquisto di una prenotazione è l'opzione più conveniente. Se ad esempio si eseguono costantemente quattro istanze di un servizio senza una prenotazione, verranno applicate le tariffe in base al consumo. Se invece si acquista una prenotazione per tali risorse, si otterrà immediatamente lo sconto per la prenotazione. Per le risorse non verranno più addebitate tariffe in base al consumo.

## <a name="what-charges-does-a-reservation-cover"></a>Quali spese vengono coperte da una prenotazione?

- Istanza di macchina virtuale riservata: una prenotazione copre solo i costi di calcolo della macchina virtuale e non i costi aggiuntivi relativi a software, rete o archiviazione.
- vCore riservato di database SQL: in una prenotazione sono inclusi solo i costi di calcolo. I costi della licenza vengono fatturati separatamente.
- Capacità riservata di Azure Cosmos DB: una prenotazione si applica alla velocità effettiva in cui viene effettuato il provisioning per le risorse e non copre i costi di archiviazione e rete.

Per le macchine virtuali Windows e il database SQL, i costi delle licenze possono essere coperti con le offerte di [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Chi è idoneo all'acquisto di una prenotazione?

Possono acquistare una prenotazione i clienti di Azure con i tipi di sottoscrizione seguenti:

- Tipo di offerta di sottoscrizione Contratto Enterprise (MS-AZR-0017P o MS-AZR-0148P).
- Tipo di offerta di sottoscrizione Pagamento in base al consumo (MS-AZR-003P o MS-AZR-0023P).
- Per acquistare prenotazioni di Azure, i partner CSP (Cloud Solution Provider) possono usare il portale di Azure o il [Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations).

Lo sconto sull'acquisto di prenotazioni viene applicato solo alle risorse con sottoscrizioni di tipo Enterprise, con pagamento in base al consumo o CSP.

 Per acquistare un piano:

- È necessario disporre del ruolo di Proprietario per almeno una sottoscrizione aziendale o con pagamento in base al consumo.

## <a name="how-is-a-reservation-billed"></a>Come viene fatturata una prenotazione?

La prenotazione viene addebitata in base al metodo di pagamento associato alla sottoscrizione. Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Se tale saldo non copre il costo della prenotazione, l'eccedenza verrà fatturata. Se si dispone di una sottoscrizione con pagamento in base al consumo, il costo verrà addebitato immediatamente sulla carta di credito associata all'account. Se invece l'addebito avviene tramite fattura, il costo risulterà visibile sulla fattura successiva.

## <a name="how-is-the-reservation-discount-applied"></a>Come viene applicato lo sconto per la prenotazione?

Lo sconto relativo alla prenotazione viene applicato all'utilizzo delle risorse corrispondenti agli attributi selezionati in fase di acquisto. Gli attributi includono l'ambito in cui vengono eseguiti i database SQL, Azure Cosmos DB, le macchine virtuali o le altre risorse corrispondenti. Se ad esempio si vuole usufruire di uno sconto relativo alla prenotazione per quattro macchine virtuali Standard D2 nell'area Stati Uniti occidentali, selezionare la sottoscrizione in cui sono in esecuzione le macchine virtuali. Se le macchine virtuali sono in esecuzione in diverse sottoscrizioni all'interno della sottoscrizione o dell'account corrente, selezionare l'ambito condiviso. L'ambito condiviso consente l'applicazione dello sconto relativo all'acquisto di istanze riservate tra sottoscrizioni. Dopo aver acquistato una prenotazione è possibile modificare l'ambito. Per altre informazioni, vedere [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md).

Lo sconto sull'acquisto di prenotazioni viene applicato solo alle risorse con sottoscrizioni di tipo Enterprise, con pagamento in base al consumo o CSP. Le risorse eseguite in una sottoscrizione con altri tipi di offerta non prevedono questo tipo di sconto.

Per comprendere meglio l'impatto delle prenotazioni sulla fatturazione, vedere gli argomenti seguenti:

- [Informazioni sullo sconto per le istanze di macchina virtuale riservate di Azure](billing-understand-vm-reservation-charges.md)
- [Comprendere lo sconto sulle prenotazioni di Azure](billing-understand-vm-reservation-charges.md)
- [Informazioni sullo sconto per le prenotazioni di Azure Cosmos DB](billing-understand-cosmosdb-reservation-charges.md)
- [Informazioni sullo sconto per le prenotazioni di Azure e l'utilizzo per SUSE](billing-understand-suse-reservation-charges.md)

## <a name="what-happens-when-the-reservation-term-expires"></a>Cosa accade alla scadenza del periodo di prenotazione?

La scadenza del periodo di prenotazione comporta anche la scadenza dello sconto sulla fatturazione. Al termine di questo periodo, le macchine virtuali, i database SQL, Azure Cosmos DB, o le altre risorse verranno fatturati in base alle tariffe con pagamento in base al consumo. Le prenotazioni di Azure non vengono rinnovate automaticamente. Per continuare a usufruire dello sconto sulla fatturazione è necessario acquistare una nuova prenotazione per i servizi e il software idonei.

## <a name="discount-applies-to-different-sizes-with-instance-size-flexibility"></a>Lo sconto si applica a diverse dimensioni con flessibilità in termini di dimensioni dell'istanza

Quando si acquista una prenotazione, è possibile applicare lo sconto ad altre istanze con gli attributi compresi nello stesso gruppo di dimensioni. La flessibilità della copertura degli sconti dipende dal tipo di prenotazione e dagli attributi scelti all'acquisto della prenotazione.

- Istanze di macchina virtuale riservate: quando si acquista la prenotazione, se si seleziona **Ottimizzato per**: **flessibilità delle dimensioni istanza**, la copertura degli sconti dipende dalle dimensioni delle macchine virtuali selezionate. La prenotazione può essere applicata alle dimensioni delle macchine virtuali (VM) nello stesso gruppo di serie di dimensioni. Per altre informazioni vedere [Flessibilità di dimensioni delle macchine virtuali con le istanze di macchina virtuale riservate](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Piano software SUSE Linux Enterprise: la copertura degli sconti dipende dalle vCPU delle macchine virtuali in cui viene eseguito il software SUSE. Per altre informazioni vedere [Informazioni su come viene applicato lo sconto relativo al piano software SUSE Linux Enterprise](billing-understand-suse-reservation-charges.md).
- Capacità riservata del database SQL: la copertura degli sconti dipende dal livello di prestazioni selezionato. Per altre informazioni, vedere [Informazioni su come viene applicato ai database SQL lo sconto sulla prenotazione](billing-understand-reservation-charges.md).
- Capacità riservata di Azure Cosmos DB: la copertura degli sconti dipende dalla velocità effettiva di cui viene effettuato il provisioning. Per altre informazioni, vedere [Informazioni su come viene applicato lo sconto per la prenotazione ad Azure Cosmos DB](billing-understand-cosmosdb-reservation-charges.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- È possibile iniziare subito a risparmiare sui costi delle macchine virtuali con l'acquisto di un'[istanza di macchina virtuale riservata](../virtual-machines/windows/prepay-reserved-vm-instances.md), di [capacità riservata del database SQL](../sql-database/sql-database-reserved-capacity.md) o di [capacità riservata di Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md).
- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
    - [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md)
    - [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
    - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
    - [Costi del software Windows non inclusi nelle prenotazioni](billing-reserved-instance-windows-software-costs.md)
    - [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)

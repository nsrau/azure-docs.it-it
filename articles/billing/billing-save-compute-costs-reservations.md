---
title: Informazioni sulle prenotazioni di Azure | Microsoft Docs
description: Informazioni sulle prenotazioni e sui prezzi di Azure per risparmiare sui costi di macchine virtuali, database SQL, Azure Cosmos DB e altre risorse.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: b20983c45cd62b9812cdb52de32a6e29da459efe
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370799"
---
# <a name="what-are-azure-reservations"></a>Informazioni sulle prenotazioni di Azure

Le prenotazioni di Azure consentono di risparmiare effettuando il pagamento anticipato della capacità di calcolo delle macchine virtuali o dei database SQL, la velocità effettiva di Azure Cosmos DB o altre risorse di Azure per un periodo di uno o tre anni. Il pagamento anticipato consente di ottenere uno sconto sulle risorse usate. Le prenotazioni possono ridurre notevolmente i costi di macchina virtuale, calcolo dei database SQL, Azure Cosmos DB o altre risorse fino al 72% sui prezzi con pagamento a consumo. Le prenotazioni offrono uno sconto a livello di fatturazione e non hanno alcuna ripercussione sullo stato di runtime delle risorse.

È possibile acquistare una prenotazione nel [portale di Azure](https://aka.ms/reservations).

## <a name="why-buy-a-reservation"></a>Perché acquistare una prenotazione?

Se si dispone di macchine virtuali, Azure Cosmos DB o database SQL eseguiti per lunghi periodi di tempo, acquisto di una prenotazione offre l'opzione più conveniente. Ad esempio, quando si eseguono in modo continuo quattro istanze di un servizio senza una prenotazione, ti viene addebitata in base alle tariffe con pagamento a consumo. Se si acquista una prenotazione per tali risorse, vengono immediatamente visualizzati lo sconto di prenotazione. Per le risorse non verranno più addebitate tariffe in base al consumo.

## <a name="charges-covered-by-reservation"></a>Addebiti coperti dalla prenotazione

Piani di servizio:

- Istanza di macchina virtuale riservata: una prenotazione copre solo i costi di calcolo della macchina virtuale e non i costi aggiuntivi relativi a software, rete o archiviazione.
- Capacità riservata di Azure Cosmos DB: Una prenotazione copre velocità effettiva di provisioning per le risorse. Non copre i costi di archiviazione e rete.
- vCore riservato di database SQL: in una prenotazione sono inclusi solo i costi di calcolo. I costi della licenza vengono fatturati separatamente.

Per le macchine virtuali Windows e il database SQL, i costi delle licenze possono essere coperti con le offerte di [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Chi è idoneo all'acquisto di una prenotazione?

Per acquistare un piano, è necessario disporre di un ruolo di proprietario di sottoscrizione in una sottoscrizione di pagamento a consumo (MS-AZR - 003p o MS-AZR - 0023P) o Enterprise (MS-AZR - 0017p o MS-AZR - 0148p). Cloud solution provider può usare il portale di Azure oppure [Centro per i Partner](/partner-center/azure-reservations) per l'acquisto di prenotazioni di Azure.

I clienti con contratto Enterprise Agreement possono limitare gli acquisti al contratto Enterprise admins disabilitando il **aggiungere le istanze riservate** opzione nel portale EA. Contratti Enterprise admins deve essere un proprietario della sottoscrizione per almeno una sottoscrizione con contratto Enterprise Agreement per acquistare una prenotazione. L'opzione è utile per le aziende che desiderano un team centralizzato per l'acquisto di prenotazioni per diversi reparti. Dopo l'acquisto, centralizzato i team possono aggiungere i proprietari di centro di costo per le prenotazioni. I proprietari possono quindi definire l'ambito la prenotazione per le sottoscrizioni. Il team centrale non deve necessariamente avere accesso proprietario di sottoscrizione in cui viene acquistata la prenotazione.

Lo sconto sull'acquisto di prenotazioni viene applicato solo alle risorse con sottoscrizioni di tipo Enterprise, con pagamento in base al consumo o CSP.

## <a name="reservation-scope"></a>Ambito della prenotazione

Un ambito della prenotazione determina le risorse a cui si applica lo sconto di prenotazione. Un ambito della prenotazione può avere i valori seguenti:

**Ambito condiviso** -lo sconto della prenotazione viene applicato alle risorse corrispondente nelle sottoscrizioni idonee all'interno del contesto di fatturazione.

- Per i clienti con contratto Enterprise, il contesto di fatturazione è la registrazione.
 Per i clienti con pagamento a consumo, l'ambito di fatturazione è tutte le sottoscrizioni idonee create dall'amministratore dell'account.

**Singola sottoscrizione** -lo sconto della prenotazione viene applicato alle risorse corrispondente nella sottoscrizione selezionata.

È possibile [aggiornare l'ambito dopo l'acquisto di una prenotazione](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

## <a name="discounted-subscription-and-offer-types"></a>Tipi di sottoscrizione e dell'offerta scontati

Gli sconti di prenotazione si applicano alle seguenti sottoscrizioni idonee e tipi di offerta.

- Contratto Enterprise agreement (numeri dell'offerta: MS-AZR-0017P o MS-AZR - 0148p)
- Pagamento in base al consumo (numero offerta: MS-AZR-0003P o MS-AZR - 0023 P)
- Sottoscrizioni CSP

Le risorse eseguite in una sottoscrizione con altri tipi di offerta non prevedono questo tipo di sconto.

## <a name="how-is-a-reservation-billed"></a>Come viene fatturata una prenotazione?

La prenotazione viene addebitata in base al metodo di pagamento associato alla sottoscrizione. Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Se tale saldo non copre il costo della prenotazione, l'eccedenza verrà fatturata. Se si dispone di una sottoscrizione con pagamento in base al consumo, il costo verrà addebitato immediatamente sulla carta di credito associata all'account. Se invece l'addebito avviene tramite fattura, il costo risulterà visibile sulla fattura successiva.

## <a name="how-reservation-discount-is-applied"></a>Come viene applicato lo sconto della prenotazione

Per l'utilizzo delle risorse corrispondenti gli attributi selezionati quando si acquista la prenotazione viene applicato lo sconto di prenotazione. Gli attributi includono l'ambito in cui vengono eseguiti i database SQL, Azure Cosmos DB, le macchine virtuali o le altre risorse corrispondenti. Ad esempio, se si desidera uno sconto di prenotazione per quattro macchine virtuali D2 Standard nell'area Stati Uniti occidentali, quindi selezionare la sottoscrizione in cui vengono eseguite le macchine virtuali.

È uno sconto di prenotazione "*utilizzare-it-o-perdere-it*". Se non si dispone delle risorse di corrispondenza per qualsiasi ora, si perde una quantità di prenotazione per quell'ora. Non sarà possibile eseguire inoltrare ore riservate non usate.

Quando si arresta una risorsa, lo sconto della prenotazione viene applicato automaticamente a un'altra risorsa corrisponda nell'ambito specificato. Se viene trovata alcuna risorsa corrisponda nell'ambito specificato, quindi sono le ore riservate *persi*.

In un secondo momento si potrebbe, ad esempio, crea una risorsa e hanno una prenotazione corrisponda che è sottoutilizzata. In questo esempio, lo sconto di prenotazione si applica automaticamente alla nuova risorsa corrisponda.

Se le macchine virtuali sono in esecuzione in diverse sottoscrizioni all'interno della sottoscrizione o dell'account corrente, selezionare l'ambito condiviso. L'ambito condiviso consente l'applicazione dello sconto relativo all'acquisto di istanze riservate tra sottoscrizioni. Dopo aver acquistato una prenotazione è possibile modificare l'ambito. Per altre informazioni, vedere [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md).

Lo sconto sull'acquisto di prenotazioni viene applicato solo alle risorse con sottoscrizioni di tipo Enterprise, con pagamento in base al consumo o CSP. Le risorse eseguite in una sottoscrizione con altri tipi di offerta non prevedono questo tipo di sconto.

## <a name="when-the-reservation-term-expires"></a>Alla scadenza del periodo di prenotazione

La scadenza del periodo di prenotazione comporta anche la scadenza dello sconto sulla fatturazione. Al termine di questo periodo, le macchine virtuali, i database SQL, Azure Cosmos DB, o le altre risorse verranno fatturati in base alle tariffe con pagamento in base al consumo. Le prenotazioni di Azure non vengono rinnovate automaticamente. Per continuare a usufruire dello sconto sulla fatturazione è necessario acquistare una nuova prenotazione per i servizi e il software idonei.

## <a name="discount-applies-to-different-sizes"></a>Viene applicato uno sconto per diverse dimensioni

Quando si acquista una prenotazione, è possibile applicare lo sconto ad altre istanze con gli attributi compresi nello stesso gruppo di dimensioni. Questa funzionalità è detta flessibilità le dimensioni dell'istanza. La flessibilità della copertura degli sconti dipende dal tipo di prenotazione e dagli attributi scelti all'acquisto della prenotazione.

Piani di servizio:

- Istanze di macchina virtuale riservate: Quando si acquista la prenotazione e si sceglie **ottimizzato per**: **flessibilità le dimensioni dell'istanza**, il code coverage sconto dipende dalle dimensioni della macchina virtuale si seleziona. La prenotazione può essere applicata alle dimensioni delle macchine virtuali (VM) nello stesso gruppo di serie di dimensioni. Per altre informazioni vedere [Flessibilità di dimensioni delle macchine virtuali con le istanze di macchina virtuale riservate](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Capacità riservata del database SQL: la copertura degli sconti dipende dal livello di prestazioni selezionato. Per altre informazioni, vedere [Informazioni su come viene applicato ai database SQL lo sconto sulla prenotazione](billing-understand-reservation-charges.md).
- Capacità riservata di Azure Cosmos DB: la copertura degli sconti dipende dalla velocità effettiva di cui viene effettuato il provisioning. Per altre informazioni, vedere [Informazioni su come viene applicato lo sconto per la prenotazione ad Azure Cosmos DB](billing-understand-cosmosdb-reservation-charges.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
    - [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md)
    - [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
    - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
    - [Costi del software Windows non inclusi nelle prenotazioni](billing-reserved-instance-windows-software-costs.md)
    - [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](/partner-center/azure-reservations)

- Altre informazioni su prenotazioni per i piani di servizio:
    - [Macchine virtuali con le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Capacità riservata di risorse di Azure Cosmos DB con Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Capacità riservata di risorse di calcolo di Database SQL con Database SQL di Azure](../sql-database/sql-database-reserved-capacity.md) altre informazioni sulle prenotazioni per i piani software:
    - [Piani software Red Hat dalle prenotazioni di Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Piani software SUSE dalle prenotazioni di Azure](../virtual-machines/linux/prepay-suse-software-charges.md)

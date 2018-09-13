---
title: Informazioni sulle prenotazioni di Azure | Microsoft Docs
description: Informazioni sulle prenotazioni e sui prezzi di Azure per risparmiare sui costi di macchine virtuali, database SQL e altre risorse.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: yashar
ms.openlocfilehash: 82b23f46acc94fefccc871583657200b90a33f05
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2018
ms.locfileid: "43303563"
---
# <a name="what-are-azure-reservations"></a>Informazioni sulle prenotazioni di Azure

Le prenotazioni di Azure consentono di risparmiare effettuando il pagamento anticipato della capacità di calcolo delle macchine virtuali o dei database SQL o altre risorse di Azure per un periodo di uno o tre anni. Il pagamento anticipato consente di ottenere uno sconto sulle risorse usate. Le prenotazioni possono ridurre notevolmente i costi di macchina virtuale, calcolo dei database SQL o altre risorse fino al 72% sui prezzi con pagamento a consumo. Le prenotazioni offrono uno sconto a livello di fatturazione e non hanno alcuna ripercussione sullo stato di runtime delle risorse.

È possibile acquistare una prenotazione nel [portale di Azure](https://aka.ms/reservations). Per ulteriori informazioni, vedere gli argomenti seguenti:

- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagare in anticipo le risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../sql-database/sql-database-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>Perché acquistare una prenotazione?

Se si hanno macchine virtuali o database SQL in esecuzione per lunghi periodi di tempo, l'acquisto di una prenotazione è l'opzione più conveniente. Se ad esempio si eseguono costantemente quattro istanze di un servizio senza una prenotazione, verranno applicate le tariffe in base al consumo. Se invece si acquista una prenotazione per tali risorse, si otterrà immediatamente lo sconto per la prenotazione. Per le risorse non verranno più addebitate tariffe in base al consumo.

## <a name="what-charges-does-a-reservation-cover"></a>Quali spese vengono coperte da una prenotazione?

- Istanza di macchina virtuale riservata: una prenotazione copre solo i costi di calcolo della macchina virtuale e non i costi aggiuntivi relativi a software, rete o archiviazione.
- vCore riservato del database SQL: in una prenotazione sono inclusi solo i costi di calcolo. I costi della licenza vengono fatturati separatamente.

Per le macchine virtuali Windows e il database SQL, i costi delle licenze possono essere coperti con le offerte di [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Chi è idoneo all'acquisto di una prenotazione?

Possono acquistare una prenotazione i clienti di Azure con i tipi di sottoscrizione seguenti:

- Tipo di offerta di sottoscrizione Contratto Enterprise (MS-AZR-0017P).
- Tipo di offerta di sottoscrizione [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) (MS-AZR-003P). Per poter acquistare una prenotazione, l'utente deve avere il ruolo "Proprietario" nella sottoscrizione.
- Per acquistare prenotazioni di Azure, i partner CSP (Cloud Solution Provider) possono usare il portale di Azure o il [Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations).

Lo sconto sull'acquisto di prenotazioni viene applicato solo alle risorse con sottoscrizioni di tipo Enterprise, con pagamento in base al consumo o CSP.

## <a name="how-is-a-reservation-billed"></a>Come viene fatturata una prenotazione?

La prenotazione viene addebitata in base al metodo di pagamento associato alla sottoscrizione. Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Se tale saldo non copre il costo delle istanze riservate, l'eccedenza verrà fatturata. Se si dispone di una sottoscrizione con pagamento in base al consumo, il costo verrà addebitato immediatamente sulla carta di credito associata all'account. Se invece l'addebito avviene tramite fattura, il costo risulterà visibile sulla fattura successiva.

## <a name="how-is-the-reservation-discount-applied"></a>Come viene applicato lo sconto per la prenotazione?

Lo sconto relativo alla prenotazione viene applicato all'utilizzo delle risorse corrispondenti agli attributi selezionati in fase di acquisto. Gli attributi includono l'ambito in cui vengono eseguiti i database SQL, le macchine virtuali o le altre risorse corrispondenti. Se ad esempio si vuole usufruire di uno sconto relativo alla prenotazione per quattro macchine virtuali Standard D2 nell'area Stati Uniti occidentali, selezionare la sottoscrizione in cui sono in esecuzione le macchine virtuali. Se le macchine virtuali sono in esecuzione in diverse sottoscrizioni all'interno della sottoscrizione o dell'account corrente, selezionare l'ambito condiviso. L'ambito condiviso consente l'applicazione dello sconto relativo all'acquisto di istanze riservate tra sottoscrizioni. Dopo aver acquistato una prenotazione è possibile modificare l'ambito. Per altre informazioni, vedere [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md).

Lo sconto sull'acquisto di prenotazioni viene applicato solo alle risorse con sottoscrizioni di tipo Enterprise, con pagamento in base al consumo o CSP. Le risorse eseguite in una sottoscrizione con altri tipi di offerta non prevedono questo tipo di sconto. Per le iscrizioni Enterprise, le sottoscrizioni di tipo Sviluppo/test Enterprise non possono usufruire dei vantaggi relativi alle prenotazioni.

Per comprendere meglio l'impatto delle prenotazioni sulla fatturazione, vedere gli argomenti seguenti:

-  [Informazioni sullo sconto per le istanze di macchina virtuale riservate di Azure](billing-understand-vm-reservation-charges.md)
- [Comprendere lo sconto sulle prenotazioni di Azure](billing-understand-vm-reservation-charges.md)
- Informazioni sullo sconto per la prenotazione di Azure e l'utilizzo per SUSE

## <a name="what-happens-when-the-reservation-term-expires"></a>Cosa accade alla scadenza del periodo di prenotazione?

La scadenza del periodo di prenotazione comporta anche la scadenza dello sconto sulla fatturazione. Al termine di questo periodo, le macchine virtuali, i database SQL o le altre risorse verranno fatturati in base alle tariffe con pagamento in base al consumo. Le prenotazioni di Azure non vengono rinnovate automaticamente. Per continuare a usufruire dello sconto sulla fatturazione è necessario acquistare una nuova prenotazione per i servizi e il software idonei.

## <a name="next-steps"></a>Passaggi successivi

È possibile iniziare subito a risparmiare sui costi delle macchine virtuali con l'acquisto di un'[istanza di macchina virtuale riservata](../virtual-machines/windows/prepay-reserved-vm-instances.md) o di [capacità riservata del database SQL](../sql-database/sql-database-reserved-capacity.md).

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Costi del software Windows non inclusi nelle prenotazioni](billing-reserved-instance-windows-software-costs.md)
- [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

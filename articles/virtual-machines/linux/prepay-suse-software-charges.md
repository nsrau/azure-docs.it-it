---
title: Acquistare piani SUSE Linux - Prenotazioni di Azure | Microsoft Docs
description: Informazioni su come è possibile pagare in anticipo per l'utilizzo di SUSE e risparmiare rispetto ai costi con pagamento in base al consumo.
services: virtual-machines-linux
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2018
ms.author: yashar
ms.openlocfilehash: 9c3976a5fa98049de03f2a65b71f1fc927947142
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2018
ms.locfileid: "43310891"
---
# <a name="prepay-for-suse-software-plans-from-azure-reservations"></a>Pagare in anticipo per i piani software SUSE dalle prenotazioni di Azure

Pagare in anticipo per l'utilizzo di SUSE e risparmiare rispetto ai costi con pagamento in base al consumo. Gli sconti si applicano solo ai contatori SUSE e non all'utilizzo della macchina virtuale. È possibile acquistare le prenotazioni per le macchine virtuali separatamente per risparmiare ancora di più.

È possibile acquistare i piani software SUSE nel portale di Azure. Per acquistare un piano:

- È necessario disporre del ruolo di Proprietario per almeno una sottoscrizione aziendale o con pagamento in base al consumo.
- Per le sottoscrizioni aziendali gli acquisti di prenotazioni devono essere abilitati nel [portale EA](https://ea.azure.com).
- Per il programma Cloud Solution Provider (CSP), gli agenti di amministrazione o gli agenti di vendita possono acquistare i piani SUSE.

## <a name="buy-a-suse-software-plan"></a>Acquistare un piano software SUSE

1. Andare su [Prenotazioni](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) nel portale di Azure.
1. Selezionare **Aggiungi** e quindi SUSE Linux.
1. Compilare i campi obbligatori. Lo sconto viene applicato a qualsiasi VM SUSE Linux che corrisponda agli attributi di ciò che si acquista. Il numero effettivo di distribuzioni a cui viene applicato lo sconto dipende dall'ambito e dalla quantità selezionati.

    | Campo      | DESCRIZIONE|
    |:------------|:--------------|
    |NOME        |Il nome dell'acquisto.|
    |Sottoscrizione|La sottoscrizione usata per pagare il piano. L'acquisto delle istanze di macchina virtuale riservate viene addebitato in base al metodo di pagamento associato alla sottoscrizione. Il tipo di sottoscrizione deve essere un contratto Enterprise Agreement (numero offerta: MS-AZR-0017P) o con pagamento in base al consumo (numero offerta: MS-AZR-0003P). Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Se si dispone di una sottoscrizione con pagamento in base al consumo, il costo viene addebitato alla carta di credito o al metodo di pagamento tramite fattura per la sottoscrizione.|
    |Scope       |L'ambito può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona: <ul><li>Sottoscrizione singola: lo sconto relativo al piano viene applicato all'utilizzo di SUSE Linux in questa sottoscrizione. </li><li>Condivisa: lo sconto relativo al piano viene applicato all'utilizzo di SUSE Linux in tutte le sottoscrizioni all'interno del contesto di fatturazione. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni (eccetto le sottoscrizioni di sviluppo/test) all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso copre tutte le sottoscrizioni con pagamento in base al consumo create dall'amministratore dell'account.</li></ul>|
    |Piano software     |Selezionare il piano SUSE Linux. Per informazioni su come identificare gli elementi da acquistare, vedere [Understand how the SUSE Linux Enterprise reservation plan discount is applied](../../billing/billing-understand-suse-reservation-charges.md) (Informazioni su come viene applicato lo sconto relativo al piano del software SUSE Linux Enterprise).|
    |Dimensioni macchina virtuale     |I prezzi di SUSE Linux dipendono dal numero di vCPU nella macchina virtuale. Selezionare l'opzione che rappresenta il numero di vCPU nelle macchine virtuali SUSE Linux.|
    |Termine        |Un anno o tre anni.|
    |Quantità    |Il numero di macchine virtuali per il quale si sta acquistando il piano SUSE Linux. La quantità è il numero di istanze di SUSE Linux in esecuzione che possono ottenere lo sconto sulla fatturazione.|
1. Selezionare **Acquisto**.
1. Selezionare **Visualizza questa prenotazione** per vedere lo stato dell'acquisto.

Lo sconto per la prenotazione viene applicato automaticamente a tutte le macchine virtuali SUSE in esecuzione che corrispondono alla prenotazione. Lo sconto è valido solo per il contatore SUSE. I costi relativi al calcolo delle macchine virtuali non vengono coperti da questo piano.

## <a name="discount-applies-to-different-vm-sizes-with-instance-size-flexibility"></a>Lo sconto si applica a diverse dimensioni di VM con flessibilità in termini di dimensioni dell'istanza

Come le istanze di macchina virtuale riservate, i piani SUSE Linux offrono flessibilità in termini di dimensioni dell'istanza. Ciò significa che lo sconto si applica anche quando si distribuisce una VM di dimensioni diverse rispetto al piano SUSE acquistato. Per altre informazioni, vedere [Understand how the SUSE Linux Enterprise software reservation discount is applied](../../billing/billing-understand-suse-reservation-charges.md) (Informazioni su come viene applicato lo sconto relativo alla prenotazione del software SUSE Linux Enterprise).

## <a name="cancellation-and-exchanges-not-allowed"></a>Annullamento e scambi non consentiti

Non è possibile annullare o scambiare un piano SUSE acquistato. Controllare l'utilizzo per assicurarsi di acquistare il piano giusto. Per informazioni su come identificare gli elementi da acquistare, vedere [Understand how the SUSE Linux Enterprise reservation plan discount is applied](../../billing/billing-understand-suse-reservation-charges.md) (Informazioni su come viene applicato lo sconto relativo al piano del software SUSE Linux Enterprise).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come gestire una prenotazione, vedere [Gestire le prenotazioni di Azure](../../billing/billing-manage-reserved-vm-instance.md).

Per altre informazioni, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](../../billing/billing-save-compute-costs-reservations.md)
- [Gestire le prenotazioni in Azure](../../billing/billing-manage-reserved-vm-instance.md)
- [Understand how the SUSE reservation discount is applied](../../billing/billing-understand-suse-reservation-charges.md) (Informazioni su come viene applicato lo sconto sulla prenotazione SUSE)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](../../billing/billing-understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
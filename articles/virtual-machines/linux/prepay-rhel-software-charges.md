---
title: Acquistare piani di Red Hat Enterprise Linux - Azure prenotazioni | Microsoft Docs
description: Informazioni su come è possibile pagare in anticipo per l'utilizzo di Red Hat e risparmiare denaro rispetto ai costi con pagamento a consumo.
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
ms.date: 01/18/2019
ms.author: yashar
ms.openlocfilehash: e90241d9da140ca33d1aae8ffc5636f8eb2fbc74
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58653018"
---
# <a name="prepay-for-red-hat-enterprise-linux-plans-from-azure-reservations"></a>Pagare in anticipo per i piani di Red Hat Enterprise Linux da Azure prenotazioni

Pagare in anticipo per l'utilizzo di Red Hat e risparmiare denaro rispetto ai costi con pagamento a consumo. Sconti si applicano solo ai contatori di Red Hat e non sull'utilizzo della macchina virtuale. È possibile acquistare le prenotazioni per le macchine virtuali separatamente per risparmiare ancora di più.

È possibile acquistare i piani software Red Hat nel portale di Azure. Per acquistare un piano:

- È necessario disporre del ruolo di Proprietario per almeno una sottoscrizione aziendale o con pagamento in base al consumo.
- Per le sottoscrizioni Enterprise, **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore della sottoscrizione con contratto Enterprise.
- Per il programma Cloud Solution Provider (CSP), di agenti di amministrazione o gli agenti di vendita possono acquistare i piani di Red Hat.

## <a name="buy-a-red-hat-software-plan"></a>Acquista un piano software Red Hat

1. Andare su [Prenotazioni](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) nel portale di Azure.
1. Selezionare **Add** e selezionare Red Hat Linux.
1. Compilare i campi obbligatori. Qualsiasi macchina virtuale Linux Red Hat che corrisponde agli attributi dei quali si acquistano Ottiene lo sconto. Il numero effettivo di distribuzioni a cui viene applicato lo sconto dipende dall'ambito e dalla quantità selezionati.

    | Campo      | DESCRIZIONE|
    |:------------|:--------------|
    |NOME        |Il nome dell'acquisto.|
    |Sottoscrizione|La sottoscrizione usata per pagare il piano. L'acquisto delle istanze di macchina virtuale riservate viene addebitato in base al metodo di pagamento associato alla sottoscrizione. Il tipo di sottoscrizione deve essere un contratto Enterprise (numeri offerta: MS-AZR-0017P o MS-AZR-0148P) o con pagamento in base al consumo (numeri offerta: MS-AZR-0003P o MS-AZR-0023P). Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Se si dispone di una sottoscrizione con pagamento in base al consumo, il costo viene addebitato alla carta di credito o al metodo di pagamento tramite fattura per la sottoscrizione.|
    |Scope       |L'ambito può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona: <ul><li>Sottoscrizione singola: lo sconto di piano viene applicata all'utilizzo di Red Hat Linux in questa sottoscrizione. </li><li>Lo sconto del piano condiviso - viene applicato all'utilizzo di Red Hat Linux in tutte le sottoscrizioni all'interno del contesto di fatturazione. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso copre tutte le sottoscrizioni con pagamento in base al consumo create dall'amministratore dell'account.</li></ul>|
    |Pianificazione     |Selezionare il piano di Red Hat Linux. Per informazioni su come identificare gli elementi da acquistare, vedere Understand how viene applicato lo sconto di prenotazione software Red Hat Linux Enterprise.|
    |Dimensioni macchina virtuale     |Prezzi di Red Hat Linux dipende dal numero di Vcpu della macchina virtuale. Selezionare l'opzione che rappresenta il numero di Vcpu nelle macchine virtuali Red Hat Linux.|
    |Termine        |Un anno o tre anni.|
    |Quantità    |Pianificazione il numero di macchine virtuali che acquisti questo Red Hat Linux. La quantità è il numero di istanze di Red Hat Linux che è possono ottenere lo sconto sulla fatturazione.|
1. Selezionare **Acquisto**.
1. Selezionare **Visualizza questa prenotazione** per vedere lo stato dell'acquisto.

Lo sconto della prenotazione viene applicato automaticamente le macchine virtuali Red Hat in esecuzione che corrisponde alla prenotazione. Lo sconto è valido solo per il contatore di Red Hat. I costi relativi al calcolo delle macchine virtuali non vengono coperti da questo piano.

## <a name="discount-applies-to-different-vm-sizes"></a>Viene applicato uno sconto per diverse dimensioni delle macchine Virtuali

Ad esempio istanze di VM riservate, piani di Red Hat Linux offrono flessibilità le dimensioni dell'istanza. Ciò significa che lo sconto si applica anche quando si distribuisce una macchina virtuale che è una dimensione diversa dal piano di Red Hat acquistate. Per altre informazioni, vedere Understand modo in cui viene applicato lo sconto di prenotazione software Red Hat Linux Enterprise.

## <a name="cancellation-and-exchanges-not-allowed"></a>Annullamento e scambi non consentiti

Non è possibile annullare o lo scambio di un piano di Red Hat acquistate. Controllare l'utilizzo per assicurarsi di acquistare il piano giusto. Per informazioni su come identificare gli elementi da acquistare, vedere Understand how viene applicato lo sconto di prenotazione software Red Hat Linux Enterprise.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come gestire una prenotazione, vedere [Gestire le prenotazioni di Azure](../../billing/billing-manage-reserved-vm-instance.md).

Per altre informazioni, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](../../billing/billing-save-compute-costs-reservations.md)
- [Gestire le prenotazioni in Azure](../../billing/billing-manage-reserved-vm-instance.md)
- Informazioni su come viene applicato lo sconto di prenotazione di Red Hat
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](../../billing/billing-understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Per altre domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
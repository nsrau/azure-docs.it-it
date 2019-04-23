---
title: Gli scambi di self-servizi e rimborsi per le prenotazioni di Azure | Microsoft Docs
description: Informazioni su come è possibile scambiare o rimborsare le prenotazioni di Azure.
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 54578746ea8029a760663edc456660f98358abc5
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009311"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Gli scambi di self-servizi e rimborsi per le prenotazioni di Azure

Le prenotazioni di Azure offrono flessibilità per soddisfare le esigenze in continua evoluzione. È possibile scambiare una prenotazione per un altro prenotazione dello stesso tipo. È possibile rimborsare una prenotazione, fino a 50.000 USD all'anno, anche se è non è più necessario.

Funzionalità di exchange e Annulla self-service non è disponibile per i clienti US Government contratto Enterprise Agreement. Sono supportati altri tipi di sottoscrizione di US Government inclusi con pagamento a consumo e CSP.

È necessario disporre degli accessi dei proprietari dell'ordine di prenotazione per lo scambio o restituzione di una prenotazione esistente.

## <a name="exchange-an-existing-reserved-instance"></a>Un'istanza riservata esistente di Exchange

È possibile scambiare la prenotazione con tre passaggi veloci nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Selezionare le prenotazioni che si desidera rimborsare e fare clic su **Exchange**.  
    ![Immagine di esempio che mostra le prenotazioni da restituire](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. Selezionare il prodotto della macchina virtuale che si desidera acquistare e digitare una quantità. Assicurarsi che il nuovo totale di acquisto è maggiore del totale restituito. [Determinare le dimensioni corrette prima di acquistare](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Immagine di esempio che illustra il prodotto della macchina virtuale per l'acquisto con uno scambio](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. Esaminare e completare la transazione.  
    ![Immagine di esempio che illustra il prodotto della macchina virtuale per l'acquisto con uno scambio, completare il valore restituito](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

Per una prenotazione di rimborso, passare a **i dettagli della prenotazione** e fare clic su **rimborso**.

## <a name="how-transactions-are-processed"></a>Modalità di elaborazione delle transazioni

In primo luogo, Microsoft consente di annullare la prenotazione esistente e rimborsi la quantità proporzionale per tale prenotazione. Se si verifica uno scambio, viene elaborata l'acquisto di nuovo. Microsoft elabora le restituzioni usando uno dei metodi seguenti, a seconda del tipo di account e il metodo di pagamento:

### <a name="enterprise-agreement-customers"></a>Clienti con contratto Enterprise

Money viene aggiunto per l'impegno monetario per lo scambio e rimborsi se è stato effettuato l'acquisto originale usando uno. Tutte le fatture in eccedenza, poiché gli acquisti originali sono riaperto e rerated per assicurarsi che l'impegno monetario viene utilizzato. Se il termine dell'impegno monetario utilizzando che è stata acquistata la prenotazione non è più attivo, carta di credito viene aggiunto al termine di corrente enterprise agreement con impegno monetario.

Se è stato effettuato l'acquisto originale come eccedenza, Microsoft rilascia una nota di credito.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>I pagamenti tramite fatturazione con pagamento a consumo e programma CSP

La fattura di acquisto di prenotazioni originale viene annullata e quindi viene creata una nuova fattura per la restituzione. Per gli scambi, la nuova fattura Mostra il rimborso e l'acquisto di nuovo. La quantità di rimborso viene regolata per l'acquisto. Se si rimborsate solo una prenotazione, la quantità ripartita rimane assegnato a Microsoft e viene modificato contro un acquisto della prenotazione future.

### <a name="pay-as-you-go-credit-card-customers"></a>Clienti di carta di credito di pagamento a consumo

La fattura originale viene annullata e viene creata una nuova fattura. Il denaro è rimborsato alla carta di credito usata per l'acquisto originale. Se è stata modificata la carta [contattare il supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="exchange-policies"></a>Criteri di Exchange

- È possibile restituire più le prenotazioni esistenti per acquistare una nuova prenotazione dello stesso tipo. Non è possibile scambiare le prenotazioni di un tipo per un altro. È ad esempio, non può restituire una prenotazione di macchine Virtuali per acquistare una prenotazione di SQL.
- Solo i proprietari di prenotazione in grado di elaborare uno scambio. [Informazioni su come aggiungere o modificare gli utenti che possono gestire una prenotazione](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance#add-or-change-users-who-can-manage-a-reservation).
- Uno scambio viene elaborato come un rimborso e riscatto: transazioni diverse vengono create per l'annullamento e l'acquisto di nuovo. La quantità di prenotazione ripartito verrà rimborsata per le prenotazioni che per lo scambio si. Ti viene addebitata completamente per l'acquisto di nuovo. La quantità di prenotazione con ripartizione proporzionale è il valore residuo con ripartizione proporzionale giornaliero della prenotazione viene restituito.
- È possibile scambiare o prenotazioni di rimborso anche se il contratto enterprise consente di acquistare la prenotazione scade e come un nuovo contratto è stato rinnovato.
- È possibile modificare qualsiasi proprietà di prenotazione, ad esempio dimensioni, area, quantity e termini con uno scambio.
- Il nuovo totale di acquisto deve essere uguale o essere maggiore rispetto alla quantità restituita.
- Le nuove riservate acquistate come parte di exchange ha una validità di nuovo a partire dall'ora di exchange.
- Non sono penalità o limiti annuali per lo scambio.

## <a name="refund-policies"></a>Criteri di restituzione

- La quantità totale di rimborso non può superare i 50.000 dollari in una finestra con 12 mesi.
- Solo i proprietari di prenotazione in grado di elaborare un rimborso. [Informazioni su come aggiungere o modificare gli utenti che possono gestire una prenotazione](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Microsoft si riserva il diritto ad addebitare i costi una riduzione del 12% per qualsiasi restituisce un valore, anche se la riduzione non è attualmente addebitata.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Archiviazione di Exchange non premium per l'archiviazione premium

È possibile scambiare una prenotazione acquistata per una dimensione di VM che non supporta archiviazione premium a una dimensione di macchina virtuale corrispondente che esegue. Ad esempio, un' _F1_ per un' _F1s_. Per semplificare lo scambio, passare a dettagli della prenotazione e fare clic su **Exchange**. Lo scambio non reimpostare il termine dell'istanza riservata o creare una nuova transazione.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come gestire una prenotazione, vedere [Gestire le prenotazioni di Azure](billing-manage-reserved-vm-instance.md).
- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
    - [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
    - [Gestire le prenotazioni in Azure](billing-manage-reserved-vm-instance.md)
    - [Informazioni su come viene applicato lo sconto sulla prenotazione](billing-understand-vm-reservation-charges.md)
    - [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](billing-understand-reserved-instance-usage.md)
    - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)
    - [Costi del software Windows non inclusi nelle prenotazioni](billing-reserved-instance-windows-software-costs.md)
    - [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](/partner-center/azure-reservations)

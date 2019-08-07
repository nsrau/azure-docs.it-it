---
title: Scambi e rimborsi self-service per le prenotazioni di Azure
description: Informazioni su come scambiare o rimborsare le prenotazioni di Azure.
author: yashesvi
manager: yashesvi
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2019
ms.author: banders
ms.openlocfilehash: 5e38684500520d4565835456b94200aea399c938
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814108"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Scambi e rimborsi self-service per le prenotazioni di Azure

Le prenotazioni di Azure offrono flessibilità per soddisfare le tue esigenze in continua evoluzione. È possibile scambiare una prenotazione per un'altra dello stesso tipo. È anche possibile rimborsare una prenotazione, fino a 50.000 USD all'anno, se questa non è più necessaria.

La funzionalità di scambio e annullamento self-service non è disponibile per i clienti US Government con Contratto Enterprise. Sono supportati altri tipi di sottoscrizione per enti pubblici degli Stati Uniti, tra cui il pagamento in base al consumo e CSP.

È necessario disporre dell'accesso proprietario per l'ordine di prenotazione per lo scambio o il rimborso di una prenotazione esistente.

## <a name="exchange-an-existing-reserved-instance"></a>Scambia un'istanza riservata esistente

È possibile scambiare la prenotazione con tre passaggi rapidi nell' [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Selezionare le prenotazioni che si desidera rimborsare e fare clic su **Exchange**.  
    ![Immagine di esempio che mostra le prenotazioni da restituire](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. Selezionare il prodotto della macchina virtuale che si desidera acquistare e digitare una quantità. Verificare che il nuovo totale di acquisto sia superiore al totale restituito. [Determinare le dimensioni corrette prima di acquistare](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Immagine di esempio che mostra il prodotto VM da acquistare con uno scambio](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. Verificare e completare la transazione.  
    ![Immagine di esempio che mostra il prodotto VM da acquistare con uno scambio, completando la restituzione](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

Per rimborsare una prenotazione, passare a **Dettagli prenotazione** e fare clic su **rimborso**.

## <a name="how-transactions-are-processed"></a>Modalità di elaborazione delle transazioni

In primo luogo, Microsoft annulla la prenotazione esistente e rimborsa l'importo pro-rated per la prenotazione. Se è presente uno scambio, il nuovo acquisto viene elaborato. Microsoft elabora i rimborsi usando uno dei metodi seguenti, a seconda del tipo di account e del metodo di pagamento:

### <a name="enterprise-agreement-customers"></a>Clienti con contratto Enterprise Agreement

Il denaro viene aggiunto all'impegno monetario per gli scambi e i rimborsi se l'acquisto originale è stato effettuato usando uno di questi. Tutte le fatture in eccedenza, in quanto gli acquisti originali vengono riaperte e rivalutate per assicurarsi che venga usato l'impegno monetario. Se il termine dell'impegno monetario che usa la prenotazione è stato acquistato non è più attivo, il credito viene aggiunto al periodo dell'impegno monetario del contratto Enterprise corrente.

Se l'acquisto originale è stato effettuato come eccedenza, Microsoft rilascia una nota di credito.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Fatturazione con pagamento in base al consumo e programma CSP

La fattura di acquisto della prenotazione originale viene annullata e quindi viene creata una nuova fattura per il rimborso. Per gli scambi, la nuova fattura Mostra il rimborso e il nuovo acquisto. L'importo del rimborso viene regolato in base all'acquisto. Se si prevede di rimborsare solo una prenotazione, l'importo prorateato rimane con Microsoft e viene regolato in base a un acquisto di prenotazione futura.

### <a name="pay-as-you-go-credit-card-customers"></a>Clienti con carta di credito con pagamento in base al consumo

La fattura originale viene annullata e viene creata una nuova fattura. Il denaro viene rimborsato alla carta di credito usata per l'acquisto originale. Se la scheda è stata modificata, [contattare il supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>Criteri di annullamento, scambio e rimborso

Azure prevede i seguenti criteri per gli annullamenti, gli scambi e i rimborsi.

**Criteri di annullamento**

- Se si annulla una prenotazione, potrebbe essere prevista una tariffa per la terminazione del 12%.
- Il rimborso ricevuto per un annullamento è il saldo proporzionale rimanente, meno il 12% della tariffa per la terminazione anticipata. Per annullare, passare alla prenotazione nel portale di Azure e selezionare **rimborso**.

**Criteri di Exchange**

- È possibile restituire più prenotazioni esistenti per acquistare una nuova prenotazione dello stesso tipo. Non è possibile scambiare prenotazioni di un tipo per un altro tipo. Ad esempio, non è possibile restituire una prenotazione della macchina virtuale per acquistare una prenotazione SQL.
- Solo i proprietari delle prenotazioni possono elaborare uno scambio. [Informazioni su come aggiungere o modificare gli utenti che possono gestire una prenotazione](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Uno scambio viene elaborato come un rimborso e un nuovo acquisto: vengono create transazioni diverse per l'annullamento e il nuovo acquisto. L'importo della prenotazione riordinata viene rimborsato per le prenotazioni di cui si esegue il Trade. Viene addebitato il costo completo del nuovo acquisto. La quantità di prenotazione rivalutata è il valore residuo prorate giornaliero della prenotazione da restituire.
- È possibile scambiare o rimborsare prenotazioni anche se il contratto Enterprise Agreement usato per acquistare la prenotazione è scaduto ed è stato rinnovato come nuovo contratto.
- È possibile modificare qualsiasi proprietà di prenotazione, ad esempio dimensioni, area, quantità e termini con uno scambio.
- Il nuovo totale di acquisto deve essere uguale o maggiore del valore restituito.
- La nuova prenotazione acquistata come parte di Exchange ha un nuovo termine a partire dal momento del cambio.
- Non sono previsti limiti per gli scambi.

**Criteri di rimborso**
- L'importo del rimborso totale non può superare $50.000 USD in una finestra in sequenza di 12 mesi.
- I rimborsi vengono calcolati in base al prezzo minimo del prezzo di acquisto o del prezzo attuale della prenotazione.
- Solo i proprietari delle prenotazioni possono elaborare un rimborso. [Informazioni su come aggiungere o modificare gli utenti che possono gestire una prenotazione](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Microsoft si riserva il diritto di addebitare un rigore del 12% per qualsiasi risultato, anche se la penalità non è attualmente addebitata.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Archiviazione non Premium di Exchange per archiviazione Premium

È possibile scambiare una prenotazione acquistata per una macchina virtuale di dimensioni che non supporta l'archiviazione Premium per le dimensioni della VM corrispondenti. Ad esempio, _F1_ per un _F1s_. Per eseguire lo scambio, passare a dettagli prenotazione e fare clic su **Exchange**. Exchange non reimposta il termine dell'istanza riservata o crea una nuova transazione.

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

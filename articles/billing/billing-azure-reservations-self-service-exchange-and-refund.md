---
title: Scambi e rimborsi in modalità self-service per le prenotazioni di Azure
description: Informazioni su come scambiare le prenotazioni di Azure o chiederne il rimborso.
author: yashesvi
manager: yashesvi
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2019
ms.author: banders
ms.openlocfilehash: 87f8a90b2213c5e78b456477e8be61d2f013fd71
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224109"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Scambi e rimborsi in modalità self-service per le prenotazioni di Azure

Le prenotazioni di Azure offrono la flessibilità necessaria a soddisfare le esigenze in continua evoluzione. È possibile scambiare una prenotazione per un'altra dello stesso tipo. È anche possibile rimborsare una prenotazione, fino a 50.000 USD all'anno, se questa non è più necessaria.

La funzionalità di scambio e annullamento self-service non è disponibile per i clienti US Government con Contratto Enterprise. Sono supportati altri tipi di sottoscrizione US Government, inclusi con pagamento in base al consumo e CSP.

È necessario avere l'accesso proprietario all'ordine di prenotazione per richiedere lo scambio o il rimborso di una prenotazione esistente.

## <a name="exchange-an-existing-reserved-instance"></a>Scambiare un'istanza riservata esistente

È possibile scambiare la prenotazione con tre passaggi rapidi nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Selezionare le prenotazioni di cui si vuole chiedere il rimborso e fare clic su **Scambia**.  
    ![Immagine di esempio che mostra le prenotazioni da restituire](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. Selezionare il prodotto della macchina virtuale che si desidera acquistare e digitare una quantità. Assicurarsi che il nuovo totale di acquisto sia maggiore del totale restituito. [Determinare le dimensioni corrette prima di acquistare](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Immagine di esempio che mostra il prodotto VM da acquistare con uno scambio](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. Rivedere e completare la transazione.  
    ![Immagine di esempio che mostra il prodotto VM da acquistare con uno scambio, completando la restituzione](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

Per rimborsare una prenotazione, aprire **Dettagli prenotazione e** fare clic su **Rimborsa**.

## <a name="how-transactions-are-processed"></a>Modalità di elaborazione delle transazioni

In primo luogo, Microsoft annulla la prenotazione esistente e rimborsa l'importo ripartito per la prenotazione. Se è previsto uno scambio, viene elaborato il nuovo acquisto. Microsoft elabora i rimborsi usando uno dei metodi seguenti, a seconda del tipo di account e del metodo di pagamento:

### <a name="enterprise-agreement-customers"></a>Clienti con Contratto Enterprise

Il denaro viene aggiunto all'impegno monetario per gli scambi e i rimborsi se l'acquisto originale è stato effettuato usando uno di questi. Tutte le fatture in eccedenza dall'acquisto originale vengono riaperte e rivalutate per assicurarsi che venga usato l'impegno monetario. Se il termine dell'impegno monetario con cui è stata acquistata la prenotazione non è più attivo, il credito viene aggiunto al termine dell'impegno monetario del contratto Enterprise corrente. Il credito è valido per 90 giorni dalla data di rimborso. Il credito inutilizzato scade al termine dei 90 giorni.

Se l'acquisto originale è stato effettuato come eccedenza, Microsoft rilascia una nota di credito.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Fatturazione con pagamento in base al consumo e programma CSP

La fattura di acquisto della prenotazione originale viene annullata e quindi viene creata una nuova fattura per il rimborso. Per gli scambi, la nuova fattura mostra il rimborso e il nuovo acquisto. L'importo del rimborso viene regolato in base all'acquisto. Se si prevede di rimborsare solo una prenotazione, l'importo ripartito resta a Microsoft e viene regolato in base all'acquisto di una prenotazione futura.

### <a name="pay-as-you-go-credit-card-customers"></a>Clienti con carta di credito con pagamento in base al consumo

La fattura originale viene annullata e viene creata una nuova fattura. Il denaro viene rimborsato alla carta di credito usata per l'acquisto originale. Se è stata usata un'altra carta, [contattare il supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>Criteri di annullamento, scambio e rimborso

Azure prevede i seguenti criteri per gli annullamenti, gli scambi e i rimborsi.

**Criteri di Exchange**

- È possibile restituire più prenotazioni esistenti per acquistarne una nuova dello stesso tipo. Non è possibile scambiare prenotazioni di un tipo per un altro tipo. Ad esempio, non è possibile restituire una prenotazione di VM per acquistare una prenotazione SQL.
- Solo i proprietari delle prenotazioni possono elaborare uno scambio. [Informazioni su come aggiungere o modificare gli utenti che possono gestire una prenotazione](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Uno scambio viene elaborato come rimborso e nuovo acquisto: vengono create transazioni diverse per l'annullamento e il nuovo acquisto. L'importo ripartito della prenotazione viene rimborsato per le prenotazioni scambiate. Viene addebitato il costo completo del nuovo acquisto. L'importo ripartito della prenotazione è il valore residuo ripartito giornaliero della prenotazione restituita.
- È possibile scambiare o rimborsare prenotazioni anche se il contratto Enterprise usato per acquistare la prenotazione è scaduto ed è stato rinnovato come nuovo contratto.
- È possibile modificare qualsiasi proprietà di prenotazione, ad esempio dimensioni, area, quantità e termine con uno scambio.
- Il nuovo totale di acquisto deve essere uguale o maggiore del valore restituito.
- La nuova prenotazione acquistata come parte dello scambio ha un nuovo termine a partire dal momento del cambio.
- Non sono previste penali né limiti annuali per gli scambi.

**Criteri di rimborso**
- Se si annulla una prenotazione, potrebbe essere applicata una penale per la risoluzione anticipata del 12%.
- Il rimborso ricevuto per un annullamento corrisponde al saldo ripartito rimanente meno la penale per la risoluzione del 12%. Per annullare, andare alla prenotazione nel portale di Azure e selezionare **Rimborso**.
- L'importo del rimborso totale non può superare 50.000 USD in una sequenza di 12 mesi.
- I rimborsi vengono calcolati in base al prezzo più basso, tra quello di acquisto o quello corrente della prenotazione.
- Solo i proprietari delle prenotazioni possono elaborare un rimborso. [Informazioni su come aggiungere o modificare gli utenti che possono gestire una prenotazione](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Microsoft si riserva il diritto di addebitare una penale del 12% per eventuali restituzioni. La penale non viene addebitata al momento, ma lo sarà in futuro.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Archiviazione non Premium di Exchange per archiviazione Premium

È possibile scambiare una prenotazione acquistata per dimensioni macchina virtuale che non supportano l'archiviazione Premium in dimensioni macchina virtuale che invece la supportano. Ad esempio, è possibile scambiare _F1_ con _F1s_. Per eseguire lo scambio, passare a Dettagli prenotazione e fare clic su **Scambia**. Lo scambio non reimposta il termine dell'istanza riservata né crea una nuova transazione.

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

---
title: Scambi e rimborsi in modalità self-service per le prenotazioni di Azure
description: Informazioni su come scambiare le prenotazioni di Azure o chiederne il rimborso. Per richiedere lo scambio o il rimborso delle prenotazioni, è necessario avere l'accesso come proprietario all'ordine di prenotazione.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 32db8396a687428c668a9b8a4213b50986614083
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150132"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Scambi e rimborsi in modalità self-service per le prenotazioni di Azure

Le prenotazioni di Azure offrono la flessibilità necessaria a soddisfare le esigenze in continua evoluzione. È possibile scambiare una prenotazione per un'altra dello stesso tipo. È ad esempio possibile scambiare una prenotazione di macchina virtuale per acquistare un'altra prenotazione per qualsiasi altra dimensione di VM o area. Analogamente, è possibile scambiare una prenotazione del database SQL PaaS per acquistare un'altra prenotazione per qualsiasi tipo o area SQL PaaS. È anche possibile rimborsare le prenotazione, ma la somma totale dell'impegno di tutte le prenotazioni annullate nell'ambito di fatturazione, ad esempio Contratto Enterprise, Contratto del cliente Microsoft e Contratto Microsoft Partner, non può superare i 50.000 USD in una finestra di mobile di 12 mesi. La capacità riservata di Azure Databricks, la prenotazione della soluzione Azure VMware di CloudSimple, la prenotazione di Azure Red Hat Open Shift, i piani di Red Hat e i piani di SUSE Linux non sono idonei per i rimborsi.

La funzionalità di scambio e annullamento self-service non è disponibile per i clienti US Government con Contratto Enterprise. Sono supportati altri tipi di sottoscrizione US Government, inclusi con pagamento in base al consumo e CSP (Cloud Solution Provider).

> [!NOTE]
> - **È necessario avere l'accesso proprietario all'ordine di prenotazione per richiedere lo scambio o il rimborso di una prenotazione esistente.** È possibile [Aggiungere o modificare gli utenti che possono gestire una prenotazione](./manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
> - Microsoft non applica attualmente penali per risoluzione anticipata per i rimborsi delle prenotazioni. Le penali potrebbero essere addebitate per i rimborsi effettuati in futuro. Non è attualmente prevista alcuna data per l'abilitazione delle penali.

## <a name="how-to-exchange-or-refund-an-existing-reservation"></a>Come scambiare o rimborsare una prenotazione esistente

È possibile scambiare la prenotazione dal [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Selezionare le prenotazioni di cui si vuole chiedere il rimborso e selezionare **Scambia**.  
    [![Immagine di esempio che mostra le prenotazioni da restituire](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png#lightbox)
1. Selezionare il prodotto della macchina virtuale che si desidera acquistare e digitare una quantità. Assicurarsi che il nuovo totale di acquisto sia maggiore del totale restituito. [Determinare le dimensioni corrette prima di acquistare](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    [![Immagine di esempio che mostra il prodotto VM da acquistare con uno scambio](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png#lightbox)
1. Rivedere e completare la transazione.  
    [![Immagine di esempio che mostra il prodotto VM da acquistare con uno scambio, completando la restituzione](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png#lightbox)

Per rimborsare una prenotazione, aprire **Dettagli prenotazione** e selezionare **Rimborsa**.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Archiviazione non Premium di Exchange per archiviazione Premium

È possibile scambiare una prenotazione acquistata per dimensioni macchina virtuale che non supportano l'archiviazione Premium in dimensioni macchina virtuale che invece la supportano. Ad esempio, è possibile scambiare _F1_ con _F1s_. Per eseguire lo scambio, passare a Dettagli prenotazione e selezionare **Scambia**. Lo scambio non reimposta il termine dell'istanza riservata né crea una nuova transazione. 

## <a name="how-transactions-are-processed"></a>Modalità di elaborazione delle transazioni

In primo luogo, Microsoft annulla la prenotazione esistente e rimborsa l'importo ripartito per la prenotazione. Se è previsto uno scambio, viene elaborato il nuovo acquisto. Microsoft elabora i rimborsi usando uno dei metodi seguenti, a seconda del tipo di account e del metodo di pagamento:

### <a name="enterprise-agreement-customers"></a>Clienti con Contratto Enterprise

Il denaro viene aggiunto all'impegno monetario per gli scambi e i rimborsi se l'acquisto originale è stato effettuato usando uno di questi. Se il termine dell'impegno monetario con cui è stata acquistata la prenotazione non è più attivo, il credito viene aggiunto al termine dell'impegno monetario del contratto Enterprise corrente. Il credito è valido per 90 giorni dalla data del rimborso. Il credito inutilizzato scade al termine dei 90 giorni.

Se l'acquisto originale è stato effettuato come eccedenza, la fattura originale in cui è stata acquistata la prenotazione e tutte le fatture successive verranno riaperte e modificate. Microsoft emette una nota di credito per i rimborsi.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Fatturazione con pagamento in base al consumo e programma CSP

La fattura di acquisto della prenotazione originale viene annullata e quindi viene creata una nuova fattura per il rimborso. Per gli scambi, la nuova fattura mostra il rimborso e il nuovo acquisto. L'importo del rimborso viene regolato in base all'acquisto. Se si prevede di rimborsare solo una prenotazione, l'importo ripartito resta a Microsoft e viene regolato in base all'acquisto di una prenotazione futura. Se è stata acquistata una prenotazione con le tariffe con pagamento in base al consumo e successivamente si è passati a un CSP, la prenotazione può essere restituita e riacquistata senza penali.

### <a name="pay-as-you-go-credit-card-customers"></a>Clienti con carta di credito con pagamento in base al consumo

La fattura originale viene annullata e viene creata una nuova fattura. Il denaro viene rimborsato alla carta di credito usata per l'acquisto originale. Se è stata usata un'altra carta, [contattare il supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>Criteri di annullamento, scambio e rimborso

Azure prevede i seguenti criteri per gli annullamenti, gli scambi e i rimborsi.

**Criteri di Exchange**

- È possibile restituire più prenotazioni esistenti per acquistarne una nuova dello stesso tipo. Non è possibile scambiare prenotazioni di un tipo per un altro tipo. Ad esempio, non è possibile restituire una prenotazione di VM per acquistare una prenotazione SQL. È possibile modificare qualsiasi proprietà di prenotazione, ad esempio famiglia, serie, versioni, SKU, area, quantità e termine con uno scambio.
- Solo i proprietari delle prenotazioni possono elaborare uno scambio. [Informazioni su come aggiungere o modificare gli utenti che possono gestire una prenotazione](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Uno scambio viene elaborato come rimborso e nuovo acquisto: vengono create transazioni diverse per l'annullamento e il nuovo acquisto della prenotazione. L'importo ripartito della prenotazione viene rimborsato per le prenotazioni scambiate. Viene addebitato il costo completo del nuovo acquisto. L'importo ripartito della prenotazione è il valore residuo ripartito giornaliero della prenotazione restituita.
- È possibile scambiare o rimborsare prenotazioni anche se il contratto Enterprise usato per acquistare la prenotazione è scaduto ed è stato rinnovato come nuovo contratto.
- L'impegno per la durata della nuova prenotazione deve essere uguale o superiore all'impegno residuo della prenotazione restituita. Esempio: per una prenotazione di tre anni da 100 USD al mese e scambiata dopo il 18° pagamento, l'impegno per la durata della nuova prenotazione deve essere pari almeno a 1.800 USD, con pagamento mensile o anticipato.
- La nuova prenotazione acquistata come parte dello scambio ha un nuovo termine a partire dal momento del cambio.
- Non sono previste penali né limiti annuali per gli scambi.

**Criteri di rimborso**

- Non viene attualmente applicata alcuna penale per risoluzione anticipata, ma in futuro è possibile che venga applicata una penale per risoluzione anticipata del 12% per gli annullamenti.
- L'impegno totale annullato non può superare i 50.000 USD in una finestra mobile di 12 mesi per un profilo di fatturazione o una singola registrazione. Ad esempio, per una prenotazione di tre anni da 100 USD al mese e rimborsata nel 18° mese, l'impegno annullato sarà pari a 1.800 USD. Dopo il rimborso il nuovo limite disponibile per i rimborsi sarà pari a 48.200 USD. Dopo 365 giorni dal rimborso, il limite di 48.200 USD verrà incrementato di 1.800 USD e il nuovo pool sarà pari a 50.000 USD. Eventuali altri annullamenti di prenotazioni per il profilo di fatturazione o la registrazione EA esauriranno lo stesso pool e verrà applicata la stessa logica di rifornimento.
- Azure non elaborerà i rimborsi che superano il limite di 50.000 USD in una finestra di 12 mesi per un profilo di fatturazione o una registrazione EA.
- I rimborsi vengono calcolati in base al prezzo più basso, tra quello di acquisto o quello corrente della prenotazione.
- Solo i proprietari degli ordini di prenotazioni possono elaborare un rimborso. [Informazioni su come aggiungere o modificare gli utenti che possono gestire una prenotazione](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come gestire una prenotazione, vedere [Gestire le prenotazioni di Azure](manage-reserved-vm-instance.md).
- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
    - [Informazioni sulle prenotazioni di Azure](save-compute-costs-reservations.md)
    - [Gestire le prenotazioni in Azure](manage-reserved-vm-instance.md)
    - [Informazioni su come viene applicato lo sconto sulla prenotazione](../manage/understand-vm-reservation-charges.md)
    - [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](understand-reserved-instance-usage.md)
    - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](understand-reserved-instance-usage-ea.md)
    - [Costi del software Windows non inclusi nelle prenotazioni](reserved-instance-windows-software-costs.md)
    - [Prenotazioni di Azure nel programma CSP](/partner-center/azure-reservations)
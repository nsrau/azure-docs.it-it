---
title: Acquista prenotazioni di Azure con pagamenti anticipati o mensili
description: Scopri in che modo puoi acquistare prenotazioni di Azure con pagamenti anticipati o mensili.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: banders
ms.openlocfilehash: d211334ad2aa760cd63b98c6827fb2512811a1d3
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806948"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Prenotazioni di acquisto con pagamenti mensili

Fino ad ora, le prenotazioni di Azure richiedono il pagamento anticipato. È ora possibile pagare le prenotazioni con pagamenti mensili. A differenza di un acquisto anticipato in cui si paga l'importo completo, l'opzione di pagamento mensile divide il costo totale della prenotazione in modo uniforme ogni mese del periodo. Il costo totale di prenotazioni anticipate e mensili è lo stesso e non vengono addebitate spese aggiuntive quando si sceglie di pagare mensilmente.

L'importo del pagamento mensile può variare a seconda del tasso di cambio di mercato del mese corrente per la valuta locale.

Sono disponibili pagamenti mensili per:

- Macchine virtuali
- Database SQL
- SQL Data Warehouse
- Cosmos DB
- Tariffa del timbro del servizio app

Prenotazioni di acquisto nel [portale di Azure](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade).

![Esempio che mostra l'acquisto della prenotazione](./media/billing-monthly-payments-reservations/purchase-reservation.png)

Durante l'acquisto di una prenotazione, è possibile visualizzare la pianificazione dei pagamenti. Fare clic su **Visualizza pianificazione pagamenti completi**.

![Esempio che mostra la pianificazione del pagamento della prenotazione](./media/billing-monthly-payments-reservations/prepurchase-schedule.png)

Per visualizzare la pianificazione dei pagamenti dopo l'acquisto, selezionare una prenotazione, fare clic sull' **ID dell'ordine di prenotazione**e quindi fare clic sulla scheda **pagamenti** .

## <a name="view-payments-made"></a>Visualizza i pagamenti effettuati

È possibile visualizzare i pagamenti effettuati usando le API, i dati di utilizzo e l'analisi dei costi. Per le prenotazioni pagate per il mese, il valore Frequency viene visualizzato come **ricorrente** nell'API relativa ai dati di utilizzo e ai costi di prenotazione. Per le prenotazioni pagate in anticipo, il valore viene **visualizzato come una**volta.

L'analisi dei costi Mostra gli acquisti mensili nella visualizzazione predefinita. Applicare il filtro di **acquisto** per il **tipo di addebito** e per la **frequenza** **per visualizzare** tutti gli acquisti. Per visualizzare solo le prenotazioni, applicare un filtro per la **prenotazione**.

![Esempio che mostra i costi di acquisto della prenotazione nell'analisi dei costi](./media/billing-monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Passa a pagamenti mensili al rinnovo

Quando si rinnova una prenotazione, è possibile modificare la frequenza di fatturazione a ogni mese.

## <a name="exchange-and-refunds"></a>Exchange e rimborsi

Analogamente ad altre prenotazioni, è possibile rimborsare o scambiare prenotazioni acquistate con una fatturazione mensile. Attualmente, è possibile inviare una richiesta di supporto per avviare uno scambio o un rimborso per una prenotazione acquistata con fatturazione mensile.

Quando si scambia una prenotazione pagata mensilmente, il costo di durata totale del nuovo acquisto deve essere superiore ai pagamenti rimanenti annullati per la prenotazione restituita. Non sono previsti altri limiti o tariffe per gli scambi. È possibile scambiare una prenotazione pagata in anticipo per acquistare una nuova prenotazione fatturata mensilmente. Il valore di durata della nuova prenotazione, tuttavia, deve essere maggiore del valore con prorata della prenotazione da restituire.

Se si annulla una prenotazione che viene pagata mensilmente, Microsoft potrebbe applicare una tariffa di annullamento ai pagamenti con commit futuri annullati. I pagamenti rimanenti di cui è stato eseguito il commit aumentano fino al limite di rimborso $50.000 USD.

Per ulteriori informazioni su Exchange e i rimborsi, vedere la pagina relativa agli [scambi self-service e ai rimborsi per le prenotazioni di Azure](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="faq"></a>Domande frequenti

D: Azure offre "prenotazioni iniziali parziali"?<br>
R. No. Poiché i costi per le prenotazioni pagate in anticipo e mensili sono gli stessi, Microsoft non supporta i pagamenti parziali iniziali.

D: Sono disponibili pagamenti mensili per il programma Microsoft Cloud Solution Provider (CSP)?<br>
R. Sì, i partner possono acquistare prenotazioni per i clienti CSP nella portale di Azure. La possibilità di acquistare prenotazioni con fatturazione mensile non è disponibile nel centro per i partner.

D: Sono un cliente di Azure per enti pubblici, posso pagare mensilmente per gli acquisti di prenotazioni?<br>
R. No, per il momento.

D: Quando è possibile scambiare o rimborsare i portale di Azure, anziché creare un ticket di supporto?<br>
R. No, per il momento. Le richieste di scambio e di rimborso con pagamenti mensili vengono gestite dal supporto tecnico di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle prenotazioni, vedere [che cosa sono le prenotazioni di Azure?](billing-save-compute-costs-reservations.md)
- Per informazioni sulle attività che è necessario eseguire prima di acquistare una prenotazione, vedere [determinare le dimensioni corrette della macchina virtuale prima di acquistare](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)

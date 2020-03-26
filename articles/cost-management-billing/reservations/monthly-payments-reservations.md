---
title: Acquistare prenotazioni di Azure con pagamenti anticipati o mensili
description: Informazioni su come acquistare prenotazioni di Azure con pagamenti anticipati o mensili.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: ede60adc13dadc38e18ee5ade468e01b16523f4f
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235735"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Acquistare prenotazioni con pagamenti mensili

Ora è possibile pagare le prenotazioni con pagamenti mensili. A differenza di un acquisto anticipato, in cui si paga l'importo completo, l'opzione di pagamento mensile divide il costo totale della prenotazione in modo uniforme per ogni mese del periodo. Il costo totale delle prenotazioni con pagamento anticipato e mensile è lo stesso e non vengono addebitate spese aggiuntive quando si sceglie di pagare mensilmente.

Se la prenotazione è stata acquistata tramite un Contratto del cliente Microsoft, l'importo del pagamento mensile potrebbe essere soggetto a variazioni in base al tasso di cambio del mese corrente per la valuta locale.

I pagamenti mensili non sono disponibili per: Databricks, prenotazioni SUSE Linux, piani Red Hat e calcolo Azure Red Hat OpenShift.

Acquistare le prenotazioni nel [portale di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Docs).

![Esempio che mostra l'acquisto di una prenotazione](./media/monthly-payments-reservations/purchase-reservation.png)

Durante l'acquisto di una prenotazione, è possibile visualizzare la pianificazione dei pagamenti. Fare clic su **Visualizza la pianificazione completa del pagamento** .

![Esempio che mostra la pianificazione dei pagamenti di una prenotazione](./media/monthly-payments-reservations/prepurchase-schedule.png)

Per visualizzare la pianificazione dei pagamenti dopo l'acquisto, selezionare una prenotazione, fare clic su **ID ordine prenotazione** e quindi fare clic sulla scheda **Pagamenti**.

## <a name="view-payments-made"></a>Visualizzare i pagamenti effettuati

È possibile visualizzare i pagamenti effettuati usando le API, i dati di utilizzo e l'analisi dei costi. Per le prenotazioni pagate mensilmente, il valore della frequenza viene visualizzato come **ricorrente** nei dati di utilizzo e nell'API relativa agli addebiti della prenotazione. Per le prenotazioni pagate in anticipo, il valore viene visualizzato come **una tantum**.

L'analisi dei costi mostra gli acquisti mensili nella visualizzazione predefinita. Applicare il filtro **acquisto** per **Tipo di addebito** e **ricorrente** per **Frequenza** per visualizzare tutti gli acquisti. Per visualizzare solo le prenotazioni, applicare un filtro per **Prenotazione**.

![Esempio che mostra i costi di acquisto della prenotazione nell'analisi dei costi](./media/monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Passare ai pagamenti mensili al rinnovo

Al rinnovo di una prenotazione, è possibile cambiare la frequenza di fatturazione in mensile.

## <a name="exchange-and-refunds"></a>Cambio e rimborsi

Analogamente ad altre prenotazioni, è possibile scambiare le prenotazioni acquistate con la fatturazione mensile o ottenere un rimborso. 

Quando si scambia una prenotazione con pagamento mensile, il costo di durata totale del nuovo acquisto deve essere superiore ai pagamenti rimanenti annullati per la prenotazione restituita. Non sono previsti altri limiti o tariffe per gli scambi. È possibile scambiare una prenotazione pagata in anticipo per acquistare una nuova prenotazione fatturata mensilmente. Il valore di durata della nuova prenotazione, tuttavia, deve essere maggiore del valore ripartito in modo proporzionale della prenotazione da restituire.

Se si annulla una prenotazione con pagamento mensile, i pagamenti futuri annullati vengono cumulati fino al limite di rimborso di $ 50.000 USD.

Per altre informazioni su scambi e rimborsi, vedere [Scambi e rimborsi in modalità self-service per le prenotazioni di Azure](exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle prenotazioni, vedere [Informazioni sulle prenotazioni di Azure](save-compute-costs-reservations.md).
- Per informazioni sulle attività da eseguire prima di acquistare una prenotazione, vedere [Determinare le dimensioni corrette delle macchine virtuali prima dell'acquisto](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).

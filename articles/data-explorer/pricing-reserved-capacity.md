---
title: Pagamento anticipato per il markup di Azure Data Explorer per risparmiare denaro
description: Informazioni su come acquistare la capacità riservata di Azure Data Explorer per risparmiare sui costi di Azure Data Explorer.Learn how to buy Azure Data Explorer reserved capacity to save on your Azure Data Explorer costs.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: bbb2108967353b851a0fa0ee610ec30380e3fc50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969276"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Pagamento preliminare per le unità di markup di Azure Data Explorer con capacità riservata di Azure Data ExplorerPrepay for Azure Data Explorer markup units with Azure Data Explorer reserved capacity

Risparmia denaro con Azure Data Explorer pagando in anticipo le unità di markup rispetto ai prezzi con pagamento in base al tuo go. Con la capacità riservata di Azure Data Explorer, si assume un impegno per l'uso di Azure Data Explorer per un periodo di uno o tre anni per ottenere uno sconto significativo sui costi del markup di Azure Data Explorer.With Azure Data Explorer reserved capacity, you make a commitment for Azure Data Explorer use for a period of one or three years to get a significant discount on the Azure Data Explorer markup costs. Per acquistare la capacità riservata di Azure Data Explorer, è sufficiente specificare il termine, verrà applicato a tutte le distribuzioni di Azure Data Explorer in tutte le aree.

Acquistando una prenotazione, stai pagando in tempo pedina per i costi di ricarico per un periodo di uno o tre anni. Non appena si acquista una prenotazione, gli addebiti per il markup di Azure Data Explorer corrispondenti agli attributi della prenotazione non vengono più addebitati alle tariffe con pagamento in base al numero di acquisti. I cluster di Azure Data Explorer già in esecuzione o quelli appena distribuiti otterranno automaticamente il vantaggio. Questa prenotazione non riguarda i costi di calcolo, rete o archiviazione associati ai cluster. La capacità riservata per queste risorse deve essere acquistata separatamente. Alla fine del periodo di prenotazione, il vantaggio di fatturazione scade e le unità di markup di Azure Data Explorer vengono fatturate al prezzo con pagamento in base al costo. Le prenotazioni non si rinnovano automaticamente. Per informazioni sui prezzi, vedere la [pagina dei prezzi di Azure Data Explorer.For](https://azure.microsoft.com/pricing/details/data-explorer/)pricing information, see the Azure Data Explorer pricing page .

È possibile acquistare la capacità riservata di Azure Data Explorer nel portale di Azure.You can buy Azure Data Explorer reserved capacity in the [Azure portal](https://portal.azure.com). Per acquistare la capacità riservata di Azure Data Explorer:To buy Azure Data Explorer reserved capacity:

* Devi essere il proprietario di almeno un abbonamento Enterprise o Pay-As-You-Go.
* Per le sottoscrizioni Enterprise, **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore EA per l'abbonamento.
* Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare la capacità riservata di Azure Data Explorer.For the Cloud Solution Provider (CSP) program, only the admin agents or sales agents can purchase Azure Data Explorer reserved capacity.

Per informazioni dettagliate su come vengono addebitati gli acquisti di prenotazioni per i clienti aziendali e i clienti con pagamento in base al conto, vedere:
* [Informazioni sull'utilizzo della prenotazione di Azure per l'iscrizione Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 
* [Informazioni sull'utilizzo](../cost-management-billing/reservations/understand-reserved-instance-usage.md)della prenotazione di Azure per la sottoscrizione con pagamento in base al consumo.

## <a name="determine-the-right-markup-usage-before-purchase"></a>Determinare l'utilizzo corretto del markup prima dell'acquisto

La dimensione della prenotazione deve essere basata sul numero totale di unità di markup di Azure Data Explorer usate dai cluster di Azure Data Explorer esistenti o che verranno presto distribuiti. Il numero di unità di markup è uguale al numero di core del cluster del motore di Azure Data Explorer nell'ambiente di produzione (escluso lo SKU di sviluppo/test). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Acquistare la capacità riservata di Azure Data Explorer

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Selezionare **Tutti i servizi** > **Prenotazione** > **Acquista ora**. Selezionare **Aggiungi**
1. Nel riquadro **Seleziona tipo di prodotto** selezionare Azure Data Explorer per acquistare una nuova prenotazione per le unità di markup di Azure Data Explorer.In the Select Product Type pane, select Azure Data **Explorer** to purchase a new reservation for Azure Data Explorer markup units. 
1. Seleziona **Acquista**
1. Compilare i campi obbligatori. 

    ![Pagina di acquisto](media/pricing-reserved-capacity/purchase-page.png)

1. Esaminare il costo della prenotazione della capacità riservata del markup di Azure Data Explorer nella sezione **Costi.Review** the cost of the Azure Data Explorer reserved capacity reservation in the Costs section.
1. Selezionare **Acquista**.
1. Selezionare **Visualizza questa prenotazione** per vedere lo stato dell'acquisto.

## <a name="cancellations-and-exchanges"></a>Annullamenti e scambi

Se è necessario annullare la prenotazione della capacità riservata di Azure Data Explorer, potrebbe essere presente una tariffa di terminazione anticipata del 12%. I rimborsi si basano sul prezzo più basso del prezzo di acquisto o sul prezzo corrente della prenotazione. I rimborsi sono limitati a $50.000 dollari all'anno. Il rimborso ricevuto corrisponde al saldo ripartito rimanente meno il costo per la risoluzione del 12%. Per richiedere un annullamento, andare alla prenotazione nel portale di Azure e selezionare **Rimborso** per creare una richiesta di supporto.

Se è necessario modificare la prenotazione della capacità riservata di Azure Data Explorer in un altro termine, è possibile scambiarlo con un'altra prenotazione di valore uguale o maggiore. Come data di inizio del periodo della nuova prenotazione non viene conservata quella della prenotazione scambiata. Il periodo di uno o tre anni inizia dal momento della creazione della nuova prenotazione. Per richiedere uno scambio, passare alla prenotazione nel portale di Azure e selezionare **Scambio** per creare una richiesta di supporto.

Per ulteriori informazioni su come scambiare o rimborsare le prenotazioni, vedere Scambi di [prenotazione e rimborsi](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="manage-your-reserved-capacity-reservation"></a>Gestire la prenotazione della capacità riservata

Lo sconto di prenotazione delle unità di markup di Azure Data Explorer viene applicato automaticamente al numero di unità di markup che corrispondono all'ambito e agli attributi di prenotazione della capacità riservata di Azure Data Explorer.The Azure Data Explorer markup units reservation discount is applied automatically to the number of markup units that match the Azure Data Explorer reserved capacity reservation scope and attributes. 


> [!NOTE]
> * È possibile aggiornare l'ambito della prenotazione della capacità riservata di Azure Data Explorer tramite il portale di [Azure,](https://portal.azure.com)PowerShell, l'interfaccia della riga di comando o l'API.
> * Per informazioni su come gestire la prenotazione della capacità riservata di Azure Data Explorer, vedere [Gestire la capacità riservata](../cost-management-billing/reservations/understand-azure-data-explorer-reservation-charges.md)di Azure Data Explorer.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

* [Informazioni sulle prenotazioni di Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Gestire le prenotazioni di AzureManage Azure Reservations](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Comprendere lo sconto sulle prenotazioni di Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
* [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

Se hai domande o hai bisogno di aiuto, [crea una richiesta](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)di supporto .

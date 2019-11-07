---
title: Pagamento anticipato per il markup di Esplora dati di Azure per risparmiare denaro
description: Scopri come acquistare la capacità riservata di Azure Esplora dati per risparmiare sui costi di Esplora dati di Azure.
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 66c5644df7d796669105693d08788548334ae93a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681634"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Pagamento anticipato per le unità di markup Esplora dati di Azure con la capacità riservata Esplora dati di Azure

Risparmia denaro con Esplora dati di Azure prepagando le unità di markup rispetto ai prezzi con pagamento in base al consumo. Con la capacità riservata di Azure Esplora dati è possibile impegnarsi per l'uso di Azure Esplora dati per un periodo di uno o tre anni per ottenere uno sconto significativo sui costi di markup di Azure Esplora dati. Per acquistare la capacità riservata di Azure Esplora dati, è sufficiente specificare il termine, che verrà applicata a tutte le distribuzioni di Esplora dati di Azure in tutte le aree.

Con l'acquisto di una prenotazione, il pagamento anticipato per i costi di markup viene effettuato per un periodo di uno o tre anni. Non appena si acquista una prenotazione, i costi di markup di Azure Esplora dati che corrispondono agli attributi di prenotazione non vengono più addebitati in base alle tariffe con pagamento in base al consumo. I cluster Esplora dati di Azure già in esecuzione o quelli appena distribuiti otterranno automaticamente il vantaggio. Questa prenotazione non copre i prezzi di calcolo, di rete o di archiviazione associati ai cluster. La capacità riservata per queste risorse deve essere acquistata separatamente. Al termine del periodo di prenotazione, il vantaggio di fatturazione scade e le unità di markup di Azure Esplora dati vengono fatturate in base al prezzo con pagamento in base al consumo. Le prenotazioni non vengono rinnovate automaticamente. Per informazioni sui prezzi, vedere la [pagina dei prezzi di Azure Esplora dati](https://azure.microsoft.com/pricing/details/data-explorer/).

È possibile acquistare la capacità riservata di Azure Esplora dati nel [portale di Azure](https://portal.azure.com). Per acquistare la capacità riservata di Azure Esplora dati:

* È necessario essere il proprietario di almeno una sottoscrizione Enterprise o con pagamento in base al consumo.
* Per le sottoscrizioni Enterprise, **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore EA nella sottoscrizione.
* Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare la capacità riservata di Azure Esplora dati.

Per informazioni dettagliate sul modo in cui i clienti aziendali e i clienti con pagamento in base al consumo vengono addebitati sugli acquisti di prenotazione, vedere:
* [Informazioni sull'utilizzo della prenotazione di Azure per la registrazione Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md) 
* [Informazioni sull'utilizzo delle prenotazioni di Azure per la sottoscrizione con pagamento in base al consumo](../billing/billing-understand-reserved-instance-usage.md).

## <a name="determine-the-right-markup-usage-before-purchase"></a>Determinare l'utilizzo del markup corretto prima dell'acquisto

La dimensione della prenotazione deve essere basata sul numero totale di unità di markup di Azure Esplora dati usate dai cluster di Azure Esplora dati esistenti o presto distribuiti. Il numero di unità di markup è uguale al numero di core del cluster del motore Esplora dati di Azure nell'ambiente di produzione (escluso lo SKU di sviluppo/test). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Acquistare la capacità riservata di Azure Esplora dati

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **tutti i servizi** > **prenotazioni** > **Acquista ora**. Selezionare **Aggiungi**
1. Nel riquadro **Seleziona tipo di prodotto** selezionare **Azure Esplora dati** per acquistare una nuova prenotazione per le unità di markup di Azure Esplora dati. 
1. Seleziona **Acquista**
1. Compilare i campi obbligatori. 

    ![Pagina di acquisto](media/pricing-reserved-capacity/purchase-page.png)

1. Esaminare il costo della prenotazione di capacità riservata markup di Azure Esplora dati nella sezione **costi** .
1. Selezionare **Acquisto**.
1. Selezionare **Visualizza questa prenotazione** per vedere lo stato dell'acquisto.

## <a name="cancellations-and-exchanges"></a>Annullamenti e scambi

Se è necessario annullare la prenotazione di capacità riservata di Azure Esplora dati, potrebbe essere prevista una tariffa per l'interruzione anticipata del 12%. I rimborsi si basano sul prezzo minimo del prezzo di acquisto o sul prezzo attuale della prenotazione. I rimborsi sono limitati a $50.000 dollari all'anno. Il rimborso ricevuto corrisponde al saldo ripartito rimanente meno la quota del 12% per l'interruzione anticipata. Per richiedere un annullamento, andare alla prenotazione nel portale di Azure e selezionare **Rimborso** per creare una richiesta di supporto.

Se è necessario modificare la prenotazione di capacità riservata di Azure Esplora dati a un altro termine, è possibile scambiarla per un'altra prenotazione con un valore uguale o superiore. Come data di inizio del periodo della nuova prenotazione non viene conservata quella della prenotazione scambiata. Il periodo di uno o tre anni inizia dal momento della creazione della nuova prenotazione. Per richiedere uno scambio, passare alla prenotazione nel portale di Azure e selezionare **Scambio** per creare una richiesta di supporto.

Per ulteriori informazioni su come scambiare o rimborsare le prenotazioni, vedere la pagina relativa agli [scambi di prenotazione e](../billing/billing-azure-reservations-self-service-exchange-and-refund.md)ai rimborsi.

## <a name="manage-your-reserved-capacity-reservation"></a>Gestire la prenotazione di capacità riservata

Lo sconto di prenotazione unità di markup di Azure Esplora dati viene applicato automaticamente al numero di unità di markup che corrispondono agli attributi e all'ambito di prenotazione di capacità riservata di Azure Esplora dati. 


> [!NOTE]
> * È possibile aggiornare l'ambito della prenotazione di capacità riservata di Azure Esplora dati tramite il [portale di Azure](https://portal.azure.com), PowerShell, l'interfaccia della riga di comando o tramite l'API.
> * Per informazioni su come gestire la prenotazione di capacità riservata di Azure Esplora dati, vedere [gestire la capacità riservata di azure Esplora dati](../billing/billing-understand-kusto-azuredataexplorer-reservation-charges.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

* [Informazioni sulle prenotazioni di Azure](../billing/billing-save-compute-costs-reservations.md)
* [Gestire le prenotazioni di Azure](../billing/billing-manage-reserved-vm-instance.md)
* [Comprendere lo sconto sulle prenotazioni di Azure](../billing/billing-understand-reservation-charges.md)
* [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](../billing/billing-understand-reserved-instance-usage.md)
* [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)
* [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Opzioni per Contatti

In caso di domande, creare una [Richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) per assistenza.

---
title: Pagamento in anticipo per il timbro del servizio app Azure con capacità riservata
description: Scopri in che modo puoi pagare in anticipo la tariffa per il timbro isolato del servizio app Azure con capacità riservata per risparmiare denaro.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: banders
ms.openlocfilehash: 40ccee7a993ce39a9b4c7a86309b0554daa56026
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298269"
---
# <a name="prepay-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>Pagamento in anticipo per il timbro del servizio app Azure con capacità riservata

Per una durata di tre anni, è possibile risparmiare sui costi del timbro isolato del servizio app Azure. Per acquistare la capacità riservata dei timbri isolati, è necessario scegliere l'area di Azure in cui verrà distribuito il timbro e il numero di timbri da acquistare.

Quando si acquista una prenotazione, l'utilizzo della tariffa del timbro isolato corrispondente agli attributi di prenotazione non viene più addebitato in base alle tariffe con pagamento in base al consumo. La prenotazione viene applicata automaticamente al numero di indicatori isolati che corrispondono all'ambito e all'area di capacità riservata. Non è necessario assegnare una prenotazione a un timbro isolato. La prenotazione non è applicabile ai dipendenti, quindi qualsiasi altra risorsa associata al timbro viene addebitata separatamente.

Quando la capacità riservata scade, gli indicatori isolati continuano a funzionare, ma vengono fatturati in base alla tariffa con pagamento in base al consumo. Le prenotazioni non vengono rinnovate automaticamente.

## <a name="determine-the-right-reservation-to-purchase"></a>Determinare la prenotazione corretta da acquistare

Con l'acquisto di una prenotazione, si paga in anticipo per usare le quantità riservate sempre nei prossimi tre anni. Controllare i dati di utilizzo per determinare il numero di timbri servizio app Isolato che si utilizza in modo coerente e che potrebbero essere utilizzati in futuro.

Inoltre, assicurarsi di comprendere il modo in cui il timbro isolato emette il contatore Linux o Windows.

- Per impostazione predefinita, un timbro isolato vuoto emette il contatore di indicatori di Windows. Ad esempio, senza i thread di lavoro distribuiti. Continua a emettere questo contatore se i processi di lavoro di Windows vengono distribuiti nello Stamp.
- Se si distribuisce un ruolo di lavoro Linux, il misuratore passa al contatore di indicatori Linux.
- Nei casi in cui vengono distribuiti sia Linux che Windows Worker, il timbro emette il contatore di Windows.

Il contatore dei timbri può quindi variare tra Windows e Linux in base alla durata del timbro.

Acquistare le prenotazioni di Windows Stamp se uno o più ruoli di lavoro di Windows sono presenti nello Stamp. L'unico momento in cui è necessario acquistare una prenotazione di timbri Linux è se si prevede di avere _solo_ i ruoli di lavoro Linux.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Acquistare la capacità riservata di un timbro isolato

È possibile acquistare capacità riservata con timbri isolati nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D). Per acquistare capacità riservata, è necessario avere il ruolo proprietario per almeno una sottoscrizione Enterprise o una sottoscrizione singola con tariffe con pagamento in base al consumo.

- Per le sottoscrizioni aziendali, l'opzione **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com/). In alternativa, se l'impostazione è disabilitata, è necessario essere un amministratore EA.
- Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare SQL Data Warehouse capacità riservata.

**Per acquistare:**

1. Passare alla [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Selezionare una sottoscrizione. Usare l'elenco **sottoscrizione** per scegliere la sottoscrizione usata per pagare la capacità riservata. Il metodo di pagamento della sottoscrizione viene addebitato ai costi iniziali per la capacità riservata. Il tipo di sottoscrizione deve essere un contratto Enterprise (numeri offerta: MS-AZR-0017P o MS-AZR-0148P) o con pagamento in base al consumo (numeri offerta: MS-AZR-0003P o MS-AZR-0023P) o una sottoscrizione CSP.
    - Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto.
    - Se si dispone di una sottoscrizione con pagamento in base al consumo, il costo viene addebitato alla carta di credito o al metodo di pagamento tramite fattura per la sottoscrizione.
1. Selezionare un **ambito** per scegliere un ambito di sottoscrizione.
    - **Ambito del gruppo di risorse singolo** : applica lo sconto di prenotazione alle risorse corrispondenti solo nel gruppo di risorse selezionato.
    - **Singolo ambito di sottoscrizione** : applica lo sconto di prenotazione alle risorse corrispondenti nella sottoscrizione selezionata.
    - **Ambito condiviso** : applica lo sconto di prenotazione alle risorse corrispondenti nelle sottoscrizioni idonee presenti nel contesto di fatturazione. Per Enterprise Agreement clienti, il contesto di fatturazione è la registrazione. Per le singole sottoscrizioni con tariffe con pagamento in base al consumo, l'ambito di fatturazione è costituito da tutte le sottoscrizioni idonee create dall'amministratore account.
1. Selezionare un' **area** per scegliere un'area di Azure coperta dalla capacità riservata e aggiungere la prenotazione al carrello.
1. Selezionare un tipo di piano isolato, quindi fare clic su **Seleziona**.  
    ![Esempio](./media/billing-prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. Immettere la quantità di servizio app Isolato timbri da riservare. Una quantità pari a tre, ad esempio, darebbe tre timbri riservati a un'area. Fare clic su **Avanti: Verifica + Acquista**.
1. Esaminare e fare clic su **Acquista ora**.

Dopo l'acquisto, passare a [prenotazioni](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) per visualizzare lo stato di acquisto e monitorarlo in qualsiasi momento.

## <a name="cancellations-and-exchanges"></a>Annullamenti e scambi

Se è necessario annullare la capacità riservata dei timbri isolati, potrebbe essere prevista una tariffa per l'interruzione anticipata del 12%. I rimborsi si basano sul prezzo più basso tra il prezzo di acquisto o il prezzo corrente della prenotazione. I rimborsi sono limitati a $50.000,00 all'anno. Il rimborso ricevuto è il saldo proporzionale rimanente meno il 12% della tariffa per la terminazione anticipata. Per annullare, passare alla prenotazione nel portale di Azure e selezionare **rimborso**.

Se è necessario spostare la capacità riservata dei timbri isolati in un'altra area, è possibile scambiarla per un'altra prenotazione con un valore uguale o superiore. Come data di inizio del periodo della nuova prenotazione non viene conservata quella della prenotazione scambiata. Un termine di tre anni inizia quando si crea la nuova prenotazione. Per scambiare, passare alla portale di Azure, selezionare la prenotazione che si vuole scambiare e selezionare **Exchange**.

Per ulteriori informazioni su come scambiare o rimborsare le prenotazioni, vedere la pagina relativa agli [scambi di prenotazione e](billing-azure-reservations-self-service-exchange-and-refund.md)ai rimborsi.

## <a name="discount-application-shown-in-usage-data"></a>Applicazione Discount mostrata nei dati di utilizzo

I dati di utilizzo hanno un prezzo effettivo pari a zero per l'utilizzo che ottiene uno sconto di prenotazione. I dati di utilizzo mostrano lo sconto relativo alla prenotazione per ogni istanza di timbro in ogni prenotazione.

Per ulteriori informazioni sul modo in cui vengono visualizzati i dati di utilizzo, vedere [ottenere Enterprise Agreement costi di prenotazione e utilizzo](billing-understand-reserved-instance-usage-ea.md) se si è un cliente Enterprise Agreement (EA). In caso contrario, è possibile [comprendere l'utilizzo della prenotazione di Azure per la sottoscrizione singola con tariffe con pagamento in base](billing-understand-reserved-instance-usage.md)al consumo.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
  - [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
  - [Informazioni sul modo in cui viene applicato uno sconto di prenotazione per un servizio di app Azure isolato](billing-reservation-discount-app-service-isolated-stamp.md)
  - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)

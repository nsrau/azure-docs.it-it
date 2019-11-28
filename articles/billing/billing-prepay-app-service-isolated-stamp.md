---
title: Risparmiare sui costi della tariffa stamp per il servizio app di Azure per ambiente isolato con la capacità riservata
description: Informazioni su come risparmiare sui costi della tariffa stamp per il servizio app di Azure per ambiente isolato con la capacità riservata.
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 5aafc2288b9e6b7ef217ec386211af7c3739c08b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223662"
---
# <a name="save-costs-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>Risparmiare sui costi della tariffa stamp per il servizio app di Azure per ambiente isolato con la capacità riservata

È possibile risparmiare sui costi della tariffa stamp per il servizio app di Azure per ambiente isolato impegnando una prenotazione per l'utilizzo di stamp per una durata di tre anni. Per acquistare capacità riservata per la tariffa stamp per il servizio app per ambiente isolato, è necessario scegliere l'area di Azure in cui lo stamp verrà distribuito e il numero di stamp da acquistare.

Quando si acquista una prenotazione, l'utilizzo della tariffa stamp per il servizio app per ambiente isolato corrispondente agli attributi della prenotazione non viene più addebitato in base alle tariffe con pagamento in base al consumo. La prenotazione viene applicata automaticamente al numero di stamp isolati che corrispondono all'ambito e all'area della capacità riservata. Non è necessario assegnare una prenotazione a uno stamp isolato. La prenotazione non si applica ai ruoli di lavoro, quindi qualsiasi altra risorsa associata allo stamp viene addebitata separatamente.

Allo scadere della capacità riservata, l'esecuzione degli stamp isolati continua, ma gli stamp vengono addebitati in base alla tariffa con pagamento in base al consumo. Le prenotazioni non vengono rinnovate automaticamente.

## <a name="determine-the-right-reservation-to-purchase"></a>Determinare la prenotazione corretta da acquistare

Con l'acquisto di una prenotazione, ci si impegna a usare le quantità riservate nei tre anni successivi. Controllare i dati di utilizzo per determinare quanti stamp isolati del servizio app si usano in modo coerente e potrebbero essere usati in futuro.

È importante anche comprendere i meccanismi in base a cui lo stamp isolato genera un contatore Linux o Windows.

- Per impostazione predefinita, con uno stamp isolato vuoto viene generato il contatore dello stamp Windows, ad esempio senza lavoratori distribuiti. Continua a generare questo contatore se nello stamp vengono distribuiti ruoli di lavoro di Windows.
- Se si distribuisce un ruolo di lavoro Linux, il contatore passa al contatore dello stamp Linux.
- Nei casi in cui vengono distribuiti ruoli di lavoro sia Linux che Windows, lo stamp genera il contatore di Windows.

Il contatore dello stamp può quindi passare da Windows a Linux e viceversa durante il ciclo di vita dello stamp.

Acquistare prenotazioni dello stamp di Windows se nello stamp sono presenti uno o più ruoli di lavoro di Windows. Una prenotazione dello stamp di Linux deve essere acquistata solo se si prevede che lo stamp conterrà _solo_ ruoli di lavoro di Linux.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Acquistare capacità riservata per uno stamp isolato

È possibile acquistare capacità riservata per uno stamp isolato nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D). Usare [pagamenti anticipati o mensili](billing-monthly-payments-reservations.md) per acquistare la prenotazione. Per acquistare capacità riservata, è necessario avere il ruolo di proprietario per almeno una sottoscrizione Enterprise o una sottoscrizione individuale con pagamento in base al consumo.

- Per le sottoscrizioni Enterprise, l'opzione **Aggiungi istanze riservate** deve essere abilitata in [EA Portal](https://ea.azure.com/). Se questa impostazione è disabilitata, è necessario essere un amministratore EA.
- Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o di vendita possono acquistare capacità riservata per uno stamp.

**Per effettuare l'acquisto:**

1. Accedere al [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Selezionare una sottoscrizione. Usare l'elenco **Sottoscrizione** per scegliere la sottoscrizione usata per pagare la capacità riservata. I costi relativi alla capacità riservata vengono addebitati in base al metodo di pagamento della sottoscrizione. Il tipo di sottoscrizione deve essere un contratto Enterprise (numeri offerta: MS-AZR-0017P o MS-AZR-0148P) o con pagamento in base al consumo (numeri offerta: MS-AZR-0003P o MS-AZR-0023P) o sottoscrizione CSP.
    - Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto.
    - Se si dispone di una sottoscrizione con pagamento in base al consumo, il costo viene addebitato alla carta di credito o al metodo di pagamento tramite fattura per la sottoscrizione.
1. Usare l'elenco **Ambito** per scegliere un ambito della sottoscrizione.
    - **Gruppo di risorse singolo**: lo sconto della prenotazione viene applicato solo alle risorse corrispondenti incluse nel gruppo di risorse selezionato.
    - **Sottoscrizione singola**: lo sconto della prenotazione viene applicato alle risorse corrispondenti incluse nella sottoscrizione selezionata.
    - **Condiviso**: lo sconto della prenotazione viene applicato alle risorse corrispondenti nelle sottoscrizioni idonee incluse nel contesto di fatturazione. Per i clienti con contratto Enterprise Agreement, il contesto di fatturazione è la registrazione. Per le singole sottoscrizioni che prevedono tariffe con pagamento in base al consumo, l'ambito di fatturazione è costituito da tutte le sottoscrizioni idonee create dall'amministratore account.
1. In **Area** selezionare un'area di Azure coperta dalla capacità riservata e aggiungere la prenotazione al carrello.
1. Selezionare un tipo di piano Isolato, quindi fare clic su **Seleziona**.  
    ![Esempio ](./media/billing-prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. Immettere la quantità di stamp del servizio app Isolato da riservare. Ad esempio, una quantità pari a tre assegna tre stamp riservati a un'area. Fare clic su **Avanti: Rivedi e acquista**.
1. Controllare le informazioni e fare clic su **Acquista ora**.

Dopo l'acquisto, passare a [Prenotazioni](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) per visualizzare lo stato di acquisto e monitorarlo in qualsiasi momento.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Cambi self-service e rimborsi per le prenotazioni di Azure](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="discount-application-shown-in-usage-data"></a>Applicazione dello sconto visualizzata nei dati di utilizzo

I dati di utilizzo hanno un prezzo effettivo pari a zero per l'utilizzo che ottiene uno sconto sulla prenotazione. I dati di utilizzo mostrano lo sconto sulla prenotazione per ogni istanza di stamp in ogni prenotazione.

Per altre informazioni sul modo in cui lo sconto sulla prenotazione viene visualizzato nei dati di utilizzo, vedere [Ottenere informazioni sui costi di prenotazione e l'utilizzo dei contratti Enterprise Agreement](billing-understand-reserved-instance-usage-ea.md), se si ha un contratto Enterprise Agreement. In caso contrario, vedere [Informazioni sull'utilizzo della prenotazione di Azure per la sottoscrizione singola con pagamento in base al consumo](billing-understand-reserved-instance-usage.md).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
  - [Informazioni sulle prenotazioni di Azure](billing-save-compute-costs-reservations.md)
  - [Informazioni sull'applicazione dello sconto sulla prenotazione per uno stamp isolato del servizio app di Azure](billing-reservation-discount-app-service-isolated-stamp.md)
  - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](billing-understand-reserved-instance-usage-ea.md)

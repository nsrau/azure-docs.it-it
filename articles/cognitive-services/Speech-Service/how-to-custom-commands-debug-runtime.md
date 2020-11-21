---
title: Errori di debug durante l'esecuzione di un'applicazione di comandi personalizzati
titleSuffix: Azure Cognitive Services
description: In questo articolo si apprenderà come eseguire il debug degli errori di runtime in un'applicazione di comandi personalizzata.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 49d9b91df896646da7bf36e077d9f3c9187137dd
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021797"
---
# <a name="debug-errors-when-running-a-custom-commands-application"></a>Errori di debug durante l'esecuzione di un'applicazione di comandi personalizzati

Questo articolo descrive come eseguire il debug quando si verificano errori durante l'esecuzione dell'applicazione di comandi personalizzati. 

## <a name="connection-failed"></a>Connessione non riuscita

Se l'applicazione Esegui comandi personalizzati dall' [applicazione client (con l'SDK vocale)](./how-to-custom-commands-setup-speech-sdk.md) o il [client Windows Voice Assistant](./how-to-custom-commands-developer-flow-test.md), potrebbero verificarsi errori di connessione, come indicato di seguito:

| Codice errore | Dettagli |
| ------- | -------- |
| [401](#error-401) | AuthenticationFailure: l'aggiornamento di WebSocket non è riuscito con un errore di autenticazione |
| [1002](#error-1002)] | Il server ha restituito il codice di stato ' 404' quando era previsto il codice di stato ' 101'. |

### <a name="error-401"></a>errore 401
- L'area specificata nell'applicazione client non corrisponde all'area dell'applicazione di comando personalizzata

- La chiave della risorsa vocale non è valida
    
    Verificare che la chiave della risorsa vocale sia corretta.

### <a name="error-1002"></a>Errore 1002 
- L'applicazione del comando personalizzato non è pubblicata
    
    Pubblicare l'applicazione nel portale.

- Il comando personalizzato applicationId non è valido

    Verificare che l'ID applicazione del comando personalizzato sia corretto.
 applicazione comando personalizzata al di fuori della risorsa vocale

    Assicurarsi che l'applicazione di comando personalizzata venga creata sotto la risorsa di riconoscimento vocale.

Per ulteriori informazioni sulla risoluzione dei problemi di connessione, fare riferimento a [risoluzione dei problemi del client di Windows Voice Assistant](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf#troubleshooting)


## <a name="dialog-is-canceled"></a>Finestra di dialogo annullata

Quando si esegue l'applicazione dei comandi personalizzati, la finestra di dialogo viene annullata quando si verificano alcuni errori.

- Se si sta testando l'applicazione nel portale, viene visualizzata direttamente la descrizione dell'annullamento e viene riprodotto un errore earcon. 

- Se si esegue l'applicazione con il [client di Windows Voice Assistant](./how-to-custom-commands-developer-flow-test.md), viene prodotto un errore earcon. È possibile trovare l' **evento: CancelledDialog** nei **log attività**.

- Se si segue l'applicazione client di esempio dell'applicazione client [(con l'SDK vocale)](./how-to-custom-commands-setup-speech-sdk.md), si verificherà un errore earcon. È possibile trovare l' **evento: CancelledDialog** in **status**.

- Se si compila un'applicazione client personalizzata, è sempre possibile progettare le logiche desiderate per gestire gli eventi CancelledDialog.

L'evento CancelledDialog è costituito da codice di annullamento e descrizione, come indicato di seguito:

| Codice di annullamento | Descrizione annullamento |
| ------- | --------------- | ----------- |
| [MaxTurnThresholdReached](#no-progress-was-made-after-the-max-number-of-turns-allowed) | Non è stato eseguito alcun avanzamento dopo il numero massimo di consentiti |
| [RecognizerQuotaExceeded](#recognizer-usage-quota-exceeded) | Quota di utilizzo del riconoscimento superata |
| [RecognizerConnectionFailed](#connection-to-the-recognizer-failed) | Connessione al sistema di riconoscimento non riuscita |
| [RecognizerUnauthorized](#this-application-cannot-be-accessed-with-the-current-subscription) | Non è possibile accedere a questa applicazione con la sottoscrizione corrente |
| [RecognizerInputExceededAllowedLength](#input-exceeds-the-maximum-supported-length) | L'input supera la lunghezza massima supportata per il riconoscimento |
| [RecognizerNotFound](#recognizer-not-found) | Il riconoscimento non è stato trovato |
| [RecognizerInvalidQuery](#invalid-query-for-the-recognizer) | Query non valida per il riconoscimento |
| [RecognizerError](#recognizer-return-an-error) | Il riconoscimento restituisce un errore |

### <a name="no-progress-was-made-after-the-max-number-of-turns-allowed"></a>Non è stato eseguito alcun avanzamento dopo il numero massimo di consentiti
La finestra di dialogo viene annullata quando uno slot richiesto non viene aggiornato correttamente dopo un certo numero di riattivazioni. Il numero massimo di build è 3.

### <a name="recognizer-usage-quota-exceeded"></a>Quota di utilizzo del riconoscimento superata
Language Understanding (LUIS) presenta limiti di utilizzo delle risorse. In genere l'errore "superamento della quota di utilizzo del riconoscimento" può essere causato da: 
- La creazione di LUIS supera il limite

    Aggiungere una risorsa di stima all'applicazione comandi personalizzata: 
    1. passare a **Impostazioni**, risorsa Luis
    1. Scegliere una risorsa di stima dalla **risorsa di stima** oppure fare clic su **Crea nuova risorsa** 

- La risorsa di stima LUIS supera il limite

    Se si utilizza una risorsa di stima F0, il limite è di 10 mila al mese, 5 query al secondo.

Per informazioni dettagliate sui limiti delle risorse LUIS, vedere [Language Understanding utilizzo delle risorse e limite](../luis/luis-limits.md#resource-usage-and-limits)

### <a name="connection-to-the-recognizer-failed"></a>Connessione al sistema di riconoscimento non riuscita
In genere significa un errore di connessione temporanea al riconoscitore LUIS (Language Understanding). Riprovare e risolvere il problema.

### <a name="this-application-cannot-be-accessed-with-the-current-subscription"></a>Non è possibile accedere a questa applicazione con la sottoscrizione corrente
La sottoscrizione non è autorizzata ad accedere all'applicazione LUIS. 

### <a name="input-exceeds-the-maximum-supported-length"></a>L'input supera la lunghezza massima supportata
L'input ha superato i 500 caratteri. Per l'espressione di input sono consentiti al massimo 500 caratteri.

### <a name="invalid-query-for-the-recognizer"></a>Query non valida per il riconoscimento
L'input ha superato i 500 caratteri. Per l'espressione di input sono consentiti al massimo 500 caratteri.

### <a name="recognizer-return-an-error"></a>Il riconoscimento restituisce un errore
Il riconoscimento LUIS ha restituito un errore durante il tentativo di riconoscere l'input.

### <a name="recognizer-not-found"></a>Il riconoscimento non è stato trovato
Impossibile trovare il tipo di Riconoscitore specificato nel modello di finestra di dialogo comandi personalizzato. Attualmente, è supportato solo il [riconoscimento Language Understanding (Luis)](https://www.luis.ai/).

## <a name="other-common-errors"></a>Altri errori comuni
### <a name="unexpected-response"></a>Risposta imprevista
Le risposte impreviste possono essere causate da più operazioni. Alcuni controlli per iniziare:
- Sì/nessun Intent in frasi di esempio

    Attualmente non sono supportati gli Intent, tranne quando si usa la funzionalità with cresime. Non vengono rilevati tutti gli intenti Yes/No definiti nelle frasi di esempio.

- Intent ed esempi simili frasi tra i comandi

    L'accuratezza del riconoscimento LUIS può essere interessata quando due comandi condividono Intent simili ed esempi di frasi. È possibile provare a rendere le funzionalità del comando e le frasi di esempio il più possibile distinti.

    Per una procedura consigliata per migliorare l'accuratezza del riconoscimento, vedere la [procedura consigliata di Luis](../luis/luis-concept-best-practices.md).

- Finestra di dialogo annullata
    
    Verificare i motivi dell'annullamento nella sezione precedente.

### <a name="error-while-rendering-the-template"></a>Errore durante il rendering del modello
Un parametro non definito viene usato nella risposta vocale. 

### <a name="object-reference-not-set-to-an-instance-of-an-object"></a>Riferimento oggetto non impostato su un'istanza di un oggetto
Si dispone di un parametro vuoto nel payload JSON definito in **Invia attività al client** azione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Vedere gli esempi in GitHub](https://aka.ms/speech/cc-samples)
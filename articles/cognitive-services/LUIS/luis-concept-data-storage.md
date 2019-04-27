---
title: Archiviazione dei dati
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS archivia i dati crittografati in un archivio dati di Azure corrispondente alla regione specificata dalla chiave.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: diberry
ms.openlocfilehash: a1093c2a6303b453a17a52058303913de5ecfa8d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60812947"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Archiviazione e rimozione dei dati nei Servizi cognitivi di LUIS (Language Understanding)
LUIS archivia i dati crittografati in un archivio dati di Azure corrispondente alla regione specificata dalla chiave. Questi dati vengono archiviati per 30 giorni. 

## <a name="export-and-delete-app"></a>Esportare ed eliminare l'app
Gli utenti hanno il controllo completo sull'[esportazione](luis-how-to-start-new-app.md#export-app) e sull'[eliminazione](luis-how-to-start-new-app.md#delete-app) dell'app. 

## <a name="utterances"></a>Espressioni

Espressioni possono essere archiviati in due posizioni diverse. 

* Durante **il processo di creazione**, espressioni vengono create e archiviate nell'intenzione. Espressioni in Intent sono necessarie per un'app LUIS ha esito positivo. Dopo che l'app viene pubblicata e riceve le query in corrispondenza dell'endpoint, stringa di query della richiesta dell'endpoint, `log=false`, determina se utterance l'endpoint di archiviazione. Se l'endpoint è archiviato, diventa parte delle espressioni di apprendimento attivo trovato nel **compilare** sezione del portale, nella **esaminare utterances endpoint** sezione. 
* Quando si **esaminare utterances endpoint**e si aggiunge un utterance a un intent, il utterance non viene archiviato non è più come parte di espressioni l'endpoint per la revisione. Viene aggiunto alla finalità dell'applicazione. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Eliminare espressioni di esempio da un intent
Eliminare le espressioni di esempio usate per il training di [LUIS](luis-reference-regions.md). Le espressioni di esempio eliminate dall'app LUIS vengono rimosse dal servizio Web LUIS e non sono più disponibili per l'esportazione.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Eliminare espressioni in fase di revisione da apprendimento attivo

È possibile eliminare le espressioni dall'elenco di espressioni utente suggerite da LUIS nella pagina  **[Review endpoint utterances](luis-how-to-review-endpoint-utterances.md)** (Esamina espressioni endpoint). Le espressioni eliminate da questo elenco non vengono più suggerite, ma non vengono eliminate dai log.

Se non si vuole utterances apprendimento attivo, è possibile [disabilitare apprendimento attivo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). La disabilitazione di apprendimento attivo disabilita anche la registrazione.

### <a name="disable-logging-utterances"></a>Disabilitare la registrazione utterances
[La disabilitazione di apprendimento attivo](luis-how-to-review-endpoint-utterances.md#disable-active-learning) è disabilita la registrazione.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Eliminare un account
Se si elimina un account, vengono eliminate tutte le app insieme ai relativi log ed espressioni di esempio. I dati vengono conservati per 60 giorni prima che l'account e i dati vengano eliminati in modo permanente.

L'eliminazione dell'account può essere eseguita nella pagina **Settings** (Impostazioni). Selezionare il nome dell'account nella barra di spostamento in alto a destra per andare alla pagina **Settings** (Impostazioni).

## <a name="data-inactivity-as-an-expired-subscription"></a>Inattività dei dati come sottoscrizione scaduta
Ai fini della conservazione e dell'eliminazione dei dati, un'app LUIS inattiva potrebbe, a _discrezione di Microsoft_, essere considerata una sottoscrizione scaduta. Un'app è considerata inattiva se per gli ultimi 90 giorni soddisfa i criteri seguenti: 

* **Non** ha ricevuto chiamate.
* Non è stata modificata.
* Non è stata assegnata a essa alcuna chiave corrente.
* Nessun utente ha effettuato l'accesso a essa.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sull'esportazione e sull'eliminazione di un'app](luis-how-to-start-new-app.md)

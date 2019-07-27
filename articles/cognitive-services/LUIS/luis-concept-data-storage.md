---
title: Archiviazione dati-LUIS
titleSuffix: Azure Cognitive Services
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
ms.openlocfilehash: af0223db944f01346ddcbc1f198ac0c15a426be4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564018"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Archiviazione e rimozione dei dati nei Servizi cognitivi di LUIS (Language Understanding)
LUIS archivia i dati crittografati in un archivio dati di Azure corrispondente alla regione specificata dalla chiave. Questi dati vengono archiviati per 30 giorni. 

## <a name="export-and-delete-app"></a>Esportare ed eliminare l'app
Gli utenti hanno il controllo completo sull'[esportazione](luis-how-to-start-new-app.md#export-app) e sull'[eliminazione](luis-how-to-start-new-app.md#delete-app) dell'app. 

## <a name="utterances"></a>Espressioni

Le espressioni possono essere archiviate in due posizioni diverse. 

* Durante **il processo di creazione**, le espressioni vengono create e archiviate nello scopo. Le espressioni negli Intent sono necessarie per un'app LUIS riuscita. Dopo la pubblicazione dell'app e la ricezione di query sull'endpoint, la QueryString `log=false`della richiesta dell'endpoint determina se l'espressione dell'endpoint è archiviata. Se l'endpoint è archiviato, diventa parte delle espressioni di formazione attive disponibili nella sezione **Build** del portale, nella sezione **Review endpoint enunciations** . 
* Quando si **esaminano le espressioni dell'endpoint**e si aggiunge un enunciato a un preventivo, l'espressione non viene più archiviata come parte delle espressioni di endpoint da rivedere. Viene aggiunto agli Intent dell'app. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Elimina le espressioni di esempio da un Intent
Eliminare le espressioni di esempio usate per il training di [LUIS](luis-reference-regions.md). Le espressioni di esempio eliminate dall'app LUIS vengono rimosse dal servizio Web LUIS e non sono più disponibili per l'esportazione.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Elimina le espressioni nella revisione dall'apprendimento attivo

È possibile eliminare le espressioni dall'elenco di espressioni utente suggerite da LUIS nella pagina  **[Review endpoint utterances](luis-how-to-review-endpoint-utterances.md)** (Esamina espressioni endpoint). Le espressioni eliminate da questo elenco non vengono più suggerite, ma non vengono eliminate dai log.

Se non si desiderano espressioni di apprendimento attive, è possibile [disabilitare l'apprendimento attivo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). La disabilitazione dell'apprendimento attivo Disabilita anche la registrazione.

### <a name="disable-logging-utterances"></a>Disabilitare le espressioni di registrazione
La disabilitazione dell' [apprendimento attivo Disabilita](luis-how-to-review-endpoint-utterances.md#disable-active-learning) la registrazione.


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

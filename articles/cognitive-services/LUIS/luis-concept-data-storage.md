---
title: Archiviazione dati - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS archivia i dati crittografati in un archivio dati di Azure corrispondente alla regione specificata dalla chiave.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: e3028a9f046edb4013d8a2d6ec88d8c3cb2ca489
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220017"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Archiviazione e rimozione dei dati nei Servizi cognitivi di LUIS (Language Understanding)
LUIS archivia i dati crittografati in un archivio dati di Azure corrispondente alla regione specificata dalla chiave. Questi dati vengono archiviati per 30 giorni. 

## <a name="export-and-delete-app"></a>Esportare ed eliminare l'app
Gli utenti hanno il controllo completo sull'[esportazione](luis-how-to-start-new-app.md#export-app) e sull'[eliminazione](luis-how-to-start-new-app.md#delete-app) dell'app. 

## <a name="utterances"></a>Espressioni

Gli utterances possono essere archiviati in due luoghi diversi. 

* Durante il processo di **creazione,** le espressioni vengono create e archiviate nel file Intent. Le espressioni nelle finalità sono necessarie per un'app LUIS riuscita. Una volta pubblicata l'app e ricevuta le query nell'endpoint, la stringa di query della richiesta dell'endpoint `log=false`determina se l'espressione dell'endpoint è archiviata. Se l'endpoint è archiviato, diventa parte delle espressioni di apprendimento attivo disponibili nella sezione **Compila** del portale, nella sezione Esaminare le **espressioni degli endpoint.** 
* Quando si **esaminano le espressioni endpoint**e si aggiunge un'espressione a una finalità, l'espressione non viene più archiviata come parte delle espressioni endpoint da rivedere. Viene aggiunto alle finalità dell'app. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Eliminare espressioni di esempio da una finalitàDelete example utterances from an intent

Eliminare le espressioni di esempio usate per il training di [LUIS](luis-reference-regions.md). Le espressioni di esempio eliminate dall'app LUIS vengono rimosse dal servizio Web LUIS e non sono più disponibili per l'esportazione.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Eliminare le espressioni nella revisione dall'apprendimento attivoDelete utterances in review from active learning

È possibile eliminare le espressioni dall'elenco di espressioni utente suggerite da LUIS nella pagina **[Review endpoint utterances](luis-how-to-review-endpoint-utterances.md)** (Esamina espressioni endpoint). Le espressioni eliminate da questo elenco non vengono più suggerite, ma non vengono eliminate dai log.

Se non si desidera espressioni di apprendimento attive, è possibile [disabilitare l'apprendimento attivo.](luis-how-to-review-endpoint-utterances.md#disable-active-learning) La disabilitazione dell'apprendimento attivo disabilita anche la registrazione.

### <a name="disable-logging-utterances"></a>Disabilitare la registrazione delle espressioniDisable logging utterances
[La disabilitazione dell'apprendimento attivo](luis-how-to-review-endpoint-utterances.md#disable-active-learning) disabilita la registrazione.


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

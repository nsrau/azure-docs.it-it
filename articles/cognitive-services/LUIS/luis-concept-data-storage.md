---
title: Informazioni sull'archiviazione dei dati in LUIS - Azure | Microsoft Docs
description: Informazioni sull'archiviazione dei dati nel servizio LUIS (Language Understanding)
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/08/2018
ms.author: v-geberr
ms.openlocfilehash: f235c787e7d2064696e5421219a297d914b5882d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266006"
---
# <a name="data-storage-and-removal"></a>Archiviazione e rimozione dei dati
LUIS archivia i dati crittografati in un archivio dati di Azure corrispondente alla regione specificata dalla chiave. Questi dati vengono archiviati per 30 giorni. 

## <a name="export-and-delete-app"></a>Esportare ed eliminare l'app
Gli utenti hanno il controllo completo sull'[esportazione](create-new-app.md#export-app) e sull'[eliminazione](create-new-app.md#delete-app) dell'app. 

## <a name="utterances-in-an-intent"></a>Espressioni in una finalità
Eliminare le espressioni di esempio usate per il training di [LUIS][LUIS]. Le espressioni di esempio eliminate dall'app LUIS vengono rimosse dal servizio Web LUIS e non sono più disponibili per l'esportazione.

## <a name="utterances-in-review"></a>Espressioni in revisione
È possibile eliminare le espressioni dall'elenco di espressioni utente suggerite da LUIS nella pagina  **[Review endpoint utterances](label-suggested-utterances.md)** (Esamina espressioni endpoint). Le espressioni eliminate da questo elenco non vengono più suggerite, ma non vengono eliminate dai log.

## <a name="accounts"></a>Account
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
> [Informazioni sull'esportazione e sull'eliminazione di un'app](create-new-app.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
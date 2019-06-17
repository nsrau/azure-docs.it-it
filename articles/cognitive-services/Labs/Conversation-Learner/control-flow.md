---
title: Flusso di controllo di Conversation Learner - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informazioni sul flusso di controllo di Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 22a2a3472a54188f9298c580a95d53ac681822aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66385424"
---
## <a name="control-flow"></a>Flusso di controllo

Questo documento descrive il flusso di controllo di Conversation Learner, illustrato nel diagramma seguente.

![](media/controlflow.PNG)

1. L'utente immette un termine o una frase nel bot, ad esempio "what's the weather in Seattle?".
1. Conversation Learner passa l'input utente a un modello di apprendimento automatico che estrae le entità.
   - Questo modello viene creato da Conversation Learner ed è ospitato da www.luis.ai
1. Tutte le entità estratte e il testo di input dell'utente vengono passati al metodo di callback di rilevamento entità nel codice del bot.
    - Questo codice può impostare/eliminare/modificare i valori delle entità
1. La rete neurale di Conversation Learner riceve quindi l'output dell'estrazione di entità e l'input utente e assegna un punteggio a tutte le azioni definite nel bot.
   - In questo esempio, l'azione con la probabilità più elevata è la comunicazione delle previsioni meteo:

     ![](media/controlflow_forecast.PNG)

1. L'azione selezionata richiede in questo caso una chiamata API per recuperare le previsioni meteo. 
1. Viene quindi richiamata questa API, che è stata registrata con il metodo CL.AddCallback.  Il risultato dell'API viene quindi restituito all'utente sotto forma di messaggio, ad esempio "Sunny with a high of 67".
1. Viene quindi effettuata la chiamata alla rete neurale per determinare l'azione successiva in base al passaggio precedente.
1. La rete neurale prevede quindi il successivo set di azioni possibili e all'utente viene presentata l'azione selezionata, in questo caso "Anything else?".

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Come insegnare con strumento di apprendimento di conversazioni](./how-to-teach-cl.md)

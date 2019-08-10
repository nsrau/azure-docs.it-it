---
title: Riferimento a dominio predefinito-LUIS
titleSuffix: Azure Cognitive Services
description: Informazioni di riferimento sui domini predefiniti che sono raccolte predefinite di finalità ed entità di Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 6e701396191ec8bf66ece0e2858b9f32857e7b17
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933596"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Informazioni di riferimento sui domini predefiniti per l'app LUIS
Queste informazioni di riferimento descrivono i [domini predefiniti](luis-how-to-use-prebuilt-domains.md) che sono raccolte predefinite delle finalità ed delle entità offerte da LUIS.

I [domini personalizzati](luis-how-to-start-new-app.md), al contrario, vengono avviati senza finalità e modelli. È possibile aggiungere le finalità e le entità dei domini predefiniti a un modello personalizzato.

## <a name="supported-domains-across-cultures"></a>Domini supportati tra le diverse impostazioni cultura

Le uniche impostazioni cultura supportate sono la lingua inglese. 

<!--

The table below summarizes the currently supported domains. Support for English is usually more complete than others.


| Entity Type       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| Calendar    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -      | -    | -    | -     | -  |
| Communication   | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Email           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| HomeAutomation           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Notes      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Places    | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| RestaurantReservation   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo_IPA        | ✓    | ✓       | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Utilities          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Weather        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Web    | ✓    | -        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
||||||||||||| 

-->

<br><br>

|Tipo di entità|description|
|--|--|
|Calendar|Il calendario riguarda riunioni e appuntamenti personali, _non_ eventi pubblici, ad esempio le pianificazioni della Coppa del mondo, i calendari degli eventi di Seattle, oppure calendari generici (ad esempio, il giorno attuale, che cosa inizia, quando è il giorno del lavoro).|
|Comunicazioni|Richieste di effettuare chiamate, inviare testi o messaggi istantanei, trovare e aggiungere contatti e diverse altre richieste correlate alla comunicazione (in genere in uscita). Il nome contatto solo le query non appartengono al dominio di comunicazione.|
|Email|Il messaggio di posta elettronica è un sottodominio del dominio di comunicazione. Contiene principalmente le richieste di invio e ricezione di messaggi tramite posta elettronica.|
|HomeAutomation|Il dominio HomeAutomation fornisce Intent ed entità correlati al controllo dei dispositivi Smart Home. Supporta principalmente il comando di controllo relativo a luci e condizionatori d'aria, ma presenta alcune funzionalità di generalizzazione per altri dispositivi elettrici.|
|Note|Nota Domain fornisce Intent ed entità per la creazione di note e la scrittura di elementi per gli utenti.|
|Località|I luoghi includono aziende, istituzioni, ristoranti, spazi pubblici e indirizzi. Il dominio supporta la ricerca e l'individuazione delle informazioni di un luogo pubblico, ad esempio località, ore operative e distanza.|
|RestaurantReservation|Il dominio di prenotazione del ristorante supporta gli Intent per la gestione delle prenotazioni per i ristoranti.|
|ToDo|ToDo Domain fornisce i tipi di elenchi di attività che consentono agli utenti di aggiungere, contrassegnare ed eliminare gli elementi todo.|
|Servizi pubblici|Utilities Domain è un dominio generale tra tutti i modelli predefiniti di LUIS, che contiene finalità e espressioni comuni negli scenari di differenza.|
|Weather|Il dominio meteorologico è incentrato sul controllo della condizione meteorologica e degli avvisi con località e ora o verifica del tempo in base alle condizioni meteorologiche.|
|Web|Il dominio web fornisce le finalità e le entità per la ricerca di un sito Web.|

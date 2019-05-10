---
title: Informazioni di riferimento sui domini predefiniti
titleSuffix: Azure
description: Informazioni di riferimento sui domini predefiniti che sono raccolte predefinite di finalità ed entità di Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 3265477108b7e74d65050408add6c5d5c94b4852
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233883"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Informazioni di riferimento sui domini predefiniti per l'app LUIS
Queste informazioni di riferimento descrivono i [domini predefiniti](luis-how-to-use-prebuilt-domains.md) che sono raccolte predefinite delle finalità ed delle entità offerte da LUIS.

I [domini personalizzati](luis-how-to-start-new-app.md), al contrario, vengono avviati senza finalità e modelli. È possibile aggiungere le finalità e le entità dei domini predefiniti a un modello personalizzato.

# <a name="supported-domains-across-cultures"></a>Domini supportati nelle impostazioni cultura

L'unica lingua supportata è impostata sull'inglese. 

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
|Calendario|Calendario è alcuna operazione sulle riunioni personale e gli appuntamenti _non_ eventi pubblici (come mondo FIFA pianificazioni, calendari Seattle o calendari generici (ad esempio quale giorno è oggi, ciò che rientrano avviata, quando è Labor Day).|
|Comunicazioni|Le richieste per effettuare chiamate, inviare messaggi o messaggi immediati, trovare e aggiungere contatti e varie altre richieste alle comunicazioni (a livello generale in uscita). Nome del contatto solo le query non appartengono al dominio di comunicazione.|
|Posta|Messaggio di posta elettronica è un sottodominio del dominio di comunicazione. Contiene principalmente le richieste per inviare e ricevere messaggi tramite messaggi di posta elettronica.|
|HomeAutomation|Il dominio HomeAutomation fornisce finalità ed entità correlate al controllo del smart device home. Principalmente supporta il comando di controllo correlato alla luce puntiforme e condizionatore d'aria, ma presenta alcune funzionalità di generalizzazione per altre Appliance electric.|
|Note|Dominio nota fornisce finalità ed entità per la creazione di note e annotare gli elementi per gli utenti.|
|Località|Posizioni includono le aziende, gli istituti, i ristoranti, spazi pubblici e indirizzi. Il dominio supporta la posizione di ricerca e che richiede le informazioni di un luogo pubblico, ad esempio posizione, ore e distanza operativi.|
|RestaurantReservation|Dominio di prenotazione ristorante supporta gli Intent per la gestione delle prenotazioni per ristoranti.|
|ToDo|Dominio ToDo fornisce tipi di elenchi di attività per gli utenti aggiungere, contrassegnare ed eliminare i propri elementi todo.|
|Servizi pubblici|Dominio di utilità è generale tra LUIS tutti i modelli predefiniti che contiene gli Intent ed espressioni in scenari di differenza comuni.|
|Weather|Dominio meteo è incentrato sulla verifica condizioni meteorologiche e gli avvisi con posizione e l'ora o il controllo di tempo per le condizioni meteo.|
|Web|Il dominio Web fornisce la finalità e le entità per la ricerca di un sito Web.|

---
title: Supporto delle lingue - QnA Maker
titleSuffix: Azure Cognitive Services
description: La lingua di una knowledge base influisce sulla capacità di QnA Maker di estrarre automaticamente domande e risposte dalle origini, nonché la pertinenza dei risultati forniti da QnA Maker in risposta alle query degli utenti. Elenco delle impostazioni cultura e dei linguaggi naturali supportati da QnA Maker per la knowledge base. Non combinare linguaggi diversi nella stessa knowledge base.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: bec09e5cd053774902083c6dd042c1ed4b293d20
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066673"
---
# <a name="language-support-for-qna-maker"></a>Supporto delle lingue per QnA Maker

La lingua di una knowledge base influisce sulla capacità di QnA Maker di estrarre automaticamente domande e risposte dalle [origini](../Concepts/data-sources-supported.md), nonché la pertinenza dei risultati forniti da QnA Maker in risposta alle query degli utenti.

## <a name="auto-extraction"></a>Estrazione automatica
QnA Maker supporta l'estrazione di domanda/risposta in qualsiasi pagina della lingua, ma l'efficacia dell'estrazione è molto più elevata per le lingue seguenti in quanto QnA Maker usa parole chiave per identificare le domande.

|Lingue supportate| Impostazioni locali|
|-----|----|
|Inglese|en-*|
|Francese|fr-*|
|Italiano|it-*|
|Tedesco|de-*|
|Spagnolo|es-*|

## <a name="primary-language-detection"></a>Rilevamento della lingua principale

La lingua primaria utilizzata per il rilevamento è impostata per la risorsa QnA Maker e per tutte le Knowledge base create sulla risorsa, quando il primo documento o URL viene aggiunto alla prima Knowledge base. Il linguaggio non può essere modificato. 

Se l'utente prevede di supportare più lingue, è necessario che disponga di una nuova risorsa QnA Maker per ciascuna lingua. Informazioni su come [creare una Knowledge base QnA Maker basata sul linguaggio](../how-to/language-knowledge-base.md).  

Verificare la lingua principale attenendosi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).  
1. Cercare e selezionare la risorsa di ricerca di Azure creata come parte della risorsa QnA Maker. Il nome della risorsa di ricerca di Azure inizierà con lo stesso nome della risorsa QnA Maker e avrà il **servizio di ricerca**dei tipi. Si tenga presente che è possibile collegare solo una risorsa QnA Maker a una risorsa di ricerca di Azure.
1. Nella pagina **Panoramica** della risorsa Cerca selezionare **indici**. 
1. Selezionare l'indice **testkb**.
1. Selezionare la scheda **campi** . 
1. Visualizzare la colonna **analizzatore** per le **domande** e i campi di **risposta** . 


## <a name="query-matching-and-relevance"></a>Query corrispondente e pertinenza
QnA Maker dipende dagli [analizzatori del linguaggio](https://docs.microsoft.com/rest/api/searchservice/language-support) in ricerca di Azure per fornire i risultati. Sono disponibili funzionalità speciali di riclassificazione per lingua En-* che consentono una migliore pertinenza.

Le funzionalità di Ricerca di Azure sono all'altezza per le lingue supportate, ma QnA Maker ha un'utilità di classificazione aggiuntiva superiore ai risultati delle ricerche di Azure. In questo modello di classificazione vengono usate alcune funzionalità di semantica e basate su parole specifiche in en-*, che non sono ancora disponibili per altri linguaggi. Queste funzionalità non sono disponibili perché fanno parte del lavoro interno del rango del QnA Maker. 

QnA Maker [rileva automaticamente il linguaggio della Knowledge base durante la](#primary-language-detection) creazione e imposta l'analizzatore di conseguenza. È possibile creare knowledge base nelle lingue seguenti. 

|Lingue supportate|
|-----|
|Arabo|
|Armeno|
Bengali|
|Basco|
|Bulgaro|
|Catalano|
|Cinese semplificato|
|Cinese tradizionale|
|Croato|
|Ceco|
|Danese|
|Olandese|
|Inglese|
|Estone|
|Finlandese|
|Francese|
|Galiziano|
|Tedesco|
|Greco|
|Gujarati|
|Ebraico|
|Hindi|
|Ungherese|
|Islandese|
|Indonesiano|
|Irlandese|
|Italiano|
|Giapponese|
|Kannada|
|Coreano|
|Lettone|
|Lituano|
|Malayalam|
|Malese|
|Norvegese|
|Polacco|
|Portoghese|
|Punjabi|
|Rumeno|
|Russo|
|Serbo (alfabeto cirillico)|
|Serbo (alfabeto latino)|
|Slovacco|
|Sloveno|
|Spagnolo|
|Svedese|
|Tamil|
|Telugu|
|Thai|
|Turco|
|Ucraino|
|Urdu|
|Vietnamita|

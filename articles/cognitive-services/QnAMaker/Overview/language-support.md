---
title: Supporto delle lingue - QnA Maker
titleSuffix: Azure Cognitive Services
description: La lingua di una knowledge base influisce sulla capacità di QnA Maker di estrarre automaticamente domande e risposte dalle origini, nonché la pertinenza dei risultati forniti da QnA Maker in risposta alle query degli utenti. Elenco delle impostazioni cultura e dei linguaggi naturali supportati da QnA Maker per la knowledge base. Non combinare linguaggi diversi nella stessa knowledge base.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 820a9ae0df91fba8cf00764428867bec6196841a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58335862"
---
# <a name="language-support-for-qna-maker"></a>Supporto del linguaggio per QnA Maker

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

## <a name="primary-language-detection"></a>Rilevamento della lingua primario

La lingua principale usata per il rilevamento è impostata per la risorsa di QnA Maker e tutte le knowledge base create su tale risorsa, quando il primo documento o l'URL viene aggiunto alla prima knowledge base. Non è possibile modificare la lingua. 

Se si prevede di supportare più lingue, devono disporre di una nuova risorsa di QnA Maker per ciascuna lingua. Informazioni su come [creare una knowledge base QnA Maker di basata sul linguaggio](/how-to/language-knowledge-base.md).  

Verificare che la lingua principale con i passaggi seguenti:

1. Accedere al [portale di Azure](http://portal.azure.com).  
1. Cercare e selezionare la risorsa di ricerca di Azure creata come parte della risorsa di QnA Maker. Il nome di risorsa di ricerca di Azure inizierà con lo stesso nome della risorsa di QnA Maker e sarà di tipo **servizio di ricerca**. 
1. Dal **Overview** pagina della risorsa di ricerca, seleziona **indici**. 
1. Selezionare l'indice **testkb**.
1. Selezionare il **campi** scheda. 
1. Visualizza i **analizzatore** colonna per il **domande** e **risposta** campi. 


## <a name="query-matching-and-relevance"></a>Query corrispondente e pertinenza
QnA Maker dipende dagli [analizzatori del linguaggio](https://docs.microsoft.com/rest/api/searchservice/language-support) in ricerca di Azure per fornire i risultati. Sono disponibili funzionalità speciali di riclassificazione per lingua En-* che consentono una migliore pertinenza.

Le funzionalità di Ricerca di Azure sono all'altezza per le lingue supportate, ma QnA Maker ha un'utilità di classificazione aggiuntiva superiore ai risultati delle ricerche di Azure. In questo modello permettendoti, utilizziamo alcune caratteristiche speciali di semantiche e basato sulle frasi in en-*, che non sono ancora disponibili per altri linguaggi. Microsoft non rendere disponibili tali caratteristiche, come fanno parte dell'attività interna della permettendoti di QnA Maker. 

QnA Maker [rileva automaticamente la lingua della knowledge base](#primary-language-detection) durante la creazione e imposta di conseguenza l'analizzatore. È possibile creare knowledge base nelle lingue seguenti. 

|Lingue supportate|
|-----|
|Arabo|
|Armeno|
Bengalese|
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

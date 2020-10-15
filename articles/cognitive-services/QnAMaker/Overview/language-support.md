---
title: Supporto delle lingue - QnA Maker
titleSuffix: Azure Cognitive Services
description: Elenco delle impostazioni cultura e dei linguaggi naturali supportati da QnA Maker per la knowledge base. Non combinare linguaggi diversi nella stessa knowledge base.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 09/24/2019
ms.openlocfilehash: c990b6980dea871679b0b301e293e4fb94748db7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89650892"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>Supporto delle lingue per una risorsa QnA Maker e Knowledge base

La lingua per il servizio viene selezionata quando si crea la prima Knowledge base nella risorsa. Tutte le Knowledge base aggiuntive nella risorsa devono essere nella stessa lingua. 

La lingua determina la pertinenza dei risultati QnA Maker fornisce in risposta alle query degli utenti. La risorsa QnA Maker e tutte le Knowledge base all'interno di tale risorsa supportano una sola lingua. Il linguaggio singolo è necessario per fornire i risultati ottimali per la risposta a una query.

## <a name="single-language-per-resource"></a>Singola lingua per risorsa

Considerare quanto segue:

* Un servizio QnA Maker e tutte le relative Knowledge base supportano solo una lingua.
* La lingua viene impostata in modo esplicito al momento della creazione della prima Knowledge base del servizio
* Il linguaggio è determinato dai file e dagli URL aggiunti quando viene creata la Knowledge base
* Non è possibile modificare la lingua per altre Knowledge base nel servizio
* Il linguaggio viene usato dal servizio ricerca cognitiva (Ranker #1) e dal servizio QnA Maker (Ranker #2) per generare la risposta migliore a una query

## <a name="supporting-multiple-languages"></a>Supporto di più lingue

Se è necessario supportare un sistema Knowledge base, che include diverse lingue, è possibile:

* Utilizzare il [servizio di conversione](../../translator/translator-info-overview.md) per tradurre una domanda in una sola lingua prima di inviare la domanda alla Knowledge base. In questo modo è possibile concentrarsi sulla qualità di una singola lingua e sulla qualità delle domande e risposte alternative.
* Creare una risorsa di QnA Maker e una Knowledge base all'interno di tale risorsa per ogni lingua. In questo modo è possibile gestire le domande alternative separate e il testo di risposta più sfumato per ogni lingua. In questo modo si ottiene una maggiore flessibilità, ma è necessario un costo di manutenzione molto più elevato quando le domande o le risposte cambiano in tutte le lingue.

Esaminare le [lingue supportate](../overview/language-support.md) per QnA Maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Supportare ogni lingua con una risorsa QnA Maker

* Creare una risorsa QnA Maker per ogni lingua
* Aggiungi solo file e URL per tale lingua
* Usare una convenzione di denominazione per la risorsa per identificare la lingua. Un esempio è `qna-maker-fr` relativo a tutte le Knowledge base per i documenti in francese


## <a name="languages-supported"></a>Lingue supportate

L'elenco seguente contiene le lingue supportate per una risorsa QnA Maker. 

|Linguaggio|
|--|
|Arabo|
|Armeno|
|Bengalese|
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
|Romeno|
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

## <a name="query-matching-and-relevance"></a>Query corrispondente e pertinenza
QnA Maker dipende dagli [analizzatori del linguaggio ricerca cognitiva di Azure](https://docs.microsoft.com/rest/api/searchservice/language-support) per fornire i risultati.

Sebbene le funzionalità di ricerca cognitiva di Azure siano in par per le lingue supportate, QnA Maker dispone di un rango aggiuntivo che si trova sopra i risultati di ricerca di Azure. In questo modello di classificazione vengono usate alcune funzionalità speciali basate su parole e semantiche nei linguaggi seguenti.

|Lingue con rango aggiuntivo|
|--|
|Cinese|
|Ceco|
|Olandese|
|Inglese|
|Francese|
|Tedesco|
|Ungherese|
|Italiano|
|Giapponese|
|Coreano|
|Polacco|
|Portoghese|
|Spagnolo|
|Svedese|

Questo rango aggiuntivo è un lavoro interno del rango del QnA Maker.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Selezione della lingua](../how-to/language-knowledge-base.md)

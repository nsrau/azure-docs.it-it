---
title: Supporto delle lingue - QnA Maker
titleSuffix: Azure Cognitive Services
description: Elenco delle lingue supportate da QnA Maker.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/09/2018
ms.author: tulasim
ms.openlocfilehash: 8c47c4a59f03328b1dc8d3df7771bac81864bb34
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566636"
---
# <a name="language-and-region-support-for-qna-maker"></a>Supporto di lingua e area geografica per QnA Maker

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

## <a name="query-matching-and-relevance"></a>Query corrispondente e pertinenza
QnA Maker dipende dagli [analizzatori del linguaggio](https://docs.microsoft.com/rest/api/searchservice/language-support) in ricerca di Azure per fornire i risultati. Sono disponibili funzionalità speciali di riclassificazione per lingua En-* che consentono una migliore pertinenza.

Le funzionalità di Ricerca di Azure sono all'altezza per le lingue supportate, ma QnA Maker ha un'utilità di classificazione aggiuntiva superiore ai risultati delle ricerche di Azure. In questo modello di utilità di classificazione, per le lingue en-* vengono usate una semantica speciale e funzionalità basate sulle parole non ancora disponibili per le altre lingue. Queste non vengono rese disponibili perché fanno parte del funzionamento interno dell'utilità di classificazione. 

QnA Maker rileva automaticamente la lingua della knowledge base durante la creazione e imposta l'analizzatore di conseguenza. È possibile creare knowledge base nelle lingue seguenti. Leggere [qui](../How-To/language-knowledge-base.md) per altri dettagli sulla modalità di gestione delle lingue di QnA Maker.


> [!Tip]
> Gli analizzatori del linguaggio, una volta impostati, non possono essere modificati. Inoltre, l'analizzatore del linguaggio si applica a tutte le knowledge base in un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Se si intende disporre di knowledge base in lingue diverse, è necessario crearli in servizi QnA Maker separati.

|Lingue supportate|
|-----|
|Arabo|
|Armeno|,
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

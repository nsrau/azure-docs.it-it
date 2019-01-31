---
title: Supporto delle lingue - QnA Maker
titleSuffix: Azure Cognitive Services
description: Elenco delle impostazioni cultura e dei linguaggi naturali supportati da QnA Maker per la knowledge base. Non combinare linguaggi diversi nella stessa knowledge base.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 11/09/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 9d173fa6786778584abed30be0743559e691bde2
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221947"
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

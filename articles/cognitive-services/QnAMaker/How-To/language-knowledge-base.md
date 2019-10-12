---
title: Concetti relativi al linguaggio-QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker supporta i contenuti della Knowledge Base in molte lingue. Ogni servizio QnA Maker deve essere tuttavia riservato a una sola lingua. La prima Knowledge base creata, destinata a un particolare servizio di QnA Maker, imposta la lingua di tale servizio.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: 849c919950c57a1df3b0fb76021de6e10254c7b4
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286398"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Supporto per le lingue dei contenuti della Knowledge Base per QnA Maker

La lingua per il servizio viene selezionata quando si crea la prima Knowledge base nella risorsa. Tutte le Knowledge base aggiuntive nella risorsa devono essere nella stessa lingua. 

La lingua determina la pertinenza dei risultati QnA Maker fornisce in risposta alle query degli utenti.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>Una lingua per tutte le Knowledge base nella risorsa

QnA Maker consente di selezionare la lingua per il servizio QnA, durante la creazione della prima Knowledge base. Per tutte le Knowledge base in una risorsa QnA Maker, tutte devono trovarsi nella stessa lingua. Questa lingua non può essere modificata.

La creazione di Knowledge base in linguaggi diversi in una risorsa influisce negativamente sulla pertinenza dei risultati QnA Maker fornisce in risposta alle query degli utenti.

Esaminare un elenco di [lingue supportate](../overview/language-support.md#languages-supported) e il modo in cui i linguaggi incidono sulla [corrispondenza e sulla pertinenza](#query-matching-and-relevance). 

## <a name="select-language-when-creating-first-knowledge-base"></a>Selezionare la lingua durante la creazione della prima Knowledge base

La selezione della lingua fa parte dei passaggi per creare la prima Knowledge base in una risorsa. 

![Screenshot del portale di QnA Maker della selezione della lingua per la prima Knowledge base](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>Query corrispondente e pertinenza
QnA Maker dipende dagli [analizzatori del linguaggio di ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/language-support) per fornire i risultati. 

Le funzionalità di Ricerca di Azure sono all'altezza per le lingue supportate, ma QnA Maker ha un'utilità di classificazione aggiuntiva superiore ai risultati delle ricerche di Azure. In questo modello di classificazione vengono usate alcune funzionalità speciali basate su parole e semantiche nei linguaggi seguenti. 

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

## <a name="verify-language"></a>Verifica lingua

È possibile verificare la lingua della risorsa QnA Maker dalla pagina Impostazioni servizio nella QnA Maker.

![Screenshot del portale di QnA Maker della pagina delle impostazioni del servizio](../media/language-support/language-knowledge-base.png) 


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un bot QnA con il servizio Azure Bot](../Tutorials/create-qna-bot.md)

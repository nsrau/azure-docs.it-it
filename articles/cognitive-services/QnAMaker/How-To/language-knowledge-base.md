---
title: Concetti linguistici - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker supporta i contenuti della Knowledge Base in molte lingue. Ogni servizio QnA Maker deve essere tuttavia riservato a una sola lingua. La prima Knowledge Base creata, destinata a un particolare servizio QnA Maker, imposta la lingua di tale servizio.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: 38701e8bbef1c5d78eca2242105e81fe7261c0f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220633"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Supporto per le lingue dei contenuti della Knowledge Base per QnA Maker

La lingua per il servizio viene selezionata quando si crea la prima Knowledge Base nella risorsa. Tutte le basi di conoscenza aggiuntive nella risorsa devono essere nella stessa lingua.

La lingua determina la pertinenza dei risultati forniti da QnA Maker in risposta alle query degli utenti.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>Una lingua per tutte le knowledge base nelle risorse

QnA Maker consente di selezionare la lingua per il servizio QnA, creando la prima Knowledge Base. Per tutte le knowledge base in una risorsa QnA Maker, devono essere tutte nella stessa lingua. Questa lingua non può essere modificata.

La creazione di knowledge base in lingue diverse in una risorsa influisce negativamente sulla pertinenza dei risultati forniti da QnA Maker in risposta alle query degli utenti.

Esaminare un elenco delle [lingue supportate](../overview/language-support.md#languages-supported) e l'impatto delle lingue [in corrispondenza e pertinenza](#query-matching-and-relevance).

## <a name="select-language-when-creating-first-knowledge-base"></a>Selezionare la lingua durante la creazione della prima Knowledge Base

La selezione della lingua fa parte dei passaggi per creare la prima Knowledge Base in una risorsa.

![Schermata del portale QnA Maker della selezione della lingua per la prima knowledge base](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>Query corrispondente e pertinenza
QnA Maker dipende dagli [analizzatori del linguaggio di Ricerca cognitiva](https://docs.microsoft.com/rest/api/searchservice/language-support) di Azure per fornire risultati.

Mentre le funzionalità di Ricerca cognitiva di Azure sono alla pari per le lingue supportate, QnA Maker ha un controllo di classificazione aggiuntivo che si trova al di sopra dei risultati della ricerca di Azure.While the Azure Cognitive Search capabilities are on par for supported languages, QnA Maker has an additional ranker that sits above the Azure search results. In questo modello più classificato, utilizziamo alcune funzionalità semantiche e basate su parole speciali nei linguaggi seguenti.

|Lingue con classificazione aggiuntiva|
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

Questa classifica aggiuntiva è un lavoro interno del ranker del QnA Maker.

## <a name="verify-language"></a>Verificare la lingua

È possibile verificare la lingua della risorsa QnA Maker dalla pagina delle impostazioni del servizio in QnA Maker.

![Screenshot del portale QnA Maker della pagina Impostazioni servizio](../media/language-support/language-knowledge-base.png)


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire la migrazione di una knowledge base](../Tutorials/migrate-knowledge-base.md)

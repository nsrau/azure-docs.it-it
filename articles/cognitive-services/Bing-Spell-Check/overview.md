---
title: Informazioni sull'API Controllo ortografico Bing
titlesuffix: Azure Cognitive Services
description: Informazioni sull'API Controllo ortografico Bing, che usa le funzionalità di apprendimento automatico e di traduzione automatica statistica per il controllo ortografico contestuale.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 22f75efb3cb4baa645030e7ad64072674de662ed
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890810"
---
# <a name="what-is-the-bing-spell-check-api"></a>Informazioni sull'API Controllo ortografico Bing

L'API Controllo ortografico Bing consente di eseguire il controllo grammaticale e ortografico contestuale. Mentre la maggior parte dei correttori ortografici usa set di regole basate sul dizionario, il controllo ortografico Bing usa l'apprendimento automatico e la traduzione automatica statistica per fornire correzioni accurate e contestuali. 

## <a name="features"></a>Funzionalità


|  |  |
|---------|---------|
|Modalità multiple di controllo ortografico     | Le modalità multiple di controllo ortografico permettono di ottenere correzioni incentrate sulla grammatica e/o sull'ortografia. |
|Riconoscimento di espressioni gergali e linguaggio informale     | Vengono riconosciuti le espressioni comuni e i termini informali usati nel testo.         |
|Distinzione tra parole simili     | Viene individuato l'utilizzo corretto di parole con suono simile ma significato diverso, ad esempio "see" e "sea" per l'inglese.        |
|Supporto di marchi, titoli ed espressioni di uso comune     | Vengono riconosciuti i nuovi marchi, i titoli e altre espressioni popolari non appena si presentano. |

## <a name="workflow"></a>Flusso di lavoro

L'API Controllo ortografico Bing può essere chiamata facilmente da qualsiasi linguaggio di programmazione in grado di effettuare richieste HTTP e analizzare risposte JSON. Il servizio è accessibile tramite API REST o SDK di Controllo ortografico Bing. 

1. Creare un [account API Servizi cognitivi](../cognitive-services-apis-create-account.md) con accesso alle API di ricerca Bing. Se non si ha una sottoscrizione di Azure, è possibile creare un account gratuito. 
2. Inviare una richiesta all'API Ricerca Web Bing.
3. Analizzare la risposta JSON.

## <a name="next-steps"></a>Passaggi successivi

Provare prima di tutto la [demo interattiva](https://azure.microsoft.com/services/cognitive-services/spell-check/) dell'API Controllo ortografico Bing per scoprire come è possibile controllare rapidamente una vasta gamma di testi.

Quando si è pronti per chiamare l'API, creare un [account API servizi cognitivi](../../cognitive-services/cognitive-services-apis-create-account.md). Se non si ha una sottoscrizione di Azure, è possibile [creare un account](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuito.

---
title: Panoramica di Web Language Model API
titleSuffix: Azure Cognitive Services
description: Web Language Model API in Servizi cognitivi Microsoft fornisce strumenti d'avanguardia per l'elaborazione di un linguaggio naturale.
services: cognitive-services
author: piyushbehre
manager: cgronlun
ms.service: cognitive-services
ms.component: web-language-model
ms.topic: overview
ms.date: 08/12/2016
ms.author: pibehre
ROBOTS: NOINDEX
ms.openlocfilehash: 3c7f9e4cc4cf023aa93bd80ad1165ba9595e9034
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807438"
---
# <a name="what-is-the-web-language-model-api-preview"></a>Informazioni su Web Language Model API (Anteprima)

> [!IMPORTANT]
> L'anteprima di Web Language Model è stata ritirata il 9 agosto 2018. Per l'elaborazione e l'analisi del testo è consigliabile usare i [moduli di analisi del testo di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics).

L'API Web Language Model di Microsoft consiste in un servizio cloud basato su REST che fornisce strumenti d'avanguardia per l'elaborazione di un linguaggio naturale. Usando questa API, l'applicazione dell'utente può sfruttare la potenza dei big data tramite modelli di linguaggio sottoposto a training basandosi su corpora con scalabilità a livello del web raccolti da Bing nel mercato en-US.

Il training di questi modelli del linguaggio con N-grammi semplificati, che supportano catene con ordine Markov fino a 5, viene eseguito sui corpora seguenti:

- Testo del corpo di pagine Web
- Testo del titolo di pagine Web
- Testo dell'ancoraggio di pagine Web
- Testo delle query di ricerca Web

Il Web Language Model API supporta quattro operazioni di ricerca:

1. Probabilità congiunta (log10) di una sequenza di parole.
2. Probabilità condizionale (log10) di una parola con una sequenza specifica di parole precedenti.
3. Un elenco di parole (completamenti) che hanno buona probabilità di seguire una sequenza specifica.
4. Separazione di string di parole prive di spazi.

## <a name="getting-started"></a>Introduzione

1. Sottoscrizione al servizio.
2. Scaricare l'[SDK](https://www.github.com/microsoft/cognitive-weblm-windows).
3. Eseguire il codice di esempio SDK.
4. Consultare il [riferimento API](http://web.archive.org/web/20170503191852/westus.dev.cognitive.microsoft.com/docs/services/55de9ca4e597ed1fd4e2f104/operations/55de9ca4e597ed19b0de8a51) per i dettagli completi degli endpoint, inclusi i frammenti di codice in un'ampia gamma di lingue.

## <a name="underlying-technology"></a>Tecnologia sottostante

Il documento seguente fornisce informazioni dettagliate sullo sviluppo di questi modelli di lingua e dovrebbe essere citato nelle pubblicazioni di ricerca che usano questo servizio:

- [Panoramica del corpus e delle applicazioni del servizio Microsoft Web N-gram](https://research.microsoft.com/apps/pubs/default.aspx?id=130762), NAACL HLT 2010

Fare clic [qui](https://academic.microsoft.com/#/search?iq=And%28Ty%3D'0'%2CRId%3D2145833060%29&q=papers%20citing%20an%20overview%20of%20microsoft%20web%20n%20gram%20corpus%20and%20applications&filters=&from=0&sort=0) per un elenco attuale dei documenti che citano questo lavoro.

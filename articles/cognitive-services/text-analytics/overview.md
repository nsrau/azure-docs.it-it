---
title: Text mining e analisi del testo con l'API Analisi del testo - Servizi cognitivi di Azure
titleSuffix: Azure Cognitive Services
description: Informazioni sul text mining con l'API Analisi del testo. Usare questa funzionalità per l'analisi del sentiment, il rilevamento della lingua e altre forme di elaborazione del linguaggio naturale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 09/09/2020
ms.author: aahi
keywords: text mining, analisi del sentiment, analisi del testo
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 544de4adb1891c3d558a524466a076daefb42aa4
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89647462"
---
# <a name="what-is-the-text-analytics-api"></a>Informazioni sull'API Analisi del testo

L'API Analisi del testo è un servizio basato su cloud che offre funzionalità di elaborazione del linguaggio naturale per text mining e analisi del testo e include analisi del sentiment, opinion mining, estrazione di frasi chiave, rilevamento della lingua e riconoscimento dell'entità denominata.

L'API fa parte di [Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/), una raccolta di algoritmi di Machine Learning e intelligenza artificiale disponibili nel cloud per i progetti di sviluppo. È possibile usare queste funzionalità con l'[API REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/) o la [libreria client](quickstarts/text-analytics-sdk.md).

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

## <a name="sentiment-analysis"></a>Analisi del sentiment

Usare l'[analisi del sentiment](how-tos/text-analytics-how-to-sentiment-analysis.md) e conoscere l'opinione delle persone su un marchio o su un argomento eseguendo il text mining alla ricerca di indizi su sentiment positivo o negativo. Questa funzionalità dell'API restituisce un punteggio sentiment compreso tra 0 e 1 per ogni documento, dove 1 è il risultato più positivo.

A partire dalla versione 3.1 in anteprima, l'opinion mining è una funzionalità di Analisi del sentiment. Nota anche come analisi del sentiment basata su aspetto nell'elaborazione del linguaggio naturale (NLP), questa funzionalità fornisce informazioni più granulari sulle opinioni relative ad aspetti del testo come gli attributi di prodotti o servizi.

## <a name="key-phrase-extraction"></a>Estrazione di frasi chiave

Usare l'[estrazione di frasi chiave](how-tos/text-analytics-how-to-keyword-extraction.md) per identificare rapidamente i concetti principali del testo. Ad esempio, per il testo "Il cibo era delizioso e il personale era meraviglioso", Estrazione frasi chiave restituirà i punti rilevanti del discorso, ovvero "cibo" e "personale meraviglioso".

## <a name="language-detection"></a>Rilevamento della lingua

Questa funzionalità consente di [rilevare la lingua in cui è stato scritto il testo di input](how-tos/text-analytics-how-to-language-detection.md) e indicare un singolo codice lingua per ogni documento inviato nella richiesta in un'ampia gamma di lingue, varianti, dialetti e alcune lingue regionali/culturali. Il codice lingua è associato a un punteggio che indica il livello di attendibilità.

## <a name="named-entity-recognition"></a>Riconoscimento di entità denominate

Riconoscimento entità denominata può [identificare e classificare le entità](how-tos/text-analytics-how-to-entity-linking.md) nel testo come persone, luoghi, organizzazioni e quantità. Anche le entità note vengono riconosciute e collegate a altre informazioni sul Web.

## <a name="use-containers"></a>Usare i contenitori

[Usare i contenitori di Analisi del testo](how-tos/text-analytics-how-to-install-containers.md) come soluzione locale per il text mining e l'uso dell'API. Questi contenitori Docker consentono di estrarre frasi chiave, rilevare la lingua e analizzare il sentiment attenendosi ai dati.

## <a name="typical-workflow"></a>Flusso di lavoro tipico

Il flusso di lavoro è semplice: vengono inviati i dati da analizzare e vengono gestiti gli output nel codice. Gli analizzatori vengono utilizzati così come sono, senza bisogno di configurazioni o personalizzazioni aggiuntive.

1. [Creare una risorsa di Azure](../cognitive-services-apis-create-account.md) per Analisi del testo. Successivamente, [ottenere la chiave](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) generata automaticamente per autenticare le richieste.

2. [Formulare una richiesta](how-tos/text-analytics-how-to-call-api.md#json-schema) contenente i dati come testo non elaborato non strutturato in JSON.

3. Pubblicare la richiesta nell'endpoint stabilito durante l'iscrizione, aggiungendo la risorsa desiderata: analisi del sentiment, estrazione di frasi chiave, rilevamento della lingua o identificazione di entità denominate.

4. Trasmettere o archiviare la risposta in locale. A seconda della richiesta, i risultati sono un punteggio sentiment, una raccolta di frasi chiave estratte o un codice della lingua.

L'output viene restituito come un singolo documento JSON, con risultati per ogni documento di testo pubblicato, in base all'ID. In seguito è possibile analizzare, visualizzare o categorizzare i risultati in informazioni attuabili.

I dati non vengono archiviati nell'account. Le operazioni eseguite dall'API Analisi del testo sono senza stato. Questo significa che il testo specificato dall'utente viene elaborato e i risultati vengono restituiti immediatamente.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Analisi del testo per più livelli di esperienza di programmazione

È possibile iniziare a usare l'API Analisi del testo nei processi anche se non si ha molta esperienza di programmazione. Usare queste esercitazioni per informazioni su come usare l'API per analizzare il testo in modi diversi in base al proprio livello di esperienza. 

* Programmazione minima richiesta:
    * [Estrarre informazioni in Excel usando Analisi del testo e Power Automate](tutorials/extract-excel-information.md)
    * [Usare l'API Analisi del testo e Microsoft Flow per identificare il sentiment dei commenti in un gruppo Yammer](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Integrare Power BI con l'API Analisi del testo per analizzare il feedback del cliente](tutorials/tutorial-power-bi-key-phrases.md)
* Esperienza di programmazione consigliata:
    * [Analisi della valutazione sui dati in streaming con Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Creare un'app Flask per tradurre testo, analizzare sentiment e sintetizzare la voce](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Lingue supportate

Questa sezione è stata spostata in un articolo separato per una migliore rintracciabilità. Per questo contenuto, vedere [Lingue supportate nell'API Analisi del testo](text-analytics-supported-languages.md).

<a name="data-limits"></a>

## <a name="data-limits"></a>Limiti dei dati

Tutti gli endpoint dell'API Analisi del testo accettano dati di testo non elaborati. Per altre informazioni, vedere l'articolo sui [Limiti dei dati](concepts/data-limits.md).

## <a name="unicode-encoding"></a>Codifica Unicode

L'API Analisi del testo usa la codifica Unicode per la rappresentazione di testo e i calcoli del numero di caratteri. Le richieste possono essere inviate sia in UTF-8 che in UTF-16 senza differenze misurabili nel numero di caratteri. Gli elementi di codice Unicode vengono usati come euristica per la lunghezza dei caratteri e vengono considerati equivalenti ai fini dei limiti di dati dell'analisi del testo. Se si usa [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) per ottenere il numero di caratteri, si usa lo stesso metodo impiegato per misurare le dimensioni dei dati.

## <a name="next-steps"></a>Passaggi successivi

+ [Creare una risorsa di Azure](../cognitive-services-apis-create-account.md) per Analisi del testo per ottenere una chiave e un endpoint per le applicazioni.

+ Usare l'[avvio rapido](quickstarts/text-analytics-sdk.md) per iniziare l'invio di chiamate API. Informazioni su come inviare testo, scegliere un'analisi e visualizzare i risultati con una quantità minima di codice.

+ Per informazioni sulle nuove versioni e funzionalità, vedere [Novità dell'API Analisi del testo](whats-new.md).

+ Per approfondire i contenuti di questa [esercitazione sull'analisi del sentiment](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services), usare Azure Databricks.

+ Per un elenco di post di blog e video su come usare l'API Analisi del testo con altri strumenti e tecnologie, vedere la pagina [Contenuto esterno e della community](text-analytics-resource-external-community.md).

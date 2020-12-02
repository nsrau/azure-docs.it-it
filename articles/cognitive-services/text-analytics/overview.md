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
ms.date: 11/17/2020
ms.author: aahi
keywords: text mining, analisi del sentiment, analisi del testo
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 68f892fea01582b16dad5efd8c86dbf0b578e50f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353052"
---
# <a name="what-is-the-text-analytics-api"></a>Informazioni sull'API Analisi del testo

L'API Analisi del testo è un servizio basato su cloud che offre funzionalità di elaborazione del linguaggio naturale per text mining e analisi del testo e include analisi del sentiment, opinion mining, estrazione di frasi chiave, rilevamento della lingua e riconoscimento dell'entità denominata.

L'API fa parte di [Servizi cognitivi di Azure](../index.yml), una raccolta di algoritmi di Machine Learning e intelligenza artificiale disponibili nel cloud per i progetti di sviluppo. È possibile usare queste funzionalità con l'[API REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/) o la [libreria client](quickstarts/text-analytics-sdk.md).

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

## <a name="sentiment-analysis"></a>Analisi del sentiment

Usare l'[analisi del sentiment](how-tos/text-analytics-how-to-sentiment-analysis.md) e conoscere l'opinione delle persone su un marchio o su un argomento eseguendo il text mining alla ricerca di indizi su sentiment positivo o negativo. 

La funzionalità prevede etichette per il sentiment, ad esempio "negativo", "neutro" e "positivo", in base al punteggio di attendibilità più alto trovato dal servizio a livello di frase e di documento. Questa funzionalità restituisce inoltre i punteggi di attendibilità compresi tra 0 e 1 per ogni documento e per le frasi al suo interno per il sentiment positivo, neutro e negativo. È anche possibile eseguire il servizio in locale [usando un contenitore](how-tos/text-analytics-how-to-install-containers.md).

A partire dalla versione 3.1 in anteprima, l'opinion mining è una funzionalità di Analisi del sentiment. Nota anche come analisi del sentiment basata su aspetto nell'elaborazione del linguaggio naturale (NLP), questa funzionalità fornisce informazioni più granulari sulle opinioni relative ad aspetti del testo come gli attributi di prodotti o servizi.

## <a name="key-phrase-extraction"></a>Estrazione di frasi chiave

Usare l'[estrazione di frasi chiave](how-tos/text-analytics-how-to-keyword-extraction.md) per identificare rapidamente i concetti principali del testo. Ad esempio, per il testo "Il cibo era delizioso e il personale era meraviglioso", Estrazione frasi chiave restituirà i punti rilevanti del discorso, ovvero "cibo" e "personale meraviglioso".

## <a name="language-detection"></a>Rilevamento della lingua

Questa funzionalità consente di [rilevare la lingua in cui è stato scritto il testo di input](how-tos/text-analytics-how-to-language-detection.md) e indicare un singolo codice lingua per ogni documento inviato nella richiesta in un'ampia gamma di lingue, varianti, dialetti e alcune lingue regionali/culturali. Il codice lingua è associato a un punteggio che indica il livello di attendibilità.

## <a name="named-entity-recognition"></a>Riconoscimento di entità denominate

Riconoscimento entità denominata può [identificare e classificare le entità](how-tos/text-analytics-how-to-entity-linking.md) nel testo come persone, luoghi, organizzazioni e quantità. Anche le entità note vengono riconosciute e collegate a altre informazioni sul Web.

## <a name="deploy-on-premises-using-docker-containers"></a>Distribuire in locale con i contenitori Docker

[Usare i contenitori di Analisi del testo](how-tos/text-analytics-how-to-install-containers.md) per distribuire le funzionalità dell'API in locale. Questi contenitori Docker consentono di avvicinare il servizio ai dati per motivi di conformità, sicurezza o di altro tipo. Analisi del testo offre i contenitori seguenti:

* analisi del sentiment
* estrazione di frasi chiave (anteprima)
* rilevamento lingua (anteprima)
* Analisi del testo per la sanità (anteprima)

## <a name="asynchronous-operations"></a>Operazioni asincrone

L'endpoint `/analyze` consente di usare specifiche funzionalità dell'API Analisi del testo [in modo asincrono](how-tos/text-analytics-how-to-call-api.md), ad esempio Riconoscimento entità denominata ed estrazione di frasi chiave.

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
    * [Usare l'API Analisi del testo e Microsoft Flow per identificare il sentiment dei commenti in un gruppo Yammer](/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)
    * [Integrare Power BI con l'API Analisi del testo per analizzare il feedback del cliente](tutorials/tutorial-power-bi-key-phrases.md)
* Esperienza di programmazione consigliata:
    * [Analisi della valutazione sui dati in streaming con Azure Databricks](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)
    * [Creare un'app Flask per tradurre testo, analizzare sentiment e sintetizzare la voce](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Lingue supportate

Questa sezione è stata spostata in un articolo separato per una migliore rintracciabilità. Per questo contenuto, vedere [Lingue supportate nell'API Analisi del testo](./language-support.md).

<a name="data-limits"></a>

## <a name="data-limits"></a>Limiti dei dati

Tutti gli endpoint dell'API Analisi del testo accettano dati di testo non elaborati. Per altre informazioni, vedere l'articolo sui [Limiti dei dati](concepts/data-limits.md).

## <a name="unicode-encoding"></a>Codifica Unicode

L'API Analisi del testo usa la codifica Unicode per la rappresentazione di testo e i calcoli del numero di caratteri. Le richieste possono essere inviate sia in UTF-8 che in UTF-16 senza differenze misurabili nel numero di caratteri. Gli elementi di codice Unicode vengono usati come euristica per la lunghezza dei caratteri e vengono considerati equivalenti ai fini dei limiti di dati dell'analisi del testo. Se si usa [`StringInfo.LengthInTextElements`](/dotnet/api/system.globalization.stringinfo.lengthintextelements) per ottenere il numero di caratteri, si usa lo stesso metodo impiegato per misurare le dimensioni dei dati.

## <a name="next-steps"></a>Passaggi successivi

+ [Creare una risorsa di Azure](../cognitive-services-apis-create-account.md) per Analisi del testo per ottenere una chiave e un endpoint per le applicazioni.

+ Usare l'[avvio rapido](quickstarts/text-analytics-sdk.md) per iniziare l'invio di chiamate API. Informazioni su come inviare testo, scegliere un'analisi e visualizzare i risultati con una quantità minima di codice.

+ Per informazioni sulle nuove versioni e funzionalità, vedere [Novità dell'API Analisi del testo](whats-new.md).

+ Per approfondire i contenuti di questa [esercitazione sull'analisi del sentiment](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services), usare Azure Databricks.

+ Per un elenco di post di blog e video su come usare l'API Analisi del testo con altri strumenti e tecnologie, vedere la pagina [Contenuto esterno e della community](text-analytics-resource-external-community.md).
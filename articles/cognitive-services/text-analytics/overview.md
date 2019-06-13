---
title: Informazioni sull'API Analisi del testo - Funzionalità -
titleSuffix: Azure Cognitive Services
description: Usare l'API Analisi del testo di Servizi cognitivi di Azure per l'analisi del sentiment, l'estrazione di frasi chiave, il rilevamento della lingua e il riconoscimento delle entità.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/03/2019
ms.author: aahi
ms.openlocfilehash: 7d52585b51af09c430130141c3680b5630f7b95e
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417279"
---
# <a name="what-is-text-analytics-api"></a>Informazioni sull'API Analisi del testo

L'API Analisi del testo è un servizio basato su cloud che garantisce l'elaborazione avanzata in linguaggio naturale su testo non elaborato e include quattro funzioni principali: analisi del sentiment, estrazione di frasi chiave, rilevamento della lingua e riconoscimento delle entità.

L'API fa parte di [Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/), una raccolta di algoritmi di Machine Learning e intelligenza artificiale disponibili nel cloud per i progetti di sviluppo.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

L'analisi del testo può avere diversi significati, ma in Servizi cognitivi l'API Analisi del testo offre quattro tipi di analisi, come descritto nella tabella seguente.

## <a name="sentiment-analysis"></a>Analisi del sentiment
Usare l'[analisi del sentiment](how-tos/text-analytics-how-to-sentiment-analysis.md) per conoscere l'opinione dei clienti su un marchio o su un argomento analizzando il testo non elaborato alla ricerca di indizi su sentiment positivo o negativo. L'API restituisce un punteggio sentiment compreso tra 0 e 1 per ogni documento, dove 1 è il risultato più positivo.<br /> I modelli di analisi vengono formati preventivamente usando un corpo completo di testo e tecnologie di linguaggio naturale di Microsoft. Per [alcune lingue](text-analytics-supported-languages.md), l'API può analizzare e assegnare punteggi a qualsiasi testo non elaborato fornito dall'utente, restituendo direttamente i risultati all'applicazione chiamante. È possibile usare l'API [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c9) o [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) SDK.

## <a name="key-phrase-extraction"></a>Estrazione frasi chiave
[Estrae frasi chiave](how-tos/text-analytics-how-to-keyword-extraction.md) in modo automatico per identificare rapidamente i punti principali. Ad esempio, per il testo di input "Il cibo era delizioso e il personale era meraviglioso", l'API restituisce i punti rilevanti del discorso, ovvero "cibo" e "personale meraviglioso". È possibile usare qui l'API [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6) o [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) SDK.

## <a name="language-detection"></a>Rilevamento lingua
È possibile [rilevare la lingua in cui è stato scritto il testo di input](how-tos/text-analytics-how-to-language-detection.md) e indicare un singolo codice lingua per ogni documento inviato nella richiesta in un'ampia varietà di lingue, varianti, dialetti e alcune lingue regionali/culturali. Il codice lingua è associato a un punteggio che indica il livello di attendibilità. È possibile usare l'API [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c7) o [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) SDK.

## <a name="named-entity-recognition"></a>Riconoscimento di entità denominate
[Identificare e classificare le entità](how-tos/text-analytics-how-to-entity-linking.md) nel testo, come persone, luoghi, organizzazioni, data/ora, quantità, percentuali, valute e altro ancora. Le entità più note vengono anche riconosciute e collegate ad altre informazioni sul Web. È possibile usare l'API [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634).

## <a name="use-containers"></a>Usare i contenitori

[Usare i contenitori di Analisi del testo](how-tos/text-analytics-how-to-install-containers.md) per estrarre frasi chiave, rilevare la lingua e analizzare il sentiment in locale installando contenitori Docker standardizzati più vicino ai dati.

## <a name="typical-workflow"></a>Flusso di lavoro tipico

Il flusso di lavoro è semplice: vengono inviati i dati da analizzare e vengono gestiti gli output nel codice. Gli analizzatori vengono utilizzati così come sono, senza bisogno di configurazioni o personalizzazioni aggiuntive.

1. [Iscriversi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per una [chiave di accesso](how-tos/text-analytics-how-to-access-key.md). La chiave deve essere trasmessa in ogni richiesta.

2. [Formulare una richiesta](how-tos/text-analytics-how-to-call-api.md#json-schema) contenente i dati come testo non elaborato non strutturato in JSON.

3. Pubblicare la richiesta nell'endpoint stabilito durante l'iscrizione, aggiungendo la risorsa desiderata: analisi del sentiment, estrazione di frasi chiave, rilevamento della lingua o identificazione dell'entità.

4. Trasmettere o archiviare la risposta in locale. A seconda della richiesta, i risultati sono un punteggio sentiment, una raccolta di frasi chiave estratte o un codice della lingua.

L'output viene restituito come un singolo documento JSON, con risultati per ogni documento di testo pubblicato, in base all'ID. In seguito è possibile analizzare, visualizzare o categorizzare i risultati in informazioni attuabili.

I dati non vengono archiviati nell'account. Le operazioni eseguite dall'API Analisi del testo sono senza stato. Questo significa che il testo specificato dall'utente viene elaborato e i risultati vengono restituiti immediatamente.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Analisi del testo per più livelli di esperienza di programmazione

È possibile iniziare a usare l'API Analisi del testo nei processi anche se non si ha molta esperienza di programmazione. Usare queste esercitazioni per informazioni su come usare l'API per analizzare il testo in modi diversi in base al proprio livello di esperienza. 

* Programmazione minima richiesta:
    * [Usare l'API Analisi del testo e Microsoft Flow per identificare il sentiment dei commenti in un gruppo Yammer](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Integrare Power BI con l'API Analisi del testo per analizzare il feedback del cliente](tutorials/tutorial-power-bi-key-phrases.md)
* Esperienza di programmazione consigliata:
    * [Analisi della valutazione sui dati in streaming con Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Creare un'app Flask per tradurre testo, analizzare sentiment e sintetizzare la voce](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Lingue supportate

Questa sezione è stata spostata in un articolo separato per una migliore rintracciabilità. Per questo contenuto, fare riferimento a [Supported languages in Text Analytics API](text-analytics-supported-languages.md) (Lingue supportate nell'API Analisi del testo).

<a name="data-limits"></a>

## <a name="data-limits"></a>Limiti dei dati

Tutti gli endpoint dell'API Analisi del testo accettano dati di testo non elaborati. Il limite corrente è di 5.120 caratteri per ogni documento. Se è necessario analizzare documenti di dimensioni maggiori, è possibile suddividerli in blocchi più piccoli. Se si richiede comunque un limite maggiore [contattare Microsoft](https://azure.microsoft.com/overview/sales-number/) in modo da poter discutere dei requisiti.

| Limite | Valore |
|------------------------|---------------|
| Dimensioni massime di un singolo documento | 5.120 caratteri misurati da [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Dimensioni massime dell'intera richiesta | 1 MB |
| Numero massimo di documenti in una richiesta | 1.000 documenti |

Il limite di velocità è 100 richieste al seconda e 1000 richieste al minuto. È possibile inviare una grande quantità di documenti in una singola chiamata (fino a 1000 documenti).

## <a name="unicode-encoding"></a>Codifica Unicode

L'API Analisi del testo usa la codifica Unicode per la rappresentazione di testo e i calcoli del numero di caratteri. Le richieste possono essere inviate sia in UTF-8 che in UTF-16 senza differenze misurabili nel numero di caratteri. Gli elementi di codice Unicode vengono usati come euristica per la lunghezza dei caratteri e vengono considerati equivalenti ai fini dei limiti di dati dell'analisi del testo. Se si usa [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) per ottenere il numero di caratteri, si usa lo stesso metodo impiegato per misurare le dimensioni dei dati.

## <a name="next-steps"></a>Passaggi successivi

+ [Iscriversi](how-tos/text-analytics-how-to-signup.md) per richiedere una chiave di accesso ed esaminare i passaggi per [chiamare l'API](how-tos/text-analytics-how-to-call-api.md).

+ L'[Avvio rapido](quickstarts/csharp.md) è una procedura dettagliata per le chiamate API REST scritte in C#. Informazioni su come inviare testo, scegliere un'analisi e visualizzare i risultati con una quantità minima di codice. Se si preferisce, in alternativa è possibile iniziare con l'[avvio rapido per Python](quickstarts/python.md).

+ Per approfondire i contenuti di questa [esercitazione sull'analisi del sentiment](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services), usare Azure Databricks.

+ Nella [pagina del contenuto esterno e della community](text-analytics-resource-external-community.md) è disponibile un elenco di post di blog e video che mostrano come usare l'API Analisi del testo con altri strumenti e tecnologie.

---
title: Informazioni su Analisi del testo
titleSuffix: Azure Cognitive Services
description: Analisi del testo nei Servizi cognitivi di Azure per l'analisi del sentiment, l'estrazione di frasi chiave, il rilevamento della lingua e il collegamento delle entità.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: overview
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: 0fe4a9f05e0f6d1abed7b906cc5cd89854885ae5
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2019
ms.locfileid: "53992856"
---
# <a name="what-is-text-analytics"></a>Informazioni su Analisi del testo

L'API Analisi del testo è un servizio basato su cloud che garantisce l'elaborazione avanzata in linguaggio naturale su testo non elaborato e include tre funzioni principali: analisi del sentiment, estrazione di frasi chiave e rilevamento della lingua.

L'API è supportata dalle risorse dei [Servizi cognitivi Microsoft](https://docs.microsoft.com/azure/cognitive-services/), una raccolta di algoritmi di apprendimento automatico e AI nel cloud, facilmente utilizzabili nei progetti di sviluppo.

## <a name="capabilities-in-text-analytics"></a>Funzionalità di Analisi del testo

Analisi del testo può avere diversi significati, ma nei Servizi cognitivi l'API Analisi del testo offre quattro tipi di analisi, come descritto nella tabella seguente.

| Operazioni| DESCRIZIONE | API |
|-----------|-------------|------|
|[**Analisi del sentiment**](how-tos/text-analytics-how-to-sentiment-analysis.md) | Serve a scoprire l'opinione dei clienti sul marchio o sull'argomento analizzando il testo non elaborato alla ricerca di indizi su sentiment positivo o negativo. L'API restituisce un punteggio sentiment compreso tra 0 e 1 per ogni documento, dove 1 è il risultato più positivo.<br /> I modelli di analisi vengono formati preventivamente usando un corpo completo di testo e tecnologie di linguaggio naturale di Microsoft. Per [alcune lingue](text-analytics-supported-languages.md), l'API può analizzare e assegnare punteggi a qualsiasi testo non elaborato fornito dall'utente, restituendo direttamente i risultati all'applicazione chiamante. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) <br /> [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package)  |
|[**Estrazione frasi chiave**](how-tos/text-analytics-how-to-keyword-extraction.md) | Estrae automaticamente frasi chiave per identificare rapidamente i punti principali. Ad esempio, per il testo di input "Il cibo era delizioso e il personale era meraviglioso", l'API restituisce i punti rilevanti del discorso, ovvero "cibo" e "personale meraviglioso".  | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) <br /> [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) |
|[**Rilevamento lingua**](how-tos/text-analytics-how-to-language-detection.md) | Per un massimo di 120 lingue, rileva la lingua in cui è scritto il testo di input e crea un report su un codice lingua singolo per ogni documento inviato nella richiesta. Il codice lingua è associato a un punteggio che indica il livello di attendibilità. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) <br />  [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) |
|[**Riconoscimento di entità (Anteprima)**](how-tos/text-analytics-how-to-entity-linking.md) | Identificare e classificare le entità nel testo come persone, luoghi, organizzazioni, data/ora, quantità, percentuali, valute e altro ancora. Le entità più note vengono anche riconosciute e collegate ad altre informazioni sul Web. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) |

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

<a name="supported-languages"></a>

## <a name="supported-languages"></a>Lingue supportate

Questa sezione è stata spostata in un articolo separato per una migliore rintracciabilità. Per questo contenuto, fare riferimento a [Supported languages in Text Analytics API](text-analytics-supported-languages.md) (Lingue supportate nell'API Analisi del testo).

<a name="data-limits"></a>

## <a name="data-limits"></a>Limiti dei dati

Tutti gli endpoint dell'API Analisi del testo accettano dati di testo non elaborati. Il limite corrente è di 5.000 caratteri per ogni documento. Se è necessario analizzare documenti di dimensioni maggiori, è possibile suddividerli in blocchi più piccoli. Se si richiede comunque un limite maggiore [contattare Microsoft](https://azure.microsoft.com/overview/sales-number/) in modo da poter discutere dei requisiti.

| Limite | Valore |
|------------------------|---------------|
| Dimensioni massime di un singolo documento | 5.000 caratteri misurati da `String.Length`. |
| Dimensioni massime dell'intera richiesta | 1 MB |
| Numero massimo di documenti in una richiesta | 1.000 documenti |

Il limite di velocità è 100 chiamate al minuto. Si noti che è possibile inviare una grande quantità di documenti in una singola chiamata (fino a 1.000 documenti).

## <a name="unicode-encoding"></a>Codifica Unicode

L'API Analisi del testo usa la codifica Unicode per la rappresentazione di testo e i calcoli del numero di caratteri. Le richieste possono essere inviate sia in UTF-8 che in UTF-16 senza differenze misurabili nel numero di caratteri. Gli elementi di codice Unicode vengono usati come euristica per la lunghezza dei caratteri e vengono considerati equivalenti ai fini dei limiti di dati dell'analisi del testo. Se si usa [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) per ottenere il numero di caratteri, si usa lo stesso metodo impiegato per misurare le dimensioni dei dati.

## <a name="next-steps"></a>Passaggi successivi

Innanzitutto, provare la [demo interattiva](https://azure.microsoft.com/services/cognitive-services/text-analytics/). È possibile incollare un input di testo (massimo 5.000 caratteri) per rilevare la lingua (fino a 120), calcolare un punteggio sentiment o estrarre le frasi chiave. Non è necessario iscriversi.

Quando si è pronti per chiamare direttamente l'API:

+ [Iscriversi](how-tos/text-analytics-how-to-signup.md) per richiedere una chiave di accesso ed esaminare i passaggi per [chiamare l'API](how-tos/text-analytics-how-to-call-api.md).

+ L'[Avvio rapido](quickstarts/csharp.md) è una procedura dettagliata per le chiamate API REST scritte in C#. Informazioni su come inviare testo, scegliere un'analisi e visualizzare i risultati con una quantità minima di codice.

+ La [documentazione di riferimento dell'API](//go.microsoft.com/fwlink/?LinkID=759346) offre la documentazione tecnica per le API. La documentazione supporta chiamate incorporate in modo che sia possibile chiamare l'API da ogni pagina della documentazione.

+ I [contenuti esterni e della community](text-analytics-resource-external-community.md) forniscono un elenco di post di blog e video che mostrano come usare Analisi del testo con altri strumenti e tecnologie.

## <a name="see-also"></a>Vedere anche 

 [Documentazione dei servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/)

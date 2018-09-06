---
title: Panoramica di Analisi del testo - Servizi cognitivi di Azure | Microsoft Docs
description: Analisi del testo nei Servizi cognitivi di Azure per l'analisi del sentiment, l'estrazione di frasi chiave, il rilevamento della lingua e il collegamento delle entità.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: a514618713afe2306b6fb99b2f8c038aeac56009
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2018
ms.locfileid: "43092204"
---
# <a name="what-is-text-analytics"></a>Informazioni su Analisi del testo

Il servizio Analisi del testo fornisce l'elaborazione del linguaggio naturale avanzato per il testo non strutturato non elaborato. Include quattro funzioni principali come l'analisi del sentiment, l'estrazione delle frasi chiave, il rilevamento della lingua e il collegamento delle entità.

## <a name="analyze-sentiment"></a>Analizzare la valutazione

Serve a [scoprire](how-tos/text-analytics-how-to-sentiment-analysis.md) l'opinione dei clienti sul marchio o sull'argomento analizzando il testo non elaborato alla ricerca di indizi su sentiment positivo o negativo. L'API restituisce un punteggio sentiment compreso tra 0 e 1 per ogni documento, dove 1 è il risultato più positivo.<br />
I modelli di analisi vengono formati preventivamente usando un corpo completo di testo e tecnologie di linguaggio naturale di Microsoft. Per [alcune lingue](text-analytics-supported-languages.md), l'API può analizzare e assegnare punteggi a qualsiasi testo non elaborato fornito dall'utente.

## <a name="extract-key-phrases"></a>Estrarre le frasi chiave

[Estrae frasi chiave](how-tos/text-analytics-how-to-keyword-extraction.md) in modo automatico per identificare rapidamente i punti principali. Ad esempio, dato il testo di input "Il cibo era delizioso e il personale era meraviglioso", il servizio Analisi del testo restituisce i punti rilevanti del discorso, ovvero "cibo" e "personale meraviglioso".

## <a name="detect-language"></a>Rileva lingua

Per un massimo di 120 lingue, [rileva](how-tos/text-analytics-how-to-language-detection.md) la lingua in cui è scritto il testo di input e crea un report relativo a un codice lingua singolo per ogni documento inviato nella richiesta. Il codice lingua è associato a un punteggio che indica il livello di attendibilità.

## <a name="identify-linked-entities-preview"></a>Identificare le entità collegate (anteprima)

[Identifica](how-tos/text-analytics-how-to-entity-linking.md) le entità note nel testo e collega ad altre informazioni sul Web. Entity Linking riconosce quando un termine viene usato come uno dei verbi, delle entità e delle altre forme distinguibili separatamente e crea una distinzione.

## <a name="typical-workflow"></a>Flusso di lavoro tipico

Il flusso di lavoro è semplice: vengono inviati i dati da analizzare e vengono gestiti gli output nel codice. Gli analizzatori vengono utilizzati così come sono, senza bisogno di configurazioni o personalizzazioni aggiuntive.

1. [Iscriversi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per una [chiave di accesso](how-tos/text-analytics-how-to-access-key.md). La chiave deve essere trasmessa in ogni richiesta.

2. [Formulare una richiesta](how-tos/text-analytics-how-to-call-api.md#json-schema) in JSON contenente i dati come testo non elaborato non strutturato.

3. Pubblicare la richiesta nell'endpoint stabilito durante l'iscrizione, aggiungendo l'API da richiamare: analisi del sentiment, estrazione di frasi chiave, rilevamento della lingua o identificazione dell'entità.

4. Trasmettere o archiviare la risposta in locale. A seconda della richiesta, i risultati sono un punteggio sentiment, una raccolta di frasi chiave estratte o un codice della lingua.

L'output viene restituito come un singolo documento JSON, con risultati per ogni documento di testo pubblicato, in base all'ID. In seguito è possibile analizzare, visualizzare o categorizzare i risultati in informazioni attuabili.

Le operazioni eseguite dal servizio di Analisi del testo sono senza stato. I dati non vengono archiviati nell'account.

<a name="data-limits"></a>

## <a name="specifications"></a>Specifiche

### <a name="supported-languages"></a>Lingue supportate

Vedere [Lingue supportate in Analisi del testo](text-analytics-supported-languages.md).

### <a name="data-limits"></a>Limiti dei dati

Tutti gli endpoint del servizio Analisi del testo accettano dati di testo non elaborati. Il limite corrente è di 5.000 caratteri per ogni documento. Se è necessario analizzare documenti di dimensioni maggiori, è possibile suddividerli in blocchi più piccoli. Se si richiede comunque un limite maggiore [contattare Microsoft](https://azure.microsoft.com/overview/sales-number/) in modo da poter discutere dei requisiti.

| Limite | Valore |
|------------------------|---------------|
| Dimensioni massime di un singolo documento | 5.000 caratteri misurati da `String.Length`. |
| Dimensioni massime dell'intera richiesta | 1 MB |
| Numero massimo di documenti in una richiesta | 1.000 documenti |

Il limite di velocità è 100 chiamate al minuto. Si noti che è possibile inviare una grande quantità di documenti in una singola chiamata (fino a 1000 documenti).

### <a name="unicode-encoding"></a>Codifica Unicode

Il servizio Analisi del testo usa la codifica Unicode per la rappresentazione di testo e i calcoli del numero di caratteri. Le richieste possono essere inviate sia in UTF-8 che in UTF-16 senza differenze misurabili nel numero di caratteri. Se si usa `String.Length` per ottenere il numero di caratteri, si usa lo stesso metodo impiegato per misurare le dimensioni dei dati.

## <a name="next-steps"></a>Passaggi successivi

Innanzitutto, provare la [demo interattiva](https://azure.microsoft.com/services/cognitive-services/text-analytics/). È possibile incollare un input di testo (massimo 5.000 caratteri) per rilevare la lingua (fino a 120), calcolare un punteggio sentiment, estrarre le frasi chiave o identificare le entità collegate. Non è necessario iscriversi.

Quando si è pronti per chiamare direttamente il servizio di Analisi del testo:

+ [Iscriversi](how-tos/text-analytics-how-to-signup.md) per richiedere una chiave di accesso ed esaminare i passaggi per [chiamare l'API](how-tos/text-analytics-how-to-call-api.md).

+ L'[Avvio rapido](quickstarts/csharp.md) è una procedura dettagliata per le chiamate API REST scritte in C#. Informazioni su come inviare testo, scegliere un'analisi e visualizzare i risultati con una quantità minima di codice.

+ La [documentazione di riferimento dell'API](//go.microsoft.com/fwlink/?LinkID=759346) offre la documentazione tecnica per le API REST. La documentazione supporta chiamate incorporate in modo che sia possibile chiamare l'API da ogni pagina della documentazione.

+ I [contenuti esterni e della community](text-analytics-resource-external-community.md) forniscono un elenco di post di blog e video che mostrano come usare Analisi del testo con altri strumenti e tecnologie.

## <a name="see-also"></a>Vedere anche 

 [Documentazione dei servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/)

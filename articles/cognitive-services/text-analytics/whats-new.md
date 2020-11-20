---
title: Novità della API Analisi del testo
titleSuffix: Text Analytics - Azure Cognitive Services
description: Questo articolo fornisce informazioni sulle nuove versioni e funzionalità per i servizi cognitivi di Azure Analisi del testo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 8743e765a050bfc4a2ba93a1b3e0344a960edb76
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966888"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Novità dell'API Analisi del testo

Il API Analisi del testo viene aggiornato su base continuativa. Per rimanere sempre aggiornati sui recenti sviluppi, in questo articolo vengono fornite informazioni sulle nuove versioni e funzionalità.

## <a name="november-2020"></a>Novembre 2020

* Un [nuovo endpoint](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Analyze) con API analisi del testo v 3.1-Preview. 3 per la nuova [API di analisi](how-tos/text-analytics-how-to-call-api.md?tabs=analyze)asincrona che supporta l'elaborazione batch per le operazioni di estrazione di ner, pii e frasi chiave.
* Un [nuovo endpoint](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) con API analisi del testo v 3.1-Preview. 3 per la nuova [analisi del testo asincrona per](how-tos/text-analytics-for-health.md) l'API ospitata sull'integrità con supporto per l'elaborazione batch.
* Entrambe le nuove funzionalità elencate in precedenza sono disponibili solo nelle aree seguenti: `West US 2` ,, `East US 2` `Central US` `North Europe` e `West Europe` .
* Il portoghese (Brasile) `pt-BR` è ora supportato in [analisi del sentiment](how-tos/text-analytics-how-to-sentiment-analysis.md) V3. x, a partire dalla versione del modello `2020-04-01` . Aggiunge al `pt-PT` supporto esistente per il portoghese.
* Librerie client aggiornate, che includono analisi asincrona e Analisi del testo per le operazioni di integrità. È possibile trovare esempi su GitHub:

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)


> [!div class="nextstepaction"]
> [Altre informazioni su API Analisi del testo v 3.1-Preview. 3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

## <a name="october-2020"></a>Ottobre 2020

* Supporto hindi per Analisi del sentiment V3. x, a partire dalla versione del modello `2020-04-01` . 
* Versione `2020-09-01` del modello per l'endpoint/languages V3, che aggiunge miglioramenti alla precisione e al rilevamento della lingua.
* disponibilità V3 in India centrale e Emirati Arabi Uniti settentrionali.

## <a name="september-2020"></a>Settembre 2020

### <a name="general-api-updates"></a>Aggiornamenti generali dell'API

* Rilascio di un nuovo URL per l'anteprima pubblica di Analisi del testo v 3.1 per supportare gli aggiornamenti agli endpoint denominati di Entity Recognition V3 seguenti: 
    * `/pii` l'endpoint include ora la nuova `redactedText` proprietà nel codice JSON della risposta dove le entità pii rilevate nel testo di input vengono sostituite da un `*` per ogni carattere di tali entità.
    * `/linking` l'endpoint include ora la `bingID` proprietà nel codice JSON della risposta per le entità collegate.
* Gli endpoint dell'API Analisi del testo anteprima seguenti sono stati ritirati il 4 settembre 2020:
    * versione 2.1-anteprima
    * v3.0 - Anteprima
    * v 3.0-Anteprima. 1
    
> [!div class="nextstepaction"]
> [Altre informazioni su API Analisi del testo v 3.1-Preview. 2](quickstarts/text-analytics-sdk.md)

### <a name="text-analytics-for-health-container-updates"></a>Analisi del testo per gli aggiornamenti del contenitore di integrità

Gli aggiornamenti seguenti sono specifici della versione di settembre del Analisi del testo solo per il contenitore di integrità.
* Una nuova immagine contenitore con tag `1.1.013530001-amd64-preview` con la nuova versione del modello `2020-09-03` è stata rilasciata nel repository containerpreview. 
* Questa versione del modello offre miglioramenti al riconoscimento delle entità, al rilevamento delle abbreviazioni e ai miglioramenti della latenza.

> [!div class="nextstepaction"]
> [Altre informazioni su Analisi del testo per l'integrità](how-tos/text-analytics-for-health.md)

## <a name="august-2020"></a>Agosto 2020

### <a name="general-api-updates"></a>Aggiornamenti generali dell'API

* Versione `2020-07-01` del modello per V3 `/keyphrases` `/pii` ed `/languages` endpoint, che aggiunge:
    * [Categorie di entità](named-entity-types.md?tabs=personal) governative e specifiche del paese aggiuntive per il riconoscimento delle entità denominate.
    * Supporto norvegese e turco in Analisi del sentiment V3.
* Verrà ora restituito un errore HTTP 400 per le richieste API V3 che superano i [limiti dei dati](concepts/data-limits.md)pubblicati. 
* Gli endpoint che restituiscono un offset supportano ora il `stringIndexType` parametro facoltativo, che consente di modificare `offset` i `length` valori restituiti e in modo che corrispondano a uno [schema di indice delle stringhe](concepts/text-offsets.md)supportato.

### <a name="text-analytics-for-health-container-updates"></a>Analisi del testo per gli aggiornamenti del contenitore di integrità

Gli aggiornamenti seguenti sono specifici della versione di agosto del Analisi del testo solo per il contenitore di integrità.

* Nuova versione modello per Analisi del testo per l'integrità: `2020-07-24`
* Nuovo URL per l'invio di Analisi del testo per le richieste di integrità: `http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health` (si noti che sarà necessaria una cancellazione della cache del browser per poter usare l'app Web demo inclusa in questa nuova immagine contenitore)

Sono state modificate le proprietà seguenti nella risposta JSON:

* `type` è stata rinominata `category` 
* `score` è stata rinominata `confidenceScore`
* Le entità nel `category` campo dell'output JSON sono ora in formato Pascal. Le entità seguenti sono state rinominate:
    * `EXAMINATION_RELATION` è stato rinominato in `RelationalOperator`.
    * `EXAMINATION_UNIT` è stato rinominato in `MeasurementUnit`.
    * `EXAMINATION_VALUE` è stato rinominato in `MeasurementValue`.
    * `ROUTE_OR_MODE` è stato rinominato `MedicationRoute` .
    * L'entità relazionale `ROUTE_OR_MODE_OF_MEDICATION` è stata rinominata in `RouteOfMedication` .

Sono state aggiunte le entità seguenti:

* NER
    * `AdministrativeEvent`
    * `CareEnvironment`
    * `HealthcareProfession`
    * `MedicationForm` 

* Estrazione di relazioni
    * `DirectionOfCondition`
    * `DirectionOfExamination`
    * `DirectionOfTreatment`

> [!div class="nextstepaction"]
> [Altre informazioni su Analisi del testo per il contenitore di integrità](how-tos/text-analytics-for-health.md)

## <a name="july-2020"></a>Luglio 2020 

### <a name="text-analytics-for-health-container---public-gated-preview"></a>Analisi del testo per il contenitore di integrità-anteprima pubblica gestita

Il Analisi del testo per il contenitore di integrità è ora disponibile in anteprima pubblicamente gestita, che consente di estrarre informazioni da testo in lingua inglese non strutturato in documenti clinici come moduli di assunzione di pazienti, note mediche, documenti di ricerca e riepiloghi di scaricamento. Attualmente, non verranno addebitati Analisi del testo per l'utilizzo del contenitore di integrità.

Il contenitore offre le funzionalità seguenti:

* Riconoscimento di entità denominate
* Estrazione di relazioni
* Collegamento di entità
* Negazione

## <a name="may-2020"></a>Maggio 2020

### <a name="text-analytics-api-v3-general-availability"></a>Disponibilità generale API Analisi del testo V3

L'API di analisi del testo V3 è ora disponibile a livello generale con i seguenti aggiornamenti:

* Versione modello `2020-04-01`
* Nuovi [limiti dei dati](concepts/data-limits.md) per ogni funzionalità
* [Supporto delle lingue](language-support.md) aggiornato per [analisi del sentiment (SA) v3](how-tos/text-analytics-how-to-sentiment-analysis.md)
* Endpoint separato per il collegamento di entità 
* Nuova categoria di entità "Address" in [nome entità riconoscibile (ner) v3](how-tos/text-analytics-how-to-entity-linking.md).
* Nuove sottocategorie in NER V3:
   * Località-geografica
   * Posizione-strutturale
   * Organizzazione-scambio scorte
   * Organizzazione-medico
   * Organizzazione-sport
   * Evento-culturale
   * Evento-naturale
   * Evento-sport

Sono state aggiunte le proprietà seguenti nella risposta JSON:
   * `SentenceText` in Analisi del sentiment
   * `Warnings` per ogni documento 

I nomi delle seguenti proprietà nella risposta JSON sono stati modificati, se applicabile:

* `score` è stata rinominato in `confidenceScore`
    * `confidenceScore` ha due punti decimali di precisione. 
* `type` è stata rinominata `category`
* `subtype` è stata rinominata `subcategory`

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

> [!div class="nextstepaction"]
> [Altre informazioni su API Analisi del testo V3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>Anteprima pubblica di API Analisi del testo v 3.1
   * Nuova funzionalità Analisi del sentiment- [Opinion Mining](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * Nuovo `PII` filtro di dominio Personal () per informazioni protette sull'integrità ( `PHI` ).

> [!div class="nextstepaction"]
> [Scopri di più su API Analisi del testo versione 3.1 Preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>Febbraio 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Supporto SDK per l'anteprima pubblica API Analisi del testo V3

Nell'ambito della [versione unificata di Azure SDK](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290), il API analisi del testo V3 SDK è ora disponibile come anteprima pubblica per i linguaggi di programmazione seguenti:
   * [C#](./quickstarts/text-analytics-sdk.md?pivots=programming-language-csharp&tabs=version-3)
   * [Python](./quickstarts/text-analytics-sdk.md?pivots=programming-language-python&tabs=version-3)
   * [JavaScript (Node.js)](./quickstarts/text-analytics-sdk.md?pivots=programming-language-javascript&tabs=version-3)
   * [Java](./quickstarts/text-analytics-sdk.md?pivots=programming-language-java&tabs=version-3)
   
> [!div class="nextstepaction"]
> [Scopri di più su API Analisi del testo V3 SDK](./quickstarts/text-analytics-sdk.md?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Anteprima pubblica di riconoscimento entità denominata V3

I tipi di entità aggiuntivi sono ora disponibili nel servizio di anteprima pubblica per il riconoscimento delle entità denominate (NER) v3 durante l'espansione del rilevamento di entità di informazioni generali e personali presenti nel testo. Questo aggiornamento introduce la [versione del modello](concepts/model-versioning.md) `2020-02-01` , che include:

* Riconoscimento dei seguenti tipi di entità generali (solo in inglese):
    * PersonType
    * Prodotto
    * Event
    * Entità geopolitica (GPE) come sottotipo in location
    * Competenza

* Riconoscimento dei seguenti tipi di entità di informazioni personali (solo in inglese):
    * Persona
    * Organizzazione
    * Age come sottotipo sotto Quantity
    * Data come sottotipo sotto DateTime
    * Email 
    * Numero di telefono (solo Stati Uniti)
    * URL
    * Indirizzo IP

### <a name="october-2019"></a>Ottobre 2019

#### <a name="named-entity-recognition-ner"></a>Riconoscimento delle entità denominate (NER)

* Un [nuovo endpoint](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii) per il riconoscimento dei tipi di entità di informazioni personali (solo in inglese)

* Separare gli endpoint per il [riconoscimento delle entità](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral) e il collegamento delle [entità](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking).

* [Versione del modello](concepts/model-versioning.md) `2019-10-01` , che include:
    * Rilevamento espanso e categorizzazione di entità presenti nel testo. 
    * Riconoscimento dei nuovi tipi di entità seguenti:
        * Numero di telefono
        * Indirizzo IP

Il collegamento di entità supporta inglese e spagnolo. Il supporto del linguaggio NER varia in base al tipo di entità.

#### <a name="sentiment-analysis-v3-public-preview"></a>Anteprima pubblica di Analisi del sentiment versione 3

* Un [nuovo endpoint](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/Sentiment) per analizzare i sentimenti.
* [Versione del modello](concepts/model-versioning.md) `2019-10-01` , che include:

    * Miglioramenti significativi nell'accuratezza e nei dettagli della categorizzazione e del punteggio del testo dell'API.
    * Assegnazione automatica di etichette per diversi sentimenti nel testo.
    * Analisi dei sentimenti e output a livello di documento e di frase. 

Supporta inglese (), `en` giapponese () `ja` , cinese semplificato ( `zh-Hans` ), cinese tradizionale ( `zh-Hant` ), francese ( `fr` ), Italiano ( `it` ), spagnolo ( `es` ), olandese ( `nl` ), portoghese () e `pt` tedesco ( `de` ) ed è disponibile nelle aree seguenti: `Australia East` ,, `Central Canada` `Central US` , `East Asia` , `East US` , `East US 2` , `North Europe` , `Southeast Asia` , `South Central US` , `UK South` , `West Europe` e `West US 2` . 

> [!div class="nextstepaction"]
> [Altre informazioni su Analisi del sentiment V3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sull'API Analisi del testo](overview.md)  
* [Esempi di scenari utente](text-analytics-user-scenarios.md)
* [Analisi del sentiment](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Rilevamento della lingua](how-tos/text-analytics-how-to-language-detection.md)
* [Riconoscimento delle entità](how-tos/text-analytics-how-to-entity-linking.md)
* [Estrazione delle frasi chiave](how-tos/text-analytics-how-to-keyword-extraction.md)

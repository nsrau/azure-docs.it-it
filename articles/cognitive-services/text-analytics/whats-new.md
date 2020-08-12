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
ms.date: 08/06/2020
ms.author: aahi
ms.openlocfilehash: 95e9b208159e9af41563f12ce8af7892e13f6629
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121851"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Novità dell'API Analisi del testo

Il API Analisi del testo viene aggiornato su base continuativa. Per rimanere sempre aggiornati sui recenti sviluppi, in questo articolo vengono fornite informazioni sulle nuove versioni e funzionalità.

## <a name="august-2020"></a>Agosto 2020

### <a name="general-api-updates"></a>Aggiornamenti generali dell'API

* Versione `2020-07-01` del modello per V3 `/keyphrases` `/pii` ed `/languages` endpoint, che aggiunge:
    * [Categorie di entità](named-entity-types.md?tabs=personal) governative e specifiche del paese aggiuntive per il riconoscimento delle entità denominate.
* Verrà ora restituito un errore HTTP 400 per le richieste API V3 che superano i [limiti dei dati](concepts/data-limits.md)pubblicati. 

### <a name="text-analytics-for-health-container-august-updates"></a>Analisi del testo per il contenitore di integrità aggiornamenti agosto

Gli aggiornamenti seguenti sono specifici della versione di agosto del Analisi del testo solo per il contenitore di integrità.

* Nuova versione modello per Analisi del testo per l'integrità:`2020-07-24`
* Nuovo URL per l'invio di Analisi del testo per le richieste di integrità:`http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health` 

Sono state modificate le proprietà seguenti nella risposta JSON:

* `type` è stata rinominata `category` 
* `score` è stata rinominata `confidenceScore`
* Le entità nel `category` campo dell'output JSON sono ora in formato Pascal. Le entità seguenti sono state rinominate:
    * `EXAMINATION_RELATION` è stato rinominato in `RelationalOperator`.
    * `EXAMINATION_UNIT` è stato rinominato in `MeasurementUnit`.
    * `EXAMINATION_VALUE` è stato rinominato in `MeasurementValue`.
    * `ROUTE_OR_MODE`è stato rinominato `MedicationRoute` .
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

* Versione modello`2020-04-01`
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
   * `SentenceText`in Analisi del sentiment
   * `Warnings`per ogni documento 

I nomi delle seguenti proprietà nella risposta JSON sono stati modificati, se applicabile:

* `score` è stata rinominato in `confidenceScore`
    * `confidenceScore`ha due punti decimali di precisione. 
* `type` è stata rinominata `category`
* `subtype` è stata rinominata `subcategory`

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

> [!div class="nextstepaction"]
> [Altre informazioni su API Analisi del testo V3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>Anteprima pubblica di API Analisi del testo v 3.1
   * Nuova funzionalità Analisi del sentiment- [Opinion Mining](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * Nuovo [filtro di `PII` dominio Personal ()](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) per informazioni protette sull'integrità ( `PHI` ).

> [!div class="nextstepaction"]
> [Scopri di più su API Analisi del testo versione 3.1 Preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>Febbraio 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>Supporto SDK per l'anteprima pubblica API Analisi del testo V3

Nell'ambito della [versione unificata di Azure SDK](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290), il API analisi del testo V3 SDK è ora disponibile come anteprima pubblica per i linguaggi di programmazione seguenti:
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)
   
   > [!div class="nextstepaction"]
> [Scopri di più su API Analisi del testo V3 SDK](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Anteprima pubblica di riconoscimento entità denominata V3

I tipi di entità aggiuntivi sono ora disponibili nel servizio di anteprima pubblica per il riconoscimento delle entità denominate (NER) v3 durante l'espansione del rilevamento di entità di informazioni generali e personali presenti nel testo. Questo aggiornamento introduce la [versione del modello](concepts/model-versioning.md) `2020-02-01` , che include:

* Riconoscimento dei seguenti tipi di entità generali (solo in inglese):
    * PersonType
    * Prodotto
    * Evento
    * Entità geopolitica (GPE) come sottotipo in location
    * Competenza

* Riconoscimento dei seguenti tipi di entità di informazioni personali (solo in inglese):
    * Persona
    * Organization
    * Age come sottotipo sotto Quantity
    * Data come sottotipo sotto DateTime
    * Email 
    * Numero di telefono (solo Stati Uniti)
    * URL
    * Indirizzo IP

> [!div class="nextstepaction"]
> [Altre informazioni su Named Entity Recognition V3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Ottobre 2019

#### <a name="named-entity-recognition-ner"></a>Riconoscimento delle entità denominate (NER)

* Un [nuovo endpoint](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) per il riconoscimento dei tipi di entità di informazioni personali (solo in inglese)

* Separare gli endpoint per il [riconoscimento delle entità](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) e il collegamento delle [entità](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

* [Versione del modello](concepts/model-versioning.md) `2019-10-01` , che include:
    * Rilevamento espanso e categorizzazione di entità presenti nel testo. 
    * Riconoscimento dei nuovi tipi di entità seguenti:
        * Numero di telefono
        * Indirizzo IP

Il collegamento di entità supporta inglese e spagnolo. Il supporto del linguaggio NER varia in base al tipo di entità.

#### <a name="sentiment-analysis-v3-public-preview"></a>Anteprima pubblica di Analisi del sentiment versione 3

* Un [nuovo endpoint](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) per analizzare i sentimenti.
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

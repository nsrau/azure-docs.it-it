---
title: Creare una definizione di indice e concetti - Ricerca di Azure
description: Introduzione all'indicizzazione di termini e concetti in Ricerca di Azure, incluse le parti dei componenti e la struttura fisica.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/01/2019
ms.custom: seodec2018
ms.openlocfilehash: 77f4b597ad4b87db7e720dd57191c6b192a4c93b
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000951"
---
# <a name="create-a-basic-index-in-azure-search"></a>Creare un indice di base in Ricerca di Azure

In Ricerca di Azure un *indice* è un archivio persistente di *documenti* e altri costrutti usati per la ricerca filtrata e full-text in un servizio di Ricerca di Azure. A livello concettuale, un documento è un singola unità di dati ricercabili nell'indice. Ad esempio, un rivenditore di e-commerce può avere un documento per ogni elemento in vendita, un'agenzia di stampa può avere un documento per ogni articolo e così via. Applicando questi concetti ai più familiari elementi di database equivalenti, un *indice* è concettualmente analogo a una *tabella* e i *documenti* equivalgono in linea di massima alle *righe* di una tabella.

Quando si aggiunge o si carica un indice, Ricerca di Azure crea strutture fisiche basate sullo schema fornito dall'utente. Ad esempio, se un campo nell'indice è contrassegnato come ricercabile, viene creato un indice invertito per il campo. Successivamente, quando si aggiungono o caricano documenti o si inviano le query di ricerca in Ricerca di Azure, si inviano le richieste a un indice specifico nel servizio di ricerca. Il caricamento di campi con valori di documenti viene chiamato *indicizzazione* o inserimento dati.

È possibile creare un indice nel portale, [API REST](search-create-index-rest-api.md), o [.NET SDK](search-create-index-dotnet.md).

## <a name="components-of-an-index"></a>Componenti di un indice

In modo schematico, un indice Ricerca di Azure è costituito dagli elementi seguenti. 

La [*raccolta campi*](#fields-collection) in genere costituisce la maggior parte di un indice, in cui ogni campo è denominato, tipizzato e attribuito con comportamenti consentiti che determinano la modalità di utilizzo. Altri elementi sono [Componenti per il suggerimento](#suggesters), profili di punteggio, analizzatori con parti di componenti per supportare la personalizzazione, e le opzioni CORS.

```json
{  
  "name": (optional on PUT; required on POST) "name_of_index",  
  "fields": [  
    {  
      "name": "name_of_field",  
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",  
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),  
      "filterable": true (default) | false,  
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),  
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),  
      "key": true | false (default, only Edm.String fields can be keys),  
      "retrievable": true (default) | false,  
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }  
  ],  
  "suggesters": [  
    {  
      "name": "name of suggester",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": ["field1", "field2", ...]  
    }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "name of scoring profile",  
      "text": (optional, only applies to searchable fields) {  
        "weights": {  
          "searchable_field_name": relative_weight_value (positive #'s),  
          ...  
        }  
      },  
      "functions": (optional) [  
        {  
          "type": "magnitude | freshness | distance | tag",  
          "boost": # (positive number used as multiplier for raw score != 1),  
          "fieldName": "...",  
          "interpolation": "constant | linear (default) | quadratic | logarithmic",  
          "magnitude": {  
            "boostingRangeStart": #,  
            "boostingRangeEnd": #,  
            "constantBoostBeyondRange": true | false (default)  
          },  
          "freshness": {  
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)  
          },  
          "distance": {  
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)  
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)  
          },  
          "tag": {  
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)  
          }  
        }  
      ],  
      "functionAggregation": (optional, applies only when functions are specified)   
        "sum (default) | average | minimum | maximum | firstMatching"  
    }  
  ],  
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",  
  "corsOptions": (optional) {  
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],  
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)  
  }  
}
```

## <a name="fields-collection-and-attribution"></a>Raccolta di campi e attribuzione
Quando si definisce lo schema, è necessario specificare il nome, tipo e gli attributi di ogni campo nell'indice. Il tipo di campo classifica i dati archiviati in quel campo. Gli attributi sono impostati nei singoli campi per specificare come viene usato il campo. La tabella seguente enumera gli attributi che è possibile specificare.

### <a name="data-types"></a>Tipi di dati
| Type | DESCRIZIONE |
| --- | --- |
| *Edm.String* |Testo facoltativamente soggetto a tokenizzazione per la ricerca full-text (suddivisione delle parole, stemming e così via). |
| *Collection(Edm.String)* |Elenco di stringhe facoltativamente soggette a tokenizzazione per la ricerca full-text. Non esiste alcun limite superiore teorico al numero di elementi in una raccolta, ma alle raccolte si applica il limite massimo di 16 MB di dimensioni del payload. |
| *Edm.Boolean* |Contiene valori true/false. |
| *Edm.Int32* |Valori integer a 32 bit. |
| *Edm.Int64* |Valori integer a 64 bit. |
| *Edm.Double* |Dati numerici a precisione doppia. |
| *Edm.DateTimeOffset* |Valori di ora rappresentati in formato OData V4 (ad esempio `yyyy-MM-ddTHH:mm:ss.fffZ` o `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |Punto che rappresenta una località geografica del mondo. |

È possibile trovare altre informazioni sui [tipi di dati supportati di Ricerca di Azure qui](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types).

### <a name="index-attributes"></a>Attributi dell'indice
| Attributo | DESCRIZIONE |
| --- | --- |
| *Chiave* |Stringa che fornisce l'ID univoco di ogni documento, usata per la ricerca di documenti. Ogni indice deve avere una chiave. Un solo campo può essere la chiave e deve essere impostata su Edm.String. |
| *Recuperabile* |Specifica se il campo può essere restituito nel risultato di una ricerca. |
| *Filtrabile* |Consente di usare il campo nelle query di filtro. |
| *Ordinabile* |Consente a una query ordinare i risultati della ricerca usando questo campo. |
| *Con facet* |Consente di usare un campo in una struttura di [esplorazione in base a facet](search-faceted-navigation.md) per i filtri autoindirizzati. In genere, i campi che contengono valori ricorrenti che è possibile usare per raggruppare più documenti, ad esempio, più documenti che rientrano in una categoria di servizi o una singola marca, funzionano meglio come facet. |
| *Ricercabile* |Contrassegna il campo come disponibile per la ricerca full-text. |

È possibile trovare altre informazioni sugli [attributi di indice di Ricerca di Azure qui](https://docs.microsoft.com/rest/api/searchservice/Create-Index).

## <a name="suggesters"></a>Componenti per il suggerimento
Un componente di suggerimento è una sezione dello schema che definisce quali campi in un indice vengono utilizzati per supportare le query con completamento automatico nelle ricerche. In genere le stringhe di ricerca parziale vengono inviate ai Suggerimenti (API REST per il servizio Ricerca di Azure) mentre l'utente digita la query di ricerca e l'API restituisce un set di espressioni suggerite. Un componente per il suggerimento definito dall'utente nell'indice consente di determinare i campi utilizzati per creare i termini di ricerca a completamento automatico. Per altre informazioni, vedere [Aggiungere suggerimenti](index-add-suggesters.md) per i dettagli della configurazione.

## <a name="scoring-profiles"></a>Profili di punteggio

Un profilo di punteggio è una sezione dello schema che definisce i comportamenti di punteggio personalizzati che consentono di determinare quali elementi verranno visualizzati più in alto nei risultati della ricerca. I profili di punteggio sono costituiti da funzioni e campi ponderati. Per utilizzarli, è necessario specificare il nome di un profilo nella stringa di query.

Un profilo di punteggio predefinito viene eseguito in background al fine di calcolare un punteggio di ricerca per ogni elemento visualizzato in un set di risultati. È possibile utilizzare un profilo di punteggio interno, senza nome. In alternativa, è possibile impostare defaultScoringProfile affinché usi un profilo personalizzato come predefinito. Tale profilo può essere richiamato ogni volta in cui un profilo personalizzato non viene specificato nella stringa di query.

Per altre informazioni, vedere [Aggiungere profili di punteggio](index-add-scoring-profiles.md).

## <a name="analyzers"></a>Analizzatori

L'elemento analizzatori imposta il nome dell'analizzatore di lingua da usare per il campo. Per il set di valori consentito, vedere [Analizzatori di linguaggi in Ricerca di Azure](index-add-language-analyzers.md). Questa opzione può essere usata solo con campi ricercabili e non può essere impostata con **searchAnalyzer** o **indexAnalyzer**. Una volta scelto, l'analizzatore non può essere cambiato per il campo.

## <a name="cors"></a>CORS

JavaScript sul lato client non può chiamare API per impostazione predefinita perché il browser impedisce tutte le richieste con origini diverse. Per consentire query con origini diverse nell'indice, abilitare CORS (Cross-Origin Resource Sharing) impostando l'attributo **corsOptions**. Per motivi di sicurezza, solo le API di query supportano CORS. 

Per CORS è possibile impostare le opzioni seguenti:

+ **allowedOrigins** (obbligatoria): Si tratta di un elenco di origini a cui verrà concesso l'accesso all'indice. Questo significa che al codice JavaScript servito da queste origini sarà consentito eseguire query sull'indice, purché fornisca la chiave API corretta. Ogni origine è in genere nel formato `protocol://<fully-qualified-domain-name>:<port>` anche se `<port>` spesso viene omessa. Vedere [Utilizzare la condivisione di risorse tra origini (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) per altre informazioni.

  Per consentire l'accesso a tutte le origini, includere `*` come unico elemento nella matrice**allowedOrigins**. *Non si consiglia questa pratica per i servizi di ricerca della produzione* ma spesso è utile per lo sviluppo e il debug.

+ **maxAgeInSeconds** (facoltativa): I browser usano questo valore per determinare la durata (in secondi) di memorizzazione nella cache delle risposte preliminari CORS. Questo valore deve essere un intero non negativo. A un valore più grande corrispondono prestazioni migliori, ma deve trascorrere più tempo prima che le modifiche dei criteri CORS diventino effettive. Se questo valore non è impostato, viene usata una durata predefinita di 5 minuti.

## <a name="next-steps"></a>Passaggi successivi

Conoscendo la composizione dell'indice, è possibile continuare nel portale per creare il primo indice.

> [!div class="nextstepaction"]
> [Aggiungere un indice (portale)](search-create-index-portal.md)
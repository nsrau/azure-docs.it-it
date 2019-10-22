---
title: Creare una definizione di indice e concetti - Ricerca di Azure
description: Introduzione all'indicizzazione di termini e concetti in Ricerca di Azure, incluse le parti dei componenti e la struttura fisica.
author: HeidiSteen
manager: nitinme
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: seodec2018
ms.openlocfilehash: 0a26cfc578f12044cb5834f202a0fed5d0a30274
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "69647361"
---
# <a name="create-a-basic-index-in-azure-search"></a>Creare un indice di base in Ricerca di Azure

In Ricerca di Azure un *indice* è un archivio persistente di *documenti* e altri costrutti usati per la ricerca filtrata e full-text in un servizio di Ricerca di Azure. A livello concettuale, un documento è un singola unità di dati ricercabili nell'indice. Ad esempio, un rivenditore di e-commerce può avere un documento per ogni elemento in vendita, un'agenzia di stampa può avere un documento per ogni articolo e così via. Applicando questi concetti ai più familiari elementi di database equivalenti, un *indice* è concettualmente analogo a una *tabella* e i *documenti* equivalgono in linea di massima alle *righe* di una tabella.

Quando si aggiunge o si carica un indice, Ricerca di Azure crea strutture fisiche basate sullo schema fornito dall'utente. Ad esempio, se un campo nell'indice è contrassegnato come ricercabile, viene creato un indice invertito per il campo. Successivamente, quando si aggiungono o caricano documenti o si inviano le query di ricerca in Ricerca di Azure, si inviano le richieste a un indice specifico nel servizio di ricerca. Il caricamento di campi con valori di documenti viene chiamato *indicizzazione* o inserimento dati.

È possibile creare un indice nel portale, [API REST](search-create-index-rest-api.md), o [.NET SDK](search-create-index-dotnet.md).

## <a name="recommended-workflow"></a>Flusso di lavoro consigliato

Il raggiungimento di una corretta progettazione degli indici si ottiene in genere tramite più iterazioni. L'utilizzo di una combinazione di strumenti e API consente di finalizzare rapidamente il progetto.

1. Determinare se è possibile usare un [indicizzatore](search-indexer-overview.md#supported-data-sources). Se i dati esterni sono una delle origini dati supportate, è possibile creare un prototipo e caricare un indice usando la procedura guidata [**Importa dati**](search-import-data-portal.md).

2. Se non è possibile usare **Importa dati**, è comunque possibile [creare un indice iniziale nel portale](search-create-index-portal.md), aggiungendo campi, tipi di dati e assegnando attributi tramite i controlli nella pagina **Aggiungi indice**. Il portale visualizza gli attributi che sono disponibili per i tipi di dati diversi. Ciò è utile se non si ha familiarità con la progettazione di indici.

   ![Pagina Aggiungi indice con attributi per tipo di dati](media/search-create-index-portal/field-attributes.png "Pagina Aggiungi indice con attributi per tipo di dati")
  
   Facendo clic su **Crea**, si creano nel servizio di ricerca tutte le strutture fisiche che supportano l'indice.

3. Scaricare lo schema dell'indice mediante [Get Index REST API (Ottenere un indice API REST)](https://docs.microsoft.com/rest/api/searchservice/get-index) e uno strumento di test Web come [Postman](search-get-started-postman.md). Si ottiene così una rappresentazione JSON dell'indice creato nel portale. 

   A questo punto si passa a un approccio basato sul codice. Poiché non è possibile modificare un indice creato in precedenza, il portale non è particolarmente adatto per l'iterazione. È tuttavia possibile usare Postman e REST per le attività rimanenti.

4. [Caricare l'indice con i dati](search-what-is-data-import.md). Ricerca di Azure accetta i documenti JSON. Per caricare i dati a livello di codice, è possibile usare Postman con i documenti JSON nel payload della richiesta. Se i dati non possono essere facilmente espressi in JSON, questo passaggio sarà più laborioso.

5. Eseguire query sull'indice, esaminare i risultati ed eseguire ulteriormente l'iterazione sullo schema dell'indice fino a quando non si visualizzano i risultati previsti. È possibile usare [**Esplora ricerche**](search-explorer.md) o Postman per eseguire query sull'indice.

6. Continuare a usare il codice per eseguire l'iterazione sul progetto.  

Poiché le strutture fisiche vengono create nel servizio, è necessario [eliminare e ricreare gli indici](search-howto-reindex.md) quando si apportano modifiche sostanziali a una definizione di campo esistente. Ciò significa che durante lo sviluppo, è necessario pianificare ricompilazioni frequenti. È possibile prendere in considerazione l'uso di un subset di dati per velocizzare le ricompilazioni. 

Per la progettazione iterativa è consigliabile un codice anziché un approccio basato sul portale. Se ci si affida al portale per la definizione dell'indice, è necessario compilare la definizione dell'indice a ogni ricompilazione. In alternativa, strumenti come [Postman e API REST](search-get-started-postman.md) sono utili per eseguire test dei modelli di verifica quando i progetti di sviluppo sono ancora in fase iniziale. È possibile apportare modifiche incrementali a una definizione di indice nel corpo della richiesta e quindi inviare la richiesta al servizio per ricreare un indice usando uno schema aggiornato.

## <a name="components-of-an-index"></a>Componenti di un indice

In modo schematico, un indice Ricerca di Azure è costituito dagli elementi seguenti. 

La [*raccolta campi*](#fields-collection) in genere costituisce la maggior parte di un indice, in cui ogni campo è denominato, tipizzato e attribuito con comportamenti consentiti che determinano la modalità di utilizzo. Altri elementi includono [suggerimenti](#suggesters), [profili di Punteggio](#scoring-profiles)e [analizzatori](#analyzers) con parti componente per supportare le opzioni di personalizzazione, [CORS](#cors) e [chiave di crittografia](#encryption-key) .

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
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>Raccolta di campi e attributi di campi

Quando si definisce lo schema, è necessario specificare il nome, tipo e gli attributi di ogni campo nell'indice. Il tipo di campo classifica i dati archiviati in quel campo. Gli attributi sono impostati nei singoli campi per specificare come viene usato il campo. La tabella seguente enumera gli attributi che è possibile specificare.

### <a name="data-types"></a>Tipi di dati
| Type | Description |
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

Esattamente un campo nell'indice deve essere designato come un campo **chiave** che identifica in modo univoco ogni documento.

Altri attributi determinano la modalità di utilizzo di un campo in un'applicazione. Ad esempio, l'attributo **ricercabile** viene assegnato a ogni campo che deve essere incluso in una ricerca full-text. 

Le API usate per compilare un indice hanno comportamenti predefiniti variabili. Per le [API REST](https://docs.microsoft.com/rest/api/searchservice/Create-Index), la maggior parte degli attributi è abilitata per impostazione predefinita (ad esempio, la **ricerca** e il **recupero** sono true per i campi di stringa) ed è spesso necessario impostarli solo se si desidera disattivarli. Per .NET SDK, il valore opposto è true. Per le proprietà non impostate in modo esplicito, per impostazione predefinita viene disabilitato il comportamento di ricerca corrispondente, a meno che non venga abilitato in modo specifico.

| Attributo | Description |
| --- | --- |
| `key` |Stringa che fornisce l'ID univoco di ogni documento, usata per la ricerca di documenti. Ogni indice deve avere una chiave. Un solo campo può essere la chiave e deve essere impostata su Edm.String. |
| `retrievable` |Specifica se il campo può essere restituito nel risultato di una ricerca. |
| `filterable` |Consente di usare il campo nelle query di filtro. |
| `Sortable` |Consente a una query ordinare i risultati della ricerca usando questo campo. |
| `facetable` |Consente di usare un campo in una struttura di [esplorazione in base a facet](search-faceted-navigation.md) per i filtri autoindirizzati. In genere, i campi che contengono valori ricorrenti che è possibile usare per raggruppare più documenti, ad esempio, più documenti che rientrano in una categoria di servizi o una singola marca, funzionano meglio come facet. |
| `searchable` |Contrassegna il campo come disponibile per la ricerca full-text. |


## <a name="storage-implications"></a>Implicazioni relative all'archiviazione

Gli attributi selezionati hanno un impatto sull'archiviazione. Lo screenshot seguente illustra i modelli di archiviazione dell'indice derivanti da diverse combinazioni di attributi.

L'indice è basato sull'origine dati [incorporata di esempio Real Immobiliare](search-get-started-portal.md) , che è possibile indicizzare ed eseguire query nel portale. Anche se non vengono visualizzati gli schemi dell'indice, è possibile dedurre gli attributi in base al nome dell'indice. Ad esempio, l'indice *realestate-searchable*ha soltanto l'attributo **ricercabile** selezionato, l'indice *realestate-retrievable*ha soltanto l'attributo **recuperabile** selezionato e così via.

![Dimensioni dell'indice basate sulla selezione degli attributi](./media/search-what-is-an-index/realestate-index-size.png "Dimensioni dell'indice basate sulla selezione degli attributi")

Sebbene queste varianti di indice siano artificiali, è possibile farvi riferimento per una considerazione generale sul modo in cui gli attributi influiscono sull'archiviazione. L'impostazione **recuperabile** aumenta le dimensioni dell'indice? No. L'aggiunta di campi a uno **Strumento suggerimenti** aumenta le dimensioni dell'indice? Sì.

Gli indici che supportano filtro e ordinamento sono proporzionalmente più grandi rispetto agli indici che supportano soltanto la ricerca full-text. Questo avviene perché filtro e ordinamento eseguono query su corrispondenze esatte in modo che i documenti siano archiviati senza alterazioni. Al contrario, i campi ricercabili che supportano la ricerca full-text e fuzzy usano indici invertiti, popolati con termini in formato token che consumano meno spazio rispetto a documenti interi.

> [!Note]
> L'architettura di archiviazione è considerata un dettaglio di implementazione di Ricerca di Azure e può essere soggetta a modifiche senza preavviso. Non è garantito che il comportamento attuale verrà mantenuto in futuro.

## <a name="suggesters"></a>Suggerimenti
Un componente di suggerimento è una sezione dello schema che definisce quali campi in un indice vengono utilizzati per supportare le query con completamento automatico nelle ricerche. In genere, le stringhe di ricerca parziale vengono inviate ai [Suggerimenti (API REST)](https://docs.microsoft.com/rest/api/searchservice/suggestions) mentre l'utente digita la query di ricerca e l'API restituisce un set di espressioni suggerite. 

I campi aggiunti a uno strumento suggerimenti vengono usati per compilare i termini di ricerca con completamento automatico. Tutti i termini di ricerca vengono creati durante l'indicizzazione e archiviati separatamente. Per altre informazioni sulla creazione di una struttura per uno strumento suggerimenti, vedere [Aggiungere strumenti suggerimenti](index-add-suggesters.md).

## <a name="scoring-profiles"></a>Profili di punteggio

Un [profilo di punteggio](index-add-scoring-profiles.md) è una sezione dello schema che definisce i comportamenti di punteggio personalizzati che consentono di determinare quali elementi verranno visualizzati più in alto nei risultati della ricerca. I profili di punteggio sono costituiti da funzioni e campi ponderati. Per utilizzarli, è necessario specificare il nome di un profilo nella stringa di query.

Un profilo di punteggio predefinito viene eseguito in background al fine di calcolare un punteggio di ricerca per ogni elemento visualizzato in un set di risultati. È possibile utilizzare un profilo di punteggio interno, senza nome. In alternativa, è possibile impostare **defaultScoringProfile** affinché usi un profilo personalizzato come predefinito. Tale profilo può essere richiamato ogni volta in cui un profilo personalizzato non viene specificato nella stringa di query.

## <a name="analyzers"></a>Analizzatori

L'elemento analizzatori imposta il nome dell'analizzatore di lingua da usare per il campo. Per altre informazioni sull'intervallo degli analizzatori disponibili, vedere [Aggiungere analizzatori del linguaggio a un indice di Ricerca di Azure](search-analyzers.md). Gli analizzatori possono essere usati solo con campi ricercabili. Dopo aver assegnato l'analizzatore a un campo sarà possibile modificarlo solo ricompilando l'indice.

## <a name="cors"></a>CORS

JavaScript sul lato client non può chiamare API per impostazione predefinita perché il browser impedisce tutte le richieste con origini diverse. Per consentire query con origini diverse nell'indice, abilitare CORS (Cross-Origin Resource Sharing) impostando l'attributo **corsOptions**. Per motivi di sicurezza, solo le API di query supportano CORS. 

Per CORS è possibile impostare le opzioni seguenti:

+ **allowedOrigins** (obbligatorio): si tratta di un elenco di origini a cui verrà concesso l'accesso all'indice. Questo significa che al codice JavaScript servito da queste origini sarà consentito eseguire query sull'indice, purché fornisca la chiave API corretta. Ogni origine è in genere nel formato `protocol://<fully-qualified-domain-name>:<port>` anche se `<port>` spesso viene omessa. Vedere [Utilizzare la condivisione di risorse tra origini (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) per altre informazioni.

  Per consentire l'accesso a tutte le origini, includere `*` come unico elemento nella matrice**allowedOrigins**. *Non si consiglia questa pratica per i servizi di ricerca della produzione* ma spesso è utile per lo sviluppo e il debug.

+ **maxAgeInSeconds** (facoltativo): i browser usano questo valore per determinare la durata (in secondi) di memorizzazione nella cache delle risposte preliminari CORS. Questo valore deve essere un intero non negativo. A un valore più grande corrispondono prestazioni migliori, ma deve trascorrere più tempo prima che le modifiche dei criteri CORS diventino effettive. Se questo valore non è impostato, viene usata una durata predefinita di 5 minuti.

## <a name="encryption-key"></a>Chiave di crittografia

Sebbene tutti gli indici di ricerca di Azure siano crittografati per impostazione predefinita usando chiavi gestite da Microsoft, è possibile configurare gli indici per la crittografia con **chiavi gestite dal cliente** in Key Vault. Per altre informazioni, vedere [gestire le chiavi di crittografia in ricerca di Azure](search-security-manage-encryption-keys.md).

## <a name="next-steps"></a>Passaggi successivi

Conoscendo la composizione dell'indice, è possibile continuare nel portale per creare il primo indice.

> [!div class="nextstepaction"]
> [Aggiungere un indice (portale)](search-create-index-portal.md)

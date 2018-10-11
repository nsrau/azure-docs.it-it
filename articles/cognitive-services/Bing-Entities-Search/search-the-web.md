---
title: Informazioni su Ricerca entità Bing
titlesuffix: Azure Cognitive Services
description: Informazioni su come usare l'API Ricerca entità Bing per cercare entità e località nel Web.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: overview
ms.date: 07/06/2016
ms.author: scottwhi
ms.openlocfilehash: 2b3adf07a8522322434a6596475fa06c0df978e8
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48813601"
---
# <a name="what-is-bing-entity-search"></a>Informazioni su Ricerca entità Bing

L'API Ricerca entità Bing invia una query di ricerca a Bing e ottiene risultati che includono entità e località. I risultati relativi alle località includono ristoranti, hotel o altre aziende locali. Bing restituisce località se la query specifica il nome dell'azienda locale o richiede un tipo di azienda (ad esempio, ristoranti nelle vicinanze). Bing restituisce entità se la query specifica persone, oggetti o località note (attrazioni, stati, paesi e così via).

## <a name="suggesting--using-search-terms"></a>Suggerimenti e uso dei termini di ricerca

Se si fornisce una casella di ricerca in cui l'utente immette il termine di ricerca, usare l'[API Suggerimenti automatici Bing](../bing-autosuggest/get-suggested-search-terms.md) per migliorare l'esperienza. Mentre l'utente digita, l'API restituisce le stringhe di query suggerite, in base ai termini di ricerca parziali.

Dopo che l'utente ha immesso il termine di ricerca, l'URL codifica il termine prima di impostare il parametro di query [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query). Ad esempio, se l'utente immette *Marcus Appel*, impostare `q` su *Marcus+Appel* oppure su *Marcus%20Appel*.

Se il termine di ricerca contiene un errore di ortografia, la risposta della ricerca include un oggetto [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext). L'oggetto mostra l'ortografia originale e l'ortografia corretta usata da Bing per la ricerca.

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="requesting-entities"></a>Richiesta di entità

Per una richiesta di esempio, vedere [Making your first entities request](./quick-start.md) (Come effettuare la prima richiesta di entità).

## <a name="the-response"></a>Risposta

La risposta contiene un oggetto [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse). Se Bing rileva un'entità o una località pertinente, l'oggetto include il campo `entities`, il campo `places` o entrambi. In caso contrario, l'oggetto di risposta non include uno dei due campi.
> [!NOTE]
> Le risposte Entità supportano più mercati, ma la risposta Località supporta solo sedi aziendali negli Stati Uniti. 

Il campo `entities` è un oggetto [EntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entityanswer) che contiene un elenco di oggetti [Entity](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity) (vedere il campo `value`). L'elenco può contenere una singola entità dominante, più entità di disambiguazione o entrambi. 

Un'entità dominante è un'entità ritenuta da Bing l'unica in grado di soddisfare la richiesta (nessuna ambiguità rispetto all'entità che soddisfa la richiesta). Se più entità possono soddisfare la richiesta, l'elenco contiene più di un'entità di disambiguazione. Se ad esempio la richiesta usa il titolo generico di una serie di film, l'elenco conterrà probabilmente entità di disambiguazione. Se invece la richiesta specifica un titolo specifico della serie, l'elenco conterrà probabilmente una singola entità dominante.

Le entità includono personaggi famosi, ad esempio cantanti, attori, atleti e modelli, località e punti di riferimento noti, come il Monte Rainier o il Lincoln Memorial, e oggetti comuni, ad esempio una banana, un cagnolino, il titolo di un libro o di un film. Il campo [entityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) contiene hint che identificano il tipo di entità, ad esempio una persona, un film, un animale o un'attrazione. Per un elenco dei tipi possibili, vedere [Entity Types](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types) (Tipi di entità)

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

L'esempio seguente mostra una risposta che include un'entità dominante e di disambiguazione.

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Mount Rainier"
    },
    "entities": {
        "value": [{
            "contractualRules": [{
                "_type": "ContractualRules/LicenseAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "license": {
                    "name": "CC-BY-SA",
                    "url": "http://creativecommons.org/licenses/by-sa/3.0/"
                },
                "licenseNotice": "Text under CC-BY-SA license"
            },
            {
                "_type": "ContractualRules/LinkAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "text": "contoso.com",
                "url": "http://contoso.com/mount_rainier"
            },
            {
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount-rainier"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier...",
            "name": "Mount Rainier",
            "url": "http://www.northwindtraders.com/",
            "image": {
                "name": "Mount Rainier",
                "thumbnailUrl": "https://www.bing.com/th?id=A4ae343983daa4...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier"
                }],
                "hostPageUrl": "http://contoso.com/commons/7/72/mount_rain...",
                "width": 110,
                "height": 110
            },
            "description": "Mount Rainier is 14,411 ft tall and the highest mountain...",
            "entityPresentationInfo": {
                "entityScenario": "DominantEntity",
                "entityTypeHints": ["Attraction"]
            },
            "bingId": "38b9431e-cf91-93be-0584-c42a3ecbfdc7"
        },
        {
            "contractualRules": [{
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount_rainier_national_park"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier%20National...",
            "name": "Mount Rainier National Park",
            "url": "http://worldwideimporters.com/",
            "image": {
                "name": "Mount Rainier National Park",
                "thumbnailUrl": "https://www.bing.com/th?id=A91bdc5a1b648a695a39...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier_national_park"
                }],
                "hostPageUrl": "http://contoso.com/en/7/7a...",
                "width": 50,
                "height": 50
            },
            "description": "Mount Rainier National Park is a United States National Park...",
            "entityPresentationInfo": {
                "entityScenario": "DisambiguationItem",
                "entityTypeHints": ["Organization"]
            },
            "bingId": "29d4b681-227a-3924-7bb1-8a54e8666b8c"
        }]
    }
}
```

L'entità include un campo `name`, `description` e `image`. Quando si visualizzano questi campi nell'esperienza utente, è necessario attribuirli. Il campo `contractualRules` contiene un elenco di attribuzioni che è necessario applicare. La regola contrattuale identifica il campo a cui si applica l'attribuzione. Per informazioni sull'applicazione di un'attribuzione, vedere [Attribuzione](#data-attribution).

```json
"contractualRules": [{
    "_type": "ContractualRules/LicenseAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "license": {
        "name": "CC-BY-SA",
        "url": "http://creativecommons.org/licenses/by-sa/3.0/"
    },
    "licenseNotice": "Text under CC-BY-SA license"
},
{
    "_type": "ContractualRules/LinkAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "text": "contoso.com",
    "url": "http://contoso.com/wiki/Mount_Rainier"
},
{
    "_type": "ContractualRules/MediaAttribution",
    "targetPropertyName": "image",
    "mustBeCloseToContent": true,
    "url": "http://contoso.com/wiki/Mount_Rainier"
}], ...
```

Quando si visualizzano le informazioni sull'entità (nome, descrizione e immagine), è necessario usare anche l'URL nel campo `webSearchUrl` per il collegamento alla pagina dei risultati della ricerca Bing contenente l'entità.


Il campo `places` è un oggetto [LocalEntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#localentityanswer) che contiene un elenco di oggetti [Place](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#place) (vedere il campo `value`). L'elenco contiene una o più entità locali che soddisfano la richiesta.

Le località includono ristoranti, hotel o aziende locali. Il campo [entityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) contiene hint che identificano il tipo di entità locale. L'elenco contiene hint come Place, LocalBusiness e Restaurant. Ogni hint successivo nella matrice restringe il tipo dell'entità. Per un elenco dei tipi possibili, vedere [Entity Types](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types) (Tipi di entità)

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```
> [!NOTE]
> Le risposte Entità supportano più mercati, ma la risposta Località supporta solo sedi aziendali negli Stati Uniti. 

Query di entità che tengono conto del luogo, come *ristoranti nelle vicinanze*, richiedono la posizione dell'utente per fornire risultati accurati. Le richieste devono sempre usare le intestazioni X-Search-Location e X-MSEdge-ClientIP per specificare la posizione dell'utente. Se Bing ritiene che la query possa migliorare con la posizione dell'utente, imposta il campo `askUserForLocation` di [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) su **true**. 

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Sinful Bakery and Cafe",
        "askUserForLocation": true
    },
    ...
}
```

Un risultato relativo a una località include il nome, l'indirizzo, il numero di telefono e l'URL al sito Web dell'entità. Quando si visualizzano le informazioni sull'entità, è necessario usare anche l'URL nel campo `webSearchUrl` per il collegamento alla pagina dei risultati della ricerca Bing contenente l'entità.

```json
"places": {
    "value": [{
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Sinful%20Bakery...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "http://libertysdelightfulsinfulbakeryandcafe.com/",
        "entityPresentationInfo": {
            "entityScenario": "ListItem",
            "entityTypeHints": ["Place",
            "LocalBusiness",
            "Restaurant"]
        },
        "address": {
            "addressLocality": "Seattle",
            "addressRegion": "WA",
            "postalCode": "98112",
            "addressCountry": "US",
            "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
    }]
}
```

> [!NOTE]
> L'utente, o una terza parte per conto dell'utente, non può usare, conservare, archiviare, memorizzare nella cache, condividere o distribuire alcun dato dall'API Entità allo scopo di testare, sviluppare, eseguire il training, distribuire o rendere disponibile un servizio o una funzionalità.  

## <a name="data-attribution"></a>Attribuzione di dati

Le risposte dell'API Entità di Bing contengono informazioni di proprietà di terze parti. L'utente è tenuto a garantire un uso appropriato, ad esempio con la conformità a qualsiasi licenza Creative Commons su ci potrebbe basarsi l'esperienza utente.

Se una risposta o un risultato include i campi `contractualRules`, `attributions` o `provider`, è necessario attribuire i dati. Se la risposta non include uno di questi campi, non è necessaria alcuna attribuzione. Se la risposta include il campo `contractualRules` e i campi `attributions` e/o `provider`, è necessario usare le regole contrattuali per attribuire i dati.

L'esempio seguente illustra un'entità che include una regola contrattuale MediaAttribution e un'immagine che include un campo `provider`. La regola MediaAttribution identifica l'immagine come destinazione della regola, pertanto è possibile ignorare il campo `provider` dell'immagine e usare la regola MediaAttribution per fornire l'attribuzione.  

```json
"value": [{
    "contractualRules": [
        ...
        {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://contoso.com/mount_rainier"
        }
    ],
    ...
    "image": {
        "name": "Mount Rainier",
        "thumbnailUrl": "https://www.bing.com/th?id=A46378861201...",
        "provider": [{
            "_type": "Organization",
            "url": "http://contoso.com/mount_rainier"
        }],
        "hostPageUrl": "http://www.graphicdesigninstitute.com/Uploaded...",
        "width": 110,
        "height": 110
    },
    ...
}]
```

Se una regola contrattuale include il campo `targetPropertyName`, la regola si applica solo al campo di destinazione. In caso contrario, la regola si applica all'oggetto padre che contiene il campo `contractualRules`.

Nell'esempio seguente la regola `LinkAttribution` include il campo `targetPropertyName`, quindi la regola si applica al campo `description`. Per le regole che si applicano a campi specifici, è necessario includere una riga subito dopo i dati di destinazione contenenti un collegamento ipertestuale al sito Web del provider. Ad esempio, per attribuire la descrizione, includere una riga subito dopo il testo della descrizione contenente un collegamento ipertestuale ai dati sul sito Web del provider, in questo caso creare un collegamento a contoso.com.

```json
"entities": {
    "value": [{
            ...
            "description": "Marcus Appel is a former American....",
            ...
            "contractualRules": [{
                    "_type": "ContractualRules/LinkAttribution",
                    "targetPropertyName": "description",
                    "mustBeCloseToContent": true,
                    "text": "contoso.com",
                    "url": "http://contoso.com/cr?IG=B8AD73..."
                 },
            ...
  
```

### <a name="license-attribution"></a>Attribuzione di licenze

Se l'elenco di regole contrattuali include una regola [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution), è necessario visualizzare l'avviso sulla riga immediatamente successiva al contenuto a cui si applica la licenza. La regola `LicenseAttribution` usa il campo `targetPropertyName` per identificare la proprietà a cui si applica la licenza.

Di seguito è riportato un esempio che include una regola `LicenseAttribution`.

![Attribuzione di licenze](./media/cognitive-services-bing-entities-api/licenseattribution.png)

L'avviso di licenza visualizzato deve includere un collegamento ipertestuale al sito Web che contiene informazioni sulla licenza. In genere l'utente rende il nome della licenza un collegamento ipertestuale. Ad esempio, se l'avviso è **Text under CC-BY-SA license** (Testo nella licenza CC-BY-SA) è il nome della licenza, CC-BY-SA viene reso un collegamento ipertestuale.

### <a name="link-and-text-attribution"></a>Attribuzione di collegamento e testo

Le regole [LinkAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#linkattribution) e [TextAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#textattribution) vengono in genere usate per identificare il provider dei dati. Il campo `targetPropertyName` identifica il campo a cui si applica la regola.

Per attribuire i provider, includere una riga subito dopo il contenuto a cui si applicano le attribuzioni (ad esempio, il campo di destinazione). La riga deve essere chiaramente etichettata per indicare che i provider sono l'origine dei dati. Ad esempio, "Data from: contoso.com" (Dati da:contoso.com). Per le regole `LinkAttribution` è necessario creare un collegamento ipertestuale al sito Web del provider.

Di seguito è riportato un esempio che include le regole `LinkAttribution` e `TextAttribution`.

![Attribuzione del testo di un collegamento](./media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>Attribuzione di file multimediali

Se l'entità include un'immagine e viene visualizzata, è necessario fornire un collegamento click-through al sito Web del provider. Se l'entità include una regola [MediaAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mediaattribution), usare l'URL della regola per creare il collegamento click-through. In caso contrario, usare l'URL incluso nel campo `provider` dell'immagine per creare il collegamento click-through.

Di seguito è riportato un esempio che include il campo `provider` e le regole contrattuali di un'immagine. Poiché l'esempio include la regola contrattuale, viene ignorato il campo `provider` dell'immagine e viene applicata la regola `MediaAttribution`.

![Attribuzione di file multimediali](./media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>Esperienza di ricerca o analoga a quella di una ricerca

Proprio come l'API Ricerca Web Bing, l'API Ricerca entità Bing può essere usata solo in seguito a una ricerca o una query utente diretta o come risultato di un'azione all'interno di un'app o un'esperienza che può essere logicamente interpretata come richiesta di ricerca di un utente. A scopo illustrativo, di seguito sono riportati alcuni esempi accettabili di esperienze di ricerca o analoghe a quelle di una ricerca.

- L'utente immette una query direttamente in una casella di ricerca in un'app
- L'utente seleziona un testo o un'immagine specifica e richiedere "altre informazioni" o "informazioni aggiuntive"
- L'utente richiede uno specifico argomento in un bot di ricerca
- L'utente si sofferma su uno specifico oggetto o entità in uno scenario di tipo ricerca visiva

Se non si è certi del fatto che l'esperienza possa essere considerata analoga a quella di una ricerca, è consigliabile rivolgersi a Microsoft.

## <a name="throttling-requests"></a>Limitazione delle richieste

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Passaggi successivi

Per iniziare rapidamente con la prima richiesta, vedere [Making your first entities request](./quick-start.md) (Come effettuare la prima richiesta di entità).

Acquisire familiarità con la documentazione di riferimento sull'[API Ricerca entità Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference). La documentazione di riferimento contiene le intestazioni e i parametri di query da usare per richiedere i risultati della ricerca. Include anche le definizioni degli oggetti della risposta. 

Per migliorare l'esperienza utente con la casella di ricerca, vedere [API Suggerimenti automatici Bing](../bing-autosuggest/get-suggested-search-terms.md). Quando l'utente immette i termini della query, è possibile chiamare questa API per ottenere i termini della query pertinenti usati da altri utenti.

Per non violare alcuna regola relativa all'uso dei risultati della ricerca, vedere [Bing Use and Display Requirements](./use-display-requirements.md) (Requisiti per l'uso e la visualizzazione di Bing).
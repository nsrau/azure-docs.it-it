---
title: Procedure consigliate per le mappe di Azure servizio di ricerca | Mappe Microsoft Azure
description: Informazioni su come applicare le procedure consigliate quando si usa il servizio di ricerca da Microsoft Azure maps.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8d62d7d278323baa0ae49b9e12f46468efb067a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335314"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Procedure consigliate per le mappe di Azure servizio di ricerca

Azure Maps [servizio di ricerca](https://docs.microsoft.com/rest/api/maps/search) include API che offrono varie funzionalità. Ad esempio, l'API di ricerca di indirizzi può trovare punti di interesse o dati in una posizione specifica. 

Questo articolo illustra come applicare le procedure audio quando si chiamano dati da mappe di Azure servizio di ricerca. Si apprenderà come:

* Compilare query per restituire corrispondenze rilevanti.
* Limita i risultati della ricerca.
* Informazioni sulle differenze tra i tipi di risultati.
* Leggere la struttura di ricerca indirizzo-risposta.

## <a name="prerequisites"></a>Prerequisiti

Per effettuare chiamate alle API del servizio Maps di Azure, sono necessari un account Azure Maps e una chiave. Per ulteriori informazioni, vedere [creare un account](quick-demo-map-app.md#create-an-account-with-azure-maps) e [ottenere una chiave primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account). 

Per informazioni sull'autenticazione in mappe di Azure, vedere [Manage Authentication in Azure Maps](./how-to-manage-authentication.md).

> [!TIP]
> Per eseguire query servizio di ricerca, è possibile usare l' [app post](https://www.getpostman.com/apps) per compilare chiamate REST. In alternativa, è possibile usare qualsiasi ambiente di sviluppo API preferito.

## <a name="best-practices-to-geocode-addresses"></a>Procedure consigliate per la geocodifica degli indirizzi

Quando si cerca un indirizzo completo o parziale usando mappe di Azure servizio di ricerca, l'API legge le parole chiave dalla query di ricerca. Restituisce quindi le coordinate di longitudine e latitudine dell'indirizzo. Questo processo è denominato *geocodifica*. 

La possibilità di geocodificare in un paese dipende dalla disponibilità dei dati stradali e dalla precisione del servizio di geocodifica. Per altre informazioni sulle funzionalità di geocodifica di Azure Maps per paese o area geografica, vedere [copertura della geocodifica](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage).

### <a name="limit-search-results"></a>Limitare i risultati della ricerca

 L'API di ricerca di mappe di Azure può aiutare a limitare i risultati della ricerca in modo appropriato. È possibile limitare i risultati in modo da visualizzare i dati rilevanti per gli utenti.

> [!NOTE]
> Le API di ricerca supportano più parametri rispetto a quelli illustrati in questo articolo.

#### <a name="geobiased-search-results"></a>Risultati della ricerca geopolarizzata

Per geopolarizzare i risultati nell'area pertinente per l'utente, aggiungere sempre il maggior numero possibile di dettagli sulla località. È possibile che si desideri limitare i risultati della ricerca specificando alcuni tipi di input:

* Impostare il `countrySet` parametro. È possibile, ad esempio `US,FR`, impostarlo su. Per impostazione predefinita, l'API Cerca nell'intero mondo, in modo che possa restituire risultati non necessari. Se la query non ha `countrySet` parametri, la ricerca potrebbe restituire risultati non accurati. Ad esempio, una ricerca di una città denominata *Bellevue* restituisce i risultati degli Stati Uniti e della Francia perché entrambi i paesi contengono una città denominata *Bellevue*.

* È possibile utilizzare i `btmRight` parametri `topleft` e per impostare il rettangolo di delimitazione. Questi parametri limitano la ricerca a un'area specifica sulla mappa.

* Per influenzare l'area di pertinenza per i risultati, definire i `lat` parametri `lon` della coordinata e. Usare il `radius` parametro per impostare il raggio dell'area di ricerca.


#### <a name="fuzzy-search-parameters"></a>Parametri di ricerca fuzzy

Si consiglia di usare l' [API fuzzy di ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) Maps di Azure quando non si conoscono gli input utente per una query di ricerca. L'API combina la ricerca e la geocodifica del PDI in una *ricerca a riga singola*canonica: 

* I `minFuzzyLevel` parametri `maxFuzzyLevel` e consentono di restituire corrispondenze rilevanti anche quando i parametri di query non corrispondono esattamente alle informazioni desiderate dall'utente. Per ottimizzare le prestazioni e ridurre i risultati insoliti, impostare le query `minFuzzyLevel=1` di `maxFuzzyLevel=2`ricerca sulle impostazioni predefinite di e. 

    Ad esempio, quando il `maxFuzzyLevel` parametro è impostato su 2, il termine di ricerca *restrant* viene associato al *ristorante*. Quando necessario, è possibile eseguire l'override dei livelli fuzzy predefiniti. 

* Usare il `idxSet` parametro per assegnare una priorità al set esatto di tipi di risultati. Per classificare in ordine di priorità un set di risultati esatto, è possibile inviare un elenco delimitato da virgole di indici. Nell'elenco, l'ordine degli elementi non è rilevante. Azure Maps supporta gli indici seguenti:

* `Addr` - **Intervalli di indirizzi**: punti di indirizzo interpolati dall'inizio e dalla fine della strada. Questi punti sono rappresentati come intervalli di indirizzi.
* `Geo` - **Aree geografiche**: divisioni amministrative di terra. Una geografia può essere un paese, uno stato o una città, ad esempio.
* `PAD` - **Indirizzi punto**: indirizzi che includono un nome e un numero via. Gli indirizzi punto sono reperibili in un indice. Un esempio è *Soquel Dr 2501*. Un indirizzo punto fornisce il livello più elevato di accuratezza disponibile per gli indirizzi.  
* `POI` - **Punti di interesse**: punta su una mappa considerata come importante o che potrebbe essere interessante. L' [API di ricerca dell'indirizzo](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) non restituisce i pois.  
* `Str` - **Strade**: le strade della mappa.
* `XStr` - **Tra le strade o le intersezioni**: le giunzioni o i punti in cui due strade si intersecano.


#### <a name="usage-examples"></a>Esempi di utilizzo

* `idxSet=POI`-Solo per la ricerca. 

* `idxSet=PAD,Addr`-Cerca solo indirizzi. `PAD`indica l'indirizzo del punto e `Addr` indica l'intervallo di indirizzi.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>Invertire la geocodifica e filtrare per un tipo di entità geography

Quando si esegue una ricerca di geocodifica inversa nell' [API di ricerca inversa dell'indirizzo](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), il servizio può restituire poligoni per le aree amministrative.Per restringere la ricerca a specifici tipi di entità geography `entityType` , includere il parametro nelle richieste. 

La risposta risultante contiene l'ID geografia e il tipo di entità di cui è stata trovata una corrispondenza. Se si specifica più di un'entità, l'endpoint restituisce l' *entità più piccola disponibile*. È possibile usare l'ID Geometry restituito per ottenere la geometria della geografia tramite il [servizio poligono di ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

#### <a name="sample-request"></a>Richiesta di esempio

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>Risposta

```JSON
{
    "summary": {
        "queryTime": 14,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="set-the-results-language"></a>Impostazione della lingua dei risultati

Usare il `language` parametro per impostare la lingua per i risultati della ricerca restituiti. Se la richiesta non imposta la lingua, per impostazione predefinita servizio di ricerca usa la lingua più comune del paese o dell'area geografica. Quando non sono disponibili dati nella lingua specificata, viene utilizzata la lingua predefinita. 

Per altre informazioni, vedere le [lingue supportate di Azure Maps](https://docs.microsoft.com/azure/azure-maps/supported-languages).


### <a name="use-predictive-mode-automatic-suggestions"></a>Usare la modalità predittiva (suggerimenti automatici)

Per trovare più corrispondenze per le query parziali `typeahead` , impostare `true`il parametro su. Questa query viene interpretata come input parziale e la ricerca entra in modalità predittiva. Se non si imposta il `typeahead` parametro su `true`, il servizio presuppone che tutte le informazioni rilevanti siano state passate.

Nella query di esempio seguente viene eseguita una query per il servizio di ricerca dell'indirizzo per *Microso*. In questo caso `typeahead` , il parametro `true`è impostato su. La risposta indica che il servizio di ricerca ha interpretato la query come query parziale. La risposta contiene i risultati per una query suggerita automaticamente.

#### <a name="sample-query"></a>Query di esempio

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>Risposta

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="encode-a-uri-to-handle-special-characters"></a>Codificare un URI per la gestione di caratteri speciali 

Per trovare gli indirizzi tra le vie, è necessario codificare l'URI per gestire i caratteri speciali nell'indirizzo. Si consideri questo esempio di indirizzo: *1 Avenue & Union Street, Seattle*. In questo caso, codificare il`&`carattere e commerciale () prima di inviare la richiesta. 

Si consiglia di codificare i dati di tipo carattere in un URI. In un URI si codificano tutti i caratteri usando un segno di percentuale`%`() e un valore esadecimale di due caratteri che corrisponde al codice UTF-8 dei caratteri.

#### <a name="usage-examples"></a>Esempi di utilizzo

Inizia con questo indirizzo:

```
query=1st Avenue & E 111th St, New York
```

Codificare l'indirizzo:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

È possibile utilizzare i metodi seguenti.

JavaScript o TypeScript:
```Javascript
encodeURIComponent(query)
```

C# o Visual Basic: 
```csharp
Uri.EscapeDataString(query)
```

Java:
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python:
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++:
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP:
```PHP
urlencode(query)
```

Ruby
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Passare
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>Procedure consigliate per la ricerca di un PDI

In una ricerca di un PDI è possibile richiedere i risultati dei punti di interesse in base al nome. Ad esempio, è possibile cercare un'attività in base al nome. 

Si consiglia vivamente di utilizzare il parametro `countrySet` per specificare i paesi in cui l'applicazione necessita di copertura. Il comportamento predefinito prevede la ricerca nell'intero mondo. Questa ricerca generale può restituire risultati non necessari e la ricerca potrebbe richiedere molto tempo.

### <a name="brand-search"></a>Ricerca del marchio

Per migliorare la pertinenza dei risultati e delle informazioni nella risposta, una risposta di ricerca di un PDI include informazioni sul marchio. È possibile usare queste informazioni per analizzare ulteriormente la risposta.

In una richiesta, è possibile inviare un elenco delimitato da virgole di nomi di marchi. Usare l'elenco per limitare i risultati a marchi specifici impostando il `brandSet` parametro. Nell'elenco, l'ordine degli elementi non è rilevante. Quando si forniscono più elenchi di marchi, i risultati restituiti devono appartenere ad almeno uno degli elenchi.

Per esplorare la ricerca del marchio, viene ora eseguita una richiesta di ricerca di una [categoria](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) . Nell'esempio seguente vengono cercate le stazioni di gas vicino al campus Microsoft a Redmond, Washington. La risposta Mostra le informazioni sul marchio per ogni PDI che è stato restituito.

#### <a name="sample-query"></a>Query di esempio

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>Risposta

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Ricerca aeroporto

Usando l'API di ricerca di un PDI, è possibile cercare gli aeroporti usando il codice ufficiale. Ad esempio, è possibile usare *Sea* per trovare l'aeroporto internazionale Seattle-Tacoma: 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Ricerca nelle vicinanze

Per recuperare i risultati dei punti di interesse in una posizione specifica, è possibile provare a usare l' [API di ricerca vicina](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby). L'endpoint restituisce solo i risultati dei punti di interesse. Non accetta un parametro di query di ricerca. 

Per limitare i risultati, è consigliabile impostare il raggio.

## <a name="understanding-the-responses"></a>Informazioni sulle risposte

Per trovare un indirizzo a Seattle, effettuare una richiesta di ricerca di indirizzi all'servizio di ricerca mappe di Azure. Nell'URL della richiesta seguente si imposta il `countrySet` parametro su `US` per cercare l'indirizzo negli Stati Uniti.

### <a name="sample-query"></a>Query di esempio

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>Tipi di risultati supportati

* **Indirizzo punto**: punta su una mappa con un indirizzo specifico con un nome e un numero via. L'indirizzo del punto fornisce il livello più elevato di accuratezza per gli indirizzi. 

* **Intervallo di indirizzi**: intervallo di punti di indirizzo interpolati dall'inizio e dalla fine della strada.  

* **Geografia**: aree di una mappa che rappresentano divisioni amministrative di una terra, ad esempio paese, stato o città. 

* **PDI**: punta a una mappa che vale la pena di prestare attenzione e che potrebbe essere interessante.

* **Via**: strade sulla mappa. Gli indirizzi vengono risolti in base alle coordinate di latitudine e longitudine della strada che contiene l'indirizzo. Il numero civico potrebbe non essere elaborato. 

* **Cross Street**: intersezione. Tra le strade sono rappresentate le giunzioni in cui due strade si intersecano.

### <a name="response"></a>Risposta

Esaminiamo la struttura della risposta. Nella risposta che segue, i tipi degli oggetti risultato sono diversi. Se si osserva con attenzione, vengono visualizzati tre tipi di oggetti risultato:

* Indirizzo punto
* Street
* Tra le strade

Si noti che la ricerca di indirizzi non restituisce POIs.  

Il `Score` parametro per ogni oggetto Response indica il modo in cui il Punteggio di corrispondenza è correlato ai punteggi di altri oggetti nella stessa risposta. Per ulteriori informazioni sui parametri dell'oggetto Response, vedere [ottenere un indirizzo di ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress).

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometry

Un tipo di risposta *Geometry* può includere l'ID Geometry restituito nell' `dataSources` oggetto in `geometry` e. `id` Ad esempio, è possibile usare il [servizio poligono di ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) per richiedere i dati geometrici in un formato GeoJSON. Usando questo formato, è possibile ottenere una struttura della città o dell'aeroporto per un set di entità. È quindi possibile usare questi dati limite per [configurare una recinzione](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) geometrica o un punto di [ricerca all'interno della geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


Le risposte per l'API di [ricerca dell'indirizzo](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) o l'API [fuzzy di ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) possono includere l'ID Geometry restituito nell' `dataSources` oggetto in `geometry` e `id`:


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere:

> [!div class="nextstepaction"]
> [Come creare mappe di Azure servizio di ricerca richieste](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

> [!div class="nextstepaction"]
> [Documentazione dell'API servizio di ricerca](https://docs.microsoft.com/rest/api/maps/search)
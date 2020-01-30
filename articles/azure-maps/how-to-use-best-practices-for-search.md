---
title: Eseguire ricerche in modo efficiente usando le mappe di Azure servizio di ricerca | Mappe Microsoft Azure
description: Informazioni su come applicare le procedure consigliate per il servizio di ricerca usando mappe di Microsoft Azure
author: walsehgal
ms.author: v-musehg
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 68c7408f13027ded7beaabf46fb663217a90c52b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845761"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Procedure consigliate per l'uso di mappe di Azure servizio di ricerca

Azure Maps [servizio di ricerca](https://docs.microsoft.com/rest/api/maps/search) include API con diverse funzionalità. Ad esempio, l'API di ricerca di indirizzi viene usata per cercare punti di interesse o dati in una posizione specifica. Questo articolo illustra le procedure audio da applicare quando si chiamano i dati dai servizi di ricerca di Azure maps. Si apprenderà come:

* Query di compilazione per restituire corrispondenze rilevanti
* Limitare i risultati della ricerca
* Informazioni sulle differenze tra i vari tipi di risultati
* Leggere la struttura di risposta di ricerca degli indirizzi


## <a name="prerequisites"></a>Prerequisiti

Per eseguire chiamate alle API del servizio Maps, sono necessari un account Azure Maps e una chiave. Se necessario, seguire le istruzioni in [creare un account](quick-demo-map-app.md#create-an-account-with-azure-maps) e [ottenere una chiave primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account). Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](./how-to-manage-authentication.md).

> [!Tip]
> Per eseguire una query sul servizio di ricerca, è possibile usare l' [app post](https://www.getpostman.com/apps) per compilare chiamate REST oppure è possibile usare qualsiasi ambiente di sviluppo API preferito.


## <a name="best-practices-for-geocoding-address-search"></a>Procedure consigliate per la geocodifica (Ricerca indirizzi)

Quando si cerca un indirizzo completo o parziale usando mappe di Azure servizio di ricerca, l'API legge le parole chiave dalla query di ricerca e restituisce le coordinate di longitudine e latitudine dell'indirizzo. Questo processo è denominato geocodifica. La capacità di geocodifica in un paese dipende dalla copertura dei dati stradali e dalla precisione di geocodifica del servizio di geocodifica.

Per altre informazioni sulle funzionalità di geocodifica di Azure Maps per paese/area geografica, vedere [code coverage](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) .

### <a name="limit-search-results"></a>Limitare i risultati della ricerca

 L'API di ricerca di mappe di Azure può aiutare a limitare i risultati della ricerca in modo appropriato, in modo da poter visualizzare i dati rilevanti per gli utenti.

   > [!Note]
   > Non tutti i parametri supportati per le API di ricerca sono elencati di seguito

   **Risultati della ricerca con polarizzazione geografica**

   Per la polarizzazione geografica dei risultati nell'area pertinente per l'utente, è necessario aggiungere sempre il massimo possibile input della posizione dettagliata. Per limitare i risultati della ricerca, è consigliabile aggiungere i tipi di input seguenti:

   1. Impostare il parametro `countrySet`, ad esempio "US, FR". Il comportamento di ricerca predefinito prevede la ricerca nell'intero mondo, restituendo potenzialmente risultati non necessari. Se la query dispone del parametro `countrySet`, la ricerca può restituire risultati non accurati. Ad esempio, la ricerca di una città denominata **Bellevue** restituirà risultati da Stati Uniti e Francia, dal momento che sono presenti città denominate **Bellevue** in brodo Francia e Stati Uniti.

   2. È possibile utilizzare i parametri `btmRight` e `topleft` per impostare il rettangolo di delimitazione, in modo da limitare la ricerca a un'area specifica della mappa.

   3. Per influenzare l'area di pertinenza per i risultati, è possibile definire il `lat`e `lon` i parametri delle coordinate e impostare il raggio dell'area di ricerca usando il parametro `radius`.


   **Parametri di ricerca fuzzy**
   
  L' [API ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) di Azure Maps è il servizio consigliato da usare quando non si conoscono gli input degli utenti per una query di ricerca. L'API combina la ricerca di punti di interesse e la geocodifica in una *ricerca a riga singola*canonica.

   1. Il `minFuzzyLevel` e `maxFuzzyLevel` consentono di restituire corrispondenze rilevanti anche quando i parametri di query non corrispondono esattamente alle informazioni desiderate. Per ottenere prestazioni e ridurre i risultati insoliti, le query di ricerca predefinite `minFuzzyLevel=1` e `maxFuzzyLevel=2`. Si prenda ad esempio un termine di ricerca "restrant", che viene abbinato a "Restaurant" quando il `maxFuzzyLevel` è impostato su 2. È possibile eseguire l'override dei livelli fuzzy predefiniti in base alle esigenze.  

   2. È anche possibile classificare in ordine di priorità il set esatto di tipi di risultato da restituire utilizzando il parametro `idxSet`. A questo scopo, è possibile inviare un elenco delimitato da virgole di indici; l'ordine degli elementi non è rilevante. Sono supportati gli indici seguenti:

       * `Addr` - **intervalli di indirizzi**: per alcune strade sono presenti punti di indirizzo, che vengono interpolati dall'inizio e dalla fine della strada. Questi punti sono rappresentati come intervalli di indirizzi.
       * `Geo` - **geografie**: aree in una mappa che rappresentano la divisione amministrativa di una terra, ovvero paese, stato, città.
       * `PAD`**indirizzo punto**di  - : punta a una mappa in cui è possibile trovare un indirizzo specifico con un nome e un numero via in un indice, ad esempio, Soquel DR 2501. Questo valore idxSet è il livello più elevato di accuratezza disponibile per gli indirizzi.  
       * `POI` - **punti di interesse**: punta a una mappa che vale la pena di prestare attenzione e potrebbe essere interessante.  [Ottenere l'indirizzo di ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) non restituisce i pois.  
       * `Str` - **strade**: rappresentazione delle strade sulla mappa.
       * `XStr` - **tra le strade o le intersezioni**: rappresentazione di giunzioni; luoghi in cui due strade si intersecano.


       **Esempi di utilizzo**:

       * idxSet = PDI (Cerca solo punti di interesse) 

       * idxSet = PAD, addr (solo indirizzi di ricerca, PAD = indirizzo punto, addr = intervallo di indirizzi)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Filtro del tipo di entità geografica e geografico inverso

Quando si esegue una ricerca di geocodifica inversa con l' [API Reverse dell'indirizzo di ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), il servizio ha la possibilità di restituire poligoni per le aree amministrative. Specificando il parametro `entityType` nella richiesta, è possibile restringere la ricerca dei tipi di entità geography specificati. La risposta risultante conterrà l'ID geografia e il tipo di entità corrispondente. Se si specifica più di un'entità, endpoint restituirà l' **entità più piccola disponibile**. L'ID Geometry restituito può essere usato per ottenere la geometria di tale Geografia tramite il [servizio Get Polygon](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Richiesta di esempio:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Risposta:**

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
                },
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

### <a name="search-results-language"></a>Lingua dei risultati della ricerca

Il `language` parametro consente di selezionare la lingua dei risultati restituiti dall'API. Se la lingua non è impostata nella richiesta, per impostazione predefinita il servizio di ricerca viene impostato automaticamente sulla lingua più comune nel paese. Inoltre, quando i dati nella lingua specificata non sono disponibili, viene utilizzata la lingua predefinita. Per un elenco delle lingue supportate, vedere le [lingue](https://docs.microsoft.com/azure/azure-maps/supported-languages) supportate da Azure Maps Services per paese/area geografica.


### <a name="predictive-mode-autosuggest"></a>Modalità predittiva (suggerimenti automatici)

Per trovare più corrispondenze per le query parziali, `typeahead` parametro deve essere impostato su' true '. La query verrà interpretata come input parziale e la ricerca entrerà in modalità predittiva. In caso contrario, il servizio presuppone che tutte le informazioni rilevanti siano state passate.

Nella query di esempio riportata di seguito, è possibile osservare che viene eseguita una query sul servizio Address di ricerca per "Microso" con il parametro `typeahead` impostato su **true**. Se si osserva la risposta, è possibile osservare che il servizio di ricerca ha interpretato la query come query parziale. La risposta contiene i risultati per la query suggerita in modo automatico.

**Query di esempio:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**Risposta:**

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


### <a name="uri-encoding-to-handle-special-characters"></a>Codifica URI per la gestione di caratteri speciali 

Per trovare gli indirizzi tra le vie, è necessario codificare l'URI per gestire i caratteri speciali nell'indirizzo. Si consideri questo esempio di indirizzo: "1st Avenue & Union Street, Seattle". Il carattere speciale "&" deve essere codificato prima di inviare la richiesta. È consigliabile codificare i dati di tipo carattere in un URI, in cui tutti i caratteri sono codificati usando un carattere '%' e un valore esadecimale di due caratteri corrispondente al carattere UTF-8.

**Esempi di utilizzo**:

Ottenere l'indirizzo di ricerca:

```
query=1st Avenue & E 111th St, New York
```

 deve essere codificato come:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


Ecco i diversi metodi da usare per le diverse lingue: 

JavaScript/TypeScript:
```Javascript
encodeURIComponent(query)
```

C#/VB:
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


## <a name="best-practices-for-poi-search"></a>Procedure consigliate per la ricerca nei punti di interesse

La ricerca di punti di interesse (PDI) consente di richiedere i risultati dei punti di interesse in base al nome, ad esempio ricercare il nome per il business. È consigliabile usare il parametro `countrySet` per specificare i paesi in cui l'applicazione necessita di copertura, perché il comportamento predefinito consiste nel cercare l'intero mondo, restituendo potenzialmente i risultati non necessari e/o comportando tempi di ricerca più lunghi.

### <a name="brand-search"></a>Ricerca del marchio

Per migliorare la pertinenza dei risultati e le informazioni nella risposta, la risposta alla ricerca punto di interesse (PDI) include le informazioni sul marchio che possono essere usate ulteriormente per analizzare la risposta.

È anche possibile inviare un elenco delimitato da virgole di nomi di marchi nella richiesta. È possibile utilizzare l'elenco per limitare i risultati a marchi specifici utilizzando il parametro `brandSet`. L'ordine degli elementi non è rilevante. Se vengono forniti più marchi, vengono restituiti solo i risultati che appartengono almeno a uno degli elenchi specificati.

Si effettuerà una richiesta di ricerca di una [categoria](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) di punti di interesse per le stazioni di gas vicino a Microsoft Campus (Redmond, WA). Se si osserva la risposta, è possibile visualizzare le informazioni sul marchio per ogni PDI restituito.


**Query di esempio:**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

**Risposta:**

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

La ricerca di punti di interesse supporta la ricerca di aeroporti usando i codici ufficiali dell'aeroporto. Ad esempio, **Sea** (Seattle-Tacoma International Airport). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Ricerca nelle vicinanze

Per recuperare solo i risultati dei punti di interesse in una posizione specifica, l' [API di ricerca vicina](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) potrebbe essere la scelta corretta. Questo endpoint restituisce solo i risultati dei punti di interesse e non accetta un parametro di query di ricerca. Per limitare i risultati, è consigliabile impostare il raggio.

## <a name="understanding-the-responses"></a>Informazioni sulle risposte

Si effettuerà una richiesta di ricerca di indirizzi al [servizio ricerca](https://docs.microsoft.com/rest/api/maps/search) di Azure Maps per un indirizzo a Seattle. Se si osserva attentamente l'URL della richiesta riportato di seguito, è stato impostato il parametro **`countrySet` per cercare** l'indirizzo nella Stati Uniti di America.

**Query di esempio:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

Si osservi inoltre la struttura della risposta riportata di seguito. I tipi di risultato degli oggetti risultato nella risposta sono diversi. Osservando con attenzione, sono disponibili tre diversi tipi di oggetti risultato, ovvero "indirizzo punto", "via" e "traversa". Si noti che la ricerca di indirizzi non restituisce i POIs. Il parametro `Score` per ogni oggetto Response indica il punteggio corrispondente relativo ai punteggi di altri oggetti nella stessa risposta. Per ulteriori informazioni sui parametri degli oggetti risposta, vedere [ottenere l'indirizzo di ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) .

**Tipi di risultato supportati:**

* **Indirizzo punto:** Punta a una mappa con un indirizzo specifico con un nome e un numero via. Livello massimo di accuratezza disponibile per gli indirizzi. 

* **Intervallo di indirizzi:**  Per alcune strade sono presenti punti di indirizzo interpolati dall'inizio e dalla fine della strada; questi punti sono rappresentati come intervalli di indirizzi. 

* **Geografia:** Aree in una mappa che rappresentano la divisione amministrativa di una terra, ovvero paese, stato, città. 

* **PDI-(punti di interesse):** Punta a una mappa che vale la pena di prestare attenzione e potrebbe essere interessante.

* **Via:** Rappresentazione delle strade sulla mappa. Gli indirizzi vengono risolti nella coordinata di Latitudine/Longitudine della strada che contiene l'indirizzo. Il numero civico può non essere elaborato. 

* **Tra le strade:** Intersezioni. Rappresentazioni di giunzioni; luoghi in cui due strade si intersecano.

**Risposta:**

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

Quando il tipo di risposta è **Geometry**, può includere l'ID Geometry restituito nell'oggetto **DataSources** in "Geometry" e "ID". [Get Polygon Service](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) consente, ad esempio, di richiedere i dati geometrici in formato GeoJSON. Ad esempio una struttura City o Airport per un set di entità. È possibile usare questi dati limite per la [geoschermatura](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) o per [la ricerca all'interno della geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


Le risposte all' [indirizzo di ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) o all'API [fuzzy di ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) possono includere l' **ID Geometry** restituito nell'oggetto DataSources in "Geometry" e "ID".


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Passaggi successivi

* Informazioni [su come compilare richieste di servizio di ricerca di Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Esplorare la documentazione dell' [API del servizio di ricerca](https://docs.microsoft.com/rest/api/maps/search)Maps di Azure. 

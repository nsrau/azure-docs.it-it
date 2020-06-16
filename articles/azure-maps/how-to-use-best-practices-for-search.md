---
title: Procedure consigliate per il servizio di ricerca di Mappe di Azure | Mappe di Microsoft Azure
description: Informazioni su come applicare le procedure consigliate quando si usa il servizio di ricerca di Mappe di Microsoft Azure.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: ea44355795f0685f42de1306e979707f34d8f142
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83742761"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Procedure consigliate per il servizio di ricerca di Mappe di Azure

Il [servizio di ricerca](https://docs.microsoft.com/rest/api/maps/search) di Mappe di Azure include API che offrono varie funzionalità. Ad esempio, l'API di ricerca di indirizzi può trovare punti di interesse o dati nei dintorni di una posizione specifica. 

Questo articolo descrive come applicare procedure efficaci quando si chiamano dati dal servizio di ricerca di Mappe di Azure. Si apprenderà come:

* Creare query per restituire corrispondenze rilevanti.
* Limitare i risultati della ricerca.
* Identificare le differenze tra i tipi di risultati.
* Interpretare la struttura di ricerca e risposta degli indirizzi.

## <a name="prerequisites"></a>Prerequisiti

Per effettuare chiamate alle API del servizio Mappe di Azure, sono necessari un account Mappe di Azure e una chiave. Per altre informazioni, vedere [Creare un account](quick-demo-map-app.md#create-an-account-with-azure-maps) e [Ottenere una chiave primaria](quick-demo-map-app.md#get-the-primary-key-for-your-account). 

Per altre informazioni sull'autenticazione in Mappe di Azure, vedere [Gestire l'autenticazione in Mappe di Azure](./how-to-manage-authentication.md).

> [!TIP]
> Per eseguire query sul servizio di ricerca, è possibile usare l'[app Postman](https://www.getpostman.com/apps) per creare chiamate REST. In alternativa, è possibile usare qualsiasi ambiente di sviluppo API preferito.

## <a name="best-practices-to-geocode-addresses"></a>Procedure consigliate per la codifica geografica degli indirizzi

Quando si cerca un indirizzo completo o parziale usando il servizio di ricerca di Mappe di Azure, l'API legge le parole chiave dalla query di ricerca. Restituisce quindi le coordinate di longitudine e latitudine dell'indirizzo. Questo processo è noto come *geocodifica*. 

La capacità di implementare la geocodifica in un paese o un'area dipende dalla disponibilità di dati stradali e dalla precisione del servizio di geocodifica. Per altre informazioni sulle funzionalità di geocodifica di Mappe di Azure per paese o area, vedere [Copertura della geocodifica](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage).

### <a name="limit-search-results"></a>Limitare i risultati della ricerca

 L'API di ricerca di Mappe di Azure può aiutare a limitare i risultati della ricerca in modo appropriato. È possibile limitare i risultati in modo da visualizzare i dati pertinenti per gli utenti.

> [!NOTE]
> Le API di ricerca supportano più parametri rispetto a quelli presentati in questo articolo.

#### <a name="geobiased-search-results"></a>Risultati della ricerca limitati geograficamente

Per limitare geograficamente i risultati in base all'area pertinente per l'utente, aggiungere sempre il maggior numero possibile di dettagli sulla località. Può essere utile limitare i risultati della ricerca specificando alcuni tipi di input:

* Impostare il parametro `countrySet`. Ad esempio, è possibile impostarlo su `US,FR`. Per impostazione predefinita, l'API cerca in tutto il mondo e può quindi restituire risultati non necessari. Se la query non include un parametro `countrySet`, la ricerca può restituire risultati non accurati. Ad esempio, la ricerca di una città denominata *Bellevue* restituisce risultati dagli Stati Uniti e dalla Francia, perché in entrambi i paesi esiste una città con questo nome.

* È possibile usare i parametri `btmRight` e `topleft` per impostare il riquadro delimitatore. Questi parametri limitano la ricerca a un'area specifica sulla mappa.

* Per influire sull'area di pertinenza per i risultati, definire i parametri delle coordinate `lat` e `lon`. Usare il parametro `radius` per impostare il raggio dell'area di ricerca.


#### <a name="fuzzy-search-parameters"></a>Parametri di ricerca fuzzy

È consigliabile usare l'[API di ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) di Mappe di Azure quando non si conoscono gli input utente per una query di ricerca. L'API combina la ricerca di punti di interesse e la geocodifica in una normale *ricerca a riga singola*: 

* I parametri `minFuzzyLevel` e `maxFuzzyLevel` consentono di restituire corrispondenze pertinenti anche quando i parametri di query non corrispondono esattamente alle informazioni cercate dall'utente. Per ottimizzare le prestazioni e ridurre i risultati insoliti, impostare le query di ricerca sui valori predefiniti `minFuzzyLevel=1` e `maxFuzzyLevel=2`. 

    Ad esempio, quando il parametro `maxFuzzyLevel` è impostato su 2, il termine di ricerca *ristrant* viene associato al termine *ristorante*. È possibile sostituire i livelli fuzzy predefiniti, se necessario. 

* Usare il parametro `idxSet` per classificare in ordine di priorità il set esatto di tipi di risultati. Per classificare in ordine di priorità un set esatto di risultati, è possibile inviare un elenco di indici delimitati da virgole. Nell'elenco l'ordine degli elementi non è rilevante. Mappe di Azure supporta gli indici seguenti:

* `Addr` - **Intervalli di indirizzi**: punti di indirizzo interpolati dall'inizio e dalla fine della strada. Questi punti sono rappresentati come intervalli di indirizzi.
* `Geo` - **Aree geografiche**: divisioni amministrative del territorio. Un'area geografica può essere, ad esempio, un paese, uno stato o una città.
* `PAD` - **Indirizzi di punti**: indirizzi che includono un nome e un numero di via. Gli indirizzi di punti si trovano in un indice. Un esempio è *Via Roma 18*. Un indirizzo di punti fornisce il livello maggiore di accuratezza disponibile per gli indirizzi.  
* `POI` - **Punti di interesse**: punti su una mappa considerati meritevoli di attenzione o che possono essere interessanti. L'[API di ricerca di indirizzi](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) non restituisce punti di interesse.  
* `Str` - **Vie**: strade sulla mappa.
* `XStr` - **Incroci stradali o intersezioni**: punti di congiunzione o luoghi in cui si intersecano due o più strade.


#### <a name="usage-examples"></a>Esempi di utilizzo

* `idxSet=POI`: ricerca solo di punti di interessi. 

* `idxSet=PAD,Addr`: ricerca solo di indirizzi. `PAD` indica l'indirizzo del punto e `Addr` indica l'intervallo di indirizzi.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>Geocodifica inversa e filtro per un tipo di entità geografia

Quando si esegue una ricerca di geocodifica inversa nell'[API di ricerca inversa di indirizzi](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), il servizio può restituire poligoni per le aree amministrative. Per limitare la ricerca a specifici tipi di entità geografia, includere il parametro `entityType` nelle richieste. 

La risposta risultante contiene l'ID area geografica e il tipo di entità di cui è stata trovata una corrispondenza. Se si specifica più di un'entità, l'endpoint restituisce l'*entità più piccola disponibile*. È possibile usare l'ID geometria restituito per ottenere la geometria dell'area geografica tramite il [servizio di ricerca di poligoni](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

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

### <a name="set-the-results-language"></a>Impostare la lingua dei risultati

Usare il parametro `language` per impostare la lingua per i risultati della ricerca restituiti. Se la richiesta non imposta la lingua, per impostazione predefinita il servizio di ricerca usa la lingua più comune del paese o dell'area geografica. Quando non sono disponibili dati nella lingua specificata, viene usata la lingua predefinita. 

Per altre informazioni, vedere [Lingue supportate da Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/supported-languages).


### <a name="use-predictive-mode-automatic-suggestions"></a>Usare la modalità predittiva (suggerimenti automatici)

Per trovare più corrispondenze per le query parziali, impostare il parametro `typeahead` su `true`. Questa query viene interpretata come input parziale e la ricerca entra in modalità predittiva. Se non si imposta il parametro `typeahead` su `true`, il servizio presuppone che siano state passate tutte le informazioni pertinenti.

Nella query di esempio seguente viene eseguita una query sul servizio di ricerca di indirizzi per *Microso*. Qui il parametro `typeahead` è impostato su `true`. La risposta mostra che il servizio di ricerca ha interpretato la query come parziale. La risposta contiene i risultati per una query suggerita automaticamente.

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


### <a name="encode-a-uri-to-handle-special-characters"></a>Codificare un URI per gestire i caratteri speciali 

Per trovare indirizzi di incroci, è necessario codificare l'URI in modo da gestire i caratteri speciali nell'indirizzo. Osservare questo esempio di indirizzo: *1st Avenue & Union Street, Seattle*. In questo caso, codificare il carattere di e commerciale (`&`) prima di inviare la richiesta. 

È consigliabile codificare i dati di tipo carattere in un URI. In un URI è necessario codificare tutti i caratteri usando un simbolo di percentuale (`%`) e un valore esadecimale a due caratteri corrispondente al codice UTF-8 dei caratteri.

#### <a name="usage-examples"></a>Esempi di utilizzo

Per iniziare, osservare questo indirizzo:

```
query=1st Avenue & E 111th St, New York
```

Codificare l'indirizzo:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

È possibile usare i metodi seguenti.

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

Ruby:
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Go:
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>Procedure consigliate per la ricerca di punti di interesse

In una ricerca di punti di interesse è possibile richiedere risultati costituiti da punti di interesse per nome. Ad esempio, è possibile cercare un'azienda per nome. 

È assolutamente consigliabile usare il parametro `countrySet` per specificare paesi o aree in cui l'applicazione richiede copertura. Il comportamento predefinito consiste nel cercare in tutto il mondo. Questa ricerca più ampia può restituire risultati non necessari e richiedere molto tempo.

### <a name="brand-search"></a>Ricerca di marchi

Per migliorare la pertinenza dei risultati e delle informazioni nella risposta, una risposta a una ricerca di punti di interesse include informazioni sul marchio. È possibile usare queste informazioni per analizzare ulteriormente la risposta.

In una richiesta è possibile inviare un elenco di nomi di marchi separati da virgole. Usare l'elenco per limitare i risultati a marchi specifici impostando il parametro `brandSet`. Nell'elenco l'ordine degli elementi non è importante. Quando si forniscono più elenchi di marchi, i risultati restituiti devono appartenere ad almeno uno degli elenchi.

Per esplorare la ricerca di marchi, viene creata qui una richiesta di [ricerca di una categoria di punti di interessi](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory). Nell'esempio seguente vengono cercate le stazioni di rifornimento vicino al campus Microsoft a Redmond, Washington. La risposta mostra informazioni sul marchio per ogni punto di interesse restituito.

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


### <a name="airport-search"></a>Ricerca di aeroporti

Usando l'API di ricerca di punti di interesse, è possibile cercare aeroporti usando il rispettivo codice ufficiale. Ad esempio, è possibile usare *SEA* per trovare l'aeroporto internazionale di Seattle-Tacoma: 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Ricerca nelle vicinanze

Per recuperare i risultati dei punti di interesse in una località specifica, è possibile provare a usare l'[API di ricerca nelle vicinanze](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby). L'endpoint restituisce solo risultati di punti di interesse. Non accetta un parametro di query di ricerca. 

Per limitare i risultati, è consigliabile impostare il raggio.

## <a name="understanding-the-responses"></a>Informazioni sulle risposte

Si supponga di voler trovare un indirizzo a Seattle inviando una richiesta di ricerca di indirizzi al servizio di ricerca di Mappe di Azure. Nell'URL della richiesta seguente si imposta il parametro `countrySet` su `US` per cercare l'indirizzo negli Stati Uniti.

### <a name="sample-query"></a>Query di esempio

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>Tipi di risultati supportati

* **Punto di interesse**: punti su una mappa che hanno un indirizzo specifico con un nome e un numero di strada. Un indirizzo di punti fornisce il livello maggiore di accuratezza per gli indirizzi. 

* **Intervallo di indirizzi**: intervallo di punti di indirizzo interpolati dall'inizio e dalla fine della strada.  

* **Geography**: aree su una mappa che rappresentano le divisioni amministrative di un territorio, ad esempio paese/area geografica, stato o città. 

* **Punto di interesse**: punti su una mappa considerati meritevoli di attenzione o che possono essere interessanti.

* **Via**: strade sulla mappa. Gli indirizzi vengono risolti in base alle coordinate di latitudine e longitudine della strada che contiene l'indirizzo. Il numero civico può non essere elaborato. 

* **Incrocio**: intersezioni. Gli incroci rappresentano i punti di congiunzione in cui si intersecano due strade.

### <a name="response"></a>Risposta

Viene ora esaminata la struttura della risposta. Nella risposta seguente i tipi degli oggetti risultato sono diversi. Se si osserva con attenzione, si noteranno tre tipi di oggetti risultato:

* Punto di interesse
* Via
* Incrocio

Si noti che la ricerca di indirizzi non restituisce punti di interesse.  

Il parametro `Score` per ogni oggetto risposta indica la correlazione tra il punteggio corrispondente e i punteggi di altri oggetti nella stessa risposta. Per altre informazioni sui parametri degli oggetti risposta, vedere [Get Search Address](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress).

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

Una risposta di tipo *geometria* può includere l'ID geometria restituito nell'oggetto `dataSources` in `geometry` e `id`. Ad esempio, è possibile usare il [servizio di ricerca di poligoni](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) per richiedere i dati geometrici in un formato GeoJSON. Usando questo formato, è possibile ottenere una struttura di città o aeroporto per un set di entità. È quindi possibile usare questi dati limite per [configurare un recinto virtuale](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) o [cercare punti di interesse all'interno della geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


Le risposte per l'API di [ricerca di indirizzi](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) o l'API di [ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) possono includere l'ID geometria restituito nell'oggetto `dataSources` in `geometry` e `id`:


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere:

> [!div class="nextstepaction"]
> [Come creare richieste al servizio di ricerca di Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

> [!div class="nextstepaction"]
> [Documentazione dell'API del servizio di ricerca](https://docs.microsoft.com/rest/api/maps/search)
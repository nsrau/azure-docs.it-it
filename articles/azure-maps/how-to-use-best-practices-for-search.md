---
title: Come eseguire ricerche in modo efficiente usando il servizio di ricerca di Azure Maps | Microsoft Docs
description: Informazioni su come usare le procedure consigliate per la ricerca tramite il servizio di ricerca Maps di Azure
author: walsehgal
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 25615ae8bc9bc8cadbe973f3a1859c2d43b067a9
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915569"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Procedure consigliate per l'uso di mappe di Azure servizio di ricerca

Azure Maps [servizio di ricerca](https://docs.microsoft.com/rest/api/maps/search) include API con diverse funzionalità, ad esempio dalla ricerca di indirizzi alla ricerca di dati punto di interesse (poi) intorno a una posizione specifica. In questo articolo si condivideranno le procedure consigliate per chiamare i dati tramite i servizi di ricerca di Azure maps. Si apprenderà come:

* Query di compilazione per restituire corrispondenze rilevanti
* Limitare i risultati della ricerca
* Informazioni sulle differenze tra i vari tipi di risultati
* Leggere la struttura di risposta di ricerca degli indirizzi


## <a name="prerequisites"></a>Prerequisiti

Per eseguire chiamate alle API del servizio Mappe sono necessari un account di Mappe e una chiave. Per informazioni sulla creazione di un account, seguire le istruzioni in [gestire l'account](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) e seguire i passaggi in [ottenere la chiave primaria](./tutorial-search-location.md#getkey) per recuperare una chiave di sottoscrizione primaria per l'account.

> [!Tip]
> Per eseguire una query sul servizio di ricerca, è possibile usare l' [app post](https://www.getpostman.com/apps) per compilare chiamate REST oppure è possibile usare qualsiasi ambiente di sviluppo API preferito.


## <a name="best-practices-for-geocoding"></a>Procedure consigliate per la geocodifica

Quando si cerca un indirizzo completo o parziale usando le mappe di Azure servizio di ricerca, viene eseguito il termine di ricerca e vengono restituite le coordinate di longitudine e latitudine dell'indirizzo. Questo processo è denominato geocodifica. La capacità di geocodifica in un paese dipende dalla copertura dei dati stradali e dalla precisione di geocodifica del servizio di geocodifica.

Per altre informazioni sulle funzionalità di geocodifica di Azure Maps per paese/area geografica, vedere [code coverage](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) .

### <a name="limit-search-results"></a>Limitare i risultati della ricerca

   In questa sezione si apprenderà come usare le API di ricerca di Azure Maps per limitare i risultati della ricerca. 

   > [!Note]
   > Non tutte le API di ricerca supportano completamente i parametri elencati di seguito

   **Risultati della ricerca con polarizzazione geografica**

   Per la polarizzazione geografica dei risultati nell'area pertinente per l'utente, è necessario aggiungere sempre il massimo possibile input della posizione dettagliata. Per limitare i risultati della ricerca, è consigliabile aggiungere i tipi di input seguenti:

   1. Impostare il `countrySet` parametro, ad esempio "US, fr". Il comportamento di ricerca predefinito prevede la ricerca nell'intero mondo, restituendo potenzialmente risultati non necessari. Se la query non include `countrySet` il parametro, è possibile che la ricerca restituisca risultati non accurati. Ad esempio, la ricerca di una città denominata **Bellevue** restituirà risultati da Stati Uniti e Francia, dal momento che sono presenti città denominate **Bellevue** in Francia e negli Stati Uniti.

   2. È possibile usare i `btmRight` parametri `topleft` e per impostare il rettangolo di delimitazione per limitare la ricerca a un'area specifica sulla mappa.

   3. Per influenzare l'area di pertinenza per i risultati, è possibile definire i `lat`parametri `lon` della coordinata e e impostare il raggio dell'area di `radius` ricerca usando il parametro.


   **Parametri di ricerca fuzzy**

   1. `minFuzzyLevel` E`maxFuzzyLevel`, consentono di restituire corrispondenze rilevanti anche quando i parametri di query non corrispondono esattamente alle informazioni desiderate. La maggior parte delle query `minFuzzyLevel=1` di `maxFuzzyLevel=2` ricerca viene impostata automaticamente su e per ottenere prestazioni e ridurre i risultati anomali. Si prenda ad esempio un termine di ricerca "restrant", che viene abbinato a "Restaurant" quando `maxFuzzyLevel` è impostato su 2. È possibile eseguire l'override dei livelli fuzzy predefiniti in base alle esigenze della richiesta. 

   2. È inoltre possibile specificare il set esatto di tipi di risultato da restituire utilizzando il `idxSet` parametro. A questo scopo è possibile inviare un elenco delimitato da virgole di indici, l'ordine degli elementi non è rilevante. Gli indici supportati sono i seguenti:

       * `Addr` - **Intervalli di indirizzi**: Per alcune strade sono presenti punti di indirizzo interpolati dall'inizio e dalla fine della strada; questi punti sono rappresentati come intervalli di indirizzi.
       * `Geo` - **Aree geografiche**: Aree in una mappa che rappresentano la divisione amministrativa di una terra, ovvero paese, stato, città.
       * `PAD` - **Indirizzo punto**:  Punta a una mappa in cui è possibile trovare un indirizzo specifico con il nome e il numero della strada in un indice, ad esempio Soquel DR 2501. Si tratta del livello più elevato di accuratezza disponibile per gli indirizzi.  
       * `POI` - **Punti di interesse**: Punta a una mappa che vale la pena di prestare attenzione e potrebbe essere interessante.  [Ottenere l'indirizzo di ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) non restituisce i pois.  
       * `Str` - **Strade**: Rappresentazione delle strade sulla mappa.
       * `XStr` - **Strade/intersezioni**:  Rappresentazione di giunzioni; luoghi in cui due strade si intersecano.


       **Esempi di utilizzo**:

       * idxSet = PDI (Cerca solo punti di interesse) 

       * idxSet = PAD, addr (solo indirizzi di ricerca, PAD = indirizzo punto, addr = intervallo di indirizzi)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Filtro del tipo di entità geografica e geografico inverso

Quando si esegue una ricerca di geocodifica inversa con l' [API dell'indirizzo di ricerca inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), il servizio ha la possibilità di restituire poligoni per le aree amministrative. Specificando il parametro `entityType` nella richiesta, è possibile restringere la ricerca dei tipi di entità geography specificati. La risposta risultante conterrà l'ID geografia e il tipo di entità corrispondente. Se si specifica più di un'entità, endpoint restituirà l' **entità più piccola disponibile**. L'ID Geometry restituito può essere usato per ottenere la geometria di tale Geografia tramite il [servizio Get Polygon](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

**Richiesta di esempio:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Risposta:**

```JSON
{
    "summary": {
        "queryTime": 8,
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
                "freeformAddress": "Redmond, WA",
                "boundingBox": {
                    "northEast": "47.717105,-122.034537",
                    "southWest": "47.627016,-122.164998",
                    "entity": "position"
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

Il `language` parametro consente di impostare in cui devono essere restituiti i risultati della ricerca nella lingua. Se la lingua non è impostata nella richiesta, il servizio di ricerca viene automaticamente impostato sul linguaggio più comune nel paese. Inoltre, quando i dati nella lingua specificata non sono disponibili, viene utilizzata la lingua predefinita. Vedere le [lingue supportate](https://docs.microsoft.com/azure/azure-maps/supported-languages) per un elenco delle lingue supportate rispetto ai servizi di Azure Maps per paese/area geografica.


### <a name="predictive-mode-auto-suggest"></a>Modalità predittiva (suggerimento automatico)

Per trovare più corrispondenze per le query `typeahead` parziali, il parametro deve essere impostato su' true '. La query verrà interpretata come input parziale e la ricerca entrerà in modalità predittiva. In caso contrario, il servizio presuppone che tutte le informazioni rilevanti siano state passate.

Nella query di esempio riportata di seguito è possibile osservare che viene eseguita una query sul servizio Address di ricerca per "Microso" con il `typeahead` parametro impostato su **true**. Se si osserva la risposta, è possibile notare che il servizio di ricerca ha interpretato la query come query parziale e la risposta contiene i risultati per la query suggerita automaticamente.

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
        "queryTime": 25,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/10294417",
            "score": 2.594,
            "dist": 327.546040632591,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980526399,980528300",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63989,
                "lon": -122.12509
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309
                },
                "btmRightPoint": {
                    "lat": 47.64223,
                    "lon": -122.13061
                }
            }
        },
        ...,
        ...,
        ...,
        ...,
        {
            "type": "Street",
            "id": "US/STR/p0/9063400",
            "score": 2.075,
            "dist": 3655467.6406921702,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "postalCode": "28217",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28217",
                "countrySubdivisionName": "North Carolina"
            },
            "position": {
                "lat": 35.14279,
                "lon": -80.91814
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814
                },
                "btmRightPoint": {
                    "lat": 35.14279,
                    "lon": -80.91824
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>Codifica URI per la gestione di caratteri speciali 

Per trovare gli indirizzi tra le vie, ovvero "1st Avenue & Union Street, Seattle", il carattere speciale "&" deve essere codificato prima di inviare la richiesta. È consigliabile codificare i dati di tipo carattere in un URI, in cui tutti i caratteri sono codificati usando un carattere '%' e un valore esadecimale di due caratteri corrispondente al carattere UTF-8.

**Esempi di utilizzo**:

Ottenere l'indirizzo di ricerca:

```
query=1st Avenue & E 111th St, New York
```

 deve essere codificato come:

```
query"=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
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

Swift
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Passare
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>Procedure consigliate per la ricerca nei punti di interesse

La ricerca di punti di interesse (PDI) consente di richiedere i risultati dei punti di interesse in base al nome, ad esempio ricercare il nome per il business. È consigliabile usare il `countrySet` parametro per specificare i paesi in cui l'applicazione necessita di copertura, perché il comportamento predefinito consiste nel cercare l'intero mondo, restituendo potenzialmente risultati superflui e/o producendo tempi di ricerca più lunghi.

### <a name="brand-search"></a>Ricerca del marchio

Per migliorare la pertinenza dei risultati e le informazioni nella risposta, la risposta alla ricerca punto di interesse (PDI) include le informazioni sul marchio che possono essere usate ulteriormente per analizzare la risposta.

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
        "queryTime": 206,
        "numResults": 3,
        "offset": 0,
        "totalResults": 742169,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/245813",
            "score": 5.663,
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
                "url": "www.chevron.com",
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
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "2444 Bel Red Rd, Bellevue, WA 98007",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63223,
                        "lon": -122.13311
                    }
                }
            ]
        },
        ...,
        {
            "type": "POI",
            "id": "US/POI/p0/7727106",
            "score": 5.662,
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
                "url": "www.texaco.com/",
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
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980525511",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "15248 Bel Red Rd, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62826,
                        "lon": -122.13626
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

Si effettuerà una richiesta di ricerca di indirizzi al [servizio ricerca](https://docs.microsoft.com/rest/api/maps/search) di Azure Maps per un indirizzo a Seattle. Se si osserva attentamente l'URL della richiesta riportato di seguito, il `countrySet` parametro viene impostato su **US** per cercare l'indirizzo nel Stati Uniti di America.

**Query di esempio:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

Si osservi inoltre la struttura della risposta riportata di seguito. I tipi di risultato degli oggetti risultato nella risposta sono diversi. Osservando con attenzione, sono disponibili tre diversi tipi di oggetti risultato, ovvero "indirizzo punto", "via" e "traversa". Si noti che la ricerca di indirizzi non restituisce i POIs. Il `Score` parametro per ogni oggetto Response indica il punteggio corrispondente relativo ai punteggi di altri oggetti nella stessa risposta. Per ulteriori informazioni sui parametri degli oggetti risposta, vedere [ottenere l'indirizzo di ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) .

**Tipi di risultato supportati:**

* **Indirizzo punto:** Punta a una mappa con un indirizzo specifico con un nome e un numero via. Livello massimo di accuratezza disponibile per gli indirizzi. 

* **Intervallo di indirizzi:**  Per alcune strade sono presenti punti di indirizzo interpolati dall'inizio e dalla fine della strada; questi punti sono rappresentati come intervalli di indirizzi. 

* **Geografia** Aree in una mappa che rappresentano la divisione amministrativa di una terra, ovvero paese, stato, città. 

* **PDI-(punti di interesse):** Punta a una mappa che vale la pena di prestare attenzione e potrebbe essere interessante.

* **Strada** Rappresentazione delle strade sulla mappa. Gli indirizzi vengono risolti nella coordinata di Latitudine/Longitudine della strada che contiene l'indirizzo. Il numero civico può non essere elaborato. 

* **Tra le strade:** Intersezioni. Rappresentazioni di giunzioni; luoghi in cui due strade si intersecano.

**Risposta:**

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 129,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/43076024",
            "score": 9.894,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, South Lake Union, Lower Queen Anne",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "400 Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/2440854",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Westlake, South Lake Union",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "extendedPostalCode": "981094347,981094700,981094701,981094702",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62553,
                "lon": -122.33936
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62545,
                    "lon": -122.33861
                },
                "btmRightPoint": {
                    "lat": 47.62574,
                    "lon": -122.33974
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8450985",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109,98121",
                "extendedPostalCode": "981094991,981211117,981211237,981213206",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.61691,
                "lon": -122.35251
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61502,
                    "lon": -122.35041
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.35484
                }
            }
        },
        ...,
        ...,
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/3816818",
            "score": 6.759,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street & Valley Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62574,
                "lon": -122.33861
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62664,
                    "lon": -122.33994
                },
                "btmRightPoint": {
                    "lat": 47.62484,
                    "lon": -122.33728
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometria

Quando il tipo di risposta è **Geometry**, può includere l'ID Geometry restituito nell'oggetto **DataSources** in "Geometry" e "ID". [Get Polygon Service](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) consente, ad esempio, di richiedere i dati geometrici in formato GeoJSON, ad esempio una struttura City o Airport per un set di entità. È possibile usare questi dati limite per la [geoschermatura](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) o per [la ricerca all'interno della geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


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

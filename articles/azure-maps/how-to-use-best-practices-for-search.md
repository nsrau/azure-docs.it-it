---
title: Come eseguire una ricerca in modo efficiente utilizzando il servizio di ricerca di mappe di Azure | Microsoft Docs
description: Informazioni su come usare le procedure consigliate per la ricerca usando il servizio di ricerca di mappe di Azure
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9cb0f89b4a48d7139adb35dcef48c0115b005c57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65205615"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Le procedure consigliate per usare il servizio di ricerca mappe di Azure

Mappe di Azure [servizio di ricerca](https://docs.microsoft.com/rest/api/maps/search) include API con varie funzionalità, ad esempio, da ricerca di indirizzi per la ricerca dei dati di punto di interesse (punto di interesse) intorno a un percorso specifico. In questo articolo, si condividerà le procedure consigliate per chiamare i dati tramite servizi di ricerca mappe di Azure. Si apprenderà come:

* Compilazione di query per restituire corrispondenze pertinenti
* Limitare i risultati della ricerca
* Altre differenze tra i vari tipi di risultato
* Leggere la struttura di risposta di ricerca di indirizzi


## <a name="prerequisites"></a>Prerequisiti

Per eseguire chiamate alle API del servizio Mappe sono necessari un account di Mappe e una chiave. Per informazioni sulla creazione di un account e il recupero di una chiave, vedere [Come gestire l'account e le chiavi dell'account di Mappe di Azure](how-to-manage-account-keys.md).

> [!Tip]
> Per eseguire query sul servizio di ricerca, è possibile usare la [app Postman](https://www.getpostman.com/apps) compilare REST chiamate oppure è possibile usare qualsiasi ambiente di sviluppo di API che preferisci.


## <a name="best-practices-for-geocoding"></a>Le procedure consigliate per la Geocodifica

Quando esegue una ricerca per un indirizzo completo o parziale usando il servizio di ricerca di Azure esegue il mapping, accetta il termine di ricerca e restituisce le coordinate di longitudine e latitudine dell'indirizzo. Questo processo viene chiamato la geocodifica. La capacità di geocodifica in un paese dipende dalla copertura dei dati stradali e dalla precisione di geocodifica del servizio di geocodifica.

Visualizzare [copertura per la geocodifica](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) per saperne di più sulle funzionalità di geocodifica di mappe di Azure da paese/area geografica.

### <a name="limit-search-results"></a>Limitare i risultati della ricerca

   In questa sezione si apprenderà come usare le API di ricerca di mappe di Azure per limitare i risultati della ricerca. 

   > [!Note]
   > Ricerca non tutte le API supportano completamente i parametri elencati di seguito

   **Risultati della ricerca geografica distorsione**

   In ordine di geo-distorsione i risultati ottenuti con l'area rilevante per l'utente, aggiungere sempre il numero massimo consentito dettagliato input di percorso. Per limitare i risultati della ricerca, è consigliabile aggiungere i tipi di input seguenti:

   1. Impostare il `countrySet` parametro, ad esempio "US, FR". Il comportamento di ricerca predefinito è la ricerca di tutto il mondo, potenzialmente restituire risultati non necessari. Se la query non include `countrySet` parametro, la ricerca potrebbe restituire risultati non accurati. Ad esempio, cercare una città denominata **Bellevue** restituirà i risultati da Stati Uniti e Francia, poiché sono presenti le città denominate **Bellevue** in Francia e negli Stati Uniti.

   2. È possibile usare la `btmRight` e `topleft` parametri da impostare il riquadro delimitatore di finestra per restringere la ricerca a un'area specifica sulla mappa.

   3. Per poter influire sulla relativa alla pertinenza dei risultati, è possibile definire le `lat`e `lon` coordinare i parametri e imposta il raggio dell'area di ricerca tramite la `radius` parametro.


   **Parametri di ricerca fuzzy**

   1. Il `minFuzzyLevel` e `maxFuzzyLevel`, aiutare a restituire corrispondenze pertinenti, anche quando i parametri di query corrispondono esattamente alle informazioni desiderate. Per impostazione predefinita la maggior parte delle query di ricerca `minFuzzyLevel=1` e `maxFuzzyLevel=2` per ottenere prestazioni e ridurre i risultati insoliti. Eseguire un esempio di un termine di ricerca "restrant", corrisponde a "ristorante" quando il `maxFuzzyLevel` è impostato su 2. Livelli predefiniti fuzzy possono essere sostituiti in base alle esigenze di richiesta. 

   2. È anche possibile specificare il set esatto di tipi di risultati da restituire mediante la `idxSet` parametro. A tale scopo è possibile inviare l'elenco delimitato da virgole degli indici, non è importante l'ordine degli elementi. Di seguito sono gli indici supportati:

       * `Addr` - **Gli intervalli di indirizzi**: Per alcune strade, sono disponibili punti di indirizzo vengono interpolati all'inizio e alla fine della via; tali punti sono rappresentati come gli intervalli di indirizzi.
       * `Geo` - **Aree geografiche**: Aree su una mappa che rappresentano divisione amministrativa della terra, vale a dire, paese, stato, città.
       * `PAD` - **Indirizzo del punto**:  Punti su una mappa in cui un indirizzo specifico con il nome della via e numero è reperibile in un indice, ad esempio, Soquel 2501 di ripristino di emergenza. È il massimo livello di accuratezza disponibile per gli indirizzi.  
       * `POI` - **I punti di interesse**: Punti su una mappa che vale la pena attenzione e potrebbero essere interessante.  [Ottenere l'indirizzo di ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) non restituisce i punti di interesse.  
       * `Str` - **Streets**: Rappresentazione di strade sulla mappa.
       * `XStr` - **Cross/le intersezioni di strade**:  Rappresentazione di congiunzioni; posizioni in cui si intersecano due vie.


       **Esempi di utilizzo**:

       * idxSet = punto di interesse (ricerca di punti di interesse solo) 

       * idxSet = riempimento, Addr (ricerca indirizzi solo, riempimento = punto Address, Addr = intervallo di indirizzi)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Invertire geocode e geography filtro del tipo di entità

Quando si esegue una ricerca di codifica geografica inversa con [API di ricerca indirizzi inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), il servizio ha funzionalità per restituire i poligoni per aree di amministrazione. Fornendo il parametro `entityType` nella richiesta, è possibile restringere la ricerca per i tipi di entità di geografia specificata. La risposta risultante conterrà l'ID di geografia, nonché il tipo di entità corrispondente. Se si specifica più di un'entità, verrà restituito l'endpoint di **entità più piccola disponibili**. Ha restituito l'ID di geometria è utilizzabile per ottenere la geometria di quell'area geografica tramite [servizio di ottenere poligono](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

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

Il `language` parametro consente di impostare in cui la ricerca del linguaggio deve essere restituito alcun risultato. Se la lingua non è impostata nella richiesta, servizio di ricerca viene impostato automaticamente sulla lingua più comuni nel paese/area geografica. Inoltre, quando i dati nella lingua specificata non sono disponibili, viene utilizzata la lingua predefinita. Visualizzare [lingue supportate](https://docs.microsoft.com/azure/azure-maps/supported-languages) per un elenco dei linguaggi supportati rispetto a servizi di mappe di Azure da paese/area geografica.


### <a name="predictive-mode-auto-suggest"></a>Modalità predittiva (suggerimenti automatici)

Per trovare più corrispondenze per le query parziale, `typeahead` parametro deve essere impostato su 'true'. La query verrà interpretata come un input parziale e la ricerca passerà alla modalità predittiva. In caso contrario, il servizio presuppone che tutte le informazioni pertinenti sono state passate.

Nell'esempio di query seguente è possibile vedere che il servizio di indirizzo di ricerca viene eseguita una query per "Microso" con il `typeahead` parametro è impostato su **true**. Se si osserva la risposta, si noterà che il servizio di ricerca interpretata la query come query parziale e risposta contiene i risultati di query suggerite automaticamente.

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


### <a name="uri-encoding-to-handle-special-characters"></a>URI di codifica per gestire i caratteri speciali 

Per trovare tra indirizzi stradali, vale a dire, "1 ° Viale & unione via e numero civico, Seattle", un carattere speciale '&' deve essere codificata prima di inviare la richiesta. Si consiglia di codifica dei dati di tipo carattere in un URI, in cui tutti i caratteri vengono codificati usando un carattere '% s'e un valore esadecimale a due caratteri corrispondenti per i caratteri UTF-8.

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
```C#
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

rubino:
```Ruby
CGI::escape(query) 
```

Repentino:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Vedere:
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>Le procedure consigliate per la ricerca di punto di interesse

I punti di interesse (punto di interesse) ricerca consente di richiedere i risultati di punto di interesse in base al nome, ad esempio, ricerca business in base al nome. È consigliabile usare il `countrySet` parametro per specificare i paesi in cui l'applicazione richiede copertura, come il comportamento predefinito, è necessario eseguire la ricerca di tutto il mondo, potenzialmente restituire risultati non necessari e/o comportare tempi di ricerca.

### <a name="brand-search"></a>Ricerca del marchio

Per migliorare la pertinenza dei risultati e le informazioni nella risposta, risposta della ricerca con punto di interesse (punto di interesse) include le informazioni del marchio che possono essere ulteriormente utilizzate per analizzare la risposta.

Creiamo un [ricerca di categoria di punto di interesse](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) richiesta per le stazioni di servizio in prossimità del campus Microsoft (Redmond, WA). Se si osserva la risposta, è possibile visualizzare informazioni del marchio per ogni punto di interesse restituito.

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


### <a name="airport-search"></a>Ricerca dell'aeroporto

Punto di interesse ricerca supporta la ricerca negli aeroporti usando i codici degli aeroporti ufficiali. Ad esempio, **SEA** (Seattle Tacoma International Airport). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Ricerca nelle vicinanze

Per recuperare solo i risultati di punto di interesse in una posizione specifica, il [nelle vicinanze API di ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) potrebbe essere la scelta giusta. Questo endpoint e non accetta un parametro di query di ricerca restituirà solo i risultati di punto di interesse. Per limitare i risultati, è consigliabile impostare il raggio.

## <a name="understanding-the-responses"></a>Comprendere le risposte

È possibile effettuare una richiesta di ricerca di indirizzo per le mappe di Azure [servizio di ricerca](https://docs.microsoft.com/rest/api/maps/search) per un indirizzo di Seattle. Se si osserva con attenzione l'URL della richiesta riportato di seguito, è stato impostato il `countrySet` parametro per **Stati Uniti** per la ricerca dell'indirizzo negli Stati Uniti d'America.

**Query di esempio:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

Inoltre si guardi alla struttura risposta riportata di seguito. I tipi di risultato degli oggetti risultato nella risposta sono diversi. Se si osserva attentamente che può osservare, ci sono tre diversi tipi di oggetti risultato, che sono "Punto Address", "Street" e "Cross Street". Si noti che ricerca di indirizzi non restituisce i punti di interesse. Il `Score` parametro per ogni oggetto di risposta indica il punteggio di corrispondenza relativo a numerosi altri oggetti nella stessa risposta. Visualizzare [Ottieni indirizzo ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) per altre informazioni sui parametri dell'oggetto risposta.

**Tipi di risultato supportati:**

* **Indirizzo del punto:** Punti su una mappa con un indirizzo specifico con un file e numero civico. Il massimo livello di accuratezza disponibile per gli indirizzi. 

* **Intervallo di indirizzi:**  Per alcune strade, sono disponibili punti di indirizzo vengono interpolati all'inizio e alla fine della via; tali punti sono rappresentati come gli intervalli di indirizzi. 

* **Geografia:** Aree su una mappa che rappresentano divisione amministrativa della terra, vale a dire, paese, stato, città. 

* **Punto di interesse - (i punti di interesse):** Punti su una mappa che vale la pena attenzione e potrebbero essere interessante.

* **via:** Rappresentazione di strade sulla mappa. Gli indirizzi vengono risolti per la coordinata di latitudine/longitudine della via che contiene l'indirizzo. Il numero civico può non essere elaborato. 

* **Cross Street:** Intersezioni. Rappresentazioni di congiunzioni; posizioni in cui si intersecano due vie.

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

### <a name="geometry"></a>Geometry

Quando il tipo di risposta è **geometria**, può includere l'ID di geometria restituita nel **dataSources** oggetto posizionato sotto "la geometria" e "id". Ad esempio, [servizio di ottenere poligono](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) consente di richiedere i dati della geometria in formato GeoJSON, ad esempio una struttura o un aeroporto e città per un set di entità. È possibile usare i dati per limite [Geofencing](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) oppure [ricerca punti di interesse all'interno della geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).


[Cerca indirizzo](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) oppure [ricerca Fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) risposte API possono includere il **ID di geometria** restituito nell'oggetto dataSources sotto "la geometria" e "id".


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Passaggi successivi

* Scopri [come creare richieste di servizio di ricerca di Azure esegue il mapping](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address).
* Esplorare le mappe di Azure [documentazione dell'API del servizio ricerca](https://docs.microsoft.com/rest/api/maps/search). 
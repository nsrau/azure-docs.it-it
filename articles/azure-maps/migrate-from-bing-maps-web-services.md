---
title: 'Esercitazione: Eseguire la migrazione dei servizi Web da Bing Maps | Mappe di Microsoft Azure'
description: Come eseguire la migrazione dei servizi Web da Bing Maps a Mappe di Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 14e0998b75e0e5bd3ae996f5f5010ecc50180f14
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91741670"
---
# <a name="migrate-web-service-from-bing-maps"></a>Eseguire la migrazione di un servizio Web da Bing Maps

Sia Mappe di Azure che Bing Maps forniscono l'accesso alle API spaziali tramite servizi Web REST. Le interfacce API per queste piattaforme eseguono funzionalità simili, ma usano convenzioni di denominazione e oggetti risposta diversi.

La tabella seguente illustra le API del servizio Mappe di Azure che forniscono funzionalità simili alle API del servizio Bing Maps.

| API del servizio Bing Maps                 | API del servizio Mappe di Azure      |
|---------------------------------------|-----------------------------|
| Suggerimenti automatici                           | [Ricerca](https://docs.microsoft.com/rest/api/maps/search)     |
| Indicazioni stradali (anche per camion)          | [Indicazioni dei percorsi](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)                          |
| Matrice di distanze                       | [Matrice di percorsi](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)                          |
| Immagini - Mappa statica                  | [Rendering](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                                   |
| Isocrone                            | [Estensione dei percorsi](https://docs.microsoft.com/rest/api/maps/route/getrouterange)                                    |
| Informazioni dettagliate locali                        | [Ricerca](https://docs.microsoft.com/rest/api/maps/search) + [Estensione dei percorsi](https://docs.microsoft.com/rest/api/maps/route/getrouterange)    |
| Ricerca locale                          | [Ricerca](https://docs.microsoft.com/rest/api/maps/search)     |
| Riconoscimento della posizione (punti di interesse)           | [Ricerca](https://docs.microsoft.com/rest/api/maps/search)     |
| Località (geocodifica diretta/inversa) | [Ricerca](https://docs.microsoft.com/rest/api/maps/search)                                               |
| Blocca sulla strada                          | [Indicazioni dei percorsi POST](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)                         |
| Servizi dati spaziali (SDS)           | [Ricerca](https://docs.microsoft.com/rest/api/maps/search) + [Percorso](https://docs.microsoft.com/rest/api/maps/route) e altri servizi di Azure |
| Fuso orario                             | [Fusi orari](https://docs.microsoft.com/rest/api/maps/timezone)  |
| Incidenti stradali                     | [Dettagli degli incidenti stradali](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidentdetail)                     |

Le API dei servizi seguenti non sono attualmente disponibili in Mappe di Azure:

-   Altitudine - Pianificata
-   Percorsi di itinerari ottimizzati - Pianificata. L'API Percorso di Mappe di Azure supporta l'ottimizzazione del tragitto di minima percorrenza per un singolo veicolo.
-   Metadati delle immagini: usati principalmente per ottenere gli URL delle tessere in Bing Maps. Mappe di Azure include un servizio autonomo per l'accesso diretto alle tessere mappa.

Mappe di Azure include numerosi servizi Web REST aggiuntivi che possono risultare utili:

-   [Creator di Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/creator-indoor-maps): creare un gemello digitale privato personalizzato di edifici e spazi.
-   [Operazioni spaziali](https://docs.microsoft.com/rest/api/maps/spatial): trasferire il carico di operazioni e calcoli spaziali complessi, ad esempio il geofencing, a un servizio.
-   [Tessere mappa](https://docs.microsoft.com/rest/api/maps/render/getmaptile): accedere alle tessere di strade e immagini da Mappe di Azure come tessere raster e vettoriali.
-   [Pianificazione di percorsi in batch](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): consente di effettuare fino a 1.000 richieste di percorsi in un singolo batch in un determinato periodo di tempo. I percorsi vengono calcolati in parallelo nel server per accelerare l'elaborazione.
-   [Flusso del traffico](https://docs.microsoft.com/rest/api/maps/traffic): accedere ai dati del flusso del traffico in tempo reale come tessere raster e vettoriali.
-   [API di georilevazione](https://docs.microsoft.com/rest/api/maps/geolocation/getiptolocationpreview): ottenere la posizione di un indirizzo IP.
-   [Servizi meteorologici](https://docs.microsoft.com/rest/api/maps/weather): ottenere l'accesso a dati meteorologici in tempo reale e di previsione.

Assicurarsi di consultare anche le seguenti guide alle procedure consigliate:

-   [Procedure consigliate per la ricerca](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search)
-   [Procedure consigliate per la pianificazione percorso](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-routing)

## <a name="geocoding-addresses"></a>Geocodifica di indirizzi

La geocodifica è il processo di conversione di un indirizzo, ad esempio `"1 Microsoft way, Redmond, WA"`, in una coordinata, ad esempio longitudine: -122.1298, latitudine: 47.64005. Le coordinate vengono quindi usate spesso per posizionare una puntina su una mappa o per centrare una mappa.

Mappe di Azure offre numerosi metodi per la geocodifica degli indirizzi:

-   [Geocodifica di indirizzi in formato libero](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): la richiesta viene elaborata immediatamente quando si specifica una singola stringa di indirizzo, ad esempio `"1 Microsoft way, Redmond, WA"`. Questo servizio è consigliato se è necessario geocodificare rapidamente singoli indirizzi.
-   [Geocodifica di indirizzi strutturati](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): consente di specificare le parti di un singolo indirizzo, ad esempio nome della via, città, paese e codice postale, e di elaborare immediatamente la richiesta. Questo servizio è consigliato se è necessario geocodificare rapidamente singoli indirizzi e i dati sono già stati analizzati nelle singole parti dell'indirizzo.
-   [Geocodifica di indirizzi batch](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): consente di creare una richiesta contenente fino a 10.000 indirizzi e li elabora in un certo periodo di tempo. Verrà eseguita la geocodifica di tutti gli indirizzi in parallelo nel server e, al termine dell'operazione, sarà possibile scaricare il set di risultati completo. Questo servizio è consigliato per la geocodifica di set di dati di grandi dimensioni.
-   [Ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): Questa API combina la geocodifica degli indirizzi con la ricerca di punti di interesse. Accetta una stringa in formato libero che può essere un indirizzo, un luogo, un punto di riferimento, un punto di interesse o una categoria di punti di interesse ed elabora immediatamente la richiesta. Questa API è consigliata per le applicazioni in cui gli utenti possono cercare indirizzi o punti di interesse dalla stessa casella di testo.
-   [Ricerca fuzzy batch](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): consente di creare una richiesta contenente fino a 10.000 indirizzi, luoghi, punti di riferimento o punti di interesse e li elabora in un certo periodo di tempo. Tutti i dati verranno elaborati in parallelo nel server e, al termine dell'operazione, sarà possibile scaricare il set di risultati completo.

Le tabelle seguenti contengono riferimenti incrociati tra i parametri delle API di Bing Maps e i parametri delle API equivalenti di Mappe di Azure per la geocodifica di indirizzi in formato libero e strutturati.

**Posizione in base a indirizzo (indirizzo strutturato)**

| Parametro dell'API di Bing Maps              | Parametro API di Mappe di Azure equivalente                 |
|--------------------------------------|-----------------------------------------------------|
| `addressLine`                      | `streetNumber`, `streetName` o `crossStreet` |
| `adminDistrict`                    | `countrySubdivision`                              |
| `countryRegion`                    | `country` e `countryCode`                     |
| `locality`                         | `municipality` o `municipalitySubdivision`     |
| `postalCode`                       | `postalCode`                                      |
| `maxResults` (`maxRes`)          | `limit`                                           |
| `includeNeighborhood` (`inclnb`) | N/D: sempre restituito da Mappe di Azure se disponibile.   |
| `include` (`incl`)               | N/D: codice paese ISO2 sempre restituito da Mappe di Azure. |
| `key`                              | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication). |
| `culture` (`c`)                  | `language`: vedere la documentazione relativa alle [lingue supportate](https://docs.microsoft.com/azure/azure-maps/supported-languages). |
| `userRegion` (`ur`)              | `view`: vedere la documentazione sulle [visualizzazioni supportate](https://aka.ms/AzureMapsLocalizationViews). |

Mappe di Azure supporta anche:

-   `countrySecondarySubdivision`: contea, distretti
-   `countryTertiarySubdivision`: aree denominate; borghi, cantoni, comuni
-   `ofs`: scorrere i risultati in combinazione con il parametro `maxResults`.

**Posizione in base a query (stringa di indirizzo in formato libero)**

| Parametro dell'API di Bing Maps              | Parametro API di Mappe di Azure equivalente      |
|--------------------------------------|------------------------------------------|
| `query`                            | `query`                                |
| `maxResults` (`maxRes`)          | `limit`                                |
| `includeNeighborhood` (`inclnb`) | N/D: sempre restituito da Mappe di Azure se disponibile.  |
| `include` (`incl`)               | N/D: codice paese ISO2 sempre restituito da Mappe di Azure.  |
| `key`                              | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication). |
| `culture` (`c`)                  | `language`: vedere la documentazione relativa alle [lingue supportate](https://docs.microsoft.com/azure/azure-maps/supported-languages).  |
| `userRegion` (`ur`)              | `view`: vedere la documentazione sulle [visualizzazioni supportate](https://aka.ms/AzureMapsLocalizationViews). |

Mappe di Azure supporta anche:

-   `typeahead`: specifica se la query verrà interpretata come input parziale e la ricerca passerà in modalità predittiva (suggerimenti automatici/completamento automatico).
-   `countrySet`: elenco di codici di paesi ISO2 delimitati da virgole in base a cui limitare la ricerca.
-   `lat`/`lon`, `topLeft`/`btmRight`, `radius`: specificare la posizione e l'area utente per rendere i risultati più pertinenti a livello locale.
-   `ofs`: scorrere i risultati in combinazione con il parametro `maxResults`.

Un esempio di utilizzo del servizio di ricerca è documentato [qui](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address). Assicurarsi di esaminare la documentazione relativa alle [procedure consigliate per la ricerca](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search).

## <a name="reverse-geocode-a-coordinate-find-a-location-by-point"></a>Invertire la geocodifica di una coordinata (trovare una posizione in base a un punto)

La geocodifica inversa è il processo di conversione delle coordinate geografiche, ad esempio, longitudine:-122.1298, latitudine: 47.64005, nel relativo indirizzo approssimativo, ad esempio `"1 Microsoft way, Redmond, WA"`.

Mappe di Azure offre numerosi metodi di geocodifica inversa:

-   [Geocodifica inversa di indirizzi](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): consente di specificare una singola coordinata geografica per ottenere l'indirizzo corrispondente approssimativo e di elaborare immediatamente la richiesta.
-   [Geocodifica inversa di incroci](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): consente di specificare una singola coordinata geografica per ottenere le informazioni sugli incroci adiacenti, ad esempio 1st street e Main street, e di elaborare immediatamente la richiesta.
-   [Geocodifica inversa di indirizzi batch](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): consente di creare una richiesta contenente fino a 10.000 coordinate e le elabora in un certo periodo di tempo. Tutti i dati verranno elaborati in parallelo nel server e, al termine dell'operazione, sarà possibile scaricare il set di risultati completo.

La tabella seguente include i riferimenti incrociati tra i parametri delle API di Bing Maps e i parametri equivalenti delle API di Mappe di Azure.

| Parametro dell'API di Bing Maps              | Parametro API di Mappe di Azure equivalente       |
|--------------------------------------|-------------------------------------------|
| `point`                              | `query`                                   |
| `includeEntityTypes`                 | `entityType`: vedere la tabella di confronto dei tipi di entità di seguito.    |
| `includeNeighborhood` (`inclnb`)     | N/D: sempre restituito da Mappe di Azure se disponibile.         |
| `include` (`incl`)                   | N/D: codice paese ISO2 sempre restituito da Mappe di Azure.    |
| `key`                                | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication). |
| `culture` (`c`)                      | `language`: vedere la documentazione relativa alle [lingue supportate](https://docs.microsoft.com/azure/azure-maps/supported-languages).   |
| `userRegion` (`ur`)                  | `view`: vedere la documentazione sulle [visualizzazioni supportate](https://aka.ms/AzureMapsLocalizationViews). |

Assicurarsi di esaminare la documentazione relativa alle [procedure consigliate per la ricerca](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search).

L'API di geocodifica inversa di Mappe di Azure include alcune funzionalità aggiuntive non disponibili in Bing Maps che potrebbero essere utili da integrare durante la migrazione dell'app:

-   Recupero dei dati sui limiti di velocità.
-   Recupero delle informazioni specifiche della strada: strada locale, arteria, accesso limitato, rampa e così via.
-   Lato della strada corrispondente alla coordinata specificata.

**Tabella di confronto dei tipi di entità**

La tabella seguente include i riferimenti incrociati tra i valori dei tipi di entità di Bing Maps e i nomi di proprietà equivalenti di Mappe di Azure.

| Tipo di entità di Bing Maps | Tipo di entità equivalente di Mappe di Azure               | Descrizione                                |
|-----------------------|-------------------------------------------------|--------------------------------------------|
| `Address`             |                                                 | *Indirizzo*                                  |
| `Neighborhood`        | `Neighbourhood`                                 | *Quartiere*                             |
| `PopulatedPlace`      | `Municipality` o `MunicipalitySubdivision`     | *Città*, *area metropolitana o sobborgo* o *metropoli*     |
| `Postcode1`           | `PostalCodeArea`                                | *Codice postale* o *CAP*                |
| `AdminDivision1`      | `CountrySubdivision`                            | *Stato* o *provincia*                      |
| `AdminDivision2`      | `CountrySecondarySubdivison`                    | *Contea* o *distretti*                    |
| `CountryRegion`       | `Country`                                       | *Nome del paese*                             |
|                       | `CountryTertiarySubdivision`                    | *Borghi*, *cantoni*, *comuni*          |

## <a name="get-location-suggestions-autosuggest"></a>Ottenere suggerimenti sulla posizione (suggerimenti automatici)

Diverse API di ricerca di Mappe di Azure supportano la modalità predittiva che può essere usata per gli scenari dei suggerimenti automatici. L'API di [ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) di Mappe di Azure è la più simile all'API Suggerimenti automatici di Bing Maps. Anche le API seguenti supportano la modalità predittiva. Aggiungere `&typeahead=true` alla query:

-   [Geocodifica di indirizzi in formato libero](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): la richiesta viene elaborata immediatamente quando si specifica una singola stringa di indirizzo, ad esempio `"1 Microsoft way, Redmond, WA"`. Questo servizio è consigliato se è necessario geocodificare rapidamente singoli indirizzi.
-   [Ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): Questa API combina la geocodifica degli indirizzi con la ricerca di punti di interesse. Accetta una stringa in formato libero che può essere un indirizzo, un luogo, un punto di riferimento, un punto di interesse o una categoria di punti di interesse ed elabora immediatamente la richiesta. Questa API è consigliata per le applicazioni in cui gli utenti possono cercare indirizzi o punti di interesse dalla stessa casella di testo.
-   [Ricerca di punti di interesse](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): consente di cercare i punti di interesse in base al nome. Ad esempio, `"starbucks"`.
-   [Ricerca per categoria di punti di interesse](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): consente di cercare i punti di interesse in base alla categoria. Ad esempio, "ristorante".

## <a name="calculate-routes-and-directions"></a>Calcolare percorsi e indicazioni

È possibile usare Mappe di Azure per calcolare percorsi e indicazioni. Mappe di Azure offre numerose funzionalità uguali a quelle del servizio di pianificazione percorso di Bing Maps, ad esempio:

-   Orari di arrivo e di partenza
-   Percorsi del traffico basati su previsioni e in tempo reale
-   Modalità di trasporto diverse, ad esempio in auto, a piedi, in camion
-   Ottimizzazione dell'ordine dei punti di tragitto

> [!NOTE]
> Mappe di Azure richiede che tutti i punti di tragitto siano coordinate. È quindi prima necessario eseguire la geocodifica degli indirizzi.

Il servizio di pianificazione percorso di Mappe di Azure fornisce le API seguenti per il calcolo dei percorsi:

-   [Calcolo percorso](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): consente di calcolare un percorso e di elaborare immediatamente la richiesta. Questa API supporta le richieste GET e POST. Le richieste POST sono consigliate quando si specifica un numero elevato di punti di tragitto o quando si usano molte opzioni pel percorso per assicurarsi che la richiesta URL non diventi troppo lunga e causi problemi.
-   [Percorso batch](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): consente di creare una richiesta contenente fino a 1.000 richieste di percorso e le elabora in un certo periodo di tempo. Tutti i dati verranno elaborati in parallelo nel server e, al termine dell'operazione, sarà possibile scaricare il set di risultati completo.
-   [Servizi di mobilità](https://docs.microsoft.com/rest/api/maps/mobility): consente di calcolare percorsi e indicazioni usando il trasporto pubblico.

La tabella seguente include i riferimenti incrociati tra i parametri delle API di Bing Maps e i parametri equivalenti delle API di Mappe di Azure.

| Parametro dell'API di Bing Maps                                    | Parametro API di Mappe di Azure equivalente               |
|------------------------------------------------------------|---------------------------------------------------|
| `avoid`                                                    | `avoid`                                           |
| `dateTime` (`dt`)                                          | `departAt` o `arriveAt`                          |
| `distanceBeforeFirstTurn` (`dbft`)                         | N/D                                               |
| `distanceUnit` (`du`)                                      | N/D: Mappe di Azure usa solo il sistema metrico.     |
| `heading` (`hd`)                                           | `vehicleHeading`                                  |
| `maxSolutions` (`maxSolns`)                                | `maxAlternatives`, `alternativeType`, `minDeviationDistance` e `minDeviationTime`  |
| `optimize` (`optwz`)                                       | `routeType` e `traffic`                         |
| `optimizeWaypoints` (`optWp`)                              | `computeBestOrder`                                |
| `routeAttributes` (`ra`)                                   | `instructionsType`                                |
| `routePathOutput` (`rpo`)                                  | `routeRepresentation`                             |
| `timeType` (`tt`)                                          | `departAt` o `arriveAt`                          |
| `tolerances` (`tl`)                                        | N/D                                               |
| `travelMode`                                               | `travelMode`                                      |
| `waypoint.n` (`wp.n`) o `viaWaypoint.n` (`vwp.n`)         | `query`: coordinate nel formato `lat0,lon0:lat1,lon1….`   |
| `key`                                                      | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication). |
| `culture` (`c`)                                            | `language`: vedere la documentazione relativa alle [lingue supportate](https://docs.microsoft.com/azure/azure-maps/supported-languages). |
| `userRegion` (`ur`)                                        | `view`: vedere la documentazione sulle [visualizzazioni supportate](https://aka.ms/AzureMapsLocalizationViews). |

L'API di pianificazione percorso di Mappe di Azure supporta anche i percorsi per i camion. La tabella seguente include i riferimenti incrociati tra i parametri di pianificazione percorso dei camion di Bing Maps e i parametri equivalenti delle API di Mappe di Azure.

| Parametro dell'API di Bing Maps                  | Parametro API di Mappe di Azure equivalente        |
|------------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                 | N/D: supportate solo dimensioni in metri. |
| `weightUnit` (`wu`)                      | N/D: supportati solo pesi in chilogrammi. |
| `vehicleHeight` (`height`)               | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                 | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                   | `vehicleLength`                            |
| `vehicleWeight` (`weight`)               | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                 | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                 | **N/D**                                    |
| `vehicleSemi` (`semi`)                   | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)             | **N/D**                                    |
| `vehicleMinTurnRadius` (`vmtr`)          | **N/D**                                    |
| `vehicleAvoidCrossWind` (`vacw`)         | **N/D**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)     | **N/D**                                    |
| `vehicleHazardousMaterials` (`vhm`)      | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)        | `vehicleLoadType`                          |

> [!TIP]
> Per impostazione predefinita, l'API di pianificazione percorso di Mappe di Azure restituisce solo un riepilogo (distanza e ora) e le coordinate per il percorso. Usare il parametro `instructionsType` per recuperare le istruzioni dettagliate. Il parametro `routeRepresentation` può essere usato per filtrare il riepilogo e il percorso.

Assicurarsi di vedere la documentazione relativa alle [procedure consigliate per la pianificazione percorso](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-routing).

L'API di pianificazione percorso di Mappe di Azure include numerose funzionalità aggiuntive non disponibili in Bing Maps che potrebbe essere utile integrare durante la migrazione dell'app:

-   Supporto per il tipo di percorso: più breve, più veloce, più interessante/panoramico e con minore consumo di carburante.
-   Supporto per modalità di viaggio aggiuntive: bicicletta, autobus, motocicletta, taxi, camion e furgone.
-   Supporto per 150 punti di tragitto.
-   Calcolo di più tempi di viaggio in un'unica richiesta. Traffico cronologico, traffico in tempo reale, senza traffico.
-   Possibilità di evitare tipi di strade aggiuntivi: corsie preferenziali, strade non asfaltate, strade già usate.
-   Pianificazione percorso basato sulle specifiche del motore. Calcolo dei percorsi per veicoli a combustione o elettrici in base al carburante o alla carica rimanente e alle specifiche del motore.
-   Possibilità di specificare la velocità massima del veicolo.

## <a name="snap-coordinates-to-road"></a>Allineare le coordinate alla strada

Esistono diversi modi per allineare le coordinate alle strade in Mappe di Azure.

-   Usare l'API di indicazioni percorso per allineare le coordinate a un percorso logico lungo la rete stradale.
-   Usare Azure Maps Web SDK per allineare singole coordinate alla strada più vicina nelle tessere vettoriali.
-   Usare direttamente le tessere vettoriali di Mappe di Azure per allineare singole coordinate.

**Uso dell'API di indicazioni percorso per allineare le coordinate**

Mappe di Azure consente di allineare le coordinate alle strade tramite l'API di [indicazioni percorso](https://docs.microsoft.com/rest/api/maps/route/postroutedirections). Questo servizio può essere usato per ricostruire un percorso logico tra un set di coordinate ed è equivalente all'API di allineamento alla strada di Bing Maps.

Esistono due modi diversi per usare l'API di indicazioni percorso per allineare le coordinate alle strade.

-   Se le coordinate sono al massimo 150, possono essere passate come punti di tragitto nell'API GET di indicazioni percorso. Con questo approccio è possibile recuperare due tipi diversi di dati allineati: le istruzioni sul percorso conterranno i singoli punti di tragitto allineati, mentre il tragitto del percorso avrà un set interpolato di coordinate che riempiono il percorso completo tra le coordinate.
-   Se le coordinate sono più di 150, è possibile usare l'API POST di indicazioni percorso. Le coordinate di inizio e di fine devono essere passate nel parametro di query, ma tutte le coordinate possono essere passate nel parametro `supportingPoints` nel corpo della richiesta POST e formattate come una raccolta di punti geometrici GeoJSON. Gli unici dati allineati disponibili con questo approccio corrispondono al tragitto del percorso, ovvero un set interpolato di coordinate che riempiono il tragitto completo tra le coordinate. [Ecco un esempio](https://azuremapscodesamples.azurewebsites.net/?sample=Snap%20points%20to%20logical%20route%20path) di questo approccio usando il modulo dei servizi di Azure Maps Web SDK.

La tabella seguente include i riferimenti incrociati tra i parametri delle API di Bing Maps e i parametri equivalenti delle API di Mappe di Azure.

| Parametro dell'API di Bing Maps    | Parametro API di Mappe di Azure equivalente                                 |
|----------------------------|---------------------------------------------------------------------|
| `points`                   | `supportingPoints`: passare questi punti nel corpo della richiesta POST  |
| `interpolate`              | N/D                                                                 |
| `includeSpeedLimit`        | N/D                                                                 |
| `includeTruckSpeedLimit`   | N/D                                                                 |
| `speedUnit`                | N/D                                                                 |
| `travelMode`               | `travelMode`                                                        |
| `key`                      | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication). |
| `culture` (`c`)            | `language`: vedere la documentazione relativa alle [lingue supportate](https://docs.microsoft.com/azure/azure-maps/supported-languages).   |
| `userRegion` (`ur`)        | `view`: vedere la documentazione sulle [visualizzazioni supportate](https://aka.ms/AzureMapsLocalizationViews).   |

L'API di pianificazione percorso di Mappe di Azure supporta anche il parametro per la pianificazione percorso dei camion per assicurare che vengano calcolati tragitti logici. La tabella seguente include i riferimenti incrociati tra i parametri di pianificazione percorso dei camion di Bing Maps e i parametri equivalenti delle API di Mappe di Azure.

| Parametro dell'API di Bing Maps                 | Parametro API di Mappe di Azure equivalente        |
|-----------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                | N/D: supportate solo dimensioni in metri. |
| `weightUnit` (`wu`)                     | N/D: supportati solo pesi in chilogrammi. |
| `vehicleHeight` (`height`)              | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                  | `vehicleLength`                            |
| `vehicleWeight` (`weight`)              | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                | **N/D**                                    |
| `vehicleSemi` (`semi`)                  | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)            | **N/D**                                    |
| `vehicleMinTurnRadius` (`vmtr`)         | **N/D**                                    |
| `vehicleAvoidCrossWind` (`vacw`)        | **N/D**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)    | **N/D**                                    |
| `vehicleHazardousMaterials` (`vhm`)     | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)       | `vehicleLoadType`                          |

Poiché questo approccio usa l'API di indicazioni percorso, è possibile usare il set completo di opzioni in tale servizio per personalizzare la logica usata per allineare le coordinate alle strade. Se ad esempio si specifica un'ora di partenza, verranno presi in considerazione i dati cronologici sul traffico.

L'API di indicazioni percorso di Mappe di Azure attualmente non restituisce i dati sui limiti di velocità, che possono tuttavia essere recuperati usando l'API di geocodifica inversa di Mappe di Azure.

**Uso di Web SDK per allineare le coordinate**

Azure Maps Web SDK usa le tessere vettoriali per eseguire il rendering delle mappe. Queste tessere vettoriali contengono dati non elaborati sulla geometria delle strade e possono essere usate per calcolare la strada più vicina a una coordinata per l'allineamento di base di singole coordinate. Questa funzione è utile per mostrare visivamente le coordinate sulle strade e si usa già Azure Maps Web SDK per visualizzare i dati.

Con questo approccio, tuttavia, vengono allineati alla strada solo i segmenti caricati all'interno della visualizzazione mappa. Quando si fa zoom indietro a livello di paese è possibile che non siano presenti dati sulle strade, quindi l'allineamento non può essere eseguito. Tuttavia, a questo livello di zoom un singolo pixel può rappresentare l'area di diversi blocchi di città, quindi l'allineamento non è necessario. Per risolvere questo problema, la logica di allineamento può essere applicata ogni volta che termina lo spostamento della mappa. [Ecco un esempio di codice](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic) che dimostra questa funzionalità.

**Uso diretto delle tessere vettoriali di Mappe di Azure per allineare le coordinate**

Le tessere vettoriali di Mappe di Azure contengono dati non elaborati sulla geometria delle strade e possono essere usate per calcolare il punto di una strada più vicino a una coordinata per l'allineamento di base di singole coordinate. Tutti i segmenti stradali vengono visualizzati nei settori al livello di zoom 15, quindi è consigliabile recuperare le tessere da qui. È quindi possibile usare il [modello matematico a piramide di tessere dell'albero quadramentale](https://docs.microsoft.com/azure/azure-maps/zoom-levels-and-tile-grid) per determinare le tessere necessarie e convertirle in geometrie. Da qui è possibile usare una libreria matematica spaziale, ad esempio [turf js](http://turfjs.org/) o [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) per calcolare i segmenti di linea più vicini.

## <a name="retrieve-a-map-image-static-map"></a>Recuperare un'immagine mappa (mappa statica)

Mappe di Azure fornisce un'API per il rendering delle immagini mappa statiche con overlay dei dati. L'API di [rendering immagini mappa](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) di Mappe di Azure è simile all'API di mappe statiche di Bing Maps.

> [!NOTE]
> Mappe di Azure richiede che il centro, tutte le puntine e le posizioni dei tragitti siano coordinate nel formato `longitude,latitude` mentre Bing Maps usa il formato `latitude,longitude`.</p>
<p>È quindi prima necessario eseguire la geocodifica degli indirizzi.

La tabella seguente include i riferimenti incrociati tra i parametri delle API di Bing Maps e i parametri equivalenti delle API di Mappe di Azure.

| Parametro dell'API di Bing Maps  | Parametro API di Mappe di Azure equivalente            |
|--------------------------|------------------------------------------------|
| `centerPoint`            | `center`                                       |
| `format`                 | `format`: specificato come parte del percorso URL. Attualmente è supportato solo il formato PNG.  |
| `heading`                | N/D: vista a livello strada non supportata.                |
| `imagerySet`             | `layer` e `style`: vedere la documentazione relativa agli [stili mappa supportati](https://docs.microsoft.com/azure/azure-maps/supported-map-styles).   |
| `mapArea` (`ma`)         | `bbox`                                         |
| `mapLayer` (`ml`)        | N/D                                            |
| `mapSize` (`ms`)         | `width` e `height`: dimensioni massime fino a 8192x8192. |
| `declutterPins` (`dcl`)  | N/D                                            |
| `dpi`                    | N/D                                            |
| `drawCurve`              | `path`                                         |
| `mapMetadata`            | N/D                                            |
| `pitch`                  | N/D: vista a livello strada non supportata.                |
| `pushpin` (`pp`)         | `pins`                                         |
| `zoomLevel`              | `zoom`                                         |
| `query`                  | N/D: è necessario usare il centro o il rettangolo delimitatore.     |
| `highlightEntity` (`he`) | N/D                                            |
| `style`                  | N/D                                            |
| Parametri di percorso         | N/D                                            |
| `key`                    | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication). |
| `culture` (`c`)          | `language`: vedere la documentazione relativa alle [lingue supportate](https://docs.microsoft.com/azure/azure-maps/supported-languages).   |
| `userRegion` (`ur`)      | `view`: vedere la documentazione sulle [visualizzazioni supportate](https://aka.ms/AzureMapsLocalizationViews). |

> [!NOTE]
> Mappe di Azure usa un sistema di tessere che sono di dimensioni doppie rispetto alle tessere mappa usate in Bing Maps. Di conseguenza, il valore del livello di zoom in Mappe di Azure sarà maggiore di uno rispetto a Bing Maps. Ridurre il livello di zoom di uno nelle richieste di cui si esegue la migrazione per compensare la differenza.

Per altre informazioni, vedere la [guida pratica sull'API di rendering delle immagini di mappe](https://docs.microsoft.com/azure/azure-maps/how-to-render-custom-data).

Oltre alla generazione di un'immagine di mappa statica, il servizio di rendering di Mappe di Azure offre anche la possibilità di accedere direttamente alle tessere mappa in formato raster (PNG) e vettoriale:

-   [Tessera mappa](https://docs.microsoft.com/rest/api/maps/render/getmaptile): recupera le tessere raster (PNG) e vettoriali per le mappe di base (strade, confini, sfondo).
-   [Tessera immagine di mappa](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile): recupera le tessere di immagini aeree e satellitari.

### <a name="pushpin-url-parameter-format-comparison"></a>Confronto tra il formato dei parametri URL per le puntine

**Prima: Bing Maps**

In Bing Maps è possibile aggiungere puntine a un'immagine di mappa statica usando il parametro `pushpin` nell'URL. Il parametro `pushpin` accetta una posizione nel formato `latitude,longitude`, uno stile di icona e un'etichetta di testo (fino a tre caratteri) come illustrato di seguito:

> `&pushpin=latitude,longitude;iconStyle;label`

È possibile usare altre puntine aggiungendo altri parametri `pushpin` all'URL con un set di valori diversi. Per le icone delle puntine possono è possibile usare solo gli stili predefiniti disponibili nell'API di Bing Maps.

In Bing Maps è ad esempio possibile aggiungere alla mappa una puntina rossa con l'etichetta "AB" in corrispondenza delle coordinate (longitudine:-110, latitudine: 45) con il parametro URL seguente:

> `&pushpin=45,-110;7;AB`

<center>

![Puntina di mappa statica di Bing Maps](media/migrate-bing-maps-web-service/bing-maps-static-map-pin.jpg)</center>

**Dopo: Mappe di Azure**

Anche in Mappe di Azure è possibile aggiungere puntine a un'immagine di mappa statica specificando il parametro `pins` nell'URL. Le puntine in Mappe di Azure sono definite specificando uno stile di icona e un elenco di posizioni che usano tale stile. Queste informazioni vengono quindi passate nel parametro `pins` che è possibile specificare più volte per supportare puntine con stili diversi.

> `&pins=iconType|pinStyles||pinLocation1|pinLocation2|...`

È possibile usare ulteriori stili aggiungendo altri parametri `pins` all'URL con uno stile e un set di posizioni diversi.

Per quanto riguarda le posizioni delle puntine, Mappe di Azure richiede che le coordinate siano in formato `longitude latitude`, mentre Bing Maps usa il formato `latitude,longitude`. Si noti anche che in Mappe di Azure la longitudine e la latitudine sono **separate da uno spazio, non da una virgola**.

Il valore `iconType` specifica il tipo di segnaposto da creare con i valori possibili seguenti:

-   `default`: icona predefinita del segnaposto.
-   `none`: nessuna icona visualizzata. Verrà eseguito il rendering solo delle etichette.
-   `custom`: specifica che è necessario usare un'icona personalizzata. È possibile aggiungere un URL che punta all'immagine dell'icona alla fine del parametro `pins` dopo le informazioni sulla posizione del segnaposto.
-   `{udid}`: ID dati univoco (UDID) per un'icona archiviata nella piattaforma di archiviazione dati di Mappe di Azure.

Gli stili dei segnaposto in Mappe di Azure vengono aggiunti con il formato `optionNameValue`, con più stili separati dalla barra verticale (`|`), ad esempio: `iconType|optionName1Value1|optionName2Value2`. Si noti che i nomi e i valori delle opzioni non sono separati. È possibile usare i nomi delle opzioni di stile seguenti per le puntine in Mappe di Azure:

-   `al`: specifica l'opacità (alfa) delle puntine. Può essere un numero compreso tra 0 e 1.
-   `an`: specifica l'ancoraggio del segnaposto. Valori x e y in pixel specificati nel formato `x y`.
-   `co`: colore del segnaposto. Deve essere un colore esadecimale a 24 bit: da `000000` a `FFFFFF`.
-   `la`: specifica l'ancoraggio dell'etichetta. Valori x e y in pixel specificati nel formato `x y`.
-   `lc`: colore dell'etichetta. Deve essere un colore esadecimale a 24 bit: da `000000` a `FFFFFF`.
-   `ls`: dimensioni dell'etichetta in pixel. Può essere un numero maggiore di 0.
-   `ro`: valore in gradi per la rotazione dell'icona. Può essere un numero compreso tra -360 e 360.
-   `sc`: valore di scala per l'icona del segnaposto. Può essere un numero maggiore di 0.

Vengono specificati valori di etichette per la posizione di ogni puntina, invece di un singolo valore di etichetta applicato a tutte le puntine nell'elenco di posizioni. Il valore dell'etichetta può essere costituito da una stringa di più caratteri racchiusa tra virgolette singole per assicurarsi che non venga interpretato come valore di stile o posizione.

In Mappe di Azure, ad esempio, è possibile aggiungere un'icona predefinita di colore rosso (`FF0000`) con l'etichetta "Space Needle" posizionata sotto (15 50) l'icona in corrispondenza delle coordinate (longitudine: -122.349300, latitudine: 47.620180) con il parametro URL seguente:

> `&pins=default|coFF0000|la15 50||'Space Needle'-122.349300 47.620180`

<center>

![Puntina di mappa statica di Mappe di Azure](media/migrate-bing-maps-web-service/azure-maps-static-map-pin.jpg)</center>

Nell'esempio seguente vengono aggiunti tre segnaposto con i valori di etichetta '1', '2' e '3':

> `&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12`

<center>

![Più puntine di mappa statica di Mappe di Azure](media/migrate-bing-maps-web-service/azure-maps-static-map-multiple-pins.jpg)</center>

### <a name="draw-curve-url-parameter-format-comparison"></a>Confronto tra il formato dei parametri URL per il disegno di curve

**Prima: Bing Maps**

In Bing Maps è possibile aggiungere linee e poligoni a un'immagine di mappa statica usando il parametro `drawCurve` nell'URL. Il parametro `drawCurve` accetta un tipo di forma, un tipo di stile e un elenco di posizioni di cui verrà eseguito il rendering sulla mappa, come illustrato di seguito:

> `&drawCurve=shapeType,styleType,location1,location2...`

È possibile usare ulteriori stili aggiungendo altri parametri `drawCurve` all'URL con uno stile e un set di posizioni diversi.

Le posizioni in Bing Maps sono specificate nel formato `latitude1,longitude1_latitude2,longitude2_…`. Possono anche essere codificate.

I tipi di forma in Bing Maps includono linee, poligoni, cerchio e curva. I tipi di stile includono il colore e lo spessore della linea, il colore del bordo e del riempimento, lo spessore del bordo e il raggio circolare.

In Bing Maps, ad esempio, è possibile aggiungere alla mappa una linea blu con il 50% di opacità e uno spessore di quattro pixel tra le coordinate (longitudine: -110, latitudine: 45 e longitudine: -100, latitudine: 50) con il parametro URL seguente:

`&drawCurve=l,FF000088,4;45,-110_50,-100`

<center>

![Linea di mappa statica di Bing Maps](media/migrate-bing-maps-web-service/bing-maps-static-map-line.jpg)</center>

**Dopo: Mappe di Azure**

Anche in Mappe di Azure è possibile aggiungere linee e poligoni a un'immagine di mappa statica specificando il parametro *path* nell'URL. Come in Bing Maps, in questo parametro è possibile specificare uno stile e un elenco di posizioni ed è possibile specificare il parametro *path* più volte per eseguire il rendering di più cerchi, linee e poligoni con stili diversi.

> `&path=pathStyles||pathLocation1|pathLocation2|...`

Per quanto riguarda le posizioni dei tragitti, Mappe di Azure richiede che le coordinate siano in formato `longitude latitude`, mentre Bing Maps usa il formato `latitude,longitude`. Si noti anche che in Mappe di Azure la longitudine e la latitudine sono **separate da uno spazio, non da una virgola**. Mappe di Azure attualmente non supporta tragitti codificati. È possibile caricare set di dati di dimensioni maggiori come file GeoJSON nell'API di archiviazione dei dati di Mappe di Azure, come illustrato [qui](https://docs.microsoft.com/azure/azure-maps/how-to-render-custom-data#get-data-from-azure-maps-data-storage).

Gli stili dei percorsi in Mappe di Azure vengono aggiunti con il formato `optionNameValue`, con più stili separati dalla barra verticale (`|`), ad esempio: `optionName1Value1|optionName2Value2`. Si noti che i nomi e i valori delle opzioni non sono separati. È possibile usare i nomi delle opzioni di stile seguenti per applicare uno stile ai percorsi in Mappe di Azure:

-   `fa`: opacità del colore di riempimento (alfa) usata per il rendering dei poligoni. Può essere un numero compreso tra 0 e 1.
-   `fc`: colore di riempimento usato per il rendering dell'area di un poligono.
-   `la`: opacità del colore della linea (alfa) usata per il rendering delle linee e del contorno dei poligoni. Può essere un numero compreso tra 0 e 1.
-   `lc`: colore della linea usato per il rendering delle linee e del contorno dei poligoni.
-   `lw`: larghezza della linea in pixel.
-   `ra`: specifica il raggio di un cerchio in metri.

In Mappe di Azure, ad esempio, è possibile aggiungere alla mappa una linea blu con il 50% di opacità e uno spessore di quattro pixel tra le coordinate (longitudine: -110, latitudine: 45 e longitudine: -100, latitudine: 50) con il parametro URL seguente:

> `&path=lc0000FF|la.5|lw4||-110 45|-100 50`

<center>

![Linea di mappa statica di Mappe di Azure](media/migrate-bing-maps-web-service/azure-maps-static-map-line.jpg)</center>

## <a name="calculate-a-distance-matrix"></a>Calcolare una matrice di distanze

Mappe di Azure fornisce un'API per il calcolo dei tempi di viaggio e delle distanze tra un set di posizioni come matrice di distanze. L'API della matrice di distanze di Mappe di Azure è equivalente all'API della matrice di distanze in Bing Maps:

-   [Matrice di percorsi](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): calcola in modo asincrono i tempi di viaggio e le distanze per un set di origini e destinazioni. Sono supportate fino a 700 celle per richiesta (il numero di origini moltiplicato per il numero di destinazioni). Tenendo presente questo vincolo, gli esempi di possibili dimensioni della matrice sono: `700x1`, `50x10`, `10x10`, `28x25`, `10x70`.

> [!NOTE]
> È possibile inviare una richiesta all'API della matrice di distanze solo usando una richiesta POST con le informazioni sull'origine e sulla destinazione nel corpo della richiesta.</p>
<p>Mappe di Azure richiede inoltre che tutte le origini e le destinazioni siano coordinate. È quindi prima necessario eseguire la geocodifica degli indirizzi.

La tabella seguente include i riferimenti incrociati tra i parametri delle API di Bing Maps e i parametri equivalenti delle API di Mappe di Azure.

| Parametro dell'API di Bing Maps | Parametro API di Mappe di Azure equivalente                         |
|-------------------------|-------------------------------------------------------------|
| `origins`               | `origins`: specificare nel corpo della richiesta POST come GeoJSON.    |
| `destinations`          | `destination`: specificare nel corpo della richiesta POST come GeoJSON. |
| `endTime`               | `arriveAt`                                                  |
| `startTime`             | `departAt`                                                  |
| `travelMode`            | `travelMode`                                                |
| `resolution`            | N/D                                                         |
| `distanceUnit`          | N/D: tutte le distanze in metri.                              |
| `timeUnit`              | N/D: tutti i periodi di tempo in secondi.                                 |
| `key`                   | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication). |
| `culture` (`c`)         | `language`: vedere la documentazione relativa alle [lingue supportate](https://docs.microsoft.com/azure/azure-maps/supported-languages).  |
| `userRegion` (`ur`)     | `view`: vedere la documentazione sulle [visualizzazioni supportate](https://aka.ms/AzureMapsLocalizationViews).     |

> [!TIP]
> Tutte le opzioni di pianificazione percorso avanzate disponibili nell'API di pianificazione percorso di Mappe di Azure (percorso autocarro, specifiche del motore, evitare...) sono supportate nell'API della matrice di distanze di Mappe di Azure.

## <a name="calculate-an-isochrone"></a>Calcolare una isocrona

Mappe di Azure include un'API per il calcolo di una isocrona, ovvero un poligono che copre un'area raggiungibile in qualsiasi direzione da un punto di origine entro un periodo di tempo specificato o con una quantità di carburante/carica. L'API dell'estensione di percorsi di Mappe di Azure paragonabile all'API di isocrone in Bing Maps:

-   Estensione di [percorsi](https://docs.microsoft.com/rest/api/maps/route/getrouterange)**: calcola un poligono che copre un'area raggiungibile in qualsiasi direzione da un punto di origine entro un periodo di tempo specificato, una certa distanza o una quantità di carburante/carica disponibile.

> [!NOTE]
> Mappe di Azure richiede che l'origine della query sia una coordinata. È quindi prima necessario eseguire la geocodifica degli indirizzi.</p>
<p>Inoltre, Bing Maps è in grado di calcolare isocrone in base a tempo o alla distanza, mentre Mappe di Azure in base al tempo, dalla distanza o alla quantità di carburante/carica disponibile.

La tabella seguente include i riferimenti incrociati tra i parametri delle API di Bing Maps e i parametri equivalenti delle API di Mappe di Azure.

| Parametro dell'API di Bing Maps      | Parametro API di Mappe di Azure equivalente            |
|------------------------------|------------------------------------------------|
| `waypoint` (`wp`)            | `query`                                        |
| `dateTime` (`dt`)            | `departAt`                                     |
| `maxTime`                    | `timeBudgetInSec`                              |
| `timeUnit` (`tu`)            | N/D: tutti i periodi di tempo in secondi.                    |
| `travelMode` (`mode`)        | `travelMode`                                   |
| `maxDistance` (`maxDis`)     | `distanceBudgetInMeters`                       |
| `distanceUnit` (`du`)        | N/D: tutte le distanze in metri.                 |
| `optimize` (`optmz`)         | `routeType`                                    |
| `key`                        | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication). |
| `culture` (`c`)              | `language`: vedere la documentazione relativa alle [lingue supportate](https://docs.microsoft.com/azure/azure-maps/supported-languages).  |
| `userRegion` (`ur`)          | `view`: vedere la documentazione sulle [visualizzazioni supportate](https://aka.ms/AzureMapsLocalizationViews). |

> [!TIP]
> Tutte le opzioni avanzate di pianificazione percorso disponibili nell'API di pianificazione percorso di Mappe di Azure (percorso di camion, specifiche del motore e così via) sono supportate nell'API di isocrone di Mappe di Azure.

## <a name="search-for-points-of-interest"></a>Cercare i punti di interesse

È possibile cercare i dati dei punti di interesse in Bing Maps usando le API seguenti:

-   **Ricerca locale:** cerca i punti di interesse vicini (ricerca radiale), per nome o per tipo di entità (categoria). Le API di [ricerca di punti di interesse](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) e di [ricerca di categorie di punti di interesse](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) di Mappe di Azure sono molto simili a questa API.
-   **Riconoscimento della posizione**: cerca i punti di interesse che si trovano entro una certa distanza da una posizione. L'API di [ricerca nelle vicinanze](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) di Mappe di Azure è molto simile a questa API.
-   **Informazioni locali:** cerca i punti di interesse che si trovano entro un tempo di guida massimo specificato o entro la distanza da una coordinata specifica. Questa funzionalità si può ottenere con Mappe di Azure calcolando prima una isocrona e poi passandola all'API di [ricerca entro un'area geometrica](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry).

Mappe di Azure offre numerose API di ricerca per i punti di interesse:

-   [Ricerca di punti di interesse](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): consente di cercare i punti di interesse in base al nome. Ad esempio, `"starbucks"`.
-   [Ricerca per categoria di punti di interesse](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): consente di cercare i punti di interesse in base alla categoria. Ad esempio, "ristorante".
-   [Ricerca nelle vicinanze](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): cerca i punti di interesse che si trovano entro una certa distanza da una posizione.
-   [Ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): Questa API combina la geocodifica degli indirizzi con la ricerca di punti di interesse. Accetta una stringa in formato libero che può essere un indirizzo, un luogo, un punto di riferimento, un punto di interesse o una categoria di punti di interesse ed elabora immediatamente la richiesta. Questa API è consigliata per le applicazioni in cui gli utenti possono cercare indirizzi o punti di interesse dalla stessa casella di testo.
-   [Ricerca entro un'area geometrica](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): consente di cercare i punti di interesse all'interno di una geometria specificata (poligono).
-   [Ricerca lungo un percorso](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): consente di cercare i punti di interesse lungo un percorso specificato.
-   [Ricerca fuzzy batch](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): consente di creare una richiesta contenente fino a 10.000 indirizzi, luoghi, punti di riferimento o punti di interesse e li elabora in un certo periodo di tempo. Tutti i dati verranno elaborati in parallelo nel server e, al termine dell'operazione, sarà possibile scaricare il set di risultati completo.

Assicurarsi di esaminare la documentazione relativa alle [procedure consigliate per la ricerca](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search).

## <a name="get-traffic-incidents"></a>Ottenere informazioni sugli incidenti stradali

Mappe di Azure offre diverse API per recuperare i dati sul traffico. Sono disponibili due tipi di dati sul traffico:

-   **Dati sul flusso**: metriche sul flusso di traffico in sezioni di strade. Si usano spesso per applicare codici colore alle strade. Questi dati vengono aggiornati ogni 2 minuti.
-   **Dati sugli incidenti**: dati su lavori in corso, chiusure di strade, incidenti e altri eventi che possono influire sul traffico. Questi dati vengono aggiornati ogni minuto.

Bing Maps fornisce i dati sul flusso del traffico e sugli incidenti nei controlli mappa interattivi e rende anche disponibili i dati sugli incidenti come servizio.

I dati sul traffico sono integrati anche nei controlli mappa interattivi di Mappe di Azure. Mappe di Azure offre anche le seguenti API di servizi sul traffico:

-   [Segmenti del flusso di traffico](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficflowsegment): informazioni sulla velocità e i tempi di viaggio del pezzo di strada più vicino alle coordinate specificate.
-   [Tessere del flusso del traffico](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficflowtile): tessere raster e vettoriali contenenti dati sul flusso del traffico. Possono essere usate con i controlli di Mappe di Azure o in controlli mappa di terze parti come Leaflet. Le tessere vettoriali possono anche essere usate per l'analisi avanzata dei dati.
-   [Dettagli degli incidenti stradali](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidentdetail): dettagli degli incidenti stradali inclusi in un rettangolo delimitatore, un livello di zoom e un modello di traffico.
-   [Tessere di incidenti stradali](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidenttile): tessere raster e vettoriali contenenti dati sugli incidenti stradali.
-   [Riquadro di visualizzazione di incidenti stradali](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidentviewport): recupera le informazioni legali e tecniche per il riquadro di visualizzazione descritto nella richiesta, ad esempio l'ID modello del traffico.

La tabella seguente include i riferimenti incrociati tra i parametri delle API del traffico di Bing Maps e i parametri equivalenti delle API di dettagli degli incidenti stradali di Mappe di Azure.

| Parametro dell'API di Bing Maps  | Parametro API di Mappe di Azure equivalente   |
|--------------------------|---------------------------------------|
| `mapArea`                | `boundingBox` e `boundingZoom`      |
| `includeLocationCodes`   | N/D                                   |
| `severity` (`s`)         | N/D: tutti i dati restituiti               |
| `type` (`t`)             | N/D: tutti i dati restituiti               |
| `key`                    | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication). |
| `culture` (`c`)          | `language`: vedere la documentazione relativa alle [lingue supportate](https://docs.microsoft.com/azure/azure-maps/supported-languages). |
| `userRegion` (`ur`)      | `view`: vedere la documentazione sulle [visualizzazioni supportate](https://aka.ms/AzureMapsLocalizationViews). |

## <a name="get-a-time-zone"></a>Ottenere un fuso orario

Mappe di Azure fornisce un'API per il recupero del fuso orario in cui si trova una coordinata. L'API per i fusi orari di Mappe di Azure è equivalente all'API per i fusi orari di Bing Maps:

-   [Fuso orario per coordinata](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): consente di specificare una coordinata e ottenere i dettagli relativi al fuso orario in cui si trova.

La tabella seguente include i riferimenti incrociati tra i parametri delle API di Bing Maps e i parametri equivalenti delle API di Mappe di Azure.

| Parametro dell'API di Bing Maps | Parametro API di Mappe di Azure equivalente          |
|-------------------------|----------------------------------------------|
| `point`                 | `query`                                      |
| `query`                 | N/D: le posizioni devono essere prima geocodificate.      |
| `dateTime`              | `timeStamp`                                  |
| `includeDstRules`       | N/D: sempre incluse nella risposta da mappe di Azure. |
| `key`                   | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication). |
| `culture` (`c`)         | `language`: vedere la documentazione relativa alle [lingue supportate](https://docs.microsoft.com/azure/azure-maps/supported-languages).  |
| `userRegion` (`ur`)     | `view`: vedere la documentazione sulle [visualizzazioni supportate](https://aka.ms/AzureMapsLocalizationViews).  |

Oltre a questo, la piattaforma Mappe di Azure fornisce anche alcune API aggiuntive per i fusi orari per semplificare le conversioni con ID e nomi dei fusi orari:

-   [Fuso orario in base all'ID](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): Restituisce le informazioni sul fuso orario correnti, cronologiche e future per l'ID del fuso orario IANA specificato.
-   [Enumerazione fusi orari IANA](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): restituisce un elenco completo di ID di fusi orari IANA. Gli aggiornamenti al servizio IANA si riflettono nel sistema entro un giorno. 
-   [Enumerazione fusi orari Windows](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): restituisce un elenco completo di ID di fusi orari Windows.
-   [Versione fuso orario IANA](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): restituisce il numero di versione IANA corrente usato da Mappe di Azure. 
-   [Fuso orario da Windows a IANA](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): restituisce l'ID IANA corrispondente da ID di fuso orario Windows valido. Per un singolo ID Windows è possibile che vengano restituiti più ID IANA.

## <a name="spatial-data-services-sds"></a>Servizi dati spaziali (SDS)

I servizi dati spaziali di Bing Maps forniscono tre funzionalità principali:

-   Geocodifica batch: elaborazione di un batch di grandi dimensioni di geocodici di indirizzi con un'unica richiesta.
-   Recupero di dati dei confini amministrativi: uso di una coordinata per ottenere il confine di intersezione per un tipo di entità specificato.
-   Hosting e query di dati aziendali spaziali: caricamento di una semplice tabella 2D di dati accessibile con alcune semplici query spaziali.

### <a name="batch-geocode-data"></a>Dati di geocodifica batch

Il processo di geocodifica batch passa in una singola richiesta a un servizio un numero elevato di indirizzi o luoghi che vengono tutti geocodificati in parallelo, quindi i risultati vengono restituiti in una singola risposta.

Bing Maps consente di passare fino a 200.000 indirizzi in una singola richiesta di geocodifica batch. Questa richiesta viene inserita in una coda e in genere viene elaborata in un periodo di tempo che varia da pochi minuti ad alcune ore, a seconda delle dimensioni del set di dati e del carico del servizio. Ogni indirizzo nella richiesta genera una transazione.

Azure Maps include un servizio di geocodifica batch, ma consente di passare fino a 10.000 indirizzi in una singola richiesta e l'elaborazione richiede da pochi secondi ad alcuni minuti, a seconda delle dimensioni del set di dati e del carico del servizio. Ogni indirizzo nella richiesta genera una transazione. In Mappe di Azure il servizio di geocodifica batch è disponibile solo nel livello S1.

Un'altra opzione per la geocodifica di un numero elevato di indirizzi in Mappe di Azure consiste nell'effettuare richieste parallele all'API di ricerca standard. Questi servizi accettano un solo indirizzo per ogni richiesta, ma possono essere usati con il livello S0 che prevede anche limiti di utilizzo gratuiti. Il livello S0 consente di effettuare fino a 50 richieste al secondo per la piattaforma Mappe di Azure da un singolo account. Quindi, per rimanere entro tale limite, è possibile geocodificare fino a 180.000 indirizzi in un'ora. Il livello S1 non ha un limite documentato per il numero di query al secondo che possono essere effettuate da un account, pertanto è possibile elaborare molti più dati più rapidamente quando si usa tale piano tariffario. Tuttavia, l'uso del servizio di geocodifica batch consente di ridurre la quantità totale di dati trasferiti e di ridurre drasticamente il traffico di rete.

-   [Geocodifica di indirizzi in formato libero](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): la richiesta viene elaborata immediatamente quando si specifica una singola stringa di indirizzo, ad esempio `"1 Microsoft way, Redmond, WA"`. Questo servizio è consigliato se è necessario geocodificare rapidamente singoli indirizzi.
-   [Geocodifica di indirizzi strutturati](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): consente di specificare le parti di un singolo indirizzo, ad esempio nome della via, città, paese e codice postale, e di elaborare immediatamente la richiesta. Questo servizio è consigliato se è necessario geocodificare rapidamente singoli indirizzi e i dati sono già stati analizzati nelle singole parti dell'indirizzo.
-   [Geocodifica di indirizzi batch](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): consente di creare una richiesta contenente fino a 10.000 indirizzi e li elabora in un certo periodo di tempo. Verrà eseguita la geocodifica di tutti gli indirizzi in parallelo nel server e, al termine dell'operazione, sarà possibile scaricare il set di risultati completo. Questo servizio è consigliato per la geocodifica di set di dati di grandi dimensioni.
-   [Ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): Questa API combina la geocodifica degli indirizzi con la ricerca di punti di interesse. Accetta una stringa in formato libero che può essere un indirizzo, un luogo, un punto di riferimento, un punto di interesse o una categoria di punti di interesse ed elabora immediatamente la richiesta. Questa API è consigliata per le applicazioni in cui gli utenti possono cercare indirizzi o punti di interesse dalla stessa casella di testo.
-   [Ricerca fuzzy batch](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): consente di creare una richiesta contenente fino a 10.000 indirizzi, luoghi, punti di riferimento o punti di interesse e li elabora in un certo periodo di tempo. Tutti i dati verranno elaborati in parallelo nel server e, al termine dell'operazione, sarà possibile scaricare il set di risultati completo.

### <a name="get-administrative-boundary-data"></a>Ottenere dati sui confini amministrativi

In Bing Maps i confini amministrativi per paesi, stati, contee, città e codici postali vengono resi disponibili tramite l'API Geodata. Questa API accetta una coordinata o una query per la geocodifica. Se viene passata una query, viene geocodificata e vengono usate le coordinate del primo risultato. Questa API considera le coordinate e recupera il confine del tipo di entità specificato che interseca la coordinata. Si noti che questa API non restituisce necessariamente il confine per la query passata. Se viene passata una query per `"Seattle, WA"`, ma il valore del tipo di entità è impostato sull'area di un paese, viene restituito il confine per gli Stati Uniti.

Anche Mappe di Azure fornisce l'accesso ai confini amministrativi (paesi, stati, contee, città e codici postali). Per recuperare un confine, è necessario eseguire una query su una delle API di ricerca, ad esempio `Seattle, WA`. Se al risultato della ricerca è associato un confine, nella risposta verrà fornito un ID geometria. È quindi possibile usare l'API di ricerca poligoni per recuperare i confini esatti per uno o più ID geometria. Questa funzionalità è leggermente diversa da quella di Bing Maps, perché Mappe di Azure restituisce il confine in base al termine di ricerca, mentre Bing Maps restituisce un confine per un tipo di entità specificato in corrispondenza di una coordinata specificata. Inoltre, i dati dei confini restituiti da Mappe di Azure sono in formato GeoJSON.

Per ricapitolare:

1.  Passare una query per il confine da ricevere in una delle API di ricerca seguenti.
    -   [Geocodifica di indirizzi in formato libero](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)
    -   [Geocodifica di indirizzi strutturati](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured)
    -   [Geocodifica di indirizzi batch](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview)
    -   [Ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
    -   [Ricerca fuzzy batch](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)
2.  Se i risultati desiderati hanno uno o più ID di area geometrica, passarli all'[API di ricerca poligoni](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

### <a name="host-and-query-spatial-business-data"></a>Hosting e query di dati aziendali spaziali

I servizi dati spaziali di Bing Maps forniscono una semplice soluzione di archiviazione dei dati spaziali per ospitare i dati aziendali ed esporli come servizio REST spaziale. Questo servizio fornisce quattro query principali: trova per proprietà, trova nelle vicinanze, trova nel rettangolo delimitatore e trova con 1 miglio di un percorso. Molte aziende che usano questo servizio, spesso hanno già dati aziendali archiviati in un database, un piccolo subset dei quali è stato caricato nel servizio per applicazioni come i localizzatori di punti vendita. Poiché l'autenticazione basata su chiavi offre sicurezza di base, è consigliabile usare questo servizio solo con i dati disponibili pubblicamente.

La maggior parte dei dati aziendali sulle posizioni inizia in un database. Di conseguenza, è consigliabile usare soluzioni di archiviazione di Azure esistenti, ad esempio SQL di Azure o Azure PostgreSQL (con il plug-in PostGIS). Entrambe queste soluzioni di archiviazione supportano i dati spaziali e forniscono un set completo di funzionalità per query spaziali. I dati inseriti in una soluzione di archiviazione idonea possono quindi essere integrati nell'applicazione creando un servizio Web personalizzato oppure usando un framework come ASP.NET o Entity Framework. Con questo approccio è possibile ottenere più funzionalità per le query, oltre a opzioni di sicurezza molto più avanzate.

Anche Azure Cosmos DB offre un set limitato di funzionalità spaziali che, a seconda dello scenario, potrebbero essere sufficienti.

Ecco alcune risorse utili per l'hosting e le query di dati spaziali in Azure.

-   [Panoramica dei tipi di dati spaziali di SQL di Azure](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview)
-   [Funzionalità spaziali di SQL di Azure - Query sul vicino più prossimo](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor)
-   [Panoramica delle funzionalità geospaziali di Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/geospatial)

## <a name="client-libraries"></a>Librerie client

Mappe di Azure fornisce le librerie client per i linguaggi di programmazione seguenti:

-   JavaScript, TypeScript, Node.js - [Documentazione](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module) \| [Pacchetto NPM](https://www.npmjs.com/package/azure-maps-rest)

Librerie client open source per altri linguaggi di programmazione:

-   .NET Standard 2.0 - [Progetto GitHub](https://github.com/perfahlen/AzureMapsRestServices) \| [Pacchetto NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui servizi REST di Mappe di Azure.

> [!div class="nextstepaction"]
> [Procedure consigliate per l'uso del servizio di ricerca](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Procedure consigliate per l'uso del servizio di pianificazione percorso](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Come usare il modulo dei servizi (Web SDK)](how-to-use-best-practices-for-routing.md)

> [!div class="nextstepaction"]
> [Documentazione di riferimento per l'API del servizio REST di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/)

> [!div class="nextstepaction"]
> [Esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-maps)

---
title: Eseguire la migrazione di servizi Web da Google Maps | Microsoft Docs
description: Esercitazione su come eseguire la migrazione di servizi Web da Google Maps a mappe Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 8fc3270d15f77576ff353a110d8a3a9a17c7144d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480021"
---
# <a name="migrate-web-service-from-google-maps"></a>Eseguire la migrazione del servizio Web da Google Maps

Sia Azure che Google Maps forniscono l'accesso alle API spaziali tramite i servizi Web REST. Le interfacce API per queste piattaforme eseguono funzionalità simili, ma usano convenzioni di denominazione e oggetti di risposta diversi.

La tabella seguente fornisce le API del servizio mappe di Azure che forniscono funzionalità simili alle API del servizio Google Maps elencate.

| API del servizio Google Maps | API del servizio Maps di Azure                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Directions              | [Route](https://docs.microsoft.com/rest/api/maps/route)                               |
| Matrice di distanza         | [Matrice di route](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) |
| Geocodifica               | [Ricerca](https://docs.microsoft.com/rest/api/maps/search)                             |
| Cerca in posizioni           | [Ricerca](https://docs.microsoft.com/rest/api/maps/search)                             |
| Inserisci completamento automatico      | [Ricerca](https://docs.microsoft.com/rest/api/maps/search)                             |
| Mappa statica              | [Rendering](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                 |
| Fuso orario               | [Fuso orario](https://docs.microsoft.com/rest/api/maps/timezone)                        |

Le seguenti API del servizio non sono attualmente disponibili in Maps di Azure:

- Altitudine
- Georilevazione
- Inserisce dettagli e foto. I numeri di telefono e l'URL del sito Web disponibili nell'API di ricerca Maps di Azure.
- URL mappa
- Strade: i dati relativi ai limiti di velocità sono disponibili tramite le API routing e geocodifica inversa nelle mappe di Azure.
- Visualizzazione statica via

Azure Maps include diversi servizi Web REST aggiuntivi che possono essere di interesse:

- [Operazioni spaziali](https://docs.microsoft.com/rest/api/maps/spatial): eseguire l'offload di operazioni e calcoli spaziali complessi, ad esempio la geoschermatura, in un servizio.
- [Traffico](https://docs.microsoft.com/rest/api/maps/traffic): accedere al flusso di traffico in tempo reale e ai dati degli eventi imprevisti.

## <a name="geocoding-addresses"></a>Indirizzi di geocodifica

La geocodifica è il processo di conversione di un indirizzo (ad esempio "1 Microsoft Way, Redmond, WA") in una coordinata (ad esempio, Longitudine:-122,1298, Latitudine: 47,64005). Le coordinate vengono quindi usate spesso per posizionare un marcatore su una mappa o al centro di una mappa.

Azure Maps offre diversi metodi per la geocodifica degli indirizzi:

- [**Geocodifica degli indirizzi in formato libero**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): specificare una singola stringa di indirizzo, ad esempio "1 Microsoft Way, Redmond, WA", ed elaborare immediatamente la richiesta. Questa opzione è consigliata se è necessario geocodificare rapidamente i singoli indirizzi.
- [**Geocodifica degli indirizzi strutturati**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): specificare le parti di un singolo indirizzo, ad esempio via nome, città, paese e codice postale ed elaborare immediatamente la richiesta. Questa opzione è consigliata se è necessario geocodificare i singoli indirizzi rapidamente e i dati sono già analizzati nelle singole parti dell'indirizzo.
- [**Geocodifica degli indirizzi batch**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): creare una richiesta contenente fino a 10.000 indirizzi e elaborarli in un determinato periodo di tempo. Tutti gli indirizzi verranno geocodificati in parallelo sul server e, al termine del completamento, sarà possibile scaricare il set di risultati completo. Questa operazione è consigliata per la geocodifica di set di dati di grandi dimensioni.
- [**Ricerca fuzzy**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): questa API combina la geocodifica degli indirizzi con la ricerca di punti di interesse. Questa API accetta una stringa in formato libero che può essere un indirizzo, un luogo, un punto di riferimento, un punto di interesse o una categoria di punti di interesse ed elabora immediatamente la richiesta. Questa API è consigliata per le applicazioni in cui gli utenti possono cercare indirizzi o punti di interesse dalla stessa casella di testo.
- [**Ricerca in batch fuzzy**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): creare una richiesta contenente fino a 10.000 indirizzi, luoghi, punti di riferimento o punti di interesse ed elaborarli in un determinato periodo di tempo. Tutti i dati verranno elaborati in parallelo sul server e, al termine del completamento, sarà possibile scaricare il set di risultati completo.

La tabella seguente fa riferimento incrociato ai parametri dell'API Google Maps con i parametri dell'API paragonabili in mappe di Azure.

| Parametro API Google Maps | Parametro API Maps di Azure paragonabile  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` e `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality`-città/città<br/>`municipalitySubdivision`-Neighborhood, Sub/Super City<br/>stato `countrySubdivision` o provincia<br/>`countrySecondarySubdivision`-County<br/>`countryTertiarySubdivision`-distretto<br/>`countryCode`-codice paese a due lettere |
| `key`                       | `subscription-key`: vedere anche la documentazione relativa all' [autenticazione con Maps di Azure](azure-maps-authentication.md) . |
| `language`                  | `language`: vedere la documentazione relativa alle [lingue supportate](supported-languages.md) .  |
| `region`                    | `countrySet`                       |

Un esempio di come usare il servizio di ricerca è documentato [qui](how-to-search-for-address.md). Assicurarsi di esaminare le [procedure consigliate per la documentazione di ricerca](how-to-use-best-practices-for-search.md) .

> [!TIP]
> Le API per la geocodifica e la ricerca fuzzy degli indirizzi in formato libero possono essere usate in modalità di completamento automatico aggiungendo `&amp;typeahead=true` all'URL della richiesta. In questo modo si indica al server che il testo di input è probabilmente parziale e passa alla modalità predittiva.

## <a name="reverse-geocode-a-coordinate"></a>Inverti la codifica geocodifica di una coordinata

La geocodifica inversa è il processo di conversione delle coordinate geografiche (ad esempio, Longitudine:-122,1298, Latitudine: 47,64005) nell'indirizzo approssimativo (ad esempio "1 Microsoft Way, Redmond, WA").

Azure Maps offre diversi metodi di geocodifica inversa:

- [**Address Reverse geocoder**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): specificare una singola coordinata geografica per ottenere l'indirizzo approssimativo ed elaborare immediatamente la richiesta.
- [**Geocodificatore inverso tra le strade**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): specificare una singola coordinata geografica per ottenere le informazioni più vicine, ad esempio 1 & principale, ed elaborare immediatamente la richiesta.
- [**Geocodificatore Reverse indirizzo batch**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): creare una richiesta contenente fino a 10.000 coordinate e elaborarle in un periodo di tempo. Tutti i dati verranno elaborati in parallelo sul server e, al termine del completamento, sarà possibile scaricare il set di risultati completo.

La tabella seguente fa riferimento incrociato ai parametri dell'API Google Maps con i parametri dell'API paragonabili in mappe di Azure.

| Parametro API Google Maps   | Parametro API Maps di Azure paragonabile   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key`: vedere anche la documentazione relativa all' [autenticazione con Maps di Azure](azure-maps-authentication.md) . |
| `language`                  | `language`: vedere la documentazione relativa alle [lingue supportate](supported-languages.md) .  |
| `latlng`                    | `query`  |
| `location_type`             | *N/D*     |
| `result_type`               | `entityType`    |

Assicurarsi di esaminare le [procedure consigliate per la documentazione di ricerca](how-to-use-best-practices-for-search.md) .

L'API di geocodifica inversa di mappe di Azure include alcune funzionalità aggiuntive non disponibili in Google Maps che potrebbero essere utili per l'integrazione quando si esegue la migrazione dell'app:

- Recuperare i dati sui limiti di velocità.
- Recuperare le informazioni sull'uso della strada: strada locale, arteriosa, accesso limitato, rampa e così via.
- Il lato della strada su cui cade la coordinata.

## <a name="search-for-points-of-interest"></a>Cercare i punti di interesse

È possibile cercare i dati del punto di interesse in Google Maps usando l'API di ricerca. Questa API offre tre modi diversi per cercare i punti di interesse:

- **Trova luogo da testo:** Cerca un punto di interesse in base al nome, all'indirizzo o al numero di telefono.
- **Ricerca nelle vicinanze**: Cerca i punti di interesse che si trovano entro una certa distanza da una località.
- **Ricerca di testo:** Cerca le posizioni usando un testo in formato libero che includa informazioni sul punto di interesse e sulla posizione. Ad esempio, "pizza in New York" o "ristoranti vicino alla Main St".

Azure Maps offre diverse API di ricerca per i punti di interesse:

- [**Ricerca**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)dei punti di interesse: ricerca di punti di interesse in base al nome. Ad esempio, "Starbucks".
- [**Ricerca della categoria**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory)del punto di interesse: consente di cercare i punti di interesse per categoria. Ad esempio, "Restaurant".
- [**Ricerca nelle vicinanze**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): Cerca i punti di interesse che si trovano entro una certa distanza da una località.
- [**Ricerca fuzzy**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): questa API combina la geocodifica degli indirizzi con la ricerca di punti di interesse. Questa API accetta una stringa in formato libero che può essere un indirizzo, un luogo, un punto di riferimento, un punto di interesse o una categoria di punti di interesse ed elabora immediatamente la richiesta. Questa API è consigliata per le applicazioni in cui gli utenti possono cercare indirizzi o punti di interesse dalla stessa casella di testo.
- [**Cerca in geometria**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): Cerca i punti di interesse all'interno di una geometria specificata (poligono).
- [**Cerca lungo**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute)il percorso: Cerca i punti di interesse che si trovano lungo un percorso di route specificato.
- [**Ricerca in batch fuzzy**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): creare una richiesta contenente fino a 10.000 indirizzi, luoghi, punti di riferimento o punti di interesse ed elaborarli in un determinato periodo di tempo. Tutti i dati verranno elaborati in parallelo sul server e, al termine del completamento, sarà possibile scaricare il set di risultati completo.

Attualmente Maps di Azure non dispone di un'API paragonabile all'API di ricerca del testo di Google Maps.

> [!TIP]
> Le API di ricerca, ricerca e ricerca fuzzy possono essere usate in modalità di completamento automatico aggiungendo `&amp;typeahead=true` all'URL della richiesta. In questo modo si indica al server che il testo di input è probabilmente parziale e passa alla modalità predittiva.

Assicurarsi di esaminare le [procedure consigliate per la documentazione di ricerca](how-to-use-best-practices-for-search.md) .

### <a name="find-place-from-text"></a>Trova luogo da testo

Per cercare i punti di interesse in base al nome o all'indirizzo, è possibile usare le API di [ricerca](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) e ricerca [fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) di Azure maps.

La tabella seguente fa riferimento incrociato ai parametri dell'API Google Maps con i parametri dell'API paragonabili in mappe di Azure.

| Parametro API Google Maps | Parametro API Maps di Azure paragonabile |
|---------------------------|-------------------------------------|
| `fields`                  | *N/D*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/D*                               |
| `key`                     | `subscription-key`: vedere anche la documentazione relativa all' [autenticazione con Maps di Azure](azure-maps-authentication.md) . |
| `language`                | `language`: vedere la documentazione relativa alle [lingue supportate](supported-languages.md) .  |
| `locationbias`            | `lat`, `lon` e `radius`<br/>`topLeft` e `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Ricerca nelle vicinanze

È possibile recuperare i punti di interesse nelle mappe di Azure usando l'API di [ricerca vicina](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) .

La tabella seguente fa riferimento incrociato ai parametri dell'API Google Maps con i parametri dell'API paragonabili in mappe di Azure.

| Parametro API Google Maps | Parametro API Maps di Azure paragonabile  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key`: vedere anche la documentazione relativa all' [autenticazione con Maps di Azure](azure-maps-authentication.md) . |
| `keyword`                   | `categorySet` e `brandSet`        |
| `language`                  | `language`: vedere la documentazione relativa alle [lingue supportate](supported-languages.md) .  |
| `location`                  | `lat` e `lon`                     |
| `maxprice`                  | *N/D*                               |
| `minprice`                  | *N/D*                               |
| `name`                      | `categorySet` e `brandSet`        |
| `opennow`                   | *N/D*                               |
| `pagetoken`                 | `ofs` e `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N/D*                               |
| `type`                      | `categorySet –` vedere la documentazione relativa alle [categorie di ricerca supportate](supported-search-categories.md) .   |

## <a name="calculate-routes-and-directions"></a>Calcola Route e direzioni

Mappe di Azure può essere usato per calcolare Route e direzioni. Azure Maps offre molte delle funzionalità del servizio di routing di Google Maps, ad esempio:

- Orari di arrivo e di partenza.
- Route di traffico in tempo reale e predittive.
- modalità di trasporto diverse; guidare, camminare, biciclico.

> [!NOTE]
> Mappe di Azure richiede che tutti i waypoint siano coordinate. Gli indirizzi dovranno prima essere geocodificati.

Il servizio di routing Azure Maps fornisce le API seguenti per il calcolo delle route:

- [**Calcola Route**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): calcolare una route e richiedere l'elaborazione immediata della richiesta. Questa API supporta le richieste GET e POST. Le richieste POST sono consigliate quando si specifica un numero elevato di waypoint o quando si usano molte opzioni di route per assicurarsi che la richiesta URL non diventi troppo lunga e causi problemi.
- [**Route batch**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): creare una richiesta contenente fino a 1.000 richieste di route ed elaborarle in un determinato periodo di tempo. Tutti i dati verranno elaborati in parallelo sul server e, al termine del completamento, sarà possibile scaricare il set di risultati completo.
- [**Servizi Mobility**](https://docs.microsoft.com/rest/api/maps/mobility): calcola le route e le direzioni usando il trasporto pubblico.

La tabella seguente fa riferimento incrociato ai parametri dell'API Google Maps con i parametri dell'API paragonabili in mappe di Azure.

| Parametro API Google Maps    | Parametro API Maps di Azure paragonabile  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query`: coordinate nel formato `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key`: vedere anche la documentazione relativa all' [autenticazione con Maps di Azure](azure-maps-authentication.md) . |
| `language`                     | `language`: vedere la documentazione relativa alle [lingue supportate](supported-languages.md) .   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *N/A* : si tratta di una funzionalità correlata alla geocodifica. Usare il parametro *countrySet* quando si usa l'API di geocodifica di Azure maps.  |
| `traffic_model`               | *N/A* : è possibile specificare solo se i dati di traffico devono essere utilizzati con il parametro di *traffico* . |
| `transit_mode`                | Vedere la [documentazione dei servizi Mobility](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Vedere la [documentazione dei servizi Mobility](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *N/A* : Azure Maps usa solo il sistema metrico.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Per impostazione predefinita, l'API route di Azure maps restituisce solo un riepilogo (distanza e ora) e le coordinate per il percorso della route. Usare il parametro `instructionsType` per recuperare le istruzioni di turn-by-turn. Il parametro `routeRepresentation` può essere utilizzato per filtrare il riepilogo e il percorso della route.

L'API di routing Maps di Azure include molte funzionalità aggiuntive non disponibili in Google Maps che potrebbero essere utili per l'integrazione quando si esegue la migrazione dell'app:

- Supporto per il tipo di route: più breve, più veloce, Trillo e con maggiore efficienza del carburante.
- Supporto per modalità di viaggio aggiuntive: bus, motocicletta, taxi, camion e Van.
- Supporto per i waypoint 150.
- Calcolo di più tempi di viaggio in un'unica richiesta; traffico cronologico, traffico in tempo reale, nessun traffico.
- Evitare tipi di strade aggiuntivi: strade di carpooling, strade non pavimentate, strade già usate.
- Specificare le aree personalizzate da evitare.
- Limitare l'elevazione in modo che la route possa ascendere a.
- Routing basato su specifiche del motore. Calcola le route per combuste o veicoli elettrici in base alle rispettive specifiche del carburante, dell'addebito e del motore.
- Supporto per i parametri di route per veicoli commerciali; dimensioni del veicolo, peso, numero di Axel e tipo di carico.
- Specificare la velocità massima del veicolo.

Inoltre, il servizio di routing in Azure Maps supporta anche il [calcolo di intervalli instradabili](https://docs.microsoft.com/rest/api/maps/route/getrouterange), noti anche come isocrone, che generano un poligono che copre un'area che può essere spostata in qualsiasi direzione da un punto di origine entro un periodo di tempo specificato o una quantità di carburante/costi.

## <a name="retrieve-a-map-image"></a>Recuperare un'immagine mappa

Azure Maps fornisce un'API per il rendering delle immagini della mappa statica con sovrapposti ai dati. L'API di [rendering dell'immagine mappa](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) di Azure Maps è paragonabile all'API mappa statica in Google Maps.

> [!NOTE]
> Mappe di Azure richiede che il centro, tutti i percorsi del marcatore e del percorso siano coordinate nel formato "Longitudine, latitudine", mentre Google Maps usa il formato "latitudine, Longitudine". Gli indirizzi dovranno prima essere geocodificati.

La tabella seguente fa riferimento incrociato ai parametri dell'API Google Maps con i parametri dell'API paragonabili in mappe di Azure.

| Parametro API Google Maps | Parametro API Maps di Azure paragonabile  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format`: specificato come parte del percorso URL. Attualmente è supportato solo il formato PNG. |
| `key`                       | `subscription-key`: vedere anche la documentazione relativa all' [autenticazione con Maps di Azure](azure-maps-authentication.md) . |
| `language`                  | `language`: vedere la documentazione relativa alle [lingue supportate](supported-languages.md) .  |
| `maptype`                   | `layer` e `style`: vedere la documentazione relativa agli [stili di mappa supportati](supported-map-styles.md) . |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N/A* : si tratta di una funzionalità correlata alla geocodifica. Usare il parametro `countrySet` quando si usa l'API di geocodifica di Azure maps.  |
| `scale`                     | *N/D*                              |
| `size`                      | `width` e `height`: possono avere dimensioni fino a 8192x8192. |
| `style`                     | *N/D*                              |
| `visible`                   | *N/D*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> Azure Maps usa un sistema di riquadri con riquadri che corrispondono al doppio delle dimensioni delle tessere mappa usate in Google Maps. Di conseguenza, il valore del livello di zoom in mappe di Azure verrà visualizzato un livello di zoom più vicino a Maps di Azure rispetto a Google Maps. Abbassare il livello di zoom nelle richieste di cui si esegue la migrazione di uno per compensare questo problema.

Per ulteriori informazioni, vedere la [Guida alle procedure sull'API di rendering dell'immagine della mappa](how-to-render-custom-data.md).

Oltre a essere in grado di generare un'immagine mappa statica, il servizio di rendering di Azure Maps offre anche la possibilità di accedere direttamente alle tessere mappa in formato raster (PNG) e vettoriali:

- [**Tessera mappa**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): recupera i riquadri raster (png) e vettoriali per le mappe di base (strade, limiti, sfondo).
- [**Riquadro immagini mappa**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile): recuperare i riquadri delle immagini aeree e satellite.

> [!TIP]
> Molte applicazioni di Google Maps sono passate da esperienze di mapping interattive a immagini statiche mappate pochi anni fa come un metodo di risparmio sui costi. Nelle mappe di Azure, spesso è molto più conveniente usare il controllo interattivo della mappa in Web SDK, perché viene addebitato in base ai carichi delle tessere mappa. Le tessere mappa in mappe di Azure sono di grandi dimensioni e spesso richiede solo pochi per ricreare la stessa vista mappa di una mappa statica e i riquadri della mappa vengono memorizzati nella cache automaticamente dal browser. Di conseguenza, il controllo mappa interattiva spesso genera solo una frazione di una transazione durante la riproduzione di una visualizzazione mappa statica. La panoramica e lo zoom caricherà più riquadri, tuttavia sono disponibili opzioni nel controllo mappa per disabilitare questo comportamento, se lo si desidera. Il controllo mappa interattiva fornisce anche altre opzioni di visualizzazione rispetto a quelle dei servizi mappa statici.

### <a name="marker-url-parameter-format-comparison"></a>Confronto formato parametri URL marcatore

**Prima: Google Maps**

È possibile aggiungere i marcatori di Google Maps a un'immagine mappa statica usando il parametro `markers` nell'URL. Il parametro `markers` accetta uno stile e un elenco di percorsi di cui eseguire il rendering sulla mappa con tale stile, come illustrato di seguito:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

È possibile utilizzare stili aggiuntivi aggiungendo parametri di `markers` aggiuntivi all'URL con uno stile e un set di percorsi diversi.

Le posizioni del marcatore vengono specificate con il formato "latitudine, Longitudine".

Gli stili degli indicatori in Google Maps vengono aggiunti con il formato `optionName:value`, con più stili separati da pipe (\|) caratteri come questo "optionName1: value1\|optionName2: Value2". Si noti che i nomi e i valori delle opzioni sono separati da due punti (:). I nomi delle opzioni di stile seguenti possono essere usati per applicare uno stile ai marcatori in Google Maps:

- `color`: colore dell'icona del marcatore predefinito. Può essere un colore esadecimale a 24 bit (`0xrrggbb`) o uno dei valori seguenti. `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red``white`.
- `label`: un singolo carattere alfanumerico in maiuscolo da visualizzare sulla parte superiore dell'icona.
- `size`: dimensioni del marcatore. Può essere `tiny`, `mid` o `small`.

Le icone personalizzate possono essere usate anche in Google Maps usando i nomi delle opzioni di stile seguenti:

- `anchor`: specifica come allineare l'immagine dell'icona alla coordinata. Può essere un valore in pixel (x, y) o uno dei valori seguenti. `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft`o `bottomright`.
- `icon`: un URL che punta all'immagine dell'icona.

In Google Maps, ad esempio, è possibile aggiungere un marcatore rosso di medie dimensioni alla mappa in corrispondenza delle coordinate (Longitudine:-110, Latitudine: 45) con il parametro URL seguente:

```
&markers=color:red|size:mid|45,-110
```

<center>

![marcatore di Google Maps](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**Dopo: mappe di Azure**

I marcatori di mappe di Azure possono essere aggiunti anche a un'immagine statica della mappa specificando il parametro `pins` nell'URL. Analogamente a Google Maps, è possibile specificare uno stile e un elenco di percorsi in questo parametro e il parametro `pins` può essere specificato più volte per supportare i marcatori con stili diversi.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

È possibile utilizzare stili aggiuntivi aggiungendo parametri di `pins` aggiuntivi all'URL con uno stile e un set di percorsi diversi.

Quando si tratta di punti di blocco, Azure Maps richiede che le coordinate siano in formato "Latitudine Longitudine", mentre Google Maps usa il formato "latitudine, Longitudine". Si noti inoltre che è presente uno spazio, non una virgola che separa la longitudine e la latitudine in mappe di Azure.

Il valore `iconType` specifica il tipo di pin da creare e può presentare i valori seguenti:

- `default`: icona del pin predefinita.
- `none`: non viene visualizzata alcuna icona. verrà eseguito il rendering solo delle etichette.
- `custom`: specifica che è necessario usare un'icona personalizzata. Un URL che punta all'immagine dell'icona può essere aggiunto alla fine del `pins` parametro dopo le informazioni sul percorso del PIN.
- `{udid}`: ID dati univoco (UDID) per un'icona archiviata nella piattaforma di archiviazione dei dati di Azure maps.

Gli stili del PIN nelle mappe di Azure vengono aggiunti con il formato `optionNameValue`, con più stili separati da caratteri di pipe (\|) come questa `iconType|optionName1Value1|optionName2Value2`. Si noti che i nomi e i valori delle opzioni non sono separati. I nomi delle opzioni di stile seguenti possono essere usati per applicare uno stile ai marcatori in mappe di Azure:

- `al`: specifica l'opacità (alfa) del marcatore. Può essere un numero compreso tra 0 e 1.
- `an`: specifica l'ancoraggio del PIN. Valori dei pixel x e y specificati nel formato "x y".
- `co`: colore del PIN. Deve essere un colore esadecimale a 24 bit: `000000` `FFFFFF`.
- `la`: specifica l'ancoraggio dell'etichetta. Valori dei pixel x e y specificati nel formato "x y".
- `lc`: colore dell'etichetta. Deve essere un colore a 24 ma esadecimale: `000000` `FFFFFF`.
- `ls`: dimensione dell'etichetta in pixel. Può essere un numero maggiore di 0.
- `ro`: valore in gradi per ruotare l'icona. Può essere un numero compreso tra-360 e 360.
- `sc`: valore di scala per l'icona del PIN. Può essere un numero maggiore di 0.

I valori delle etichette vengono specificati per ogni posizione del PIN anziché avere un singolo valore etichetta applicabile a tutti i marcatori nell'elenco di posizioni. Il valore dell'etichetta può essere costituito da una stringa di più caratteri ed essere racchiuso tra virgolette singole per assicurarsi che non venga scambiato come valore di stile o posizione.

Ad esempio, in mappe di Azure, aggiungendo un'icona di colore rosso (`FF0000`) con l'etichetta "Space Needle" posizionata sotto (15 50) l'icona alle coordinate (Longitudine:-122,349300, Latitudine: 47,620180) può essere eseguita con il parametro URL seguente:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![marcatore mappe di Azure](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

Nell'esempio seguente vengono aggiunti tre pin con i valori di etichetta "1", "2" e "3":

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Azure esegue il mapping di più marcatori](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Confronto formato parametri URL percorso

**Prima: Google Maps**

In Google Maps le linee e i poligoni possono essere aggiunti a un'immagine statica della mappa utilizzando il parametro `path` nell'URL. Il parametro `path` accetta uno stile e un elenco di percorsi di cui eseguire il rendering sulla mappa con tale stile, come illustrato di seguito:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

È possibile utilizzare stili aggiuntivi aggiungendo parametri di `path` aggiuntivi all'URL con uno stile e un set di percorsi diversi.

I percorsi dei percorsi in Google Maps vengono specificati con il formato `latitude1,longitude1|latitude2,longitude2|…`. I percorsi possono essere codificati o contenere indirizzi per i punti.

Gli stili di percorso in Google Maps vengono aggiunti con il formato `optionName:value`, con più stili separati da caratteri di pipe (\|) come questa `optionName1:value1|optionName2:value2`. Si noti che i nomi e i valori delle opzioni sono separati da due punti (:). I nomi delle opzioni di stile seguenti possono essere usati per applicare uno stile ai percorsi in Google Maps:

- `color`: colore del tracciato o del contorno poligono. Può essere un colore esadecimale a 24 bit (`0xrrggbb`), un colore esadecimale a 32 bit (`0xrrggbbbaa`) o uno dei valori seguenti. nero, marrone, verde, viola, giallo, blu, grigio, arancione, rosso, bianco.
- `fillColor`: colore per riempire l'area del tracciato con (poligono). Può essere un colore esadecimale a 24 bit (`0xrrggbb`), un colore esadecimale a 32 bit (`0xrrggbbbaa`) o uno dei valori seguenti. nero, marrone, verde, viola, giallo, blu, grigio, arancione, rosso, bianco.
- `geodesic`: indica se il percorso deve essere una linea che segue la curvatura della terra.
- `weight`: lo spessore della linea di percorso in pixel.

In Google Maps, ad esempio, è possibile aggiungere una linea rossa con opacità del 50% e uno spessore di 4 pixel alla mappa tra le coordinate (Longitudine:-110, Latitudine: 45 e Longitudine:-100, Latitudine: 50) con il parametro URL seguente:

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![linea di Google Maps](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**Dopo: mappe di Azure**

Nelle mappe di Azure le linee e i poligoni possono essere aggiunti anche a un'immagine statica della mappa specificando il parametro `path` nell'URL. Analogamente a Google Maps, è possibile specificare uno stile e un elenco di percorsi in questo parametro e il parametro `path` può essere specificato più volte per eseguire il rendering di più cerchi, linee e poligoni con stili diversi.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Per quanto riguarda i percorsi di percorso, Azure Maps richiede che le coordinate siano in formato "Latitudine Longitudine", mentre Google Maps usa il formato "latitudine, Longitudine". Si noti inoltre che è presente uno spazio, non una virgola che separa la longitudine e la latitudine in mappe di Azure. Mappe di Azure non supporta percorsi o indirizzi codificati per i punti. I set di dati più grandi possono essere caricati come un GeoJSON riempie l'API di archiviazione dei dati di mappe di Azure, come descritto [qui](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage).

Gli stili di percorso nelle mappe di Azure vengono aggiunti con il formato `optionNameValue`, con più stili separati da caratteri di pipe (\|) come questa `optionName1Value1|optionName2Value2`. Si noti che i nomi e i valori delle opzioni non sono separati. I nomi delle opzioni di stile seguenti possono essere usati per applicare uno stile ai percorsi nelle mappe di Azure:

- `fa`: l'opacità del colore di riempimento (alfa) utilizzata durante il rendering dei poligoni. Può essere un numero compreso tra 0 e 1.
- `fc`: colore di riempimento utilizzato per il rendering dell'area di un poligono.
- `la`: l'opacità del colore della linea (alfa) utilizzata durante il rendering delle linee e il contorno dei poligoni. Può essere un numero compreso tra 0 e 1.
- `lc`: il colore della linea usato per il rendering delle linee e il contorno dei poligoni.
- `lw`: larghezza della linea in pixel.
- `ra`: specifica un raggio di cerchi in metri.

In mappe di Azure, ad esempio, è possibile aggiungere una linea rossa con opacità del 50% e uno spessore di 4 pixel alla mappa tra le coordinate (Longitudine:-110, Latitudine: 45 e Longitudine:-100, Latitudine: 50) con il parametro URL seguente:

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center> polilinea di Azure Maps

## <a name="calculate-a-distance-matrix"></a>Calcolare una matrice di distanza

Azure Maps fornisce un'API per il calcolo dei tempi di viaggio e delle distanze tra un set di posizioni come matrice di distanza. L'API della matrice di distanza di Azure Maps è paragonabile all'API della matrice di distanza in Google Maps.

- [**Route Matrix**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): calcola in modo asincrono tempi di viaggio e distanze per un set di origini e destinazioni. Sono supportate fino a 700 celle per richiesta (il numero di origini moltiplicato per il numero di destinazioni). Tenendo presente questo vincolo, esempi di possibili dimensioni della matrice sono: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Una richiesta all'API della matrice distance può essere effettuata solo usando una richiesta POST con le informazioni sull'origine e sulla destinazione nel corpo della richiesta. Azure Maps richiede inoltre che tutte le origini e le destinazioni siano coordinate. Gli indirizzi dovranno prima essere geocodificati.

La tabella seguente fa riferimento incrociato ai parametri dell'API Google Maps con i parametri dell'API paragonabili in mappe di Azure.

| Parametro API Google Maps      | Parametro API Maps di Azure paragonabile  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination`: specificare nel corpo della richiesta POST come GeoJSON. |
| `key`                          | `subscription-key`: vedere anche la documentazione relativa all' [autenticazione con Maps di Azure](azure-maps-authentication.md) . |
| `language`                     | `language`: vedere la documentazione relativa alle [lingue supportate](supported-languages.md) .  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins`: specificare nel corpo della richiesta POST come GeoJSON.  |
| `region`                       | *N/A* : si tratta di una funzionalità correlata alla geocodifica. Usare il parametro `countrySet` quando si usa l'API di geocodifica di Azure maps. |
| `traffic_model`                | *N/A* : è possibile specificare solo se i dati di traffico devono essere utilizzati con il parametro `traffic`. |
| `transit_mode`                 | Le matrici di distanza basate sul transito *N/A* non sono attualmente supportate.  |
| `transit_routing_preference`   | Le matrici di distanza basate sul transito *N/A* non sono attualmente supportate.  |
| `units`                        | *N/A* : Azure Maps usa solo il sistema metrico. |

> [!TIP]
> Tutte le opzioni di routing avanzate disponibili nell'API di routing Maps di Azure (routing del camion, specifiche del motore, evitare...) sono supportate nell'API della matrice di distanza delle mappe di Azure.

## <a name="get-a-time-zone"></a>Ottenere un fuso orario

Azure Maps fornisce un'API per il recupero del fuso orario in cui si trova una coordinata. L'API del fuso orario di Azure Maps è paragonabile all'API del fuso orario in Google Maps:

- [**Fuso orario per coordinata**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): specificare una coordinata e ottenere i dettagli per il fuso orario in cui rientra.

La tabella seguente fa riferimento incrociato ai parametri dell'API Google Maps con i parametri dell'API paragonabili in mappe di Azure.

| Parametro API Google Maps | Parametro API Maps di Azure paragonabile   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key`: vedere anche la documentazione relativa all' [autenticazione con Maps di Azure](azure-maps-authentication.md) .       |
| `language`                  | `language`: vedere la documentazione relativa alle [lingue supportate](supported-languages.md) .    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Oltre a questo, la piattaforma Azure Maps fornisce anche alcune API del fuso orario aggiuntive per semplificare le conversioni con ID e nomi dei fusi orari:

- [**Fuso orario in base all'ID**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): restituisce le informazioni correnti, cronologiche e del fuso orario future per l'ID del fuso orario di IANA specificato.
- [**IANA dell'enum del fuso orario**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): restituisce un elenco completo di ID del fuso orario di IANA. Gli aggiornamenti al servizio IANA vengono riflessi nel sistema entro un giorno.
- [**Fuso orario enumerazione Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): restituisce un elenco completo di ID del fuso orario di Windows.
- [**Versione IANA del fuso orario**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): restituisce il numero di versione IANA corrente usato da mappe di Azure.
- [**Fuso orario da Windows a IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): restituisce un ID IANA corrispondente, dato un ID del fuso orario di Windows valido. Per un singolo ID Windows è possibile che vengano restituiti più ID IANA.

## <a name="client-libraries"></a>Librerie client

Azure Maps fornisce librerie client per i linguaggi di programmazione seguenti:

- JavaScript, TypeScript, node. js- [documentazione](how-to-use-services-module.md) \| [pacchetto NPM](https://www.npmjs.com/package/azure-maps-rest)

Librerie client open source per altri linguaggi di programmazione:

- .NET Standard 2,0- [progetto GitHub](https://github.com/perfahlen/AzureMapsRestServices) \| [pacchetto NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>Risorse aggiuntive

Di seguito sono riportate alcune risorse e documentazione aggiuntiva sui servizi REST di Azure maps.

- [Procedure consigliate per la ricerca](how-to-use-best-practices-for-search.md)
- [Cercare un indirizzo](how-to-search-for-address.md)
- [Documentazione di riferimento dell'API del servizio REST di mappe di Azure](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui servizi REST di Azure maps.

> [!div class="nextstepaction"]
> [Procedure consigliate per l'utilizzo del servizio di ricerca](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Come usare il modulo servizi (Web SDK)](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-maps)
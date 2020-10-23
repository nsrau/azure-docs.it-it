---
title: Esercitazione - Eseguire la migrazione di servizi Web da Google Maps | Mappe di Microsoft Azure
description: Questa esercitazione illustra come eseguire la migrazione di servizi Web da Google Maps a Mappe di Microsoft Azure
author: rbrundritt
ms.author: richbrun
ms.date: 08/19/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 5da42ebd31e4b09eb8bc223560aec976584c47e9
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874459"
---
# <a name="tutorial---migrate-web-service-from-google-maps"></a>Esercitazione - Eseguire la migrazione di un servizio Web da Google Maps

Sia Mappe di Azure che Google Maps forniscono l'accesso alle API spaziali tramite i servizi Web REST. Le interfacce API di queste piattaforme eseguono funzionalità simili. Tuttavia, ognuna di esse usa convenzioni di denominazione e oggetti di risposta diversi.

La tabella seguente illustra le API del servizio Mappe di Azure che forniscono funzionalità simili alle API del servizio Google Maps elencate.

| API del servizio Google Maps | API del servizio Mappe di Azure                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Indicazioni              | [Route](https://docs.microsoft.com/rest/api/maps/route)                                     |
| Matrice di distanze         | [Matrice di percorsi](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)       |
| Geocodifica               | [Ricerca](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Ricerca luoghi           | [Ricerca](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Completamento automatico luoghi      | [Ricerca](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Blocca sulla strada            | Vedere la sezione [Calcolare percorsi e indicazioni](#calculate-routes-and-directions).            |
| Limiti di velocità            | Vedere la sezione [Geocodifica inversa di una coordinata](#reverse-geocode-a-coordinate).                  |
| Mappa statica              | [Rendering](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                       |
| Fuso orario               | [Fusi orari](https://docs.microsoft.com/rest/api/maps/timezone)                              |

Le API dei servizi seguenti non sono attualmente disponibili in Mappe di Azure:

- Altitudine
- Georilevazione
- Dettagli e foto di luoghi - I numeri di telefono e gli URL dei siti Web sono disponibili nell'API di ricerca di Mappe di Azure.
- URL mappa
- Strade più vicine - Ciò è possibile usando l'SDK Web come mostrato [qui](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic
), ma al momento non disponibile come servizio.
- Visualizzazione strada statica

Mappe di Azure offre numerosi servizi Web REST aggiuntivi che possono risultare utili:

- [Operazioni spaziali](https://docs.microsoft.com/rest/api/maps/spatial): consente di eseguire l'offload di operazioni e calcoli spaziali complessi, ad esempio il geofencing, in un servizio.
- [Traffico](https://docs.microsoft.com/rest/api/maps/traffic): consente di accedere ai dati degli eventi e del flusso di traffico in tempo reale.

## <a name="geocoding-addresses"></a>Geocodifica di indirizzi

La geocodifica è il processo di conversione di un indirizzo in una coordinata. Ad esempio, "1 Microsoft Way, Redmond, WA" viene convertito in longitudine: -122.1298, latitudine: 47.64005. Le coordinate possono quindi essere usate per diversi tipi di scopi, ad esempio, per posizionare e centrare un indicatore su una mappa.

Mappe di Azure offre numerosi metodi per la geocodifica degli indirizzi:

- [**Geocodifica di indirizzi in formato libero**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): consente di specificare una singola stringa di indirizzo e di elaborare immediatamente la richiesta. "1 Microsoft Way, Redmond, WA" è un esempio di una singola stringa di indirizzo. Questa API è consigliata se è necessario eseguire rapidamente la geocodifica di singoli indirizzi.
- [**Geocodifica di indirizzi strutturati**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): specificare le parti di un singolo indirizzo, ad esempio via, città, paese e codice postale, per elaborare immediatamente la richiesta. Questa API è consigliata se è necessario eseguire rapidamente la geocodifica di singoli indirizzi e i dati sono già analizzati nelle singole parti dell'indirizzo.
- [**Geocodifica di indirizzi batch**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): consente di creare una richiesta contenente fino a 10.000 indirizzi e li elabora in un certo periodo di tempo. Verrà eseguita la geocodifica di tutti gli indirizzi in parallelo nel server e, al termine dell'operazione, sarà possibile scaricare il set di risultati completo. Questa opzione è consigliata per la geocodifica di set di dati di grandi dimensioni.
- [**Ricerca fuzzy**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): Questa API combina la geocodifica degli indirizzi con la ricerca di punti di interesse. Questa API accetta una stringa in formato libero. La stringa può essere un indirizzo, un luogo, un riferimento, un punto di interesse o una categoria di punti di interesse. Questa API elabora la richiesta quasi in tempo reale. Questa API è consigliata per le applicazioni in cui gli utenti possono cercare indirizzi o punti di interesse dalla stessa casella di testo.
- [**Ricerca fuzzy batch**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): consente di creare una richiesta contenente fino a 10.000 indirizzi, luoghi, punti di riferimento o punti di interesse e li elabora in un certo periodo di tempo. Tutti i dati verranno elaborati in parallelo nel server e, al termine dell'operazione, sarà possibile scaricare il set di risultati completo.

La tabella seguente offre un riferimento incrociato dei parametri dell'API Google Maps e dei parametri equivalenti dell'API in Mappe di Azure.

| Parametro API di Google Maps | Parametro API di Mappe di Azure equivalente  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` e `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality`: città<br/>`municipalitySubdivision`: quartiere, zona urbana/area metropolitana<br/>`countrySubdivision`: stato o provincia<br/>`countrySecondarySubdivision`: contea<br/>`countryTertiarySubdivision`: distretto<br/>`countryCode`: codice paese/area geografica di due lettere |
| `key`                       | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](azure-maps-authentication.md). |
| `language`                  | `language`: vedere la documentazione relativa alle [lingue supportate](supported-languages.md).  |
| `region`                    | `countrySet`                       |

Un esempio di utilizzo del servizio di ricerca è documentato [qui](how-to-search-for-address.md). Assicurarsi di esaminare le [procedure consigliate per la ricerca](how-to-use-best-practices-for-search.md).

> [!TIP]
> Le API per la ricerca fuzzy e la geocodifica degli indirizzi in formato libero possono essere usate con la modalità di completamento automatico aggiungendo `&typeahead=true` all'URL della richiesta. Questo comunica al server che è probabile che il testo di input sia parziale e che la ricerca passerà quindi alla modalità predittiva.

## <a name="reverse-geocode-a-coordinate"></a>Geocodifica inversa di una coordinata

La geocodifica inversa è il processo di conversione delle coordinate geografiche in un indirizzo approssimativo. Le coordinate con "longitudine: -122.1298, latitudine: 47.64005" vengono convertite nell'indirizzo "1 Microsoft way, Redmond, WA".

Mappe di Azure offre numerosi metodi di geocodifica inversa:

- [**Geocodifica inversa di indirizzi**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): Specificare una singola coordinata geografica per ottenere l'indirizzo approssimativo corrispondente alla coordinata. La richiesta viene elaborata quasi in tempo reale.
- [**Geocodifica inversa di incroci**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): consente di specificare una singola coordinata geografica per ottenere le informazioni sugli incroci adiacenti e di elaborare immediatamente la richiesta. Ad esempio, è possibile ricevere l'incrocio tra 1st Ave e Main St.
- [**Geocodifica inversa di indirizzi batch**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): consente di creare una richiesta contenente fino a 10.000 coordinate e le elabora in un certo periodo di tempo. Tutti i dati verranno elaborati in parallelo nel server. Al termine della richiesta, è possibile scaricare l'intero set di risultati.

Questa tabella offre un riferimento incrociato dei parametri dell'API Google Maps e dei parametri equivalenti dell'API in Mappe di Azure.

| Parametro API di Google Maps   | Parametro API di Mappe di Azure equivalente   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](azure-maps-authentication.md). |
| `language`                  | `language`: vedere la documentazione relativa alle [lingue supportate](supported-languages.md).  |
| `latlng`                    | `query`  |
| `location_type`             | *N/D*     |
| `result_type`               | `entityType`    |

Vedere le [procedure consigliate per la ricerca](how-to-use-best-practices-for-search.md).

L'API di geocodifica inversa di Mappe di Azure include alcune funzionalità aggiuntive che non sono disponibili in Google Maps. Potrebbe risultare utile integrare queste funzionalità nell'applicazione durante la migrazione dell'app:

- Recupero dei dati sui limiti di velocità
- Recupero delle informazioni specifiche della strada: strada locale, arteria stradale, accesso limitato, rampa e così via
- Recupero del lato della strada in cui si trova una coordinata

## <a name="search-for-points-of-interest"></a>Cercare i punti di interesse

È possibile cercare i dati del punto di interesse in Google Maps usando l'API di ricerca luoghi. Questa API offre tre modi diversi per cercare i punti di interesse:

- **Ricerca di un luogo dal testo:** cerca un punto di interesse in base al nome, all'indirizzo o al numero di telefono.
- **Ricerca nelle vicinanze**: cerca i punti di interesse che si trovano entro una certa distanza da una posizione.
- **Ricerca testuale:** cerca i luoghi usando un testo in formato libero contenente informazioni sul punto di interesse e sulla posizione. Ad esempio: "pizza a New York" o "ristoranti vicino a Main St".

Mappe di Azure offre numerose API di ricerca per i punti di interesse:

- [**Ricerca di punti di interesse**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): consente di cercare i punti di interesse in base al nome. Ad esempio: "Starbucks".
- [**Ricerca per categoria di punti di interesse**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): consente di cercare i punti di interesse in base alla categoria. Ad esempio: "ristorante".
- [**Ricerca nelle vicinanze**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): cerca i punti di interesse che si trovano entro una certa distanza da una posizione.
- [**Ricerca fuzzy**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): Questa API combina la geocodifica degli indirizzi con la ricerca di punti di interesse. Accetta una stringa in formato libero che può essere un indirizzo, un luogo, un punto di riferimento, un punto di interesse o una categoria di punti di interesse ed elabora la richiesta quasi in tempo reale. Questa API è consigliata per le applicazioni in cui gli utenti possono cercare indirizzi o punti di interesse dalla stessa casella di testo.
- [**Ricerca all'interno di una geometria**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): consente di cercare i punti di interesse all'interno di una geometria specificata. Ad esempio, cercare un punto di interesse all'interno di un poligono.
- [**Ricerca lungo un percorso**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): consente di cercare i punti di interesse lungo un percorso specificato.
- [**Ricerca fuzzy batch**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): consente di creare una richiesta contenente fino a 10.000 indirizzi, luoghi, punti di riferimento o punti di interesse ed elabora la richiesta in un certo periodo di tempo. Tutti i dati verranno elaborati in parallelo nel server. Al termine dell'elaborazione della richiesta, è possibile scaricare l'intero set di risultati.

Attualmente Mappe di Azure non dispone di un'API equivalente all'API di ricerca testuale in Google Maps.

> [!TIP]
> Le API per la ricerca di punti di interesse, la ricerca per categoria di punti di interesse e la ricerca fuzzy possono essere usate con la modalità di completamento automatico aggiungendo `&typeahead=true` all'URL della richiesta. Questo comunica al server che è probabile che il testo di input sia parziale, quindi l'API eseguirà la ricerca nella modalità predittiva.

Esaminare la documentazione relativa alle [procedure consigliate per la ricerca](how-to-use-best-practices-for-search.md).

### <a name="find-place-from-text"></a>Ricerca di un luogo dal testo

Per cercare i punti di interesse in base al nome o all'indirizzo, usare [Ricerca di punti di interesse](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) e [Ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) di Mappe di Azure.

La tabella offre un riferimento incrociato dei parametri dell'API Google Maps e dei parametri equivalenti dell'API di Mappe di Azure.

| Parametro API di Google Maps | Parametro API di Mappe di Azure equivalente |
|---------------------------|-------------------------------------|
| `fields`                  | *N/D*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/D*                               |
| `key`                     | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](azure-maps-authentication.md). |
| `language`                | `language`: vedere la documentazione relativa alle [lingue supportate](supported-languages.md).  |
| `locationbias`            | `lat`, `lon` e `radius`<br/>`topLeft` e `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Ricerca nelle vicinanze

Per recuperare i punti di interesse nelle vicinanze in Mappe di Azure, è possibile usare l'API [Ricerca nelle vicinanze](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby).

La tabella mostra i parametri dell'API Google Maps e i parametri equivalenti dell'API di Mappe di Azure.

| Parametro API di Google Maps | Parametro API di Mappe di Azure equivalente  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](azure-maps-authentication.md). |
| `keyword`                   | `categorySet` e `brandSet`        |
| `language`                  | `language`: vedere la documentazione relativa alle [lingue supportate](supported-languages.md).  |
| `location`                  | `lat` e `lon`                     |
| `maxprice`                  | *N/D*                               |
| `minprice`                  | *N/D*                               |
| `name`                      | `categorySet` e `brandSet`        |
| `opennow`                   | *N/D*                               |
| `pagetoken`                 | `ofs` e `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N/D*                               |
| `type`                      | `categorySet –` Vedere la documentazione relativa alle [categorie di ricerca supportate](supported-search-categories.md).   |

## <a name="calculate-routes-and-directions"></a>Calcolare percorsi e indicazioni

Calcolare percorsi e indicazioni usando Mappe di Azure. Mappe di Azure offre numerose funzionalità equivalenti a quelle del servizio di pianificazione percorso di Google Maps, ad esempio:

- Orari di arrivo e di partenza.
- Percorsi basati sul traffico in tempo reale e predittivi.
- Modalità di trasporto diverse, ad esempio, in auto, a piedi o in bicicletta.

> [!NOTE]
> Mappe di Azure richiede che tutti i punti di tragitto siano coordinate. Gli indirizzi devono prima essere geocodificati.

Il servizio di pianificazione percorso di Mappe di Azure fornisce le API seguenti per il calcolo dei percorsi:

- [**Calcolo percorso**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): consente di calcolare un percorso e di elaborare immediatamente la richiesta. Questa API supporta le richieste GET e POST. Le richieste POST sono consigliate quando si specifica un numero elevato di punti di tragitto o quando si usano molte opzioni pel percorso per assicurarsi che la richiesta URL non diventi troppo lunga e causi problemi. La direzione del percorso POST in Mappe di Azure offre un'opzione che può contenere migliaia di [punti di supporto](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints) e li userà per ricreare un percorso logico tra di essi (blocca sulla strada). 
- [**Percorso batch**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): consente di creare una richiesta contenente fino a 1.000 richieste di percorso e le elabora in un certo periodo di tempo. Tutti i dati verranno elaborati in parallelo nel server e, al termine dell'operazione, sarà possibile scaricare il set di risultati completo.
- [**Servizi di mobilità**](https://docs.microsoft.com/rest/api/maps/mobility): consente di calcolare percorsi e indicazioni usando il trasporto pubblico.

La tabella offre un riferimento incrociato dei parametri dell'API Google Maps e dei parametri equivalenti dell'API in Mappe di Azure.

| Parametro API di Google Maps    | Parametro API di Mappe di Azure equivalente  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query`: coordinate nel formato `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](azure-maps-authentication.md). |
| `language`                     | `language`: vedere la documentazione relativa alle [lingue supportate](supported-languages.md).   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *N/D*: si tratta di una funzionalità relativa alla geocodifica. Usare il parametro *countrySet* quando si usa l'API di geocodifica di Mappe di Azure.  |
| `traffic_model`               | *N/D*: può specificare solo se usare o meno i dati del traffico con il parametro *traffic*. |
| `transit_mode`                | Vedere la [documentazione dei servizi di mobilità](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Vedere la [documentazione dei servizi di mobilità](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *N/D*: Mappe di Azure usa solo il sistema metrico.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Per impostazione predefinita, l'API di pianificazione percorso di Mappe di Azure restituisce solo un riepilogo. Ovvero, la distanza, gli orari e le coordinate per il percorso. Usare il parametro `instructionsType` per recuperare le istruzioni dettagliate. E usare il parametro `routeRepresentation` per filtrare il riepilogo e il percorso.

L'API di pianificazione percorso di Mappe di Azure include funzionalità aggiuntive che non sono disponibili in Google Maps. Quando si esegue la migrazione dell'app, valutare la possibilità di includere queste funzionalità utili.

- Supporto per il tipo di percorso: più breve, più veloce, più interessante/panoramico e con minore consumo di carburante.
- Supporto per modalità di viaggio aggiuntive: autobus, motocicletta, taxi, autocarro e furgone.
- Supporto per 150 punti di tragitto.
- Calcolo di più tempi di viaggio in un'unica richiesta. Traffico cronologico, traffico in tempo reale, senza traffico.
- Possibilità di evitare tipi di strade aggiuntivi: corsie preferenziali, strade non asfaltate, strade già usate.
- Possibilità di specificare aree personalizzate da evitare.
- Limitare l'elevazione consentita per il percorso.
- Percorso basato sulle specifiche del motore. Calcolo dei percorsi per veicoli a combustione o elettrici in base alle specifiche del motore e al carburante o alla carica rimanente.
- Supporto per i parametri dei percorsi per veicoli commerciali. Ad esempio, dimensioni del veicolo, peso, numero di assi e tipo di carico.
- Possibilità di specificare la velocità massima del veicolo.

Inoltre, il servizio di pianificazione percorso in Mappe di Azure supporta il [calcolo dei punti raggiungibili](https://docs.microsoft.com/rest/api/maps/route/getrouterange), anche noto come isocrone. Comporta la generazione di un poligono che copre un'area percorribile in qualsiasi direzione da un punto di partenza, in base a una quantità di tempo o di carburante/carica specificata.

Vedere la documentazione relativa alle [procedure consigliate per la pianificazione percorso](how-to-use-best-practices-for-routing.md).

## <a name="retrieve-a-map-image"></a>Recuperare un'immagine mappa

Mappe di Azure fornisce un'API per il rendering delle immagini mappa statiche con overlay dei dati. L'API [rendering immagini mappa](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) di Mappe di Azure è simile all'API mappa statica in Google Maps.

> [!NOTE]
> Mappe di Azure richiede che il centro e le posizioni di indicatori e percorso siano coordinate nel formato "longitudine,latitudine", mentre Google Maps usa il formato "latitudine,longitudine". È quindi prima necessario eseguire la geocodifica degli indirizzi.

La tabella offre un riferimento incrociato dei parametri dell'API Google Maps e dei parametri equivalenti dell'API in Mappe di Azure.

| Parametro API di Google Maps | Parametro API di Mappe di Azure equivalente  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format`: specificato come parte del percorso URL. Attualmente è supportato solo il formato PNG. |
| `key`                       | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](azure-maps-authentication.md). |
| `language`                  | `language`: vedere la documentazione relativa alle [lingue supportate](supported-languages.md).  |
| `maptype`                   | `layer` e `style`: vedere la documentazione relativa agli [stili mappa supportati](supported-map-styles.md). |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N/D*: si tratta di una funzionalità relativa alla geocodifica. Usare il parametro `countrySet` quando si usa l'API di geocodifica di Mappe di Azure.  |
| `scale`                     | *N/D*                              |
| `size`                      | `width` e `height`: dimensioni massime fino a 8192x8192. |
| `style`                     | *N/D*                              |
| `visible`                   | *N/D*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> Mappe di Azure usa un sistema a tessere con tessere di dimensioni doppie rispetto a quelle delle tessere mappa usate in Google Maps. Di conseguenza, il valore del livello di zoom in Mappe di Azure sarà maggiore di uno rispetto a Google Maps. Ridurre il livello di zoom di uno nelle richieste di cui si esegue la migrazione per compensare la differenza.

Per altre informazioni, vedere la [guida pratica per l'API di rendering delle immagini mappa](how-to-render-custom-data.md).

Oltre a poter generare un'immagine mappa statica, il servizio di rendering di Mappe di Azure offre la possibilità di accedere direttamente alle tessere mappa in formato raster (PNG) e vettoriale:

- [**Tessera mappa**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): recupera le tessere raster (PNG) e vettoriali per le mappe di base (strade, limiti, sfondo).
- [**Tessera immagine mappa**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile): recupera le tessere di immagini aeree e satellitari.

> [!TIP]
> Qualche anno fa, molte applicazioni di Google Maps sono passate da esperienze mappa interattive a immagini mappa statiche per risparmiare sui costi. In Mappe di Azure è spesso più conveniente usare il controllo mappa interattivo in Web SDK, perché viene addebitato in base al numero di caricamenti delle tessere mappa. Le tessere mappa in Mappe di Azure sono di grandi dimensioni. Spesso sono necessari solo poche tessere per ricreare la stessa visualizzazione mappa di una mappa statica. Le tessere mappa vengono automaticamente memorizzate nella cache dal browser. Di conseguenza, il controllo mappa interattivo spesso genera solo una frazione di una transazione per la riproduzione di una visualizzazione mappa statica. Le operazioni di panoramica e zoom caricheranno più tessere, tuttavia nel controllo mappa sono disponibili opzioni per disabilitare questo comportamento. Il controllo mappa interattivo fornisce anche numerose altre opzioni di visualizzazione rispetto a quelle dei servizi mappa statici.

### <a name="marker-url-parameter-format-comparison"></a>Confronto tra il formato dei parametri URL per gli indicatori

**Prima: Google Maps**

Aggiungere gli indicatori usando il parametro `markers` nell'URL. Il parametro `markers` accetta uno stile e un elenco di posizioni in base a cui verrà eseguito il rendering sulla mappa, come illustrato di seguito:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Per usare altri stili, aggiungere i parametri `markers` all'URL con uno stile e un set di posizioni diversi.

Specificare le posizioni dell'indicatore con il formato "latitudine,longitudine".

Aggiungere gli stili degli indicatori con il formato `optionName:value`, con più stili separati dalla barra verticale (\|), ad esempio: "optionName1:value1\|optionName2:value2". Si noti che i nomi e i valori delle opzioni sono separati da due punti (:). Usare i nomi delle opzioni di stile seguenti per applicare uno stile agli indicatori in Google Maps:

- `color`: colore dell'icona predefinita dell'indicatore. Può essere un colore esadecimale a 24 bit (`0xrrggbb`) o uno dei valori seguenti: `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label`: singolo carattere alfanumerico maiuscolo da visualizzare nella parte superiore dell'icona.
- `size`: dimensioni dell'indicatore. Può essere `tiny`, `mid` o `small`.

Usare i nomi delle opzioni di stile seguenti per le icone personalizzate in Google Maps:

- `anchor`: specifica come allineare l'immagine dell'icona alla coordinata. Può essere un valore in pixel (x, y) o uno dei valori seguenti: `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft` o `bottomright`.
- `icon`: URL che punta all'immagine dell'icona.

Ad esempio, ora verrà aggiunto alla mappa un indicatore rosso di medie dimensioni in corrispondenza delle coordinate di longitudine: -110, latitudine: 45:

```
&markers=color:red|size:mid|45,-110
```


![Marcatore di Google Maps](media/migrate-google-maps-web-services/google-maps-marker.png)

**Dopo: Mappe di Azure**

Aggiungere gli indicatori a un'immagine mappa statica specificando il parametro `pins` nell'URL. Come in Google Maps, specificare uno stile e un elenco di posizioni nel parametro. Il parametro `pins` può essere specificato più volte per supportare gli indicatori con stili diversi.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Per usare altri stili, aggiungere altri parametri `pins` all'URL con uno stile e un set di posizioni diversi.

In Mappe di Azure la posizione del segnaposto deve essere espressa nel formato "longitudine latitudine", mentre Google Maps usa il formato "latitudine,longitudine". Nel formato di Mappe di Azure la longitudine e la latitudine sono separate da uno spazio, anziché da una virgola.

`iconType` specifica il tipo di segnaposto da creare. I valori possibili sono i seguenti:

- `default`: icona predefinita del segnaposto.
- `none`: nessuna icona visualizzata. Verrà eseguito il rendering solo delle etichette.
- `custom`: specifica che è necessario usare un'icona personalizzata. È possibile aggiungere un URL che punta all'immagine dell'icona alla fine del parametro `pins` dopo le informazioni sulla posizione del segnaposto.
- `{udid}`: ID dati univoco (UDID) per un'icona archiviata nella piattaforma di archiviazione dati di Mappe di Azure.

Aggiungere gli stili dei segnaposto con il formato `optionNameValue`. Separare più stili con la barra verticale (\|). Ad esempio: `iconType|optionName1Value1|optionName2Value2`. I nomi e i valori delle opzioni non sono separati. Usare i nomi delle opzioni di stile seguenti per applicare lo stile agli indicatori:

- `al`: specifica l'opacità (alfa) dell'indicatore. Scegliere un numero compreso tra 0 e 1.
- `an`: specifica l'ancoraggio del segnaposto. Specificare i valori dei pixel x e y nel formato "x y".
- `co`: colore del segnaposto. Specificare un colore esadecimale a 24 bit: da `000000` a `FFFFFF`.
- `la`: specifica l'ancoraggio dell'etichetta. Specificare i valori dei pixel x e y nel formato "x y".
- `lc`: colore dell'etichetta. Specificare un colore esadecimale a 24 bit: da `000000` a `FFFFFF`.
- `ls`: dimensioni dell'etichetta in pixel. Scegliere un numero maggiore di 0.
- `ro`: valore in gradi per la rotazione dell'icona. Scegliere un numero compreso tra -360 e 360.
- `sc`: valore di scala per l'icona del segnaposto. Scegliere un numero maggiore di 0.

Specificare i valori delle etichette per ogni posizione del segnaposto. Questo approccio è più efficiente rispetto all'applicazione di un singolo valore di etichetta a tutti gli indicatori nell'elenco di posizioni. Il valore dell'etichetta può essere una stringa di più caratteri. Racchiudere la stringa tra virgolette singole per assicurarsi che non venga scambiata per un valore di stile o di posizione.

Ora verrà aggiunta un'icona predefinita rossa (`FF0000`) con l'etichetta "Space Needle", posizionata sotto (15 50). L'icona è posizionata in corrispondenza delle coordinate di longitudine: -122.349300, latitudine: 47.620180:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```


![Marcatore di Mappe di Azure](media/migrate-google-maps-web-services/azure-maps-marker.png)

Aggiungere tre segnaposto con i valori di etichetta "1", "2" e "3":

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```



![Più indicatori in Mappe di Azure](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)

### <a name="path-url-parameter-format-comparison"></a>Confronto tra il formato dei parametri URL per i percorsi

**Prima: Google Maps**

Aggiungere linee e poligoni a un'immagine mappa statica usando il parametro `path` nell'URL. Il parametro `path` accetta uno stile e un elenco di posizioni in base a cui verrà eseguito il rendering sulla mappa, come illustrato di seguito:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Usare ulteriori stili aggiungendo altri parametri `path` all'URL con uno stile e un set di posizioni diversi.

Le posizioni dei percorsi sono specificate con il formato `latitude1,longitude1|latitude2,longitude2|…`. I percorsi possono essere codificati o contenere gli indirizzi per i punti.

Aggiungere gli stili del percorso con il formato `optionName:value` e separare più stili con la barra verticale (\|). Infine, separare i nomi e i valori delle opzioni con i due punti (:), nel modo seguente: `optionName1:value1|optionName2:value2`. È possibile usare i nomi delle opzioni di stile seguenti per applicare uno stile ai percorsi in Google Maps:

- `color`: colore del percorso o del contorno del poligono. Può essere un colore esadecimale a 24 bit (`0xrrggbb`), un colore esadecimale a 32 bit (`0xrrggbbbaa`) o uno dei valori seguenti: nero, marrone, verde, viola, giallo, blu, grigio, arancione, rosso, bianco.
- `fillColor`: colore di riempimento dell'area del percorso (poligono). Può essere un colore esadecimale a 24 bit (`0xrrggbb`), un colore esadecimale a 32 bit (`0xrrggbbbaa`) o uno dei valori seguenti: nero, marrone, verde, viola, giallo, blu, grigio, arancione, rosso, bianco.
- `geodesic`: indica se il percorso deve essere una linea che segue la curvatura della terra.
- `weight`: spessore della linea del percorso in pixel.

Aggiungere alla mappa l'opacità della linea rossa e lo spessore in pixel tra le coordinate nel parametro URL. Per l'esempio seguente, la linea ha un'opacità del 50% e uno spessore di quattro pixel. Le coordinate sono longitudine: -110, latitudine: 45 e longitudine: -100, latitudine: 50.

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

![Polilinea di Google Maps](media/migrate-google-maps-web-services/google-maps-polyline.png)

**Dopo: Mappe di Azure**

Aggiungere le linee e i poligoni a un'immagine mappa statica specificando il parametro `path` nell'URL. Come in Google Maps, specificare uno stile e un elenco di posizioni nel parametro. Specificare il parametro `path` più volte per eseguire il rendering di più cerchi, linee e poligoni con stili diversi.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Per le posizioni dei percorsi, Mappe di Azure richiede che le coordinate siano espresse nel formato "longitudine latitudine", mentre Google Maps usa il formato "latitudine,longitudine". Nel formato di Mappe di Azure la longitudine e la latitudine sono separate da uno spazio, anziché da una virgola. Mappe di Azure non supporta percorsi o indirizzi codificati per i punti. È possibile caricare set di dati di dimensioni maggiori come file GeoJSON nell'API di archiviazione dei dati di Mappe di Azure, come illustrato [qui](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage).

Aggiungere gli stili dei percorsi con il formato `optionNameValue`. Separare più stili con la barra verticale (\|), in questo modo: `optionName1Value1|optionName2Value2`. I nomi e i valori delle opzioni non sono separati. Usare i nomi delle opzioni di stile seguenti per applicare uno stile ai percorsi in Mappe di Azure:

- `fa`: opacità del colore di riempimento (alfa) usata per il rendering dei poligoni. Scegliere un numero compreso tra 0 e 1.
- `fc`: colore di riempimento usato per il rendering dell'area di un poligono.
- `la`: opacità del colore della linea (alfa) usata per il rendering delle linee e del contorno dei poligoni. Scegliere un numero compreso tra 0 e 1.
- `lc`: colore della linea usato per il rendering delle linee e del contorno dei poligoni.
- `lw`: larghezza della linea in pixel.
- `ra`: specifica il raggio di un cerchio in metri.

Aggiungere alla mappa l'opacità della linea rossa e lo spessore in pixel tra le coordinate nel parametro URL. Per l'esempio seguente, la linea ha un'opacità del 50% e uno spessore di quattro pixel. Le coordinate hanno i valori seguenti: longitudine:-110, latitudine: 45 e longitudine: -100, latitudine: 50.

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

![Polilinea di Mappe di Azure](media/migrate-google-maps-web-services/azure-maps-polyline.png)

## <a name="calculate-a-distance-matrix"></a>Calcolare una matrice di distanze

Mappe di Azure fornisce l'API matrice di distanze. Usare questa API per calcolare i tempi di viaggio e le distanze tra un set di posizioni, con una matrice di distanze. È simile all'API per la matrice di distanze in Google Maps.

- [**Matrice di percorsi**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): calcola in modo asincrono i tempi di viaggio e le distanze per un set di origini e destinazioni. Supporta fino a 700 celle per richiesta ed equivale al numero di origini moltiplicato per il numero di destinazioni. Tenendo presente questo vincolo, gli esempi di possibili dimensioni della matrice sono: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> È possibile inviare una richiesta all'API della matrice di distanze solo usando una richiesta POST con le informazioni sull'origine e sulla destinazione nel corpo della richiesta. Mappe di Azure richiede inoltre che tutte le origini e le destinazioni siano coordinate. È quindi prima necessario eseguire la geocodifica degli indirizzi.

Questa tabella offre un riferimento incrociato dei parametri dell'API Google Maps e dei parametri equivalenti dell'API di Mappe di Azure.

| Parametro API di Google Maps      | Parametro API di Mappe di Azure equivalente  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination`: specificare nel corpo della richiesta POST come GeoJSON. |
| `key`                          | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](azure-maps-authentication.md). |
| `language`                     | `language`: vedere la documentazione relativa alle [lingue supportate](supported-languages.md).  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins`: specificare nel corpo della richiesta POST come GeoJSON.  |
| `region`                       | *N/D*: si tratta di una funzionalità relativa alla geocodifica. Usare il parametro `countrySet` quando si usa l'API di geocodifica di Mappe di Azure. |
| `traffic_model`                | *N/D*: può specificare solo se usare o meno i dati del traffico con il parametro `traffic`. |
| `transit_mode`                 | *N/D*: le matrici di distanze basate su transito non sono attualmente supportate.  |
| `transit_routing_preference`   | *N/D*: le matrici di distanze basate su transito non sono attualmente supportate.  |
| `units`                        | *N/D*: Mappe di Azure usa solo il sistema metrico. |

> [!TIP]
> Tutte le opzioni di pianificazione percorso avanzate disponibili nell'API di pianificazione percorso di Mappe di Azure sono supportate nell'API della matrice di distanze di Mappe di Azure. Le opzioni di pianificazione percorso avanzate includono: percorso autocarro, specifiche del motore e così via.

Vedere la documentazione relativa alle [procedure consigliate per la pianificazione percorso](how-to-use-best-practices-for-routing.md).

## <a name="get-a-time-zone"></a>Ottenere un fuso orario

Mappe di Azure fornisce un'API per il recupero del fuso orario di una coordinata. L'API fusi orari di Mappe di Azure è simile all'API fusi orari in Google Maps:

- [**Fuso orario per coordinata**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): consente di specificare una coordinata e ricevere i dettagli del fuso orario della coordinata.

Questa tabella offre un riferimento incrociato dei parametri dell'API Google Maps e dei parametri equivalenti dell'API in Mappe di Azure.

| Parametro API di Google Maps | Parametro API di Mappe di Azure equivalente   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](azure-maps-authentication.md).       |
| `language`                  | `language`: vedere la documentazione relativa alle [lingue supportate](supported-languages.md).    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Oltre a questa API, Mappe di Azure fornisce diverse API per i fusi orari. Queste API convertono l'ora in base ai nomi o agli ID del fuso orario:

- [**Fuso orario in base all'ID**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): Restituisce le informazioni sul fuso orario correnti, cronologiche e future per l'ID del fuso orario IANA specificato.
- [**Enumerazione fusi orari IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): restituisce un elenco completo di ID di fusi orari IANA. Gli aggiornamenti al servizio IANA si riflettono nel sistema entro un giorno.
- [**Enumerazione fusi orari Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): restituisce un elenco completo di ID di fusi orari Windows.
- [**Versione fuso orario IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): restituisce il numero di versione IANA corrente usato da Mappe di Azure.
- [**Fuso orario da Windows a IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): restituisce l'ID IANA corrispondente da ID di fuso orario Windows valido. Per un singolo ID Windows è possibile che vengano restituiti più ID IANA.

## <a name="client-libraries"></a>Librerie client

Mappe di Azure fornisce le librerie client per i linguaggi di programmazione seguenti:

- JavaScript, TypeScript, Node.js - [Documentazione](how-to-use-services-module.md) \| [Pacchetto NPM](https://www.npmjs.com/package/azure-maps-rest)

Queste librerie client open source sono destinate ad altri linguaggi di programmazione:

- .NET Standard 2.0 - [Progetto GitHub](https://github.com/perfahlen/AzureMapsRestServices) \| [Pacchetto NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>Risorse aggiuntive

Di seguito sono riportate alcune risorse e documentazione aggiuntive per i servizi REST di Mappe di Azure.

- [Procedure consigliate per la ricerca](how-to-use-best-practices-for-search.md)
- [Cercare un indirizzo](how-to-search-for-address.md)
- [Procedure consigliate per la pianificazione percorso](how-to-use-best-practices-for-routing.md)
- [Documentazione di riferimento per l'API del servizio REST di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/)
- [Esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-maps)
- [Come usare il modulo dei servizi (Web SDK)](how-to-use-best-practices-for-routing.md)
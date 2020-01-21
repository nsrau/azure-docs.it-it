---
title: 'Esercitazione: Eseguire la migrazione di servizi Web da Google Maps | Mappe di Microsoft Azure'
description: Come eseguire la migrazione di servizi Web da Google Maps a Mappe di Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 51c00524c781d9af58f60b36aa3baeb079c6eafa
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910747"
---
# <a name="migrate-web-service-from-google-maps"></a>Eseguire la migrazione di un servizio Web da Google Maps

Sia Mappe di Azure che Google Maps forniscono l'accesso alle API spaziali tramite i servizi Web REST. Le interfacce API per queste piattaforme eseguono funzionalità simili, ma usano convenzioni di denominazione e oggetti risposta diversi.

La tabella seguente illustra le API del servizio Mappe di Azure che forniscono funzionalità simili alle API del servizio Google Maps elencate.

| API del servizio Google Maps | API del servizio Mappe di Azure                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Indicazioni              | [Route](https://docs.microsoft.com/rest/api/maps/route)                               |
| Matrice di distanze         | [Matrice di percorsi](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) |
| Geocodifica               | [Ricerca](https://docs.microsoft.com/rest/api/maps/search)                             |
| Ricerca luoghi           | [Ricerca](https://docs.microsoft.com/rest/api/maps/search)                             |
| Completamento automatico luoghi      | [Ricerca](https://docs.microsoft.com/rest/api/maps/search)                             |
| Mappa statica              | [Rendering](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                 |
| Fuso orario               | [Fusi orari](https://docs.microsoft.com/rest/api/maps/timezone)                        |

Le API dei servizi seguenti non sono attualmente disponibili in Mappe di Azure:

- Altitudine
- Georilevazione
- Dettagli e foto di luoghi. Numeri di telefono e URL dei siti Web disponibili nell'API di ricerca di Mappe di Azure.
- URL mappa
- Strade: i dati relativi ai limiti di velocità sono disponibili tramite le API di pianificazione percorso e geocodifica inversa in Mappe di Azure.
- Visualizzazione strada statica

Mappe di Azure offre numerosi servizi Web REST aggiuntivi che possono risultare utili:

- [Operazioni spaziali](https://docs.microsoft.com/rest/api/maps/spatial): consente di eseguire l'offload di operazioni e calcoli spaziali complessi, ad esempio il geofencing, in un servizio.
- [Traffico](https://docs.microsoft.com/rest/api/maps/traffic): consente di accedere ai dati degli eventi e del flusso di traffico in tempo reale.

## <a name="geocoding-addresses"></a>Geocodifica di indirizzi

La geocodifica è il processo di conversione di un indirizzo, ad esempio "1 Microsoft Way, Redmond, WA", in una coordinata, ad esempio longitudine:-122.1298, latitudine: 47.64005. Le coordinate vengono quindi usate spesso per posizionare un indicatore su una mappa o per centrare una mappa.

Mappe di Azure offre numerosi metodi per la geocodifica degli indirizzi:

- [**Geocodifica di indirizzi in formato libero**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): consente di specificare una singola stringa di indirizzo, ad esempio "1 Microsoft Way, Redmond, WA", e di elaborare immediatamente la richiesta. Questa opzione è consigliata se è necessario eseguire rapidamente la geocodifica di singoli indirizzi.
- [**Geocodifica di indirizzi strutturati**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): consente di specificare le parti di un singolo indirizzo, ad esempio nome della via, città, paese e codice postale, e di elaborare immediatamente la richiesta. Questa opzione è consigliata se è necessario eseguire rapidamente la geocodifica di singoli indirizzi e i dati sono già analizzati nelle singole parti dell'indirizzo.
- [**Geocodifica di indirizzi batch**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): consente di creare una richiesta contenente fino a 10.000 indirizzi e li elabora in un certo periodo di tempo. Verrà eseguita la geocodifica di tutti gli indirizzi in parallelo nel server e, al termine dell'operazione, sarà possibile scaricare il set di risultati completo. Questa opzione è consigliata per la geocodifica di set di dati di grandi dimensioni.
- [**Ricerca fuzzy**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): Questa API combina la geocodifica degli indirizzi con la ricerca di punti di interesse. Accetta una stringa in formato libero che può essere un indirizzo, un luogo, un punto di riferimento, un punto di interesse o una categoria di punti di interesse ed elabora immediatamente la richiesta. Questa API è consigliata per le applicazioni in cui gli utenti possono cercare indirizzi o punti di interesse dalla stessa casella di testo.
- [**Ricerca fuzzy batch**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): consente di creare una richiesta contenente fino a 10.000 indirizzi, luoghi, punti di riferimento o punti di interesse e li elabora in un certo periodo di tempo. Tutti i dati verranno elaborati in parallelo nel server e, al termine dell'operazione, sarà possibile scaricare il set di risultati completo.

La tabella seguente offre un riferimento incrociato dei parametri dell'API Google Maps e dei parametri equivalenti dell'API in Mappe di Azure.

| Parametro API di Google Maps | Parametro API di Mappe di Azure equivalente  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` e `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality`: città<br/>`municipalitySubdivision`: quartiere, zona urbana/area metropolitana<br/>`countrySubdivision`: stato o provincia<br/>`countrySecondarySubdivision`: contea<br/>`countryTertiarySubdivision`: distretto<br/>`countryCode`: codice paese a due lettere |
| `key`                       | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](azure-maps-authentication.md). |
| `language`                  | `language`: vedere la documentazione relativa alle [lingue supportate](supported-languages.md).  |
| `region`                    | `countrySet`                       |

Un esempio di utilizzo del servizio di ricerca è documentato [qui](how-to-search-for-address.md). Assicurarsi di esaminare la documentazione relativa alle [procedure consigliate per la ricerca](how-to-use-best-practices-for-search.md).

> [!TIP]
> Le API per la ricerca fuzzy e la geocodifica degli indirizzi in formato libero possono essere usate con la modalità di completamento automatico aggiungendo `&amp;typeahead=true` all'URL della richiesta. Questo comunica al server che è probabile che il testo di input sia parziale e di passare quindi alla modalità predittiva.

## <a name="reverse-geocode-a-coordinate"></a>Geocodifica inversa di una coordinata

La geocodifica inversa è il processo di conversione delle coordinate geografiche, ad esempio, longitudine:-122.1298, latitudine: 47.64005 nell'indirizzo corrispondente approssimativo, ad esempio "1 Microsoft Way, Redmond, WA".

Mappe di Azure offre numerosi metodi di geocodifica inversa:

- [**Geocodifica inversa di indirizzi**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): consente di specificare una singola coordinata geografica per ottenere l'indirizzo corrispondente approssimativo e di elaborare immediatamente la richiesta.
- [**Geocodifica inversa di incroci**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): consente di specificare una singola coordinata geografica per ottenere le informazioni sugli incroci adiacenti, ad esempio 1st street e Main street, e di elaborare immediatamente la richiesta.
- [**Geocodifica inversa di indirizzi batch**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): consente di creare una richiesta contenente fino a 10.000 coordinate e le elabora in un certo periodo di tempo. Tutti i dati verranno elaborati in parallelo nel server e, al termine dell'operazione, sarà possibile scaricare il set di risultati completo.

La tabella seguente offre un riferimento incrociato dei parametri dell'API Google Maps e dei parametri equivalenti dell'API in Mappe di Azure.

| Parametro API di Google Maps   | Parametro API di Mappe di Azure equivalente   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](azure-maps-authentication.md). |
| `language`                  | `language`: vedere la documentazione relativa alle [lingue supportate](supported-languages.md).  |
| `latlng`                    | `query`  |
| `location_type`             | *N/D*     |
| `result_type`               | `entityType`    |

Assicurarsi di esaminare la documentazione relativa alle [procedure consigliate per la ricerca](how-to-use-best-practices-for-search.md).

L'API di geocodifica inversa di Mappe di Azure include alcune funzionalità aggiuntive non disponibili in Google Maps che potrebbero essere utili da integrare durante la migrazione dell'app:

- Recupero dei dati sui limiti di velocità.
- Recupero delle informazioni specifiche della strada: strada locale, arteria stradale, accesso limitato, rampa e così via.
- Lato della strada corrispondente alla coordinata specificata.

## <a name="search-for-points-of-interest"></a>Cercare i punti di interesse

È possibile cercare i dati del punto di interesse in Google Maps usando l'API di ricerca luoghi. Questa API offre tre modi diversi per cercare i punti di interesse:

- **Ricerca di un luogo dal testo:** cerca un punto di interesse in base al nome, all'indirizzo o al numero di telefono.
- **Ricerca nelle vicinanze**: cerca i punti di interesse che si trovano entro una certa distanza da una posizione.
- **Ricerca testuale:** cerca i luoghi usando un testo in formato libero che includa informazioni sul punto di interesse e sulla posizione. Ad esempio: "pizza a New York" o "ristoranti vicino a Main St".

Mappe di Azure offre numerose API di ricerca per i punti di interesse:

- [**Ricerca di punti di interesse**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): consente di cercare i punti di interesse in base al nome. Ad esempio: "Starbucks".
- [**Ricerca per categoria di punti di interesse**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): consente di cercare i punti di interesse in base alla categoria. Ad esempio: "ristorante".
- [**Ricerca nelle vicinanze**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): cerca i punti di interesse che si trovano entro una certa distanza da una posizione.
- [**Ricerca fuzzy**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): Questa API combina la geocodifica degli indirizzi con la ricerca di punti di interesse. Accetta una stringa in formato libero che può essere un indirizzo, un luogo, un punto di riferimento, un punto di interesse o una categoria di punti di interesse ed elabora immediatamente la richiesta. Questa API è consigliata per le applicazioni in cui gli utenti possono cercare indirizzi o punti di interesse dalla stessa casella di testo.
- [**Ricerca all'interno di una geometria**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): consente di cercare i punti di interesse all'interno di una geometria specificata (poligono).
- [**Ricerca lungo un percorso**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): consente di cercare i punti di interesse lungo un percorso specificato.
- [**Ricerca fuzzy batch**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): consente di creare una richiesta contenente fino a 10.000 indirizzi, luoghi, punti di riferimento o punti di interesse e li elabora in un certo periodo di tempo. Tutti i dati verranno elaborati in parallelo nel server e, al termine dell'operazione, sarà possibile scaricare il set di risultati completo.

Attualmente Mappe di Azure non dispone di un'API equivalente all'API di ricerca testuale di Google Maps.

> [!TIP]
> Le API per la ricerca di punti di interesse, la ricerca per categoria di punti di interesse e la ricerca fuzzy possono essere usate con la modalità di completamento automatico aggiungendo `&amp;typeahead=true` all'URL della richiesta. Questo comunica al server che è probabile che il testo di input sia parziale e di passare quindi alla modalità predittiva.

Assicurarsi di esaminare la documentazione relativa alle [procedure consigliate per la ricerca](how-to-use-best-practices-for-search.md).

### <a name="find-place-from-text"></a>Ricerca di un luogo dal testo

Per cercare i punti di interesse in base al nome o all'indirizzo, è possibile usare le API [Ricerca di punti di interesse](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) e [Ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) di Mappe di Azure.

La tabella seguente offre un riferimento incrociato dei parametri dell'API Google Maps e dei parametri equivalenti dell'API in Mappe di Azure.

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

La tabella seguente offre un riferimento incrociato dei parametri dell'API Google Maps e dei parametri equivalenti dell'API in Mappe di Azure.

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

È possibile usare Mappe di Azure per calcolare percorsi e indicazioni. Mappe di Azure offre numerose funzionalità equivalenti a quelle del servizio di pianificazione percorso di Google Maps, ad esempio:

- Orari di arrivo e di partenza.
- Percorsi basati sul traffico in tempo reale e predittivi.
- Modalità di trasporto diverse, ad esempio in auto, a piedi, in bicicletta.

> [!NOTE]
> Mappe di Azure richiede che tutti i punti di tragitto siano coordinate. È quindi prima necessario eseguire la geocodifica degli indirizzi.

Il servizio di pianificazione percorso di Mappe di Azure fornisce le API seguenti per il calcolo dei percorsi:

- [**Calcolo percorso**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): consente di calcolare un percorso e di elaborare immediatamente la richiesta. Questa API supporta le richieste GET e POST. Le richieste POST sono consigliate quando si specifica un numero elevato di punti di tragitto o quando si usano molte opzioni pel percorso per assicurarsi che la richiesta URL non diventi troppo lunga e causi problemi.
- [**Percorso batch**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): consente di creare una richiesta contenente fino a 1.000 richieste di percorso e le elabora in un certo periodo di tempo. Tutti i dati verranno elaborati in parallelo nel server e, al termine dell'operazione, sarà possibile scaricare il set di risultati completo.
- [**Servizi di mobilità**](https://docs.microsoft.com/rest/api/maps/mobility): consente di calcolare percorsi e indicazioni usando il trasporto pubblico.

La tabella seguente offre un riferimento incrociato dei parametri dell'API Google Maps e dei parametri equivalenti dell'API in Mappe di Azure.

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
> Per impostazione predefinita, l'API di pianificazione percorso di Mappe di Azure restituisce solo un riepilogo (distanza e ora) e le coordinate per il percorso. Usare il parametro `instructionsType` per recuperare le istruzioni dettagliate. Il parametro `routeRepresentation` può essere usato per filtrare il riepilogo e il percorso.

L'API di pianificazione percorso di Mappe di Azure include numerose funzionalità aggiuntive non disponibili in Google Maps che potrebbero essere utili da integrare durante la migrazione dell'app:

- Supporto per il tipo di percorso: più breve, più veloce, più interessante/panoramico e con minore consumo di carburante.
- Supporto per modalità di viaggio aggiuntive: autobus, motocicletta, taxi, autocarro e furgone.
- Supporto per 150 punti di tragitto.
- Calcolo di più tempi di viaggio in un'unica richiesta. Traffico cronologico, traffico in tempo reale, senza traffico.
- Possibilità di evitare tipi di strade aggiuntivi: corsie preferenziali, strade non asfaltate, strade già usate.
- Possibilità di specificare aree personalizzate da evitare.
- Limitazione dell'elevazione massima del percorso.
- Pianificazione percorso basato sulle specifiche del motore. Calcolo dei percorsi per veicoli a combustione o elettrici in base al carburante o alla carica rimanente e alle specifiche del motore.
- Supporto dei parametri del percorso per veicoli commerciali; dimensioni del veicolo, peso, numero di assi e tipo di carico.
- Possibilità di specificare la velocità massima del veicolo.

Inoltre, il servizio di pianificazione percorso in Mappe di Azure supporta anche il [calcolo dei punti raggiungibili](https://docs.microsoft.com/rest/api/maps/route/getrouterange), noti anche come isocrone, che generano un poligono che copre un'area percorribile in qualsiasi direzione da un punto di origine entro un periodo di tempo specificato o una quantità di carburante/carica specificata.

## <a name="retrieve-a-map-image"></a>Recuperare un'immagine mappa

Mappe di Azure fornisce un'API per il rendering delle immagini mappa statiche con overlay dei dati. L'API [rendering immagini mappa](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) di Mappe di Azure è simile all'API mappa statica in Google Maps.

> [!NOTE]
> Mappe di Azure richiede che il centro e le posizioni di indicazioni e percorso siano coordinate nel formato "longitudine, latitudine", mentre Google Maps usa il formato "latitudine, longitudine". È quindi prima necessario eseguire la geocodifica degli indirizzi.

La tabella seguente offre un riferimento incrociato dei parametri dell'API Google Maps e dei parametri equivalenti dell'API in Mappe di Azure.

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

Oltre a poter generare un'immagine mappa statica, il servizio di rendering di Mappe di Azure offre anche la possibilità di accedere direttamente alle tessere mappa in formato raster (PNG) e vettoriale:

- [**Tessera mappa**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): recupera le tessere raster (PNG) e vettoriali per le mappe di base (strade, limiti, sfondo).
- [**Tessera immagine mappa**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile): recupera le tessere di immagini aeree e satellitari.

> [!TIP]
> Qualche anno fa, molte applicazioni di Google Maps sono passate da esperienze mappa interattive a immagini mappa statiche per risparmiare sui costi. In Mappe di Azure è spesso più conveniente usare il controllo mappa interattivo in Web SDK, perché viene addebitato in base ai caricamenti delle tessere mappa. Le tessere mappa in Mappe di Azure sono grandi e spesso sono necessari pochi istanti per ricreare la stessa visualizzazione mappa come mappa statica e le tessere mappa vengono automaticamente memorizzate nella cache dal browser. Di conseguenza, il controllo mappa interattivo spesso genera solo una frazione di una transazione per la riproduzione di una visualizzazione mappa statica. Le operazioni di panoramica e zoom caricheranno più tessere, tuttavia nel controllo mappa sono disponibili opzioni per disabilitare questo comportamento, se lo si desidera. Il controllo mappa interattivo fornisce anche numerose altre opzioni di visualizzazione rispetto a quelle dei servizi mappa statici.

### <a name="marker-url-parameter-format-comparison"></a>Confronto tra il formato dei parametri URL per gli indicatori

**Prima: Google Maps**

In Google Maps è possibile aggiungere gli indicatori a un'immagine mappa statica usando il parametro `markers` nell'URL. Il parametro `markers` accetta uno stile e un elenco di posizioni in base a cui verrà eseguito il rendering sulla mappa, come illustrato di seguito:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

È possibile usare ulteriori stili aggiungendo altri parametri `markers` all'URL con uno stile e un set di posizioni diversi.

Le posizioni dell'indicatore vengono specificate con il formato "latitudine, longitudine".

Gli stili degli indicatori in Google Maps vengono aggiunti con il formato `optionName:value`, con più stili separati dalla barra verticale (\|), ad esempio: "optionName1:value1\|optionName2:value2". Si noti che i nomi e i valori delle opzioni sono separati da due punti (:). È possibile usare i nomi delle opzioni di stile seguenti per applicare uno stile agli indicatori in Google Maps:

- `color`: colore dell'icona predefinita dell'indicatore. Può essere un colore esadecimale a 24 bit (`0xrrggbb`) o uno dei valori seguenti: `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label`: singolo carattere alfanumerico maiuscolo da visualizzare nella parte superiore dell'icona.
- `size`: dimensioni dell'indicatore. Può essere `tiny`, `mid` o `small`.

È possibile usare anche icone personalizzate in Google Maps usando i nomi delle opzioni di stile seguenti:

- `anchor`: specifica come allineare l'immagine dell'icona alla coordinata. Può essere un valore in pixel (x, y) o uno dei valori seguenti: `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft` o `bottomright`.
- `icon`: URL che punta all'immagine dell'icona.

In Google Maps è ad esempio possibile aggiungere un indicatore rosso di medie dimensioni alla mappa in corrispondenza delle coordinate (longitudine:-110, latitudine: 45) con il parametro URL seguente:

```
&markers=color:red|size:mid|45,-110
```

<center>

![Indicatore di Google Maps](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**Dopo: Mappe di Azure**

In Mappe di Azure è anche possibile aggiungere gli indicatori a un'immagine mappa statica specificando il parametro `pins` nell'URL. Come in Google Maps, in questo parametro è possibile specificare uno stile e un elenco di posizioni ed è possibile specificare il parametro `pins` più volte per supportare indicatori con stili diversi.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

È possibile usare ulteriori stili aggiungendo altri parametri `pins` all'URL con uno stile e un set di posizioni diversi.

Per le posizioni dei segnaposto, Mappe di Azure richiede che le coordinate siano espresse nel formato "longitudine latitudine", mentre Google Maps usa il formato "latitudine,longitudine". Si noti anche che in Mappe di Azure la longitudine e la latitudine sono separate da uno spazio anziché da una virgola.

Il valore `iconType` specifica il tipo di segnaposto da creare con i valori possibili seguenti:

- `default`: icona predefinita del segnaposto.
- `none`: nessuna icona visualizzata. Verrà eseguito il rendering solo delle etichette.
- `custom`: specifica che è necessario usare un'icona personalizzata. È possibile aggiungere un URL che punta all'immagine dell'icona alla fine del parametro `pins` dopo le informazioni sulla posizione del segnaposto.
- `{udid}`: ID dati univoco (UDID) per un'icona archiviata nella piattaforma di archiviazione dati di Mappe di Azure.

Gli stili dei segnaposto in Mappe di Azure vengono aggiunti con il formato `optionNameValue`, con più stili separati dalla barra verticale (\|), ad esempio: `iconType|optionName1Value1|optionName2Value2`. Si noti che i nomi e i valori delle opzioni non sono separati. È possibile usare i nomi delle opzioni di stile seguenti per applicare uno stile agli indicatori in Mappe di Azure:

- `al`: specifica l'opacità (alfa) dell'indicatore. Può essere un numero compreso tra 0 e 1.
- `an`: specifica l'ancoraggio del segnaposto. Valori x e y in pixel specificati nel formato "x y".
- `co`: colore del segnaposto. Deve essere un colore esadecimale a 24 bit: da `000000` a `FFFFFF`.
- `la`: specifica l'ancoraggio dell'etichetta. Valori x e y in pixel specificati nel formato "x y".
- `lc`: colore dell'etichetta. Deve essere un colore esadecimale a 24 bit: da `000000` a `FFFFFF`.
- `ls`: dimensioni dell'etichetta in pixel. Può essere un numero maggiore di 0.
- `ro`: valore in gradi per la rotazione dell'icona. Può essere un numero compreso tra -360 e 360.
- `sc`: valore di scala per l'icona del segnaposto. Può essere un numero maggiore di 0.

I valori delle etichette vengono specificati per ogni posizione del segnaposto anziché usare un singolo valore di etichetta applicabile a tutti gli indicatori nell'elenco di posizioni. Il valore dell'etichetta può essere costituito da una stringa di più caratteri racchiusa tra virgolette singole per assicurarsi che non venga interpretato come valore di stile o posizione.

In Mappe di Azure, ad esempio, è possibile aggiungere un'icona predefinita di colore rosso (`FF0000`) con l'etichetta "Space Needle" posizionata sotto (15 50) l'icona in corrispondenza delle coordinate (longitudine: -122.349300, latitudine: 47.620180) con il parametro URL seguente:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Indicatore di Mappe di Azure](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

Nell'esempio seguente vengono aggiunti tre segnaposto con i valori di etichetta '1', '2' e '3':

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Più indicatori in Mappe di Azure](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Confronto tra il formato dei parametri URL per i percorsi

**Prima: Google Maps**

In Google Maps è possibile aggiungere linee e poligoni a un'immagine mappa statica usando il parametro `path` nell'URL. Il parametro `path` accetta uno stile e un elenco di posizioni in base a cui verrà eseguito il rendering sulla mappa, come illustrato di seguito:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

È possibile usare ulteriori stili aggiungendo altri parametri `path` all'URL con uno stile e un set di posizioni diversi.

Le posizioni dei percorsi in Google Maps sono specificate con il formato `latitude1,longitude1|latitude2,longitude2|…`. I percorsi possono essere codificati o contenere gli indirizzi per i punti.

Gli stili dei percorsi in Google Maps vengono aggiunti con il formato `optionName:value`, con più stili separati dalla barra verticale (\|), ad esempio: `optionName1:value1|optionName2:value2`. Si noti che i nomi e i valori delle opzioni sono separati da due punti (:). È possibile usare i nomi delle opzioni di stile seguenti per applicare uno stile ai percorsi in Google Maps:

- `color`: colore del percorso o del contorno del poligono. Può essere un colore esadecimale a 24 bit (`0xrrggbb`), un colore esadecimale a 32 bit (`0xrrggbbbaa`) o uno dei valori seguenti: nero, marrone, verde, viola, giallo, blu, grigio, arancione, rosso, bianco.
- `fillColor`: colore di riempimento dell'area del percorso (poligono). Può essere un colore esadecimale a 24 bit (`0xrrggbb`), un colore esadecimale a 32 bit (`0xrrggbbbaa`) o uno dei valori seguenti: nero, marrone, verde, viola, giallo, blu, grigio, arancione, rosso, bianco.
- `geodesic`: indica se il percorso deve essere una linea che segue la curvatura della terra.
- `weight`: spessore della linea del percorso in pixel.

In Google Maps, ad esempio, è possibile aggiungere alla mappa una linea rossa con opacità del 50% e uno spessore di 4 pixel tra le coordinate (longitudine: -110, latitudine: 45 e longitudine: -100, latitudine: 50) con il parametro URL seguente:

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Polilinea di Google Maps](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**Dopo: Mappe di Azure**

In Mappe di Azure è anche possibile aggiungere linee e poligoni a un'immagine mappa statica specificando il parametro `path` nell'URL. Come in Google Maps, in questo parametro è possibile specificare uno stile e un elenco di posizioni ed è possibile specificare il parametro `path` più volte per eseguire il rendering di più cerchi, linee e poligoni con stili diversi.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Per le posizioni dei percorsi, Mappe di Azure richiede che le coordinate siano espresse nel formato "longitudine, latitudine", mentre Google Maps usa il formato "latitudine, longitudine". Si noti anche che in Mappe di Azure la longitudine e la latitudine sono separate da uno spazio anziché da una virgola. Mappe di Azure non supporta percorsi o indirizzi codificati per i punti. È possibile caricare set di dati di dimensioni maggiori come file GeoJSON nell'API di archiviazione dei dati di Mappe di Azure, come illustrato [qui](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage).

Gli stili dei percorsi in Mappe di Azure vengono aggiunti con il formato `optionNameValue`, con più stili separati dalla barra verticale (\|), ad esempio: `optionName1Value1|optionName2Value2`. Si noti che i nomi e i valori delle opzioni non sono separati. È possibile usare i nomi delle opzioni di stile seguenti per applicare uno stile ai percorsi in Mappe di Azure:

- `fa`: opacità del colore di riempimento (alfa) usata per il rendering dei poligoni. Può essere un numero compreso tra 0 e 1.
- `fc`: colore di riempimento usato per il rendering dell'area di un poligono.
- `la`: opacità del colore della linea (alfa) usata per il rendering delle linee e del contorno dei poligoni. Può essere un numero compreso tra 0 e 1.
- `lc`: colore della linea usato per il rendering delle linee e del contorno dei poligoni.
- `lw`: larghezza della linea in pixel.
- `ra`: specifica il raggio di un cerchio in metri.

In Mappe di Azure, ad esempio, è possibile aggiungere una linea rossa con opacità del 50% e uno spessore di 4 pixel alla mappa tra le coordinate (longitudine: -110, latitudine: 45 e longitudine: -100, latitudine: 50) con il parametro URL seguente:

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Polilinea di Mappe di Azure](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>Calcolare una matrice di distanze

Mappe di Azure fornisce un'API per il calcolo dei tempi di viaggio e delle distanze tra un set di posizioni come matrice di distanze. L'API della matrice di distanze di Mappe di Azure è simile all'API della matrice di distanze in Google Maps.

- [**Matrice di percorsi**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): calcola in modo asincrono i tempi di viaggio e le distanze per un set di origini e destinazioni. Sono supportate fino a 700 celle per richiesta (il numero di origini moltiplicato per il numero di destinazioni). Tenendo presente questo vincolo, gli esempi di possibili dimensioni della matrice sono: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> È possibile inviare una richiesta all'API della matrice di distanze solo usando una richiesta POST con le informazioni sull'origine e sulla destinazione nel corpo della richiesta. Mappe di Azure richiede inoltre che tutte le origini e le destinazioni siano coordinate. È quindi prima necessario eseguire la geocodifica degli indirizzi.

La tabella seguente offre un riferimento incrociato dei parametri dell'API Google Maps e dei parametri equivalenti dell'API in Mappe di Azure.

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
> Tutte le opzioni di pianificazione percorso avanzate disponibili nell'API di pianificazione percorso di Mappe di Azure (percorso autocarro, specifiche del motore, evitare...) sono supportate nell'API della matrice di distanze di Mappe di Azure.

## <a name="get-a-time-zone"></a>Ottenere un fuso orario

Mappe di Azure fornisce un'API per il recupero del fuso orario in cui si trova una coordinata. L'API fusi orari di Mappe di Azure è simile all'API fusi orari in Google Maps:

- [**Fuso orario per coordinata**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): consente di specificare una coordinata e ottenere i dettagli relativi al fuso orario in cui si trova.

La tabella seguente offre un riferimento incrociato dei parametri dell'API Google Maps e dei parametri equivalenti dell'API in Mappe di Azure.

| Parametro API di Google Maps | Parametro API di Mappe di Azure equivalente   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key`: vedere anche la documentazione relativa all'[Autenticazione con Mappe di Azure](azure-maps-authentication.md).       |
| `language`                  | `language`: vedere la documentazione relativa alle [lingue supportate](supported-languages.md).    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Oltre a questo, la piattaforma Mappe di Azure fornisce anche alcune API per i fusi orari aggiuntive per semplificare le conversioni con ID e nomi dei fusi orari:

- [**Fuso orario in base all'ID**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): Restituisce le informazioni sul fuso orario correnti, cronologiche e future per l'ID del fuso orario IANA specificato.
- [**Enumerazione fusi orari IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): restituisce un elenco completo di ID di fusi orari IANA. Gli aggiornamenti al servizio IANA si riflettono nel sistema entro un giorno.
- [**Enumerazione fusi orari Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): restituisce un elenco completo di ID di fusi orari Windows.
- [**Versione fuso orario IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): restituisce il numero di versione IANA corrente usato da Mappe di Azure.
- [**Fuso orario da Windows a IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): restituisce l'ID IANA corrispondente da ID di fuso orario Windows valido. Per un singolo ID Windows è possibile che vengano restituiti più ID IANA.

## <a name="client-libraries"></a>Librerie client

Mappe di Azure fornisce le librerie client per i linguaggi di programmazione seguenti:

- JavaScript, TypeScript, Node.js - [Documentazione](how-to-use-services-module.md) \| [Pacchetto NPM](https://www.npmjs.com/package/azure-maps-rest)

Librerie client open source per altri linguaggi di programmazione:

- .NET Standard 2.0 - [Progetto GitHub](https://github.com/perfahlen/AzureMapsRestServices) \| [Pacchetto NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>Risorse aggiuntive

Di seguito sono riportate alcune risorse e documentazione aggiuntive sui servizi REST di Mappe di Azure.

- [Procedure consigliate per la ricerca](how-to-use-best-practices-for-search.md)
- [Cercare un indirizzo](how-to-search-for-address.md)
- [Documentazione di riferimento per l'API del servizio REST di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui servizi REST di Mappe di Azure.

> [!div class="nextstepaction"]
> [Procedure consigliate per l'uso del servizio di ricerca](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Come usare il modulo dei servizi (Web SDK)](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-maps)
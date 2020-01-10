---
title: Glossario di Mappe di Azure | Microsoft Docs
description: Glossario dei termini usati di frequente associati a mappe di Azure, servizi location based e GIS.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 02bf5ba30a1fc7b4ee739cb0a591ffe084269541
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75408865"
---
# <a name="glossary"></a>Glossario

Di seguito è riportato un elenco dei termini comuni usati con Mappe di Azure.

## <a name="a"></a>Una

<a name="address-validation"></a>**Convalida degli indirizzi**: processo di verifica dell'esistenza di un indirizzo.

<a name="advanced-routing"></a>**Routing avanzato**: raccolta di servizi che eseguono operazioni avanzate usando i dati di routing stradale, ad esempio il calcolo di intervalli raggiungibili (isocrone), matrici di distanza e richieste di route batch.

<a name="aerial-imagery"></a>**Immagini aeree**: vedere [Immagini satellite](#satellite-imagery). 

<a name="along-a-route-search"></a>**Lungo una ricerca di route**: una query spaziale che cerca i dati che rientrano in un periodo di deviazione o una distanza specificata da un percorso di route.

<a name="altitude"></a>**Altitude**: altezza o altezza verticale di un punto sopra una superficie di riferimento. Le misure dell'altitudine sono basate su un datum di riferimento, ad esempio il livello medio del mare. Vedere anche Quota.

<a name="ambiguous"></a>**Ambiguo**: stato di incertezza nella classificazione dei dati esistente quando a un oggetto possono essere assegnati in modo appropriato due o più valori per un attributo specifico. Ad esempio, quando si esegue la geocodifica di "CA", vengono restituiti due risultati ambigui, "Canada" e "California", poiché "CA" è un codice che si riferisce rispettivamente a un paese e a uno Stato. 

<a name="annotation"></a>**Annotazione**: testo o grafica visualizzato sulla mappa per fornire informazioni all'utente. L'annotazione può identificare o descrivere un'entità specifica sulla mappa, fornire informazioni generali relative a un'area della mappa o mostrare informazioni sulla mappa stessa.

<a name="antimeridian"></a>**Antimeridian**: noto anche<sup>come meridiano 180</sup> , è il punto in cui-180 gradi e 180 gradi di longitudine soddisfano. È opposto al meridiano zero sul globo.

<a name="application-programming-interface-api"></a>**API (Application Programming Interface)** : specifica che consente agli sviluppatori di creare applicazioni.

<a name="api-key"></a>**Chiave API**: vedere [autenticazione con chiave condivisa](#shared-key-authentication).

<a name="area-of-interest-aoi"></a>**Area di interesse (AOI)** : l'extent utilizzato per definire un'area di interesse per una mappa o per la produzione di un database.

<a name="asset-tracking"></a>**Rilevamento delle risorse**: processo di rilevamento della posizione di un asset, ad esempio una persona, un veicolo o un altro oggetto.

<a name="asynchronous-request"></a>**Richiesta asincrona**: una richiesta HTTP che apre una connessione ed effettua una richiesta al server che restituisce un identificatore per la richiesta asincrona, quindi chiude la connessione. Il server continua a elaborare la richiesta e l'utente può controllare lo stato usando l'identificatore. Al termine dell'elaborazione della richiesta, l'utente può scaricare la risposta. Questo tipo di richiesta viene usato per i processi a esecuzione prolungata.

<a name="autocomplete"></a>**Completamento automatico**: una funzionalità di un'applicazione consente di stimare il resto di una parola che un utente sta digitando. 

<a name="autosuggest"></a>**Suggerimenti**automatici: una funzionalità di un'applicazione consente di stimare le possibilità logiche per la digitazione dell'utente.

<a name="azure-location-based-services-lbs"></a>**Servizi location based di Azure (lbs)** : il nome precedente di Azure Maps quando era in anteprima.

<a name="azure-active-directory"></a>**Azure Active Directory (Azure ad)** : Azure ad è il servizio Microsoft di gestione delle identità e degli accessi basato sul cloud. Azure Maps Azure AD l'integrazione è attualmente disponibile in anteprima per tutte le API di Azure maps. Azure AD supporta il controllo degli accessi in base al ruolo (RBAC) per consentire l'accesso con granularità fine alle risorse di Azure maps. Per altre informazioni sull'integrazione di Azure Maps Azure AD, vedere [mappe e Azure ad di Azure](azure-maps-authentication.md) e [gestire l'autenticazione in mappe di Azure](how-to-manage-authentication.md).

<a name="azure-maps-key"></a>**Azure Maps Key**: vedere [autenticazione con chiave condivisa](#shared-key-authentication).

## <a name="b"></a>b

<a name="base-map"></a>**Mappa di base**: parte di un'applicazione mappa che visualizza informazioni di riferimento in background, ad esempio strade, punti di interesse e confini politici.

<a name="batch-request"></a>**Richiesta batch**: processo di combinazione di più richieste in un'unica richiesta.

<a name="bearing"></a>**Bearing**: direzione orizzontale di un punto in relazione a un altro punto. Questo valore viene espresso come un angolo rispetto al Nord, da 0 a 360 gradi in senso orario. 

<a name="boundary"></a>**Limite**: linea o poligono che separa le entità politiche adiacenti, ad esempio paesi/aree, distretti e proprietà. Un confine è costituito da una linea che può seguire o meno le caratteristiche fisiche, come fiumi, montagne o muri.

<a name="bounds"></a>**Limiti**: vedere il rettangolo di [delimitazione](#bounding-box).

<a name="bounding-box"></a>Rettangolo di **delimitazione**: set di coordinate utilizzato per rappresentare un'area rettangolare sulla mappa. 

## <a name="c"></a>C

<a name="cadastre"></a>**Catasto**: record di proprietà e terreni registrati. Vedere anche [Lotto](#parcel).

<a name="camera"></a>**Fotocamera**: nel contesto di un controllo mappa interattivo, una fotocamera definisce il campo di visualizzazione Maps. Il riquadro di visualizzazione della fotocamera è determinato in base a diversi parametri della mappa, ovvero il centro, il livello di zoom, l'inclinazione e l'orientamento. 

<a name="centroid"></a>**Baricentro**: centro geometrico di una funzionalità. Il centroide di una linea è il relativo punto medio, mentre il centroide di un poligono è il centro della relativa area.

<a name="choropleth-map"></a>**Mappa choropleth**: mappa tematica in cui le aree sono ombreggiate in proporzione rispetto a una misura di una variabile statistica visualizzata sulla mappa. Ad esempio, una mappa in cui i confini di ogni Stato degli Stati Uniti sono colorati in base alla popolazione rispetto a tutti gli altri Stati.

<a name="concave-hull"></a>**Guscio concavo**: forma che rappresenta una possibile geometria concavo che racchiude tutte le forme nel set di dati specificato. La forma generata è simile al risultato che si otterrebbe impacchettando i dati in un sacchetto di plastica e riscaldandolo. In questo modo, infatti, si produrrebbero ampi spazi tra i diversi punti dati.

<a name="consumption-model"></a>**Modello a consumo**: informazioni che definiscono la velocità con cui un veicolo consuma carburante o energia elettrica. Vedere anche la [documentazione relativa al modello di consumo](consumption-model.md).

<a name="control"></a>**Controllo**: componente autonomo o riutilizzabile costituito da un'interfaccia utente grafica che definisce un set di comportamenti per l'interfaccia. Un controllo mappa, ad esempio, è in genere la parte dell'interfaccia utente che carica una mappa interattiva.

<a name="convex-hull"></a>**Convessa Hull**: una struttura convessa è una forma che rappresenta la geometria convessa minima che racchiude tutte le forme nel set di dati specificato. La forma generata è simile al risultato che si otterrebbe avvolgendo in un elastico tutto il set di dati.

<a name="coordinate"></a>**Coordinata**: è costituito dai valori di longitudine e latitudine usati per rappresentare una posizione su una mappa.

<a name="coordinate-system"></a>**Sistema di coordinate**: un Framework di riferimento usato per definire le posizioni dei punti nello spazio in due o tre dimensioni.

<a name="country-code"></a>**Indicativo paese**: identificatore univoco per un paese/area geografica in base allo standard ISO. ISO2 è un codice di due caratteri per un paese (ad esempio, US), mentre ISO3 è un codice di tre caratteri (ad esempio, USA).

<a name="country-subdivision"></a>**Suddivisione in paesi**: una suddivisione di primo livello di un paese/area geografica, comunemente nota come stato o provincia.

<a name="country-secondary-subdivision"></a>**Suddivisione secondaria del paese**: una suddivisione di secondo livello di un paese, comunemente nota come regione.

<a name="country-tertiary-subdivision"></a>**Suddivisione dei paesi terziaria**: una suddivisione di terzo livello di un paese/area geografica, in genere un'area denominata come un reparto.

<a name="cross-street"></a>**Cross Street**: punto in cui due o più strade si intersecano.

<a name="cylindrical-projection"></a>**Proiezione cilindrica**: proiezione che trasforma i punti da un sferoide o una sfera su un cilindro tangente o secante. Il cilindro viene quindi sezionato dall'alto verso il basso e trasferito su un piano.

## <a name="d"></a>D

<a name="datum"></a>**Datum**: le specifiche di riferimento di un sistema di misurazione, un sistema di posizioni delle coordinate su una superficie (un datum orizzontale) o altezze sopra o sotto una superficie (un dato verticale).

<a name="dbf-file"></a>**File DBF**: formato di file di database usato in combinazione con forma (SHP).

<a name="degree-minutes-seconds-dms"></a>**Degree minutes seconds (DMS)** : unità di misura per la descrizione della latitudine e della longitudine. Un grado è 1/360<sup>o</sup> di un cerchio. Il grado viene ulteriormente suddiviso in 60 minuti e un minuto in 60 secondi.

<a name="delaunay-triangulation"></a>**Triangolazione Delaunay**: una tecnica per la creazione di una mesh di triangoli non sovrapposte contigui da un set di dati di punti. Nessun punto appartenente al set di dati è presente all'interno del circumcerchio di ogni triangolo.

<a name="demographics"></a>**Dati demografici**: caratteristiche statistiche, ad esempio età, tasso di nascita e reddito, di una popolazione umana.

<a name="destination"></a>**Destinazione**: un punto finale o un percorso in cui un utente sta viaggiando.

<a name="digital-elevation-model-dem"></a>**Modello di elevazione digitale (DEM)** : set di dati di elevazione dei valori correlati a una superficie, acquisiti su un'area a intervalli regolari usando un riferimento comune. I DEM vengono in genere usati per rappresentare il rilievo del terreno.

<a name="dijkstra's-algorithm"></a>**Algoritmo di Dijkstra**: algoritmo che esamina la connettività di una rete per trovare il percorso più breve tra due punti.

<a name="distance-matrix"></a>**Distance Matrix**: matrice che contiene le informazioni sulla distanza e sul tempo di viaggio tra un set di origini e destinazioni. 

## <a name="e"></a>E

<a name="elevation"></a>**Innalzamento**di livello: la distanza verticale di un punto o di un oggetto al di sopra o al di sotto di una superficie di riferimento o di un datum (in genere media Sea Il termine di riferisce in genere all'altitudine del terreno.

<a name="envelope"></a>**Busta**: vedere il rettangolo di [delimitazione](#bounding-box).

<a name="extended-postal-code"></a>**Codice postale esteso**: codice postale che può includere informazioni aggiuntive. Negli Stati Uniti, ad esempio, i codici postali sono composti da cinque cifre, ma esistono anche codici postali estesi che includono altre quattro cifre. Queste ultime vengono usate per identificare un segmento geografico all'interno dell'area di recapito contrassegnata da cinque cifre, ad esempio un isolato, un condominio o una cassetta postale, in modo da rendere più efficiente l'ordinamento e il recapito della posta.

<a name="extent"></a>**Extent**: vedere il rettangolo di [delimitazione](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a>**Autenticazione federata**: metodo di autenticazione che consente di usare un singolo meccanismo di accesso/autenticazione tra più app Web e per dispositivi mobili. 

<a name="feature"></a>**Funzionalità**: oggetto che combina una geometria con informazioni aggiuntive sui metadati. 

<a name="feature-collection"></a>**Raccolta di funzionalità**: raccolta di oggetti feature.

<a name="find-along-route"></a>**Trova lungo Route**: una query spaziale che cerca i dati che si trovano entro il tempo di deviazione o la distanza specificata da un percorso di route.

<a name="find-nearby"></a>**Trova nelle vicinanze**: una query spaziale che esegue la ricerca di una distanza di linea retta fissa (come il corvo vola) da un punto.

<a name="fleet-management"></a>**Gestione della flotta**: la gestione di veicoli commerciali, ad esempio automobili, camion, navi e aerei. La gestione della flotta può includere una vasta gamma di funzioni, ad esempio per il finanziamento, la manutenzione e la telematica (rilevamento e diagnostica) dei veicoli e anche per la gestione di guidatori, velocità, carburante, salute e sicurezza. La gestione della flotta è un processo adottato dalle società che svolgono attività commerciali basate sui trasporti per ridurre al minimo i rischi e i costi complessivi del trasporto e del personale e garantire al tempo stesso la conformità alle normative vigenti.

<a name="free-flow-speed"></a>**Velocità del flusso libero**: la velocità del flusso disponibile è prevista in condizioni ideali. Corrisponde in genere al limite di velocità.

<a name="free-form-address"></a>**Indirizzo formato libero**: indirizzo completo rappresentato come una singola riga di testo.

<a name="fuzzy-search"></a>**Ricerca fuzzy**: una ricerca che accetta una stringa di testo in formato libero che può essere un indirizzo o un punto di interesse. 

## <a name="g"></a>G

<a name="geocode"></a>**Geocode**: indirizzo o percorso convertito in una coordinata che può essere usata per visualizzare tale posizione su una mappa. 

<a name="geocoding"></a>La **geocodifica**: nota anche come geocodifica diretta, è il processo di conversione dell'indirizzo dei dati della posizione in coordinate. 

<a name="geodesic-path"></a>**Percorso geodetico**: il percorso più breve tra due punti su una superficie curva. Quando ne viene eseguito il rendering in Mappe di Azure, questo percorso viene visualizzato come una linea curva per effetto della proiezione di Mercatore.

<a name="geofence"></a>**Geofence**: area geografica definita che può essere usata per attivare eventi quando un dispositivo immette o esiste nell'area.

<a name="geojson"></a>**GeoJSON**: è un formato di file basato su JSON comune usato per archiviare dati di vettori geografici, ad esempio punti, linee e poligoni. **Nota**: in Mappe di Azure viene usata una versione estesa di GeoJSON, come [illustrato in questo articolo](extend-geojson.md).

<a name="geometry"></a>**Geometry**: rappresenta un oggetto spaziale, ad esempio un punto, una linea o un poligono.

<a name="geometrycollection"></a>**GeometryCollection**: raccolta di oggetti Geometry.

<a name="geopol"></a>**GeoPol**: si riferisce ai dati sensibili geopoliticamente, ad esempio i bordi controversi e i nomi delle località.

<a name="georeference"></a>**Georeference**: processo di allineamento di dati geografici o immagini a un sistema di coordinate noto. Questo processo può includere attività di spostamento, rotazione, ridimensionamento o inclinazione.

<a name="georss"></a>**GeoRSS**: estensione XML per l'aggiunta di dati spaziali ai feed RSS.

<a name="gis"></a>**GIS**: acronimo di "Geographic Information System". Termine comune usato per descrivere il settore della cartografia computerizzata.

<a name="gml"></a>**GML**: noto anche come Geography Markup Language. Estensione di file XML per l'archiviazione di dati spaziali.

<a name="gps"></a>**GPS**: anche noto come Global Positioning System, è un sistema di satelliti usati per determinare la posizione dei dispositivi sulla terra. I satelliti orbitanti trasmettono segnali che consentono a un ricevitore GPS in un punto qualsiasi sulla superficie terrestre di calcolare la propria posizione mediante una tecnica di trilaterazione.

<a name="gpx"></a>**GPX**: noto anche come formato di scambio GPS, è un formato di file XML creato comunemente da dispositivi GPS.  

<a name="great-circle-distance"></a>**Distanza dal cerchio**: la distanza più breve tra due punti sulla superficie di una sfera.

<a name="greenwich-mean-time-gmt"></a>**Ora di Greenwich (GMT)** : l'ora del meridiano principale, che viene eseguita attraverso l'Osservatorio reale a Greenwich, Inghilterra.

<a name="guid"></a>**GUID**: identificatore univoco globale. Stringa usata per identificare in modo univoco un'interfaccia, una classe, una libreria di tipi, una categoria di componenti o un record.

## <a name="h"></a>H

<a name="haversine-formula"></a>**Formula haversine**: equazione comune utilizzata per calcolare la distanza del cerchio tra due punti su una sfera.

<a name="hd-maps"></a>**Mappe HD**: note anche come mappe ad alta definizione, sono costituite da informazioni sulle reti stradali ad alta fedeltà, ad esempio contrassegni di corsia, segnaletica e luci di direzione necessarie per la guida autonoma.

<a name="heading"></a>**Intestazione**: direzione del puntatore o della direzione di un elemento. Vedere anche [Orientamento](#heading).

<a name="heatmap"></a>**Mappa termica**: visualizzazione dei dati in cui un intervallo di colori rappresenta la densità dei punti in una determinata area. Vedere anche Mappa tematica.

<a name="hybrid-imagery"></a>**Immagini ibride**: immagini satellite o aeree che hanno dati stradali e etichette sovrapposte.

## <a name="i"></a>I

<a name="iana"></a>**IANA**: acronimo di Internet Assigned Numbers Authority. indica un gruppo no profit che controlla l'allocazione di indirizzi IP a livello globale.

<a name="isochrone"></a>**Isochrone**: un isochrone definisce l'area in cui un utente può spostarsi entro il tempo specificato per la modalità di trasporto in qualsiasi direzione da una determinata posizione. Vedere anche [Area di raggiungibilità](#reachable-range).

<a name="isodistance"></a>**Isodistance**: data una posizione, un isochrone definisce l'area in cui un utente può spostarsi entro una determinata distanza per una modalità di trasporto in qualsiasi direzione. Vedere anche [Area di raggiungibilità](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a>**KML**: noto anche come linguaggio di markup della serratura, è un formato di file XML comune per archiviare dati di vettori geografici, ad esempio punti, linee e poligoni. 

## <a name="l"></a>L

<a name="landsat"></a>**Landsat**: satellite multispettrale con orbita terrestre sviluppato dalla NASA che raccoglie immagini di terra usate in molti settori, ad esempio agricoltura, silvicoltura e cartografia.

<a name="latitude"></a>**Latitudine**: distanza angolare misurata in gradi dall'equatore nella direzione nord o sud.

<a name="level-of-detail"></a>**Livello di dettaglio**: vedere livello di zoom.

<a name="lidar"></a>**LIDAR**: acronimo di Light Detection e ranging. Tecnica di telerilevamento che usa impulsi laser per misurare le distanze da superfici riflettenti.

<a name="linear-interpolation"></a>**Interpolazione lineare**: la stima di un valore sconosciuto usando la distanza lineare tra i valori noti.

<a name="linestring"></a>**LineString**: geometria utilizzata per rappresentare una riga. Nota anche come polilinea. 

<a name="localization"></a>**Localizzazione**: supporto per lingue e impostazioni cultura diverse.

<a name="logistics"></a>**Logistica**: il processo di trasferimento di persone, veicoli, forniture o asset in modo coordinato.

<a name="longitude"></a>**Longitudine**: distanza angolare misurata in gradi dal meridiano principale in una direzione est o ovest.

## <a name="m"></a>M

<a name="map-tile"></a>**Riquadro Mappa**: immagine rettangolare che rappresenta una partizione di un'area di disegno della mappa. Per altre informazioni, vedere l'articolo [Livelli di zoom e griglia riquadri](zoom-levels-and-tile-grid.md).

<a name="marker"></a>**Marker**: nota anche come pin o puntina da disegno, è un'icona che rappresenta una posizione punto su una mappa.

<a name="mercator-projection"></a>**Proiezione Mercator**: proiezione di mappa cilindrica che è diventata la proiezione della mappa standard per scopi nautici grazie alla possibilità di rappresentare linee di corsi costanti, note come linee lossodromica, come segmenti dritti che conservano gli angoli con i meridiani. Tutte le proiezioni cartografiche piane hanno l'effetto di distorcere le forme o le dimensioni della mappa rispetto alla reale forma della superficie terrestre. La proiezione di Mercatore esagera le aree lontane dall'equatore e pertanto le aree più piccole appaiano di dimensioni maggiori sulla mappa quanto più ci si avvicina ai poli. 

<a name="multilinestring"></a>**MultiLineString**: geometria che rappresenta una raccolta di oggetti LineString. 

<a name="multipoint"></a>**Multipoint**: geometria che rappresenta una raccolta di oggetti punto.

<a name="multipolygon"></a>**MultiPolygon**: geometria che rappresenta una raccolta di oggetti Polygon. Ad esempio, per mostrare i confini delle Hawaii, la costa di ogni isola sarebbe delimitata da un poligono e i confini delle Hawaii sarebbero pertanto rappresentati da un multipoligono.

<a name="municipality"></a>**Municipalità**: città o città. 

<a name="municipality-subdivision"></a>**Suddivisione del comune**: una suddivisione di un comune, ad esempio un quartiere o un nome di area locale, ad esempio "Downtown".

## <a name="n"></a>N

<a name="navigation-bar"></a>**Barra di spostamento**: il set di controlli in una mappa utilizzata per regolare il livello di zoom, il pitch, la rotazione e il cambio del livello della mappa di base.

<a name="nearby-search"></a>**Ricerca nelle vicinanze**: una query spaziale che esegue la ricerca di una distanza di linea retta fissa (come il corvo vola) da un punto.

<a name="neutral-ground-truth"></a>**Neutral Ground Truth**: mappa che esegue il rendering delle etichette nella lingua ufficiale dell'area che rappresenta e negli script locali, se disponibili.

## <a name="o"></a>O

<a name="origin"></a>**Origin**: punto iniziale o percorso in cui si trova un utente.

## <a name="p"></a>P

<a name="panning"></a>**Panoramica**: processo di spostamento della mappa in qualsiasi direzione mantenendo un livello di zoom costante.

<a name="parcel"></a>**Parcel**: tracciato del limite della proprietà o della superficie.

<a name="pitch"></a>**Pitch**: la quantità di inclinazione della mappa rispetto alla verticale, dove 0 sta cercando verso il basso la mappa.

<a name="point"></a>**Point**: geometria che rappresenta una singola posizione sulla mappa. 

<a name="points-of-interest-poi"></a>**Punti di interesse (PDI)** : un'azienda, un punto di riferimento o un luogo comune di interesse.

<a name="polygon"></a>**Polygon**: geometria solida che rappresenta un'area su una mappa. 

<a name="polyline"></a>**Polilinea**: geometria utilizzata per rappresentare una linea. Nota anche come linea spezzata. 

<a name="position"></a>**Position**: Longitudine, latitudine e altitudine (coordinate x, y, z) di un punto.

<a name="post-code"></a>**Post-codice**: vedere [codice postale](#postal-code).

<a name="postal-code"></a>**Cap: una**serie di lettere o numeri, o entrambi, in un formato specifico, usato dal servizio postale di un paese/area geografica per dividere le aree geografiche in zone per semplificare la consegna della posta.

<a name="primary-key"></a>**Chiave primaria**: la prima delle due chiavi di sottoscrizione fornite per l'autenticazione con chiave condivisa di Azure maps. Vedere [autenticazione con chiave condivisa](#shared-key-authentication).

<a name="prime-meridian"></a>**Meridiano principale**: una linea di longitudine che rappresenta la longitudine di 0 gradi. I valori di longitudine diminuiscono quando ci si sposta fino a 180 gradi verso Ovest e aumentano quando ci si sposta fino a -180 gradi verso Est. 

<a name="prj"></a>**PRJ**: un file di testo che spesso accompagna un file di forma contenente informazioni sul sistema di coordinate proiettato in cui si trova il set di dati.

<a name="projection"></a>**Proiezione**: un sistema di coordinate proiettato basato su una proiezione della mappa, ad esempio trasversal Mercator, Albe uguali e Robinson. In questo modo, le mappe della superficie sferica della Terra possono essere proiettate su un piano di coordinate cartesiane bidimensionale. I sistemi di coordinate proiettati sono talvolta definiti proiezioni cartografiche.

## <a name="q"></a>Q

<a name="quadkey"></a>**Quadkey**: Indice di indirizzi in base 4 per un riquadro in un sistema di affiancamento quadtree. Per altre informazioni, vedere l'articolo [Livelli di zoom e griglia riquadri](zoom-levels-and-tile-grid.md).

<a name="quadtree"></a>**Quadtree**: una struttura di dati in cui ogni nodo dispone esattamente di quattro elementi figlio. Il sistema di partizionamento adottato in Mappe di Azure è basato su una struttura di tipo quadtree. In questo modo, quando un utente esegue lo zoom avanti di un livello, ogni tessera della mappa viene suddivisa in quattro tessere secondarie.  Per altre informazioni, vedere l'articolo [Livelli di zoom e griglia riquadri](zoom-levels-and-tile-grid.md).

<a name="queries-per-second-qps"></a>**Query al secondo (query al secondo)** : numero di query o richieste che possono essere effettuate a un servizio o a una piattaforma entro un secondo. 

## <a name="r"></a>R

<a name="radial-search"></a>**Ricerca radiale**: una query spaziale che esegue la ricerca di una distanza di linea retta fissa (come il corvo vola) da un punto. 

<a name="raster-data"></a>**Dati raster**: una matrice di celle (o pixel) organizzata in righe e colonne (o in una griglia) in cui ogni cella contiene un valore che rappresenta le informazioni, ad esempio la temperatura. Le fotografie aeree digitali, le immagini satellitari, le foto digitali e le mappe digitalizzate sono tutte costituite da dati raster.

<a name="raster-layer"></a>**Livello raster**: un livello sezione costituito da immagini raster.

<a name="reachable-range"></a>**Intervallo raggiungibile**: un intervallo raggiungibile definisce l'area in cui un utente può spostarsi entro una determinata ora o distanza, per la modalità di trasporto, in qualsiasi direzione da una località. Vedere anche [Isocrona](#isochrone) e [Isodistanza](#isodistance).

<a name="remote-sensing"></a>**Rilevamento remoto**: processo di raccolta e interpretazione dei dati dei sensori da una distanza.

<a name="rest-service"></a>**REST Service (servizio REST**): l'acronimo Rest sta per il trasferimento di stato di rappresentazione. Un servizio REST è un servizio Web basato su URL che adotta la tecnologia Web di base per comunicare. I metodi più comuni del servizio sono costituiti da richieste GET e POST HTTP. I servizi di questo tipo tendono a essere molto più veloci e compatti rispetto ai servizi tradizionali basati su SOAP.

<a name="reverse-geocode"></a>**Geocodifica inversa**: il processo di acquisizione di una coordinata e di determinazione dell'indirizzo in cui è rappresentata su una mappa.

<a name="reproject"></a>**Riprogetto**: vedere [trasformazione](#transformation).

<a name="rest-service"></a>**REST Service**: acronimo di Representational State Transfer. Architettura per lo scambio di informazioni tra peer in un ambiente distribuito decentralizzato. REST consente a programmi in computer diversi di comunicare indipendentemente dal sistema operativo o dalla piattaforma inviando a un URL (Uniform Resource Locator) una richiesta HTTP (Hypertext Transfer Protocol) per ottenere determinati dati.

<a name="route"></a>**Route**: un percorso tra due o più posizioni, che può includere anche informazioni aggiuntive, ad esempio istruzioni per i waypoint lungo la route.

<a name="requests-per-second-rps"></a>**Richieste al secondo (RPS)** : vedere le [query al secondo (query al secondo)](#queries-per-second-qps). 

<a name="rss"></a>**RSS**: acronimo di syndication molto semplice, riepilogo del sito di Resource Description Framework (RDF) o riepilogo completo del sito, a seconda dell'origine. RSS è un formato XML semplice e strutturato per la condivisione di contenuto tra diversi siti Web. I documenti RSS includono elementi di metadati significativi, ad esempio l'autore, la data, il titolo, una breve descrizione e un collegamento ipertestuale. Queste informazioni consentono a un utente (o a un servizio di pubblicazione RSS) di identificare quali contenuti valga la pena di approfondire.

## <a name="s"></a>S

<a name="satellite-imagery"></a>**Immagini satellite**: immagini acquisite da piani e satelliti che puntano verso il basso.

<a name="secondary-key"></a>**Chiave secondaria**: la seconda di due chiavi di sottoscrizione fornite per l'autenticazione con chiave condivisa di Azure maps. Vedere [autenticazione con chiave condivisa](#shared-key-authentication).

<a name="shapefile-shp"></a>File di forma **(SHP)** : noto anche come file di forma ESRI, è un formato di archiviazione di dati vettoriali per archiviare il percorso, la forma e gli attributi delle funzionalità geografiche. Un file di forma viene archiviato in un set di file correlati.

<a name="shared-key-authentication"></a>**Autenticazione con chiave condivisa**: l'autenticazione con chiave condivisa si basa sul passaggio delle chiavi generate dall'account Azure Maps a ogni richiesta ad Azure maps. Queste chiavi sono spesso denominate chiavi di sottoscrizione. È consigliabile rigenerare periodicamente le chiavi per la sicurezza. Vengono fornite due chiavi in modo che sia possibile mantenere le connessioni usando una chiave durante la rigenerazione dell'altra. Quando si rigenerano le chiavi è necessario aggiornare tutte le applicazioni che accedono a questo account per usare le nuove chiavi. Per altre informazioni sull'autenticazione di Azure Maps, vedere [mappe di Azure e Azure ad](azure-maps-authentication.md) e [gestire l'autenticazione in mappe di Azure](how-to-manage-authentication.md).

<a name="software-development-kit-sdk"></a>**Software Development Kit (SDK)** : raccolta di documentazione, codice di esempio e app di esempio per aiutare gli sviluppatori a usare un'API per creare app.

<a name="spherical-mercator-projection"></a>**Proiezione Mercator sferica**: vedere [Web Mercator](#web-mercator). 

<a name="spatial-query"></a>**Query spaziale**: richiesta effettuata a un servizio che esegue un'operazione spaziale. Ad esempio, una ricerca radiale o una ricerca lungo un percorso.

<a name="spatial-reference"></a>**Riferimento spaziale**: sistema locale, regionale o globale basato su coordinate usato per individuare le entità geografiche in modo preciso. Definisce il sistema di coordinate usato per stabilire una correlazione tra le coordinate della mappa e le località nel mondo reale. I riferimenti spaziali garantiscono che i dati spaziali provenienti da diversi layer o origini possano essere integrati in modo da essere visualizzati o analizzati correttamente. Mappe di Azure usa il sistema di coordinate di riferimento [EPSG:3857](https://epsg.io/3857) e WGS84 per l'input dei dati geometrici. 

<a name="sql-spatial"></a>**SQL Spatial**: si riferisce alla funzionalità spaziale incorporata in SQL Azure e SQL Server 2008 e versioni successive. Queste funzionalità spaziali sono disponibili anche come libreria .NET utilizzabile indipendentemente da SQL Server. Per altre informazioni, vedere la [documentazione relativa ai dati spaziali di SQL Server](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server).

<a name="subscription-key"></a>**Chiave di sottoscrizione**: vedere [autenticazione con chiave condivisa](#shared-key-authentication).

<a name="synchronous-request"></a>**Richiesta sincrona**: una richiesta http apre una connessione e attende una risposta. I browser limitano il numero di richieste HTTP simultanee che possono essere inviate da una pagina. Se più richieste sincrone a esecuzione prolungata vengono inviate contemporaneamente, è possibile che questo limite venga raggiunto e che le richieste vengano ritardate finché non sono state completate altre richieste.

## <a name="t"></a>T

<a name="telematics"></a>**Telematica**: invio, ricezione e archiviazione di informazioni tramite dispositivi di telecomunicazione insieme all'effetto del controllo sugli oggetti remoti. 

<a name="temporal-data"></a>**Dati temporali**: dati che si riferiscono in modo specifico a orari o date. I dati temporali possono fare riferimento a eventi discreti, come la caduta di fulmini, a oggetti in movimento, come i treni, oppure a osservazioni ripetute, come i valori rilevati dai sensori del traffico.

<a name="terrain"></a>**Terrain**: area di terra avente una particolare caratteristica, ad esempio un terreno sabbioso o un terreno montagnoso.

<a name="thematic-maps"></a>**Mappe tematiche**: una mappa tematica è una semplice mappa fatta per riflettere un tema su un'area geografica. Uno scenario comune per questo tipo di mappa consiste nel colorare le aree amministrative quali paesi/aree geografiche in base a una metrica di dati.

<a name="tile-layer"></a>**Livello sezione**: un livello visualizzato mediante l'assemblaggio di riquadri mappa (sezioni rettangolari) in un livello continuo. Le tessere possono essere di tipo raster o vettoriale. I layer a tessere raster vengono in genere sottoposti a rendering in anticipo e archiviati come immagini in un server. Ciò può richiedere una notevole quantità di spazio di archiviazione. Il rendering dei livelli delle tessere vettoriali viene eseguito all'interno dell'applicazione client, quindi i requisiti di archiviazione sul lato server sono inferiori.

<a name="time-zone"></a>**Fuso orario**: area del globo che osserva un'ora solare uniforme per finalità legali, commerciali e sociali. I fusi orari tendono a seguire i limiti di paesi/aree geografiche e le relative suddivisioni.

<a name="transaction"></a>**Transazione**: Azure Maps usa un modello di licenza transazionale in cui;

- Viene creata una transazione per ogni 15 tessere di mappa o traffico richieste.
- Viene creata una transazione per ogni chiamata API a uno dei servizi di Mappe di Azure, ad esempio ricerca o calcolo di un percorso.

<a name="transformation"></a>**Transformation**: processo di conversione dei dati tra diversi sistemi di coordinate geografiche. È ad esempio possibile che alcuni dati siano stati acquisiti nel Regno Unito in base al sistema di coordinate geografiche OSGB 1936. Mappe di Azure usa il sistema di coordinate di riferimento [EPSG:3857](https://epsg.io/3857), una variante di WGS84. Di conseguenza, per la visualizzazione corretta dei dati, il sistema di coordinate deve essere trasformato da un sistema all'altro.

<a name="traveling-salesmen-problem-tsp"></a>**Travel venditoris problem (TSP)** : un problema di circuito hamiltoniana in cui un venditore deve trovare il modo più efficiente per visitare una serie di interruzioni e quindi tornare alla posizione iniziale.  

<a name="trilateration"></a>**Trilatenza**: processo di determinazione della posizione di un punto sulla superficie della terra rispetto ad altri due punti, misurando le distanze tra i tre punti.

<a name="turn-by-turn-navigation"></a>**Spostamento turn-by-turn**: un'applicazione che fornisce istruzioni di route per ogni passaggio di una route mentre gli utenti si avvicinano alla manovra successiva.

## <a name="v"></a>V

<a name="vector-data"></a>**Dati vettoriali**: dati basati su coordinate rappresentati come punti, linee o poligoni.

<a name="vector-tile"></a>**Riquadro vettoriale**: specifica Open Data per l'archiviazione di dati di vettori geospaziali usando lo stesso sistema di sezioni del controllo mappa. Vedere anche [Layer a tessere](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a>**Problema di routing del veicolo (VRP)** : una classe di problemi in cui un set di route ordinate per una flotta di veicoli viene calcolato tenendo conto come set di vincoli. ad esempio intervalli di recapito, capacità di effettuare più percorsi e durata del viaggio.

<a name="voronoi-diagram"></a>**Diagramma Voronoi**: partizione di spazio in aree, o celle, che racchiudono un set di oggetti geometrici (in genere funzionalità di punto). Queste celle, o poligoni, devono soddisfare i criteri definiti per i triangoli di Delaunay. Tutte le posizioni all'interno di un'area sono più vicine all'oggetto che circonda rispetto a qualsiasi altro oggetto nel set. I diagrammi di Voronoi vengono spesso usati per delineare le aree di influenza attorno alle feature geografiche. 

## <a name="w"></a>W

<a name="waypoint"></a>**Waypoint**: un waypoint è una posizione geografica specificata definita dalla longitudine e dalla latitudine utilizzate a scopo di navigazione. Un punto di tragitto viene spesso usato per rappresentare una tappa intermedia di un percorso.

<a name="waypoint-optimization"></a>**Ottimizzazione del waypoint**: processo di riordinamento di un set di waypoint per ridurre al minimo il tempo di viaggio o la distanza necessaria per passare tutti i waypoint specificati. Questo processo è spesso definito [TSP (Traveling Salesmen Problem)](#traveling-salesmen-problem-tsp) o [VRP (Vehicle Routing Problem)](#vehicle-routing-problem-vrp), a seconda della complessità dell'ottimizzazione.

<a name="web-map-service-wms"></a>**Web Map Service (WMS)** : WMS è uno standard OGC (Open Geographic Consortium) che definisce i servizi mappa basati su immagini. I servizi WMS forniscono su richiesta le immagini relative ad aree specifiche all'interno di una mappa. Le immagini includono simbologia precedentemente sottoposta a rendering e possono essere generate in base a uno dei diversi stili eventualmente definiti dal servizio.

<a name="web-mercator"></a>**Web Mercator**: la proiezione sferica Mercator è una lieve variante della proiezione Mercator, una utilizzata principalmente nei programmi di mapping basati sul Web. Usa le stesse formule della proiezione standard di Mercatore adottate per le mappe con fattore di scala ridotto. La proiezione Web Mercator, tuttavia, usa il modello sferico per qualsiasi fattore di scala, mentre le mappe di Mercatore con fattore di scala elevato usano in genere il modello ellissoidale della proiezione. La differenza è impercettibile su scala globale, ma causa una leggera deviazione delle mappe delle aree locali rispetto alle vere mappe di Mercatore ellissoidali con lo stesso fattore di scala.

<a name="wgs84"></a>**WGS84**: set di costanti utilizzato per correlare le coordinate spaziali con le posizioni sulla superficie della mappa. Il datum WGS84 è quello standard usato dalla maggior parte dei dispositivi GPS e provider di mappe online. Mappe di Azure usa il sistema di coordinate di riferimento [EPSG:3857](https://epsg.io/3857), una variante di WGS84.

## <a name="z"></a>Z

<a name="z-coordinate"></a>**Coordinata Z**: vedere [Altitude](#altitude). 

<a name="zip-code"></a>**Cap: vedere**Cap [.](#postal-code)

<a name="Zoom level"></a>**Livello di zoom**: specifica il livello di dettaglio e la quantità della mappa visibile. Quando si esegue lo zoom fino a un livello 0, l'intera mappa del mondo sarà spesso visualizzata, ma visualizzerà dettagli limitati, ad esempio nomi di paese/area geografica e bordi, oltre a nomi di oceano. Se si esegue lo zoom avanti fino a giungere in prossimità del livello 17, la mappa mostrerà l'area corrispondente ad alcuni isolati di una città con informazioni stradali dettagliate. Per altre informazioni, vedere l'articolo [Livelli di zoom e griglia riquadri](zoom-levels-and-tile-grid.md).


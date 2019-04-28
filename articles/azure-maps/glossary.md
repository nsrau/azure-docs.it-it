---
title: Glossario di Mappe di Azure | Microsoft Docs
description: Glossario dei termini di uso comune associati a Mappe di Azure, Servizi Location Based e GIS.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 11e49d362508e9720cf65fc20e020930b5f5c3ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769839"
---
# <a name="glossary"></a>Glossario

Di seguito è riportato un elenco dei termini comuni usati con Mappe di Azure.

## <a name="a"></a>Una 

<a name="address-validation"></a> **Convalida di indirizzo**: processo di verifica dell'esistenza di un indirizzo.

<a name="advanced-routing"></a> **Calcolo dei percorsi avanzato**: raccolta di servizi che eseguono operazioni avanzate usando i dati dei percorsi stradali, ad esempio il calcolo delle aree di raggiungibilità (isocrone), delle matrici di distanze e delle richieste di percorso in batch.

<a name="aerial-imagery"></a> **Immagini aeree**: vedere [Immagini satellitari](#satellite-imagery). 

<a name="along-a-route-search"></a> **Lungo un percorso, ricerca**: query spaziale che esegue la ricerca di dati entro una distanza o un tempo specificato per la deviazione da un percorso.

<a name="altitude"></a> **Altitudine**: altezza o elevazione verticale di un punto al di sopra di una superficie di riferimento. Le misure dell'altitudine sono basate su un datum di riferimento, ad esempio il livello medio del mare. Vedere anche Quota.

<a name="ambiguous"></a> **Ambiguo**: stato di incertezza in merito alla classificazione dei dati che è presente quando a un oggetto possono essere assegnati due o più valori per un determinato attributo. Ad esempio, quando si esegue la geocodifica di "CA", vengono restituiti due risultati ambigui, "Canada" e "California", poiché "CA" è un codice che si riferisce rispettivamente a un paese e a uno Stato. 

<a name="annotation"></a> **Annotazione**: testo o elemento grafico visualizzato sulla mappa per fornire informazioni all'utente. L'annotazione può identificare o descrivere un'entità specifica sulla mappa, fornire informazioni generali relative a un'area della mappa o mostrare informazioni sulla mappa stessa.

<a name="antimeridian"></a> **Antimeridiano**: noto anche come 180<sup>o</sup> meridiano, rappresenta il punto di incontro tra -180 gradi e 180 gradi di longitudine. È opposto al meridiano zero sul globo.

<a name="application-programming-interface-api"></a> **API (Application Programming Interface)**: specifica che consente agli sviluppatori di creare applicazioni.

<a name="api-key"></a> **Chiave API**: vedere Chiave di Mappe di Azure.

<a name="area-of-interest-aoi"></a> **Area di interesse**: estensione usata per definire un'area di interesse per la creazione di una mappa o di un database.

<a name="asset-tracking"></a> **Rilevamento di asset**: processo di rilevamento della posizione di un asset, ad esempio una persona, un veicolo o un altro oggetto.

<a name="asynchronous-request"></a> **Richiesta asincrona**: richiesta HTTP che apre una connessione e invia una richiesta al server, il quale restituisce un identificatore per tale richiesta e quindi chiude la connessione. Il server continua a elaborare la richiesta e l'utente può controllare lo stato usando l'identificatore. Al termine dell'elaborazione della richiesta, l'utente può scaricare la risposta. Questo tipo di richiesta viene in genere usato per i processi a esecuzione prolungata.

<a name="autocomplete"></a> **Completamento automatico**: funzionalità di un'applicazione in grado di prevedere la parte restante di una parola che l'utente sta digitando. 

<a name="autosuggest"></a> **Suggerimenti automatici**: funzionalità di un'applicazione in grado di prevedere il testo logicamente possibile per ciò che l'utente sta digitando.

<a name="azure-location-based-services-lbs"></a> **Servizi Location Based di Azure**: nome precedente di Mappe di Azure, usato durante la fase di anteprima.

<a name="azure-maps-key"></a> **Chiave di Mappe di Azure**: stringa univoca usata per autenticare una richiesta di servizio o applicazione di Mappe di Azure da parte di un utente. 

## <a name="b"></a>b

<a name="base-map"></a> **Mappa di base**: parte di un'applicazione per le mappe che visualizza le informazioni di riferimento sullo sfondo, ad esempio strade, punti di riferimento e confini politici.

<a name="batch-request"></a> **Richiesta batch**: combinazione di più richieste in un'unica richiesta.

<a name="bearing"></a> **Orientamento**: direzione orizzontale di un punto in relazione a un altro punto. Questo valore viene espresso come un angolo rispetto al Nord, da 0 a 360 gradi in senso orario. 

<a name="boundary"></a> **Confine**: linea o poligono che separa entità politiche adiacenti, ad esempio paesi, territori e aree demaniali. Un confine è costituito da una linea che può seguire o meno le caratteristiche fisiche, come fiumi, montagne o muri.

<a name="bounds"></a> **Limiti**: vedere [Rettangolo delimitatore](#bounding-box).

<a name="bounding-box"></a> **Rettangolo delimitatore**: set di coordinate usato per rappresentare un'area rettangolare sulla mappa. 

## <a name="c"></a>C

<a name="cadastre"></a> **Catasto**: registro dei terreni e dei beni immobili. Vedere anche [Lotto](#parcel).

<a name="camera"></a> **Fotocamera**: nel contesto di un controllo mappa interattivo, definisce il campo di visualizzazione della mappa. Il riquadro di visualizzazione della fotocamera è determinato in base a diversi parametri della mappa, ovvero il centro, il livello di zoom, l'inclinazione e l'orientamento. 

<a name="centroid"></a> **Centroide**: centro geometrico di un elemento. Il centroide di una linea è il relativo punto medio, mentre il centroide di un poligono è il centro della relativa area.

<a name="choropleth-map"></a> **Mappa coropletica**: mappa tematica in cui le aree sono ombreggiate proporzionalmente a una misura di una variabile statistica visualizzata sulla mappa. Ad esempio, una mappa in cui i confini di ogni Stato degli Stati Uniti sono colorati in base alla popolazione rispetto a tutti gli altri Stati.

<a name="concave-hull"></a> **Inviluppo concavo**: forma che rappresenta un possibile oggetto geometrico concavo in cui sono racchiuse tutte le forme del set di dati specificato. La forma generata è simile al risultato che si otterrebbe impacchettando i dati in un sacchetto di plastica e riscaldandolo. In questo modo, infatti, si produrrebbero ampi spazi tra i diversi punti dati.

<a name="consumption-model"></a> **Modello di consumo**: informazioni che definiscono il consumo di carburante o elettricità per un veicolo. Vedere anche la [documentazione relativa al modello di consumo](consumption-model.md).

<a name="control"></a> **Controllo**: componente autonomo o riutilizzabile costituito da un'interfaccia utente grafica che definisce un set di comportamenti per l'interfaccia. Un controllo mappa, ad esempio, è in genere la parte dell'interfaccia utente che carica una mappa interattiva.

<a name="convex-hull"></a> **Inviluppo convesso**: forma che rappresenta l'oggetto geometrico convesso minimo in cui sono racchiuse tutte le forme del set di dati specificato. La forma generata è simile al risultato che si otterrebbe avvolgendo in un elastico tutto il set di dati.

<a name="coordinate"></a> **Coordinate**: valori di longitudine e latitudine usati per rappresentare una posizione su una mappa.

<a name="coordinate-system"></a> **Sistema di coordinate**: framework di riferimento usato per definire le posizioni dei punti nello spazio in due o tre dimensioni.

<a name="country-code"></a> **Codice paese**: identificatore univoco per un paese basato sullo standard ISO. ISO2 è un codice di due caratteri per un paese (ad esempio, US), mentre ISO3 è un codice di tre caratteri (ad esempio, USA).

<a name="country-subdivision"></a> **Suddivisione di paese**: area di primo livello in cui è suddiviso un paese, comunemente definita regione o Stato.

<a name="country-secondary-subdivision"></a> **Suddivisione di paese di secondo livello**: area di secondo livello in cui è suddiviso un paese, comunemente definita provincia o contea.

<a name="country-tertiary-subdivision"></a> **Suddivisione di paese di terzo livello**: area di terzo livello in cui è suddiviso un paese. A questa area è in genere assegnato un nome, ad esempio distretto.

<a name="cross-street"></a> **Incrocio**: punto in cui si intersecano due o più strade.

<a name="cylindrical-projection"></a> **Proiezione cilindrica**: proiezione che trasforma i punti di uno sferoide o di una sfera in un cilindro tangente o secante. Il cilindro viene quindi sezionato dall'alto verso il basso e trasferito su un piano.

## <a name="d"></a>D

<a name="datum"></a> **Datum**: specifiche di riferimento di un sistema di misura, posizioni di un sistema di coordinate su una superficie (datum orizzontale) o altezze al di sopra o al di sotto di una superficie (datum verticale).

<a name="dbf-file"></a> **DBF**: formato di file di database che viene usato in combinazione con i file di forma (SHP).

<a name="degree-minutes-seconds-dms"></a> **DMS (Degree Minutes Seconds)**: unità di misura, espressa in grado minuti secondi, per la descrizione di latitudine e longitudine. Un grado è 1/360<sup>o</sup> di un cerchio. Il grado viene ulteriormente suddiviso in 60 minuti e un minuto in 60 secondi.

<a name="delaunay-triangulation"></a> **Triangolazione di Delaunay**: tecnica per la creazione di una mesh di triangoli contigui non sovrapposti da un set di dati di punti. Nessun punto appartenente al set di dati è presente all'interno del circumcerchio di ogni triangolo.

<a name="demographics"></a> **Dati demografici**: caratteristiche statistiche (ad esempio età, tasso di natalità e reddito) relative a un gruppo di persone.

<a name="destination"></a> **Destinazione**: punto o località finale del percorso di un utente.

<a name="digital-elevation-model-dem"></a> **DEM (Digital Elevation Model)**: set di dati dei valori di quota correlati a una superficie, acquisiti su un'area a intervalli regolari in base a un datum comune. I DEM vengono in genere usati per rappresentare il rilievo del terreno.

<a name="dijkstra's-algorithm"></a> **Algoritmo di Dijkstra**: algoritmo che esamina la connettività di una rete per trovare il percorso più breve tra due punti.

<a name="distance-matrix"></a> **Matrice di distanze**: matrice che contiene informazioni sulle distanze e i tempi di percorrenza tra un set di origini e destinazioni. 

## <a name="e"></a>E

<a name="elevation"></a> **Quota**: distanza verticale di un punto o di un oggetto al di sopra o al di sotto di un datum o di una superficie di riferimento (in genere, il livello medio del mare). Il termine di riferisce in genere all'altitudine del terreno.

<a name="envelope"></a> **Busta**: vedere [Rettangolo delimitatore](#bounding-box).

<a name="extended-postal-code"></a> **Codice postale esteso**: codice postale che può includere informazioni aggiuntive. Negli Stati Uniti, ad esempio, i codici postali sono composti da cinque cifre, ma esistono anche codici postali estesi che includono altre quattro cifre. Queste ultime vengono usate per identificare un segmento geografico all'interno dell'area di recapito contrassegnata da cinque cifre, ad esempio un isolato, un condominio o una cassetta postale, in modo da rendere più efficiente l'ordinamento e il recapito della posta.

<a name="extent"></a> **Estensione**: vedere [Rettangolo delimitatore](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a> **Autenticazione federata**: metodo che consente l'uso di un meccanismo di autenticazione o accesso singolo in più app Web e per dispositivi mobili. 

<a name="feature"></a> **Feature**: oggetto che combina una geometria con metadati aggiuntivi. 

<a name="feature-collection"></a> **Raccolta di feature**: raccolta di oggetti di tipo feature.

<a name="find-along-route"></a> **Ricerca lungo un percorso**: query spaziale che esegue la ricerca di dati entro una distanza o un tempo specificato per la deviazione da un percorso.

<a name="find-nearby"></a> **Ricerca nelle vicinanze**: query spaziale che esegue la ricerca in base a una distanza fissa in linea retta (o in linea d'aria) rispetto a un punto.

<a name="fleet-management"></a> **Gestione della flotta**: gestione del parco di veicoli commerciali, ad esempio autovetture, autocarri, navi e aerei. La gestione della flotta può includere una vasta gamma di funzioni, ad esempio per il finanziamento, la manutenzione e la telematica (rilevamento e diagnostica) dei veicoli e anche per la gestione di guidatori, velocità, carburante, salute e sicurezza. La gestione della flotta è un processo adottato dalle società che svolgono attività commerciali basate sui trasporti per ridurre al minimo i rischi e i costi complessivi del trasporto e del personale e garantire al tempo stesso la conformità alle normative vigenti.

<a name="free-flow-speed"></a> **Velocità con viabilità scorrevole**: velocità prevista in condizioni di traffico ideali. Corrisponde in genere al limite di velocità.

<a name="free-form-address"></a> **Indirizzo in formato libero**: indirizzo completo, rappresentato come una singola riga di testo.

<a name="fuzzy-search"></a> **Ricerca fuzzy**: ricerca che accetta una stringa di testo in formato libero che può corrispondere approssimativamente a un indirizzo o a un punto di interesse. 

## <a name="g"></a>G

<a name="geocode"></a> **Codifica geografica**: indirizzo o posizione che è stata convertita in coordinate per consentirne la visualizzazione su una mappa. 

<a name="geocoding"></a> **Geocodifica**: processo, noto anche come geocodifica diretta, che esegue la conversione dei dati di un indirizzo nelle coordinate corrispondenti. 

<a name="geodesic-path"></a> **Percorso geodetico**: il percorso più breve tra due punti su una superficie curva. Quando ne viene eseguito il rendering in Mappe di Azure, questo percorso viene visualizzato come una linea curva per effetto della proiezione di Mercatore.

<a name="geofence"></a> **Recinto virtuale**: area geografica definita che può essere usata per attivare eventi quando un dispositivo entra o è presente in tale area.

<a name="geojson"></a> **GeoJSON**: formato di file comune basato su JSON che viene usato per archiviare i dati geografici vettoriali, ad esempio punti, linee e poligoni. **Nota**: in Mappe di Azure viene usata una versione estesa di GeoJSON, come [illustrato in questo articolo](extend-geojson.md).

<a name="geometry"></a> **Geometria**: rappresentazione di un oggetto spaziale, ad esempio un punto, una linea o un poligono.

<a name="geometrycollection"></a> **GeometryCollection**: raccolta di oggetti geometrici.

<a name="geopol"></a> **Geopolitica**: fa riferimento a dati geopoliticamente sensibili, ad esempio confini o nomi di luogo contestati.

<a name="georeference"></a> **Georeferenziazione**: processo di allineamento di immagini e dati geografici a un sistema di coordinate noto. Questo processo può includere attività di spostamento, rotazione, ridimensionamento o inclinazione.

<a name="georss"></a> **GeoRSS**: estensione XML per l'aggiunta di dati spaziali ai feed RSS.

<a name="gis"></a> **GIS**: acronimo di Geographic Information System. Termine comune usato per descrivere il settore della cartografia computerizzata.

<a name="gml"></a> **GML**: noto anche come Geography Markup Language. Estensione di file XML per l'archiviazione di dati spaziali.

<a name="gps"></a> **GPS**: noto anche come Global Positioning System. Sistema satellitare usato per determinare la posizione dei dispositivi sulla Terra. I satelliti orbitanti trasmettono segnali che consentono a un ricevitore GPS in un punto qualsiasi sulla superficie terrestre di calcolare la propria posizione mediante una tecnica di trilaterazione.

<a name="gpx"></a> **GPX**: noto anche come formato di scambio GPS, è un formato di file XML comunemente creato dai dispositivi GPS.  

<a name="great-circle-distance"></a> **Distanza ortodromica**: distanza più breve tra due punti sulla superficie di una sfera.

<a name="greenwich-mean-time-gmt"></a> **Ora di Greenwich (GMT)**: ora del meridiano zero che passa attraverso il Royal Observatory di Greenwich, in Inghilterra.

<a name="guid"></a> **GUID**: identificatore univoco globale. Stringa usata per identificare in modo univoco un'interfaccia, una classe, una libreria di tipi, una categoria di componenti o un record.

## <a name="h"></a>H

<a name="haversine-formula"></a> **Formula dell'emisenoverso**: equazione comune usata per calcolare la distanza ortodromica tra due punti su una sfera.

<a name="hd-maps"></a> **Mappe HD**: dette anche mappe ad alta definizione, sono costituite da informazioni ad alta definizione sulla rete stradale, ad esempio indicazioni sulle corsie, la segnaletica e le luci di direzione richieste per la guida autonoma.

<a name="heading"></a> **Rotta**: direzione verso cui è indirizzato un oggetto. Vedere anche [Orientamento](#heading).

<a name="heatmap"></a> **Mappa termica**: visualizzazione dei dati in cui un intervallo di colori rappresenta la densità di punti in una determinata area. Vedere anche Mappa tematica.

<a name="hybrid-imagery"></a> **Immagini ibride**: immagini satellitari o aeree a cui sono sovrapposti i dati e le etichette dei percorsi stradali.

## <a name="i"></a>I

<a name="iana"></a> **IANA**: acronimo di Internet Assigned Numbers Authority, indica un gruppo no profit che controlla l'allocazione di indirizzi IP a livello globale.

<a name="isochrone"></a> **Isocrona**: linea che definisce l'area in cui un utente può spostarsi entro un intervallo di tempo specificato, per una particolare modalità di trasporto, in qualsiasi direzione a partire da una determinata posizione. Vedere anche [Area di raggiungibilità](#reachable-range).

<a name="isodistance"></a> **Isodistanza**: linea che definisce l'area in cui un utente può spostarsi entro una distanza specificata, per una particolare modalità di trasporto, in qualsiasi direzione a partire da una determinata posizione. Vedere anche [Area di raggiungibilità](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a> **KML**: acronimo di Keyhole Markup Language, indica un formato di file XML comune per l'archiviazione di dati geografici vettoriali, ad esempio punti, linee e poligoni. 

## <a name="l"></a>L

<a name="landsat"></a> **Landsat**: satelliti orbitali multispettrali sviluppati dalla NASA per raccogliere immagini della superficie terrestre da usare in vari settori, ad esempio l'agricoltura, la selvicoltura e la cartografia.

<a name="latitude"></a> **Latitudine**: distanza angolare misurata in gradi dall'equatore in direzione Nord o Sud.

<a name="level-of-detail"></a> **Livello di dettaglio**: vedere Livello di zoom.

<a name="lidar"></a> **Lidar**: acronimo di Light Detection and Ranging. Tecnica di telerilevamento che usa impulsi laser per misurare le distanze da superfici riflettenti.

<a name="linear-interpolation"></a> **Interpolazione lineare**: stima di un valore sconosciuto in base alla distanza lineare tra valori noti.

<a name="linestring"></a> **LineString**: geometria usata per rappresentare una linea. Nota anche come polilinea. 

<a name="localization"></a> **Localizzazione**: supporto per lingue e culture diverse.

<a name="logistics"></a> **Logistica**: processo che consiste nello spostamento coordinato di persone, veicoli, forniture o asset.

<a name="longitude"></a> **Longitudine**: distanza angolare misurata in gradi dal meridiano zero verso Est o Ovest.

## <a name="m"></a>M

<a name="map-tile"></a> **Tessera di mappa**: immagine rettangolare che rappresenta una sezione dell'area di una mappa. Per altre informazioni, vedere l'articolo [Livelli di zoom e griglia riquadri](zoom-levels-and-tile-grid.md).

<a name="marker"></a> **Marcatore**: icona che rappresenta la posizione di un punto su una mappa. È indicata anche come puntina da disegno.

<a name="mercator-projection"></a> **Proiezione di Mercatore**: proiezione cartografica cilindrica che è diventata lo standard per le mappe nautiche grazie alla capacità di rappresentare le linee di costante angolo di rotta, note come linee lossodromiche, come segmenti rettilinei che conservano gli angoli con i meridiani. Tutte le proiezioni cartografiche piane hanno l'effetto di distorcere le forme o le dimensioni della mappa rispetto alla reale forma della superficie terrestre. La proiezione di Mercatore esagera le aree lontane dall'equatore e pertanto le aree più piccole appaiano di dimensioni maggiori sulla mappa quanto più ci si avvicina ai poli. 

<a name="multilinestring"></a> **Multilinea**: geometria che rappresenta una raccolta di linee spezzate. 

<a name="multipoint"></a> **Multipunto**: geometria che rappresenta una raccolta di punti.

<a name="multipolygon"></a> **Multipoligono**: geometria che rappresenta una raccolta di poligoni. Ad esempio, per mostrare i confini delle Hawaii, la costa di ogni isola sarebbe delimitata da un poligono e i confini delle Hawaii sarebbero pertanto rappresentati da un multipoligono.

<a name="municipality"></a> **Area urbana**: insieme di edificazioni che forma una città. 

<a name="municipality-subdivision"></a> **Suddivisione di area urbana**: una delle zone in cui è suddivisa un'area urbana, ad esempio il quartiere centrale di una città.

## <a name="n"></a>N

<a name="navigation-bar"></a> **Barra di spostamento**: set di controlli su una mappa che vengono usati per regolare il livello di zoom, l'inclinazione, la rotazione e la commutazione del livello mappa di base.

<a name="nearby-search"></a> **Ricerca nelle vicinanze**: query spaziale che esegue la ricerca in base a una distanza fissa in linea retta (o in linea d'aria) rispetto a un punto.

<a name="neutral-ground-truth"></a> **Mappa con esonimi**: mappa in cui le etichette sono visualizzate nella lingua ufficiale adottata nell'area rappresentata e con lo specifico sistema di caratteri, se disponibile.

## <a name="o"></a>O

<a name="origin"></a> **Origine**: punto o posizione iniziale in cui si trova l'utente.

## <a name="p"></a>P

<a name="panning"></a> **Panoramica**: spostamento della mappa in qualsiasi direzione a un livello di zoom costante.

<a name="parcel"></a> **Lotto**: appezzamento di terreno o confine di una proprietà.

<a name="pitch"></a> **Inclinazione**: livello di inclinazione della mappa rispetto alla direzione verticale, dove 0 corrisponde alla visualizzazione perpendicolare della mappa.

<a name="point"></a> **Punto**: geometria che rappresenta una singola posizione sulla mappa. 

<a name="points-of-interest-poi"></a> **Punto di interesse**: attività commerciale, punto di riferimento o luogo di interesse comune.

<a name="polygon"></a> **Poligono**: geometria chiusa che rappresenta un'area su una mappa. 

<a name="polyline"></a> **Polilinea**: geometria usata per rappresentare una linea. Nota anche come linea spezzata. 

<a name="position"></a> **Posizione**: longitudine, latitudine e altitudine (coordinate x,y,z) di un punto.

<a name="post-code"></a> **CAP**: vedere [Codice postale](#postal-code).

<a name="postal-code"></a> **Codice postale**: serie di lettere e/o numeri in un formato specifico, usata dal servizio postale di un paese per la suddivisione di aree geografiche in zone al fine di semplificare il recapito della posta.

<a name="prime-meridian"></a> **Meridiano zero**: linea longitudinale che rappresenta una longitudine di 0 gradi. I valori di longitudine diminuiscono quando ci si sposta fino a 180 gradi verso Ovest e aumentano quando ci si sposta fino a -180 gradi verso Est. 

<a name="prj"></a> **PRJ**: file di testo spesso correlato a un file di forma che contiene informazioni sul sistema di coordinate proiettato in cui si trova il set di dati.

<a name="projection"></a> **Proiezione**: sistema di coordinate proiettato basato su una proiezione cartografica, ad esempio la proiezione trasversa di Mercatore, la proiezione conica equivalente di Albers e la proiezione di Robinson. In questo modo, le mappe della superficie sferica della Terra possono essere proiettate su un piano di coordinate cartesiane bidimensionale. I sistemi di coordinate proiettati sono talvolta definiti proiezioni cartografiche.

## <a name="q"></a>Q

<a name="quadkey"></a> **Codice quadrante**: indice di indirizzo su base 4 per una tessera nell'ambito di un sistema di partizionamento di tipo quadtree. Per altre informazioni, vedere l'articolo [Livelli di zoom e griglia riquadri](zoom-levels-and-tile-grid.md).

<a name="quadtree"></a> **Quadtree**: struttura di dati in cui ogni nodo è suddiviso esattamente in quattro elementi figlio. Il sistema di partizionamento adottato in Mappe di Azure è basato su una struttura di tipo quadtree. In questo modo, quando un utente esegue lo zoom avanti di un livello, ogni tessera della mappa viene suddivisa in quattro tessere secondarie.  Per altre informazioni, vedere l'articolo [Livelli di zoom e griglia riquadri](zoom-levels-and-tile-grid.md).

<a name="queries-per-second-qps"></a> **Query al secondo (QPS)**: numero di query o richieste che possono essere inviate a un servizio o a una piattaforma nell'arco di un secondo. 

## <a name="r"></a>R

<a name="radial-search"></a> **Ricerca radiale**: query spaziale che esegue la ricerca in base a una distanza fissa in linea retta (o in linea d'aria) rispetto a un punto. 

<a name="raster-data"></a> **Dati raster**: matrice di celle (o pixel) organizzata in righe e colonne (o in una griglia) in cui ogni cella contiene un valore che rappresenta una determinata informazione, ad esempio la temperatura. Le fotografie aeree digitali, le immagini satellitari, le foto digitali e le mappe digitalizzate sono tutte costituite da dati raster.

<a name="raster-layer"></a> **Layer raster**: layer a tessere costituito da immagini raster.

<a name="reachable-range"></a> **Area di raggiungibilità**: area in cui un utente può spostarsi entro una distanza o un intervallo di tempo specificato, per una particolare modalità di trasporto, in qualsiasi direzione a partire da una determinata posizione. Vedere anche [Isocrona](#isochrone) e [Isodistanza](#isodistance).

<a name="remote-sensing"></a> **Rilevamento remoto**: processo di raccolta e interpretazione dei dati dei sensori da una determinata distanza.

<a name="rest-service"></a> **Servizio REST**: REST è l'acronimo di Representational State Transfer (trasferimento di stato rappresentativo). Un servizio REST è un servizio Web basato su URL che adotta la tecnologia Web di base per comunicare. I metodi più comuni del servizio sono costituiti da richieste GET e POST HTTP. I servizi di questo tipo tendono a essere molto più veloci e compatti rispetto ai servizi tradizionali basati su SOAP.

<a name="reverse-geocode"></a> **Geocodifica inversa**: processo di trasformazione di una coordinata nell'indirizzo in cui è rappresentata su una mappa.

<a name="reproject"></a> **Riproiettare**: vedere [Trasformazione](#transformation).

<a name="rest-service"></a> **Servizio REST**: acronimo di Representational State Transfer (trasferimento di stato rappresentativo). Architettura per lo scambio di informazioni tra peer in un ambiente distribuito decentralizzato. REST consente a programmi in computer diversi di comunicare indipendentemente dal sistema operativo o dalla piattaforma inviando a un URL (Uniform Resource Locator) una richiesta HTTP (Hypertext Transfer Protocol) per ottenere determinati dati.

<a name="route"></a> **Percorso**: itinerario tra due o più posizioni, che può includere anche altre informazioni, ad esempio le istruzioni relative a determinati punti di tragitto.

<a name="requests-per-second-rps"></a> **Richieste al secondo (RPS)**: vedere [Query al secondo (QPS)](#queries-per-second-qps). 

<a name="rss"></a> **RSS**: acronimo di Really Simple Syndication, Resource Description Framework (RDF) Site Summary o Rich Site Summary, a seconda dell'origine. RSS è un formato XML semplice e strutturato per la condivisione di contenuto tra diversi siti Web. I documenti RSS includono elementi di metadati significativi, ad esempio l'autore, la data, il titolo, una breve descrizione e un collegamento ipertestuale. Queste informazioni consentono a un utente (o a un servizio di pubblicazione RSS) di identificare quali contenuti valga la pena di approfondire.

## <a name="s"></a>S

<a name="satellite-imagery"></a> **Immagini satellitari**: immagini acquisite da satelliti o aerei con l'obiettivo in direzione perpendicolare rispetto alla superficie terrestre.

<a name="software-development-kit-sdk"></a> **SDK (Software Development Kit)**: raccolta di documentazione, codice di esempio e app di esempio progettata per aiutare gli sviluppatori a usare un'API per la creazione di app.

<a name="shapefile-shp"></a> **File di forma (SHP)**: noto anche come file di forma ESRI, è un formato di archiviazione di dati vettoriali che consente di archiviare la posizione, la forma e gli attributi di feature geografiche. Un file di forma viene archiviato in un set di file correlati.

<a name="spherical-mercator-projection"></a> **Proiezione sferica di Mercatore**: vedere [Web Mercator](#web-mercator). 

<a name="spatial-query"></a> **Query spaziale**: richiesta inviata a un servizio che esegue un'operazione spaziale. Ad esempio, una ricerca radiale o una ricerca lungo un percorso.

<a name="spatial-reference"></a> **Riferimento spaziale**: sistema globale, regionale o locale basato su coordinate che consente di individuare con precisione le entità geografiche. Definisce il sistema di coordinate usato per stabilire una correlazione tra le coordinate della mappa e le località nel mondo reale. I riferimenti spaziali garantiscono che i dati spaziali provenienti da diversi layer o origini possano essere integrati in modo da essere visualizzati o analizzati correttamente. Mappe di Azure usa il sistema di coordinate di riferimento [EPSG:3857](https://epsg.io/3857) e WGS84 per l'input dei dati geometrici. 

<a name="sql-spatial"></a> **Funzionalità spaziali SQL**: funzionalità per il supporto di dati spaziali integrate in SQL Azure e SQL Server 2008 e versioni successive. Queste funzionalità spaziali sono disponibili anche come libreria .NET utilizzabile indipendentemente da SQL Server. Per altre informazioni, vedere la [documentazione relativa ai dati spaziali di SQL Server](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server).

<a name="synchronous-request"></a> **Richiesta sincrona**: richiesta HTTP che apre una connessione e rimane in attesa di una risposta. I browser limitano il numero di richieste HTTP simultanee che possono essere inviate da una pagina. Se più richieste sincrone a esecuzione prolungata vengono inviate contemporaneamente, è possibile che questo limite venga raggiunto e che le richieste vengano ritardate finché non sono state completate altre richieste.

## <a name="t"></a>T

<a name="telematics"></a> **Telematica**: invio, ricezione e archiviazione di informazioni tramite dispositivi di telecomunicazione eseguite in combinazione con funzionalità di controllo su oggetti remoti. 

<a name="temporal-data"></a> **Dati temporali**: dati che fanno esplicito riferimento a orari e date. I dati temporali possono fare riferimento a eventi discreti, come la caduta di fulmini, a oggetti in movimento, come i treni, oppure a osservazioni ripetute, come i valori rilevati dai sensori del traffico.

<a name="terrain"></a> **Terreno**: area con particolari caratteristiche, ad esempio terreno sabbioso o montuoso.

<a name="thematic-maps"></a> **Mappa tematica**: mappa semplice creata per fornire informazioni su un tema specifico in relazione a un'area geografica. Uno scenario comune per questo tipo di mappa consiste nell'assegnazione di colori alle aree amministrative, ad esempio i paesi, in base a una determinata metrica di dati.

<a name="tile-layer"></a> **Layer a tessere**: layer visualizzato assemblando le tessere della mappa (sezioni rettangolari) in una superficie continua. Le tessere possono essere di tipo raster o vettoriale. I layer a tessere raster vengono in genere sottoposti a rendering in anticipo e archiviati come immagini in un server. Ciò può richiedere una notevole quantità di spazio di archiviazione. I layer a tessere vettoriali vengono sottoposti a rendering in tempo reale all'interno dell'applicazione client e pertanto i requisiti di archiviazione sul lato server sono minori.

<a name="time-zone"></a> **Fuso orario**: zona del globo in cui viene adottato uno stesso orario per scopi legali, commerciali e sociali. I fusi orari tendono a rispettare i confini dei paesi e le relative suddivisioni.

<a name="transaction"></a> **Transazione**: Mappe di Azure adotta un modello di licenze basato su transazione, dove:

- Viene creata una transazione per ogni 15 tessere di mappa o traffico richieste.
- Viene creata una transazione per ogni chiamata API a uno dei servizi di Mappe di Azure, ad esempio ricerca o calcolo di un percorso.

<a name="transformation"></a> **Trasformazione**: processo di conversione dei dati tra diversi sistemi di coordinate geografiche. È ad esempio possibile che alcuni dati siano stati acquisiti nel Regno Unito in base al sistema di coordinate geografiche OSGB 1936. Mappe di Azure usa il sistema di coordinate di riferimento [EPSG:3857](https://epsg.io/3857), una variante di WGS84. Di conseguenza, per la visualizzazione corretta dei dati, il sistema di coordinate deve essere trasformato da un sistema all'altro.

<a name="traveling-salesmen-problem-tsp"></a> **TSP (Traveling Salesmen Problem)**:  problema del ciclo hamiltoniano in cui un commesso viaggiatore deve trovare la soluzione più efficiente per raggiungere una serie di tappe e quindi tornare alla posizione iniziale.  

<a name="trilateration"></a> **Trilaterazione**: processo volto a determinare la posizione di un punto sulla superficie terrestre rispetto alla altri due punti, misurando le distanze fra tutti e tre i punti.

<a name="turn-by-turn-navigation"></a> **Navigatore**: applicazione che fornisce indicazioni stradali per ogni tappa di un percorso quando il conducente sta per passare alla manovra successiva.

## <a name="v"></a>V

<a name="vector-data"></a> **Dati vettoriali**: dati basati su coordinate che sono rappresentati come punti, linee o poligoni.

<a name="vector-tile"></a> **Tessera vettoriale**: specifica di dati aperta per l'archiviazione dei dati geospaziali di tipo vettoriale che uso lo stesso sistema di partizionamento adottato dal controllo mappa. Vedere anche [Layer a tessere](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a> **Problema di Routing del veicolo (VRP)**: classe di problemi in cui viene calcolato un set di percorsi ordinati per una flotta di veicoli tenendo in considerazione una serie di vincoli, ad esempio intervalli di recapito, capacità di effettuare più percorsi e durata del viaggio.

<a name="voronoi-diagram"></a> **Diagramma di Voronoi**: diagramma di partizionamento dello spazio in aree, o celle, che racchiudono un set di oggetti geometrici (in genere, feature di tipo punto). Queste celle, o poligoni, devono soddisfare i criteri definiti per i triangoli di Delaunay. Tutte le posizioni all'interno di un'area sono più vicine all'oggetto che circonda rispetto a qualsiasi altro oggetto nel set. I diagrammi di Voronoi vengono spesso usati per delineare le aree di influenza attorno alle feature geografiche. 

## <a name="w"></a>W

<a name="waypoint"></a> **Punto di tragitto**: specifica posizione geografica definita da latitudine e longitudine che viene usata ai fini della navigazione. Un punto di tragitto viene spesso usato per rappresentare una tappa intermedia di un percorso.

<a name="waypoint-optimization"></a> **Ottimizzazione dei punti di tragitto**: processo di riordinamento di un set di punti di tragitto per ridurre al minimo la distanza o il tempo di percorrenza necessario per attraversare tutti i punti di tragitto specificati. Questo processo è spesso definito [TSP (Traveling Salesmen Problem)](#traveling-salesmen-problem-tsp) o [VRP (Vehicle Routing Problem)](#vehicle-routing-problem-vrp), a seconda della complessità dell'ottimizzazione.

<a name="web-map-service-wms"></a> **WMS (Web Map Service)**: standard dell'OGC (Open Geographic Consortium) che definisce i servizi di generazione di mappe basate su immagini. I servizi WMS forniscono su richiesta le immagini relative ad aree specifiche all'interno di una mappa. Le immagini includono simbologia precedentemente sottoposta a rendering e possono essere generate in base a uno dei diversi stili eventualmente definiti dal servizio.

<a name="web-mercator"></a> **Web Mercator**: nota anche come proiezione sferica di Mercatore, è una variante della proiezione di Mercatore che viene usata principalmente nei programmi Web per la generazione di mappe. Usa le stesse formule della proiezione standard di Mercatore adottate per le mappe con fattore di scala ridotto. La proiezione Web Mercator, tuttavia, usa il modello sferico per qualsiasi fattore di scala, mentre le mappe di Mercatore con fattore di scala elevato usano in genere il modello ellissoidale della proiezione. La differenza è impercettibile su scala globale, ma causa una leggera deviazione delle mappe delle aree locali rispetto alle vere mappe di Mercatore ellissoidali con lo stesso fattore di scala.

<a name="wgs84"></a> **WGS84**: set di vincoli usati per stabilire una correlazione tra le coordinate spaziali e le posizioni sulla superficie della mappa. Il datum WGS84 è quello standard usato dalla maggior parte dei dispositivi GPS e provider di mappe online. Mappe di Azure usa il sistema di coordinate di riferimento [EPSG:3857](https://epsg.io/3857), una variante di WGS84.

## <a name="z"></a>Z

<a name="z-coordinate"></a> **Coordinata Z**: vedere [Altitudine](#altitude). 

<a name="zip-code"></a> **ZIP, codice**: vedere [Codice postale](#postal-code).

<a name="Zoom level"></a> **Livello di zoom**: specifica il livello di dettaglio e l'ampiezza dell'area visibile della mappa. Quando il livello di zoom viene ridotto a 0, verrà probabilmente visualizzata l'intera mappa del mondo, ma con informazioni limitate, ad esempio i nomi e i confini dei paesi e i nomi degli oceani. Se si esegue lo zoom avanti fino a giungere in prossimità del livello 17, la mappa mostrerà l'area corrispondente ad alcuni isolati di una città con informazioni stradali dettagliate. Per altre informazioni, vedere l'articolo [Livelli di zoom e griglia riquadri](zoom-levels-and-tile-grid.md).


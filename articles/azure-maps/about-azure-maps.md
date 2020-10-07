---
title: Panoramica di Mappe di Microsoft Azure
description: Questo articolo illustra i servizi e le funzionalità di Mappe di Microsoft Azure e descrive come usarli nelle applicazioni.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/31/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, references_regions
ms.openlocfilehash: 11a0ddc5c7b297d0700e6fd07d60f8efe0e55a8f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91285333"
---
# <a name="what-is-azure-maps"></a>Informazioni su Mappe di Azure

Mappe di Azure è una raccolta di servizi geospaziali ed SDK che usano dati di mappa aggiornati per fornire un contesto geografico alle applicazioni Web e per dispositivi mobili. Mappe di Azure offre:

* API REST per il rendering delle mappe vettoriali e raster in più stili e con immagini satellitari.
* Servizi Creator per creare ed eseguire il rendering delle mappe in base ai dati delle piante dell'interno private.
* Servizi di ricerca per trovare indirizzi, luoghi e punti di interesse in tutto il mondo.
* Varie opzioni di pianificazione percorso: tra due punti, tra più punti, con ottimizzazione multipunto, isocrona, per veicoli elettrici, per veicoli commerciali, con gli effetti del traffico e con matrice.
* Visualizzazione del flusso di traffico e visualizzazione degli incidenti per le applicazioni che richiedono informazioni sul traffico in tempo reale.
* Servizio di mobilità per richiedere informazioni sul transito pubblico, pianificare i percorsi combinando modalità di viaggio diverse e arrivi in tempo reale.
* Servizi per fuso orario e georilevazione.
* Servizio di geofencing e archiviazione dei dati sulle mappe, con informazioni sulla posizione ospitate in Azure.
* Dati di intelligence per la posizione attraverso l'analisi di dati geospaziali.

Inoltre, i servizi di Mappe di Azure sono disponibili tramite Web SDK e Android SDK. Questi strumenti consentono agli sviluppatori di sviluppare e ridimensionare rapidamente soluzioni che integrano informazioni sulla posizione nelle soluzioni di Azure.

Per iniziare subito a sviluppare, iscriversi per ottenere un [account gratuito di Mappe di Azure](https://azure.microsoft.com/services/azure-maps/).

Il video seguente spiega Mappe di Azure in modo approfondito:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Controlli mappa

### <a name="web-sdk"></a>Web SDK

Azure Maps Web SDK consente di personalizzare le mappe interattive con contenuto e immagini personali È possibile usare questa mappa interattiva per le applicazioni Web o per dispositivi mobili. Il controllo mappa usa WebGL, per poter eseguire il rendering di set di dati di grandi dimensioni con prestazioni elevate. È possibile sviluppare con l'SDK usando JavaScript o TypeScript.

:::image type="content" source="./media/about-azure-maps/intro_web_map_control.png" alt-text="Mappa di esempio della modifica della popolazione creata con Azure Maps Web SDK":::

### <a name="android-sdk"></a>Android SDK

Usare Android SDK di Mappe di Azure per creare applicazioni per dispositivi mobili che usano le mappe.

:::image type="content" source="./media/about-azure-maps/android_sdk.png" border="false" alt-text="Mappa di esempio della modifica della popolazione creata con Azure Maps Web SDK":::

## <a name="services-in-azure-maps"></a>Servizi di Mappe di Azure

Mappe di Azure è costituito dai servizi seguenti che possono fornire il contesto geografico alle applicazioni di Azure.

### <a name="data-service"></a>Servizio dati

I dati sono essenziali per le mappe. Usare il servizio dati per caricare e archiviare i dati geospaziali per l'uso con operazioni spaziali o la composizione di immagini.  Avvicinando i dati dei clienti al servizio Mappe di Azure è possibile ridurre la latenza, aumentare la produttività e creare nuovi scenari nelle applicazioni. Per informazioni dettagliate su questo servizio, vedere la [documentazione del servizio dati](https://docs.microsoft.com/rest/api/maps/data).

### <a name="geolocation-service"></a>Servizio di georilevazione

Il servizio di georilevazione consente di visualizzare in anteprima il codice paese/area geografica a due lettere recuperato per un indirizzo IP. Il servizio permette anche di migliorare l'esperienza utente offrendo contenuto dell'applicazione personalizzato in base alla posizione geografica.

Per informazioni dettagliate, vedere la [documentazione del servizio di georilevazione](https://docs.microsoft.com/rest/api/maps/geolocation).

### <a name="mobility-service"></a>Servizio Mobility

Il servizio di mobilità di Mappe di Azure accelera i tempi di sviluppo per le applicazioni con funzionalità di pianificazione percorso con trasporti pubblici, ad esempio per gli itinerari con trasporti pubblici e la ricerca delle fermate dei trasporti pubblici nelle vicinanze. Gli utenti possono recuperare informazioni dettagliate su fermate dei trasporti pubblici, linee e orari. Il servizio di mobilità consente inoltre agli utenti di recuperare le geometrie delle fermate e delle linee, gli avvisi per le fermate, le linee e le aree di servizio, nonché gli arrivi dei trasporti pubblici e gli avvisi dei servizi in tempo reale. Il servizio di mobilità fornisce anche funzionalità di pianificazione percorso con opzioni di pianificazione di itinerari multimodali. La pianificazione di itinerari multimodali incorpora le opzioni di itinerario a piedi, in bicicletta e con trasporti pubblici, in un unico itinerario. Gli utenti possono anche accedere a itinerari dettagliati multimodali.

Per altre informazioni sul servizio, vedere la [documentazione del servizio di mobilità](https://docs.microsoft.com/rest/api/maps/mobility).

### <a name="render-service"></a>Servizio di rendering

Il [servizio di rendering v2](https://docs.microsoft.com/rest/api/maps/renderv2), ora disponibile in anteprima, introduce una nuova versione dell'[API Recupero tessera mappa v2](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview). L'API Recupero tessera mappa v2 consente ora ai clienti di richiedere tessere stradali di Mappe di Azure, tessere meteorologiche o tessere mappa create tramite la risorsa Creator di Mappe di Azure. È consigliabile usare la nuova API Recupera tessere mappa v2.  

:::image type="content" source="./media/about-azure-maps/intro_map.png" border="false" alt-text="Mappa di esempio della modifica della popolazione creata con Azure Maps Web SDK":::

Per informazioni dettagliate, vedere la [documentazione del servizio di rendering v2](https://docs.microsoft.com/rest/api/maps/renderv2).

Per altre informazioni sul servizio di rendering v1 disponibile a livello generale, vedere la [documentazione del servizio di rendering v1](https://docs.microsoft.com/rest/api/maps/render).  

### <a name="route-service"></a>Servizio di pianificazione percorso

È possibile usare i servizi di pianificazione percorso per calcolare gli orari di arrivo stimati per ogni percorso richiesto. Le API di pianificazione percorso considerano i fattori, ad esempio le informazioni sul traffico in tempo reale e i dati sul traffico cronologici, come le velocità su strada tipiche per il giorno della settimana e l'ora del giorno richiesti. Le API restituiscono i percorsi più brevi o più veloci disponibili per più destinazioni alla volta in sequenza o in ordine ottimizzato, in base al tempo o alla distanza. Il servizio consente agli sviluppatori di calcolare le indicazioni stradali per diverse modalità di trasporto, ad esempio in auto, autocarro, bicicletta, a piedi o con un veicolo elettrico. Il servizio tiene anche in considerazione parametri di input, ad esempio l'ora di partenza, le limitazioni di peso o il trasporto di materiale pericoloso.

:::image type="content" source="./media/about-azure-maps/intro_route.png" border="false" alt-text="Mappa di esempio della modifica della popolazione creata con Azure Maps Web SDK":::

Il servizio di pianificazione percorso offre funzionalità avanzate, ad esempio:

* Elaborazione in batch di più richieste di percorso.
* Matrici con tempi di percorrenza e distanza tra un set di origini e destinazioni.
* Ricerca di percorsi e distanze percorribili dagli utenti in base ai requisiti di tempo o carburante.

Per informazioni dettagliate sulle funzionalità di pianificazione dei percorsi, vedere la [documentazione del servizio di pianificazione percorso](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Servizio di ricerca

Il servizio di ricerca consente agli sviluppatori di offrire funzionalità per la ricerca di indirizzi, località, punti di interesse, elenchi di aziende per nome o categoria e altre informazioni geografiche. Include la funzionalità di [codifica geografica inversa](https://en.wikipedia.org/wiki/Reverse_geocoding), ovvero può convertire le coordinate di latitudine e longitudine in indirizzi e incroci stradali.

:::image type="content" source="./media/about-azure-maps/intro_search.png" border="false" alt-text="Mappa di esempio della modifica della popolazione creata con Azure Maps Web SDK":::

Il servizio di ricerca offre anche funzionalità avanzate come:

* Ricerca lungo un percorso.
* Ricerca all'interno di un'area più ampia.
* Esecuzione in batch di un gruppo di richieste di ricerca.
* Ricerca delle stazioni di ricarica per veicoli elettrici e i dati dei punti di interesse in base al nome del marchio.

Per informazioni dettagliate sulle funzionalità di ricerca, vedere la [documentazione del servizio di ricerca](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-service"></a>Servizio spaziale

Il servizio spaziale analizza rapidamente le informazioni sulla posizione per comunicare ai clienti gli eventi in corso che si verificano nel tempo e nello spazio. Consente l'analisi quasi in tempo reale e la modellazione predittiva di eventi.

Il servizio consente ai clienti di ottimizzare i dati di intelligence sulla posizione con una raccolta di calcoli matematici geospaziali comuni, tra cui servizi come il punto più vicino, l'ortodromia e i buffer. Per altre informazioni sul servizio e sulle funzionalità disponibili, vedere la [documentazione del servizio spaziale](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="timezone-service"></a>Servizio fuso orario

Il servizio fusi orari consente di eseguire query sulle informazioni attuali, precedenti e future relative ai fusi orari, usando le coordinate di latitudine e longitudine o un [ID IANA](https://www.iana.org/) come input. Il servizio fusi orari consente anche di:

* Convertire gli ID di fuso orario di Microsoft Windows in fusi orari IANA.
* Recuperare la differenza di fuso orario rispetto all'ora UTC.
* Recuperare l'ora corrente nel fuso orario preferito.

Una tipica risposta JSON per una query inviata al servizio fusi orari ha un aspetto simile all'esempio seguente:

```JSON
{
  "Version": "2020a",
  "ReferenceUtcTimestamp": "2020-07-31T19:15:14.4570053Z",
  "TimeZones": [
    {
      "Id": "America/Los_Angeles",
      "Names": {
        "ISO6391LanguageCode": "en",
        "Generic": "Pacific Time",
        "Standard": "Pacific Standard Time",
        "Daylight": "Pacific Daylight Time"
      },
      "ReferenceTime": {
        "Tag": "PDT",
        "StandardOffset": "-08:00:00",
        "DaylightSavings": "01:00:00",
        "WallTime": "2020-07-31T12:15:14.4570053-07:00",
        "PosixTzValidYear": 2020,
        "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
      }
    }
  ]
}
```

Per informazioni dettagliate su questo servizio, vedere la [documentazione del servizio fusi orari](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Servizio informazioni sul traffico

Il servizio informazioni sul traffico è costituito da più servizi Web utilizzabili dagli sviluppatori per le applicazioni Web e per dispositivi mobili che richiedono informazioni sul traffico. Il servizio fornisce due tipi di dati:

* Flusso del traffico: dati sulla velocità e sui tempi di percorrenza osservati in tempo reale per tutte le strade principali della rete.
* Eventi imprevisti del traffico: visualizzazione aggiornata di ingorghi e incidenti nella rete stradale.

![Esempio di una mappa con informazioni sul traffico](media/about-azure-maps/intro_traffic.png)

Per altre informazioni, vedere la [documentazione del servizio informazioni sul traffico](https://docs.microsoft.com/rest/api/maps/traffic).

### <a name="weather-service"></a>Servizio meteo

Il servizio meteo offre API che gli sviluppatori possono usare per recuperare le informazioni meteo per una determinata località. Le informazioni contengono informazioni dettagliate, ad esempio la data e l'ora di osservazione, una breve descrizione delle condizioni meteo, l'icona meteo, i flag degli indicatori delle precipitazioni, la temperatura e le informazioni sulla velocità del vento. Vengono restituiti anche altri dettagli, ad esempio la temperatura RealFeel™ e l'indice UV.

Gli sviluppatori possono usare l'[API per il recupero del meteo lungo il percorso](https://docs.microsoft.com/rest/api/maps/weather/getweatheralongroutepreview) per recuperare le informazioni meteorologiche lungo un itinerario specifico. Il servizio supporta inoltre la generazione di notifiche meteo per i waypoint interessati da rischi meteorologici, ad esempio alluvioni o pioggia intensa.

L'[API Recupera tessera mappa v2](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) consente di richiedere le tessere radar e satellitari passate, correnti e future.

![Esempio di mappa con tessere radar meteo in tempo reale](media/about-azure-maps/intro_weather.png)

### <a name="maps-creator-service"></a>Servizio Creator di Mappe

Il servizio Creator di Mappe di Azure è una suite di servizi Web che gli sviluppatori possono usare per creare applicazioni con funzionalità mappa basate sui dati delle piante dell'interno.

Il servizio Creator di Mappe di Azure fornisce tre servizi principali:

* [Servizio Set di dati](https://docs.microsoft.com/rest/api/maps/dataset). Usare il servizio Set di dati per creare un set di dati da un pacchetto di disegno convertito. Per informazioni sui requisiti del pacchetto di disegno, vedere Requisiti del pacchetto di disegno.

* [Servizio di conversione](https://docs.microsoft.com/rest/api/maps/dataset). Usare il servizio di conversione per convertire un file di progettazione DWG in dati del pacchetto di disegno per le piante dell'interno.

* [Servizio Set di tessere](https://docs.microsoft.com/rest/api/maps/tileset). Usare il servizio Set di tessere per creare una rappresentazione basata su vettori di un set di dati. Le applicazioni possono usare un set di tessere per presentare una visualizzazione basata su tessere visiva del set di dati.

* [Servizio Stato della funzionalità](https://docs.microsoft.com/rest/api/maps/featurestate). Usare il servizio Stato della funzionalità per supportare l'applicazione di stili mappa dinamica. L'applicazione di stili mappa dinamica consente alle applicazioni di riflettere gli eventi in tempo reale sugli spazi forniti dai sistemi IoT.

* [Servizio WFS](https://docs.microsoft.com/rest/api/maps/featurestate). Usare il servizio WFS per eseguire query sui dati della pianta dell'interno. Il servizio WFS segue gli standard previsti dall'[Open Geospatial Consortium API](http://docs.opengeospatial.org/is/17-069r3/17-069r3.html) per l'esecuzione di query su un singolo set di dati.

## <a name="programming-model"></a>Modello di programmazione

Il servizio Mappe di Azure è progettato per la mobilità e può supportare applicazioni multipiattaforma. Usa un modello di programmazione indipendente dal linguaggio e supporta l'output JSON tramite [API REST](https://docs.microsoft.com/rest/api/maps/).

Mappe di Azure offre inoltre un pratico [controllo mappa JavaScript](https://docs.microsoft.com/javascript/api/azure-maps-control) con un semplice modello di programmazione. Lo sviluppo è semplice e rapido sia per le applicazioni web che per quelle per dispositivi mobili.

## <a name="power-bi-visual"></a>Oggetto visivo di Power BI

L'oggetto visivo Mappe di Azure per Power BI offre un set completo di visualizzazioni dei dati spaziali su una mappa. Si stima che oltre l'80% dei dati aziendali abbia un contesto di posizione. L'oggetto visivo Mappe di Azure offre una soluzione senza codice per acquisire informazioni approfondite sulla relazione di questo contesto di posizione con i dati aziendali e sugli effetti che produce.

:::image type="content" source="./media/about-azure-maps/intro-power-bi.png" border="false" alt-text="Mappa di esempio della modifica della popolazione creata con Azure Maps Web SDK":::

Per altre informazioni, vedere la documentazione introduttiva sull'[oggetto visivo Mappe di Azure di Power BI](power-bi-visual-getting-started.md).

## <a name="usage"></a>Uso

Per accedere ai servizi di Mappe di Azure, passare al [portale di Azure](https://portal.azure.com) e creare un account Mappe di Azure.

Mappe di Azure usa uno schema di autenticazione basato su chiavi. Quando si crea l'account, vengono generate due chiavi. Per eseguire l'autenticazione ai servizi di Mappe di Azure, è possibile usare una delle due chiavi.

Nota: il servizio Mappe di Azure condivide le query su indirizzo/posizione fornite dal cliente (query) con la terza parte TomTom per la funzionalità di creazione di mappe. Le query non sono collegate ad alcun cliente o utente finale se condivise con TomTom e non possono essere usate per l'identificazione personale. I servizi di mobilità e meteo che includono l'integrazione con Moovit e AccuWeather sono attualmente disponibili in [ANTEPRIMA](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Microsoft sta per aggiungere TomTom, Moovit e AccuWeather all'elenco di subappaltatori dei Microsoft Online Services.

## <a name="supported-regions"></a>Aree supportate

I servizi di Mappe di Azure non sono disponibili nei paesi/aree geografiche seguenti:

* Cina
* Corea del Sud

Verificare che la località dell'indirizzo IP corrente sia in uno dei paesi/aree geografiche supportati.

## <a name="next-steps"></a>Passaggi successivi

Provare un'app di esempio che presenta Mappe di Azure:

[Avvio rapido: Creare un'app Web](quick-demo-map-app.md)

Rimanere aggiornati su Mappe di Azure:

[Blog di Mappe di Azure](https://azure.microsoft.com/blog/topics/azure-maps/)

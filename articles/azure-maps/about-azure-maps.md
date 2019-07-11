---
title: Panoramica di Mappe di Azure | Microsoft Docs
description: Introduzione a Mappe di Azure
author: walsehgal
ms.author: v-musehg
ms.date: 02/04/2019
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 5f41263113568cf9f3771119135be8db37119181
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442951"
---
# <a name="what-is-azure-maps"></a>Informazioni su Mappe di Azure

Mappe di Azure è una raccolta di servizi geospaziali che usano dati di mappa aggiornati per fornire un contesto geografico preciso alle applicazioni Web e per dispositivi mobili. Mappe di Azure offre:

* API REST per il rendering delle mappe in più stili e con immagini satellitari.
* Funzionalità di ricerca per indirizzi, luoghi e punti di interesse in tutto il mondo.
* Pianificazione dei percorsi tra due punti, tra più punti, con ottimizzazione multipunto, isocrona, per veicoli commerciali, con gli effetti del traffico e con matrice; visualizzazione di flusso del traffico ed eventi imprevisti.
* Servizi di mobilità per richiedere informazioni su trasporto pubblico e mezzi di trasporto alternativi (come biciclette, scooter e automobili condivisi) e per pianificare percorsi in tempo reale. 
* Individuazione della posizione dell'utente attraverso la georilevazione e conversione della posizione in fusi orari. 
* Servizi per geofencing e archiviazione dei dati sulle mappe, con informazioni sulla posizione ospitate in Azure. 
* Dati di intelligence per la posizione attraverso l'analisi di dati geospaziali. 

Oltre alle API REST, i servizi di Mappe di Azure sono disponibili tramite Web SDK o Android SDK. Questi strumenti consentono agli sviluppatori di sviluppare e ridimensionare rapidamente soluzioni che integrano informazioni sulla posizione nelle soluzioni di Azure. 

Per iniziare subito a sviluppare, iscriversi per ottenere un [account gratuito di Mappe di Azure](https://azure.microsoft.com/services/azure-maps/).

Il video seguente spiega Mappe di Azure in modo approfondito:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Controlli mappa

### <a name="web-sdk"></a>Web SDK

Web SDK di Mappe di Azure consente di personalizzare le mappe interattive con contenuto e immagini personali per la visualizzazione in applicazioni Web o per dispositivi mobili. Questo controllo usa WebGL, in modo da poter eseguire il rendering di set di dati di grandi dimensioni con prestazioni elevate. Sviluppare con l'SDK usando JavaScript o TypeScript.

![Mappa di esempio di cambiamenti della popolazione](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Android SDK

Usare Android SDK di Mappe di Azure per creare applicazioni per dispositivi mobili che usano le mappe. 

![Esempi di mappa in un dispositivo mobile](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Servizi di Mappe di Azure

Mappe di Azure consiste nei nove servizi seguenti che possono fornire il contesto geografico alle applicazioni di Azure.

### <a name="data-service"></a>Servizio dati

I dati sono essenziali per le mappe. Usare il servizio dati per caricare e archiviare i dati geospaziali per l'uso con operazioni spaziali o la composizione di immagini.  Avvicinando i dati dei clienti al servizio Mappe di Azure è possibile ridurre la latenza, aumentare la produttività e creare nuovi scenari nelle applicazioni. Per informazioni dettagliate su questo servizio, vedere la [documentazione dell'API del servizio dati](https://docs.microsoft.com/rest/api/maps/data).

### <a name="mobility-service"></a>Servizio Mobility

Il servizio Mobility di Mappe di Azure consente la pianificazione di viaggi in tempo reale. Restituisce le opzioni di percorso migliori possibili e offre un'ampia gamma di modalità di spostamento. Per le aree metropolitana (città), queste modalità possono includere spostamenti a piedi, in bici e con i trasporti pubblici. Gli sviluppatori possono richiedere i dettagli dell'itinerario di trasporto, ad esempio tracciati delle linee, elenchi delle fermate, orari di arrivo pianificati e in tempo reale e avvisi per il servizio.

Il servizio supporta anche la ricerca di tipi di oggetto specifici, ad esempio biciclette, scooter o auto condivisi in un'area. Gli utenti possono richiedere quante biciclette condivise disponibili sono rimaste nello stallo più vicino. Possono eseguire la ricerca dei veicoli in car sharing disponibili e trovare dettagli come la disponibilità futura e il livello di carburante corrente.

Per altre informazioni sul servizio, vedere la [documentazione dell'API Mobility](https://docs.microsoft.com/rest/api/maps/mobility).

### <a name="render-service"></a>Servizio di rendering

Il servizio di rendering consente agli sviluppatori di creare applicazioni Web e per dispositivi mobili basate sull'uso di mappe. Offre grafica raster di alta qualità, disponibile in 19 livelli di zoom, oppure mappe in formato vettoriale completamente personalizzabili.

![Esempio di mappa dal servizio di rendering](media/about-azure-maps/Introduction_Map.png)

Il servizio di rendering offre ora le API per consentire agli sviluppatori di lavorare con immagini satellitari in anteprima. Per altre informazioni, vedere la [documentazione dell'API di rendering](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Servizio di pianificazione percorso

Il servizio di pianificazione percorso contiene potenti calcoli geometrici applicati alle infrastrutture del mondo reale e prevede indicazioni stradali diverse a seconda della modalità di trasporto. Il servizio consente agli sviluppatori di calcolare le indicazioni stradali per diverse modalità di trasporto, come l'auto, la bicicletta, l'autocarro o il percorso pedonale. Il servizio può anche tenere in considerazione parametri di input, ad esempio le condizioni di traffico, le limitazioni di peso o il trasporto di materiale pericoloso.

![Esempio di una mappa dal servizio di pianificazione percorso](media/about-azure-maps/Introduction_Route.png)

Il servizio di pianificazione percorso offre un'anteprima delle funzionalità avanzate, ad esempio: 

* Elaborazione in batch di più richieste di percorso.
* Matrici con tempi di percorrenza e distanza tra un set di origini e destinazioni.
* Ricerca di percorsi e distanze percorribili dagli utenti in base ai requisiti di tempo o carburante. 

Per informazioni dettagliate sulle funzionalità di pianificazione dei percorsi, vedere la [documentazione dell'API di pianificazione percorso](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Servizio di ricerca

Il servizio di ricerca consente agli sviluppatori di offrire funzionalità per la ricerca di indirizzi, località, punti di interesse, elenchi di aziende per nome o categoria e altre informazioni geografiche. Include la funzionalità di [geodecodifica](https://en.wikipedia.org/wiki/Reverse_geocoding), ovvero può convertire le coordinate di latitudine e longitudine in indirizzi e incroci stradali.

![Esempio di una ricerca su una mappa](media/about-azure-maps/Introduction_Search.png)

Il servizio di ricerca offre anche funzionalità avanzate come:

* Ricerca lungo un percorso.
* Ricerca all'interno di un'area più ampia.
* Esecuzione in batch di un gruppo di richieste di ricerca.
* Ricerca di un'area più grande, invece di un punto di posizione. 

Le API per la ricerca in batch e di area sono attualmente in anteprima. Per altre informazioni sulle funzionalità di ricerca, vedere la [documentazione dell'API di ricerca](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-operations-service"></a>Servizio operazioni spaziali

Il servizio operazioni spaziali di Mappe di Azure accetta le informazioni sulla posizione e le analizza in tempo reale per informare i clienti di eventi in corso che si verificano nel tempo e nello spazio. Consente l'analisi quasi in tempo reale e la modellazione predittiva di eventi. 

Il servizio consente ai clienti di ottimizzare i dati di intelligence sulla posizione con una raccolta di calcoli matematici geospaziali comuni, tra cui servizi come il punto più vicino, l'ortodromia e i buffer. Per altre informazioni sul servizio e le varie funzionalità, vedere la [documentazione dell'API per le operazioni spaziali](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="time-zone-service"></a>Servizio fuso orario

Il servizio fuso orario consente di eseguire query sulle informazioni attuali, precedenti e future relative ai fusi orari usando le coordinate di latitudine e longitudine o un [ID IANA](https://www.iana.org/). Il servizio fuso orario consente anche di:

* Convertire gli ID di fuso orario di Microsoft Windows in fusi orari IANA.
* Recuperare la differenza di fuso orario rispetto all'ora UTC.
* Recuperare l'ora corrente in un fuso orario. 

Una tipica risposta JSON per una query inviata al servizio fuso orario ha un aspetto simile all'esempio seguente:

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

Per informazioni dettagliate su questo servizio, vedere la [documentazione dell'API del fuso orario](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Servizio informazioni sul traffico

Il servizio informazioni sul traffico è costituito da più servizi Web utilizzabili dagli sviluppatori per creare applicazioni Web e per dispositivi mobili che richiedono informazioni sul traffico. Il servizio fornisce due tipi di dati:

* Flusso del traffico: dati sulla velocità e sui tempi di percorrenza osservati in tempo reale per tutte le strade principali della rete.
* Eventi imprevisti del traffico: visualizzazione aggiornata di ingorghi e incidenti nella rete stradale.

![Esempio di una mappa con informazioni sul traffico](media/about-azure-maps/Introduction_Traffic.png)

Per altre informazioni, vedere la [documentazione delle API per il traffico](https://docs.microsoft.com/rest/api/maps/traffic).

### <a name="ip-to-location-service"></a>Servizio di geolocalizzazione indirizzo IP

Il servizio di geolocalizzazione indirizzo IP consente di visualizzare in anteprima il codice paese di due lettere recuperato per un indirizzo IP. Il servizio permette anche di personalizzare e migliorare l'esperienza utente offrendo contenuto dell'applicazione personalizzato in base alla posizione geografica.

Per altre informazioni sulle API REST per l'indirizzo di geolocalizzazione indirizzo IP, vedere la [documentazione dell'API di georilevazione di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Modello di programmazione

Il servizio Mappe di Azure è progettato per la mobilità e può supportare applicazioni multipiattaforma. Usa un modello di programmazione indipendente dal linguaggio e supporta l'output JSON tramite [API REST](https://docs.microsoft.com/rest/api/maps/).

Mappe di Azure offre anche un pratico [controllo mappa JavaScript](https://docs.microsoft.com/javascript/api/azure-maps-control) con un semplice modello di programmazione per sviluppare applicazioni Web e per dispositivi mobili in modo rapido e intuitivo.

## <a name="usage"></a>Uso

Per accedere ai servizi di Mappe di Azure è sufficiente passare al [portale di Azure](https://portal.azure.com) e creare un account di Mappe di Azure.

Mappe di Azure usa uno schema di autenticazione basato su chiavi. L'account viene fornito con due chiavi pre-generate automaticamente. È possibile iniziare a integrare nell'applicazione queste funzionalità sulla posizione usando una delle chiavi e inviando una richiesta al servizio Mappe di Azure.

## <a name="supported-regions"></a>Aree supportate

Le API di Mappe di Azure sono attualmente disponibili in tutti i paesi/aree geografiche, ad eccezione dei seguenti:

* Argentina
* Cina
* India
* Marocco
* Pakistan
* Corea del Sud

Verificare che la località dell'indirizzo IP corrente non sia in uno dei paesi/aree geografiche non supportati.

## <a name="next-steps"></a>Passaggi successivi

Provare un'app di esempio che presenta Mappe di Azure:

> [!div class="nextstepaction"]
> [Guida introduttiva: Creare un'app Web](quick-demo-map-app.md)

Rimanere aggiornati su Mappe di Azure: 

> [!div class="nextstepaction"]
> [Blog di Mappe di Azure](https://azure.microsoft.com/blog/topics/azure-maps/)

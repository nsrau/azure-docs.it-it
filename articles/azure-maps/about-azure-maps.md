---
title: Panoramica di Mappe di Azure | Microsoft Docs
description: Introduzione a Mappe di Azure
author: dsk-2015
ms.author: dkshir
ms.date: 09/12/2018
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9bf168a0f06d662526d172687e54327b55d50bd8
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104268"
---
# <a name="what-is-azure-maps"></a>Informazioni su Mappe di Azure

Mappe di Azure è una raccolta di servizi geospaziali supportati da dati di mappa aggiornati che consente di fornire un contesto geografico preciso alle applicazioni Web e per dispositivi mobili. Contiene le API REST per il rendering delle mappe e la ricerca di punti di interesse. Le API possono anche trovare gli itinerari per i punti di interesse, le condizioni del traffico, i fusi orari e un percorso da un indirizzo IP. È possibile usare queste API con strumenti già noti per sviluppare e ridimensionare rapidamente soluzioni che integrano informazioni sulla posizione nelle soluzioni di Azure. Insieme alle API REST viene fornito anche il [controllo mappa JavaScript](https://docs.microsoft.com/javascript/api/azure-maps-control) basato sul Web che rende lo sviluppo semplice, flessibile e compatibile con più supporti.

Il video seguente spiega Mappe di Azure in modo approfondito:

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Controlli mappa

### <a name="web-control"></a>Controllo Web

Il controllo Web di Mappe di Azure consente di personalizzare le mappe interattive con contenuto e immagini personali per la visualizzazione in applicazioni Web o per dispositivi mobili. Questo controllo usa WebGL, consentendo di eseguire il rendering di set di dati di grandi dimensioni con prestazioni elevate. Sviluppare con il controllo usando JavaScript o TypeScript.

![Controllo Web di Mappe di Azure](media/about-azure-maps/Introduction_WebMapControl.png)

## <a name="services-in-azure-maps"></a>Servizi di Mappe di Azure

Mappe di Azure è costituito dai sei servizi seguenti che possono fornire il contesto geografico alle applicazioni di Azure.

### <a name="render-service"></a>Servizio di rendering

Il servizio di rendering è stato progettato per consentire agli sviluppatori di creare applicazioni Web e per dispositivi mobili basate sull'uso di mappe. Offre grafica raster di alta qualità, disponibile in 19 livelli di zoom, oppure mappe in formato vettoriale completamente personalizzabili.

![Azure Maps Map.png](media/about-azure-maps/Introduction_Map.png)

Il servizio di rendering offre ora le API per consentire agli sviluppatori di lavorare con immagini satellitari in anteprima. Per altre informazioni, vedere le [API di rendering di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Servizio di pianificazione percorso

Il servizio di pianificazione percorso contiene potenti calcoli geometrici applicati alle infrastrutture del mondo reale e prevede indicazioni stradali diverse a seconda della modalità di trasporto. Il servizio consente agli sviluppatori di calcolare le indicazioni stradali per diverse modalità di trasporto, come l'auto, la bicicletta, l'autocarro o il percorso pedonale. Il servizio può anche tenere in considerazione parametri di input, ad esempio le condizioni di traffico, le limitazioni di peso o il trasporto di materiale pericoloso.

![Azure Maps Route.png](media/about-azure-maps/Introduction_Route.png)

Il servizio di pianificazione percorso offre ora un'anteprima di funzionalità avanzate come l'elaborazione in batch di più richieste di percorso, matrici di tempi di viaggio e distanze tra un set di origini e destinazioni e ricerca di percorsi o distanze che è possibile percorrere in base a determinati requisiti di tempo o carburante. Per informazioni dettagliate sulle funzionalità di routing, vedere la [le API di percorso di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Servizio di ricerca

Il servizio di ricerca è stato progettato per consentire agli sviluppatori di offrire funzionalità per la ricerca di indirizzi, località, punti di interesse, elenchi di aziende per nome o categoria e altre informazioni geografiche. Include anche la funzionalità di [geodecodifica](https://en.wikipedia.org/wiki/Reverse_geocoding), ovvero può convertire le coordinate di latitudine e longitudine in indirizzi e incroci stradali.

![Azure Maps Search.png](media/about-azure-maps/Introduction_Search.png)

Il servizio di ricerca fornisce anche funzionalità avanzate come la ricerca lungo un percorso, la ricerca all'interno di un'area più ampia, l'elaborazione in batch di gruppi di richieste di ricerca e la ricerca di un'area più grande anziché di una posizione corrispondente a un punto. Le API per la ricerca in batch e di area sono attualmente in anteprima. Per altre informazioni sulle funzionalità di ricerca, vedere la pagina dell'[API di ricerca di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/search).

### <a name="time-zone-service"></a>Servizio fuso orario

Il servizio fuso orario consente di eseguire query sulle informazioni attuali, precedenti e future relative ai fusi orari usando le coordinate di latitudine e longitudine o un [ID IANA](https://www.iana.org/). Consente inoltre di convertire gli ID dei fusi orari di Microsoft Windows in fusi orari IANA, identificare una differenza di fuso orario rispetto all'ora UTC e ottenere l'ora corrente in un determinato fuso orario. Una tipica risposta JSON per una query inviata al servizio fusi orari ha un aspetto simile all'esempio seguente:

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

Per informazioni dettagliate su questo servizio, visitare la pagina delle [API di fuso orario di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Servizio informazioni sul traffico

Il servizio informazioni sul traffico è costituito da più servizi Web progettati per consentire agli sviluppatori di creare applicazioni Web e per dispositivi mobili che richiedono informazioni sul traffico. Il servizio fornisce due tipi di dati:

* Flusso del traffico: dati sulla velocità e sui tempi di percorrenza osservati in tempo reale per tutte le strade principali della rete.
* Eventi imprevisti del traffico: visualizzazione accurata di ingorghi e incidenti nella rete stradale.

![Traffico in Mappe di Azure](media/about-azure-maps/Introduction_Traffic.png)

Per altre informazioni, visitare la pagina delle [API del traffico di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/traffic).

### <a name="ip-to-location"></a>Da IP a posizione

Il servizio da IP a posizione consente di visualizzare in anteprima il codice paese di due lettere recuperato per un determinato indirizzo IP. Il servizio permette anche di personalizzare e migliorare l'esperienza utente modificando il contenuto dell'applicazione personalizzata in base alla posizione geografica.

Per informazioni sulle API REST per il servizio da IP a posizione, visitare la pagina delle [API per la georilevazione di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Modello di programmazione

Mappe di Azure è progettato per la mobilità e può supportare applicazioni multipiattaforma. Usa un modello di programmazione indipendente dal linguaggio e supporta l'output JSON mediante l'[API REST](https://docs.microsoft.com/rest/api/maps/).

Mappe di Azure offre anche un pratico [controllo mappa JavaScript](https://docs.microsoft.com/javascript/api/azure-maps-control) con un semplice modello di programmazione per sviluppare applicazioni Web e per dispositivi mobili in modo rapido e intuitivo.

## <a name="usage"></a>Uso

Per accedere ai servizi Mappe, è quindi sufficiente passare al [portale di Azure](https://portal.azure.com) e creare un account di Mappe di Azure.

Mappe di Azure usa uno schema di autenticazione basato su chiavi. che viene fornito con due chiavi pre-generate automaticamente. È possibile iniziare a integrare nell'applicazione queste funzionalità sulla posizione usando una delle chiavi e inviando una richiesta al servizio Mappe di Azure.

## <a name="supported-regions"></a>Aree supportate

L'API di Mappe di Azure è attualmente disponibile in tutti i paesi, ad eccezione dei seguenti:

* Argentina
* Cina
* India
* Marocco
* Pakistan
* Corea del Sud

Verificare che la località dell'indirizzo IP corrente non sia uno dei paesi non supportati sopra elencati.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle nuove funzionalità di Mappe di Azure:

> [!div class="nextstepaction"]
> [Matrice di percorsi, isocrone, ricerca IP e altre funzioni](https://azure.microsoft.com/blog/route-matrix-isochrones-ip-lookup-and-more-added-to-azure-maps/)

Procedere e provare un'app di esempio che dimostra il servizio:

> [!div class="nextstepaction"]
> [Avviare una mappa demo per la ricerca interattiva](quick-demo-map-app.md)

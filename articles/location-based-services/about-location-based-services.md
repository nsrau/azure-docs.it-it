---
title: Informazioni generali su Servizi Location Based di Azure | Microsoft Docs
description: Introduzione a Servizi Location Based di Azure (anteprima)
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: article
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: c8ebce06a72bcaf769a11ec954702463d7489aa0
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="an-introduction-to-azure-location-based-services-preview"></a>Introduzione a Servizi Location Based di Azure (anteprima)
Servizi Location Based di Azure è un portfolio di servizi geospaziali che include API di servizi per mappe, ricerca, pianificazione del percorso, informazioni sul traffico e fusi orari. Il portfolio di servizi conformi con Azure OneAPI consente di usare strumenti di sviluppo già noti per sviluppare e ridimensionare rapidamente soluzioni che integrano informazioni sulla posizione nelle soluzioni di Azure. Servizi Location Based di Azure offre agli sviluppatori di qualsiasi settore potenti funzionalità geospaziali che includono dati delle mappe aggiornati, indispensabili per fornire il contesto geografico alle applicazioni Web e per dispositivi mobili. Questi servizi sono costituiti da un set di API REST conforme con Azure OneAPI, accompagnato da un controllo JavaScript basato sul Web, che rende lo sviluppo estremamente semplice, flessibile e compatibile con più supporti. 

Servizi Location Based di Azure include cinque servizi principali per fornire supporto alle applicazioni Azure che richiedono un contesto geografico. Di seguito sono descritti in dettaglio i singoli servizi.

**Servizio di rendering**: questo servizio è stato progettato per consentire agli sviluppatori di creare applicazioni Web e per dispositivi mobili basate sull'uso di mappe. Offre grafica raster di alta qualità, disponibile in 19 livelli di zoom, oppure mappe in formato vettoriale completamente personalizzabili.

![Mappa di Servizi Location Based di Azure](media/about-location-based-services/Introduction_Map.png)

**Servizio di pianificazione percorso**: questo servizio è basato su potenti calcoli geometrici applicati alle infrastrutture del mondo reale e prevede indicazioni stradali diverse a seconda della modalità di trasporto. Consente agli sviluppatori di calcolare le indicazioni stradali per diverse modalità di trasporto, come l'auto, la bicicletta, l'autocarro o il percorso pedonale, e di specificare diversi parametri di input, ad esempio le condizioni di traffico, le limitazioni di peso o il trasporto di materiale pericoloso.

![Percorso di Servizi Location Based di Azure](media/about-location-based-services/Introduction_Route.png)

**Servizio di ricerca**: questo servizio è stato progettato per consentire agli sviluppatori di offrire funzionalità per la ricerca di indirizzi, località, punti di interesse, elenchi di aziende per nome o categoria e altre informazioni geografiche. Include anche la funzionalità di [geodecodifica](https://en.wikipedia.org/wiki/Reverse_geocoding), ovvero può convertire le coordinate di latitudine e longitudine in indirizzi e incroci stradali. 

![Ricerca di Servizi Location Based di Azure](media/about-location-based-services/Introduction_Search.png)

**Servizio fusi orari**: questo servizio consente di eseguire query sulle informazioni attuali, precedenti e future relative ai fusi orari usando le coordinate di latitudine e longitudine o un [ID IANA](http://www.iana.org/). Consente inoltre di convertire gli ID dei fusi orari di Microsoft Windows in fusi orari IANA, identificare una differenza di fuso orario rispetto all'ora UTC e ottenere l'ora corrente in un determinato fuso orario. Una tipica risposta JSON per una query inviata al servizio fusi orari ha un aspetto simile al seguente:

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

**Servizio informazioni sul traffico**: questo servizio è costituito da più servizi Web progettati per consentire agli sviluppatori di creare applicazioni Web e per dispositivi mobili che richiedono informazioni sul traffico. Il servizio è suddiviso in componenti che gestiscono le informazioni seguenti:
1. Flusso del traffico: fornisce dati sulla velocità e sui tempi di percorrenza osservati in tempo reale per tutte le strade principali della rete. 
2. Eventi imprevisti del traffico: fornisce una visualizzazione accurata di ingorghi e incidenti nella rete stradale.

![Informazioni sul traffico di Servizi Location Based di Azure](media/about-location-based-services/Introduction_Traffic.png)

Servizi Location Based di Azure è stato progettato per la mobilità e può essere usato per applicazioni multipiattaforma poiché il modello di programmazione è indipendente dalla piattaforma e supporta l'output JSON tramite le API REST. Questo portfolio di servizi offre inoltre un pratico controllo mappa JavaScript con un modello di programmazione semplice per sviluppare applicazioni Web e per dispositivi mobili in modo rapido e intuitivo. 

Per Servizi Location Based di Azure viene usato uno schema di autenticazione basato su chiavi. Pertanto, per accedere ai servizi, è sufficiente passare al [portale di Azure](http://portal.azure.com) e creare uno specifico account, che viene fornito con due chiavi pre-generate automaticamente. È possibile iniziare a integrare queste funzionalità sulla posizione direttamente nelle applicazioni usando una delle proprie chiavi nelle richieste inviate a Servizi Location Based di Azure.

Eseguendo l'iscrizione si otterrà [subito un account Servizi Location Based di Azure](http://aka.ms/azurelbsportal)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata presentata una panoramica di Servizi Location Based di Azure (anteprima). Come passaggio successivo è possibile provare un'app di esempio che illustra l'uso di Servizi Location Based e creare uno scenario end-to-end nella propria applicazione Web.

> [!div class="nextstepaction"]
> [Avviare una demo di una ricerca interattiva sulla mappa con Servizi Location Based di Azure (anteprima)](quick-demo-map-app.md)
> [Cercare un punto di interesse vicino tramite Servizi Location Based di Azure](tutorial-search-location.md)
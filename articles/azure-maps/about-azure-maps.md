---
title: Panoramica di Mappe di Azure | Microsoft Docs
description: Introduzione a Mappe di Azure
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9eb6039a10cbdac004ab25f8295f5659c246a743
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600062"
---
# <a name="an-introduction-to-azure-maps"></a>Introduzione a Mappe di Azure
Mappe di Azure è un portfolio di servizi geospaziali che include API di servizi per mappe, ricerca, pianificazione del percorso, informazioni sul traffico e fusi orari. Il portfolio di servizi consente di usare strumenti già noti per sviluppare e ridimensionare rapidamente soluzioni che integrano informazioni sulla posizione nelle soluzioni di Azure. Mappe di Azure offre agli sviluppatori di tutti i settori potenti funzionalità geospaziali che includono i dati mappa aggiornati indispensabili per fornire contesto geografico alle applicazioni Web e per dispositivi mobili. Mappe di Azure è un set di API REST accompagnate da un controllo JavaScript basato sul Web, che rende lo sviluppo semplice, flessibile e compatibile con più supporti. 

Il video seguente presenta Mappe di Azure:

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Mappe di Azure include cinque servizi principali per fornire supporto alle applicazioni Azure che richiedono un contesto geografico. I singoli servizi sono descritti in dettaglio.

Il **servizio di rendering** è stato progettato per consentire agli sviluppatori di creare applicazioni Web e per dispositivi mobili basate sull'uso di mappe. Offre grafica raster di alta qualità, disponibile in 19 livelli di zoom, oppure mappe in formato vettoriale completamente personalizzabili.

![Azure Maps Map.png](media/about-azure-maps/Introduction_Map.png)

Il **servizio di pianificazione percorso** contiene potenti calcoli geometrici applicati alle infrastrutture del mondo reale e prevede indicazioni stradali diverse a seconda della modalità di trasporto. Il servizio consente agli sviluppatori di calcolare le indicazioni stradali per diverse modalità di trasporto, come l'auto, la bicicletta, l'autocarro o il percorso pedonale. Il servizio può anche tenere in considerazione parametri di input, ad esempio le condizioni di traffico, le limitazioni di peso o il trasporto di materiale pericoloso.

![Azure Maps Route.png](media/about-azure-maps/Introduction_Route.png)

Il **servizio di ricerca** è stato progettato per consentire agli sviluppatori di offrire funzionalità per la ricerca di indirizzi, località, punti di interesse, elenchi di aziende per nome o categoria e altre informazioni geografiche. Include anche la funzionalità di [geodecodifica](https://en.wikipedia.org/wiki/Reverse_geocoding), ovvero può convertire le coordinate di latitudine e longitudine in indirizzi e incroci stradali. 

![Azure Maps Search.png](media/about-azure-maps/Introduction_Search.png)

Il **servizio fusi orari** consente di eseguire query sulle informazioni attuali, precedenti e future relative ai fusi orari usando le coordinate di latitudine e longitudine o un [ID IANA](http://www.iana.org/). Consente inoltre di convertire gli ID dei fusi orari di Microsoft Windows in fusi orari IANA, identificare una differenza di fuso orario rispetto all'ora UTC e ottenere l'ora corrente in un determinato fuso orario. Una tipica risposta JSON per una query inviata al servizio fusi orari ha un aspetto simile all'esempio seguente:

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

Il **servizio informazioni sul traffico** è costituito da più servizi Web progettati per consentire agli sviluppatori di creare applicazioni Web e per dispositivi mobili che richiedono informazioni sul traffico. Il servizio fornisce due tipi di dati:
* Flusso del traffico: dati sulla velocità e sui tempi di percorrenza osservati in tempo reale per tutte le strade principali della rete. 
* Eventi imprevisti del traffico: visualizzazione accurata di ingorghi e incidenti nella rete stradale.

![Traffico in Mappe di Azure](media/about-azure-maps/Introduction_Traffic.png)

Mappe di Azure è stato progettato per la mobilità e può essere usato per applicazioni multipiattaforma perché il modello di programmazione è indipendente dalla piattaforma e supporta l'output JSON tramite le API REST. Mappe di Azure offre anche un pratico controllo mappa JavaScript con un semplice modello di programmazione per sviluppare applicazioni Web e per dispositivi mobili in modo rapido e intuitivo. 

Per Mappe di Azure viene usato uno schema di autenticazione basato su chiavi. Per accedere ai servizi, è quindi sufficiente passare al [portale di Azure](http://portal.azure.com) e creare uno specifico account, che viene fornito con due chiavi pre-generate automaticamente. È possibile iniziare a integrare queste funzionalità sulla posizione direttamente nelle applicazioni usando una delle proprie chiavi nelle richieste inviate a Mappe di Azure.

## <a name="unsupported-regions"></a>Aree non supportate
L'API di Mappe di Azure non è attualmente disponibile in alcuni paesi. Controllare l'indirizzo IP corrente e verificare che la località dell'indirizzo IP non sia uno dei paesi seguenti non supportati:

* Argentina
* Cina
* India
* Marocco
* Pakistan
* Corea del Sud

## <a name="relationship-with-bing-maps"></a>Relazione con Bing Mappe
La soluzione Mappe descritta in questo documento è diversa da Bing Mappe. Anche se queste soluzioni condividono molte funzionalità, si tratta di due servizi diversi e non correlati. Questo servizio di Azure non ha alcun impatto sull'offerta o sugli sviluppi futuri di Bing Mappe.

L'obiettivo di Microsoft è quello di offrire alla community di sviluppatori la possibilità di scegliere tra servizi di posizione diversi. La tabella seguente contiene indicazioni per gli sviluppatori che devono decidere quale servizio usare: 

| Scenario | Usare Mappe di Azure quando… | Usare Bing Mappe quando… |
| ------------- | ------------- | ------------- |
| Ambiente di sviluppo | Si crea una soluzione all'interno di altri servizi di Azure o coordinando tali servizi | Si usa un ambiente cloud di terze parti o un altro ambiente di sviluppo |
| Fase di sviluppo  | Mappe di Azure è ottimizzato per i test iniziali e lo sviluppo di un modello di verifica | È necessario un contratto di servizio di livello aziendale per un ambiente di produzione |
| Opzioni di prezzo | Sono sufficienti opzioni di prezzo preliminari per lo sviluppo | Sono necessari prezzi di livello enterprise personalizzati |
| Ambiente del caso d'uso | È necessario l'uso nei veicoli | Non è necessario l'uso nei veicoli |
| Copertura geografica | Non è necessaria la copertura delle mappe di India, Cina, Giappone e Corea del Sud | È necessaria la copertura delle mappe di India, Cina, Giappone e Corea del Sud |
| Contenuto delle mappe | Sono sufficienti mappe di superficie standard | Sono necessarie immagini satellitari, aeree e delle strade |
| Fonte delle mappe sottostante | Si preferiscono i dati delle mappe TomTom | Si preferiscono i dati delle mappe HERE |

Iscriversi a un [account di Mappe di Azure subito](http://aka.ms/azurelbsportal).

## <a name="next-steps"></a>Passaggi successivi

Quella descritta è una panoramica di Mappe di Azure. Il passaggio successivo consiste nel provare un'app di esempio che presenta il servizio.

> [!div class="nextstepaction"]
> [Avviare una mappa demo per la ricerca interattiva](quick-demo-map-app.md)


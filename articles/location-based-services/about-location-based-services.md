---
title: Informazioni generali su Servizi Location Based di Azure | Microsoft Docs
description: Introduzione a Servizi Location Based di Azure (anteprima)
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 02/05/2017
ms.topic: overview
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 9e6236f7d69556d7636962c98886d9f9508445ac
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
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

Servizi Location Based di Azure è stato progettato per la mobilità e può essere usato per applicazioni multipiattaforma poiché il modello di programmazione è indipendente dalla piattaforma e supporta l'output JSON tramite le API REST. Servizi Location Based di Azure offre inoltre un pratico controllo mappa JavaScript con un semplice modello di programmazione per sviluppare applicazioni Web e per dispositivi mobili in modo rapido e intuitivo. 

Per Servizi Location Based di Azure viene usato uno schema di autenticazione basato su chiavi. Pertanto, per accedere ai servizi, è sufficiente passare al [portale di Azure](http://portal.azure.com) e creare uno specifico account, che viene fornito con due chiavi pre-generate automaticamente. È possibile iniziare a integrare queste funzionalità sulla posizione direttamente nelle applicazioni usando una delle proprie chiavi nelle richieste inviate a Servizi Location Based di Azure.

## <a name="relationship-with-bing-maps"></a>Relazione con Bing Mappe
La soluzione Servizi Location Based di Azure descritta in questo documento è diversa da Bing Mappe.  Anche se queste soluzioni condividono molte funzionalità, si tratta di due servizi diversi e non correlati.  La disponibilità di questo nuovo servizio di Azure, che verrà gestito separatamente, non ha alcun impatto sull'offerta o sugli sviluppi futuri di Bing Mappe.

L'obiettivo di Microsoft è quello di offrire alla community di sviluppatori la possibilità di scegliere tra servizi di posizione diversi.  Di seguito vengono fornite alcune indicazioni rapide per gli sviluppatori in merito al servizio da scegliere in base ai diversi casi d'uso e alle esigenze dei clienti.  Le indicazioni fornite fanno riferimento alla versione di anteprima di Servizi Location Based di Azure e verranno aggiornate quando il servizio sarà disponibile a livello generale, più avanti nel 2018.

| Criteri del cliente | Usare Servizi Location Based di Azure quando… | Usare Bing Mappe quando… |
| ------------- | ------------- | ------------- |
| Ambiente di sviluppo | Si crea una soluzione all'interno di altri servizi di Azure o sfruttando tali servizi | Si usa un ambiente cloud di terze parti o un altro ambiente di sviluppo |
| Fase di sviluppo  | Poiché Servizi Location Based di Azure è attualmente disponibile in versione di anteprima pubblica, è ottimizzato per i test iniziali e lo sviluppo di un modello di verifica | È necessario un contratto di servizio di livello aziendale per un ambiente di produzione |
| Opzioni di prezzo | Sono sufficienti opzioni di prezzo preliminari per lo sviluppo | Sono necessari prezzi di livello enterprise personalizzati |
| Ambiente del caso d'uso | È necessario l'uso nei veicoli | Non è necessario l'uso nei veicoli |
| Copertura geografica | Non è necessaria la copertura delle mappe di India, Cina, Giappone e Corea del Sud | È necessaria la copertura delle mappe di India, Cina, Giappone e Corea del Sud |
| Contenuto delle mappe | Sono sufficienti mappe di superficie standard | Sono necessarie immagini satellitari, aeree e delle strade |
| Fonte delle mappe sottostante | Si preferiscono i dati delle mappe TomTom | Si preferiscono i dati delle mappe HERE |

Eseguendo l'iscrizione si otterrà [subito un account Servizi Location Based di Azure](http://aka.ms/azurelbsportal).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata presentata una panoramica di Servizi Location Based di Azure (anteprima). Come passaggio successivo è possibile provare un'app di esempio che illustra l'uso di Servizi Location Based e creare uno scenario end-to-end nella propria applicazione Web.

> [!div class="nextstepaction"]
> [Avviare una demo di una ricerca interattiva sulla mappa con Servizi Location Based di Azure (anteprima)](quick-demo-map-app.md)
> [Cercare un punto di interesse vicino tramite Servizi Location Based di Azure](tutorial-search-location.md)

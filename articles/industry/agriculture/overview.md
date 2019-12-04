---
title: Panoramica di Azure FarmBeats
description: Questo articolo contiene una panoramica di Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: ff28ae7dbff40910ac0431fb47f7f0be3e1569c0
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538134"
---
# <a name="overview-of-azure-farmbeats"></a>Panoramica di Azure FarmBeats

Azure FarmBeats è una raccolta di servizi e funzionalità di Azure, progettata per facilitare la creazione rapida di soluzioni intelligenti basate sui dati correlati all'agricoltura. Azure FarmBeats è un'offerta di Azure Marketplace che consente di acquisire, aggregare ed elaborare dati correlati all'agricoltura da diverse origini, ad esempio sensori, droni, fotocamere e satellite, senza investire in risorse di ingegneria dei dati avanzate.

> [!NOTE]
> Azure FarmBeats è attualmente disponibile in anteprima pubblica. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Azure FarmBeats è disponibile senza un contratto di servizio. Per ricevere supporto, usare il [forum su Azure FarmBeats](https://aka.ms/FarmBeatsMSDN ).

Con Azure FarmBeats è possibile ottenere dati da diverse origini, ad esempio sensori, satelliti e droni, tutti nel contesto di un'azienda agricola (area geografica di interesse).

Sarà possibile:

- Aggregare set di dati correlati all'agricoltura da provider diversi
- Creare rapidamente modelli di intelligenza artificiale e apprendimento automatico combinando set di dati diversi

Azure FarmBeats offre un modo semplice e affidabile per eseguire le operazioni seguenti:

- Creare una mappa per l'azienda agricola tramite il file GeoJSON.
- Valutare lo stato dell'azienda agricola usando l'indice di vegetazione e l'indice idrico basato sulle immagini satellitari.
- Ottenere consigli sul numero di sensori da usare e sul percorso in cui posizionarli.
- Tenere traccia della condizione dell'azienda agricola visualizzando i dati del terreno raccolti dai sensori di fornitori diversi.
- Ottenere la mappa dell'umidità del suolo basata sulla fusione dei dati del sensore e del satellite.
- Ottenere informazioni dettagliate di utilità pratica creando modelli di intelligenza artificiale/apprendimento automatico sulla base di set di dati aggregati.
- Creare o potenziare la soluzione di agricoltura digitale grazie ad avvisi sullo stato dell'azienda agricola.

I componenti di Azure FarmBeats sono descritti nelle sezioni seguenti di questo articolo.

## <a name="data-hub"></a>Hub di dati

L'hub di dati Azure FarmBeats è un livello API, che consente l'aggregazione, la normalizzazione e la contestualizzazione di diversi set di dati di agricoltura tra i provider. A partire da questa versione di anteprima, è possibile usare due provider di sensori, ovvero [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/) e [Teralytic](https://teralytic.com/), un provider di immagini satellitari, ovvero [Sentinel-2](https://sentinel.esa.int/web/sentinel/home), e tre provider di immagini drone, ovvero [senseFly](https://www.sensefly.com/), [SlantRange](https://slantrange.com/) e [DJI](https://dji.com/). L'hub di dati è progettato come una piattaforma per le API e collaboriamo con molti più provider per l'integrazione con Azure FarmBeats, in modo da aumentare le possibilità di scelta durante la creazione della soluzione.

## <a name="accelerator"></a>Acceleratore

Soluzione di esempio, basata sull'hub di dati, che avvia rapidamente l'interfaccia utente e lo sviluppo del modello. Questa applicazione Web sfrutta le API per visualizzare come grafici i dati dei sensori inseriti e come mappe l'output del modello. È possibile ad esempio usare l'acceleratore per creare rapidamente un'azienda agricola e ottenere con facilità una mappa dell'indice di vegetazione o una mappa di posizionamento dei sensori.

## <a name="resources"></a>Risorse

Per altre informazioni, vedere il [blog](https://aka.ms/AzureFarmBeats) e i [forum](https://aka.ms/FarmBeatsMSDN) di Azure FarmBeats.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare Azure FarmBeats, visitare [Azure Marketplace](https://aka.ms/FarmBeatsMarketplace) per avviare il processo di distribuzione.

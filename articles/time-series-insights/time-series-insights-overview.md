---
title: Panoramica Informazioni su Azure Time Series Insights | Microsoft Docs
description: Introduzione ad Azure Time Series Insights, un nuovo servizio per le soluzioni IoT e di analisi dei dati delle serie temporali
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 15674d995036d11e1b4ec0ceda5aa4bbf4952906
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990018"
---
# <a name="what-is-azure-time-series-insights"></a>Informazioni su Azure Time Series Insights

Azure Time Series Insights è una soluzione per archiviare, visualizzare ed eseguire query su grandi quantità di dati relativi a serie temporali, ad esempio i dati generati dai dispositivi IoT. Se si desidera archiviare, gestire, eseguire query o visualizzare i dati delle serie temporali nel cloud, Time Series Insights rappresenta la soluzione ideale. 

[![Diagramma di flusso di Time Series Insights](media/overview/time-series-insights-flowchart.png)](media/overview/time-series-insights-flowchart.png#lightbox)

Time Series Insights è caratterizzato da quattro processi chiavi:

- Integrazione completa con i gateway cloud, ad esempio Hub IoT di Azure e Hub eventi di Azure. Connessione semplificata a queste origini evento e analisi JSON da messaggi e strutture di dati in colonne e righe normali. Unione di metadati con dati di telemetria e indicizzazione dei dati in un archivio a colonne.
- Time Series Insights gestisce l'archiviazione dei dati. Per assicurarsi che siano sempre facilmente accessibili, i dati vengono archiviati nella memoria e nelle unità SSD per un massimo di 400 giorni. È possibile eseguire query in modalità interattiva su miliardi di eventi in pochi secondi e su richiesta.
- In Time Series Insights è disponibile una visualizzazione predefinita tramite lo strumento di esplorazione di Time Series Insights. 
- Time Series Insights fornisce un servizio di query sia nello strumento di esplorazione di Time Series Insights che tramite l'uso di API di facile integrazione per incorporare dati delle serie temporali nelle applicazioni personalizzate.

Se si compila un'applicazione per consumo interno o destinata ai clienti esterni, è possibile usare Time Series Insights come back-end. È possibile usarla per indicizzare, archiviare e aggregare i dati delle serie temporali. Per creare una visualizzazione e un'esperienza utente personalizzate, usare [Client SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). Time Series Insights include anche diverse [API di query](how-to-shape-query-json.md) per abilitare questi scenari personalizzati.

I dati delle serie temporali rappresentano il modo in cui un asset o un processo varia nel corso del tempo. I dati delle serie temporali vengono indicizzati in base ai timestamp e l'ora è l'asse più significativo lungo il quale tali dati vengono organizzati. I dati delle serie temporali in genere vengono acquisiti in ordine sequenziale, quindi vengono considerati un inserimento anziché un aggiornamento al database.

Può essere difficile archiviare, indicizzare, eseguire query, analizzare e visualizzare i dati delle serie temporali in volumi di grandi dimensioni.
Azure Time Series Insights acquisisce e archivia ogni nuovo evento sotto forma di riga e le variazioni vengono misurate in modo efficiente nel corso del tempo. Di conseguenza, è possibile analizzare i dati a ritroso per ottenere informazioni dal passato e prevedere variazioni future.

## <a name="video"></a>Video

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>Altre informazioni su Azure Time Series Insights, la piattaforma di analisi IoT basata sul cloud.</br>

[![VIDEO](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Scenari principali

- Archiviare i dati delle serie temporali in modo scalabile. 

   Time Series Insights si basa su un database appositamente sviluppato per i dati delle serie temporali. Poiché è scalabile e completamente gestito, Time Series Insights riesce a gestire le operazioni di archiviazione e gestione degli eventi.

- Esplorare i dati in tempo quasi reale. 

   Lo strumento di esplorazione di Time Series Insights consente di visualizzare tutti i dati trasmessi a un ambiente. Subito dopo aver effettuato la connessione a un'origine evento, è possibile visualizzare, esplorare ed eseguire query sui dati dell'evento in Time Series Insights. I dati consentono di verificare se un dispositivo genera dati come previsto ed esegue il monitoraggio dello stato di integrità, della produttività e dell'efficienza complessiva di un asset IoT. 

- Eseguire l'analisi della causa radice e rilevare le anomalie.

   Time Series Insights ha strumenti quali, ad esempio, le visualizzazioni modello e prospettiva, che consentono di eseguire e salvare analisi della causa radice in più passaggi. Time Series Insights può anche essere usato con altri servizi di avviso, ad esempio Analisi di flusso di Azure, per poter visualizzare quasi in tempo reale gli avvisi e le anomalie rilevate nello strumento di esplorazione. 

- Ottenere una visualizzazione globale dei dati delle serie temporali trasmessi da posizioni geografiche diverse per il confronto tra più asset o siti.

   È possibile connettere più origini evento a un ambiente Time Series Insights. In questo modo è possibile visualizzare contemporaneamente i dati trasmessi da più posizioni geografiche diverse in tempo quasi reale. Gli utenti possono sfruttare questa visibilità per condividere dati con i responsabili aziendali. Possono collaborare al meglio con esperti di dominio che possono applicare le proprie competenze per risolvere problemi, applicare le procedure consigliate e condividere esperienze.

- Creare applicazioni dei clienti basate su Time Series Insights. 

   Time Series Insights espone le API per query REST, che è possibile usare per creare applicazioni che usano i dati delle serie temporali.

## <a name="capabilities"></a>Capabilities

- **Uso immediato**: poiché Azure Time Series Insights non richiede la preparazione dei dati iniziale, è possibile connettersi rapidamente a milioni di eventi nell'hub IoT o nell'hub eventi. Dopo la connessione è possibile visualizzare e interagire con i dati dei sensori per convalidare rapidamente le soluzioni IoT. È possibile interagire con i dati senza scrivere codice e senza dover imparare un nuovo linguaggio. Time Series Insights offre agli utenti avanzati una superficie di query granulare con testo libero e un'esplorazione più semplice.

- **Informazioni dettagliate quasi in tempo reale**: Time Series Insights può inserire milioni di eventi di sensori al giorno con una latenza di un minuto. Time Series Insights consente di ottenere informazioni dettagliate sui dati dei sensori. Usarle per individuare tendenze e anomalie, eseguire analisi delle cause radice ed evitare costosi tempi di inattività. La correlazione incrociata tra dati in tempo reale e cronologici consente di trovare le tendenze nascoste nei dati.

- **Compilazione di soluzioni personalizzate**: incorporare i dati di Azure Time Series Insights nelle applicazioni esistenti. È anche possibile creare nuove soluzioni personalizzate con le API REST di Time Series Insights. Creare visualizzazioni personalizzate che è possibile condividere per consentire ad altri di usufruire delle informazioni dettagliate rilevate.

- **Scalabilità**: Time Series Insights è progettato per supportare IoT su larga scala. Può infatti inserire da 1 milione a 100 milioni di eventi al giorno, con un intervallo di conservazione di 31 giorni. È possibile visualizzare e analizzare i flussi dei dati attivi quasi in tempo reale, oltre a dati cronologici.

## <a name="get-started"></a>Attività iniziali

Seguire questa procedura per iniziare.

1. Effettuare il provisioning di un nuovo ambiente Time Series Insights nel portale di Azure.
1. Connettersi a un'origine evento, ad esempio un hub IoT o un hub eventi. 
1. Caricare i dati di riferimento. Non è un servizio aggiuntivo.
1. I dati verranno visualizzati nel giro di pochi minuti nello strumento di esplorazione di Time Series Insights.

## <a name="time-series-insights-explorer"></a>Strumento di esplorazione di Time Series Insights

Questo diagramma illustra un esempio di dati di Time Series Insights visualizzati tramite lo strumento di esplorazione di Time Series Insights.

![Strumento di esplorazione di Time Series Insights](media/time-series-insights-explorer/explorer4.png)

## <a name="next-steps"></a>Passaggi successivi

- Esplorare l'[ambiente di dimostrazione gratuito](./time-series-quickstart.md) con disponibilità generale di Azure Time Series Insights.
- Altre informazioni su come [pianificare l'ambiente di Time Series Insights](time-series-insights-environment-planning.md).

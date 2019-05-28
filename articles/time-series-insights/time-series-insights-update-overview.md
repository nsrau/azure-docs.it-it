---
title: Panoramica Anteprima di Azure Time Series Insights | Microsoft Docs
description: Panoramica dell’anteprima di Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.workload: big-data
ms.topic: overview
ms.date: 04/26/2019
ms.custom: seodec18
ms.openlocfilehash: a742e9f235812cdbdafdcb0a39581e0779fcd040
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64572919"
---
# <a name="the-azure-time-series-insights-preview"></a>Anteprima di Azure Time Series Insights

Anteprima di Azure Time Series Insights è una piattaforma end-to-end distribuita come servizio. È utilizzata per raccogliere, elaborare, archiviare, analizzare e sottoporre a query dati su scala IoT contestualizzati e ottimizzati per le serie temporali. Time Series Insights è ideale per l'esplorazione di dati ad hoc e per l'analisi operativa. Time Series Insights è un servizio estendibile e personalizzato in modo unico in grado di soddisfare le esigenze di vario tipo delle distribuzioni IoT industriali.

> [!TIP]
> Per le funzionalità in disponibilità generale, vedere la panoramica [Azure Time Series Insights disponibile a livello generale](time-series-insights-overview.md).

## <a name="video"></a>Video

### <a name="learn-more-about-the-azure-time-series-insights-preview-br"></a>Altre informazioni sull'anteprima di Time Series Insights. </br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="define-iot-data"></a>Definire i dati IoT

I dati IoT sono tutti i dati industriali disponibili nelle organizzazioni che fanno un uso intensivo di asset. Spesso i dati IoT sono altamente strutturati perché sono inviati dagli asset che registrano misurazioni piuttosto fastidiose. Queste misure includono movimento, temperatura e umidità. Questi flussi di dati sono spesso caratterizzati da lacune significative, messaggi danneggiati e false letture. Prima di eseguire l'analisi dei dati di tali flussi, è necessario eseguirne la pulizia.

I dati IoT spesso sono significativi solo nel contesto di input di dati aggiuntivi che provengono da origini proprietarie, ad esempio CRM o ERP. Gli input provengono anche da origini dati di terze parti, ad esempio meteo o posizione.

Di conseguenza, solo una frazione dei dati viene usata per scopi commerciali e operativi. Tali dati forniscono informazioni coerenti, complete, aggiornate e corrette per l'analisi e la creazione di report aziendali. Rendere i dati IoT raccolti informazioni dettagliate richiede:

* Elaborazione dei dati per pulire, filtrare, interpolare, trasformare e preparare i dati per l'analisi.
* Definizione di una struttura per esplorare e comprendere i dati, ossia, per normalizzare e contestualizzare i dati.
* Archiviazione a basso costo per la conservazione a lungo termine o illimitata per decenni di dati elaborati o derivati e non elaborati.

Nella figura seguente viene illustrato un tipico flusso di dati IoT.

  ![Flusso di dati IoT][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights per i dati IoT industriali

L'attuale panorama IoT è eterogeneo. I clienti variano dal settore della produzione, al settore automobilistico, dell’energia, delle utilità, degli edifici intelligenti e della consulenza per i settori. Gli scenari includono l'esplorazione dei dati ad hoc in cui la forma dei dati è sconosciuta. Gli scenari includono anche analisi operative su dati schematizzati o modellati in modo esplicito, per l'efficienza operativa. Questi scenari sono in genere presenti in combinazione e supportano casi d'uso diversi. Le funzionalità della piattaforma che sono fondamentali per il successo delle aziende di IoT industriali e la loro rivoluzione digitale includono:

- Archiviazione a più livelli, sia usata raramente che di frequente.
- La possibilità di archiviare decenni di dati delle serie temporali.
- La possibilità di modellare e ottimizzare in modo esplicito le query basate su intelligence operativa basata sugli asset.

Time Series Insights è una soluzione di piattaforma distribuita come servizio end-to-end che offre l'esplorazione dei dati IoT e il servizio Operational Insights. Time Series Insights è un servizio cloud completamente gestito per l'analisi dei dati IoT di serie temporali.

È possibile archiviare dati non elaborati in un archivio in memoria senza schema. È quindi possibile eseguire query interattive ad hoc attraverso un motore di query distribuite e attraverso l’API. Utilizzare la ricca esperienza utente avanzata per visualizzare miliardi di eventi in pochi secondi. Ulteriori informazioni sulle [funzionalità di esplorazione dei dati](./time-series-insights-overview.md).

Time Series Insights offre anche le funzionalità del servizio Operational Insights, attualmente in versione di anteprima. Insieme all'esplorazione interattiva dei dati e alle informazioni operative, è possibile usare Time Series Insights per ottenere maggior valore dai dati raccolti dagli asset IoT. L’offerta dell’anteprima supporta:

* Un archivio dati di serie temporali a scalabilità, prestazioni e costi ottimizzati. Questa soluzione IoT basata su cloud può indicare la tendenza di anni di dati delle serie temporali in pochi secondi.
* Il supporto di modelli semantici che descrive il dominio e i metadati associati ai segnali derivati e non derivati da asset e dispositivi.
* Esperienza utente migliorata che combina informazioni dettagliate sui dati basate su asset con analisi dei dati ad hoc avanzate. Questa combinazione guida il business e l’intelligence operativa.
* Integrazione con gli strumenti avanzati di apprendimento automatico e degli strumenti analitici. Gli strumenti includono Azure Databricks, Apache Spark, Azure Machine Learning, i notebook di Jupyter e Power BI. Questi strumenti consentono di affrontare le sfide dei dati delle serie temporali per l'efficienza operativa.

Insieme, Operational Insights e le funzionalità di esplorazione dei dati vengono offerti con un semplice modello di prezzi con pagamento in base al consumo per l'elaborazione dei dati, l'archiviazione e le query,. Questo modello di fatturazione è adatto alle mutevoli esigenze aziendali.

Questo diagramma di flusso di dati di alto livello mostra gli aggiornamenti.

  ![Funzionalità principali][2]

Con l'introduzione di queste funzionalità IoT industriali chiave, Time Series Insights offre gli importanti vantaggi illustrati di seguito.

| | |
| ---| ---|
| **Archiviazione a più livelli per i dati IoT delle serie temporali** | Con una pipeline di elaborazione dei dati comuni per l'inserimento di dati, è possibile archiviare i dati nell'archiviazione ad accesso frequente per le query interattive. È anche possibile archiviare i dati nell'archiviazione offline sicura per grandi volumi di dati. Sfruttare i vantaggi delle [query](./time-series-insights-update-tsq.md) ad alte prestazioni basate sugli asset. |
| **Modello di Time Series per contestualizzare i dati di telemetria non elaborati e ricavare informazioni dettagliate basate sugli asset** | Contestualizzare i dati di telemetria non elaborati con il [modello Time Series](./time-series-insights-update-tsm.md) descrittivo. Derivare informazioni operative avanzate con query basate su dispositivo con prestazioni e costi altamente ottimizzati. |
| **Integrazione facile e continua con altre soluzioni di dati** |  I dati in Time Series Insights vengono [archiviati](./time-series-insights-update-storage-ingress.md) nei file Apache Parquet open source. L'integrazione con altre soluzioni di dati, di prime o di terzi parti, è facile per gli scenari end-to-end. Questi scenari includono business intelligence, apprendimento automatico avanzato e analitica predittiva. |
| **Esplorazione dei dati quasi in tempo reale** | L'esperienza utente dell’[anteprima di Azure Time Series Insights Explorer](./time-series-insights-update-explorer.md) fornisce una visualizzazione per tutti i dati trasmessi nella pipeline di inserimento. Subito dopo aver effettuato la connessione a un'origine evento, è possibile visualizzare, esplorare ed eseguire query sui dati dell'evento. In questo modo, è possibile verificare se un dispositivo genera dati come previsto. È anche possibile monitorare l'integrità, la produttività e l'efficienza complessiva di un asset IoT. |
| **Analisi della causa radice e rilevamento delle anomalie** | L’[anteprima di Azure Time Series Insights Explorer](./time-series-insights-update-explorer.md) supporta il modello e le visualizzazioni in prospettiva che consentono di eseguire e salvare analisi della causa radice in più passaggi. Insieme ad Analisi di flusso di Azure, è possibile usare Time Series Insights per rilevare avvisi e anomalie quasi in tempo reale. |
| **Applicazioni personalizzate costruite sulla piattaforma Time Series Insights** | Time Series Insights supporta [JavaScript SDK](./tutorial-explore-js-client-lib.md). SDK fornisce controlli avanzati e accesso semplificato alle query. Usare SDK per creare applicazioni IoT personalizzate all'inizio di Time Series Insights in base alle esigenze aziendali specifiche. Inoltre, è possibile usare le [API di query](./time-series-insights-update-tsq.md) di Time Series Insights direttamente per integrare i dati nelle applicazioni IoT personalizzate. |

## <a name="next-steps"></a>Passaggi successivi

Inizia a usare l’anteprima di Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Leggere la guida di avvio rapido](./time-series-insights-update-quickstart.md)

Ulteriori informazioni sui casi d'uso:

> [!div class="nextstepaction"]
> [Casi d’uso sull'anteprima di Azure Time Series Insights](./time-series-insights-update-use-cases.md)

<!-- Images -->
[1]: media/v2-update-overview/overview_one.png
[2]: media/v2-update-overview/overview_two.png

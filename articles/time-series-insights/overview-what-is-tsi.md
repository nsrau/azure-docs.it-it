---
title: "Panoramica: Che cos'è Azure Time Series Insights Gen2? - Azure Time Series Insights Gen2 | Microsoft Docs"
description: Informazioni sulle modifiche, sui miglioramenti e sulle funzionalità di Azure Time Series Insights Gen2.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: overview
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 8fdbb42d06f26849d14e093c340c6d2457ee6ec9
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020096"
---
# <a name="what-is-azure-time-series-insights-gen2"></a>Che cos'è Azure Time Series Insights Gen2

Azure Time Series Insights Gen2 è un servizio di analisi IoT end-to-end aperto e scalabile che offre esperienze utente ottimali e API avanzate per integrare funzionalità sofisticate nel flusso di lavoro o in un'applicazione esistente dell'utente.

Consente di eseguire operazioni di raccolta, archiviazione, query e visualizzazione di dati a livello di Internet delle cose, ovvero dati altamente contestualizzati e ottimizzati per le serie temporali.

Azure Time Series Insights Gen2 è progettato per l'esplorazione di dati ad hoc e per l'analisi operativa, consentendo di individuare tendenze nascoste, rilevare anomalie ed eseguire l'analisi della causa radice. È un servizio aperto e flessibile, in grado di soddisfare le esigenze di vario tipo delle distribuzioni IoT industriali.

## <a name="video"></a>Video

Altre informazioni su Azure Time Series Insights Gen2.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Using-Azure-Time-Series-Insights-to-create-an-Industrial-IoT-analytics-platform/player]

## <a name="definition-of-iot-data"></a>Definizione di dati IoT

I dati di IoT industriale nelle organizzazioni a elevato utilizzo di risorse spesso mancano di coerenza strutturale a causa della diversificata natura di dispositivi e sensori in un ambiente industriale. I dati di questi flussi sono caratterizzati da lacune significative e in alcuni casi da messaggi danneggiati e false letture. I dati IoT spesso sono significativi nel contesto di input di dati aggiuntivi che provengono da origini proprietarie o di terzi, ad esempio CRM o ERP che aggiungono contesto per flussi di lavoro end-to-end. Gli input provenienti da origini dati di terze parti, ad esempio i dati meteorologici, possono contribuire ad aumentare i flussi di dati di telemetria in una determinata installazione.

Ciò implica che solo una parte dei dati viene usata per scopi operativi e aziendali e l'analisi richiede la contestualizzazione. I dati industriali sono spesso disposti in ordine cronologico per un'analisi approfondita basata su intervalli di tempo più lunghi, al fine di comprendere e correlare le tendenze. Rendere i dati IoT raccolti informazioni dettagliate richiede:

* Elaborazione dei dati per pulire, filtrare, interpolare, trasformare e preparare i dati per l'analisi.
* Definizione di una struttura per esplorare e comprendere i dati, ossia, per normalizzare e contestualizzare i dati.
* Archiviazione a basso costo per la conservazione a lungo termine o illimitata di dati elaborati o derivati e dati non elaborati.

Tali dati forniscono informazioni coerenti, complete, aggiornate e corrette per l'analisi e la creazione di report aziendali.

La figura seguente illustra un tipico flusso di dati IoT.

[![Flusso di dati IoT](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-gen2-for-industrial-iot"></a>Azure Time Series Insights Gen2 per i dati IoT industriali

Il panorama IoT è diverso con i clienti che si occupano di vari segmenti industriali, tra cui produzione, automobili, energia, servizi pubblici, edifici intelligenti e consulenza. In questa vasta gamma di mercati IoT industriali, le soluzioni native del cloud che forniscono analisi complete mirate a dati IoT su larga scala sono in continua evoluzione.

Azure Time Series Insights Gen2 risponde a questa esigenza del mercato offrendo una soluzione di analisi IoT end-to-end pronta all'uso, con funzionalità di modellazione semantica avanzate per la contestualizzazione dei dati delle serie temporali, dati analitici basati su asset e un'esperienza utente ottimale per l'individuazione, il riconoscimento delle tendenze, il rilevamento delle anomalie e l'intelligence operativa.

Azure Time Series Insights Gen2 è una piattaforma di analisi operativa avanzata che, combinata con le funzionalità di esplorazione dei dati interattive, consente di ottenere maggior valore dai dati raccolti dagli asset IoT. L'offerta Gen2 supporta:

* Soluzione di archiviazione a più livelli con supporto di analisi a caldo e a freddo che offre ai clienti la possibilità di instradare i dati tra caldo e freddo per l'analisi interattiva sui dati a caldo e l'intelligenza operativa su decenni di dati cronologici.

  * Una soluzione di analisi a caldo estremamente interattiva per eseguire un numero elevato di query frequenti su dati di intervalli di tempo più brevi
  * Un data lake delle serie temporali scalabile e ottimizzato per il costo e per le prestazioni, basato su Archiviazione di Azure, che consente ai clienti di individuare le tendenze di anni di dati delle serie temporali in pochi secondi.

* Il supporto di modelli semantici che descrive il dominio e i metadati associati ai segnali derivati e non elaborati da asset e dispositivi.

* Piattaforma analitica flessibile per archiviare i dati cronologici delle serie temporali nell'account di Archiviazione di Azure di proprietà del cliente, consentendo così ai clienti di disporre della proprietà dei propri dati IoT. I dati vengono archiviati in un formato open source Apache Parquet che consente la connettività e l'interoperabilità tra diversi scenari di dati, tra cui analisi predittiva, Machine Learning e altri calcoli personalizzati eseguiti usando tecnologie note, tra cui Spark e Databricks.

* Analisi avanzate con API di query avanzate ed esperienza utente che combina informazioni dettagliate sui dati basati su asset con analisi avanzate dei dati ad hoc con supporto per interpolazione, funzioni scalari e di aggregazione, variabili categoriali, grafici a dispersione e segnali delle serie temporali con cambio d'ora per l'analisi approfondita.

* Piattaforma di livello Enterprise per supportare le esigenze di scalabilità, prestazioni, sicurezza e affidabilità dei clienti IoT aziendali.

* Supporto di estendibilità e integrazione per l'analisi end-to-end. Azure Time Series Insights Gen2 offre una piattaforma di analisi estendibile per diversi scenari di dati. Il connettore Power BI consente ai clienti di trasportare le query eseguite in Azure Time Series Insights Gen2 direttamente in Power BI per ottenere una visualizzazione unificata delle analisi di BI e delle serie temporali in un unico pannello.

Il diagramma seguente mostra il flusso di lavoro generale.

  [![Funzionalità principali](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Azure Time Series Insights Gen2 offre un modello di determinazione dei prezzi con pagamento in base al consumo scalabile per l'elaborazione dei dati, l'archiviazione (dati e metadati) e le query, consentendo ai clienti di ottimizzare l'utilizzo in base alle esigenze aziendali.

Con l'introduzione di queste funzionalità IoT industriali chiave, Azure Time Series Insights Gen2 offre anche gli importanti vantaggi illustrati di seguito:  

| Funzionalità | Vantaggi |
| ---| ---|
| Archiviazione a più livelli per i dati IoT delle serie temporali | Con una pipeline di elaborazione dei dati condivisi per l'inserimento di dati, è possibile inserire dati in archivi ad accesso sporadico e frequente. Usare l'archivio ad accesso frequente per le query interattive e l'archivio ad accesso sporadico per archiviare volumi elevati di dati. Per altre informazioni su come sfruttare i vantaggi delle query basate su asset con prestazioni elevate, vedere l'argomento relativo alle [query](./concepts-query-overview.md). |
| Modello di Time Series per contestualizzare i dati di telemetria non elaborati e ricavare informazioni dettagliate basate sugli asset | È possibile usare il modello Time Series per creare istanze, gerarchie, tipi e variabili per i dati delle serie temporali. Per altre informazioni sul modello Time Series, vedere l'argomento relativo al [modello Time Series](./concepts-model-overview.md).  |
| Integrazione facile e continua con altre soluzioni di dati | I dati nell'archivio ad accesso sporadico di Azure Time Series Insights Gen2 vengono [archiviati](./concepts-storage.md) in file Apache Parquet open source. Ciò consente l'integrazione dei dati con altre soluzioni dati di proprietà o di terzi, per scenari che includono business intelligence, Machine Learning avanzato e analisi predittiva. |
| Esplorazione dei dati quasi in tempo reale | L'esperienza utente dello [strumento di esplorazione di Azure Time Series Insights Gen2](./concepts-ux-panels.md) fornisce una visualizzazione per tutti i dati trasmessi nella pipeline di inserimento. Dopo aver effettuato la connessione a un'origine evento, è possibile visualizzare, esplorare ed eseguire query sui dati dell'evento. In questo modo, è possibile verificare se un dispositivo genera dati come previsto. È anche possibile monitorare l'integrità, la produttività e l'efficienza complessiva di un asset IoT. |
| Estendibilità e integrazione | L'integrazione del connettore Power BI è disponibile direttamente nell'esperienza utente dello strumento di esplorazione di Time Series tramite l'opzione **Esporta**, consentendo ai clienti di esportare direttamente in Power BI Desktop le query delle serie temporali create nell'esperienza utente e di visualizzare i relativi grafici delle serie temporali insieme ad altre analisi BI. In questo modo, si apre la porta a una nuova classe di scenari per le aziende IoT industriali che hanno investito in Power BI, offrendo un unico pannello per visualizzare le analisi di diverse origini dati, tra cui le serie temporali IoT. |
| Applicazioni personalizzate basate sulla piattaforma Azure Time Series Insights Gen2 | Azure Time Series Insights Gen2 supporta [JavaScript SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). SDK fornisce controlli avanzati e accesso semplificato alle query. Usare l'SDK per creare applicazioni IoT personalizzate basate su Azure Time Series Insights Gen2 secondo le esigenze aziendali. È inoltre possibile usare le [API di query](./concepts-query-overview.md) di Azure Time Series Insights Gen2 direttamente per integrare i dati nelle applicazioni IoT personalizzate. |

## <a name="next-steps"></a>Passaggi successivi

Iniziare a usare Azure Time Series Insights Gen2:

> [!div class="nextstepaction"]
> [Guida di avvio rapido](./quickstart-explore-tsi.md)

Ulteriori informazioni sui casi d'uso:

> [!div class="nextstepaction"]
> [Casi d'uso di Azure Time Series Insights Gen2](./overview-use-cases.md)
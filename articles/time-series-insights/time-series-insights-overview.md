---
title: Panoramica Informazioni su Azure Time Series Insights | Microsoft Docs
description: Introduzione ad Azure Time Series Insights, un nuovo servizio per le soluzioni IoT e di analisi dei dati delle serie temporali
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: overview
ms.date: 04/26/2019
ms.custom: seodec18
ms.openlocfilehash: 85ce1748ee6c68ac96436b353d9240a22f8a479a
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826399"
---
# <a name="what-is-azure-time-series-insights"></a>Informazioni su Azure Time Series Insights

Azure Time Series Insights è una soluzione che consente di archiviare, visualizzare ed eseguire query su grandi quantità di dati relativi a serie temporali, ad esempio i dati generati dai dispositivi IoT.  Se si desidera archiviare, gestire, eseguire query o visualizzare i dati delle serie temporali nel cloud, Time Series Insights rappresenta la soluzione ideale.  

![Diagramma di flusso di Time Series Insights](media/overview/time-series-insights-flowchart.png)

Time Series Insights è caratterizzato da quattro processi chiavi:

- Integrazione completa con i gateway cloud, ad esempio Hub IoT di Azure e Hub eventi di Azure. Connessione semplificata a queste origini evento e analisi JSON da messaggi e strutture di dati in colonne e righe normali. Unione di metadati con dati di telemetria e indicizzazione dei dati in un archivio a colonne.
- Time Series Insights gestisce l'archiviazione dei dati. Per garantire un accesso semplificato ai dati, i dati vengono archiviati nella memoria e nell'unità SSD per un massimo di 400 giorni. È possibile eseguire query in modalità interattiva su miliardi di eventi in pochi secondi e su richiesta.
- In Time Series Insights è disponibile una visualizzazione predefinita tramite lo strumento di esplorazione di Time Series Insights.  
- Time Series Insights fornisce un servizio di query sia nello strumento di esplorazione di Time Series Insights sia tramite l'uso di API di facile integrazione per l'incorporamento di dati delle serie temporali nelle applicazioni personalizzate.  

Se si intende compilare un'applicazione per uso interno o destinata a clienti esterni, è possibile usare Time Series Insights come back-end per l'indicizzazione, l'archiviazione e l'aggregazione di dati relativi alle serie temporali. È anche possibile creare una visualizzazione e un'esperienza utente personalizzate usando [Client SDK](tutorial-explore-js-client-lib.md). Time Series Insights include anche diverse [API di query](how-to-shape-query-json.md) per abilitare questi scenari personalizzati.  

I dati delle serie temporali rappresentano il modo in cui un asset o un processo varia nel corso del tempo. I dati delle serie temporali vengono quindi indicizzati in base ai timestamp e l'ora è l'asse più significativo lungo il quale tali dati vengono organizzati. I dati delle serie temporali in genere vengono acquisiti in ordine sequenziale e di conseguenza vengono considerati un inserimento nel database e non un suo aggiornamento.

In presenza di ingenti quantità di dati, l'archiviazione, l'indicizzazione, l'esecuzione di query, l'analisi e la visualizzazione dei dati delle serie temporali possono trasformarsi in operazioni molto complesse.
Azure Time Series Insights, tuttavia, acquisisce e archivia ogni nuovo evento sotto forma di riga e le variazioni vengono misurate in modo efficiente nel corso del tempo, consentendo di analizzare i dati a ritroso per ottenere informazioni dal passato e prevedere variazioni future.

## <a name="video"></a>Video

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>Altre informazioni su Azure Time Series Insights, la piattaforma di analisi IoT basata sul cloud.</br>

[![VIDEO](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Scenari principali

- Archiviazione dei dati delle serie temporali in modo scalabile.  

   Time Series Insights si basa su un database appositamente sviluppato per i dati delle serie temporali.  Poiché è scalabile e completamente gestito, Time Series Insights è in grado di gestire le operazioni di archiviazione e gestione degli eventi.

- Esplorazione dei dati quasi in tempo reale.  

   Lo strumento di esplorazione di Time Series Insights consente di visualizzare il flusso di tutti i dati in ingresso in un ambiente.  I dati degli eventi possono essere visualizzati, analizzati e sottoposti a query all'interno di Time Series Insights subito dopo essersi connessi a un'origine evento.  I dati sono utili per la convalida se un dispositivo genera dati come previsto ed esegue il monitoraggio dello stato di integrità, della produttività e dell'efficienza complessiva di un asset IoT.  

- Analisi della causa radice e rilevamento delle anomalie.

   Time Series Insights dispone di strumenti quali, ad esempio, le visualizzazioni modello e prospettiva, che consentono di eseguire e salvare analisi della causa radice in più passaggi.  Time Series Insights può inoltre essere usato con altri servizi di avviso, ad esempio Analisi di flusso di Azure, per visualizzare quasi in tempo reale gli avvisi e le anomalie rilevate nello strumento di esplorazione.  

- Visualizzazione globale dei flussi di dati delle serie temporali da posizioni geografiche diverse per il confronto tra più asset/siti.

   È possibile connettere più origini evento a un ambiente Time Series Insights.  Ciò significa che i flussi di dati in ingresso provenienti da più posizioni diverse possono essere visualizzati contemporaneamente quasi in tempo reale.  Gli utenti possono avvalersi di questa visibilità avanzata per condividere dati con manager di alto livello e ottimizzare la collaborazione con gli esperti di dominio, che saranno in grado di concentrarsi sulla risoluzione dei problemi, l'applicazione di procedure consigliate e la condivisione di informazioni strategiche.

- Compilazione di applicazioni dei clienti basate su Time Series Insights. 

   Time Series Insights espone le API per query REST, che consentono di compilare applicazioni che usano i dati delle serie temporali.

## <a name="capabilities"></a>Capabilities

- **Uso immediato**: Azure Time Series Insights non richiede di preparare i dati in anticipo. È possibile connettersi a milioni di eventi nell'hub IoT di Azure o nell'hub eventi in pochi minuti. Dopo la connessione è possibile visualizzare e interagire con i dati dei sensori per convalidare rapidamente le soluzioni IoT. È possibile interagire con i dati senza scrivere codice.
Non è necessario avere familiarità con un particolare linguaggio. Time Series Insights offre agli utenti avanzati una superficie di query granulare con testo libero e un'esplorazione più semplice.

- **Informazioni dettagliate quasi in tempo reale**: Time Series Insights può inserire milioni di eventi di sensori al giorno con una latenza di un minuto. Time Series Insights consente di ottenere informazioni dettagliate sui dati dei sensori permettendo di individuare tendenze e anomalie e di eseguire analisi della causa radice evitando i costi dovuti ai tempi di inattività. Abilitando la correlazione incrociata tra dati in tempo reale e cronologici, Time Series Insights consente di sbloccare le tendenze nascoste nei dati.

- **Compilazione di soluzioni personalizzate**: è possibile incorporare i dati di Azure Time Series Insights nelle applicazioni esistenti o creare nuove soluzioni personalizzate con le API REST di Time Series Insights. Creare visualizzazioni personalizzate che è possibile condividere per consentire ad altri di usufruire delle informazioni dettagliate rilevate.

- **Scalabilità**: Time Series Insights è progettato per supportare IoT su larga scala. Può infatti inserire da 1 milione a 100 milioni di eventi al giorno, con un intervallo di conservazione di 31 giorni. È possibile visualizzare e analizzare i flussi dei dati attivi quasi in tempo reale, oltre a dati cronologici. I tassi di inserimento e conservazione aumenteranno man mano proporzionalmente in base alle esigenze aziendali.

## <a name="getting-started"></a>Introduzione

Iniziare a usare Time Series Insights richiede meno di cinque minuti.

1. Per iniziare, eseguire il provisioning di un ambiente Time Series Insights nel portale di Azure.
1. Connettersi a un'origine evento, ad esempio un hub IoT di Azure o un hub eventi.  
1. Caricare i dati di riferimento (non è un servizio aggiuntivo).
1. I dati verranno visualizzati nel giro di pochi minuti nello strumento di esplorazione di Time Series Insights.

## <a name="time-series-insights-explorer"></a>Strumento di esplorazione di Time Series Insights

Questo diagramma illustra un esempio di dati di Time Series Insights visualizzati tramite lo strumento di esplorazione: ![Strumento di esplorazione di Time Series Insights](media/time-series-insights-explorer/explorer4.png)

## <a name="next-steps"></a>Passaggi successivi

- Esplorare l'[ambiente di dimostrazione gratuito](./time-series-quickstart.md) con disponibilità generale di Azure Time Series Insights.

- Altre informazioni sulla [pianificazione dell'ambiente di Time Series Insights](time-series-insights-environment-planning.md).

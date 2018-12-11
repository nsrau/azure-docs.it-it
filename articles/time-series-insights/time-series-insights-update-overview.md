---
title: Panoramica di Azure Time Series Insights | Microsoft Docs
description: Panoramica di Azure Time Series Insights
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.workload: big-data
ms.topic: overview
ms.date: 12/03/2018
ms.openlocfilehash: 83513ab9f7bee5c51fc909e5f4246105b6f145ca
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52876132"
---
# <a name="azure-time-series-insights-overview"></a>Panoramica di Azure Time Series Insights

Azure Time Series Insights (TSI) è una soluzione PaaS (piattaforma distribuita come servizio) end-to-end che consente di inserire, elaborare, e archiviare dati IoT ottimizzati per le serie temporali e notevolmente contestualizzati ed eseguire query su tali dati. Azure Time Series Insights è quindi ideale per l'esplorazione di dati ad hoc oltre che per l'analisi operativa. Time Series Insights è un servizio estendibile e personalizzato in modo unico in grado di soddisfare le esigenze di vario tipo delle distribuzioni IoT industriali.

## <a name="what-is-iot-data"></a>Cosa sono i dati IoT?

I dati IoT sono tutti i dati "industriali" disponibili nelle organizzazioni che fanno un uso intensivo di asset. I dati IoT sono spesso non strutturati in quanto vengono inviati da una vasta gamma di asset che registrano misurazioni con molto rumore, come temperatura, umidità e movimento. Questi flussi di dati sono inoltre spesso caratterizzati da lacune significative, messaggi danneggiati e false letture. Prima di eseguire l'analisi dei dati di tali flussi, è necessario eseguirne la pulizia. I dati IoT spesso sono significativi solo nel contesto di input di dati aggiuntivi provenienti da origini dati proprietarie (come sistemi CRM o ERP) o di terze parti (ad esempio informazioni su meteo o posizione).

Di conseguenza, solo una minima frazione dei dati viene usata per scopi commerciali e operativi. Tali dati forniscono informazioni coerenti, complete, aggiornate e corrette per l'analisi e la creazione di report aziendali. Per trasformare i dati IoT raccolti in informazioni dettagliate di utilità pratica sono quindi necessarie alcune importanti funzionalità:

* Elaborazione dei dati per pulire, filtrare, interpolare, trasformare e preparare i dati per l'analisi
* Definizione di una struttura per esplorare e comprendere i dati (per normalizzare e contestualizzare i dati)
* Archiviazione a basso costo per la conservazione a lungo termine o illimitata per decenni di dati elaborati (derivati) e non elaborati

Il tipico flusso di dati IoT è simile a quanto illustrato di seguito:

  ![Flusso di dati IoT][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights per i dati IoT industriali

L'attuale panorama IoT è eterogeneo. Include clienti che operano in settori che vanno da quello automobilistico a quello di gas e petrolio, dal settore energetico e dei servizi pubblici agli edifici intelligenti, fino alla consulenza. Gli scenari includono l'esplorazione di dati ad hoc in cui la forma dei dati è sconosciuta, oltre che l'analisi operativa di dati schematizzati (modellati in modo esplicito) per favorire l'efficienza operativa. Questi scenari sono in genere presenti in combinazione e supportano casi d'uso diversi. Funzionalità della piattaforma, come archiviazione a più livelli (archiviazione ad accesso frequente e archiviazione offline sicura), la possibilità di archiviare decenni di dati delle serie temporali e la possibilità di modellare e ottimizzare in modo esplicito le query per informazioni operative basate sugli asset stanno diventando fondamentali per il successo delle aziende IoT industriali e la loro rivoluzione digitale.

Azure Time Series Insights è una soluzione PaaS (piattaforma distribuita come servizio) end-to-end che offre sia l'esplorazione dei dati IoT sia il servizio Operational Insights. Time Series Insights è un servizio cloud completamente gestito per l'analisi dei dati IoT di serie temporali.

I clienti possono archiviare i dati non elaborati in un archivio in memoria senza schema ed eseguire query ad hoc interattive attraverso un motore di query distribuite e un'API, oltre che sfruttare l'esperienza utente avanzata per visualizzare miliardi di eventi in pochi secondi. Leggere altre informazioni sulle [funzionalità di esplorazione dei dati](./time-series-insights-overview.md).

Time Series Insights offre anche le funzionalità del servizio Operational Insights, attualmente in versione di anteprima pubblica. Insieme all'esplorazione interattiva dei dati e alle informazioni operative, Time Series Insights consente ai clienti di ottenere maggior valore dai dati raccolti dagli asset IoT. In particolare, la versione di anteprima pubblica supporta le funzionalità principali illustrate di seguito:

* Archivio dati delle serie temporali scalabile e ottimizzato per prestazioni e costi che consente a una soluzione IoT basata sul cloud di individuare le tendenze di anni di dati delle serie temporali in pochi secondi.
* Supporto di modelli semantici per descrivere il dominio e i metadati associati ai segnali derivati e non derivati da asset e dispositivi.
* Esperienza utente migliorata che combina informazioni dettagliate sui dati basate su asset con analisi dei dati ad hoc avanzate per ottenere business intelligence e informazioni operative.
* Integrazione con strumenti avanzati di apprendimento automatico e analisi come Azure Databricks, Apache Spark, Azure Machine Learning, notebook Jupyter, Power BI e così via, che aiutano i clienti ad affrontare con successo le sfide legate ai dati delle serie temporali e migliorare l'efficacia operativa.

Insieme, Operational Insights e le funzionalità di esplorazione dei dati vengono offerti con un semplice modello di prezzi con pagamento in base al consumo per l'elaborazione dei dati, l'archiviazione e le query, per offrire ai clienti un modello molto più scalabile in grado di soddisfare le esigenze aziendali in continua evoluzione.

Di seguito è illustrato un diagramma del flusso di dati generale che mostra le funzionalità aggiornate:

  ![Funzionalità principali][2]

Con l'introduzione di queste funzionalità IoT industriali chiave, Azure Time Series Insights offre gli importanti vantaggi illustrati di seguito.

* Archiviazione a più livelli per i dati IoT delle serie temporali

  * Con una pipeline di elaborazione dati comune per l'inserimento dei dati, i clienti hanno la possibilità di archiviare i dati nell'archiviazione ad accesso frequente per le query interattive e/o nell'archiviazione offline sicura per grossi volumi di dati, oltre che di sfruttare i vantaggi delle query basate su asset ad alte prestazioni.

  * Il routing dinamico tra livelli di archiviazione sarà presto disponibile.

* Modello di serie temporale per contestualizzare i dati di telemetria non elaborati e ricavare informazioni dettagliate basate su asset

  * I clienti possono contestualizzare i dati di telemetria non elaborati con un modello di serie temporale descrittivo e ottenere informazioni operative avanzate con query basate su dispositivi ottimizzate per i costi e le prestazioni.

* Facile integrazione con altre soluzioni di dati
  
  * Poiché i dati in Azure Time Series Insights vengono archiviati in file Apache Parquet open source, i clienti possono usufruire di una facile integrazione con altre soluzioni di dati (proprietarie o di terze parti) per scenari end-to-end, tra cui business intelligence, apprendimento automatico avanzato, analisi predittiva e così via.

* Esplorazione dei dati quasi in tempo reale

  * L'esperienza utente di Azure Time Series Insights Explorer fornisce una visualizzazione per tutti i dati trasmessi nella pipeline di inserimento. Poco dopo aver stabilito una connessione a un'origine evento, i clienti possono visualizzare ed esplorare i dati degli eventi, oltre che eseguire query su di essi, per verificare se un dispositivo genera dati come previsto e per monitorare lo stato di integrità, la produttività e l'efficienza complessiva di un asset IoT.

* Analisi della causa radice e rilevamento delle anomalie

  * Azure Time Series Insights Explorer supporta modelli e visualizzazioni in prospettiva che consentono di eseguire e salvare analisi della causa radice in più passaggi. Insieme ad Analisi di flusso di Azure, i clienti possono usare Time Series Insights per rilevare avvisi e anomalie quasi in tempo reale.

* Creazione di applicazioni personalizzate sulla piattaforma Time Series Insights

  * Azure Time Series Insights supporta JavaScript SDK, con controlli avanzati e accesso semplificato alle query per consentire ai clienti di creare applicazioni IoT personalizzate basate sulla piattaforma Time Series Insights per soddisfare le esigenze delle singole aziende.
  * I clienti possono anche usare le API di query di Time Series Insights direttamente per integrare i dati nelle applicazioni IoT personalizzate.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile iniziare a usare Azure Time Series Insights (anteprima):

> [!div class="nextstepaction"]
> [Leggere la guida di avvio rapido](./time-series-insights-update-quickstart.md)

<!-- Images -->
[1]: media/v2-update-overview/overview_one.png
[2]: media/v2-update-overview/overview_two.png
---
title: Traccia distribuita in Azure Application Insights | Microsoft Docs
description: Fornisce informazioni sul supporto di Microsoft per la traccia distribuita tramite la partnership nel progetto OpenCensus
ms.topic: conceptual
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 83575aa7f9b63615f453e00bd06b00a5540b9a9e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80892258"
---
# <a name="what-is-distributed-tracing"></a>Che cos'è la traccia distribuita?

L'avvento delle architetture moderne di [microservizi](https://azure.com/microservices) e cloud ha dato luogo a servizi semplici e distribuiti in modo indipendente che consentono di ridurre i costi aumentando la disponibilità e la velocità effettiva. Mentre questi movimenti hanno reso i singoli servizi più facili da comprendere nel suo complesso, hanno reso i sistemi complessivi più difficili da motivare ed eseguire il debug.

Nelle architetture monolitiche, abbiamo usato per eseguire il debug con stack di chiamate. Gli stack di chiamate sono strumenti molto utili per la visualizzazione del flusso di esecuzione (il metodo A ha chiamato il metodo B che ha chiamato il metodo C), insieme a informazioni dettagliate e parametri per ognuna delle chiamate. Si tratta di uno strumento ideale per le architetture monolitiche o i servizi in esecuzione in un singolo processo, ma come eseguire il debug quando la chiamata passa attraverso un limite di processo e non è semplicemente un riferimento nello stack locale? 

Qui viene fornita la traccia distribuita.  

La traccia distribuita è l'equivalente degli stack di chiamate per le architetture di microservizi e cloud moderne, con l'aggiunta di un profiler di prestazioni semplicistico. Monitoraggio di Azure offre due esperienze per l'utilizzo dei dati di traccia distribuita. La prima è costituita dalla vista della [diagnostica delle transazioni](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics), che è analoga a uno stack di chiamate con l'aggiunta di una dimensione temporale. La vista della diagnostica delle transazioni offre visibilità su una singola transazione o richiesta ed è utile per individuare la causa principale dei problemi di affidabilità e i colli di bottiglia delle prestazioni per ogni singola richiesta.

Monitoraggio di Azure offre inoltre una vista della [mappa delle applicazioni](https://docs.microsoft.com/azure/application-insights/app-insights-app-map), che aggrega numerose transazioni per mostrare una visualizzazione topologica dell'interazione tra i sistemi, con informazioni sulle prestazioni medie e sulla frequenza degli errori. 

## <a name="how-to-enable-distributed-tracing"></a>Come abilitare la traccia distribuita

L'abilitazione della traccia distribuita tra i servizi di un'applicazione è semplice quanto l'aggiunta dell'agente, dell'SDK o della libreria appropriata a ogni servizio, in base alla lingua in cui è stato implementato il servizio.

## <a name="enabling-via-application-insights-through-auto-instrumentation-or-sdks"></a>Abilitazione tramite Application Insights tramite strumentazione o SDK automatici

Gli agenti Application Insights e/o gli SDK per .NET, .NET Core, Java, Node.js e JavaScript supportano in modo nativo la traccia distribuita. Le istruzioni per l'installazione e la configurazione di ogni versione di Application Insights SDK sono disponibili di seguito:

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)
* [Python](opencensus-python.md)

Dopo avere installato e configurato la versione corretta di Application Insights SDK, le informazioni di traccia vengono raccolte automaticamente per le librerie, le tecnologie e i framework più diffusi da agenti di raccolta delle dipendenze dell'SDK automatici. L'elenco completo delle tecnologie supportate è disponibile nella [documentazione sulla raccolta automatica delle dipendenze](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies).

 È anche possibile tenere traccia manualmente di qualsiasi tecnologia con una chiamata a [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) in [TelemetryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics).

## <a name="enable-via-opencensus"></a>Eseguire l'abilitazione tramite OpenCensus

Oltre che tramite Application Insights SDK, Application Insights supporta la traccia distribuita anche tramite [OpenCensus](https://opencensus.io/). OpenCensus è una singola distribuzione di librerie open source indipendente dal fornitore che fornisce raccolta di metriche e traccia distribuita per i servizi. Permette inoltre alla community open source di abilitare la traccia distribuita con tecnologie diffuse, come Redis, Memcached o MongoDB. [Microsoft collabora al progetto OpenCensus con diversi altri partner cloud e che si occupano di monitoraggio](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

[Python](opencensus-python.md) 

Il sito Web OpenCensus include la documentazione di riferimento delle API per [Python](https://opencensus.io/api/python/trace/usage.html) e [Go](https://godoc.org/go.opencensus.io), oltre che molte guide diverse per l'uso di OpenCensus. 

## <a name="next-steps"></a>Passaggi successivi

* [Guida all'utilizzo di Python con OpenCensus](https://opencensus.io/api/python/trace/usage.html)
* [Mappa delle applicazioni](./../../azure-monitor/app/app-map.md)
* [Monitoraggio delle prestazioni end-to-end](./../../azure-monitor/learn/tutorial-performance.md)

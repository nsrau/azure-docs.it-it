---
title: Traccia distribuita in Azure Application Insights | Microsoft Docs
description: Fornisce informazioni sul supporto di Microsoft per la traccia distribuita tramite la partnership nel progetto OpenCensus
ms.topic: conceptual
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 9eb753981821a6b53d81b1d03e65abf68e0064dc
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539993"
---
# <a name="what-is-distributed-tracing"></a>Che cos'è la traccia distribuita?

L'avvento delle architetture moderne di [microservizi](https://azure.com/microservices) e cloud ha dato luogo a servizi semplici e distribuiti in modo indipendente che consentono di ridurre i costi aumentando la disponibilità e la velocità effettiva. Mentre questi movimenti hanno reso i singoli servizi più facili da comprendere nel suo complesso, hanno reso i sistemi complessivi più difficili da motivare ed eseguire il debug.

Nelle architetture monolitiche, abbiamo usato per eseguire il debug con stack di chiamate. Gli stack di chiamate sono strumenti molto utili per la visualizzazione del flusso di esecuzione (il metodo A ha chiamato il metodo B che ha chiamato il metodo C), insieme a informazioni dettagliate e parametri per ognuna delle chiamate. Si tratta di uno strumento ideale per le architetture monolitiche o i servizi in esecuzione in un singolo processo, ma come eseguire il debug quando la chiamata passa attraverso un limite di processo e non è semplicemente un riferimento nello stack locale? 

Qui viene fornita la traccia distribuita.  

La traccia distribuita è l'equivalente degli stack di chiamate per le architetture di microservizi e cloud moderne, con l'aggiunta di un profiler di prestazioni semplicistico. Monitoraggio di Azure offre due esperienze per l'utilizzo dei dati di traccia distribuita. La prima è costituita dalla vista della [diagnostica delle transazioni](./transaction-diagnostics.md), che è analoga a uno stack di chiamate con l'aggiunta di una dimensione temporale. La vista della diagnostica delle transazioni offre visibilità su una singola transazione o richiesta ed è utile per individuare la causa principale dei problemi di affidabilità e i colli di bottiglia delle prestazioni per ogni singola richiesta.

Monitoraggio di Azure offre inoltre una vista della [mappa delle applicazioni](./app-map.md), che aggrega numerose transazioni per mostrare una visualizzazione topologica dell'interazione tra i sistemi, con informazioni sulle prestazioni medie e sulla frequenza degli errori. 

## <a name="how-to-enable-distributed-tracing"></a>Come abilitare la traccia distribuita

L'abilitazione della traccia distribuita tra i servizi di un'applicazione è semplice quanto l'aggiunta dell'agente, dell'SDK o della libreria appropriata a ogni servizio, in base alla lingua in cui è stato implementato il servizio.

## <a name="enabling-via-application-insights-through-auto-instrumentation-or-sdks"></a>Abilitazione tramite Application Insights tramite strumentazione o SDK automatici

Gli agenti Application Insights e/o gli SDK per .NET, .NET Core, Java, Node.js e JavaScript supportano in modo nativo la traccia distribuita. Le istruzioni per l'installazione e la configurazione di ogni versione di Application Insights SDK sono disponibili di seguito:

* [.NET](../learn/quick-monitor-portal.md)
* [.NET Core](../learn/dotnetcore-quick-start.md)
* [Java](./java-in-process-agent.md)
* [Node.js](../learn/nodejs-quick-start.md)
* [JavaScript](./javascript.md)
* [Python](opencensus-python.md)

Dopo avere installato e configurato la versione corretta di Application Insights SDK, le informazioni di traccia vengono raccolte automaticamente per le librerie, le tecnologie e i framework più diffusi da agenti di raccolta delle dipendenze dell'SDK automatici. L'elenco completo delle tecnologie supportate è disponibile nella [documentazione sulla raccolta automatica delle dipendenze](./auto-collect-dependencies.md).

 È anche possibile tenere traccia manualmente di qualsiasi tecnologia con una chiamata a [TrackDependency](./api-custom-events-metrics.md) in [TelemetryClient](./api-custom-events-metrics.md).

## <a name="enable-via-opencensus"></a>Eseguire l'abilitazione tramite OpenCensus

Oltre che tramite Application Insights SDK, Application Insights supporta la traccia distribuita anche tramite [OpenCensus](https://opencensus.io/). OpenCensus è una singola distribuzione di librerie open source indipendente dal fornitore che fornisce raccolta di metriche e traccia distribuita per i servizi. Permette inoltre alla community open source di abilitare la traccia distribuita con tecnologie diffuse, come Redis, Memcached o MongoDB. [Microsoft collabora al progetto OpenCensus con diversi altri partner cloud e che si occupano di monitoraggio](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

[Python](opencensus-python.md) 

Il sito Web OpenCensus include la documentazione di riferimento delle API per [Python](https://opencensus.io/api/python/trace/usage.html) e [Go](https://godoc.org/go.opencensus.io), oltre che molte guide diverse per l'uso di OpenCensus. 

## <a name="next-steps"></a>Passaggi successivi

* [Guida all'utilizzo di Python con OpenCensus](https://opencensus.io/api/python/trace/usage.html)
* [Mappa delle applicazioni](./../../azure-monitor/app/app-map.md)
* [Monitoraggio delle prestazioni end-to-end](./../../azure-monitor/learn/tutorial-performance.md)

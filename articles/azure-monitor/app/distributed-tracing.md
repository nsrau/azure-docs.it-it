---
title: Traccia distribuita in Azure Application Insights | Microsoft Docs
description: Vengono fornite informazioni sul supporto di Microsoft per la traccia distribuita tramite l'utilità di inoltro locale e la collaborazione al progetto OpenCensus
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 0b51617966ad85037ad29f3e5005a17f66602e01
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677132"
---
# <a name="what-is-distributed-tracing"></a>Che cos'è la traccia distribuita?

L'avvento di architetture di [microservizi](https://azure.com/microservices) e cloud moderne ha contribuito alla nascita di semplici servizi distribuibili in modo indipendente che consentono di ridurre i costi aumentando la disponibilità e la velocità effettiva. Ma sebbene questi cambiamenti abbiano semplificato la comprensione dei singoli servizi, hanno reso più complessi la comprensione e il debug dei sistemi globali.

Nelle architetture monolitiche si esegue in genere il debug con gli stack di chiamate. Gli stack di chiamate sono strumenti molto utili per la visualizzazione del flusso di esecuzione (il metodo A ha chiamato il metodo B che ha chiamato il metodo C), insieme a informazioni dettagliate e parametri per ognuna delle chiamate. Si tratta di uno strumento ideale per le architetture monolitiche o i servizi in esecuzione in un singolo processo, ma come eseguire il debug quando la chiamata passa attraverso un limite di processo e non è semplicemente un riferimento nello stack locale? 

In questo caso entra in gioco la traccia distribuita.  

La traccia distribuita è l'equivalente degli stack di chiamate per le architetture di microservizi e cloud moderne, con l'aggiunta di un profiler di prestazioni semplicistico. Monitoraggio di Azure offre due esperienze per l'utilizzo dei dati di traccia distribuita. La prima è costituita dalla vista della [diagnostica delle transazioni](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics), che è analoga a uno stack di chiamate con l'aggiunta di una dimensione temporale. La vista della diagnostica delle transazioni offre visibilità su una singola transazione o richiesta ed è utile per individuare la causa principale dei problemi di affidabilità e i colli di bottiglia delle prestazioni per ogni singola richiesta.

Monitoraggio di Azure offre inoltre una vista della [mappa delle applicazioni](https://docs.microsoft.com/azure/application-insights/app-insights-app-map), che aggrega numerose transazioni per mostrare una visualizzazione topologica dell'interazione tra i sistemi, con informazioni sulle prestazioni medie e sulla frequenza degli errori. 

## <a name="how-to-enable-distributed-tracing"></a>Come abilitare la traccia distribuita

Per abilitare la traccia distribuita tra i servizi in un'applicazione è sufficiente aggiungere la libreria o l'SDK appropriato a ogni servizio, in base al linguaggio con cui è stato implementato il servizio.

## <a name="enabling-via-application-insights-sdks"></a>Abilitazione tramite Application Insights SDK

Le versioni di Application Insights SDK per .NET, .NET Core, Java, Node.js e JavaScript supportano tutte la traccia distribuita in modo nativo. Le istruzioni per l'installazione e la configurazione di ogni versione di Application Insights SDK sono disponibili di seguito:

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* [Node.js](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)

Dopo avere installato e configurato la versione corretta di Application Insights SDK, le informazioni di traccia vengono raccolte automaticamente per le librerie, le tecnologie e i framework più diffusi da agenti di raccolta delle dipendenze dell'SDK automatici. L'elenco completo delle tecnologie supportate è disponibile nella [documentazione sulla raccolta automatica delle dipendenze](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies).

 È anche possibile tenere traccia manualmente di qualsiasi tecnologia con una chiamata a [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) in [TelemetryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics).

## <a name="enable-via-opencensus"></a>Eseguire l'abilitazione tramite OpenCensus

Oltre che tramite Application Insights SDK, Application Insights supporta la traccia distribuita anche tramite [OpenCensus](https://opencensus.io/). OpenCensus è una singola distribuzione di librerie open source indipendente dal fornitore che fornisce raccolta di metriche e traccia distribuita per i servizi. Permette inoltre alla community open source di abilitare la traccia distribuita con tecnologie diffuse, come Redis, Memcached o MongoDB. [Microsoft collabora al progetto OpenCensus con diversi altri partner cloud e che si occupano di monitoraggio](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

Per aggiungere funzionalità di traccia distribuita a un'applicazione con OpenCensus, prima di tutto [installare e configurare l'utilità di inoltro locale di Application Insights](./../../azure-monitor/app/opencensus-local-forwarder.md). Configurare quindi OpenCensus per inoltrare i dati di traccia distribuita tramite l'utilità di inoltro locale. Sono supportati sia [Python](./../../azure-monitor/app/opencensus-python.md) che [Go](./../../azure-monitor/app/opencensus-go.md).

Il sito Web OpenCensus include la documentazione di riferimento delle API per [Python](https://opencensus.io/api/python/trace/usage.html) e [Go](https://godoc.org/go.opencensus.io), oltre che molte guide diverse per l'uso di OpenCensus. 

## <a name="next-steps"></a>Passaggi successivi

* [Guida all'utilizzo di OpenCensus Python](https://opencensus.io/api/python/trace/usage.html)
* [Mappa delle applicazioni](./../../azure-monitor/app/app-map.md)
* [Monitoraggio delle prestazioni end-to-end](./../../azure-monitor/learn/tutorial-performance.md)

---
title: Monitorare le applicazioni in esecuzione su Funzioni di Azure con Application Insights - Monitoraggio di Azure | Microsoft Docs
description: Monitoraggio di Azure si integra perfettamente con l'applicazione in esecuzione su Funzioni di Azure e consente di monitorare le prestazioni e trovare i problemi con le app in tempi ridotti.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/20/2020
ms.openlocfilehash: a936c77abb9aed5886fae8b2ec4a10bb076b7cb5
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83776297"
---
# <a name="monitoring-azure-functions-with-azure-monitor-application-insights"></a>Monitoraggio di Funzioni di Azure con Application Insights di Monitoraggio di Azure

[Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview) offre l'integrazione predefinita con Azure Application Insights per il monitoraggio delle funzioni. 

Application Insights raccoglie i dati relativi a log, prestazioni ed errori e rileva automaticamente le anomalie delle prestazioni. Application Insights include strumenti di analisi potenti per diagnosticare i problemi e acquisire informazioni sul modo in cui le funzioni vengono utilizzate. Quando si ha visibilità sui dati dell'applicazione, è possibile migliorare continuamente le prestazioni e l'usabilità. È anche possibile usare Application Insights durante lo sviluppo di un progetto locale di app per le funzioni. 

La strumentazione di Application Insights necessaria è incorporata in Funzioni di Azure. L'unico elemento necessario è una chiave di strumentazione valida per connettere l'app per le funzioni a una risorsa Application Insights. La chiave di strumentazione deve essere aggiunta alle impostazioni dell'applicazione quando la risorsa dell'app per le funzioni viene creata in Azure. Se l'app per le funzioni non dispone già di questa chiave, è possibile impostarla manualmente. Per altre informazioni, vedere la sezione sul [monitoraggio di Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitoring?tabs=cmd).

## <a name="distributed-tracing-for-java-applications-public-preview"></a>Traccia distribuita per le applicazioni Java (anteprima pubblica)


> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima pubblica per Funzioni di Azure Java. Per il piano a consumo, presenta un'implicazione di avvio a freddo di 8-9 secondi.

Se le applicazioni sono scritte in Java, è possibile visualizzare dati più completi dalle applicazioni per le funzioni, tra cui richieste, dipendenze, log e metriche. I dati aggiuntivi consentono inoltre di visualizzare e diagnosticare le transazioni end-to-end e visualizzare la mappa delle applicazioni, che aggrega numerose transazioni per mostrare una visualizzazione topologica dell'interazione tra i sistemi, con informazioni sulle prestazioni medie e sulla frequenza degli errori.

La diagnostica end-to-end e la mappa delle applicazioni forniscono visibilità su una singola transazione o richiesta. Insieme, queste due funzionalità sono molto utili per individuare la causa radice dei problemi di affidabilità e dei colli di bottiglia delle prestazioni per le singole richieste.

### <a name="how-to-enable-distributed-tracing-for-java-function-apps"></a>Istruzioni per abilitare la traccia distribuita per le app per le funzioni Java

Passare al pannello di panoramica delle app per le funzioni e andare alle configurazioni. In Impostazioni applicazione fare clic su "+ Nuova impostazione applicazione". Aggiungere le seguenti due impostazioni dell'applicazione con i valori riportati di seguito, quindi fare clic su Salva nell'angolo in alto a sinistra. Operazione completata.

```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

## <a name="next-steps"></a>Passaggi successivi

* Altre istruzioni e informazioni sul [Monitoraggio di Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
* Panoramica della [traccia distribuita](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)
* Possibilità di utilizzo della [Mappa delle applicazioni](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net)
* Informazioni sulle [richieste e dipendenze per le app Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)
* Altre informazioni su [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) e [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)
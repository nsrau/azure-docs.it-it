---
title: Monitorare le applicazioni Docker in Azure Application Insights | Microsoft Docs
description: I contatori delle prestazioni, gli eventi e le eccezioni di Docker possono essere visualizzati in Application Insights, insieme ai dati di telemetria dalle app nei contenitori.
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 27a3083d-d67f-4a07-8f3c-4edb65a0a685
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: b0d98688518acd2157905fda6a9a847763e45ae7
ms.contentlocale: it-it
ms.lasthandoff: 08/17/2017

---
# <a name="monitor-docker-applications-in-application-insights"></a>Monitoraggio di applicazioni Docker in Application Insights
I contatori delle prestazioni e degli eventi del ciclo di vita da contenitori [Docker](https://www.docker.com/) possono essere disegnati in Application Insights. Installare l'immagine di [Application Insights](app-insights-overview.md) in un contenitore dell'host. Verranno visualizzati i contatori delle prestazioni per l'host e per le altre immagini.

Con Docker si distribuiscono le app in contenitori leggeri completi di tutte le dipendenze. Verranno eseguite su tutti i computer host che eseguono un motore Docker.

Quando si esegue l'[immagine di Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) nell'host Docker, si ottengono questi vantaggi:

* I dati di telemetria del ciclo di vita su tutti i contenitori che sono in esecuzione nell'host - avvio, arresto e così via.
* I contatori delle prestazioni per tutti i contenitori. CPU, memoria, utilizzo della rete e altro ancora.
* Se si [è installato Application Insights SDK per Java](app-insights-java-live.md) nelle app in esecuzione nei contenitori, tutti i dati di telemetria di tali app avranno proprietà aggiuntive che identificano il contenitore e il computer host. Ad esempio, se si dispone di istanze di un'app in esecuzione in più host, è possibile filtrare facilmente dall'host i dati di telemetria dell'app.

![esempio](./media/app-insights-docker/00.png)

## <a name="set-up-your-application-insights-resource"></a>Configurare la risorsa di Application Insights
1. Accedere al [portale di Microsoft Azure](https://azure.com) e aprire la risorsa di Application Insights per l'app o [crearne una nuova](app-insights-create-new-resource.md). 
   
    *Quale risorsa si deve usare?* Se le app in esecuzione nell'host sono state sviluppate da un altro utente, è necessario [creare una nuova risorsa di Application Insights](app-insights-create-new-resource.md). Da questo punto vengono visualizzati e analizzati i dati di telemetria. Selezionare "Generale" per il tipo di app.
   
    Ma se si è lo sviluppatore delle App, l’augurio è quello di aver [aggiunto SDK di Application Insights](app-insights-java-live.md) a ciascuna di esse. Se sono tutti effettivamente componenti di una singola applicazione business, è possibile configurare tutti gli elementi per inviare i dati di telemetria a una risorsa e si utilizzerà la stessa risorsa per visualizzare i dati delle prestazioni e del ciclo di vita di Docker. 
   
    Un terzo scenario è che l’utente abbia sviluppato la maggior parte delle app, ma utilizzi risorse separate per visualizzare i dati di telemetria. In tal caso, probabilmente si intende anche creare una risorsa separata per i dati di Docker. 
2. Aggiungere il riquadro Docker: scegliere **Aggiungi riquadro**, trascinare il riquadro Docker dalla raccolta e quindi fare clic su **Fine**. 
   
    ![esempio](./media/app-insights-docker/03.png)
3. Fare clic sull'elenco a discesa **Informazioni di base** e copiare la chiave di strumentazione. Questa chiave serve per indicare all'SDK dove inviare i dati di telemetria.

    ![esempio](./media/app-insights-docker/02-props.png)

Lasciare aperta la finestra del browser, a cui si tornerà a breve per esaminare la telemetria.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Eseguire il monitoraggio di Application Insights sull'host
Ora che è disponibile una posizione in cui visualizzare la telemetria, è possibile configurare l'app dei contenitori che la raccoglierà e invierà.

1. Connettersi all'host Docker. 
2. Modificare la chiave di strumentazione in questo comando, e poi eseguirlo:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Solo un'immagine di Application Insights è obbligatoria per ogni host Docker. Se l'applicazione viene distribuita su più host di Docker, ripetere il comando in ogni host.

## <a name="update-your-app"></a>Aggiornamento dell'app
Se l'applicazione è instrumentata con [Application Insights SDK per Java](app-insights-java-get-started.md), aggiungere la riga seguente nel file ApplicationInsights.xml nel progetto, sotto l'elemento `<TelemetryInitializers>`:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Ciò aggiunge informazioni di Docker, come ad esempio il contenitore e l’id dell’host, ad ogni elemento di telemetria inviato dall'app.

## <a name="view-your-telemetry"></a>Visualizzare i dati di telemetria
Tornare alla risorsa Application Insights nel portale di Azure.

Fare clic nel riquadro Docker.

In breve verranno visualizzati i dati in arrivo dall’app Docker, soprattutto se nel motore Docker sono in esecuzione altri contenitori.

Di seguito sono elencate alcune delle visualizzazioni disponibili.

### <a name="perf-counters-by-host-activity-by-image"></a>Contatori delle prestazioni per host, attività per immagine
![esempio](./media/app-insights-docker/10.png)

![esempio](./media/app-insights-docker/11.png)

Fare clic su un nome dell’host o dell’immagine per altri dettagli.

Per personalizzare la visualizzazione, fare clic su un grafico o sull'intestazione della griglia oppure usare Aggiungi grafico. 

[Altre informazioni su Esplora metriche](app-insights-metrics-explorer.md)

### <a name="docker-container-events"></a>Eventi del contenitore Docker
![esempio](./media/app-insights-docker/13.png)

Per analizzare i singoli eventi, fare clic su [Cerca](app-insights-diagnostic-search.md). Eseguire una ricerca e applicare filtri per trovare gli eventi desiderati. Per ottenere altri dettagli, fare clic su qualsiasi evento.

### <a name="exceptions-by-container-name"></a>Eccezioni per nome del contenitore
![esempio](./media/app-insights-docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Contesto Docker aggiunto ai dati di telemetria dell’app
Richiesta di dati di telemetria inviata dall'applicazione instrumentata con SDK di Application Insights, arricchita con contesto Docker:

![esempio](./media/app-insights-docker/16.png)

Tempo di elaborazione e contatori delle prestazioni di memoria disponibile, arricchiti e raggruppati per nome del contenitore Docker:

![esempio](./media/app-insights-docker/15.png)

## <a name="q--a"></a>Domande e risposte
*Quali sono i vantaggi di Application Insights rispetto a Docker?*

* Suddivisione dettagliata dei contatori delle prestazioni in base al contenitore e all'immagine.
* Integrazione dei dati dell'app e dei contenitori in un unico dashboard.
* [Esportare la telemetria](app-insights-export-telemetry.md) per ulteriori analisi in un database, in Power BI o in un altro dashboard.

*Come ottenere la telemetria direttamente dall'app?*

* Installare Application Insights SDK nell'app. Per informazioni, vedere [App Web Java](app-insights-java-get-started.md) e [App Web Windows](app-insights-asp-net.md).

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Passaggi successivi

* [Application Insights per Java](app-insights-java-get-started.md)
* [Application Insights per Node.js](app-insights-nodejs.md)
* [Application Insights per ASP.NET](app-insights-asp-net.md)


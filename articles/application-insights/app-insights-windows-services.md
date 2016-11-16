---
title: Application Insights per i servizi di Windows e i ruoli di lavoro | Microsoft Docs
description: "Aggiungere manualmente Application Insights SDK all&quot;applicazione ASP.NET per analizzare utilizzo, disponibilità e prestazioni."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 106ba99b-b57a-43b8-8866-e02f626c8190
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/01/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: aac35e524759d5f4356e9a9e386f658e9003d2ca


---
# <a name="manually-configure-application-insights-for-aspnet-4-applications"></a>Configurare manualmente Application Insights per applicazioni ASP.NET 4
[Application Insights](app-insights-overview.md) è uno strumento estendibile che consente agli sviluppatori Web di monitorare le prestazioni e l'uso dell'applicazione attiva. È possibile configurarlo manualmente per monitorare i servizi Windows, i ruoli di lavoro e altre applicazioni ASP.NET. Per le app Web, la configurazione manuale è un'alternativa alla [configurazione automatica](app-insights-asp-net.md) offerta da Visual Studio.

![Grafici di monitoraggio delle prestazioni di esempio](./media/app-insights-windows-services/10-perf.png)

#### <a name="before-you-start"></a>Prima di iniziare
È necessario:

* Una sottoscrizione a [Microsoft Azure](http://azure.com). Se il team o l'organizzazione ha una sottoscrizione di Azure, il proprietario potrà aggiungere l'utente alla sottoscrizione usando il rispettivo [account Microsoft](http://live.com).
* Visual Studio 2013 o versione successiva.

## <a name="a-nameadda1-create-an-application-insights-resource"></a><a name="add"></a>1. Creare una risorsa Application Insights
Accedere al [portale di Azure](https://portal.azure.com/)e creare una nuova risorsa di Application Insights. Scegliere ASP.NET come tipo di applicazione.

![Fare clic su Nuovo, Application Insights](./media/app-insights-windows-services/01-new-asp.png)

Una [risorsa](app-insights-resources-roles-access-control.md) in Azure è un'istanza di un servizio. In questa risorsa la telemetria dell'app verrà analizzata e visualizzata.

La scelta del tipo di applicazione imposta il contenuto predefinito dei pannelli delle risorse e le proprietà visibili in [Esplora metriche](app-insights-metrics-explorer.md).

#### <a name="copy-the-instrumentation-key"></a>Eseguire una copia della chiave di strumentazione
La chiave identifica la risorsa e verrà installata subito nell'SDK per indirizzare i dati alla risorsa.

![Fare clic su Proprietà, selezionare il tasto e premere CTRL+C](./media/app-insights-windows-services/02-props-asp.png)

La procedura appena effettuata per creare una nuova risorsa è un buon modo di iniziare a monitorare qualsiasi applicazione. È ora possibile inviare dati a esso.

## <a name="a-namesdka2-install-the-sdk-in-your-application"></a><a name="sdk"></a>2. Installare l'SDK nell'applicazione
L'installazione e la configurazione di Application Insights SDK varia a seconda della piattaforma in cui si sta lavorando. Per le applicazioni ASP.NET, è facile.

1. In Visual Studio modificare i pacchetti NuGet del progetto dell'app Web.
   
    ![Fare clic con il pulsante destro del mouse sul progetto e selezionare Gestisci pacchetti NuGet](./media/app-insights-windows-services/03-nuget.png)
2. Installare Application Insights SDK per app Web.
   
    ![Cercare "Application Insights"](./media/app-insights-windows-services/04-ai-nuget.png)
   
    *È possibile usare altri pacchetti?*
   
    Sì. Se si desidera usare l'API per inviare i propri dati di telemetria, scegliere l'API di base (Microsoft.ApplicationInsights). Il pacchetto di Windows Server include automaticamente l'API di base e in più altri pacchetti, come ad esempio la raccolta dei contatori delle prestazioni e il monitoraggio della dipendenza. 

#### <a name="to-upgrade-to-future-sdk-versions"></a>Per eseguire l'aggiornamento a future versioni di SDK
Si rilascerà una nuova versione del SDK di tanto in tanto.

Per eseguire l'aggiornamento a una [nuova versione dell'SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases/), riaprire Gestione pacchetti NuGet e filtrare i pacchetti installati. Selezionare **Microsoft.ApplicationInsights.Web** e scegliere **Aggiorna**.

Se sono state eseguite tutte le personalizzazioni apportate al file ApplicationInsights.config, salvarne una copia prima di eseguire l'aggiornamento e, successivamente, unire le modifiche nella nuova versione.

## <a name="3-send-telemetry"></a>3. Inviare dati di telemetria
**Se è installato solo il pacchetto di API di base:**

* Impostare la chiave di strumentazione nel codice, ad esempio in `main()`: 
  
    `TelemetryConfiguration.Active.InstrumentationKey = "` *nome della chiave* `";` 
* [Scrivere dati di telemetria usando l'API](app-insights-api-custom-events-metrics.md#ikey).

**Se sono installati altri pacchetti di Application Insights** è possibile, se si preferisce, usare il file config per impostare la chiave di strumentazione:

* Modificare ApplicationInsights.config (che è stato aggiunto dall'installazione di NuGet). Inserire questo comando immediatamente prima del tag di chiusura:
  
    `<InstrumentationKey>` *chiave di strumentazione copiata* `</InstrumentationKey>`
* Verificare che le proprietà di ApplicationInsights.config in Esplora soluzioni siano impostate su **= Contenuto, Copia nella directory di output = Copia**.

## <a name="a-nameruna-run-your-project"></a><a name="run"></a> Eseguire il progetto
Eseguire l'applicazione premendo **F5** e provarla aprendo pagine diverse per generare alcuni dati di telemetria.

In Visual Studio verrà visualizzato il conteggio degli eventi che sono stati inviati.

![Conteggio degli eventi in Visual Studio](./media/app-insights-windows-services/appinsights-09eventcount.png)

## <a name="a-namemonitora-view-your-telemetry"></a><a name="monitor"></a> Visualizzare i dati di telemetria
Tornare al [portale di Azure](https://portal.azure.com/) e passare alla risorsa Application Insights.

Cercare i dati nei grafici Panoramica. All'inizio si vedranno solo uno o due punti. Ad esempio:

![Fare clic per visualizzare altri dati.](./media/app-insights-windows-services/12-first-perf.png)

Fare clic su qualsiasi grafico per visualizzare metriche più dettagliate. [Altre informazioni sulle metriche.](app-insights-web-monitor-performance.md)

#### <a name="no-data"></a>Dati non visualizzati
* Usare l'applicazione, aprendo pagine diverse in modo da generare alcuni dati di telemetria.
* Aprire il riquadro [Ricerca](app-insights-diagnostic-search.md) per visualizzare i singoli eventi. Talvolta agli eventi ci vuole un po' più di tempo per passare attraverso la pipeline delle metriche.
* Attendere alcuni secondi e fare clic su **Aggiorna**. I grafici si aggiornano periodicamente, ma è possibile aggiornare manualmente se si è in attesa di alcuni dati da visualizzare.
* Vedere [Domande su Application Insights per ASP.NET](app-insights-troubleshoot-faq.md).

## <a name="publish-your-app"></a>Pubblicare l'app
Distribuire ora l'applicazione nel server o in Azure e osservare l'accumulo dei dati.

![Utilizzare Visual Studio per pubblicare l'app](./media/app-insights-windows-services/15-publish.png)

Quando si esegue la modalità debug, la telemetria viene velocizzata nella pipeline, quindi i dati vengono visualizzati in pochi secondi. Quando si distribuisce l'applicazione nella configurazione Release, i dati si accumulano più lentamente.

#### <a name="no-data-after-you-publish-to-your-server"></a>Nessun dato dopo la pubblicazione nel server?
Aprire le seguenti porte per il traffico in uscita nel firewall del server:

* `dc.services.visualstudio.com:443`
* `f5.services.visualstudio.com:443`

#### <a name="trouble-on-your-build-server"></a>Problemi del server di compilazione
Vedere [questa sezione sulla risoluzione dei problemi](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).

> [!NOTE]
> Se l'app genera molti dati di telemetria (e si usa ASP.NET SDK versione 2.0.0-beta3 o successiva), il modulo di campionamento adattivo riduce automaticamente il volume che viene inviato al portale inviando solo una frazione rappresentativa di eventi. Tuttavia, gli eventi che fanno parte della stessa richiesta verranno selezionati o deselezionati come gruppo, per rendere possibile lo spostamento tra eventi correlati. 
> [Informazioni sul campionamento.](app-insights-sampling.md)
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere altri dati di telemetria](app-insights-asp-net-more.md) per un quadro completo a 360 gradi dell'applicazione.




<!--HONumber=Nov16_HO2-->



---
title: Configurare l'analisi di app Web per ASP.NET con Azure Application Insights | Microsoft Docs
description: "Configurare l'analisi delle prestazioni, della disponibilità e dell'utilizzo per un sito Web ASP.NET, ospitato in locale o in Azure."
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/15/2017
ms.author: bwren
ms.openlocfilehash: cb247ee68da88265f7c51258644064463d44f8b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Installare Application Insights per un sito Web ASP.NET

Questa procedura consente di configurare un'app Web ASP.NET per l'invio di dati di telemetria al servizio [Azure Application Insights](app-insights-overview.md). È valida per le app ASP.NET ospitate nel server IIS o nel cloud. Offre grafici e un linguaggio di query avanzato che permettono di comprendere le prestazioni dell'app e il suo utilizzo da parte degli utenti, oltre ad avvisi automatici in caso di errori o problemi di prestazioni. Molti sviluppatori trovano utili queste funzionalità così come sono, ma è anche possibile estendere e personalizzare i dati di telemetria, se necessario.

Il programma di installazione richiede pochi clic in Visual Studio. Per evitare addebiti è possibile limitare il volume dei dati di telemetria. In questo modo è possibile provare le funzionalità ed eseguire il debug o monitorare un sito con un numero di utenti limitato. Se si decide di monitorare l'intero sito di produzione, è facile aumentare il limite in un secondo momento.

## <a name="before-you-start"></a>Prima di iniziare
Sono necessari:

* Visual Studio 2013 Update 3 o versioni successive. È preferibile una versione successiva.
* Una sottoscrizione a [Microsoft Azure](http://azure.com). Se il team o l'organizzazione ha una sottoscrizione di Azure, il proprietario potrà aggiungere l'utente alla sottoscrizione usando il rispettivo [account Microsoft](http://live.com).

Se si è interessati, vedere gli argomenti alternativi seguenti:

* [Strumentazione di un'app Web in fase di esecuzione](app-insights-monitor-performance-live-website-now.md)
* [Servizi cloud di Azure](app-insights-cloudservices.md)

## <a name="ide"></a>Passaggio 1: Aggiungere Application Insights SDK

Fare clic con il pulsante destro del mouse sul progetto dell'app Web in Esplora soluzioni e scegliere **Aggiungi** > **Application Insights Telemetry** oppure **Configura Application Insights**.

![Screenshot di Esplora soluzioni con le opzioni Aggiungi e Application Insights Telemetry evidenziate](./media/app-insights-asp-net/appinsights-03-addExisting.png)

In Visual Studio 2015, un'opzione per l'aggiunta di Application Insights è disponibile anche nella finestra di dialogo Nuovo progetto.

Passare alla pagina di configurazione di Application Insights:

![Screenshot della pagina Registra l'app con Application Insights](./media/app-insights-asp-net/visual-studio-register-dialog.png)

**a.** Selezionare l'account e la sottoscrizione usati per accedere ad Azure.

**b.** Selezionare la risorsa in Azure in cui si vogliono visualizzare i dati dell'app. In genere:

* Usare un'[unica risorsa per diversi componenti](app-insights-monitor-multi-role-apps.md) di una singola applicazione. 
* Creare risorse separate per applicazioni non correlate.
 
Se si vuole impostare il gruppo di risorse o la località in cui verranno archiviati i dati, fare clic su **Configura impostazioni**. I gruppi di risorse vengono usati per controllare l'accesso ai dati. Se si hanno diverse app che fanno parte dello stesso sistema, ad esempio, è possibile inserire i relativi dati di Application Insights nello stesso gruppo di risorse.

**c.** Impostare un tetto massimo al limite del volume di dati gratuito, per evitare eventuali addebiti. Application Insights è gratuito fino a un determinato volume di dati di telemetria. Dopo aver creato la risorsa, è possibile modificare la selezione nel portale aprendo **Funzionalità + prezzi** > **Gestione del volume dati** > **Limite di utilizzo volume giornaliero**.

**d.** Fare clic su **Registra** per proseguire e configurare Application Insights per l'app Web. I dati di telemetria verranno inviati al [portale di Azure](https://portal.azure.com), sia durante il debug che dopo la pubblicazione dell'app.

**e.** Per non inviare i dati di telemetria al portale durante il debug, è possibile aggiungere Application Insights SDK all'app senza configurare una risorsa nel portale. Si potranno visualizzare i dati di telemetria in Visual Studio durante il debug. Successivamente, è possibile tornare a questa pagina di configurazione oppure attendere di aver distribuito l'app e quindi [attivare la telemetria in fase di esecuzione](app-insights-monitor-performance-live-website-now.md).


## <a name="run"></a>Passaggio 2: Eseguire l'app
Eseguire l'app con F5. Aprire pagine diverse per generare alcuni dati di telemetria.

In Visual Studio verrà visualizzato il conteggio degli eventi che sono stati registrati.

![Screenshot di Visual Studio. Il pulsante Application Insights viene visualizzato durante il debug.](./media/app-insights-asp-net/54.png)

## <a name="step-3-see-your-telemetry"></a>Passaggio 3: Visualizzare i dati di telemetria
È possibile visualizzare i dati di telemetria in Visual Studio o nel portale Web di Application Insights. Cercare i dati di telemetria in Visual Studio per eseguire il debug dell'app. Monitorare le prestazioni e l'utilizzo nel portale Web quando il sistema è attivo. 

### <a name="see-your-telemetry-in-visual-studio"></a>Visualizzare i dati di telemetria in Visual Studio

In Visual Studio aprire la finestra di Application Insights. Fare clic sul pulsante **Application Insights** oppure fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni, scegliere **Application Insights** e quindi fare clic su **Cerca nei dati di telemetria attivi**.

Nella finestra Ricerca di Application Insights di Visual Studio esaminare i dati di telemetria generati sul lato server dell'app nella visualizzazione **Dati di Dati di telemetria della sessione di debug**. Sperimentare i filtri e fare clic su qualsiasi evento per visualizzare altri dettagli.

![Screenshot della visualizzazione Dati di Dati di telemetria della sessione di debug nella finestra di Application Insights.](./media/app-insights-asp-net/55.png)

> [!NOTE]
> Se non vengono visualizzati dati, verificare che l'intervallo di tempo sia corretto e fare clic sull'icona di ricerca.

[Uso di Application Insights in Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Visualizzare i dati di telemetria nel portale Web

Se non si è scelto di installare solo l'SDK, è possibile visualizzare i dati di telemetria anche nel portale Web di Application Insights. Il portale offre un maggior numero di grafici, strumenti di analisi e viste di più componenti rispetto a Visual Studio. Nel portale sono anche disponibili avvisi.

Aprire la risorsa Application Insights. Accedere al [portale di Azure](https://portal.azure.com/) per cercarla o fare clic con il pulsante destro del mouse sul progetto in Visual Studio e scegliere la risorsa.

![Screenshot di Visual Studio che mostra come aprire il portale di Application Insights](./media/app-insights-asp-net/appinsights-04-openPortal.png)

> [!NOTE]
> Se viene visualizzato un errore di accesso, è possibile che si abbiano più set di credenziali Microsoft e che l'accesso sia stato eseguito con il set sbagliato. Nel portale disconnettersi e accedere nuovamente.

Nel portale verrà visualizzata la telemetria dell'app.

![Screenshot della pagina Panoramica di Application Insights](./media/app-insights-asp-net/66.png)

Per visualizzare altri dettagli nel portale, fare clic su qualsiasi riquadro o grafico.

[Altre informazioni sull'uso di Application Insights nel portale di Azure](app-insights-dashboards.md).

## <a name="step-4-publish-your-app"></a>Passaggio 4: Pubblicare l'app
Pubblicare l'app nel server IIS o in Azure. Verificare in [Flusso metriche attive](app-insights-metrics-explorer.md#live-metrics-stream) che tutto funzioni correttamente.

La telemetria viene creata nel portale di Application Insights, in cui è possibile monitorare le metriche, eseguire ricerche sui dati di telemetria e configurare i [dashboard](app-insights-dashboards.md), nonché usare l'avanzato [linguaggio di query di Log Analytics](https://docs.loganalytics.io/) per analizzare l'utilizzo e le prestazioni o trovare eventi specifici.

È anche possibile continuare ad analizzare i dati di telemetria in [Visual Studio](app-insights-visual-studio.md) con strumenti come la ricerca diagnostica e le [tendenze](app-insights-visual-studio-trends.md).

> [!NOTE]
> Se la quantità di dati di telemetria inviata dall'app sta per raggiungere le [limitazioni](app-insights-pricing.md#limits-summary), viene attivato il [campionamento](app-insights-sampling.md) automatico. Il campionamento riduce la quantità di dati di telemetria inviata dall'app mantenendo i dati correlati per scopi diagnostici.
>
>

## <a name="land"></a> Le impostazioni sono state completate.

Congratulazioni. Il pacchetto Application Insights è stato installato nell'app e configurato per l'invio di dati di telemetria al servizio Application Insights in Azure.

![Diagramma dello spostamento dei dati di telemetria](./media/app-insights-asp-net/01-scheme.png)

La risorsa di Azure che riceve i dati di telemetria dell'app è identificata da una *chiave di strumentazione*, disponibile nel file ApplicationInsights.config.


## <a name="upgrade-to-future-sdk-versions"></a>Eseguire l'aggiornamento alle versioni future dell'SDK
Per eseguire l'aggiornamento a una [nuova versione dell'SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), aprire di nuovo **Gestione pacchetti NuGet** e filtrare i pacchetti installati. Selezionare **Microsoft.ApplicationInsights.Web** e scegliere **Aggiorna**.

Se sono state apportate personalizzazioni a ApplicationInsights.config, salvarne una copia prima di eseguire l'aggiornamento. Successivamente, unire le modifiche nella nuova versione.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Passaggi successivi

### <a name="more-telemetry"></a>Altri dati di telemetria

* **[Dati sul browser e sul caricamento di pagine](app-insights-javascript.md)**: inserire un frammento di codice nelle pagine Web.
* **[Ottenere un monitoraggio più dettagliato di dipendenze ed eccezioni](app-insights-monitor-performance-live-website-now.md)**: installare Status Monitor nel server.
* **[Scrivere codice per gli eventi personalizzati](app-insights-api-custom-events-metrics.md)**: ottenere conteggi, orari o misurazioni delle azioni utente.
* **[Ottenere dati di log](app-insights-asp-net-trace-logs.md)**: correlare i dati di log con i dati di telemetria.

### <a name="analysis"></a>Analisi

* **[Uso di Application Insights in Visual Studio](app-insights-visual-studio.md)**<br/>Include informazioni su debug con telemetria, ricerca diagnostica e drill-through nel codice.
* **[Uso del portale Application Insights](app-insights-dashboards.md)**<br/> Include informazioni su dashboard, strumenti avanzati di diagnostica e di analisi, avvisi, mappa attiva delle dipendenze dell'applicazione ed esportazione dei dati di telemetria.
* **[Analytics](app-insights-analytics-tour.md)**: linguaggio di query avanzato.

### <a name="alerts"></a>Avvisi

* [Test di disponibilità](app-insights-monitor-web-app-availability.md): creare test per verificare che il sito sia visibile sul Web.
* [Diagnostica intelligente](app-insights-proactive-diagnostics.md): questi test vengono eseguiti automaticamente e non è quindi necessario effettuare alcuna operazione per configurarli. Se l'app ha una frequenza insolita di richieste non riuscite, verrà comunicato automaticamente.
* [Avvisi per le metriche](app-insights-alerts.md): impostare questi avvisi per essere avvertiti se una metrica supera una soglia. È possibile impostarli nelle metriche personalizzate di cui si scrive il codice nell'app.

### <a name="automation"></a>Automazione

* [Automatizzare la creazione di risorse di Application Insights](app-insights-powershell.md)

---
title: Monitorare un&quot;app Web | Microsoft Docs
description: Informazioni su come configurare il monitoraggio nell&quot;app Web
services: App-Service
keywords: 
author: btardif
ms.author: byvinyal
ms.date: 04/04/2017
ms.topic: article
ms.service: app-service-web
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 63bfc6922de224f56186003991f4a51d8f99ed35
ms.lasthandoff: 04/15/2017

---
# <a name="monitor-app-service"></a>Monitorare il servizio app
Questa esercitazione descrive come usare gli strumenti della piattaforma predefiniti per monitorare ed eseguire la diagnostica dell'app ospitata nel servizio app. 

## <a name="in-this-tutorial"></a>Contenuto dell'esercitazione

1. [Esplora processi](#explorer)
    - Ottenere informazioni dettagliate sull'app in esecuzione nelle istanze del piano di servizio app
1. [Metriche del servizio app](#metrics) 
   - Informazioni su come monitorare l'app usando i grafici predefiniti
   - Configurare i grafici in base alle proprie esigenze
   - Creare un dashboard personalizzato aggiungendo i grafici personalizzati
1. [Configurare gli avvisi](#alerts)
    - Informazioni su come configurare gli avvisi per l'app e il piano di servizio app
1. [App Service Companion](#Companion)
    - Monitorare e risolvere i problemi relativi all'app usando un dispositivo mobile.
1. [Configurare la registrazione](#logging)
    - Informazioni su come raccogliere i log del server e delle applicazioni.
    - Informazioni sui diversi percorsi di archiviazione dei log e su come accedervi.
1. [Streaming dei log](#streaming)
    - Usare i log in streaming per visualizzare i log dell'applicazione e WC3 generati.
1. [Debug remoto](#remote)
    - Usare Visual Studio per eseguire il debug in modalità remota del progetto in esecuzione nel servizio app.
1. [Diagnostica e risoluzione dei problemi](#diagnose)
    - Identificare i problemi dell'applicazione e ottenere informazioni su come risolverli.
1. [Application Insights](#insights)
    - Profilatura e monitoraggio avanzati per l'app

## <a name="before-you-begin"></a>Prima di iniziare

- Per monitorare e seguire la procedura descritta è necessaria un'app Web. 
    - È possibile creare un'applicazione seguendo la procedura descritta nell'esercitazione [Creare un'app ASP.NET in Azure con un database SQL](app-service-web-tutorial-dotnet-sqldatabase.md).

- Se si vuole provare il **Debug remoto** dell'applicazione è necessario Visual Studio. 
    - Se Visual Studio 2017 non è ancora installato, è possibile scaricare e usare la versione gratuita [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). 
    - Durante l'installazione di Visual Studio abilitare **Sviluppo di Azure**.

## <a name="explorer"></a> Passaggio 1: Esplora processi

Esplora processi è uno strumento che consente di visualizzare informazioni dettagliate sul funzionamento interno del piano di servizio app.

Usare **Esplora processi** per:

- Enumerare tutti i processi nelle diverse istanze del piano di servizio app.
- Eseguire il drill-down e visualizzare gli handle e i moduli associati a ogni processo. 
- Visualizzare il numero di CPU, working set e thread a livello di processo per identificare i processi con eccessivo tempo di esecuzione
- Trovare gli handle di file aperti e terminare l'istanza di un processo specifico.

Esplora processi è disponibile in **Monitoraggio** > **Esplora processi**.

![Esplora processi](media/app-service-web-tutorial-monitoring/app-service-monitor-processexplorer.png)

## <a name="metrics"></a> Passaggio 2: visualizzare le metriche del servizio app
Le **metriche** offrono informazioni dettagliate sull'app Web e sulle interazioni con gli utenti e la piattaforma.

È possibile usare le metriche per ottenere informazioni su:
- il numero di risorse usate dall'app
- il volume di traffico dell'app
- le richieste o gli errori generali
- il volume dei dati in ingresso e in uscita

Per tutte le app ospitate nel servizio app è necessario monitorare l'app Web e il piano di servizio app.

- Le metriche **App** visualizzano informazioni sulle richieste HTTP e gli errori e sul tempo medio di risposta.
- Le metriche **Piano di servizio app** visualizzano informazioni sull'uso delle risorse.

Il portale di Azure consente di esaminare visivamente in modo rapido le metriche dell'app usando **Monitoraggio di Azure**.

- Passare al pannello **Panoramica** dell'app che si vuole monitorare.

![Monitorare un'app](media/app-service-web-tutorial-monitoring/app-service-monitor.png)

- È possibile visualizzare le metriche dell'app in un **riquadro Monitoraggio**.
- Fare clic sul riquadro per modificare e configurare le metriche da visualizzare e l'intervallo di tempo di visualizzazione.

![Configurare un grafico](media/app-service-web-tutorial-monitoring/app-service-monitor-configure.png)

- È possibile aggiungere grafici personalizzati nel dashboard per un accesso semplice e rapido.

![Aggiungere un grafico](media/app-service-web-tutorial-monitoring/app-service-monitor-pin.png)

> [!TIP]
> Per informazioni sul monitoraggio di Azure usare i collegamenti seguenti:
> - [Introduzione al monitoraggio di Azure](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [Metriche di Azure](..\monitoring-and-diagnostics\monitoring-overview-metrics.md)
> - [Metriche supportate con il monitoraggio di Azure](..\monitoring-and-diagnostics\monitoring-supported-metrics.md#microsoftwebsites-including-functions)
> - [Dashboard del portale di Azure](..\azure-portal\azure-portal-dashboards.md)

## <a name="alerts"></a> Passaggio 3: configurare gli avvisi

Gli **avvisi** consentono di automatizzare il monitoraggio dell'applicazione.

Usare gli avvisi per ricevere una notifica quando vengono rilevate condizioni specifiche che hanno effetto sull'app.

Per creare un avviso:
- Passare al pannello **Panoramica** dell'app che si vuole monitorare.
- Dal menu passare a **Monitoraggio** > **Avvisi**
- Selezionare **[+] Aggiungi avviso**
- Configurare l'avviso in base alle proprie esigenze.

![Avvisi](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts.png)

> [!TIP]
> Per informazioni sugli avvisi di Azure usare i collegamenti seguenti:
> - [Cosa sono gli avvisi in Microsoft Azure?](..\monitoring-and-diagnostics\monitoring-overview-alerts.md)
> - [Eseguire operazioni sulle metriche](..\monitoring-and-diagnostics\monitoring-overview.md)

## <a name="companion"></a> Passaggio 4: App Service Companion
**App Service Companion** consente di monitorare l'app con un'esperienza nativa nel dispositivo mobile iOS o Android.

Usare App Service Companion per:
- Rivedere le metriche dell'applicazione
- Rivedere ed eseguire azioni su avvisi e suggerimenti dell'applicazione
- Eseguire operazioni di base per la risoluzione dei problemi (esplorare, avviare, arrestare, riavviare l'app)
- Ottenere notifiche push per gli eventi critici.

![App Service Companion](media/app-service-web-tutorial-monitoring/app-service-companion.png)

[![App Service Companion in App Store](media/app-service-web-tutorial-monitoring/app-service-companion-appStore.png)](https://itunes.apple.com/app/azure-app-service-companion/id1146659260)
[![App Service Companion in Google Play](media/app-service-web-tutorial-monitoring/app-service-companion-googlePlay.png)](https://play.google.com/store/apps/details?id=azureApps.AzureApps)

È possibile installare App Service Companion da [App Store](https://itunes.apple.com/app/azure-app-service-companion/id1146659260) o [Google Play](https://play.google.com/store/apps/details?id=azureApps.AzureApps)

## <a name="logging"></a> Passaggio 5: registrazione
La registrazione consente di raccogliere i log di **diagnostica dell'applicazione** e di **diagnostica del server Web** per l'app Web.

Usare i log di diagnostica per comprendere il comportamento dell'applicazione, risolvere i problemi e individuare le condizioni di errore.

### <a name="application-diagnostics"></a>Diagnostica applicazioni
Diagnostica applicazioni consente di acquisire le tracce prodotte dall'applicazione durante il runtime. 

Per abilitare la registrazione delle applicazioni:

- Passare a **Monitoraggio** > **Log di diagnostica**. 
- Abilitare la registrazione delle applicazioni usando i controlli.

I log dell'applicazione possono essere archiviati nel file system dell'app Web o inseriti nell'archiviazione BLOB.

> [!TIP]
> Per gli scenari di produzione è consigliabile usare l'archiviazione BLOB

![Monitorare un'app](media/app-service-web-tutorial-monitoring/app-service-monitor-applogs.png)

In ASP.NET è possibile registrare le tracce dell'applicazione usando la classe [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx) per generare eventi che vengono acquisiti dall'infrastruttura dei log. È anche possibile specificare la gravità della traccia per filtrare i risultati in modo più semplice.

```csharp
public ActionResult Delete(Guid? id)
{
    System.Diagnostics.Trace.TraceInformation("GET /Todos/Delete/" + id);
    if (id == null)
    {
        System.Diagnostics.Trace.TraceError("/Todos/Delete/ failed, ID is null");
        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
    }
    Todo todo = db.Todoes.Find(id);
    if (todo == null)
    {
        System.Diagnostics.Trace.TraceWarning("/Todos/Delete/ failed, ID: " + id + " could not be found");
        return HttpNotFound();
    }
    System.Diagnostics.Trace.TraceInformation("GET /Todos/Delete/" + id + "completed successfully");
    return View(todo);
}
```

> [!IMPORTANT]
> L'abilitazione della registrazione ha effetto sulle prestazioni dell'applicazione e l'uso delle risorse. Per gli scenari di produzione è consigliabile usare i log degli errori. Abilitare una registrazione più dettagliata solo durante l'analisi dei problemi.

 ### <a name="web-server-diagnostics"></a>Diagnostica del server Web
Il servizio app consente di raccogliere tre tipi diversi di log del server:

- **Registrazione del server Web** 
    - Informazioni sulle transazioni HTTP che usano il [formato di file di log esteso W3C](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). 
    - Consente di determinare le metriche generali del sito, ad esempio il numero di richieste gestite oppure il numero di richieste provenienti da un indirizzo IP specifico.
- **Registrazione degli errori dettagliata** 
    - Informazioni dettagliate sugli errori relativi ai codici di stato HTTP che indicano un'operazione non riuscita (codice di stato 400 o superiore). 
- **Traccia delle richieste non riuscite** 
    - Informazioni dettagliate sulle richieste non riuscite, inclusa una traccia dei componenti IIS usati per elaborare la richieste e il tempo impiegato in ogni componente. 
    - I log delle richieste non riuscite consentono di individuare la causa di un errore HTTP specifico.

Per abilitare la registrazione del server:
- Passare a **Monitoraggio** > **Log di diagnostica**. 
- Abilitare i diversi tipi di diagnostica del server Web usando i controlli.

![Monitorare un'app](media/app-service-web-tutorial-monitoring/app-service-monitor-serverlogs.png)

> [!IMPORTANT]
> L'abilitazione della registrazione ha effetto sulle prestazioni dell'applicazione e l'uso delle risorse. Per gli scenari di produzione è consigliabile usare i log degli errori. Abilitare una registrazione più dettagliata solo durante l'analisi dei problemi.

### <a name="accessing-logs"></a>Accesso ai log
I log memorizzati nell'archiviazione BLOB sono accessibili tramite Azure Storage Explorer.

I log archiviati nel file system dell'app Web sono accessibili tramite FTP nei percorsi seguenti:

- **Application logs** - /LogFiles/Application/. 
    - In questa cartella sono presenti uno o più file di testo contenenti le informazioni generate dalla registrazione dell'applicazione.
- **Failed Request Traces** - /LogFiles/W3SVC#########/. 
    - Questa cartella contiene un file XSL e uno o più file XML. 
- **Detailed Error Logs** - /LogFiles/DetailedErrors/. 
    - Questa cartella contiene uno o più file con estensione htm con informazioni dettagliate sugli errori HTTP generati dall'app.
- **Web Server Logs** - /LogFiles/http/RawLogs. 
    - Questa cartella contiene uno o più file di testo formattati usando il formato di file di log esteso W3C.

## <a name="streaming"></a> Passaggio 6: streaming dei log
Il servizio app può eseguire lo streaming dei **Log applicazione** e dei **Log del server Web** generati. 

I log in streaming sono utili durante il debug di un'applicazione poiché consentono di risparmiare tempo rispetto all'accesso dei log tramite FTP.

> [!TIP]
> Prima di tentare di eseguire lo streaming dei log, assicurarsi di aver abilitato la raccolta dei log come descritto nella sezione [Registrazione](#logging).

Per eseguire lo streaming dei log:
- Passare a **Monitoraggio**> **Flusso di registrazione**
- Selezionare **Log applicazione** o **Log del server Web**, a seconda delle informazioni desiderate.
- A questo punto, è anche possibile sospendere, riavviare e cancellare il buffer.

![Log in streaming](media/app-service-web-tutorial-monitoring/app-service-monitor-logstream.png)

> [!TIP]
> I log vengono generati soltanto quando è presente traffico nell'app. È anche possibile aumentare il livello di dettaglio dei log per ottenere più eventi o informazioni.

## <a name="remote"></a> Passaggio 7 - Debug remoto
Il **debug remoto** consente di collegare un debugger all'app Web in esecuzione nel cloud. È possibile impostare punti di interruzione, manipolare direttamente la memoria, esaminare il codice e cambiarne il percorso come in un'app eseguita in locale.

Usare il debug remoto con i log di diagnostica per individuare e correggere i problemi dell'applicazione.

Per collegare il debugger all'app in esecuzione nel cloud:

- In Visual Studio 2017 aprire la soluzione per l'app di cui si vuole eseguire il debug 
- Impostare alcuni punti di interruzione esattamente come si farebbe per lo sviluppo locale.
- Aprire **Cloud Explorer** (CTRL+/, CTRL+X).
- Accedere con le credenziali di Azure.
- Individuare l'app di cui si vuole eseguire il debug
- Selezionare **Collega debugger** dal riquadro **Azioni**.

![Debug remoto](media/app-service-web-tutorial-monitoring/app-service-monitor-vsdebug.png)

Visual Studio configura l'applicazione per il debug remoto e apre una finestra del browser che consente di passare all'app. Esplorare l'app per attivare i punti di interruzione ed esaminare il codice.

> [!WARNING]
> L'esecuzione in modalità debug in produzione non è una scelta consigliata. Se l'app in produzione non viene ampliata con più istanze del server, il debug impedisce al server Web di rispondere ad altre richieste. Per la risoluzione dei problemi di produzione è consigliabile [configurare la registrazione](#logging) e usare [Application Insights](#insights)

## <a name="diagnose"></a> Passaggio 8: Diagnostica e risoluzione dei problemi
**Diagnostica e risoluzione dei problemi** è uno strumento incorporato che analizza le ultime 24 ore di attività dell'app Web. All'utente viene visualizzato un riepilogo di tutti i problemi identificati.

Usare questa funzionalità per distinguere i problemi dell'applicazione da quelli della piattaforma e individuare le soluzioni possibili per ripristinare l'integrità dell'app Web.

![Diagnostica e risoluzione dei problemi](media/app-service-web-tutorial-monitoring/app-service-monitor-diagnosis.png)

## <a name="insights"></a> Passaggio 9: Application Insights
**Application Insights** offre funzionalità avanzate di profilatura e monitoraggio dell'app. 

Usare Application Insights per rilevare e diagnosticare le eccezioni e i problemi di prestazioni nell'app Web.

È possibile abilitare Application Insights per l'app Web in **Monitoraggio** > **Application Insights** 

> [!NOTE]
> Application Insights potrebbe richiedere l'installazione dell'estensione del sito Application Insights per avviare la raccolta dati. L'installazione dell'estensione del sito comporta il riavvio dell'applicazione.

![Application Insights](media/app-service-web-tutorial-monitoring/app-service-monitor-appinsights.png)

Application Insights include un'ampia gamma di funzionalità. Per altre informazioni, usare i collegamenti inclusi nella sezione [Passaggi successivi](#next).

## <a name="next"></a> Passaggi successivi

 - [Informazioni su Azure Application Insights](..\application-insights\app-insights-overview.md)
 - [Monitoraggio delle prestazioni dell'applicazione web di Azure](..\application-insights\app-insights-azure-web-apps.md)
 - [Monitorare la disponibilità e la velocità di risposta dei siti Web](..\application-insights\app-insights-monitor-web-app-availability.md)

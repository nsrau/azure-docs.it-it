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
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 29df824062d00e01b786533033097948c008588f
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017

---
# <a name="monitor-app-service"></a>Monitorare il servizio app
Questa esercitazione fornisce istruzioni dettagliate su come monitorare l'applicazione e usare gli strumenti della piattaforma predefinita per risolvere i problemi quando si verificano.

Ogni sezione di questo documento illustra una funzionalità specifica. Un utilizzo combinato delle funzionalità consente di:
- Identificare un problema nell'applicazione.
- Determinare se il problema è causato dal codice o dalla piattaforma.
- Limitare l'origine del problema al codice.
- Eseguire il debug e risolvere il problema.

## <a name="before-you-begin"></a>Prima di iniziare
- Per monitorare e seguire la procedura descritta è necessaria un'app Web.
    - È possibile creare un'applicazione seguendo la procedura descritta nell'esercitazione [Creare un'app ASP.NET in Azure con un database SQL](app-service-web-tutorial-dotnet-sqldatabase.md).

- Se si vuole provare il **Debug remoto** dell'applicazione è necessario Visual Studio.
    - Se Visual Studio 2017 non è ancora installato, è possibile scaricare e usare la versione gratuita [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).
    - Durante l'installazione di Visual Studio abilitare **Sviluppo di Azure**.

## <a name="metrics"></a>Passaggio 1: visualizzare le metriche
Le **metriche** sono utili per comprendere:
- Integrità dell'applicazione
- Prestazioni dell'applicazione
- Utilizzo di risorse

Quando si analizza un problema dell'app, esaminare le metriche è un buon punto di partenza. Il portale di Azure consente di esaminare visivamente in modo rapido le metriche dell'app usando **Monitoraggio di Azure**.

Le metriche forniscono una visualizzazione cronologica in numerose aggregazioni chiave per l'app. Per tutte le app ospitate nel servizio app, si dovrebbero monitorare l'app Web e il piano di servizio app.

> [!NOTE]
> Un piano di servizio app rappresenta la raccolta delle risorse fisiche usate per ospitare le app. Tutte le applicazioni assegnate a un piano di servizio app condividono le risorse definite dal piano, in modo da consentire un risparmio sui costi quando si ospitano più app.
>
> I piani di servizio app definiscono:
> * Area: Europa settentrionale, Stati Uniti orientali, Asia sud-orientale e così via.
> * Dimensione dell'istanza: Small, Medium, Large e così via.
> * Numero di scala: una, due o tre istanze e così via.
> * SKU: Free, Shared, Basic, Standard, Premium e così via.

Per esaminare le metriche per l'app Web, passare al pannello **Panoramica** dell'app che si vuole monitorare. A questo punto, è possibile visualizzare un grafico per le metriche dell'app come un **riquadro Monitoraggio**. Fare clic sul riquadro per modificare e configurare le metriche da visualizzare e l'intervallo di tempo di visualizzazione.

Per impostazione predefinita, il pannello delle risorse fornisce una visualizzazione per le richieste dell'app e gli errori nell'ultima ora.
![Monitorare un'app](media/app-service-web-tutorial-monitoring/app-service-monitor.png)

Come è possibile notare nell'esempio, si dispone di un'app che genera molti **Errori server HTTP**. Il volume elevato di errori è la prima indicazione, dobbiamo esaminare questa app.

> [!TIP]
> Per informazioni sul monitoraggio di Azure usare i collegamenti seguenti:
> - [Introduzione al monitoraggio di Azure](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [Metriche di Azure](..\monitoring-and-diagnostics\monitoring-overview-metrics.md)
> - [Metriche supportate con il monitoraggio di Azure](..\monitoring-and-diagnostics\monitoring-supported-metrics.md)
> - [Dashboard di Azure](..\azure-portal\azure-portal-dashboards.md)

## <a name="alerts"></a> Passaggio 2: configurare gli avvisi
Gli **avvisi** possono essere configurati per attivare condizioni specifiche per l'app.

Nel [Passaggio 1: visualizzare le metriche](#metrics), abbiamo visto che l'app includeva un numero elevato di errori.

Configuriamo un avviso per ricevere notifiche automaticamente quando si verificano errori. In questo caso vogliamo che l'avviso invii un messaggio di posta elettronica ogni qualvolta il numero di errori HTTP 50X supera una determinata soglia.

Per creare un avviso, passare a **Monitoraggio** > **Avvisi** e fare clic su **[+] Aggiungi avviso**.

![Avvisi](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts.png)

Fornire valori per la configurazione degli avvisi:
- **Risorsa:** il sito per il monitoraggio con l'avviso.
- **Nome:** un nome per l'avviso, in questo caso: *Troppi HTTP 50X*.
- **Descrizione:** spiegazione in formato testo normale di cosa sta monitorando questo avviso.
- **Avviso per:** gli avvisi possono monitorare Metriche o Eventi, in questo esempio monitoreremo le metriche.
- **Metrica:** la metrica da monitorare, in questo caso *Errori server HTTP*.
- **Condizione:** quando inviare l'avviso, in questo caso selezionare l'opzione *maggiore di*.
- **Soglia:** valore da cercare, in questo caso *400*.
- **Periodo:** gli avvisi operano oltre il valore medio di una metrica. Per i periodi di tempo più ristretti, gli avvisi sensibili vengono sospesi. In questo caso, consideriamo *5 minuti*.
- **Invia messaggio di posta elettronica a proprietari e collaboratori:** in questo caso *Abilitato*.

Ora che l'avviso è stato creato, viene inviato un messaggio di posta elettronica ogni volta che l'app supera la soglia configurata. Gli avvisi attivi possono essere consultati anche nel portale di Azure.

![Avvisi attivati](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts-triggered.png)


> [!TIP]
> Per informazioni sugli avvisi di Azure usare i collegamenti seguenti:
> - [Cosa sono gli avvisi in Microsoft Azure?](..\monitoring-and-diagnostics\monitoring-overview-alerts.md)
> - [Eseguire operazioni sulle metriche](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [Creare avvisi delle metriche](..\monitoring-and-diagnostics\insights-alerts-portal.md)

## <a name="companion"></a> Passaggio 3: App Service Companion
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

## <a name="diagnose"></a> Passaggio 4: diagnostica e risoluzione dei problemi
La **diagnostica e risoluzione dei problemi** consente di separare i problemi legati all'app da quelli legati alla piattaforma. Può inoltre suggerire possibili soluzioni per ripristinare l'integrità dell'app Web.

![Diagnostica e risoluzione dei problemi](media/app-service-web-tutorial-monitoring/app-service-monitor-diagnosis.png)

Continuando con l'esempio dai passaggi precedenti, possiamo vedere che l'app presenta dei problemi di disponibilità. Dall'altro lato, però, la disponibilità della piattaforma non si è spostata dal 100%.

Quando l'app, diversamente dalla piattaforma, presenta dei problemi, si tratta di un chiaro segno che i problemi sono legati esclusivamente all'app.

## <a name="logging"></a> Passaggio 5: registrazione
Ora che gli errori sono stati circoscritti a un problema dell'app, per ottenere altre informazioni possiamo esaminare i log dell'app e del server.

La registrazione consente di raccogliere i log di **diagnostica dell'applicazione** e di **diagnostica del server Web** per l'app Web.

### <a name="application-diagnostics"></a>Diagnostica applicazioni
Diagnostica applicazioni consente di acquisire le tracce prodotte dall'applicazione durante il runtime.

L'aggiunta della traccia all'app migliora notevolmente la capacità di eseguire il debug e di individuare con esattezza i problemi.

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
Per abilitare la registrazione dell'applicazione, andare al **Monitoraggio** > **Log di diagnostica** e abilitare la registrazione delle applicazioni usando i controlli.

![Monitorare un'app](media/app-service-web-tutorial-monitoring/app-service-monitor-applogs.png)

I log dell'applicazione possono essere archiviati nel file system dell'app Web o inseriti nell'archiviazione BLOB. Per gli scenari di produzione è consigliabile usare l'archiviazione BLOB.

> [!IMPORTANT]
> L'abilitazione della registrazione ha effetto sulle prestazioni dell'applicazione e l'uso delle risorse. Per gli scenari di produzione è consigliabile usare i log degli errori. Abilitare una registrazione più dettagliata solo durante l'analisi dei problemi.

 ### <a name="web-server-diagnostics"></a>Diagnostica del server Web
I log del server Web vengono generati anche se l'app non è instrumentata. Il servizio app consente di raccogliere tre tipi diversi di log del server:

- **Registrazione del server Web**
    - Informazioni sulle transazioni HTTP che usano il [formato di file di log esteso W3C](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).
    - Consente di determinare le metriche generali del sito, ad esempio il numero di richieste gestite oppure il numero di richieste provenienti da un indirizzo IP specifico.
- **Registrazione degli errori dettagliata**
    - Informazioni dettagliate sugli errori relativi ai codici di stato HTTP che indicano un'operazione non riuscita (codice di stato 400 o superiore).
    - [Altre informazioni sulla registrazione degli errori dettagliata](https://www.iis.net/learn/troubleshoot/diagnosing-http-errors/how-to-use-http-detailed-errors-in-iis)
- **Traccia delle richieste non riuscite**
    - Informazioni dettagliate sulle richieste non riuscite, inclusa una traccia dei componenti IIS usati per elaborare la richieste e il tempo impiegato in ogni componente.
    - I log delle richieste non riuscite consentono di individuare la causa di un errore HTTP specifico.
    - [Altre informazioni sulla traccia delle richieste non riuscite](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing/troubleshooting-failed-requests-using-tracing-in-iis)

Per abilitare la registrazione del server:
- Passare a **Monitoraggio** > **Log di diagnostica**.
- Abilitare i diversi tipi di diagnostica del server Web usando i controlli.

![Monitorare un'app](media/app-service-web-tutorial-monitoring/app-service-monitor-serverlogs.png)

> [!IMPORTANT]
> L'abilitazione della registrazione ha effetto sulle prestazioni dell'applicazione e l'uso delle risorse. Per gli scenari di produzione è consigliabile usare i log degli errori. Abilitare una registrazione più dettagliata solo durante l'analisi dei problemi.

### <a name="accessing-logs"></a>Accesso ai log
I log memorizzati nell'archiviazione BLOB sono accessibili tramite Azure Storage Explorer. I log archiviati nel file system dell'app Web sono accessibili tramite FTP nei percorsi seguenti:

- **Log applicazioni** - `%HOME%/LogFiles/Application/`.
    - In questa cartella sono presenti uno o più file di testo contenenti le informazioni generate dalla registrazione dell'applicazione.
- **Traccia delle richieste non riuscite** - `%HOME%/LogFiles/W3SVC#########/`.
    - Questa cartella contiene un file XSL e uno o più file XML.
- **Registrazione degli errori dettagliata** - `%HOME%/LogFiles/DetailedErrors/`.
    - Questa cartella contiene uno o più file con estensione htm con informazioni dettagliate sugli errori HTTP generati dall'app.
- **Log del server Web** - `%HOME%/LogFiles/http/RawLogs`.
    - Questa cartella contiene uno o più file di testo formattati usando il formato di file di log esteso W3C.

## <a name="streaming"></a> Passaggio 6: streaming dei log
I log in streaming sono utili durante il debug di un'applicazione poiché consentono di risparmiare tempo rispetto all'[accesso dei log](#Accessing-Logs) tramite FTP.

Il servizio app può eseguire lo streaming dei **Log applicazione** e dei **Log del server Web** generati.

> [!TIP]
> Prima di tentare di eseguire lo streaming dei log, assicurarsi di aver abilitato la raccolta dei log come descritto nella sezione [Registrazione](#logging).

Per eseguire lo streaming dei log, passare a **Monitoraggio**> **Flusso di registrazione**. Selezionare **Log applicazione** o **Log del server Web**, a seconda delle informazioni desiderate. A questo punto, è anche possibile sospendere, riavviare e cancellare il buffer.

![Log in streaming](media/app-service-web-tutorial-monitoring/app-service-monitor-logstream.png)

> [!TIP]
> I log vengono generati soltanto quando è presente traffico nell'app. È anche possibile aumentare il livello di dettaglio dei log per ottenere più eventi o informazioni.

## <a name="remote"></a> Passaggio 7 - Debug remoto
Una volta individuata l'origine dei problemi dell'app, usare **Debug remoto** per esaminare il codice.

Il debug remoto consente di collegare un debugger all'app Web in esecuzione nel cloud. È possibile impostare punti di interruzione, manipolare direttamente la memoria, esaminare il codice e cambiarne il percorso come in un'app eseguita in locale.

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
> L'esecuzione in modalità debug in produzione non è una scelta consigliata. Se l'app in produzione non viene ampliata con più istanze del server, il debug impedisce al server Web di rispondere ad altre richieste. Per la risoluzione dei problemi di produzione è consigliabile [configurare la registrazione](#logging) e usare [Application Insights](#insights).



## <a name="explorer"></a> Passaggio 8: esplora processi
Quando l'app viene scalata orizzontalmente a più di un'istanza, **Esplora processi** consente di identificare i problemi specifici delle istanze.

Usare **Esplora processi** per:

- Enumerare tutti i processi nelle diverse istanze del piano di servizio app.
- Eseguire il drill-down e visualizzare gli handle e i moduli associati a ogni processo.
- Visualizzare il numero di CPU, working set e thread a livello di processo per identificare i processi con eccessivo tempo di esecuzione
- Trovare gli handle di file aperti e terminare l'istanza di un processo specifico.

Esplora processi è disponibile in **Monitoraggio** > **Esplora processi**.

![Esplora processi](media/app-service-web-tutorial-monitoring/app-service-monitor-processexplorer.png)


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


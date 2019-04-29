---
title: Abilitare la registrazione diagnostica per le app - Servizio app di Azure
description: Informazioni su come abilitare la registrazione diagnostica e aggiungere strumentazione all'applicazione e su come accedere alle informazioni registrate da Azure.
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 37455c278d665d05636ec120ca91b76153e53d16
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835720"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Abilitare la registrazione diagnostica per le app nel Servizio app di Azure
## <a name="overview"></a>Panoramica
Azure offre diagnostica integrata per facilitare il debug di un'[app del servizio app](https://go.microsoft.com/fwlink/?LinkId=529714). In questo articolo viene descritto come abilitare la registrazione diagnostica e aggiungere strumentazione all'applicazione. Viene anche descritto come accedere alle informazioni registrate da Azure.

L'articolo illustra anche l'uso del [portale di Azure](https://portal.azure.com) e dell'interfaccia della riga di comando di Azure per elaborare i log di diagnostica. Per informazioni sull'elaborazione dei log di diagnostica in Visual Studio vedere [Risoluzione dei problemi di Azure in Visual Studio](troubleshoot-dotnet-visual-studio.md).

## <a name="whatisdiag"></a>Diagnostica del server Web e diagnostica dell'applicazione
Il servizio app offre funzionalità diagnostiche per la registrazione di informazioni sia dal server Web sia dall'applicazione Web, logicamente separate in **diagnostica server Web** e **diagnostica applicazioni**.

### <a name="web-server-diagnostics"></a>Diagnostica del server Web
È possibile abilitare o disabilitare i seguenti tipi di log:

* **Registrazione degli errori dettagliata** -informazioni dettagliate per tutte le richieste che genera codice di stato HTTP 400 o superiore. incluse eventuali informazioni che aiutano a determinare il motivo per cui il server ha restituito il codice di errore. Un file HTML viene generato per ogni errore nel file system dell'app e fino a 50 errori (file) vengono mantenuti. Quando il numero di file HTML di superare i 50, i file di 26 meno recenti vengono eliminati automaticamente.
* **Traccia delle richieste non riuscita** : consente di registrare informazioni dettagliate sulle richieste non riuscite, inclusa una traccia dei componenti IIS utilizzati per elaborare la richieste e il tempo impiegato in ciascun componente. È utile per migliorare le prestazioni del sito o isolare uno specifico errore HTTP. Una cartella viene generata per ogni errore nel file system dell'app. Criteri di conservazione dei file sono le stesse di registrazione sopra dettagliata degli errori.
* **Registrazione del server Web** : consente di registrare informazioni sulle transazioni HTTP tramite il [formato di file di log esteso W3C](/windows/desktop/Http/w3c-logging). È utile nel determinare le metriche generali del sito, ad esempio il numero delle richieste gestite oppure quante di esse provengono da uno specifico indirizzo IP.

### <a name="application-diagnostics"></a>Diagnostica applicazioni
La diagnostica applicazioni consente di acquisire le informazioni prodotte da un'applicazione Web. Le applicazioni ASP.NET possono utilizzare la classe [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) per registrare le informazioni nel log di diagnostica applicazioni. Ad esempio: 

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

È possibile recuperare i log in runtime per facilitare la risoluzione dei problemi. Per altre informazioni, vedere [Risoluzione dei problemi del Servizio app di Azure in Visual Studio](troubleshoot-dotnet-visual-studio.md).

Il servizio app registra anche le informazioni di distribuzione dei log quando si pubblicano contenuti in un'app. Ciò avviene automaticamente e non sono disponibili impostazioni di configurazione per la registrazione di distribuzione. La registrazione di distribuzione consente di determinare il motivo per cui una distribuzione non è riuscita. Ad esempio, se si usa uno script di distribuzione personalizzata, si potrebbe usare la registrazione di distribuzione per determinare il motivo per cui lo script non è riuscito.

## <a name="enablediag"></a>Come abilitare la diagnostica
Per abilitare la diagnostica nel [portale di Azure](https://portal.azure.com), passare alla pagina dell'app e fare clic su **Impostazioni > Log di diagnostica**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Parte del log](./media/web-sites-enable-diagnostic-log/logspart.png)

Se si abilita **Diagnostica applicazioni**, è anche possibile scegliere il **Livello**. La tabella seguente elenca le categorie dei log incluse in ogni livello:

| Level| Categorie di log incluse |
|-|-|
|**Disabilitato** | Nessuna |
|**Error (Errore) (Error (Errore)e)** | Errore, Errore critico |
|**Warning** | Avviso, Errore, Errore critico|
|**Informazioni** | Informazioni, Avviso, Errore, Errore critico|
|**Dettagliato** | Analisi, Debug, Informazioni, Avviso, Errore, Errore critico (tutte le categorie) |
|-|-|

Per il **registro applicazioni** è possibile attivare l'opzione del file system temporaneamente per scopi di debug. Questa opzione si disattiva automaticamente dopo 12 ore. È anche possibile attivare l'opzione di archiviazione BLOB per selezionare un contenitore BLOB in cui scrivere i log.

> [!NOTE]
> Attualmente solo i log applicazioni .NET possono essere scritti nell'archiviazione BLOB. I registri applicazioni Java, PHP, Node.js e Python possono essere archiviati solo nel file system (senza modifiche al codice per la scrittura di log in risorse di archiviazione esterne).
>
>

Per la **registrazione del server Web** è possibile selezionare **archiviazione** o **file system**. Se si seleziona **Archiviazione**, è possibile selezionare un account di archiviazione e quindi un contenitore BLOB in cui scrivere i log. 

Se si archiviano i log nel file system, è possibile accedere a questi file mediante FTP oppure scaricarli come archivio zip tramite l'interfaccia della riga di comando di Azure.

Per impostazione predefinita, i log non vengono eliminati automaticamente, ad eccezione di **Registrazione applicazioni (file system)**. Per eliminare automaticamente i log, impostare il campo relativo al **periodo di conservazione (giorni)**.

> [!NOTE]
> Se si [rigenerano le chiavi di accesso dell'account di archiviazione](../storage/common/storage-create-storage-account.md), è necessario reimpostare la configurazione di registrazione corrispondente per l'uso delle chiavi aggiornate. A tale scopo, effettuare l'operazione seguente:
>
> 1. Nella scheda **Configura** impostare la funzionalità di registrazione corrispondente su **Off**. Salvare l’impostazione.
> 2. Abilitare di nuovo la registrazione al BLOB dell'account di archiviazione. Salvare l’impostazione.
>
>

È possibile abilitare contemporaneamente qualsiasi combinazione di file system o archiviazione BLOB e disporre di singole configurazioni del livello di log. Ad esempio, può essere opportuno registrare gli errori e gli avvisi nell'archiviazione BLOB come soluzione di registrazione a lungo termine e abilitare la registrazione del file system a livello dettagliato.

Benché entrambi i percorsi di archiviazione offrano le stesse informazioni di base per gli eventi registrati, l'**archiviazione BLOB** consente di registrare informazioni aggiuntive come l'ID istanza, l'ID di thread e un timestamp maggiormente granulare (formato tick) rispetto alla registrazione sul **file system**.

> [!NOTE]
> Le informazioni memorizzate nell'**archiviazione BLOB** sono accessibili solo tramite un client o un'applicazione di archiviazione in grado di funzionare direttamente con questi sistemi di archiviazione. Ad esempio, Visual Studio 2013 contiene uno strumento di esplorazione dell'archiviazione, che può essere usato per esplorare l'archiviazione BLOB, e HDInsight, che può accedere ai dati memorizzati nell'archiviazione BLOB. È inoltre possibile scrivere un'applicazione che acceda all'archiviazione Azure mediante uno degli [Azure SDK](https://azure.microsoft.com/downloads/).
>

## <a name="download"></a> Procedura: Scaricare i log
Le informazioni diagnostiche memorizzate nel file system dell'app sono accessibili direttamente tramite FTP. È inoltre possibile scaricarle come archivio zip tramite l'interfaccia della riga di comando di Azure.

La struttura di directory in cui sono memorizzati i log è la seguente:

* **Application logs** - /LogFiles/Application/. In questa cartella sono presenti uno o più file di testo contenenti le informazioni generate dalla registrazione dell'applicazione.
* **Failed Request Traces** - /LogFiles/W3SVC#########/. Questa cartella contiene un file XSL e uno o più file XML. Verificare che il file XSL venga scaricato nella stessa directory dei file XML in quanto il file XSL fornisce le funzionalità per la formattazione e il filtro dei contenuti del file XML per la visualizzazione in Internet Explorer.
* **Detailed Error Logs** - /LogFiles/DetailedErrors/. Questa cartella contiene uno o più file HTM che forniscono informazioni dettagliate relative agli eventuali errori HTTP che si sono verificati.
* **Web Server Logs** - /LogFiles/http/RawLogs. Questa cartella contiene uno o più file di testo formattati utilizzando il [formato di file di log esteso W3C](/windows/desktop/Http/w3c-logging).
* **Deployment logs** - /LogFiles/Git. Questa cartella contiene i log generati dai processi di distribuzione interna usati dal Servizio app di Azure, oltre ai log per le distribuzioni Git. I log di distribuzione sono anche disponibili in D:\home\site\deployments.

### <a name="ftp"></a>FTP

Per aprire una connessione FTP al server FTP dell'app, vedere [Distribuire l'app nel servizio app di Azure usando FTP/S](deploy-ftp.md).

Una volta connessi al server FTP/S dell'app, aprire la cartella **LogFiles** in cui sono archiviati i file di log.

### <a name="download-with-azure-cli"></a>Download con l'interfaccia della riga di comando di Azure
Per scaricare i file di log mediante l'interfaccia della riga di comando di Azure, aprire un nuovo prompt dei comandi o una sessione di PowerShell, bash o terminal e immettere il comando seguente:

    az webapp log download --resource-group resourcegroupname --name appname

Questo comando Salva i log dell'App denominata 'appname' in un file denominato **webapp_logs.zip** nella directory corrente.

> [!NOTE]
> Se l'interfaccia della riga di comando di Azure non è stata installata o configurata per l'uso della sottoscrizione di Azure, vedere [Come usare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Procedura: Visualizzare i log in Application Insights
Visual Studio Application Insights fornisce strumenti per il filtro e ricerca dei log e per correlare i log con le richieste e altri eventi.

1. Aggiungere Application Insights al progetto in Visual Studio
   * In Esplora soluzioni fare clic con il pulsante destro del mouse e scegliere Aggiungi Application Insights. L'interfaccia guida l'utente attraverso la procedura, che include la creazione di una risorsa di Application Insights. [Altre informazioni](../azure-monitor/app/asp-net.md)
2. Aggiungere il pacchetto di Listener di traccia al progetto.
   * Fare clic con il pulsante destro del mouse sul progetto e scegliere Gestisci pacchetti NuGet. Selezionare `Microsoft.ApplicationInsights.TraceListener` [ulteriori](../azure-monitor/app/asp-net-trace-logs.md)
3. Caricare il progetto ed eseguire la generazione di dati del log.
4. Nel [portale di Azure](https://portal.azure.com/) passare alla nuova risorsa di Application Insights e aprire **Ricerca**. Verranno visualizzati i dati dei log insieme a quelli relativi alle richieste e all'utilizzo e ad altri dati di telemetria. Alcuni dati di telemetria potrebbe richiedere alcuni minuti per arrivare: fare clic su Aggiorna. [Altre informazioni](../azure-monitor/app/diagnostic-search.md)

[Ulteriori informazioni sulle prestazioni di rilevamento con Application Insights](../azure-monitor/app/azure-web-apps.md)

## <a name="streamlogs"></a> Procedura: Eseguire lo streaming dei log
Durante lo sviluppo di un'applicazione, è spesso utile visualizzare le informazioni di registrazione in tempo quasi reale. È possibile eseguire lo streaming delle informazioni di registrazione al proprio ambiente di sviluppo tramite l'interfaccia della riga di comando di Azure.

> [!NOTE]
> Alcuni tipi di buffer di registrazione scrivono nel file di log, producendo nel caso eventi di "fuori servizio" nel flusso. Ad esempio, una voce del log di applicazione che si verifica quando un utente visita una pagina può essere visualizzata nel flusso prima della corrispondente voce di log HTTP per la richiesta della pagina.
>
> [!NOTE]
> Lo streaming dei log trasmette anche le informazioni scritte in qualsiasi file di testo memorizzato nella cartella **D:\\home\\LogFiles\\**.
>
>

### <a name="streaming-with-azure-cli"></a>Streaming con l'interfaccia della riga di comando di Azure
Per eseguire lo streaming delle informazioni di registrazione, aprire un nuovo prompt dei comandi o una sessione di PowerShell, bash o terminal e immettere il comando seguente:

    az webapp log tail --name appname --resource-group myResourceGroup

Il comando consente di connettersi all'app denominata "appname" e di iniziare lo streaming delle informazioni alla finestra mentre gli eventi del log si verificano nell'app. Eventuali informazioni scritte nei file con estensione txt, log o htm memorizzati nella directory /LogFiles (d:/home/logfiles) vengono trasmesse alla console locale.

Per filtrare eventi specifici, ad esempio gli errori, utilizzare il parametro **-Filter** . Ad esempio: 

    az webapp log tail --name appname --resource-group myResourceGroup --filter Error

Per filtrare tipi di log specifici, ad esempio HTTP, usare il parametro **--Path** . Ad esempio: 

    az webapp log tail --name appname --resource-group myResourceGroup --path http

> [!NOTE]
> Se l'interfaccia della riga di comando di Azure non è stata installata o configurata per l'uso della sottoscrizione di Azure, vedere [Come usare l'interfaccia della riga di comando di Azure](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a> Procedura: Comprendere i log di diagnostica
### <a name="application-diagnostics-logs"></a>Log di diagnostica applicazioni
La diagnostica applicazioni consente di memorizzare le informazioni in un formato specifico per le applicazioni .NET, a seconda della destinazione di archiviazione dei log: file system o archiviazione BLOB. 

Il set di base dei dati archiviati è lo stesso in entrambi i tipi di archiviazione: data e ora in cui si è verificato l'evento, l'ID del processo che ha generato l'evento, il tipo di evento (informazioni, avviso, errore) e il messaggio relativo all'evento. L'uso del file system per l'archiviazione dei log è utile quando è richiesto l'accesso immediato per la risoluzione di un problema, dal momento che i file di log vengono aggiornati quasi istantaneamente. L'archiviazione BLOB viene usata a scopo di archiviazione, dal momento che i file vengono memorizzati nella cache e quindi scaricati nel contenitore di archiviazione in base a una pianificazione.

**File system**

Ogni riga registrata nel file system o ricevuta tramite streaming viene visualizzata nel formato seguente:

    {Date}  PID[{process ID}] {event type/level} {message}

Ad esempio, l'aspetto di un evento di errore sarà simile all'esempio seguente:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

La registrazione nel file system fornisce le informazioni più basilari dei tre metodi disponibili, indicando solo l'ora e la data, l'ID di processo, il livello dell'evento e il messaggio.

**Archiviazione BLOB**

Durante la registrazione sull'archiviazione BLOB, i dati vengono memorizzati in formato file con valori delimitati da virgole (CSV). Verranno registrati altri campi per fornire informazioni più granulari sull'evento. Verranno utilizzate le proprietà seguenti per ogni riga memorizzata nel file CSV:

| Nome proprietà | Valore/formato |
| --- | --- |
| Data |Data e ora in cui si è verificato l'evento |
| Level |Livello dell'evento, ad esempio, errore, avviso, informazioni |
| ApplicationName |Nome dell'app |
| InstanceId |Istanza dell'app nella quale si è verificato l'evento |
| EventTickCount |Data e ora in cui si è verificato l'evento, in formato Tick (maggiore precisione) |
| EventId |ID evento di questo evento<p><p>Se non specificato, il valore predefinito è 0 |
| Pid |ID di processo |
| Tid |ID del thread che ha prodotto l'evento |
| Message |Messaggio dettagliato sull'evento |

L'aspetto dei dati archiviati in un BLOB sarà simile all'esempio seguente:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> Per ASP.NET Core, la registrazione viene eseguita tramite il provider [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices), che deposita altri file di log nel contenitore BLOB. Per altre informazioni, vedere [Registrazione in ASP.NET Core- Registrazione in Azure](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#logging-in-azure).
>
>

### <a name="failed-request-traces"></a>Failed Request Traces
Le tracce delle richieste non riuscite vengono memorizzate nei file XML denominati **fr######.xml**. Per semplificare la visualizzazione delle informazioni registrate, è disponibile un foglio di stile XSL denominato **freb.xsl** nella stessa directory dei file XML. Se si apre uno dei file XML in Internet Explorer, Internet Explorer usa il foglio di stile XSL per offrire una visualizzazione formattata delle informazioni di traccia, simile all'esempio seguente:

![richiesta non riuscita visualizzata nel browser](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

> [!NOTE]
> Un modo semplice per visualizzare le tracce di richieste non riuscite formattata è passare alla pagina dell'app nel portale. Nel menu a sinistra, selezionare **diagnosi e risoluzione dei problemi**, quindi cercare **non è stato possibile log traccia delle richieste**, quindi fare clic sull'icona per esplorare e visualizzare la traccia desiderato.
>

### <a name="detailed-error-logs"></a>Detailed Error Logs
I log di errore dettagliati sono documenti HTML che offrono informazioni più approfondite sugli errori HTTP verificatisi. Poiché si tratta di semplici documenti HTML, è possibile visualizzarli in un browser Web.

### <a name="web-server-logs"></a>Web Server Logs
I log del server Web vengono formattati con il [formato file di log esteso W3C](/windows/desktop/Http/w3c-logging). È possibile leggere queste informazioni con un editor di testo oppure analizzarle con utilità come [Log Parser](https://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> I log prodotti dal Servizio app di Azure non supportano i campi **s-computername**, **s-ip** o **cs-version**.
>
>

## <a name="nextsteps"></a> Passaggi successivi
* [Come monitorare il Servizio app di Azure](web-sites-monitor.md)
* [Risoluzione dei problemi del Servizio app di Azure in Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analizzare i log delle app in HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

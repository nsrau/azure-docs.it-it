<properties
	pageTitle="Abilitare la registrazione diagnostica per le app Web nel servizio app di Azure"
	description="Informazioni su come abilitare la registrazione diagnostica e aggiungere strumentazione all'applicazione e su come accedere alle informazioni registrate da Azure."
	services="app-service"
	documentationCenter=".net"
	authors="cephalin"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="cephalin"/>

# Abilitare la registrazione diagnostica per le app Web nel servizio app di Azure

## Panoramica

Azure offre diagnostica integrata per facilitare il debug di un'[app Web del servizio app](http://go.microsoft.com/fwlink/?LinkId=529714). In questo articolo viene descritto come abilitare la registrazione diagnostica e aggiungere strumentazione all'applicazione, oltre a come accedere alle informazioni registrate da Azure.

L'articolo illustra inoltre l'uso del [portale di Azure](https://portal.azure.com), di Azure PowerShell e dell'interfaccia della riga di comando di Azure (Azure CLI) per elaborare i log di diagnostica. Per informazioni sull'elaborazione dei log di diagnostica in Visual Studio vedere [Risoluzione dei problemi di Azure in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>Diagnostica del server Web e diagnostica dell'applicazione

App Web del servizio app offre funzionalità diagnostiche per la registrazione di informazioni sia dal server Web sia dall'applicazione Web, logicamente separate in **diagnostica del server Web** e **diagnostica dell'applicazione**.

### Diagnostica del server Web

È possibile abilitare o disabilitare i seguenti tipi di log:

- **Registrazione degli errori dettagliata**: consente di registrare informazioni dettagliate sugli errori relativi ai codici di stato HTTP che indicano un'operazione non riuscita (codice di stato 400 o superiore), incluse eventualmente le informazioni che aiutano a determinare il motivo per cui il server ha restituito il codice di errore.
- **Traccia delle richieste non riuscita**: consente di registrare informazioni dettagliate sulle richieste non riuscite, inclusa una traccia dei componenti IIS utilizzati per elaborare la richieste e il tempo impiegato in ciascun componente. Ciò può essere utile se si sta tentando di aumentare le prestazioni del sito oppure isolare la causa della restituzione di uno specifico errore HTTP.
- **Registrazione del server Web**: consente di registrare informazioni sulle transazioni HTTP tramite il [formato di file di log esteso W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Questo è utile nel determinare le metriche generali del sito, ad esempio il numero delle richieste gestite oppure quante di esse provengono da uno specifico indirizzo IP

### Diagnostica applicazioni

La diagnostica applicazioni consente di acquisire le informazioni prodotte da un'applicazione Web. Le applicazioni ASP.NET possono utilizzare la classe [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) per registrare le informazioni nel log di diagnostica applicazioni. Ad esempio:

	System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

È possibile recuperare i registri in fase di esecuzione per facilitare la risoluzione dei problemi. Per altre informazioni, vedere [Risoluzione dei problemi delle app Web di Azure in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

Le app Web del servizio app registrano anche le informazioni di distribuzione dei log quando si pubblicano contenuti in un'app Web. Ciò avviene automaticamente e non sono disponibili impostazioni di configurazione per la registrazione di distribuzione. La registrazione di distribuzione consente di determinare il motivo per cui una distribuzione non è riuscita. Ad esempio, se si utilizza uno script di distribuzione personalizzata, si potrebbe usare la registrazione di distribuzione per determinare il motivo per cui lo script non è riuscito.

## <a name="enablediag"></a>Come abilitare la diagnostica

Per abilitare la diagnostica nel [portale di Azure](https://portal.azure.com), passare al pannello dell'app Web e fare clic su **Impostazioni > Log di diagnostica**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Parte del log](./media/web-sites-enable-diagnostic-log/logspart.png)

Quando si abilita**Diagnostica applicazioni** è anche possibile scegliere il **Livello**. Questa impostazione consente di filtrare le informazioni acquisite come **informativa**, **avviso** o **errore**. Impostando il filtro su **Dettagliato** è possibile registrare tutte le informazioni generate dall'applicazione.

> [AZURE.NOTE] Diversamente da quanto accade nella modifica del file web.config, l'abilitazione della diagnostica applicazioni o la modifica dei livelli dei log di diagnostica non comportano il riciclaggio del dominio dell'applicazione in cui viene eseguita la stessa.

Nella scheda **Configura** dell'app Web nel [portale classico](https://manage.windowsazure.com), è possibile selezionare **archiviazione** o **file system** per **Registrazione server Web**. La selezione di **storage** consente di selezionare un account di archiviazione e quindi un contenitore BLOB in cui verranno scritti i log. Tutti gli altri log per la **diagnostica del sito** verranno scritti solo sul file system.

La scheda **Configura** dell'app Web del [portale classico](https://manage.windowsazure.com) include anche altre impostazioni per la diagnostica applicazioni:

* **File system**: consente di archiviare le informazioni di diagnostica applicazioni sul file system dell'app Web. È possibile accedere a questi file mediante FTP oppure scaricarli come archivio zip tramite Azure PowerShell o l'interfaccia della riga di comando di Azure.
* **Archivio tabelle**: consente di memorizzare le informazioni di diagnostica applicazioni nell'account di archiviazione di Azure e nel nome tabella specificati.
* **Archivio BLOB**: consente di memorizzare le informazioni di diagnostica applicazioni nell'account di archiviazione di Azure e nel contenitore BLOB specificati.
* **Periodo di conservazione**: per impostazione predefinita, i log non vengono eliminati automaticamente dall'**archiviazione BLOB**. Selezionare **imposta mantenimento** e immettere il numero di giorni per cui conservare i log, se si desidera eliminarli automaticamente.

>[AZURE.NOTE] Se si [rigenerano le chiavi di accesso dell'account di archiviazione](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys), è necessario reimpostare la configurazione di registrazione corrispondente per l'utilizzo delle chiavi aggiornate. A tale scopo, seguire questa procedura:
>
> 1. Nella scheda **Configura**, impostare la funzionalità di registrazione corrispondente su **Off**. Salvare l’impostazione.
> 2. Abilitare di nuovo la registrazione al BLOB dell'account di archiviazione o alla tabella. Salvare l’impostazione.

È possibile abilitare contemporaneamente qualsiasi combinazione di file system, archiviazione tabelle e archiviazione BLOB e disporre di singole configurazioni del livello di log. Ad esempio, può essere opportuno registrare gli errori e gli avvisi nell'archiviazione BLOB come soluzione di registrazione a lungo termine e abilitare la registrazione del file system a livello dettagliato.

Benché tutti e tre i percorsi di archiviazione offrano le stesse informazioni di base per gli eventi registrati, l'**archiviazione tabelle** e l'**archiviazione BLOB** consentono di registrare informazioni aggiuntive come l'ID istanza, l'ID di thread e un timestamp maggiormente granulare (formato tick) rispetto alla registrazione sul **file system**.

> [AZURE.NOTE] Le informazioni memorizzate nell'**archivio tabelle** o **nell'archivio BLOB** sono accessibili solo tramite un client o un'applicazione di archiviazione che possano funzionare direttamente con questi sistemi di archiviazione. Ad esempio, Visual Studio 2013 contiene lo strumento Esplora archivi, che può essere utilizzato per esplorare l'archiviazione tabelle o BLOB, e HDInsight, che può accedere ai dati memorizzati nell'archiviazione BLOB. È inoltre possibile scrivere un'applicazione che acceda all'archiviazione Azure mediante uno degli [Azure SDK](/downloads/#).

> [AZURE.NOTE] È inoltre possibile abilitare la diagnostica da Azure PowerShell mediante il cmdlet **Set-AzureWebsite**. Se Azure PowerShell non è stato installato o configurato per l'utilizzo della sottoscrizione di Azure, vedere [Come utilizzare Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).

##<a name="download"></a> Procedura: Scaricare i log

Le informazioni diagnostiche memorizzate nel file system dell'app Web sono accessibili direttamente tramite FTP. È inoltre possibile scaricarle come archivio zip mediante Azure PowerShell o l'interfaccia della riga di comando di Azure.

La struttura di directory in cui sono memorizzati i log è la seguente:

* **Application logs** - /LogFiles/Application/. In questa cartella sono presenti uno o più file di testo contenenti le informazioni generate dalla registrazione dell'applicazione.

* **Failed Request Traces** - /LogFiles/W3SVC#########/. Questa cartella contiene un file XSL e uno o più file XML. Verificare che il file XSL venga scaricato nella stessa directory dei file XML in quanto il file XSL fornisce le funzionalità per la formattazione e il filtro dei contenuti del file XML per la visualizzazione in Internet Explorer.

* **Detailed Error Logs** - /LogFiles/DetailedErrors/. Questa cartella contiene uno o più file HTM che forniscono informazioni dettagliate relative agli eventuali errori HTTP che si sono verificati.

* **Web Server Logs** - /LogFiles/http/RawLogs. Questa cartella contiene uno o più file di testo formattati utilizzando il [formato di file di log esteso W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).

* **Deployment logs** - /LogFiles/Git. Questa cartella contiene i log generati dai processi di distribuzione interna utilizzati da app Web di Azure, oltre ai log per le distribuzioni Git.

### FTP

Per accedere alle informazioni sulle diagnostiche tramite FTP, visitare il **Dashboard** dell'app Web nel [portale classico](https://manage.windowsazure.com). Nella sezione **quick glance** usare il collegamento **FTP Diagnostic Logs** per accedere ai file di log mediante FTP. In **Deployment/FTP User** è indicato il nome utente da utilizzare per accedere al sito FTP.

> [AZURE.NOTE] Se la voce **Utente FTP/distribuzione** non è impostata oppure se è stata dimenticata la password per questo utente, è possibile creare un nuovo utente con relativa password mediante il collegamento **Reimpostare le credenziali** di distribuzione nella sezione **Riepilogo rapido** del **Dashboard**.

### Download con Azure PowerShell

Per scaricare i file di log, avviare una nuova istanza di Azure PowerShell e utilizzare il comando seguente:

	Save-AzureWebSiteLog -Name webappname

In tal modo, i log dell'app Web specificata dal parametro **-Name** verranno salvati in un file denominato **logs.zip** nella directory corrente.

> [AZURE.NOTE] Se Azure PowerShell non è stato installato o configurato per l'utilizzo della sottoscrizione di Azure, vedere [Come utilizzare Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).

### Download con l'interfaccia della riga di comando di Azure

Per scaricare i file di log mediante l'interfaccia della riga di comando di Azure, aprire un nuovo prompt dei comandi o una sessione di PowerShell, bash o terminal e immettere il comando seguente:

	azure site log download webappname

In tal modo, i log dell'app Web denominata "nomeappWeb" verranno salvati in un file denominato **diagnostics.zip** nella directory corrente.

> [AZURE.NOTE] Se l'interfaccia della riga di comando di Azure non è stata installata o configurata per l'uso della sottoscrizione di Azure, vedere [Come usare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).

## Procedura: visualizza i registri in Application Insights

Visual Studio Application Insights fornisce strumenti per il filtro e ricerca dei registri e per correlare i registri con le richieste e altri eventi.

1. Aggiungere Application Insights al progetto in Visual Studio
 * Fare clic con il pulsante destro del mouse in Esplora soluzioni e scegliere Aggiungi Application Insights. Informazioni dettagliate dei passaggi che includono la creazione di una risorsa di Application Insights. [Altre informazioni](../application-insights/app-insights-start-monitoring-app-health-usage.md)
2. Aggiungere il pacchetto di Listener di traccia al progetto.
 * In Visual Studio fare clic con il pulsante destro del mouse sul progetto e scegliere Gestisci pacchetti NuGet. Selezionare `Microsoft.ApplicationInsights.TraceListener` [ulteriori](../application-insights/app-insights-asp-net-trace-logs.md)
3. Caricare il progetto ed eseguire la generazione di dati del log.
4. Nel [portale di Azure](https://portal.azure.com/) individuare la nuova risorsa di Application Insights e aprire **Ricerca**. Verranno visualizzati i dati dei log insieme a quelli relativi alle richieste, all'utilizzo e alla telemetria. Alcuni dati di telemetria potrebbe richiedere alcuni minuti per arrivare: fare clic su Aggiorna. [Ulteriori informazioni](../application-insights/app-insights-diagnostic-search.md)

[Ulteriori informazioni sulle prestazioni di rilevamento con Application Insights](../azure-portal/insights-perf-analytics.md)

##<a name="streamlogs"></a> Procedura: Eseguire lo streaming dei log

Durante lo sviluppo di un'applicazione, è spesso utile visualizzare le informazioni di registrazione in tempo quasi reale. A tale scopo, eseguire lo streaming delle informazioni di registrazione nel proprio ambiente di sviluppo mediante Azure PowerShell o l'interfaccia della riga di comando di Azure.

> [AZURE.NOTE] Alcuni tipi di buffer di registrazione scrivono nel file di log, producendo nel caso eventi di "fuori servizio" nel flusso. Ad esempio, una voce del log di applicazione che si verifica quando un utente visita una pagina può essere visualizzata nel flusso prima della corrispondente voce di log HTTP per la richiesta della pagina.

> [AZURE.NOTE] Lo streaming del log trasmetterà inoltre le informazioni scritte in qualsiasi file di testo memorizzato nella cartella **D:\\home\\LogFiles\**

### Streaming con Azure PowerShell

Per trasmettere le informazioni di registrazione, avviare una nuova istanza di Azure PowerShell e usare il comando seguente:

	Get-AzureWebSiteLog -Name webappname -Tail

Il comando consente di connettersi all'app Web specificata dal parametro **-Name** e di iniziare lo streaming delle informazioni alla finestra di PowerShell mentre gli eventi del log si verificano sull'app Web. Eventuali informazioni scritte nei file con estensione txt, log o htm che sono memorizzati nella directory /LogFiles (d:/home/logfiles) verranno trasmesse alla console locale.

Per filtrare eventi specifici, ad esempio gli errori, utilizzare il parametro **-Message**. Ad esempio:

	Get-AzureWebSiteLog -Name webappname -Tail -Message Error

Per filtrare tipi di log specifici, ad esempio HTTP, usare il parametro **-Path**. Ad esempio:

	Get-AzureWebSiteLog -Name webappname -Tail -Path http

Per visualizzare un elenco di percorsi disponibili usare il parametro -ListPath.

> [AZURE.NOTE] Se Azure PowerShell non è stato installato o configurato per l'utilizzo della sottoscrizione di Azure, vedere [Come utilizzare Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).

### Streaming con l'interfaccia della riga di comando di Azure

Per eseguire lo streaming delle informazioni di registrazione, aprire un nuovo prompt dei comandi o una sessione di PowerShell, bash o terminal e immettere il comando seguente:

	azure site log tail webappname

Il comando consente di connettersi all'app Web denominata e di iniziare lo streaming delle informazioni alla finestra di PowerShell mentre gli eventi del log si verificano sull'app Web. Eventuali informazioni scritte nei file con estensione txt, log o htm che sono memorizzati nella directory /LogFiles (d:/home/logfiles) verranno trasmesse alla console locale.

Per filtrare eventi specifici, ad esempio gli errori, utilizzare il parametro **-Filter**. Ad esempio:

	azure site log tail webappname --filter Error

Per filtrare tipi di log specifici, ad esempio HTTP, usare il parametro **--Path**. Ad esempio:

	azure site log tail webappname --path http

> [AZURE.NOTE] Se l'interfaccia della riga di comando di Azure non è stata installata o configurata per l'uso della sottoscrizione di Azure, vedere [Come usare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).

##<a name="understandlogs"></a> Procedura: Comprendere i log di diagnostica

### Log di diagnostica applicazioni

La diagnostica applicazioni consente di memorizzare le informazioni in un formato specifico per le applicazioni .NET, a seconda della destinazione di archiviazione dei log: file system, archiviazione tabelle o archiviazione BLOB. L'insieme di dati memorizzati di base è lo stesso in tutti e tre i tipi di archiviazione: data e ora in cui si è verificato l'evento, l'ID del processo che ha prodotto l'evento, il tipo di evento (informazioni, avviso, errore) e il messaggio di evento.

__File system__

Ogni riga registrata nel file system o ricevuta mediante streaming apparirà nel formato seguente:

	{Date}  PID[{process id}] {event type/level} {message}

Ad esempio, l'aspetto di un evento di errore sarà simile al seguente:

	2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

La registrazione sul file system fornisce le informazioni più basilari dei tre metodi disponibili, indicando solo l'ora e la data, l'ID di processo, il livello dell'evento e il messaggio.

__Archiviazione tabelle__

Durante la registrazione nell'archiviazione tabelle verranno utilizzate proprietà aggiuntive per facilitare la ricerca dei dati memorizzati nella tabella, oltre a informazioni più granulari sull'evento. Verranno utilizzate le proprietà seguenti (colonne) per ogni entità (riga) memorizzata nella tabella.

Nome proprietà|Valore/formato
---|---
PartitionKey|Data/ora dell'evento nel formato aaaaMMggHH
RowKey|Valore GUID che identifica questa entità in modo univoco
Timestamp|Data e ora in cui si è verificato l'evento
EventTickCount|Data e ora in cui si è verificato l'evento, in formato Tick (maggiore precisione)
ApplicationName|Nome dell'app Web
Level|Livello dell'evento (ad esempio, errore, avviso, informazione)
EventId|ID evento di questo evento<p><p>Se non specificato, il valore predefinito è 0
InstanceId|Istanza dell'app Web sulla quale si è verificato l'evento
Pid|ID di processo
Tid|ID del thread che ha prodotto l'evento
Message|Messaggio dettagliato sull'evento

__Archiviazione BLOB__

Durante la registrazione sull'archiviazione BLOB, i dati vengono memorizzati in formato file con valori delimitati da virgole (CSV). In maniera analoga all'archiviazione tabelle, verranno registrati ulteriori campi per fornire informazioni più granulari sull'evento. Verranno utilizzate le proprietà seguenti per ogni riga memorizzata nel file CSV:

Nome proprietà|Valore/formato
---|---
Date|Data e ora in cui si è verificato l'evento
Level|Livello dell'evento (ad esempio, errore, avviso, informazione)
ApplicationName|Nome dell'app Web
InstanceId|Istanza dell'app Web nella quale si è verificato l'evento
EventTickCount|Data e ora in cui si è verificato l'evento, in formato Tick (maggiore precisione)
EventId|ID evento di questo evento<p><p>Se non specificato, il valore predefinito è 0
Pid|ID di processo
Tid|ID del thread che ha prodotto l'evento
Message|Messaggio dettagliato sull'evento

L'aspetto dei dati archiviati in un BLOB sarà simile al seguente:

	date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
	2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [AZURE.NOTE] La prima riga del log conterrà le intestazioni di colonna, come rappresentato in questo esempio.

### Tracce delle richieste non riuscite

Le tracce delle richieste non riuscite vengono memorizzate nei file XML denominati __fr######.xml__. Per semplificare la visualizzazione delle informazioni registrate, è disponibile un foglio di stile XSL denominato __freb.xsl__ nella stessa directory dei file XML. L'apertura di uno dei file XML in Internet Explorer comporterà l'uso del foglio di stile XSL allo scopo di produrre una visualizzazione formattata delle informazioni di traccia. Il risultato sarà simile al seguente:

![richiesta non riuscita visualizzata nel browser](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### Log di errore dettagliati

I log di errore dettagliati sono documenti HTML che offrono informazioni più approfondite sugli errori HTTP verificatisi. Poiché si tratta di semplici documenti HTML, è possibile visualizzarli in un browser Web.

### Log del server Web

I log del server Web vengono formattati con il [formato file di log esteso W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). È possibile leggere queste informazioni con un editor di testo oppure analizzarle con utilità come [Log Parser](http://go.microsoft.com/fwlink/?LinkId=246619).

> [AZURE.NOTE] I log prodotti dalle app Web di Azure non supportano i campi __s-computername__, __s-ip__ o __cs-version__.

##<a name="nextsteps"></a> Passaggi successivi

- [Come monitorare le app Web](/manage/services/web-sites/how-to-monitor-websites/)
- [Risoluzione dei problemi delle app Web di Azure in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
- [Analisi dei log delle app Web in HDInsight](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

> [AZURE.NOTE] Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una Guida per la modifica di siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sui servizi di Azure esistente](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida relativa al passaggio dal portale precedente al nuovo portale, vedere [Informazioni di riferimento per l'esplorazione del portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=AcomDC_0211_2016-->
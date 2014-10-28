<properties linkid="develop-net-common-tasks-diagnostics-logging-and-instrumentation" urlDisplayName="Enable diagnostic logging" pageTitle="Enable diagnostic logging - Azure Websites" metaKeywords="Azure diagnostics web sites, Azure Management Portal diagnostics, Azure diagnostics, web site diagnostics, web site debug" description="Learn how to enable diagnostic logging and add instrumentation to your application, as well as how to access the information logged by Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Enable diagnostic logging for Azure Websites" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Abilitare la registrazione diagnostica per Siti Web di Azure

Azure offre una funzionalità di diagnostica integrata per semplificare il debug di un'applicazione ospitata in Siti Web di Azure. In questo articolo viene descritto come abilitare la registrazione diagnostica e aggiungere strumentazione all'applicazione, oltre a come accedere alle informazioni registrate da Azure.

> [WACOM.NOTE] In questo articolo viene descritto come usare il portale di gestione di Azure, Azure PowerShell e l'interfaccia della riga di comando multipiattaforma Azure per elaborare i log di diagnostica. Per informazioni sull'elaborazione dei log di diagnostica in Visual Studio vedere [Risoluzione dei problemi di Siti Web di Azure in Visual Studio][Risoluzione dei problemi di Siti Web di Azure in Visual Studio].

## Sommario

-   [Informazioni sulla diagnostica del sito Web][Informazioni sulla diagnostica del sito Web]
-   [Procedura: Abilitare la diagnostica][Procedura: Abilitare la diagnostica]
-   [Procedura: Scaricare i log][Procedura: Scaricare i log]
-   [Procedura: Eseguire lo streaming dei log][Procedura: Eseguire lo streaming dei log]
-   [Procedura: Comprendere i log di diagnostica][Procedura: Comprendere i log di diagnostica]
-   [Passaggi successivi][Passaggi successivi]

<a name="whatisdiag"></a>

## Informazioni sulla diagnostica del sito Web

</p>
Siti Web di Azure offre funzionalità diagnostiche per la registrazione di informazioni sia dal server Web sia dall'applicazione Web, logicamente separate in **diagnostica del sito** e **diagnostica applicazioni**.

### Diagnostica del sito

La diagnostica del sito consente di abilitare o disabilitare le opzioni seguenti:

-   **Detailed Error Logging**: consente di registrare informazioni dettagliate sugli errori relativi ai codici di stato HTTP che indicano un'operazione non riuscita (codice di stato 400 o superiore), incluse eventualmente le informazioni che aiutano a determinare il motivo per cui il server ha restituito il codice di errore.
-   **Failed Request Tracing**: consente di registrare informazioni dettagliate sulle richieste non riuscite, inclusa una traccia dei componenti IIS utilizzati per elaborare la richieste e il tempo impiegato in ciascun componente. Ciò può essere utile se si sta tentando di aumentare le prestazioni del sito oppure isolare la causa della restituzione di uno specifico errore HTTP.
-   **Registrazione del server Web**: consente di registrare tutte le transazioni HTTP su un sito Web tramite il [formato di file di log esteso W3C][formato di file di log esteso W3C]. Questo report è utile nel determinare le metriche generali del sito, ad esempio il numero di richieste gestite oppure quante di esse provengono da uno specifico indirizzo IP

### Diagnostica applicazioni

La diagnostica applicazioni consente di acquisire le informazioni prodotte da un'applicazione Web. Le applicazioni ASP.NET possono usare la classe [System.Diagnostics.Trace][System.Diagnostics.Trace] per registrare le informazioni nel log di diagnostica applicazioni. Ad esempio:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

La diagnostica applicazioni consente di risolvere i problemi dell'applicazione in esecuzione mediante il rilascio di informazioni quando vengono utilizzati determinati frammenti di codice. Si tratta di una funzione piuttosto utile quando si tenta di determinare il motivo per cui il codice intraprende un percorso specifico e, di solito, quando tale percorso produce un errore o un altro comportamento indesiderato.

Per informazioni sull'utilizzo della diagnostica applicazioni in Visual Studio vedere [Risoluzione dei problemi di Siti Web di Azure in Visual Studio][1].

> [WACOM.NOTE] Diversamente da quanto accade nella modifica del file web.config, l'abilitazione della diagnostica applicazioni o la modifica dei livelli dei log di diagnostica non comportano il riciclaggio del dominio dell'applicazione in cui viene eseguita la stessa.

Siti Web di Azure registra anche le informazioni di distribuzione quando si pubblica un'applicazione su un sito Web. Ciò avviene automaticamente e non sono disponibili impostazioni di configurazione per la registrazione di distribuzione. La registrazione di distribuzione consente di determinare il motivo per cui una distribuzione non è riuscita. Ad esempio, se si utilizza uno script di distribuzione personalizzata, si potrebbe usare la registrazione di distribuzione per determinare il motivo per cui lo script non è riuscito.

<a name="enablediag"></a>

## Procedura: Abilitare la diagnostica

</p>
È possibile abilitare la diagnostica visitando la pagina **Configura** del sito Web di Azure nel [portale di gestione di Azure][portale di gestione di Azure]. Nella pagina **Configure** usare le sezioni dedicate alla **diagnostica applicazioni** e alla **diagnostica del sito** per abilitare la registrazione.

Quando si abilita la **diagnostica applicazioni** è inoltre necessario selezionare il **livello di registrazione** e se abilitare la registrazione sul **file system**, l'**archiviazione tabelle** o l'**archiviazione BLOB**. Benché tutti e tre i percorsi di archiviazione offrano le stesse informazioni di base per gli eventi registrati, l'**archiviazione tabelle** e l'**archiviazione BLOB** consentono di registrare informazioni aggiuntive come l'ID istanza, l'ID di thread e un timestamp maggiormente granulare (formato tick) rispetto alla registrazione sul **file system**.

Quando si abilita la **diagnostica del sito** è necessario selezionare **storage** o **file system** per **web server logging**. La selezione di **storage** consente di selezionare un account di archiviazione e quindi un contenitore BLOB in cui verranno scritti i log. Tutti gli altri log per la **diagnostica del sito** verranno scritti solo sul file system.

> [WACOM.NOTE] Le informazioni memorizzate nell'**archiviazione tabelle** o nell'**archiviazione BLOB** sono accessibili solo tramite un client o un'applicazione di archiviazione che possano funzionare direttamente con questi sistemi di archiviazione. Ad esempio, Visual Studio 2013 contiene lo strumento Esplora archivi, che può essere usato per esplorare l'archiviazione tabelle o BLOB, e HDInsight, che può accedere ai dati memorizzati nell'archiviazione BLOB. È inoltre possibile scrivere un'applicazione che acceda all'archiviazione Azure mediante uno degli [Azure SDK][Azure SDK].

Le impostazioni seguenti sono disponibili quando si abilita la **diagnostica applicazioni**:

-   **Logging level**: consente di filtrare le informazioni acquisite come **informazioni**, **avvisi** o **errori**. Impostando il filtro su **verbose** è possibile registrare tutte le informazioni generate dall'applicazione. È possibile impostare l'opzione **Logging level** in maniera diversa per la registrazione su **file system**, **archiviazione tabelle** e **archiviazione BLOB**.
-   **File system**: consente di archiviare le informazioni di Application Diagnostics nel file system del sito Web. È possibile accedere a questi file mediante FTP oppure scaricarli come archivio zip tramite Azure PowerShell o gli strumenti da riga di comando di Azure.
-   **Table storage**: consente di memorizzare le informazioni di diagnostica applicazioni nell'account di archiviazione di Azure e nel nome tabella specificati.
-   **Blob storage**: consente di memorizzare le informazioni di diagnostica applicazioni nell'account di archiviazione di Azure e nel contenitore BLOB specificati.
-   **Retention period**: per impostazione predefinita, i log non vengono eliminati automaticamente dall'**archiviazione BLOB**. Selezionare **set retention** e immettere il numero di giorni per cui conservare i log, se si desidera eliminarli automaticamente.

> [WACOM.NOTE] È possibile abilitare contemporaneamente qualsiasi combinazione di file system, archiviazione tabelle e archiviazione BLOB e disporre di singole configurazioni del livello di log. Ad esempio, può essere opportuno registrare gli errori e gli avvisi nell'archiviazione BLOB come soluzione di registrazione a lungo termine e abilitare la registrazione del file system a livello dettagliato.

> [WACOM.NOTE] È inoltre possibile abilitare la diagnostica da Azure PowerShell mediante il cmdlet **Set-AzureWebsite**. Se Azure PowerShell non è stato installato o configurato per l'uso della sottoscrizione di Azure, vedere [Come usare Azure PowerShell][Come usare Azure PowerShell].

<a name="download"></a>

## Procedura: Scaricare i log

</p>
Le informazioni diagnostiche memorizzate nel file system del sito Web sono accessibili direttamente tramite FTP. È inoltre possibile scaricarle come archivio zip mediante Azure PowerShell o gli strumenti da riga di comando di Azure.

La struttura di directory in cui sono memorizzati i log è la seguente:

-   **Application logs** - /LogFiles/Application/. In questa cartella sono presenti uno o più file di testo contenenti le informazioni generate dalla registrazione dell'applicazione.

-   **Failed Request Traces** - /LogFiles/W3SVC#\#\#\#\#\#\#\#\#/. Questa cartella contiene un file XSL e uno o più file XML. Verificare che il file XSL venga scaricato nella stessa directory dei file XML in quanto il file XSL fornisce le funzionalità per la formattazione e il filtro dei contenuti del file XML per la visualizzazione in Internet Explorer.

-   **Detailed Error Logs** - /LogFiles/DetailedErrors/. Questa cartella contiene uno o più file HTM che forniscono informazioni dettagliate relative agli eventuali errori HTTP che si sono verificati.

-   **Web Server Logs** - /LogFiles/http/RawLogs. Questa cartella contiene uno o più file di testo formattati usando il [formato di file di log esteso W3C][formato di file di log esteso W3C].

-   **Deployment logs** - /LogFiles/Git. Questa cartella contiene i log generati dai processi di distribuzione interna usati da Siti Web di Azure, oltre ai log per le distribuzioni Git.

### FTP

Per accedere alle informazioni diagnostiche tramite FTP, visitare la scheda **Dashboard** del proprio sito Web nel portale di gestione di Azure. Nella sezione **quick glance** usare il collegamento **FTP Diagnostic Logs** per accedere ai file di log mediante FTP. In **Deployment/FTP User** è indicato il nome utente da usare per accedere al sito FTP.

> [WACOM.NOTE] Se la voce **Deployment/FTP User** non è impostata oppure se è stata dimenticata la password per questo utente, è possibile creare un nuovo utente con relativa password mediante il collegamento **Reset deployment credentials** nella sezione **quick glance** della scheda **Dashboard**.

### Download con Azure PowerShell

Per scaricare i file di log, avviare una nuova istanza di Azure PowerShell e usare il comando seguente:

    Save-AzureWebSiteLog -Name websitename

In tal modo, i log del sito Web specificato dal parametro **-Name** verranno salvati in un file denominato **logs.zip** nella directory corrente.

> [WACOM.NOTE] Se Azure PowerShell non è stato installato o configurato per l'uso della sottoscrizione di Azure, vedere [Come usare Azure PowerShell][Come usare Azure PowerShell].

### Download con gli strumenti da riga di comando di Azure

Per scaricare i file di log mediante gli strumenti da riga di comando di Azure, aprire un nuovo prompt dei comandi o una sessione di PowerShell, bash o terminal e immettere il comando seguente:

    azure site log download websitename

In tal modo, i log del sito Web denominato "nomesitoWeb" verranno salvati in un file denominato **diagnostics.zip** nella directory corrente.

> [WACOM.NOTE] Se gli strumenti da riga di comando di Azure non sono stati installati o configurati per l'uso della sottoscrizione di Azure, vedere [Come usare gli strumenti da riga di comando di Azure][Come usare gli strumenti da riga di comando di Azure].

<a name="streamlogs"></a>

## Procedura: Eseguire lo streaming dei log

</p>
Durante lo sviluppo di un'applicazione, è spesso utile visualizzare le informazioni di registrazione in tempo quasi reale. A tale scopo, eseguire lo streaming delle informazioni di registrazione nel proprio ambiente di sviluppo mediante Azure PowerShell o gli strumenti da riga di comando di Azure.

> [WACOM.NOTE] Alcuni tipi di buffer di registrazione scrivono nel file di log, producendo nel caso eventi di "fuori servizio" nel flusso. Ad esempio, una voce del log di applicazione che si verifica quando un utente visita una pagina può essere visualizzata nel flusso prima della corrispondente voce di log HTTP per la richiesta della pagina.

> [WACOM.NOTE] Lo streaming del log trasmetterà inoltre le informazioni scritte in qualsiasi file di testo memorizzato nella cartella **D:\\home\\LogFiles\\**.

### Streaming con Azure PowerShell

Per trasmettere le informazioni di registrazione, avviare una nuova istanza di Azure PowerShell e usare il comando seguente:

    Get-AzureWebSiteLog -Name websitename -Tail

Il comando consente di connettersi al sito Web specificato dal parametro **-Name** e di iniziare lo streaming delle informazioni alla finestra di PowerShell mentre gli eventi del log si verificano sul sito Web. Eventuali informazioni scritte nei file con estensione txt, log o htm che sono memorizzati nella directory /LogFiles (d:/home/logfiles) verranno trasmesse alla console locale.

Per filtrare eventi specifici, ad esempio gli errori, usare il parametro **-Message**. Ad esempio:

    Get-AzureWebSiteLog -Name websitename -Tail -Message Error

Per filtrare tipi di log specifici, ad esempio HTTP, usare il parametro **-Path**. Ad esempio:

    Get-AzureWebSiteLog -Name websitename -Tail -Path http

Per visualizzare un elenco di percorsi disponibili usare il parametro -ListPath.

> [WACOM.NOTE] Se Azure PowerShell non è stato installato o configurato per l'uso della sottoscrizione di Azure, vedere [Come usare Azure PowerShell][Come usare Azure PowerShell].

### Streaming con gli strumenti da riga di comando di Azure

Per eseguire lo streaming delle informazioni di registrazione, aprire un nuovo prompt dei comandi o una sessione di PowerShell, bash o terminal e immettere il comando seguente:

    azure site log tail websitename

Il comando consente di connettersi al sito Web denominato "nomesitoWeb" e di iniziare il flusso lo streaming delle informazioni alla finestra di PowerShell mentre gli eventi del log si verificano sul sito Web. Eventuali informazioni scritte nei file con estensione txt, log o htm che sono memorizzati nella directory /LogFiles (d:/home/logfiles) verranno trasmesse alla console locale.

Per filtrare eventi specifici, ad esempio gli errori, usare il parametro **-Filter**. Ad esempio:

    azure site log tail websitename --filter Error

Per filtrare tipi di log specifici, ad esempio HTTP, usare il parametro **--Path**. Ad esempio:

    azure site log tail websitename --path http

> [WACOM.NOTE] Se gli strumenti da riga di comando di Azure non sono stati installati o configurati per l'uso della sottoscrizione di Azure, vedere [Come usare gli strumenti da riga di comando di Azure][Come usare gli strumenti da riga di comando di Azure].

<a name="understandlogs"></a>

## Procedura: Comprendere i log di diagnostica

</p>
### Log di diagnostica applicazioni

La diagnostica applicazioni consente di memorizzare le informazioni in un formato specifico per le applicazioni .NET, a seconda della destinazione di archiviazione dei log: file system, archiviazione tabelle o archiviazione BLOB. L'insieme di dati memorizzati di base è lo stesso in tutti e tre i tipi di archiviazione: data e ora in cui si è verificato l'evento, l'ID del processo che ha prodotto l'evento, il tipo di evento (informazioni, avviso, errore) e il messaggio di evento.

**File system**

Ogni riga registrata nel file system o ricevuta mediante streaming apparirà nel formato seguente:

    {Date}  PID[{process id}] {event type/level} {message}

Ad esempio, l'aspetto di un evento di errore sarà simile al seguente:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

La registrazione sul file system fornisce le informazioni più basilari dei tre metodi disponibili, indicando solo l'ora e la data, l'ID di processo, il livello dell'evento e il messaggio.

**Archiviazione tabelle**

Durante la registrazione nell'archiviazione tabelle verranno utilizzate proprietà aggiuntive per facilitare la ricerca dei dati memorizzati nella tabella, oltre a informazioni più granulari sull'evento. Verranno utilizzate le proprietà seguenti (colonne) per ogni entità (riga) memorizzata nella tabella.

<table style="width:100%;border-collapse:collapse">
<thead>
<tr>
<th style="width:45%;border:1px solid black;background-color:#0099dd">
Nome proprietà

</th>
<th style="border:1px solid black;vertical-align:top;background-color:#0099dd">
Valore/formato

</th>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">
PartitionKey

</td>
<td style="border:1px solid black;vertical-align:top">
Data/ora dell'evento nel formato aaaaMMggHH

</td>
</tr>
</thead>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">
RowKey

</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">
Valore GUID che identifica questa entità in modo univoco

</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">
Timestamp

</td>
<td style="border:1px solid black;vertical-align:top">
Data e ora in cui si è verificato l'evento

</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">
EventTickCount

</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">
Data e ora in cui si è verificato l'evento, in formato Tick (maggiore precisione)

</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">
ApplicationName

</td>
<td style="border:1px solid black;vertical-align:top">
Nome del sito Web

</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">
Level

</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">
Livello dell'evento (ad esempio, errore, avviso, informazione)

</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">
EventId

</td>
<td style="border:1px solid black;vertical-align:top">
ID evento di questo evento
Se non specificato, il valore predefinito è 0

</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">
InstanceId

</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">
Istanza del sito Web in cui si è verificato l'evento

</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">
Pid

</td>
<td style="border:1px solid black;vertical-align:top">
ID di processo

</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">
Tid

</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">
ID del thread che ha prodotto l'evento

</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">
Messaggio

</td>
<td style="border:1px solid black;vertical-align:top">
Messaggio dettagliato sull'evento

</td>
</tr>
</table>

</table>
<p><strong>Archiviazione BLOB</strong></p>
<p>Durante la registrazione sull'archiviazione BLOB, i dati vengono memorizzati in formato file con valori delimitati da virgole (CSV). In maniera analoga all'archiviazione tabelle, verranno registrati ulteriori campi per fornire informazioni pi&ugrave; granulari sull'evento. Verranno utilizzate le propriet&agrave; seguenti per ogni riga memorizzata nel file CSV:</p>
<table style="width:100%;border-collapse:collapse">
<thead>
<tr>
<th style="width:45%;border:1px solid black;background-color:#0099dd">Nome propriet&agrave;</th>
<th style="border:1px solid black;vertical-align:top;background-color:#0099dd">Valore/formato</th>
</tr>
</thead>
<tr>
<td style="border:1px solid black;vertical-align:top">Date</td>
<td style="border:1px solid black;vertical-align:top">Data e ora in cui si &egrave; verificato l'evento</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Level</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Livello dell'evento (ad esempio, errore, avviso, informazione)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">ApplicationName</td>
<td style="border:1px solid black;vertical-align:top">Nome del sito Web</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">InstanceId</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Istanza del sito Web in cui si &egrave; verificato l'evento</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">EventTickCount</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Data e ora in cui si &egrave; verificato l'evento, in formato Tick (maggiore precisione)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">EventId</td>
<td style="border:1px solid black;vertical-align:top">ID evento di questo evento<br>Se non specificato, il valore predefinito &egrave; 0</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Pid</td>
<td style="border:1px solid black;vertical-align:top">ID di processo</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Tid</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">ID del thread che ha prodotto l'evento</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Messaggio</td>
<td style="border:1px solid black;vertical-align:top">Messaggio dettagliato sull'evento</td>
</tr>
</table>

L'aspetto dei dati memorizzati in un BLOB sarà simile al seguente:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebsite,6ee38a,635266966128818593,0,3096,9,An error occurred

> [WACOM.NOTE] La prima riga del log conterrà le intestazioni di colonna, come rappresentato in questo esempio.

### Tracce delle richieste non riuscite

Le tracce delle richieste non riuscite vengono memorizzate nei file XML denominati **fr\#\#\#\#\#\#.xml**. Per semplificare la visualizzazione delle informazioni registrate, è disponibile un foglio di stile XSL denominato **freb.xsl** nella stessa directory dei file XML. L'apertura di uno dei file XML in Internet Explorer comporterà l'uso del foglio di stile XSL allo scopo di produrre una visualizzazione formattata delle informazioni di traccia. Il risultato sarà simile al seguente:

![richiesta non riuscita visualizzata nel browser][richiesta non riuscita visualizzata nel browser]

### Log di errore dettagliati

I log di errore dettagliati sono documenti HTML che offrono informazioni più approfondite sugli errori HTTP verificatisi. Poiché si tratta di semplici documenti HTML, è possibile visualizzarli in un browser Web.

### Log del server Web

I log del server Web vengono formattati con il [formato file di log esteso W3C][formato di file di log esteso W3C]. È possibile leggere queste informazioni con un editor di testo oppure analizzarle con utilità come [Log Parser][Log Parser].

> [WACOM.NOTE] I log prodotti da Siti Web di Azure non supportano i campi **s-computername**, **s-ip** o **cs-version**.

<a name="nextsteps"></a>

## Passaggi successivi

</p>
-   [Come monitorare i siti Web][Come monitorare i siti Web]
-   [Esercitazione - Risoluzione dei problemi relativi a Siti Web di Azure][Esercitazione - Risoluzione dei problemi relativi a Siti Web di Azure]
-   [Risoluzione dei problemi di Siti Web di Azure in Visual Studio][Risoluzione dei problemi di Siti Web di Azure in Visual Studio]
-   [Analisi dei log dei siti Web in HDInsight][Analisi dei log dei siti Web in HDInsight]

  [Risoluzione dei problemi di Siti Web di Azure in Visual Studio]: /it-it/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [Informazioni sulla diagnostica del sito Web]: #whatisdiag
  [Procedura: Abilitare la diagnostica]: #enablediag
  [Procedura: Scaricare i log]: #download
  [Procedura: Eseguire lo streaming dei log]: #streamlogs
  [Procedura: Comprendere i log di diagnostica]: #understandlogs
  [Passaggi successivi]: #nextsteps
  [formato di file di log esteso W3C]: http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx
  [System.Diagnostics.Trace]: http://msdn.microsoft.com/it-it/library/36hhw2t6.aspx
  [1]: http://www.windowsazure.com/it-it/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [portale di gestione di Azure]: https://manage.microsoft.com
  [Azure SDK]: http://www.windowsazure.com/it-it/downloads/#
  [Come usare Azure PowerShell]: http://www.windowsazure.com/it-it/develop/nodejs/how-to-guides/powershell-cmdlets/
  [Come usare gli strumenti da riga di comando di Azure]: http://www.windowsazure.com/it-it/develop/nodejs/how-to-guides/command-line-tools/
  [richiesta non riuscita visualizzata nel browser]: ./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png
  [Log Parser]: http://go.microsoft.com/fwlink/?LinkId=246619
  [Come monitorare i siti Web]: /it-it/manage/services/web-sites/how-to-monitor-websites/
  [Esercitazione - Risoluzione dei problemi relativi a Siti Web di Azure]: /it-it/develop/net/best-practices/troubleshooting-web-sites/
  [Analisi dei log dei siti Web in HDInsight]: http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413

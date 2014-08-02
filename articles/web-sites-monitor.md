<properties linkid="manage-services-how-to-monitor-websites" urlDisplayName="How to monitor" pageTitle="How to monitor web sites - Azure service management" metaKeywords="Azure monitoring web sites, Azure Management Portal Monitor, Azure monitoring" description="Learn how to monitor Azure web sites by using the Monitor page in the Management Portal." metaCanonical="" services="web-sites" documentationCenter="" title="How to Monitor Web Sites" authors="" solutions="" manager="" editor="" />

Come monitorare i siti Web
==========================

In Siti Web è disponibile una funzionalità di monitoraggio disponibile tramite la pagina di gestione Monitor. Nella pagina di gestione Monitor sono fornite statistiche sulle prestazioni per un sito Web, come descritto di seguito.

Sommario
--------

-   [Procedura: Aggiungere metriche del sito Web](#websitemetrics)
-   [Procedura: Ricevere avvisi dalle metriche del sito Web](#howtoreceivealerts)
-   [Procedura: Visualizzare le quote di utilizzo per un sito Web](#howtoviewusage)
-   [Procedura: Ridurre l'utilizzo delle risorse](#resourceusage)
-   [Cosa succede quando si supera la quota di utilizzo di una risorsa](#exceeded)
-   [Procedura: Configurare i log di diagnostica e download per un sito Web](#howtoconfigdiagnostics)
-   [Procedura: Monitorare lo stato degli endpoint](#webendpointstatus)

Procedura: Aggiungere metriche del sito Web
-------------------------------------------

1.  Nel [portale di gestione di Azure](http://manage.windowsazure.com/), dalle pagine di gestione del sito fare clic sulla scheda **Monitor** per visualizzare la pagina di gestione **Monitor**. Per impostazione predefinita, il grafico nella pagina **Monitor** visualizza le stesse metriche del grafico nella pagina **Dashboard**.

2.  Per visualizzare altre metriche per il sito Web, fare clic su **Add Metrics** nella parte inferiore della pagina per visualizzare la finestra di dialogo **Choose Metrics**.

3.  Fare clic per selezionare altre metriche da visualizzare nella pagina **Monitor**.

4.  Dopo avere selezionato le metriche che si desidera aggiungere alla pagina **Monitor**, fare clic su **OK**.

5.  Dopo avere aggiunto le metriche alla pagina **Monitor**, fare clic per abilitare o disabilitare la casella di opzione accanto a ogni metrica per aggiungere o rimuovere la metrica dal grafico nella parte superiore della pagina.

6.  Per rimuovere le metriche dalla pagina **Monitor**, selezionare la metrica da rimuovere e quindi fare clic sull'icona **Delete Metric** nella parte inferiore della pagina.

Nell'elenco seguente sono descritte le metriche che è possibile visualizzare nel grafico nella pagina **Monitor**:

-   **CPUTime**: misura dell'utilizzo della CPU del sito Web.
-   **Requests**: conteggio delle richieste client al sito Web.
-   **Data Out**: misura dei dati inviati dal sito Web ai client.
-   **Data In**: misura dei dati ricevuti dal sito Web dai client.
-   **Http Client Errors**: numero di messaggi di errore del client HTTP 4xx inviati.
-   **Http Server Errors**: numero di messaggi di errore del server HTTP 5xx inviati.
-   **Http Successes**: numero di messaggi di operazioni HTTP completate 2xx inviati.
-   **Http Redirects**: numero di messaggi di reindirizzamento HTTP 3xx inviati.
-   **Http 401 errors**: numero di messaggi di errore HTTP 401 - Non autorizzato inviati.
-   **Http 403 errors**: numero di messaggi di errore HTTP 403 - Accesso negato inviati.
-   **Http 404 errors**: numero di messaggi di errore HTTP 404 - Non trovato inviati.
-   **Http 406 errors**: numero di messaggi di errore HTTP 406 - Non accettabile inviati.

Procedura: Ricevere avvisi dalle metriche del sito Web
------------------------------------------------------

Nella modalità del sito Web **Standard** è possibile ricevere avvisi basati sulle metriche di monitoraggio del sito Web. La funzionalità di avviso richiede che venga innanzitutto configurato un endpoint Web per il monitoraggio nella sezione **Monitoring** della pagina **Configure**. Nella pagina **Settings** del portale di gestione di Azure è possibile creare una regola per attivare un avviso quando la metrica scelta raggiunge il valore specificato. È inoltre possibile impostare l'invio di un messaggio di posta elettronica all'attivazione dell'avviso. Per ulteriori informazioni, vedere [Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

Procedura: Visualizzare le quote di utilizzo per un sito Web
------------------------------------------------------------

È possibile configurare i siti Web per l'esecuzione in modalità **Shared** o **Standard** dalla pagina di gestione **Scale** del sito Web. Ogni sottoscrizione di Azure ha accesso a un pool di risorse fornito allo scopo di eseguire fino a 100 siti Web per area in modalità sito Web **Shared**. Il pool di risorse disponibile per ogni sottoscrizione Sito Web per questo scopo è condiviso da altri siti Web configurati per l'esecuzione in modalità **Shared** nella stessa area geografica. Poiché tali risorse sono condivise con altri siti Web, tutte le sottoscrizioni prevedono un limite per l'utilizzo di tali risorse. I limiti applicati all'utilizzo di queste risorse da parte della sottoscrizione sono espressi come quote di utilizzo visualizzate nella sezione relativa alla panoramica sull'utilizzo della pagina di gestione **Dashboard** di ogni sito Web.

**Nota**
 Quando un sito Web è configurato per l'esecuzione in modalità **Standard** gli vengono allocate risorse dedicate pari alle dimensioni delle macchine virtuali **Small** (valore predefinito), **Medium** o **Large** come illustrato nella tabella [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](http://go.microsoft.com/fwlink/?LinkID=309169). Non sono previsti limiti alle risorse che una sottoscrizione può utilizzare per l'esecuzione di siti Web in modalità **Standard**. Tuttavia, il numero di siti Web in modalità **Standard** che è possibile creare per ogni area è pari a 500.

### Visualizzazione delle quote di utilizzo per i siti Web configurati per la modalità sito Web Shared

Per determinare il livello di impatto di un sito Web sulle quote di utilizzo delle risorse, eseguire la procedura seguente:

1.  Aprire la pagina di gestione **Dashboard** del sito Web.
2.  Nella sezione **usage overview** sono visualizzate le quote di utilizzo per **Data Out**, **CPU Time** e **File System Storage**. La barra verde visualizzata per ogni risorsa indica la quota di utilizzo delle risorse di una sottoscrizione attualmente utilizzata dal sito Web corrente e la barra grigia visualizzata per ogni risorsa indica la quota di utilizzo delle risorse di una sottoscrizione è utilizzata da tutti gli altri siti Web in modalità condivisa associati alla propria sottoscrizione Sito Web.

Le quote di utilizzo delle risorse consentono di prevenire l'uso eccessivo delle risorse seguenti:

-   **Data Out**: misura della quantità di dati inviati dai siti Web in esecuzione in modalità **Shared** ai relativi client nell'intervallo di quota corrente (24 ore).
-   **CPU Time**: quantità di tempo di CPU utilizzato dai siti Web in esecuzione in modalità **Shared** per l'intervallo di quota corrente.
-   **File System Storage**: quantità di archiviazione file system utilizzata dai siti Web in esecuzione in modalità **Shared**.

Quando le quote di utilizzo di una sottoscrizione vengono superate, Azure intraprende misure correttive per impedire l'utilizzo in eccesso delle risorse, al fine di impedire ai sottoscrittori di esaurire le risorse a scapito di altri sottoscrittori.

Procedura: Ridurre l'utilizzo delle risorse
-------------------------------------------

Poiché Azure calcola le quote di utilizzo delle risorse misurando le risorse utilizzate dai siti Web in modalità condivisa di una sottoscrizione per un intervallo di quota di 24 ore, tenere conto di quanto riportato di seguito:

-   Con l'aumentare del numero di siti Web configurati per l'esecuzione in modalità condivisa, aumenta anche la probabilità di superare le quote di utilizzo delle risorse in modalità condivisa. Valutare la possibilità di ridurre il numero di siti Web configurati per l'esecuzione in modalità condivisa qualora si superino le quote di utilizzo delle risorse.
-   Allo stesso modo, con l'aumentare del numero delle istanze dei siti Web in esecuzione in modalità condivisa, aumenta anche la probabilità di superare le quote di utilizzo delle risorse in modalità condivisa. Valutare quindi la possibilità di ridurre le istanze aggiuntive dei siti Web in modalità condivisa qualora si superino le quote di utilizzo delle risorse.

Cosa succede quando si supera la quota di utilizzo di una risorsa
-----------------------------------------------------------------

Quando si superano le quote di utilizzo delle risorse di una sottoscrizione durante un intervallo di quota (24 ore), Azure intraprende le misure seguenti:

-   **Data Out**: quando si supera questa quota, Azure arresta tutti i siti Web di una sottoscrizione che sono configurati per l'esecuzione in modalità **Shared** per il tempo residuo della quota di intervallo corrente. Azure avvierà i siti Web all'inizio dell'intervallo di quota successivo.

-   **CPU Time**: quando si supera questa quota, Azure arresta tutti i siti Web di una sottoscrizione che sono configurati per l'esecuzione in modalità **Shared** per il tempo residuo della quota di intervallo corrente. Azure avvierà i siti Web all'inizio dell'intervallo di quota successivo.

-   **File System Storage**: Azure impedisce la distribuzione dei siti Web di una sottoscrizione che sono configurati per l'esecuzione in modalità condivisa se la distribuzione determina il superamento della quota di utilizzo di archiviazione del file system. Quando raggiungono le dimensioni massime consentite dalla quota, le risorse di archiviazione del file system rimangono accessibili per operazioni in lettura, ma tutte le operazioni in scrittura, incluse quelle necessarie per la normale attività del sito Web, risultano bloccate. In questo caso, è possibile configurare uno o più siti Web in esecuzione in modalità condivisa per l'esecuzione in modalità Standard oppure ridurre l'utilizzo delle risorse di archiviazione del file system al di sotto della quota di utilizzo.

Procedura: Configurare i log di diagnostica e download per un sito Web
----------------------------------------------------------------------

La diagnostica è abilitata nella pagina di gestione **Configure** per il sito Web. Sono disponibili due tipi di diagnostica: **diagnostica applicazioni** e **diagnostica del sito**.

#### Diagnostica applicazioni

Nella sezione **application diagnostics** della pagina di gestione **Configure** è possibile controllare la registrazione delle informazioni prodotte dall'applicazione, ad esempio la registrazione di eventi che si verificano all'interno di un'applicazione. Quando nell'applicazione si verifica un errore, è ad esempio possibile presentare all'utente un semplice messaggio di errore e scrivere le informazioni dettagliate relative all'errore nel log per un'analisi successiva.

È possibile abilitare o disabilitare le seguenti opzioni di diagnostica applicazioni:

-   **Application Logging (File System)**: attiva la registrazione delle informazioni prodotte dall'applicazione. Il campo **Logging Level** determina se vengono registrate le informazioni a livello di errore, avviso o informazioni. È inoltre possibile selezionare l'opzione Verbose per registrare tutte le informazioni generate dall'applicazione.

    I log generati da questa impostazione sono archiviati nel file system del sito Web ed è possibile scaricandoli tramite la procedura descritta nella sezione **Download dei file di log per un sito Web** riportata di seguito.

-   **Application Logging (Table Storage)**: attiva la registrazione delle informazioni generate dall'applicazione, come quando si seleziona l'opzione Application Logging (File System). In questo caso, le informazioni del log vengono archiviate in una tabella in un account di archiviazione di Azure.

    Per specificare l'account di archiviazione di Azure e la tabella, scegliere **On**, selezionare il **Logging Level** e quindi scegliere **Manage Table Storage**. Specificare l'account di archiviazione e la tabella da utilizzare o creare una nuova tabella.

    È possibile accedere alle informazioni del log archiviate nella tabella tramite un client di archiviazione di Azure.

-   **Application Logging (Blob storage)**: attiva la registrazione delle informazioni generate dall'applicazione, come quando si seleziona l'opzione Application Logging (Table Storage). In questo caso, le informazioni del log vengono archiviate in un BLOB in un account di archiviazione di Azure.

    Per specificare l'account di archiviazione di Azure e il BLOB, scegliere **On**, selezionare il **Logging Level** e quindi scegliere **Manage Blob Storage**. Specificare l'account di archiviazione, il contenitore BLOB e il nome BLOB da utilizzare o creare un contenitore e un BLOB nuovi.

Per ulteriori informazioni sugli account di archiviazione di Azure, vedere [Come gestire gli account di archiviazione](https://www.windowsazure.com/en-us/manage/services/storage/how-to-manage-a-storage-account/).

**Nota**

La registrazione delle informazioni delle applicazioni nell'archiviazione BLOB o tabella è supportata solo per le applicazioni .NET.

Poiché la registrazione dell'applicazione nell'archivio richiede l'utilizzo di un client di archiviazione per visualizzare i dati di log, risulta più utile quando si prevede di utilizzare un servizio o un'applicazione che riconosce come leggere ed elaborare i dati direttamente dall'archivio tabelle o BLOB di Azure. La registrazione nel file system genera file che possono essere scaricati nel computer locale mediante FTP o altre utilità descritte più avanti in questa sezione.

**Nota**

È possibile abilitare contemporaneamente **Application diagnostics (file system)**, **Application diagnostics (table storage)** e **Application diagnostics (blob storage)** ed effettuare configurazioni del livello di log individuali. Ad esempio, è possibile registrare gli errori e gli avvisi nell'archivio come soluzione di registrazione a lungo termine e abilitare la registrazione del file system a livello dettagliato dopo avere instrumentato il codice dell'applicazione per la risoluzione di un problema.

**Nota**

È inoltre possibile abilitare la diagnostica da Azure PowerShell mediante il cmdlet **Set-AzureWebsite**.

Se Azure PowerShell non è stato installato o configurato per l'utilizzo della sottoscrizione di Azure, vedere [Come utilizzare Azure PowerShell](http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/powershell-cmdlets/).

**Nota**

La registrazione dell'applicazione si basa sulle informazioni di log generate dall'applicazione. Il metodo utilizzato per generare le informazioni di log e il formato delle informazioni sono specifici del linguaggio in cui è scritta l'applicazione. Per informazioni specifiche del linguaggio per l'utilizzo della registrazione delle applicazioni, vedere gli articoli seguenti:

-   **.NET**: [Abilitazione della registrazione diagnostica per Siti Web di Azure](/en-us/develop/net/common-tasks/diagnostics-logging-and-instrumentation/)
-   **Node.js**: [Come eseguire il debug di un'applicazione Node.js in Siti Web di Azure](/en-us/develop/nodejs/how-to-guides/Debug-Website/)

La registrazione delle informazioni delle applicazioni nell'archiviazione BLOB o tabella è supportata solo per le applicazioni .NET.

#### Diagnostica del sito

La sezione **site diagnostics** della pagina di gestione **Configure** consente di controllare la registrazione effettuata dal server Web, ad esempio la registrazione delle richieste Web, la mancata presentazione delle pagine o il tempo impiegato per presentare una pagina. È possibile abilitare o disabilitare le opzioni seguenti:

-   **Web Server Logging**: attivare la registrazione del server Web per salvare i log del sito Web utilizzando il formato di file di log esteso W3C. La registrazione del server Web genera un record di tutte le richieste in ingresso al sito Web. Il record contiene informazioni quali indirizzo IP client, URI richiesto, codice di stato HTTP della risposta e stringa agente utente del client. È possibile salvare i log in un account di archiviazione di Azure o nel file system.

Per salvare i log del server Web in un account di archiviazione di Azure, scegliere **Storage** e quindi **manage storage** per specificare un account di archiviazione e un contenitore BLOB di Azure in cui conservare i log. Per ulteriori informazioni sugli account di archiviazione di Azure, vedere [Come gestire gli account di archiviazione](https://www.windowsazure.com/en-us/manage/services/storage/how-to-manage-a-storage-account/).

Per salvare i log del server Web nel file system, scegliere **File System**. Verrà abilitata la casella **Quota** in cui è possibile impostare la quantità massima di spazio su disco per i file di log. La dimensione minima è 25 MB, la massima è di 100 MB. La dimensione predefinita è 35 MB.

Per impostazione predefinita, i log del server Web non vengono mai eliminati. Per specificare un periodo di tempo trascorso il quale i log vengono automaticamente eliminati, selezionare **Set Retention** e immettere il numero di giorni per la conservazione dei log nella casella **Retention Period**. Questa impostazione è disponibile per entrambe le opzioni di salvataggio, nell'account di archiviazione di Azure e nel file system.

-   **Detailed Error Messages**: attivare la registrazione dei messaggi di errore dettagliati per registrare ulteriori informazioni sugli errori HTTP (codici di stato maggiori di 400).

-   **Failed Request Tracing**: attivare la traccia delle richieste non riuscite per acquisire informazioni sulle richieste client non riuscite, ad esempio un codice di stato HTTP della serie 400. La traccia delle richieste non riuscite genera un documento XML contenente una traccia dei moduli di richiesta passati tramite IIS, i dettagli restituiti da modulo e l'ora in cui è stato richiamato il modulo. Queste informazioni possono essere utilizzate per isolare il componente in cui si è verificato l'errore.

Dopo avere abilitato la diagnostica per un sito Web, fare clic sull'icona **Save** nella parte inferiore della pagina di gestione **Configure** per applicare le opzioni impostate.

**Importante**

La registrazione e la traccia sottopongono il sito Web a una domanda significativa. È consigliabile disattivare la registrazione e la traccia non appena sono stati riprodotti i problemi da risolvere.

### Configurazione avanzata

È possibile modificare ulteriormente la diagnostica aggiungendo coppie chiave-valore nella sezione **app settings** della pagina di gestione **Configure**. Di seguito sono riportate le impostazioni che possono essere configurate da **app settings**:

**DIAGNOSTICS\_TEXTTRACELOGDIRECTORY**

-   Posizione in cui verranno salvati i log dell'applicazione, relativi alla radice Web.

-   Valore predefinito: ..\\..\\LogFiles\\Application

**DIAGNOSTICS\_TEXTTRACEMAXBUFFERSIZEBYTES**

-   Dimensione massima del buffer da utilizzare durante l'acquisizione dei log dell'applicazione. Le informazioni vengono inizialmente scritte nel buffer prima di essere scaricate nel file o nell'archivio. Se nel buffer vengono scritte nuove informazioni prima che possano essere scaricate, è possibile che le informazioni registrate in precedenza vengano perse. Se l'applicazione genera grandi quantità di informazioni di log, valutare la possibilità di aumentare la dimensione del buffer.

-   Valore predefinito: 10 MB

**DIAGNOSTICS\_TEXTTRACEMAXLOGFOLDERSIZEBYTES**

-   Dimensione massima della cartella **Application** in cui sono archiviati le informazioni diagnostiche dell'applicazione scritte nel file.

-   Valore predefinito: 1 MB

### Download dei file di log per un sito Web

È possibile scaricare i file di log mediante FTP, Azure PowerShell o gli strumenti da riga di comando di Azure.

**FTP**

1.  Aprire la pagina di gestione **Dashboard** del sito Web e prendere nota del sito FTP indicato in **Diagnostics Logs** e dell'account visualizzato in **Deployment User**. Il sito FTP è il sito in cui si trovano i file di log e l'account visualizzato in Deployment User è l'account utilizzato per eseguire l'autenticazione al sito FTP.
2.  Se non sono ancora state create le credenziali di distribuzione, l'account visualizzato in **Deployment User** indica **Not set**. In questo caso, è necessario creare le credenziali di distribuzione come descritto nella sezione Reset Deployment Credentials del Dashboard in quanto è necessario utilizzare queste credenziali per eseguire l'autenticazione al sito FTP in cui sono archiviati i file di log. In Azure non è supportata l'autenticazione al sito FTP mediante le credenziali Live ID.
3.  Valutare l'utilizzo di un client FTP, ad esempio [FileZilla](http://go.microsoft.com/fwlink/?LinkId=247914) per connettersi al sito FTP. Un client FTP semplifica l'immissione delle credenziali e la visualizzazione delle cartelle in un sito FTP rispetto al browser.
4.  Copiare i file di log dal sito FTP al computer locale.

**Azure PowerShell**

1.  Dalla **schermata Start** o dal **menu Start** cercare **Azure PowerShell**. Fare clic con il pulsante destro del mouse su **Azure PowerShell** e scegliere **Esegui come amministratore**.

    **Nota**

    Se **Azure PowerShell** non è installato, vedere l'[introduzione ai cmdlet di Azure PowerShell](http://msdn.microsoft.com/en-us/library/windowsazure/jj554332.aspx) per informazioni sull'installazione e la configurazione.

2.  Dal prompt di Azure PowerShell, utilizzare i comandi seguenti per scaricare i file di log:

         Save-AzureWebSiteLog -Name websitename

    Verranno scaricati i file di log per il sito Web specificato da **nomesitoWeb** e verranno salvati in un file denominato **log.zip** nella directory corrente.

    È inoltre possibile visualizzare un flusso in diretta degli eventi di log utilizzando il comando seguente:

         Get-AzureWebSiteLog -Name websitename -Tail

    Verranno visualizzate le informazioni di log nel prompt di Azure PowerShell man mano che si verificano.

**Strumenti da riga di comando di Azure**

Aprire un nuovo prompt dei comandi o una sessione di PowerShell, bash o terminal e utilizzare il comando seguente per scaricare i file di log:

    azure site log download nomesitoWeb

Verranno scaricati i file di log per il sito Web specificato da **nomesitoWeb** e verranno salvati in un file denominato **log.zip** nella directory corrente.

È inoltre possibile visualizzare un flusso in diretta degli eventi di log utilizzando il comando seguente:

    azure site log tail nomesitoWeb

Le informazioni di log verranno visualizzate al prompt dei comandi o nella sessione di PowerShell, bash o terminal da cui viene eseguito il comando.

**Nota**

Se il comando **azure**non è installato, vedere [Come utilizzare gli strumenti da riga di comando di Azure](http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/command-line-tools/) per informazioni sull'installazione e la configurazione.

### Lettura dei file di log

I file di log generati dopo avere abilitato la registrazione e/o la traccia per un sito Web variano in base al livello di registrazione/traccia impostato nella pagina di gestione Configure per il sito Web. Di seguito sono indicati i percorsi dei file di log e i modi in cui è possibile analizzarli:

**Tipo di file di log: Application Logging**

-   Percorso /LogFiles/Application/. In questa cartella sono presenti uno o più file di testo contenenti le informazioni generate dalla registrazione dell'applicazione. Le informazioni registrate includono data e ora, ID processo (PID) dell'applicazione e il valore generato dall'instrumentazione dell'applicazione.

-   Leggere i file con: Un editor di file o un parser in grado di riconoscere i valori generati dall'applicazione

**Tipo di file di log: Traccia delle richieste non riuscite**

-   Percorso: /LogFiles/W3SVC########\#/. Questa cartella contiene un file XSL e uno o più file XML. Verificare che il file XSL venga scaricato nella stessa directory dei file XML in quanto il file XSL fornisce le funzionalità per la formattazione e il filtro dei contenuti del file XML per la visualizzazione in Internet Explorer.

-   Leggere i file con: Internet Explorer

**Tipo di file di log: Detailed Error Logging**

-   Percorso: /LogFiles/DetailedErrors/. La cartella /LogFiles/DetailedErrors/ contiene uno o più file HTM che forniscono informazioni dettagliate relative agli eventuali errori HTTP che si sono verificati.

-   Leggere i file con: Web browser

Nei file HTM sono incluse le sezioni seguenti:

-   **Detailed Error Information:** Include informazioni sull'errore, ad esempio *Module*, *Handler*, *Error Code* e *Requested URL*.

-   **Most likely causes:** Visualizza un elenco di cause possibili per l'errore.

-   **Things you can try:** Visualizza un elenco di soluzioni possibili per risolvere il problema segnalato dall'errore.

-   **Links and More Information**: Fornisce informazioni di riepilogo aggiuntive sull'errore ed eventualmente i collegamenti ad altre risorse, ad esempio articoli della Microsoft Knowledge Base.

**Tipo di file di log: Registrazione del server Web**

-   Percorso: /LogFiles/http/RawLogs. Le informazioni archiviate nei file sono formattate mediante il [formato di log esteso W3C](http://go.microsoft.com/fwlink/?LinkID=90561). I campi s-computername, s-ip e cs-version non sono utilizzati da Siti Web di Azure.

-   Leggere i file con: Log Parser. Utilizzato per l'esecuzione di analisi e di query sui file di log IIS. Log Parser 2.2 è disponibile nell'Area download Microsoft all'indirizzo <http://go.microsoft.com/fwlink/?LinkId=246619>.

Procedura: Monitorare lo stato degli endpoint
---------------------------------------------

Questa funzionalità, disponibile nella modalità **Standard**, consente di monitorare fino a 2 endpoint da un massimo di 3 posizioni geografiche.

Il monitoraggio degli endpoint configura i test Web da posizioni distribuite a livello geografico che testano il tempo di risposta e di attività degli URL Web. Il test esegue un'operazione GET HTTP sull'URL Web per determinare il tempo di risposta e di attività da ogni posizione. Ogni posizione configurata esegue un testo ogni cinque minuti.

Il tempo di attività viene monitorato mediante codici di risposta HTTP e il tempo di risposta è misurato in millisecondi. Il tempo di attività si considera al 100% quando il tempo di risposta è inferiore a 30 secondi e il codice di stato HTTP è minore di 400. Il tempo di attività è pari allo 0% quando il tempo di risposta è maggiore di 30 secondi o il codice di stato HTTP è maggiore di 400.

Dopo avere configurato il monitoraggio degli endpoint, è possibile eseguire il drill-down nei singoli endpoint per visualizzare i dettagli relativi a tempo di risposta e stato di attività nell'intervallo di monitoraggio da ognuna delle posizioni di test.

**Per configurare il monitoraggio degli endpoint:**

1.  Aprire **Web Sites**. Fare clic sul nome del sito Web da configurare.
2.  Fare clic sulla scheda **Configure**.
3.  Passare alla sezione **Monitoring** per immettere le impostazioni dell'endpoint.
4.  Immettere un nome per l'endpoint.
5.  Immettere l'URL per il servizio da monitorare. Ad esempio, <http://contoso.cloudapp.net>.
6.  Selezionare una o più posizioni geografiche nell'elenco.
7.  Facoltativamente, ripetere i passaggi precedenti per creare un secondo endpoint.
8.  Fare clic su **Save**. Potrebbero essere necessari alcuni istanti prima che i dati di monitoraggio dell'endpoint Web siano disponibili nelle schede **Dashboard** e **Monitor**.

Per ulteriori informazioni sul monitoraggio degli endpoint del sito Web, vedere i video seguenti:

-   [Scott Guthrie: Introduzione a Siti Web di Azure e configurazione del monitoraggio degli endpoint](http://www.windowsazure.com/en-us/documentation/videos/websites-and-endpoint-monitoring-scottgu/)

-   [Mantenere attivi i siti Web di Azure e monitorare gli endpoint, con Stefan Schackow](http://www.windowsazure.com/en-us/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/)



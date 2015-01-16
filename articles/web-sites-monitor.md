<properties urlDisplayName="How to monitor" pageTitle="Come monitorare i siti Web - Gestione servizi di Azure" metaKeywords="Azure monitoring web sites, Azure Management Portal Monitor, Azure monitoring" description="Informazioni su come monitorare i siti Web di Azure usando la pagina di monitoraggio nel portale di gestione." metaCanonical="" services="web-sites" documentationCenter="" title="How to Monitor Websites" authors="cephalin" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="cephalin" />



#<a name="howtomonitor"></a>Come monitorare i siti Web

In Siti Web è disponibile una funzionalità di monitoraggio tramite la pagina di gestione Monitoraggio, che fornisce statistiche sulle prestazioni per un sito Web, come descritto di seguito.

## Sommario ##
- [Procedura: Aggiungere metriche del sito Web](#websitemetrics)
- [Procedura: Ricevere avvisi dalle metriche del sito Web](#howtoreceivealerts)
- [Procedura: Visualizzare le quote di uso per un sito Web](#howtoviewusage)
- [Procedura: Evitare di superare le quote](#resourceusage)
- [Procedura: Configurare i log di diagnostica e download per un sito Web](#howtoconfigdiagnostics)
- [Procedura: Monitorare lo stato degli endpoint](#webendpointstatus)

##<a name="websitemetrics"></a>Procedura: Aggiungere metriche del sito Web
1. Nel [portale di gestione di Azure](http://manage.windowsazure.com/), dalle pagine di gestione del sito fare clic sulla scheda **Monitoraggio** per visualizzare la pagina di gestione **Monitoraggio**. Per impostazione predefinita, il grafico nella pagina **Monitoraggio** visualizza le stesse metriche del grafico nella pagina **Dashboard**. 

2. Per visualizzare altre metriche per il sito Web, fare clic su **Aggiungi metriche** nella parte inferiore della pagina per visualizzare la finestra di dialogo **Scegli metriche**. 

3. Fare clic per selezionare altre metriche da visualizzare nella pagina **Monitoraggio**. 

4. Dopo avere selezionato le metriche che si desidera aggiungere alla pagina **Monitoraggio**, fare clic su OK. 

5. Dopo avere aggiunto le metriche alla pagina **Monitoraggio**, fare clic per abilitare o disabilitare la casella di opzione accanto a ogni metrica per aggiungere o rimuovere la metrica dal grafico nella parte superiore della pagina.

6. Per rimuovere le metriche dalla pagina **Monitoraggio**, selezionare la metrica da rimuovere e quindi fare clic sull'icona **Elimina metrica** nella parte inferiore della pagina.

##<a name="howtoreceivealerts"></a>Procedura: Ricevere avvisi dalle metriche del sito Web
Nella modalità del sito Web **Standard** è possibile ricevere avvisi basati sulle metriche di monitoraggio del sito Web. La funzionalità di avviso richiede che venga innanzitutto configurato un endpoint Web per il monitoraggio nella sezione **Monitoraggio** della pagina **Configura**. Nella pagina **Impostazioni** del portale di gestione di Azure è possibile creare una regola per attivare un avviso quando la metrica scelta raggiunge il valore specificato. È inoltre possibile impostare l'invio di un messaggio di posta elettronica all'attivazione dell'avviso. Per altre informazioni, vedere [Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure](http://go.microsoft.com/fwlink/?LinkId=309356).  

##<a name="howtoviewusage"></a>Procedura: Visualizzare le quote di uso per un sito Web

È possibile configurare i siti Web per l'esecuzione in modalità **Condivisa** o **Standard** dalla pagina di gestione **Scala** del sito Web. Ogni sottoscrizione Azure ha accesso a un pool di risorse fornito allo scopo di eseguire un massimo di 100 siti Web per area in modalità sito Web **Condivisa**. Il pool di risorse disponibile per ogni sottoscrizione di Sito Web per questo scopo è condiviso da altri siti Web configurati per l'esecuzione in modalità **Condivisa** nella stessa area geografica. Poiché tali risorse sono condivise con altri siti Web, tutte le sottoscrizioni prevedono un limite per l'uso di tali risorse. I limiti applicati all'uso di queste risorse da parte della sottoscrizione sono espressi come quote di uso visualizzate nella sezione relativa alla panoramica sull'uso della pagina di gestione **Dashboard** di ogni sito Web.

>[WACOM.NOTE] Quando un sito Web è configurato per l'esecuzione in modalità **Standard** gli vengono allocate risorse dedicate pari alle dimensioni delle macchine virtuali **Piccola** (valore predefinito), **Media** o **Grande**, come illustrato nella tabella [Dimensioni delle macchine virtuali e dei servizi cloud per Azure][vmsizes]. Non sono previsti limiti alle risorse che una sottoscrizione può usare per l'esecuzione di siti Web in modalità **Standard**. Tuttavia, il numero di siti Web in modalità **Standard** che è possibile creare per ogni area è pari a 500.
 
### Procedura: Visualizzazione delle quote di uso per i siti Web configurati per la modalità Condivisa ###
Per determinare il livello di impatto di un sito Web sulle quote di uso delle risorse, eseguire questa procedura:

1. Aprire la pagina di gestione **Dashboard** del sito Web.
2. Nella sezione **panoramica sull'uso** vengono visualizzate le quote di uso per il rispettivo piano di hosting Web, cioè un subset dei seguenti:
	-	**Dati in uscita**, **Tempo CPU** e **Memoria**:quando si supera questa quota, Azure arresta il sito Web per il tempo residuo della quota di intervallo corrente. Azure avvierà il sito Web all'inizio dell'intervallo di quota successivo.
	-	**Archiviazione file system**: quando si raggiunge questa quota, le risorse di archiviazione del file system rimangono accessibili per operazioni in lettura, ma tutte le operazioni in scrittura, incluse quelle necessarie per la normale attività del sito Web, risultano bloccate. Le operazioni in scrittura riprenderanno quando si riduce l'uso dei file o si sposta il sito Web in un piano di hosting Web con una quota maggiore.
	-	**Risorse collegate**: sono visualizzate qui anche le quote relative a qualsiasi risorsa collegata, come database o archiviazione.   

	È possibile applicare alcune quote per ogni piano di hosting Web, mentre è possibile applicarne altre per ogni sito. Per informazioni dettagliate sulle quote di uso per ogni piano di hosting Web, vedere i [Limiti relativi ai siti Web](http://azure.microsoft.com/it-it/documentation/articles/azure-subscription-service-limits/#websiteslimits).


##<a name="resourceusage"></a>Procedura: Evitare di superare le quote

Le quote non sono una questione di prestazioni o costi, ma rappresentano lo strumento per mezzo del quale Azure governa l'uso delle risorse in un ambiente multitenant, prevenendo l'uso eccessivo di risorse condivise da parte dei tenant. Poiché il superamento delle quota implica tempi di inattività o funzionalità ridotta per il sito Web, si consideri quanto segue in modo da garantire il funzionamento del sito quando si stanno per raggiungere le quote:

- Spostare il sito (o i siti) Web in un piano di hosting Web di livello superiore per sfruttare una quota più ampia. Ad esempio, l'unica quota per i piani **Basic** e **Standard** è Archiviazione file system. 
- Con l'aumentare del numero delle istanze di un sito Web, aumenta anche la probabilità di superare le quote di uso delle risorse condivise. Se opportuno, considerare il ridimensionamento di ulteriori istanze di un sito Web quando si superano le quote delle risorse condivise.


##<a name="howtoconfigdiagnostics"></a>Procedura: Configurare i log di diagnostica e download per un sito Web

La diagnostica è abilitata nella pagina di gestione **Configura** per il sito Web. Sono disponibili due tipi di diagnostica: **diagnostica applicazioni** e **diagnostica del sito**.

####Diagnostica applicazioni####

Nella sezione **diagnostica applicazioni** della pagina di gestione **Configura** è possibile controllare la registrazione delle informazioni prodotte dall'applicazione, ad esempio la registrazione di eventi che si verificano all'interno di un'applicazione. Quando nell'applicazione si verifica un errore, è ad esempio possibile presentare all'utente un semplice messaggio di errore e scrivere le informazioni dettagliate relative all'errore nel log per un'analisi successiva.

È possibile abilitare o disabilitare le seguenti opzioni di diagnostica applicazioni:

- **Registrazione applicazioni (file system)**: attiva la registrazione delle informazioni prodotte dall'applicazione. Il campo **Livello di registrazione** determina se vengono registrate le informazioni a livello di errore, avviso o informazioni. È inoltre possibile selezionare l'opzione Verbose per registrare tutte le informazioni generate dall'applicazione.

	I log generati da questa impostazione sono archiviati nel file system del sito Web ed è possibile scaricarli tramite la procedura descritta nella sezione **Download dei file di log per un sito Web** riportata di seguito.

- **Registrazione applicazioni (archiviazione tabella)**: attiva la registrazione delle informazioni generate dall'applicazione, come quando si seleziona l'opzione Registrazione applicazioni (file system). In questo caso, le informazioni del log vengono archiviate in una tabella in un account di archiviazione di Azure.

	Per specificare l'account di archiviazione di Azure e la tabella, scegliere **On**, selezionare il **Livello di registrazione** e quindi scegliere **Gestisci archivio tabelle**. Specificare l'account di archiviazione e la tabella da usare o creare una nuova tabella.

	È possibile accedere alle informazioni del log archiviate nella tabella tramite un client di archiviazione di Azure.

- **Registrazione applicazioni (archiviazione BLOB)**: attiva la registrazione delle informazioni generate dall'applicazione, come quando si seleziona l'opzione Registrazione applicazioni (archiviazione tabelle). In questo caso, le informazioni del log vengono archiviate in un BLOB in un account di archiviazione di Azure.

	Per specificare l'account di archiviazione di Azure e il BLOB, scegliere **On**, selezionare il **Livello di registrazione** e quindi scegliere **Gestisci archivio BLOB**. Specificare l'account di archiviazione, il contenitore BLOB e il nome BLOB da usare o creare un contenitore e un BLOB nuovi.

Per altre informazioni sugli account di archiviazione di Azure, vedere [Come gestire gli account di archiviazione](https://www.windowsazure.com/it-it/manage/services/storage/how-to-manage-a-storage-account/).

<div class="dev-callout"on> 
<b>Note</b> 
<p>Application logging to table or blob storage is only supported for .NET applications.</p> </div>

Since application logging to storage requires using a storage client to view the log data, it is most useful when you plan on using a service or application that understands how to read and process the data directly from Azure Table or Blob Storage. Logging to the file system produces files that can be downloaded to your local computer using FTP or other utilities as described later in this section.

<div class="dev-callout"> 
	<b>Nota</b> 
	<p><b>È possibile abilitare contemporaneamente Diagnostica applicazioni (file system)</b>, <b>Diagnostica applicazioni (archiviazione tabelle)</b> e <b>Diagnostica applicazioni (archiviazione BLOB)</b> ed effettuare configurazioni del livello di log individuali. Ad esempio, è possibile registrare gli errori e gli avvisi nell'archivio come soluzione di registrazione a lungo termine e abilitare la registrazione del file system a livello dettagliato dopo avere instrumentato il codice dell'applicazione per la risoluzione di un problema.</p> </div>

<div class="dev-callout"> 
	<b>Nota</b> 
	<p>È inoltre possibile abilitare la diagnostica da Azure PowerShell mediante il cmdlet <b>Set-AzureWebsite</b>.</p><p>Se Azure PowerShell non è stato installato o configurato per l'uso della sottoscrizione di Azure, vedere <a href="http://www.windowsazure.com/it-it/develop/nodejs/how-to-guides/powershell-cmdlets/">Come usare Azure PowerShell</a>.</p></div>

<div class="dev-callout"> 
<b>Nota</b> 
<p>La registrazione dell'applicazione si basa sulle informazioni di log generate dall'applicazione. Il metodo usato per generare le informazioni di log e il formato delle informazioni sono specifici del linguaggio in cui è scritta l'applicazione. Per informazioni specifiche del linguaggio per l'uso della registrazione delle applicazioni, vedere gli articoli seguenti:</p>
<ul>
<li><b>.NET</b> - <a href="/it-it/develop/net/common-tasks/diagnostics-logging-and-instrumentation/">Abilitare la registrazione diagnostica per Siti Web di Azure</a></li>
<li><b>Node.js</b> - <a href="/it-it/develop/nodejs/how-to-guides/Debug-Website/">Come eseguire il debug di un'applicazione Node.js in Siti Web di Azure</a></li>
</ul>
<p>La registrazione delle informazioni delle applicazioni nell'archiviazione BLOB o tabella è supportata solo per le applicazioni .NET.</p>
</div>

####Diagnostica del sito####

La sezione **diagnostica del sito** della pagina di gestione **Configura** consente di controllare la registrazione effettuata dal server Web, ad esempio la registrazione delle richieste Web, la mancata presentazione delle pagine o il tempo impiegato per presentare una pagina. È possibile abilitare o disabilitare le opzioni seguenti:

- **Registrazione del server Web**: attivare la registrazione del server Web per salvare i log del sito Web usando il formato di file di log esteso W3C. La registrazione del server Web genera un record di tutte le richieste in ingresso al sito Web. Il record contiene informazioni quali indirizzo IP client, URI richiesto, codice di stato HTTP della risposta e stringa agente utente del client. È possibile salvare i log in un account di archiviazione di Azure o nel file system.

 Per salvare i log del server Web in un account di archiviazione di Azure, scegliere **Archiviazione** e quindi **gestisci archiviazione** per specificare un account di archiviazione e un contenitore BLOB di Azure in cui conservare i log. Per altre informazioni sugli account di archiviazione di Azure, vedere [Come gestire gli account di archiviazione](https://www.windowsazure.com/it-it/manage/services/storage/how-to-manage-a-storage-account/).

   Per salvare i log del server Web nel file system, scegliere **File system**. Verrà abilitata la casella **Quota** in cui è possibile impostare la quantità massima di spazio su disco per i file di log. La dimensione minima è 25 MB, la massima è di 100 MB. La dimensione predefinita è 35 MB.

 Per impostazione predefinita, i log del server Web non vengono mai eliminati. Per specificare un periodo di tempo trascorso il quale i log vengono automaticamente eliminati, selezionare **Imposta mantenimento** e immettere il numero di giorni per la conservazione dei log nella casella **Imposta mantenimento**. Questa impostazione è disponibile per entrambe le opzioni di salvataggio, nell'account di archiviazione di Azure e nel file system.

- **Messaggi di errore dettagliati**: attivare la registrazione dei messaggi di errore dettagliati per registrare ulteriori informazioni sugli errori HTTP (codici di stato maggiori di 400).

- **Traccia delle richieste non riuscite**: attivare la traccia delle richieste non riuscite per acquisire informazioni sulle richieste client non riuscite, ad esempio un codice di stato HTTP della serie 400.  La traccia delle richieste non riuscite genera un documento XML contenente una traccia dei moduli di richiesta passati tramite IIS, i dettagli restituiti da modulo e l'ora in cui è stato richiamato il modulo. Queste informazioni possono essere usate per isolare il componente in cui si è verificato l'errore.


Dopo avere abilitato la diagnostica per un sito Web, fare clic sull'icona **Salva** nella parte inferiore della pagina di gestione **Configura** per applicare le opzioni impostate.

<div class="dev-callout"> 
<b>Importante</b> 
<p>La registrazione e la traccia sottopongono il sito Web a un carico significativo. È consigliabile disattivare la registrazione e la traccia non appena sono stati riprodotti i problemi da risolvere.</p> 
</div>

###Configurazione avanzata###

È possibile modificare ulteriormente la diagnostica aggiungendo coppie chiave-valore nella sezione **Impostazioni app** della pagina di gestione **Configura**. Di seguito sono riportate le impostazioni che possono essere configurate da **Impostazioni app**:

**DIAGNOSTICS_TEXTTRACELOGDIRECTORY**

- Posizione in cui verranno salvati i log dell'applicazione, relativi alla radice Web.

- Valore predefinito: ..\\..\\LogFiles\\Application

**DIAGNOSTICS_TEXTTRACEMAXBUFFERSIZEBYTES**

- Dimensione massima del buffer da usare durante l'acquisizione dei log dell'applicazione. Le informazioni vengono inizialmente scritte nel buffer prima di essere scaricate nel file o nell'archivio. Se nel buffer vengono scritte nuove informazioni prima che possano essere scaricate, è possibile che le informazioni registrate in precedenza vengano perse. Se l'applicazione genera grandi quantità di informazioni di log, valutare la possibilità di aumentare la dimensione del buffer.

- Valore predefinito: 10MB

**DIAGNOSTICS_TEXTTRACEMAXLOGFOLDERSIZEBYTES**

- Dimensione massima della cartella **Applicazione** in cui sono archiviati le informazioni diagnostiche dell'applicazione scritte nel file.

- Valore predefinito: 1MB

###Download dei file di log per un sito Web###

È possibile scaricare i file di log mediante FTP, Azure PowerShell o gli strumenti da riga di comando di Azure.

**FTP**

1. Aprire la pagina di gestione **Dashboard** del sito Web e prendere nota del sito FTP indicato in **Log di diagnostica** e dell'account visualizzato in **Utente distribuzione**. Il sito FTP è il sito in cui si trovano i file di log e l'account visualizzato in Deployment User è l'account usato per eseguire l'autenticazione al sito FTP.
2. Se non sono ancora state create le credenziali di distribuzione, l'account visualizzato in **Utente distribuzione** indica **Non impostato**. In questo caso, è necessario creare le credenziali di distribuzione come descritto nella sezione Reset Deployment Credentials del Dashboard in quanto è necessario usare queste credenziali per eseguire l'autenticazione al sito FTP in cui sono archiviati i file di log. In Azure non è supportata l'autenticazione al sito FTP mediante le credenziali Live ID.
3. Valutare l'uso di un client FTP, ad esempio [FileZilla][fzilla] per connettersi al sito FTP. Un client FTP semplifica l'immissione delle credenziali e la visualizzazione delle cartelle in un sito FTP rispetto al browser.
4. Copiare i file di log dal sito FTP al computer locale.

**Azure PowerShell**

1. Dalla **schermata Start** o dal **menu Start** cercare **Azure PowerShell**. Fare clic con il pulsante destro del mouse su **Azure PowerShell** e scegliere **Esegui come amministratore**.

	<div class="dev-callout"> 
	<b>Nota</b> 
	<p>Se <b>Azure PowerShell</b> non è installato, vedere l'<a href="http://msdn.microsoft.com/it-it/library/windowsazure/jj554332.aspx">introduzione ai cmdlet di Azure PowerShell</a> per informazioni sull'installazione e la configurazione.</p> 
	</div>

2. Dal prompt di Azure PowerShell, usare i comandi seguenti per scaricare i file di log:

		Save-AzureWebSiteLog -Name websitename

	Verranno scaricati i file di log per il sito Web specificato da **nomesitoWeb** e verranno salvati in un file denominato **log.zip** nella directory corrente.
	
	È inoltre possibile visualizzare un flusso in diretta degli eventi di log usando il comando seguente:

		Get-AzureWebSiteLog -Name websitename -Tail

	Verranno visualizzate le informazioni di log nel prompt di Azure PowerShell man mano che si verificano.

**Strumenti da riga di comando di Azure**

Aprire un nuovo prompt dei comandi o una sessione di PowerShell, bash o terminal e usare il comando seguente per scaricare i file di log:

	azure site log download websitename

Verranno scaricati i file di log per il sito Web specificato da **nomesitoWeb** e verranno salvati in un file denominato **log.zip** nella directory corrente.

È inoltre possibile visualizzare un flusso in diretta degli eventi di log usando il comando seguente:

	azure site log tail websitename

Le informazioni di log verranno visualizzate al prompt dei comandi o nella sessione di PowerShell, bash o terminal da cui viene eseguito il comando.

<div class="dev-callout"> 
<b>Nota</b> 
<p>Se il comando <b>azure</b> non è installato, vedere <a href="http://www.windowsazure.com/it-it/develop/nodejs/how-to-guides/command-line-tools/">Come usare gli strumenti da riga di comando di Azure</a> per informazioni sull'installazione e la configurazione.</p>
</div>

###Lettura dei file di log###

I file di log generati dopo avere abilitato la registrazione e/o la traccia per un sito Web variano in base al livello di registrazione/traccia impostato nella pagina di gestione Configura per il sito Web. Di seguito sono indicati i percorsi dei file di log e i modi in cui è possibile analizzarli:

**Tipo di file di log: Application Logging**

- Percorso /LogFiles/Application/. In questa cartella sono presenti uno o più file di testo contenenti le informazioni generate dalla registrazione dell'applicazione. Le informazioni registrate includono data e ora, ID processo (PID) dell'applicazione e il valore generato dall'instrumentazione dell'applicazione.

- Leggere i file con: Un editor di file o un parser in grado di riconoscere i valori generati dall'applicazione

**Tipo di file di log: Traccia delle richieste non riuscite**

- Location: /LogFiles/W3SVC#########/. Questa cartella contiene un file XSL e uno o più file XML. Verificare che il file XSL venga scaricato nella stessa directory dei file XML in quanto il file XSL fornisce le funzionalità per la formattazione e il filtro dei contenuti del file XML per la visualizzazione in Internet Explorer. 

- Leggere i file con: Internet Explorer

**Tipo di file di log: Detailed Error Logging**

- Location: /LogFiles/DetailedErrors/. La cartella /LogFiles/DetailedErrors/ contiene uno o più file HTM che forniscono informazioni dettagliate relative agli eventuali errori HTTP che si sono verificati. 

- Leggere i file con: Web browser

Nei file HTM sono incluse le sezioni seguenti:

- **Informazioni dettagliate sull'errore:** include informazioni sull'errore, ad esempio <em>Modulo</em>, <em>Gestore</em>, <em>Codice di errore</em> e <em>URL richiesto</em>.

- **Cause più probabili:** visualizza un elenco di cause possibili per l'errore.

- **Possibili operazioni:** visualizza un elenco di soluzioni possibili per risolvere il problema segnalato dall'errore.

- **Collegamenti e altre informazioni**: fornisce informazioni di riepilogo aggiuntive sull'errore ed eventualmente i collegamenti ad altre risorse, ad esempio articoli della Microsoft Knowledge Base.

**Tipo di file di log: Registrazione del server Web**

- Location: /LogFiles/http/RawLogs. Le informazioni archiviate nei file sono formattate mediante il [formato di log esteso W3C](http://go.microsoft.com/fwlink/?LinkID=90561). I campi s-computername, s-ip e cs-version non sono usati da Siti Web di Azure.

- Leggere i file con: Log Parser. Usato per l'esecuzione di analisi e di query sui file di log IIS. Log Parser 2.2 è disponibile nell'Area download Microsoft all'indirizzo <a href="http://go.microsoft.com/fwlink/?LinkId=246619">http://go.microsoft.com/fwlink/?LinkId=246619</a>.


##<a name="webendpointstatus"></a>Procedura: Monitorare lo stato degli endpoint

Questa funzionalità, disponibile nella modalità **Standard**, consente di monitorare fino a 2 endpoint da un massimo di 3 posizioni geografiche. 

Il monitoraggio degli endpoint configura i test Web da posizioni distribuite a livello geografico che testano il tempo di risposta e di attività degli URL Web. Il test esegue un'operazione GET HTTP sull'URL Web per determinare il tempo di risposta e di attività da ogni posizione. Ogni posizione configurata esegue un testo ogni cinque minuti.

Il tempo di attività viene monitorato mediante codici di risposta HTTP e il tempo di risposta è misurato in millisecondi. Il tempo di attività si considera al 100% quando il tempo di risposta è inferiore a 30 secondi e il codice di stato HTTP è minore di 400. Il tempo di attività è pari allo 0% quando il tempo di risposta è maggiore di 30 secondi o il codice di stato HTTP è maggiore di 400.

Dopo avere configurato il monitoraggio degli endpoint, è possibile eseguire il drill-down nei singoli endpoint per visualizzare i dettagli relativi a tempo di risposta e stato di attività nell'intervallo di monitoraggio da ognuna delle posizioni di test. È anche possibile configurare una regola di avviso, ad esempio quando l'endpoint impiega troppo tempo a rispondere. 

**Per configurare il monitoraggio degli endpoint:**

1.	Aprire **Siti Web**. Fare clic sul nome del sito Web da configurare.
2.	Fare clic sulla scheda **Configura**. 
3.     Passare alla sezione **Monitoraggio** per immettere le impostazioni dell'endpoint.
4.	Immettere un nome per l'endpoint.
5.	Immettere l'URL per la parte del sito Web da monitorare, ad esempio [http://contoso.azurewebsites.net/archive](http://contoso.azurewebsites.net/archive). 
6.	Selezionare una o più posizioni geografiche nell'elenco.
7.	Facoltativamente, ripetere i passaggi precedenti per creare un secondo endpoint.
8.	Fare clic su **Salva**. Potrebbero essere necessari alcuni istanti prima che i dati di monitoraggio dell'endpoint Web siano disponibili nelle schede **Dashboard** e **Monitor**.

	Per creare una regola di posta elettronica, effettuare le operazioni seguenti:

9.	Nella barra di servizio a sinistra, fare clic su **Servizi di gestione**.
10.	Fare clic su **Aggiungi regola** nella parte inferiore.
11.	In **Tipo di servizio**, selezionare **Sito Web**, quindi selezionare il sito Web per cui è stato configurato in precedenza il monitoraggio dell'endpoint. Fare clic su **Avanti**.
12.	In **Metrica** è ora possibile selezionare ulteriori metriche per l'endpoint configurato. Ad esempio: **Tempo di risposta (homepage/US: IL-Chicago)**. Selezionare la metrica Tempo di risposta e il tipo 3 in **Valore soglia** per specificare una soglia di 3 secondi.
13.	Selezionare **Invia un messaggio di posta elettronica all'amministratore e ai coamministratori del servizio**. Fare clic su **Operazione completata**.

	Azure monitorerà attivamente l'endpoint e invierà un avviso di posta elettronica quando impiega più di tre secondi per rispondere.


Per altre informazioni sul monitoraggio degli endpoint del sito Web, vedere i video seguenti:

- [Scott Guthrie: Introduzione a Siti Web di Azure e configurazione del monitoraggio degli endpoint](http://www.windowsazure.com/it-it/documentation/videos/websites-and-endpoint-monitoring-scottgu/)

- [Mantenere attivi i siti Web di Azure e monitorare gli endpoint, con Stefan Schackow](http://www.windowsazure.com/it-it/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/)





[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169





<!--HONumber=35.1-->

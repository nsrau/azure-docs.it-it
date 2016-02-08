<properties
	pageTitle="Monitorare le app Web nel servizio app di Azure"
	description="Informazioni su come monitorare App Web nel servizio app di Azure usando il portale di gestione."
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/13/2016"
	ms.author="byvinyal"/>

#<a name="howtomonitor"></a>Monitorare le app Web nel servizio app di Azure

[App Web del servizio app](http://go.microsoft.com/fwlink/?LinkId=529714) offre funzionalità di monitoraggio per piani del servizio app Standard e Premium attraverso la pagina di gestione Monitoraggio. La pagina di gestione Monitoraggio offre statistiche sulle prestazioni di un'app Web, come descritto di seguito.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##Criteri di conservazione delle metriche

>[AZURE.NOTE] I criteri di conservazione per le app della metrica variano in base alla granularità.

- Le metriche di granularità **minuto** vengono mantenute per **24 ore**
- Le metriche di granularità **ora** vengono mantenute per **7 giorni**
- Le metriche di granularità **giorno** vengono mantenute per **30 giorni**

##<a name="websitemetrics"></a>Procedura: aggiungere metriche dell'app Web

1. Nel [portale classico](https://manage.windowsazure.com), dalle pagine delle app Web, fare clic sulla scheda **Monitoraggio** per visualizzare la pagina di gestione **Monitoraggio**. Per impostazione predefinita, il grafico nella pagina **Monitor** visualizza le stesse metriche del grafico nella pagina **Dashboard**.

2. Per visualizzare altre metriche per l'app Web, fare clic su **Aggiungi metriche** nella parte inferiore della pagina per visualizzare la finestra di dialogo **Scegli metriche**.

3. Fare clic per selezionare altre metriche da visualizzare nella pagina **Monitoraggio**.

4. Dopo avere selezionato le metriche che si desidera aggiungere alla pagina **Monitoraggio**, fare clic su OK.

5. Dopo avere aggiunto le metriche alla pagina **Monitoraggio**, fare clic per abilitare o disabilitare la casella di opzione accanto a ogni metrica per aggiungere o rimuovere la metrica dal grafico nella parte superiore della pagina.

6. Per rimuovere le metriche dalla pagina **Monitor**, selezionare la metrica da rimuovere e quindi fare clic sull'icona **Delete Metric** nella parte inferiore della pagina.



##<a name="howtoreceivealerts"></a>Procedura: ricevere avvisi dalle metriche dell'app Web

Nella modalità dell'app Web **Standard**, è possibile ricevere avvisi basati sulle metriche di monitoraggio dell'app Web. La funzionalità di avviso richiede che venga innanzitutto configurato un endpoint Web per il monitoraggio nella sezione **Monitoring** della pagina **Configure**. È anche possibile impostare l'invio di un messaggio di posta elettronica quando una metrica scelta raggiunge un valore specificato. Per altre informazioni, vedere [Procedura: Ricevere notifiche di avviso e gestire le relative regole in Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

##<a name="howtoviewusage"></a>Procedura: visualizzare le quote di utilizzo per un'app Web

È possibile configurare le app Web per l'esecuzione in modalità **Condivisa** o **Standard** dalla pagina di gestione **Scalabilità** dell'app Web nel [portale classico](https://manage.windowsazure.com). Ogni sottoscrizione Azure ha accesso a un pool di risorse fornito allo scopo di eseguire un massimo di 100 app Web per area in modalità **Condivisa**. Il pool di risorse disponibile per ogni sottoscrizione dell'app Web per questo scopo è condiviso da altre app Web nella stessa area configurata per l'esecuzione in modalità **Condivisa**. Poiché tali risorse sono condivise con altre app Web, tutte le sottoscrizioni prevedono un limite per l'utilizzo di tali risorse. I limiti applicati all'uso di queste risorse da parte della sottoscrizione sono espressi come quote di uso visualizzate nella sezione relativa alla panoramica sull'uso della pagina di gestione **Dashboard** di ogni app Web.

>[AZURE.NOTE] Quando un'app Web app è configurata per l'esecuzione in modalità **Standard**, le vengono allocate risorse dedicate pari alle dimensioni delle macchine virtuali **Piccola** (predefinita), **Media** o **Grande**, come illustrato nella tabella in Dimensioni delle [macchine virtuali e dei servizi cloud per Azure][vmsizes]. Non sono previsti limiti alle risorse che una sottoscrizione può utilizzare per l'esecuzione di app Web in modalità **Standard**. Tuttavia, il numero di app Web in modalità **Standard** che è possibile creare per ogni area è pari a 500.

### Visualizzare quote di utilizzo per app Web configurate per la modalità Condivisa ###
Per determinare il livello di impatto di un'app Web sulle quote di utilizzo delle risorse, eseguire la procedura seguente:

1. Aprire la pagina di gestione **Dashboard** dell'app Web nel [portale classico](https://manage.windowsazure.com).
2. Nella sezione **panoramica sull'uso** vengono visualizzate le quote di uso del rispettivo piano del [servizio app](http://go.microsoft.com/fwlink/?LinkId=529714), cioè un subset dei seguenti:
	-	**Dati in uscita**, **Tempo CPU** e **Memoria**: quando si supera questa quota, Azure arresta l'app Web per il tempo residuo della quota di intervallo corrente. Azure avvierà l'app Web all'inizio dell'intervallo di quota successivo.
	-	**Archiviazione file system**: quando si raggiunge questa quota, le risorse di archiviazione del file system rimangono accessibili per operazioni in lettura, ma tutte le operazioni in scrittura, incluse quelle necessarie per la normale attività dell'app Web, risultano bloccate. Le operazioni in scrittura riprenderanno quando si riduce l'uso dei file o si sposta l'app Web in un piano del servizio app con una quota maggiore.
	-	**Risorse collegate**: sono visualizzate qui anche le quote relative a qualsiasi risorsa collegata dell'app Web, come database o archiviazione.

	È possibile applicare alcune quote per ogni piano di hosting Web, mentre è possibile applicarne altre per ogni sito. Per informazioni dettagliate sulle quote di uso per ogni piano di hosting Web, vedere i [Limiti relativi ai siti Web](azure-subscription-service-limits.md#websiteslimits).

##<a name="resourceusage"></a>Procedura: evitare di superare le quote

Le quote non sono una questione di prestazioni o costi, ma rappresentano lo strumento per mezzo del quale Azure governa l'uso delle risorse in un ambiente multitenant, prevenendo l'uso eccessivo di risorse condivise da parte dei tenant. Poiché il superamento delle quota implica tempi di inattività o funzionalità ridotta per l'app Web, si consideri quanto segue in modo da garantire il funzionamento del sito quando si stanno per raggiungere le quote:

- Spostare l'app (o le app) Web in un piano del servizio app di livello superiore per sfruttare una quota più ampia. Ad esempio, l'unica quota per i piani **Basic** e **Standard** è Archiviazione file system.
- Con l'aumentare del numero delle istanze di un'app Web, aumenta anche la probabilità di superare le quote di uso delle risorse condivise. Se opportuno, considerare il ridimensionamento di ulteriori istanze di un'app Web quando si superano le quote delle risorse condivise.

##<a name="howtoconfigdiagnostics"></a>Procedura: configurare i log di diagnostica e download per un'app Web

La diagnostica è abilitata nella scheda **Configurazione** dell'app Web nel [portale classico](https://manage.windowsazure.com). Esistono due tipi di dati diagnostici: **diagnostica applicazioni** e **diagnostica del sito**.

#### Diagnostica applicazioni ####

Nella sezione **application diagnostics** della pagina di gestione **Configure** è possibile controllare la registrazione delle informazioni prodotte dall'applicazione, ad esempio la registrazione di eventi che si verificano all'interno di un'applicazione. Quando nell'applicazione si verifica un errore, è ad esempio possibile presentare all'utente un semplice messaggio di errore e scrivere le informazioni dettagliate relative all'errore nel log per un'analisi successiva.

È possibile abilitare o disabilitare le seguenti opzioni di diagnostica applicazioni:

- **Application Logging (File System)**: attiva la registrazione delle informazioni prodotte dall'applicazione. Il campo **Logging Level** determina se vengono registrate le informazioni a livello di errore, avviso o informazioni. È inoltre possibile selezionare l'opzione Verbose per registrare tutte le informazioni generate dall'applicazione.

	I log generati da questa impostazione sono archiviati nel file system dell'app Web e possono essere scaricati mediante la procedura descritta nella sezione **Download dei file di registro di un'app Web** riportata di seguito.

- **Application Logging (Table Storage)**: attiva la registrazione delle informazioni generate dall'applicazione, come quando si seleziona l'opzione Application Logging (File System). In questo caso, le informazioni del log vengono archiviate in una tabella in un account di archiviazione di Azure.

	Per specificare l'account di archiviazione di Azure e la tabella, scegliere **On**, selezionare il **Logging Level** e quindi scegliere **Manage Table Storage**. Specificare l'account di archiviazione e la tabella da utilizzare o creare una nuova tabella.

	È possibile accedere alle informazioni del log archiviate nella tabella tramite un client di archiviazione di Azure.

- **Application Logging (Blob storage)**: attiva la registrazione delle informazioni generate dall'applicazione, come quando si seleziona l'opzione Application Logging (Table Storage). In questo caso, le informazioni del log vengono archiviate in un BLOB in un account di archiviazione di Azure.

	Per specificare l'account di archiviazione di Azure e il BLOB, scegliere **On**, selezionare il **Logging Level** e quindi scegliere **Manage Blob Storage**. Specificare l'account di archiviazione, il contenitore BLOB e il nome BLOB da utilizzare o creare un contenitore e un BLOB nuovi.

Per ulteriori informazioni sugli account di archiviazione di Azure, vedere [Come gestire gli account di archiviazione](/manage/services/storage/how-to-manage-a-storage-account/).

> [AZURE.NOTE] La registrazione delle informazioni delle applicazioni nell'archiviazione BLOB o tabella è supportata solo per le applicazioni .NET.

Poiché la registrazione dell'applicazione nell'archivio richiede l'utilizzo di un client di archiviazione per visualizzare i dati di log, risulta più utile quando si prevede di utilizzare un servizio o un'applicazione che riconosce come leggere ed elaborare i dati direttamente dall'archivio tabelle o BLOB di Azure. La registrazione nel file system genera file che possono essere scaricati nel computer locale mediante FTP o altre utilità descritte più avanti in questa sezione.

**È possibile abilitare contemporaneamente **Application diagnostics (file system)**, **Application diagnostics (table storage)** e **Application diagnostics (blob storage) ed effettuare configurazioni del livello di log individuali. Ad esempio, è possibile registrare gli errori e gli avvisi nell'archivio come soluzione di registrazione a lungo termine e abilitare la registrazione del file system a livello dettagliato dopo avere instrumentato il codice dell'applicazione per la risoluzione di un problema.

È inoltre possibile abilitare la diagnostica da Azure PowerShell mediante il cmdlet **Set-AzureWebsite**. Se Azure PowerShell non è stato installato o configurato per l'utilizzo della sottoscrizione di Azure, vedere [Come utilizzare Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).

> [AZURE.NOTE] La registrazione dell'applicazione si basa sulle informazioni di log generate dall'applicazione. Il metodo utilizzato per generare informazioni di registro, come il formato delle informazioni è specifico per la lingua in che dell'applicazione è scritta. Per informazioni specifiche della lingua sull'utilizzo di registrazione dell'applicazione, vedere gli articoli seguenti:
>
> - **.NET**: [Risolvere i problemi di un'app Web in Azure App Service tramite Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
> - **Node.js**: [Come eseguire il debug di un'applicazione Node.js in Siti Web di Azure](web-sites-nodejs-debug.md)
>
> La registrazione delle informazioni delle applicazioni nell'archiviazione BLOB o tabella è supportata solo per le applicazioni .NET.

#### Diagnostica del sito ####

La sezione **site diagnostics** della pagina di gestione **Configure** consente di controllare la registrazione effettuata dal server Web, ad esempio la registrazione delle richieste Web, la mancata presentazione delle pagine o il tempo impiegato per presentare una pagina. È possibile abilitare o disabilitare le opzioni seguenti:

- **Registrazione del server Web**: attivare la registrazione del server Web per salvare i log dell'app Web usando il formato di file di log esteso W3C. La registrazione del server Web genera un record di tutte le richieste in ingresso all'app Web. Il record contiene informazioni quali indirizzo IP client, URI richiesto, codice di stato HTTP della risposta e stringa agente utente del client. È possibile salvare i log in un account di archiviazione di Azure o nel file system.

 Per salvare i log del server Web in un account di archiviazione di Azure, scegliere **Storage** e quindi **manage storage** per specificare un account di archiviazione e un contenitore BLOB di Azure in cui conservare i log. Per ulteriori informazioni sugli account di archiviazione di Azure, vedere [Come gestire gli account di archiviazione](/manage/services/storage/how-to-manage-a-storage-account/).

   Per salvare i log del server Web nel file system, scegliere **File System**. Verrà abilitata la casella **Quota** in cui è possibile impostare la quantità massima di spazio su disco per i file di log. La dimensione minima è 25 MB, la massima è di 100 MB. La dimensione predefinita è 35 MB.

 Per impostazione predefinita, i log del server Web non vengono mai eliminati. Per specificare un periodo di tempo trascorso il quale i log vengono automaticamente eliminati, selezionare **Set Retention** e immettere il numero di giorni per la conservazione dei log nella casella **Retention Period**. Questa impostazione è disponibile per entrambe le opzioni di salvataggio, nell'account di archiviazione di Azure e nel file system.

- **Detailed Error Messages**: attivare la registrazione dei messaggi di errore dettagliati per registrare ulteriori informazioni sugli errori HTTP (codici di stato maggiori di 400).

- **Failed Request Tracing**: attivare la traccia delle richieste non riuscite per acquisire informazioni sulle richieste client non riuscite, ad esempio un codice di stato HTTP della serie 400. La traccia delle richieste non riuscite genera un documento XML contenente una traccia dei moduli di richiesta passati tramite IIS, i dettagli restituiti da modulo e l'ora in cui è stato richiamato il modulo. Queste informazioni possono essere utilizzate per isolare il componente in cui si è verificato l'errore.


Dopo avere abilitato la diagnostica per un sito Web, fare clic sull'icona **Salva** nella parte inferiore della pagina di gestione **Configurazione** per applicare le opzioni impostate.

> [AZURE.IMPORTANT] Messaggi di errore dettagliati e Traccia delle richieste non riuscite sottopongono un'app Web a condizioni impegnative. È consigliabile disattivare queste funzionalità non appena sono stati riprodotti i problemi da risolvere.

### Configurazione avanzata ###

È possibile modificare ulteriormente la diagnostica aggiungendo coppie chiave-valore nella sezione **app settings** della pagina di gestione **Configure**. Di seguito sono riportate le impostazioni che possono essere configurate da **app settings**:

**DIAGNOSTICS\_TEXTTRACELOGDIRECTORY**

- Posizione in cui verranno salvati i log dell'applicazione, relativi alla radice Web.

- Valore predefinito:.. \\.. \\LogFiles\\Application

**DIAGNOSTICS\_TEXTTRACEMAXBUFFERSIZEBYTES**

- Dimensione massima del buffer da utilizzare durante l'acquisizione dei log dell'applicazione. Le informazioni vengono inizialmente scritte nel buffer prima di essere scaricate nel file o nell'archivio. Se nel buffer vengono scritte nuove informazioni prima che possano essere scaricate, è possibile che le informazioni registrate in precedenza vengano perse. Se l'applicazione genera grandi quantità di informazioni di log, valutare la possibilità di aumentare la dimensione del buffer.

- Valore predefinito: 10MB

**DIAGNOSTICS\_TEXTTRACEMAXLOGFOLDERSIZEBYTES**

- Dimensione massima della cartella **Application** in cui sono archiviati le informazioni diagnostiche dell'applicazione scritte nel file.

- Valore predefinito: 1MB

###Download dei file di log per un'app Web

È possibile scaricare i file di log mediante FTP, Azure PowerShell o l'interfaccia della riga di comando di Azure.

**FTP**

1. Aprire la pagina di gestione **Dashboard** dell'app Web nel [portale classico](https://manage.windowsazure.com) e prendere nota del sito FTP indicato in **Log di diagnostica** e dell'account visualizzato in **Utente della distribuzione**. Il sito FTP è il sito in cui si trovano i file di log e l'account visualizzato in Deployment User è l'account utilizzato per eseguire l'autenticazione al sito FTP.
2. Se non sono ancora state create le credenziali di distribuzione, l'account visualizzato in **Deployment User** indica **Not set**. In questo caso, è necessario creare le credenziali di distribuzione come descritto nella sezione Reset Deployment Credentials del Dashboard in quanto è necessario utilizzare queste credenziali per eseguire l'autenticazione al sito FTP in cui sono archiviati i file di log. In Azure non è supportata l'autenticazione al sito FTP mediante le credenziali Live ID.
3. Valutare l'utilizzo di un client FTP, ad esempio [FileZilla][fzilla] per connettersi al sito FTP. Un client FTP semplifica l'immissione delle credenziali e la visualizzazione delle cartelle in un sito FTP rispetto al browser.
4. Copiare i file di log dal sito FTP al computer locale.

**Azure PowerShell**

1. Dalla **schermata Start** o dal **menu Start** cercare **Azure PowerShell**. Fare clic con il pulsante destro del mouse su **Azure PowerShell** e scegliere **Esegui come amministratore**.

	> [AZURE.NOTE] Se **Azure PowerShell** non è installato, vedere l'[introduzione ai cmdlet di Azure PowerShell](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx) per informazioni sull'installazione e la configurazione.

2. Dal prompt di Azure PowerShell, utilizzare i comandi seguenti per scaricare i file di log:

		Save-AzureWebSiteLog -Name webappname

	Verranno scaricati i file di log per l'app Web specificata da **webappname** e verranno salvati in un file denominato **log.zip** nella directory corrente.

	È inoltre possibile visualizzare un flusso in diretta degli eventi di log utilizzando il comando seguente:

		Get-AzureWebSiteLog -Name webappname -Tail

	Verranno visualizzate le informazioni di log nel prompt di Azure PowerShell man mano che si verificano.

**Interfaccia della riga di comando di Azure**

Aprire un nuovo prompt dei comandi o una sessione di PowerShell, bash o terminal e utilizzare il comando seguente per scaricare i file di log:

	azure site log download webappname

Verranno scaricati i file di log per l'app Web specificata da **webappname** e verranno salvati in un file denominato **log.zip** nella directory corrente.

È inoltre possibile visualizzare un flusso in diretta degli eventi di log utilizzando il comando seguente:

	azure site log tail webappname

Le informazioni di log verranno visualizzate al prompt dei comandi o nella sessione di PowerShell, bash o terminal da cui viene eseguito il comando.

> [AZURE.NOTE] Se il comando **azure**non è installato, vedere [Come usare l'interfaccia della riga di comando di Azure](../virtual-machines-command-line-tools.md) per informazioni sull'installazione e la configurazione.

### Lettura dei file di log ###

I file di log generati dopo avere abilitato la registrazione e/o la traccia per un'app Web variano in base al livello di registrazione/traccia impostato nella pagina di gestione Configurazione dell'app Web. Di seguito sono indicati i percorsi dei file di log e i modi in cui è possibile analizzarli:

**Tipo di File di log: Registrazione dell'applicazione**

- Percorso /LogFiles/Application/. In questa cartella sono presenti uno o più file di testo contenenti le informazioni generate dalla registrazione dell'applicazione. Le informazioni registrate includono data e ora, ID processo (PID) dell'applicazione e il valore generato dall'instrumentazione dell'applicazione.

- Un editor di file o un parser in grado di riconoscere i valori generati dall'applicazione

**Tipo di File di registro: Failed Request Tracing**

- Percorso: / LogFiles/W3SVC # # # /. Questa cartella contiene un file XSL e uno o più file XML. Verificare che il file XSL venga scaricato nella stessa directory dei file XML in quanto il file XSL fornisce le funzionalità per la formattazione e il filtro dei contenuti del file XML per la visualizzazione in Internet Explorer.

- Leggere i file con: Internet Explorer

**Tipo di File di log: Registrazione di messaggi di errore dettagliati**

- /LogFiles/DetailedErrors/. La cartella /LogFiles/DetailedErrors/ contiene uno o più file HTM che forniscono informazioni dettagliate relative agli eventuali errori HTTP che si sono verificati.

- Leggere i file con: browser Web

Nei file HTM sono incluse le sezioni seguenti:

- **Informazioni dettagliate sull'errore:** include informazioni sull'errore, ad esempio <em>Modulo</em>, <em>Gestore</em>, <em>Codice di errore</em> e <em>URL richiesto</em>.

- **Cause più probabili:** visualizza un elenco di cause possibili per l'errore.

- **Possibili operazioni:** visualizza un elenco di soluzioni possibili per risolvere il problema segnalato dall'errore.

- **Collegamenti e ulteriori informazioni**: fornisce informazioni di riepilogo aggiuntive sull'errore ed eventualmente i collegamenti ad altre risorse, ad esempio articoli della Microsoft Knowledge Base.

**Tipo di File di log: Registrazione del Server Web**

- /LogFiles/http/RawLogs. Le informazioni archiviate nei file sono formattate mediante il [formato di log esteso W3C](http://go.microsoft.com/fwlink/?LinkID=90561). I campi s-computername, s-ip e cs-version non sono utilizzati dalle app Web di Azure.

- Leggere i file con: Log Parser. Utilizzato per l'esecuzione di analisi e di query sui file di log IIS. Log Parser 2.2 è disponibile nell'Area download Microsoft all'indirizzo <a href="http://go.microsoft.com/fwlink/?LinkId=246619">http://go.microsoft.com/fwlink/?LinkId=246619</a>.


##<a name="webendpointstatus"></a>Procedura: monitorare lo stato degli endpoint

Questa funzionalità, disponibile nella modalità **Standard**, consente di monitorare fino a 2 endpoint da un massimo di 3 posizioni geografiche.

Il monitoraggio degli endpoint configura i test Web da posizioni distribuite a livello geografico che testano il tempo di risposta e di attività degli URL Web. Il test esegue un'operazione GET HTTP sull'URL Web per determinare il tempo di risposta e di attività da ogni posizione. Ogni posizione configurata esegue un testo ogni cinque minuti.

Il tempo di attività viene monitorato mediante codici di risposta HTTP e il tempo di risposta è misurato in millisecondi. Il tempo di attività si considera al 100% quando il tempo di risposta è inferiore a 30 secondi e il codice di stato HTTP è minore di 400. Il tempo di attività è pari allo 0% quando il tempo di risposta è maggiore di 30 secondi o il codice di stato HTTP è maggiore di 400.

Dopo avere configurato il monitoraggio degli endpoint, è possibile eseguire il drill-down nei singoli endpoint per visualizzare i dettagli relativi a tempo di risposta e stato di attività nell'intervallo di monitoraggio da ognuna delle posizioni di test. È anche possibile configurare una regola di avviso, ad esempio quando l'endpoint impiega troppo tempo a rispondere.

**Per configurare il monitoraggio degli endpoint:**

1.	Aprire **App Web**. Fare clic sul nome dell'app Web per configurarne le impostazioni.
2.	Fare clic sulla scheda **Configure**.
3.     Passare alla sezione **Monitoring** per immettere le impostazioni dell'endpoint.
4.	Immettere un nome per l'endpoint.
5.	Immettere l'URL per la parte dell'app Web da monitorare, ad esempio [http://contoso.azurewebsites.net/archive](http://contoso.azurewebsites.net/archive).
6.	Selezionare una o più posizioni geografiche nell'elenco.
7.	Facoltativamente, ripetere i passaggi precedenti per creare un secondo endpoint.
8.	Fare clic su **Salva**. Potrebbero essere necessari alcuni istanti prima che i dati di monitoraggio dell'endpoint Web siano disponibili nelle schede **Dashboard** e **Monitor**.

Per creare una regola di posta elettronica, effettuare le operazioni seguenti:

9.	Nella barra di servizio a sinistra, fare clic su **Servizi di gestione**.
10.	Fare clic su **Aggiungi regola** nella parte inferiore.
11.	In **Tipo di servizio**, selezionare **App Web**, quindi selezionare l'app Web per cui è stato configurato in precedenza il monitoraggio dell'endpoint. Fare clic su **Avanti**.
12.	In **Metrica** è ora possibile selezionare ulteriori metriche per l'endpoint configurato. Ad esempio: **tempo di risposta (home page / degli Stati Uniti: Chicago IL)**. Selezionare la metrica Tempo di risposta e il tipo 3 in **Valore soglia** per specificare una soglia di 3 secondi.
13.	Selezionare **Invia un messaggio di posta elettronica all'amministratore e ai coamministratori del servizio**. Fare clic su **Operazione completata**.

	Azure monitorerà attivamente l'endpoint e invierà un avviso di posta elettronica quando impiega più di tre secondi per rispondere.

Per ulteriori informazioni sul monitoraggio degli endpoint dell'app Web, vedere i video seguenti:

- [Scott Guthrie: Introduzione a Siti Web di Azure e configurazione del monitoraggio degli endpoint](/documentation/videos/websites-and-endpoint-monitoring-scottgu/)

- [Mantenere attivi i siti Web di Azure e monitorare gli endpoint, con Stefan Schackow](/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/)

>[AZURE.NOTE] Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una Guida per la modifica di siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sui servizi di Azure esistente](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per informazioni su cosa è cambiato nel passaggio dal portale di Azure al portale di anteprima di Azure, vedere [Informazioni di riferimento per l'esplorazione del portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

[fzilla]: http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]: http://go.microsoft.com/fwlink/?LinkID=309169
 

<!---HONumber=AcomDC_0128_2016-->
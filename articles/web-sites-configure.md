<properties 
	pageTitle="Come configurare i siti Web - Gestione servizi di Azure" 
	description="Informazioni su come configurare siti Web in Azure, incluso come configurare un sito Web per l'uso del database SQL o del database MySQL." 
	services="web-sites" 
	documentationCenter="" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/18/2014" 
	ms.author="mwasson"/>


# Come configurare i siti Web #
Nel portale di gestione di Azure è possibile modificare le opzioni di configurazione per i siti Web e collegare questi ultimi ad altre risorse Azure, ad esempio un database.

## Sommario ##
- [Procedura: Modificare le opzioni di configurazione per un sito Web](#howtochangeconfig)
- [Procedura: Configurare un sito Web per l'uso di un database SQL](#howtoconfigSQL)
- [Procedura: Configurare un sito Web per l'uso di un database MySQL](#howtoconfigMySQL)
- [Procedura: Configurare un nome di dominio personalizzato](#howtodomain)
- [Procedura: Configurare un sito Web per l'uso di SSL](#howtoconfigSSL)
- [Passaggi successivi](#next)


##<a name="howtochangeconfig"></a>Procedura: Modificare le opzioni di configurazione per un sito Web

<!-- HOW TO: CHANGE CONFIGURATION OPTIONS FOR A WEBSITE -->

Per impostare le opzioni di configurazione per un sito Web:

1. Nel [portale di gestione](https://manage.windowsazure.com/) aprire le pagine di gestione del sito Web.
1. Fare clic sulla scheda <strong>Configura</strong>.

La scheda **Configura** include le seguenti sezioni:

### Generale

**Versioni del framework**. Impostare le seguenti opzioni se l'app usa uno dei seguenti framework: 

- **.NET Framework**: Impostare la versione del framework .NET. 
- **PHP**: Impostare la versione PHP oppure impostare **DISATTIVATO** per disabilitare PHP. 
- **Java**: Selezionare la versione visualizzata per abilitare Java oppure <strong>DISATTIVATO</strong> per disabilitare Java. 
- Se si abilita Java, usare l'opzione <strong>Contenitore Web</strong> per scegliere tra le versioni Tomcat e Jetty.
- **Python**: Selezionare la versione Python oppure impostare **DISATTIVATO** per disabilitare Python.

Per motivi tecnici l'abilitazione di Java per il sito Web disabilita le opzioni .NET, PHP e Python.

<strong>Modalità pipeline gestita</strong>. Consente di impostare la [modalità pipeline](http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application) IIS. Lasciare questa opzione impostata su Integrato (l'impostazione predefinita) salvo il caso in cui il sito Web richieda una versione precedente di IIS.

<strong>Piattaforma</strong>. Scegliere se eseguire l'applicazione in un ambiente a 32 bit o a 64 bit. L'ambiente a 64 bit richiede la modalità Basic o Standard. Le modalità Gratuito e Condiviso vengono eseguite sempre in un ambiente a 32 bit.

<strong>WebSocket</strong>. Impostare **ATTIVATO** per abilitare il protocollo WebSocket, ad esempio se il sito Web usa [ASP.NET SignalR](http://www.asp.net/signalr) o [socket.io](http://azure.microsoft.com/documentation/articles/web-sites-nodejs-chat-app-socketio/).

<strong>Sempre attivo</strong>. Per impostazione predefinita i siti Web vengono scaricati se restano inattivi per un determinato periodo di tempo. Ciò consente al sistema di conservare le risorse. In modalità Basic o Standard è possibile abilitare <strong>Sempre attivo</strong> affinché il sito venga caricato sempre. Se il sito esegue continuamente processi Web, è necessario abilitare **Sempre attivo**, altrimenti l'esecuzione dei processi Web potrebbe non avvenire in modo affidabile.

<strong>Modifica in Visual Studio Online</strong>. Abilita la modifica del codice in tempo reale con Visual Studio Online. Se abilitata, la scheda Dashboard mostra un collegamento denominato <strong>Modifica in Visual Studio Online</strong> nella sezione <strong>Riepilogo rapido</strong>. Fare clic su questo collegamento per modificare il sito Web direttamente online. Se è necessario eseguire l'autenticazione, è possibile usare le credenziali di distribuzione di base.

Nota: Se si abilita la funzionalità di distribuzione dal controllo del codice sorgente, è possibile sovrascrivere le modifiche apportate nell'editor di Visual Studio Online per una distribuzione. 


### Certificati

In modalità Basic o Standard è possibile caricare certificati SSL per un dominio personalizzato. Per altre informazioni, vedere [Abilitare HTTPS per un sito Web di Azure](http://azure.microsoft.com/documentation/articles/web-sites-configure-ssl-certificate/). 

I certificati caricati sono elencati qui. Dopo aver caricato un certificato, è possibile assegnarlo a qualsiasi sito Web nella sottoscrizione e nell'area geografica specifiche. I certificati con caratteri jolly possono essere usati per qualsiasi sito nel dominio in cui è valido. Un certificato può essere eliminato solo se non presenta associazioni attive in alcun sito.

### Nomi di dominio

Visualizzare o aggiungere nomi di dominio aggiuntivi per il sito Web. Per altre informazioni, vedere [Configurazione di un nome di dominio personalizzato per un sito Web di Azure](http://azure.microsoft.com/documentation/articles/web-sites-custom-domain-name/).

### Associazioni SSL

Se sono stati caricati certificati SSL, è possibile associarli a nomi di dominio personalizzati. Per altre informazioni, vedere [Abilitare HTTPS per un sito Web di Azure](http://azure.microsoft.com/documentation/articles/web-sites-configure-ssl-certificate/).

### Distribuzioni

Questa sezione viene visualizzata solo se è stata abilitata la distribuzione dal controllo del codice sorgente. Usare queste impostazioni per configurare le distribuzioni.

- <strong>URL GIT</strong>. Se è stato creato un repository Git nel sito Web di Azure, si tratta dell'URL in cui eseguire il push del contenuto.
- <strong>URL trigger distribuzione</strong>. Questo URL può essere impostato in un repository GitHub, CodePlex, Bitbucket o in un altro repository per attivare la distribuzione quando viene eseguito il push di un commit nel repository.
- <strong>Ramo da distribuire</strong>. Consente di specificare il ramo che verrà distribuito quando si esegue il push dei contenuti al ramo in questione.

Per configurare la distribuzione dal controllo del codice sorgente, visualizzare la scheda **Dashboard** e fare clic su **Imposta distribuzione dal controllo del codice sorgente**. 

### Diagnostica applicazioni

Opzioni per la scrittura dei log di diagnostica da un'applicazione Web che supporta la registrazione: 

- <strong>File system</strong>. Consente di eseguire la scrittura dei log nel file system del sito Web. La registrazione del file system ha una durata di 12 ore. È possibile accedere ai log dalla condivisione FTP del sito Web (vedere [Credenziali FTP](http://azure.microsoft.com/documentation/articles/web-sites-manage#ftp-credentials)).
- <strong>Archivio tabelle</strong>. Consente di scrivere i log nell'archivio tabelle di Azure. Non è presente alcun limite di tempo e la registrazione rimane abilitata fino alla relativa disabilitazione. 
- <strong>Archivio BLOB</strong>. Consente di scrivere log nell'archivio BLOB di Azure. Non è presente alcun limite di tempo e la registrazione rimane abilitata fino alla relativa disabilitazione.

<strong>Livello di registrazione</strong>. Quando è abilitata la registrazione, questa opzione consente di specificare la quantità di informazioni registrata (Errore, Avviso, Informazioni o Dettagliato).

**Gestisci archivio tabelle**. Quando l'archivio tabelle è abilitato, fare clic su questo pulsante per impostare l'account di archiviazione e il nome della tabella.

**Gestisci archivio BLOB.** Quando l'archivio BLOB è abilitato, fare clic su questo pulsante per impostare l'account di archiviazione e il nome dell'archivio BLOB.

### Diagnostica del sito

Opzioni per la raccolta di informazioni diagnostiche per il sito Web.

<strong>Registrazione del server Web</strong>. Abilita la registrazione del server Web. I log vengono salvati nel formato file di log esteso W3C. È possibile salvare i log in Archiviazione di Azure oppure nel file system.
 
- Se si sceglie <strong>File system</strong>, i log vengono salvati nel sito FTP indicato in "Registri di diagnostica FTP" nella pagina Dashboard (vedere [Credenziali FTP](http://azure.microsoft.com/documentation/articles/web-sites-manage#ftp-credentials)). 
- Se si sceglie **File system**, usare la casella <strong>Quota</strong> per impostare la quantità massima di spazio su disco per i file di log. La dimensione minima è 25 MB, mentre la massima è 100 MB. Il valore predefinito è 35 MB. Quando viene raggiunta la quota, i file più obsoleti vengono successivamente sovrascritti da quelli più recenti. Se si ritiene necessario conservare una cronologia di dimensione maggiore di 100 MB, usare Archiviazione di Azure, che offre una capacità di archiviazione decisamente superiore.
- Facoltativamente, fare clic su <strong>Imposta mantenimento</strong> per eliminare automaticamente i file dopo un periodo di tempo. Per impostazione predefinita, i log non vengono mai eliminati.   

<strong>Messaggi di errore dettagliati</strong>. Se abilitato, i messaggi di errore dettagliati vengono salvati come file HTM. Per visualizzare i file, passare al sito FTP indicato in "Registri di diagnostica FTP" nella pagina Dashboard. I file vengono salvati in /LogFiles/DetailedErrors nel sito FTP (vedere [Credenziali FTP](http://azure.microsoft.com/documentation/articles/web-sites-manage#ftp-credentials)).

<strong>Traccia delle richieste non riuscita</strong>. Se abilitato, le richieste non riuscite vengono registrate in file XML. Per visualizzare i file, passare al sito FTP indicato in "Registri di diagnostica FTP" nella pagina Dashboard. (vedere [Credenziali FTP](http://azure.microsoft.com/documentation/articles/web-sites-manage#ftp-credentials)). I file vengono salvati in /LogFiles/W3SVC*xxx*, dove xxx è un identificatore univoco. Questa cartella contiene un file XSL e uno o più file XML. Assicurarsi di scaricare il file XSL, perché fornisce la funzionalità di formattazione e filtro dei contenuti dei file XML.

<strong>Debug remoto</strong>. Abilita il debug remoto. Quando viene abilitato, è possibile usare il debugger remoto in Visual Studio per connettersi direttamente al sito Web di Azure. Il debug remoto resterà abilitato per 48 ore.

**Nota**: il debug remoto non funzionerà con un nome sito o un nome utente con più di 20 caratteri. 

### Monitoraggio

Nella modalità Basic o Standard, è possibile testare la disponibilità degli endpoint HTTP o HTTPS, da un numero massimo di tre posizioni geograficamente distribuite. Un test di monitoraggio ha esito negativo se il codice della risposta HTTP è un errore (4xx o 5xx) o se la risposta richiede più di 30 secondi. Un endpoint è considerato disponibile se il test di monitoraggio ha esito positivo da tutte le posizioni specificate. 

Per altre informazioni, vedere [Procedura: Monitorare lo stato degli endpoint](http://go.microsoft.com/fwLink/?LinkID=279906&clcid=0x409).


### Developer Analytics

Scegliere <strong>Componente aggiuntivo</strong> per selezionare un componente aggiuntivo di analisi da un elenco oppure passare ad Azure Store per selezionarne uno. Scegliere <strong>Personalizzato</strong> per selezionare un provider di analisi, ad esempio New Relic, da un elenco. Se si usa un provider personalizzato, è necessario immettere il codice di licenza nella casella <strong> Chiave provider</strong>. 

Per altre informazioni sull'uso dell'applicazione New Relic con Siti Web di Azure, vedere <a href="http://azure.microsoft.com/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/">Gestione delle prestazioni delle applicazioni con New Relic in Siti Web di Azure</a>.

### Impostazioni app

Coppie nome-valore che verranno caricate dall'applicazione Web all'avvio. 

- Per i siti .NET queste impostazioni verranno inserite nella configurazione .NET in fase di esecuzione, sostituendo le impostazioni esistenti. 

- Le applicazioni PHP, Python, Java e Node possono accedere a queste impostazioni come variabili di ambiente durante il runtime. Per ciascuna impostazione dell'app vengono create due variabili di ambiente, una con il nome specificato dalla voce dell'impostazione dell'app e l'altra con il prefisso APPSETTING_. Entrambe contengono lo stesso valore.

### Stringhe di connessione

Stringhe di connessione per le risorse collegate. 

Per i siti .NET tali stringhe vengono inserite nelle impostazioni della configurazione .NET durante il runtime, sostituendo le voci esistenti in cui la chiave è uguale al nome del database collegato. 

Per le applicazioni PHP, Python, Java e Node queste impostazioni saranno disponibili come variabili di ambiente durante il runtime, con il tipo di connessione come prefisso. I prefissi delle variabili di ambiente sono i seguenti: 

- SQL Server: SQLCONNSTR_
- MySQL: MYSQLCONNSTR_
- Database SQL: SQLAZURECONNSTR_
- Personalizzato: CUSTOMCONNSTR_

Ad esempio, se una stringa di connessione MySql fosse denominata connectionstring1, l'accesso avverrebbe attraverso la variabile di ambiente <code>MYSQLCONNSTR_connectionString1</code>.

<strong>Nota</strong>: le stringhe di connessione vengono create anche quando si collega una risorsa di database a un sito Web. Le stringhe di connessione create in questo modo vengono lette solo se visualizzate nella 
pagina di gestione della configurazione.

### Documenti predefiniti

Il documento predefinito di un sito Web è la pagina Web che viene visualizzata; non specifica una pagina particolare del sito Web. Se nell'elenco del sito Web contiene più file nell'elenco, accertarsi che il documento predefinito del sito stesso si trovi all'inizio dell'elenco.

Le applicazioni Web potrebbero usare moduli che vengono instradati in base all'URL invece di visualizzare contenuto statico, nel qual caso non è presente alcun documento predefinito.   

### Mapping dei gestori

Usare quest'area per aggiungere processori script personalizzati allo scopo di gestire le richieste di estensioni di file specifiche. 

- **Estensione**. L'estensione file da gestire, ad esempio *.php o handler.fcgi. 
- **Percorso processore script**. Il percorso assoluto del processore script. Le richieste di file che corrispondono a questo modello saranno elaborate dal processore script. Usare il percorso <code>D:\home\site\wwwroot</code> per fare riferimento alla directory radice del sito.
- **Argomenti aggiuntivi**. Argomenti facoltativi della riga dei comando per il processore script 


### Applicazioni e directory virtuali 

Per configurare applicazioni e directory virtuali associate al sito Web, specificare ogni directory virtuale e il corrispondente percorso fisico relativo alla radice del sito. È facoltativamente possibile selezionare la casella di controllo <strong>Applicazione</strong> in modo da contrassegnare una directory virtuale come applicazione nella configurazione del sito.

	

<!-- HOW TO: CONFIGURE A WEBSITE TO USE A SQL DATABASE -->
##<a name="howtoconfigSQL"></a>Procedura: Configurare un sito Web per l'uso di un database SQL

Per collegare un sito Web a un database SQL, seguire questa procedura:

1. Nel [portale di gestione](http://manage.windowsazure.com) selezionare **Siti Web** per visualizzare l'elenco di siti Web creati dall'account attualmente connesso.

2. Selezionare un sito Web dal relativo elenco per aprire le pagine **Gestione** del sito Web.

3. Fare clic sulla scheda **Risorse collegate**. Nella pagina **Risorse collegate** verrà visualizzato il messaggio **Nessuna risorsa collegata**.

4. Fare clic su **Collega una risorsa** per visualizzare la procedura guidata **Collega una risorsa**.

5. Fare clic su **Crea una nuova risorsa** per visualizzare un elenco dei tipi di risorse che è possibile collegare al sito Web.

6. Fare clic su **Database SQL** per visualizzare la procedura guidata **Collega database**.

7. Completare i campi richiesti nelle pagine 3 e 4 della procedura guidata **Collega database**, quindi fare clic sul segno di spunta **Fine** nella pagina 4.

Verrà creato un database SQL con i parametri specificati e tale database verrà quindi collegato al sito Web.

<!-- HOW TO: CONFIGURE A WEBSITE TO USE A MYSQL DATABASE -->
##<a name="howtoconfigMySQL"></a>Procedura: Configurare un sito Web per l'uso di un database MySQL##
Per configurare un sito Web per l'uso di un database MySQL, attenersi alla stessa procedura prevista per l'uso di un database SQL, ma nella procedura guidata **Collega una risorsa** scegliere **Database MySQL** anziché **Database SQL**. 

In alternativa, è possibile creare il sito Web con l'opzione **Creazione personalizzata**. Dal menu a discesa **Database** scegliere **Crea un nuovo database MySQL** oppure **Utilizzare un database MySQL esistente**. 

##<a name="howtodomain"></a>Procedura: Configurare un nome di dominio personalizzato

Per informazioni sulla configurazione del sito Web per l'uso di un nome di dominio personalizzato, vedere [Configurazione di un nome di dominio personalizzato per un sito Web di Azure](http://azure.microsoft.com/documentation/articles/web-sites-custom-domain-name/).

##<a name="howtoconfigSSL"></a>Procedura: Configurare un sito Web per l'uso di SSL##

Per informazioni sulla configurazione di SSL per un dominio personalizzato in Azure, vedere [Abilitazione di HTTPS per un sito Web di Azure](http://azure.microsoft.com/documentation/articles/web-sites-configure-ssl-certificate/). 

##<a name="next"></a>Passaggi successivi

* [Come scalare siti Web](http://azure.microsoft.com/documentation/articles/web-sites-scale/)

* [Come monitorare i siti Web](http://azure.microsoft.com/documentation/articles/web-sites-monitor/)



<!--HONumber=42-->

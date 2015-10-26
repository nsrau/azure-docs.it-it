<properties 
	pageTitle="Amministrazione di un servizio mobile dalla riga di comando | Microsoft Azure" 
	description="Informazioni su come creare, distribuire e gestire il servizio mobile di Azure usando gli strumenti da riga di comando." 
	services="mobile-services" 
	documentationCenter="Mobile" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="07/22/2015" 
	ms.author="glenga"/>

# Eseguire l'automazione dei servizi mobili con gli strumenti da riga di comando 

##Panoramica

Questo argomento illustra come usare gli strumenti da riga di comando di Azure per automatizzare la creazione e la gestione di Servizi mobili di Azure e descrive come installare e iniziare a usare gli strumenti da riga di comando per eseguire le attività principali di Servizi mobili.
 
Se combinati in un unico script o file batch, questi singoli comandi consentono di automatizzare i processi di creazione, verifica ed eliminazione di un servizio mobile.

Questo argomento illustra alcune attività di amministrazione comuni supportate dagli strumenti da riga di comando di Azure. Per altre informazioni, vedere la [documentazione degli strumenti da riga di comando di Azure][reference-docs].

##Installare gli strumenti da riga di comando di Azure

L'elenco seguente fornisce informazioni per l'installazione degli strumenti da riga di comando in base al sistema operativo in uso.

* **Windows**: scaricare il [programma di installazione degli strumenti da riga di comando di Azure][windows-installer]. Aprire il file con estensione msi scaricato e completare i passaggi dell'installazione seguendo le istruzioni visualizzate.

* **Mac**: scaricare il [programma di installazione di Azure SDK][mac-installer]. Aprire il file con estensione pkg scaricato e completare i passaggi dell'installazione seguendo le istruzioni visualizzate.

* **Linux**: installare la versione più recente di [Node.js][nodejs-org] (vedere l'articolo relativo all'[installazione di Node.js tramite Gestione pacchetti][install-node-linux]), quindi eseguire il comando seguente:

	npm install azure-cli -g

Per verificare l'installazione, digitare `azure` al prompt dei comandi. Se l'installazione è stata completata correttamente, viene visualizzato un elenco di tutti i comandi di `azure` disponibili.

##Come scaricare e importare impostazioni di pubblicazione

Per iniziare, è necessario innanzitutto scaricare e importare le impostazioni di pubblicazione. Sarà quindi possibile usare gli strumenti per creare e gestire servizi di Azure. Per scaricare le impostazioni di pubblicazione, usare il comando `account download`:

	azure account download

Verrà aperto il browser predefinito e richiesto di accedere al portale di gestione. Dopo l'accesso, verrà scaricato il file `.publishsettings`. Prendere nota del percorso del file salvato.

Importare quindi il file `.publishsettings` eseguendo il comando seguente e sostituendo `<path-to-settings-file>` con il percorso del proprio file `.publishsettings`:

	azure account import <path-to-settings-file>

È possibile rimuovere tutte le informazioni archiviate dal comando <code>import</code> usando il comando <code>account clear</code>:

	azure account clear

Per visualizzare un elenco di opzioni per i comandi `account`, usare l'opzione `-help`:

	azure account -help

Dopo l'importazione delle impostazioni di pubblicazione, è opportuno eliminare il file `.publishsettings` per motivi di sicurezza. Per altre informazioni, vedere [Come installare gli strumenti da riga di comando di Azure per Mac e Linux]. A questo punto è possibile iniziare a creare e gestire Servizi mobili di Azure dalla riga di comando o in file batch.

##Come creare un servizio mobile

È possibile usare gli strumenti da riga di comando per creare una nuova istanza del servizio mobile. Durante la creazione del servizio mobile, verrà inoltre creata un'istanza di database SQL in un nuovo server.

Usare il comando seguente per creare una nuova istanza del servizio mobile nella sottoscrizione, dove `<service-name>` è il nome del nuovo servizio mobile, `<server-admin>` è il nome dell'account di accesso del nuovo server e `<server-password>` è la password per il nuovo account di accesso:

	azure mobile create <service-name> <server-admin> <server-password>

Il comando `mobile create` ha esito negativo quando il servizio mobile specificato esiste già. Negli script di automazione provare a eliminare un servizio mobile prima di tentare di ricrearlo.

##Come visualizzare l'elenco dei servizi mobili esistenti in una sottoscrizione

> [AZURE.NOTE]È possibile usare i comandi nell'interfaccia da riga di comando correlati a "list" e "script" solo con il back-end JavaScript.

Il comando seguente restituisce un elenco di tutti i servizi mobili in una sottoscrizione di Azure:

	azure mobile list

Questo comando mostra inoltre lo stato corrente e l'URL di ogni servizio mobile.

##Come eliminare un servizio mobile esistente

È possibile usare gli strumenti da riga di comando per eliminare un servizio mobile esistente unitamente al database SQL e al server ad esso associati. Usare il comando seguente per eliminare il servizio mobile, dove `<service-name>` è il nome del servizio mobile da eliminare:

	azure mobile delete <service-name> -a -q

Se si includono i parametri `-a` e `-q`, questo comando consente di eliminare anche il database SQL e il server usati dal servizio mobile senza visualizzare una richiesta di conferma.

> [AZURE.NOTE]Se non viene specificato il parametro <code>-q</code> insieme al parametro <code>-a</code> o <code>-d</code>, l'esecuzione viene sospesa e viene richiesto di selezionare le opzioni di eliminazione per il database SQL. Usare il parametro <code>-a</code> solo quando nessun altro servizio usa il database o il server, altrimenti usare il parametro <code>-d</code> per eliminare solo i dati appartenenti al servizio mobile che si intende eliminare.

##Come creare una tabella nel servizio mobile

Usare il comando seguente per creare una tabella nel servizio mobile specificato, dove `<service-name>` è il nome del servizio mobile e `<table-name>` è il nome della tabella da creare:

	azure mobile table create <service-name> <table-name>

Viene creata una nuova tabella con le autorizzazioni predefinite, `application`, per le operazioni su tabella: `insert`, `read`, `update` e `delete`.

Usare il comando seguente per creare una nuova tabella con autorizzazione `read` pubblica e con autorizzazione `delete` concessa solo agli amministratori:

	azure mobile table create <service-name> <table-name> -p read=public,delete=admin

La tabella seguente illustra il valore dell'autorizzazione dello script rispetto al valore dell'autorizzazione nel [portale di gestione di Azure].

|Valore script|Valore portale di gestione| |========|========| |`public`|Tutti| |`application`(impostazione predefinita)|Chiunque con la chiave applicazione| |`user`|Solo gli utenti autenticati| |`admin`|Solo script e amministratori|

Il comando `mobile table create` ha esito negativo quando la tabella specificata esiste già. Negli script di automazione, provare a eliminare una tabella prima di tentare di ricrearla.

##Come visualizzare l'elenco delle tabelle esistenti in un servizio mobile

Usare il comando seguente per restituire un elenco di tutte le tabelle in un servizio mobile, dove `<service-name>` è il nome del servizio mobile:

	azure mobile table list <service-name>

Questo comando mostra inoltre il numero di indici in ogni tabella e il numero di righe di dati attualmente nella tabella.

##Come eliminare una tabella esistente dal servizio mobile

Usare il comando seguente per eliminare una tabella dal servizio mobile, dove `<service-name>` è il nome del servizio mobile e `<table-name>` è il nome della tabella da eliminare:

	azure mobile table delete <service-name> <table-name> -q

Negli script di automazione usare il parametro `-q` per eliminare la tabella senza visualizzare una richiesta di conferma che ne blocca l'esecuzione.

##Come registrare uno script in un'operazione su tabella

Usare il comando seguente per caricare e registrare una funzione in un'operazione su tabella, dove `<service-name>` è il nome del servizio mobile, `<table-name>` è il nome della tabella e `<operation>` è l'operazione su tabella, che può essere di tipo `read`, `insert`, `update` o `delete`:

	azure mobile script upload <service-name> table/<table-name>.<operation>.js

Si noti che questa operazione comporta il caricamento di un file JavaScript (con estensione js) dal computer locale. Il nome del file deve essere composto dai nomi della tabella e dell'operazione e deve trovarsi nella sottocartella `table` relativa al percorso in cui viene eseguito il comando. Ad esempio, l'operazione seguente carica e registra un nuovo script `insert` che appartiene alla tabella `TodoItems`:

	azure mobile script upload todolist table/todoitems.insert.js

La dichiarazione di funzione nel file di script deve corrispondere all'operazione su tabella registrata. Questo significa che, per uno script `insert`, lo script caricato contiene una funzione con la firma seguente:

	function insert(item, user, request) {
	    ...
	} 

Per altre informazioni sulla registrazione di script, vedere la pagina relativa alle [informazioni di riferimento sugli script del server di Servizi mobili].

<!-- Anchors. -->
[Download and install the command-line tools]: #install
[Download and import publish settings]: #import
[Create a new mobile service]: #create-service
[Get the master key]: #get-master-key
[Create a new table]: #create-table
[Register a new table script]: #register-script
[Delete an existing table]: #delete-table
[Delete an existing mobile service]: #delete-service
[Test the mobile service]: #test-service
[List mobile services]: #list-services
[List tables]: #list-tables
[Next steps]: #next-steps

<!-- Images. -->











<!-- URLs. -->
[informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/p?LinkId=262293

[portale di gestione di Azure]: https://manage.windowsazure.com/
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager

[mac-installer]: http://go.microsoft.com/fwlink/p?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/p?LinkID=275464
[reference-docs]: http://azure.microsoft.com/documentation/articles/virtual-machines-command-line-tools/#Commands_to_manage_mobile_services
[Come installare gli strumenti da riga di comando di Azure per Mac e Linux]: http://go.microsoft.com/fwlink/p/?LinkId=275795

 

<!---HONumber=Oct15_HO3-->
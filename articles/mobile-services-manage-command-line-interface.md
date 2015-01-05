<properties urlDisplayName="Command Line Administration" pageTitle="Amministrazione di un servizio mobile dalla riga di comando - Esercitazione su Azure" metaKeywords="" description="Learn how to create, deploy, and manage your Azure Mobile Service using command-line tools." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Automate mobile services with command-line tools" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Automatizzare i servizi mobili con gli strumenti da riga di comando 

Questo argomento descrive come usare gli strumenti da riga di comando di Azure per automatizzare la creazione e la gestione di Servizi mobili di Azure e illustra come installare e iniziare a usare gli strumenti da riga di comando per eseguire le attività di Servizi mobili seguenti:

-	[Creare un nuovo servizio mobile] 
-	[Creare una nuova tabella]
-   [Registrare uno script in un'operazione su tabella][Register a new table script]
-   [Visualizzare un elenco di tabelle]
- 	[Eliminare una tabella esistente]
-	[Visualizzare un elenco di servizi mobili]
-   [Eliminare un servizio mobile esistente]
 
Se combinati in un unico script o file batch, questi singoli comandi consentono di automatizzare i processi di creazione, verifica ed eliminazione di un servizio mobile. 

Per usare gli strumenti da riga di comando di Azure per gestire Servizi mobili, è necessario disporre di un account Azure in cui sia abilitata la funzionalità Servizi mobili di Azure.

+ Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/" target="_blank">versione di valutazione gratuita di Azure</a>.

+ Se si dispone di un account, ma è necessario abilitare l'anteprima di Servizi mobili di Azure, vedere la pagina relativa all'<a href="http://azure.microsoft.com/it-it/documentation/articles/php-create-account/#enable" target="_blank">abilitazione dell'anteprima delle funzionalità di Azure</a>.

In questo argomento vengono illustrate alcune attività di amministrazione comuni supportate dagli strumenti da riga di comando di Azure. Per altre informazioni, vedere la [documentazione degli strumenti da riga di comando di Azure][reference-docs].

<!--+  You must download and install the Azure command-line tools to your local machine. To do this, follow the instructions in the first section of this topic. 

+ (Optional) To be able to execute HTTP requests directly from the command-line, you must use cURL or an equivalent tool. cURL runs on a variety of platforms. Locate and install cURL for your specific platform from the <a href=http://go.microsoft.com/fwlink/p/?LinkId=275676 target="_blank">cURL download  page</a>.-->

<h2><a name="install"></a>Installare gli strumenti da riga di comando di Azure</h2>

Nell'elenco seguente vengono fornite informazioni per l'installazione degli strumenti da riga di comando in base al sistema operativo in uso.

* **Windows**: scaricare il [programma di installazione degli strumenti da riga di comando di Azure][windows-installer]. Aprire il file .msi scaricato e completare i passaggi dell'installazione seguendo le istruzioni visualizzate.

* **Mac**: scaricare il [programma di installazione di Azure SDK][mac-installer]. Aprire il file .pkg scaricato e completare i passaggi dell'installazione seguendo le istruzioni visualizzate.

* **Linux**: Installare la versione più recente di [Node.js][nodejs-org] (vedere l'articolo sull'[installazione di Node.js mediante Gestione pacchetti][install-node-linux]), quindi eseguire il comando seguente:

		npm install azure-cli -g

Per controllare l'installazione, digitare `azure` al prompt dei comandi. Se l'installazione è stata completata correttamente, verrà visualizzato un elenco di tutti i comandi di `azure` disponibili.
<h2><a name="import-account"></a>Come scaricare e importare impostazioni di pubblicazione</h2>

Per iniziare, è necessario innanzitutto scaricare e importare le impostazioni di pubblicazione. Sarà quindi possibile usare gli strumenti per creare e gestire servizi di Azure. Per scaricare le impostazioni di pubblicazione, usare il comando account download`:

		azure account download

Verrà aperto il browser predefinito e richiesto di accedere al portale di gestione. Dopo l'accesso, verrà scaricato il file `.publishsettings`. Prendere nota del percorso del file salvato.

Importare quindi il file `.publishsettings` eseguendo il comando seguente, sostituendo prima `<path-to-settings-file>` con il percorso del proprio file `.publishsettings`:

		azure account import <path-to-settings-file>

È possibile rimuovere tutte le informazioni archiviate dal comando <code>import</code> usando il comando <code>account clear</code>:

		azure account clear

Per visualizzare un elenco di opzioni per i comandi account`, usare l'opzione `-help`:

		azure account -help

Dopo l'importazione delle impostazioni di pubblicazione, è opportuno eliminare il file `.publishsettings` per motivi di sicurezza. Per altre informazioni, vedere [Come installare gli strumenti da riga di comando di Azure per Mac e Linux]. È ora possibile iniziare a creare e gestire Servizi mobili di Azure dalla riga di comando o in file batch.  

<h2><a name="create-service"></a>Come creare un servizio mobile</h2>

È possibile usare gli strumenti da riga di comando per creare una nuova istanza del servizio mobile. Durante la creazione del servizio mobile, verrà inoltre creata un'istanza di database SQL in un nuovo server. 

Usare il comando seguente per creare una nuova istanza del servizio mobile nella sottoscrizione, dove `<service-name>` è il nome del nuovo servizio mobile, `<server-admin>` è il nome dell'account di accesso del nuovo server e `<server-password>` è la password per il nuovo account di accesso:

		azure mobile create <service-name> <server-admin> <server-password>

Il comando `mobile create` ha esito negativo quando il servizio mobile specificato esiste già. Negli script di automazione, provare a eliminare un servizio mobile prima di tentare di ricrearlo.

<h2><a name="list-services"></a>Come visualizzare l'elenco dei servizi mobili esistenti in una sottoscrizione</h2>

Il comando seguente restituisce un elenco di tutti i servizi mobili in una sottoscrizione di Azure:

		azure mobile list

Questo comando mostra inoltre lo stato corrente e l'URL di ogni servizio mobile.

<h2><a name="delete-service"></a>Come eliminare un servizio mobile esistente</h2>

È possibile usare gli strumenti da riga di comando per eliminare un servizio mobile esistente unitamente al database SQL e al server ad esso associati. Usare il comando seguente per eliminare il servizio mobile, dove `<service-name>` è il nome del servizio mobile da eliminare:

		azure mobile delete <service-name> -a -q

Se si includono i parametri `-a` e `-q`, questo comando consente di eliminare anche il database SQL e il server usati dal servizio mobile senza visualizzare una richiesta di conferma.

<div class="dev-callout"><strong>Nota</strong> 
   <p>Se non viene specificato il parametro <code>-q</code> insieme al parametro <code>-a</code> o <code>-d</code>, l'esecuzione viene sospesa e viene richiesto di selezionare le opzioni di eliminazione per il database SQL. Uaare il parametro <code>-a</code> solo quando nessun altro servizio usa il database o il server, altrimenti usare il parametro <code>-d</code> per eliminare solo i dati appartenenti al servizio mobile che si intende eliminare.</p>
</div>

<h2><a name="create-table"></a>Come creare una tabella nel servizio mobile</h2>

Usare il comando seguente per creare una tabella nel servizio mobile specificato, dove `<service-name>` è il nome del servizio mobile e `<table-name>` è il nome della tabella da creare:

		azure mobile table create <service-name> <table-name>

Verrà creata una nuova tabella con le autorizzazioni predefinite, `application`, per le operazioni su tabella: `insert`, `read`, `update` e `delete`. 

Usare il comando seguente per creare una nuova tabella con autorizzazione `read` pubblica e con autorizzazione `delete` concessa solo agli amministratori:

		azure mobile table create <service-name> <table-name> -p read=public,delete=admin

La tabella seguente illustra il valore dell'autorizzazione dello script rispetto al valore dell'autorizzazione nel [portale di gestione di Azure].

<table border="1" width="100%"><tr><th>Valore script</th><th>Valore portale di gestione</th></tr>
<tr><td><code>public</code></td><td>Tutti</td></tr>
<tr><td><code>application</code> (predefinito)</td><td>Chiunque con la chiave applicazione</td></tr>
<tr><td><code>user</code></td><td>Solo gli utenti autenticati</td></tr>
<tr><td><code>admin	</code></td><td>Solo script e amministratori:</td></tr></table>

Il comando `mobile table create` ha esito negativo quando la tabella specificata esiste già. Negli script di automazione, provare a eliminare una tabella prima di tentare di ricrearla.

<h2><a name="list-tables"></a>Come visualizzare l'elenco delle tabelle esistenti in un servizio mobile</h2>

Usare il comando seguente per restituire un elenco di tutte le tabelle in un servizio mobile, dove `<service-name>` è il nome del servizio mobile:

		azure mobile table list <service-name>

Questo comando mostra inoltre il numero di indici in ogni tabella e il numero di righe di dati attualmente nella tabella.

<h2><a name="delete-table"></a>Come eliminare una tabella esistente dal servizio mobile</h2>

Usare il comando seguente per eliminare una tabella dal servizio mobile, dove `<service-name>` è il nome del servizio mobile e `<table-name>` è il nome della tabella da eliminare:

		azure mobile table delete <service-name> <table-name> -q

Negli script di automazione usare il parametro `-q` per eliminare la tabella senza visualizzare una richiesta di conferma che ne blocca l'esecuzione.

<h2><a name="register-script"></a>Come registrare uno script in un'operazione su tabella</h2>

Usare il comando seguente per caricare e registrare una funzione in un'operazione su tabella, dove `<service-name>` è il nome del servizio mobile, `<table-name>` è il nome della tabella e `<operation>` è l'operazione su tabella, che può essere di tipo `read`, `insert`, `update` o `delete`:

		azure mobile script upload <service-name> table/<table-name>.<operation>.js

Si noti che questa operazione comporta il caricamento di un file JavaScript (con estensione js) dal computer locale. Il nome del file deve essere composto dai nomi della tabella e dell'operazione e deve trovarsi nella sottocartella `table` relativa al percorso in cui viene eseguito il comando. Ad esempio, l'operazione seguente consente di caricare e registrare un nuovo script `insert` che appartiene alla tabella `TodoItems`:

		azure mobile script upload todolist table/todoitems.insert.js

La dichiarazione di funzione nel file di script deve corrispondere all'operazione su tabella registrata. Questo significa che per uno script `insert` lo script caricato contiene una funzione con la firma seguente:

		function insert(item, user, request) {
		    ...
		} 

Per altre informazioni sulla registrazione di script, vedere [Riferimento agli script server dei Servizi mobili].

<!--<h2><a name="test-service"></a>Test the new mobile service</h2>

When you are automating the creation of your mobile service, you can optionally use cURL or another command-line request generator to 

## <a name="nextsteps"> </a>Next Steps
Next steps here....
-->
<!-- Anchors. -->
[Scaricare e installare gli strumenti da riga di comando]: #install
[Scaricare e importare impostazioni di pubblicazione]: #import
[Creare un nuovo servizio mobile]: #create-service
[Ottenere la chiave master]: #get-master-key
[Creare una nuova tabella]: #create-table
[Registrare un nuovo script di tabella]: #register-script
[Eliminare una tabella esistente]: #delete-table
[Eliminare un servizio mobile esistente]: #delete-service
[Testare il servizio mobile]: #test-service
[Visualizzare un elenco di servizi mobili]: #list-services
[Visualizzare un elenco di tabelle]: #list-tables
[Passaggi successivi]: #next-steps

<!-- Images. -->











<!-- URLs. -->
[Informazioni di riferimento sugli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/p?LinkId=262293

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager

[mac-installer]: http://go.microsoft.com/fwlink/p?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/p?LinkID=275464
[reference-docs]: /it-it/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services
[Come installare gli strumenti da riga di comando di Azure per Mac e Linux]: http://go.microsoft.com/fwlink/p/?LinkId=275795


<!--HONumber=35.1-->

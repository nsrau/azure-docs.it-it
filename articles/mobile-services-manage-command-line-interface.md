<properties linkid="develop-mobile-tutorials-command-line-administration" urlDisplayName="Command Line Administration" pageTitle="Administering a Mobile Service at the command line - Azure tutorial" metaKeywords="" description="Learn how to create, deploy, and manage your Azure Mobile Service using command-line tools." metaCanonical="" services="" documentationCenter="Mobile" title="Automate mobile services with command-line tools" authors="glenga" solutions="" manager="" editor="" />

Automazione dei servizi mobili con gli strumenti da riga di comando
===================================================================

In questo argomento viene illustrato come utilizzare gli strumenti da riga di comando di Azure per automatizzare la creazione e la gestione di Servizi mobili di Azure e viene descritto come installare e iniziare a utilizzare gli strumenti da riga di comando per eseguire le attività di Servizi mobili seguenti:

-   [Creazione di un nuovo servizio mobile](#create-service)
-   [Creazione di una nuova tabella](#create-table)
-   [Registrazione di uno script in un'operazione su tabella](#register-script)
-   [Visualizzazione di un elenco di tabelle](#list-tables)
-   [Eliminazione di una tabella esistente](#delete-table)
-   [Visualizzazione di un elenco di servizi mobili](#list-services)
-   [Eliminazione di un servizio mobile esistente](#delete-service)

Se combinati in un unico script o file batch, questi singoli comandi consentono di automatizzare i processi di creazione, verifica ed eliminazione di un servizio mobile.

Per utilizzare gli strumenti da riga di comando di Azure per gestire Servizi mobili, è necessario disporre di un account Azure in cui sia abilitata la funzionalità Servizi mobili di Azure.

-   Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://www.windowsazure.com/en-us/pricing/free-trial/).

-   Se si dispone di un account, ma è necessario abilitare l'anteprima di Servizi mobili di Azure, vedere la pagina relativa all'[abilitazione dell'anteprima delle funzionalità di Azure](../create-a-windows-azure-account/#enable).

In questo argomento vengono illustrate alcune attività di amministrazione comuni supportate dagli strumenti da riga di comando di Azure. Per ulteriori informazioni, vedere la [documentazione degli strumenti da riga di comando di Azure](/en-us/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services).

Installazione degli strumentiInstallazione degli strumenti da riga di comando di Azure
--------------------------------------------------------------------------------------

Nell'elenco seguente vengono fornite informazioni per l'installazione degli strumenti da riga di comando in base al sistema operativo in uso.

-   **Windows**: scaricare il [programma di installazione degli strumenti da riga di comando di Azure](http://go.microsoft.com/fwlink/p?LinkID=275464). Aprire il file .msi scaricato e completare i passaggi dell'installazione seguendo le istruzioni visualizzate.

-   **Mac**: scaricare il [programma di installazione di Azure SDK](http://go.microsoft.com/fwlink/p?LinkId=252249). Aprire il file .pkg scaricato e completare i passaggi dell'installazione seguendo le istruzioni visualizzate.

-   **Linux**: installare la versione più recente di [Node.js](http://nodejs.org/) (vedere [Install Node.js via Package Manager](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager)), quindi eseguire il comando seguente:

          npm install azure-cli -g

Per controllare l'installazione, digitare `azure` al prompt dei comandi. Se l'installazione è stata completata correttamente, verrà visualizzato un elenco di tutti i comandi di `azure` disponibili.

Importazione delle impostazioniCome scaricare e importare le impostazioni di pubblicazione
------------------------------------------------------------------------------------------

Per iniziare, è necessario innanzitutto scaricare e importare le impostazioni di pubblicazione. Sarà quindi possibile utilizzare gli strumenti per creare e gestire servizi di Azure. Per scaricare le impostazioni di pubblicazione, utilizzare il comando `account download`:

     azure account download

Verrà aperto il browser predefinito e richiesto di accedere al portale di gestione. Dopo l'accesso, verrà scaricato il file `.publishsettings`. Prendere nota del percorso del file salvato.

Importare quindi il file `.publishsettings` eseguendo il comando seguente e sostituendo prima `<path-to-settings-file>` con il percorso del proprio file `.publishsettings`:

     azure account import <path-to-settings-file>

È possibile rimuovere tutte le informazioni archiviate dal comando `import` utilizzando il comando `account clear`:

     azure account clear

Per visualizzare un elenco di opzioni per i comandi `account`, utilizzare l'opzione `-help`:

     azure account -help

Dopo l'importazione delle impostazioni di pubblicazione, è opportuno eliminare il file `.publishsettings` per motivi di sicurezza. Per ulteriori informazioni, vedere [Come installare gli strumenti da riga di comando di Azure per Mac e Linux](http://go.microsoft.com/fwlink/p/?LinkId=275795). A questo punto è possibile iniziare a creare e gestire Servizi mobili di Azure dalla riga di comando o in file batch.

Creazione del servizioCome creare un servizio mobile
----------------------------------------------------

È possibile utilizzare gli strumenti da riga di comando per creare una nuova istanza del servizio mobile. Durante la creazione del servizio mobile, verrà inoltre creata un'istanza di database SQL in un nuovo server.

Utilizzare il comando seguente per creare una nuova istanza del servizio mobile nella sottoscrizione, dove `<service-name>` è il nome del nuovo servizio mobile, `<server-admin>` è il nome dell'account di accesso del nuovo server e `<server-password>` è la password per il nuovo account di accesso:

     azure mobile create <service-name> <server-admin> <server-password>

Il comando `mobile create` ha esito negativo quando il servizio mobile specificato esiste già. Negli script di automazione, provare a eliminare un servizio mobile prima di tentare di ricrearlo.

Visualizzazione di un elenco di serviziCome visualizzare l'elenco dei servizi mobili esistenti in una sottoscrizione
--------------------------------------------------------------------------------------------------------------------

Il comando seguente restituisce un elenco di tutti i servizi mobili in una sottoscrizione di Azure:

     azure mobile list

Questo comando mostra inoltre lo stato corrente e l'URL di ogni servizio mobile.

Eliminazione di un servizioCome eliminare un servizio mobile esistente
----------------------------------------------------------------------

È possibile utilizzare gli strumenti da riga di comando per eliminare un servizio mobile esistente unitamente al database SQL e al server ad esso associati. Utilizzare il comando seguente per eliminare il servizio mobile, dove `<service-name>` è il nome del servizio mobile da eliminare:

     azure mobile delete <service-name> -a -q

Se si includono i parametri `-a` e `-q`, questo comando consente di eliminare anche il database SQL e il server utilizzati dal servizio mobile senza visualizzare una richiesta di conferma.

**Nota**

Se non viene specificato il parametro `-q` insieme al parametro `-a` o `-d`, l'esecuzione viene sospesa e viene richiesto di selezionare le opzioni di eliminazione per il database SQL. Utilizzare il parametro `-a` solo quando nessun altro servizio utilizza il database o il server, altrimenti utilizzare il parametro `-d` per eliminare solo i dati appartenenti al servizio mobile che si intende eliminare.

Creazione di una tabellaCome creare una tabella nel servizio mobile
-------------------------------------------------------------------

Utilizzare il comando seguente per creare una tabella nel servizio mobile specificato, dove `<service-name>` è il nome del servizio mobile e `<table-name>` è il nome della tabella da creare:

     azure mobile table create <service-name> <table-name>

Verrà creata una nuova tabella con le autorizzazioni predefinite, `application`, per le operazioni su tabella: operazioni `insert`, `read`, `update` e `delete`.

Utilizzare il comando seguente per creare una nuova tabella con autorizzazione `read` pubblica e con autorizzazione `delete` concessa solo agli amministratori:

     azure mobile table create <service-name> <table-name> -p read=public,delete=admin

Nella tabella seguente è illustrato il valore dell'autorizzazione dello script rispetto al valore dell'autorizzazione nel [portale di gestione di Azure](https://manage.windowsazure.com/).

<table border="1" width="100%"><tr><th>Valore script</th><th>Valore portale di gestione</th></tr>
<tr><td><code>public</code></td><td>Tutti</td></tr>
<tr><td><code>application</code> (predefinito)</td><td>Chiunque con la chiave applicazione</td></tr>
<tr><td><code>user</code></td><td>	Solo gli utenti autenticati</td></tr>
<tr><td><code>admin	</code></td><td>Solo script e amministratori:</td></tr></table>

Il comando `mobile table create` ha esito negativo quando la tabella specificata esiste già. Negli script di automazione, provare a eliminare una tabella prima di tentare di ricrearla.

Visualizzazione di un elenco di tabelleCome visualizzare l'elenco delle tabelle esistenti in un servizio mobile
---------------------------------------------------------------------------------------------------------------

Utilizzare il comando seguente per restituire un elenco di tutte le tabelle in un servizio mobile, dove `<service-name>` è il nome del servizio mobile:

     azure mobile table list <service-name>

Questo comando mostra inoltre il numero di indici in ogni tabella e il numero di righe di dati attualmente nella tabella.

Eliminazione di una tabellaCome eliminare una tabella esistente dal servizio mobile
-----------------------------------------------------------------------------------

Utilizzare il comando seguente per eliminare una tabella dal servizio mobile, dove `<service-name>` è il nome del servizio mobile e `<table-name>` è il nome della tabella da eliminare:

     azure mobile table delete <service-name> <table-name> -q

Negli script di automazione utilizzare il parametro `-q` per eliminare la tabella senza visualizzare una richiesta di conferma che ne blocca l'esecuzione.

Registrazione di uno scriptCome registrare uno script in un'operazione su tabella
---------------------------------------------------------------------------------

Utilizzare il comando seguente per caricare e registrare una funzione in un'operazione su tabella, dove `<service-name>` è il nome del servizio mobile, `<table-name>` è il nome della tabella e `<operation>` è l'operazione su tabella, che può essere di tipo `read`, `insert`, `update` o `delete`:

     azure mobile script upload <service-name> table/<table-name>.<operation>.js

Si noti che questa operazione comporta il caricamento di un file JavaScript (con estensione js) dal computer locale. Il nome del file deve essere composto dai nomi della tabella e dell'operazione e deve trovarsi nella sottocartella `table` relativa al percorso in cui viene eseguito il comando. Ad esempio, l'operazione seguente consente di caricare e registrare un nuovo script `insert` che appartiene alla tabella `TodoItems`:

     azure mobile script upload todolist table/todoitems.insert.js

La dichiarazione di funzione nel file di script deve corrispondere all'operazione su tabella registrata. Questo significa che per uno script `insert` lo script caricato contiene una funzione con la firma seguente:

     function insert(item, user, request) {
            ...
        } 

Per ulteriori informazioni sulla registrazione di script, vedere la pagina relativa al [riferimento agli script del server di Servizi mobili](http://go.microsoft.com/fwlink/p?LinkId=262293).


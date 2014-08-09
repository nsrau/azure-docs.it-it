<properties  linkid="script-xplat-intro" urlDisplayName="Azure Cross-Platform Command-Line Interface" pageTitle="The Azure Cross-Platform Command-Line Interface" title="The Azure Cross-Platform Command-Line Interface" metaKeywords="Azure cross-platform command-line interface, Azure command-line, azure command-line, azure cli" description="Install and configure the Azure Cross-Platform Command-Line Interface to manage Azure Services" metaCanonical="http://www.windowsazure.com/it-it/script/xplat-cli-intro" umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="paulettm" documentationCenter="" solutions="" authors="larryfr" services="" />

# Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Azure
 
<div class="dev-center-tutorial-selector sublanding"><a  href="/en-us/manage/install-and-configure-windows-powershell/" title="PowerShell">PowerShell</a><a  href="/en-us/manage/install-and-configure-cli/" title="Interfaccia della riga di comando multipiattaforma" class="current">Interfaccia della riga di comando multipiattaforma</a></div>

L'interfaccia della riga di comando multipiattaforma (xplat-cli) di Azure rende disponibile un set di comandi multipiattaforma open source per l'utilizzo della piattaforma Azure. Fornisce gran parte delle funzionalità disponibili nel portale di gestione di Azure, tra cui la possibilità di gestire siti Web, macchine virtuali, servizi mobili, database SQL e altri servizi della piattaforma Azure.

L'interfaccia xplat-cli è scritta in JavaScript e richiede Node.js. Viene implementata mediante Azure SDK per Node.js e rilasciata con una licenza Apache 2.0. L'archivio di progetto si trova in [https://github.com/WindowsAzure/azure-sdk-tools-xplat][1].

In questo documento viene descritto come installare e configurare l'interfaccia della riga di comando multipiattaforma di Azure, nonché come eseguire attività di base con la piattaforma Azure.

## Contenuto del documento

* [Come installare l'interfaccia della riga di comando multipiattaforma di Azure](#install)
* [Come connettersi alla sottoscrizione di Azure](#configure)
* [Come utilizzare l'interfaccia della riga di comando multipiattaforma di Azure](#use)
* [Come creare script per l'interfaccia della riga di comando multipiattaforma di Azure](#script)
* [Risorse aggiuntive](#additional-resources)

<h2><a id="install" ></a>Come installare l'interfaccia della riga di comando multipiattaforma di Azure</h2>


È possibile installare xplat-cli in due modi, ossia utilizzando i pacchetti dei programmi di installazione per Windows e OS X oppure, se Node.js è installato nel sistema, il comando **npm**.

Per sistemi Linux, è necessario che Node.js sia installato ed è possibile utilizzare **npm** per installare xplat-cli come descritto di seguito oppure crearlo dal codice sorgente, disponibile all'indirizzo [http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409][2]. Per ulteriori informazioni sulla creazione da codice sorgente, vedere il file INSTALL incluso nell'archivio.

Dopo l'installazione di xplat-cli, sarà possibile utilizzare il comando **azure** dall'interfaccia della riga di comando (Bash, terminale, prompt dei comandi) per accedere ai relativi comandi.

### Tramite un programma di installazione

Sono disponibili i pacchetti di programmi di installazione seguenti:

* [Windows Installer][3]

* [OS X Installer][4]

### Tramite npm

Se Node.js è installato nel sistema, utilizzare il comando seguente per installare xplat-cli:

    npm install azure-cli -g

> [WACOM.NOTE] Per la corretta esecuzione del comando **npm**, può essere necessario utilizzare `sudo`.

Verranno installate l'interfaccia xplat-cli e le necessarie dipendenze. Al termine dell'installazione, verrà visualizzato un output simile al seguente:

    azure-cli@0.8.0 ..\node_modules\azure-cli
    |-- easy-table@0.0.1
    |-- eyes@0.1.8
    |-- xmlbuilder@0.4.2
    |-- colors@0.6.1
    |-- node-uuid@1.2.0
    |-- async@0.2.7
    |-- underscore@1.4.4
    |-- tunnel@0.0.2
    |-- omelette@0.1.0
    |-- github@0.1.6
    |-- commander@1.0.4 (keypress@0.1.0)
    |-- xml2js@0.1.14 (sax@0.5.4)
    |-- streamline@0.4.5
    |-- winston@0.6.2 (cycle@1.0.2, stack-trace@0.0.7, async@0.1.22, pkginfo@0.2.3, request@2.9.203)
    |-- kuduscript@0.1.2 (commander@1.1.1, streamline@0.4.11)
    |-- azure@0.7.13 (dateformat@1.0.2-1.2.3, envconf@0.0.4, mpns@2.0.1, mime@1.2.10, validator@1.4.0, xml2js@0.2.8, wns@0.5.3, request@2.25.0)

> [WACOM.NOTE] È possibile installare Node.js da [http://nodejs.org/][5].

<h2><a id="Configure" ></a>Come connettersi alla sottoscrizione di Azure</h2>

Per la maggior parte dei comandi di xplat-cli è necessaria una sottoscrizione, sebbene alcuni funzionino anche senza. Per configurare xplat-cli per l'utilizzo con la sottoscrizione, è possibile eseguire una delle operazioni seguenti:

* Scaricare e utilizzare un file di impostazioni di pubblicazione.

OPPURE

* Accedere ad Azure con un account aziendale. Quando si effettua l'accesso, l'autenticazione delle credenziali viene eseguita tramite Azure Active Directory.

Per scegliere facilmente il metodo di autenticazione più adatto alle proprie esigenze, tenere presente quanto segue:

* Il metodo di accesso può consentire di gestire più facilmente l'accesso alla sottoscrizione, ma può creare problemi con l'automazione, in quanto le credenziali possono scadere e sarà necessario ripetere l'accesso.
  
  > [WACOM.NOTE] Il metodo di accesso funziona solo con l'account aziendale. L'account aziendale corrisponde a un utente gestito dall'organizzazione, come definito nel tenant Azure Active Directory aziendale. Se attualmente non si dispone di un account aziendale e si utilizza un account Microsoft per accedere alla sottoscrizione di Azure, è possibile crearne facilmente uno eseguendo la procedura seguente.
  > 
  > 1.  Accedere al [portale di gestione di Azure][6] e fare clic su **Active Directory**.
  > 
  > 2.  Se non esiste alcuna directory, selezionare **Create your directory** e specificare le informazioni richieste.
  > 
  > 3.  Selezionare la directory e aggiungere un nuovo utente. Questo nuovo utente corrisponde a un account aziendale.
  >     
  >     Durante la creazione dell'utente, verranno forniti un indirizzo di posta elettronica e una password provvisoria. Salvare queste informazioni perché verranno utilizzate in un altro passaggio.
  > 
  > 4.  Nel portale di gestione selezionare **Settings** e quindi **Administrators**. Selezionare **Add** e aggiungere il nuovo utente come co-amministratore. In questo modo l'account aziendale potrà gestire la sottoscrizione di Azure.
  > 
  > 5.  Infine, disconnettersi dal portale Azure ed effettuare di nuovo l'accesso con il nuovo account aziendale. La prima volta che si accede con questo account verrà richiesto di cambiare la password.
  > 
  > Per ulteriori informazioni sull'account aziendale con Microsoft Azure, vedere [Iscrizione ad Azure come organizzazione][7].

* Con il metodo del file di impostazioni di pubblicazione viene creato un certificato che consente di eseguire attività di gestione per tutto il periodo di validità della sottoscrizione e del certificato. Con questo metodo diventa più facile utilizzare l'automazione per attività di lunga durata. Dopo aver scaricato e importato le informazioni, non sarà necessario fornirle nuovamente. Tuttavia, con questo metodo risulta più difficile gestire l'accesso a una sottoscrizione, in quanto chiunque disponga di accesso al certificato può gestire anche la sottoscrizione.

Per ulteriori informazioni sulla gestione di autenticazione e sottoscrizione, vedere [Differenza tra autenticazione basata su account e autenticazione basata su certificato][8].

Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][9].

### Utilizzare il metodo di accesso

Per accedere con un account aziendale, utilizzare il comando seguente:

    azure login [nomeutente] [password]

> [WACOM.NOTE] La prima volta che si effettua l'accesso con queste credenziali, verrà visualizzata la richiesta di specificare se si desidera memorizzare nella cache un token di autenticazione. Questa richiesta viene visualizzata anche se in precedenza è stato utilizzato il comando `azure logout` descritto di seguito. Per ignorare la richiesta per gli scenari di automazione, utilizzare il parametro `-q` con il comando `azure login`.

> [WACOM.NOTE] Quando si esegue l'autenticazione con un account aziendale, le informazioni per l'accesso alla sottoscrizione di Azure vengono archiviate in una directory `.azure` all'interno della directory `user`. La directory `user` è protetta dal sistema operativo. Tuttavia, è consigliabile eseguire altri passaggi per crittografarla. A tale scopo, è possibile procedere come segue:

Per disconnettersi, utilizzare il comando seguente:

    azure logout [nomeutente]

> [WACOM.NOTE] Se le sottoscrizioni associate all'account sono state autenticate solo con Active Directory, dopo la disconnessione le informazioni di sottoscrizione verranno eliminate dal profilo locale. Se tuttavia per le sottoscrizioni è stato anche importato il file di impostazioni di pubblicazione, con la disconnessione verranno eliminate dal profilo locale solo le informazioni correlate ad Active Directory.

> [WACOM.NOTE] I comandi seguenti non funzionano correttamente quando si esegue l'autenticazione con un account:
> 
> * `azure vm`
> * `azure network`
> * `azure mobile`
> 
> Se è necessario utilizzare questi comandi, utilizzare un file di impostazioni di pubblicazione per eseguire l'autenticazione in Azure, come descritto nella sezione seguente.

### Utilizzare il metodo del file di impostazioni di pubblicazione

Per scaricare le impostazioni di pubblicazione per l'account, utilizzare il comando seguente:

    azure account download

Verrà aperto il browser predefinito e verrà richiesto di effettuare l'accesso al portale di gestione di Azure. Dopo l'accesso, verrà scaricato il file `.publishsettings`. Prendere nota del percorso in cui il file viene salvato.

> [WACOM.NOTE] Se l'account è associato a più tenant Azure Active Directory, è possibile che venga richiesto di selezionare per quale istanza di Active Directory si desidera scaricare un file di impostazioni di pubblicazione.
> 
> Dopo aver effettuato la selezione tramite la pagina di download oppure visitando il portale di gestione di Azure, il tenant Active Directory selezionato diventerà quello utilizzato per impostazione predefinita nel portale e nella pagina di download. Dopo la scelta di un'impostazione predefinita, nella parte superiore della pagina di download verrà visualizzato un messaggio analogo al seguente: '**fare clic qui per tornare nella pagina di selezione**'. Utilizzare il collegamento specificato per tornare nella pagina di selezione.

Importare quindi il file `.publishsettings` eseguendo il comando seguente, sostituendo prima `[percorso del file .publishsettings]` con il percorso del proprio file `.publishsettings`:

    azure account import [percorso del file .publishsettings]

> [WACOM.NOTE] Quando si importano le impostazioni di pubblicazione, le informazioni per l'accesso alla sottoscrizione di Azure vengono archiviate in una directory `.azure` all'interno della directory `user`. La directory `user` è protetta dal sistema operativo. Tuttavia, è consigliabile eseguire altri passaggi per crittografarla.`` A tale scopo, è possibile procedere come segue:
> 
> * In Windows, modificare le proprietà della directory o utilizzare BitLocker.
> * In Mac, abilitare FileVault per la cartella.
> * In Ubuntu, utilizzare la funzionalità per la crittografia della cartella Home. Altre distribuzioni di Linux offrono funzionalità equivalenti.

Dopo l'importazione delle impostazioni di pubblicazione, è consigliabile eliminare il file `.publishsettings`, in quanto non è più necessario per gli strumenti da riga di comando e presenta un rischio di sicurezza, perché può essere utilizzato per ottenere l'accesso alla sottoscrizione.

### Più sottoscrizioni

Se si dispone di più sottoscrizioni, effettuando l'accesso sarà possibile accedere a tutte le sottoscrizioni associate alle credenziali. Se si utilizza un file di impostazioni di pubblicazione, il file `.publishsettings` conterrà informazioni per tutte le sottoscrizioni. Con entrambi i metodi, una sola sottoscrizione verrà selezionata come quella predefinita utilizzata da xplat-cli per l'esecuzione delle operazioni. Per visualizzare le sottoscrizioni, inclusa quella predefinita, è possibile utilizzare il comando `azure account list`. Questo comando restituirà informazioni simili alle seguenti:

    info:    Executing command account list
    data:    Name              Id                                    Current
    data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
    data:    Azure-sub-2       ####################################  false

Nell'elenco precedente la colonna **Current** indica Azure-sub-1 come sottoscrizione predefinita corrente. Per cambiare la sottoscrizione predefinita, utilizzare il comando `azure account select` e specificarne una diversa, ad esempio:

    azure account select Azure-sub-2

La sottoscrizione predefinita diventerà in questo modo Azure-sub-2.

> [WACOM.NOTE] La modifica della sottoscrizione predefinita ha effetto immediato ed è una modifica globale. I nuovi comandi di xplat, sia che vengano eseguiti dalla stessa istanza della riga di comando o da una diversa, utilizzeranno la nuova sottoscrizione predefinita.

Se si desidera utilizzare una sottoscrizione non predefinita con xplat-cli, ma senza cambiare quella attualmente predefinita, è possibile utilizzare l'opzione `--subscription` e specificare il nome della sottoscrizione da utilizzare per l'operazione.

<h2><a  id="use" ></a>Come utilizzare l'interfaccia della riga di comando multipiattaforma di Azure</h2>


Per accedere a xplat-cli, utilizzare il comando `azure`. Per visualizzare l'elenco di comandi disponibili, utilizzare il comando `azure` senza parametri. Verranno visualizzate informazioni simili alle seguenti:

	info:             _    _____   _ ___ ___
	info:            /_\  |_  / | | | _ \ __|
	info:      _ ___/ _ \__/ /| |_| |   / _|___ _ _
	info:    (___  /_/ \_\/___|\___/|_|_\___| _____)
	info:       (_______ _ _)         _ ______ _)_ _
	info:              (______________ _ )   (___ _ _)
	info:
	info:    Windows Azure: Microsoft's Cloud Platform
	info:
	info:    Tool version 0.8.0
	help:
	help:    Display help for a given command
	help:      help [options] [command]
	help:
	help:    Opens the portal in a browser
	help:      portal [options]
	help:
	help:    Commands:
	help:      account        Commands to manage your account information and publish settings
	help:      config         Commands to manage your local settings
	help:      hdinsight      Commands to manage your HDInsight accounts
	help:      mobile         Commands to manage your Mobile Services
	help:      network        Commands to manage your Networks
	help:      sb             Commands to manage your Service Bus configuration
	help:      service        Commands to manage your Cloud Services
	help:      site           Commands to manage your Web Sites
	help:      sql            Commands to manage your SQL Server accounts
	help:      storage        Commands to manage your Storage objects
	help:      vm             Commands to manage your Virtual Machines
	help:
	help:    Options:
	help:      -h, --help     output usage information
	help:      -v, --version  output the application version

I comandi di primo livello elencati sopra contengono comandi per l'utilizzo di un'area specifica di Azure. Ad esempio, il comando `azure account` contiene comandi correlati alla sottoscrizione di Azure, quali le impostazioni `download` e `import` utilizzate in precedenza.

La maggior parte dei comandi è formattata come `azure <command> <operation> [parametri]` e consente di eseguire operazioni su un servizio o un oggetto, ad esempio la configurazione dell'account. Altri comandi includono comandi secondari nel formato `azure <command> <subcommand> <operation> [parametri]`. Di seguito sono riportati esempi di comandi correlati alla configurazione dell'account:

* Per visualizzare le sottoscrizioni importate, utilizzare il comando seguente:
  
        azure account list

* Se sono state importate diverse sottoscrizioni, utilizzare il comando seguente per impostarne una come predefinita:
  
        azure account set <subscription>

Il parametro `--help` o `-h` consente di visualizzare la Guida per specifici comandi. In alternativa, è anche possibile utilizzare il formato `azure help [comando] [opzioni]` per ottenere le stesse informazioni. Ad esempio, tutti i comandi seguenti restituiranno le stesse informazioni:

    azure account set --help
    
    azure account set -h
    
    azure help account set

In caso di dubbi sui parametri necessari per un comando, fare riferimento alla Guida utilizzando `--help`, `-h` o `azure help [comando]`.

### Impostazione della modalità di configurazione

Con xplat-cli è possibile eseguire operazioni di gestione su singole *risorse*, ovvero entità gestite dall'utente come un server di database, un database o un sito Web. Questa è la modalità di funzionamento predefinita per xplat-cli ed è denominata **Gestione servizi di Azure**. Se tuttavia si dispone di una soluzione complessa costituita da più risorse, risulta utile avere la possibilità di gestirla come una singola unità.

Per supportare la gestione di un gruppo di risorse come singola unità logica, o *gruppo di risorse*, è stata introdotta una versione di anteprima di **Gestione risorse** come nuova modalità di gestione delle risorse di Azure.

> [WACOM.NOTE] Gestione risorse è attualmente in versione di anteprima e non fornisce lo stesso livello delle funzionalità di gestione disponibili con Gestione servizi di Azure.

Per supportare la nuova modalità Gestione risorse, xplat-cli consente di passare da una modalità di gestione all'altra tramite il comando `azure config mode`.

Per impostazione predefinita, con xplat-cli viene utilizzata la modalità Gestione servizi di Azure. Per passare in modalità Gestione risorse, utilizzare il comando seguente:

    azure config mode arm

Per tornare alla modalità Gestione servizi di Azure, utilizzare il comando seguente:

    azure config mode asm 

> [WACOM.NOTE] La modalità Gestione risorse e la modalità Gestione servizi di Azure si escludono a vicenda, ossia le risorse create in una modalità non possono essere gestite dall'altra.

Per ulteriori informazioni sull'utilizzo della modalità Gestione risorse con xplat-cli, vedere [Utilizzo dell'interfaccia della riga di comando multipiattaforma di Azure con Gestione risorse](/it-it/documentation/articles/xplat-cli-azure-resource-manager/).

### Utilizzo dei servizi in modalità Gestione servizi di Azure

L'interfaccia xplat-cli consente di gestire facilmente i servizi di Azure. In questo esempio verrà illustrato come utilizzare xplat-cli per gestire un sito Web di Azure.

1.  Utilizzare il comando seguente per creare un nuovo sito Web di Azure. Sostituire **mywebsite** con un nome univoco.
    
         azure site create mywebsite
    
    Verrà richiesto di specificare l'area in cui verrà creato il sito Web. Selezionare un'area geograficamente vicina. Al termine del comando, il sito Web sarà disponibile all'indirizzo http://mywebsite.azurewebsites.net (sostituire **mywebsite** con il nome specificato).
    
    > [WACOM.NOTE] Se si utilizza Git per il controllo del codice sorgente di progetto, è possibile specificare il parametro `--git` per creare un archivio Git in Azure per il sito Web. In questo modo verrà inoltre inizializzato un archivio Git nella directory da cui è stato eseguito il comando, se non ne esiste già uno.
    > Verrà anche creato un sito remoto Git, denominato **azure**, che è possibile utilizzare per effettuare il push delle distribuzioni nel sito Web di Azure con il comando `git push azure master`.
    
    > [WACOM.NOTE] Se viene visualizzato un messaggio di errore indicante che 'site' non è un comando di Azure, è molto probabile che xplat-cli sia in modalità gruppo di risorse. Per tornare in modalità risorsa, utilizzare il comando `azure config mode asm`.

2.  Utilizzare il comando seguente per elencare i siti Web per la sottoscrizione:
    
         azure site list
    
    L'elenco dovrebbe contenere il sito creato nel passaggio precedente.

3.  Utilizzare il comando seguente per interrompere il sito Web. Sostituire **mywebsite** con il nome del sito.
    
         azure site stop mywebsite
    
    Al termine del comando, è possibile aggiornare il browser per verificare che il sito sia stato interrotto.

4.  Utilizzare il comando seguente per avviare il sito Web. Sostituire **mywebsite** con il nome del sito.
    
         azure site start mywebsite
    
    Al termine del comando, è possibile aggiornare il browser per verificare che il sito sia stato avviato.

5.  Utilizzare il comando seguente per eliminare il sito Web. Sostituire **mywebsite** con il nome del sito.
    
         azure site delete mywebsite
    
    Al termine del comando, utilizzare il comando `azure site list` per verificare che il sito Web non esista più.

<h2><a  id="script" ></a>Come creare script per l'interfaccia della riga di comando multipiattaforma di Azure</h2>

Sebbene sia possibile utilizzare xplat-cli per eseguire manualmente i comandi, è anche possibile creare complessi flussi di lavoro di automazione sfruttando le funzionalità dell'interprete della riga di comando e di altre utilità della riga di comando disponibili nel sistema. Ad esempio, il comando seguente interromperà tutti i siti Web di Azure in esecuzione:

    azure site list | grep 'Running' | awk '{system("azure site stop "$2)}'

In questo esempio viene inviato un elenco di siti Web al comando `grep`, che esamina ogni riga per individuare la stringa 'Running'. Le righe corrispondenti vengono quindi inviate al comando `awk`, che chiama `azure site stop` e utilizza la seconda colonna passata (il nome del sito in esecuzione) come nome del sito da interrompere.

Anche se questo esempio dimostra come sia possibile concatenare i comandi, è inoltre possibile creare script più elaborati utilizzando le funzionalità dell'interprete della riga di comando. Interpreti della riga di comando diversi prevedono funzionalità e sintassi di scripting diverse. Bash è probabilmente l'interprete della riga di comando più diffuso per i sistemi basati su UNIX, tra cui Linux e OS X.

Per informazioni sulla creazione di script con Bash, vedere la [guida alla creazione di script avanzati con Bash][10].

Per informazioni più generali sulla creazione di script per sistemi basati su OS X o Linux, vedere l'articolo relativo a [shell script][11].

Per informazioni sulla creazione di script per sistemi basati su Windows tramite file batch, vedere le [informazioni di riferimento dalla A alla Z per la riga di comando][12].

### Significato dei risultati

Quando si creano gli script, spesso è necessario acquisire l'output di un comando e passarlo a un altro comando o eseguire un'operazione come la ricerca di un valore specifico. L'interfaccia xplat-cli genera output in STDOUT e STDERR. A ogni riga viene aggiunto come prefisso la stringa `info:` per i messaggi di stato informativi oppure `data:` per i dati restituiti su un servizio. Tuttavia, è possibile indicare a xplat-cli di restituire informazioni più dettagliate utilizzando il parametro `--verbose` o `-v`. In questo caso verranno restituite informazioni aggiuntive con il prefisso `verbose:`.

Ad esempio, il comando `azure site list` restituisce l'output seguente:

    info:    Executing command site list
    + Enumerating sites
    data:    Name           Status   Mode  Host names
    data:    -------------  -------  ----  -------------------------------
	data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
    info:    site list command OK

Se si specifica il parametro `--verbose` o `-v`, verranno restituite informazioni simili alle seguenti:

	info:    Executing command site list
	verbose: Subscription ####################################
	verbose: Enumerating sites
	verbose: [
	verbose:     {
	verbose:         ComputeMode: 'Shared',
	verbose:         HostNameSslStates: {
	verbose:             HostNameSslState: [
	verbose:                 {
	verbose:                     IpBasedSslResult: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     SslState: 'Disabled',
	verbose:                     ToUpdateIpBasedSsl: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     VirtualIP: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     Thumbprint: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     ToUpdate: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     Name: 'myawesomesite.azurewebsites.net'
	verbose:                 },
	...
	verbose:     }
	verbose: ]
	data:    Name           Status   Mode  Host names
	data:    -------------  -------  ----  -------------------------------
	data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
	info:    site list command OK

Si noti che le informazioni `verbose:` sembrano dati in formato JSON. È possibile utilizzare il parametro `--json` per restituire informazioni in formato JSON se si utilizzano utilità che riconoscono in modo nativo JSON, come [jsawk][13] o [jq][14], ad esempio:

    azure site list --json | jsawk -n 'out(this.Name)' | xargs -L 1 azure site delete -q 

Il comando precedente recupera un elenco di siti Web in formato JSON, quindi utilizza jsawk per recuperare i nomi dei siti e infine xargs per eseguire un comando di eliminazione per ogni sito, passando il nome del sito come parametro.

> [WACOM.NOTE] Il parametro `--json` blocca la generazione di informazioni di stato o dati (stringhe con il prefisso `info:` e `data:`). Se ad esempio viene utilizzato il parametro `--json` con il comando `azure site create`, non verrà restituito alcun output, perché questo comando non restituisce dati diversi da `info:`.

### Gestione degli errori

Anche se xplat-cli registra informazioni sugli errori in STDERR, è possibile che vengano registrate informazioni aggiuntive sugli errori anche in un file **azure.err** nella directory da cui è stato eseguito lo script. Se in questo file vengono registrate informazioni, in STDOUT verrà scritto quanto segue:

    info:    Error information has been recorded to azure.err

### Stato di uscita

Alcuni comandi di xplat-cli non restituiscono uno stato di uscita diverso da zero, se mancano i parametri obbligatori. Al contrario, richiederanno l'input dell'utente. Quando si utilizza ad esempio il comando `azure site create` per creare un nuovo sito Web, se non si specifica un nome del sito o un parametro `--location`, verrà richiesto di fornire questi valori.

Se si scrive uno script che si basa sullo stato di uscita, verificare che i comandi di xplat-cli utilizzati non richiedano input dell'utente.

<h2><a  id="additional-resources" ></a>Risorse aggiuntive</h2>

* Per ulteriori informazioni su xplat-cli, per scaricare codice sorgente, segnalare problemi o contribuire al progetto, visitare la pagina dell'[archivio GitHub per l'interfaccia della riga di comando multipiattaforma di Azure][1].

* Se si verificano problemi con l'utilizzo di xplat-cli o di Azure, visitare i [forum di Azure][15].

* Per ulteriori informazioni su Azure, vedere [http://www.windowsazure.com/][16].




[1]: https://github.com/WindowsAzure/azure-sdk-tools-xplat
[2]: http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409
[3]: http://go.microsoft.com/fwlink/?LinkID=275464&clcid=0x409
[4]: http://go.microsoft.com/fwlink/?LinkId=252249
[5]: http://nodejs.org/
[6]: https://manage.windowsazure.com
[7]: http://www.windowsazure.com/it-it/documentation/articles/sign-up-organization/
[8]: http://msdn.microsoft.com/it-it/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
[9]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A7171371E
[10]: http://tldp.org/LDP/abs/html/
[11]: http://en.wikipedia.org/wiki/Shell_script
[12]: http://technet.microsoft.com/en-us/library/bb490890.aspx
[13]: https://github.com/micha/jsawk
[14]: http://stedolan.github.io/jq/
[15]: http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home
[16]: http://www.windowsazure.com
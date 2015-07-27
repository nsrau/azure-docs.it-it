<properties
	pageTitle="Interfaccia della riga di comando di Azure per Mac, Linux e Windows"
	description="Installare e configurare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows per gestire i servizi di Azure"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="squillace"
	services=""/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/10/2015"
	ms.author="rasquill"/>

# Installare e configurare l'interfaccia della riga di comando di Azure

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [Azure CLI](xplat-cli.md)

L'interfaccia della riga di comando di Azure fornisce un insieme di comandi open source e multipiattaforma per usare la piattaforma Azure. L'interfaccia della riga di comando fornisce gran parte delle funzionalità disponibili nel portale di gestione di Azure, tra cui la possibilità di gestire siti Web, macchine virtuali, servizi mobili, database SQL e altri servizi della piattaforma Azure.

L'interfaccia della riga di comando di Azure è scritta in JavaScript e richiede Node.js. Viene implementata mediante Azure SDK per Node.js e rilasciata con una licenza Apache 2.0. L'archivio di progetto si trova in [https://github.com/azure/azure-xplat-cli](https://github.com/azure/azure-xplat-cli).

Questo documento descrive come installare e configurare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows, nonché come eseguire attività di base con la piattaforma Azure.

<a id="install"></a>
## Come installare l'interfaccia della riga di comando di Azure

Per informazioni sulla procedura di installazione dell'interfaccia della riga di comando di Azure, leggere la pagina [Installare l'interfaccia della riga di comando di Azure](xplat-cli-install.md).


<a id="configure"></a>
## Come connettersi alla sottoscrizione di Azure

Per la maggior parte dei comandi dell'interfaccia della riga di comando di Azure è necessaria una sottoscrizione, sebbene alcuni funzionino anche senza. Per configurare l'interfaccia della riga di comando di Azure per l'uso con la sottoscrizione, seguire i passaggi nell'argomento relativo alla [connessione alla sottoscrizione di Azure](xplat-cli-connect.md).


<a id="use"></a>
## Come usare l'interfaccia della riga di comando di Azure

Per accedere all'interfaccia della riga di comando di Azure, usare il comando `azure`. Per visualizzare un elenco di comandi disponibili, usare il comando `azure` senza parametri. Verranno visualizzate informazioni simili alle seguenti:

	info:             _    _____   _ ___ ___
	info:            /_\  |_  / | | | _ \ __|
	info:      _ ___/ _ __/ /| |_| |   / _|___ _ _
	info:    (___  /_/ _/___|___/|_|____| _____)
	info:       (_______ _ _)         _ ______ _)_ _
	info:              (______________ _ )   (___ _ _)
	info:
	info:    Microsoft Azure: Microsoft's Cloud Platform
	info:
	info:    Tool version 0.8.10
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

I comandi di primo livello elencati sopra contengono comandi per l'uso di un'area specifica di Azure. Ad esempio, il comando `azure account` contiene comandi correlati alla sottoscrizione di Azure come le impostazioni `download` e `import` usate precedentemente. Per informazioni dettagliate su comandi e opzioni disponibili, vedere l'argomento relativo all'[uso dell'interfaccia da riga di comando di Azure per Mac, Linux e Windows].

La maggior parte dei comandi è formattata come `azure <command> <operation> [parameters]` ed esegue operazioni su un servizio o un oggetto, ad esempio la configurazione dell'account. Altri comandi includono comandi secondari nel formato `azure <command> <subcommand> <operation> [parameters]`. Di seguito sono riportati esempi di comandi correlati alla configurazione dell'account:

* Per visualizzare le sottoscrizioni importate, usare il comando seguente:

		azure account list

* Se sono state importate diverse sottoscrizioni, usare il comando seguente per impostarne una come predefinita:

		azure account set <subscription>

Il parametro `--help` o `-h` consente di visualizzare la Guida per comandi specifici. In alternativa, è anche possibile usare il formato `azure help [command] [options]` per ottenere le stesse informazioni. Ad esempio, tutti i comandi seguenti restituiranno le stesse informazioni:

	azure account set --help

	azure account set -h

	azure help account set

In caso di dubbi sui parametri necessari per un comando, fare riferimento alla Guida usando `--help`, `-h` o `azure help [command]`.

### Impostazione della modalità di configurazione

Con l'interfaccia della riga di comando di Azure è possibile eseguire operazioni di gestione su singole _risorse_, ovvero entità gestite dall'utente come un server di database, un database o un sito Web. Questa è la modalità di funzionamento predefinita per l'interfaccia della riga di comando di Azure ed è denominata **Gestione servizi di Azure**. Se tuttavia si dispone di una soluzione complessa costituita da più risorse, risulta utile avere la possibilità di gestirla come una singola unità.

Per supportare la gestione di un gruppo di risorse come singola unità logica, o _gruppo di risorse_, è stata introdotta una versione di anteprima di **Gestione risorse** come nuova modalità di gestione delle risorse di Azure.

>[AZURE.NOTE]Gestione risorse è attualmente in versione di anteprima e non fornisce lo stesso livello delle funzionalità di gestione disponibili con Gestione servizi di Azure.

Per supportare la nuova modalità Gestione risorse, l'interfaccia della riga di comando di Azure consente di passare da una modalità di gestione all'altra tramite il comando `azure config mode`.

Per impostazione predefinita, con l'interfaccia della riga di comando di Azure viene usata la modalità Gestione servizi di Azure. Per passare alla modalità Gestione risorse, usare il comando seguente:

	azure config mode arm

Per tornare alla modalità Gestione servizi di Azure, usare il comando seguente:

	azure config mode asm

>[AZURE.NOTE]La modalità Gestione risorse e la modalità Gestione servizi di Azure si escludono a vicenda, ossia le risorse create in una modalità non possono essere gestite dall'altra.

Per altre informazioni sull'uso della modalità Gestione risorse con l'interfaccia della riga di comando di Azure, vedere [Uso dell'interfaccia della riga di comando multipiattaforma di Azure con Gestione risorse][cliarm].

### Uso dei servizi in modalità Gestione servizi di Azure

L'interfaccia della riga di comando di Azure consente di gestire facilmente i servizi di Azure. In questo esempio verrà illustrato come usare l'interfaccia della riga di comando di Azure per gestire un sito Web di Azure.

1. Usare il comando seguente per creare un nuovo sito Web di Azure. Sostituire **mywebsite** con un nome univoco.

		azure site create mywebsite

	Verrà chiesto di specificare l'area in cui creare il sito Web. Selezionare un'area geograficamente vicina. Al termine del comando, il sito Web sarà disponibile all'indirizzo http://mywebsite.azurewebsites.net (sostituire **mywebsite** con il nome specificato).

	> [AZURE.NOTE]Se si usa Git per il controllo del codice sorgente di progetto, è possibile specificare il parametro `--git` per creare un archivio Git in Azure per il sito Web. In questo modo verrà inoltre inizializzato un archivio Git nella directory da cui è stato eseguito il comando, se non ne esiste già uno. Verrà anche creato un sito remoto Git, denominato __azure__, che è possibile usare per effettuare il push delle distribuzioni nel sito Web di Azure con il comando `git push azure master`.

	> [AZURE.NOTE]Se viene visualizzato un messaggio di errore indicante che 'site' non è un comando di Azure, è molto probabile che l'interfaccia della riga di comando di Azure sia in modalità gruppo di risorse. Per tornare alla modalità risorsa, usare il comando `azure config mode asm`.

2. Usare il comando seguente per elencare i siti Web per la sottoscrizione:

		azure site list

	L'elenco dovrebbe contenere il sito creato nel passaggio precedente.

2. Usare il comando seguente per arrestare il sito Web. Sostituire **mywebsite** con il nome del sito.

		azure site stop mywebsite

	Al termine del comando, è possibile aggiornare il browser per verificare che il sito sia stato arrestato.

3. Usare il comando seguente per avviare il sito Web. Sostituire **mywebsite** con il nome del sito.

		azure site start mywebsite

	Al termine del comando, è possibile aggiornare il browser per verificare che il sito sia stato avviato.

4. Usare il comando seguente per eliminare il sito Web. Sostituire **mywebsite** con il nome del sito.

		azure site delete mywebsite

	Al termine del comando, usare il comando `azure site list` per verificare che il sito Web non esista più.

<a id="script"></a>
## Come generare lo script dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows

Sebbene sia possibile usare l'interfaccia della riga di comando di Azure per eseguire manualmente i comandi, è anche possibile creare complessi flussi di lavoro di automazione sfruttando le funzionalità dell'interprete della riga di comando e altre utilità della riga di comando disponibili nel sistema. Ad esempio, il comando seguente arresterà tutti i siti Web di Azure in esecuzione:

	azure site list | grep 'Running' | awk '{system("azure site stop "$2)}'

In questo esempio viene inviato un elenco di siti Web al comando `grep`, che esamina ogni riga per individuare la stringa 'Running'. Le righe corrispondenti vengono quindi inviate al comando `awk`, che chiama `azure site stop` e usa la seconda colonna passata (il nome del sito in esecuzione) come nome del sito da arrestare.

Anche se questo esempio dimostra come sia possibile concatenare i comandi, è inoltre possibile creare script più elaborati usando le funzionalità dell'interprete della riga di comando. Interpreti della riga di comando diversi prevedono funzionalità e sintassi di scripting diverse. Bash è probabilmente l'interprete della riga di comando più diffuso per i sistemi basati su UNIX, tra cui Linux e OS X.

Per informazioni sulla creazione di script con Bash, vedere la [guida alla creazione di script avanzati con Bash][advanced-bash].

Per informazioni più generali sulla creazione di script per sistemi basati su OS X o Linux, vedere l'articolo relativo a [shell script][script].

Per informazioni sulla creazione di script per sistemi basati su Windows tramite file batch, vedere le [informazioni di riferimento dalla A alla Z per la riga di comando][batch].

### Significato dei risultati

Quando si creano gli script, spesso è necessario acquisire l'output di un comando e passarlo a un altro comando o eseguire un'operazione come la ricerca di un valore specifico. L'interfaccia della riga di comando di Azure genera output in STDOUT e STDERR. A ogni riga viene aggiunta come prefisso la stringa `info:` per i messaggi di stato informativi oppure la stringa `data:` per i dati restituiti su un servizio. Tuttavia, è possibile indicare all'interfaccia della riga di comando di Azure di restituire informazioni più dettagliate usando il parametro `--verbose` o `-v`. In questo caso verranno restituite altre informazioni con la stringa di prefisso `verbose:`

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

Si noti che le informazioni `verbose:` sembrano dati in formato JSON. È possibile usare il parametro `--json` per restituire informazioni in formato JSON se si usano utilità che riconoscono in modo nativo JSON, come [jsawk](https://github.com/micha/jsawk) o [jq](http://stedolan.github.io/jq/). Ad esempio:

	azure site list --json | jsawk -n 'out(this.Name)' | xargs -L 1 azure site delete -q

Il comando precedente recupera un elenco di siti Web in formato JSON, quindi usa jsawk per recuperare i nomi dei siti e infine xargs per eseguire un comando di eliminazione per ogni sito, passando il nome del sito come parametro.

>[AZURE.NOTE]Il parametro `--json` blocca la generazione di informazioni di stato o dati (stringhe con i prefissi `info:` e `data:`). Se ad esempio viene usato il parametro `--json` con il comando `azure site create`, non verrà restituito alcun output, perché questo comando non restituisce dati diversi da `info:`.

### Gestione degli errori

Anche se l'interfaccia della riga di comando di Azure non registra informazioni sugli errori in STDERR, è possibile che vengano registrate informazioni aggiuntive sugli errori anche in un file **azure.err** nella directory da cui è stato eseguito lo script. Se in questo file vengono registrate informazioni, in STDOUT verrà scritto quanto segue:

	info:    Error information has been recorded to azure.err

### Stato di uscita

Alcuni comandi dell'interfaccia della riga di comando di Azure non restituiscono uno stato di uscita diverso da zero, se mancano i parametri obbligatori. Al contrario, richiederanno l'input dell'utente. Quando si usa ad esempio il comando `azure site create` per creare un nuovo sito Web, se non si specifica un nome del sito o un parametro `--location`, verrà chiesto di fornire questi valori.

Se si scrive uno script che si basa sullo stato di uscita, verificare che i comandi dell'interfaccia della riga di comando di Azure usati non richiedano input dell'utente.

<a id="additional-resources"></a>

## Risorse aggiuntive

* [Elenco di comandi dettagliati di gestione del servizio][Using the Azure CLI]

* [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows](cli-cli-azure-resource-manager)

* [Uso dell'interfaccia della riga di comando di Azure con Gestione risorse][cliarm]

* Per informazioni sull'interfaccia della riga di comando di Azure, per scaricare il codice sorgente, segnalare problemi o contribuire al progetto, visitare l'[archivio GitHub per l'interfaccia della riga di comando di Azure](https://github.com/azure/azure-xplat-cli).

* In caso di problemi durante l'uso dell'interfaccia della riga di comando di Azure o di Azure, visitare i [forum di Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).

* Per altre informazioni su Azure, vedere [http://azure.microsoft.com/](http://azure.microsoft.com).




[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert

[Azure Web Site]: ../media/freetrial.png
[select a preview feature]: ../media/antares-iaas-preview-02.png
[select subscription]: ../media/antares-iaas-preview-03.png
[free-trial]: http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E
[advanced-bash]: http://tldp.org/LDP/abs/html/
[script]: http://en.wikipedia.org/wiki/Shell_script
[batch]: http://technet.microsoft.com/library/bb490890.aspx
[cliarm]: xplat-cli-azure-resource-manager.md
[portal]: https://manage.windowsazure.com
[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[Using the Azure CLI]: virtual-machines-command-line-tools.md

<!---HONumber=July15_HO3-->
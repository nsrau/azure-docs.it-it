# <a name="how-to-use-the-azure-command-line-tools-for-mac-and-linux"></a>Come usare gli strumenti da riga di comando di Azure per Mac e Linux
In questa guida viene descritto come usare gli strumenti da riga di comando di Azure per Mac e Linux per creare e gestire servizi in Azure. Gli scenari presentati includono **installazione degli strumenti**, **importazione delle impostazioni di pubblicazione**, **creazione e gestione di Siti Web di Azure** e **creazione e gestione di Macchine virtuali di Azure**. Per la documentazione di riferimento completa, vedere la [documentazione sulla riga di comando di Azure per Mac e Linux][reference-docs]. 

## <a name="table-of-contents"></a>Sommario
* [Che cosa sono gli strumenti da riga di comando di Azure per Mac e Linux](#Overview)
* [Come installare gli strumenti da riga di comando di Azure per Mac e Linux](#Download)
* [Come creare un account Azure](#CreateAccount)
* [Come scaricare e importare impostazioni di pubblicazione](#Account)
* [Come creare e gestire un sito Web di Azure](#WebSites)
* [Come creare e gestire una macchina virtuale di Azure](#VMs)

<h2><a id="Overview"></a>Che cosa sono gli strumenti da riga di comando di Azure per Mac e Linux</h2>

Gli strumenti da riga di comando di Azure per Mac e Linux sono un set di strumenti da riga di comando per la distribuzione e la gestione di servizi di Azure.

Sono supportate le attività seguenti:

* Importazione delle impostazioni di pubblicazione.
* Creazione e gestione di Siti Web di Azure.
* Creazione e gestione di Macchine virtuali di Azure.

Per un elenco completo dei comandi supportati, digitare `azure -help` nella riga di comando dopo l'installazione degli strumenti, oppure vedere la [documentazione di riferimento][reference-docs].

<h2><a id="Download">Come installare gli strumenti da riga di comando di Azure per Mac e Linux</a></h2>

L'elenco seguente fornisce informazioni per l'installazione degli strumenti da riga di comando in base al sistema operativo in uso.

* **Mac**: scaricare il [programma di installazione di Azure SDK][mac-installer]. Aprire il file con estensione pkg scaricato e completare i passaggi dell'installazione seguendo le istruzioni visualizzate.
* **Linux**: installare la versione più recente di [Node.js][nodejs-org] (vedere [Installing Node.js via Package Manager][install-node-linux] - Installazione di Node.js tramite Gestione pacchetti), quindi eseguire questo comando:
  
        npm install azure-cli -g
  
    **Nota**: per l'esecuzione di questo comando potrebbero essere necessari privilegi elevati:
  
        sudo npm install azure-cli -g
* **Windows**: eseguire il file MSI di Windows Installer, disponibile negli [strumenti da riga di comando di Azure][windows-installer].

Per verificare l'installazione, digitare `azure` al prompt dei comandi. Se l'installazione è stata completata correttamente, verrà visualizzato un elenco di tutti i comandi di `azure` disponibili.

<h2><a id="CreateAccount"></a>Come creare un account Azure</h2>

Per usare gli strumenti da riga di comando di Azure per Mac e Linux, è necessario un account Azure.

Aprire un Web browser e passare a [http://www.windowsazure.com][windowsazuredotcom], quindi fare clic su **account gratuito** in alto a destra.

![Sito Web di Azure][Azure Web Site]

Seguire le istruzioni per la creazione di un account.

<h2><a id="Account"></a>Come scaricare e importare impostazioni di pubblicazione</h2>

Per iniziare, è necessario innanzitutto scaricare e importare le impostazioni di pubblicazione. Questo consentirà di usare gli strumenti per creare e gestire servizi di Azure. Per scaricare le impostazioni di pubblicazione, usare il comando `account download` :

    azure account download

Verrà aperto il browser predefinito e richiesto di accedere al portale di gestione. Dopo l'accesso, verrà scaricato il file `.publishsettings` . Prendere nota del percorso in cui il file viene salvato.

Importare quindi il file `.publishsettings` eseguendo il comando seguente e sostituendo `{path to .publishsettings file}` con il percorso del proprio file `.publishsettings`:

    azure account import {path to .publishsettings file}

È possibile rimuovere tutte le informazioni archiviate dal comando <code>import</code> usando il comando <code>account clear</code>:

    azure account clear

Per visualizzare un elenco di opzioni per i comandi `account`, usare l'opzione `-help`:

    azure account -help

Dopo l'importazione delle impostazioni di pubblicazione, è opportuno eliminare il file `.publishsettings` per motivi di sicurezza.

> [!NOTE]
> Quando si importano le impostazioni di pubblicazione, le credenziali per l'accesso alla sottoscrizione di Azure vengono archiviate all'interno della cartella `user`. La cartella `user` è protetta dal sistema operativo. È tuttavia consigliabile adottare precauzioni aggiuntive per crittografare la propria cartella `user` . A tale scopo, è possibile procedere come segue:    
> 
> * In Windows, modificare le proprietà della cartella o usare BitLocker.
> * In Mac, abilitare FileVault per la cartella.
> * In Ubuntu usare la funzionalità per la crittografia della cartella Home. Altre distribuzioni di Linux offrono funzionalità equivalenti.
> 
> 

È ora possibile iniziare a creare e gestire Siti Web di Azure e Macchine virtuali di Azure.  

<h2><a id="WebSites"></a>Come creare e gestire un sito Web di Azure</h2>

### <a name="create-a-website"></a>Creare un sito Web
Per creare un sito Web di Azure, creare innanzitutto una directory vuota denominata `MySite` e passarvi all'interno.

Quindi, eseguire il comando seguente:

    azure site create MySite --git

L'output di questo comando conterrà l'URL predefinito per il sito Web appena creato. L'opzione `--git` consente di usare Git per eseguire la pubblicazione nel sito Web creando archivi Git sia nella directory delle applicazioni locale che nel data center del sito Web. Si noti che qualora la cartella locale sia già un repository Git, il comando aggiungerà a quello esistente un nuovo repository remoto che punterà al repository del data center del sito Web.

Si noti che è possibile eseguire il comando `azure site create` con una qualsiasi delle opzioni seguenti:

* `--location [location name]`. Questa opzione consente di specificare il percorso del data center in cui viene creato il sito Web, ad esempio "Stati Uniti occidentali". Se si omette questa opzione, verrà richiesto di scegliere un percorso.
* `--hostname [custom host name]`. Questa opzione consente di specificare un nome host personalizzato per il sito Web.

È quindi possibile aggiungere contenuto alla directory del sito Web. Usare la normale sequenza Git (`git add`, `git commit`) per eseguire il commit del contenuto. Usare il comando Git seguente per eseguire il push del contenuto del sito Web ad Azure: 

    git push azure master

### <a name="set-up-publishing-from-github"></a>Configurare la pubblicazione da GitHub
Per configurare la pubblicazione continua da un archivio GitHub, usare l'opzione `--GitHub` quando si crea un sito:

    auzre site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

Se si dispone di un clone locale di un archivio GitHub o di un archivio con un unico riferimento remoto a un archivio GitHub, questo comando pubblicherà automaticamente il codice nell'archivio GitHub sul sito. Da quel momento in poi, qualsiasi modifica di cui si effettua il push nell'archivio GitHub verrà pubblicata automaticamente nel sito.

Quando si configura la pubblicazione da GitHub, viene usato il branch master come branch predefinito. Per specificare un branch diverso, eseguire il comando seguente dall'archivio locale:

    azure site repository <branch name>

### <a name="configure-app-settings"></a>Configurare le impostazioni applicazione
Le impostazioni applicazione sono coppie chiave-valore disponibili per l'applicazione in fase di esecuzione. Quando impostati per un sito Web di Azure, i valori delle impostazioni applicazione eseguiranno l'override delle impostazioni con la stessa chiave definite nel file Web.config del sito. Per applicazioni in Node.js e PHP, le impostazioni applicazione sono disponibili come variabili di ambiente. Nell'esempio seguente viene illustrato come impostare una coppia chiave-valore:

    azure site config add <key>=<value> 

Per visualizzare un elenco di tutte le coppie chiave-valore, usare il comando seguente:

    azure site config list 

Se invece si conosce la chiave e si desidera visualizzarne il valore, usare:

    azure site config get <key> 

Se si desidera modificare il valore di una chiave esistente è innanzitutto necessario cancellare la chiave esistente e quindi aggiungerla di nuovo. Il comando per questa operazione è:

    azure site config clear <key> 

### <a name="list-and-show-sites"></a>Elencare e visualizzare siti
Per elencare i siti Web, usare il comando seguente:

    azure site list

Per ottenere informazioni dettagliate su un sito, usare il comando `site show` . Nell'esempio seguente vengono illustrati i dettagli per `MySite`:

    azure site show MySite

### <a name="stop-start-or-restart-a-site"></a>Arrestare, avviare o riavviare un sito
È possibile arrestare, avviare o riavviare un sito con i comandi `site stop`, `site start` o `site restart`:

    azure site stop MySite
    azure site start MySite
    azure site restart MySite

### <a name="delete-a-site"></a>Eliminare un sito
È infine possibile eliminare un sito usando il comando `site delete`:

    azure site delete MySite

Si noti che se si esegue uno dei comandi precedenti dall'interno della cartella in cui si è eseguito il comando `site create`, non è necessario specificare il nome del sito `MySite` come ultimo parametro.

Per visualizzare un elenco completo di comandi `site`, usare l'opzione `-help`:

    azure site -help 

<h2><a id="VMs"></a>Come creare e gestire una macchina virtuale di Azure</h2>

Una macchina virtuale di Azure viene creata da un'immagine della macchina virtuale (file .vhd) fornita dall'utente o disponibile nella raccolta immagini. Per visualizzare le immagini disponibili, usare il comando `vm image list` :

    azure vm image list

È possibile eseguire il provisioning di una macchina virtuale e avviarla da una delle immagini disponibili mediante il comando `vm create` . L'esempio seguente illustra come creare una macchina virtuale Linux (denominata `myVM`) da un'immagine nella raccolta immagini (CentOS 6.2). Il nome utente e la password dell'utente ROOT per la macchina virtuale sono rispettivamente `myusername` e `Mypassw0rd`. Si noti che il parametro `--location` specifica il data center in cui viene creata la macchina virtuale. Se si omette il parametro `--location` , verrà richiesto di scegliere un percorso.

    azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd myusername --location "West US"

È possibile passare il flag `--ssh` (Linux) o `--rdp` (Windows) a `vm create` per abilitare le connessioni remote alla macchina virtuale appena creata.

Se si preferisce eseguire il provisioning di una macchina virtuale da un'immagine personalizzata, è possibile creare un'immagine da un file VHD con il comando `vm image create`, quindi usare il comando `vm create` per eseguire il provisioning della macchina virtuale. L'esempio seguente illustra come creare un'immagine Linux denominata `myImage`da un file VHD locale. Il parametro `--location` specifica i dati in cui è archiviata l'immagine.

    azure vm image create myImage /path/to/myImage.vhd --os linux --location "West US"

Anziché creare un'immagine da un file .vhd locale, è possibile crearla da un file .vhd archiviato nell'archivio BLOB di Azure. A tale scopo, usare il parametro `blob-url` :

    azure vm image create myImage --blob-url <url to .vhd in Blob Storage> --os linux

Dopo avere creato un'immagine, è possibile eseguire il provisioning di una macchina virtuale da tale immagine usando `vm create`. Il comando seguente consente di creare una macchina virtuale denominata `myVM` dall'immagine creata in precedenza (`myImage`).

    azure vm create myVM myImage myusername --location "West US"

Dopo avere eseguito il provisioning di una macchina virtuale, è possibile creare degli endpoint, ad esempio per consentire l'accesso remoto alla macchina virtuale. L'esempio seguente usa il comando `vm create endpoint` per aprire la porta esterna 22 e la porta locale 22 in `myVM`:

    azure vm endpoint create myVM 22 22

È possibile ottenere informazioni dettagliate su una macchina virtuale, ad esempio indirizzo IP, nome DNS e informazioni sugli endpoint, usando il comando `vm show`:

    azure vm show myVM

Per arrestare, avviare o riavviare la macchina virtuale, usare uno dei comandi seguenti:

    azure vm shutdown myVM
    azure vm start myVM
    azure vm restart myVM

Infine, per eliminare la macchina virtuale, è possibile usare il comando `vm delete` :

    azure vm delete myVM

Per un elenco completo dei comandi per creare e gestire macchine virtuali, usare l'opzione `-h` :

    azure vm -h

<!-- IMAGES -->
[Azure Web Site]: ./media/crossplat-cmd-tools/freetrial.png

<!-- LINKS -->
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/?LinkID=275464
[reference-docs]: http://go.microsoft.com/fwlink/?LinkId=252246
[windowsazuredotcom]: http://www.windowsazure.com


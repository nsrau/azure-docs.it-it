# Come utilizzare gli strumenti da riga di comando di Azure per Mac e Linux

In questa guida viene descritto come utilizzare gli strumenti da riga di
comando di Azure per Mac e Linux per creare e gestire servizi in Azure.
Gli scenari presentati includono **installazione degli strumenti**,
**importazione delle impostazioni di pubblicazione**, **creazione e
gestione di Siti Web di Azure** e **creazione e gestione di Macchine
virtuali di Azure**. Per la documentazione di riferimento completa,
vedere [Strumento da riga di comando di Azure per Mac e Linux][1].
## Sommario

* [Che cosa sono gli strumenti da riga di comando di Azure per Mac e
  Linux](#Overview)
* [Come installare gli strumenti da riga di comando di Azure per Mac e
  Linux](#Download)
* [Come creare un account Azure](#CreateAccount)
* [Come scaricare e importare impostazioni di pubblicazione](#Account)
* [Come creare e gestire un sito Web di Azure](#WebSites)
* [Come creare e gestire una macchina virtuale di Azure](#VMs)

<h2><a  id="Overview" ></a>Che cosa sono gli strumenti da riga di comando di Azure per Mac e Linux</h2>


Gli strumenti da riga di comando di Azure per Mac e Linux sono un set di
strumenti da riga di comando per la distribuzione e la gestione di
servizi di Azure.

Sono supportate le attività seguenti:

* Importazione delle impostazioni di pubblicazione.
* Creazione e gestione di Siti Web di Azure.
* Creazione e gestione di Macchine virtuali di Azure.

Per un elenco completo dei comandi supportati, digitare `azure -help`
nella riga di comando dopo l'installazione degli strumenti, oppure
vedere la [documentazione di riferimento][1].

<h2><a  id="Download">Come installare gli strumenti da riga di comando di Azure per Mac e Linux</a></h2>


Nell'elenco seguente vengono fornite informazioni per l'installazione
degli strumenti da riga di comando in base al sistema operativo in uso.

* **Mac**: scaricare il [programma di installazione di Azure SDK][2].
  Aprire il file .pkg scaricato e completare i passaggi
  dell'installazione seguendo le istruzioni visualizzate.

* **Linux**: installare la versione più recente di [Node.js][3] (vedere
  [Install Node.js via Package Manager][4]), quindi eseguire il comando
  seguente:
  
        npm install azure-cli -g
  
  **Nota**: per l'esecuzione di questo comando potrebbero essere
  necessari privilegi elevati:
  
        sudo npm install azure-cli -g

* **Windows**: eseguire il file .msi di Windows Installer, disponibile
  qui: [Strumenti da riga di comando di Azure][5].

Per controllare l'installazione, digitare `azure` al prompt dei
comandi. Se l'installazione è stata completata correttamente, verrà
visualizzato un elenco di tutti i comandi di `azure` disponibili.

<h2><a  id="CreateAccount" ></a>Come creare un account Azure</h2>


Per utilizzare gli strumenti da riga di comando di Azure per Mac e
Linux, è necessario un account Azure.

Aprire un Web browser e passare a [http://www.windowsazure.com][6]
quindi fare clic su **Versione di valutazione gratuita** in alto a
destra.

![Sito Web di Azure](./media/crossplat-cmd-tools/freetrial.png)

Seguire le istruzioni per la creazione di un account.

<h2><a  id="Account" ></a>Come scaricare e importare impostazioni di pubblicazione</h2>


Per iniziare, è necessario innanzitutto scaricare e importare le
impostazioni di pubblicazione. Questo consentirà di utilizzare gli
strumenti per creare e gestire servizi di Azure. Per scaricare le
impostazioni di pubblicazione, utilizzare il comando `account
download`:

    azure account download

Verrà aperto il browser predefinito e richiesto di accedere al portale
di gestione. Dopo l'accesso, verrà scaricato il file
`.publishsettings`. Prendere nota del percorso in cui il file viene
salvato.

Importare quindi il file `.publishsettings` eseguendo il comando
seguente, sostituendo prima `{path to .publishsettings file}` con il
percorso del proprio file `.publishsettings`:

    azure account import {path to .publishsettings file}

È possibile rimuovere tutte le informazioni archiviate dal comando
`import` utilizzando il comando `account clear`:

    azure account clear

Per visualizzare un elenco di opzioni per i comandi `account`,
utilizzare l'opzione `-help`:

    azure account -help

Dopo l'importazione delle impostazioni di pubblicazione, è opportuno
eliminare il file `.publishsettings` per motivi di sicurezza.

 
<div  class="dev-callout"> 
<b>Nota</b> 
<p>Quando si importano le impostazioni di pubblicazione, le credenziali per accedere alla propria sottoscrizione Azure sono archiviate all'interno della cartella <code>user</code> dell'utente. La cartella <code>user</code> è protetta dal sistema operativo. È tuttavia consigliabile adottare precauzioni aggiuntive per crittografare la propria cartella <code>user</code>. A tale scopo, è possibile procedere come segue:</p>

<ul>
<li>In Windows, modificare le proprietà della cartella o utilizzare BitLocker.</li>
<li>In Mac, abilitare FileVault per la cartella.</li>
<li>In Ubuntu, utilizzare la funzionalità per la crittografia della cartella Home. Altre distribuzioni di Linux offrono funzionalità equivalenti.</li>
</ul>

</div>

 È ora possibile iniziare a creare e gestire Siti Web di Azure e Macchine
virtuali di Azure.

<h2><a  id="WebSites" ></a>Come creare e gestire un sito Web di Azure</h2>
### Creare un sito Web

Per creare un sito Web di Azure, creare innanzitutto una directory vuota
denominata `MySite` e passarvi all'interno.

Quindi, eseguire il comando seguente:

    azure site create MySite --git

L'output di questo comando conterrà l'URL predefinito per il sito Web
appena creato. L'opzione `--git` consente di utilizzare Git per
pubblicare nel sito Web creando archivi Git sia nella directory delle
applicazioni locale che nel data center del sito Web. Si noti che
qualora la cartella locale sia già un archivio Git, il comando
aggiungerà a quello esistente un nuovo archivio remoto che punterà
all'archivio del data center del sito Web.

È possibile eseguire il comando `azure site create` con qualsiasi delle
seguenti opzioni:

* `--location [location name]`. Questa opzione consente di specificare
  il percorso del data center in cui viene creato il sito Web, ad
  esempio "Stati Uniti occidentali". Se si omette questa opzione, verrà
  richiesto di scegliere un percorso.
* `--hostname [custom host name]`. Questa opzione consente di
  specificare un nome host personalizzato per il sito Web.

È ora possibile aggiungere contenuto alla directory del sito Web.
Utilizzare la normale sequenza Git (`git add`, `git commit`) per
eseguire il commit del contenuto. Utilizzare il seguente comando Git per
eseguire il push del contenuto del sito Web ad Azure:

    git push azure master
### Configurare la pubblicazione da GitHub

Per configurare la pubblicazione continua da un archivio GitHub,
utilizzare l'opzione `--GitHub` quando si crea un sito:

    auzre site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

Se si dispone di un clone locale di un archivio GitHub o di un archivio
con un unico riferimento remoto a un archivio GitHub, questo comando
pubblicherà automaticamente il codice nell'archivio GitHub sul sito. Da
quel momento in poi, qualsiasi modifica di cui si effettua il push
nell'archivio GitHub verrà pubblicata automaticamente nel sito.

Quando si configura la pubblicazione da GitHub, viene utilizzato il
branch master come branch predefinito. Per specificare un branch
diverso, eseguire il comando seguente dall'archivio locale:

    azure site repository <branch  name>
### Configurare le impostazioni applicazione

Le impostazioni applicazione sono coppie chiave-valore disponibili per
l'applicazione in fase di esecuzione. Quando impostati per un sito Web
di Azure, i valori delle impostazioni applicazione eseguiranno
l'override delle impostazioni con la stessa chiave definite nel file
Web.config del sito. Per applicazioni in Node.js e PHP, le impostazioni
applicazione sono disponibili come variabili di ambiente. Nell'esempio
seguente viene illustrato come impostare una coppia chiave-valore:

    azure site config add <key>=<value> 

Per visualizzare un elenco di tutte le coppie chiave-valore, utilizzare
il comando seguente:

    azure site config list 

Se invece si conosce la chiave e si desidera visualizzarne il valore,
utilizzare:

    azure site config get <key> 

Se si desidera modificare il valore di una chiave esistente è
innanzitutto necessario cancellare la chiave esistente e quindi
aggiungerla di nuovo. Il comando per questa operazione è:

    azure site config clear <key> 
### Elencare e visualizzare siti

Per elencare i siti Web utilizzare il comando seguente:

    azure site list

Per ottenere informazioni dettagliate su un sito, utilizzare il comando
`site show`. Nell'esempio seguente vengono illustrati i dettagli per
`MySite`:

    azure site show MySite
### Arrestare, avviare o riavviare un sito

È possibile arrestare, avviare o riavviare un sito con i comandi `site
stop`, `site start` o `site restar`t:

    azure site stop MySite
    azure site start MySite
    azure site restart MySite
### Eliminare un sito

È infine possibile eliminare un sito utilizzando il comando `site
delete`:

    azure site delete MySite

Si noti che se si esegue uno dei comandi sopra riportati dall'interno
della cartella in cui si è eseguito `site create`, non è necessario
specificare il nome del sito `MySite` come ultimo parametro.

Per visualizzare un elenco completo di comandi `site`, utilizzare
l'opzione `-help`:

    azure site -help 

<h2><a  id="VMs" ></a>Come creare e gestire una macchina virtuale di Azure</h2>


Una macchina virtuale di Azure viene creata da un'immagine della
macchina virtuale (file .vhd) fornita dall'utente o disponibile nella
raccolta immagini. Per visualizzare le immagini disponibili, utilizzare
il comando `vm image list`:

    azure vm image list

È possibile eseguire il provisioning di una macchina virtuale e avviarla
da una delle immagini disponibili mediante il comando `vm create`.
Nell'esempio seguente viene illustrato come creare una macchina
virtuale Linux (denominata `myVM`) da un'immagine nella raccolta
immagini (CentOS 6.2). Il nome utente e la password dell'utente ROOT
per la macchina virtuale sono rispettivamente `myusername` e
`Mypassw0rd`. Si noti che il parametro `--location` specifica il data
center in cui viene creata la macchina virtuale. Se si omette il
parametro `--location`, verrà richiesto di scegliere un percorso.

    azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd myusername --location "West US"

È possibile passare il flag `--ssh` (Linux) o `--rdp` (Windows) a `vm
create` per abilitare le connessioni remote alla macchina virtuale
appena creata.

Se si preferisce eseguire il provisioning di una macchina virtuale da
un'immagine personalizzata, è possibile creare un'immagine da un file
.vhd con il comando `vm image create`, quindi utilizzare il comando `vm
create` per eseguire il provisioning della macchina virtuale.
Nell'esempio seguente viene illustrato come creare un'immagine Linux
denominata `myImage` da un file .vhd locale. Il parametro `--location`
specifica i dati in cui è archiviata l'immagine.

    azure vm image create myImage /path/to/myImage.vhd --os linux --location "West US"

Anziché creare un'immagine da un file .vhd locale, è possibile crearla
da un file .vhd archiviato nell'archiviazione BLOB di Azure. A tale
scopo, utilizzare il parametro `blob-url`:

    azure vm image create myImage --blob-url <url  to .vhd in Blob Storage> --os linux

Dopo avere creato un'immagine, è possibile eseguire il provisioning di
una macchina virtuale da tale immagine utilizzando `vm create`. Il
comando seguente consente di creare una macchina virtuale denominata
`myVM` dall'immagine creata in precedenza (`myImage`).

    azure vm create myVM myImage myusername --location "West US"

Dopo avere eseguito il provisioning di una macchina virtuale, è
possibile creare degli endpoint, ad esempio per consentire l'accesso
remoto alla macchina virtuale. Nell'esempio seguente viene utilizzato
il comando `vm create endpoint` per aprire la porta esterna 22 e la
porta locale 22 in `myVM`:

    azure vm endpoint create myVM 22 22

È possibile ottenere informazioni dettagliate su una macchina virtuale
(quali indirizzo IP, nome DNS e informazioni sugli endpoint) utilizzando
il comando `vm show`:

    azure vm show myVM

Per arrestare, avviare o riavviare la macchina virtuale, utilizzare uno
dei comandi seguenti:

    azure vm shutdown myVM
    azure vm start myVM
    azure vm restart myVM

Infine, per eliminare la macchina virtuale, è possibile utilizzare il
comando `vm delete`:

    azure vm delete myVM

Per un elenco completo dei comandi per creare e gestire macchine
virtuali, utilizzare l'opzione `-h`:

    azure vm -h

<!-- IMAGES -->

<!-- LINKS -->



[1]: http://go.microsoft.com/fwlink/?LinkId=252246
[2]: http://go.microsoft.com/fwlink/?LinkId=252249
[3]: http://nodejs.org/
[4]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[5]: http://go.microsoft.com/fwlink/?LinkID=275464
[6]: http://www.windowsazure.com

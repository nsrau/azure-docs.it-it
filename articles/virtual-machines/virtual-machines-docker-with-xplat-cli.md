<properties
	pageTitle="Uso dell'estensione della VM Docker per Linux in Azure"
	description="Descrive Docker e le estensioni di Macchine virtuali di Azure e mostra come creare a livello di codice le macchine virtuali in Azure che siano host Docker dalla riga di comando usando l'interfaccia della riga di comando di Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="05/22/2015"
	ms.author="rasquill"/>

# Uso dell’estensione della VM Docker dall’interfaccia della riga di comando di Azure (Azure CLI)

Questo argomento descrive come creare una macchina virtuale con l'estensione della VM Docker dalla modalità di gestione servizi (asm) nell’interfaccia della riga di comando di Azure su qualsiasi piattaforma. [Docker](https://www.docker.com/) è uno dei più popolari approcci alla virtualizzazione che usa [contenitori Linux](http://en.wikipedia.org/wiki/LXC) invece di macchine virtuali allo scopo di isolare i dati ed eseguire i calcoli su risorse condivise. È possibile usare l'estensione della macchina virtuale Docker per l'[Agente Linux di Azure](virtual-machines-linux-agent-user-guide.md) per creare una VM Docker che ospiti un numero qualsiasi di contenitori per le applicazioni su Azure. Per assistere a una discussione di alto livello sui contenitori e i relativi vantaggi, guardare questa [sessione con lavagna condivisa relativa a Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

+ [Come usare l'estensione della VM Docker con Azure]
+ [Estensioni della macchina virtuale per Linux e Windows]
+ [Contenitore e risorse di gestione del contenitore per Azure]
+ [Passaggi successivi]

## <a id='How to use the Docker VM Extension with Azure'>Come usare l’estensione della VM Docker con Azure</a>
Per usare l'estensione della VM Docker con Azure è necessario installare una versione dell'[interfaccia della riga di comando di Azure](https://github.com/Azure/azure-sdk-tools-xplat) successiva alla versione 0.8.6 (al momento della stesura di questo articolo, la versione corrente è la 0.8.10). È possibile installare l’interfaccia della riga di comando di Azure su Mac, Linux e Windows.



Il processo completo di utilizzo di Docker su Azure è semplice:

+ Installare l’interfaccia della riga di comando di Azure e le relative dipendenze sul computer dal quale si intende controllare Azure (su Windows, si tratterà di una distribuzione Linux in esecuzione come macchina virtuale)
+ Usare i comandi di Docker per l’interfaccia della riga di comando di Azure per creare un host Docker della VM in Azure
+ Usare i comandi locali di Docker per gestire i contenitori Docker nella VM Docker in Azure.


### Installare l'interfaccia della riga di comando di Azure

Per installare e configurare l’interfaccia della riga di comando di Azure, vedere [Come installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md). Per confermare l'installazione, digitare `azure` nel prompt dei comandi. Dopo pochi secondi verrà visualizzata la grafica ASCII dell’interfaccia della riga di comando di Azure, in cui sono elencati i comandi di base disponibili. Se l'installazione è andata a buon fine, sarà possibile digitare `azure help vm` e verificare che uno dei comandi elencati corrisponda a "docker".

> [AZURE.NOTE]Docker dispone di un programma di installazione per Windows, [Boot2Docker](https://docs.docker.com/installation/windows/), che è possibile utilizzare per automatizzare la creazione di un client docker da usare per lavorare con macchine virtuali di Azure come host Docker.

### Connettere l'interfaccia della riga di comando di Azure al proprio account Azure
Prima di poter usare l'interfaccia della riga di comando di Azure è necessario associare alla stessa le credenziali del proprio account Azure sulla propria piattaforma. La sezione [Come connettersi alla sottoscrizione di Azure](../xplat-cli-connect.md) spiega come scaricare e importare il file **.publishsettings** o associare l’interfaccia della riga di comando di Azure a un ID organizzazione.

> [AZURE.NOTE]Ci sono alcune differenze di comportamento quando si usa l'uno o l'altro metodo di autenticazione, perciò assicurarsi di leggere il documento sopra riportato per comprendere le diverse funzionalità.

### Installare Docker e usare l'estensione della VM Docker per Azure
Per installare Docker in locale sul proprio computer seguire le [istruzioni di installazione di Docker](https://docs.docker.com/installation/#installation).

Per usare Docker con una macchina virtuale di Azure, l'immagine Linux usata per la VM deve avere installato l'[agente VM Linux Azure](virtual-machines-linux-agent-user-guide.md). Al momento esistono solo due tipi di immagine che offrono queste funzionalità:

+ un'immagine Ubuntu dalla raccolta immagini di Azure o

+ un'immagine Linux personalizzata creata con l'agente VM Linux Azure installato e configurato. Per altre informazioni su come costruire una VM Linux con l'agente VM di Azure, vedere [Guida dell'utente dell'agente Linux di Azure](virtual-machines-linux-agent-user-guide.md).

### Uso della raccolta immagini di Azure

Da una sessione Bash o Terminal, usare il seguente comando dell’interfaccia della riga di comando di Azure per trovare l'immagine più recente di Ubuntu nella raccolta di VM da usare digitando

`azure vm image list | grep Ubuntu-14_04`

e selezionare uno dei nomi di immagine, ad esempio `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-it-it-30GB`, quindi usare il comando seguente per creare una nuova macchina virtuale usando tale immagine.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-it-it-30GB" <username> <password>
```

dove:

+ *&lt;vm-nome serviziocloud&gt;* è il nome della macchina virtuale che diventerà il computer host del contenitore Docker in Azure

+  *&lt;nomeutete&gt;* è il nome utente dell’utente root predefinito della macchina virtuale

+ *&lt;password&gt;* è la password dell’account *nomeutente* che soddisfa gli standard di complessità per Azure

> [AZURE.NOTE]Attualmente, una password deve contenere almeno 8 caratteri, un carattere minuscolo e uno maiuscolo, un numero e un carattere speciale, ad esempio uno dei seguenti: `!@#$%^&+=` Il punto alla fine della frase precedente NON è un carattere speciale.

Se il comando ha avuto esito positivo, verrà visualizzato un output simile al seguente, a seconda degli argomenti e delle opzioni precisi usati:

![](./media/virtual-machines-docker-with-xplat-cli/dockercreateresults.png)

> [AZURE.NOTE]La creazione di una macchina virtuale può richiedere alcuni minuti, ma dopo che ne è stato effettuato il provisioning viene avviato il daemon Docker ed è possibile connettersi all'host contenitore Docker.

Per testare la VM Docker creata in Azure, digitare

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

dove *<vm-name-you-used>* è il nome della macchina virtuale usata nella chiamata a `azure vm docker create`. Viene visualizzato codice simile al seguente, che indica che la VM host di Docker è in fase di elaborazione in Azure ed è in attesa dei comandi dell'utente.

![](./media/virtual-machines-docker-with-xplat-cli/connectingtodockerhost.png)

### Autenticazione della VM host di Docker
Oltre a creare la VM di Docker, il comando `azure vm docker create` crea automaticamente anche i certificati necessari che consentono al computer client Docker di connettersi all'host contenitore di Azure con il protocollo HTTPS. I certificati saranno archiviati sulle macchine client e host, secondo le esigenze. Nelle esecuzioni successive, i certificati esistenti verranno riutilizzati e condivisi con il nuovo host.

Per impostazione predefinita, i certificati vengono inseriti in `~/.docker` e Docker verrà configurato per l'esecuzione sulla porta **2376**. Per scegliere una porta o una directory differente, usare una delle seguenti opzioni della riga di comando `azure vm docker create` per configurare la VM host del contenitore Docker in modo da usare una porta differente o certificati differenti per connettere i client:

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

Il daemon Docker sull'host è configurato per restare in ascolto delle connessioni client e autenticarle sulla porta specificata usando i certificati generati dal comando `azure vm docker create`. La macchina client deve avere questi certificati per poter accedere all'host Docker.

> [AZURE.NOTE]Un host di rete in esecuzione senza questi certificati sarà vulnerabile a chiunque possa connettersi alla macchina. Prima di modificare la configurazione predefinita, assicurarsi di aver compreso i rischi a cui si sottopongono i computer e le applicazioni.




## Passaggi successivi

È ora possibile passare alla [guida dell'utente di Docker] e usare la VM Docker. Per creare una VM abilitata per Docker incorporata nel nuovo portale, vedere l'articolo su [come usare l'estensione della VM Docker con il portale].

<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Passaggi successivi]: #next-steps

[Come usare l'estensione della VM Docker con Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Estensioni della macchina virtuale per Linux e Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
[Contenitore e risorse di gestione del contenitore per Azure]: #Container-and-Container-Management-Resources-for-Azure

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
[come usare l'estensione della VM Docker con il portale]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[guida dell'utente di Docker]: https://docs.docker.com/userguide/
 

<!---HONumber=August15_HO6-->
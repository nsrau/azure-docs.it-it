<properties
	pageTitle="Installare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows"
	description="Installare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows per iniziare a usare i servizi di Azure"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/02/2015"
	ms.author="danlep"/>

# Installare l'interfaccia della riga di comando di Azure

In questo documento viene descritto come installare l'interfaccia della riga di comando di Azure. L'interfaccia della riga di comando di Azure fornisce un set di comandi open source basati sulla shell per la gestione delle risorse in Microsoft Azure.

> [AZURE.NOTE]Se è già stata installata l'interfaccia della riga di comando di Azure, connettersi con le risorse di Azure. Per ulteriori informazioni, vedere [Come connettersi alla sottoscrizione di Azure](xplat-cli-connect.md#configure).

L'interfaccia della riga di comando di Azure è scritta in JavaScript e richiede [Node.js](https://nodejs.org). Viene implementata mediante [Azure SDK per Node](https://github.com/azure/azure-sdk-for-node) e rilasciata con una licenza Apache 2.0. L'archivio di progetto si trova in [https://github.com/azure/azure-xplat-cli](https://github.com/azure/azure-xplat-cli).

<a id="install"></a>
## Come installare l'interfaccia della riga di comando di Azure

Esistono vari modi per installare l'interfaccia della riga di comando di Azure.

1. Tramite un programma di installazione
2. Installando Node.js e npm e utilizzando quindi il comando **npm install**
3. Eseguire l'interfaccia della riga di comando di Azure come contenitore Docker

Dopo l'installazione dell'interfaccia della riga di comando di Azure, sarà possibile utilizzare il comando **azure** dall'interfaccia della riga di comando (Bash, terminale, prompt dei comandi) per accedere ai relativi comandi.

## Tramite un programma di installazione

Sono disponibili i pacchetti di programmi di installazione seguenti:

* [Windows installer][windows-installer]

* [Programma di installazione di OS X](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Programma di installazione di Linux][linux-installer]


## Installazione e utilizzo di Node.js e npm

Se Node.js è già installato nel sistema, usare il comando seguente per installare l'interfaccia della riga di comando di Azure:

	npm install azure-cli -g

> [AZURE.NOTE]Nelle distribuzioni di Linux, potrebbe essere necessario usare `sudo` per la corretta esecuzione del comando __npm__.

### Installazione di node.js e npm in distribuzioni di Linux che utilizzano la gestione dei pacchetti [dpkg](http://en.wikipedia.org/wiki/Dpkg)
La più comune di queste distribuzioni utilizza l'[Advanced Packaging Tool (apt)](http://en.wikipedia.org/wiki/Advanced_Packaging_Tool) o altri strumenti basati sul formato di pacchetto `.deb`. Alcuni esempi sono Ubuntu e Debian.

La maggior parte delle più recenti di queste distribuzioni richiede l'installazione di **nodejs-legacy** per ottenere uno strumento **npm** correttamente configurato per installare l'interfaccia della riga di comando di Azure. Il codice seguente illustra i comandi che consentono di installare correttamente **npm** in Ubuntu 14.04.

	sudo apt-get install nodejs-legacy
	sudo apt-get install npm
	sudo npm install -g azure-cli

Alcune delle distribuzioni precedenti, ad esempio Ubuntu 12.04, richiedono l'installazione della distribuzione corrente binaria di Node.js. Il codice seguente mostra come eseguire questa operazione mediante l'installazione e l'utilizzo di **curl**.

>[AZURE.NOTE]I comandi vengono ricavati dalle istruzioni di installazione di Joyent che si trovano [qui](https://github.com/joyent/node/wiki/installing-node.js-via-package-manager). Tuttavia, quando si utilizza **sudo** come destinazione pipe, è necessario controllare gli script che vengono installati per confermare che eseguano le operazioni previste prima di eseguirli attraverso **sudo**. Grandi potenzialità significano anche grande responsabilità.

	sudo apt-get install curl
	curl -sL https://deb.nodesource.com/setup | sudo bash -
	sudo apt-get install -y nodejs
	sudo npm install -g azure-cli

### Installazione di node.js e npm in distribuzioni di Linux che utilizzano la gestione dei pacchetti [RPM](http://en.wikipedia.org/wiki/RPM_Package_Manager)

L'installazione di node.js su distribuzioni basate su RPM richiede l'abilitazione dell'archivio EPEL. Il seguente codice mostra solo le procedure migliori per l'installazione di CentOS 7. Tenere presente che nella prima riga che segue il trattino (carattere '-') è importante.

	su -
	yum update [enter]
	yum upgrade –y [enter]
	yum install epel-release [enter]
	yum install nodejs [enter]
	yum install npm [enter]
	npm install -g azure-cli  [enter]

### Installazione di node.js e npm in Windows e Mac OS X

È possibile installare node.js e npm in Windows e OS X con i programmi di installazione da [Nodejs.org](https://nodejs.org/download/). Potrebbe essere necessario riavviare il computer per completare l'installazione. Controllare se node e npm sono stati installati correttamente aprendo il prompt dei comandi e digitando

	npm -v

Se viene visualizzata la versione dell'npm installato, è possibile andare avanti e installare l'interfaccia della riga di comando di Azure con

	npm install -g azure-cli

Dopo l'installazione dell'interfaccia della riga di comando di Azure, sarà possibile usare il comando **azure** dall'interfaccia della riga di comando per accedere ai relativi comandi. Al termine dell'installazione, verrà visualizzato un output simile al seguente:

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

>[AZURE.NOTE]Per i sistemi Linux, è inoltre possibile installare l'interfaccia della riga di comando di Azure generandola dal [codice sorgente](http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409). Per ulteriori informazioni sulla creazione da codice sorgente, vedere il file INSTALL incluso nell'archivio.

## Utilizzo del contenitore Docker

In un host Docker, eseguire: ```
	docker run -it microsoft/azure-cli
```

## Esecuzione dei comandi dell'interfaccia della riga di comando di Azure

Dopo l'installazione dell'interfaccia della riga di comando di Azure, sarà possibile utilizzare il comando **azure** dall'interfaccia della riga di comando (Bash, terminale, cmd.exe e così via) per accedere ai relativi comandi. Ad esempio per eseguire il comando help in Windows, aprire un prompt dei comandi (cmd.exe) con privilegi di amministratore: ```
	C:\> azure help
```

È ora possibile iniziare la distribuzione. Successivamente è possibile [connettersi alla sottoscrizione di Azure dall'interfaccia della riga di comando di Azure](xplat-cli-connect.md) e iniziare a utilizzare i comandi **azure**.


<a id="additional-resources"></a>
## Risorse aggiuntive

* [Utilizzo dell'interfaccia della riga di comando di Azure con i comandi di Gestione servizi (o modalità ASM)][cliasm]

* [Utilizzo dell'interfaccia della riga di comando di Azure con i comandi di Gestione risorse (o modalità ASM)][cliarm]

* Per informazioni sull'interfaccia della riga di comando di Azure, scaricare il codice sorgente, segnalare problemi o contribuire al progetto, visitare l'[archivio GitHub per l'interfaccia della riga di comando di Azure](https://github.com/azure/azure-xplat-cli).

* In caso di problemi durante l'uso dell'interfaccia della riga di comando di Azure o di Azure, visitare i [forum di Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).

* Per ulteriori informazioni su Azure, vedere [http://azure.microsoft.com/](http://azure.microsoft.com).




[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[linux-installer]: http://go.microsoft.com/fwlink/?linkid=253472
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=August15_HO6-->
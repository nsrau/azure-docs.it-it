<properties
	pageTitle="Installare l'interfaccia della riga di comando di Azure | Microsoft Azure"
	description="Installare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows per iniziare a usare i servizi di Azure"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/09/2016"
	ms.author="danlep"/>

# Installare l'interfaccia della riga di comando di Azure

Installare rapidamente l'interfaccia della riga di comando di Azure per usare un set di comandi open source basati sulla shell per creare e gestire le risorse in Microsoft Azure. Sono disponibili diverse opzioni. Usare uno dei pacchetti di installazione forniti per installare l'interfaccia della riga di comando di Azure nel sistema operativo, per installarla usando Node.js e **npm** o per installarla come contenitore in un host Docker. Per altre opzioni e informazioni, vedere l'archivio di progetto in [GitHub](https://github.com/azure/azure-xplat-cli).


Dopo l'installazione dell'interfaccia della riga di comando di Azure, sarà possibile [connetterla alla sottoscrizione di Azure](xplat-cli-connect.md) ed eseguire i comandi **azure** dall'interfaccia della riga di comando (Bash, terminale, prompt dei comandi e così via) per usare le risorse di Azure.




## Usare un programma di installazione

Sono disponibili i pacchetti di programmi di installazione seguenti:

* [Windows installer][windows-installer]

* [Programma di installazione di OS X](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Programma di installazione di Linux][linux-installer]


## Installare e utilizzare Node.js e npm

In alternativa, se Node.js è già installato nel sistema, usare il comando seguente per installare l'interfaccia della riga di comando di Azure:

	npm install azure-cli -g

> [AZURE.NOTE] Nelle distribuzioni di Linux, potrebbe essere necessario usare `sudo` per la corretta esecuzione del comando __npm__.

### Installare Node.js e npm in distribuzioni di Linux che usano la gestione dei pacchetti [dpkg](http://en.wikipedia.org/wiki/Dpkg)

La più comune di queste distribuzioni utilizza l'[Advanced Packaging Tool (apt)](http://en.wikipedia.org/wiki/Advanced_Packaging_Tool) o altri strumenti basati sul formato di pacchetto `.deb`. Alcuni esempi sono Ubuntu e Debian.

La maggior parte delle più recenti di queste distribuzioni richiede l'installazione di **nodejs-legacy** per ottenere uno strumento **npm** correttamente configurato per installare l'interfaccia della riga di comando di Azure. Il codice seguente illustra i comandi che consentono di installare correttamente **npm** in Ubuntu 14.04.

	sudo apt-get install nodejs-legacy
	sudo apt-get install npm
	sudo npm install -g azure-cli

Alcune delle distribuzioni precedenti, ad esempio Ubuntu 12.04, richiedono l'installazione della distribuzione binaria corrente di Node.js. Il codice seguente mostra come eseguire questa operazione mediante l'installazione e l'utilizzo di **curl**.

>[AZURE.NOTE] I comandi vengono ricavati dalle istruzioni di installazione disponibili [qui](https://github.com/joyent/node/wiki/installing-node.js-via-package-manager). Tuttavia, quando si utilizza **sudo** come destinazione pipe, è necessario controllare gli script che vengono installati per confermare che eseguano le operazioni previste prima di eseguirli attraverso **sudo**. Grandi potenzialità significano anche grande responsabilità.

	sudo apt-get install curl
	curl -sL https://deb.nodesource.com/setup | sudo bash -
	sudo apt-get install -y nodejs
	sudo npm install -g azure-cli

### Installare Node.js e npm in distribuzioni di Linux che usano la gestione dei pacchetti [rpm](http://en.wikipedia.org/wiki/RPM_Package_Manager)

L'installazione di Node.js in distribuzioni basate su RPM richiede l'abilitazione dell'archivio EPEL. Il seguente codice mostra solo le procedure migliori per l'installazione di CentOS 7. Tenere presente che nella prima riga che segue il trattino (carattere '-') è importante.

	su -
	yum update [enter]
	yum upgrade –y [enter]
	yum install epel-release [enter]
	yum install nodejs [enter]
	yum install npm [enter]
	npm install -g azure-cli  [enter]

### Installare Node.js e npm in Windows e Mac OS X

È possibile installare Node.js e npm in Windows e OS X con i programmi di installazione disponibili in [Nodejs.org](https://nodejs.org/en/download/). Potrebbe essere necessario riavviare il computer per completare l'installazione. Per controllare se Node e npm sono stati installati correttamente, aprire una finestra di comando o una finestra terminale e digitare

	npm -v

Se viene visualizzata la versione dell'npm installato, è possibile procedere con l'installazione dell'interfaccia della riga di comando di Azure con

	npm install -g azure-cli

Al termine dell'installazione, verrà visualizzato un output simile al seguente:

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

>[AZURE.NOTE] Per i sistemi Linux, è inoltre possibile installare l'interfaccia della riga di comando di Azure generandola dal [codice sorgente](http://go.microsoft.com/fwlink/?linkid=253472). Per ulteriori informazioni sulla creazione da codice sorgente, vedere il file INSTALL incluso nell'archivio.

## Usare un contenitore Docker

In un host Docker, eseguire:

```
docker run -it microsoft/azure-cli
```

## Eseguire i comandi dell'interfaccia della riga di comando di Azure
Dopo aver installato l'interfaccia della riga di comando di Azure, sarà possibile eseguire il comando **azure** dall'interfaccia utente della riga di comando (Bash, terminale, prompt dei comandi e così via). Ad esempio, per eseguire il comando della Guida, digitare quanto segue:

```
azure help
```

Per visualizzare la versione dell'interfaccia della riga di comando di Azure installata, digitare quanto segue:

```
azure --version
```

È ora possibile iniziare la distribuzione. Per accedere a tutti i comandi dell'interfaccia della riga di comando per usare le proprie risorse, [connettersi alla sottoscrizione di Azure dall'interfaccia della riga di comando di Azure](xplat-cli-connect.md).

## Aggiornare l'interfaccia della riga di comando

Microsoft rilascia di frequente versioni aggiornate dell'interfaccia della riga di comando di Azure. Reinstallare l'interfaccia della riga di comando con il programma di installazione del sistema operativo oppure, se Node.js e npm sono installati, eseguire l'aggiornamento digitando quanto riportato di seguito. Nelle distribuzioni Linux potrebbe essere necessario usare **sudo**.

```
npm update -g azure-cli
```

## Risorse aggiuntive

* [Comandi dell'interfaccia della riga di comando di Azure in modalità Azure Resource Manager (arm)][cliarm]

* [Comandi dell'interfaccia della riga di comando di Azure in modalità Gestione servizi di Azure (asm)][cliasm]

* Per altre informazioni sull'interfaccia della riga di comando di Azure, il download del codice sorgente, la segnalazione dei problemi o la collaborazione al progetto, visitare il [repository GitHub per tale interfaccia](https://github.com/azure/azure-xplat-cli).

* In caso di domande sull'uso di Azure o dell'interfaccia della riga di comando di Azure, visitare i [forum di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=azurescripting).



[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[linux-installer]: http://go.microsoft.com/fwlink/?linkid=253472
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/azure-cli-arm-commands.md

<!---HONumber=AcomDC_0323_2016-->
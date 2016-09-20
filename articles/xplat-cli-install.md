<properties
	pageTitle="Installare l'interfaccia della riga di comando di Azure | Microsoft Azure"
	description="Installare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows per iniziare a usare i servizi di Azure"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="virtual-machines-linux,virtual-network,storage,azure-resource-manager"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="danlep"/>
    
# Installare l'interfaccia della riga di comando di Azure

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [Interfaccia della riga di comando di Azure](xplat-cli-install.md)

Installare rapidamente l'interfaccia della riga di comando di Azure per usare un set di comandi open source basati sulla shell per creare e gestire le risorse in Microsoft Azure. È possibile scegliere tra diversi modi per installare la versione più recente:

* Eseguire l'installazione da un pacchetto npm (sono necessari Node.js e npm)
* Usare uno dei pacchetti di installazione forniti per i diversi sistemi operativi
* Installare l'interfaccia della riga di comando di Azure come contenitore in un host Docker
    
Per altre opzioni e informazioni, vedere il repository dei progetti in [GitHub](https://github.com/azure/azure-xplat-cli).

Dopo l'installazione dell'interfaccia della riga di comando di Azure, [connetterla alla sottoscrizione di Azure](xplat-cli-connect.md) ed eseguire i comandi **azure** dall'interfaccia della riga di comando (Bash, terminale, prompt dei comandi e così via) per usare le risorse di Azure.



## Opzione 1. Installare un pacchetto npm

Per installare l'interfaccia della riga di comando da un pacchetto npm, è necessario che nel sistema siano installati npm e la versione più recente di Node.js. Quindi, eseguire il comando seguente per installare il pacchetto dell'interfaccia della riga di comando di Azure. Nelle distribuzioni di Linux potrebbe essere necessario usare **sudo** per la corretta esecuzione del comando __npm__.

	npm install azure-cli -g

> [AZURE.NOTE]Se è necessario installare o aggiornare Node.js e npm per il sistema operativo, vedere la documentazione all'indirizzo [Nodejs.org](https://nodejs.org/en/download/package-manager/). È consigliabile installare la versione più recente di Node.js LTS (4.x). Se si usa una versione precedente, potrebbero verificarsi errori di installazione. Per altre informazioni su npm, vedere [npmjs.com](https://www.npmjs.com/).

## Opzione 2. Usare un programma di installazione

Sono disponibili anche i pacchetti di programmi di installazione dell'interfaccia della riga di comando seguenti:


* [Programma di installazione di Mac OS X][mac-installer]

* [Windows installer][windows-installer]

* [File tar per Linux][linux-installer] \(richiede Node.js e npm): installare eseguendo `sudo npm install -g <path to downloaded tar file>`


## Opzione 3. Usare un contenitore Docker

Se è stato configurato un host Docker, è possibile eseguire l'interfaccia della riga di comando di Azure in un contenitore Docker. Eseguire:

```
docker run -it microsoft/azure-cli
```

Per altre informazioni su Docker, vedere [docker.com](https://docs.docker.com/engine/understanding-docker/).

## Eseguire i comandi dell'interfaccia della riga di comando di Azure
Una volta installata l'interfaccia della riga di comando di Azure, eseguire il comando **azure** dall'interfaccia (Bash, terminale, prompt dei comandi e così via). Ad esempio, per eseguire il comando della Guida, digitare quanto segue:

```
azure help
```
> [AZURE.NOTE]In alcune distribuzioni di Linux potrebbe essere visualizzato un errore simile a `/usr/bin/env: ‘node’: No such file or directory`. Questo errore proviene dalle installazioni recenti di nodejs che vengono installate in /usr/bin/nodejs. Per correggerlo, creare un collegamento simbolico per /usr/bin/node eseguendo questo comando:

```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Per visualizzare la versione dell'interfaccia della riga di comando di Azure installata, digitare quanto segue:

```
azure --version
```

È ora possibile iniziare la distribuzione. Per accedere a tutti i comandi dell'interfaccia della riga di comando per usare le proprie risorse, [connettersi alla sottoscrizione di Azure dall'interfaccia della riga di comando di Azure](xplat-cli-connect.md).

>[AZURE.NOTE] Quando si usa per la prima volta l'interfaccia della riga di comando di Azure, viene visualizzato un messaggio che chiede se consentire a Microsoft di raccogliere informazioni sull'uso dell'interfaccia della riga di comando. La partecipazione è facoltativa. Se si sceglie di partecipare, è possibile interrompere in qualsiasi momento eseguendo `azure telemetry --disable`. Per abilitare la partecipazione in qualsiasi momento, eseguire `azure telemetry --enable`.


## Aggiornare l'interfaccia della riga di comando

Microsoft rilascia di frequente versioni aggiornate dell'interfaccia della riga di comando di Azure. Reinstallare l'interfaccia della riga di comando per il sistema operativo o eseguire il contenitore Docker più recente. Oppure, se sono installati npm e la versione più recente di Node.js, eseguire l'aggiornamento digitando quanto riportato di seguito. Nelle distribuzioni di Linux potrebbe essere necessario usare **sudo**.

```
npm update -g azure-cli
```

## Attivare il completamento con tasto TAB

Il completamento con tasto TAB dei comandi dell'interfaccia della riga di comando è supportato per Mac e Linux.

Per abilitarlo in zsh, eseguire:

```
echo '. <(azure --completion)' >> .zshrc
```

Per abilitarlo in bash, eseguire:

```
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## Passaggi successivi 

* [Connettersi a una sottoscrizione di Azure dall'interfaccia della riga di comando di Azure](xplat-cli-connect.md) per creare e gestire le risorse di Azure.

* Per altre informazioni sull'interfaccia della riga di comando di Azure, il download del codice sorgente, la segnalazione dei problemi o la collaborazione al progetto, visitare il [repository GitHub per tale interfaccia](https://github.com/azure/azure-xplat-cli).

* In caso di domande sull'uso di Azure o dell'interfaccia della riga di comando di Azure, visitare i [forum di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=azurescripting).

* Per i sistemi Linux, è inoltre possibile installare l'interfaccia della riga di comando di Azure generandola dal [codice sorgente](http://aka.ms/linux-azure-cli). Per altre informazioni sulla compilazione da codice sorgente, vedere il file INSTALL incluso nell'archivio di codici sorgente.

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md

<!---HONumber=AcomDC_0907_2016-->
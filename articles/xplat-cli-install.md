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
	ms.date="05/23/2016"
	ms.author="danlep"/>
    
# Installare l'interfaccia della riga di comando di Azure

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [Interfaccia della riga di comando di Azure](xplat-cli-install.md)

Installare rapidamente l'interfaccia della riga di comando di Azure per usare un set di comandi open source basati sulla shell per creare e gestire le risorse in Microsoft Azure. Sono disponibili diverse opzioni di installazione: usare un pacchetto npm (richiede Node.js e npm), usare uno dei pacchetti di installazione specificati per i vari sistemi operativi oppure installare l'interfaccia della riga di comando di Azure come contenitore in un host Docker. Per altre opzioni e informazioni, vedere il repository dei progetti in [GitHub](https://github.com/azure/azure-xplat-cli).


Dopo l'installazione dell'interfaccia della riga di comando di Azure, sarà possibile [connetterla alla sottoscrizione di Azure](xplat-cli-connect.md) ed eseguire i comandi **azure** dall'interfaccia della riga di comando (Bash, terminale, prompt dei comandi e così via) per usare le risorse di Azure.



## Installare un pacchetto npm

Per installare l'interfaccia della riga di comando da un pacchetto npm è necessario che nel sistema siano installati npm e la versione più recente di Node.js. Quindi, eseguire il comando seguente per installare il pacchetto dell'interfaccia della riga di comando di Azure. Nelle distribuzioni di Linux potrebbe essere necessario usare **sudo** per la corretta esecuzione del comando __npm__.

	npm install azure-cli -g

> [AZURE.NOTE]Se è necessario installare o aggiornare Node.js e npm per il sistema operativo, vedere la documentazione all'indirizzo [Nodejs.org](https://nodejs.org/en/download/package-manager/). È consigliabile installare la versione più recente di Node.js LTS (4.x). Se si usa una versione precedente, potrebbero verificarsi errori di installazione.

## Usare un programma di installazione

Sono disponibili anche i pacchetti di programmi di installazione seguenti:


* [Programma di installazione di OS X][mac-installer]

* [Windows installer][windows-installer]

* [File tar per Linux][linux-installer] \(richiede Node.js e npm), installare eseguendo `sudo npm install -g <path to downloaded tar file>`


## Usare un contenitore Docker

In un host Docker, eseguire:

```
docker run -it microsoft/azure-cli
```

## Eseguire i comandi dell'interfaccia della riga di comando di Azure
Una volta installata l'interfaccia della riga di comando di Azure, sarà possibile eseguire il comando **azure** dall'interfaccia (Bash, terminale, prompt dei comandi e così via). Ad esempio, per eseguire il comando della Guida, digitare quanto segue:

```
azure help
```
> [AZURE.NOTE]In alcune distribuzioni di Linux è possibile che si verifichi un errore, /usr/bin/env: "nodo": Impossibile trovare il file o la directory. Questo errore deriva dalle installazioni recenti di nodejs in /usr/bin/nodejs. Per correggere l'errore creare un collegamento simbolico per /usr/bin/node eseguendo il comando seguente

```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Per visualizzare la versione dell'interfaccia della riga di comando di Azure installata, digitare quanto segue:

```
azure --version
```

È ora possibile iniziare la distribuzione. Per accedere a tutti i comandi dell'interfaccia della riga di comando per usare le proprie risorse, [connettersi alla sottoscrizione di Azure dall'interfaccia della riga di comando di Azure](xplat-cli-connect.md).

>[AZURE.NOTE] Quando si usa per la prima volta l'interfaccia della riga di comando di Azure versione 0.9.20 o successive, viene visualizzato un messaggio che chiede se consentire a Microsoft di raccogliere informazioni sull'uso dell'interfaccia della riga di comando. La partecipazione è facoltativa. Se si sceglie di partecipare, è possibile interrompere in qualsiasi momento eseguendo `azure telemetry --disable`. Per abilitare la partecipazione in qualsiasi momento, eseguire `azure telemetry --enable`.


## Aggiornare l'interfaccia della riga di comando

Microsoft rilascia di frequente versioni aggiornate dell'interfaccia della riga di comando di Azure. Reinstallare l'interfaccia della riga di comando con il programma di installazione del sistema operativo oppure, se sono installati npm e la versione più recente di Node.js, eseguire l'aggiornamento digitando quanto riportato di seguito. Nelle distribuzioni Linux potrebbe essere necessario usare **sudo**.

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

* [Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure](xplat-cli-connect.md) per creare e gestire le risorse di Azure.

* Per altre informazioni sull'interfaccia della riga di comando di Azure, il download del codice sorgente, la segnalazione dei problemi o la collaborazione al progetto, visitare il [repository GitHub per tale interfaccia](https://github.com/azure/azure-xplat-cli).

* In caso di domande sull'uso di Azure o dell'interfaccia della riga di comando di Azure, visitare i [forum di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=azurescripting).

* Per i sistemi Linux, è inoltre possibile installare l'interfaccia della riga di comando di Azure generandola dal [codice sorgente](http://aka.ms/linux-azure-cli). Per altre informazioni sulla compilazione da codice sorgente, vedere il file INSTALL incluso nell'archivio di codici sorgente.

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=windowsazurexplatcli&mode=new
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md

<!---HONumber=AcomDC_0525_2016-->
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
	ms.date="04/07/2016"
	ms.author="danlep"/>

# Installare l'interfaccia della riga di comando di Azure

Installare rapidamente l'interfaccia della riga di comando di Azure per usare un set di comandi open source basati sulla shell per creare e gestire le risorse in Microsoft Azure. Sono disponibili diverse opzioni di installazione: usare uno dei pacchetti di installazione forniti per i diversi sistemi operativi, eseguire l'installazione da un pacchetto npm o installare l'interfaccia della riga di comando di Azure come contenitore in un host Docker. Per altre opzioni e informazioni, vedere il repository del progetto in [GitHub](https://github.com/azure/azure-xplat-cli).


Dopo l'installazione dell'interfaccia della riga di comando di Azure, sarà possibile [connetterla alla sottoscrizione di Azure](xplat-cli-connect.md) ed eseguire i comandi **azure** dall'interfaccia della riga di comando (Bash, terminale, prompt dei comandi e così via) per usare le risorse di Azure.


## Usare un programma di installazione

Sono disponibili i pacchetti di programmi di installazione seguenti:

* [Windows installer][windows-installer]

* [Programma di installazione di OS X](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Programma di installazione di Linux][linux-installer]


## Installare un pacchetto npm

In alternativa, se le versioni più recenti di Node.js e npm sono già installate nel sistema, eseguire il comando seguente per installare il pacchetto dell'interfaccia della riga di comando di Azure. Nelle distribuzioni di Linux potrebbe essere necessario usare **sudo** per la corretta esecuzione del comando __npm__.

	npm install azure-cli -g

> [AZURE.NOTE]Se è necessario installare o aggiornare Node.js e npm per il sistema operativo, vedere la documentazione all'indirizzo [Nodejs.org](https://nodejs.org/en/download/package-manager/). È consigliabile installare la versione più recente di Node.js LTS (4.x). Se si usa una versione precedente, potrebbero verificarsi errori di installazione.


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

Microsoft rilascia di frequente versioni aggiornate dell'interfaccia della riga di comando di Azure. Reinstallare l'interfaccia della riga di comando con il programma di installazione del sistema operativo oppure, se sono installate le versioni più recenti di Node.js e npm, eseguire l'aggiornamento digitando quanto riportato di seguito. Nelle distribuzioni Linux potrebbe essere necessario usare **sudo**.

```
npm update -g azure-cli
```

## Passaggi successivi 

* [Connettersi dall'interfaccia della riga di comando alla sottoscrizione di Azure](xplat-cli-connect.md) per creare e gestire le risorse di Azure.

* Per altre informazioni sull'interfaccia della riga di comando di Azure, il download del codice sorgente, la segnalazione dei problemi o la collaborazione al progetto, visitare il [repository GitHub per tale interfaccia](https://github.com/azure/azure-xplat-cli).

* In caso di domande sull'uso di Azure o dell'interfaccia della riga di comando di Azure, visitare i [forum di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=azurescripting).

* Per i sistemi Linux, è inoltre possibile installare l'interfaccia della riga di comando di Azure generandola dal [codice sorgente](http://aka.ms/linux-azure-cli). Per altre informazioni sulla compilazione da codice sorgente, vedere il file INSTALL incluso nell'archivio di codici sorgente.

[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[linux-installer]: http://go.microsoft.com/fwlink/?linkid=253472
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/azure-cli-arm-commands.md

<!---HONumber=AcomDC_0413_2016-->
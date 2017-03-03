---
title: Installare l&quot;interfaccia della riga di comando di Azure | Microsoft Docs
description: Installare l&quot;interfaccia della riga di comando di Azure per Mac, Linux e Windows per iniziare a usare i servizi di Azure
editor: 
manager: timlt
documentationcenter: 
author: squillace
services: virtual-machines-linux,virtual-network,storage,azure-resource-manager
tags: azure-resource-manager,azure-service-management
ms.assetid: bdb776c8-7a76-4f3a-887c-236b4fffee10
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: command-line-interface
ms.devlang: na
ms.topic: article
ms.date: 01/21/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: cc3cc2dce324942f184b6a520dc4db28518a3091
ms.openlocfilehash: 08f0fe132858bf4629cadc53fbfc6c203592e3a7
ms.lasthandoff: 02/02/2017


---
# <a name="install-the-azure-cli"></a>Installare l'interfaccia della riga di comando di Azure
> [!div class="op_single_selector"]
> * [PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs)
> * [Interfaccia della riga di comando di Azure](xplat-cli-install.md)

Installare rapidamente l'interfaccia della riga di comando di Azure per usare un set di comandi open source basati sulla shell per creare e gestire le risorse in Microsoft Azure. Sono disponibili diverse opzioni per installare questi strumenti multipiattaforma nel computer in uso:

* **Pacchetto npm** -Esegue npm (Gestione pacchetti per JavaScript) per installare il pacchetto dell'interfaccia della riga di comando di Azure più recente nella distribuzione o sistema operativo Linux in uso. Nel computer devono essere installati node.js e npm.
* **Programma di installazione** - È possibile scaricare un programma di installazione per agevolare l'installazione in Mac o Windows.
* **Contenitore Docker** - Per usare l'interfaccia della riga di comando più recente in un contenitore Docker pronto per l'esecuzione. Nel computer deve essere installato l'host Docker.

Per altre opzioni e informazioni, vedere il repository dei progetti in [GitHub](https://github.com/azure/azure-xplat-cli).

Dopo l'installazione dell'interfaccia della riga di comando di Azure, [connetterla alla sottoscrizione di Azure](xplat-cli-connect.md) ed eseguire i comandi **azure** dall'interfaccia della riga di comando (Bash, terminale, prompt dei comandi e così via) per usare le risorse di Azure.

## <a name="option-1-install-an-npm-package"></a>Opzione 1: Installare un pacchetto npm
Per installare l'interfaccia della riga di comando da un pacchetto npm, verificare che siano state caricate e installate le [versioni più recenti di Node.js e npm](https://nodejs.org/en/download/package-manager/). Eseguire quindi il comando **npm install** per installare il pacchetto dell'interfaccia della riga di comando di Azure:

```bash
npm install -g azure-cli
```

Nelle distribuzioni di Linux potrebbe essere necessario usare **sudo** per la corretta esecuzione del comando **npm**, come segue:

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Se è necessario installare o aggiornare Node.js e npm nella distribuzione o nel sistema operativo Linux, è consigliabile installare la versione più recente di Node.js LTS (4.x). Se si usa una versione precedente, potrebbero verificarsi errori di installazione.

È anche possibile scaricare in locale il [file tar][linux-installer] di Linux più recente per il pacchetto npm. Installare quindi il pacchetto npm scaricato come indicato di seguito (nelle distribuzioni di Linux potrebbe essere necessario usare **sudo**):

```bash
npm install -g <path to downloaded tar file>
```

## <a name="option-2-use-an-installer"></a>Opzione 2: Usare un programma di installazione
Se si usa un computer Mac o Windows, è possibile scaricare i programmi di installazione del pacchetto dell'interfaccia della riga di comando seguenti:

* [Programma di installazione di Mac OS X][mac-installer]
* [Windows MSI][windows-installer]

> [!TIP]
> In Windows è anche possibile scaricare l' [Installazione guidata piattaforma Web](https://go.microsoft.com/?linkid=9828653) per installare per il pacchetto dell'interfaccia della riga di comando. Questo programma di installazione consente di installare altri strumenti Azure SDK e a riga di comando dopo l'installazione dell'interfaccia della riga di comando.

## <a name="option-3-use-a-docker-container"></a>Opzione 3: Usare un contenitore Docker
Se il computer è stato configurato come host [Docker](https://docs.docker.com/engine/understanding-docker/) è possibile eseguire l'interfaccia della riga di comando di Azure in un contenitore Docker. Eseguire il comando seguente (nelle distribuzioni di Linux potrebbe essere necessario usare **sudo**):

```bash
docker run -it microsoft/azure-cli
```

## <a name="run-azure-cli-commands"></a>Eseguire i comandi dell'interfaccia della riga di comando di Azure
Una volta installata l'interfaccia della riga di comando di Azure, eseguire il comando **azure** dall'interfaccia (Bash, terminale, prompt dei comandi e così via). Ad esempio, per eseguire il comando della Guida, digitare quanto segue:

```azurecli
azure help
```

> [!NOTE]
> In alcune distribuzioni di Linux potrebbe essere visualizzato un errore simile a `/usr/bin/env: ‘node’: No such file or directory`. Questo errore è causato dalle installazioni recenti di Node.js che vengono installate in /usr/bin/nodejs. Per correggerlo, creare un collegamento simbolico per /usr/bin/node eseguendo questo comando:

```bash
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Per visualizzare la versione dell'interfaccia della riga di comando di Azure installata, digitare quanto segue:

```azurecli
azure --version
```

È ora possibile iniziare la distribuzione. Per accedere a tutti i comandi dell'interfaccia della riga di comando per usare le proprie risorse, [connettersi alla sottoscrizione di Azure dall'interfaccia della riga di comando di Azure](xplat-cli-connect.md).

> [!NOTE]
> Quando si usa per la prima volta l'interfaccia della riga di comando di Azure, viene visualizzato un messaggio che chiede se consentire a Microsoft di raccogliere informazioni sull'utilizzo. La partecipazione è facoltativa. Se si sceglie di partecipare, è possibile interrompere in qualsiasi momento eseguendo `azure telemetry --disable`. Per abilitare la partecipazione in qualsiasi momento, eseguire `azure telemetry --enable`.

## <a name="update-the-cli"></a>Aggiornare l'interfaccia della riga di comando
Microsoft rilascia di frequente versioni aggiornate dell'interfaccia della riga di comando di Azure. Reinstallare l'interfaccia della riga di comando per il sistema operativo o eseguire il contenitore Docker più recente. Oppure, se sono installati npm e la versione più recente di Node.js, eseguire l'aggiornamento digitando quanto riportato di seguito. Nelle distribuzioni di Linux potrebbe essere necessario usare **sudo**.

```bash
npm update -g azure-cli
```

## <a name="enable-tab-completion"></a>Attivare il completamento con tasto TAB
Il completamento con tasto TAB dei comandi dell'interfaccia della riga di comando è supportato per Mac e Linux.

Per abilitarlo in zsh, eseguire:

```bash
echo '. <(azure --completion)' >> .zshrc
```

Per abilitarlo in bash, eseguire:

```bash
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## <a name="next-steps"></a>Passaggi successivi
* [Connettersi a una sottoscrizione di Azure dall'interfaccia della riga di comando di Azure](xplat-cli-connect.md) per creare e gestire le risorse di Azure.
* Per altre informazioni sull'interfaccia della riga di comando di Azure, il download del codice sorgente, la segnalazione dei problemi o la collaborazione al progetto, visitare il [repository GitHub per tale interfaccia](https://github.com/azure/azure-xplat-cli).
* In caso di domande sull'uso di Azure o dell'interfaccia della riga di comando di Azure, visitare i [forum di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).
* È anche possibile provare l'[anteprima della versione 2.0 dell'interfaccia della riga di comando di Azure](https://github.com/azure/azure-cli) basata su Python.

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: /cli/azure/get-started-with-az-cli2
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md


---
title: Uso dell&quot;interfaccia della riga di comando di Azure su Windows | Documentazione Microsoft
description: Uso dell&quot;interfaccia della riga di comando di Azure su Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/14/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: eb2fff3b72646e63c53054a246a47c18df414c60
ms.lasthandoff: 03/11/2017

---

# <a name="using-the-azure-cli-on-windows"></a>Uso dell'interfaccia della riga di comando di Azure su Windows

L'interfaccia della riga di comando di Azure (CLI) fornisce una riga di comando e l'ambiente di scripting per la creazione e la gestione delle risorse di Azure. L'interfaccia della riga di comando di Azure è disponibile per i sistemi operativi Mac OS X, Linux e Windows. In questi sistemi operativi i comandi dell'interfaccia della riga di comando sono identici. Tuttavia, la sintassi di scripting specifica del sistema operativo può differire.

Questo documento descrive le modalità di installazione ed esecuzione dell'interfaccia della riga di comando di Azure in Windows, nonché le considerazioni sulla sintassi specifica del sistema operativo. Per la documentazione dettagliata sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure]( https://docs.microsoft.com/en-us/cli/azure/overview).

## <a name="windows-subsystem-for-linux"></a>Sottosistema di Windows per Linux

Il Sottosistema Windows per Linux (WSL) fornisce un ambiente Ubuntu Linux in versione anniversario di Windows 10. Quando abilitato, WSL offre un'esperienza Bash nativa, che può essere usata per la creazione e l'esecuzione di script dell'interfaccia della riga di comando di Azure. Dal momento che WSL fornisce un'esperienza Bash nativa, gli script dell'interfaccia della riga di comando di Azure possono essere condivisi tra Mac OS X, Linux e Windows senza alcuna modifica.

Per usare l'interfaccia della riga di comando di Azure in WSL, completare le operazioni seguenti.

|Attività | Istruzioni |
|---|---|
| Abilitare WLS | [Documentazione sull'installazione di WSL](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide) |
| Installare l'interfaccia della riga di comando di Azure |[Install the CLI on WSL / Ubuntu 14.04](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2#ubuntu) (Installare l'interfaccia della riga di comando su WSL/Ubuntu 14.04)|

## <a name="powershell"></a>PowerShell

L'interfaccia della riga di comando di Azure può essere eseguita in modo nativo in Windows. In questa configurazione, il pacchetto dell'interfaccia della riga di comando di Azure è installato nel sistema operativo Windows e i comandi possono essere eseguiti da PowerShell. In questa configurazione, gli script e i comandi dell'interfaccia della riga di comando di Azure sono eseguibili in qualsiasi versione di Windows supportata. È tuttavia necessaria la sintassi di scripting specifica della piattaforma. Per questo motivo, gli script non devono necessariamente essere condivisi tra Mac OS X, Linux e Windows senza modifiche.

Per usare l'interfaccia della riga di comando di Azure in Windows, installare il pacchetto tramite le istruzioni contenute in [Install the CLI on Windows](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2#windows) (Installare l'interfaccia della riga di comando su Windows).

## <a name="docker-image"></a>Immagine Docker

Quando si usa Docker per Windows, è possibile avviare un'immagine Docker che include l'interfaccia della riga di comando di Azure. Questa immagine si basa su Linux e include un'esperienza Bash nativa.  Quando si usa Docker per Windows e per l'immagine dell'interfaccia della riga di comando di Azure, gli script devono essere condivisi tra Mac OS X, Linux e Windows. 

Per usare l'interfaccia della riga di comando di Azure in Docker per Windows, verificare che Docker per Windows sia in esecuzione ed esegua il comando seguente.

```bash
docker run -it azuresdk/azure-cli-python:latest bash
```

Al termine, verrà avviata una sessione Bash precaricata con gli strumenti dell'interfaccia della riga di comando di Azure.

## <a name="next-steps"></a>Passaggi successivi

[Esempio di interfaccia della riga di comando per macchine virtuali di Azure](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Esempi di interfaccia della riga di comando per App Web di Azure](../app-service-web/app-service-cli-samples.md)

[Esempi di interfaccia della riga di comando per Azure SQL](../sql-database/sql-database-cli-samples.md)


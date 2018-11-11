---
title: Risoluzione dei problemi client docker in Windows con Visual Studio | Microsoft Docs
description: Risoluzione dei problemi che si verificano quando si usa Visual Studio per creare e distribuire app Web in Docker su Windows mediante Visual Studio 2017.
services: azure-container-service
documentationcenter: na
author: devinb
manager: douge
editor: ''
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 10/13/2017
ms.author: devinb
ms.openlocfilehash: cd88dec2ad79ad9f4b4c004866060be86b777cd9
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2018
ms.locfileid: "50968629"
---
# <a name="troubleshoot-visual-studio-2017-development-with-docker"></a>Risoluzione dei problemi di sviluppo di Visual Studio 2017 con Docker

Quando si usa Visual Studio Tools per Docker, si potrebbero verificare problemi durante la compilazione o il debug dell'applicazione. Di seguito sono descritti alcuni passaggi comuni per la risoluzione dei problemi.

## <a name="volume-sharing-is-not-enabled-enable-volume-sharing-in-the-docker-ce-for-windows-settings--linux-containers-only"></a>La condivisione dei volumi non è abilitata. Abilitare la condivisione dei volumi nelle impostazioni di Docker CE per Windows (solo per i contenitori Linux)

Per risolvere il problema:

1. Fare doppio clic su **Docker per Windows** nell'area di notifica e quindi selezionare **Impostazioni**.
1. Fare clic su **Unità condivise** e condividere l'unità di sistema e l'unità in cui si trova il progetto.

> [!NOTE]
> Se i file risultano condivisi, potrebbe comunque essere necessario fare clic sul collegamento "Reimposta credenziali" nella parte inferiore della finestra di dialogo per abilitare di nuovo la condivisione dei volumi. Per continuare dopo aver reimpostato le credenziali, potrebbe essere necessario riavviare Visual Studio.

![unità condivise](./media/vs-azure-tools-docker-troubleshooting-docker-errors/shareddrives.png)

## <a name="mounts-denied"></a>Montaggi negati

Quando si usa Docker per macOs, potrebbe verificarsi un errore durante il riferimento alla cartella /usr/local/share/dotnet/sdk/NuGetFallbackFolder. Aggiunge la cartella alla scheda Condivisione file in Docker.

## <a name="unable-to-start-debugging"></a>Impossibile avviare il debug

Un motivo potrebbe essere correlato alla presenza di componenti di debug non aggiornati nella cartella del profilo utente. Eseguire i comandi seguenti per rimuovere queste cartelle in modo che vengano scaricati i componenti di debug più recenti alla successiva sessione di debug.

- del %userprofile%\vsdbg
- del %userprofile%\onecoremsvsmon

## <a name="errors-specific-to-networking-when-debugging-your-application"></a>Errori specifici della rete durante il debug dell'applicazione

Provare a eseguire lo script scaricabile dall'articolo relativo alla [pulizia dei componenti di rete dell'host dei contenitori](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/windows-server-container-tools/CleanupContainerHostNetworking), che aggiornerà i componenti di rete nel computer host.


## <a name="microsoftdockertools-github-repo"></a>Repository GitHub Microsoft/DockerTools

Per qualsiasi altro problema riscontrato, vedere i problemi di [Microsoft/DockerTools](https://github.com/microsoft/dockertools/issues).
---
title: Microsoft Teams su Windows Virtual Desktop - Azure
description: Come utilizzare Microsoft Teams su Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ea9bfd21e7f3b92c99600a2492a809a0fc051ed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159618"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Usare Microsoft Teams sul desktop virtuale di Windows

> Si applica a: Windows 10 e Windows 10 IoT Enterprise

Gli ambienti virtualizzati presentano una serie unica di sfide per le app di collaborazione come Microsoft Teams, tra cui una maggiore latenza, un utilizzo elevato della CPU dell'host e scarse prestazioni audio e video complessive. Per altre informazioni sull'uso di Microsoft Teams in ambienti VDI, vedere [Teams for Virtualized Desktop Infrastructure](https://docs.microsoft.com/microsoftteams/teams-for-vdi).

## <a name="prerequisites"></a>Prerequisiti

Prima di poter utilizzare Microsoft Teams su Windows Virtual Desktop, è necessario eseguire le operazioni seguenti:

- Installare il [client Windows Desktop](connect-windows-7-and-10.md) in un dispositivo Windows 10 che soddisfi i requisiti [hardware](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)di Microsoft Teams.
- Connettersi a una macchina virtuale (VM) Windows 10 Multi-session o Windows 10 Enterprise.
- [Preparare la rete](https://docs.microsoft.com/microsoftteams/prepare-network) per Microsoft Teams.

## <a name="use-unoptimized-microsoft-teams"></a>Usare Microsoft Teams non ottimizzato

Puoi utilizzare Microsoft Teams non ottimizzato negli ambienti Windows Virtual Desktop per sfruttare le funzionalità complete di chat e collaborazione di Microsoft Teams e le chiamate audio. La qualità audio nelle chiamate varia in base alla configurazione dell'host perché le chiamate non ottimizzate utilizzano una parte maggiore della CPU dell'host.

### <a name="install-microsoft-teams"></a>Installare Microsoft Teams

Per installare Microsoft Teams nell'ambiente Desktop virtuale Di Windows:

1. Scaricare il [pacchetto MSI](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) di Teams che corrisponde all'ambiente. Si consiglia di utilizzare il programma di installazione a 64 bit in un sistema operativo a 64 bit.
2. Eseguire questo comando per installare il file MSI nella macchina virtuale host.

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      Questo installerà Teams in Programmi o Programmi (x86). Al successivo accesso e avvio di Teams, l'app richiederà le credenziali.

      > [!NOTE]
      > Gli utenti e gli amministratori non possono disabilitare l'avvio automatico per Teams durante l'accesso in questo momento.

      Per disinstallare il file MSI dalla macchina virtuale host, eseguire questo comando:To uninstall the MSI from the host VM, run this command:

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > Se si installa Teams con l'impostazione MSI ALLUSER 1, gli aggiornamenti automatici verranno disabilitati. Ti consigliamo di aggiornare Teams almeno una volta al mese.

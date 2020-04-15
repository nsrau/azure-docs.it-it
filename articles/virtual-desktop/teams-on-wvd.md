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
ms.openlocfilehash: 15a4c9b16b102310fd02f8db3a4fb93cff84882b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314063"
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

### <a name="prepare-your-image-for-teams"></a>Preparare l'immagine per i team

Per abilitare l'installazione di Teams per computer, impostare la seguente chiave del Registro di sistema nell'host:

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 1
```

### <a name="install-microsoft-teams"></a>Installare Microsoft Teams

È possibile distribuire l'app desktop Teams usando un'installazione per computer. Per installare Microsoft Teams nell'ambiente Desktop virtuale Di Windows:

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
      
### <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personalizzare le proprietà di Remote Desktop Protocol per un pool host
La personalizzazione delle proprietà RDP (Remote Desktop Protocol) di un pool host, ad esempio l'esperienza con più monitor, l'abilitazione del reindirizzamento di microfoni e audio, consente di offrire agli utenti un'esperienza ottimale in base alle proprie esigenze. È possibile personalizzare le proprietà RDP in Windows Virtual Desktop utilizzando il parametro **-CustomRdpProperty** nel cmdlet **Set-RdsHostPool.**
Vedere [le impostazioni dei file RDP supportati](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) per un elenco completo delle proprietà supportate e dei relativi valori predefiniti.

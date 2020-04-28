---
title: Microsoft teams sul desktop virtuale Windows-Azure
description: Come usare Microsoft teams sul desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a860b005457c6e02187423a3ffbbc63fe7c758b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187529"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Usare Microsoft teams sul desktop virtuale di Windows

> Si applica a: Windows 10 e Windows 10 Internet (Enterprise)

Gli ambienti virtualizzati presentano un set univoco di problemi per le app di collaborazione come Microsoft teams, tra cui una maggiore latenza, un utilizzo elevato della CPU dell'host e una riduzione delle prestazioni audio e video complessiva. Per altre informazioni sull'uso di Microsoft teams in ambienti VDI, vedere [Teams for virtualizzated Desktop Infrastructure](https://docs.microsoft.com/microsoftteams/teams-for-vdi).

## <a name="prerequisites"></a>Prerequisiti

Prima di poter usare Microsoft teams sul desktop virtuale di Windows, è necessario eseguire queste operazioni:

- Installare il [client desktop di Windows](connect-windows-7-and-10.md) in un dispositivo Windows 10 che soddisfi i [requisiti hardware](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)di Microsoft teams.
- Connettersi a una macchina virtuale (VM) di Windows 10 multisessione o Windows 10 Enterprise.
- [Preparare la rete](https://docs.microsoft.com/microsoftteams/prepare-network) per Microsoft teams.

## <a name="use-unoptimized-microsoft-teams"></a>USA team Microsoft non ottimizzati

È possibile utilizzare i team Microsoft negli ambienti desktop virtuali Windows per sfruttare le funzionalità di chat e collaborazione di Microsoft teams. Desktop virtuale di Windows non supporta i team in ottimizzazioni audio/video (AV) VDI. Le chiamate e le riunioni non sono supportate. Se i criteri dell'organizzazione consentono, è comunque possibile effettuare chiamate audio e partecipare a riunioni con audio, ma la qualità audio non ottimizzata nelle chiamate può variare in base alla configurazione dell'host e potrebbe non essere affidabile. Per altre informazioni, vedere la pagina [relativa alla valutazione delle prestazioni di teams in VDI](https://docs.microsoft.com/microsoftteams/teams-for-vdi#teams-on-vdi-performance-considerations).

### <a name="prepare-your-image-for-teams"></a>Preparare l'immagine per i team

Per abilitare l'installazione dei team per computer, impostare la seguente chiave del registro di sistema nell'host:

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 1
```

### <a name="install-microsoft-teams"></a>Installare Microsoft Teams

È possibile distribuire l'app desktop dei team usando un'installazione per computer. Per installare Microsoft teams nell'ambiente desktop virtuale Windows:

1. Scaricare il [pacchetto MSI teams](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) corrispondente all'ambiente in uso. È consigliabile usare il programma di installazione a 64 bit in un sistema operativo a 64 bit.
2. Eseguire questo comando per installare il file MSI nella VM host.

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      I team vengono installati in programmi o programmi (x86). Al successivo accesso e avvio dei team, l'app richiederà le credenziali.

      > [!NOTE]
      > Gli utenti e gli amministratori non possono disabilitare l'avvio automatico per i team durante l'accesso in questo momento.

      Per disinstallare MSI dalla macchina virtuale host, eseguire questo comando:

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > Se si installano i team con l'impostazione MSI ALLUSER = 1, gli aggiornamenti automatici saranno disabilitati. È consigliabile assicurarsi di aggiornare i team almeno una volta al mese. Per altre informazioni sulla distribuzione dell'app desktop per i team, vedere [distribuire l'app desktop per i team nella macchina virtuale](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm).

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personalizzare le proprietà Remote Desktop Protocol per un pool host
La personalizzazione delle proprietà di Remote Desktop Protocol (RDP) di un pool host, ad esempio l'esperienza multimonitor, l'abilitazione del reindirizzamento microfonico e audio, ti permette di offrire un'esperienza ottimale per gli utenti in base alle esigenze. È possibile personalizzare le proprietà RDP nel desktop virtuale di Windows usando il parametro **-CustomRdpProperty** nel cmdlet **set-RdsHostPool** .
Per un elenco completo delle proprietà supportate e dei relativi valori predefiniti, vedere [impostazioni del file RDP supportate](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) .

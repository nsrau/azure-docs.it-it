---
title: Distribuire una macchina virtuale Windows 7 in un desktop virtuale Windows-Azure
description: Come configurare e distribuire una macchina virtuale Windows 7 su desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: helohr
ms.openlocfilehash: e7f565a995e4c2a5338f08437b0dd336846ba154
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679919"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Distribuire una macchina virtuale Windows 7 in un desktop virtuale Windows

Il processo di distribuzione di una macchina virtuale (VM) Windows 7 in un desktop virtuale Windows è leggermente diverso rispetto a quello delle macchine virtuali che eseguono versioni successive di Windows. Questa guida illustra come distribuire Windows 7.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, seguire le istruzioni riportate in [creare un pool di host con PowerShell](create-host-pools-powershell.md) per creare un pool di host. Successivamente, seguire le istruzioni riportate in [creare pool host in Azure Marketplace](create-host-pools-azure-marketplace.md#optional-assign-additional-users-to-the-desktop-application-group) per assegnare uno o più utenti al gruppo di applicazioni desktop.

## <a name="configure-a-windows-7-virtual-machine"></a>Configurare una macchina virtuale Windows 7

Dopo aver completato i prerequisiti, si è pronti per configurare la VM di Windows 7 per la distribuzione nel desktop virtuale di Windows.

Per configurare una macchina virtuale Windows 7 in un desktop virtuale Windows:

1. Accedere al portale di Azure e cercare l'immagine di Windows 7 Enterprise o caricare un'immagine personalizzata di Windows 7 Enterprise (x64).  
2. Distribuire una o più macchine virtuali con Windows 7 Enterprise come sistema operativo host. Verificare che le macchine virtuali consentano Remote Desktop Protocol (RDP) (porta TCP/3389).
3. Connettersi all'host Windows 7 Enterprise usando il protocollo RDP ed eseguire l'autenticazione con le credenziali definite durante la configurazione della distribuzione. 
4. Aggiungere l'account usato durante la connessione all'host con RDP al gruppo "Desktop remoto user". Se non si esegue questa operazione, potrebbe non essere possibile connettersi alla macchina virtuale dopo l'aggiunta al dominio Active Directory.
5. Passare a Windows Update nella macchina virtuale.
6. Installare tutti gli aggiornamenti di Windows nella categoria importante.
7. Installare tutti gli aggiornamenti di Windows nella categoria facoltativa (esclusi i Language Pack). Viene installato l'aggiornamento di Remote Desktop Protocol 8,0 ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35393)) necessario per completare queste istruzioni.
8. Aprire il Editor Criteri di gruppo locali e passare a **Configurazione Computer** > **modelli amministrativi** > **componenti Windows** > **Servizi Desktop remoto** > **host sessione Desktop remoto** **ambiente sessione remota** > .
9. Abilitare i criteri Remote Desktop Protocol 8,0.
10. Riavviare la macchina virtuale eseguendo il comando seguente:
    
     ```cmd
     shutdown /r /t 0
     ```
    
## <a name="next-steps"></a>Passaggi successivi

La VM di Windows 7 è ora pronta per la distribuzione nel desktop virtuale di Windows. Per completare la distribuzione, seguire le istruzioni in [preparare le installazioni delle macchine virtuali per l'agente desktop virtuale di Windows](create-host-pools-powershell.md#prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations) .

Per un elenco dei problemi noti e delle istruzioni per la risoluzione dei problemi relativi a Windows 7 su desktop virtuale Windows, vedere l'articolo sulla risoluzione dei problemi relativi alle [macchine virtuali Windows 7 in desktop virtuali Windows](troubleshoot-windows-7-vm.md).


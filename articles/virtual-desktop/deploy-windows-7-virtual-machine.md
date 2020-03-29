---
title: Distribuire Windows 7 macchina virtuale Windows Virtual Desktop - AzureDeploy Windows 7 virtual machine Windows Virtual Desktop - Azure
description: Come configurare e distribuire una macchina virtuale Windows 7 in Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 158dd9d6a38cc1aaf7667c0b16518e23b3f7cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366684"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Distribuire una macchina virtuale Windows 7 in Desktop virtuale Windows

Il processo di distribuzione di una macchina virtuale (VM) di Windows 7 in Windows Virtual Desktop è leggermente diverso rispetto alle macchine virtuali che eseguono versioni successive di Windows.The process to deploy a Windows 7 virtual machine (VM) on Windows Virtual Desktop is slightly different than for VMs running later versions of Windows. Questa guida ti dirà come distribuire Windows 7.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, seguire le istruzioni in [Creare un pool host con PowerShell](create-host-pools-powershell.md) per creare un pool host. Seguire le istruzioni in [Creare pool host in Azure Marketplace](create-host-pools-azure-marketplace.md#optional-assign-additional-users-to-the-desktop-application-group) per assegnare uno o più utenti al gruppo di applicazioni desktop.

## <a name="configure-a-windows-7-virtual-machine"></a>Configurare una macchina virtuale Windows 7

Dopo aver completato i prerequisiti, è possibile configurare la macchina virtuale Windows 7 per la distribuzione in Windows Virtual Desktop.Once you've done the prerequisites, you're ready to configure your Windows 7 VM for deployment on Windows Virtual Desktop.

Per configurare una macchina virtuale Windows 7 in Windows Virtual Desktop:

1. Accedere al portale di Azure e cercare l'immagine Windows 7 Enterprise o caricare un'immagine personalizzata di Windows 7 Enterprise (x64).  
2. Distribuire una o più macchine virtuali con Windows 7 Enterprise come sistema operativo host. Assicurarsi che le macchine virtuali consentano Remote Desktop Protocol (RDP) (la porta TCP/3389).
3. Connettersi all'host Windows 7 Enterprise utilizzando RDP ed eseguire l'autenticazione con le credenziali definite durante la configurazione della distribuzione. 
4. Aggiungere l'account utilizzato durante la connessione all'host con RDP al gruppo "Utente desktop remoto". Se non si riesce a eseguire questa operazione, potrebbe non essere possibile connettersi alla macchina virtuale dopo l'aggiunta al dominio di Active Directory.If you don't do this, you might not be able to connect to the VM after you join it to your Active Directory domain.
5. Passare a Windows Update nella macchina virtuale.
6. Installare tutti gli aggiornamenti di Windows nella categoria Importante.
7. Installare tutti gli aggiornamenti di Windows nella categoria Facoltativo (esclusi i Language Pack). In questo modo viene installato l'aggiornamento di Remote Desktop Protocol 8.0 ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)) necessario completare queste istruzioni.
8. Aprire l'Editor Criteri di gruppo locali e passare a**Modelli** > amministrativi **Configurazione** > computer Componenti amministrativi**componenti** > di Windows**Componenti Desktop remoto Servizi** > **Desktop remoto Ambiente** > **sessione**Remote Desktop Host .
9. Abilitare il criterio Remote Desktop Protocol 8.0.
10. Aggiungere questa macchina virtuale al dominio di Active Directory.Join this VM to your Active Directory domain.
11. Riavviare la macchina virtuale eseguendo il comando seguente:
    
     ```cmd
     shutdown /r /t 0
     ```
    
12. Seguire le istruzioni [qui](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/) per ottenere un token di registrazione.
13. [Scaricare l'agente desktop virtuale di Windows per Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Scaricare Gestione agente Desktop virtuale windows per Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Aprire il programma di installazione di Windows Virtual Desktop Agent e seguire le istruzioni. Quando richiesto, fornire la chiave di registrazione creata nel passaggio 12.When prompted, give the registration key you created in step 12.
16. Aprire il programma di installazione di Windows Virtual Desktop e seguire le istruzioni.
17. Facoltativamente, bloccare la porta TCP/3389 per rimuovere l'accesso diretto al protocollo Desktop remoto alla macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

La distribuzione di Windows Virtual Desktop è ora pronta per l'uso. [Scaricare la versione più recente del client Desktop virtuale](https://aka.ms/wvd/clients/windows) di Windows per iniziare.

Per un elenco dei problemi noti e istruzioni per la risoluzione dei problemi per Windows 7 in Windows Desktop virtuale, vedere il seguente articolo sulla [risoluzione dei](troubleshoot-windows-7-vm.md)problemi in Risolvere i problemi relativi alle macchine virtuali windows 7 in Windows Virtual Desktop .

---
title: Distribuire Windows 7 macchina virtuale desktop virtuale Windows-Azure
description: Come configurare e distribuire una macchina virtuale Windows 7 su desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: helohr
ms.openlocfilehash: 90b871c2b75f7ed40c290231ef822258c6b4e6d4
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606877"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Distribuire una macchina virtuale Windows 7 in Desktop virtuale Windows

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
8. Aprire il Editor Criteri di gruppo locali e passare a **Configurazione Computer** > **modelli amministrativi** > **componenti** di Windows ** > Servizi Desktop remoto > host sessione Desktop remoto** > **ambiente di sessione remota**.
9. Abilitare i criteri Remote Desktop Protocol 8,0.
10. Riavviare la macchina virtuale eseguendo il comando seguente:
    
     ```cmd
     shutdown /r /t 0
     ```
    
11. Seguire le istruzioni riportate [qui](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo) per ottenere un token di registrazione.
12. [Scaricare l'agente desktop virtuale Windows per Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
13. [Scaricare il desktop virtuale windows Agent Manager per Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
14. Aprire il programma di installazione dell'agente desktop virtuale di Windows e seguire le istruzioni. Quando richiesto, fornire la chiave di registrazione creata al passaggio 11.
15. Aprire il programma di installazione del desktop virtuale di Windows e seguire le istruzioni.
16. Facoltativamente, bloccare la porta TCP/3389 per rimuovere l'accesso diretto Remote Desktop Protocol alla macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile usare la distribuzione di desktop virtuali Windows. Per iniziare, [scaricare la versione più recente del client desktop virtuale di Windows](https://aka.ms/wvd/clients/windows) .

Per un elenco dei problemi noti e delle istruzioni per la risoluzione dei problemi relativi a Windows 7 su desktop virtuale Windows, vedere l'articolo sulla risoluzione dei problemi relativi alle [macchine virtuali Windows 7 in desktop virtuali Windows](troubleshoot-windows-7-vm.md).

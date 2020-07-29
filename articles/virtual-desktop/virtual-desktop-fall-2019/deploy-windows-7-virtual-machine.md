---
title: Distribuire la macchina virtuale Windows 7 per desktop virtuale Windows (classico)-Azure
description: Come configurare e distribuire una macchina virtuale Windows 7 in Windows Virtual Desktop Windows Desktop (classico).
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 99c82f63e88815edba13bf88ba23c18493649dd9
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285085"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop-classic"></a>Distribuire una macchina virtuale Windows 7 in un desktop virtuale Windows (classico)

>[!IMPORTANT]
>Questo contenuto si applica a desktop virtuale Windows (classico), che non supporta Azure Resource Manager oggetti desktop virtuali di Windows. Se si sta tentando di gestire Azure Resource Manager oggetti desktop virtuali Windows introdotti nella versione corrente di desktop virtuale di Windows, vedere [questo articolo](../deploy-windows-7-virtual-machine.md).

Il processo di distribuzione di una macchina virtuale (VM) Windows 7 in un desktop virtuale Windows è leggermente diverso rispetto a quello delle macchine virtuali che eseguono versioni successive di Windows. Questa guida illustra come distribuire Windows 7.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, seguire le istruzioni riportate in [creare un pool di host con PowerShell](create-host-pools-powershell-2019.md) per creare un pool di host. Successivamente, seguire le istruzioni riportate in [creare pool host in Azure Marketplace](create-host-pools-azure-marketplace-2019.md#optional-assign-additional-users-to-the-desktop-application-group) per assegnare uno o più utenti al gruppo di applicazioni desktop.

## <a name="configure-a-windows-7-virtual-machine"></a>Configurare una macchina virtuale Windows 7

Dopo aver completato i prerequisiti, si è pronti per configurare la VM di Windows 7 per la distribuzione nel desktop virtuale di Windows.

Per configurare una macchina virtuale Windows 7 in un desktop virtuale Windows:

1. Accedere al portale di Azure e cercare l'immagine di Windows 7 Enterprise o caricare un'immagine personalizzata di Windows 7 Enterprise (x64).
2. Distribuire una o più macchine virtuali con Windows 7 Enterprise come sistema operativo host. Verificare che le macchine virtuali consentano Remote Desktop Protocol (RDP) (porta TCP/3389).
3. Connettersi all'host Windows 7 Enterprise usando il protocollo RDP ed eseguire l'autenticazione con le credenziali definite durante la configurazione della distribuzione.
4. Aggiungere l'account usato durante la connessione all'host con RDP al gruppo "Desktop remoto user". Se non si esegue questa operazione, potrebbe non essere possibile connettersi alla macchina virtuale dopo l'aggiunta al dominio Active Directory.
5. Passare a Windows Update nella macchina virtuale.
6. Installare tutti gli aggiornamenti di Windows nella categoria importante.
7. Installare tutti gli aggiornamenti di Windows nella categoria facoltativa (esclusi i Language Pack). Viene installato l'aggiornamento di Remote Desktop Protocol 8,0 ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)) necessario per completare queste istruzioni.
8. Aprire il Editor criteri di gruppo locali e passare a **Configurazione computer**  >  **modelli amministrativi**  >  **componenti di Windows**  >  **Servizi Desktop remoto**  >  **host sessione Desktop remoto**  >  **ambiente sessione remota**.
9. Abilitare i criteri Remote Desktop Protocol 8,0.
10. Aggiungere questa macchina virtuale al dominio Active Directory.
11. Riavviare la macchina virtuale eseguendo il comando seguente:

     ```cmd
     shutdown /r /t 0
     ```

12. Seguire le istruzioni riportate [qui](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/) per ottenere un token di registrazione.
13. [Scaricare l'agente desktop virtuale Windows per Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Scaricare il desktop virtuale windows Agent Manager per Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Aprire il programma di installazione dell'agente desktop virtuale di Windows e seguire le istruzioni. Quando richiesto, fornire la chiave di registrazione creata nel passaggio 12.
16. Aprire il desktop virtuale di Windows Agent Manager e seguire le istruzioni.
17. Facoltativamente, bloccare la porta TCP/3389 per rimuovere l'accesso diretto Remote Desktop Protocol alla macchina virtuale.
18. Facoltativamente, verificare che la versione di .NET Framework sia almeno la 4.7.2. Questo è particolarmente importante se si sta creando un'immagine personalizzata.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile usare la distribuzione di desktop virtuali Windows. Per iniziare, [scaricare la versione più recente del client desktop virtuale di Windows](https://aka.ms/wvd/clients/windows) .

Per un elenco dei problemi noti e delle istruzioni per la risoluzione dei problemi relativi a Windows 7 su desktop virtuale Windows, vedere l'articolo sulla risoluzione dei problemi relativi alle [macchine virtuali Windows 7 in desktop virtuali Windows](troubleshoot-windows-7-vm.md).

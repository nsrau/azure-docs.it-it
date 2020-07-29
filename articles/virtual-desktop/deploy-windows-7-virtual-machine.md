---
title: Distribuire Windows 7 macchina virtuale desktop virtuale Windows-Azure
description: Come configurare e distribuire una macchina virtuale Windows 7 su desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6cb4a31e3360f3f9f8c9ed4684c30295489d27d8
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285154"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Distribuire una macchina virtuale Windows 7 in Desktop virtuale Windows

>[!IMPORTANT]
>Questo contenuto si applica al desktop virtuale di Windows con Azure Resource Manager oggetti desktop virtuali di Windows. Se si usa desktop virtuale di Windows (classico) senza Azure Resource Manager oggetti, vedere [questo articolo](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md).

Il processo di distribuzione di una macchina virtuale (VM) Windows 7 in un desktop virtuale Windows è leggermente diverso rispetto a quello delle macchine virtuali che eseguono versioni successive di Windows. Questa guida illustra come distribuire Windows 7.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, seguire le istruzioni riportate in [creare un pool di host con PowerShell](create-host-pools-powershell.md) per creare un pool di host. Se si usa il portale, seguire le istruzioni riportate nei passaggi da 1 a 9 di [creare un pool host usando il portale di Azure](create-host-pools-azure-marketplace.md). Successivamente, selezionare **Verifica + crea** per creare un pool host vuoto. 

## <a name="configure-a-windows-7-virtual-machine"></a>Configurare una macchina virtuale Windows 7

Dopo aver completato i prerequisiti, si è pronti per configurare la VM di Windows 7 per la distribuzione nel desktop virtuale di Windows.

Per configurare una macchina virtuale Windows 7 in un desktop virtuale Windows:

1. Accedere al portale di Azure e cercare l'immagine di Windows 7 Enterprise o caricare un'immagine personalizzata di Windows 7 Enterprise (x64).
2. Distribuire una o più macchine virtuali con Windows 7 Enterprise come sistema operativo host. Verificare che le macchine virtuali consentano Remote Desktop Protocol (RDP) (porta TCP/3389).
3. Connettersi all'host Windows 7 Enterprise usando il protocollo RDP ed eseguire l'autenticazione con le credenziali definite durante la configurazione della distribuzione.
4. Aggiungere l'account usato durante la connessione all'host con RDP al gruppo "Desktop remoto user". Se non si aggiunge l'account, potrebbe non essere possibile connettersi alla macchina virtuale dopo l'aggiunta al dominio Active Directory.
5. Passare a Windows Update nella macchina virtuale.
6. Installare tutti gli aggiornamenti di Windows nella categoria importante.
7. Installare tutti gli aggiornamenti di Windows nella categoria facoltativa (esclusi i Language Pack). Questo processo consente di installare l'aggiornamento di Remote Desktop Protocol 8,0 ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)) necessario per completare queste istruzioni.
8. Aprire il Editor criteri di gruppo locali e passare a **Configurazione computer**  >  **modelli amministrativi**  >  **componenti di Windows**  >  **Servizi Desktop remoto**  >  **host sessione Desktop remoto**  >  **ambiente sessione remota**.
9. Abilitare i criteri Remote Desktop Protocol 8,0.
10. Aggiungere questa macchina virtuale al dominio Active Directory.
11. Riavviare la macchina virtuale eseguendo il comando seguente:

     ```cmd
     shutdown /r /t 0
     ```

12. Seguire le istruzioni riportate [qui](/powershell/module/az.desktopvirtualization/new-azwvdregistrationinfo?view=azps-4.3.0) per ottenere un token di registrazione.
      
      - Se si preferisce usare la portale di Azure, è anche possibile passare alla pagina Panoramica del pool di host a cui si vuole aggiungere la macchina virtuale e creare un token.
  
13. [Scaricare l'agente desktop virtuale Windows per Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Scaricare il desktop virtuale windows Agent Manager per Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Aprire il programma di installazione dell'agente desktop virtuale di Windows e seguire le istruzioni. Quando richiesto, fornire la chiave di registrazione creata nel passaggio 12.
16. Aprire il desktop virtuale di Windows Agent Manager e seguire le istruzioni.
17. Facoltativamente, bloccare la porta TCP/3389 per rimuovere l'accesso diretto Remote Desktop Protocol alla macchina virtuale.
18. Facoltativamente, verificare che la versione di .NET Framework sia almeno la 4.7.2. L'aggiornamento del Framework è particolarmente importante se si sta creando un'immagine personalizzata.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile usare la distribuzione di desktop virtuali Windows. Per iniziare, [scaricare la versione più recente del client desktop virtuale di Windows](https://aka.ms/wvd/clients/windows) .

Per un elenco dei problemi noti e delle istruzioni per la risoluzione dei problemi relativi a Windows 7 su desktop virtuale Windows, vedere l'articolo sulla risoluzione dei problemi relativi alle [macchine virtuali Windows 7 in desktop virtuali Windows](./virtual-desktop-fall-2019/troubleshoot-windows-7-vm.md).

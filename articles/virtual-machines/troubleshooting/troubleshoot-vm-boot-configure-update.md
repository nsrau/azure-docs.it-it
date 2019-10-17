---
title: L'avvio della macchina virtuale si blocca su un messaggio analogo a "Preparazione di Windows. Non spegnere il computer" in Azure | Microsoft Docs
description: Presentazione della procedura per risolvere il problema di blocco dell'avvio della macchina virtuale quando viene restituito un messaggio analogo a "Preparazione di Windows. Non spegnere il computer".
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: 4263afe33caa4d6471848c8e7dbf9bc1eeec4bee
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332557"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>L'avvio della macchina virtuale si blocca su un messaggio analogo a "Preparazione di Windows. Non spegnere il computer" in Azure

Questo articolo descrive le schermate "preparazione" e "preparazione di Windows" che possono verificarsi quando si avvia una macchina virtuale (VM) Windows in Microsoft Azure. Sono incluse le procedure per raccogliere i dati per un ticket di supporto.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptoms"></a>Sintomi

Una macchina virtuale Windows non viene avviata. Quando si usa la **diagnostica di avvio** per ottenere lo screenshot della macchina virtuale, è possibile che nella macchina virtuale venga visualizzato il messaggio "preparazione" o "recupero di Windows pronto".

![Esempio di messaggio per Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Esempio di messaggio](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Causa

Questo problema si verifica in genere quando il server esegue il riavvio finale dopo che è stata modificata la configurazione. È possibile che la modifica della configurazione sia stata inizializzata da aggiornamenti di Windows o da modifiche apportate ai ruoli o alle funzionalità del server. Per Windows Update, se l'entità degli aggiornamenti è stata notevole, il sistema operativo ha bisogno di più tempo per riconfigurare le modifiche.

## <a name="collect-an-os-memory-dump"></a>Raccogliere un dump di memoria del sistema operativo

Se il problema non si risolve dopo l'attesa dell'elaborazione delle modifiche, è necessario raccogliere un file di dump della memoria e contattare il supporto tecnico. Per raccogliere il file dump, seguire questa procedura:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Collegare il disco del sistema operativo alla macchina virtuale di ripristino

1. Creare uno snapshot del disco del sistema operativo della macchina virtuale interessata come backup. Per altre informazioni, vedere [Snapshot di un disco](../windows/snapshot-copy-managed-disk.md).
2. [Collegare il disco del sistema operativo alla macchina virtuale di ripristino](../windows/troubleshoot-recovery-disks-portal.md).
3. Connettersi tramite Desktop remoto alla macchina virtuale di ripristino. 
4. Se il disco del sistema operativo è crittografato, è necessario disattivare la crittografia prima di procedere al passaggio successivo. Per altre informazioni, vedere [decrittografare il disco del sistema operativo crittografato nella macchina virtuale che non può essere avviato](troubleshoot-bitlocker-boot-error.md#solution).

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Individuare il file dump e inviare un ticket di supporto

1. Nella macchina virtuale di ripristino passare alla cartella Windows nel disco del sistema operativo collegato. Se la lettera di unità assegnata al disco del sistema operativo collegato è F, è necessario passare a F:\Windows.
2. Individuare il file Memory. dmp, quindi [inviare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) al file dump. 

Se non è possibile individuare il file dump, procedere al passaggio successivo per abilitare il log Dump e la console seriale.

### <a name="enable-dump-log-and-serial-console"></a>Abilitare il log Dump e la console seriale

Per abilitare il log Dump e la console seriale, eseguire lo script seguente.

1. Aprire una sessione del prompt dei comandi con privilegi elevati (Esegui come amministratore).
2. Eseguire lo script seguente:

    In questo script si presuppone che la lettera di unità assegnata al disco del sistema operativo collegato sia F. Sostituirla con il valore appropriato per la specifica macchina virtuale.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

    1. Assicurarsi che lo spazio sul disco sia sufficiente per allocare una quantità di memoria equivalente alla RAM, che dipende dalle dimensioni selezionate per la macchina virtuale.
    2. Se lo spazio non è sufficiente oppure se la macchina virtuale è di grandi dimensioni (serie G, GS o E), è possibile modificare la posizione in cui viene creato il file e fare riferimento a un altro disco dati collegato alla macchina virtuale. A tale scopo, è necessario modificare la chiave seguente:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Scollegare il disco del sistema operativo e quindi ricollegarlo alla macchina virtuale interessata](../windows/troubleshoot-recovery-disks-portal.md).
4. Avviare la macchina virtuale e accedere alla console seriale.
5. Selezionare **Invia interrupt non mascherabile (NMI)** per attivare il dump della memoria.
    immagine ![The su dove inviare l'interrupt non mascherabile @ no__t-1
6. Alleghi nuovamente il disco del sistema operativo a una macchina virtuale di ripristino, Raccogli file dump.

## <a name="contact-microsoft-support"></a>Contattare il supporto Microsoft

Dopo aver raccolto il file di dump, contattare il [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per analizzare la causa radice.
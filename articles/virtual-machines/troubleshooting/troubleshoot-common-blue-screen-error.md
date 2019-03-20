---
title: Errori con schermata blu all'avvio di una macchina virtuale di Azure | Microsoft Docs
description: Informazioni su come risolvere i problemi che determinano la visualizzazione di una schermata blu di errore in fase di avvio | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: genli
ms.openlocfilehash: 7e37d8e732408e70dbcdc86d3e21556f553506cd
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57768485"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>Windows visualizza una schermata blu di errore durante l'avvio di una macchina virtuale di Azure
Questo articolo descrive gli errori con schermata blu che possono verificarsi all'avvio di una macchina virtuale Windows in Microsoft Azure. Sono incluse le procedure per raccogliere i dati per un ticket di supporto. 

> [!NOTE] 
> Azure offre due modelli di distribuzione diversi per creare e usare le risorse: [Resource Manager e distribuzione classica](../../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra il modello di distribuzione Resource Manager, che Microsoft consiglia di usare per le nuove distribuzioni in sostituzione del modello di distribuzione classica.

## <a name="symptom"></a>Sintomo 

La macchina virtuale Windows non si avvia. Quando si controllano gli screenshot di avvio nella finestra [Diagnostica di avvio](./boot-diagnostics.md), viene visualizzato uno dei messaggi di errore seguenti in una schermata blu:

- Si è verificato un problema ed è necessario riavviare il PC. È in corso la raccolta di alcune informazioni sull'errore, quindi sarà possibile eseguire il riavvio.
- Si è verificato un problema ed è necessario riavviare il PC.

Questa sezione contiene un elenco dei messaggi di errore comuni che possono essere visualizzati durante la gestione di macchine virtuali:

## <a name="cause"></a>Causa

Un errore irreversibile può essere causato da diversi motivi. Le cause più comuni sono:

- Problemi relativi a un driver
- File di sistema o memoria danneggiati
- Accesso di un'applicazione a un settore non consentito della memoria

## <a name="collect-memory-dump-file"></a>Raccogliere i file dump della memoria

Per risolvere questo problema, è innanzitutto necessario raccogliere il file dump per l'arresto anomalo e contattare il supporto tecnico con il file dump. Per raccogliere il file dump, seguire questa procedura:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Collegare il disco del sistema operativo alla macchina virtuale di ripristino

1. Creare uno snapshot del disco del sistema operativo della macchina virtuale interessata come backup. Per altre informazioni, vedere [Snapshot di un disco](../windows/snapshot-copy-managed-disk.md).
2. [Collegare il disco del sistema operativo alla macchina virtuale di ripristino](../windows/troubleshoot-recovery-disks-portal.md). 
3. Connettersi tramite Desktop remoto alla macchina virtuale di ripristino.

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Individuare il file dump e inviare un ticket di supporto

1. Nella macchina virtuale di ripristino passare alla cartella Windows nel disco del sistema operativo collegato. Se la lettera di unità assegnata al disco del sistema operativo collegato è F, è necessario passare a F:\Windows.
2. Individuare il file con estensione dmp e quindi [inviare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) con il file dump. 

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
4. Avviare la macchina virtuale per riprodurre il problema. Verrà generato un file dump.
5. Collegare il disco del sistema operativo a una macchina virtuale di ripristino, raccogliere il file dump e quindi [inviare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) con il file dump.




---
title: La macchina virtuale di Azure non risponde con l'errore C01A001D quando si applica Windows Update
description: Questo articolo illustra la procedura per risolvere i problemi in cui Windows Update genera un errore e smette di rispondere in una macchina virtuale di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3528
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: v-mibufo
ms.openlocfilehash: 16c8eed3377c2191b4345ec59ec1eba8be01369d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633957"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>La macchina virtuale non risponde con l'errore "C01A001D" quando si applica Windows Update

Questo articolo illustra la procedura per risolvere i problemi in cui Windows Update (KB) genera un errore e smette di rispondere in una macchina virtuale di Azure.

## <a name="symptoms"></a>Sintomi

Quando si usa la [diagnostica di avvio](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) per visualizzare lo screenshot della macchina virtuale, viene visualizzato il Windows Update (KB) in corso, ma ha esito negativo con codice di errore:' C01A001D '.

![Windows Update non risponde](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>Causa

Non è possibile creare un file principale nella file system. Il sistema operativo non è in grado di scrivere file sul disco.

## <a name="resolution"></a>Soluzione

### <a name="process-overview"></a>Panoramica del processo

1. [Creare e accedere a una macchina virtuale di ripristino](#create-and-access-a-repair-vm).
2. [Liberare spazio sul disco rigido](#free-up-space-on-the-hard-disk).
3. [Consigliato: prima di ricompilare la macchina virtuale, abilitare la raccolta di immagini della memoria e della console seriale](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection).
4. [Ricompilare la macchina virtuale](#rebuild-the-vm).

> [!NOTE]
> Quando si verifica questo errore, il sistema operativo guest non è operativo. Per risolvere il problema, è necessario eseguire la risoluzione dei problemi in modalità offline.

### <a name="create-and-access-a-repair-vm"></a>Creare e accedere a una macchina virtuale di ripristino

1. Seguire [i passaggi 1-3 dei comandi di ripristino della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) per preparare una macchina virtuale di ripristino.
2. Connettersi alla macchina virtuale di ripristino usando Connessione Desktop remoto.

### <a name="free-up-space-on-the-hard-disk"></a>Liberare spazio sul disco rigido

Se il disco non è già 1 TB, è necessario ridimensionarlo. Quando il disco è di 1 TB, eseguire una pulizia del disco e una deframmentazione dell'unità.

1. Controllare se il disco è pieno. Se il disco è inferiore a 1 TB, [espanderlo fino a un massimo di 1 TB usando PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json).
2. Quando il disco è di 1 TB, eseguire una pulitura del disco.
    - [Scollegare il disco dati dalla macchina virtuale interruppe](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk).
    - [Alleghi il disco dati a una macchina virtuale funzionante](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm).
    - Utilizzare lo [strumento Pulitura disco](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) per liberare spazio.
3. Dopo il ridimensionamento e la pulizia, deframmentare l'unità:

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    A seconda del livello di frammentazione, l'operazione potrebbe richiedere ore.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>Scelta consigliata: prima di ricompilare la macchina virtuale, abilitare la raccolta di dump della memoria e della console seriale

1. Aprire una sessione del prompt dei comandi con privilegi elevati (Esegui come amministratore).
2. Eseguire i comandi seguenti:

    Abilita console seriale:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. Verificare che lo spazio disponibile sul disco del sistema operativo sia almeno uguale alla dimensione della memoria VM (RAM).

    Se non è disponibile spazio sufficiente sul disco del sistema operativo, modificare il percorso in cui verrà creato il file di dump della memoria e fare riferimento a un disco dati collegato alla macchina virtuale e con spazio libero sufficiente. Per modificare il percorso, sostituire `%SystemRoot%` con la lettera di unità (ad esempio "F:") del disco dati nei comandi seguenti:

    **Abilitare la configurazione consigliata del dump del sistema operativo:**

    Carica disco del sistema operativo danneggiato:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Abilita in ControlSet001:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Abilita in ControlSet002:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Scarica disco del sistema operativo danneggiato:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>Ricompilare la macchina virtuale

Usare [il passaggio 5 dei comandi di ripristino della macchina](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) virtuale per riassemblare la macchina virtuale.

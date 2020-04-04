---
title: Azure VM is unresponsive with C01A001D error when applying Windows Update
description: Questo articolo illustra come risolvere i problemi in cui l'aggiornamento di Windows genera un errore e non risponde in una macchina virtuale di Azure.This article provides steps to resolve issues where Windows update generates an error and becomes notresponsive in an Azure VM.
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
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633957"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>La macchina virtuale non risponde con l'errore "C01A001D" quando si applica Windows Update

In questo articolo viene illustrata la procedura per risolvere i problemi in cui Windows Update (KB) genera un errore e non risponde in una macchina virtuale di Azure.This article provides steps to resolve issues where Windows Update (KB) generates an error and becomes not responsive in an Azure VM.

## <a name="symptoms"></a>Sintomi

Quando si utilizza la diagnostica di [avvio](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) per visualizzare la schermata della macchina virtuale, viene visualizzato Windows Update (KB) in corso, ma non riesce con il codice di errore: 'C01A001D'.

![insensibile a Windows Update](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>Causa

Non è possibile creare un file principale nel file system. Il sistema operativo non è in grado di scrivere file sul disco.

## <a name="resolution"></a>Risoluzione

### <a name="process-overview"></a>Panoramica del processo

1. [Creare e accedere a una macchina virtuale di ripristino.](#create-and-access-a-repair-vm)
2. [Liberare spazio sul disco rigido](#free-up-space-on-the-hard-disk).
3. [Consigliato: prima di ricompilare la macchina virtuale, abilitare la console seriale e la raccolta](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection)di immagine della memoria.
4. [Ricompilare la macchina virtuale](#rebuild-the-vm).

> [!NOTE]
> Quando si verifica questo errore, il sistema operativo guest non è operativo. È necessario risolvere i problemi in modalità offline per risolvere il problema.

### <a name="create-and-access-a-repair-vm"></a>Creare e accedere a una macchina virtuale di ripristinoCreate and access a repair VM

1. Seguire [i passaggi da 1 a 3 dei comandi](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) di ripristino della macchina virtuale per preparare una macchina virtuale di ripristino.
2. Connettersi alla macchina virtuale di ripristino tramite Connessione desktop remoto.

### <a name="free-up-space-on-the-hard-disk"></a>Liberare spazio sul disco rigido

Se il disco non è già 1 Tb, è necessario ridimensionarlo. Una volta che il disco è 1 TB, eseguire una pulizia del disco e una deframmentazione dell'unità.

1. Verificare che il disco sia pieno. Se il disco è inferiore a 1 Tb, [espanderlo fino a un massimo di 1 Tb utilizzando PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json).
2. Una volta che il disco è 1 Tb, eseguire una pulizia del disco.
    - [Scollegare il disco dati dalla macchina virtuale interrotta.](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk)
    - [Collegare il disco dati a una macchina virtuale funzionante.](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm)
    - Utilizzare lo [strumento Pulitura disco](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) per liberare spazio.
3. Dopo il ridimensionamento e la pulizia, deframmentare l'unità:

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    A seconda del livello di frammentazione, l'operazione potrebbe richiedere ore.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>Consigliato: prima di ricompilare la macchina virtuale, abilitare la raccolta di dump di memoria e console serialeRecommended: Before rebuilding the VM, enable serial console and memory dump collection

1. Aprire una sessione del prompt dei comandi con privilegi elevati (Esegui come amministratore).
2. Eseguire i comandi seguenti:

    Abilita console seriale:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. Assicurarsi che lo spazio disponibile sul disco del sistema operativo sia almeno uguale alla dimensione della memoria della macchina virtuale (RAM).

    Se lo spazio sul disco del sistema operativo non è sufficiente, modificare il percorso in cui verrà creato il file di immagine della memoria e fare riferimento a un disco dati collegato alla macchina virtuale e con spazio libero sufficiente. Per modificare la `%SystemRoot%` posizione, sostituire con la lettera di unità (ad esempio "F:") del disco dati nei seguenti comandi:

    **Abilita configurazione suggerita per il dump del sistema operativo:**

    Carica disco del sistema operativo interrotto:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Abilita su ControlSet001:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Abilita su ControlSet002:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Scaricare il disco del sistema operativo rotto:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>Ricompilare la macchina virtualeRebuild the VM

Usare [il passaggio 5 dei comandi di ripristino della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) per riassemblare la macchina virtuale.

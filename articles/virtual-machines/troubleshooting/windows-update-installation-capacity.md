---
title: "Risolvere i problemi di avvio del sistema operativo: capacità per l'installazione di Windows Update"
description: Procedura per risolvere i problemi in cui Windows Update (KB) riceve un errore e smette di rispondere in una macchina virtuale di Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 6359e486-7b02-4c1e-995c-ef6d505f85f4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: 596303223554589ef26938486ccfd2281ccd46f5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "86999106"
---
# <a name="troubleshoot-os-start-up--windows-update-installation-capacity"></a>Risolvere i problemi di avvio del sistema operativo: capacità per l'installazione di Windows Update

Questo articolo illustra la procedura per risolvere i problemi in una macchina virtuale (VM) di Azure in cui Windows Update (KB) riceve un errore e smette di rispondere.

## <a name="symptom"></a>Sintomo

Quando si usa la diagnostica di avvio per visualizzare lo screenshot della macchina virtuale, si noterà che la schermata mostra Windows Update (KB) in corso, ma con esito negativo con il codice errore: **C01A001D**. La figura seguente mostra Windows Update (KB) bloccato con il messaggio "Errore C01A001D di applicazione aggiornamento ##### di ##### (######)":

![Windows Update (KB) è bloccato con il messaggio "Errore C01A001D di applicazione aggiornamento X di Y (Z)".](./media/troubleshoot-windows-update-installation-capacity/1.png)

## <a name="cause"></a>Causa

In questa situazione, il sistema operativo non è in grado di completare un'installazione di Windows Update (KB) perché non è possibile creare un file principale nel file system. In base a questo codice di errore, il sistema operativo non è in grado di scrivere alcun file sul disco.

## <a name="solution"></a>Soluzione

### <a name="process-overview"></a>Panoramica del processo:

1. Creare e accedere a una macchina virtuale di ripristino.
1. Liberare spazio sul disco.
1. Abilitare la console seriale e la raccolta di dump della memoria.
1. Ricreare la macchina virtuale.

> [!NOTE]
> Quando si verifica questo errore, il sistema operativo guest non è operativo. Risolvere il problema in modalità offline per risolvere il problema.

### <a name="create-and-access-a-repair-vm"></a>Creare e accedere a una macchina virtuale di ripristino

1. Usare i passaggi da 1 a 3 dei [comandi di ripristino della macchina virtuale](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) per preparare una macchina virtuale di ripristino.
1. Usare Connessione Desktop remoto per connettersi alla macchina virtuale di ripristino.

### <a name="free-up-space-on-the-disk"></a>Liberare spazio sul disco

Per risolvere il problema:

- Ridimensionare il disco fino a 1 TB se non è già alla dimensione massima di 1 TB.
- Eseguire una pulizia del disco.
- Deframmentare l'unità.

1. Controllare se il disco è pieno. Se le dimensioni del disco sono inferiori a 1 TB, espanderle fino a un massimo di 1 TB [usando PowerShell](../windows/expand-os-disk.md).
1. Se il disco è già 1 TB, sarà necessario eseguire una pulizia del disco.
   1. Scollegare il disco dati [dalla macchina virtuale rotta](../windows/detach-disk.md).
   1. Collegare il disco dati [a una macchina virtuale funzionante](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
   1. Usare lo [strumento di pulitura disco](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) per liberare spazio.
1. Una volta completate le operazioni di ridimensionamento e pulitura, deframmentare l'unità usando il comando seguente:

   ```
   defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
   ```
   
A seconda del livello di frammentazione, la deframmentazione potrebbe richiedere diverse ore.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Abilitare la console seriale e la raccolta di dump della memoria

**Consigliato**: Prima di ricreare la macchina virtuale, abilitare la console seriale e la raccolta di dump della memoria eseguendo lo script seguente:

1. Aprire una sessione del prompt dei comandi con privilegi elevati come amministratore.
1. Eseguire i comandi seguenti:

   **Abilitare la console seriale**:
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Verificare che lo spazio disponibile sul disco del sistema operativo sia superiore alla dimensione della memoria (RAM) nella macchina virtuale.

   Se lo spazio disponibile nel disco del sistema operativo non è sufficiente, modificare il percorso in cui verrà creato il file di dump della memoria e fare in modo che quella posizione faccia riferimento a qualsiasi disco dati collegato alla macchina virtuale con spazio libero sufficiente. Per modificare il percorso, sostituire **%SystemRoot%** con la lettera di unità del disco dati, ad esempio **F:** , nei comandi seguenti.

   Configurazione consigliata per abilitare il dump del sistema operativo:

    **Caricare disco del sistema operativo non funzionante:**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM 
   ```
   
   **Abilitare su ControlSet001**:

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
   ```
   
   **Abilitare su ControlSet002**:

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
   ```
   
   **Scaricare disco del sistema operativo non funzionante**:

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>Ricreare la macchina virtuale

Usare il [passaggio 5 dei comandi di ripristino della macchina virtuale](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) per ricreare la macchina virtuale.

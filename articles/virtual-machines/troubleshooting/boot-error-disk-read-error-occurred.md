---
title: "Risolvere l'errore di avvio: errore lettura da disco"
description: Questo articolo illustra la procedura per risolvere i problemi in cui non è possibile leggere il disco in una macchina virtuale di Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 817c9c5c-6a81-4b42-a6ad-0a0a11858b84
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/01/2020
ms.author: v-miegge
ms.openlocfilehash: f59903ed111be1fe414f4b3ded250d754c91d323
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069140"
---
# <a name="troubleshoot-boot-error---disk-read-error-occurred"></a>Risolvere l'errore di avvio: errore lettura da disco

Questo articolo illustra la procedura per risolvere i problemi in cui non è possibile leggere il disco in una macchina virtuale (VM) di Azure.

## <a name="symptoms"></a>Sintomi

Quando si usa la [diagnostica di avvio](./boot-diagnostics.md) per visualizzare lo screenshot della macchina virtuale, si noterà che verrà visualizzato il messaggio: "A disk read error occurred (Errore lettura da disco). Premere Ctrl + Alt + Canc per riavviare".

   ![Messaggio di errore: A disk read error occurred. Press Ctrl+Alt+Del to restart (Errore lettura da disco. Premere CTRL+ALT+CANC per riavviare).](./media/disk-read-error-occurred/1.png)

## <a name="cause"></a>Causa

Questo messaggio di errore indica che la struttura del disco è danneggiata e illeggibile. Se si usa una macchina virtuale di prima generazione, è anche possibile che la partizione del disco contenente i dati di configurazione di avvio non sia impostata su **Active** (Attiva).

## <a name="solution"></a>Soluzione

### <a name="process-overview"></a>Panoramica del processo

1. Creare e accedere a una macchina virtuale di ripristino.
1. Selezionare una soluzione:
   - [Impostare lo stato della partizione su attivo](#set-partition-status-to-active)
   - [Correggere la partizione del disco](#fix-the-disk-partition)
1. Abilitare la console seriale e la raccolta di dump della memoria.
1. Ricreare la macchina virtuale.

> [!NOTE]
> Quando si verifica questo errore di avvio, il sistema operativo guest non è operativo. Questo problema sarà risolto in modalità offline.

### <a name="create-and-access-a-repair-vm"></a>Creare e accedere a una macchina virtuale di ripristino

1. Usare i passaggi da 1 a 3 dei [comandi di ripristino della macchina virtuale](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) per preparare una macchina virtuale di ripristino.
1. Usare Connessione Desktop remoto per connettersi alla macchina virtuale di ripristino.

### <a name="set-partition-status-to-active"></a>Impostare lo stato della partizione su attivo

Le macchine virtuali di prima generazione devono prima verificare che la partizione del sistema operativo che include l'archivio dei dati di configurazione di avvio sia contrassegnata come **Active** (Attiva). Se si dispone di una macchina virtuale di seconda generazione, passare a [Correggere la partizione del disco](#fix-the-disk-partition), perché il flag di stato è stato deprecato nella generazione successiva.

1. Aprire un prompt dei comandi con privilegi elevati (cmd.exe).
1. Immettere **diskpart** per avviare lo strumento **DISKPART**.
1. Immettere **list disk** per elencare i dischi del sistema e identificare il disco rigido virtuale del sistema operativo collegato.
1. Dopo aver individuato il disco rigido virtuale del sistema operativo collegato, immettere **sel disk #** per selezionare il disco. Vedere l'immagine seguente per un esempio in cui il disco 1 è il disco rigido virtuale del sistema operativo collegato.

   ![Finestra diskpart con l'output del comando **list disk**, in cui il disco 0 e il disco 1 vengono visualizzati nella tabella. La finestra contiene anche l'output del comando **sel disk 1**, in cui il disco 1 è il disco selezionato](./media/disk-read-error-occurred/2.png)

1. Dopo aver selezionato il disco, immettere **list partition** per elencare le partizioni del disco selezionato.
1. Dopo aver identificato la partizione di avvio, immettere **sel partition #** per selezionare la partizione. Le dimensioni della partizione di avvio raggiungono spesso 350 MB circa.  Vedere ad esempio l'immagine seguente, in cui la partizione 1 è la partizione di avvio.

   ![Finestra diskpart con l'output del comando **list partition**, in cui la partizione 1 e la partizione 2 vengono visualizzate nella tabella. La finestra contiene anche l'output del comando **sel partition 1**, in cui la partizione 1 è il disco selezionato.](./media/disk-read-error-occurred/3.png)

1. Immettere **detail partition** per verificare lo stato della partizione. Vedere gli screenshot seguenti per esempi della partizione impostata su **Active: No** (Attiva: no) o **Active: Yes** (Attiva: sì).

   **Stato impostato su no**

   ![Finestra diskpart con l'output del comando **detail partition**, in cui la partizione 1 è impostata su **Active: No** (Attiva: no).](./media/disk-read-error-occurred/4.png)

   **Stato impostato su sì**

   ![Finestra diskpart con l'output del comando **detail partition**, in cui la partizione 1 è impostata su **Active: Yes** (Attiva: sì).](./media/disk-read-error-occurred/5.png)

1. Se la partizione non è impostata su **Active** (Attiva), immettere **active** per modificare il flag di stato su attivo.
1. Immettere **detail partition** per verificare che la modifica dello stato sia stata completata e verificare che l'output restituisca **Active: Yes** (Attiva: sì). 
1. Immettere **exit** per chiudere lo strumento DISKPART e salvare le modifiche di configurazione.

### <a name="fix-the-disk-partition"></a>Correggere la partizione del disco

1. Aprire un prompt dei comandi con privilegi elevati (cmd.exe).
1. Usare il comando seguente per eseguire **CHKDSK** nei dischi e correggere gli errore:

   `chkdsk <DRIVE LETTER>: /f`

   Se si aggiunge l'opzione di comando **/f**, tutti gli errori sul disco vengono risolti. Assicurarsi di sostituire **< LETTERA DI UNITÀ >** con la lettera del disco rigido virtuale del sistema operativo collegato.

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

   **Caricare l'hive del Registro di sistema dal disco del sistema operativo non funzionante:**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

   **Abilitare su ControlSet001:**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Abilitare su ControlSet002:**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Scaricare il disco del sistema operativo non funzionante:**

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>Ricreare la macchina virtuale

Usare il [passaggio 5 dei comandi di ripristino della macchina virtuale](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) per ricreare la macchina virtuale.

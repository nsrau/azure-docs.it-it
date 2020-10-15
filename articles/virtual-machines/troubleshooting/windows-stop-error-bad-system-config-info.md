---
title: Errore di arresto di Windows-informazioni di configurazione del sistema 0x00000074 non valide
description: Questo articolo illustra i passaggi per risolvere i problemi che non possono essere avviati da Windows e devono essere riavviati a causa di informazioni di configurazione del sistema non valide in una macchina virtuale (VM) di Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a1e47f6a-c7d5-4327-a7b0-ad48ed543641
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: v-miegge
ms.openlocfilehash: 7d1233c97ec80d5a2efa8b53c68e9e07a823165d
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977032"
---
# <a name="windows-stop-error---0x00000074-bad-system-config-info"></a>Errore di arresto di Windows-informazioni di configurazione del sistema 0x00000074 non valide

Questo articolo illustra i passaggi per risolvere i problemi che non possono essere avviati da Windows e devono essere riavviati a causa di informazioni di configurazione del sistema non valide in una macchina virtuale (VM) di Azure.

## <a name="symptom"></a>Sintomo

Quando si usa la [diagnostica di avvio](./boot-diagnostics.md) per visualizzare lo screenshot della macchina virtuale, si noterà che lo screenshot Visualizza il codice di arresto di Windows **#0x00000074** o **BAD_SYSTEM_CONFIG_INFO**.

*Il computer ha riscontrato un problema e deve essere riavviato. È possibile riavviare.* 
 *Per ulteriori informazioni su questo problema e sulle possibili correzioni, http://windows.com/stopcode visitare* 
 *Se si chiama un utente del supporto tecnico, fornire queste informazioni:* 
 *Interrompi codice: BAD_SYSTEM_CONFIG_INFO*

  ![Il codice di arresto di Windows 0x00000074, che viene visualizzato anche come "BAD_SYSTEM_CONFIG_INFO". Windows informa l'utente che il PC ha riscontrato un problema e deve essere riavviato.](./media/windows-stop-error-bad-system-config-info/1.png)

## <a name="cause"></a>Causa

Il codice di arresto **BAD_SYSTEM_CONFIG_INFO** si verifica se l'hive del registro di **sistema** sembra danneggiato. Questo errore può essere causato da uno dei motivi seguenti:

- L'hive del registro di sistema non è stato chiuso correttamente.
- L'hive del registro di sistema è danneggiato.
- Mancano chiavi o valori del registro di sistema.

## <a name="solution"></a>Soluzione

### <a name="process-overview"></a>Panoramica del processo:

1. Creare e accedere a una macchina virtuale di ripristino.
1. Verificare il danneggiamento di hive.
1. Abilitare la console seriale e la raccolta di dump della memoria.
1. Ricreare la macchina virtuale.

> [!NOTE]
> Quando si verifica questo errore, il sistema operativo guest non è operativo. Per risolvere il problema, è possibile eseguire la risoluzione dei problemi in modalità offline.

### <a name="create-and-access-a-repair-vm"></a>Creare e accedere a una macchina virtuale di ripristino

1. Usare i passaggi da 1 a 3 dei [comandi di ripristino della macchina virtuale](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) per preparare una macchina virtuale di ripristino.
1. Verificare il danneggiamento di hive.
1. Usare Connessione Desktop remoto per connettersi alla macchina virtuale di ripristino.
1. Copiare la `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config` cartella e salvarla in una partizione del disco integra o in un'altra posizione sicura. Eseguire il backup di questa cartella come precauzione poiché si modificheranno i file del registro di sistema critici. 

> [!NOTE]
> Eseguire una copia della `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config` cartella come backup nel caso in cui sia necessario eseguire il rollback delle modifiche apportate al registro di sistema.

### <a name="check-for-hive-corruption"></a>Verificare il danneggiamento di hive

Le istruzioni seguenti consentono di determinare se la causa è dovuta a un danneggiamento di hive o se l'hive non è stato chiuso correttamente. Se hive non è stato chiuso correttamente, sarà possibile sbloccare il file e correggere la macchina virtuale.

1. Nella macchina virtuale di ripristino aprire l'applicazione **Editor del registro di sistema** . Digitare "REGEDIT" nella barra di ricerca di Windows per trovarlo.
1. Nell'editor del registro di sistema selezionare **HKEY_LOCAL_MACHINE** per evidenziarlo, quindi selezionare **file > Carica hive...**  dal menu.
1. Individuare `<VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM` e selezionare **Apri**.
1. Quando viene richiesto di immettere un nome, immettere **BROKENSYSTEM**.

   1. Se non è possibile aprire l'hive o se è vuoto, l'hive è danneggiato. Se l'hive è danneggiato, [aprire un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

     ![Si verifica un errore che informa che l'editor del registro di sistema non è in grado di caricare hive.](./media/windows-stop-error-bad-system-config-info/2.png)

   1. Se hive si apre normalmente, l'hive non è stato chiuso correttamente. Continuare con il passaggio 5.

1. Per correggere un hive che non è stato chiuso correttamente, evidenziare **BROKENSYSTEM** , quindi selezionare **file > Scarica hive...** per sbloccare il file.

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
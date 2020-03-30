---
title: Errore di arresto di Windows -
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5b3ed56a-5a11-4ff9-9ee8-76aea4a5689b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 9e4c4b9c809a626c71b4a7e9235d917b442be160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373362"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Errore di arresto di Windows - #0x000000EF "Processo critico è morto"

Questo articolo illustra i passaggi per risolvere i problemi in cui un processo critico muore durante l'avvio in una macchina virtuale di Azure.This article provides steps to resolve issues where a critical process dies during boot in an Azure VM.

## <a name="symptom"></a>Sintomo

Quando si utilizza diagnostica di [avvio](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) per visualizzare la schermata della macchina virtuale, si noterà che nella schermata viene visualizzato il *#0x000000EF* di errore con il messaggio *Processo critico morto*.

!["Si è verificato un problema ed è necessario riavviare il PC. È in corso la raccolta di alcune informazioni sull'errore, quindi sarà possibile eseguire il riavvio. (%di completamento) Se vuoi saperne di più, puoi cercare online in un secondo momento per questo errore: 0x000000EF"](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>Causa

In genere, ciò è dovuto a un errore critico del processo di sistema durante l'avvio. Ulteriori informazioni sui problemi di processo critici sono in "[Bug Check 0xEF: CRITICAL_PROCESS_DIED](https://docs.microsoft.com/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)".

## <a name="solution"></a>Soluzione

### <a name="process-overview"></a>Panoramica del processo:

1. Creare e accedere a una macchina virtuale di ripristino.
2. Correggere eventuali danneggiamenti del sistema operativo.
3. **Consigliato:** prima di ricompilare la macchina virtuale, abilitare la console seriale e la raccolta di immagine della memoria.
4. Ricompilare la macchina virtuale.

> [!NOTE]
> Quando si verifica questo errore di avvio, il sistema operativo guest non è operativo. Si starà risolvendo in modalità non in linea per risolvere questo problema.

### <a name="create-and-access-a-repair-vm"></a>Creare e accedere a una macchina virtuale di ripristinoCreate and Access a Repair VM

1. Usare [i passaggi da 1 a 3 dei comandi](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) di ripristino della macchina virtuale per preparare una macchina virtuale di ripristino.
2. L'uso di Connessione desktop remoto si connette alla macchina virtuale di ripristino.

### <a name="fix-any-os-corruption"></a>Correggere eventuali danneggiamenti del sistema operativoFix any OS Corruption

1. Aprire un prompt dei comandi con privilegi elevati.
2. Eseguire il seguente comando Controllo file di sistema (SFC):

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * Dove < BOOT DISK DRIVE > è il volume di avvio della macchina virtuale di ripristino (in genere "C:") e < DI > BROKEN DISK DRIVE sarà la lettera di unità per il disco collegato dalla macchina virtuale interrotta. Sostituire i simboli maggiore / minore di e il testo in essi contenuto, ad esempio "< testo qui >", con la lettera appropriata.

3. Usare quindi [il passaggio 5 dei comandi](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) di ripristino della macchina virtuale per riassemblare la macchina virtuale e verificare se viene avviato.
4. Se la macchina virtuale non è ancora in fase di avvio, continuare a raccogliere il file di immagine della memoria.

### <a name="collect-the-memory-dump-file"></a>Raccogliere il file di dump della memoriaCollect the Memory Dump File

Se il problema persiste dopo l'esecuzione di SFC, sarà necessaria l'analisi di un file di immagine della memoria per determinare la causa del problema. Per raccogliere il file di immagine della memoria, attenersi alla seguente procedura:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Collegare il disco del sistema operativo a una nuova macchina virtuale di ripristino

1. Usare [i passaggi da 1 a 3 dei comandi](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) di ripristino della macchina virtuale per preparare una nuova macchina virtuale di ripristino.
2. L'uso di Connessione desktop remoto si connette alla macchina virtuale di ripristino.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Individuare il file dump e inviare un ticket di supporto

3. Nella macchina virtuale di ripristino passare alla cartella Windows nel disco del sistema operativo collegato. Se la lettera del driver assegnata al disco del sistema operativo collegato è *F*, è necessario passare a *F:*
4. Individuare il file *memory.dmp* e quindi [inviare un ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) di supporto con il file di immagine della memoria.

   > [!NOTE]
   > Se non è possibile trovare il file di dump, completare i passaggi seguenti per abilitare la raccolta di immagine della memoria e la console seriale, quindi tornare a questa sezione e ripetere i passaggi dell'attività precedente per raccogliere il file di immagine della memoria.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Consigliato: prima di ricompilare la macchina virtuale, abilitare la raccolta di dump della memoria e della console seriale

Per abilitare la raccolta di immagine della memoria e la console seriale, eseguire lo script seguente:

1. Aprire una sessione del prompt dei comandi con privilegi elevati (Esegui come amministratore).
2. Eseguire i comandi seguenti:

   Abilita console seriale

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   Sostituire i simboli maggiore o minore di e il testo al loro interno, ad esempio "< testo qui >".

3. Verificare che lo spazio disponibile sul disco del sistema operativo sia tanto quanto la dimensione della memoria (RAM) nella macchina virtuale.

Se lo spazio sul disco del sistema operativo non è sufficiente, è necessario modificare il percorso in cui verrà creato il file di immagine della memoria e fare riferimento a qualsiasi disco dati collegato alla macchina virtuale che dispone di spazio libero sufficiente. Per modificare il percorso, sostituire "%SystemRoot%" con la lettera di unità (ad esempio, "F:") del disco dati nei comandi seguenti.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Configurazione consigliata per abilitare il dump del sistema operativo

**Carica disco del sistema operativo interrotto**:

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Abilita su ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Abilita su ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Scarica disco osinterrotto:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Ricompilare la macchina virtuale originaleRebuild the Original VM

Usare [il passaggio 5 dei comandi](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) di ripristino della macchina virtuale per riassemblare la macchina virtuale.

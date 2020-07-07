---
title: Errore di arresto di Windows-
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80373362"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Errore di arresto di Windows-#0x000000EF "processo critico morto"

Questo articolo illustra la procedura per risolvere i problemi relativi a un processo critico che si verifica durante l'avvio in una macchina virtuale di Azure.

## <a name="symptom"></a>Sintomo

Quando si usa la [diagnostica di avvio](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) per visualizzare lo screenshot della macchina virtuale, si noterà che lo screenshot Visualizza l'errore *#0x000000EF* con il *processo critico*del messaggio.

!["Si è verificato un problema ed è necessario riavviare il PC. È in corso la raccolta di alcune informazioni sull'errore, quindi sarà possibile eseguire il riavvio. (# #% completato) Per ulteriori informazioni, è possibile eseguire la ricerca online in un secondo momento per questo errore: 0x000000EF "](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>Causa

In genere, ciò è dovuto a un errore critico del processo di sistema durante l'avvio. Per ulteriori informazioni sui problemi relativi al processo critico, vedere la sezione relativa al[controllo dei bug 0xEF: CRITICAL_PROCESS_DIED](https://docs.microsoft.com/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died).

## <a name="solution"></a>Soluzione

### <a name="process-overview"></a>Panoramica del processo:

1. Creare e accedere a una macchina virtuale di ripristino.
2. Correzione di eventuali danneggiamenti del sistema operativo.
3. **Consigliato**: prima di ricreare la macchina virtuale, abilitare la console seriale e la raccolta di dump della memoria.
4. Ricreare la macchina virtuale.

> [!NOTE]
> Quando si verifica questo errore di avvio, il sistema operativo guest non è operativo. Per risolvere il problema, sarà necessario eseguire la risoluzione dei problemi in modalità offline.

### <a name="create-and-access-a-repair-vm"></a>Creare e accedere a una macchina virtuale di ripristino

1. Seguire i [passaggi da 1 a 3 dei comandi di ripristino della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) per preparare una macchina virtuale di ripristino.
2. Usare Connessione Desktop remoto per connettersi alla macchina virtuale di ripristino.

### <a name="fix-any-os-corruption"></a>Correzione di eventuali danneggiamenti del sistema operativo

1. Aprire un prompt dei comandi con privilegi elevati.
2. Eseguire il seguente comando del controllo file di sistema (SFC):

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * Dove < > unità disco di avvio è il volume di avvio della macchina virtuale di ripristino (in genere "C:") e < unità disco interruppe > sarà la lettera di unità per il disco collegato dalla VM interruppe. Sostituire i simboli maggiore di/minore di, nonché il testo contenuto al suo interno, ad esempio "< testo qui >", con la lettera appropriata.

3. Successivamente, usare il [passaggio 5 dei comandi di ripristino della macchina](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) virtuale per riassemblare la VM e verificare se viene avviata.
4. Se la macchina virtuale non è ancora in avvio, continuare a raccogliere il file di dump della memoria.

### <a name="collect-the-memory-dump-file"></a>Raccogli il file di dump della memoria

Se il problema persiste dopo l'esecuzione di SFC, sarà necessario analizzare un file di dump della memoria per determinare la causa del problema. Per raccogliere il file di dump della memoria, attenersi alla seguente procedura:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Connetti il disco del sistema operativo a una nuova macchina virtuale di ripristino

1. Usare [i passaggi 1-3 dei comandi di ripristino della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) per preparare una nuova macchina virtuale di ripristino.
2. Usare Connessione Desktop remoto per connettersi alla macchina virtuale di ripristino.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Individuare il file dump e inviare un ticket di supporto

3. Nella macchina virtuale di ripristino andare alla cartella Windows nel disco del sistema operativo collegato. Se la lettera di driver assegnata al disco del sistema operativo collegato è *F*, è necessario passare a *F:\Windows*.
4. Individuare il file *Memory. dmp* , quindi [inviare un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) con il file di dump della memoria.

   > [!NOTE]
   > Se non è possibile trovare il file di dump, completare i passaggi seguenti per abilitare la raccolta di dump della memoria e la console seriale, quindi tornare a questa sezione e ripetere i passaggi nell'attività precedente per raccogliere il file di dump della memoria.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Scelta consigliata: prima di ricompilare la macchina virtuale, abilitare la raccolta di dump della memoria e della console seriale

Per abilitare la raccolta di dump della memoria e la console seriale, eseguire lo script seguente:

1. Aprire una sessione del prompt dei comandi con privilegi elevati (Esegui come amministratore).
2. Eseguire i comandi seguenti:

   Abilita console seriale

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   Sostituire i simboli maggiore di o minore di, nonché il testo al suo interno, ad esempio "< testo qui >".

3. Verificare che lo spazio disponibile sul disco del sistema operativo sia pari alla dimensione della memoria (RAM) nella macchina virtuale.

Se lo spazio sul disco del sistema operativo non è sufficiente, è necessario modificare il percorso in cui verrà creato il file di dump della memoria e fare riferimento a qualsiasi disco dati collegato alla VM con spazio libero sufficiente. Per modificare il percorso, sostituire "% SystemRoot%" con la lettera di unità (ad esempio, "F:") del disco dati nei comandi seguenti.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Configurazione consigliata per abilitare il dump del sistema operativo

**Caricare disco del sistema operativo non funzionante**:

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Abilitare su ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Abilitare su ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Scaricare il disco del sistema operativo non funzionante:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Ricompilare la VM originale

Usare il [passaggio 5 dei comandi di ripristino della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) per riassemblare la macchina virtuale.

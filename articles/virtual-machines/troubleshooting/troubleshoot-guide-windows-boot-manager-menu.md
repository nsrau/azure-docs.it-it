---
title: Impossibile avviare la macchina virtuale Windows a causa di Windows Boot Manager
description: Questo articolo illustra la procedura per risolvere i problemi in cui Windows Boot Manager impedisce l'avvio di una macchina virtuale di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3598
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 2457952051f575306de46e3e8145cc26678a1ef8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526540"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>Impossibile avviare la macchina virtuale Windows a causa di Windows Boot Manager

Questo articolo illustra la procedura per risolvere i problemi in cui Windows Boot Manager impedisce l'avvio di una macchina virtuale (VM) di Azure.

## <a name="symptom"></a>Sintomo

La macchina virtuale è bloccata in attesa di un prompt utente e non viene avviata a meno che non venga richiesto manualmente.

Quando si usa la [diagnostica di avvio](./boot-diagnostics.md) per visualizzare lo screenshot della macchina virtuale, si noterà che nella schermata viene visualizzato Windows Boot Manager con il messaggio *scegliere un sistema operativo da avviare oppure premere TAB per selezionare uno strumento:*.

Figura 1
 
![Windows Boot Manager che indica "scegliere un sistema operativo da avviare oppure premere TAB per selezionare uno strumento:"](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>Causa

L'errore è dovuto a un flag BCD *DISPLAYBOOTMENU* in Gestione avvio Windows. Quando il flag è abilitato, Windows Boot Manager chiede all'utente durante il processo di avvio di selezionare il caricatore che desidera eseguire, causando un ritardo di avvio. In Azure questa funzionalità può essere aggiunta al tempo necessario per l'avvio di una macchina virtuale.

## <a name="solution"></a>Soluzione

Panoramica del processo:

1. Configurare per tempi di avvio più rapidi tramite la console seriale.
2. Creare e accedere a una macchina virtuale di ripristino.
3. Configurare per tempi di avvio più rapidi in una macchina virtuale di ripristino.
4. **Consigliato**: prima di ricreare la macchina virtuale, abilitare la console seriale e la raccolta di dump della memoria.
5. Ricreare la macchina virtuale.

### <a name="configure-for-faster-boot-time-using-serial-console"></a>Configurare per tempi di avvio più rapidi tramite la console seriale

Se si ha accesso alla console seriale, è possibile ottenere tempi di avvio più rapidi in due modi. Ridurre il tempo di attesa *DISPLAYBOOTMENU* oppure rimuovere completamente il flag.

1. Seguire le istruzioni per accedere alla [console seriale di Azure per Windows](./serial-console-windows.md) per accedere alla console basata su testo.

   > [!NOTE]
   > Se non si riesce ad accedere alla console seriale, passare a [creare e accedere a una macchina virtuale di ripristino](#create-and-access-a-repair-vm).

2. **Opzione A**: ridurre il tempo di attesa

   a. Il tempo di attesa viene impostato su 30 secondi per impostazione predefinita, ma può essere modificato in un tempo più veloce, ad esempio 5 secondi.

   b. Usare il comando seguente nella console seriale per modificare il valore di timeout:

      `bcdedit /set {bootmgr} timeout 5`

3. **Opzione B**: rimuovere il flag BCD

   a. Per evitare che la richiesta di visualizzazione del menu di avvio sia completa, immettere il comando seguente:

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > Se non è stato possibile usare la console seriale per configurare un tempo di avvio più veloce nei passaggi precedenti, è possibile continuare con i passaggi seguenti. Per risolvere il problema, è ora possibile eseguire la risoluzione dei problemi in modalità offline.

### <a name="create-and-access-a-repair-vm"></a>Creare e accedere a una macchina virtuale di ripristino

1. Seguire i [passaggi da 1 a 3 dei comandi di ripristino della macchina virtuale](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) per preparare una macchina virtuale di ripristino.
2. Usare Connessione Desktop remoto connettersi alla macchina virtuale di ripristino.

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>Configurare per tempi di avvio più rapidi in una macchina virtuale di ripristino

1. Apri una finestra del prompt dei comandi con privilegi elevati.
2. Immettere quanto segue per abilitare DisplayBootMenu:

   Usare questo comando per le **macchine virtuali di prima generazione**:

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   Usare questo comando per le **macchine virtuali di seconda generazione**:

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   Sostituire i simboli maggiore di o minore di, nonché il testo al suo interno, ad esempio "< testo qui >".

3. Modificare il valore di timeout in 5 secondi:

   Usare questo comando per le **macchine virtuali di prima generazione**:

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   Usare questo comando per le **macchine virtuali di seconda generazione**:

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   Sostituire i simboli maggiore di o minore di, nonché il testo al suo interno, ad esempio "< testo qui >".

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

Usare il [passaggio 5 dei comandi di ripristino della macchina virtuale](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) per riassemblare la macchina virtuale.

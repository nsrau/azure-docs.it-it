---
title: Impossibile avviare la macchina virtuale Windows a causa del gestore di avvio di WindowsWindows virtual machine cannot boot due to windows boot manager
description: In questo articolo viene illustrata la procedura per risolvere i problemi in cui Gestione avvio di Windows impedisce l'avvio di una macchina virtuale di Azure.This article provides steps to resolve issues where Windows Boot Manager prevents the booting of an Azure Virtual Machine.
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
ms.openlocfilehash: 5d2fb62870e2c41af635627f5d692f08c67f8394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373349"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>Impossibile avviare la macchina virtuale Windows a causa di Gestione avvio di Windows

In questo articolo viene illustrata la procedura per risolvere i problemi in cui Gestione avvio di Windows impedisce l'avvio di una macchina virtuale di Azure(VM).

## <a name="symptom"></a>Sintomo

La macchina virtuale è bloccata in attesa di un prompt dell'utente e non viene avviata a meno che non venga richiesto manualmente.

Quando si utilizza diagnostica di [avvio](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) per visualizzare la schermata della macchina virtuale, si noterà che nella schermata viene visualizzato Gestione avvio di Windows con il messaggio *Scegliere un sistema operativo da avviare oppure premere TAB per selezionare uno strumento:*.

Figura 1
 
![Gestione avvio di Windows che indica "Scegliere un sistema operativo da avviare o premere TAB per selezionare uno strumento:"](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>Causa

L'errore è dovuto a un flag BCD *displaybootmenu* in Gestione avvio di Windows. Quando il flag è abilitato, Gestione avvio di Windows richiede all'utente, durante il processo di avvio, di selezionare il caricatore che desidera eseguire, causando un ritardo di avvio. In Azure questa funzionalità può essere aggiunta al tempo necessario per avviare una macchina virtuale.

## <a name="solution"></a>Soluzione

Panoramica del processo:

1. Configurare il tempo di avvio più veloce utilizzando la console seriale.
2. Creare e accedere a una macchina virtuale di ripristino.
3. Configurare per un tempo di avvio più veloce in una macchina virtuale di ripristino.
4. **Consigliato:** prima di ricompilare la macchina virtuale, abilitare la console seriale e la raccolta di immagine della memoria.
5. Ricompilare la macchina virtuale.

### <a name="configure-for-faster-boot-time-using-serial-console"></a>Configurazione per tempi di avvio più rapidi tramite la console seriale

Se si ha accesso alla console seriale, è possibile ottenere tempi di avvio più rapidi in due modi. Diminuire il tempo di attesa *displaybootmenu* o rimuovere completamente il flag.

1. Seguire le istruzioni per accedere alla [Console seriale di Azure per Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) per accedere alla console basata su testo.

   > [!NOTE]
   > Se non è possibile accedere alla console seriale, passare a Crea e accedi a [una macchina virtuale di ripristino](#create-and-access-a-repair-vm).

2. **Opzione A**: Ridurre il tempo di attesa

   a. Il tempo di attesa è impostato su 30 secondi per impostazione predefinita, ma può essere modificato in un tempo più veloce (ad esempio 5 secondi).

   b. Utilizzare il seguente comando nella console seriale per regolare il valore di timeout:

      `bcdedit /set {bootmgr} timeout 5`

3. **Opzione B**: Rimuovere il flag BCD

   a. Per evitare del tutto il prompt Visualizza menu di avvio, immettere il seguente comando:

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > Se non è stato possibile utilizzare la console seriale per configurare un tempo di avvio più veloce nei passaggi precedenti, è invece possibile continuare con i passaggi seguenti. Ora si starà risolvendo la risoluzione dei problemi in modalità offline per risolvere questo problema.

### <a name="create-and-access-a-repair-vm"></a>Creare e accedere a una macchina virtuale di ripristinoCreate and Access a Repair VM

1. Usare [i passaggi da 1 a 3 dei comandi](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) di ripristino della macchina virtuale per preparare una macchina virtuale di ripristino.
2. Usare Connessione Desktop remoto per connettersi alla macchina virtuale di ripristino.

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>Configurare per un tempo di avvio più veloce in una macchina virtuale di ripristinoConfigure for Faster Boot Time on a Repair VM

1. Aprire un prompt dei comandi con privilegi elevati.
2. Immettete quanto segue per abilitare DisplayBootMenu:

   Usare questo comando per **le macchine virtuali di generazione 1:Use**this command for Generation 1 VMs:

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   Usare questo comando per **le macchine virtuali di generazione 2:Use**this command for Generation 2 VMs:

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   Sostituire i simboli maggiore o minore di e il testo al loro interno, ad esempio "< testo qui >".

3. Modificare il valore di timeout in 5 secondi:

   Usare questo comando per **le macchine virtuali di generazione 1:Use**this command for Generation 1 VMs:

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   Usare questo comando per **le macchine virtuali di generazione 2:Use**this command for Generation 2 VMs:

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   Sostituire i simboli maggiore o minore di e il testo al loro interno, ad esempio "< testo qui >".

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
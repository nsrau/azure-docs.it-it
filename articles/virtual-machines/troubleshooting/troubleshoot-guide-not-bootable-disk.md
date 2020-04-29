---
title: 'Errore di avvio: "non è un disco di avvio"'
description: Questo articolo illustra la procedura per risolvere i problemi in cui il disco non è avviabile in una macchina virtuale di Azure
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5d6db4e3-c2f5-40c7-afea-54edf745f5eb
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: v-mibufo
ms.openlocfilehash: 9f0c6350b89dcfecefcadcc166f7af35abc4b128
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80300979"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>Errore di avvio: questo non è un disco di avvio

Questo articolo illustra la procedura per risolvere i problemi in cui il disco non è avviabile in una macchina virtuale (VM) di Azure.

## <a name="symptoms"></a>Sintomi

Quando si usa la [diagnostica di avvio](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) per visualizzare lo screenshot della macchina virtuale, si noterà che nella schermata viene visualizzato un messaggio che indica che non si tratta di un disco di avvio. Inserire un floppy di avvio e premere un tasto qualsiasi per riprovare.. .'.

   Figura 1

   ![Nella figura 1 viene visualizzato il messaggio * "non si tratta di un disco di avvio. Inserire un floppy di avvio e premere un tasto qualsiasi per riprovare... "*](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>Causa

Questo messaggio di errore indica che il processo di avvio del sistema operativo non è riuscito a individuare una partizione di sistema attiva Questo errore può anche indicare la presenza di un riferimento mancante nell'archivio di dati configurazione di avvio (BCD), impedendone l'individuazione della partizione di Windows.

## <a name="solution"></a>Soluzione

### <a name="process-overview"></a>Panoramica del processo

1. Creare e accedere a una macchina virtuale di ripristino.
2. Impostare stato partizione su attivo.
3. Correggere la partizione del disco.
4. **Scelta consigliata**: prima di ricompilare la macchina virtuale, abilitare la raccolta di dump della memoria e della console seriale.
5. Ricompilare la VM originale.

   > [!NOTE]
   > Quando si verifica questo errore di avvio, il sistema operativo guest non è operativo. Per risolvere il problema, è necessario eseguire la risoluzione dei problemi in modalità offline.

### <a name="create-and-access-a-repair-vm"></a>Creare e accedere a una macchina virtuale di ripristino

1. Usare i passaggi 1-3 dei [comandi di ripristino della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) per preparare una macchina virtuale di ripristino.
2. Usare Connessione Desktop remoto connettersi alla macchina virtuale di ripristino.

### <a name="set-partition-status-to-active"></a>Imposta lo stato della partizione su attivo

Le macchine virtuali di prima generazione devono prima verificare che la partizione del sistema operativo, che include l'archivio BCD, sia contrassegnata come *attiva*. Se si dispone di una macchina virtuale di seconda generazione, andare avanti per [correggere la partizione del disco](#fix-the-disk-partition), perché il flag di *stato* è stato deprecato nella generazione successiva.

1. Aprire un prompt dei comandi con privilegi elevati *(cmd. exe)*.
2. Immettere *DiskPart* per avviare lo strumento Diskpart.
3. Immettere il *disco elenco* per elencare i dischi del sistema e identificare il VHD del sistema operativo collegato.
4. Una volta individuato il VHD del sistema operativo collegato, immettere *SEL disk #* per selezionare il disco.  Vedere la figura 2, dove il disco 1 è il VHD del sistema operativo collegato.

   Figura 2

   ![Nella figura 2 è illustrata la finestra * DISKPART * che mostra l'output del comando list disk, disk 0 e Disk 1 visualizzati nella tabella.  Mostra anche l'output del comando SEL Disk 1, il disco 1 è il disco selezionato](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. Una volta selezionato il disco, immettere *List Partition* per elencare le partizioni del disco selezionato.
6. Una volta identificata la partizione di avvio, immettere *SEL Partition #* per selezionare la partizione.  In genere, la partizione di avvio avrà dimensioni di circa 350 MB.  Vedere la figura 3, dove partition 1 è la partizione di avvio.

   Figura 3

   ![Nella figura 3 viene illustrata la finestra * DISKPART * con l'output del comando * list partition *. La partizione 1 e la partizione 2 vengono visualizzate nella tabella. Viene inoltre visualizzato l'output del comando * SEL partition 1 *, quando Partition 1 è il disco selezionato.](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. Immettere ' detail partition ' per verificare lo stato della partizione. Vedere la figura 4, in cui la partizione è *attiva: No*o la figura 5, dove la partizione è' Active: Yes '.

   Figura 4

   ![Nella figura 4 viene illustrata la finestra * DISKPART * con l'output del comando * detail partition *, quando la partizione 1 è impostata su * Active: No *](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   Figura 5

   ![Nella figura 5 viene illustrata la finestra * DISKPART * con l'output del comando * detail partition *, quando Partition 1 è impostato su * Active: Yes *.](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. Se la partizione **non è attiva**, immettere *attivo* per modificare il flag *attivo* .
9. Verificare che la modifica dello stato sia stata eseguita correttamente digitando *dettaglio partizione*.

   Figura 6

   ![Nella figura 6 viene illustrata la finestra DiskPart con l'output del comando * detail partition *, quando la partizione 1 è impostata su * Active: Yes *](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. Immettere *Exit* per chiudere lo strumento DiskPart e salvare le modifiche alla configurazione.

### <a name="fix-the-disk-partition"></a>Correggere la partizione del disco

1. Aprire un prompt dei comandi con privilegi elevati (cmd. exe).
2. Usare il comando seguente per eseguire *chkdsk* sui dischi e correggere gli errori:

   `chkdsk <DRIVE LETTER>: /f`

   L'aggiunta dell'opzione di comando '/f ' consente di correggere eventuali errori sul disco. Assicurarsi di sostituire <DRIVE LETTER> con la lettera del disco rigido virtuale del sistema operativo collegato.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Scelta consigliata: prima di ricompilare la macchina virtuale, abilitare la raccolta di dump della memoria e della console seriale

Per abilitare la raccolta di dump della memoria e la console seriale, eseguire lo script seguente:

1. Aprire una sessione del prompt dei comandi con privilegi elevati (Esegui come amministratore).
2. Eseguire i comandi seguenti:

   Abilita console seriale

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Verificare che lo spazio disponibile sul disco del sistema operativo sia pari alla dimensione della memoria (RAM) nella macchina virtuale.

   Se lo spazio sul disco del sistema operativo non è sufficiente, è necessario modificare il percorso in cui verrà creato il file di dump della memoria e fare riferimento a qualsiasi disco dati collegato alla VM con spazio libero sufficiente. Per modificare il percorso, sostituire "% SystemRoot%" con la lettera di unità (ad esempio, "F:") del disco dati nei comandi seguenti.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Configurazione consigliata per abilitare il dump del sistema operativo

**Carica disco del sistema operativo danneggiato**:

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Abilita in ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Abilita in ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Scarica disco del sistema operativo danneggiato:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Ricompilare la VM originale

Usare [il passaggio 5 dei comandi di ripristino della macchina](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) virtuale per riassemblare la macchina virtuale.

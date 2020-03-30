---
title: Errore di avvio – "questo non è un disco di avvio"
description: Questo articolo illustra i passaggi per risolvere i problemi in cui il disco non è avviabile in una macchina virtuale di AzureThis article provides steps to resolve issues where the disk isn't bootable in an Azure Virtual Machine
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80300979"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>Errore di avvio – Questo non è un disco di avvio

Questo articolo illustra la procedura per risolvere i problemi in cui il disco non è avviabile in una macchina virtuale di Azure.This article provides steps to resolve issues where the disk isn't bootable in an Azure Virtual Machine (VM).

## <a name="symptoms"></a>Sintomi

Quando si utilizza diagnostica di [avvio](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) per visualizzare la schermata della macchina virtuale, si noterà che la schermata viene visualizzato un prompt con il messaggio 'Questo non è un disco avviabile. Inserire un floppy di avvio e premere un tasto qualsiasi per riprovare...".

   Figura 1

   ![Figura 1 Viene illustrato il messaggio "Questo non è un disco di avvio. Inserire un floppy avviabile e premere un tasto qualsiasi per riprovare..."](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>Causa

Questo messaggio di errore indica che il processo di avvio del sistema operativo non è riuscito a individuare una partizione di sistema attiva. Questo errore potrebbe anche significare che è presente un riferimento mancante nell'archivio dei dati di configurazione di avvio (BCD), impedendogli di individuare la partizione di Windows.

## <a name="solution"></a>Soluzione

### <a name="process-overview"></a>Panoramica del processo

1. Creare e accedere a una macchina virtuale di ripristino.
2. Impostare Stato partizione su Attivo.Set Partition Status to Active.
3. Correggere la partizione del disco.
4. **Consigliato:** prima di ricompilare la macchina virtuale, abilitare la console seriale e la raccolta di immagine della memoria.
5. Ricompilare la macchina virtuale originale.

   > [!NOTE]
   > Quando si verifica questo errore di avvio, il sistema operativo guest non è operativo. La risoluzione dei problemi in modalità offline verrà la risoluzione dei problemi.

### <a name="create-and-access-a-repair-vm"></a>Creare e accedere a una macchina virtuale di ripristinoCreate and Access a Repair VM

1. Usare i passaggi da 1 a 3 dei comandi di ripristino della [macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) per preparare una macchina virtuale di ripristino.
2. L'uso di Connessione desktop remoto si connette alla macchina virtuale di ripristino.

### <a name="set-partition-status-to-active"></a>Impostare lo stato della partizione su Attivo

Le macchine virtuali di generazione 1 devono innanzitutto verificare che la partizione del sistema operativo, che contiene l'archivio BCD, sia contrassegnata come *attiva.* Se si dispone di una macchina virtuale di generazione 2, passare a [Correggere la partizione del disco](#fix-the-disk-partition), poiché il flag di *stato* è stato deprecato nella generazione successiva.

1. Aprire un prompt dei comandi con privilegi elevati *(cmd.exe)*.
2. Immettere *diskpart* per avviare lo strumento DISKPART.
3. Immettere *il disco dell'elenco* per elencare i dischi nel sistema e identificare il disco rigido virtuale del sistema operativo collegato.
4. Una volta individuato il disco rigido virtuale del sistema operativo collegato, immettere *sel disk per* selezionare il disco.  Vedere Figura 2, dove disco 1 è il disco rigido virtuale del sistema operativo collegato.

   Figura 2

   ![Nella figura 2 è illustrata la finestra "DISKPART" che mostra l'output del comando del disco di elenco, del disco 0 e del disco 1 visualizzati nella tabella.  Mostra anche l'output del comando sel disk 1, disco 1 è il disco selezionato](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. Una volta selezionato il disco, immettere *list partition* per elencare le partizioni del disco selezionato.
6. Una volta identificata la partizione di avvio, immettere *sel partition per* selezionare la partizione.  Di solito la partizione di avvio sarà di circa 350 MB di dimensioni.  Vedere Figura 3, dove partizione 1 è la partizione di avvio.

   Figura 3

   ![Nella figura 3 è illustrata la finestra "DISKPART" con l'output del comando "list partition". La partizione 1 e la partizione 2 vengono visualizzate nella tabella. Viene inoltre illustrato l'output del comando partizione 1, quando la partizione 1 è il disco selezionato.](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. Immettere 'detail partition' per controllare lo stato della partizione. Vedere Figura 4, in cui la partizione è *attiva: No*, o Figura 5 , in cui la partizione è 'Attivo: Sì'.

   Figura 4

   ![Nella Figura 4 viene illustrata la finestra "DISKPART" con l'output del comando "Detail partition" quando la partizione 1 è impostata su "Active: No"](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   Figura 5

   ![Nella Figura 5 viene illustrata la finestra "DISKPART" con l'output del comando "Detail partition", quando la partizione 1 è impostata su "Active: Yes".](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. Se la partizione non è **attiva**, immettere *active* per modificare il flag *Attivo.*
9. Verificare che la modifica dello stato sia stata eseguita correttamente digitando *detail partition*.

   Figura 6

   ![Figura 6 Mostra la finestra diskpart con l'output del comando di partizione di dettaglio, quando la partizione 1 è impostata su "Attivo: Sì"](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. Immettere *exit* per chiudere lo strumento DISKPART e salvare le modifiche alla configurazione.

### <a name="fix-the-disk-partition"></a>Correggere la partizione del discoFix the Disk Partition

1. Aprire un prompt dei comandi con privilegi elevati (cmd.exe).
2. Utilizzare il comando seguente per eseguire *CHKDSK* sui dischi e correggere gli errori:

   `chkdsk <DRIVE LETTER>: /f`

   L'aggiunta dell'opzione di comando '/f' correggerà eventuali errori sul disco. Assicurarsi di <DRIVE LETTER> sostituire con la lettera del disco rigido virtuale del sistema operativo allegato.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Consigliato: prima di ricompilare la macchina virtuale, abilitare la raccolta di dump della memoria e della console seriale

Per abilitare la raccolta di immagine della memoria e la console seriale, eseguire lo script seguente:

1. Aprire una sessione del prompt dei comandi con privilegi elevati (Esegui come amministratore).
2. Eseguire i comandi seguenti:

   Abilita console seriale

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

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

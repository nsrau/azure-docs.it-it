---
title: "Risolvere l'errore di Windows Boot Manager: 0xC0000225 \"Stato non trovato\""
description: Procedura per risolvere i problemi in cui si verifica il codice errore 0xC0000225 in una macchina virtuale di Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: c17899a4-b270-4725-9530-0dcd829b178c
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: 817f9e362e639cbb8f0cc79607c376c0e8216ec7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663729"
---
# <a name="troubleshoot-windows-boot-manager-error----0xc0000225-status-not-found"></a>Risolvere l'errore di Windows Boot Manager: 0xC0000225 "Stato non trovato"
 
Questo articolo illustra la procedura per risolvere i problemi in cui si verifica il codice errore 0xC0000225 in una macchina virtuale di Azure. Questo errore indica che lo stato o l'oggetto non è stato trovato.

## <a name="symptoms"></a>Sintomi

Quando si usa la [diagnostica di avvio](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) per visualizzare lo screenshot della macchina virtuale, si noterà che lo screenshot mostra l'errore Avvio di Windows non riuscito con il codice di stato *0xc0000225*.

Il file associato a questo codice errore indicherà i passaggi da eseguire per risolvere il problema. Individuare il testo della sezione **File:** per determinare la linea di azione appropriata.

### <a name="drivers-os-related-or-third-party"></a>Driver, correlato al sistema operativo o di terze parti

Se il file è presente, ma fa riferimento a un driver (come illustrato) o è correlato al sistema operativo o di terze parti, attenersi alla procedura riportata nella sezione [Ripristinare il file di sistema](#repair-the-system-file).
 
Nell'immagine seguente, Windows Boot Manager indica "Avvio di Windows non riuscito. Il problema potrebbe essere dovuto a una modifica recente apportata all'hardware o al software". L'immagine mostra anche lo Stato come "0xc0000225", **File:** come `\windows\System32\drivers\atapi.sys` e **Info:** come "Impossibile caricare il sistema operativo. Driver di sistema critico mancante o con errori".

![Windows Boot Manager indica "Avvio di Windows non riuscito. Il problema potrebbe essere dovuto a una modifica recente apportata all'hardware o al software". Mostra anche lo Stato come "0xc0000225", il File come "\windows\System32\drivers\atapi.sys" e nella sezione Info indica: "Impossibile caricare il sistema operativo. Driver di sistema critico mancante o con errori".](./media/troubleshoot-boot-error-status-not-found/1.png)

### <a name="no-file"></a>Nessun file

Se viene visualizzato il codice di stato, ma non viene visualizzato alcun file, seguire la procedura descritta nella sezione [Aggiungere la variabile OSDEVICE](#add-the-osdevice-variable).

Nell'immagine seguente, Windows Boot Manager indica "Avvio di Windows non riuscito. Il problema potrebbe essere dovuto a una modifica recente apportata all'hardware o al software". L'immagine mostra anche lo Stato come "0xc0000225" e **Info:** come "Selezione di avvio non riuscita. Un dispositivo necessario è inaccessibile".

![Windows Boot Manager indica "Avvio di Windows non riuscito. Il problema potrebbe essere dovuto a una modifica recente apportata all'hardware o al software". Mostra anche lo Stato come "0xc0000225" e nella sezione Info indica: "Selezione di avvio non riuscita. Un dispositivo necessario è inaccessibile".](./media/troubleshoot-boot-error-status-not-found/2.png)

### <a name="registry-file"></a>File del Registro di sistema

Se si riferisce a uno dei file del Registro di sistema, ad esempio \windows\system32\config\system, seguire la procedura descritta nella sezione [Creare un ticket di supporto](#contact-support).
 
Nell'immagine seguente, Windows Boot Manager indica "Avvio di Windows non riuscito. Il problema potrebbe essere dovuto a una modifica recente apportata all'hardware o al software". L'immagine mostra anche lo Stato come "0xc0000225", il File come `\windows\System32\config\system` e **Info:** come "Impossibile caricare il sistema operativo. File del Registro di sistema mancante o con errori".

![Windows Boot Manager indica "Avvio di Windows non riuscito. Il problema potrebbe essere dovuto a una modifica recente apportata all'hardware o al software". Mostra anche lo Stato come "0xc0000225", il File come "\windows\System32\config\system" e nella sezione Info indica: "Impossibile caricare il sistema operativo. File del Registro di sistema mancante o con errori".](./media/troubleshoot-boot-error-status-not-found/3.png)

Nella figura seguente, la schermata di ripristino indica "È necessario ripristinare il PC/dispositivo. Impossibile caricare il sistema operativo. File del Registro di sistema mancante o con errori". L'immagine mostra anche il codice errore come "0xc0000225" e il File come `\windows\System32\config\system`.

![La schermata di ripristino indica "È necessario ripristinare il PC/dispositivo. Impossibile caricare il sistema operativo. File del Registro di sistema mancante o con errori". Mostra anche il codice errore come "0xc0000225" e il File come "\windows\System32\config\system".](./media/troubleshoot-boot-error-status-not-found/4.png)

## <a name="causes"></a>Cause

### <a name="missing-binary"></a>File binario mancante

È possibile che si sia verificato un file binario mancante o danneggiato nel file di sistema **(.sys)** .

### <a name="bcd-corruption-or-improper-vhd-migration"></a>Danneggiamento dei dati configurazione di avvio o migrazione del disco rigido virtuale danneggiata non corretta

In questo caso, i **dati configurazione di avvio** sono danneggiati o è stata eseguita la migrazione del **disco rigido virtuale** da un ambiente locale, ma la preparazione del disco non è stata eseguita correttamente. Il risultato è che la variabile **OSDEVICE** manca e deve essere aggiunta.

### <a name="registry-hive-corruption"></a>Danneggiamento dell'hive del Registro di sistema

Il danneggiamento dell'hive del Registro di sistema potrebbe essere dovuto a uno dei motivi seguenti:

- Errore dell'hive
- L'hive viene montato ma è vuoto
- L'hive non è stato chiuso correttamente
## <a name="solution"></a>Soluzione

### <a name="process-overview"></a>Panoramica del processo

1. Creare e accedere a una macchina virtuale di ripristino.
1. Selezionare una soluzione:
   - [Ripristinare il file di sistema](#repair-the-system-file)
   - [Aggiungere la variabile OSDevice](#add-the-osdevice-variable)
   - [Creare un ticket di supporto](#contact-support)
1. Abilitare la console seriale e la raccolta di dump della memoria.
1. Ricreare la macchina virtuale.

### <a name="create-and-access-a-repair-vm"></a>Creare e accedere a una macchina virtuale di ripristino

1. Usare i passaggi da 1 a 3 dei [comandi di ripristino della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) per preparare una macchina virtuale di ripristino.
1. Usare Connessione Desktop remoto per connettersi alla macchina virtuale di ripristino.

### <a name="select-a-solution"></a>Selezionare una soluzione

1. Aprire un prompt dei comandi con privilegi elevati.
1. In base al sintomo identificato in precedenza, seguire i passaggi nella soluzione corrispondente. È possibile saltare i passaggi delle altre soluzioni, poiché non si applicano al problema riscontrato:

- [Ripristinare il file di sistema](#repair-the-system-file)
- [Aggiungere la variabile OSDevice](#add-the-osdevice-variable)
- [Creare un ticket di supporto](#contact-support)

### <a name="repair-the-system-file"></a>Ripristinare il file di sistema

1. Usando il disco rigido virtuale collegato, accedere al percorso del file binario mostrato nello screenshot della macchina virtuale (VM).
1. Fare clic con il pulsante destro del mouse sul file, selezionare **Proprietà** e quindi selezionare la scheda **Dettagli** per visualizzare le informazioni sul file.
   1. Prendere nota della versione del file, come mostrato nell'immagine seguente:

      ![La finestra delle proprietà del file "cng.sys", con la versione del file evidenziata.](./media/troubleshoot-boot-error-status-not-found/5.png)

1. Rinominare il file in **< BINARY.SYS >.old**, sostituendo **< BINARY.SYS >** con il nome del file.

   Per l'immagine nel passaggio precedente, il file **cng.sys** verrà rinominato in **cng.sys.old**

   > [!NOTE]
   > Se si prova a rinominare il file e si riceve il messaggio "Il file è danneggiato e illeggibile", [contattare il supporto tecnico per l'assistenza](https://azure.microsoft.com/support/create-ticket/), poiché questa soluzione non funzionerà.

1. Dopo aver rinominato il file danneggiato, correggere il file ripristinandolo dal relativo repository interno.
   1. Avviare una sessione **CMD**.
   1. Passare a **\windows\winsxs**.

   1. Cercare il file binario che si trova all'inizio di questa sezione usando il comando seguente:

      `dir <BINARY WITH ".SYS" EXTENSION>  /s`

      Questo comando elenca tutte le versioni del file presenti nel computer, fornendo la cronologia dei percorsi di tale componente.
      
      Ad esempio, **dir cng.sys** verrà rinominato **dir cng.sys /s**

   1. Scegliere la versione più recente del file nell'elenco (o quella che si preferisce) e copiare il file nella cartella **windows\system32** usando il percorso precedente e il comando seguente:

      `copy <drive>:\Windows\WinSxS\<DIRECTORY WHERE FILE IS>\<BINARY WITH ".SYS" EXTENSION> <DRIVE>:\Windows\System32\Drivers\`

      > [!NOTE]
      > Se il file binario più recente non funziona, provare con una versione precedente o con una qualsiasi versione in cui è presente un file stabile, come una versione precedente a una patch.

      Se, ad esempio, il file binario cercato è **cmimcext.sys**, l'unità in errore è l'unità **F:** ed è stata appena eseguita una ricerca della versione più recente, viene visualizzata l'immagine seguente, in cui una query nel prompt dei comandi di `dir cmim* /s` individua la versione più recente del file cmimcext.sys.

      ![Una query nel prompt dei comandi di "dir cmim* /s" per individuare la versione più recente del file cmimcext.sys.](./media/troubleshoot-boot-error-status-not-found/6.png)

      Nell'immagine di esempio precedente la query è stata eseguita in **C:** , mentre la lettera di unità deve essere quella dell'unità in errore, **F:** , ovvero il disco del sistema operativo collegato come disco dati nella macchina virtuale di ripristino.

      Il comando risultante per copiare il file sarà: `copy F:\Windows\WinSxS\amd64_xxxxxx\cmimcext.sys F:\Windows\System32\Drivers`.

Al termine di questa attività, continuare con [Abilitare la console seriale e la raccolta di dump della memoria](#enable-the-serial-console-and-memory-dump-collection).

### <a name="add-the-osdevice-variable"></a>Aggiungere la variabile OSDEVICE

Raccogliere le informazioni di configurazione di avvio correnti e prendere nota dell'identificatore nella partizione attiva. Queste informazioni vengono quindi usate per aggiungere la variabile **OSDEVICE**, seguendo le istruzioni per la generazione della macchina virtuale.

Se la raccolta di queste informazioni restituisce un errore in cui non è presente alcun file **\boot\bcd**, usare le istruzioni riportate in [Ripristinare il file di sistema](#repair-the-system-file). 

1. Per le macchine virtuali di prima generazione aprire un prompt dei comandi con privilegi elevati come amministratore e immettere il comando seguente:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /enum`

   Questa immagine mostra il caricatore di avvio di Windows in una macchina virtuale di prima generazione, con l'attributo identifier evidenziato. L'attributo identifier evidenziato mostra una stringa alfanumerica univoca.

   ![Caricatore di avvio di Windows visualizzato in una macchina virtuale di prima generazione con l'attributo identifier evidenziato. L'attributo identifier evidenziato mostra una stringa alfanumerica univoca.](./media/troubleshoot-boot-error-status-not-found/7.png)

   Prendere nota dell'identificatore del caricatore di avvio di Windows, il cui percorso è **\windows\system32\winload.exe**.

1. Per le macchine virtuali di seconda generazione, verificare che il disco del sistema operativo sia online e che siano state assegnate le lettere di unità di partizione. Una volta verificate queste condizioni, raccogliere le informazioni di configurazione di avvio.
   1. In **Windows Search** digitare **Gestione disco** e quindi aprire la console Gestione disco. Usare questa console per identificare il numero del disco collegato nella macchina virtuale di ripristino e la partizione EFI (Extensible Firmware Interface) che contiene l'archivio dei dati di configurazione di avvio.

   Nell'immagine seguente, il Disco 2 è il numero del disco collegato alla macchina virtuale di ripristino. L'immagine mostra anche la partizione di sistema EFI nel Disco 2, che ha una dimensione di 100 MB e non ha una lettera assegnata.

   ![Il Disco 2 viene mostrato come numero del disco collegato alla macchina virtuale di ripristino. Viene mostrata anche la partizione di sistema EFI nel Disco 2, che ha una dimensione di 100 MB e non ha una lettera assegnata.](./media/troubleshoot-boot-error-status-not-found/8.png)

   1. Aprire un prompt dei comandi con privilegi elevati come amministratore e immettere i comandi seguenti:
      1. Aprire lo strumento **DISKPART** usando il comando `diskpart`.
      1. Elencare tutti i dischi, quindi selezionare il disco collegato identificato nel passaggio precedente:
      
         ```
         list disk
         sel disk <DISK #>
         ```

         La figura seguente mostra i risultati della visualizzazione dell'elenco e della selezione di un disco. Vengono elencati Disco 0 (127 GB | Online), Disco 1 (32 GB | Online) e Disco 2 (127 GB | Online), con la selezione del Disco 2 usando il comando `sel disk 2`.

         ![I risultati della visualizzazione dell'elenco e quindi della selezione di un disco. Vengono elencati Disco 0 (127 GB | Online), Disco 1 (32 GB | Online) e Disco 2 (127 GB | Online), con la selezione del Disco 2.](./media/troubleshoot-boot-error-status-not-found/9.png)

      1. Elencare le partizioni e selezionare la partizione di sistema EFI identificata nel passaggio precedente:
      
         ```
         list partition
         sel partition <PARTITION #>
         ```

         La figura seguente mostra i risultati della visualizzazione dell'elenco e della selezione di una partizione. Vengono elencate Partizione 1 (Riservata | 16 MB), Partizione 2 (Sistema | 100 MB) e Partizione 3 (Primaria | 126 GB), con la Partizione 2 selezionata usando il comando `sel part 2`.

         ![I risultati della visualizzazione dell'elenco e quindi della selezione di una partizione. Vengono elencate Partizione 1 (Riservata | 16 MB), Partizione 2 (Sistema | 100 MB) e Partizione 3 (Primaria | 126 GB), con la Partizione 2 selezionata.](./media/troubleshoot-boot-error-status-not-found/10.png)

      1. Assegnare una lettera alla partizione EFI usando il comando `assign`.

         Nell'immagine seguente, il `assign` comando e la nuova unità **SYSTEM (F:)** vengono entrambi visualizzati in Esplora file.

         ![Il comando assign e la nuova unità SYSTEM (F:) vengono visualizzati in Esplora file.](./media/troubleshoot-boot-error-status-not-found/11.png)

      1. Elencare i dati dell'archivio dei dati configurazione di avvio usando il comando seguente:
      
         `bcdedit /store <LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

         Nell'immagine seguente, il caricatore di avvio di Windows si trova in una macchina virtuale di seconda generazione con l'attributo identifier evidenziato. Il valore dell'attributo identifier evidenziato è **{default}** .

         ![Caricatore di avvio di Windows visualizzato in una macchina virtuale di seconda generazione con l'attributo identifier evidenziato. L'attributo identifier evidenziato mostra default come valore.](./media/troubleshoot-boot-error-status-not-found/12.png)

         Prendere nota dell'identificatore del caricatore di avvio di Windows, il cui percorso è **\windows\system32\winload.efi**.

1. Si noti che nella partizione attiva manca la variabile OSDEVICE:

   ![Gli attributi di Windows Boot Manager e del caricatore di avvio di Windows sono elencati nel prompt dei comandi, con l'attributo del dispositivo del sistema operativo mancante.](./media/troubleshoot-boot-error-status-not-found/13.png)
   
   In questa immagine gli attributi di Windows Boot Manager e del caricatore di avvio di Windows sono elencati nel prompt dei comandi, ma manca l'attributo OSDEVICE.

1. Aggiungere la variabile OSDEVICE in base alle informazioni seguenti:

   Per i dischi del sistema operativo a partizione singola, aggiungere `BOOT`.

   > [!NOTE]
   > La cartella di avvio sarà nella stessa partizione della cartella di Windows **\windows**.
   > - La cartella di avvio per le macchine virtuali di prima generazione è **\boot\bcd**.
   > - La cartella di avvio per le macchine virtuali di seconda generazione è **EFI\Microsoft\boot\bcd**.

   Per le macchine virtuali di prima generazione, immettere il comando seguente:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   Per le macchine virtuali di seconda generazione, immettere il comando seguente:

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   Per i dischi del sistema operativo con più partizioni, aggiungere `PARTITION=<LETTER OF WINDOWS FOLDER>:`.

   > [!NOTE]
   > La cartella di avvio sarà probabilmente in una partizione diversa da quella della cartella di Windows **\windows**.
   > - La cartella di avvio per le macchine virtuali di prima generazione è **\boot\bcd**.
   > - La cartella di avvio per le macchine virtuali di seconda generazione è **EFI\Microsoft\boot\bcd**.

   Per le macchine virtuali di prima generazione, immettere il comando seguente:

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

   Per le macchine virtuali di seconda generazione, immettere il comando seguente:

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {< IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

Al termine di questa attività, continuare con [Abilitare la console seriale e la raccolta di dump della memoria](#enable-the-serial-console-and-memory-dump-collection).

### <a name="contact-support"></a>Contattare il supporto tecnico

L'errore del **file di Registro di sistema** ha una soluzione, ma è necessario [creare un ticket di supporto](https://azure.microsoft.com/support/create-ticket/) per ulteriore assistenza.

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

Usare il [passaggio 5 dei comandi di ripristino della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) per ricreare la macchina virtuale.

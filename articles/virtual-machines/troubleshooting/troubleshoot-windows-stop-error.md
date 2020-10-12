---
title: Errore di arresto di Windows - Memoria esaurita
description: Questo articolo illustra i passaggi per risolvere i problemi di avvio di Windows e visualizza lo stato "stato senza memoria".
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: d29c7e49-4578-43c8-b829-831da4e48932
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 33b4c59e14301e496d0eddafa7bdfdf201b7aa29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87005906"
---
# <a name="windows-stop-error---status-no-memory"></a>Errore di arresto di Windows - Memoria esaurita

Questo articolo illustra i passaggi per risolvere i problemi in cui non è possibile avviare Windows e visualizza il codice di stato o di errore #0xC0000017, noto anche come "stato senza memoria".

## <a name="symptom"></a>Sintomo

Quando si usa la [diagnostica di avvio](./boot-diagnostics.md) per visualizzare lo screenshot della macchina virtuale (VM), si noterà che nella schermata è visualizzato il codice di errore: `0xC0000017` . A seconda della versione di Windows in esecuzione, questo codice può essere visualizzato in **Gestione avvio Windows** o nella **schermata di ripristino**.

   **Windows Boot Manager**

   ![Windows Boot Manager indica che non è stato possibile avviare Windows. Potrebbe trattarsi di una modifica hardware o software recente. Scorrendo verso il basso, viene visualizzato il codice di stato "0xC0000017", nonché le informazioni che indicano che l'applicazione o il sistema operativo non è stato caricato perché un file necessario manca o contiene errori ".](./media/troubleshoot-windows-stop-error/1.png)

   **Schermata di ripristino**
 
   ![Schermata di ripristino che informa che è necessario ripristinare il PC/dispositivo. La memoria disponibile non è sufficiente per creare un dispositivo ramdisk ". Dovrebbe essere visualizzato anche il codice di errore "0xC0000017".](./media/troubleshoot-windows-stop-error/2.png)

## <a name="cause"></a>Causa

Il disco del sistema operativo è pieno, troppo frammentato oppure il sistema operativo non è in grado di accedere alla memoria o al file di paging o a entrambi.

## <a name="solution"></a>Soluzione

### <a name="process-overview"></a>Panoramica del processo:

1. Creare e accedere a una macchina virtuale di ripristino
1. Liberare spazio sul disco
1. Pulire la memoria non valida dall'archivio BCD
1. Ripristinare il percorso predefinito del file di paging
1. Abilitare la raccolta di immagini di memoria e console seriale
1. Ricreare la macchina virtuale

> [!NOTE]
> Quando si verifica questo errore, il sistema operativo guest non è operativo. Questo problema sarà risolto in modalità offline.

### <a name="create-and-access-a-repair-vm"></a>Creare e accedere a una macchina virtuale di ripristino

1. Seguire i [passaggi da 1 a 3 dei comandi di ripristino della macchina virtuale](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) per preparare una macchina virtuale di ripristino.
1. Usare Connessione Desktop remoto per connettersi alla macchina virtuale di ripristino.

### <a name="for-generation-2-vms-assign-a-letter-to-the-extensible-firmware-interface-efi-partition"></a>Per le macchine virtuali di seconda generazione, assegnare una lettera alla partizione Extensible Firmware Interface (EFI):

Se si usa una macchina virtuale di seconda generazione, la partizione EFI del disco collegato potrebbe non avere una lettera assegnata. Prima di procedere con questa guida alla risoluzione dei problemi, è necessario attenersi alla procedura seguente per assegnare una lettera alla partizione.

1. In Windows Search immettere `diskmgmt` e aprire la **console di gestione disco**.
1. Identificare il disco rotto collegato alla macchina virtuale di ripristino. In genere, questo disco è elencato per ultimo nella console e ha il valore numerico più elevato.
1. Si noti che in tale disco è presente una partizione che contiene la **partizione di sistema EFI**, a cui non è assegnato un valore lettera, ad esempio l'unità *F:*. Se sono state assegnate tutte le partizioni, è possibile saltare per liberare spazio sul disco. In caso contrario, continuare a assegnare una lettera a questo disco.

   ![La console di gestione disco con il disco collegato "Disk 2", nonché la partizione non assegnata 100 MB ed è la "partizione di sistema EFI".](./media/troubleshoot-windows-stop-error/3.png)

1. Aprire un prompt dei comandi con privilegi elevati come amministratore e immettere `diskpart` per avviare lo strumento **DiskPart** .
1. Immettere i comandi seguenti:

   ```
   list disk
   sel disk <NUMBER OF THE ATTACHED DISK>
   list partition
   sel partition <NUMBER OF THE SYSTEM (EFI) PARTION>
   assign
   ```
   
   - Nel comando sostituire `<NUMBER OF THE ATTACHED DISK>` con il numero di disco identificato nel passaggio 2.
   - Nel comando sostituire `<NUMBER OF THE SYSTEM PARTION>` con il numero di partizione della partizione di sistema EFI. A questa partizione non è ancora stata assegnata una lettera, ma deve essere del **sistema** di tipi e avere dimensioni pari a circa 100 MB.

   > [!NOTE]
   > Il confronto tra le partizioni nella console Gestione disco e quelle elencate nello strumento DISKPART può essere utile per determinare quale numero di partizione corrisponde alla partizione di sistema EFI nel disco collegato.

1. Chiudere la finestra del prompt dei comandi.

### <a name="free-up-space-on-the-disk"></a>Liberare spazio sul disco

Ora che il disco rotto è collegato alla macchina virtuale di ripristino, è necessario verificare che il sistema operativo su tale disco disponga di spazio sufficiente per il corretto funzionamento. 

1. Controllare se il disco è pieno facendo clic con il pulsante destro del mouse sull'unità del disco collegato e selezionando **Proprietà**.
1. Se lo spazio disponibile sul disco è **inferiore a 300 MB**, [espanderlo fino a un massimo di 1 TB usando PowerShell](../windows/expand-os-disk.md).
1. Quando le dimensioni del disco sono pari a **1 TB**, è necessario eseguire una pulitura del disco. È possibile utilizzare lo [strumento Pulitura disco](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) per liberare spazio.
1. Aprire un'istanza del prompt dei comandi con privilegi elevati (Esegui come amministratore) ed eseguire un'operazione di deframmentazione sull'unità:

   ``
   defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g
   ``
   
   - A seconda del livello di frammentazione, la deframmentazione potrebbe richiedere ore.
   - Nel comando sostituire `<LETTER ASSIGNED TO THE OS DISK>` con la lettera del disco del sistema operativo, ad esempio l'unità *F:*.

### <a name="clean-out-bad-memory-from-the-boot-configuration-data-bcd-store"></a>Pulitura della memoria non valida dall'archivio dati configurazione di avvio (BCD)

1. Aprire un prompt dei comandi con privilegi elevati (Esegui come amministratore).
1. Eseguire una query sul file di configurazione di avvio per i flag di memoria non validi con il comando seguente:

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /enum {badmemory}
   ``
   
   - Nel comando sostituire `<LETTER ASSIGNED TO THE OS DISK>` con la lettera del disco del sistema operativo, ad esempio l'unità *F:*.

1. Se la query non visualizza alcun blocco di memoria errato, passare all'attività successiva. In caso contrario, continuare con il passaggio 4.
1. Se vengono identificati blocchi di memoria errati, questi blocchi impediscono la creazione di un ramdisk ed è necessario eliminarli con il comando seguente:

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /deletevalue {badmemory} badmemorylist
   ``
   
   - Nel comando sostituire `<LETTER ASSIGNED TO THE OS DISK>` con la lettera del disco del sistema operativo, ad esempio l'unità *F:*.

### <a name="restore-the-page-file-to-its-default-location"></a>Ripristinare il percorso predefinito del file di paging

Il file di paging archivia i dati che non possono essere conservati dalla memoria ad accesso casuale (RAM) del computer sotto forma di overflow o backup. È possibile che il file sia ospitato in un disco rigido virtuale anziché nell'unità temporanea, ovvero la località di Azure predefinita. Se true, il file potrebbe non essere accessibile e deve essere ripristinato nel percorso predefinito.

Prima di eseguire qualsiasi passaggio, è necessario creare una copia della cartella **\Windows\System32\Config** in un disco integro. Questo passaggio consente di annullare eventuali modifiche indesiderate. Si lavorerà su file di sistema importanti, quindi questa precauzione è molto consigliata.

1. In Windows Search immettere **Regedit** e aprire l'applicazione editor del registro di sistema.
1. Nell'editor del registro di Sistema evidenziare la chiave **HKEY_LOCAL_MACHINE** e selezionare **file > Load hive...** dal menu.

   ![Menu Load hive dell'editor del registro di sistema.](./media/troubleshoot-windows-stop-error/4.png)

1. Nella finestra di dialogo Carica hive selezionare **\windows\system32\config\SYSTEM** e fare clic su Apri.
   1. Verrà richiesto di specificare un nome, che deve essere immesso **BROKENSYSTEM**. Questo nome consente di distinguere gli hive interessati durante la risoluzione dei problemi.
   1. Espandere **HKEY_LOCAL_MACHINE** per visualizzare la nuova chiave BROKENSYSTEM aggiunta.
1. Usando l'editor del registro di sistema, determinare il controllo da cui viene avviato il computer.
   1. Passare a **HKEY_LOCAL_MACHINE >> BROKENSYSTEM >> Select**.
   1. Nelle chiavi elencate, prendere nota del valore di dati corrente. Se, ad esempio, questo valore è **1** o **0x00000001 (1)**, il set di controlli sarà ControlSet001.
1. Controllare la posizione in cui è configurata la creazione del PageFile.
   1. In HKEY_LOCAL_MACHINE\BROKENSYSTEM espandere la directory corrispondente al numero di controlli identificato nel passaggio 4, ad esempio **ControlSet001**.
   1. Passare a **Control >> Session Manager >> gestione della memoria** e prendere nota del percorso della chiave **ExistingPageFiles** .
   1. Questa chiave deve trovarsi nella posizione predefinita di Azure dell'unità temporanea. Se non è presente e si trova in un disco rigido virtuale in un'altra posizione, ad esempio l'unità disco dati o l'unità del sistema operativo, sarà necessario eliminarla.
   1. Passare a tale percorso in Esplora file e quindi eliminare il file di **pagefile.sys** .

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Abilitare la console seriale e la raccolta di dump della memoria

**Consigliato**: Prima di ricreare la macchina virtuale, abilitare la console seriale e la raccolta di dump della memoria eseguendo lo script seguente:

Per abilitare la raccolta di dump della memoria e la console seriale, eseguire lo script seguente:

1. Aprire una sessione del prompt dei comandi con privilegi elevati come amministratore.
1. Elencare i dati dell'archivio BCD e determinare l'identificatore del caricatore di avvio, che verrà usato nel passaggio successivo.

   1. Per una macchina virtuale di prima generazione, immettere il comando seguente e annotare l'identificatore elencato:
   
      ``
      bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
      ``
   
   - Nel comando sostituire `<BOOT PARTITON>` con la lettera della partizione nel disco collegato che contiene la cartella di avvio.

      ![L'output dell'elenco dell'archivio BCD in una macchina virtuale di prima generazione, che elenca il numero identificativo del caricatore di avvio di Windows.](./media/troubleshoot-windows-stop-error/5.png)

   1. Per una macchina virtuale di seconda generazione, immettere il comando seguente e annotare l'identificatore elencato:
   
      ``
      bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum
      ``
   
   - Nel comando sostituire `<LETTER OF THE EFI SYSTEM PARTITION>` con la lettera della partizione di sistema EFI.
   - Potrebbe essere utile avviare la console Gestione disco per identificare la partizione di sistema appropriata etichettata come **partizione di sistema EFI**.
   - L'identificatore può essere un GUID univoco oppure il **Bootmgr**predefinito.

1. Eseguire i comandi seguenti per abilitare la console seriale:

   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
   
   - Nel comando sostituire `<VOLUME LETTER WHERE THE BCD FOLDER IS>` con la lettera della cartella BCD.
   - Nel comando sostituire `<BOOT LOADER IDENTIFIER>` con l'identificatore trovato nel passaggio precedente.

1. Verificare che lo spazio disponibile sul disco del sistema operativo sia superiore alla dimensione della memoria (RAM) nella macchina virtuale.

   Se lo spazio disponibile nel disco del sistema operativo non è sufficiente, modificare il percorso in cui verrà creato il file di dump della memoria e fare in modo che quella posizione faccia riferimento a qualsiasi disco dati collegato alla macchina virtuale con spazio libero sufficiente. Per modificare il percorso, sostituire **% systemroot%** con la lettera di unità del disco dati, ad esempio l'unità **F:**, nei comandi seguenti.

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

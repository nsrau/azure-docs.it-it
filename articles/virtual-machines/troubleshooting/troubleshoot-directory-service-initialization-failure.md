---
title: Risolvere i problemi relativi a errori irreversibili - errore di inizializzazione del servizio directory
description: Risolvere i problemi in cui una macchina virtuale (VM) di controller di dominio Active Directory è bloccata in un ciclo e informa che è necessario riavviare.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3396f8fe-7573-4a15-a95d-a1e104c6b76d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/05/2020
ms.author: v-miegge
ms.openlocfilehash: 118c81dd52951729bfbbb97a510e693861666ee6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663939"
---
# <a name="troubleshoot-windows-stop-error--directory-service-initialization-failure"></a>Risolvere i problemi relativi a errori irreversibili - errore di inizializzazione del servizio directory

Questo articolo illustra la procedura per risolvere i problemi in cui una macchina virtuale (VM) di controller di dominio Active Directory è bloccata in un ciclo e informa che è necessario riavviare.

## <a name="symptom"></a>Sintomo

Lo screenshot della macchina virtuale visualizzato con lo strumento [Diagnostica di avvio](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) mostra che la macchina virtuale deve essere riavviata a causa di un errore, visualizzando il codice di arresto **0xC00002E1** in Windows Server 2008 R2 o **0xC00002E2** in Windows Server 2012 o versioni successive.

![La schermata iniziale di Windows Server 2012 indica "Si è verificato un problema ed è necessario riavviare il PC. È in corso la raccolta di alcune informazioni sull'errore, quindi il riavvio verrà eseguito automaticamente".](./media/troubleshoot-directory-service-initialization-failure/1.png)

## <a name="cause"></a>Causa

Il codice di errore **0xC00002E2** rappresenta **STATUS_DS_INIT_FAILURE** e il codice di errore **0xC00002E1** rappresenta **STATUS_DS_CANT_START**. Entrambi gli errori si verificano quando è presente un problema con il servizio directory.

All'avvio del sistema operativo, è quindi necessario riavviarlo automaticamente mediante il server di autenticazione della protezione locale (**LSASS.exe**), che autentica gli account di accesso degli utenti. Non è possibile eseguire l'autenticazione quando il sistema operativo della macchina virtuale è un controller di dominio che non ha accesso in lettura/scrittura al database di Active Directory locale. A causa della mancanza di accesso ad **Active Directory (AD)** , LSASS.exe non è in grado di eseguire l'autenticazione ed è costretto a riavviare il sistema operativo.

Questo errore può dipendere da una delle condizioni seguenti:

- Non è possibile accedere al disco che contiene il database di Active Directory locale (**NTDS.DIT**).
- Il disco che contiene il database di Active Directory locale (NTDS.DIT) ha esaurito lo spazio disponibile.
- Il file del database di Active Directory locale (NTDS.DIT) è mancante.
- La macchina virtuale contiene più dischi e il criterio SAN (rete di archiviazione) è configurato in modo errato. Il criterio SAN non è impostato su **ONLINEALL**e i dischi non del sistema operativo sono collegati in modalità offline nel gestore dischi.
- Il file del database di Active Directory locale (NTDS.DIT) è danneggiato.

## <a name="solution"></a>Soluzione

### <a name="process-overview"></a>Panoramica del processo:

1. Creare e accedere a una macchina virtuale di ripristino.
1. Liberare spazio sul disco.
1. Verificare che l'unità contenente il database di Active Directory sia collegata.
1. Abilitare la modalità ripristino servizi directory.
1. **Consigliato**: prima di ricreare la macchina virtuale, abilitare la console seriale e la raccolta di dump della memoria.
1. Ricreare la macchina virtuale.
1. Riconfigurare il criterio SAN.

> [!NOTE]
> Quando si verifica questo errore, il sistema operativo guest non è operativo. La risoluzione dei problemi sarà effettuata in modalità offline.

### <a name="create-and-access-a-repair-vm"></a>Creare e accedere a una macchina virtuale di ripristino

1. Seguire i [passaggi da 1 a 3 dei comandi di ripristino della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) per preparare una macchina virtuale di ripristino.
1. Usare Connessione Desktop remoto per connettersi alla macchina virtuale di ripristino.

### <a name="free-up-space-on-disk"></a>Liberare spazio sul disco

Poiché il disco è ora collegato a una macchina virtuale di ripristino, verificare che il disco contenente il database interno di Active Directory disponga di spazio sufficiente per funzionare correttamente.

1. Controllare se il disco è pieno facendo clic con il pulsante destro del mouse sull'unità e selezionando **Proprietà**.
1. Se lo spazio disponibile sul disco è inferiore a 300 MB, [espanderlo fino a un massimo di 1 TB usando PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk).
1. Se il disco ha raggiunto 1 TB di spazio usato, eseguire una pulitura del disco.

   1. Usare PowerShell per [scollegare il disco dati](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-powershell) dalla macchina virtuale non funzionante.
   1. Dopo averlo scollegato, [collegare il disco dati](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm) a una macchina virtuale funzionante.
   1. Usare lo [strumento di pulitura disco](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) per liberare spazio aggiuntivo.

1. **Facoltativo**: se è necessaria una quantità di spazio maggiore, aprire un'istanza CMD e immettere il comando `defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g` per eseguire una deframmentazione sull'unità:

  * Nel comando, sostituire `<LETTER ASSIGNED TO THE OS DISK>` con la lettera del disco del sistema operativo. Se, ad esempio, la lettera del disco è `F:`, il comando sarà `defrag F: /u /x /g`.

  * A seconda del livello di frammentazione, la deframmentazione potrebbe richiedere ore.

Se lo spazio sul disco è sufficiente, passare all'attività successiva.

### <a name="check-that-the-drive-containing-the-active-directory-database-is-attached"></a>Verificare che l'unità contenente il database di Active Directory sia collegata

1. Aprire un'istanza CMD con privilegi elevati ed eseguire i comandi seguenti:

   1. Caricare il file del Registro di sistema:

      `REG LOAD HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM`

      La designazione `f:` presuppone che il disco sia l'unità `F:`. Usare la lettera di unità che appartiene all'unità contenente il disco del sistema operativo.

   1. Determinare la lettera di unità e la cartella di **NTDS.DIT**:

      ```
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Working Directory"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Database file"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database backup path"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database log files path"
      ```

   1. Scaricare il file del Registro di sistema:

      `REG UNLOAD HKLM\BROKENSYSTEM`

1. Tramite il portale di Azure, verificare che l'unità in cui è configurato NTDS.DIT, è aggiunta alla macchina virtuale.
1. Usare la console Gestione disco dal sistema operativo guest per verificare se il disco contenente NTDS.DIT è online.
   1. Lo strumento Gestione disco è disponibile in **Strumenti di amministrazione > Gestione computer > Archiviazione** oppure è possibile accedervi usando il comando `diskmgmt.msc` in un'istanza CMD.
1. Se il disco non è collegato alla macchina virtuale, ricollegare il disco dati per risolvere il problema.

   Se il disco è stato collegato normalmente, continuare con l'attività successiva.

### <a name="enable-directory-services-restore-mode"></a>Abilitare la modalità ripristino servizi directory

Configurare la macchina virtuale per l'avvio in **Modalità ripristino servizi directory (DSRM)** per ignorare la verifica dell'esistenza del file NTDS.DIT durante l'avvio.

1. Prima di continuare, verificare di aver completato le attività precedenti per collegare il disco a una macchina virtuale di ripristino e di aver determinato in quale disco si trova il file NTDS.DIT.
1. Tramite un'istanza CMD con privilegi elevati, elencare le informazioni sulla partizione di avvio in tale archivio per trovare l'identificatore dalla partizione attiva:

   `bcdedit /store <Drive Letter>:\boot\bcd /enum`

   Sostituire `< Drive Letter >` con la lettera determinata nei passaggi precedenti.

   ![Lo screenshot mostra un'istanza CMD con privilegi elevati dopo aver immesso il comando "bcdedit /store <lettera di unità>:\boot\bcd /enum", che visualizza Windows Boot Manager con l'identificatore.](./media/troubleshoot-directory-service-initialization-failure/2.png)

1. Abilitare il flag `safeboot DsRepair` nella partizione di avvio:

   `bcdedit /store <Drive Letter>:\boot\bcd /set {<Identifier>} safeboot dsrepair`

   Sostituire `< Drive Letter >` e `< Identifier >` con i valori determinati nei passaggi precedenti.

1. Eseguire di nuovo una query sulle opzioni di avvio per assicurarsi che la modifica sia stata impostata correttamente.

   ![Lo screenshot mostra un'istanza CMD con privilegi elevati dopo aver abilitato il flag DsRepair di safeboot.](./media/troubleshoot-directory-service-initialization-failure/3.png)

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Consigliato: prima di ricreare la macchina virtuale, abilitare la console seriale e la raccolta di dump della memoria

Per abilitare la raccolta di dump della memoria e la console seriale, eseguire lo script seguente aprendo una sessione del prompt dei comandi con privilegi elevati (Esegui come amministratore) ed eseguire i comandi seguenti.

1. Abilitare la console seriale:

  ```
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
  ```

1. Verificare che lo spazio disponibile sul disco del sistema operativo sia almeno uguale alla dimensione della memoria (RAM) nella macchina virtuale.

  1. Se lo spazio disponibile nel disco del sistema operativo non è sufficiente, modificare il percorso in cui verrà creato il file di dump della memoria e fare riferimento a qualsiasi disco dati collegato alla macchina virtuale con spazio libero sufficiente.

     Per modificare il percorso, sostituire `%SystemRoot%` con la lettera di unità (ad esempio `F:`) del disco dati nei comandi seguenti.

  #### <a name="the-following-configuration-is-suggested-to-enable-os-dump"></a>Per abilitare il dump del sistema operativo si suggerisce la configurazione seguente:

  **Caricare disco del sistema operativo non funzionante**:

  `REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

  **Abilitare su ControlSet001**:

  ```
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
  ```

  **Abilitare su ControlSet002**:

  ```
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
  ```

  **Scaricare disco del sistema operativo non funzionante**:

  `REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-vm"></a>Ricreare la macchina virtuale

1. Usare il [passaggio 5 dei comandi di ripristino della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) per riassemblare la macchina virtuale.

### <a name="reconfigure-the-storage-area-network-policy"></a>Riconfigurare il criterio SAN

1. Quando si esegue l'avvio in modalità ripristino servizi directory, l'unico utente disponibile per l'accesso è l'amministratore del ripristino, che è stato usato quando la macchina virtuale è stata innalzata di livello a controller di dominio. Tutti gli altri utenti visualizzeranno un errore di autenticazione.

   1. Se non sono disponibili altri controller di dominio, è necessario eseguire l'accesso in locale usando `.\administrator` o `machinename\administrator` e la password della modalità ripristino servizi directory.

1. Configurare il criterio SAN in modo che tutti i dischi siano online.

   1. Aprire un prompt dei comandi con privilegi elevati e immettere `DISKPART`.
   1. Eseguire una query per l'elenco dei dischi.

      `DISKPART> list disk`

   1. Immettere i comandi seguenti per selezionare il disco da portare online e modificare il criterio SAN:

      ```
      DISKPART> select disk 1
      Disk 1 is now the selected disk.

      DISKPART> attributes disk clear readonly
      Disk attributes cleared successfully.

      DISKPART> attributes disk
      Current Read-only State : No
      Read-only : No
      Boot Disk : No
      Pagefile Disk : No
      Hibernation File Disk : No
      Crashdump Disk : No
      Clustered Disk : No

      DISKPART> online disk
      DiskPart successfully onlined the selected disk.

      DISKPART> san
      SAN Policy : Online All
      ```

1. Dopo aver risolto il problema, assicurarsi che il flag `DsRepair safeboot` venga rimosso:

   `bcdedit /deletevalue {default} safeboot dsrepair`

1. Riavviare la VM.

   > [!NOTE]
   > Se è stata appena eseguita la migrazione della macchina virtuale dall'ambiente locale e si vuole eseguire la migrazione di altri controller di dominio da locale ad Azure, è necessario seguire i passaggi descritti nell'articolo seguente per evitare che il problema si verifichi nelle migrazioni future:
   >
   > [Come caricare i controller di dominio Hyper-V locali in Azure tramite Azure PowerShell](https://support.microsoft.com/help/2904015)

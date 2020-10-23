---
title: Eseguire il backup del server di Backup di Microsoft Azure
description: Informazioni su come eseguire il backup del server di Backup di Microsoft Azure (MAB).
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: 81a6ee005e15b1d7ab7b11a938b8ab14143818f4
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172123"
---
# <a name="back-up-the-mabs-server"></a>Eseguire il backup del server di Backup di Microsoft Azure

Per assicurarsi che i dati possano essere ripristinati in caso di errore di Backup di Microsoft Azure Server (MAB), è necessaria una strategia per il backup del server di MAB. Se non viene eseguito il backup, sarà necessario ricompilarlo manualmente dopo un errore e i punti di ripristino basati su disco non saranno ripristinabili. È possibile eseguire il backup dei server MAB eseguendo il backup del database di MAB.

## <a name="back-up-the-mabs-database"></a>Eseguire il backup del database MAB

Come parte della strategia di backup di MAB, sarà necessario eseguire il backup del database di MAB. Il database MAB è denominato DPMDB. Questo database contiene la configurazione di MAB insieme ai dati relativi ai backup di MAB. Se si verifica un'emergenza, è possibile ricompilare la maggior parte delle funzionalità di un server MAB usando un backup recente del database. Supponendo che sia possibile ripristinare il database, i backup basati su nastro sono accessibili e mantengono tutte le impostazioni del gruppo protezione dati e le pianificazioni di backup. Se i dischi del pool di archiviazione MAB non sono stati interessati dall'interruzione, anche i backup basati su disco potranno essere usati dopo una ricompilazione. È possibile eseguire il backup del database con diversi metodi.

|Metodo di backup del database|Vantaggi|Svantaggi|
|--------------------------|--------------|-----------------|
|[Backup in Azure](#back-up-to-azure)|<li>Facile configurazione e monitoraggio in MAB.<li>Più percorsi dei file del database di backup.<li>L'archiviazione cloud offre una soluzione efficace per il ripristino di emergenza.<li>Archiviazione molto sicura per il database.<li>Supporta 120 punti di ripristino online.|<li>Richiede l'account Azure e la configurazione aggiuntiva di MAB. Comporta un costo per l'archiviazione Azure.<li> Richiede una versione supportata del sistema basato su Windows Server con l'agente di Azure per ottenere l'accesso ai backup di MAB archiviati nell'insieme di credenziali di backup di Azure. Non può essere un altro server di MAB.<li>Opzione non disponibile se il database è ospitato in locale e vuoi abilitare la protezione secondaria. <li>Occorre più tempo per la preparazione e il ripristino.|
|[Eseguire il backup del database eseguendo il backup del pool di archiviazione MAB](#back-up-the-database-by-backing-up-the-mabs-storage-pool)|<li>Facilità di configurazione e monitoraggio.<li>Il backup viene mantenuto nei dischi del pool di archiviazione MAB ed è facile da accedere localmente.<li>I backup pianificati di MAB supportano 512 backup completi rapidi. Se esegui il backup ogni ora, avrai 21 giorni di protezione completa.|<li>Non è un'opzione valida per il ripristino di emergenza. È online e il ripristino potrebbe non funzionare come previsto se il server MAB o il disco del pool di archiviazione ha esito negativo.<li>Opzione non disponibile se il database è ospitato in locale e vuoi abilitare la protezione secondaria. <li>Per ottenere l'accesso ai punti di ripristino se il servizio o la console MAB non è in esecuzione o funzionante, è necessario eseguire alcune operazioni di preparazione e speciali.|
|[Backup con il backup nativo di SQL Server in un disco locale](#back-up-with-native-sql-server-backup-to-a-local-disk)|<li>Incorporato in SQL Server.<li>Il backup viene mantenuto in un disco locale facilmente accessibile.<li>L'esecuzione può essere pianificata con la frequenza desiderata.<li>Completamente indipendente da MAB.<li>È possibile pianificare una pulizia del file di backup.|<li>Non è un'opzione valida per il ripristino di emergenza, a meno che i backup non vengano copiati in una posizione remota.<li>Richiede l'archiviazione locale per i backup, che può limitare la conservazione e la frequenza.|
|[Eseguire il backup con il backup nativo di SQL e la protezione di Mab in una condivisione protetta da MAB](#back-up-to-a-share-protected-by-mabs)|<li>Facile monitoraggio in MAB.<li>Più percorsi dei file del database di backup.<li>Facilità di accesso da qualsiasi computer Windows in rete.<li>È potenzialmente il metodo di ripristino più veloce.|<li>Supporta solo 64 punti di ripristino.<li>Non è un'opzione valida per il ripristino di emergenza dei siti. Un errore del disco del pool di archiviazione MAB o del server MAB può ostacolare le attività di ripristino.<li>Opzione non disponibile se il database MAB è ospitato localmente e si desidera abilitare la protezione secondaria. <li>Sono necessarie alcune attività di preparazione aggiuntive per la configurazione e il testing.<li>Il tempo di preparazione e ripristino aggiuntivo è necessario se il server MAB è inattivo, ma i dischi del pool di archiviazione MAB sono buoni.|

- Se si esegue il backup usando un gruppo protezione dati MAB, è consigliabile usare un gruppo protezione dati univoco per il database.

    > [!NOTE]
    > A scopo di ripristino, l'installazione di MAB da ripristinare con il database MAB deve corrispondere alla versione del database di MAB.  Ad esempio, se il database che si desidera ripristinare è da un oggetto MAB V3 con l'installazione dell'aggiornamento cumulativo 1, il server MAB deve eseguire la stessa versione con aggiornamento cumulativo 1. Ciò significa che potrebbe essere necessario disinstallare e reinstallare MAB con una versione compatibile prima di ripristinare il database.  Per visualizzare la versione del database potrebbe essere necessario montarlo manualmente in un nome di database temporaneo, quindi eseguire una query SQL sul database per verificare l'ultimo aggiornamento cumulativo installato in base alla versione principale e secondaria.

- Per controllare la versione del database MAB, attenersi alla seguente procedura:

    1. Per eseguire la query, aprire SQL Management Studio e quindi connettersi all'istanza di SQL in cui è in esecuzione il database di MAB.

    2. Selezionare il database MAB, quindi avviare una nuova query.

    3. Incollare il codice SQL seguente nel riquadro della query ed eseguirlo:

        `Select distinct MajorVersionNumber,MinorVersionNumber ,BuildNumber, FileName FROM dbo.tbl\_AM\_AgentPatch order byMajorVersionNumber,MinorVersionNumber,BuildNumber`

    Se non viene restituito alcun risultato nei risultati della query o se il server MAB è stato aggiornato da versioni precedenti, ma non è stato installato alcun nuovo aggiornamento cumulativo da allora, non sarà presente alcuna voce per il principale, minore per un'installazione di base di MAB. Per verificare le versioni di MAB associate agli aggiornamenti cumulativi, vedere l' [elenco dei numeri di build per MAB](https://social.technet.microsoft.com/wiki/contents/articles/36381.microsoft-azure-backup-server-list-of-build-numbers.aspx).

### <a name="back-up-to-azure"></a>Backup in Azure

1. Prima di iniziare, è necessario eseguire uno script per recuperare il percorso del punto di montaggio del volume di replica di MAB, in modo che sia possibile stabilire quale punto di ripristino contiene il backup di MAB. Eseguire questa operazione dopo la replica iniziale con Backup di Azure. Nello script sostituire `dplsqlservername%` con il nome dell'istanza di SQL Server che ospita il database di MAB.

    ```SQL
    Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%dpmdb%'
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
    ```

    Assicurarsi di avere il codice di accesso specificato quando è stato installato l'agente di servizi di ripristino di Azure e il server MAB è stato registrato nell'insieme di credenziali di backup di Azure. Questo passcode sarà necessario per il ripristino del backup.

2. Creare un insieme di credenziali di Backup di Azure, scaricare il file di installazione dell’agente Backup di Azure e l’insieme di credenziali. Eseguire il file di installazione per installare l'agente nel server MAB e usare le credenziali dell'insieme di credenziali per registrare il server MAB nell'insieme di credenziali. [Altre informazioni](backup-azure-microsoft-azure-backup.md)

3. Dopo aver configurato l'insieme di credenziali, configurare un gruppo protezione dati MAB che contiene il database di MAB. Selezionare per eseguirne il backup su disco e in Azure.

#### <a name="recover-the-mabs-database-from-azure"></a>Ripristinare il database MAB da Azure

È possibile ripristinare il database da Azure usando qualsiasi server MAB registrato nell'insieme di credenziali di backup di Azure, come indicato di seguito:

1. Nella console di MAB selezionare **ripristino**  >  **Aggiungi MAB esterno**.

2. Fornire le credenziali dell'insieme di credenziali (scaricare dall'insieme di credenziali di backup di Azure). Si noti che le credenziali sono valide solo per due giorni.

3. In **Seleziona MAB esterno per il ripristino**selezionare il server MAB per il quale si desidera ripristinare il database, digitare la passphrase di crittografia e fare clic su **OK.**

4. Selezionare il punto di ripristino che si vuole usare nell'elenco dei punti disponibili. Selezionare **Clear External MAB** per tornare alla visualizzazione MAB locale.

## <a name="back-up-the-mabs-database-to-mabs-storage-pool"></a>Eseguire il backup del database MAB nel pool di archiviazione di MAB

> [!NOTE]  
> Questa opzione è applicabile a MAB con Modern Backup Storage.

1. Nella console di MAB selezionare **protezione**  >  **Crea gruppo protezione**dati.
2. Nella pagina **Selezione tipo di gruppo protezione dati** seleziona **Server**.
3. Nella pagina **Seleziona membri del gruppo** selezionare **database DPM**. Espandere il server MAB e selezionare DPMDB.
4. Nella pagina **Seleziona metodo protezione dati** selezionare **Protezione dati a breve termine tramite: disco**. Specificare le opzioni per i criteri di protezione a breve termine.
5. Dopo la replica iniziale del database MAB, eseguire lo script SQL seguente:

```SQL
select AG.NetbiosName, DS.DatasourceName, V.AccessPath, LR.PhysicalReplicaId from tbl_IM_DataSource DS
join tbl_PRM_LogicalReplica as LR
on DS.DataSourceId = LR.DataSourceId
join tbl_AM_Server as AG
on DS.ServerId=AG.ServerId
join tbl_PRM_ReplicaVolume RV
on RV.ReplicaId = LR.PhysicalReplicaId
join tbl_STM_Volume V
on RV.StorageId = V.StorageId
where datasourcename like N'%dpmdb%' and ds.ProtectedGroupId is not null
and LR.Validity in (1,2)
and AG.ServerName like N'%<dpmsqlservername>%' -- <dpmsqlservername> is a placeholder, put netbios name of server hosting DPMDB
```

### <a name="recover-mabs-database"></a>Ripristina database MAB

Per ricostruire l'oggetto MAB con lo stesso database, è necessario innanzitutto ripristinare il database MAB e sincronizzarlo con il nuovo MAB installato.

#### <a name="use-the-following-steps"></a>Usare la procedura seguente

1. Aprire un prompt dei comandi amministrativo ed eseguire `psexec.exe -s powershell.exe` per avviare una finestra di PowerShell nel contesto di sistema.
2. Decidere da quale posizione si vuole ripristinare il database.

#### <a name="to-copy-the-database-from-the-last-backup"></a>Per copiare il database dall'ultimo backup

1. Passare al percorso del disco rigido virtuale di replica `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PhysicalReplicaId\>`
2. Montare il **disk0. vhdx** presente utilizzando il `mount-vhd disk0.vhdx` comando.
3. Una volta montato il disco rigido virtuale di replica, usare `mountvol.exe` per assegnare una lettera di unità al volume di replica usando l'ID replica fisica dell'output dello script SQL. ad esempio `mountvol X: \?\Volume{}\`

#### <a name="to-copy-the-database-from-a-previous-recovery-point"></a>Per copiare il database da un punto di ripristino precedente

1. Passare alla directory del contenitore DPMDB  `\<MABSServer FQDN\>\<PhysicalReplicaId\>` . Verranno visualizzate più directory con alcuni identificatori GUID univoci in base ai punti di ripristino corrispondenti effettuati per il database di MAB. Altre directory rappresentano un punto di ripristino.
2. Passare a un percorso del disco rigido virtuale PIT, ad esempio, `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PITId\>` e montare **disk0. vhdx** in tale percorso usando il `mount-vhd disk0.vhdx` comando.
3. Una volta montato il disco rigido virtuale di replica, usare `mountvol.exe` per assegnare una lettera di unità al volume di replica, usando l'ID replica fisica dell'output dello script SQL. ad esempio `mountvol X: \?\Volume{}\`

   Tutti i termini visualizzati con parentesi angolari nei passaggi precedenti sono i titolari di posizione. Sostituirli con i valori appropriati, come indicato di seguito:
   - **ReFSVolume** -percorso di accesso dall'output dello script SQL
   - **MABSSERVER FQDN** -nome completo del server MAB
   - **PhysicalReplicaId** -ID replica fisica dallo script SQL in uscita
   - **PITId** -identificatore GUID diverso dall'ID replica fisica nella directory del contenitore.
4. Aprire un altro prompt dei comandi amministrativo ed eseguire `psexec.exe -s cmd.exe` per avviare un prompt dei comandi nel contesto di sistema.
5. Passare alla directory dell'unità X: e passare al percorso dei file di database di MAB.
6. Copiarli in un percorso di facile ripristino. Al termine della copia chiudere la finestra di comando di psexec.
7. Passare alla finestra di PowerShell di PsExec aperta nel passaggio 1, passare al percorso VHDX e smontare il VHDX usando il comando `dismount-vhd disk0.vhdx` .
8. Dopo aver reinstallato il server MAB, è possibile usare il DPMDB ripristinato per connettersi al server MAB eseguendo il `DPMSYNC-RESTOREDB` comando.
9. Esegui `DPMSYNC-SYNC` una volta `DPMSYNC-RESTOREDB` è completata.

### <a name="back-up-the-database-by-backing-up-the-mabs-storage-pool"></a>Eseguire il backup del database eseguendo il backup del pool di archiviazione MAB

> [!NOTE]
> Questa opzione è applicabile a MAB con archiviazione legacy.

Prima di iniziare, è necessario eseguire uno script per recuperare il percorso del punto di montaggio del volume di replica di MAB, in modo che sia possibile stabilire quale punto di ripristino contiene il backup di MAB. Eseguire questa operazione dopo la replica iniziale con Backup di Azure. Nello script sostituire `dplsqlservername%` con il nome dell'istanza di SQL Server che ospita il database di MAB.

```SQL
Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
from tbl_IM_DataSource as ds
join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
and vol.Usage =1
and lr.Validity in (1,2)
where ds.datasourcename like '%dpmdb%'
and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
```

1. Nella console di MAB selezionare **protezione**  >  **Crea gruppo protezione**dati.

2. Nella pagina **Selezione tipo di gruppo protezione dati** selezionare **Server**.

3. Nella pagina **Seleziona membri del gruppo** selezionare il database MAB. Espandere l'elemento server MAB e selezionare **DPMDB**.

4. Nella pagina  **Selezione metodo protezione dati** selezionare protezione a **breve termine tramite disco**. Specificare le opzioni per i criteri di protezione a breve termine. Si consiglia un periodo di mantenimento dati di due settimane per i database di MAB.

#### <a name="recover-the-database"></a>Ripristinare il database

Se il server MAB è ancora operativo e il pool di archiviazione è intatto (ad esempio, problemi con il servizio o la console di MAB), copiare il database dal volume di replica o da una copia shadow come indicato di seguito:

1. Decidere da dove si vuole ripristinare il database.

    - Se si vuole copiare il database dall'ultimo backup effettuato direttamente dal volume di replica di MAB, usare **mountvol.exe** per assegnare una lettera di unità al volume di replica usando il GUID dall'output dello script SQL. ad esempio `C:\Mountvol X: \\?\Volume{d7a4fd76\-a0a8\-11e2\-8fd3\-001c23cb7375}\`

    - Se si vuole copiare il database da un punto di ripristino precedente (copia shadow), è necessario elencare tutte le copie shadow per la replica usando il GUID del volume dall'output dello script SQL. Questo comando elenca le copie shadow per il volume: `C:\>Vssadmin list shadows /for\=\\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` . Prendere nota dell'ora di creazione e dell'ID copia shadow da cui si desidera eseguire il ripristino.

2. Usare quindi **diskshadow.exe** per montare la copia shadow in una lettera di unità inutilizzata X: usando l'ID della copia shadow, in modo da poter copiare i file di database.

3. Aprire un prompt dei comandi amministrativo ed eseguire `psexec.exe -s cmd.exe` per avviare un prompt dei comandi nel contesto di sistema, in modo che si disponga dell'autorizzazione per passare al volume di replica (X:) e copiare i file.

4. Da CD all'unità X: e passare al percorso dei file di database di MAB. Copiarli in un percorso di facile ripristino. Al termine della copia, esiste la finestra cmd PsExec ed eseguire **diskshadow.exe** e annullare l'esposizione del volume X:.

5. A questo punto è possibile ripristinare i file di database usando SQL Management Studio o eseguendo **DPMSYNC \- RESTOREDB**.

## <a name="back-up-with-native-sql-server-backup-to-a-local-disk"></a>Backup con il backup nativo di SQL Server in un disco locale

È possibile eseguire il backup del database MAB in un disco locale con il backup nativo di SQL Server, indipendentemente da MAB.

- [Panoramica ](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) di backup e ripristino di database SQL Server.

- [Altre informazioni](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) sul backup di SQL Server nel cloud.

## <a name="back-up-to-a-share-protected-by-mabs"></a>Eseguire il backup in una condivisione protetta da MAB

Questa opzione di backup usa SQL nativo per eseguire il backup del database MAB in una condivisione, protegge la condivisione con MAB e usa le versioni precedenti di Windows VSS per facilitare il ripristino.

### <a name="before-you-start"></a>Prima di iniziare

1. Nella SQL Server creare una cartella in un'unità con spazio libero sufficiente per conservare una sola copia di un backup. Ad esempio: `C:\MABSBACKUP`.

1. Condividi la cartella. Ad esempio, condividere `C:\MABSBACKUP` la cartella come *DPMBACKUP*.

1. Copiare e incollare il comando OSQL seguente nel blocco note e salvarlo in un file denominato `C:\MABSACKUP\bkupdb.cmd` . Assicurarsi che non sia presente l'estensione txt. Modificare il SQL_Instance_name e DPMDB_NAME in modo che corrispondano all'istanza e al nome DPMDB usati dal server MAB.

    ```SQL
    OSQL -E -S localhost\SQL_INSTANCE_NAME -Q "BACKUP DATABASE DPMDB_NAME TO DISK='C:\DPMBACKUP\dpmdb.bak' WITH FORMAT"
    ```

1. Utilizzando blocco note, aprire il file **ScriptingConfig.xml** che si trova nella `C:\Program Files\Microsoft System Center\DPM\DPM\Scripting` cartella del server MAB.

1. Modificare **ScriptingConfig.xml** e modificare **DataSourceName =** in modo che sia la lettera dell'unità che contiene la cartella/condivisione DPMDBBACKUP. Modificare la voce PreBackupScript con il percorso completo e il nome di **BkUpDB. cmd** salvati nel passaggio 3.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ScriptConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="https://schemas.microsoft.com/2003/dls/ScriptingConfig.xsd">
    <DatasourceScriptConfig DataSourceName="C:">
    <PreBackupScript>C:\MABSDBBACKUP\bkupdb.cmd</PreBackupScript>
    <TimeOut>120</TimeOut>
    </DatasourceScriptConfig>
    </ScriptConfiguration>
    ```

1. Salvare le modifiche apportate al **ScriptingConfig.xml**.

1. Proteggere la cartella C:\MABSBACKUP o la `\sqlservername\MABSBACKUP` condivisione usando MAB e attendere la creazione della replica iniziale. Deve essere presente un **DPMDB. bak** nella cartella C:\MABSBACKUP in seguito allo script di pre-backup in esecuzione, che a sua volta è stato copiato nella replica di MAB.

1. Se non si Abilita il ripristino in modo self-service, sono necessari alcuni passaggi aggiuntivi per condividere la cartella MABSBACKUP nella replica:

    1. Nella console di MAB > la **protezione**, individuare l'origine dati MABSBACKUP e selezionarla. Nella sezione dettagli selezionare **fare clic per visualizzare i dettagli** sul collegamento al percorso di replica e copiare il percorso nel blocco note. Rimuovi il percorso di origine e mantieni il percorso di destinazione. Il percorso dovrebbe essere simile al seguente: `C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP` .

    2. Creare una condivisione per tale percorso usando il nome di condivisione **MABSSERVERNAME-DPMDB**. Puoi usare il comando Net Share seguente da un prompt dei comandi amministrativo.

        ```cmd
        Net Share MABSSERVERNAME-dpmdb="C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

### <a name="configure-the-backup"></a>Configurare il backup

È possibile eseguire il backup del database MAB come per qualsiasi altro database SQL Server usando SQL Server backup nativo.

- [Panoramica ](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) di backup e ripristino di database SQL Server.

- [Altre informazioni](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) sul backup di SQL Server nel cloud.

### <a name="recover-the-mabs-database"></a>Ripristinare il database MAB

1. Connettersi alla `\\MABSServer\MABSSERVERNAME-dpmdb` condivisione tramite Esplora risorse da qualsiasi computer Windows.

2. Fare clic con il pulsante destro del mouse sul file **DPMDB. bak** per visualizzare le proprietà. Nella scheda **Versioni precedenti** sono elencati tutti i backup che è possibile selezionare e copiare. È disponibile anche l'ultimo backup ancora disponibile nella cartella C:\MABSBACKUP, che è facilmente accessibile.

3. Se è necessario spostare in un altro server un disco del pool di archiviazione con SAN collegato a un altro server per poter leggere dal volume di replica o reinstallare Windows per la lettura dei dischi collegati localmente, è necessario conoscere in anticipo il percorso del punto di montaggio del volume di replica o il GUID del volume del database MAB, in modo da sapere quale volume include il backup del database. Puoi usare lo script SQL seguente per estrarre tali informazioni in qualsiasi momento dopo la protezione iniziale, ma prima di dover eseguire il ripristino. Sostituire `%dpmsqlservername%` con il nome del SQL Server che ospita il database.

    ```sql
    Select ag.NetbiosName as
    ServerName,ds.DataSourceName,vol.MountPointPath,vol.GuidName
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%C:\%' -- volume drive letter for DPMBACKUP
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB

    ```

4. Se è necessario eseguire il ripristino dopo aver spostato i dischi del pool di archiviazione MAB o una ricompilazione del server MAB:

    1. Se si dispone del GUID del volume, è necessario montare tale volume in un altro server Windows o dopo la ricompilazione del server MAB, utilizzare **mountvol.exe** per assegnargli una lettera di unità utilizzando il GUID del volume dall'output dello script SQL: `C:\Mountvol X: \\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` .

    2. Ricondividere la cartella MABSBACKUP nel volume di replica usando la lettera di unità e la parte del percorso di replica che rappresenta la struttura di cartelle.

        ```cmd
        net share SERVERNAME-DPMDB="X:\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

    3. Connettersi alla `\\SERVERNAME\MABSSERVERNAME-dpmdb` condivisione tramite Esplora risorse da qualsiasi computer Windows.

    4. Fare clic con il pulsante destro del mouse sul file **DPMDB. bak** per visualizzare le proprietà. Nella scheda **Versioni precedenti** sono elencati tutti i backup che è possibile selezionare e copiare.

## <a name="using-dpmsync"></a>Uso di DPMSync

**DpmSync** è uno strumento da riga di comando che consente di sincronizzare il database MAB con lo stato dei dischi nel pool di archiviazione e con gli agenti protezione installati. DpmSync ripristina il database MAB, sincronizza il database di MAB con le repliche nel pool di archiviazione, ripristina il database di report e rialloca le repliche mancanti.

### <a name="parameters"></a>Parametri

| Parametro      | Descrizione    |
|----------------|-----------------------------|
| **-RestoreDb**                       | Ripristina un database MAB da un percorso specificato.|
| **-Sync**                            | Sincronizza i database ripristinati. Dopo aver ripristinato i database, è necessario eseguire **DPMSync-Sync** . Dopo l'esecuzione di **DPMSync-Sync**, è possibile che alcune repliche siano ancora contrassegnate come mancanti. |
| **-** *Percorso* DbLoc                | Identifica la posizione del backup del database di MAB.|
| **-Nomeistanza** <br/>*server\istanza*     | Istanza in cui il database DPM deve essere ripristinato.|
| **-ReallocateReplica**         | Rialloca tutti i volumi di replica mancanti senza sincronizzazione. |
| **-DataCopied**                      | Indica che il caricamento dei dati nei volumi di replica appena allocati è stato completato. Si applica solo ai computer client. |

**Esempio 1:** Per ripristinare il database MAB dal supporto di backup locale nel server MAB, eseguire il comando seguente:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak
```

Dopo aver ripristinato il database di MAB, per sincronizzare i database, eseguire il comando seguente:

```cmd
DpmSync -Sync
```

Dopo il ripristino e la sincronizzazione del database MAB e prima del ripristino della replica, eseguire il comando seguente per riallocare lo spazio su disco per la replica:

```cmd
DpmSync -ReallocateReplica
```

**Esempio 2:** Per ripristinare il database MAB da un database remoto, eseguire il comando seguente nel computer remoto:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak –InstanceName contoso\ms$dpm
```

Dopo aver ripristinato il database di MAB, per sincronizzare i database, eseguire il comando seguente nel server MAB:

```cmd
DpmSync -Sync
```

Dopo il ripristino e la sincronizzazione del database MAB e prima del ripristino della replica, eseguire il comando seguente nel server MAB per riallocare lo spazio su disco per la replica:

```cmd
DpmSync -ReallocateReplica
```

## <a name="next-steps"></a>Passaggi successivi

- [Matrice di supporto di MAB](backup-support-matrix-mabs-dpm.md)
- [DOMANDE FREQUENTI SU MAB](backup-azure-dpm-azure-server-faq.md)
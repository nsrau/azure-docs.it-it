---
title: Ripristinare i database di SAP HANA nelle macchine virtuali di Azure
description: Questo articolo illustra come ripristinare SAP HANA database in esecuzione in macchine virtuali di Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a3db88ca3c995c3c190da051dbf9df6ae5e29530
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85851450"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Ripristinare i database di SAP HANA nelle macchine virtuali di Azure

Questo articolo descrive come ripristinare SAP HANA database in esecuzione in una macchina virtuale (VM) di Azure, in cui è stato eseguito il backup del servizio backup di Azure in un insieme di credenziali di servizi di ripristino. I ripristini possono essere usati per creare copie dei dati per scenari di sviluppo/test o per tornare a uno stato precedente.

Per altre informazioni su come eseguire il backup dei database di SAP HANA, vedere [eseguire il backup di database SAP Hana in macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Ripristinare fino a un punto nel tempo o a un punto di ripristino

Backup di Azure può ripristinare database SAP HANA in esecuzione nelle macchine virtuali di Azure seguendo questa procedura:

* Ripristinare una data specifica o un'ora (al secondo), usando i backup del log. In base ai tempi di ripristino specificati, Backup di Azure determina automaticamente i backup completi, differenziali e la catena di backup del log necessari per ripristinare in base all'ora selezionata.

* Ripristinare un backup completo o differenziale specifico per il ripristino a un punto di ripristino specifico.

## <a name="prerequisites"></a>Prerequisiti

Prima di ripristinare un database, tenere presente quanto segue:

* È possibile ripristinare il database solo in un'istanza SAP HANA che risiede nella stessa area

* L'istanza di destinazione deve essere registrata con lo stesso insieme di credenziali dell'origine

* Backup di Azure non può identificare due istanze di SAP HANA diverse nella stessa macchina virtuale. Il ripristino dei dati da un'istanza a un'altra nella stessa macchina virtuale non è quindi possibile.

* Per assicurarsi che l'istanza di SAP HANA di destinazione sia pronta per il ripristino, verificare lo stato di **conformità del backup** :

  1. Aprire l'insieme di credenziali in cui è registrata l'istanza di SAP HANA di destinazione

  1. Nel dashboard dell'insieme di credenziali, in **Guida introduttiva**, scegliere **backup** .

      ![Backup nel dashboard dell'insieme di credenziali](media/sap-hana-db-restore/getting-started-backup.png)

  1. In **backup**, in **che cosa si vuole eseguire il backup?** scegliere **SAP Hana nella macchina virtuale di Azure**

      ![Scegliere SAP HANA in una macchina virtuale di Azure](media/sap-hana-db-restore/sap-hana-backup.png)

  1. In **individuare i database nelle macchine virtuali**fare clic su **Visualizza dettagli**

      ![Visualizza i dettagli](media/sap-hana-db-restore/view-details.png)

  1. Verificare la **conformità del backup** della macchina virtuale di destinazione

      ![Server protetti](media/sap-hana-db-restore/protected-servers.png)

* Per altre informazioni sui tipi di ripristino supportati da SAP HANA, vedere la nota SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>Ripristinare un database

Per eseguire il ripristino, sono necessarie le autorizzazioni seguenti:

* Autorizzazioni dell' **operatore di backup** nell'insieme di credenziali in cui si sta eseguendo il ripristino.
* **Collaboratore (scrittura)** accesso all'origine della macchina virtuale con backup eseguito.
* Accesso **collaboratore (scrittura**) alla macchina virtuale di destinazione:
  * Se si esegue il ripristino nella stessa VM, si tratta della VM di origine.
  * Se si esegue il ripristino in un percorso alternativo, si tratta della nuova VM di destinazione.

1. Aprire l'insieme di credenziali in cui è registrato il database SAP HANA da ripristinare

1. Nel dashboard dell'insieme di credenziali, in **elementi protetti**, scegliere **elementi di backup**

    ![Elementi di backup](media/sap-hana-db-restore/backup-items.png)

1. In **elementi di backup**in **tipo di gestione di backup** Selezionare **SAP Hana nella macchina virtuale di Azure**

    ![Tipo di gestione di backup](media/sap-hana-db-restore/backup-management-type.png)

1. Selezionare il database da ripristinare

    ![Database da ripristinare](media/sap-hana-db-restore/database-to-restore.png)

1. Verificare il menu database. Fornisce informazioni sul backup del database, tra cui:

    * I punti di ripristino meno recenti e più recenti

    * Stato di backup del log per le ultime 24 ore e 72 ore per il database

    ![Menu database](media/sap-hana-db-restore/database-menu.png)

1. Selezionare **Ripristina database**

1. In **configurazione ripristino**specificare dove (o come) ripristinare i dati:

    * **Percorso alternativo**: ripristinare il database in un percorso alternativo e salvare il database di origine originale.

    * **Sovrascrivi database**: consente di ripristinare i dati nella stessa istanza di SAP Hana dell'origine originale. Questa opzione sovrascrive il database originale.

      ![Configurazione ripristino](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Ripristinare in un percorso alternativo

1. Nel menu **configurazione ripristino** , in **where to Restore**, selezionare **percorso alternativo**.

    ![Ripristinare in un percorso alternativo](media/sap-hana-db-restore/restore-alternate-location.png)

1. Selezionare il nome host SAP HANA e il nome dell'istanza in cui si desidera ripristinare il database.
1. Controllare se l'istanza di SAP HANA di destinazione è pronta per il ripristino garantendo la **conformità del backup.** Per ulteriori informazioni, vedere la [sezione Prerequisiti](#prerequisites) .
1. Nella finestra di dialogo **Nome del database ripristinato** inserire il nome del database di destinazione.

    > [!NOTE]
    > I ripristini di Database singolo container (DSC) devono seguire questi [controlli](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore).

1. Se applicabile, selezionare **Sovrascrivi se il database con lo stesso nome esiste già nell'istanza di Hana selezionata**.
1. Selezionare **OK**.

    ![Configurazione ripristino-schermata finale](media/sap-hana-db-restore/restore-configuration-last.png)

1. In **Seleziona punto di ripristino**selezionare **registri (temporizzato)** per eseguire [il ripristino a un momento specifico](#restore-to-a-specific-point-in-time). In alternativa, selezionare **completo & differenziale** per [ripristinare un punto di ripristino specifico](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Ripristinare e sovrascrivere

1. Nel menu **configurazione ripristino** , in **where to Restore**, selezionare **overwrite DB**  >  **OK**.

    ![Sovrascrivere il database](media/sap-hana-db-restore/overwrite-db.png)

1. In **Seleziona punto di ripristino**selezionare **registri (temporizzato)** per eseguire [il ripristino a un momento specifico](#restore-to-a-specific-point-in-time). In alternativa, selezionare **completo & differenziale** per [ripristinare un punto di ripristino specifico](#restore-to-a-specific-recovery-point).

### <a name="restore-as-files"></a>Ripristina come file

Per ripristinare i dati di backup come file anziché come database, scegliere **Ripristina come file**. Una volta scaricati i file in un percorso specificato, è possibile usare questi file in qualsiasi computer SAP HANA in cui si desidera ripristinarli come database. Poiché è possibile spostare questi file in qualsiasi computer, è ora possibile ripristinare i dati tra sottoscrizioni e aree geografiche.

1. Nel menu **configurazione ripristino** , in **where and How to Restore**, selezionare **Restore As files**.
1. Selezionare il nome del server **host** /Hana in cui si desidera ripristinare i file di backup.
1. Nel **percorso di destinazione nel server**immettere il percorso della cartella nel server selezionato nel passaggio 2. Si tratta del percorso in cui il servizio eseguirà il dump di tutti i file di backup necessari.

    I file di cui è stato eseguito il dump sono:

    * File di backup del database
    * File di catalogo
    * File di metadati JSON (per ogni file di backup richiesto)

    In genere, un percorso di condivisione di rete o un percorso di una condivisione file di Azure montata quando viene specificato come percorso di destinazione consente di accedere più facilmente a questi file da altri computer nella stessa rete o con la stessa condivisione file di Azure montata su di essi.

    >[!NOTE]
    >Per ripristinare i file di backup del database in una condivisione file di Azure montata nella macchina virtuale di destinazione registrata, verificare che l'account radice disponga di autorizzazioni di lettura/scrittura per la condivisione file di Azure.

    ![Scegliere il percorso di destinazione](media/sap-hana-db-restore/restore-as-files.png)

1. Selezionare il **punto di ripristino** corrispondente al quale verranno ripristinati tutti i file e le cartelle di backup.

    ![Seleziona punto di ripristino](media/sap-hana-db-restore/select-restore-point.png)

1. Tutti i file di backup associati al punto di ripristino selezionato vengono scaricati nel percorso di destinazione.
1. In base al tipo di punto di ripristino scelto (**temporizzato** o **completo & differenziale**), verranno visualizzate una o più cartelle create nel percorso di destinazione. Una delle cartelle denominate `Data_<date and time of restore>` contiene i backup completi e differenziali e l'altra cartella denominata `Log` contiene i backup del log.
1. Spostare i file ripristinati nel server SAP HANA in cui si desidera ripristinarli come database.
1. Seguire quindi questa procedura:
    1. Impostare le autorizzazioni per la cartella o la directory in cui vengono archiviati i file di backup usando il comando seguente:

        ```bash
        chown -R <SID>adm:sapsys <directory>
        ```

    1. Eseguire il set di comandi successivo come`<SID>adm`

        ```bash
        su - <sid>adm
        ```

    1. Genera il file di catalogo per il ripristino. Estrarre il **BackupId** dal file di metadati JSON per il backup completo, che verrà usato in un secondo momento nell'operazione di ripristino. Assicurarsi che i backup completi e del log si trovino in cartelle diverse ed eliminare i file di catalogo e i file di metadati JSON in queste cartelle.

        ```bash
        hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
        ```

        Nel comando precedente:

        * `<DataFileDir>`-la cartella che contiene i backup completi
        * `<LogFilesDir>`-la cartella che contiene i backup del log
        * `<PathToPlaceCatalogFile>`-la cartella in cui deve essere inserito il file di catalogo generato

    1. Eseguire il ripristino usando il file di catalogo appena generato tramite HANA Studio o eseguire la query di ripristino HDBSQL con il catalogo appena generato. Le query HDBSQL sono elencate di seguito:

    * Per eseguire il ripristino a un punto nel tempo:

        Se si sta creando un nuovo database ripristinato, eseguire il comando HDBSQL per creare un nuovo database `<DatabaseName>` e quindi arrestare il database per il ripristino. Tuttavia, se si sta ripristinando solo un database esistente, eseguire il comando HDBSQL per arrestare il database.

        Eseguire quindi il comando seguente per ripristinare il database:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>`: Nome del nuovo database o del database esistente che si desidera ripristinare
        * `<Timestamp>`-Timestamp esatto del ripristino temporizzato
        * `<DatabaseName@HostName>`: Nome del database il cui backup viene utilizzato per il ripristino e il nome del server **host** /SAP Hana in cui risiede il database. L' `USING SOURCE <DatabaseName@HostName>` opzione specifica che il backup dei dati (usato per il ripristino) è di un database con un SID o un nome diverso rispetto al computer SAP Hana di destinazione. Non è quindi necessario specificare per i ripristini eseguiti nello stesso server HANA da cui viene eseguito il backup.
        * `<PathToGeneratedCatalogInStep3>`-Percorso del file di catalogo generato nel **passaggio C**
        * `<DataFileDir>`-la cartella che contiene i backup completi
        * `<LogFilesDir>`-la cartella che contiene i backup del log
        * `<BackupIdFromJsonFile>`- **BackupId** estratti nel **passaggio C**

    * Per eseguire il ripristino in un backup completo o differenziale specifico:

        Se si sta creando un nuovo database ripristinato, eseguire il comando HDBSQL per creare un nuovo database `<DatabaseName>` e quindi arrestare il database per il ripristino. Tuttavia, se si sta ripristinando solo un database esistente, eseguire il comando HDBSQL per arrestare il database:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>`: nome del nuovo database o del database esistente che si desidera ripristinare
        * `<Timestamp>`: timestamp esatto del ripristino temporizzato
        * `<DatabaseName@HostName>`: il nome del database il cui backup viene utilizzato per il ripristino e il nome del server **host** /SAP Hana in cui risiede il database. L' `USING SOURCE <DatabaseName@HostName>` opzione specifica che il backup dei dati (usato per il ripristino) è di un database con un SID o un nome diverso rispetto al computer SAP Hana di destinazione. Non è quindi necessario specificare per i ripristini eseguiti nello stesso server HANA da cui viene eseguito il backup.
        * `<PathToGeneratedCatalogInStep3>`: percorso del file di catalogo generato nel **passaggio C** .
        * `<DataFileDir>`-la cartella che contiene i backup completi
        * `<LogFilesDir>`-la cartella che contiene i backup del log
        * `<BackupIdFromJsonFile>`- **BackupId** estratti nel **passaggio C**

### <a name="restore-to-a-specific-point-in-time"></a>Ripristinare un punto temporizzato specifico

Se si è scelto **log (punto temporizzato)** come tipo di ripristino, eseguire le operazioni seguenti:

1. Selezionare un punto di ripristino dal grafico di log e selezionare **OK** per scegliere il punto di ripristino.

    ![Punto di ripristino](media/sap-hana-db-restore/restore-point.png)

1. Nel menu **Ripristina** selezionare **Ripristina** per avviare il processo di ripristino.

    ![Selezionare Ripristina](media/sap-hana-db-restore/restore-restore.png)

1. Tenere traccia dello stato di avanzamento dell'operazione di ripristino nell'area **notifiche** o monitorarlo selezionando **Ripristina processi** dal menu database.

    ![Il ripristino è stato attivato](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Ripristinare un punto di ripristino specifico

Se si è scelto **Completo e differenziale** come tipo di ripristino, eseguire le operazioni seguenti:

1. Selezionare un punto di ripristino dall'elenco e selezionare **OK** per scegliere il punto di ripristino.

    ![Ripristinare un punto di ripristino specifico](media/sap-hana-db-restore/specific-recovery-point.png)

1. Nel menu **Ripristina** selezionare **Ripristina** per avviare il processo di ripristino.

    ![Avviare il processo di ripristino](media/sap-hana-db-restore/restore-specific.png)

1. Tenere traccia dello stato di avanzamento dell'operazione di ripristino nell'area **notifiche** o monitorarlo selezionando **Ripristina processi** dal menu database.

    ![Stato del ripristino](media/sap-hana-db-restore/restore-progress.png)

    > [!NOTE]
    > Quando il database di sistema viene ripristinato in un'istanza di destinazione in più database del contenitore di database (MDC), è necessario eseguire di nuovo lo script di pre-registrazione. Solo allora sarà possibile eseguire il ripristino dei database tenant successivi. Per altre informazioni, vedere [risoluzione dei problemi: ripristino di MDC](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come](sap-hana-db-manage.md) gestire i database di SAP Hana backup con backup di Azure

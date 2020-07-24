---
title: Note sulla versione per il server di Backup di Microsoft Azure v3
description: Questo articolo fornisce le informazioni sui problemi noti e le soluzioni alternative per Backup di Microsoft Azure Server (MAB) v3.
ms.topic: conceptual
ms.date: 06/03/2020
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: 1f4900bb129ee67cd75d2b793f4179e3135569a6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87032562"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Note sulla versione per il server di Backup di Microsoft Azure

Questo articolo descrive i problemi noti e le soluzioni alternative per il server di Backup di Microsoft Azure v3.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Esisto negativo di backup e ripristino per i carichi di lavoro in cluster

**Descrizione:** il backup/ripristino ha esito negativo per le origini dati in cluster, ad esempio cluster Hyper-V o cluster SQL (SQL AlwaysOn) o Exchange nel gruppo di disponibilità del database dopo l'aggiornamento del server di Backup di Microsoft Azure dalla versione 2 alla versione 3.

**Soluzione alternativa:** per evitare questo problema, aprire SQL Server Management Studio (SSMS) ed eseguire lo script SQL seguente nel database DPM:

```sql
    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>L'aggiornamento alla versione 3 del server di Backup di Microsoft Azure ha esito negativo nelle impostazioni locali russe

**Descrizione:** l'aggiornamento dalla versione 2 alla versione 3 del server di Backup di Microsoft Azure nelle impostazioni locali russe ha esito negativo con codice di errore **4387**.

**Soluzione alternativa:** seguire questa procedura per eseguire l'aggiornamento alla versione 3 del server di Backup di Microsoft Azure usando il pacchetto di installazione per la lingua russa:

1. Creare una copia di [backup](/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server#SSMSProcedure) del database SQL e disinstallare la versione 2 del server di Backup di Microsoft Azure (scegliere di mantenere i dati protetti durante la disinstallazione).
2. Eseguire l'aggiornamento a SQL 2017 (Enterprise) e disinstallare le funzionalità di report nell'ambito dell'aggiornamento.
3. [Installa](/sql/reporting-services/install-windows/install-reporting-services#install-your-report-server) SQL Server Reporting Services (SSRS).
4. [Installare](/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio (SSMS).
5. Configurare le funzionalità di report usando i parametri documentati in [Configurazione di SSRS con SQL 2017](./backup-azure-microsoft-azure-backup.md#upgrade-mabs).
6. [Installare](backup-azure-microsoft-azure-backup.md) la versione 3 del server di Backup di Microsoft Azure.
7. [Ripristinare](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms) SQL con SSMS ed eseguire lo strumento di sincronizzazione di DPM, come descritto [qui](/system-center/dpm/back-up-the-dpm-server?view=sc-dpm-2019#using-dpmsync).
8. Aggiornare la proprietà 'DataBaseVersion' nella tabella dbo.tbl_DLS_GlobalSetting usando il comando seguente:

    ```sql
            UPDATE dbo.tbl_DLS_GlobalSetting
            set PropertyValue = '13.0.415.0'
            where PropertyName = 'DatabaseVersion'
    ```

9. Avviare il servizio MSDPM.

## <a name="after-installing-ur1-the-mabs-reports-arent-updated-with-new-rdl-files"></a>Dopo l'installazione di UR1, i report di MAB non vengono aggiornati con i nuovi file RDL

**Descrizione**: con UR1, il problema di formattazione del rapporto MAB è stato risolto con i file RDL aggiornati. I nuovi file RDL non vengono sostituiti automaticamente con i file esistenti.

**Soluzione alternativa**: per sostituire i file RDL, attenersi alla procedura seguente:

1. Nel computer MAB aprire l'URL del portale Web di SQL Reporting Services.
1. Nell'URL del portale Web la cartella DPMReports è presente nel formato**`DPMReports_<GUID>`**

    >[!NOTE]
    >Questa convenzione di denominazione contiene sempre una sola cartella. Se MAB viene aggiornato da una versione precedente, potrebbe essere presente anche un'altra cartella precedente, ma non sarà possibile aprirlo.

    ![Cartella DPMReports](./media/backup-mabs-release-notes-v3/dpm-reports-folder.png)

1. Selezionare e aprire la **`DPMReports_<GUID>`** cartella. I singoli file di report verranno elencati come illustrato di seguito.

    ![Elenco dei singoli file di report](./media/backup-mabs-release-notes-v3/individual-report-files.png)

1. Selezionare i file di report che non terminano con il **report**, fare clic con il pulsante destro del mouse sull' **opzione** e scegliere **Gestisci**.

    ![Selezionare Gestisci per i file di report](./media/backup-mabs-release-notes-v3/manage-files.png)

1. Nella nuova pagina selezionare l'opzione **Sostituisci** per sostituire i file con i file di report più recenti.

    I file di report più recenti sono reperibili nel percorso`<MABS Installation Directory>\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    Ad esempio: `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    ![Sostituire i file con i file di report più recenti](./media/backup-mabs-release-notes-v3/replace-files.png)

    Dopo la sostituzione dei file, assicurarsi che il **nome** e la **Descrizione** siano intatti e non siano vuoti.

1. Dopo la sostituzione dei file, riavviare i servizi MAB e utilizzare i file di report.

## <a name="next-steps"></a>Passaggi successivi

[Novità di MAB](backup-mabs-whats-new-mabs.md)

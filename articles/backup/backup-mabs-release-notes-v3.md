---
title: Note sulla versione per il server di Backup di Microsoft Azure v3
description: Questo articolo fornisce informazioni sui problemi noti e le soluzioni alternative per il server di Backup di Microsoft Azure v3.
services: backup
author: JYOTHIRMAISURI
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 11/22/2018
ms.author: v-jysur
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: 9ff66b386a6293c11ad67ec90e165b927ad6669b
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52870555"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Note sulla versione per il server di Backup di Microsoft Azure
Questo articolo descrive i problemi noti e le soluzioni alternative per il server di Backup di Microsoft Azure v3.

##  <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Esisto negativo di backup e ripristino per i carichi di lavoro in cluster

**Descrizione:** il backup/ripristino ha esito negativo per le origini dati in cluster, ad esempio cluster Hyper-V o cluster SQL (SQL AlwaysOn) o Exchange nel gruppo di disponibilità del database dopo l'aggiornamento del server di Backup di Microsoft Azure dalla versione 2 alla versione 3.

**Soluzione alternativa:** per evitare questo problema, aprire SQL Server Management Studio (SSMS) ed eseguire lo script SQL seguente nel database DPM:


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


##  <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>L'aggiornamento alla versione 3 del server di Backup di Microsoft Azure ha esito negativo nelle impostazioni locali russe

**Descrizione:** l'aggiornamento dalla versione 2 alla versione 3 del server di Backup di Microsoft Azure nelle impostazioni locali russe ha esito negativo con codice di errore **4387**.

**Soluzione alternativa:** seguire questa procedura per eseguire l'aggiornamento alla versione 3 del server di Backup di Microsoft Azure usando il pacchetto di installazione per la lingua russa:

1.  Creare una copia di [backup](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) del database SQL e disinstallare la versione 2 del server di Backup di Microsoft Azure (scegliere di mantenere i dati protetti durante la disinstallazione).
2.  Eseguire l'aggiornamento a SQL 2017 (Enterprise) e disinstallare le funzionalità di report nell'ambito dell'aggiornamento.
3. [Installare](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) SQL Server Reporting Services (SSRS).
4.  [Installare](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017#ssms-installation-tips-and-issues-ssms-1791) SQL Server Management Studio (SSMS).
5.  Configurare le funzionalità di report usando i parametri documentati in [Configurazione di SSRS con SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6.  [Installare](backup-azure-microsoft-azure-backup.md) la versione 3 del server di Backup di Microsoft Azure.
7. [Ripristinare](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL con SSMS ed eseguire lo strumento di sincronizzazione di DPM, come descritto [qui](https://docs.microsoft.com/it-it/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10)).
8.  Aggiornare la proprietà 'DataBaseVersion' nella tabella dbo.tbl_DLS_GlobalSetting usando il comando seguente:

        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'


9.  Avviare il servizio MSDPM.


## <a name="next-steps"></a>Passaggi successivi

[Novità del server di Backup di Microsoft Azure v3](backup-mabs-whats-new-mabs.md)

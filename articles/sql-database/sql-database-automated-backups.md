---
title: Backup automatici e con ridondanza geografica del database SQL di Azure | Microsoft Docs
description: Il database SQL crea automaticamente e ripetutamente una copia di backup locale del database a pochi minuti l'una dall'altra e usa l'archiviazione con ridondanza geografica e accesso in lettura di Azure per la ridondanza geografica.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 09/26/2019
ms.openlocfilehash: a43783110f625dd5faef13c83228a2659155ead0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492239"
---
# <a name="automated-backups"></a>Backup automatizzati

Il database SQL crea automaticamente i backup del database conservati da 7 a 35 giorni e usa l'archiviazione con [ridondanza geografica e accesso in lettura di Azure (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) per assicurarsi che vengano conservati anche se il Data Center non è disponibile. Questi backup vengono creati automaticamente. I backup dei database sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. Se le regole di sicurezza richiedono che i backup siano disponibili per un periodo di tempo prolungato (fino a 10 anni), è possibile configurare una [conservazione a lungo termine](sql-database-long-term-retention.md) su database singleton e pool elastici.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Informazioni sul backup del database SQL

Il database SQL usa la tecnologia SQL Server per creare [backup completi](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) ogni settimana, [backup differenziali](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) ogni 12 ore e [backup del log delle transazioni](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) ogni 5-10 minuti. I backup vengono archiviati nei [BLOB di archiviazione RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) replicati in una [Data Center abbinata](../best-practices-availability-paired-regions.md) per la protezione da un'interruzione del Data Center. Quando si ripristina un database, il servizio individua i backup completi, differenziali e del log delle transazioni da ripristinare.

È possibile usare questi backup per:

- **Ripristinare un database esistente a un punto nel tempo precedente** entro il periodo di conservazione usando il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Nel database singolo e nei pool elastici questa operazione creerà un nuovo database nello stesso server del database originale. In Istanza gestita questa operazione può creare una copia del database o dello stesso o di Istanza gestita diversi nella stessa sottoscrizione.
  - **[Modificare il periodo di conservazione del backup](#how-to-change-the-pitr-backup-retention-period)** compreso tra 7 e 35 giorni per configurare i criteri di backup.
  - **Modificare i criteri di conservazione a lungo termine fino a 10 anni** in database singolo e nei pool elastici usando [il portale di Azure](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies) o [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups).
- **Ripristinare un database eliminato fino al momento in cui è stato eliminato** o in qualsiasi momento entro il periodo di memorizzazione. Il database eliminato può essere ripristinato solo nello stesso server logico o in Istanza gestita in cui è stato creato il database originale.
- **Ripristinare un database in un'altra area geografica**. Il ripristino geografico consente di eseguire un ripristino di emergenza geografico quando è impossibile accedere al server e al database. Crea un nuovo database in qualsiasi server esistente ovunque nel mondo.
- **Ripristinare un database da uno specifico backup a lungo termine** in Database singolo o pool elastico se il database è stato configurato con un criterio di conservazione a lungo termine (LTR). LTR consente di ripristinare una versione precedente del database usando [il portale di Azure](sql-database-long-term-backup-retention-configure.md#view-backups-and-restore-from-a-backup-using-azure-portal) o [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups) per soddisfare una richiesta di conformità o per eseguire una versione precedente dell'applicazione. Per altre informazioni, vedere [Long-term retention](sql-database-long-term-retention.md) (Conservazione a lungo termine).
- Per eseguire un ripristino, vedere l'articolo su come [ripristinare un database da un backup](sql-database-recovery-using-backups.md).

> [!NOTE]
> In Archiviazione di Azure il termine *replica* fa riferimento alla copia dei file da una località a un'altra. La *replica di database* di SQL fa riferimento a più database secondari sincronizzati con un database primario.

È possibile provare alcune di queste operazioni usando gli esempi seguenti:

| | Portale di Azure | Azure PowerShell |
|---|---|---|
| Modificare la conservazione dei backup | [Database singolo](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-azure-portal) <br/> [Istanza gestita](sql-database-automated-backups.md#managed-instance-database) | [Database singolo](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Istanza gestita](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Modificare la conservazione dei backup a lungo termine | [Database singolo](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Istanza gestita-N/A  | [Database singolo](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups)<br/>Istanza gestita-N/A  |
| Ripristinare il database da un punto nel tempo | [Database singolo](sql-database-recovery-using-backups.md#point-in-time-restore) | [Database singolo](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Istanza gestita](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Ripristinare un database eliminato | [Database singolo](sql-database-recovery-using-backups.md) | [Database singolo](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Istanza gestita](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Ripristinare il database dall'archiviazione BLOB di Azure | Database singolo-N/A <br/>Istanza gestita-N/A  | Database singolo-N/A <br/>[Istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="how-long-are-backups-kept"></a>Per quanto tempo sono conservati i backup

Tutti i database SQL di Azure (singoli database di istanze gestite e in pool) hanno un periodo di conservazione dei backup predefinito di **sette** giorni. È possibile [modificare il periodo di conservazione dei backup fino a 35 giorni](#how-to-change-the-pitr-backup-retention-period).

Se si elimina un database, il database SQL manterrà i backup come farebbe con un database online. Ad esempio, se si elimina un database Basic con un periodo di conservazione di sette giorni, un backup di quattro giorni viene salvato per altri tre giorni.

Se è necessario conservare i backup per un periodo superiore al periodo di conservazione massimo, è possibile modificare le proprietà del backup per aggiungere uno o più periodi di conservazione a lungo termine al database. Per altre informazioni, vedere [Long-term retention](sql-database-long-term-retention.md) (Conservazione a lungo termine).

> [!IMPORTANT]
> Se si elimina SQL Server di Azure che ospita i database SQL, vengono eliminati anche tutti i database e i pool elastici appartenenti al server e non sarà possibile recuperarli. Non è possibile ripristinare un server eliminato. Tuttavia, se è stata configurata la conservazione a lungo termine, i backup per i database con conservazione a lungo termine non verranno cancellati e questi database potranno essere ripristinati.

## <a name="how-often-do-backups-happen"></a>Con quale frequenza si verificano i backup

### <a name="backups-for-point-in-time-restore"></a>Backup per il ripristino temporizzato

Database SQL supporta la funzionalità self-service per il ripristino temporizzato (PITR) mediante la creazione automatica di backup completi, backup differenziali e backup del log delle transazioni. I backup completi del database vengono creati ogni settimana, i backup differenziali del database vengono creati generalmente ogni 12 ore e i backup del log delle transazioni vengono creati in genere ogni 5-10 minuti, con la frequenza in base alle dimensioni di calcolo e alla quantità di attività del database. Il primo backup completo viene pianificato subito dopo la creazione di un database. Il completamento richiede in genere 30 minuti, ma potrebbe richiedere più tempo se le dimensioni del database sono elevate. Il backup iniziale, ad esempio, può richiedere più tempo in un database ripristinato o in una copia del database. Dopo il primo backup completo, l'esecuzione di tutti i successivi backup è pianificata e gestita automaticamente in background. Il momento esatto per l'esecuzione dei backup di database è determinato dal servizio SQL Database in modo da bilanciare il carico di lavoro complessivo del sistema. Non è possibile modificare o disabilitare i processi di backup. 

I backup di ripristino temporizzato sono a ridondanza geografica e protetti dalla [riproduzione su più aree di Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Per altre informazioni vedere l'articolo relativo al [Ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Backup per la conservazione a lungo termine

I database autonomi e in pool offrono la possibilità di configurare la conservazione a lungo termine dei backup completi fino a 10 anni in Archiviazione BLOB di Azure. Se il criterio LTR è abilitato, i backup completi settimanali vengono copiati automaticamente in un contenitore di Archiviazione con ridondanza geografica e accesso in lettura diverso. A secondo dei vari requisiti di conformità, è possibile selezionare periodi di conservazione diversi per i backup settimanali, mensili e/o annuali. L'utilizzo delle risorse di archiviazione dipende dalla frequenza selezionata con cui vengono eseguiti backup e dal periodo di conservazione. È possibile usare lo strumento di [calcolo dei prezzi per la conservazione a lungo termine](https://azure.microsoft.com/pricing/calculator/?service=sql-database) per stimare il costo di questo tipo di archiviazione.

Come i backup di ripristino temporizzato, i backup di conservazione a lungo termine sono a ridondanza geografica e protetti dalla [riproduzione su più aree di Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Per altre informazioni, vedere [Conservazione dei backup a lungo termine](sql-database-long-term-retention.md).

## <a name="storage-costs"></a>Costi di archiviazione
Per i database singoli e le istanze gestite, viene fornita una quantità minima di spazio di archiviazione per il backup pari al 100% delle dimensioni del database senza costi aggiuntivi. Per i pool elastici, un importo di archiviazione di backup minimo pari al 100% dell'archiviazione dei dati allocati per il pool viene fornito senza costi aggiuntivi. L'utilizzo aggiuntivo dell'archivio di backup verrà addebitato in base a GB/mese. Questo consumo aggiuntivo dipenderà dal carico di lavoro e dalle dimensioni dei singoli database.

Per altre informazioni sui prezzi delle risorse di archiviazione, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/sql-database/single/). 

## <a name="are-backups-encrypted"></a>I backup sono crittografati?

Se il database è crittografato con TDE, i backup vengono crittografati automaticamente quando i dati sono inattivi, inclusi i backup di conservazione a lungo termine. Quando la crittografia TDE viene abilitata per un database SQL di Azure, anche i backup vengono crittografati. Tutti i nuovi database SQL di Azure vengono configurati con la crittografia TDE abilitata per impostazione predefinita. Per altre informazioni su TDE, vedere [Transparent Data Encryption con il database SQL di Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-does-microsoft-ensure-backup-integrity"></a>In che modo Microsoft garantisce l'integrità dei backup

Con cadenza continuativa, il team di progettazione del database SQL di Azure testa automaticamente il ripristino dei backup automatici dei database inseriti nei server logici e nei pool elastici. questa operazione non è disponibile in Istanza gestita. Al momento del ripristino temporizzato, i database ricevono anche controlli di integrità tramite DBCC CHECKDB.

Istanza gestita esegue un backup iniziale automatico con `CHECKSUM` dei database ripristinati utilizzando il comando `RESTORE` nativo o il servizio migrazione dati una volta completata la migrazione.

Gli eventuali problemi rilevati durante la verifica dell'integrità determinano la generazione di un avviso per il team di progettazione. Per altre informazioni sull'integrità dei dati nel database SQL di Azure, vedere [Data Integrity in Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) (Integrità dei dati nel database SQL di Azure).

## <a name="how-do-automated-backups-impact-compliance"></a>In che modo i backup automatici influiscono sulla conformità

Quando si migra il database da un livello di servizio basato su DTU con una conservazione di ripristino temporizzato predefinita di 35 giorni a un livello di servizio basato sulla vCore, la conservazione di ripristino temporizzato viene preservata per garantire che il criterio di ripristino dei dati dell'applicazione non venga compromesso. Se il periodo di conservazione predefinito non soddisfa i requisiti di conformità, è possibile modificare il periodo di conservazione di ripristino temporizzato utilizzando PowerShell o API REST. Per altre informazioni, vedere [Modifica del periodo di conservazione backup](#how-to-change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>Come modificare il periodo di conservazione dei backup per il recupero temporizzato

È possibile modificare il periodo di conservazione predefinito del backup ripristino temporizzato usando il portale di Azure, PowerShell o l'API REST. I valori supportati sono: 7, 14, 21, 28 e 35 giorni. Gli esempi che seguono illustrano come modificare la conservazione di ripristino temporizzato a 28 giorni.

> [!WARNING]
> Se si riduce il periodo di conservazione corrente, tutti i backup esistenti precedenti al nuovo periodo di conservazione non saranno più disponibili. Se si aumenta il periodo di conservazione corrente, Database SQL manterrà i backup esistenti fino al raggiungimento del periodo di conservazione più lungo.

> [!NOTE]
> Queste API avranno un impatto solo sul periodo di conservazione del recupero temporizzato. Se è configurata una conservazione a lungo termine per il database, questo non sarà interessato. Per altre informazioni su come modificare i periodi di conservazione a lungo termine, vedere [Conservazione a lungo termine](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-azure-portal"></a>Modificare il periodo di conservazione del backup ripristino temporizzato usando portale di Azure

Per modificare il periodo di conservazione dei backup ripristino temporizzato usando il portale di Azure, passare all'oggetto server di cui si desidera modificare il periodo di memorizzazione nel portale e quindi selezionare l'opzione appropriata in base all'oggetto server che si sta modificando.

#### <a name="single-azure-sql-database"></a>Singolo database SQL di Azure

La modifica della conservazione dei backup ripristino temporizzato per i singoli database SQL di Azure viene eseguita a livello di server. Le modifiche apportate a livello di server si applicano ai database in tale server. Per modificare ripristino temporizzato per il server di database SQL di Azure da portale di Azure, passare al pannello panoramica del server, fare clic su Gestisci backup nel menu di navigazione, quindi fare clic su Configura conservazione sulla barra di spostamento.

![Modificare il portale di Azure per il recupero temporizzato](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance-database"></a>Database istanza gestita

La modifica della conservazione dei backup di ripristino temporizzato per l'istanza gestita di database SQL viene eseguita a livello di singolo database. Per modificare la conservazione dei backup di ripristino temporizzato per un database dell'istanza da portale di Azure, passare al pannello panoramica del singolo database e quindi fare clic su Configura conservazione backup nella barra di spostamento.

![Modificare il portale di Azure per il recupero temporizzato](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Modificare il periodo di conservazione dei backup di ripristino temporizzato usando PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per questi cmdlet, vedere [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRm sono sostanzialmente identici.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>Modificare il periodo di conservazione di ripristino temporizzato con l'API REST

#### <a name="sample-request"></a>Richiesta di esempio

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Request Body

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Risposta di esempio

Codice di stato: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Per altre informazioni, vedere [Backup Retention REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies) (API REST di conservazione dei backup).

## <a name="next-steps"></a>Passaggi successivi

- I backup dei database sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. Per informazioni sulle altre soluzioni di continuità aziendale del database SQL Azure, vedere [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).
- Per eseguire il ripristino temporizzato nel portale di Azure, vedere l'articolo sul [ripristino temporizzato nel portale di Azure](sql-database-recovery-using-backups.md).
- Per eseguire il ripristino temporizzato usando PowerShell, vedere l'articolo sul [ripristino temporizzato con PowerShell](scripts/sql-database-restore-database-powershell.md).
- Per configurare, gestire e ripristinare dalla conservazione a lungo termine di backup automatici in Archiviazione BLOB di Azure tramite il portale di Azure, vedere [Gestire la conservazione a lungo termine dei backup usando il portale di Azure](sql-database-long-term-backup-retention-configure.md).
- Per configurare, gestire e ripristinare dalla conservazione a lungo termine di backup automatici nell'archivio BLOB di Azure tramite PowerShell, vedere [gestire la conservazione dei backup a lungo termine usando PowerShell](sql-database-long-term-backup-retention-configure.md).

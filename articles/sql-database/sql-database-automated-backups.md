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
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 05/20/2019
ms.openlocfilehash: 3e56244f074e31672cf77bc74998096e215a4db7
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357338"
---
# <a name="automated-backups"></a>Backup automatizzati

Il Database SQL automaticamente consente di creare i backup del database in cui vengono mantenuti tra 7 e 35 giorni e Usa Azure [archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) per garantire che vengono conservati anche se il data center non è disponibile. Questi backup vengono creati automaticamente e senza costi aggiuntivi. Non è necessario intervenire manualmente per eseguire i backup. I backup dei database sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. Se le regole di sicurezza richiedono che i backup siano disponibili per un lungo periodo di tempo (fino a 10 anni), è possibile configurare un [conservazione a lungo termine](sql-database-long-term-retention.md) per i database Singleton ed Elastic pool.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Informazioni sul backup del database SQL

Database SQL Usa la tecnologia di SQL Server per creare [i backup completi](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) ogni settimana [differenziali](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) ogni 12 ore, e [backup del log delle transazioni](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) ogni 5-10 minuti. I backup vengono archiviati nel [BLOB di archiviazione RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) che vengono replicati in un [data center abbinato](../best-practices-availability-paired-regions.md) per la protezione da interruzioni prolungate nel data center. Quando si ripristina un database, il servizio individua i backup completi, differenziali e del log delle transazioni da ripristinare.

È possibile usare questi backup per:

- **Ripristinare un database esistente in un punto nel tempo nel passato** entro il periodo di conservazione usando il portale di Azure, Azure PowerShell, CLI di Azure o l'API REST. In un database singolo e pool elastici, questa operazione creerà un nuovo database nello stesso server del database originale. In istanza gestita, questa operazione può creare una copia del database o uguali o diversi istanza gestita nella stessa sottoscrizione.
  - **[Modificare il periodo di conservazione dei Backup](#how-to-change-the-pitr-backup-retention-period)**  compreso tra da 7 a 35 giorni per configurare i criteri di backup.
  - **Modificare i criteri di conservazione a lungo termine 10 anni** sul Database singolo e pool elastici usando [portale di Azure](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies) oppure [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups).
- **Ripristinare un database eliminato al momento in cui è stato eliminato** o in qualsiasi momento entro il periodo di conservazione. Il database eliminato può essere ripristinato solo nello stesso server logico o istanza gestita in cui è stato creato il database originale.
- **Ripristinare un database in un'altra area geografica**. Il ripristino geografico consente di eseguire un ripristino di emergenza geografico quando è impossibile accedere al server e al database. Crea un nuovo database in qualsiasi server esistente ovunque nel mondo.
- **Ripristinare un database da un backup a lungo termine specifico** su Database singolo o Pool elastico, se il database è stato configurato con criteri di conservazione a lungo termine (conservazione a lungo termine). Conservazione a lungo termine consente di ripristinare una versione precedente del database usando [portale di Azure](sql-database-long-term-backup-retention-configure.md#view-backups-and-restore-from-a-backup-using-azure-portal) oppure [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups) per soddisfare una richiesta di conformità o per eseguire una versione precedente dell'applicazione. Per altre informazioni, vedere [Long-term retention](sql-database-long-term-retention.md) (Conservazione a lungo termine).
- Per eseguire un ripristino, vedere l'articolo su come [ripristinare un database da un backup](sql-database-recovery-using-backups.md).

> [!NOTE]
> In Archiviazione di Azure il termine *replica* fa riferimento alla copia dei file da una località a un'altra. La *replica di database* di SQL fa riferimento a più database secondari sincronizzati con un database primario.

È possibile provare alcune di queste operazioni usando gli esempi seguenti:

| | Portale di Azure | Azure PowerShell |
|---|---|---|
| Modificare la conservazione backup | [Database singolo](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-the-azure-portal) <br/> [Istanza gestita](sql-database-automated-backups.md#change-pitr-for-a-managed-instance) | [Database singolo](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Istanza gestita](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Modifica conservazione backup a lungo termine | [Database singolo](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Istanza gestita: n/d  | [Database singolo](sql-database-long-term-backup-retention-configure.md#use-powershell-to-configure-long-term-retention-policies-and-restore-backups)<br/>Istanza gestita: n/d  |
| Ripristinare database da un punto nel tempo | [Database singolo](sql-database-recovery-using-backups.md#point-in-time-restore) | [Database singolo](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Istanza gestita](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Ripristinare un database eliminato | [Database singolo](sql-database-recovery-using-backups.md#deleted-database-restore-using-the-azure-portal) | [Database singolo](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Istanza gestita](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Ripristinare database da archiviazione Blob di Azure | Database Single - n/d <br/>Istanza gestita: n/d  | Database Single - n/d <br/>[Istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="how-long-are-backups-kept"></a>Per quanto tempo sono conservati i backup

Ogni database SQL ha un periodo di conservazione dei backup predefinito che varia da 7 e 35 giorni e dipende dal modello di acquisto e dal livello di servizio. È possibile aggiornare il periodo di conservazione dei backup per un database in Database di SQL server. Per altre informazioni, vedere [Modifica del periodo di conservazione backup](#how-to-change-the-pitr-backup-retention-period).

Se si elimina un database, il database SQL manterrà i backup come farebbe con un database online. Ad esempio, se si elimina un database Basic con un periodo di conservazione di sette giorni, un backup di quattro giorni viene salvato per altri tre giorni.

Se è necessario conservare i backup per un periodo superiore al periodo di conservazione massimo, è possibile modificare le proprietà del backup per aggiungere uno o più periodi di conservazione a lungo termine al database. Per altre informazioni, vedere [Long-term retention](sql-database-long-term-retention.md) (Conservazione a lungo termine).

> [!IMPORTANT]
> Se si elimina SQL Server di Azure che ospita i database SQL, vengono eliminati anche tutti i database e i pool elastici appartenenti al server e non sarà possibile recuperarli. Non è possibile ripristinare un server eliminato. Tuttavia, se è stata configurata la conservazione a lungo termine, i backup per i database con conservazione a lungo termine non verranno cancellati e questi database potranno essere ripristinati.

### <a name="default-backup-retention-period"></a>Periodo di conservazione dei backup predefinito

#### <a name="dtu-based-purchasing-model"></a>modello di acquisto basato su DTU

Il periodo di conservazione predefinito per un database creato tramite il modello di acquisto basato su DTU varia in base al livello di servizio:

- Livello di servizio Basic viene **uno** settimana.
- Livello di servizio standard viene **cinque** settimane.
- Livello di servizio Premium viene **cinque** settimane.

#### <a name="vcore-based-purchasing-model"></a>Modello di acquisto basato su vCore

Se si usa la [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md), il periodo di conservazione dei backup predefinito è **sette** giorni (per un singolo, in pool e database dell'istanza). Per tutti i database SQL di Azure (database singoli, in pool e dell'istanza), è possibile [modificare il periodo di conservazione dei backup fino a un massimo di 35 giorni](#how-to-change-the-pitr-backup-retention-period).

> [!WARNING]
> Se si riduce il periodo di memorizzazione corrente, tutti i backup esistenti anteriori al periodo di conservazione dei nuovi non sono più disponibili. Se si aumenta il periodo di conservazione corrente, Database SQL manterrà i backup esistenti fino al raggiungimento del periodo di conservazione più lungo.

## <a name="how-often-do-backups-happen"></a>Con quale frequenza si verificano i backup

### <a name="backups-for-point-in-time-restore"></a>Backup per il ripristino temporizzato

Database SQL supporta la funzionalità self-service per il ripristino temporizzato (PITR) mediante la creazione automatica di backup completi, backup differenziali e backup del log delle transazioni. I backup completi del database vengono creati ogni settimana, i backup differenziali del database vengono creati generalmente ogni 12 ore e i backup del log delle transazioni vengono creati in genere ogni 5-10 minuti, con la frequenza in base alle dimensioni di calcolo e alla quantità di attività del database. Il primo backup completo viene pianificato subito dopo la creazione di un database. Il completamento richiede in genere 30 minuti, ma potrebbe richiedere più tempo se le dimensioni del database sono elevate. Il backup iniziale, ad esempio, può richiedere più tempo in un database ripristinato o in una copia del database. Dopo il primo backup completo, l'esecuzione di tutti i successivi backup è pianificata e gestita automaticamente in background. Il momento esatto per l'esecuzione dei backup di database è determinato dal servizio SQL Database in modo da bilanciare il carico di lavoro complessivo del sistema. È possibile modificare o disabilitare i processi di backup. 

I backup di ripristino temporizzato sono a ridondanza geografica e protetti dalla [riproduzione su più aree di Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Per altre informazioni vedere l'articolo relativo al [Ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Backup per la conservazione a lungo termine

I database autonomi e in pool offrono la possibilità di configurare la conservazione a lungo termine dei backup completi fino a 10 anni in Archiviazione BLOB di Azure. Se il criterio LTR è abilitato, i backup completi settimanali vengono copiati automaticamente in un contenitore di Archiviazione con ridondanza geografica e accesso in lettura diverso. A secondo dei vari requisiti di conformità, è possibile selezionare periodi di conservazione diversi per i backup settimanali, mensili e/o annuali. L'utilizzo delle risorse di archiviazione dipende dalla frequenza selezionata con cui vengono eseguiti backup e dal periodo di conservazione. È possibile usare lo strumento di [calcolo dei prezzi per la conservazione a lungo termine](https://azure.microsoft.com/pricing/calculator/?service=sql-database) per stimare il costo di questo tipo di archiviazione.

Come i backup di ripristino temporizzato, i backup di conservazione a lungo termine sono a ridondanza geografica e protetti dalla [riproduzione su più aree di Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Per altre informazioni, vedere [Conservazione dei backup a lungo termine](sql-database-long-term-retention.md).

## <a name="storage-costs"></a>Costi di archiviazione
Sette giorni di backup automatizzati dei tuoi database vengono copiati nell'archivio BLOB Standard con archiviazione con ridondanza geografica e accesso in lettura per impostazione predefinita. Le risorse di archiviazione vengono usate da backup completi settimanali, backup differenziali giornalieri e backup del log delle transazioni copiati ogni 5 minuti. Le dimensioni del log delle transazioni dipendono dalla frequenza di modifica del database. Uno spazio di archiviazione minimo equivalente al 100% delle dimensioni del database viene fornito senza addebiti aggiuntivi. L'utilizzo aggiuntivo dell'archivio di backup verrà addebitato in base a GB/mese.

Per altre informazioni sui prezzi delle risorse di archiviazione, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/sql-database/single/). 

## <a name="are-backups-encrypted"></a>I backup sono crittografati?

Se il database è crittografato con TDE, i backup vengono crittografati automaticamente quando i dati sono inattivi, inclusi i backup di conservazione a lungo termine. Quando la crittografia TDE viene abilitata per un database SQL di Azure, anche i backup vengono crittografati. Tutti i nuovi database SQL di Azure vengono configurati con la crittografia TDE abilitata per impostazione predefinita. Per altre informazioni su TDE, vedere [Transparent Data Encryption con il database SQL di Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-does-microsoft-ensure-backup-integrity"></a>In che modo Microsoft garantisce l'integrità dei backup

In modo continuativo, il team di progettazione del database SQL di Azure verifica automaticamente il ripristino dei backup automatici dei database nel servizio. Al momento del ripristino, i database sono anche sottoposti a controlli di integrità tramite DBCC CHECKDB. Gli eventuali problemi rilevati durante la verifica dell'integrità determinano la generazione di un avviso per il team di progettazione. Per altre informazioni sull'integrità dei dati nel database SQL di Azure, vedere [Data Integrity in Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) (Integrità dei dati nel database SQL di Azure).

## <a name="how-do-automated-backups-impact-compliance"></a>In che modo i backup automatici influiscono sulla conformità

Quando si migra il database da un livello di servizio basato su DTU con una conservazione di ripristino temporizzato predefinita di 35 giorni a un livello di servizio basato sulla vCore, la conservazione di ripristino temporizzato viene preservata per garantire che il criterio di ripristino dei dati dell'applicazione non venga compromesso. Se il periodo di conservazione predefinito non soddisfa i requisiti di conformità, è possibile modificare il periodo di conservazione di ripristino temporizzato utilizzando PowerShell o API REST. Per altre informazioni, vedere [Modifica del periodo di conservazione backup](#how-to-change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>Come modificare il periodo di conservazione dei backup per il recupero temporizzato

È possibile modificare il periodo di conservazione dei backup Temporizzato predefinito tramite il portale di Azure, PowerShell o l'API REST. I valori supportati sono: 7, 14, 21, 28 giorni o 35 giorni. Gli esempi che seguono illustrano come modificare la conservazione di ripristino temporizzato a 28 giorni.

> [!NOTE]
> Queste API avranno un impatto solo sul periodo di conservazione del recupero temporizzato. Se è configurata una conservazione a lungo termine per il database, questo non sarà interessato. Per altre informazioni su come modificare i periodi di conservazione a lungo termine, vedere [Conservazione a lungo termine](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-the-azure-portal"></a>Modificare il periodo di conservazione dei backup per il recupero temporizzato usando il portale di Azure

Per modificare il periodo di conservazione dei backup di ripristino Temporizzato nel portale di Azure, passare all'oggetto server il cui periodo di memorizzazione che si desidera modificare all'interno del portale e quindi selezionare l'opzione appropriata in base nell'oggetto server di cui si sta modificando.

#### <a name="change-pitr-for-a-sql-database-server"></a>Modificare il recupero temporizzato per un server di database SQL

![Modificare il portale di Azure per il recupero temporizzato](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="change-pitr-for-a-managed-instance"></a>Modificare il recupero temporizzato per un'istanza gestita

![Modificare il portale di Azure per il recupero temporizzato](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Modificare il periodo di conservazione dei backup di ripristino temporizzato usando PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure PowerShell per Resource Manager è ancora supportato dal Database SQL di Azure, ma i progetti di sviluppo future è per il modulo Az.Sql. Per questi cmdlet, vedere [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli AzureRm sono sostanzialmente identici.

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
- Per configurare, gestire e ripristinare dalla conservazione a lungo termine dei backup automatici in archiviazione Blob di Azure con PowerShell, vedere [gestire conservazione backup a lungo termine usando PowerShell](sql-database-long-term-backup-retention-configure.md).

---
title: Backup automatici con ridondanza geografica
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
ms.date: 12/13/2019
ms.openlocfilehash: 9ac6927df63d51830a58773e32ad0968920c0867
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061771"
---
# <a name="automated-backups"></a>Backup automatizzati

Il database SQL di Azure crea automaticamente i backup del database conservati per la durata del periodo di conservazione configurato. Usa l'archiviazione con ridondanza geografica di accesso in lettura di Azure [(RA-GRS)](../storage/common/storage-redundancy.md) per garantire che i backup vengano mantenuti anche se il data center non è disponibile.

I backup dei database sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. Se le regole di sicurezza richiedono che i backup siano disponibili per un periodo di tempo prolungato (fino a 10 anni), è possibile configurare la [conservazione a lungo termine nei](sql-database-long-term-retention.md) database singleton e nei pool di database elastici.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Informazioni sul backup del database SQL

Il database SQL utilizza la tecnologia SQL Server per creare [backup completi](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) ogni settimana, [backup differenziali](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) ogni 12 ore e [backup del log delle transazioni](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) ogni 5-10 minuti. I backup vengono archiviati in BLOB di [archiviazione RA-GRS](../storage/common/storage-redundancy.md) replicati in un [data center accoppiato](../best-practices-availability-paired-regions.md) per la protezione in un'interruzione del datacenter. Quando si ripristina un database, il servizio determina quali backup completi, differenziali e del log delle transazioni devono essere ripristinati.

È possibile usare questi backup per:

- **Ripristinare un database esistente a un punto nel tempo nel passato** nel periodo di conservazione usando il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Per i singoli database e i pool di database elastici, questa operazione creerà un nuovo database nello stesso server del database originale. Nell'istanza gestita, questa operazione può creare una copia del database o la stessa o un'istanza gestita diversa nella stessa sottoscrizione.
- **Ripristinare un database eliminato all'ora dell'eliminazione** o in qualsiasi momento all'interno del periodo di conservazione. Il database eliminato può essere ripristinato solo nello stesso server logico o nello stesso'istanza gestita in cui è stato creato il database originale.
- Ripristinare un database in **un'altra area geografica.** Il ripristino geografico consente di eseguire il ripristino da un'emergenza geografica quando non è possibile accedere al server e al database. Crea un nuovo database su qualsiasi server esistente, in qualsiasi parte del mondo.
- **Ripristinare un database da un backup a lungo termine specifico** in un singolo database o in un pool di database elastico, se il database è configurato con un criterio di conservazione a lungo termine. LTR consente di ripristinare una versione precedente del database usando [il portale](sql-database-long-term-backup-retention-configure.md#using-azure-portal) di Azure o [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) per soddisfare una richiesta di conformità o per eseguire una versione precedente dell'applicazione. Per ulteriori informazioni, vedere [Conservazione a lungo termine](sql-database-long-term-retention.md).

Per eseguire un ripristino, vedere [Ripristino di database da backup](sql-database-recovery-using-backups.md).

> [!NOTE]
> In Archiviazione di Azure il termine replica si *riferisce* alla copia di file da una posizione a un'altra. Per *replica di database* di SQL Server SI intende la conservazione di più database secondari sincronizzati con un database primario.

È possibile provare alcune di queste operazioni utilizzando gli esempi seguenti:You can try some of these operations by using the following examples:

| | Portale di Azure | Azure PowerShell |
|---|---|---|
| Modificare la conservazione dei backup | [Database singolo](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Istanza gestita](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Database singolo](sql-database-automated-backups.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Istanza gestita](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Modificare la conservazione dei backup a lungo termine | [Database singolo](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Istanza gestita - N/D  | [Database singolo](sql-database-long-term-backup-retention-configure.md)<br/>Istanza gestita - N/D  |
| Ripristinare un database da un punto nel tempo | [Database singolo](sql-database-recovery-using-backups.md#point-in-time-restore) | [Database singolo](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Istanza gestita](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Ripristino di un database eliminato | [Database singolo](sql-database-recovery-using-backups.md) | [Database singolo](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Istanza gestita](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Ripristinare un database dall'archiviazione BLOB di AzureRestore a database from Azure Blob storage | Database singolo - N/D <br/>Istanza gestita - N/D  | Database singolo - N/D <br/>[Istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Frequenza di backup

### <a name="point-in-time-restore"></a>Ripristino temporizzato

Il database SQL supporta il servizio self-service per il ripristino temporizzato (PITR) mediante la creazione automatica di backup completi, backup differenziali e backup del log delle transazioni. I backup completi del database vengono creati settimanalmente e i backup differenziali del database vengono in genere creati ogni 12 ore. I backup del log delle transazioni vengono in genere creati ogni 5-10 minuti. La frequenza dei backup del log delle transazioni si basa sulle dimensioni di calcolo e sulla quantità di attività del database. 

Il primo backup completo viene pianificato subito dopo la creazione di un database. Questo backup viene in genere completato entro 30 minuti, ma può richiedere più tempo quando il database è di grandi dimensioni. Il backup iniziale, ad esempio, può richiedere più tempo in un database ripristinato o in una copia del database. Dopo il primo backup completo, l'esecuzione di tutti i successivi backup è pianificata e gestita automaticamente in background. Il momento esatto per l'esecuzione dei backup di database è determinato dal servizio SQL Database in modo da bilanciare il carico di lavoro complessivo del sistema. Non è possibile modificare o disabilitare i processi di backup.

I backup PITR sono protetti con storage con ridondanza geografica. Per altre informazioni, vedere [Ridondanza di Archiviazione di Azure](../storage/common/storage-redundancy.md).

Per ulteriori informazioni su PITR, vedere [Ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore).

### <a name="long-term-retention"></a>Conservazione a lungo termine

Per i database singoli e in pool, è possibile configurare la conservazione a lungo termine (LTR) dei backup completi per un massimo di 10 anni nell'archiviazione BLOB di Azure.For single and pooled databases, you can configure long-term retention (LTR) of full backups for up to 10 years in Azure Blob storage. Se si abilitano i criteri LTR, i backup completi settimanali vengono copiati automaticamente in un contenitore di archiviazione RA-GRS diverso. Per soddisfare i vari requisiti di conformità, è possibile selezionare periodi di conservazione diversi per i backup settimanali, mensili e/o annuali. Il consumo di spazio di archiviazione dipende dalla frequenza selezionata dei backup e dal periodo o periodo di conservazione. È possibile utilizzare il calcolatore dei [prezzi LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) per stimare il costo dello storage LTR.

Analogamente ai backup PITR, i backup LTR sono protetti con storage con ridondanza geografica. Per altre informazioni, vedere [Ridondanza di Archiviazione di Azure](../storage/common/storage-redundancy.md).

Per ulteriori informazioni su LTR, vedere [Conservazione dei backup a lungo termine](sql-database-long-term-retention.md).

## <a name="backup-storage-consumption"></a>Consumo di archiviazione di backup

Per i singoli database, questa equazione viene utilizzata per calcolare l'utilizzo totale dello spazio di archiviazione di backup:For single databases, this equation is used to calculate the total backup storage usage:

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

Per i pool di database elastici, la dimensione totale dell'archiviazione di backup viene aggregata a livello di pool e calcolata come segue:

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

I backup che si verificano prima del periodo di conservazione vengono eliminati automaticamente in base al timestamp. Poiché i backup differenziali e i backup del log richiedono l'utilità di un backup completo precedente, vengono eliminati insieme in blocchi settimanali.

Il database SQL di Azure calcola l'archiviazione totale del backup in conservazione come valore cumulativo. Ogni ora, questo valore viene segnalato alla pipeline di fatturazione di Azure, che è responsabile dell'aggregazione di questo utilizzo orario per calcolare il consumo alla fine di ogni mese. Dopo l'eliminazione del database, il consumo diminuisce con l'età dei backup. Dopo che i backup sono diventati più vecchi del periodo di conservazione, la fatturazione viene interrotta.

   > [!IMPORTANT]
   > I backup di un database vengono conservati per il periodo di conservazione specificato, anche se il database è stato eliminato. Mentre l'eliminazione e la ricreazione di un database possono spesso risparmiare sui costi di storage e calcolo, potrebbe aumentare i costi di archiviazione dei backup perché Microsoft conserva un backup per il periodo di conservazione specificato (che è almeno 7 giorni) per ogni database eliminato, volta che è caduto.

### <a name="monitor-consumption"></a>Monitorare il consumo

Ogni tipo di backup (completo, differenziale e log) viene segnalato nel pannello di monitoraggio del database come metrica separata. Nel diagramma seguente viene illustrato come monitorare l'utilizzo dell'archiviazione di backup per un singolo database. Questa funzionalità non è attualmente disponibile per le istanze gestite.

![Monitorare l'utilizzo del backup del database nel portale di AzureMonitor database backup consumption in the Azure portal](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Ottimizzare l'utilizzo dello storage di backup

L'eccesso di utilizzo dello storage di backup dipende dal carico di lavoro e dalle dimensioni dei singoli database. Prendere in considerazione alcune delle tecniche di ottimizzazione seguenti per ridurre il consumo di archiviazione di backup:

* Riduci il periodo di conservazione dei [backup](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) al minimo possibile per le tue esigenze.
* Evitare di eseguire operazioni di scrittura di grandi dimensioni, ad esempio ricompilazioni di indici, con maggiore frequenza del necessario.
* Per le operazioni di caricamento dei dati di grandi dimensioni, prendere in considerazione l'utilizzo di [indici columnstore cluster](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), ridurre il numero di indici non cluster e considerare le operazioni di caricamento bulk con un conteggio di dimensioni pari a circa 1 milione.
* Nel livello di servizio generico, l'archiviazione dei dati di cui è stato eseguito il provisioning è meno costosa del prezzo dell'archiviazione di backup in eccesso. Se si dispone di costi di archiviazione di backup in continuo aumento, è consigliabile aumentare l'archiviazione dei dati per risparmiare sull'archiviazione di backup.
* Usare TempDB anziché le tabelle permanenti nella logica ETL per l'archiviazione di risultati temporanei. (Applicabile solo all'istanza gestita.)
* Valutare la possibilità di disattivare la crittografia TDE per i database che non contengono dati sensibili ( ad esempio, database di sviluppo o di test). I backup per i database non crittografati vengono in genere compressi con un rapporto di compressione più elevato.

> [!IMPORTANT]
> Per i carichi di lavoro del data warehouse analitico, è consigliabile utilizzare [gli indici columnstore cluster,](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)ridurre il numero di indici non cluster e considerare le operazioni di caricamento bulk con un numero di righe pari a circa 1 milione per ridurre l'utilizzo eccessivo dello spazio di archiviazione di backup.

## <a name="storage-costs"></a>Costi di archiviazione

Il prezzo per l'archiviazione varia a seconda che si utilizzi il modello DTU o il modello vCore.

### <a name="dtu-model"></a>Modello DTU

Non sono previsti costi aggiuntivi per l'archiviazione di backup per database e pool di database elastici se si utilizza il modello DTU.

### <a name="vcore-model"></a>Modello vCore

Per i singoli database, viene fornita una quantità minima di archiviazione di backup pari al 100% delle dimensioni del database senza costi aggiuntivi. Per i pool di database elastici e le istanze gestite, una quantità minima di archiviazione di backup pari al 100% dell'archiviazione dei dati allocata per il pool o le dimensioni dell'istanza, rispettivamente, viene fornita senza costi aggiuntivi. L'utilizzo aggiuntivo dell'archivio di backup verrà addebitato in base a GB/mese. Questo consumo aggiuntivo dipenderà dal carico di lavoro e dalle dimensioni dei singoli database.

Il database SQL di Azure calcolerà l'archiviazione totale del backup in conservazione come valore cumulativo. Ogni ora, questo valore viene segnalato alla pipeline di fatturazione di Azure, che è responsabile dell'aggregazione di questo utilizzo orario per ottenere il consumo alla fine di ogni mese. Dopo l'eliminazione del database, Microsoft riduce il consumo in base all'età dei backup. Dopo che i backup sono diventati più vecchi del periodo di conservazione, la fatturazione viene interrotta. Poiché tutti i backup del log e i backup differenziali vengono conservati per il periodo di conservazione completo, i database pesantemente modificati avranno costi di backup più elevati.

Si supponga che un database ha accumulato 744 GB di spazio di archiviazione di backup e che questa quantità rimane costante per tutto il mese. Per convertire questo consumo cumulativo di storage in utilizzo orario, dividerlo per 744.0 (31 giorni al mese - 24 ore al giorno). Così database SQL segnalerà che il database utilizzato 1 GB di backup PITR ogni ora. La fatturazione di Azure aggregherà questo consumo e mostrerà un utilizzo di 744 GB per l'intero mese. Il costo sarà basato sulla tariffa di /GB/mese nella tua regione.

Ora, un esempio più complesso. Si supponga che la conservazione del database è aumentata a 14 giorni a metà mese. Si supponga che questo aumento (ipoteticamente) comporta il raddoppio dello spazio di archiviazione di backup totale a 1.488 GB. Il database SQL segnalerebbe 1 GB di utilizzo per le ore da 1 a 372. Sarebbe segnalare l'utilizzo come 2 GB per ore 373 a 744. Questo utilizzo verrà aggregato a una fattura finale di 1.116 GB/mese.

### <a name="monitor-costs"></a>Monitorare i costi

Per comprendere i costi di archiviazione dei backup, passare a **Gestione costi - Fatturazione** nel portale di Azure, selezionare Gestione **costi**e quindi Analisi **dei costi**. Selezionare la sottoscrizione desiderata come **Ambito**, quindi filtrare in base al periodo di tempo e al servizio a cui si è interessati.

Aggiungere un filtro per **Nome servizio**e quindi selezionare **database sql** nell'elenco a discesa. Utilizzare il filtro **sottocategoria del contatore** per scegliere il contatore di fatturazione per il servizio. Per un singolo database o un pool di database elastico, selezionare Archiviazione di backup pool **singolo/elastico.** Per un'istanza gestita, selezionare **mi pitr backup storage**. Anche le sottocategorie **Archiviazione** e **calcolo** potrebbero interessarti, ma non sono associate ai costi di archiviazione dei backup.

![Analisi dei costi di archiviazione dei backup](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>Conservazione backup

Tutti i database SQL di Azure (database singoli, in pool e dell'istanza gestita) hanno un periodo di conservazione del backup predefinito di 7 giorni. È possibile modificare il periodo di [conservazione del backup](#change-the-pitr-backup-retention-period) fino a 35 giorni.

Se si elimina un database, il database SQL manterrà i backup come farebbe con un database online. Ad esempio, se si elimina un database di base con un periodo di conservazione di sette giorni, un backup vecchio di quattro giorni viene salvato per altri tre giorni.

Se è necessario conservare i backup per un periodo superiore al periodo di conservazione massimo, è possibile modificare le proprietà del backup per aggiungere uno o più periodi di conservazione a lungo termine al database. Per ulteriori informazioni, vedere [Conservazione a lungo termine](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Se si elimina il server SQL di Azure che ospita i database SQL, vengono eliminati anche tutti i pool di database elastici e i database che appartengono al server. Non possono essere recuperati. Non è possibile ripristinare un server eliminato. Tuttavia, se è stata configurata la conservazione a lungo termine, i backup per i database con LTR non verranno eliminati e questi database potranno essere ripristinati.

## <a name="encrypted-backups"></a>Backup crittografati

Se il database è crittografato con TDE, i backup vengono automaticamente crittografati inattivi, inclusi i backup LTR. Quando la crittografia TDE viene abilitata per un database SQL di Azure, anche i backup vengono crittografati. Tutti i nuovi database SQL di Azure vengono configurati con la crittografia TDE abilitata per impostazione predefinita. Per altre informazioni su TDE, vedere [Transparent Data Encryption con il database SQL di Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integrità del backup

In modo continuativo, il team di progettazione del database SQL di Azure verifica automaticamente il ripristino dei backup automatici dei database dei database inseriti in server logici e pool di database elastici. Questo test non è disponibile nell'istanza gestita. Al momento del ripristino temporizzato, i database ricevono anche i controlli di integrità DBCC CHECKDB.

L'istanza gestita `CHECKSUM` esegue il backup `RESTORE` iniziale automatico con i database ripristinati con il comando nativo o con il servizio Migrazione dati di Azure al termine della migrazione.

Gli eventuali problemi rilevati durante la verifica dell'integrità determinano la generazione di un avviso per il team di progettazione. Per altre informazioni, vedere Integrità dei dati nel database SQL di Azure.For more information, see [Data Integrity in Azure SQL Database.](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="compliance"></a>Conformità

Quando si esegue la migrazione del database da un livello di servizio basato su DTU a un livello di servizio basato su vCore, la conservazione PITR viene mantenuta per garantire che i criteri di recupero dei dati dell'applicazione non vengano compromessi. Se la conservazione predefinita non soddisfa i requisiti di conformità, è possibile modificare il periodo di conservazione PITR usando PowerShell o l'API REST. Per ulteriori informazioni, vedere Modificare il periodo di [conservazione del backup PITR](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Modificare il periodo di conservazione del backup PITR

È possibile modificare il periodo di conservazione del backup PITR predefinito usando il portale di Azure, PowerShell o l'API REST. Negli esempi seguenti viene illustrato come modificare la conservazione PITR in 28 giorni.

> [!WARNING]
> Se si riduce il periodo di conservazione corrente, tutti i backup esistenti precedenti al nuovo periodo di conservazione non saranno più disponibili. Se si aumenta il periodo di conservazione corrente, il database SQL manterrà i backup esistenti fino al raggiungito termine del periodo di conservazione più lungo.

> [!NOTE]
> Queste API influiranno solo sul periodo di conservazione PITR. Se LTR è stato configurato per il database, non sarà interessato. Per informazioni su come modificare i periodi di conservazione DellTR, vedere [Conservazione a lungo termine](sql-database-long-term-retention.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Modificare il periodo di conservazione del backup PITR tramite il portale di AzureChange the PITR backup retention period by using the Azure portal

Per modificare il periodo di conservazione del backup PITR tramite il portale di Azure, passare all'oggetto server di cui si vuole modificare il periodo di conservazione nel portale. Selezionare quindi l'opzione appropriata in base all'oggetto server che si sta modificando.

#### <a name="single-database-and-elastic-database-pools"></a>[Pool di database e di database eelastici singoli database](#tab/single-database)

Le modifiche alla conservazione dei backup PITR per singoli database SQL di Azure vengono eseguite a livello di server. Le modifiche apportate a livello di server si applicano ai database nel server. Per modificare la conservazione PITR per un server di database SQL di Azure dal portale di Azure, passare al pannello Panoramica del server. Selezionare **Gestisci backup** nel riquadro sinistro e quindi selezionare **Configura conservazione** nella parte superiore dello schermo:

![Modificare la conservazione PITR, a livello di server](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[Istanza gestita](#tab/managed-instance)

Le modifiche alla conservazione dei backup PITR per le istanze gestite del database SQL vengono eseguite a livello di singolo database. Per modificare la conservazione del backup PITR per un database di istanza dal portale di Azure, passare al pannello Panoramica database singolo. Selezionare **quindi Configura conservazione backup** nella parte superiore dello schermo:

![Modificare la conservazione PITR, istanza gestitaChange PITR retention, managed instance](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>Modificare il periodo di conservazione del backup PITR tramite PowerShellChange the PITR backup retention period by using PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo PowerShell AzureRM è ancora supportato dal database SQL di Azure, ma tutto lo sviluppo futuro è per il modulo Az.Sql.The PowerShell AzureRM module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. Per altre informazioni, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az sono sostanzialmente identici a quelli nei moduli di AzureRm.The arguments for the commands in the Az module are sostanzialment identical to those in the AzureRm modules.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>Modificare il periodo di conservazione del backup PITR utilizzando l'API REST

#### <a name="sample-request"></a>Richiesta di esempio

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Corpo della richiesta

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
- Altre informazioni su come ripristinare un database a un punto nel tempo usando il portale di [Azure.](sql-database-recovery-using-backups.md)
- Per altre informazioni su come [ripristinare un database a un punto nel tempo, usare PowerShell](scripts/sql-database-restore-database-powershell.md).
- Per informazioni su come configurare, gestire e ripristinare dalla conservazione a lungo termine dei backup automatici nell'archiviazione BLOB di Azure tramite il portale di Azure, vedere Gestire la [conservazione dei backup a lungo termine tramite il portale](sql-database-long-term-backup-retention-configure.md)di Azure.
- Per informazioni su come configurare, gestire e ripristinare dalla conservazione a lungo termine dei backup automatici nell'archivio BLOB di Azure tramite PowerShell, vedere Gestire la [conservazione dei backup a lungo termine tramite PowerShell.](sql-database-long-term-backup-retention-configure.md)

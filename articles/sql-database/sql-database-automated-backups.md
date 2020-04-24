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

Il database SQL di Azure crea automaticamente i backup del database mantenuti per la durata del periodo di memorizzazione configurato. Usa l' [archiviazione con ridondanza geografica e accesso in lettura di Azure (RA-GRS)](../storage/common/storage-redundancy.md) per garantire che i backup vengano conservati anche se il Data Center non è disponibile.

I backup dei database sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. Se le regole di sicurezza richiedono che i backup siano disponibili per un periodo di tempo prolungato (fino a 10 anni), è possibile configurare la [conservazione a lungo termine](sql-database-long-term-retention.md) in database singleton e pool di database elastici.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Informazioni sul backup del database SQL

Il database SQL usa la tecnologia SQL Server per creare [backup completi](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) ogni settimana, [backup differenziali](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) ogni 12 ore e [backup del log delle transazioni](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) ogni 5-10 minuti. I backup vengono archiviati nei [BLOB di archiviazione RA-GRS](../storage/common/storage-redundancy.md) replicati in un [Data Center associato](../best-practices-availability-paired-regions.md) per la protezione da un'interruzione del Data Center. Quando si ripristina un database, il servizio determina quali backup completi, differenziali e del log delle transazioni devono essere ripristinati.

È possibile usare questi backup per:

- **Ripristinare un database esistente fino a un punto nel tempo precedente** entro il periodo di conservazione usando il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Per i database singoli e i pool di database elastici, questa operazione creerà un nuovo database nello stesso server del database originale. In istanza gestita, questa operazione può creare una copia del database o della stessa istanza gestita o di una diversa nella stessa sottoscrizione.
- **Ripristinare un database eliminato al momento dell'eliminazione** o in qualsiasi momento entro il periodo di memorizzazione. Il database eliminato può essere ripristinato solo nello stesso server logico o in un'istanza gestita in cui è stato creato il database originale.
- **Ripristinare un database in un'altra area geografica**. Il ripristino geografico consente di eseguire il ripristino da un'emergenza geografica quando non è possibile accedere al server e al database. Consente di creare un nuovo database in qualsiasi server esistente, ovunque nel mondo.
- **Ripristinare un database da uno specifico backup a lungo termine** in un database singolo o in un pool di database elastici, se il database è configurato con un criterio di conservazione a lungo termine (LTR). LTR consente di ripristinare una versione precedente del database usando [il portale di Azure](sql-database-long-term-backup-retention-configure.md#using-azure-portal) o [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) per soddisfare una richiesta di conformità o per eseguire una versione precedente dell'applicazione. Per altre informazioni, vedere [conservazione a lungo termine](sql-database-long-term-retention.md).

Per eseguire un ripristino, vedere [Restore database from backups](sql-database-recovery-using-backups.md).

> [!NOTE]
> In archiviazione di Azure, il termine *replica* si riferisce alla copia di file da una posizione a un'altra. SQL Server *replica di database* si riferisce a mantenere più database secondari sincronizzati con un database primario.

È possibile provare alcune di queste operazioni usando gli esempi seguenti:

| | Portale di Azure | Azure PowerShell |
|---|---|---|
| Modificare la conservazione dei backup | [Database singolo](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Istanza gestita](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Database singolo](sql-database-automated-backups.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Istanza gestita](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Modificare la conservazione dei backup a lungo termine | [Database singolo](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Istanza gestita-N/A  | [Database singolo](sql-database-long-term-backup-retention-configure.md)<br/>Istanza gestita-N/A  |
| Ripristinare un database da un punto nel tempo | [Database singolo](sql-database-recovery-using-backups.md#point-in-time-restore) | [Database singolo](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Istanza gestita](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Ripristino di un database eliminato | [Database singolo](sql-database-recovery-using-backups.md) | [Database singolo](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Istanza gestita](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Ripristinare un database da un archivio BLOB di Azure | Database singolo-N/A <br/>Istanza gestita-N/A  | Database singolo-N/A <br/>[Istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Frequenza di backup

### <a name="point-in-time-restore"></a>Ripristino temporizzato

Il database SQL supporta la gestione self-service per il ripristino temporizzato (ripristino temporizzato) creando automaticamente backup completi, backup differenziali e backup del log delle transazioni. I backup completi del database vengono creati settimanalmente e i backup differenziali del database vengono in genere creati ogni 12 ore. I backup del log delle transazioni vengono in genere creati ogni 5-10 minuti. La frequenza dei backup del log delle transazioni è basata sulle dimensioni di calcolo e sulla quantità di attività del database. 

Il primo backup completo viene pianificato subito dopo la creazione di un database. Questo backup viene in genere completato entro 30 minuti, ma può richiedere più tempo quando il database è di grandi dimensioni. Il backup iniziale, ad esempio, può richiedere più tempo in un database ripristinato o in una copia del database. Dopo il primo backup completo, l'esecuzione di tutti i successivi backup è pianificata e gestita automaticamente in background. Il momento esatto per l'esecuzione dei backup di database è determinato dal servizio SQL Database in modo da bilanciare il carico di lavoro complessivo del sistema. Non è possibile modificare o disabilitare i processi di backup.

I backup ripristino temporizzato sono protetti con archiviazione con ridondanza geografica. Per altre informazioni, vedere [Ridondanza di Archiviazione di Azure](../storage/common/storage-redundancy.md).

Per ulteriori informazioni su ripristino temporizzato, vedere [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore).

### <a name="long-term-retention"></a>Conservazione a lungo termine

Per i database singoli e in pool, è possibile configurare la conservazione a lungo termine dei backup completi per un massimo di 10 anni nell'archivio BLOB di Azure. Se si abilitano i criteri di LTR, i backup completi settimanali vengono copiati automaticamente in un altro contenitore di archiviazione RA-GRS. Per soddisfare i diversi requisiti di conformità, è possibile selezionare diversi periodi di conservazione per i backup settimanali, mensili e/o annuali. Il consumo di spazio di archiviazione dipende dalla frequenza di backup selezionata e dal periodo di memorizzazione o dai periodi. È possibile usare il [calcolatore dei prezzi di LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) per stimare il costo dell'archiviazione con ltr.

Analogamente ai backup ripristino temporizzato, i backup di LTR sono protetti con archiviazione con ridondanza geografica. Per altre informazioni, vedere [Ridondanza di Archiviazione di Azure](../storage/common/storage-redundancy.md).

Per ulteriori informazioni su LTR, vedere [conservazione dei backup a lungo termine](sql-database-long-term-retention.md).

## <a name="backup-storage-consumption"></a>Utilizzo dell'archiviazione di backup

Per i database singoli, questa equazione viene utilizzata per calcolare l'utilizzo totale dello spazio di archiviazione di backup:

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

Per i pool di database elastici, le dimensioni totali dello spazio di archiviazione di backup vengono aggregate a livello di pool e vengono calcolate nel modo seguente:

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

I backup che si verificano prima del periodo di memorizzazione vengono eliminati automaticamente in base al timestamp. Poiché i backup differenziali e i backup del log richiedono un backup completo precedente, sono ripuliti tra loro in blocchi settimanali.

Il database SQL di Azure calcola il totale di archiviazione di backup in conservazione come valore cumulativo. Ogni ora, questo valore viene segnalato alla pipeline di fatturazione di Azure, che è responsabile dell'aggregazione di questo utilizzo orario per calcolare il consumo alla fine di ogni mese. Dopo l'eliminazione del database, l'utilizzo diminuisce in base all'età dei backup. Quando i backup diventano più vecchi del periodo di memorizzazione, la fatturazione viene arrestata.

   > [!IMPORTANT]
   > I backup di un database vengono conservati per il periodo di memorizzazione specificato, anche se il database è stato eliminato. Durante l'eliminazione e la ricreazione di un database è spesso possibile risparmiare sui costi di archiviazione e di calcolo, che potrebbero aumentare i costi di archiviazione di backup perché Microsoft mantiene un backup per il periodo di memorizzazione specificato (ovvero 7 giorni al minimo) per ogni database eliminato, ogni volta che viene eliminato.

### <a name="monitor-consumption"></a>Monitorare l'utilizzo

Ogni tipo di backup (completo, differenziale e di log) viene segnalato nel pannello Monitoraggio database come metrica separata. Nel diagramma seguente viene illustrato come monitorare il consumo di spazio di archiviazione di backup per un singolo database. Questa funzionalità non è attualmente disponibile per le istanze gestite.

![Monitorare il consumo di backup del database nel portale di Azure](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Ottimizzazione dell'utilizzo dell'archiviazione di backup

L'utilizzo eccessivo dell'archiviazione di backup dipende dal carico di lavoro e dalle dimensioni dei singoli database. Prendere in considerazione alcune delle tecniche di ottimizzazione seguenti per ridurre il consumo di spazio di archiviazione di backup:

* Ridurre il [periodo di conservazione dei backup](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) al minimo possibile in base alle esigenze.
* Evitare di eseguire operazioni di scrittura di grandi dimensioni, ad esempio ricompilazioni di indici, più frequentemente di quanto necessario.
* Per le operazioni di caricamento dati di grandi dimensioni, è consigliabile usare gli [indici columnstore cluster](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), ridurre il numero di indici non cluster e prendere in considerazione le operazioni di caricamento bulk con numero di righe pari a 1 milione.
* Nel livello di servizio per utilizzo generico, l'archiviazione dei dati di cui è stato effettuato il provisioning è meno costosa del prezzo dell'archiviazione di backup in eccesso. Se i costi di archiviazione di backup sono costantemente elevati, è possibile aumentare l'archiviazione dei dati per salvare l'archivio di backup.
* Utilizzare TempDB anziché le tabelle permanenti nella logica ETL per archiviare i risultati temporanei. (Applicabile solo all'istanza gestita.)
* Si consiglia di disattivare la crittografia Transparent Data Encryption per i database che non contengono dati sensibili (ad esempio, i database di sviluppo o di test). I backup per i database non crittografati vengono in genere compressi con un rapporto di compressione superiore.

> [!IMPORTANT]
> Per i carichi di lavoro analitici data mart \ data warehouse, è consigliabile usare gli [indici columnstore cluster](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), ridurre il numero di indici non cluster e prendere in considerazione le operazioni di caricamento bulk con numero di righe intorno a 1 milione per ridurre il consumo di spazio di archiviazione di backup eccessivo.

## <a name="storage-costs"></a>Costi di archiviazione

Il prezzo per l'archiviazione varia a seconda che si stia usando il modello DTU o il modello vCore.

### <a name="dtu-model"></a>Modello DTU

Se si usa il modello DTU, non sono previsti costi aggiuntivi per l'archiviazione di backup per i database e i pool di database elastici.

### <a name="vcore-model"></a>Modello vCore

Per i database singoli, viene fornita una quantità minima di spazio di archiviazione per il backup pari al 100% delle dimensioni del database senza costi aggiuntivi. Per i pool di database elastici e le istanze gestite, viene fornita una quantità minima di spazio di archiviazione per il backup pari al 100% dell'archivio dati allocato per le dimensioni del pool o dell'istanza, senza costi aggiuntivi. L'utilizzo aggiuntivo dell'archivio di backup verrà addebitato in base a GB/mese. Questo consumo aggiuntivo dipenderà dal carico di lavoro e dalle dimensioni dei singoli database.

Il database SQL di Azure calcolerà il totale di archiviazione di backup in conservazione come valore cumulativo. Ogni ora, questo valore viene segnalato alla pipeline di fatturazione di Azure, che è responsabile dell'aggregazione di questo utilizzo orario per ottenere il consumo alla fine di ogni mese. Dopo l'eliminazione del database, Microsoft diminuisce il consumo come età dei backup. Quando i backup diventano più vecchi del periodo di memorizzazione, la fatturazione viene arrestata. Poiché tutti i backup del log e i backup differenziali vengono conservati per il periodo di conservazione completo, i database fortemente modificati avranno un costo di backup superiore.

Si supponga che un database abbia accumulato 744 GB di spazio di archiviazione di backup e che questa quantità rimanga costante in un intero mese. Per convertire questo consumo di spazio di archiviazione cumulativo in utilizzo orario, suddividerlo per 744,0 (31 giorni al mese * 24 ore al giorno). Il database SQL segnalerà quindi che il database ha utilizzato 1 GB di backup ripristino temporizzato ogni ora. La fatturazione di Azure aggrega questo consumo e Mostra un utilizzo di 744 GB per l'intero mese. Il costo verrà calcolato in base alla tariffa di $/GB/month nella propria area.

Ora, un esempio più complesso. Si supponga che la conservazione del database sia aumentata a 14 giorni a metà del mese. Si supponga che questo aumento (ipoteticamente) consideri l'archiviazione di backup totale raddoppiata a 1.488 GB. Il database SQL segnala 1 GB di utilizzo per le ore da 1 a 372. Segnala l'utilizzo come 2 GB per le ore da 373 a 744. Questo utilizzo verrebbe aggregato a una fattura finale di 1.116 GB al mese.

### <a name="monitor-costs"></a>Monitorare i costi

Per informazioni sui costi di archiviazione di backup, vedere **Gestione costi e fatturazione** nella portale di Azure, selezionare **Gestione costi**, quindi selezionare **analisi dei costi**. Selezionare la sottoscrizione desiderata come **ambito**, quindi filtrare per il periodo di tempo e il servizio a cui si è interessati.

Aggiungere un filtro per **nome servizio**e quindi selezionare **database SQL** nell'elenco a discesa. Usare il filtro **sottocategoria contatore** per scegliere il contatore di fatturazione per il servizio. Per un database singolo o un pool di database elastici, selezionare **spazio di archiviazione di backup ripristino temporizzato del pool singolo/elastico**. Per un'istanza gestita, selezionare **mi ripristino temporizzato backup storage**. Anche le sottocategorie di **archiviazione** e di **calcolo** potrebbero essere interessanti, ma non sono associate ai costi di archiviazione di backup.

![Analisi dei costi di archiviazione di backup](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>Conservazione backup

Tutti i database SQL di Azure (singoli database di istanza gestita e in pool) hanno un periodo di conservazione dei backup predefinito di 7 giorni. È possibile [modificare il periodo di conservazione dei backup](#change-the-pitr-backup-retention-period) fino a 35 giorni.

Se si elimina un database, il database SQL manterrà i backup come farebbe con un database online. Se ad esempio si elimina un database di base con un periodo di conservazione di sette giorni, viene salvato un backup di quattro giorni per altri tre giorni.

Se è necessario conservare i backup per un periodo superiore al periodo di conservazione massimo, è possibile modificare le proprietà del backup per aggiungere uno o più periodi di conservazione a lungo termine al database. Per altre informazioni, vedere [conservazione a lungo termine](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Se si elimina il server SQL di Azure che ospita i database SQL, vengono eliminati anche tutti i pool di database elastici e i database che appartengono al server. Non possono essere recuperati. Non è possibile ripristinare un server eliminato. Tuttavia, se è stata configurata la conservazione a lungo termine, i backup per i database con LTR non verranno eliminati e questi database potranno essere ripristinati.

## <a name="encrypted-backups"></a>Backup crittografati

Se il database è crittografato con Transparent Data Encryption, i backup vengono crittografati automaticamente a riposo, inclusi i backup di LTR. Quando la crittografia TDE viene abilitata per un database SQL di Azure, anche i backup vengono crittografati. Tutti i nuovi database SQL di Azure vengono configurati con la crittografia TDE abilitata per impostazione predefinita. Per altre informazioni su TDE, vedere [Transparent Data Encryption con il database SQL di Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integrità backup

Con cadenza continuativa, il team di progettazione del database SQL di Azure testa automaticamente il ripristino dei backup automatici dei database inseriti nei server logici e nei pool di database elastici. Questo test non è disponibile nell'istanza gestita. Al momento del ripristino temporizzato, i database ricevono anche i controlli di integrità di DBCC CHECKDB.

Istanza gestita prende il backup iniziale automatico `CHECKSUM` con i database ripristinati con `RESTORE` il comando nativo o con il servizio migrazione dati di Azure al termine della migrazione.

Gli eventuali problemi rilevati durante la verifica dell'integrità determinano la generazione di un avviso per il team di progettazione. Per altre informazioni, vedere [integrità dei dati nel database SQL di Azure](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="compliance"></a>Conformità

Quando si esegue la migrazione del database da un livello di servizio basato su DTU a un livello di servizio basato su vCore, viene mantenuta la conservazione del ripristino temporizzato per assicurarsi che i criteri di ripristino dei dati dell'applicazione non siano compromessi. Se la conservazione predefinita non soddisfa i requisiti di conformità, è possibile modificare il periodo di conservazione ripristino temporizzato usando PowerShell o l'API REST. Per altre informazioni, vedere [modificare il periodo di conservazione dei backup di ripristino temporizzato](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Modificare il periodo di conservazione dei backup di ripristino temporizzato

È possibile modificare il periodo di conservazione predefinito del backup ripristino temporizzato usando il portale di Azure, PowerShell o l'API REST. Gli esempi seguenti illustrano come modificare la conservazione del ripristino temporizzato in 28 giorni.

> [!WARNING]
> Se si riduce il periodo di conservazione corrente, tutti i backup esistenti precedenti al nuovo periodo di conservazione non saranno più disponibili. Se si aumenta il periodo di conservazione corrente, il database SQL manterrà i backup esistenti fino a quando non viene raggiunta la fine del periodo di conservazione più lungo.

> [!NOTE]
> Queste API avranno effetto solo sul periodo di conservazione ripristino temporizzato. Se è stato configurato LTR per il database, non sarà interessato. Per informazioni su come modificare i periodi di conservazione del LTR, vedere [conservazione a lungo termine](sql-database-long-term-retention.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Modificare il periodo di conservazione dei backup ripristino temporizzato usando il portale di Azure

Per modificare il periodo di conservazione dei backup ripristino temporizzato usando il portale di Azure, passare all'oggetto server il cui periodo di memorizzazione si vuole modificare nel portale. Selezionare quindi l'opzione appropriata in base all'oggetto server che si sta modificando.

#### <a name="single-database-and-elastic-database-pools"></a>[Database singolo e pool di database elastici](#tab/single-database)

Le modifiche apportate alla conservazione dei backup ripristino temporizzato per i singoli database SQL di Azure vengono eseguite a livello di server. Le modifiche apportate a livello di server si applicano ai database nel server. Per modificare la conservazione ripristino temporizzato per un server di database SQL di Azure dalla portale di Azure, passare al pannello panoramica del server. Selezionare **Gestisci backup** nel riquadro sinistro, quindi selezionare **Configura conservazione** nella parte superiore della schermata:

![Modificare la conservazione ripristino temporizzato, a livello di server](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[Istanza gestita](#tab/managed-instance)

Le modifiche alla conservazione dei backup ripristino temporizzato per le istanze gestite di database SQL vengono eseguite a livello di singolo database. Per modificare la conservazione dei backup ripristino temporizzato per un database di istanza dalla portale di Azure, passare al pannello di panoramica del singolo database. Selezionare quindi **Configura conservazione backup** nella parte superiore della schermata:

![Modificare la conservazione ripristino temporizzato, istanza gestita](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>Modificare il periodo di conservazione dei backup ripristino temporizzato usando PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo AzureRM di PowerShell è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per ulteriori informazioni, vedere [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo AZ sono sostanzialmente identici a quelli nei moduli AzureRm.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>Modificare il periodo di conservazione dei backup ripristino temporizzato usando l'API REST

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
- Per ulteriori informazioni su come [ripristinare un database a un momento specifico utilizzando il portale di Azure](sql-database-recovery-using-backups.md).
- Ulteriori informazioni su come [ripristinare un database a un momento specifico tramite PowerShell](scripts/sql-database-restore-database-powershell.md).
- Per informazioni su come configurare, gestire e ripristinare dalla conservazione a lungo termine dei backup automatici nell'archivio BLOB di Azure usando il portale di Azure, vedere [gestire la conservazione dei backup a lungo termine usando il portale di Azure](sql-database-long-term-backup-retention-configure.md).
- Per informazioni su come configurare, gestire e ripristinare dalla conservazione a lungo termine dei backup automatici nell'archivio BLOB di Azure tramite PowerShell, vedere [gestire la conservazione dei backup a lungo termine usando PowerShell](sql-database-long-term-backup-retention-configure.md).

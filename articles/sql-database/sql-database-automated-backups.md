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
ms.openlocfilehash: 16ee8c1e271f0aa3e6565322f9a4a422dd90b8b8
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461774"
---
# <a name="automated-backups"></a>Backup automatizzati

Il database SQL crea automaticamente i backup del database mantenuti per la durata del periodo di memorizzazione configurato e usa l' [archiviazione con ridondanza geografica e accesso in lettura di Azure (RA-GRS)](../storage/common/storage-redundancy.md) per assicurarsi che vengano conservati anche se il Data Center non è disponibile. Questi backup vengono creati automaticamente. I backup dei database sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. Se le regole di sicurezza richiedono che i backup siano disponibili per un periodo di tempo prolungato (fino a 10 anni), è possibile configurare una [conservazione a lungo termine](sql-database-long-term-retention.md) su database singleton e pool elastici.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Informazioni sul backup del database SQL

Il database SQL usa la tecnologia SQL Server per creare [backup completi](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) ogni settimana, [backup differenziali](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) ogni 12 ore e [backup del log delle transazioni](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) ogni 5-10 minuti. I backup vengono archiviati nei [BLOB di archiviazione RA-GRS](../storage/common/storage-redundancy.md) replicati in una [Data Center abbinata](../best-practices-availability-paired-regions.md) per la protezione da un'interruzione del Data Center. Quando si ripristina un database, il servizio individua i backup completi, differenziali e del log delle transazioni da ripristinare.

È possibile usare questi backup per:

- **Ripristinare un database esistente a un punto nel tempo precedente** entro il periodo di conservazione usando il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Nel database singolo e nei pool elastici questa operazione creerà un nuovo database nello stesso server del database originale. In Istanza gestita questa operazione può creare una copia del database o dello stesso o di Istanza gestita diversi nella stessa sottoscrizione.
- **Ripristinare un database eliminato fino al momento in cui è stato eliminato** o in qualsiasi momento entro il periodo di memorizzazione. Il database eliminato può essere ripristinato solo nello stesso server logico o in Istanza gestita in cui è stato creato il database originale.
- **Ripristinare un database in un'altra area geografica**. Il ripristino geografico consente di eseguire un ripristino di emergenza geografico quando è impossibile accedere al server e al database. Crea un nuovo database in qualsiasi server esistente ovunque nel mondo.
- **Ripristinare un database da uno specifico backup a lungo termine** in Database singolo o pool elastico se il database è stato configurato con un criterio di conservazione a lungo termine (LTR). LTR consente di ripristinare una versione precedente del database usando [il portale di Azure](sql-database-long-term-backup-retention-configure.md#using-azure-portal) o [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) per soddisfare una richiesta di conformità o per eseguire una versione precedente dell'applicazione. Per altre informazioni, vedere [Long-term retention](sql-database-long-term-retention.md) (Conservazione a lungo termine).
- Per eseguire un ripristino, vedere l'articolo su come [ripristinare un database da un backup](sql-database-recovery-using-backups.md).

> [!NOTE]
> In Archiviazione di Azure il termine *replica* fa riferimento alla copia dei file da una località a un'altra. La *replica di database* di SQL fa riferimento a più database secondari sincronizzati con un database primario.

È possibile provare alcune di queste operazioni usando gli esempi seguenti:

| | Portale di Azure | Azure PowerShell |
|---|---|---|
| Modificare la conservazione dei backup | [Database singolo](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) <br/> [Istanza gestita](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) | [Database singolo](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Istanza gestita](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Modificare la conservazione dei backup a lungo termine | [Database singolo](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Istanza gestita-N/A  | [Database singolo](sql-database-long-term-backup-retention-configure.md)<br/>Istanza gestita-N/A  |
| Ripristinare il database da un punto nel tempo | [Database singolo](sql-database-recovery-using-backups.md#point-in-time-restore) | [Database singolo](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Istanza gestita](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Ripristinare un database eliminato | [Database singolo](sql-database-recovery-using-backups.md) | [Database singolo](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Istanza gestita](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Ripristinare il database dall'archiviazione BLOB di Azure | Database singolo-N/A <br/>Istanza gestita-N/A  | Database singolo-N/A <br/>[Istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Frequenza di backup

### <a name="point-in-time-restore"></a>Ripristino temporizzato

Database SQL supporta la funzionalità self-service per il ripristino temporizzato (PITR) mediante la creazione automatica di backup completi, backup differenziali e backup del log delle transazioni. I backup completi del database vengono creati ogni settimana, i backup differenziali del database vengono creati generalmente ogni 12 ore e i backup del log delle transazioni vengono creati in genere ogni 5-10 minuti, con la frequenza in base alle dimensioni di calcolo e alla quantità di attività del database. Il primo backup completo viene pianificato subito dopo la creazione di un database. Il completamento richiede in genere 30 minuti, ma potrebbe richiedere più tempo se le dimensioni del database sono elevate. Il backup iniziale, ad esempio, può richiedere più tempo in un database ripristinato o in una copia del database. Dopo il primo backup completo, l'esecuzione di tutti i successivi backup è pianificata e gestita automaticamente in background. Il momento esatto per l'esecuzione dei backup di database è determinato dal servizio SQL Database in modo da bilanciare il carico di lavoro complessivo del sistema. Non è possibile modificare o disabilitare i processi di backup.

I backup ripristino temporizzato sono protetti con archiviazione con ridondanza geografica. Per altre informazioni, vedere [ridondanza di archiviazione di Azure](../storage/common/storage-redundancy.md).

Per altre informazioni vedere l'articolo relativo al [Ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="long-term-retention"></a>Conservazione a lungo termine

I database autonomi e in pool offrono la possibilità di configurare la conservazione a lungo termine dei backup completi fino a 10 anni in Archiviazione BLOB di Azure. Se il criterio LTR è abilitato, i backup completi settimanali vengono copiati automaticamente in un contenitore di Archiviazione con ridondanza geografica e accesso in lettura diverso. A secondo dei vari requisiti di conformità, è possibile selezionare periodi di conservazione diversi per i backup settimanali, mensili e/o annuali. L'utilizzo delle risorse di archiviazione dipende dalla frequenza selezionata con cui vengono eseguiti backup e dal periodo di conservazione. È possibile usare lo strumento di [calcolo dei prezzi per la conservazione a lungo termine](https://azure.microsoft.com/pricing/calculator/?service=sql-database) per stimare il costo di questo tipo di archiviazione.

Analogamente a ripristino temporizzato, i backup di LTR sono protetti con archiviazione con ridondanza geografica. Per altre informazioni, vedere [ridondanza di archiviazione di Azure](../storage/common/storage-redundancy.md).

Per altre informazioni, vedere [Conservazione dei backup a lungo termine](sql-database-long-term-retention.md).

## <a name="backup-storage-consumption"></a>Utilizzo dell'archiviazione di backup 

Per i database singoli, l'utilizzo dell'archiviazione di backup totale viene calcolato come segue:   
`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`.

Per i pool elastici, le dimensioni totali dello spazio di archiviazione di backup vengono aggregate a livello di pool e vengono calcolate come segue:   
`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`. 

I backup precedenti al periodo di memorizzazione vengono eliminati automaticamente in base al timestamp. Poiché i backup differenziali e i backup del log richiedono un backup completo precedente, vengono eliminati insieme in blocchi settimanali. 

Il database SQL di Azure calcolerà il totale di archiviazione di backup in conservazione come valore cumulativo. Ogni ora, questo valore viene segnalato alla pipeline di fatturazione di Azure, che è responsabile dell'aggregazione di questo utilizzo orario per calcolare il consumo alla fine di ogni mese. Dopo l'eliminazione del database, l'utilizzo diminuisce in base all'età dei backup. Quando i backup diventano più vecchi del periodo di memorizzazione, la fatturazione viene arrestata. 

   > [!IMPORTANT]
   > I backup di un database vengono conservati per il periodo di memorizzazione specificato, anche se il database è stato eliminato. Durante l'eliminazione e la ricreazione di un database è spesso possibile risparmiare sui costi di archiviazione e di calcolo, ma è possibile che i costi di archiviazione di backup vengano incrementati in quanto si mantiene un backup per il periodo di memorizzazione specificato (ovvero 7 giorni al minimo) per ogni database eliminato, ogni volta che viene eliminato. 



### <a name="monitor-consumption"></a>Monitorare l'utilizzo

Ogni tipo di backup (completo, differenziale e di log) viene segnalato nel pannello Monitoraggio database come metrica separata. Il diagramma seguente illustra come monitorare il consumo di spazio di archiviazione dei backup per un singolo database. Questa funzionalità non è attualmente disponibile per le istanze gestite.

![Monitorare il consumo di backup del database nel pannello Monitoraggio database del portale di Azure](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-the-backup-storage-consumption"></a>Ottimizzazione del consumo di spazio di archiviazione di backup

Il consumo di spazio di archiviazione di backup eccedente dipenderà dal carico di lavoro e dalle dimensioni dei singoli database. È possibile prendere in considerazione l'implementazione di alcune delle tecniche di ottimizzazione seguenti per ridurre ulteriormente il consumo di spazio di archiviazione di backup:

* Ridurre il [periodo di conservazione dei backup](#change-pitr-backup-retention-period-using-azure-portal) al minimo possibile in base alle esigenze.
* Evitare di eseguire operazioni di scrittura di grandi dimensioni più spesso del necessario, ad esempio le ricompilazioni degli indici.
* Per le operazioni di caricamento dei dati di grandi dimensioni, è consigliabile usare [indici columnstore cluster](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), ridurre il numero di indici non cluster e prendere in considerazione anche le operazioni di caricamento bulk con numero di righe intorno 1 milione.
* In per utilizzo generico livello di servizio, l'archiviazione dei dati di cui è stato effettuato il provisioning è meno costosa rispetto al prezzo dell'archiviazione di backup in eccesso, perché i clienti con costi di archiviazione di backup in eccesso continui potrebbero prendere in considerazione l'aumento dell'archiviazione dei dati per salvare il Archivio di backup.
* Utilizzare TempDB nella logica ETL per archiviare i risultati temporanei anziché le tabelle permanenti (applicabili solo all'istanza gestita).
* Provare a disattivare la crittografia Transparent Data Encryption per i database che non contengono dati sensibili (ad esempio, i database di sviluppo o di test). I backup per i database non crittografati vengono in genere compressi con un rapporto di compressione superiore.

> [!IMPORTANT]
> Per i carichi di lavoro analitici data mart \ data warehouse si consiglia di usare gli [indici columnstore cluster](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), ridurre il numero di indici non cluster e prendere in considerazione anche le operazioni di caricamento bulk con numero di righe intorno a 1 milione per ridurre l'utilizzo eccessivo dell'archivio di backup.


## <a name="storage-costs"></a>Costi di archiviazione

Il prezzo per l'archiviazione varia se si usa il modello DTU o il modello vCore. 

### <a name="dtu-model"></a>Modello DTU

Non sono previsti costi aggiuntivi per l'archiviazione di backup per i database e i pool elastici usando il modello DTU. 

### <a name="vcore-model"></a>Modello vCore

Per i database singoli, viene fornita una quantità minima di spazio di archiviazione per il backup pari al 100% delle dimensioni del database senza costi aggiuntivi. Per i pool elastici e le istanze gestite, viene fornita una quantità minima di spazio di archiviazione per il backup pari al 100% dell'archiviazione dei dati allocata per le dimensioni del pool o dell'istanza, senza costi aggiuntivi. L'utilizzo aggiuntivo dell'archivio di backup verrà addebitato in base a GB/mese. Questo consumo aggiuntivo dipenderà dal carico di lavoro e dalle dimensioni dei singoli database.

Il database SQL di Azure calcolerà il totale di archiviazione di backup in conservazione come valore cumulativo. Ogni ora, questo valore viene segnalato alla pipeline di fatturazione di Azure, che è responsabile dell'aggregazione di questo utilizzo orario per ottenere il consumo alla fine di ogni mese. Dopo l'eliminazione del database, si riduce il consumo come età dei backup. Una volta che diventano più vecchi del periodo di memorizzazione, la fatturazione viene arrestata. Poiché tutti i backup del log e i backup differenziali vengono conservati per il periodo di conservazione completo, i database modificati in modo intensivo avranno un costo di backup superiore. 

Si supponga che il database abbia accumulato 744 GB di spazio di archiviazione di backup e che questa quantità rimanga costante in un intero mese. Per convertire questo consumo di spazio di archiviazione cumulativo in un utilizzo orario, lo divideremo per 744,0 (31 giorni al mese * 24 ore al giorno). In questo modo, il database SQL segnalerà il database utilizzato 1 GB di backup ripristino temporizzato ogni ora. La fatturazione di Azure aggrega questa funzionalità e Mostra un utilizzo di 744 GB per l'intero mese e il costo in base alla tariffa di $/GB/mo nella propria area. 

Ora, un esempio più complesso. Si supponga che la conservazione del database sia aumentata fino a 14 giorni a metà del mese e che, in teoria, il numero totale di archiviazione dei backup sia pari a 1488 GB. Il database SQL segnala 1 GB di utilizzo per le ore 1-372 e quindi segnala l'utilizzo come 2 GB per le ore 373-744. Questa operazione verrebbe aggregata in modo da essere una fattura finale di 1116 GB/mo. 

### <a name="monitor-costs"></a>Monitorare i costi

Per informazioni sui costi di archiviazione dei backup, vedere **Gestione costi e fatturazione** dalla portale di Azure, selezionare **Gestione costi**, quindi selezionare **analisi dei costi**. Selezionare la sottoscrizione desiderata come **ambito**, quindi filtrare per il periodo di tempo e il servizio a cui si è interessati. 

Aggiungere un filtro per **nome servizio**, quindi scegliere **database SQL** dall'elenco a discesa. Usare il filtro **sottocategoria contatore** per scegliere il contatore di fatturazione per il servizio. Per un singolo database o un pool elastico, scegliere **spazio di archiviazione di backup ripristino temporizzato del pool singolo/elastico**. Per un'istanza gestita, scegliere **mi ripristino temporizzato backup storage**. Anche le sottocategorie di **archiviazione** e di **calcolo** possono interessare, anche se non sono associate ai costi di archiviazione di backup. 

![Analisi dei costi di archiviazione di backup](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)


## <a name="backup-retention"></a>Conservazione backup

Tutti i database SQL di Azure (singoli database di istanze gestite e in pool) hanno un periodo di conservazione dei backup predefinito di **sette** giorni. È possibile [modificare il periodo di conservazione dei backup fino a 35 giorni](#change-pitr-backup-retention-period).

Se si elimina un database, il database SQL manterrà i backup come farebbe con un database online. Ad esempio, se si elimina un database Basic con un periodo di conservazione di sette giorni, un backup di quattro giorni viene salvato per altri tre giorni.

Se è necessario conservare i backup per un periodo superiore al periodo di conservazione massimo, è possibile modificare le proprietà del backup per aggiungere uno o più periodi di conservazione a lungo termine al database. Per altre informazioni, vedere [Long-term retention](sql-database-long-term-retention.md) (Conservazione a lungo termine).

> [!IMPORTANT]
> Se si elimina SQL Server di Azure che ospita i database SQL, vengono eliminati anche tutti i database e i pool elastici appartenenti al server e non sarà possibile recuperarli. Non è possibile ripristinare un server eliminato. Tuttavia, se è stata configurata la conservazione a lungo termine, i backup per i database con conservazione a lungo termine non verranno cancellati e questi database potranno essere ripristinati.

## <a name="encrypted-backups"></a>Backup crittografati

Se il database è crittografato con TDE, i backup vengono crittografati automaticamente quando i dati sono inattivi, inclusi i backup di conservazione a lungo termine. Quando la crittografia TDE viene abilitata per un database SQL di Azure, anche i backup vengono crittografati. Tutti i nuovi database SQL di Azure vengono configurati con la crittografia TDE abilitata per impostazione predefinita. Per altre informazioni su TDE, vedere [Transparent Data Encryption con il database SQL di Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integrità backup

Con cadenza continuativa, il team di progettazione del database SQL di Azure testa automaticamente il ripristino dei backup automatici dei database inseriti nei server logici e nei pool elastici. questa operazione non è disponibile in Istanza gestita. Al momento del ripristino temporizzato, i database ricevono anche controlli di integrità tramite DBCC CHECKDB.

Istanza gestita esegue un backup iniziale automatico con `CHECKSUM` dei database ripristinati utilizzando il comando `RESTORE` nativo o il servizio migrazione dati una volta completata la migrazione.

Gli eventuali problemi rilevati durante la verifica dell'integrità determinano la generazione di un avviso per il team di progettazione. Per altre informazioni sull'integrità dei dati nel database SQL di Azure, vedere [Data Integrity in Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) (Integrità dei dati nel database SQL di Azure).

## <a name="compliance"></a>Conformità

Quando si esegue la migrazione del database da un livello di servizio basato su DTU a un livello di servizio basato su vCore, viene mantenuta la conservazione del ripristino temporizzato per assicurarsi che i criteri di ripristino dei dati dell'applicazione non siano compromessi. Se il periodo di conservazione predefinito non soddisfa i requisiti di conformità, è possibile modificare il periodo di conservazione di ripristino temporizzato utilizzando PowerShell o API REST. Per altre informazioni, vedere [Modifica del periodo di conservazione backup](#change-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-pitr-backup-retention-period"></a>Modificare il periodo di conservazione dei backup di ripristino temporizzato

È possibile modificare il periodo di conservazione predefinito del backup ripristino temporizzato usando il portale di Azure, PowerShell o l'API REST. Gli esempi che seguono illustrano come modificare la conservazione di ripristino temporizzato a 28 giorni.

> [!WARNING]
> Se si riduce il periodo di conservazione corrente, tutti i backup esistenti precedenti al nuovo periodo di conservazione non saranno più disponibili. Se si aumenta il periodo di conservazione corrente, Database SQL manterrà i backup esistenti fino al raggiungimento del periodo di conservazione più lungo.

> [!NOTE]
> Queste API avranno un impatto solo sul periodo di conservazione del recupero temporizzato. Se è configurata una conservazione a lungo termine per il database, questo non sarà interessato. Per altre informazioni su come modificare i periodi di conservazione a lungo termine, vedere [Conservazione a lungo termine](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-azure-portal"></a>Modificare il periodo di conservazione del backup ripristino temporizzato usando portale di Azure

Per modificare il periodo di conservazione dei backup ripristino temporizzato usando il portale di Azure, passare all'oggetto server di cui si desidera modificare il periodo di memorizzazione nel portale e quindi selezionare l'opzione appropriata in base all'oggetto server che si sta modificando.

#### <a name="single-database--elastic-pools"></a>[Database singolo e pool elastici](#tab/single-database)

La modifica della conservazione dei backup ripristino temporizzato per i singoli database SQL di Azure viene eseguita a livello di server. Le modifiche apportate a livello di server si applicano ai database in tale server. Per modificare ripristino temporizzato per il server di database SQL di Azure da portale di Azure, passare al pannello panoramica del server, fare clic su Gestisci backup nel menu di navigazione, quindi fare clic su Configura conservazione sulla barra di spostamento.

![Modificare il portale di Azure per il recupero temporizzato](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[Istanza gestita](#tab/managed-instance)

La modifica della conservazione dei backup di ripristino temporizzato per l'istanza gestita di database SQL viene eseguita a livello di singolo database. Per modificare la conservazione dei backup di ripristino temporizzato per un database dell'istanza da portale di Azure, passare al pannello panoramica del singolo database e quindi fare clic su Configura conservazione backup nella barra di spostamento.

![Modificare il portale di Azure per il recupero temporizzato](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

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

#### <a name="request-body"></a>Corpo richiesto

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

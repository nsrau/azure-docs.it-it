---
title: Backup automatici con ridondanza geografica
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Il database SQL di Azure e Azure SQL Istanza gestita crea automaticamente un backup del database locale ogni pochi minuti e usa l'archiviazione con ridondanza geografica e accesso in lettura di Azure per la ridondanza geografica.
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: sqldbrb=2
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 08/04/2020
ms.openlocfilehash: 205e99303cd53adf6aa952ccd65441b72471f3a2
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810275"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>Backup automatici: database SQL di Azure & SQL Istanza gestita

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>Informazioni sul backup del database

I backup del database sono una parte essenziale di qualsiasi strategia di continuità aziendale e di ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione. Questi backup consentono il ripristino del database fino a un punto nel tempo entro il periodo di memorizzazione configurato. Se le regole di protezione dei dati richiedono che i backup siano disponibili per un periodo di tempo prolungato (fino a 10 anni), è possibile configurare la [conservazione a lungo termine](long-term-retention-overview.md) per i database singoli e in pool.

### <a name="backup-frequency"></a>Frequenza di backup

Sia il database SQL che SQL Istanza gestita utilizzano la tecnologia SQL Server per creare [backup completi](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) ogni settimana, [backup differenziali](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) ogni 12-24 ore e [backup del log delle transazioni](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) ogni 5-10 minuti. La frequenza dei backup del log delle transazioni è basata sulle dimensioni di calcolo e sulla quantità di attività del database.

Quando si ripristina un database, il servizio determina quali backup completi, differenziali e del log delle transazioni devono essere ripristinati.

### <a name="backup-storage-redundancy"></a>Ridondanza dell'archiviazione di backup

> [!IMPORTANT]
> La ridondanza di archiviazione configurabile per i backup è attualmente disponibile solo per SQL Istanza gestita e può essere specificata solo durante il processo di creazione dell'istanza gestita. Una volta eseguito il provisioning della risorsa, non è possibile modificare l'opzione di ridondanza dell'archiviazione di backup.

L'opzione per configurare la ridondanza dell'archiviazione di backup offre la flessibilità di scegliere tra i [BLOB di archiviazione](../../storage/common/storage-redundancy.md)con ridondanza locale (con ridondanza locale), con ridondanza della zona (ZRS) o con ridondanza geografica (RA-GRS). I meccanismi di ridondanza dell'archiviazione archiviano più copie dei dati in modo che siano protetti da eventi pianificati e non pianificati, inclusi errori hardware temporanei, interruzioni della rete o dell'alimentazione o calamità naturali di grandi dimensioni. Questa funzionalità è attualmente disponibile solo per SQL Istanza gestita.

I BLOB di archiviazione RA-GRS vengono replicati in un' [area abbinata](../../best-practices-availability-paired-regions.md) per evitare interruzioni che influiscano sull'archiviazione dei backup nell'area primaria e consentono di ripristinare il server in un'area diversa in caso di emergenza. 

Viceversa, i BLOB di archiviazione con ridondanza locale e ZRS assicurano che i dati rimangano nella stessa area in cui è distribuito il database SQL o SQL Istanza gestita. L'archiviazione con ridondanza della zona (ZRS) è attualmente disponibile solo in [determinate aree geografiche](../../storage/common/storage-redundancy.md#zone-redundant-storage).

> [!IMPORTANT]
> In SQL Istanza gestita la ridondanza del backup configurata viene applicata alle impostazioni di conservazione dei backup a breve termine usate per il ripristino temporizzato (ripristino temporizzato) e i backup con conservazione a lungo termine usati per i backup a lungo termine (LTR).

### <a name="backup-usage"></a>Utilizzo backup

È possibile usare questi backup per:

- **Ripristino temporizzato di un database esistente**  -  [Ripristinare un database esistente fino a un punto nel tempo precedente](recovery-using-backups.md#point-in-time-restore) entro il periodo di conservazione usando portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Per il database SQL, questa operazione crea un nuovo database nello stesso server del database originale, ma utilizza un nome diverso per evitare la sovrascrittura del database originale. Al termine del ripristino, è possibile eliminare il database originale. In alternativa, è possibile [rinominare](https://docs.microsoft.com/sql/relational-databases/databases/rename-a-database) il database originale, quindi rinominare il database ripristinato con il nome del database originale. Analogamente, per Istanza gestita SQL, questa operazione crea una copia del database nella stessa istanza di o in un'istanza gestita diversa nella stessa sottoscrizione e nella stessa area.
- **Ripristino temporizzato del database eliminato**  -  [Ripristinare un database eliminato al momento dell'eliminazione](recovery-using-backups.md#deleted-database-restore) o a qualsiasi punto nel tempo entro il periodo di memorizzazione. Il database eliminato può essere ripristinato solo nello stesso server o in un'istanza gestita in cui è stato creato il database originale. Quando si elimina un database, il servizio esegue un backup del log delle transazioni finale prima dell'eliminazione, per evitare la perdita di dati.
- **Ripristino**  -  geografico [Ripristinare un database in un'altra area geografica](recovery-using-backups.md#geo-restore). Il ripristino geografico consente di eseguire il ripristino da un'emergenza geografica quando non è possibile accedere al database o ai backup nell'area primaria. Viene creato un nuovo database in qualsiasi server esistente o istanza gestita, in qualsiasi area di Azure.
   > [!IMPORTANT]
   > Il ripristino geografico è disponibile solo per le istanze gestite con archiviazione di backup con ridondanza geografica (RA-GRS) configurata.
- **Ripristino dal backup**  -  a lungo termine [Ripristinare un database da uno specifico backup a lungo termine](long-term-retention-overview.md) di un database singolo o di un database in pool, se il database è stato configurato con un criterio di conservazione a lungo termine (LTR). LTR consente di ripristinare una versione precedente del database usando [il portale di Azure](long-term-backup-retention-configure.md#using-the-azure-portal) o [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) per soddisfare una richiesta di conformità o per eseguire una versione precedente dell'applicazione. Per altre informazioni, vedere [conservazione a lungo termine](long-term-retention-overview.md).

Per eseguire un ripristino, vedere [Restore database from backups](recovery-using-backups.md).

> [!NOTE]
> In archiviazione di Azure, il termine *replica* si riferisce alla copia di BLOB da una posizione a un'altra. In SQL la *replica di database* si riferisce a diverse tecnologie utilizzate per la sincronizzazione di più database secondari con un database primario.

È possibile provare a eseguire le operazioni di configurazione e ripristino del backup usando gli esempi seguenti:

| Operazione | Portale di Azure | Azure PowerShell |
|---|---|---|
| **Modificare la conservazione dei backup** | [Database SQL](automated-backups-overview.md?tabs=single-database#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Istanza gestita di SQL](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Database SQL](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Istanza gestita di SQL](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| **Modificare la conservazione dei backup a lungo termine** | [Database SQL](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Istanza gestita SQL-N/A  | [Database SQL](long-term-backup-retention-configure.md)<br/>[Istanza gestita di SQL](../managed-instance/long-term-backup-retention-configure.md)  |
| **Ripristinare un database da un punto nel tempo** | [Database SQL](recovery-using-backups.md#point-in-time-restore)<br>[Istanza gestita di SQL](../managed-instance/point-in-time-restore.md) | [Database SQL](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Istanza gestita di SQL](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **Ripristino di un database eliminato** | [Database SQL](recovery-using-backups.md)<br>[Istanza gestita di SQL](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) | [Database SQL](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Istanza gestita di SQL](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **Ripristinare un database da un archivio BLOB di Azure** | Database SQL-N/A <br/>Istanza gestita SQL-N/A  | Database SQL-N/A <br/>[Istanza gestita di SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="backup-scheduling"></a>Pianificazione dei backup

Il primo backup completo viene pianificato subito dopo la creazione o il ripristino di un nuovo database. Questo backup viene in genere completato entro 30 minuti, ma può richiedere più tempo quando il database è di grandi dimensioni. Il backup iniziale, ad esempio, può richiedere più tempo a un database ripristinato o a una copia del database, che in genere è più grande di un nuovo database. Dopo il primo backup completo, tutti gli altri backup vengono pianificati e gestiti automaticamente. La tempistica esatta di tutti i backup del database è determinata dal database SQL o dal servizio SQL Istanza gestita in quanto bilancia il carico di lavoro complessivo del sistema. Non è possibile modificare la pianificazione dei processi di backup o disabilitarli.

> [!IMPORTANT]
> Per un database nuovo, ripristinato o copiato, la funzionalità di ripristino temporizzato diventa disponibile dal momento in cui viene creato il backup del log delle transazioni iniziale che segue il backup completo iniziale.

## <a name="backup-storage-consumption"></a>Utilizzo dell'archiviazione di backup

Con SQL Server tecnologia di backup e ripristino, il ripristino di un database a un momento specifico richiede una catena di backup senza interruzioni costituita da un backup completo, facoltativamente un backup differenziale e uno o più backup del log delle transazioni. Il database SQL e la pianificazione del backup di SQL Istanza gestita includono un backup completo ogni settimana. Pertanto, per abilitare ripristino temporizzato entro l'intero periodo di conservazione, il sistema deve archiviare backup completi, differenziali e del log delle transazioni aggiuntivi fino a una settimana più a lungo rispetto al periodo di conservazione configurato. 

In altre parole, per qualsiasi punto nel tempo durante il periodo di memorizzazione, deve essere presente un backup completo precedente all'ora meno recente del periodo di memorizzazione, oltre a una catena ininterrotta di backup differenziali e del log delle transazioni dal backup completo fino al backup completo successivo.

> [!NOTE]
> Per abilitare ripristino temporizzato, i backup aggiuntivi vengono archiviati per un periodo di tempo massimo di una settimana superiore al periodo di conservazione configurato. Per l'archiviazione di backup viene addebitata la stessa tariffa per tutti i backup. 

I backup che non sono più necessari per fornire la funzionalità Ripristino temporizzato vengono eliminati automaticamente. Poiché i backup differenziali e i backup del log richiedono un backup completo precedente per poter essere ripristinabili, tutti e tre i tipi di backup vengono eliminati insieme in set settimanali.

Per tutti i database che includono database [crittografati con crittografia](transparent-data-encryption-tde-overview.md) Transparent, i backup vengono compressi per ridurre i costi e la compressione dell'archiviazione di backup Il rapporto di compressione di backup medio è 3-4 volte, ma può essere significativamente inferiore o superiore a seconda della natura dei dati e della compressione dei dati utilizzata nel database.

Il database SQL e SQL Istanza gestita calcolano l'archivio di backup totale usato come valore cumulativo. Ogni ora, questo valore viene segnalato alla pipeline di fatturazione di Azure, che è responsabile dell'aggregazione di questo utilizzo orario per calcolare il consumo alla fine di ogni mese. Dopo l'eliminazione del database, l'utilizzo diminuisce con l'esaurimento dei backup e l'eliminazione. Una volta eliminati tutti i backup e ripristino temporizzato non è più possibile, la fatturazione viene arrestata.
   
> [!IMPORTANT]
> I backup di un database vengono conservati per abilitare ripristino temporizzato anche se il database è stato eliminato. Durante l'eliminazione e la ricreazione di un database, è possibile che i costi di archiviazione e calcolo vengano aumentati, in quanto il servizio conserva i backup per ogni database eliminato ogni volta che viene eliminato. 

### <a name="monitor-consumption"></a>Monitorare l'utilizzo

Per i database vCore, lo spazio di archiviazione utilizzato da ogni tipo di backup (completo, differenziale e di log) viene segnalato nel pannello Monitoraggio database come metrica separata. Nel diagramma seguente viene illustrato come monitorare il consumo di spazio di archiviazione di backup per un singolo database. Questa funzionalità non è attualmente disponibile per le istanze gestite.

![Monitorare il consumo di backup del database nel portale di Azure](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Ottimizzazione dell'utilizzo dell'archiviazione di backup

L'utilizzo dell'archiviazione di backup fino alle dimensioni massime dei dati per un database non viene addebitato. L'utilizzo eccessivo dell'archiviazione di backup dipende dal carico di lavoro e dalle dimensioni massime dei singoli database. Prendere in considerazione alcune delle tecniche di ottimizzazione seguenti per ridurre il consumo di spazio di archiviazione di backup:

- Ridurre il [periodo di conservazione dei backup](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) al minimo possibile in base alle esigenze.
- Evitare di eseguire operazioni di scrittura di grandi dimensioni, ad esempio ricompilazioni di indici, più frequentemente di quanto necessario.
- Per operazioni di caricamento di dati di grandi dimensioni, è consigliabile utilizzare [indici columnstore cluster](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes) e le [procedure consigliate](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)correlate e/o ridurre il numero di indici non cluster.
- Nel livello di servizio per utilizzo generico, l'archiviazione dei dati di cui è stato effettuato il provisioning è meno costosa del prezzo dell'archiviazione di backup. Se i costi di archiviazione di backup sono costantemente elevati, è possibile considerare l'aumento dell'archiviazione dei dati per il salvataggio nell'archivio di backup.
- Utilizzare TempDB anziché le tabelle permanenti nella logica dell'applicazione per archiviare i risultati temporanei e/o i dati temporanei.
- Usare l'archiviazione di backup con ridondanza locale quando possibile (ad esempio, ambienti di sviluppo/test)

## <a name="backup-retention"></a>Conservazione backup

Per tutti i nuovi database, ripristinati e copiati, il database SQL di Azure e Azure SQL Istanza gestita conservano backup sufficienti per consentire ripristino temporizzato negli ultimi 7 giorni per impostazione predefinita. Fatta eccezione per i database con iperscalabilità, è possibile [modificare il periodo di conservazione dei backup](#change-the-pitr-backup-retention-period) per ogni database attivo nell'intervallo di 1-35 giorni. Come descritto in [utilizzo dell'archiviazione di backup](#backup-storage-consumption), i backup archiviati per abilitare ripristino temporizzato potrebbero essere precedenti al periodo di conservazione. Solo per Istanza gestita SQL di Azure, è possibile impostare il tasso di conservazione dei backup di ripristino temporizzato dopo l'eliminazione di un database nell'intervallo di 0-35 giorni. 

Se si elimina un database, il sistema mantiene i backup allo stesso modo di un database online con il periodo di memorizzazione specifico. Non è possibile modificare il periodo di conservazione dei backup per un database eliminato.

> [!IMPORTANT]
> Se si elimina un server o un'istanza gestita, verranno eliminati anche tutti i database di tale server o istanza gestita e non sarà possibile recuperarli. Non è possibile ripristinare un server o un'istanza gestita eliminata. Tuttavia, se era stata configurata la conservazione a lungo termine per un database o un'istanza gestita, i backup con conservazione a lungo termine non vengono eliminati e possono essere usati per ripristinare i database in un server o in un'istanza gestita diversa nella stessa sottoscrizione, fino a un punto nel tempo in cui è stato effettuato un backup di conservazione a lungo termine.

La conservazione dei backup per gli scopi di ripristino temporizzato negli ultimi 1-35 giorni è talvolta denominata conservazione dei backup a breve termine. Se è necessario mantenere i backup per un periodo più lungo rispetto al periodo di conservazione a breve termine massimo di 35 giorni, è possibile abilitare la [conservazione a lungo termine](long-term-retention-overview.md).

### <a name="long-term-retention"></a>Conservazione a lungo termine

Per database SQL e Istanza gestita SQL è possibile configurare la conservazione a lungo termine del backup completo (LTR) per un massimo di 10 anni nell'archivio BLOB di Azure. Una volta configurato il criterio LTR, i backup completi vengono copiati automaticamente in un contenitore di archiviazione diverso ogni settimana. Per soddisfare i diversi requisiti di conformità, è possibile selezionare diversi periodi di conservazione per i backup completi settimanali, mensili e/o annuali. Il consumo di spazio di archiviazione dipende dalla frequenza e dai periodi di memorizzazione dei backup di LTR selezionati. È possibile usare il [calcolatore dei prezzi di LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) per stimare il costo dell'archiviazione con ltr.

Per ulteriori informazioni su LTR, vedere [conservazione dei backup a lungo termine](long-term-retention-overview.md).

## <a name="storage-costs"></a>Costi di archiviazione

Il prezzo per l'archiviazione dei backup varia a seconda del modello di acquisto (DTU o vCore), dell'opzione di ridondanza dell'archiviazione di backup scelta e anche della propria area. Per i prezzi, l'archiviazione di backup viene addebitata per GB/mese, vedere la pagina dei prezzi del [database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/single/) e la pagina dei [prezzi di Azure SQL istanza gestita](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/) .

### <a name="dtu-model"></a>Modello DTU

Nel modello DTU non sono previsti costi aggiuntivi per l'archiviazione di backup per i database e i pool elastici. Il prezzo dell'archiviazione di backup è parte del prezzo del database o del pool.

### <a name="vcore-model"></a>Modello vCore

Per i database singoli nel database SQL, un importo di archiviazione di backup pari al 100% delle dimensioni massime di archiviazione dei dati per il database viene fornito senza costi aggiuntivi. Per i pool elastici e le istanze gestite, viene fornito un importo di archiviazione di backup pari al 100% dell'archiviazione dei dati massima per il pool o le dimensioni massime di archiviazione dell'istanza, rispettivamente, senza costi aggiuntivi. 

Per i database singoli, questa equazione viene utilizzata per calcolare l'utilizzo totale di archiviazione di backup fatturabile:

`Total billable backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

Per i database in pool, le dimensioni totali di archiviazione dei backup fatturabili vengono aggregate a livello di pool e vengono calcolate come segue:

`Total billable backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

Per le istanze gestite, le dimensioni totali di archiviazione dei backup fatturabili vengono aggregate a livello di istanza e vengono calcolate nel modo seguente:

`Total billable backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

Lo spazio di archiviazione di backup fatturabile totale, se presente, verrà addebitato in GB al mese. Questo utilizzo dell'archiviazione di backup dipenderà dal carico di lavoro e dalle dimensioni dei singoli database, dei pool elastici e delle istanze gestite. I database fortemente modificati presentano backup differenziali e del log più grandi, perché la dimensione di questi backup è proporzionale alla quantità di modifiche apportate ai dati. Pertanto, tali database avranno addebiti di backup più elevati.

Il database SQL e SQL Istanza gestita calcola l'archivio di backup fatturabile totale come valore cumulativo in tutti i file di backup. Ogni ora, questo valore viene segnalato alla pipeline di fatturazione di Azure, che aggrega questo utilizzo orario per ottenere il consumo di spazio di archiviazione di backup alla fine di ogni mese. Se un database viene eliminato, l'utilizzo dell'archiviazione di backup diminuisce gradualmente man mano che si esauriscono i backup meno recenti e vengono eliminati. Poiché i backup differenziali e i backup del log richiedono un backup completo precedente per poter essere ripristinabili, tutti e tre i tipi di backup vengono eliminati insieme in set settimanali. Una volta eliminati tutti i backup, la fatturazione viene arrestata. 

Come esempio semplificato, si supponga che un database abbia accumulato 744 GB di spazio di archiviazione di backup e che questa quantità rimanga costante durante tutto il mese perché il database è completamente inattivo. Per convertire questo consumo di spazio di archiviazione cumulativo in utilizzo orario, suddividerlo per 744,0 (31 giorni al mese * 24 ore al giorno). Il database SQL segnalerà alla pipeline di fatturazione di Azure che il database ha utilizzato 1 GB di backup ripristino temporizzato ogni ora, a una velocità costante. La fatturazione di Azure aggrega questo consumo e Mostra un utilizzo di 744 GB per l'intero mese. Il costo verrà calcolato in base alla tariffa di quantità/GB/mese nella propria area.

Ora, un esempio più complesso. Si supponga che la conservazione dello stesso database inattivo sia aumentata da 7 a 14 giorni a metà del mese. Questo aumento comporta il raddoppio del totale di archiviazione dei backup a 1.488 GB. Il database SQL segnala 1 GB di utilizzo per le ore da 1 a 372 (la prima metà del mese). Segnala l'utilizzo come 2 GB per le ore da 373 a 744 (la seconda metà del mese). Questo utilizzo verrebbe aggregato a una fattura finale di 1.116 GB al mese.

Gli scenari di fatturazione dei backup effettivi sono più complessi. Poiché la frequenza delle modifiche nel database dipende dal carico di lavoro ed è variabile nel corso del tempo, le dimensioni di ogni backup differenziale e del log variano, causando la fluttuazione del consumo di spazio di archiviazione di backup orario. Ogni backup differenziale, inoltre, contiene tutte le modifiche apportate nel database dall'ultimo backup completo, pertanto le dimensioni totali di tutti i backup differenziali aumentano gradualmente nel corso di una settimana e quindi vengono eliminate in modo nitido dopo un set di backup completo, differenziale e del log obsoleti. Se, ad esempio, un'attività di scrittura pesante, ad esempio la ricompilazione dell'indice, è stata eseguita subito dopo il completamento di un backup completo, le modifiche apportate dalla ricompilazione dell'indice verranno incluse nei backup del log delle transazioni eseguite per la durata della ricompilazione, nel backup differenziale successivo e in ogni backup differenziale eseguito fino al backup completo successivo. Per il secondo scenario in database di dimensioni maggiori, un'ottimizzazione del servizio crea un backup completo anziché un backup differenziale se un backup differenziale è troppo grande in caso contrario. In questo modo si riduce la dimensione di tutti i backup differenziali fino al backup completo seguente.

È possibile monitorare il consumo totale di spazio di archiviazione di backup per ogni tipo di backup (completo, differenziale, log delle transazioni) nel tempo, come descritto in [monitorare l'utilizzo](#monitor-consumption).

### <a name="backup-storage-redundancy"></a>Ridondanza dell'archiviazione di backup

La ridondanza dell'archiviazione di backup influisca sui costi del backup nel modo seguente:
- Prezzo con ridondanza locale = x
- Prezzo ZRS = 1.25 x
- Prezzo RA-GRS = 2x

Per altre informazioni sui prezzi di archiviazione dei backup, vedere la pagina dei prezzi del [database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/single/) e la [pagina dei prezzi di Azure SQL istanza gestita](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

> [!IMPORTANT]
> La ridondanza di archiviazione configurabile per i backup è attualmente disponibile solo per SQL Istanza gestita e può essere specificata solo durante il processo di creazione dell'istanza gestita. Una volta eseguito il provisioning della risorsa, non è possibile modificare l'opzione di ridondanza dell'archiviazione di backup.

### <a name="monitor-costs"></a>Monitorare i costi

Per informazioni sui costi di archiviazione di backup, vedere **Gestione costi e fatturazione** nella portale di Azure, selezionare **Gestione costi**, quindi selezionare **analisi dei costi**. Selezionare la sottoscrizione desiderata come **ambito**, quindi filtrare per il periodo di tempo e il servizio a cui si è interessati.

Aggiungere un filtro per **nome servizio**e quindi selezionare **database SQL** nell'elenco a discesa. Usare il filtro **sottocategoria contatore** per scegliere il contatore di fatturazione per il servizio. Per un database singolo o un pool di database elastici, selezionare **spazio di archiviazione di backup ripristino temporizzato del pool singolo/elastico**. Per un'istanza gestita, selezionare **mi ripristino temporizzato backup storage**. Anche le sottocategorie di **archiviazione** e di **calcolo** potrebbero essere interessanti, ma non sono associate ai costi di archiviazione di backup.

![Analisi dei costi di archiviazione di backup](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

  >[!NOTE]
  > I contatori sono visibili solo per i contatori attualmente in uso. Se un contatore non è disponibile, è probabile che la categoria non sia attualmente in uso. I contatori di istanze gestite, ad esempio, non saranno presenti per i clienti che non dispongono di un'istanza gestita distribuita. Analogamente, i contatori di archiviazione non saranno visibili per le risorse che non utilizzano l'archiviazione. 

## <a name="encrypted-backups"></a>Backup crittografati

Se il database è crittografato con Transparent Data Encryption, i backup vengono crittografati automaticamente a riposo, inclusi i backup di LTR. Tutti i nuovi database in Azure SQL sono configurati con Transparent Data Encryption abilitato per impostazione predefinita. Per ulteriori informazioni su Transparent Data Encryption, vedere [Transparent Data Encryption con SQL Database & sql istanza gestita](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integrità backup

Con cadenza continuativa, il team di progettazione di SQL Azure testa automaticamente il ripristino dei backup automatici del database. (Questo test non è attualmente disponibile in SQL Istanza gestita.) Al momento del ripristino temporizzato, i database ricevono anche i controlli di integrità di DBCC CHECKDB.

Gli eventuali problemi rilevati durante la verifica dell'integrità determinano la generazione di un avviso per il team di progettazione. Per altre informazioni, vedere [integrità dei dati nel database SQL](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

Tutti i backup di database vengono eseguiti con l'opzione CHECKSUM per offrire un'integrità di backup aggiuntiva.

## <a name="compliance"></a>Conformità

Quando si esegue la migrazione del database da un livello di servizio basato su DTU a un livello di servizio basato su vCore, viene mantenuta la conservazione del ripristino temporizzato per assicurarsi che i criteri di ripristino dei dati dell'applicazione non siano compromessi. Se la conservazione predefinita non soddisfa i requisiti di conformità, è possibile modificare il periodo di conservazione del ripristino temporizzato. Per altre informazioni, vedere [modificare il periodo di conservazione dei backup di ripristino temporizzato](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Modificare il periodo di conservazione dei backup di ripristino temporizzato

È possibile modificare il periodo di conservazione predefinito del backup ripristino temporizzato usando il portale di Azure, PowerShell o l'API REST. Gli esempi seguenti illustrano come modificare la conservazione del ripristino temporizzato in 28 giorni.

> [!WARNING]
> Se si riduce il periodo di conservazione corrente, si perde la possibilità di eseguire il ripristino fino a punti nel tempo precedenti al nuovo periodo di conservazione. I backup che non sono più necessari per fornire ripristino temporizzato entro il nuovo periodo di conservazione vengono eliminati. Se si aumenta il periodo di conservazione corrente, non è possibile ottenere immediatamente il ripristino in momenti meno recenti entro il nuovo periodo di conservazione. Questa capacità viene acquisita nel tempo, perché il sistema inizia a mantenere i backup per un periodo più lungo.

> [!NOTE]
> Queste API avranno effetto solo sul periodo di conservazione ripristino temporizzato. Se è stato configurato LTR per il database, non sarà interessato. Per informazioni su come modificare i periodi di conservazione del LTR, vedere [conservazione a lungo termine](long-term-retention-overview.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Modificare il periodo di conservazione dei backup ripristino temporizzato usando il portale di Azure

Per modificare il periodo di conservazione dei backup ripristino temporizzato per i database attivi utilizzando il portale di Azure, passare al server o all'istanza gestita con i database di cui si desidera modificare il periodo di memorizzazione. 

#### <a name="sql-database"></a>[Database SQL](#tab/single-database)

Le modifiche apportate alla conservazione dei backup di ripristino temporizzato per il database SQL vengono eseguite nella pagina Server nel portale. Per modificare la conservazione ripristino temporizzato per i database in un server, passare al pannello Panoramica Server. Selezionare **Gestisci backup** nel riquadro sinistro, selezionare i database nell'ambito della modifica e quindi selezionare **Configura conservazione** nella parte superiore della schermata:

![Modificare la conservazione ripristino temporizzato, a livello di server](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[Istanza gestita di SQL](#tab/managed-instance)

Le modifiche apportate alla conservazione dei backup ripristino temporizzato per SQL Istanza gestita vengono eseguite a livello di singolo database. Per modificare la conservazione dei backup ripristino temporizzato per un database di istanza dalla portale di Azure, passare al pannello di panoramica del singolo database. Selezionare quindi **Configura conservazione backup** nella parte superiore della schermata:

![Modificare la conservazione ripristino temporizzato, istanza gestita](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>Modificare il periodo di conservazione dei backup ripristino temporizzato usando PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo AzureRM di PowerShell è ancora supportato dal database SQL e da SQL Istanza gestita, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per ulteriori informazioni, vedere [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo AZ sono sostanzialmente identici a quelli nei moduli AzureRm.

#### <a name="sql-database"></a>[Database SQL](#tab/single-database)

Per modificare la conservazione dei backup ripristino temporizzato per i database SQL di Azure attivi, usare l'esempio di PowerShell seguente.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

#### <a name="sql-managed-instance"></a>[Istanza gestita di SQL](#tab/managed-instance)

Per modificare la conservazione dei backup ripristino temporizzato per un singolo database SQL Istanza gestita **attivo** , usare l'esempio di PowerShell seguente.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase -RetentionDays 1
```

Per modificare la conservazione dei backup ripristino temporizzato per tutti i database SQL Istanza gestita **attivi** , usare l'esempio di PowerShell seguente.

```powershell
# SET new PITR backup retention period for ALL active databases
# Valid backup retention must be between 1 and 35 days
Get-AzSqlInstanceDatabase -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 1
```

Per modificare la conservazione dei backup ripristino temporizzato per un singolo database SQL Istanza gestita **eliminato** , usare l'esempio di PowerShell seguente.
 
```powershell
# SET new PITR backup retention on an individual deleted database
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database.
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

Per modificare la conservazione dei backup ripristino temporizzato per tutti i database SQL Istanza gestita **eliminati** , usare l'esempio di PowerShell seguente.

```powershell
# SET new PITR backup retention for ALL deleted databases
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

La conservazione dei giorni zero (0) indica che il backup viene eliminato immediatamente e non viene più mantenuto per un database eliminato.
Una volta ridotta la conservazione dei backup di ripristino temporizzato per un database eliminato, non è più possibile aumentarlo.

---

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

## <a name="configure-backup-storage-redundancy"></a>Configurare la ridondanza dell'archiviazione di backup

> [!NOTE]
> La ridondanza di archiviazione configurabile per i backup è attualmente disponibile solo per SQL Istanza gestita e può essere specificata solo durante il processo di creazione dell'istanza gestita. Una volta eseguito il provisioning della risorsa, non è possibile modificare l'opzione di ridondanza dell'archiviazione di backup.

Una ridondanza di archiviazione di backup di un'istanza gestita può essere impostata solo durante la creazione dell'istanza. Il valore predefinito è archiviazione con ridondanza geografica (RA-GRS). Per le differenze tra i prezzi tra archiviazione con ridondanza locale (con ridondanza locale), con ridondanza della zona (ZRS) e archiviazione con ridondanza geografica (RA-GRS), vedere la [pagina dei prezzi delle istanze gestite](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

### <a name="configure-backup-storage-redundancy-by-using-the-azure-portal"></a>Configurare la ridondanza dell'archiviazione di backup usando il portale di Azure

Nell'portale di Azure l'opzione per modificare la ridondanza dell'archiviazione dei backup si trova nel pannello **calcolo e archiviazione** accessibile dall'opzione **Configura istanza gestita** nella scheda **nozioni di base** durante la creazione del istanza gestita SQL.
![Apri calcolo + configurazione archiviazione-pannello](./media/automated-backups-overview/open-configuration-blade-mi.png)

Trovare l'opzione per selezionare la ridondanza dell'archiviazione di backup nel pannello **calcolo e archiviazione** .
![Configurare la ridondanza dell'archiviazione di backup](./media/automated-backups-overview/select-backup-storage-redundancy-mi.png)

## <a name="next-steps"></a>Passaggi successivi

- I backup dei database sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. Per informazioni sulle altre soluzioni di continuità aziendale del database SQL, vedere [Panoramica della continuità aziendale](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- Per ulteriori informazioni su come [ripristinare un database a un momento specifico utilizzando il portale di Azure](recovery-using-backups.md).
- Ulteriori informazioni su come [ripristinare un database a un momento specifico tramite PowerShell](scripts/restore-database-powershell.md).
- Per informazioni su come configurare, gestire e ripristinare dalla conservazione a lungo termine dei backup automatici nell'archivio BLOB di Azure usando il portale di Azure, vedere [gestire la conservazione dei backup a lungo termine usando il portale di Azure](long-term-backup-retention-configure.md).
- Per informazioni su come configurare, gestire e ripristinare dalla conservazione a lungo termine dei backup automatici nell'archivio BLOB di Azure tramite PowerShell, vedere [gestire la conservazione dei backup a lungo termine usando PowerShell](long-term-backup-retention-configure.md).
- Per informazioni su come ottimizzare la conservazione e i costi di archiviazione dei backup per Istanza gestita SQL di Azure, vedere [ottimizzazione dei costi di archiviazione di backup in istanza gestita](https://aka.ms/mi-backup-tuning).

---
title: Backup automatici e con ridondanza geografica del database SQL di Azure | Microsoft Docs
description: Il database SQL crea automaticamente e ripetutamente una copia di backup locale del database a pochi minuti l'una dall'altra e usa l'archiviazione con ridondanza geografica e accesso in lettura di Azure per la ridondanza geografica.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.workload: Active
ms.date: 07/18/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: cedad5f48769ed864fef10cfd7059111a4502fd3
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136605"
---
# <a name="learn-about-automatic-sql-database-backups"></a>Informazioni sui backup automatici del database SQL

Il database SQL crea automaticamente i backup del database e usa l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) di Azure per fornire una ridondanza geografica. Questi backup vengono creati automaticamente e senza costi aggiuntivi. Non è necessario intervenire manualmente per eseguire i backup. I backup dei database sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. Se le regole di sicurezza richiedono che i backup siano disponibili per un lungo periodo di tempo, è possibile configurare un criterio di conservazione dei backup a lungo termine. Per altre informazioni, vedere [Long-term retention](sql-database-long-term-retention.md) (Conservazione a lungo termine).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Informazioni sul backup del database SQL

Il database SQL usa la tecnologia di SQL Server per creare backup [completi](https://msdn.microsoft.com/library/ms186289.aspx), [differenziali](http://msdn.microsoft.com/library/ms175526.aspx) e del [log delle transazioni](https://msdn.microsoft.com/library/ms191429.aspx) ai fini del ripristino temporizzato. I backup del log delle transazioni vengono eseguiti in genere ogni 5-10 minuti mentre i backup differenziali ogni 12 ore, tale frequenza è determinata dal livello di prestazioni e dalla quantità delle attività del database. I backup del log delle transazioni, con backup completi e differenziali, consentono di ripristinare un database a un punto specifico nel tempo nello stesso server che ospita il database. Quando si ripristina un database, il servizio individua i backup completi, differenziali e del log delle transazioni da ripristinare.


È possibile usare questi backup per:

* Ripristinare un database in un punto nel tempo entro il periodo di memorizzazione. Questa operazione creerà un nuovo database nello stesso server del database originale.
* Ripristinare un database eliminato al momento in cui è stato eliminato o a qualsiasi momento del periodo di conservazione. Il database eliminato può essere ripristinato solo nello stesso server in cui è stato creato il database originale.
* Ripristinare un database in un'altra area geografica. Ciò consente di eseguire un ripristino di emergenza geografico quando è impossibile accedere al server e al database. Crea un nuovo database in qualsiasi server esistente ovunque nel mondo. 
* Ripristinare un database da uno specifico backup a lungo termine se il database è stato configurato con criteri di conservazione a lungo termine (LTR). Ciò consente di ripristinare una versione precedente del database per soddisfare una richiesta di conformità o di eseguire una versione precedente dell'applicazione. Vedere [Conservazione a lungo termine](sql-database-long-term-retention.md).
* Per eseguire un ripristino, vedere l'articolo su come [ripristinare un database da un backup](sql-database-recovery-using-backups.md).

> [!NOTE]
> In Archiviazione di Azure il termine *replica* fa riferimento alla copia dei file da una località a un'altra. La *replica di database* di SQL fa riferimento a più database secondari sincronizzati con un database primario. 
> 

## <a name="how-long-are-backups-kept"></a>Per quanto tempo sono conservati i backup?
Ogni backup del database SQL ha un periodo di conservazione predefinito basato sul livello di servizio e distingue tra il [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) e il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md). È possibile aggiornare il periodo di conservazione dei backup per un database. Visualizzare [Modifica del periodo di conservazione backup](#how-to-change-backup-retention-period) per ulteriori dettagli.

Se si elimina un database, il database SQL manterrà i backup come farebbe con un database online. Ad esempio, se si elimina un database Basic con un periodo di conservazione di sette giorni, un backup di quattro giorni viene salvato per altri tre giorni.

Se è necessario conservare i backup per un periodo superiore al periodo di conservazione massimo del ripristino temporizzato, è possibile modificare le proprietà del backup per aggiungere uno o più periodi di conservazione a lungo termine al database. Vedere l'articolo sulla [conservazione del backup a lungo termine](sql-database-long-term-retention.md) per ulteriori dettagli.

> [!IMPORTANT]
> Se si elimina SQL Server di Azure che ospita i database SQL, vengono eliminati anche tutti i database e i pool elastici appartenenti al server e non sarà possibile recuperarli. Non è possibile ripristinare un server eliminato. Tuttavia, se è stata configurata la conservazione a lungo termine, i backup per i database con conservazione a lungo termine non verranno cancellati e questi database potranno essere ripristinati.

### <a name="pitr-retention-for-dtu-based-service-tiers"></a>Conservazione per ripristino temporizzato per i livelli di servizio basati su DTU
Il periodo di conservazione predefinito per un database creato tramite il modello di acquisto basato su DTU varia in base al livello di servizio:

* Il livello di servizio Base è 1 settimana.
* Il livello di servizio Standard è di 5 settimane.
* Il livello di servizio premium è di 5 settimane.

Se si riduce il periodo di conservazione per ripristino temporizzato corrente, tutti i backup esistenti anteriori al nuovo periodo di conservazione non saranno disponibili. 

Se si aumenta il periodo di conservazione per ripristino temporizzato corrente, SQL Database manterrà i backup esistenti fino al raggiungimento del periodo di conservazione più lungo.

## <a name="how-often-do-backups-happen"></a>Con quale frequenza si verificano i backup?
### <a name="backups-for-point-in-time-restore"></a>Backup per il ripristino temporizzato
Database SQL supporta la funzionalità self-service per il ripristino temporizzato (PITR) mediante la creazione automatica di backup completi, backup differenziali e backup del log delle transazioni. I backup completi del database vengono creati ogni settimana, i backup differenziali del database vengono creati generalmente ogni 12 ore e i backup del log delle transazioni vengono creati in genere ogni 5-10 minuti, con la frequenza in base al livello di prestazioni e quantità di attività del database. Il primo backup completo viene pianificato subito dopo la creazione di un database. Il completamento richiede in genere 30 minuti, ma potrebbe richiedere più tempo se le dimensioni del database sono elevate. Il backup iniziale, ad esempio, può richiedere più tempo in un database ripristinato o in una copia del database. Dopo il primo backup completo, l'esecuzione di tutti i successivi backup è pianificata e gestita automaticamente in background. Il momento esatto per l'esecuzione dei backup di database è determinato dal servizio SQL Database in modo da bilanciare il carico di lavoro complessivo del sistema.

I backup di ripristino temporizzato sono a ridondanza geografica e protetti dalla [riproduzione su più aree di Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Per altre informazioni vedere l'articolo relativo al [Ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Backup per la conservazione a lungo termine
Database SQL offre la possibilità di configurare la conservazione a lungo termine (LTR) dei backup completi fino a 10 anni. Se il criterio LTR è abilitato, i backup completi settimanali vengono copiati automaticamente in un contenitore di Archiviazione con ridondanza geografica e accesso in lettura diverso. A secondo dei vari requisiti di conformità, è possibile selezionare periodi di conservazione diversi per i backup settimanali, mensili e/o annuali. L'utilizzo delle risorse di archiviazione dipende dalla frequenza selezionata con cui vengono eseguiti backup e dal periodo di conservazione. È possibile usare lo strumento di [calcolo dei prezzi per la conservazione a lungo termine](https://azure.microsoft.com/pricing/calculator/?service=sql-database) per stimare il costo di questo tipo di archiviazione. 

Come i backup di ripristino temporizzato, i backup di conservazione a lungo termine sono a ridondanza geografica e protetti dalla [riproduzione su più aree di Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Per altre informazioni, vedere [Conservazione dei backup a lungo termine](sql-database-long-term-retention.md).

## <a name="are-backups-encrypted"></a>I backup sono crittografati?

Se il database è crittografato con TDE, i backup vengono crittografati automaticamente quando i dati sono inattivi, inclusi i backup di conservazione a lungo termine. Quando la crittografia TDE viene abilitata per un database SQL di Azure, anche i backup vengono crittografati. Tutti i nuovi database SQL di Azure vengono configurati con la crittografia TDE abilitata per impostazione predefinita. Per altre informazioni su TDE, vedere [Transparent Data Encryption con il database SQL di Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-do-automated-backups-impact-my-compliance"></a>In che modo i backup automatici influiscono sulla conformità?

Quando si migra il database da un livello di servizio basato su DTU con una conservazione di ripristino temporizzato predefinita di 35 giorni a un livello di servizio basato sulla vCore, la conservazione di ripristino temporizzato viene preservata per garantire che il criterio di ripristino dei dati dell'applicazione non venga compromesso. Se il periodo di conservazione predefinito non soddisfa i requisiti di conformità, è possibile modificare il periodo di conservazione di ripristino temporizzato utilizzando PowerShell o API REST. Visualizzare [Modifica del periodo di conservazione backup](#how-to-change-backup-retention-period) per ulteriori dettagli.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-backup-retention-period"></a>Come modificare il periodo di conservazione dei backup
È possibile modificare il periodo di conservazione predefinito utilizzando l'API REST o PowerShell. I valori supportati sono: 7, 14, 21, 28 e 35 giorni. Gli esempi che seguono illustrano come modificare la conservazione di ripristino temporizzato a 28 giorni. 

> [!NOTE]
> Queste API avranno un impatto solo sul periodo di conservazione di ripristino temporizzato. Se è configurata una conservazione a lungo termine per il database, questo non sarà interessato. Vedere [Conservazione del backup a lungo termine](sql-database-long-term-retention.md) per maggiori dettagli su come modificare i periodi di conservazione a lungo termine.

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Modificare il periodo di conservazione dei backup di ripristino temporizzato usando PowerShell
```powershell
Set-AzureRmSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```
> [!IMPORTANT]
> Questa API è inclusa nel modulo AzureRM.Sql PowerShell a partire dalla versione [4.7.0-preview](https://www.powershellgallery.com/packages/AzureRM.Sql/4.7.0-preview). 

### <a name="change-pitr-retention-period-using-rest-api"></a>Modificare il periodo di conservazione di ripristino temporizzato con l'API REST
**Richiesta di esempio**
```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```
**Corpo della richiesta**
```json
{
  "properties":{  
      "retentionDays":28
   }
}
```
**Risposta di esempio**

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
Per ulteriori dettagli, vedere [REST API di conservazione del backup](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Passaggi successivi

- I backup dei database sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. Per informazioni sulle altre soluzioni di continuità aziendale del database SQL Azure, vedere [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).
- Per eseguire il ripristino temporizzato nel portale di Azure, vedere l'articolo sul [ripristino temporizzato nel portale di Azure](sql-database-recovery-using-backups.md).
- Per eseguire il ripristino temporizzato usando PowerShell, vedere l'articolo sul [ripristino temporizzato con PowerShell](scripts/sql-database-restore-database-powershell.md).
- Per configurare, gestire e ripristinare dalla conservazione a lungo termine di backup automatici in un insieme di credenziali di Servizi di ripristino di Azure tramite il portale di Azure, vedere l'articolo sulla [gestione della conservazione a lungo termine dei backup tramite il portale di Azure](sql-database-long-term-backup-retention-configure.md).
- Per configurare, gestire e ripristinare dalla conservazione a lungo termine di backup automatici in un insieme di credenziali di Servizi di ripristino di Azure tramite Powershell, vedere l'articolo sulla [gestione della conservazione a lungo termine dei backup tramite il portale di PowerShell](sql-database-long-term-backup-retention-configure.md).

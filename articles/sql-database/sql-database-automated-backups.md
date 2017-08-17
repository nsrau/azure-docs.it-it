---
title: Backup automatici e con ridondanza geografica del database SQL di Azure | Microsoft Docs
description: Il database SQL crea automaticamente e ripetutamente una copia di backup locale del database a pochi minuti l'una dall'altra e usa l'archiviazione con ridondanza geografica e accesso in lettura di Azure per la ridondanza geografica.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 3ee3d49d-16fa-47cf-a3ab-7b22aa491a8d
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/05/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: 5e07dd0412570eedf69609fb42ec329d3b0c1936
ms.contentlocale: it-it
ms.lasthandoff: 08/01/2017

---
# <a name="learn-about-automatic-sql-database-backups"></a>Informazioni sui backup automatici del database SQL

Il database SQL crea automaticamente un backup del database e usa l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) di Azure per fornire una ridondanza geografica. Questi backup vengono creati automaticamente e senza costi aggiuntivi. Non è necessario intervenire manualmente per eseguire i backup. I backup dei database sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. Se si desidera mantenere i backup in un contenitore di archiviazione è possibile configurare un criterio di conservazione dei backup a lungo termine. Per altre informazioni, vedere [Long-term retention](sql-database-long-term-retention.md) (Conservazione a lungo termine).

## <a name="what-is-a-sql-database-backup"></a>Informazioni sul backup del database SQL

Il database SQL usa la tecnologia di SQL Server per creare backup [completi](https://msdn.microsoft.com/library/ms186289.aspx), [differenziali](https://msdn.microsoft.com/library/ms175526.aspx) e del [log delle transazioni](https://msdn.microsoft.com/library/ms191429.aspx). I backup del log delle transazioni vengono eseguiti in genere ogni 5-10 minuti e tale frequenza è determinata dal livello di prestazioni e dalla quantità delle attività del database. I backup del log delle transazioni, con backup completi e differenziali, consentono di ripristinare un database a un punto specifico nel tempo nello stesso server che ospita il database. Quando si ripristina un database, il servizio individua i backup completi, differenziali e del log delle transazioni da ripristinare.


È possibile usare questi backup per:

* Ripristinare un database in un punto nel tempo entro il periodo di memorizzazione. Questa operazione creerà un nuovo database nello stesso server del database originale.
* Ripristinare un database eliminato al momento in cui è stato eliminato o a qualsiasi momento del periodo di conservazione. Il database eliminato può essere ripristinato solo nello stesso server in cui è stato creato il database originale.
* Ripristinare un database in un'altra area geografica. Ciò consente di eseguire un ripristino di emergenza geografico quando è impossibile accedere al server e al database. Crea un nuovo database in qualsiasi server esistente ovunque nel mondo. 
* Ripristinare un database da un backup specifico nell'insieme di credenziali di Servizi di ripristino di Azure. Ciò consente di ripristinare una versione precedente del database per soddisfare una richiesta di conformità o di eseguire una versione precedente dell'applicazione. Vedere [Conservazione a lungo termine](sql-database-long-term-retention.md).
* Per eseguire un ripristino, vedere l'articolo su come [ripristinare un database da un backup](sql-database-recovery-using-backups.md).

> [!NOTE]
> In Archiviazione di Azure il termine *replica* fa riferimento alla copia dei file da una località a un'altra. La *replica di database* di SQL fa riferimento a più database secondari sincronizzati con un database primario. 
> 

## <a name="how-much-backup-storage-is-included-at-no-cost"></a>Quanto spazio di archiviazione di backup è incluso senza costi aggiuntivi?
Il database SQL offre fino al 200% delle risorse di archiviazione massime del database sottoposto a provisioning come risorsa di archiviazione di backup senza costi aggiuntivi. Se si usa ad esempio un'istanza di database Standard con una dimensione di database con provisioning pari a 250 GB, sono disponibili 500 GB di archiviazione di backup senza costi aggiuntivi. Se il database supera le risorse di archiviazione di backup fornite, è possibile scegliere di ridurre il periodo di conservazione contattando il supporto tecnico di Azure. È anche possibile pagare per ottenere risorse di archiviazione di backup aggiuntive, fatturate in base alla tariffa Standard per l'Archiviazione con ridondanza geografica e accesso in lettura. 

## <a name="how-often-do-backups-happen"></a>Con quale frequenza si verificano i backup?
I backup di database completi vengono eseguiti settimanalmente, i backup differenziali di solito sono eseguiti a intervalli di poche ore e i backup del log delle transazioni ogni 5-10 minuti. Il primo backup completo viene pianificato subito dopo la creazione di un database. Il completamento richiede in genere 30 minuti, ma potrebbe richiedere più tempo se le dimensioni del database sono elevate. Il backup iniziale, ad esempio, può richiedere più tempo in un database ripristinato o in una copia del database. Dopo il primo backup completo, l'esecuzione di tutti i successivi backup è pianificata e gestita automaticamente in background. Il momento esatto per l'esecuzione dei backup di database è determinato dal servizio SQL Database in modo da bilanciare il carico di lavoro complessivo del sistema. 

La replica geografica della risorsa di archiviazione di backup viene eseguita in base alla pianificazione della replica di Archiviazione di Azure.

## <a name="how-long-do-you-keep-my-backups"></a>Quanto tempo vengono conservati i backup?
Ogni backup del Database SQL ha un periodo di memorizzazione che si basa sul [livello del servizio](sql-database-service-tiers.md) del database. Il periodo di memorizzazione per un database nel:


* Il livello di servizio Base è 7 giorni.
* livello di servizio Standard è di 35 giorni.
* livello di servizio premium è di 35 giorni.

Se si esegue il downgrade del database dai livelli di servizio Standard o Premium al Basic, i backup vengono salvati per sette giorni. Tutti i backup esistenti più vecchi di sette giorni non sono più disponibili. 

Se si aggiorna il database dal livello del servizio Basic a Standard o Premium, il database SQL mantiene i backup esistenti fino a 35 giorni. Allo scadere dei 35, il database conserva i nuovi backup.

Se si elimina un database, il database SQL mantiene i backup come farebbe con un database online. Si supponga, ad esempio, che si elimina un database con livello di servizio Basic e periodo di conservazione di sette giorni. Per tre giorni viene conservato un backup dei quattro giorni precedenti.

> [!IMPORTANT]
> Se si elimina SQL Server di Azure che ospita i database SQL, vengono eliminati anche tutti i database appartenenti al server e non sarà possibile recuperarli. Non è possibile ripristinare un server eliminato.
> 

## <a name="how-to-extend-the-backup-retention-period"></a>Come estendere il periodo di conservazione dei backup?
Se l'applicazione richiede che i backup siano disponibili per un periodo di tempo più lungo, è possibile estendere il periodo di conservazione predefinito configurando il criterio di conservazione dei backup a lungo termine per singoli database (criterio LTR). Ciò consente di estendere il periodo di conservazione predefinito da 35 giorni fino a 10 anni. Per altre informazioni, vedere [Long-term retention](sql-database-long-term-retention.md) (Conservazione a lungo termine).

Dopo aver aggiunto il criterio LTR a un database tramite il portale di Azure o l'API, i backup settimanali completi del database verranno copiati automaticamente nel proprio insieme di credenziali di Servizio Backup di Azure. Se il database è crittografato con TDE, i backup vengono crittografati automaticamente quando i dati sono inattivi.  L'insieme di credenziali dei servizi eliminerà automaticamente i backup scaduti in base ai loro timestamp e al criterio LTR.  Non è quindi necessario gestire la pianificazione di backup o preoccuparsi di operazioni di pulitura dei vecchi file. L'API di ripristino supporta i backup archiviati nell'insieme di credenziali, purché l'insieme di credenziali si trovi nella stessa sottoscrizione del database SQL. È possibile usare il portale di Azure o PowerShell per accedere a questi backup.

> [!TIP]
> Per informazioni di guida, vedere [Configurare e ripristinare dalla conservazione dei backup a lungo termine del database SQL di Azure](sql-database-long-term-backup-retention-configure.md)
>

## <a name="are-backups-encrypted"></a>I backup sono crittografati?

Quando la crittografia TDE viene abilitata per un database SQL di Azure, anche i backup vengono crittografati. Tutti i nuovi database SQL di Azure vengono configurati con la crittografia TDE abilitata per impostazione predefinita. Per altre informazioni su TDE, vedere [Transparent Data Encryption con il database SQL di Azure](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database).

## <a name="next-steps"></a>Passaggi successivi

- I backup dei database sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. Per informazioni sulle altre soluzioni di continuità aziendale del database SQL Azure, vedere [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).
- Per eseguire il ripristino temporizzato nel portale di Azure, vedere l'articolo sul [ripristino temporizzato nel portale di Azure](sql-database-recovery-using-backups.md).
- Per eseguire il ripristino temporizzato usando PowerShell, vedere l'articolo sul [ripristino temporizzato con PowerShell](scripts/sql-database-restore-database-powershell.md).
- Per configurare, gestire e ripristinare dalla conservazione a lungo termine di backup automatici in un insieme di credenziali di Servizi di ripristino di Azure tramite il portale di Azure, vedere l'articolo sulla [gestione della conservazione a lungo termine dei backup tramite il portale di Azure](sql-database-long-term-backup-retention-configure.md).
- Per configurare, gestire e ripristinare dalla conservazione a lungo termine di backup automatici in un insieme di credenziali di Servizi di ripristino di Azure tramite Powershell, vedere l'articolo sulla [gestione della conservazione a lungo termine dei backup tramite il portale di PowerShell](sql-database-long-term-backup-retention-configure.md).


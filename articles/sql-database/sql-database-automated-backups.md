---
title: Backup automatici e con ridondanza geografica del database SQL di Azure | Microsoft Docs
description: Il database SQL crea automaticamente e ripetutamente una copia di backup locale del database a pochi minuti l'una dall'altra e usa l'archiviazione con ridondanza geografica e accesso in lettura di Azure per la ridondanza geografica.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.workload: Active
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: ab1793621950fd57d3f0be545772d85b32f5d7b8
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2018
---
# <a name="learn-about-automatic-sql-database-backups"></a>Informazioni sui backup automatici del database SQL

Il database SQL crea automaticamente i backup del database e usa l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) di Azure per fornire una ridondanza geografica. Questi backup vengono creati automaticamente e senza costi aggiuntivi. Non è necessario intervenire manualmente per eseguire i backup. I backup dei database sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. Se si desidera mantenere i backup in un contenitore di archiviazione è possibile configurare un criterio di conservazione dei backup a lungo termine. Per altre informazioni, vedere [Conservazione a lungo termine](sql-database-long-term-retention.md).

## <a name="what-is-a-sql-database-backup"></a>Che cos'è un backup del database SQL?

Il database SQL usa la tecnologia di SQL Server per creare backup [completi](https://msdn.microsoft.com/library/ms186289.aspx), [differenziali](https://msdn.microsoft.com/library/ms175526.aspx) e del [log delle transazioni](https://msdn.microsoft.com/library/ms191429.aspx) ai fini del ripristino temporizzato. I backup del log delle transazioni vengono eseguiti in genere ogni 5-10 minuti e tale frequenza è determinata dal livello di prestazioni e dalla quantità delle attività del database. I backup del log delle transazioni, con backup completi e differenziali, consentono di ripristinare un database a un punto specifico nel tempo nello stesso server che ospita il database. Quando si ripristina un database, il servizio individua i backup completi, differenziali e del log delle transazioni da ripristinare.


È possibile usare questi backup per:

* Ripristinare un database in un punto nel tempo entro il periodo di memorizzazione. Questa operazione creerà un nuovo database nello stesso server del database originale.
* Ripristinare un database eliminato al momento in cui è stato eliminato o a qualsiasi momento del periodo di conservazione. Il database eliminato può essere ripristinato solo nello stesso server in cui è stato creato il database originale.
* Ripristinare un database in un'altra area geografica. Ciò consente di eseguire un ripristino di emergenza geografico quando è impossibile accedere al server e al database. Crea un nuovo database in qualsiasi server esistente ovunque nel mondo. 
* Ripristinare un database da uno specifico backup a lungo termine se il database è stato configurato con criteri di conservazione a lungo termine. Ciò consente di ripristinare una versione precedente del database per soddisfare una richiesta di conformità o di eseguire una versione precedente dell'applicazione. Vedere [Conservazione a lungo termine](sql-database-long-term-retention.md).
* Per eseguire un ripristino, vedere l'articolo su come [ripristinare un database da un backup](sql-database-recovery-using-backups.md).

> [!NOTE]
> In Archiviazione di Azure il termine *replica* fa riferimento alla copia dei file da una località a un'altra. La *replica di database* di SQL fa riferimento a più database secondari sincronizzati con un database primario. 
> 

## <a name="how-often-do-backups-happen"></a>Con quale frequenza si verificano i backup?
I backup di database completi vengono eseguiti settimanalmente, i backup differenziali di solito sono eseguiti a intervalli di poche ore e i backup del log delle transazioni ogni 5-10 minuti. Il primo backup completo viene pianificato subito dopo la creazione di un database. Il completamento richiede in genere 30 minuti, ma potrebbe richiedere più tempo se le dimensioni del database sono elevate. Il backup iniziale, ad esempio, può richiedere più tempo in un database ripristinato o in una copia del database. Dopo il primo backup completo, l'esecuzione di tutti i successivi backup è pianificata e gestita automaticamente in background. Il momento esatto per l'esecuzione dei backup di database è determinato dal servizio SQL Database in modo da bilanciare il carico di lavoro complessivo del sistema. 

La replica geografica della risorsa di archiviazione di backup viene eseguita in base alla pianificazione della replica di Archiviazione di Azure.

## <a name="how-long-do-you-keep-my-backups"></a>Quanto tempo vengono conservati i backup?
Ogni backup del database SQL ha un periodo di conservazione che si basa sul [livello di servizio](sql-database-service-tiers.md) del database. Il periodo di conservazione per un database nel:


* livello di servizio Basic è di 7 giorni.
* livello di servizio Standard è di 35 giorni.
* livello di servizio Premium è di 35 giorni.
* Il livello Utilizzo generico è configurabile con un massimo di 35 giorni (7 giorni per impostazione predefinita)*
* Il livello Business Critical è configurabile con un massimo di 35 giorni (7 giorni per impostazione predefinita)*

\* Durante l'anteprima, il periodo di conservazione dei backup non è configurabile e ha una durata fissa di 7 giorni.

Se si converte un database con un periodo di conservazione dei backup più lungo in un database con un periodo di conservazione più breve, tutti i backup esistenti anteriori al periodo di conservazione del livello di destinazione non sono più disponibili.

Se si aggiorna un database con un periodo di conservazione più breve in un database con un periodo di conservazione più lungo, il database SQL mantiene i backup esistenti finché non viene raggiunto il periodo di conservazione più lungo. 

Se si elimina un database, il database SQL mantiene i backup come farebbe con un database online. Si supponga, ad esempio, che si elimina un database con livello di servizio Basic e periodo di conservazione di sette giorni. Per tre giorni viene conservato un backup dei quattro giorni precedenti.

> [!IMPORTANT]
> Se si elimina SQL Server di Azure che ospita i database SQL, vengono eliminati anche tutti i database appartenenti al server e non sarà possibile recuperarli. Non è possibile ripristinare un server eliminato.
> 

## <a name="how-to-extend-the-backup-retention-period"></a>Come estendere il periodo di conservazione dei backup?

Se l'applicazione richiede che i backup siano disponibili per un periodo di tempo più lungo rispetto al periodo massimo di conservazione dei backup per il ripristino temporizzato, è possibile configurare criteri di conservazione dei backup a lungo termine per singoli database. Ciò consente di estendere il periodo di conservazione predefinito dal periodo massimo di 35 giorni fino a 10 anni. Per altre informazioni, vedere [Conservazione a lungo termine](sql-database-long-term-retention.md).

Dopo che i criteri di conservazione a lungo termine sono stati aggiunti a un database tramite l'API o il portale di Azure, i backup settimanali completi del database vengono copiati automaticamente in un contenitore di archiviazione separato con ridondanza geografica e accesso in lettura per la conservazione a lungo termine. Se il database è crittografato con TDE, i backup vengono crittografati automaticamente quando i dati sono inattivi. Il database SQL eliminerà automaticamente i backup scaduti in base ai relativi timestamp e ai criteri di conservazione a lungo termine. Dopo aver configurato i criteri, non è necessario gestire la pianificazione dei backup o eseguire operazioni di pulitura dei file meno recenti. Per visualizzare, ripristinare o eliminare questi backup è possibile usare il portale di Azure o PowerShell.

## <a name="are-backups-encrypted"></a>I backup sono crittografati?

Quando la crittografia TDE viene abilitata per un database SQL di Azure, anche i backup vengono crittografati. Tutti i nuovi database SQL di Azure vengono configurati con la crittografia TDE abilitata per impostazione predefinita. Per altre informazioni su TDE, vedere [Transparent Data Encryption con il database SQL di Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="are-the-automatic-backups-compliant-with-gdpr"></a>I backup automatici sono conformi al Regolamento generale sulla protezione dei dati?
Se il backup contiene dati personali ed è quindi soggetto al Regolamento generale sulla protezione dei dati, è necessario applicare misure di sicurezza avanzate per proteggere i dati dall'accesso non autorizzato. Per assicurare la conformità al Regolamento generale sulla protezione dei dati, è necessario adottare una soluzione per gestire le richieste di dati dei proprietari senza dover accedere ai backup.  Per i backup a breve termine, una soluzione può essere quella di configurare un intervallo di backup più breve, inferiore a 30 giorni, ovvero l'intervallo consentito per completare le richieste di accesso ai dati.  Se sono necessari backup con un periodo di conservazione più lungo, è consigliabile archiviare nei backup solo dati con pseudonimi. In questo modo, se i dati relativi a una persona devono essere eliminati o aggiornati, non sarà necessario eliminare o aggiornare i backup esistenti. Altre informazioni sulle procedure consigliate per la conformità al Regolamento generale sulla protezione dei dati sono disponibili in [Data Governance for GDPR Compliance](https://info.microsoft.com/DataGovernanceforGDPRCompliancePrinciplesProcessesandPractices-Registration.html) (Governance dei dati per la conformità al Regolamento generale sulla protezione dei dati).

## <a name="next-steps"></a>Passaggi successivi

- I backup dei database sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. Per informazioni sulle altre soluzioni di continuità aziendale del database SQL Azure, vedere [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).
- Per eseguire il ripristino temporizzato nel portale di Azure, vedere l'articolo sul [ripristino temporizzato nel portale di Azure](sql-database-recovery-using-backups.md).
- Per eseguire il ripristino temporizzato usando PowerShell, vedere l'articolo sul [ripristino temporizzato con PowerShell](scripts/sql-database-restore-database-powershell.md).
- Per configurare, gestire e ripristinare dalla conservazione a lungo termine di backup automatici in un insieme di credenziali di Servizi di ripristino di Azure tramite il portale di Azure, vedere l'articolo sulla [gestione della conservazione a lungo termine dei backup tramite il portale di Azure](sql-database-long-term-backup-retention-configure.md).
- Per configurare, gestire e ripristinare dalla conservazione a lungo termine di backup automatici in un insieme di credenziali di Servizi di ripristino di Azure tramite Powershell, vedere l'articolo sulla [gestione della conservazione a lungo termine dei backup tramite il portale di PowerShell](sql-database-long-term-backup-retention-configure.md).

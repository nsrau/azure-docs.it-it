---
title: Backup del database di SQL - automatico, con ridondanza geografica | Documentazione Microsoft
description: Il database SQL crea automaticamente e ripetutamente una copia di backup locale del database a pochi minuti l&quot;una dall&quot;altra e usa l&quot;archiviazione con ridondanza geografica e accesso in lettura di Azure per la ridondanza geografica.
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 3ee3d49d-16fa-47cf-a3ab-7b22aa491a8d
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2016
ms.author: sashan;carlrab;barbkess
translationtype: Human Translation
ms.sourcegitcommit: a5b69625272b96fc9e42b9b7e57b5401351d8d6e
ms.openlocfilehash: 9e58a144be039db609ad1400246ef2de05c1b84d


---
# <a name="learn-about-sql-database-backups"></a>Informazioni sul backup del database SQL
<!------------------
This topic is annotated with TEMPLATE guidelines for FEATURE TOPICS.

Metadata guidelines

pageTitle
    60 characters or less. Includes name of the feature - primary benefit. Not the same as H1. Its 60 characters or fewer including all characters between the quotes and the Microsoft Azure site identifier.

description
    115-145 characters. Duplicate of the first sentence in the introduction. This is the abstract of the article that displays under the title when searching in Bing or Google. 

    Example: "SQL Database automatically creates a local database backup every few minutes and uses Azure read-access geo-redundant storage for geo-redundancy."

TEMPLATE GUIDELINES for feature topics

The Feature Topic is a one-pager (ok, sometimes longer) that explains a capability of the product or service. It explains what the capability is and characteristics of the capability.  

It is a "learning" topic, not an action topic.

DO explain this:
    • Definition of the feature terminology.  i.e., What is a database backup?
    • Characteristics and capabilities of the feature. (How the feature works)
    • Common uses with links to overview topics that recommend when to use the feature.
    • Reference specifications (Limitations and Restrictions, Permissions, General Remarks, etc.)
    • Next Steps with links to related overviews, features, and tasks.

DON'T explain this:
    • How to steps for using the feature (Tasks)
    • How to solve business problems that incorporate the feature (Overviews)

GUIDELINES for the H1 

    The H1 should answer the question "What is in this topic?" Write the H1 heading in conversational language and use search key words as much as possible. Since this is a learning topic, make sure the title indicates that and doesn't mislead people to think this will tell them how to do tasks.  

    To help people understand this is a learning topic and not an action topic, start the title with "Learn about ... "

    Heading must use an industry standard term. If your feature is a proprietary name like "Elastic database pools", use a synonym. For example:    "Learn about elastic database pools for multi-tenant databases". In this case multi-tenant database is the industry-standard term that will be an anchor for finding the topic.

GUIDELINES for introduction

    The introduction is 1-2 sentences.  It is optimized for search and sets proper expectations about what to expect in the article. It should contain the top key words that you are using throughout the article.The introduction should be brief and to the point of what the feature is, what it is used for, and what's in the article. 

    If the introduction is short enough, your article can pop to the top in Google Instant Answers.

    In this example:

Sentence #1 Explains what the article will cover, which is what the feature is or does. This is also the metadata description. 
    SQL Database automatically creates a database backup every five minutes and uses Azure read-access geo-redundant storage (RA-GRS) to provide geo-redundancy. 

Sentence #2 Explains why I should care about this.  
    Database backups are an essential part of any business continuity and disaster recovery strategy because they protect your data from accidental corruption or deletion.

-------------------->

Il database SQL crea automaticamente un backup del database e usa l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) di Azure per fornire una ridondanza geografica. Questi backup vengono creati automaticamente e senza costi aggiuntivi. Non è necessario intervenire manualmente per eseguire i backup. I backup dei database sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. Se si desidera mantenere i backup in un contenitore di archiviazione è possibile configurare un criterio di conservazione dei backup a lungo termine. Per altre informazioni, vedere [Long-term retention](sql-database-long-term-retention.md) (Conservazione a lungo termine).

<!-- This image needs work, so not putting it in right now.

This diagram shows SQL Database running in the US East region. It creates a database backup every five minutes, which it stores locally to Azure Read Access Geo-redundant Storage (RA-GRS). Azure uses geo-replication to copy the database backups to a paired data center in the US West region.

![geo-restore](./media/sql-database-geo-restore/geo-restore-1.png)

-->

<!---------------
GUIDELINES for the first ## H2.

    The first ## describes what the feature encompasses and how it is used. It points to related task articles.

    For consistency, being the heading with "What is ... "
----------------->

## <a name="what-is-a-sql-database-backup"></a>Informazioni sul backup del database SQL
<!-- 
    Explains what a SQL Database backup is and answers an important question that people want to know.
-->


<!----------------- 
    Explains first component of the backup feature
------------------>

Il database SQL usa la tecnologia di SQL Server per creare backup [completi](https://msdn.microsoft.com/library/ms186289.aspx), [differenziali](https://msdn.microsoft.com/library/ms175526.aspx) e del [log delle transazioni](https://msdn.microsoft.com/library/ms191429.aspx). I backup del log delle transazioni vengono eseguiti in genere ogni 5-10 minuti e tale frequenza è determinata dal livello di prestazioni e dalla quantità delle attività del database. I backup del log delle transazioni, con backup completi e differenziali, consentono di ripristinare un database a un punto specifico nel tempo nello stesso server che ospita il database. Quando si ripristina un database, il servizio individua i backup completi, differenziali e del log delle transazioni da ripristinare.

<!--------------- 
    Explicit list of what to do with a local backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

È possibile usare questi backup per:

* Ripristinare un database in un punto nel tempo entro il periodo di memorizzazione. Questa operazione creerà un nuovo database nello stesso server del database originale.
* Ripristinare un database eliminato al momento in cui è stato eliminato o a qualsiasi momento del periodo di conservazione. Il database eliminato può essere ripristinato solo nello stesso server in cui è stato creato il database originale.
* Ripristinare un database in un'altra area geografica. Ciò consente di eseguire un ripristino di emergenza geografico quando è impossibile accedere al server e al database. Crea un nuovo database in qualsiasi server esistente ovunque nel mondo. 
* Ripristinare un database da un backup specifico nell'insieme di credenziali di Servizi di ripristino di Azure. Ciò consente di ripristinare una versione precedente del database per soddisfare una richiesta di conformità o di eseguire una versione precedente dell'applicazione. Vedere [Conservazione a lungo termine](sql-database-long-term-retention.md).
* Per eseguire un ripristino, vedere l'articolo su come [ripristinare un database da un backup](sql-database-recovery-using-backups.md).

<!----------------- 
    Explains first component of the backup feature
------------------>

<!--------------- 
    Explicit list of what to do with a geo-redundant backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

> [!NOTE]
> In Archiviazione di Azure il termine *replica* fa riferimento alla copia dei file da una località a un'altra. La *replica di database* di SQL fa riferimento a più database secondari sincronizzati con un database primario. 
> 
> 

<!----------------
    The next ## H2's discuss key characteristics of how the feature works. The title is in conversational language and asks the question that will be answered.
------------------->
## <a name="how-much-backup-storage-is-included-at-no-cost"></a>Quanto spazio di archiviazione di backup è incluso senza costi aggiuntivi?
Il database SQL offre fino al 200% delle risorse di archiviazione massime del database sottoposto a provisioning come risorsa di archiviazione di backup senza costi aggiuntivi. Se si usa ad esempio un'istanza di database Standard con una dimensione di database con provisioning pari a 250 GB, sono disponibili 500 GB di archiviazione di backup senza costi aggiuntivi. Se il database supera le risorse di archiviazione di backup fornite, è possibile scegliere di ridurre il periodo di conservazione contattando il supporto tecnico di Azure. È anche possibile pagare per ottenere risorse di archiviazione di backup aggiuntive, fatturate in base alla tariffa Standard per l'Archiviazione con ridondanza geografica e accesso in lettura. 

## <a name="how-often-do-backups-happen"></a>Con quale frequenza si verificano i backup?
I backup di database completi vengono eseguiti settimanalmente, i backup differenziali di solito sono eseguiti a intervalli di poche ore e i backup del log delle transazioni ogni 5-10 minuti. Il primo backup completo viene pianificato subito dopo la creazione di un database. Il completamento richiede in genere 30 minuti, ma potrebbe richiedere più tempo se le dimensioni del database sono elevate. Il backup iniziale, ad esempio, può richiedere più tempo in un database ripristinato o in una copia del database. Dopo il primo backup completo, l'esecuzione di tutti i successivi backup è pianificata e gestita automaticamente in background. Il momento esatto per l'esecuzione dei backup di database è determinato dal servizio SQL Database in modo da bilanciare il carico di lavoro complessivo del sistema. 

La replica geografica della risorsa di archiviazione di backup viene eseguita in base alla pianificazione della replica di Archiviazione di Azure.

## <a name="how-long-do-you-keep-my-backups"></a>Quanto tempo vengono conservati i backup?
Ogni backup del Database SQL ha un periodo di memorizzazione che si basa sul [livello del servizio](sql-database-service-tiers.md) del database. Il periodo di memorizzazione per un database nel:

<!------------------

    Using a list so the information is easy to find when scanning.
------------------->

* Il livello di servizio Base è 7 giorni.
* livello di servizio Standard è di 35 giorni.
* livello di servizio premium è di 35 giorni.

Se si esegue il downgrade del database dai livelli di servizio Standard o Premium al Basic, i backup vengono salvati per sette giorni. Tutti i backup esistenti più vecchi di sette giorni non sono più disponibili. 

Se si aggiorna il database dal livello del servizio Basic a Standard o Premium, il database SQL mantiene i backup esistenti fino a 35 giorni. Allo scadere dei 35, il database conserva i nuovi backup.

Se si elimina un database, il database SQL mantiene i backup come farebbe con un database online. Si supponga, ad esempio, che si elimina un database con livello di servizio Basic e periodo di conservazione di sette giorni. Per tre giorni viene conservato un backup dei quattro giorni precedenti.

> [!IMPORTANT]
> Se si elimina SQL Server di Azure che ospita i database SQL, vengono eliminati anche tutti i database appartenenti al server e non sarà possibile recuperarli. Non è possibile ripristinare un server eliminato.
> 
> 

## <a name="how-to-extend-the-backup-retention-period"></a>Come estendere il periodo di conservazione dei backup?
Se l'applicazione richiede che i backup siano disponibili per un periodo di tempo più lungo, è possibile estendere il periodo di conservazione predefinito configurando il criterio di conservazione dei backup a lungo termine per singoli database (criterio LTR). Ciò consente di estendere il periodo di conservazione predefinito da 35 giorni fino a 10 anni. Per altre informazioni, vedere [Long-term retention](sql-database-long-term-retention.md) (Conservazione a lungo termine).

Dopo aver aggiunto il criterio LTR a un database tramite il portale di Azure o l'API, i backup settimanali completi del database verranno copiati automaticamente nel proprio insieme di credenziali di Servizio Backup di Azure. Se il database è crittografato con TDE, i backup vengono crittografati automaticamente quando i dati sono inattivi.  L'insieme di credenziali dei servizi eliminerà automaticamente i backup scaduti in base ai loro timestamp e al criterio LTR.  Non sarà pertanto necessario gestire la pianificazione di backup o preoccuparsi di operazioni di pulitura dei vecchi file. L'API di ripristino supporta i backup archiviati nell'insieme di credenziali, purché l'insieme di credenziali si trovi nella stessa sottoscrizione del database SQL. È possibile usare il portale o PowerShell per accedere a questi backup.

<!-------------------
OPTIONAL section
## Best practices 
--------------------->

<!-------------------
OPTIONAL section
## General remarks
--------------------->

<!-------------------
OPTIONAL section
## Limitations and restrictions
--------------------->

<!-------------------
OPTIONAL section
## Metadata
--------------------->

<!-------------------
OPTIONAL section
## Performance
--------------------->

<!-------------------
OPTIONAL section
## Permissions
--------------------->

<!-------------------
OPTIONAL section
## Security
--------------------->

<!-------------------
GUIDELINES for Next Steps

    The last section is Next Steps. Give a next step that would be relevant to the customer after they have learned about the feature and the tasks associated with it.  Perhaps point them to one or two key scenarios that use this feature.

    You don't need to repeat links you have already given them.
--------------------->

## <a name="next-steps"></a>Passaggi successivi
I backup dei database sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. Per informazioni sulle altre soluzioni di continuità aziendale del database SQL Azure, vedere [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).




<!--HONumber=Nov16_HO5-->



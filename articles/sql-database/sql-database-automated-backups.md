<properties
   pageTitle="Informazioni sul backup del database SQL | Microsoft Azure" 
   description="Informazioni sui backup dei database predefiniti del database SQL che consentono di ripristinare un database SQL di Azure a un momento precedente o di copiare un database in uno nuovo in un'area geografica (fino a 35 giorni)."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="carlrab"/>


# <a name="learn-about-sql-database-backups"></a>Informazioni sul backup del database SQL

Il database SQL consente di creare un backup locale del database ogni cinque minuti e usa l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) di Azure per copiare alcuni backup del database in un'altra area geografica. Questi backup si verificano **automaticamente e senza costi aggiuntivi**. Usare i backup dei database locali per [ripristinare il database a un momento specifico](sql-database-point-in-time-restore-portal.md) nello stesso server. Usare i backup con ridondanza geografica per [ripristinare il database in un'area geografica diversa](sql-database-geo-restore-portal.md).  

>[AZURE.NOTE] I backup con ridondanza geografica e i backup locali vengono eseguiti in modo automatico. Non è necessario eseguire alcuna azione e non sono previsti costi aggiuntivi. 

Nel diagramma seguente il database SQL è in esecuzione nell'area Stati Uniti orientali. Viene creato un backup del database ogni cinque minuti, archiviato in locale nell'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) di Azure. Azure copia i backup del database in un data center associato nell'area Stati Uniti occidentali.

![Ripristino geografico](./media/sql-database-geo-restore/geo-restore-1.png)

<!--## What is <feature>?" -->

## <a name="what-is-a-sql-database-backup"></a>Informazioni sul backup del database SQL  

<!-- 
First sentence begins with "The <feature> is ..." followed by a definition of the feature. Provide a 1-2 paragraph intro to explain what the feature is, how it works, and the importance of the feature for solving business problems.
-->
Un backup del database SQL è un file che archivia le informazioni relative allo stato del database in un momento specifico. Il database SQL usa la tecnologia di SQL Server per creare backup locali [completi](https://msdn.microsoft.com/library/ms186289.aspx), [differenziali](https://msdn.microsoft.com/library/ms175526.aspx ) e del [log delle transazioni](https://msdn.microsoft.com/library/ms191429.aspx). I backup del log delle transazioni viene eseguito ogni cinque minuti, consentendo di eseguire un ripristino in un punto specifico del tempo all'interno dello stesso server che ospita il database. Quando si ripristina un database, il servizio individua i backup completi, differenziali e del log delle transazioni da ripristinare.

>[AZURE.NOTE] Il database SQL crea automaticamente backup di database locali e backup con ridondanza geografica. Non è necessario intervenire manualmente per eseguire i backup. Non sono previsti costi aggiuntivi.

Usare il backup del database per:

- Ripristinare un database in un punto nel tempo entro il periodo di memorizzazione. Con un backup del database è possibile ripristinare un database a un punto specifico del tempo, ripristinare un database eliminato al momento in cui è stato eliminato oppure ripristinare un database in un'altra area geografica. Per eseguire un ripristino, vedere l'articolo su come [ripristinare un database da un backup del database](sql-database-recovery-using-backups.md).

- Copiare un database in un SQL server nella stessa area o in un'area diversa. La copia è coerente a livello transazionale con il database SQL corrente. Per eseguire una copia, vedere l'articolo relativo alla [copia del database](sql-database-copy.md).

- Archiviare il backup del database oltre il periodo di conservazione dei backup. Per eseguire l'archiviazione, [esportare un database SQL in un file con estensione BACPAC](sql-database-export.md). È possibile archiviare il file di archiviazione a lungo termine BACPAC e conservarlo oltre il periodo di conservazione. In alternativa, usare il file BACPAC per trasferire una copia del database in SQL Server, in locale o in una macchina virtuale di Azure.

## <a name="backups-have-geographical-redundancy"></a>I backup hanno ridondanza geografica

Il database SQL usa la [replica dell'archiviazione di Azure](../storage/storage-redundancy.md) per eseguire il backup del database in una posizione geografica differente. Per fornire archiviazione con ridondanza geografica, il database SQL archivia i file di backup del database locale in un account di [archiviazione con ridondanza geografica e accesso in lettura (RA-GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage). Azure replica i file di backup in un [data center associato](../best-practices-availability-paired-regions.md). La replica geografica assicura che sia possibile ripristinare un database nel caso in cui non si possa accedere al backup del database dall'area del database primario. 

>[AZURE.NOTE] In Archiviazione di Azure il termine *replica* fa riferimento alla copia dei file da una località a un'altra. La *replica di database* di SQL fa riferimento a più database secondari sincronizzati con un database primario. 


## <a name="how-much-backup-storage-is-included-at-no-cost"></a>Quanto spazio di archiviazione di backup è incluso senza costi aggiuntivi?

Il database SQL offre fino al 200% delle risorse di archiviazione massime del database sottoposto a provisioning come risorsa di archiviazione di backup senza costi aggiuntivi. Se si usa ad esempio un'istanza di database Standard con una dimensione di database con provisioning pari a 250 GB, sono disponibili 500 GB di archiviazione di backup senza costi aggiuntivi. Se il database supera le risorse di archiviazione di backup fornite, è possibile scegliere di ridurre il periodo di conservazione contattando il supporto tecnico di Azure. È anche possibile pagare per ottenere risorse di archiviazione di backup aggiuntive, fatturate in base alla tariffa Standard per l'Archiviazione con ridondanza geografica e accesso in lettura. 

## <a name="how-often-do-backups-happen"></a>Con quale frequenza si verificano i backup?

Il backup completo, il backup differenziale e il backup del log delle transazioni vengono eseguiti rispettivamente ogni settimana, ogni ora e ogni cinque minuti. Il primo backup completo viene pianificato subito dopo la creazione di un database. Il completamento richiede in genere 30 minuti, ma potrebbe richiedere più tempo se le dimensioni del database sono elevate. Il backup iniziale, ad esempio, può richiedere più tempo in un database ripristinato o in una copia del database. Dopo il primo backup completo, l'esecuzione di tutti i successivi backup è pianificata e gestita automaticamente in background. Il momento esatto per l'esecuzione dei backup di database completi e [differenziali](https://msdn.microsoft.com/library/ms175526.aspx) viene determinato in modo da bilanciare il carico di lavoro di sistema complessivo. 

## <a name="how-long-do-you-keep-my-backups"></a>Quanto tempo vengono conservati i backup?

Ogni backup del Database SQL ha un periodo di memorizzazione che si basa sul [livello del servizio](sql-database-service-tiers.md) del database. Il periodo di memorizzazione per un database nel:

- livello di servizio Basic è di sette giorni.
- livello di servizio Standard è di 35 giorni.
- livello di servizio premium è di 35 giorni.


Se si esegue il downgrade del database dai livelli di servizio Standard o Premium al Basic, i backup vengono salvati per sette giorni. Tutti i backup esistenti più vecchi di sette giorni non sono più disponibili. 

Se si aggiorna il database dal livello del servizio Basic a Standard o Premium, il database SQL mantiene i backup esistenti fino a 35 giorni. Allo scadere dei 35, il database conserva i nuovi backup.
 
Se si elimina un database, il database SQL mantiene i backup come farebbe con un database online. Si supponga, ad esempio, che si elimina un database con livello di servizio Basic e periodo di conservazione di sette giorni. Per tre giorni viene conservato un backup dei quattro giorni precedenti.

> [AZURE.IMPORTANT] Se si elimina SQL Server di Azure che ospita i database SQL, vengono eliminati anche tutti i database appartenenti al server e non sarà possibile recuperarli. Non è possibile ripristinare un server eliminato.

## <a name="next-steps"></a>Passaggi successivi

I backup dei database sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. Per informazioni su come integrare i backup del database nella strategia aziendale, vedere [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).





<!---HONumber=Oct16_HO2-->



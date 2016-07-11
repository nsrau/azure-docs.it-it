<properties 
   pageTitle="Esercitazioni per il ripristino di emergenza del database SQL | Microsoft Azure" 
   description="Istruzioni e procedure consigliate relative all'uso del database SQL di Azure per eseguire esercitazioni per il ripristino di emergenza che consentono di mantenere la resilienza delle applicazioni aziendali cruciali in caso di errori e interruzioni del servizio." 
   services="sql-database" 
   documentationCenter="" 
   authors="mihaelablendea" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="06/16/2016"
   ms.author="mihaelab"/>

#Esercitazione per il ripristino di emergenza

È consigliabile verificare periodicamente la conformità delle applicazioni per il flusso di lavoro di ripristino. La verifica del comportamento di un'applicazione e delle implicazioni legate alla perdita di dati e/o all'interruzione del servizio che comporta il failover è una buona norma di progettazione. Inoltre, questa verifica è richiesta dalla maggior parte degli standard del settore come parte della certificazione di continuità aziendale.

L'esercitazione per il ripristino di emergenza prevede l'esecuzione delle attività seguenti:

- Simulazione dell'interruzione del livello dati
- Ripristino
- Convalida dell'integrità dell'applicazione dopo il ripristino

A seconda della modalità di [progettazione dell’applicazione per la continuità aziendale](sql-database-business-continuity.md), il flusso di lavoro dell'esercitazione può variare. Di seguito sono descritte le procedure consigliate per eseguire un'esercitazione per il ripristino di emergenza nel contesto del database SQL di Azure.

##Ripristino geografico

Per evitare il rischio di perdita di dati durante l'esecuzione di un'esercitazione per il ripristino di emergenza, è consigliabile usare un ambiente di test creando una copia dell'ambiente di produzione e usandolo per verificare il flusso di lavoro di failover dell'applicazione.
 
####Simulazione dell'interruzione del servizio

Per simulare l'interruzione è possibile eliminare o rinominare il database di origine. Ciò causerà un errore di connettività dell'applicazione.

####Ripristino

- Eseguire il ripristino geografico del database in un server diverso, come descritto [qui](sql-database-disaster-recovery.md).
- Modificare la configurazione dell'applicazione per connettersi ai database ripristinati e seguire la guida [Configurare un database dopo il ripristino](sql-database-disaster-recovery.md) per completare il ripristino.

####Convalida

- Completare l'esercitazione verificando l'integrità dell'applicazione dopo il ripristino (ad esempio, stringhe di connessione, account di accesso, test di funzionalità di base o altre verifiche correlate alle procedure standard di convalida delle applicazioni).

##Replica geografica

Per un database protetto mediante la replica geografica l’esercitazione comporterà un failover pianificato per il database secondario. Il failover pianificato assicura che i database primario e secondario restino sincronizzati quando si invertono i ruoli. A differenza del failover non pianificato, questa operazione non comporterà la perdita di dati, quindi l’esercitazione può essere realizzata nell'ambiente di produzione.

####Simulazione dell'interruzione del servizio

Per simulare l'interruzione è possibile disabilitare l'applicazione web o la macchina virtuale connessa al database. Ciò genererà errori di connessione per i client web.

####Ripristino

- Assicurarsi che la configurazione dell'applicazione nell'area DR punti al database secondario precedente, che diventerà un nuovo database primario completamente accessibile.
- Eseguire [failover pianificato](sql-database-geo-replication-powershell.md#initiate-a-planned-failover) per rendere il database secondario un nuovo database primario
- Seguire la guida [Configurare un database dopo il ripristino](sql-database-disaster-recovery.md) per completare il ripristino.

####Convalida

- Completare l'esercitazione verificando l'integrità dell'applicazione dopo il ripristino (ad esempio, stringhe di connessione, account di accesso, test di funzionalità di base o altre verifiche correlate alle procedure standard di convalida delle applicazioni).


## Passaggi successivi

- Per informazioni sui backup automatici del database SQL di Azure, vedere [Panoramica: Backup automatici del database SQL](sql-database-automated-backups.md)
- Per informazioni sugli scenari di progettazione e ripristino della continuità aziendale, vedere l'articolo relativo agli [scenari di continuità aziendale](sql-database-business-continuity-scenarios.md)
- Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)
- Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md)
- Per altre informazioni sull'uso dei backup automatici per l'archiviazione, vedere [Copiare un database SQL di Azure](sql-database-copy.md)

<!---HONumber=AcomDC_0629_2016-->
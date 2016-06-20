<properties 
   pageTitle="Progettazione del database SQL per la continuità aziendale" 
   description="Linee guida per la scelta delle funzionalità di ripristino di emergenza per la continuità aziendale in base a requisiti specifici. Sono incluse le descrizioni delle funzionalità offerte automaticamente dal database SQL."
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="05/27/2016"
   ms.author="elfish"/>

# Progettazione per la continuità aziendale

Quando si progetta un'applicazione per la continuità aziendale è necessario fornire una risposta alle domande seguenti:

1. Quale funzionalità per la continuità aziendale è adatta a proteggere l'applicazione da eventuali interruzioni del servizio?
2. Quale livello di ridondanza e topologia di replica è opportuno usare?

Per strategie di ripristino dettagliate da usare con un pool elastico, vedere [Strategie di ripristino di emergenza per applicazioni che usano il pool elastico del database SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

## Quando usare il ripristino geografico

La funzionalità di [ripristino geografico](sql-database-geo-restore.md) fornisce un'opzione predefinita di ripristino quando un database non è disponibile a causa di un evento imprevisto nell'area in cui è ospitato. Per impostazione predefinita, il database SQL offre una protezione di base incorporata per ciascun database. archiviando i backup del database in Archiviazione di Azure con ridondanza geografica (GRS). Se si sceglie questo metodo, non è necessaria alcuna configurazione speciale o allocazione di risorse aggiuntive. Con questi backup, è possibile ripristinare il database in qualsiasi area usando il comando per il ripristino geografico. Per informazioni dettagliate sull'uso della funzionalità di ripristino geografico per ripristinare l'applicazione, vedere [Ripristino dopo un'interruzione del servizio](sql-database-disaster-recovery.md).

È consigliabile usare la protezione incorporata se l'applicazione soddisfa i criteri seguenti:

1. Non è considerata cruciale. Non ha un contratto di servizio vincolante e, di conseguenza, un tempo di inattività di 24 o più ore non comporta alcuna responsabilità finanziaria.
2. La frequenza di modifica dei dati è bassa (ad esempio, è espressa in termini di transazioni all'ora). Un valore RPO di 1 ora non genererà una perdita di dati significativa.
3. L'applicazione è suscettibile alla variazione del costo e non può giustificare il costo aggiuntivo della replica geografica. 

Per abilitare la funzionalità di ripristino geografico, vedere [Eseguire il ripristino geografico di un database SQL di Azure da un backup con ridondanza geografica tramite il portale di Azure](sql-database-geo-restore-portal.md).

> [AZURE.NOTE] La funzionalità di ripristino geografico non esegue un'allocazione preliminare della capacità di calcolo in un'area specifica per ripristinare i database attivi dal backup durante l'interruzione del servizio. Il servizio gestirà il carico di lavoro associato alle richieste di ripristino geografico in modo da ridurre al minimo l'impatto sui database esistenti in tale area e le loro richieste di capacità saranno prioritarie. Pertanto, il tempo di ripristino del database dipenderà da quanti altri database verranno ripristinati contemporaneamente nella stessa area.

## Quando usare la replica geografica attiva

La funzionalità di [replica geografica attiva](sql-database-geo-replication-overview.md) consente di creare database leggibili (secondari) in un'area diversa da quella del database primario. Questa funzionalità garantisce che il database abbia le risorse di calcolo e i dati necessari per supportare il carico di lavoro dell'applicazione dopo il ripristino. Per informazioni sull'uso del failover per ripristinare l'applicazione, vedere [Ripristino dopo un'interruzione del servizio](sql-database-disaster-recovery.md).

È consigliabile utilizzare la replica geografica se l’applicazione soddisfa i criteri seguenti:

1. È considerata cruciale. Ha un contratto di servizio vincolante con valori RPO e RTO elevati. La perdita di dati e disponibilità comporta una responsabilità finanziaria. 
2. La frequenza di modifica dei dati è alta (ad esempio, è espressa in termini di transazioni al minuto o al secondo). Un valore RPO di 1 ora associato alla protezione predefinita avrà come risultato una perdita di dati inaccettabile.
3. Il costo legato all'uso della replica geografica è notevolmente inferiore rispetto alla potenziale responsabilità finanziaria e alla perdita di profitto associata.

Per abilitare la funzionalità di replica geografica attiva, vedere [Configurare la replica geografica per il database SQL di Azure con il portale di Azure](sql-database-geo-replication-portal.md)

> [AZURE.NOTE] La replica geografica attiva supporta inoltre l'accesso in sola lettura al database secondario e fornisce quindi capacità aggiuntiva per i carichi di lavoro di sola lettura.




##Come scegliere la configurazione di failover 

Quando si progetta l'applicazione per la continuità aziendale, è necessario considerare alcune opzioni di configurazione. La scelta dipenderà dalla topologia di distribuzione dell'applicazione e dalle parti dell'applicazione più vulnerabili a un'interruzione del servizio. Per informazioni aggiuntive, vedere [Progettazione di soluzioni cloud per il ripristino di emergenza mediante la replica geografica](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

<!---HONumber=AcomDC_0608_2016-->
---
title: Continuità aziendale cloud - Ripristino del database - Database SQL | Documentazione Microsoft
description: Informazioni su come il database SQL di Azure supporta la continuità aziendale cloud e il ripristino del database e consente di mantenere le applicazioni cloud cruciali in esecuzione.
keywords: continuità aziendale, continuità aziendale cloud, ripristino di emergenza del database, ripristino del database
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 10/23/2018
ms.openlocfilehash: 14fdc0dc12debb32f63d7322c233e06fc2fc0d9e
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161535"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Panoramica della continuità aziendale del database SQL di Azure

Database SQL di Azure è un'implementazione del motore di database di Microsoft SQL Server configurata e ottimizzata per l'ambiente cloud di Azure, che offre[disponibilità elevata](sql-database-high-availability.md) e resilienza agli errori che potrebbero interessare i processo di business. **La continuità aziendale** nel database SQL di Azure fa riferimento a meccanismi, criteri e procedure che consentono a un'azienda di continuare a funzionare in caso di interruzione, in particolare per quanto riguarda la propria infrastruttura di calcolo. Nella maggior parte dei casi, il database SQL di Azure gestirà gli eventi di arresto improvviso che potrebbero verificarsi nell'ambiente cloud e manterrà i processi aziendali in esecuzione. Tuttavia, esistono alcuni eventi di arresto improvviso che non possono essere gestiti dal database SQL, ad esempio:

- Un utente elimina o aggiorna accidentalmente una riga in una tabella.
- Un utente malintenzionato riesce a eliminare dati o database.
- Un terremoto causa un'interruzione dell'alimentazione e una disabilitazione temporanea del data center.

Questi casi non possono essere controllati dal database SQL di Azure ed è quindi necessario usare la funzionalità di continuità aziendale nel database SQL, che consente di recuperare i dati e mantenere le applicazioni in esecuzione.

Questa panoramica descrive le funzionalità offerte dal database SQL di Azure per la continuità aziendale e il ripristino di emergenza. Informazioni su opzioni, raccomandazioni ed esercitazioni per il ripristino da eventi di arresto improvviso che potrebbero provocare la perdita di dati o la disattivazione del database e dell'applicazione. Informazioni sulle operazioni da eseguire quando si verifica un errore generato da un utente o da un'applicazione che influisce sull'integrità dei dati, se in un'area di Azure si verifica un'interruzione o quando l'applicazione richiede manutenzione.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funzionalità del database SQL che è possibile usare per assicurare la continuità aziendale

Dalla prospettiva del database, sono presenti quattro scenari principali di potenziali interruzioni:

- Errori hardware o software locali che interessano il nodo del database, ad esempio un errore del disco rigido.
- Il danneggiamento o l'eliminazione di dati causati in genere da un bug dell'applicazione o da un errore umano. Questi errori sono intrinsecamente specifici dell'applicazione e non possono essere rilevati in base a una regola o risolti automaticamente dall'infrastruttura.
- Interruzione del Data Center, probabilmente causata da una calamità naturale. Questo scenario richiede un certo livello di ridondanza geografica con il failover delle applicazioni in un Data Center alternativo.
- Errori di aggiornamento o manutenzione, problemi imprevisti che si verificano durante gli aggiornamenti o le operazioni di manutenzione pianificate di un'applicazione o un database possono richiedere il rollback rapido a uno stato precedente del database.

Il database SQL offre diverse funzionalità di continuità aziendale, inclusi i backup automatici e la replica facoltativa del database, in grado di mitigare tali scenari. In primo luogo, è necessario comprendere in che modo l'[architettura a disponibilità elevata](sql-database-high-availability.md) del database SQL fornisce disponibilità e resilienza al 99,99% per alcuni eventi di arresto improvviso che potrebbero influire sul processo di business.
Quindi, sono disponibili informazioni sui meccanismi aggiuntivi che è possibile usare per il ripristino da eventi di arresto improvviso che non possono essere gestiti dall'architettura a disponibilità elevata del database SQL, come ad esempio:

- [Le tabelle temporali](sql-database-temporal-tables.md) consentono di ripristinare le versioni delle righe da qualsiasi punto nel tempo.
- [I backup automatici incorporati](sql-database-automated-backups.md) e il [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore) consentono di ripristinare un database completo a un certo punto nel tempo negli ultimi 35 giorni.
- È possibile [ripristinare il database eliminato](sql-database-recovery-using-backups.md#deleted-database-restore) al punto in cui è stato eliminato se **il server logico non è stato eliminato**.
- [La conservazione backup a lungo termine](sql-database-long-term-retention.md) consente di conservare i backup fino a 10 anni.
- Il [gruppo di failover automatico](sql-database-geo-replication-overview.md#auto-failover-group-capabilities) consente all'applicazione di eseguire un ripristino automatico in caso di interruzione a livello di data center.

Ogni funzionalità presenta caratteristiche diverse in termini di tempo di recupero stimato (ERT) e di potenziale perdita di dati per le transazioni recenti. Dopo aver compreso le opzioni disponibili, è possibile scegliere una di esse o, nella maggior parte dei casi, usarle in modo combinato per i diversi scenari. Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo l'evento di arresto improvviso. Il tempo necessario per un ripristino completo dell'applicazione è noto come obiettivo del tempo di ripristino (RTO). È anche necessario conoscere la perdita massima di aggiornamenti di dati recenti (intervallo di tempo) che l'applicazione è in grado di tollerare durante il ripristino dopo l'evento di arresto improvviso. Il periodo di tempo degli aggiornamenti che è possibile perdere è noto come obiettivo del punto di ripristino (RPO).

La tabella seguente mette a confronto i valori ERT e RPO per ogni livello di servizio relativi ai tre scenari più comuni.

| Funzionalità | Basic | Standard | Premium | Utilizzo generico | Business Critical
| --- | --- | --- | --- |--- |--- |
| Ripristino temporizzato dal backup |Qualsiasi punto di ripristino entro sette giorni |Qualsiasi punto di ripristino entro 35 giorni |Qualsiasi punto di ripristino entro 35 giorni |Qualsiasi punto di ripristino entro il periodo configurato (fino a 35 giorni)|Qualsiasi punto di ripristino entro il periodo configurato (fino a 35 giorni)|
| Ripristino geografico dai backup con replica geografica |ERT < 12 ore<br> RPO < 1 ora |ERT < 12 ore<br>RPO < 1 ora |ERT < 12 ore<br>RPO < 1 ora |ERT < 12 ore<br>RPO < 1 ora|ERT < 12 ore<br>RPO < 1 ora|
| Gruppi di failover automatico |RTO = 1 ora<br>RPO < 5 secondi |RTO = 1 ora<br>RPO < 5 secondi |RTO = 1 ora<br>RPO < 5 secondi |RTO = 1 ora<br>RPO < 5 secondi|RTO = 1 ora<br>RPO < 5 secondi|

## <a name="recover-a-database-to-the-existing-server"></a>Recuperare un database nel server esistente

Il database SQL esegue automaticamente una combinazione di backup completi del database su base settimanale, backup differenziali del database (in genere ogni 12 ore) e backup dei log delle transazioni ogni 5-10 minuti per proteggere l'azienda dalla perdita di dati. I backup vengono archiviati in archiviazione RA-GRS per 35 giorni per tutti i livelli di servizio, ad eccezione dei livelli di servizio Basic DTU, in cui i backup sono archiviati per 7 giorni. Per altre informazioni, vedere [backup automatici del database SQL](sql-database-automated-backups.md). È possibile ripristinare i backup automatizzati di un modulo di database esistente a un punto specifico nel tempo come un nuovo database nello stesso server logico tramite il portale di Azure, PowerShell o l'API REST. Per altre informazioni vedere l'articolo relativo al [Ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore).

Se il periodo di conservazione massimo point-in-time restore supportato del ripristino temporizzato non è sufficiente per l'applicazione, è possibile estenderlo configurando i criteri di conservazione a lungo termine per il database. Per altre informazioni, vedere [Conservazione dei backup a lungo termine](sql-database-long-term-retention.md).

È possibile usare questi backup automatici del database per ripristinare un database da una serie di eventi di arresto improvviso, sia all'interno del proprio data center sia verso un altro data center. Il tempo di recupero di solito è inferiore a 12 ore. Potrebbe essere necessario più tempo per ripristinare un database attivo o di dimensioni molto estese. Il tempo stimato per il ripristino tramite backup automatici del database dipende da diversi fattori, tra cui il numero totale di database in fase di ripristino nella stessa area contemporaneamente, le dimensioni del database, le dimensioni del log delle transazioni e la larghezza di banda della rete. Per altre informazioni sui tempi di ripristino, vedere il [tempo di ripristino dei database](sql-database-recovery-using-backups.md#recovery-time). Durante il ripristino in un'altra area dati, la potenziale perdita di dati è limitata a 1 ora tramite l'uso dei backup con ridondanza geografica.

Usare i backup automatici e il [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore) come meccanismo di continuità aziendale e ripristino se l'applicazione:

- Non è considerata cruciale.
- Non ha un contratto di servizio vincolante: un tempo di inattività di 24 o più ore non comporta alcuna responsabilità finanziaria.
- Ha una bassa frequenza di modifica dei dati, ovvero poche transazioni per ora, e la perdita di un massimo di un'ora di modifica dei dati è accettabile.
- Dipende dal costo.

Se è necessario un ripristino più veloce, usare i [gruppi di failover](sql-database-geo-replication-overview.md#auto-failover-group-capabilities
) (vedere più avanti). Se è necessario essere in grado di ripristinare i dati da un periodo antecedente a 35 giorni, usare la [conservazione a lungo termine](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-region"></a>Recuperare un database in un'altra area

Anche se raramente, un data center di Azure può subire un'interruzione del servizio. Quando si verifica un'interruzione, viene generata un'interruzione delle attività che potrebbe durare solo pochi minuti oppure ore.

- Una delle opzioni è attendere che il database torni online al termine dell'interruzione del data center. Questa opzione funziona per le applicazioni che possono rimanere con il database offline. Ad esempio, un progetto di sviluppo o una versione di valutazione gratuita su cui non è necessario lavorare costantemente. Quando un data center registra un'interruzione del servizio, non si sa quanto tempo essa durerà. Pertanto, questa opzione funziona solo se è possibile rinunciare al database per un periodo di tempo.
- Un'altra opzione consiste nel ripristinare un database su qualunque server in qualsiasi area di Azure usando [i backup dei database con ridondanza geografica](sql-database-recovery-using-backups.md#geo-restore) (ripristino geografico). Il ripristino geografico usa un backup con ridondanza geografica come origine e può essere usato per ripristinare un database anche se il database o il data center è inaccessibile a causa di un'interruzione del servizio.
- Infine, è possibile eseguire rapidamente il ripristino dopo un'interruzione del servizio se è stato configurato un [gruppo di failover automatico](sql-database-geo-replication-overview.md#auto-failover-group-capabilities) per i database. È possibile personalizzare i criteri di failover per l'uso del failover automatico o manuale. Il failover richiede solo alcuni secondi, ma il servizio impiegherà almeno un'ora per attivare il processo. Questo passaggio è necessario per assicurarsi che il failover sia giustificato dalla portata dell'interruzione. Inoltre, il failover può comportare una lieve perdita di dati a causa della natura della replica asincrona. Per informazioni dettagliate su RTO e RPO del failover automatico, vedere la tabella riportata in precedenza in questo articolo.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>
> [!IMPORTANT]
> Per usare la replica geografica attiva e i gruppi di failover automatico, è necessario essere il proprietario della sottoscrizione o disporre delle autorizzazioni di amministrazione in SQL Server. È possibile configurare ed eseguire il failover tramite il portale di Azure, PowerShell o l'API REST usando le autorizzazioni della sottoscrizione o usando Transact-SQL con le autorizzazioni di SQL Server.

Usare i gruppi di failover automatico se l'applicazione soddisfa uno qualsiasi dei criteri seguenti:

- È considerata cruciale.
- Ha un contratto di servizio che non consente 12 ore o più di inattività.
- Il tempo di inattività può implicare responsabilità finanziaria.
- Ha un'elevata frequenza di modifica dei dati e una perdita di dati di un'ora non è accettabile.
- Il costo aggiuntivo della replica geografica attiva è inferiore rispetto alla potenziale responsabilità finanziaria e alla perdita di profitto associata.

Quando si esegue l'operazione, il tempo necessario per eseguire il ripristino e la quantità di dati persi dipendono dalle modalità in cui si decide di usare la funzionalità di continuità aziendale nell'applicazione. In effetti, è possibile scegliere di usare una combinazione di backup del database e replica geografica attiva a seconda dei requisiti dell'applicazione. Per una descrizione delle considerazioni sulla progettazione di applicazioni per database autonomi e pool elastici tramite queste funzioni di continuità aziendale, vedere [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [Strategie di ripristino di emergenza per applicazioni che usano il pool elastico del database SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

Le sezioni seguenti forniscono una panoramica della procedura per eseguire il ripristino tramite backup del database o replica geografica attiva. Per la procedura dettagliata, inclusa la pianificazione dei requisiti, i passaggi successivi al ripristino e informazioni su come simulare un'interruzione del servizio per eseguire un'analisi del ripristino di emergenza, vedere [Ripristinare un database SQL di Azure o eseguire il failover in un database secondario](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Prepararsi a un'interruzione del servizio

Indipendentemente dalla funzionalità di continuità aziendale in uso, è necessario:

- Identificare e preparare il server di destinazione, tra cui le regole del firewall a livello di server, gli account di accesso e le autorizzazioni a livello di database master.
- Stabilire come verranno reindirizzati i client e le applicazioni client verso il nuovo server
- Documentare altre dipendenze, ad esempio impostazioni di controllo e avvisi

Se non ci si prepara adeguatamente al ripristino, riportare online le applicazioni dopo un failover o un ripristino del database richiederà ulteriore tempo e probabilmente la risoluzione di problemi aggiuntivi in un momento di stress: una combinazione da evitare.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Failover a un database secondario con replica geografica

Se si usa la replica geografica attiva e i gruppi con failover automatico come meccanismo di ripristino, è possibile configurare i criteri di failover automatico o usare il [failover manuale](sql-database-disaster-recovery.md#fail-over-to-geo-replicated-secondary-server-in-the-failover-group). Una volta avviato, il failover fa sì che il database secondario venga alzato di livello come nuovo database primario e sia pronto per registrare nuove transazioni e rispondere a tutte le query, con una perdita di dati minima per i dati non ancora replicati. Per informazioni su come progettare il processo di failover, vedere [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Quando il data center ritorna in linea, i database primari precedenti si ricollegano automaticamente al nuovo database primario e diventano database secondari. Se si desidera spostare di nuovo il database primario nell'area originale è possibile avviare manualmente un failover pianificato (failback).

### <a name="perform-a-geo-restore"></a>Eseguire un ripristino geografico

Se si usano i backup automatici con l'archiviazione con ridondanza geografica (abilitata per impostazione predefinita), è possibile ripristinare il database tramite il [ripristino geografico](sql-database-disaster-recovery.md#recover-using-geo-restore). In genere il ripristino avviene entro 12 ore, con una perdita di dati fino a 1 ora, a seconda dell'ultima acquisizione e replica del backup del log. Fino a quando non viene completato il ripristino, il database non è in grado di registrare tutte le transazioni o rispondere a tutte le query. Si noti che il ripristino geografico consente solo di ripristinare il database al punto di ripristino più recente disponibile.

> [!NOTE]
> Se il data center ritorna online prima che l'applicazione venga spostata sul database ripristinato, è possibile annullare il ripristino.

### <a name="perform-post-failover--recovery-tasks"></a>Eseguire attività successive al filover/ripristino

Dopo il ripristino da un meccanismo di ripristino, è necessario eseguire le seguenti attività aggiuntive prima che utenti e applicazioni siano nuovamente attivi e in esecuzione:

- Reindirizzare i client e le applicazioni client verso il nuovo server e il database ripristinato
- Verificare che siano disponibili le regole del firewall a livello di server appropriate per consentire agli utenti di connettersi o usare i [firewall a livello di database](sql-database-firewall-configure.md#creating-and-managing-firewall-rules)
- Verificare che siano presenti gli account di accesso appropriato e le autorizzazioni a livello di database master o usare gli [utenti contenuti](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)
- Configurare il controllo in base alle proprie esigenze.
- Configurare gli avvisi in base alle proprie esigenze.

> [!NOTE]
> Se si usa un gruppo di failover e ci si connette ai database mediante il listener di lettura/scrittura, il reindirizzamento dopo il failover verrà eseguito automaticamente e in modo trasparente per l'applicazione.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Aggiornare un'applicazione con tempo di inattività minimo

A volte un'applicazione deve essere portata offline per ragioni di manutenzione pianificata, ad esempio un aggiornamento dell'applicazione. [Gestione degli aggiornamenti dell'applicazione](sql-database-manage-application-rolling-upgrade.md) descrive come usare la replica geografica attiva per consentire gli aggiornamenti in sequenza dell'applicazione cloud al fine di ridurre al minimo i tempi di inattività durante gli aggiornamenti e fornire un percorso di recupero se si verificano problemi.

## <a name="next-steps"></a>Passaggi successivi

Per una descrizione delle considerazioni sulla progettazione di applicazioni per database autonomi e pool elastici, vedere [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [Strategie di ripristino di emergenza per applicazioni che usano il pool elastico del database SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

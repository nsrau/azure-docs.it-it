---
title: Continuità aziendale cloud - Ripristino del database - Database SQL | Documentazione Microsoft
description: Informazioni su come il database SQL di Azure supporta la continuità aziendale cloud e il ripristino del database e consente di mantenere le applicazioni cloud cruciali in esecuzione.
keywords: continuità aziendale, continuità aziendale cloud, ripristino di emergenza del database, ripristino del database
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 06/25/2019
ms.openlocfilehash: 2f3723e0a1b14edd6f516f3cc080501bea80d486
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442046"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Panoramica della continuità aziendale del database SQL di Azure

**La continuità aziendale** nel database SQL di Azure fa riferimento a meccanismi, criteri e procedure che consentono all'azienda di continuare a funzionare in caso di interruzione, in particolare per quanto riguarda la propria infrastruttura di calcolo. Nella maggior parte dei casi, il database SQL di Azure gestirà gli eventi di arresto improvviso che potrebbero verificarsi nell'ambiente cloud e manterrà le applicazioni e i processi aziendali in esecuzione. Tuttavia, esistono alcuni eventi di arresto improvviso che non possono essere gestiti automaticamente, ad esempio dal Database SQL:

- Un utente elimina o aggiorna accidentalmente una riga in una tabella.
- Un utente malintenzionato riesce a eliminare dati o database.
- Un terremoto causa un'interruzione dell'alimentazione e una disabilitazione temporanea del data center.

Questa panoramica descrive le funzionalità offerte dal database SQL di Azure per la continuità aziendale e il ripristino di emergenza. Informazioni su opzioni, raccomandazioni ed esercitazioni per il ripristino da eventi di arresto improvviso che potrebbero provocare la perdita di dati o la disattivazione del database e dell'applicazione. Informazioni sulle operazioni da eseguire quando si verifica un errore generato da un utente o da un'applicazione che influisce sull'integrità dei dati, se in un'area di Azure si verifica un'interruzione o quando l'applicazione richiede manutenzione.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funzionalità del database SQL che è possibile usare per assicurare la continuità aziendale

Dalla prospettiva del database, sono presenti quattro scenari principali di potenziali interruzioni:

- Errori hardware o software locali che interessano il nodo del database, ad esempio un errore del disco rigido.
- Il danneggiamento o l'eliminazione di dati causati in genere da un bug dell'applicazione o da un errore umano. Questi errori sono specifici dell'applicazione e in genere non possono essere rilevati dal servizio di database.
- Interruzione del Data Center, probabilmente causata da una calamità naturale. Questo scenario richiede un certo livello di ridondanza geografica con il failover delle applicazioni in un Data Center alternativo.
- Errori di aggiornamento o la manutenzione, problemi imprevisti che si verificano durante la manutenzione dell'infrastruttura pianificata o gli aggiornamenti possono richiedere il rollback rapido a uno stato precedente del database.

Per attenuare l'hardware locale e gli errori di software, il Database SQL include un [architettura a disponibilità elevata](sql-database-high-availability.md), che garantisce il recupero automatico da questi errori con un massimo di % 99,995 disponibilità contratto di servizio.  

Per proteggere l'azienda dalla perdita di dati, Database SQL crea automaticamente backup completi del database backup settimanale, backup differenziali del database ogni 12 ore e transazione i backup del log ogni 5 - 10 minuti. I backup vengono archiviati in archiviazione con ridondanza geografica e accesso in lettura per almeno 7 giorni per tutti i livelli di servizio. Tutti i livelli di servizio, ad eccezione di Basic supportano il periodo di conservazione dei backup configurabile per il ripristino temporizzato in, fino a 35 giorni. 

Database SQL offre anche diverse funzionalità di continuità aziendale, che è possibile usare per ridurre i vari scenari non pianificati. 

- [Le tabelle temporali](sql-database-temporal-tables.md) consentono di ripristinare le versioni delle righe da qualsiasi punto nel tempo.
- [Backup automatici incorporato](sql-database-automated-backups.md) e [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore) consente il ripristino completo del database a un certo punto nel tempo entro il periodo di conservazione configurato fino a 35 giorni.
- È possibile [ripristinare il database eliminato](sql-database-recovery-using-backups.md#deleted-database-restore) al punto in cui è stato eliminato se **il server di database SQL non è stato eliminato**.
- [La conservazione backup a lungo termine](sql-database-long-term-retention.md) consente di conservare i backup fino a 10 anni.
- La [replica geografica attiva](sql-database-active-geo-replication.md) consente di creare repliche leggibili e di eseguire il failover manuale a qualsiasi replica in caso di interruzione del data center o di aggiornamento dell'applicazione.
- Il [gruppo di failover automatico](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) consente all'applicazione di eseguire un ripristino automatico in caso di interruzione del data center.

## <a name="recover-a-database-within-the-same-azure-region"></a>Ripristinare un database nella stessa area di Azure

È possibile usare i backup automatici del database per ripristinare un database a un punto nel tempo nel passato. In questo modo è possibile ripristinare da danneggiamenti di dati dovuti a errori umani. Il punto nel tempo ripristino consente di creare un nuovo database nello stesso server che rappresenta lo stato dei dati prima dell'evento danneggiare. Per la maggior parte dei database, le operazioni di ripristino richiede meno di 12 ore. Potrebbe richiedere più tempo per ripristinare un database molto grande o molto attivo. Per altre informazioni sui tempi di ripristino, vedere il [tempo di ripristino dei database](sql-database-recovery-using-backups.md#recovery-time). 

Se il periodo di conservazione massimo supportato di point-in-time restore (PITR) non è sufficiente per l'applicazione, è possibile estenderlo configurando Criteri di conservazione a lungo termine per i database. Per altre informazioni, vedere [Conservazione dei backup a lungo termine](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-azure-region"></a>Ripristinare un database in un'altra area di Azure

Anche se raramente, un data center di Azure può subire un'interruzione del servizio. Quando si verifica un'interruzione, viene generata un'interruzione delle attività che potrebbe durare solo pochi minuti oppure ore.

- Una delle opzioni è attendere che il database torni online al termine dell'interruzione del data center. Questa opzione funziona per le applicazioni che possono rimanere con il database offline. Ad esempio, un progetto di sviluppo o una versione di valutazione gratuita su cui non è necessario lavorare costantemente. Quando un data center registra un'interruzione del servizio, non si sa quanto tempo essa durerà. Pertanto, questa opzione funziona solo se è possibile rinunciare al database per un periodo di tempo.
- Un'altra opzione consiste nel ripristinare un database su qualunque server in qualsiasi area di Azure usando [i backup dei database con ridondanza geografica](sql-database-recovery-using-backups.md#geo-restore) (ripristino geografico). Il ripristino geografico usa un backup con ridondanza geografica come origine e può essere usato per ripristinare un database anche se il database o il data center è inaccessibile a causa di un'interruzione del servizio.
- Infine, è possibile recuperare rapidamente un'interruzione del servizio se è stata configurata tramite replica geografica secondaria [replica geografica attiva](sql-database-active-geo-replication.md) o un' [gruppo di failover automatico](sql-database-auto-failover-group.md) per il database o database. A seconda della tecnologia scelta, è possibile usare il failover manuale o automatico. Il failover richiede solo alcuni secondi, ma il servizio impiegherà almeno un'ora per attivare il processo. Questo passaggio è necessario per assicurarsi che il failover sia giustificato dalla portata dell'interruzione. Inoltre, il failover può comportare una lieve perdita di dati a causa della natura della replica asincrona. 

Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo l'evento di arresto improvviso. Il tempo necessario per un ripristino completo dell'applicazione è noto come obiettivo tempo di ripristino (RTO). È anche necessario conoscere la durata massima di dati aggiornamenti recenti (intervallo di tempo) l'applicazione può tollerare durante il ripristino da un evento di arresto improvviso non pianificato. La potenziale perdita di dati è noto come obiettivo del punto di ripristino (RPO).

Vari metodi di ripristino offrono livelli diversi di RPO e RTO. È possibile scegliere un metodo di recupero specifico, o usare una combinazione di metodi per il ripristino dell'applicazione completo achicethe. La tabella seguente confronta RPO e RTO di ogni opzione di ripristino.

| Metodo di ripristino | RTO | RPO |
| --- | --- | --- | 
| Ripristino geografico dai backup con replica geografica | 12 h | 1 ora |
| Gruppi di failover automatico | 1 ora | 5 s |
| Failover manuale del database | 30 s | 5 s |

> [!NOTE]
> *Failover manuale del database* fa riferimento per eseguire il failover di un singolo database per la replica geografica secondaria usando la [modalità non pianificata](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).
Per informazioni dettagliate su RTO e RPO del failover automatico, vedere la tabella riportata in precedenza in questo articolo.


Usare i gruppi di failover automatico se l'applicazione soddisfa uno qualsiasi dei criteri seguenti:

- È considerata cruciale.
- Ha un contratto di servizio che non consente 12 ore o più di inattività.
- Il tempo di inattività può implicare responsabilità finanziaria.
- Ha un'elevata frequenza di modifica dei dati e una perdita di dati di un'ora non è accettabile.
- Il costo aggiuntivo della replica geografica attiva è inferiore rispetto alla potenziale responsabilità finanziaria e alla perdita di profitto associata.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

È possibile scegliere di utilizzare una combinazione di backup del database e replica geografica attiva a seconda dei requisiti dell'applicazione. Per una descrizione delle considerazioni sulla progettazione per i database autonomi e pool elastici tramite queste funzionalità di continuità aziendale, vedere [progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [emergenza pool elastico strategie di ripristino](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

Le sezioni seguenti forniscono una panoramica della procedura per eseguire il ripristino tramite backup del database o replica geografica attiva. Per la procedura dettagliata, inclusa la pianificazione dei requisiti, i passaggi successivi al ripristino e informazioni su come simulare un'interruzione del servizio per eseguire un'analisi del ripristino di emergenza, vedere [Ripristinare un database SQL di Azure o eseguire il failover in un database secondario](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Prepararsi a un'interruzione del servizio

Indipendentemente dalla funzionalità di continuità aziendale in uso, è necessario:

- Identificare e preparare il server di destinazione, tra cui le regole del firewall IP a livello di server, gli account di accesso e le autorizzazioni a livello di database master.
- Stabilire come verranno reindirizzati i client e le applicazioni client verso il nuovo server
- Documentare altre dipendenze, ad esempio impostazioni di controllo e avvisi

Se non ci si prepara adeguatamente al ripristino, riportare online le applicazioni dopo un failover o un ripristino del database richiederà ulteriore tempo e probabilmente la risoluzione di problemi aggiuntivi in un momento di stress: una combinazione da evitare.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Failover a un database secondario con replica geografica

Se si usa la replica geografica attiva o i gruppi di failover automatico come meccanismo di ripristino, è possibile configurare i criteri di failover automatico o usare [failover manuale pianificato](sql-database-active-geo-replication-portal.md#initiate-a-failover). Una volta avviato, il failover fa sì che il database secondario venga alzato di livello come nuovo database primario e sia pronto per registrare nuove transazioni e rispondere a tutte le query, con una perdita di dati minima per i dati non ancora replicati. Per informazioni su come progettare il processo di failover, vedere [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Quando il data center ritorna in linea, i database primari precedenti si ricollegano automaticamente al nuovo database primario e diventano database secondari. Se si desidera spostare di nuovo il database primario nell'area originale è possibile avviare manualmente un failover pianificato (failback).

### <a name="perform-a-geo-restore"></a>Eseguire un ripristino geografico

Se si usano i backup automatici con l'archiviazione con ridondanza geografica (abilitata per impostazione predefinita), è possibile ripristinare il database tramite il [ripristino geografico](sql-database-disaster-recovery.md#recover-using-geo-restore). In genere il ripristino avviene entro 12 ore, con una perdita di dati fino a 1 ora, a seconda dell'ultima acquisizione e replica del backup del log. Fino a quando non viene completato il ripristino, il database non è in grado di registrare tutte le transazioni o rispondere a tutte le query. Si noti che il ripristino geografico consente solo di ripristinare il database al punto di ripristino più recente disponibile.

> [!NOTE]
> Se il data center ritorna online prima che l'applicazione venga spostata sul database ripristinato, è possibile annullare il ripristino.

### <a name="perform-post-failover--recovery-tasks"></a>Eseguire attività successive al filover/ripristino

Dopo il ripristino da un meccanismo di ripristino, è necessario eseguire le seguenti attività aggiuntive prima che utenti e applicazioni siano nuovamente attivi e in esecuzione:

- Reindirizzare i client e le applicazioni client verso il nuovo server e il database ripristinato
- Verificare che siano disponibili regole del firewall IP a livello di server appropriate per consentire agli utenti di connettersi o usare i [firewall a livello di database](sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-the-azure-portal) per abilitare regole appropriate.
- Verificare che siano presenti gli account di accesso appropriato e le autorizzazioni a livello di database master o usare gli [utenti contenuti](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)
- Configurare il controllo in base alle proprie esigenze.
- Configurare gli avvisi in base alle proprie esigenze.

> [!NOTE]
> Se si usa un gruppo di failover e ci si connette ai database mediante il listener di lettura/scrittura, il reindirizzamento dopo il failover verrà eseguito automaticamente e in modo trasparente per l'applicazione.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Aggiornare un'applicazione con tempo di inattività minimo

A volte un'applicazione deve essere portata offline per ragioni di manutenzione pianificata, ad esempio un aggiornamento dell'applicazione. [Gestione degli aggiornamenti dell'applicazione](sql-database-manage-application-rolling-upgrade.md) descrive come usare la replica geografica attiva per consentire gli aggiornamenti in sequenza dell'applicazione cloud al fine di ridurre al minimo i tempi di inattività durante gli aggiornamenti e fornire un percorso di recupero se si verificano problemi.

## <a name="next-steps"></a>Passaggi successivi

Per una descrizione delle considerazioni sulla progettazione di applicazioni per database autonomi e pool elastici, vedere [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [Strategie di ripristino di emergenza per applicazioni che usano il pool elastico del database SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

---
title: Continuità aziendale cloud-ripristino del database-database SQL
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
ms.date: 06/25/2019
ms.openlocfilehash: c4e82b0311e010eb8a4b8f37f3ff817d9a5400ad
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691037"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Panoramica della continuità aziendale del database SQL di Azure

**La continuità aziendale** nel database SQL di Azure fa riferimento a meccanismi, criteri e procedure che consentono all'azienda di continuare a funzionare in caso di interruzione, in particolare per quanto riguarda la propria infrastruttura di calcolo. Nella maggior parte dei casi, il database SQL di Azure gestirà gli eventi di arresto improvviso che potrebbero verificarsi nell'ambiente cloud e manterrà le applicazioni e i processi aziendali in esecuzione. Tuttavia, esistono alcuni eventi di disturbo che non possono essere gestiti dal database SQL automaticamente, ad esempio:

- Un utente elimina o aggiorna accidentalmente una riga in una tabella.
- Un utente malintenzionato riesce a eliminare dati o database.
- Un terremoto causa un'interruzione dell'alimentazione e una disabilitazione temporanea del data center.

Questa panoramica descrive le funzionalità offerte dal database SQL di Azure per la continuità aziendale e il ripristino di emergenza. Informazioni su opzioni, raccomandazioni ed esercitazioni per il ripristino da eventi di arresto improvviso che potrebbero provocare la perdita di dati o la disattivazione del database e dell'applicazione. Informazioni sulle operazioni da eseguire quando si verifica un errore generato da un utente o da un'applicazione che influisce sull'integrità dei dati, se in un'area di Azure si verifica un'interruzione o quando l'applicazione richiede manutenzione.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funzionalità del database SQL che è possibile usare per assicurare la continuità aziendale

Dalla prospettiva del database, sono presenti quattro scenari principali di potenziali interruzioni:

- Errori hardware o software locali che interessano il nodo del database, ad esempio un errore del disco rigido.
- Il danneggiamento o l'eliminazione di dati causati in genere da un bug dell'applicazione o da un errore umano. Tali errori sono specifici dell'applicazione e in genere non possono essere rilevati dal servizio di database.
- Interruzione del Data Center, probabilmente causata da una calamità naturale. Questo scenario richiede un certo livello di ridondanza geografica con il failover delle applicazioni in un Data Center alternativo.
- Errori di aggiornamento o manutenzione, problemi imprevisti che si verificano durante la manutenzione o gli aggiornamenti dell'infrastruttura pianificata possono richiedere un rollback rapido a uno stato precedente del database.

Per attenuare gli errori hardware e software locali, il database SQL include un' [architettura a disponibilità elevata](sql-database-high-availability.md)che garantisce il ripristino automatico da questi errori con un contratto di contratto di disponibilità fino al 99,995%.  

Per proteggere l'azienda dalla perdita di dati, il database SQL crea automaticamente backup completi del database ogni settimana, backup differenziali del database ogni 12 ore e backup del log delle transazioni ogni 5-10 minuti. I backup vengono archiviati nell'archiviazione RA-GRS per almeno 7 giorni per tutti i livelli di servizio. Tutti i livelli di servizio ad eccezione supporto Basic periodo di conservazione dei backup configurabile per il ripristino temporizzato, fino a 35 giorni. 

Il database SQL offre inoltre diverse funzionalità di continuità aziendale, che è possibile utilizzare per attenuare diversi scenari non pianificati. 

- [Le tabelle temporali](sql-database-temporal-tables.md) consentono di ripristinare le versioni delle righe da qualsiasi punto nel tempo.
- I [backup automatici predefiniti](sql-database-automated-backups.md) e il [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore) consentono di ripristinare il database completo fino a un determinato punto nel tempo entro il periodo di conservazione configurato fino a 35 giorni.
- È possibile [ripristinare il database eliminato](sql-database-recovery-using-backups.md#deleted-database-restore) al punto in cui è stato eliminato se **il server di database SQL non è stato eliminato**.
- [La conservazione backup a lungo termine](sql-database-long-term-retention.md) consente di conservare i backup fino a 10 anni.
- La [replica geografica attiva](sql-database-active-geo-replication.md) consente di creare repliche leggibili e di eseguire il failover manuale a qualsiasi replica in caso di interruzione del data center o di aggiornamento dell'applicazione.
- Il [gruppo di failover automatico](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) consente all'applicazione di eseguire un ripristino automatico in caso di interruzione del data center.

## <a name="recover-a-database-within-the-same-azure-region"></a>Ripristinare un database nella stessa area di Azure

È possibile utilizzare i backup automatici del database per ripristinare un database a un punto nel tempo precedente. In questo modo è possibile eseguire il ripristino dai danneggiamenti dei dati causati da errori umani. Il ripristino temporizzato consente di creare un nuovo database nello stesso server che rappresenta lo stato dei dati prima dell'evento danneggiato. Per la maggior parte dei database, le operazioni di ripristino richiedono meno di 12 ore. Il ripristino di un database molto grande o molto attivo potrebbe richiedere più tempo. Per altre informazioni sui tempi di ripristino, vedere il [tempo di ripristino dei database](sql-database-recovery-using-backups.md#recovery-time). 

Se il periodo di conservazione dei backup massimo supportato per il ripristino temporizzato (ripristino temporizzato) non è sufficiente per l'applicazione, è possibile estenderlo configurando un criterio di conservazione a lungo termine per i database. Per altre informazioni, vedere [Conservazione dei backup a lungo termine](sql-database-long-term-retention.md).

## <a name="compare-geo-replication-with-failover-groups"></a>Confrontare la replica geografica con i gruppi di failover

I [gruppi di failover automatico](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) semplificano la distribuzione e l'uso della [replica geografica](sql-database-active-geo-replication.md) e aggiungono le funzionalità aggiuntive, come descritto nella tabella seguente:

|                                              | Replica geografica | Gruppi di failover  |
|:---------------------------------------------| :-------------- | :----------------|
| Failover automatico                           |     No          |      Sì         |
| Eseguire contemporaneamente il failover di più database  |     No          |      Sì         |
| Aggiornare la stringa di connessione dopo il failover      |     Sì         |      No          |
| Istanza gestita supportata                   |     No          |      Sì         |
| Può trovarsi nella stessa area del database primario             |     Sì         |      No          |
| Più repliche                            |     Sì         |      No          |
| Supporta la scalabilità in lettura                          |     Sì         |      Sì         |
| &nbsp; | &nbsp; | &nbsp; |


## <a name="recover-a-database-to-the-existing-server"></a>Recuperare un database nel server esistente

Anche se raramente, un data center di Azure può subire un'interruzione del servizio. Quando si verifica un'interruzione, viene generata un'interruzione delle attività che potrebbe durare solo pochi minuti oppure ore.

- Una delle opzioni è attendere che il database torni online al termine dell'interruzione del data center. Questa opzione funziona per le applicazioni che possono rimanere con il database offline. Ad esempio, un progetto di sviluppo o una versione di valutazione gratuita su cui non è necessario lavorare costantemente. Quando un data center registra un'interruzione del servizio, non si sa quanto tempo essa durerà. Pertanto, questa opzione funziona solo se è possibile rinunciare al database per un periodo di tempo.
- Un'altra opzione consiste nel ripristinare un database su qualunque server in qualsiasi area di Azure usando [i backup dei database con ridondanza geografica](sql-database-recovery-using-backups.md#geo-restore) (ripristino geografico). Il ripristino geografico usa un backup con ridondanza geografica come origine e può essere usato per ripristinare un database anche se il database o il data center è inaccessibile a causa di un'interruzione del servizio.
- Infine, è possibile eseguire rapidamente il ripristino a seguito di un'interruzione se è stata configurata la replica geografica secondaria usando la [replica geografica attiva](sql-database-active-geo-replication.md) o un [gruppo di failover automatico](sql-database-auto-failover-group.md) per il database o i database. A seconda della tecnologia scelta, è possibile usare il failover manuale o automatico. Il failover richiede solo alcuni secondi, ma il servizio impiegherà almeno un'ora per attivare il processo. Questo passaggio è necessario per assicurarsi che il failover sia giustificato dalla portata dell'interruzione. Inoltre, il failover può comportare una lieve perdita di dati a causa della natura della replica asincrona. 

Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo l'evento di arresto improvviso. Il tempo necessario per l'applicazione per il ripristino completo è noto come obiettivo del tempo di ripristino (RTO). È anche necessario comprendere il periodo massimo di aggiornamenti dei dati recenti (intervallo di tempo) che l'applicazione può tollerare durante il ripristino da un evento di disturbo non pianificato. La perdita di dati potenziale è nota come obiettivo del punto di ripristino (RPO).

Metodi di ripristino diversi offrono livelli diversi di RPO e RTO. È possibile scegliere un metodo di ripristino specifico oppure utilizzare una combinazione di metodi per ottenere il ripristino completo dell'applicazione. Nella tabella seguente vengono confrontati RPO e RTO di ogni opzione di ripristino. I gruppi di failover automatico semplificano la distribuzione e l'uso della replica geografica e aggiungono le funzionalità aggiuntive, come descritto nella tabella seguente.

| Metodo Recovery | RTO | RPO |
| --- | --- | --- | 
| Ripristino geografico dai backup con replica geografica | 12 ore | 1 h |
| Gruppi di failover automatico | 1 h | 5 s |
| Failover manuale del database | 30 s | 5 s |

> [!NOTE]
> Il *failover manuale del database* si riferisce al failover di un singolo database con la replica geografica secondaria usando la [modalità non pianificata](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).
Per informazioni dettagliate su RTO e RPO del failover automatico, vedere la tabella riportata in precedenza in questo articolo.


Usare i gruppi di failover automatico se l'applicazione soddisfa uno qualsiasi dei criteri seguenti:

- È considerata cruciale.
- Ha un contratto di servizio che non consente 12 ore o più di inattività.
- Il tempo di inattività può implicare responsabilità finanziaria.
- Ha un'elevata frequenza di modifica dei dati e una perdita di dati di un'ora non è accettabile.
- Il costo aggiuntivo della replica geografica attiva è inferiore rispetto alla potenziale responsabilità finanziaria e alla perdita di profitto associata.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

È possibile scegliere di usare una combinazione di backup di database e la replica geografica attiva a seconda dei requisiti dell'applicazione. Per informazioni sulle considerazioni di progettazione per i database autonomi e per i pool elastici che usano queste funzionalità di continuità aziendale, vedere [progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [strategie di ripristino di emergenza del pool elastico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

Le sezioni seguenti forniscono una panoramica della procedura per eseguire il ripristino tramite backup del database o replica geografica attiva. Per la procedura dettagliata, inclusa la pianificazione dei requisiti, i passaggi successivi al ripristino e informazioni su come simulare un'interruzione del servizio per eseguire un'analisi del ripristino di emergenza, vedere [Ripristinare un database SQL di Azure o eseguire il failover in un database secondario](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Prepararsi a un'interruzione del servizio

Indipendentemente dalla funzionalità di continuità aziendale in uso, è necessario:

- Identificare e preparare il server di destinazione, tra cui le regole del firewall IP a livello di server, gli account di accesso e le autorizzazioni a livello di database master.
- Stabilire come verranno reindirizzati i client e le applicazioni client verso il nuovo server
- Documentare altre dipendenze, ad esempio impostazioni di controllo e avvisi

Se non ci si prepara adeguatamente al ripristino, riportare online le applicazioni dopo un failover o un ripristino del database richiederà ulteriore tempo e probabilmente la risoluzione di problemi aggiuntivi in un momento di stress: una combinazione da evitare.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Failover a un database secondario con replica geografica

Se si usa la replica geografica attiva o i gruppi di failover automatico come meccanismo di ripristino, è possibile configurare un criterio di failover automatico o usare il [failover manuale non pianificato](sql-database-active-geo-replication-portal.md#initiate-a-failover). Una volta avviato, il failover fa sì che il database secondario venga alzato di livello come nuovo database primario e sia pronto per registrare nuove transazioni e rispondere a tutte le query, con una perdita di dati minima per i dati non ancora replicati. Per informazioni su come progettare il processo di failover, vedere [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Quando il data center ritorna in linea, i database primari precedenti si ricollegano automaticamente al nuovo database primario e diventano database secondari. Se si desidera spostare di nuovo il database primario nell'area originale è possibile avviare manualmente un failover pianificato (failback).

### <a name="perform-a-geo-restore"></a>Eseguire un ripristino geografico

Se si usano i backup automatici con l'archiviazione con ridondanza geografica (abilitata per impostazione predefinita), è possibile ripristinare il database tramite il [ripristino geografico](sql-database-disaster-recovery.md#recover-using-geo-restore). In genere il ripristino avviene entro 12 ore, con una perdita di dati fino a 1 ora, a seconda dell'ultima acquisizione e replica del backup del log. Fino a quando non viene completato il ripristino, il database non è in grado di registrare tutte le transazioni o rispondere a tutte le query. Si noti che il ripristino geografico consente solo di ripristinare il database al punto di ripristino più recente disponibile.

> [!NOTE]
> Se il data center ritorna online prima che l'applicazione venga spostata sul database ripristinato, è possibile annullare il ripristino.

### <a name="perform-post-failover--recovery-tasks"></a>Eseguire attività successive al filover/ripristino

Dopo il ripristino da un meccanismo di ripristino, è necessario eseguire le seguenti attività aggiuntive prima che utenti e applicazioni siano nuovamente attivi e in esecuzione:

- Reindirizzare i client e le applicazioni client verso il nuovo server e il database ripristinato
- Verificare che siano disponibili regole del firewall IP a livello di server appropriate per consentire agli utenti di connettersi o usare i [firewall a livello di database](sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) per abilitare regole appropriate.
- Verificare che siano presenti gli account di accesso appropriato e le autorizzazioni a livello di database master o usare gli [utenti contenuti](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)
- Configurare il controllo in base alle proprie esigenze.
- Configurare gli avvisi in base alle proprie esigenze.

> [!NOTE]
> Se si usa un gruppo di failover e ci si connette ai database mediante il listener di lettura/scrittura, il reindirizzamento dopo il failover verrà eseguito automaticamente e in modo trasparente per l'applicazione.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Aggiornare un'applicazione con tempo di inattività minimo

A volte un'applicazione deve essere portata offline per ragioni di manutenzione pianificata, ad esempio un aggiornamento dell'applicazione. [Gestione degli aggiornamenti dell'applicazione](sql-database-manage-application-rolling-upgrade.md) descrive come usare la replica geografica attiva per consentire gli aggiornamenti in sequenza dell'applicazione cloud al fine di ridurre al minimo i tempi di inattività durante gli aggiornamenti e fornire un percorso di recupero se si verificano problemi.

## <a name="next-steps"></a>Passaggi successivi

Per una descrizione delle considerazioni sulla progettazione di applicazioni per database autonomi e pool elastici, vedere [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [Strategie di ripristino di emergenza per applicazioni che usano il pool elastico del database SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

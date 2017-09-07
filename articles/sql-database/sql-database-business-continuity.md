---
title: "Continuità aziendale cloud - Ripristino del database - Database SQL | Documentazione Microsoft"
description: "Informazioni su come il database SQL di Azure supporta la continuità aziendale cloud e il ripristino del database e consente di mantenere le applicazioni cloud cruciali in esecuzione."
keywords: "continuità aziendale, continuità aziendale cloud, ripristino di emergenza del database, ripristino del database"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 18e5d3f1-bfe5-4089-b6fd-76988ab29822
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/25/2017
ms.author: sashan
ms.translationtype: HT
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: 4963598837b71e812ad3750aad9d20c8460111d9
ms.contentlocale: it-it
ms.lasthandoff: 08/30/2017

---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Panoramica della continuità aziendale del database SQL di Azure

Questa panoramica descrive le funzionalità offerte dal database SQL di Azure per la continuità aziendale e il ripristino di emergenza. Informazioni su opzioni, raccomandazioni ed esercitazioni per il ripristino da eventi di arresto improvviso che potrebbero provocare la perdita di dati o la disattivazione del database e dell'applicazione. Informazioni sulle operazioni da eseguire quando si verifica un errore generato da un utente o da un'applicazione che influisce sull'integrità dei dati, se in un'area di Azure si verifica un'interruzione o quando l'applicazione richiede manutenzione.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funzionalità del database SQL che è possibile usare per assicurare la continuità aziendale

Il database SQL offre diverse funzionalità di continuità aziendale, inclusi i backup automatici e la replica facoltativa del database. Ogni funzionalità presenta caratteristiche diverse in termini di tempo di recupero stimato (ERT) e di potenziale perdita di dati per le transazioni recenti. Dopo aver compreso le opzioni disponibili, è possibile scegliere una di esse o, nella maggior parte dei casi, usarle in modo combinato per i diversi scenari. Quando si sviluppa il piano di continuità aziendale, è necessario comprendere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo l'evento di arresto improvviso. Si tratta dell'obiettivo del tempo di ripristino (RTO). È anche necessario conoscere la perdita massima di aggiornamenti di dati recenti (intervallo di tempo) che l'applicazione è in grado di tollerare durante il ripristino dopo l'evento di arresto improvviso, ovvero l'obiettivo del punto di recupero (RPO).

La tabella seguente mette a confronto i valori ERT e RPO per i tre scenari più comuni.

| Funzionalità | Livello Basic | Livello Standard | Livello Premium |
| --- | --- | --- | --- |
| Ripristino temporizzato dal backup |Qualsiasi punto di ripristino entro 7 giorni |Qualsiasi punto di ripristino entro 35 giorni |Qualsiasi punto di ripristino entro 35 giorni |
| Ripristino geografico dai backup con replica geografica |ERT < 12 ore, RPO < 1 ora |ERT < 12 ore, RPO < 1 ora |ERT < 12 ore, RPO < 1 ora |
| Ripristino dall'insieme di credenziali di Backup di Azure |ERT < 12 ore, RPO < 1 sett |ERT < 12 ore, RPO < 1 sett |ERT < 12 ore, RPO < 1 sett |
| Replica geografica attiva |ERT < 30 sec, RPO < 5 sec |ERT < 30 sec, RPO < 5 sec |ERT < 30 sec, RPO < 5 sec |

### <a name="use-database-backups-to-recover-a-database"></a>Usare backup del database per il ripristinare

Il database SQL esegue automaticamente una combinazione di backup completi su base settimanale, backup differenziali del database di backup ogni ora e backup dei log delle transazioni ogni 5-10 minuti per proteggere l'azienda dalla perdita di dati. Questi backup vengono archiviati nel servizio di archiviazione con ridondanza geografica per 35 giorni per i database dei livelli di servizio Standard e Premium e per 7 giorni per il database del livello Basic. Per altre informazioni, vedere [livelli di servizio](sql-database-service-tiers.md). Se il periodo di memorizzazione per il livello di servizio non soddisfa i requisiti aziendali, è possibile aumentare il periodo di memorizzazione [modificando il livello di servizio](sql-database-service-tiers.md). I backup completi e differenziali del database vengono replicati anche su un [data center abbinato](../best-practices-availability-paired-regions.md) per la protezione da un'interruzione del data center. Per altre informazioni, vedere [backup automatici del database SQL](sql-database-automated-backups.md).

Se il periodo di memorizzazione predefinito non è sufficiente per l'applicazione, è possibile estenderlo configurando criteri di conservazione a lungo termine per il database. Per altre informazioni, vedere [Long-term retention](sql-database-long-term-retention.md) (Conservazione a lungo termine).

È possibile usare questi backup automatici del database per ripristinare un database da una serie di eventi di arresto improvviso, sia all'interno del proprio data center sia verso un altro data center. Il tempo stimato per il ripristino tramite backup automatici del database dipende da diversi fattori, tra cui il numero totale di database in fase di ripristino nella stessa area contemporaneamente, le dimensioni del database, le dimensioni del log delle transazioni e la larghezza di banda della rete. Il tempo di recupero di solito è inferiore a 12 ore. Durante il ripristino verso un'altra area dati, la potenziale perdita di dati è limitata a 1 ora per l'archiviazione con ridondanza geografica dei backup differenziali del database che si verificano ogni ora.

> [!IMPORTANT]
> Per eseguire il ripristino tramite i backup automatici, è necessario essere un membro del ruolo di collaboratore di SQL Server o proprietario della sottoscrizione. Vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md). Per il ripristino, è possibile usare il portale di Azure, PowerShell o l'API REST. Non è possibile usare Transact-SQL.
>

Usare i backup automatici come meccanismo di continuità e ripristino aziendale, se l'applicazione:

* Non è considerata cruciale.
* Non ha un contratto di servizio vincolante: un tempo di inattività di 24 o più ore non comporta alcuna responsabilità finanziaria.
* Ha una bassa frequenza di modifica dei dati, ovvero poche transazioni per ora, e la perdita di un massimo di un'ora di modifica dei dati è accettabile.
* Dipende dal costo.

Se è necessario un ripristino più veloce, usare la [Replica geografica attiva](sql-database-geo-replication-overview.md) (più avanti). Se è necessario essere in grado di ripristinare i dati da un periodo antecedente a 35 giorni, usare la [conservazione dei backup a lungo termine](sql-database-long-term-retention.md). 

### <a name="use-active-geo-replication-and-auto-failover-groups-in-preview-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>Usare la replica geografica attiva e i gruppi di failover automatico (in anteprima) per ridurre il tempo di recupero e limitare la perdita di dati associata a un ripristino

Oltre a usare i backup del database per il ripristino del database se si verifica un'interruzione aziendale, è possibile usare la [replica geografica attiva](sql-database-geo-replication-overview.md) per configurare un database in modo da avere fino a 4 database secondari leggibili nelle aree scelte. Questi database secondari vengono mantenuti sincronizzati con il database primario tramite un meccanismo di replica asincrona. Questa funzionalità viene usata per la protezione da interruzioni delle attività aziendali se si verifica un'interruzione del data center o durante un aggiornamento dell'applicazione. La replica geografica attiva può anche essere usata per offrire agli utenti situati in aree geografiche diverse prestazioni migliori per le query di sola lettura.

Per abilitare il failover automatico e trasparente è necessario organizzare i database replicati geograficamente in gruppi tramite la funzionalità [auto-failover group](sql-database-geo-replication-overview.md) del database SQL (in anteprima).

Se il database primario viene portato offline in modo imprevisto o è necessario portarlo online per attività di manutenzione, è possibile convertire rapidamente un database secondario perché diventi il database primario (detto anche failover) e configurare le applicazioni per la connessione al database primario alzato di livello. Se l'applicazione si connette ai database tramite il listener del gruppo di failover, non è necessario modificare la configurazione della stringa di connessione SQL dopo il failover. Con un failover pianificato, non si verificano perdite di dati. Con un failover non pianificato, potrebbero verificarsi piccole perdite di dati per le transazioni molto recenti a causa della natura di replica asincrona. Con l'uso di gruppi con failover automatico (in anteprima), è possibile personalizzare i criteri di failover per ridurre al minimo la perdita di dati. Dopo un failover, è possibile eseguire un failback sulla base di un piano o del momento in cui il data center ritorna online. In tutti i casi, gli utenti riscontrano un breve tempo di inattività e devono eseguire nuovamente la connessione.

> [!IMPORTANT]
> Per usare la replica geografica attiva e i gruppi di failover automatico (in anteprima), è necessario essere il proprietario della sottoscrizione o disporre delle autorizzazioni di amministrazione in SQL Server. È possibile configurare ed eseguire il failover tramite il portale di Azure, PowerShell o l'API REST usando le autorizzazioni della sottoscrizione o usando Transact-SQL con le autorizzazioni di SQL Server.
> 

Usare la replica geografica attiva e i gruppi con failover automatico (in anteprima) se l'applicazione soddisfa qualcuno dei criteri seguenti:

* È considerata cruciale.
* Ha un contratto di servizio che non consente più di 24 ore di inattività.
* Il tempo di inattività può implicare responsabilità finanziaria.
* Ha una frequenza elevata di modifica dei dati e la perdita di un'ora di dati non è accettabile.
* Il costo aggiuntivo della replica geografica attiva è inferiore rispetto alla potenziale responsabilità finanziaria e alla perdita di profitto associata.

>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

## <a name="recover-a-database-after-a-user-or-application-error"></a>Ripristinare un database in seguito a errore di un'applicazione o un utente
* Nessuno è perfetto! Un utente potrebbe accidentalmente eliminare alcuni dati, una tabella importante o addirittura un intero database. In alternativa un'applicazione potrebbe sovrascrivere accidentalmente dei dati con dati errati a causa di un difetto dell'applicazione.

In questo scenario, ecco sono le opzioni di ripristino.

### <a name="perform-a-point-in-time-restore"></a>Eseguire un ripristino temporizzato
È possibile usare i backup automatici per ripristinare una copia del database a un punto valido nel tempo, purché rientri nel periodo di memorizzazione del database. Dopo il ripristino del database, è possibile sostituire il database originale con il database ripristinato o copiare i dati necessari dai dati ripristinati nel database originale. Se il database usa la replica geografica attiva, è consigliabile copiare i dati necessari dalla copia ripristinata nel database originale. Se il database originale viene sostituito con il database ripristinato, è necessario riconfigurare e risincronizzare la replica geografica attiva (cosa che può richiedere molto tempo per un database di grandi dimensioni). Quando si ripristina un database all'ultimo punto disponibile il ripristino geo-secondario in qualsiasi punto nel tempo non è attualmente supportato.

Per altre informazioni e per i passaggi dettagliati per il ripristino di un database in un punto nel tempo tramite il portale di Azure o PowerShell, vedere [Ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore). Non è possibile eseguire il ripristino tramite Transact-SQL.

### <a name="restore-a-deleted-database"></a>Ripristino di un database eliminato
Se il database viene eliminato, ma il server logico non è stato eliminato, è possibile ripristinare il database eliminato sul punto in cui è stato eliminato. Ciò consente di ripristinare un backup del database nello stesso server SQL logico da cui è stato eliminato. È possibile eseguire il ripristino usando il nome originale o fornire un nuovo nome al database ripristinato.

Per altre informazioni e per i passaggi dettagliati per il ripristino di un database eliminato tramite il portale di Azure o PowerShell, vedere [Ripristino di un database eliminato](sql-database-recovery-using-backups.md#deleted-database-restore). Non è possibile eseguire il recupero tramite Transact-SQL.

> [!IMPORTANT]
> Se il server logico viene eliminato, non è possibile recuperare un database eliminato.
>
>

### <a name="restore-from-azure-backup-vault"></a>Ripristino dall'insieme di credenziali di Backup di Azure
Se la perdita di dati si è verificata fuori dal periodo di memorizzazione corrente per i backup automatici e il database è configurato per la conservazione a lungo termine, è possibile eseguire il ripristino da un backup settimanale nell'insieme di credenziali di Backup di Azure in un nuovo database. A questo punto, è possibile sostituire il database originale con il database ripristinato o copiare i dati necessari dai dati ripristinati nel database originale. Se si vuole recuperare una versione precedente del database prima di un aggiornamento importante dell'applicazione oppure soddisfare una richiesta dei revisori o di carattere legale, è possibile creare un database usando un backup completo salvato nell'insieme di credenziali di Backup di Azure.  Per altre informazioni, vedere [Long-term retention](sql-database-long-term-retention.md) (Conservazione a lungo termine).

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Ripristinare un database in un'altra area da un'interruzione del data center regionale di Azure
<!-- Explain this scenario -->

Anche se raramente, un data center di Azure può subire un'interruzione del servizio. Quando si verifica un'interruzione, viene generata un'interruzione delle attività che potrebbe durare solo pochi minuti oppure ore.

* Una delle opzioni è attendere che il database torni online al termine dell'interruzione del data center. Questa opzione funziona per le applicazioni che possono rimanere con il database offline. Ad esempio, un progetto di sviluppo o una versione di valutazione gratuita su cui non è necessario lavorare costantemente. Quando un data center registra un'interruzione del servizio, non si sa quanto tempo essa durerà. Pertanto, questa opzione funziona solo se è possibile rinunciare al database per un periodo di tempo.
* Un'altra opzione consiste nell'eseguire il failover in un'altra area dati se si usa la replica geografica attiva o il ripristino di un database tramite backup di database con ridondanza geografica (ripristino geografico). Il failover richiede solo pochi secondi mentre il ripristino del database da backup richiede ore.

Quando si esegue l'operazione, il tempo necessario per eseguire il ripristino e la quantità di dati persi dipendono dalle modalità in cui si decide di usare la funzionalità di continuità aziendale nell'applicazione. In effetti, è possibile scegliere di usare una combinazione di backup del database e replica geografica attiva a seconda dei requisiti dell'applicazione. Per una descrizione delle considerazioni sulla progettazione di applicazioni per database autonomi e pool elastici tramite queste funzioni di continuità aziendale, vedere [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [Strategie di ripristino di emergenza per applicazioni che usano il pool elastico del database SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

Le sezioni seguenti forniscono una panoramica della procedura per eseguire il ripristino tramite backup del database o replica geografica attiva. Per la procedura dettagliata, inclusa la pianificazione dei requisiti, i passaggi successivi al ripristino e informazioni su come simulare un'interruzione del servizio per eseguire un'analisi del ripristino di emergenza, vedere [Ripristinare un database SQL di Azure o eseguire il failover in un database secondario](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Prepararsi a un'interruzione del servizio
Indipendentemente dalla funzionalità di continuità aziendale in uso, è necessario:

* Identificare e preparare il server di destinazione, tra cui le regole del firewall a livello di server, gli account di accesso e le autorizzazioni a livello di database master.
* Stabilire come verranno reindirizzati i client e le applicazioni client verso il nuovo server
* Documentare altre dipendenze, ad esempio impostazioni di controllo e avvisi

Se non ci si prepara adeguatamente al ripristino, riportare online le applicazioni dopo un failover o un ripristino del database richiederà ulteriore tempo e probabilmente la risoluzione di problemi aggiuntivi in un momento di stress: una combinazione da evitare.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Failover a un database secondario con replica geografica
Se si usa la replica geografica attiva e i gruppi con failover automatico (in anteprima) come meccanismo di ripristino, è possibile configurare i criteri di failover automatico o usare il [failover manuale](sql-database-disaster-recovery.md#fail-over-to-geo-replicated-secondary-database). Una volta avviato, il failover fa sì che il database secondario venga alzato di livello come nuovo database primario e sia pronto per registrare nuove transazioni e rispondere a tutte le query, con una perdita di dati minima per i dati non ancora replicati. Per informazioni su come progettare il processo di failover, vedere [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Quando il data center ritorna in linea, i database primari precedenti si ricollegano automaticamente al nuovo database primario e diventano database secondari. Se si desidera spostare di nuovo il database primario nell'area originale è possibile avviare manualmente un failover pianificato (failback). 
> 

### <a name="perform-a-geo-restore"></a>Eseguire un ripristino geografico
Se si usano backup automatici con la replica dell'archiviazione con ridondanza geografica come meccanismo di ripristino, [avviare il ripristino del database tramite ripristino geografico](sql-database-disaster-recovery.md#recover-using-geo-restore). In genere il ripristino avviene entro 12 ore, con perdita di dati fino a 1 ora dall'ultima acquisizione e replica del backup differenziale orario. Fino a quando non viene completato il ripristino, il database non è in grado di registrare tutte le transazioni o rispondere a tutte le query. Quando si ripristina un database all'ultimo punto disponibile il ripristino geo-secondario in qualsiasi punto nel tempo non è attualmente supportato.

> [!NOTE]
> Se il data center ritorna online prima che l'applicazione venga spostata sul database ripristinato, è possibile annullare il ripristino.  
>
>

### <a name="perform-post-failover--recovery-tasks"></a>Eseguire attività successive al filover/ripristino
Dopo il ripristino da un meccanismo di ripristino, è necessario eseguire le seguenti attività aggiuntive prima che utenti e applicazioni siano nuovamente attivi e in esecuzione:

* Reindirizzare i client e le applicazioni client verso il nuovo server e il database ripristinato
* Verificare che siano disponibili le regole del firewall a livello di server appropriate per consentire agli utenti di connettersi o usare i [firewall a livello di database](sql-database-firewall-configure.md#creating-and-managing-firewall-rules)
* Verificare che siano presenti gli account di accesso appropriato e le autorizzazioni a livello di database master o usare gli [utenti contenuti](https://msdn.microsoft.com/library/ff929188.aspx)
* Configurare il controllo in base alle proprie esigenze.
* Configurare gli avvisi in base alle proprie esigenze.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Aggiornare un'applicazione con tempo di inattività minimo
A volte un'applicazione deve essere portata offline per ragioni di manutenzione pianificata, ad esempio un aggiornamento dell'applicazione. [Gestione degli aggiornamenti dell'applicazione](sql-database-manage-application-rolling-upgrade.md) descrive come usare la replica geografica attiva per consentire gli aggiornamenti in sequenza dell'applicazione cloud al fine di ridurre al minimo i tempi di inattività durante gli aggiornamenti e fornire un percorso di recupero se si verificano problemi. 

## <a name="next-steps"></a>Passaggi successivi
Per una descrizione delle considerazioni sulla progettazione di applicazioni per database autonomi e pool elastici, vedere [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [Strategie di ripristino di emergenza per applicazioni che usano il pool elastico del database SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).


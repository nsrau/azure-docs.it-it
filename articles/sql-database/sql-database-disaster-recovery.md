<properties 
   pageTitle="Ripristino di emergenza del database SQL | Microsoft Azure" 
   description="Informazioni su come ripristinare un database da un guasto o un'interruzione del servizio del data center a livello di area con le funzionalità di replica geografica attiva e ripristino geografico del database SQL." 
   services="sql-database" 
   documentationCenter="" 
   authors="carlrabeler" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-bcdr" 
   ms.date="06/16/2016"
   ms.author="carlrab"/>

# Ripristinare un database SQL di Azure o eseguire il failover in un database secondario

Il database SQL di Azure offre le funzionalità riportate di seguito per il ripristino da un'interruzione del servizio:

- [Replica geografica attiva](sql-database-geo-replication-overview.md)
- [Ripristino geografico](sql-database-recovery-using-backups.md#point-in-time-restore)

Per informazioni su come prepararsi per possibili emergenze e su quando ripristinare il database, vedere [Panoramica: Continuità aziendale cloud e ripristino di emergenza del database con il database SQL di Azure](sql-database-business-continuity.md) e [Ripristinare un database SQL di Azure o eseguire il failover in un database secondario]().

## Quando avviare il ripristino

L'operazione di ripristino ha un impatto sull'applicazione. Richiede la modifica della stringa di connessione SQL e può comportare una perdita di dati permanente. Pertanto, è necessario eseguirla solo quando è probabile che l'interruzione del servizio duri più a lungo dell'obiettivo del tempo di ripristino dell'applicazione. Quando l'applicazione viene distribuita nell'ambiente di produzione, è consigliabile eseguire il monitoraggio regolare dell'integrità dell'applicazione e usare i punti dati seguenti per determinare se il ripristino è possibile:

1.	Si è verificato un errore di connettività permanente dal livello applicazione al database.
2.	Il portale di Azure visualizza un avviso relativo a un evento imprevisto nell'area con un impatto importante.
3.	Il server di database SQL di Azure è contrassegnato come danneggiato.

A seconda della tolleranza dell'applicazione ai tempi di inattività e delle eventuali responsabilità aziendali è possibile prendere in considerazione le opzioni di ripristino seguenti.

Usare [Get Recoverable Database](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*), che consente di ottenere l'ultimo punto di ripristino con replica geografica.

## Attendere il ripristino del servizio

I team di Azure puntano a ripristinare la disponibilità del servizio quanto più rapidamente possibile, ma questo può richiedere ore o giorni a seconda della causa radice. Se l'applicazione può tollerare tempi di inattività significativi è possibile attendere semplicemente il completamento del ripristino. In tal caso, non è necessaria alcuna azione da parte dell'utente. È possibile vedere lo stato corrente del servizio nel [dashboard per l'integrità dei servizi di Azure](https://azure.microsoft.com/status/). Dopo il ripristino dell'area verrà ripristinata la disponibilità dell'applicazione.

## Failover al database secondario con replica geografica

Se i tempi di inattività dell'applicazione possono comportare una responsabilità aziendale, è consigliabile usare database con replica geografica nell'applicazione. Questo permette all'applicazione di ripristinare rapidamente la disponibilità in un'area diversa in caso di interruzione del servizio. Informazioni su [come configurare la replica geografica](sql-database-geo-replication-portal.md).

Per ripristinare la disponibilità dei database è necessario avviare il failover nel database secondario con replica geografica usando uno dei metodi supportati.


Per eseguire il failover in un database secondario con replica geografica, seguire una di queste procedure:

- [Failover in un database secondario con replica geografica tramite il portale di Azure](sql-database-geo-replication-portal.md)
- [Failover in un database secondario con replica geografica tramite PowerShell](sql-database-geo-replication-powershell.md)
- [Failover in un database secondario con replica geografica tramite T-SQL](sql-database-geo-replication-transact-sql.md)



## Ripristino tramite il ripristino geografico

Se i tempi di inattività dell'applicazione non comportano una responsabilità aziendale è possibile usare il ripristino geografico come metodo per il ripristino dei database dell'applicazione. Questo metodo crea una copia del database dal backup con ridondanza geografica più recente.

Per eseguire il ripristino geografico un database in una nuova area, seguire una di queste procedure:

- [Ripristino geografico di un database in una nuova area tramite il portale di Azure](sql-database-geo-restore-portal.md)
- [Ripristino geografico di un database in una nuova area tramite PowerShell](sql-database-geo-restore-powershell.md)


## Configurare il database dopo il ripristino

Se si esegue il ripristino da un'interruzione del servizio usando il failover con replica geografica o il ripristino geografico, è necessario assicurarsi che la connettività ai nuovi database sia configurata correttamente per poter riprendere il normale funzionamento dell'applicazione. Di seguito è riportato un elenco di controllo di attività per fare in modo che il database ripristinato sia pronto per la produzione.

### Aggiornare le stringhe di connessione

Poiché il database ripristinato si troverà in un server diverso, è necessario aggiornare la stringa di connessione dell'applicazione in modo che punti a tale server.

Per altre informazioni sulla modifica delle stringhe di connessione, vedere il linguaggio di sviluppo appropriato per le [raccolte di connessioni](sql-database-libraries.md).

### Configurare le regole firewall

Verificare che le regole firewall configurate nel server e nel database corrispondano a quelle configurate nel server primario e nel database primario. Per altre informazioni, vedere [Procedura: Configurare le impostazioni del firewall (Database SQL di Azure)](sql-database-configure-firewall-settings.md).


### Configurare gli account di accesso e gli utenti del database

Verificare che tutti gli account di accesso usati dall'applicazione siano presenti nel server che ospita il database ripristinato. Per altre informazioni, vedere [Come gestire la sicurezza dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).

>[AZURE.NOTE] È necessario configurare e testare le regole del firewall del server e gli account di accesso (con le relative autorizzazioni) durante un'analisi di ripristino di emergenza. Questi oggetti a livello di server e la relativa configurazione potrebbero non essere disponibili durante l'interruzione del servizio. Per altre informazioni, vedere [Esercitazione per il ripristino di emergenza](sql-database-disaster-recovery-drills.md).

### Avvisi di telemetria di configurazione

Verificare che le impostazioni della regola di avviso esistenti vengano aggiornate per il mapping al database ripristinato e al nuovo server.

Per altre informazioni sulle regole di avviso per il database, vedere [Ricevere notifiche di avviso](../azure-portal/insights-receive-alert-notifications.md) e [Tracciare l’integrità del servizio](../azure-portal/insights-service-health.md).

### Attivare il controllo

Se è necessario il controllo di accesso al database, occorre attivare il controllo dopo il ripristino del database. Per altre informazioni, vedere l'[Introduzione al controllo del database SQL](sql-database-auditing-get-started.md). Per informazioni sui client legacy, vedere [Database SQL: supporto dei client legacy e modifiche all'endpoint IP per Controllo](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).


## Passaggi successivi

- Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md)
- Per informazioni sugli scenari di progettazione e ripristino della continuità aziendale, vedere [Scenari di continuità aziendale del database SQL di Azure](sql-database-business-continuity-scenarios.md)
- Per informazioni sull'uso di backup automatici per il ripristino, vedere [Ripristinare un database SQL di Azure mediante i backup automatici del database](sql-database-recovery-using-backups.md)
- Per informazioni sulle opzioni di ripristino più veloci, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md)
- Per informazioni sull'uso di backup automatici per l'archiviazione, vedere [Copiare un database SQL di Azure](sql-database-copy.md)

<!---HONumber=AcomDC_0713_2016-->
---
title: Ripristino di emergenza del database SQL | Documentazione Microsoft
description: "Informazioni su come ripristinare un database da un guasto o un'interruzione del servizio del data center a livello di area con le funzionalità di replica geografica attiva e ripristino geografico del database SQL."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: 4800960e-3f9d-40ce-9e55-fb7f2784c067
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/14/2017
ms.author: sashan
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: e5242b5f76866ca382b31005deca07cd722af423
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Ripristinare un database SQL di Azure o eseguire il failover in un database secondario
Il database SQL di Azure offre le funzionalità riportate di seguito per il ripristino da un'interruzione del servizio:

* [Replica geografica attiva](sql-database-geo-replication-overview.md)
* [Ripristino geografico](sql-database-recovery-using-backups.md#point-in-time-restore)

Per informazioni sugli scenari di continuità aziendale e sulle funzionalità che supportano questi scenari, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).

### <a name="prepare-for-the-event-of-an-outage"></a>Prepararsi per un evento di interruzione del servizio
Per completare correttamente il ripristino su un'altra area dati tramite la replica geografica attiva o i backup con ridondanza geografica, è necessario preparare un server in un altro data center perché diventi il nuovo server primario in caso di necessità, nonché procedure ben definite, documentate e testate per garantire un ripristino senza problemi. La procedura di preparazione comprende:

* Identificare il server logico in un'altra area perché diventi il nuovo server primario. Con la replica geografica attiva, questo sarà almeno uno o forse ognuno dei server secondari. Per il ripristino geografico, questo sarà in genere un server di un'[area abbinata](../best-practices-availability-paired-regions.md) all'area in cui si trova il database.
* Identificare e definire, facoltativamente, le regole del firewall a livello di server necessarie agli utenti per accedere al nuovo database primario.
* Determinare come si desidera reindirizzare gli utenti al nuovo server primario, ad esempio tramite modifica delle stringhe di connessione o delle voci del DNS.
* Identificare e, facoltativamente, creare gli account di accesso presenti nel database master nel nuovo server primario e verificare che questi account di accesso dispongano delle autorizzazioni appropriate nel database master, se necessarie. Per altre informazioni, vedere [Come gestire la sicurezza del database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md)
* Stabilire le regole di avviso che dovranno essere aggiornata per eseguire il mapping verso il nuovo database primario.
* Documentare la configurazione di controllo nel database primario corrente
* [Esercitazione per il ripristino di emergenza](sql-database-disaster-recovery-drills.md). Per simulare un'interruzione del servizio di ripristino geografico, è possibile eliminare o rinominare il database di origine per provocare un errore di connettività dell'applicazione. Per simulare un'interruzione del servizio per la replica geografica attiva, è possibile disabilitare l'applicazione Web o la macchina virtuale connessa al database o il failover del database per causare errori di connettività dell'applicazione.

## <a name="when-to-initiate-recovery"></a>Quando avviare il ripristino
L'operazione di ripristino ha un impatto sull'applicazione. Richiede la modifica della stringa di connessione SQL o il reindirizzamento tramite DNS e può comportare una perdita di dati permanente. Pertanto, è necessario eseguirla solo quando è probabile che l'interruzione del servizio duri più a lungo dell'obiettivo del tempo di ripristino dell'applicazione. Quando l'applicazione viene distribuita nell'ambiente di produzione, è consigliabile eseguire il monitoraggio regolare dell'integrità dell'applicazione e usare i punti dati seguenti per determinare se il ripristino è possibile:

1. Si è verificato un errore di connettività permanente dal livello applicazione al database.
2. Il portale di Azure visualizza un avviso relativo a un evento imprevisto nell'area con un impatto importante.
3. Il server di database SQL di Azure è contrassegnato come danneggiato.

A seconda della tolleranza dell'applicazione ai tempi di inattività e delle eventuali responsabilità aziendali è possibile prendere in considerazione le opzioni di ripristino seguenti.

Usare [Get Recoverable Database](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*), che consente di ottenere l'ultimo punto di ripristino con replica geografica.

## <a name="wait-for-service-recovery"></a>Attendere il ripristino del servizio
I team di Azure puntano a ripristinare la disponibilità del servizio quanto più rapidamente possibile, ma questo può richiedere ore o giorni a seconda della causa radice.  Se l'applicazione può tollerare tempi di inattività significativi è possibile attendere semplicemente il completamento del ripristino. In tal caso, non è necessaria alcuna azione da parte dell'utente. È possibile vedere lo stato corrente del servizio nel [dashboard per l'integrità dei servizi di Azure](https://azure.microsoft.com/status/). Dopo il ripristino dell'area verrà ripristinata la disponibilità dell'applicazione.

## <a name="fail-over-to-geo-replicated-secondary-database"></a>Failover al database secondario con replica geografica
Se i tempi di inattività dell'applicazione possono comportare una responsabilità aziendale, è consigliabile usare database con replica geografica nell'applicazione. Questo permette all'applicazione di ripristinare rapidamente la disponibilità in un'area diversa in caso di interruzione del servizio. Informazioni su [come configurare la replica geografica](sql-database-geo-replication-portal.md).

Per ripristinare la disponibilità dei database è necessario avviare il failover nel database secondario con replica geografica usando uno dei metodi supportati.

Per eseguire il failover in un database secondario con replica geografica, seguire una di queste procedure:

* [Failover in un database secondario con replica geografica tramite il portale di Azure](sql-database-geo-replication-portal.md)
* [Failover in un database secondario con replica geografica tramite PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
* [Failover in un database secondario con replica geografica tramite T-SQL](/sql/t-sql/statements/alter-database-azure-sql-database.md)

## <a name="recover-using-geo-restore"></a>Ripristino tramite il ripristino geografico
Se i tempi di inattività dell'applicazione non comportano una responsabilità aziendale è possibile usare il [ripristino geografico](sql-database-recovery-using-backups.md) come metodo per il ripristino dei database dell'applicazione. Questo metodo crea una copia del database dal backup con ridondanza geografica più recente.

## <a name="configure-your-database-after-recovery"></a>Configurare il database dopo il ripristino
Se si esegue il ripristino da un'interruzione del servizio usando il failover con replica geografica o il ripristino geografico, è necessario assicurarsi che la connettività ai nuovi database sia configurata correttamente per poter riprendere il normale funzionamento dell'applicazione. Di seguito è riportato un elenco di controllo di attività per fare in modo che il database ripristinato sia pronto per la produzione.

### <a name="update-connection-strings"></a>Aggiornare le stringhe di connessione
Poiché il database ripristinato si troverà in un server diverso, è necessario aggiornare la stringa di connessione dell'applicazione in modo che punti a tale server.

Per altre informazioni sulla modifica delle stringhe di connessione, vedere il linguaggio di sviluppo appropriato per le [raccolte di connessioni](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Configurare le regole firewall
Verificare che le regole firewall configurate nel server e nel database corrispondano a quelle configurate nel server primario e nel database primario. Per altre informazioni, vedere [Procedura: Configurare le impostazioni del firewall (Database SQL di Azure)](sql-database-configure-firewall-settings.md).

### <a name="configure-logins-and-database-users"></a>Configurare gli account di accesso e gli utenti del database
Verificare che tutti gli account di accesso usati dall'applicazione siano presenti nel server che ospita il database ripristinato. Per altre informazioni, vedere [Configurazione della sicurezza per la replica geografica](sql-database-geo-replication-security-config.md).

> [!NOTE]
> È necessario configurare e testare le regole del firewall del server e gli account di accesso (con le relative autorizzazioni) durante un'analisi di ripristino di emergenza. Questi oggetti a livello di server e la relativa configurazione potrebbero non essere disponibili durante l'interruzione del servizio.
> 
> 

### <a name="setup-telemetry-alerts"></a>Avvisi di telemetria di configurazione
Verificare che le impostazioni della regola di avviso esistenti vengano aggiornate per il mapping al database ripristinato e al nuovo server.

Per altre informazioni sulle regole di avviso per il database, vedere [Ricevere notifiche di avviso](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) e [Tracciare l’integrità del servizio](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Attivare il controllo
Se è necessario il controllo di accesso al database, occorre attivare il controllo dopo il ripristino del database. Per altre informazioni, vedere l'articolo sul [controllo del database](sql-database-auditing.md).

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md)
* Per informazioni sugli scenari di progettazione e ripristino della continuità aziendale, vedere l'articolo relativo agli [scenari di continuità aziendale](sql-database-business-continuity.md)
* Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)



---
title: Replica transazionale
titleSuffix: Azure SQL Managed Instance
description: Informazioni sull'uso di SQL Server la replica transazionale con Istanza gestita SQL di Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 04/20/2020
ms.openlocfilehash: 00f456d87bd5791b7d49644cb801dca20431b0b5
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086399"
---
# <a name="transactional-replication-with-azure-sql-managed-instance"></a>Replica transazionale con Istanza gestita SQL di Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

La replica transazionale è una funzionalità di Azure SQL Istanza gestita e SQL Server che consente di replicare i dati da una tabella in Istanza gestita SQL di Azure o da un'istanza di SQL Server a tabelle posizionate nei database remoti. Questa funzionalità consente di sincronizzare più tabelle in database diversi.

## <a name="overview"></a>Panoramica

È possibile usare la replica transazionale per eseguire il push delle modifiche apportate in un Istanza gestita SQL di Azure per:

- Una SQL Server database locale o in una VM di Azure
- Un database nel database SQL di Azure
- Un database di istanza in Azure SQL Istanza gestita

  > [!NOTE]
  > Per usare tutte le funzionalità di Istanza gestita SQL di Azure, è necessario usare le versioni più recenti di [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).

### <a name="components"></a>Componenti

I componenti principali della replica transazionale sono **server di pubblicazione**, **server di distribuzione**e **Sottoscrittore**, come illustrato nell'immagine seguente:  

![Replica con database SQL](./media/replication-transactional-overview/replication-to-sql-database.png)

| Ruolo | database SQL di Azure | Istanza gestita di SQL di Azure |
| :----| :------------- | :--------------- |
| **Autore** | No | Sì |
| **Distribuzione** | No | Sì|
| **Sottoscrittore pull** | No | Sì|
| **Sottoscrittore push**| Sì | Sì|
| &nbsp; | &nbsp; | &nbsp; |

Il **server di pubblicazione** pubblica le modifiche apportate in alcune tabelle (articoli) inviando gli aggiornamenti al server di distribuzione. Il server di pubblicazione può essere un Istanza gestita SQL di Azure o un'istanza di SQL Server.

Il **server di distribuzione** raccoglie le modifiche apportate agli articoli da un server di pubblicazione e le distribuisce ai sottoscrittori. Il server di distribuzione può essere un Istanza gestita SQL di Azure o un'istanza di SQL Server (qualsiasi versione purché sia uguale o superiore alla versione del server di pubblicazione).

Il **Sottoscrittore** riceve le modifiche apportate al server di pubblicazione. Un'istanza di SQL Server e un Istanza gestita SQL di Azure possono essere sottoscrittori push e pull, anche se una sottoscrizione pull non è supportata quando il server di distribuzione è un Istanza gestita SQL di Azure e il Sottoscrittore non lo è. Un database nel database SQL di Azure può essere solo un Sottoscrittore push.

Istanza gestita SQL di Azure può supportare l'esecuzione di un Sottoscrittore dalle seguenti versioni di SQL Server:

- SQL Server 2016 e versioni successive
- SQL Server 2014 [RTM CU10 dalla (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) o [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) o [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

   > [!NOTE]
   >
   > - Per altre versioni di SQL Server che non supportano la pubblicazione in oggetti in Azure, è possibile usare il metodo di [ripubblicazione dei dati](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) per spostare i dati in versioni più recenti di SQL Server.
   > - Se si cerca di configurare la replica usando una versione meno recente, potrebbero essere generati gli errori MSSQL_REPL20084 (Il processo non è riuscito a connettersi al sottoscrittore) e MSSQ_REPL40532 (Impossibile aprire il server \<name> richiesto dall'account di accesso. Accesso non riuscito).

### <a name="types-of-replication"></a>Tipi di replica

Esistono diversi [tipi di replica](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication):

| Replica | database SQL di Azure | Istanza gestita di SQL di Azure |
| :----| :------------- | :--------------- |
| [**Transazionale standard**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Sì (solo come sottoscrittore) | Sì |
| [**Snapshot**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Sì (solo come sottoscrittore) | Sì|
| [**Replica di tipo merge**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | No | No|
| [**Peer-to-peer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | No | No|
| [**Bidirezionale**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | No | Sì|
| [**Sottoscrizioni aggiornabili**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | No | No|
| &nbsp; | &nbsp; | &nbsp; |

### <a name="supportability-matrix"></a>Matrice di supporto

  La matrice di supporto per la replica transazionale per Istanza gestita SQL di Azure è uguale a quella di SQL Server.
  
| **Autore**   | **Distribuzione** | **Subscriber** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="when-to-use"></a>Utilizzo

La replica transazionale è utile negli scenari seguenti:

- Pubblicare le modifiche apportate in una o più tabelle di un database e distribuirle in uno o più database in un'istanza di SQL Server o in un database SQL di Azure che ha sottoscritto le modifiche.
- Mantenere più database distribuiti in stato sincronizzato.
- Eseguire la migrazione dei database da un'istanza di SQL Server o di Azure SQL Istanza gestita a un altro database pubblicando continuamente le modifiche.

### <a name="compare-data-sync-with-transactional-replication"></a>Confrontare la sincronizzazione dati con la replica transazionale

| | Sincronizzazione dei dati | Replica transazionale |
|---|---|---|
| Vantaggi | - Supporto attivo/attivo<br/>- Bidirezionale tra database locali e database SQL di Azure | - Latenza inferiore<br/>- Coerenza delle transazioni<br/>- Riutilizzo topologia esistente dopo la migrazione |
| Svantaggi | - Latenza 5 min o superiore<br/>- Nessuna coerenza delle transazioni<br/>- Maggiore impatto sulle prestazioni | -Non è possibile pubblicare dal database SQL di Azure <br/>- Alti costi di manutenzione |
| | | |

## <a name="common-configurations"></a>Configurazioni comuni

In generale, il server di pubblicazione e il database di pubblicazione devono entrambi essere nel cloud o locali. Le configurazioni seguenti sono supportate:

### <a name="publisher-with-local-distributor-on-sql-managed-instance"></a>Server di pubblicazione con server di distribuzione locale in SQL Istanza gestita

![Singola istanza come server di pubblicazione e database di distribuzione](./media/replication-transactional-overview/01-single-instance-asdbmi-pubdist.png)

Il server di pubblicazione e il server di distribuzione sono configurati all'interno di una singola Istanza gestita SQL e distribuiscono le modifiche in un'altra istanza di SQL Istanza gestita, database SQL o SQL Server

### <a name="publisher-with-remote-distributor-on-sql-managed-instance"></a>Server di pubblicazione con server di distribuzione remoto in SQL Istanza gestita

In questa configurazione, un'istanza gestita pubblica le modifiche apportate a un server di distribuzione inserito in un altro Istanza gestita SQL che può gestire molte istanze di SQL gestite di origine e distribuisce le modifiche a una o più destinazioni nel database SQL di Azure, in Azure SQL Istanza gestita o SQL Server.

![Istanze separate per server di pubblicazione e database di distribuzione](./media/replication-transactional-overview/02-separate-instances-asdbmi-pubdist.png)

I database di pubblicazione e distribuzione sono configurati in due istanze gestite. Questa configurazione presenta alcuni vincoli:

- Entrambe le istanze gestite si trovano nello stesso vNet.
- Le due istanze gestite sono nella stessa posizione.

### <a name="on-premises-publisherdistributor-with-remote-subscriber"></a>Server di pubblicazione/distribuzione locale con Sottoscrittore remoto

![Database SQL di Azure come Sottoscrittore](./media/replication-transactional-overview/03-azure-sql-db-subscriber.png)

In questa configurazione, un database nel database SQL di Azure o in Azure SQL Istanza gestita è un Sottoscrittore. Questa configurazione supporta la migrazione dal database locale al database di Azure. Se un Sottoscrittore è un database nel database SQL di Azure, deve essere in modalità push.  

## <a name="requirements"></a>Requisiti

- Usare l'autenticazione SQL per la connettività tra i partecipanti della replica.
- Usare una condivisione dell'account di archiviazione di Azure per la directory di lavoro utilizzata dalla replica.
- Aprire la porta TCP in uscita 445 nelle regole di sicurezza della subnet per accedere alla condivisione file di Azure.
- Aprire la porta TCP in uscita 1433 quando il Istanza gestita SQL è il server di pubblicazione o il server di distribuzione e il Sottoscrittore non lo è. Potrebbe anche essere necessario modificare la regola di sicurezza in uscita di SQL Istanza gestita NSG per `allow_linkedserver_outbound` per il **tag del servizio di destinazione** porta 1433 da `virtualnetwork` a `internet` .
- Posizionare il server di pubblicazione e il server di distribuzione nel cloud o in locale.
- Configurare il peering VPN tra le reti virtuali dei partecipanti alla replica se le reti virtuali sono diverse.

> [!NOTE]
> È possibile che si verifichi l'errore 53 quando ci si connette a un file di archiviazione di Azure se la porta 445 del gruppo di sicurezza di rete (NSG) in uscita è bloccata quando il server di distribuzione è un database SQL di Azure Istanza gestita e il Sottoscrittore è in locale. [Aggiornare il NSG di vNet](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) per risolvere il problema.

## <a name="with-failover-groups"></a>Con gruppi di failover

La [replica geografica attiva](../database/active-geo-replication-overview.md) non è supportata con una istanza gestita SQL che usa la replica transazionale. Anziché eseguire la replica geografica attiva, usare i [gruppi di failover automatico](../database/auto-failover-group-overview.md), ma si noti che la pubblicazione deve essere [eliminata manualmente](transact-sql-tsql-differences-sql-server.md#replication) dall'istanza gestita primaria e ricreata nel istanza gestita SQL secondario dopo il failover.

Se la replica geografica è abilitata in un **server di pubblicazione** o in un **server di distribuzione** SQL istanza gestita in un gruppo di [failover](../database/auto-failover-group-overview.md), l'amministratore di SQL istanza gestita deve eliminare tutte le pubblicazioni nel database primario precedente e riconfigurarle sul nuovo database primario dopo un failover. In questo scenario sono necessarie le attività seguenti:

1. Arrestare tutti i processi di replica in esecuzione nel database, se presenti.
1. Eliminare i metadati della sottoscrizione dal server di pubblicazione eseguendo lo script seguente nel database del server di pubblicazione:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```

1. Rilasciare i metadati della sottoscrizione dal Sottoscrittore. Eseguire lo script seguente nel database di sottoscrizione nel Sottoscrittore SQL Istanza gestita:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>',
      @publisher_db = N'<publisher database>',
      @publication = N'<name of publication>';
   ```

1. Eliminare in modo forzato tutti gli oggetti di replica dal server di pubblicazione eseguendo lo script seguente nel database pubblicato:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Eliminare in modo forzato il server di distribuzione precedente dal Istanza gestita primario SQL originale (se si esegue il failover in un database primario precedente utilizzato per avere un server di distribuzione). Eseguire lo script seguente nel database master del server di distribuzione precedente SQL Istanza gestita:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Se la replica geografica è abilitata in un'istanza del **Sottoscrittore** in un gruppo di failover, la pubblicazione deve essere configurata per la connessione all'endpoint del listener del gruppo di failover per l'istanza gestita del Sottoscrittore. In caso di failover, l'azione successiva da parte dell'amministratore dell'istanza gestita dipende dal tipo di failover che si è verificato:

- Per un failover senza perdita di dati, la replica continuerà a funzionare dopo il failover.
- Per un failover con perdita di dati, funzionerà anche la replica. La replica delle modifiche perse verrà nuovamente eseguita.
- Per un failover con perdita di dati, ma la perdita di dati non rientra nel periodo di memorizzazione del database di distribuzione, l'amministratore di SQL Istanza gestita dovrà reinizializzare il database di sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla configurazione della replica transazionale, vedere le esercitazioni seguenti:

- [Configurare la replica tra un server di pubblicazione e un Sottoscrittore SQL Istanza gestita](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Configurare la replica tra un server di pubblicazione SQL Istanza gestita, un server di distribuzione SQL Istanza gestita e un Sottoscrittore SQL Server](../managed-instance/replication-two-instances-and-sql-server-configure-tutorial.md)
- [Creare una pubblicazione](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
- [Creare una sottoscrizione push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) usando il nome del server come Sottoscrittore (ad esempio `N'azuresqldbdns.database.windows.net` , e il database nel nome del database SQL di Azure come database di destinazione, ad esempio **AdventureWorks**. )

## <a name="see-also"></a>Vedere anche  

- [Replica con un Istanza gestita SQL e un gruppo di failover](transact-sql-tsql-differences-sql-server.md#replication)
- [Replica nel database SQL](../database/replication-to-sql-database.md)
- [Replica nell'istanza gestita](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Creare una pubblicazione](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Creare una sottoscrizione push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Tipi di replica](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitoraggio (replica)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inizializzare una sottoscrizione](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  

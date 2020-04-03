---
title: Replica transazionale
description: Informazioni sull'uso della replica transazionale di SQL Server con database singoli, in pool e dell'istanza nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 02/08/2019
ms.openlocfilehash: 2a048ddefbcd76193436da13cd3ba68b8b6ffb0a
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607603"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Replica transazionale con database singoli, in pool e dell'istanza nel database SQL di Azure

La replica transazionale è una funzionalità del database SQL di Azure e di SQL Server che consente di replicare i dati da una tabella nel database SQL di Azure o in un SQL Server verso tabelle inserite in database remoti. Questa funzionalità consente di sincronizzare più tabelle in database diversi.

## <a name="when-to-use-transactional-replication"></a>Quando usare la replica transazionale

La replica transazionale è utile negli scenari seguenti:
- Pubblicare le modifiche apportate in una o più tabelle in un database e distribuirle in uno o più SQL Server o database SQL di Azure che hanno sottoscritto le modifiche.
- Mantenere più database distribuiti in stato sincronizzato.
- Eseguire la migrazione dei database da un'istanza di SQL ServerSQL Server o un'istanza gestita a un altro database pubblicando continuamente le modifiche.

## <a name="overview"></a>Panoramica

I componenti chiave della replica transazionale sono illustrati nell'immagine seguente:  

![Replica con database SQL](media/replication-to-sql-database/replication-to-sql-database.png)

Il **server di pubblicazione** è un'istanza o un server che pubblica le modifiche apportate in alcune tabelle (articoli) inviando gli aggiornamenti al database di distribuzione. La pubblicazione in un database SQL di Azure qualsiasi da un SQL Server locale è supportata dalle versioni seguenti di SQL Server:

- SQL Server 2019 (anteprima)
- SQL Server da 2016 a 2017
- SQL Server 2014 SP1 CU3 o versione successiva (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 o versione successiva (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- Per altre versioni di SQL Server che non supportano la pubblicazione in oggetti in Azure, è possibile usare il metodo di [ripubblicazione dei dati](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) per spostare i dati in versioni più recenti di SQL Server. 

Il **database di distribuzione** è un'istanza o un server che raccoglie le modifiche negli articoli da un server di pubblicazione e li distribuisce ai sottoscrittori. Il server di distribuzione può essere l'istanza gestita del database SQL di Azure o SQL Server (a qualsiasi versione, purché sia uguale o superiore alla versione del server di pubblicazione). 

Il **sottoscrittore** è un'istanza o un server che riceve le modifiche apportate nel server di pubblicazione. I sottoscrittori possono essere database singoli, in pool e dell'istanza del database SQL di Azure o di database SQL Server. Un sottoscrittore in un database singolo o in pool deve essere configurato come sottoscrittore push. 

| Ruolo | Database singoli e in pool | Database dell'istanza |
| :----| :------------- | :--------------- |
| **Editore** | No | Sì | 
| **Database di distribuzione** | No | Sì|
| **Sottoscrittore pull** | No | Sì|
| **Sottoscrittore push**| Sì | Sì|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > Una sottoscrizione pull non è supportata quando il server di distribuzione è un database di istanza e il server di sottoscrizione non lo è. 

Esistono diversi [tipi di replica](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication):


| Replica | Database singoli e in pool | Database in istanza|
| :----| :------------- | :--------------- |
| [**Transazionale standard**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Sì (solo come sottoscrittore) | Sì | 
| [**Snapshot**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Sì (solo come sottoscrittore) | Sì|
| [**Replica di tipo merge**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | No | No|
| [**Peer-to-peer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | No | No|
| [**Bidirezionale**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | No | Sì|
| [**Abbonamenti aggiornabili**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | No | No|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Se si cerca di configurare la replica usando una versione meno recente, potrebbero essere generati gli errori MSSQL_REPL20084. (Il processo non è riuscito a connettersi al sottoscrittore) e MSSQ_REPL40532 (Impossibile aprire il server \<nome> richiesto dall'account di accesso. Accesso non riuscito).
  > - Per usare tutte le funzionalità del database SQL di Azure, è necessario usare le versioni più recenti di [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Matrice di supporto per database di istanza e sistemi localiSupportability matrix for Instance Databases and On-premises systems
  La matrice di supportabilità della replica per i database di istanza è uguale a quella di SQL Server locale. 
  
| **Editore**   | **Database di distribuzione** | **Sottoscrittore** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Requisiti

- Per la connettività viene usata l'autenticazione SQL tra i partecipanti alla replica. 
- Una condivisione di account di archiviazione di Azure per la directory di lavoro usata dalla replica. 
- La porta 445 (TCP in uscita) deve essere aperta nelle regole di sicurezza della subnet dell'istanza gestita per accedere alla condivisione file di Azure.Port 445 (TCP outbound) needs to be open in the security rules of the managed instance subnet to access the Azure file share. 
- La porta 1433 (TCP in uscita) deve essere aperta se il server di pubblicazione/server di distribuzione si trova in un'istanza gestita e il server di sottoscrizione non lo è. Potrebbe anche essere necessario modificare la regola di `allow_linkedserver_outbound` sicurezza in uscita del gruppo di `internet`sicurezza di rete dell'istanza gestita per il tag della porta 1433 servizio di **destinazione** da `virtualnetwork` a . 
- Tutti i tipi di partecipanti alla replica (server di pubblicazione, server di distribuzione, Sottoscrittore pull e Sottoscrittore Push) possono essere inseriti nelle istanze gestite, ma il server di pubblicazione e il server di distribuzione devono trovarsi sia nel cloud che in locale.
- Se il server di pubblicazione, il server di distribuzione e/o il server di sottoscrizione esistono in reti virtuali diverse, è necessario stabilire il peering VPN tra ogni entità, in modo che vi sia un peering VPN tra il server di pubblicazione e il server di distribuzione e/o il peering VPN tra il server di distribuzione e il server di sottoscrizione. 


>[!NOTE]
> - È possibile riscontrare l'errore 53 durante la connessione a un file di archiviazione di Azure se la porta 445 del gruppo di sicurezza di rete in uscita è bloccata quando il server di distribuzione è un database dell'istanza e il sottoscrittore è locale. [Aggiornare il gruppo di sicurezza di rete vNet](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) per risolvere questo problema. 


### <a name="compare-data-sync-with-transactional-replication"></a>Confrontare la sincronizzazione dati con la replica transazionale

| | Sincronizzazione dei dati | Replica transazionale |
|---|---|---|
| Vantaggi | - Supporto attivo/attivo<br/>- Bidirezionale tra database locali e database SQL di Azure | - Latenza inferiore<br/>- Coerenza delle transazioni<br/>- Riutilizzo topologia esistente dopo la migrazione |
| Svantaggi | - Latenza 5 min o superiore<br/>- Nessuna coerenza delle transazioni<br/>- Maggiore impatto sulle prestazioni | - Impossibilità di pubblicare da database singolo o in pool di Database SQL di Azure<br/>- Alti costi di manutenzione |
| | | |

## <a name="common-configurations"></a>Configurazioni comuni

In generale, il server di pubblicazione e il database di pubblicazione devono entrambi essere nel cloud o locali. Le configurazioni seguenti sono supportate: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Server di pubblicazione con server di distribuzione locale in un'istanza gestita

![Singola istanza come server di pubblicazione e database di distribuzione](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Il server di pubblicazione e il server di distribuzione vengono configurati all'interno di una singola istanza gestita e distribuendo le modifiche ad altre istanze gestite, a un singolo database, a un database in pool o a SQL Server in locale. 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Server di pubblicazione con server di distribuzione remoto in un'istanza gestita

In this configuration, one managed instance publishes changes to distributor placed on another managed instance that can serve many source managed instances and distribute changes to one or many targets on managed instance, single database, pooled database, or SQL Server.

![Istanze separate per server di pubblicazione e database di distribuzione](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

I database di pubblicazione e distribuzione sono configurati in due istanze gestite. Esistono alcuni vincoli con questa configurazione:There are some constraints with this configuration: 

- Entrambe le istanze gestite si trovano nella stessa rete virtuale.
- Le due istanze gestite sono nella stessa posizione.


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Server di pubblicazione e database di distribuzione locali con sottoscrittore in un database singolo, in pool e dell'istanza 

![Database SQL di Azure come sottoscrittore](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
In questa configurazione, un database SQL di Azure (database singolo, in pool e dell'istanza) è un sottoscrittore. Questa configurazione supporta la migrazione dal database locale al database di Azure. Se un sottoscrittore si trova in un database in pool o singolo, deve essere in modalità push.  

## <a name="with-failover-groups"></a>Con gruppi di failover

Se la replica geografica è abilitata in un **server di pubblicazione** o un'istanza del **server** di distribuzione in un gruppo di [failover,](sql-database-auto-failover-group.md)l'amministratore dell'istanza gestita deve pulire tutte le pubblicazioni nel database primario precedente e riconfigurarle nel nuovo database primario dopo un failover. In questo scenario sono necessarie le seguenti attività:

1. Arrestare tutti i processi di replica in esecuzione nel database, se presenti.
2. Eliminare i metadati della sottoscrizione dall'editore eseguendo lo script seguente nel database dell'editore:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Eliminare i metadati della sottoscrizione dal sottoscrittore. Eseguire lo script seguente nel database di sottoscrizione nell'istanza del sottoscrittore:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Eliminare forzatamente tutti gli oggetti di replica dal server di pubblicazione eseguendo lo script seguente nel database pubblicato:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Rilasciare forzatamente il vecchio distributore dall'istanza primaria originale (se il failover a un vecchio primario che in precedenzare aveva un distributore). Eseguire lo script seguente nel database master nell'istanza gestita del server di distribuzione precedente:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Se la replica geografica è abilitata in un'istanza del **sottoscrittore** in un gruppo di failover, la pubblicazione deve essere configurata per connettersi all'endpoint del listener del gruppo di failover per l'istanza gestita del sottoscrittore. In caso di failover, l'azione successiva dell'amministratore dell'istanza gestita dipende dal tipo di failover che si è verificato: 

- Per un failover senza perdita di dati, la replica continuerà a funzionare dopo il failover. 
- Per un failover con perdita di dati, funzionerà anche la replica. Verranno replicate nuovamente le modifiche perse. 
- Per un failover con perdita di dati, ma la perdita di dati non rientra nel periodo di conservazione del database di distribuzione, l'amministratore dell'istanza gestita dovrà reinizializzare il database di sottoscrizione. 

## <a name="next-steps"></a>Passaggi successivi

- [Configurare la replica tra un server di pubblicazione MI e un server di sottoscrizione](replication-with-sql-database-managed-instance.md)
- [Configurare la replica tra un server di pubblicazione MI, un server di distribuzione MI e un server di sottoscrizione di SQL ServerConfigure replication between an MI publisher, MI distributor, and SQL Server subscriber](sql-database-managed-instance-configure-replication-tutorial.md)
- [Creare una pubblicazione](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
- [Creare una sottoscrizione push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) usando il nome del server di database SQL di Azure come sottoscrittore (ad esempio `N'azuresqldbdns.database.windows.net`) e il nome del database SQL di Azure come database di destinazione (ad esempio **AdventureWorks**). )


Per altre informazioni sulla configurazione della replica transazionale, vedere le esercitazioni seguenti:For more information about configuring transactional replication, see the following tutorials:



## <a name="see-also"></a>Vedere anche  

- [Replica con un MI e un gruppo di failover](sql-database-managed-instance-transact-sql-information.md#replication)
- [Replica nel database SQL](replication-to-sql-database.md)
- [Replica nell'istanza gestita](replication-with-sql-database-managed-instance.md)
- [Creare una pubblicazioneCreate a Publication](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Creazione di una sottoscrizione push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Tipi di replica](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitoraggio (replica)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inizializzazione di una sottoscrizione](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  

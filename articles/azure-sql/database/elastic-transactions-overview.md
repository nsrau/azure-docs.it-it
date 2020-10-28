---
title: Transazioni distribuite tra database cloud (anteprima)
description: Panoramica delle transazioni di database elastico con database SQL di Azure e Azure SQL Istanza gestita.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 5504b9bc87f78682ff584006255d4e75e5e69fa7
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793348"
---
# <a name="distributed-transactions-across-cloud-databases-preview"></a>Transazioni distribuite tra database cloud (anteprima)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Le transazioni di database elastico per il database SQL di Azure e Istanza gestita SQL di Azure consentono di eseguire transazioni che si estendono su più database. Le transazioni di database elastico sono disponibili per le applicazioni .NET che usano ADO.NET e si integrano con la nota esperienza di programmazione usando le classi [System. Transaction](/dotnet/api/system.transactions) . Per ottenere la libreria, vedere [Microsoft .NET Framework 4.6.1 (programma di installazione Web)](https://www.microsoft.com/download/details.aspx?id=49981).
Inoltre, per Istanza gestita transazioni distribuite sono disponibili in [Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql).

In locale, questo scenario richiede in genere l'esecuzione di Microsoft Distributed Transaction Coordinator (MSDTC). Poiché MSDTC non è disponibile per l'applicazione Platform-as-a-Service in Azure, la possibilità di coordinare le transazioni distribuite è stata ora integrata direttamente nel database SQL o in Istanza gestita. Le applicazioni possono connettersi a qualsiasi database per avviare le transazioni distribuite e uno dei database o dei server coordina in modo trasparente la transazione distribuita, come illustrato nella figura seguente.

In questo documento i termini "transazioni distribuite" e "transazioni di database elastico" sono considerati sinonimi e verranno usati in modo interscambiabile.

  ![Transazioni distribuite con il database SQL di Azure tramite transazioni di database elastico ][1]

## <a name="common-scenarios"></a>Scenari comuni

Le transazioni di database elastico consentono alle applicazioni di apportare modifiche atomiche ai dati archiviati in diversi database. L'anteprima è incentrata sulle esperienze di sviluppo sul lato client in C# e .NET. Un'esperienza sul lato server (codice scritto in stored procedure o script lato server) tramite [Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) è disponibile solo per istanza gestita.
> [!IMPORTANT]
> In anteprima, l'esecuzione di transazioni di database elastico tra database SQL di Azure e Istanza gestita SQL di Azure non è attualmente supportata. La transazione di database elastico può estendersi solo tra set di database SQL o set di istanze gestite.

Le transazioni di database elastico sono destinate agli scenari seguenti:

* Applicazioni a più database in Azure: con questo scenario, i dati vengono partizionati verticalmente tra più database nel database SQL o Istanza gestita in modo che i diversi tipi di dati risiedano in database diversi. Alcune operazioni richiedono modifiche ai dati, conservate in due o più database. L'applicazione usa le transazioni di database elastico per coordinare le modifiche tra i database e garantire l'atomicità.
* Applicazioni di database partizionati in Azure: con questo scenario, il livello dati usa la [libreria client dei database elastici](elastic-database-client-library.md) o il partizionamento automatico per partizionare orizzontalmente i dati in molti database nel database SQL o istanza gestita. Un caso d'uso significativo è la necessità di eseguire modifiche atomiche per un'applicazione multi-tenant partizionata, quando le modifiche si estendono a più tenant. Si pensi ad esempio a un trasferimento da un tenant all'altro, entrambi residenti in database diversi. Un secondo caso è il partizionamento orizzontale con granularità fine per soddisfare le esigenze di capacità per un tenant di grandi dimensioni, che a sua volta implica in genere che alcune operazioni atomiche debbano estendersi su più database usati per lo stesso tenant. Un terzo caso sono gli aggiornamenti atomici per fare riferimento ai dati replicati tra i database. Le operazioni atomiche e transazionali che seguono questo schema ora possono essere coordinate in diversi database usando l'anteprima.
  Le transazioni di database elastico utilizzano il commit a due fasi per garantire l'atomicità delle transazioni tra i database. Si tratta di una soluzione ideale per le transazioni che coinvolgono meno di 100 database alla volta all'interno di una singola transazione. Questi limiti non vengono applicati, ma è necessario aspettarsi che le prestazioni e le percentuali di successo delle transazioni di database elastico vengano ridotte quando si superano questi limiti.

## <a name="installation-and-migration"></a>Installazione e migrazione

Le funzionalità per le transazioni di database elastico vengono fornite tramite aggiornamenti alle librerie .NET System.Data.dll e System.Transactions.dll. Le DLL assicurano che il protocollo 2PC venga usato dove necessario per garantire l'atomicità. Per iniziare a sviluppare applicazioni che usano transazioni di database elastico, installare [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) o una versione successiva. Quando si esegue una versione precedente di .NET Framework, le transazioni non verranno alzate di livello a una transazione distribuita e verrà generata un'eccezione.

Dopo l'installazione, è possibile utilizzare le API delle transazioni distribuite in System. Transactions con le connessioni al database SQL e Istanza gestita. Se si dispone di applicazioni MSDTC esistenti che utilizzano queste API, ricompilare le applicazioni esistenti per .NET 4,6 dopo l'installazione di 4.6.1 Framework. Se i progetti sono destinati a .NET 4,6, useranno automaticamente le DLL aggiornate dalla nuova versione del Framework e le chiamate API delle transazioni distribuite in combinazione con le connessioni al database SQL o a Istanza gestita avranno ora esito positivo.

Tenere presente che le transazioni di database elastico non richiedono l'installazione di MSDTC. Le transazioni di database elastico sono invece gestite direttamente da e all'interno del servizio. Questo semplifica notevolmente gli scenari cloud poiché una distribuzione di MSDTC non è necessaria per l'utilizzo di transazioni distribuite con il database SQL o Istanza gestita. La sezione 4 illustra in dettaglio come distribuire le transazioni di database elastico e la versione di .NET Framework necessaria insieme alle proprie applicazioni cloud in Azure.

## <a name="net-installation-for-azure-cloud-services"></a>Installazione di .NET per i servizi cloud di Azure

Azure offre diverse soluzioni per l'hosting di applicazioni .NET. Per un confronto delle diverse soluzioni, vedere [Confronto tra Azure App Service, Servizi cloud e Macchine virtuali di Azure](/azure/architecture/guide/technology-choices/compute-decision-tree). Se il sistema operativo guest della soluzione è precedente alla versione 4.6.1 di .NET richiesta per le transazioni elastiche, è necessario aggiornare il sistema operativo guest alla versione 4.6.1.

Per il servizio app Azure, gli aggiornamenti al sistema operativo guest non sono attualmente supportati. Per le macchine virtuali di Azure, è sufficiente accedere alla macchina virtuale ed eseguire il programma di installazione del framework .NET più recente. Per i servizi cloud di Azure, è necessario includere l'installazione di una versione più recente di .NET nelle attività di avvio della distribuzione. I concetti e i passaggi sono documentati in [Installare .NET in un ruolo del servizio cloud](../../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Si noti che il programma di installazione per .NET 4.6.1 può richiedere più spazio di archiviazione temporaneo durante il processo di bootstrap nei servizi cloud di Azure rispetto al programma di installazione per .NET 4.6. Per garantire una corretta installazione, è necessario aumentare l'archiviazione temporanea per il servizio cloud di Azure nel file ServiceDefinition.csdef nella sezione LocalResources e le impostazioni di ambiente dell'attività di avvio, come illustrato nell'esempio seguente:

```xml
    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
```

## <a name="net-development-experience"></a>Esperienza di sviluppo .NET

### <a name="multi-database-applications"></a>Applicazioni con più database

L'esempio di codice seguente usa l'esperienza di programmazione familiare con System.Transactions per .NET. La classe TransactionScope definisce una transazione di ambiente in .NET. Una "transazione di ambiente" risiede nel thread corrente. Tutte le connessioni aperte all'interno di TransactionScope partecipano alla transazione. Se partecipano diversi database, la transazione viene automaticamente elevata a transazione distribuita. Il risultato della transazione viene controllato impostando l'ambito da completare per indicare un commit.

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

### <a name="sharded-database-applications"></a>Applicazioni di database partizionato

Le transazioni di database elastico per database SQL e Istanza gestita supportano anche il coordinamento delle transazioni distribuite in cui si usa il metodo OpenConnectionForKey della libreria client dei database elastici per aprire le connessioni per un livello dati con scalabilità orizzontale. Considerare i casi in cui è necessario garantire la coerenza delle transazioni per le modifiche dei diversi valori delle chiavi di partizionamento orizzontale. Le connessioni alle partizioni che ospitano i diversi valori delle chiavi di partizionamento orizzontale sono negoziate tramite OpenConnectionForKey. In generale, le connessioni possono essere stabilite a partizioni diverse, in modo tale che per assicurare garanzie transazionali sia richiesta una transazione distribuita.
Il seguente esempio di codice illustra questo approccio. Si presuppone che venga usata una variabile denominata shardmap per rappresentare un mappa partizioni dalla libreria client dei database elastici:

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

## <a name="transact-sql-development-experience"></a>Esperienza di sviluppo Transact-SQL

Le transazioni distribuite sul lato server che usano Transact-SQL sono disponibili solo per Istanza gestita SQL di Azure. La transazione distribuita può essere eseguita solo tra istanze gestite che appartengono allo stesso [gruppo di trust server](../managed-instance/server-trust-group-overview.md). In questo scenario, le istanze gestite devono usare il [server collegato](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine#TsqlProcedure) per farvi riferimento.

Nel codice Transact-SQL di esempio seguente viene utilizzata l'istruzione [Begin Distributed Transaction](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) per avviare la transazione distribuita.

```Transact-SQL

    -- Configure the Linked Server
    -- Add one Azure SQL Managed Instance as Linked Server
    EXEC sp_addlinkedserver
        @server='RemoteServer', -- Linked server name
        @srvproduct='',
        @provider='sqlncli', -- SQL Server Native Client
        @datasrc='managed-instance-server.46e7afd5bc81.database.windows.net' -- Managed Instance endpoint

    -- Add credentials and options to this Linked Server
    EXEC sp_addlinkedsrvlogin
        @rmtsrvname = 'RemoteServer', -- Linked server name
        @useself = 'false',
        @rmtuser = '<login_name>',         -- login
        @rmtpassword = '<secure_password>' -- password

    USE AdventureWorks2012;
    GO
    SET XACT_ABORT ON;
    GO
    BEGIN DISTRIBUTED TRANSACTION;
    -- Delete candidate from local instance.
    DELETE AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    -- Delete candidate from remote instance.
    DELETE RemoteServer.AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    COMMIT TRANSACTION;
    GO
```

## <a name="combining-net-and-transact-sql-development-experience"></a>Combinazione dell'esperienza di sviluppo .NET e Transact-SQL

Le applicazioni .NET che usano le classi System. Transaction possono combinare la classe TransactionScope con l'istruzione Transact-SQL BEGIN DISTRIBUted TRANSACTION. All'interno di TransactionScope, la transazione interna che esegue inizia la transazione ditributa verrà innalzata in modo esplicito alla transazione distribuita. Inoltre, quando il secondo SqlConnecton viene aperto all'interno dell'oggetto TransactionScope, verrà innalzato in modo implicito alla transazione distribuita. Una volta avviata la transazione distribuita, tutte le richieste di transazioni successive, a seconda che provengano da .NET o Transact-SQL, verranno aggiunte alla transazione distribuita padre. Di conseguenza, tutti gli ambiti di transazione annidati avviati dall'istruzione BEGIN finiranno nella stessa transazione e le istruzioni COMMIT/ROLLBACK avranno effetto sul risultato complessivo:
 * L'istruzione COMMIT non avrà alcun effetto sull'ambito di transazione avviato dall'istruzione BEGIN, ovvero non verrà eseguito il commit di alcun risultato prima che il metodo complete () venga richiamato sull'oggetto TransactionScope. Se l'oggetto TransactionScope viene eliminato prima del completamento, viene eseguito il rollback di tutte le modifiche apportate all'interno dell'ambito.
 * Con l'istruzione ROLLBACK verrà eseguito il rollback dell'intero TransactionScope. Eventuali tentativi di integrazione di nuove transazioni in TransactionScope avranno esito negativo, oltre a tentare di richiamare complete () sull'oggetto TransactionScope.

Di seguito è riportato un esempio in cui la transazione viene innalzata in modo esplicito alla transazione distribuita con Transact-SQL.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
        
            // Transaction is here promoted to distributed by BEGIN statement
            //
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            // ...
        }
     
        using (SqlConnection conn2 = new SqlConnection(DB1_ConnectionString)
        {
            conn2.Open();
            // ...
        }
        
        s.Complete();
    }
```

Nell'esempio seguente viene illustrata una transazione innalzata in modo implicito alla transazione distribuita una volta che il secondo SqlConnecton è stato avviato all'interno di TransactionScope.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
            // ...
        }
        
        using (SqlConnection conn = new SqlConnection(DB1_ConnectionString)
        {
            // Because this is second SqlConnection within TransactionScope transaction is here implicitly promoted distributed.
            //
            conn.Open(); 
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            Helper.ExecuteNonQueryOnOpenConnection(conn, lsQuery);
            // ...
        }
        
        s.Complete();
    }
```

## <a name="transactions-across-multiple-servers-for-azure-sql-database"></a>Transazioni tra più server per il database SQL di Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato da Database SQL di Azure, ma tutte le attività di sviluppo future sono incentrate sul modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici.

Le transazioni di database elastico sono supportate in server diversi nel database SQL di Azure. Quando le transazioni attraversano i limiti del server, è necessario immettere prima i server partecipanti in una relazione di comunicazione reciproca. Dopo aver stabilito la relazione di comunicazione, qualsiasi database in uno dei due server può partecipare alle transazioni elastiche con i database dell'altro server. Con le transazioni che si estendono su più di due server, è necessario disporre di una relazione di comunicazione per qualsiasi coppia di server.

Usare i seguenti cmdlet di PowerShell per la gestione delle relazioni di comunicazione tra server per le transazioni di database elastici:

* **New-AzSqlServerCommunicationLink** : usare questo cmdlet per creare una nuova relazione di comunicazione tra due server nel database SQL di Azure. La relazione è simmetrica, ovvero entrambi i server possono avviare le transazioni con l'altro server.
* **Get-AzSqlServerCommunicationLink** : usare questo cmdlet per recuperare le relazioni di comunicazione esistenti e le relative proprietà.
* **Remove-AzSqlServerCommunicationLink** : usare questo cmdlet per rimuovere una relazione di comunicazione esistente.

## <a name="transactions-across-multiple-servers-for-azure-sql-managed-instance"></a>Transazioni tra più server per Istanza gestita SQL di Azure

Le transazioni distribuite sono supportate in server diversi in Azure SQL Istanza gestita. Quando le transazioni attraversano Istanza gestita limiti, le istanze partecipanti devono prima essere immesse in una relazione di sicurezza e comunicazione reciproca. Questa operazione viene eseguita creando un [gruppo di trust del server](../managed-instance/server-trust-group-overview.md), che può essere eseguito in portale di Azure. Se le istanze gestite non si trovano nella stessa rete virtuale, è necessario configurare il [peering di rete virtuale](../../virtual-network/virtual-network-peering-overview.md) e le regole in ingresso e in uscita del gruppo di sicurezza di rete devono consentire le porte 5024 e 11000-12000 in tutte le reti virtuali coinvolte.

  ![Gruppi di attendibilità del server nel portale di Azure][3]

Il diagramma seguente mostra il gruppo di trust del server con istanze gestite che possono eseguire transazioni distribuite con .NET o Transact-SQL.

  ![Transazioni distribuite con Istanza gestita SQL di Azure con transazioni elastiche][2]

## <a name="monitoring-transaction-status"></a>Monitoraggio dello stato delle transazioni

Utilizzare le viste a gestione dinamica (DMV) per monitorare lo stato e lo stato delle transazioni di database elastico in corso. Tutte le DMV correlate alle transazioni sono rilevanti per le transazioni distribuite nel database SQL e Istanza gestita. È possibile trovare l'elenco corrispondente di DMV di seguito: [Funzioni e viste a gestione dinamica relative alle transazioni (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql).

Queste viste a gestione dinamica sono particolarmente utili:

* **sys.dm\_tran\_active\_transactions** : elenca le transazioni attualmente attive e il relativo stato. La colonna UOW (unità di lavoro) può identificare le diverse transazioni figlio che appartengono alla stessa transazione distribuita. Tutte le transazioni all'interno della stessa transazione distribuita hanno lo stesso valore di unità di lavoro. Per ulteriori informazioni, vedere la [documentazione DMV](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql).
* **sys.dm\_tran\_database\_transactions** : fornisce altre informazioni sulle transazioni, ad esempio la posizione della transazione nel log. Per ulteriori informazioni, vedere la [documentazione DMV](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql).
* **sys.dm\_tran\_locks** : fornisce informazioni sui blocchi attualmente mantenuti dalle transazioni in corso. Per ulteriori informazioni, vedere la [documentazione DMV](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql).

## <a name="limitations"></a>Limitazioni

Le limitazioni seguenti si applicano attualmente alle transazioni di database elastico nel database SQL:

* Sono supportate solo le transazioni tra database nel database SQL. Altri provider di risorse [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) e database esterni al database SQL non possono partecipare alle transazioni di database elastico. Ciò significa che le transazioni di database elastico non possono estendersi tra SQL Server locali e il database SQL di Azure. Per le transazioni distribuite in locale, continuare a usare MSDTC.
* Sono supportate solo le transazioni coordinate tramite client da un'applicazione .NET. Il supporto sul lato server per T-SQL, ad esempio BEGIN DISTRIBUTED TRANSACTION, è pianificato, ma non ancora disponibile.
* Le transazioni tra i servizi WCF non sono supportate. Ad esempio, si dispone di un metodo del servizio WCF che esegue una transazione. Se si racchiude la chiamata all'interno di un ambito della transazione, essa avrà esito negativo come eccezione [System.ServiceModel.ProtocolException](/dotnet/api/system.servicemodel.protocolexception).

Le seguenti limitazioni si applicano attualmente alle transazioni distribuite in Istanza gestita:

* Sono supportate solo le transazioni tra i database in Istanza gestita. Altri provider di risorse [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) e database all'esterno di Azure SQL istanza gestita non possono partecipare alle transazioni distribuite. Ciò significa che le transazioni distribuite non possono estendersi tra SQL Server locali e Istanza gestita SQL di Azure. Per le transazioni distribuite in locale, continuare a usare MSDTC.
* Le transazioni tra i servizi WCF non sono supportate. Ad esempio, si dispone di un metodo del servizio WCF che esegue una transazione. Se si racchiude la chiamata all'interno di un ambito della transazione, essa avrà esito negativo come eccezione [System.ServiceModel.ProtocolException](/dotnet/api/system.servicemodel.protocolexception).
* Il Istanza gestita SQL di Azure deve far parte di un [gruppo di attendibilità del server](../managed-instance/server-trust-group-overview.md) per poter partecipare alla transazione distribuita.
* Le limitazioni dei [gruppi di attendibilità del server](../managed-instance/server-trust-group-overview.md) influiscono sulle transazioni distribuite.
* Le istanze gestite che fanno parte di transazioni distribuite devono avere connettività su endpoint privati (usando un indirizzo IP privato della rete virtuale in cui vengono distribuite) ed è necessario farvi riferimento a vicenda usando FQDN privati. Le applicazioni client possono utilizzare le transazioni distribuite in endpoint privati. Inoltre, nei casi in cui Transact-SQL utilizza server collegati che fanno riferimento a endpoint privati, le applicazioni client possono utilizzare le transazioni distribuite anche negli endpoint pubblici. Questa limitazione è illustrata nel diagramma seguente.
  ![Limitazione della connettività dell'endpoint privato][4]
## <a name="next-steps"></a>Passaggi successivi

* Per domande, contattare Microsoft nella pagina Domande e risposte [&per il database SQL](/answers/topics/azure-sql-database.html).
* Per le richieste di funzionalità, aggiungerle al [Forum dei commenti e suggerimenti sul database SQL](https://feedback.azure.com/forums/217321-sql-database/) o [istanza gestita Forum](https://feedback.azure.com/forums/915676-sql-managed-instance).



<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
[2]: ./media/elastic-transactions-overview/sql-mi-distributed-transactions.png
[3]: ./media/elastic-transactions-overview/server-trust-groups-azure-portal.png
[4]: ./media/elastic-transactions-overview/managed-instance-distributed-transactions-private-endpoint-limitations.png

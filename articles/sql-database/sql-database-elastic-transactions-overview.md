---
title: Transazioni distribuite in database cloud
description: Panoramica delle transazioni di database elastico con il database SQL di Azure
services: sql-database
documentationcenter: 
author: torsteng
manager: jhubbard
editor: torsteng
ms.assetid: e14df7a3-7788-4cfb-bcd1-7ad6433ef1f9
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: sql-database
ms.date: 05/27/2016
ms.author: torsteng
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: ceb2d7a28cc416186f3faf1dc5228fce161c34c5
ms.lasthandoff: 04/10/2017


---
# <a name="distributed-transactions-across-cloud-databases"></a>Transazioni distribuite in database cloud
Le transazioni di database elastico per il database SQL di Azure consentono di eseguire transazioni estese a più database nel database SQL. Le transazioni di database elastico per il database SQL sono disponibili per le applicazioni .NET tramite ADO .NET e si integrano con la familiare esperienza di programmazione usando le classi [System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) . Per ottenere la libreria, vedere [Microsoft .NET Framework 4.6.1 (programma di installazione Web)](https://www.microsoft.com/download/details.aspx?id=49981).

In locale questo scenario richiede in genere l'esecuzione di Microsoft Distributed Transaction Coordinator (MSDTC). Poiché MSDTC non è disponibile per le applicazioni PaaS (piattaforma distribuita come servizio) in Azure, ora la possibilità di coordinare le transazioni distribuite è integrata direttamente nel database SQL. Le applicazioni possono connettersi a qualsiasi database SQL per avviare transazioni distribuite e uno dei database coordina in modo trasparente la transazione distribuita, come illustrato nella figura seguente. 

  ![Transazioni distribuite con il database SQL di Azure tramite transazioni di database elastico ][1]

## <a name="common-scenarios"></a>Scenari comuni
Le transazioni di database elastico per il database SQL consentono alle applicazioni di apportare modifiche atomiche ai dati archiviati in diversi database SQL. L'anteprima è incentrata sulle esperienze di sviluppo sul lato client in C# e .NET. Un'esperienza sul lato server con T-SQL è prevista per un momento successivo.  
Le transazioni di database elastico sono destinate agli scenari seguenti:

* Applicazioni con più database in Azure: con questo scenario i dati sono partizionati verticalmente in più database nel database SQL, in modo che diversi tipi di dati risiedano in database diversi. Alcune operazioni richiedono modifiche ai dati mantenuti in due o più database. L'applicazione usa le transazioni di database elastico per coordinare le modifiche tra i database e garantire l'atomicità.
* Applicazioni di database partizionato in Azure: con questo scenario il livello dati usa la [libreria client dei database elastici](sql-database-elastic-database-client-library.md) o il partizionamento automatico per eseguire il partizionamento orizzontale dei dati in molti database nel database SQL. Un caso d'uso significativo è la necessità di eseguire modifiche atomiche per un'applicazione multi-tenant partizionata, quando le modifiche si estendono a più tenant. Si pensi ad esempio a un trasferimento da un tenant all'altro, entrambi residenti in database diversi. Un secondo caso è il partizionamento orizzontale con granularità fine per soddisfare le esigenze di capacità per un tenant di grandi dimensioni che, a sua volta, implica in genere la necessità che alcune operazioni atomiche siano estese a diversi database usati per lo stesso tenant. Un terzo caso sono gli aggiornamenti atomici per fare riferimento ai dati replicati tra i database. Le operazioni atomiche e transazionali che seguono questo schema ora possono essere coordinate in diversi database usando l'anteprima.
  Le transazioni di database elastico usano il protocollo 2PC per garantire l'atomicità delle transazioni nei database. È una scelta ideale per le transazioni che coinvolgono meno di 100 database alla volta in una singola transazione. Questi limiti non vengono applicati, ma quando vengono superati è consigliabile prevedere una riduzione delle prestazioni e delle percentuali di riuscita delle transazioni di database elastico.

## <a name="installation-and-migration"></a>Installazione e migrazione
Le funzionalità per le transazioni di database elastico nel database SQL vengono fornite tramite aggiornamenti alle librerie .NET System.Data.dll e System.Transactions.dll. Le DLL assicurano che il protocollo 2PC venga usato dove necessario per garantire l'atomicità. Per iniziare a sviluppare applicazioni che usano transazioni di database elastico, installare [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) o una versione successiva. Quando si esegue una versione precedente di .NET Framework, le transazioni non verranno alzate di livello a una transazione distribuita e verrà generata un'eccezione.

Dopo l'installazione, è possibile usare l'API di transazione distribuita in System.Transactions con connessioni al database SQL. Se sono disponibili applicazioni di MSDTC che usano queste API, è sufficiente ricompilare le applicazioni esistenti per .NET 4.6 dopo avere installato la versione 4.6.1 di Framework. Se i progetti sono destinati a .NET 4.6, verranno usate automaticamente le DLL aggiornate della nuova versione di Framework e le chiamate API di transazione distribuita in combinazione con le connessioni al database SQL ora riusciranno.

Tenere presente che le transazioni di database elastico non richiedono l'installazione di MSDTC. Al contrario, le transazioni di database elastico sono gestite direttamente e internamente dal database SQL. In questo modo gli scenari basati sul cloud sono notevolmente più semplici, perché non è necessaria una distribuzione di MSDTC per usare transazioni distribuite con il database SQL. La sezione 4 illustra in dettaglio come distribuire le transazioni di database elastico e la versione di .NET Framework necessaria insieme alle proprie applicazioni cloud in Azure.

## <a name="development-experience"></a>Esperienza di sviluppo
### <a name="multi-database-applications"></a>Applicazioni con più database
L'esempio di codice seguente usa l'esperienza di programmazione familiare con System.Transactions per .NET. La classe TransactionScope definisce una transazione di ambiente in .NET. Una "transazione di ambiente" risiede nel thread corrente. Tutte le connessioni aperte all'interno di TransactionScope partecipano alla transazione. Se partecipano diversi database, la transazione viene automaticamente elevata a transazione distribuita. Il risultato della transazione viene controllato impostando l'ambito da completare per indicare un commit.

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

### <a name="sharded-database-applications"></a>Applicazioni di database partizionato
Le transazioni di database elastico per il database SQL supportano anche il coordinamento delle transazioni distribuite per cui si usa il metodo OpenConnectionForKey della libreria client dei database elastici per aprire le connessioni per un livello dati con scalabilità orizzontale. Considerare i casi in cui è necessario garantire la coerenza delle transazioni per le modifiche dei diversi valori delle chiavi di partizionamento orizzontale. Le connessioni alle partizioni che ospitano i diversi valori delle chiavi di partizionamento orizzontale sono negoziate tramite OpenConnectionForKey. In generale, le connessioni possono essere stabilite a partizioni diverse, in modo tale che per assicurare garanzie transazionali sia richiesta una transazione distribuita. Il seguente esempio di codice illustra questo approccio. Si presuppone che venga usata una variabile denominata shardmap per rappresentare un mappa partizioni dalla libreria client dei database elastici:

    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }


## <a name="net-installation-for-azure-cloud-services"></a>Installazione di .NET per i servizi cloud di Azure
Azure offre diverse soluzioni per l'hosting di applicazioni .NET. Per un confronto delle diverse soluzioni, vedere [Confronto tra Azure App Service, Servizi cloud e Macchine virtuali di Azure](../app-service-web/choose-web-site-cloud-service-vm.md). Se il sistema operativo guest della soluzione è precedente alla versione 4.6.1 di .NET richiesta per le transazioni elastiche, è necessario aggiornare il sistema operativo guest alla versione 4.6.1. 

Per i servizi app di Azure, gli aggiornamenti del sistema operativo guest non sono attualmente supportati. Per le macchine virtuali di Azure, è sufficiente accedere alla macchina virtuale ed eseguire il programma di installazione del framework .NET più recente. Per i servizi cloud di Azure, è necessario includere l'installazione di una versione più recente di .NET nelle attività di avvio della distribuzione. I concetti e i passaggi sono documentati in [Installare .NET in un ruolo del servizio cloud](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Si noti che il programma di installazione per .NET 4.6.1 può richiedere più spazio di archiviazione temporaneo durante il processo di bootstrap nei servizi cloud di Azure rispetto al programma di installazione per .NET 4.6. Per garantire una corretta installazione, è necessario aumentare l'archiviazione temporanea per il servizio cloud di Azure nel file ServiceDefinition.csdef nella sezione LocalResources e le impostazioni di ambiente dell'attività di avvio, come illustrato nell'esempio seguente:

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

## <a name="transactions-across-multiple-servers"></a>Transazioni tra più server
Le transazioni di database elastici sono supportate in diversi server logici del database SQL di Azure. Quando le transazioni oltrepassano i limiti dei server logici, i server partecipanti devono essere innanzitutto inseriti in una relazione di comunicazione reciproca. Dopo aver stabilito la relazione di comunicazione, qualsiasi database in uno dei due server può partecipare alle transazioni elastiche con i database dell'altro server. Con le transazioni che si estendono su più di due server logici, è necessaria una relazione di comunicazione per ogni coppia di server logici.

Usare i seguenti cmdlet di PowerShell per la gestione delle relazioni di comunicazione tra server per le transazioni di database elastici:

* **New AzureRmSqlServerCommunicationLink**: usare questo cmdlet per creare una nuova relazione di comunicazione tra due server logici in un database SQL di Azure. La relazione è simmetrica, ovvero entrambi i server possono avviare le transazioni con l'altro server.
* **Get-AzureRmSqlServerCommunicationLink**: usare questo cmdlet per recuperare le relazioni di comunicazione esistenti e le relative proprietà.
* **Remove-AzureRmSqlServerCommunicationLink**: usare questo cmdlet per rimuovere una relazione di comunicazione esistente. 

## <a name="monitoring-transaction-status"></a>Monitoraggio dello stato delle transazioni
Per monitorare lo stato e l'avanzamento delle transazioni di database elastico in corso, usare viste a gestione dinamica (DMV) nel database SQL. Tutte le viste a gestione dinamica correlate alle transazioni sono rilevanti per le transazioni distribuite nel database SQL. È possibile trovare l'elenco delle viste a gestione dinamica corrispondente in [Funzioni e viste a gestione dinamica relative alle transazioni (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

Queste viste a gestione dinamica sono particolarmente utili:

* **sys.dm\_tran\_active\_transactions**: elenca le transazioni attualmente attive e il relativo stato. La colonna UOW (unità di lavoro) può identificare le diverse transazioni figlio che appartengono alla stessa transazione distribuita. Tutte le transazioni all'interno della stessa transazione distribuita hanno lo stesso valore di unità di lavoro. Per altri dettagli, vedere la [documentazione sulle viste a gestione dinamica](https://msdn.microsoft.com/library/ms174302.aspx) .
* **sys.dm\_tran\_database\_transactions**: fornisce altre informazioni sulle transazioni, ad esempio la posizione della transazione nel log. Per altri dettagli, vedere la [documentazione sulle viste a gestione dinamica](https://msdn.microsoft.com/library/ms186957.aspx) .
* **sys.dm\_tran\_locks**: fornisce informazioni sui blocchi attualmente mantenuti dalle transazioni in corso. Per altri dettagli, vedere la [documentazione sulle viste a gestione dinamica](https://msdn.microsoft.com/library/ms190345.aspx) .

## <a name="limitations"></a>Limitazioni
Per le transazioni di database elastico nel database SQL si applicano attualmente le limitazioni seguenti:

* Sono supportate solo le transazioni tra database nel database SQL. Altri provider di risorse [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) e database al di fuori del database SQL non possono partecipare alle transazioni di database elastico. Ciò significa che le transazioni di database elastico non possono estendersi in locale al database SQL Server e al database SQL di Azure. Per le transazioni distribuite in locale, continuare a usare MSDTC. 
* Sono supportate solo le transazioni coordinate tramite client da un'applicazione .NET. Il supporto sul lato server per T-SQL, ad esempio BEGIN DISTRIBUTED TRANSACTION, è pianificato, ma non ancora disponibile. 
* Le transazioni tra i servizi WCF non sono supportate. Ad esempio, si dispone di un metodo del servizio WCF che esegue una transazione. Se si racchiude la chiamata all'interno di un ambito della transazione, essa avrà esito negativo come eccezione [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Passaggi successivi
Se ancora non si usano le funzionalità del database elastico per le applicazioni Azure Vedere la [mappa della documentazione](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/). Se ci sono domande, è possibile visitare il [forum sul database SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) mentre è possibile inserire le richieste di nuove funzionalità nel [forum relativo a commenti e suggerimenti sul database SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png





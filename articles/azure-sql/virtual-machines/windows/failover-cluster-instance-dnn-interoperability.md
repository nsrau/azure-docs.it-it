---
title: Interoperabilità delle funzionalità con SQL Server FCI & DNN
description: "Altre informazioni sulle considerazioni aggiuntive quando si lavora con determinate funzionalità di SQL Server e una risorsa DNN (Distributed Network Name) con un'istanza del cluster di failover in SQL Server in macchine virtuali di Azure. "
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: ca782e9949f990857db408919cac342d7f712d2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91272617"
---
# <a name="feature-interoperability-with-sql-server-fci--dnn"></a>Interoperabilità delle funzionalità con SQL Server FCI & DNN
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Alcune funzionalità di SQL Server si basano su un nome di rete virtuale hardcoded (VNN). Di conseguenza, quando si usa la risorsa nome di rete distribuita (DNN) con l'istanza del cluster di failover e SQL Server in macchine virtuali di Azure, è necessario tenere presenti alcune considerazioni aggiuntive. 

In questo articolo si apprenderà come configurare l'alias di rete quando si usa la risorsa DNN e quali funzionalità di SQL Server richiedono considerazioni aggiuntive.

## <a name="create-network-alias-fci"></a>Crea alias di rete (FCI)

Alcuni componenti lato server si basano su un valore VNN hardcoded e richiedono un alias di rete che esegue il mapping di VNN al nome DNS DNN per funzionare correttamente. Seguire i passaggi descritti in [creare un alias del server](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client) per creare un alias che esegue il mapping di VNN al nome DNS DNN. 

Per un'istanza predefinita, è possibile eseguire il mapping diretto del VNN al nome DNS DNN, in modo che VNN = DNN nome DNS.
Se, ad esempio, il nome di VNN è `FCI1` , `MSSQLSERVER` il nome dell'istanza è e DNN è `FCI1DNN` (client connessi in precedenza a `FCI` e a questo punto si connettono a `FCI1DNN` ), quindi eseguire il mapping tra VNN e `FCI1` DNN `FCI1DNN` . 

Per un'istanza denominata, è necessario eseguire il mapping degli alias di rete per l'istanza completa, in modo tale che `VNN\Instance`  =  `DNN\Instance` . Se, ad esempio, il nome di VNN è `FCI1` , `instA` il nome dell'istanza è e DNN è `FCI1DNN` (client connessi in precedenza a `FCI1\instA` e a questo punto si connettono a `FCI1DNN\instaA` ), quindi eseguire il mapping tra VNN e `FCI1\instaA` DNN `FCI1DNN\instaA` . 



## <a name="client-drivers"></a>Driver client

Per i driver ODBC, OLEDB, ADO.NET, JDBC, PHP e Node.js, gli utenti devono specificare in modo esplicito il nome DNS DNN come nome del server nella stringa di connessione. Per garantire una connettività rapida al failover, aggiungere `MultiSubnetFailover=True` alla stringa di connessione se il client SQL lo supporta. 

## <a name="tools"></a>Strumenti

Gli utenti di [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [SQLCMD](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is)e [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) devono specificare in modo esplicito il nome DNS DNN come nome del server nella stringa di connessione. 

## <a name="availability-groups-and-fci"></a>Gruppi di disponibilità e FCI

È possibile configurare un gruppo di disponibilità Always On usando un'istanza del cluster di failover come una delle repliche. In questa configurazione, l'URL dell'endpoint del mirroring per la replica FCI deve usare l'istanza FCI DNN. Analogamente, se l'istanza FCI viene utilizzata come replica di sola lettura, il routing di sola lettura alla replica FCI deve utilizzare la DNN FCI. 

Il formato dell'endpoint del mirroring è: `ENDPOINT_URL = 'TCP://<DNN DNS name>:<mirroring endpoint port>'` . 

Se, ad esempio, il nome DNS di DNN è `dnnlsnr` e `5022` è la porta dell'endpoint del mirroring dell'istanza FCI, il frammento di codice Transact-SQL (T-SQL) per creare l'URL dell'endpoint sarà simile al seguente: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

Analogamente, il formato per l'URL di routing di sola lettura è: `TCP://<DNN DNS name>:<SQL Server instance port>` . 

Ad esempio, se il nome DNS di DNN è `dnnlsnr` e `1444` è la porta usata dalla destinazione di sola lettura SQL Server FCI, il frammento di codice T-SQL per creare l'URL del routing di sola lettura sarà simile al seguente: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

È possibile omettere la porta nell'URL se è la porta 1433 predefinita. Per un'istanza denominata, configurare una porta statica per l'istanza denominata e specificarla nell'URL di routing di sola lettura.  

## <a name="replication"></a>Replica

La replica include tre componenti: server di pubblicazione, server di distribuzione, Sottoscrittore. Uno di questi componenti può essere un'istanza del cluster di failover di. Poiché la VNN FCI viene utilizzata molto spesso nella configurazione della replica, in modo esplicito e implicito, potrebbe essere necessario un alias di rete che esegue il mapping di VNN a DNN per il funzionamento della replica. 

Continua a usare il nome VNN come nome dell'istanza del cluster di failover nella replica, ma crea un alias di rete nelle situazioni Remote seguenti *prima di configurare la replica*:

| **Componente di replica (FCI con DNN)** | **Componente remoto** | **Mappa alias di rete** | **Server con mappa di rete**| 
|---------|---------|---------|-------- | 
|Editore | Database di distribuzione | Server di pubblicazione VNN nel server di pubblicazione DNN| Database di distribuzione| 
|Database di distribuzione|Sottoscrittore |VNN del server di distribuzione al server di distribuzione DNN| Sottoscrittore | 
|Database di distribuzione|Editore | VNN del server di distribuzione al server di distribuzione DNN | Editore| 
|Sottoscrittore| Database di distribuzione| Sottoscrittore VNN al Sottoscrittore DNN | Database di distribuzione| 

Si supponga, ad esempio, di disporre di un server di pubblicazione configurato come FCI utilizzando DNN in una topologia di replica e che il server di distribuzione sia remoto. In tal caso, creare un alias di rete nel server di distribuzione per eseguire il mapping del server di pubblicazione VNN al DNN del server di pubblicazione: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-in-configuration-manager.png" alt-text="Configurare il nome DNS di DNN come alias di rete usando Gestione configurazione SQL Server." :::

Usare il nome completo dell'istanza per un'istanza denominata, come nell'esempio di immagine seguente: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-named-instance-configuration-manager.png" alt-text="Configurare il nome DNS di DNN come alias di rete usando Gestione configurazione SQL Server." :::

## <a name="database-mirroring"></a>Mirroring del database

È possibile configurare il mirroring del database con un'istanza FCI come partner di mirroring del database. Configurarlo utilizzando [Transact-SQL (T-SQL)](/sql/database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql) invece di SQL Server Management Studio GUI. Con T-SQL si garantisce che l'endpoint del mirroring del database venga creato utilizzando DNN anziché VNN. 

Se, ad esempio, il nome DNS di DNN è `dnnlsnr` e l'endpoint del mirroring del database è 7022, il seguente frammento di codice T-SQL configura il partner di mirroring del database: 

```sql
ALTER DATABASE AdventureWorks
    SET PARTNER =
    'TCP://dnnlsnr:7022'
GO 
```

Per l'accesso client, la proprietà **failover partner** può gestire il failover del mirroring del database, ma non il failover dell'istanza FCI. 

## <a name="msdtc"></a>MSDTC

L'istanza del cluster di failover può partecipare alle transazioni distribuite coordinate da Microsoft Distributed Transaction Coordinator (MSDTC). Sebbene sia il servizio MSDTC cluster sia il servizio MSDTC locale sono supportati con DNN FCI, in Azure è ancora necessario un servizio di bilanciamento del carico per il servizio MSDTC cluster. Il DNN definito nell'istanza FCI non sostituisce il requisito Azure Load Balancer per il servizio MSDTC in cluster in Azure. 

## <a name="filestream"></a>FileStream

Sebbene FileStream sia supportato per un database in un'istanza FCI, l'accesso a FileStream o FileTable tramite API del file System con DNN non è supportato. 

## <a name="linked-servers"></a>Server collegati

L'uso di un server collegato con un DNN FCI è supportato. Usare direttamente DNN per configurare un server collegato o usare un alias di rete per eseguire il mapping di VNN a DNN. 


Per creare un server collegato con il nome DNS DNN per l' `dnnlsnr` istanza denominata, ad esempio `insta1` , usare il comando Transact-SQL (T-SQL) seguente:

```sql
USE [master]   
GO   

EXEC master.dbo.sp_addlinkedserver    
    @server = N'dnnlsnr\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 
```

In alternativa, è possibile creare il server collegato usando invece il nome di rete virtuale (VNN), ma sarà necessario definire un alias di rete per eseguire il mapping di VNN a DNN. 

Per il nome dell'istanza `insta1` , il nome di VNN `vnnname` e il nome di DNN, ad esempio, `dnnlsnr` usare il comando Transact-SQL (T-SQL) seguente per creare un server collegato con VNN:

```sql
USE [master]
GO   

EXEC master.dbo.sp_addlinkedserver   
    @server = N'vnnname\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 

```

Quindi, creare un alias di rete a cui eseguire il mapping `vnnname\insta1` `dnnlsnr\insta1` . 



## <a name="frequently-asked-questions"></a>Domande frequenti


- Quale versione di SQL Server porta il supporto DNN? 

   SQL Server 2019 CU2 e versioni successive.

- Qual è il tempo di failover previsto quando si usa DNN?

   Per DNN, il tempo di failover sarà solo l'ora di failover dell'istanza del cluster di failover, senza aggiunta di tempo, ad esempio il tempo di probe quando si usa Azure Load Balancer.

- Sono previsti requisiti di versione per i client SQL per supportare DNN con OLEDB e ODBC?

   Si consiglia il `MultiSubnetFailover=True` supporto della stringa di connessione per DNN. È disponibile a partire da SQL Server 2012 (11. x).

- Sono necessarie modifiche di configurazione SQL Server per l'uso di DNN? 

   SQL Server non richiede alcuna modifica alla configurazione per l'uso di DNN, ma alcune funzionalità SQL Server potrebbero richiedere una maggiore considerazione. 

- DNN supporta cluster a più subnet?

   Sì. Il cluster associa DNN in DNS con gli indirizzi IP fisici di tutti i nodi del cluster indipendentemente dalla subnet. Il client SQL tenta tutti gli indirizzi IP del nome DNS indipendentemente dalla subnet. 



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere: 

- [Tecnologie cluster di Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [Istanze del cluster di failover di SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)


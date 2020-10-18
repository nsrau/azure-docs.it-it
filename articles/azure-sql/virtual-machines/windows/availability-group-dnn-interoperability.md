---
title: Interoperabilità delle funzionalità con i gruppi di disponibilità e il listener DNN
description: 'Altre informazioni sulle considerazioni aggiuntive quando si lavora con determinate funzionalità di SQL Server e un listener DNN (Distributed Network Name) con un gruppo di disponibilità di Always On in SQL Server in macchine virtuali di Azure. '
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/08/2020
ms.author: mathoma
ms.openlocfilehash: 567e1696bb06b3237c30c45384b8049ff82b5848
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168844"
---
# <a name="feature-interoperability-with-ag-and-dnn-listener"></a>Interoperabilità delle funzionalità con il listener del gruppo di disponibilità e DNN 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Alcune funzionalità di SQL Server si basano su un nome di rete virtuale hardcoded (VNN). Di conseguenza, quando si usa il listener DNN (Distributed Network Name) con il gruppo di disponibilità Always On e SQL Server in macchine virtuali di Azure, è possibile che vengano riportate alcune considerazioni aggiuntive. 

Questo articolo illustra in dettaglio SQL Server funzionalità e l'interoperabilità con il listener del gruppo di disponibilità DNN. 


## <a name="client-drivers"></a>Driver client

Per i driver ODBC, OLEDB, ADO.NET, JDBC, PHP e Node.js, gli utenti devono specificare in modo esplicito il nome del listener DNN e la porta come nome del server nella stringa di connessione. Per garantire una connettività rapida al failover, aggiungere `MultiSubnetFailover=True` alla stringa di connessione se il client SQL lo supporta. 

## <a name="tools"></a>Strumenti

Gli utenti di [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [SQLCMD](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is)e [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) devono specificare in modo esplicito il nome del listener DNN e la porta come nome del server nella stringa di connessione per la connessione al listener. 

La creazione del listener DNN tramite l'interfaccia utente grafica di SQL Server Management Studio (SSMS) non è attualmente supportata. 


## <a name="availability-groups-and-fci"></a>Gruppi di disponibilità e FCI

È possibile configurare un gruppo di disponibilità Always On usando un'istanza del cluster di failover di come una delle repliche. Affinché questa configurazione funzioni con il listener DNN, è necessario che l' [istanza del cluster di failover usi anche il DNN](failover-cluster-instance-distributed-network-name-dnn-configure.md) perché non è possibile inserire l'indirizzo IP virtuale dell'istanza FCI nell'elenco di indirizzi IP DNN del gruppo di disponibilità. 

In questa configurazione, l'URL dell'endpoint del mirroring per la replica FCI deve usare l'istanza FCI DNN. Analogamente, se l'istanza FCI viene utilizzata come replica di sola lettura, il routing di sola lettura alla replica FCI deve utilizzare la DNN FCI. 

Il formato dell'endpoint del mirroring è: `ENDPOINT_URL = 'TCP://<FCI DNN DNS name>:<mirroring endpoint port>'` . 

Se, ad esempio, il nome DNS di DNN FCI è `dnnlsnr` e `5022` è la porta dell'endpoint del mirroring dell'istanza FCI, il frammento di codice Transact-SQL (T-SQL) per creare l'URL dell'endpoint sarà simile al seguente: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

Analogamente, il formato per l'URL di routing di sola lettura è: `TCP://<FCI DNN DNS name>:<SQL Server instance port>` . 

Ad esempio, se il nome DNS di DNN è `dnnlsnr` e `1444` è la porta usata dalla destinazione di sola lettura SQL Server FCI, il frammento di codice T-SQL per creare l'URL del routing di sola lettura sarà simile al seguente: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

È possibile omettere la porta nell'URL se è la porta 1433 predefinita. Per un'istanza denominata, configurare una porta statica per l'istanza denominata e specificarla nell'URL di routing di sola lettura.  

## <a name="distributed-availability-group"></a>Gruppo di disponibilità distribuito

I gruppi di disponibilità distribuiti non sono attualmente supportati con il listener DNN. 

## <a name="replication"></a>Replica

La replica transazionale, di merge e di snapshot supporta la sostituzione del listener VNN con il listener e la porta DNN negli oggetti di replica che si connettono al listener. 

Per altre informazioni su come usare la replica con i gruppi di disponibilità, vedere [Publisher e](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server)gruppo di disponibilità, [Sottoscrittore e](/sql/database-engine/availability-groups/windows/replication-subscribers-and-always-on-availability-groups-sql-server)gruppo di disponibilità, [server di distribuzione e](/sql/relational-databases/replication/configure-distribution-availability-group)gruppo di disponibilità.

## <a name="msdtc"></a>MSDTC

Sono supportati sia MSDTC locali che cluster, ma MSDTC usa una porta dinamica che richiede una Azure Load Balancer standard per configurare la porta a disponibilità elevata. Di conseguenza, la macchina virtuale deve usare una prenotazione IP standard o non può essere esposta a Internet. 

Definire due regole, una per la porta di mapping degli endpoint RPC 135 e una per la porta MSDTC reale. Dopo il failover, modificare la regola LB sulla nuova porta MSDTC dopo che è stata modificata nel nuovo nodo. 

Se MSDTC è locale, assicurarsi di consentire le comunicazioni in uscita. 

## <a name="distributed-query"></a>Query distribuita 

La query distribuita si basa su un server collegato, che può essere configurato usando il listener e la porta del gruppo di disponibilità DNN. Se la porta non è 1433, scegliere l'opzione **Usa un'altra origine dati** in SQL Server Management Studio (SSMS) durante la configurazione del server collegato. 

## <a name="filestream"></a>FileStream

FileStream è supportato, ma non per gli scenari in cui gli utenti accedono alla condivisione file con ambito usando l'API file di Windows. 

## <a name="filetable"></a>FileTable

FileTable è supportato, ma non per gli scenari in cui gli utenti accedono alla condivisione file con ambito usando l'API file di Windows. 

## <a name="linked-servers"></a>Server collegati

Configurare il server collegato utilizzando il nome e la porta del listener DNN del gruppo di disponibilità. Se la porta non è 1433, scegliere l'opzione **Usa un'altra origine dati** in SQL Server Management Studio (SSMS) durante la configurazione del server collegato. 


## <a name="frequently-asked-questions"></a>Domande frequenti


- Quale versione di SQL Server porta il supporto del listener DNN AG? 

   SQL Server 2019 CU8 e versioni successive.

- Qual è il tempo di failover previsto quando viene usato il listener DNN?

   Per il listener DNN, il tempo di failover sarà solo il tempo di failover del gruppo di disponibilità, senza ulteriori orari, ad esempio il tempo di probe quando si usa Azure Load Balancer.

- Sono previsti requisiti di versione per i client SQL per supportare DNN con OLEDB e ODBC?

   Si consiglia il `MultiSubnetFailover=True` supporto della stringa di connessione per il listener DNN. È disponibile a partire da SQL Server 2012 (11. x).

- Sono necessarie modifiche di configurazione SQL Server per usare il listener DNN? 

   SQL Server non richiede alcuna modifica alla configurazione per l'uso di DNN, ma alcune funzionalità SQL Server potrebbero richiedere una maggiore considerazione. 

- DNN supporta cluster a più subnet?

   Sì. Il cluster associa DNN in DNS con gli indirizzi IP fisici di tutte le repliche nella disponibilità indipendentemente dalla subnet. Il client SQL tenta tutti gli indirizzi IP del nome DNS indipendentemente dalla subnet. 



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere: 

- [Tecnologie cluster di Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [Gruppo di disponibilità always on](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)


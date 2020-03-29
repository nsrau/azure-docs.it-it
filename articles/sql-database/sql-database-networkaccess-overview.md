---
title: Controlli di accesso alla rete
description: Panoramica dei controlli di accesso alla rete per il database SQL di Azure e il data warehouse per gestire l'accesso e configurare un database singolo o in pool.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 822fab5c00501d415c3c184587141e869523e417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945385"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Controlli di accesso alla rete del database SQL di Azure e del data warehouseAzure SQL Database and Data Warehouse network access controls

> [!NOTE]
> Questo articolo è applicabile al server SQL di Azure e ai database SQL e di SQL Data Warehouse creati nel server SQL di Azure. Per semplicità, "database SQL" viene usato per fare riferimento sia al database SQL che al database di SQL Data Warehouse.

> [!IMPORTANT]
> Le informazioni di questo articolo *non* sono valide per **Istanza gestita di database SQL di Azure**. Per ulteriori informazioni sulla configurazione di rete, vedere [connessione a un'istanza gestita](sql-database-managed-instance-connect-app.md) .

Quando si crea un nuovo sql Server di Azure dal portale di [Azure,](sql-database-single-database-get-started.md)il risultato è un endpoint pubblico nel formato *yourservername.database.windows.net*.

È possibile utilizzare i seguenti controlli di accesso alla rete per consentire selettivamente l'accesso al database SQl tramite l'endpoint pubblico:You can use the following network access controls to selectively allow access to the SQl Database via the public endpoint:
- Consenti servizi di Azure: se impostato su ON, altre risorse all'interno del limite di Azure, ad esempio una macchina virtuale di Azure, possono accedere al database SQLAllow Azure Services: When set to ON, other resources within the Azure boundary, for example an Azure Virtual Machine, can access SQL Database

- Regole del firewall IP: utilizzare questa funzionalità per consentire in modo esplicito le connessioni da un indirizzo IP specifico, ad esempio da computer locali

È inoltre possibile consentire l'accesso privato al database SQL da reti virtuali tramite:You can also allow private access to the SQL Database from [Virtual Networks](../virtual-network/virtual-networks-overview.md) via:
- Regole del firewall della rete virtuale: usare questa funzionalità per consentire il traffico da una rete virtuale specifica all'interno del limite di AzureVirtual Network firewall rules: Use this feature to allow traffic from a specific Virtual Network within the Azure boundary

- Private Link: Use this feature to create a private endpoint for Azure SQL Server within a specific Virtual Network



Guarda il video qui sotto per una spiegazione di alto livello di questi controlli di accesso e di cosa fanno:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]


## <a name="allow-azure-services"></a>Consentire i servizi di AzureAllow Azure services 
Durante la creazione di un nuovo Azure SQL Server dal portale di [Azure,](sql-database-single-database-get-started.md)questa impostazione viene lasciata deselezionata.



È anche possibile modificare questa impostazione tramite il riquadro del firewall dopo la creazione di Azure SQL Server come indicato di seguito.
  
 ![Schermata della gestione del firewall del server][2]

Se impostato su **ON** Azure SQL Server consente comunicazioni da tutte le risorse all'interno del limite di Azure, che possono o non possono far parte della sottoscrizione.

In molti casi, l'impostazione **ON** è più permissiva di quella che la maggior parte dei clienti desidera. È possibile che si desideri impostare questa impostazione **su OFF** e sostituirla con regole firewall IP più restrittive o regole del firewall della rete virtuale. Questa operazione influisce sulle funzionalità seguenti eseguite nelle macchine virtuali in Azure che non fanno parte della rete virtuale e quindi si connettono al database SQL tramite un indirizzo IP di Azure.Doing so affects the following features that run on VMs in Azure that not part of your VNet and then connect to Sql Database via an Azure IP address.

### <a name="import-export-service"></a>Servizio Importazione/Esportazione di Azure
Il servizio di esportazione dell'importazione non funziona **Consenti ai servizi di Azure** di accedere al server impostato su OFF. Tuttavia, è possibile aggirare il problema [eseguendo manualmente sqlpackage.exe da una macchina virtuale di Azure o eseguendo l'esportazione](https://docs.microsoft.com/azure/sql-database/import-export-from-vm) direttamente nel codice usando l'API DACFx.However you can work around the problem by manually running sqlpackage.exe from an Azure VM or performing the export directly in your code by using the DACFx API.

### <a name="data-sync"></a>Sincronizzazione dei dati
Per usare la funzionalità di sincronizzazione dei dati con **Consenti ai servizi di Azure** di accedere al server impostato su OFF, è necessario creare singole voci delle regole del firewall per aggiungere indirizzi [IP](sql-database-server-level-firewall-rule.md) dal tag del **servizio Sql** per l'area che ospita il database **Hub.**
Aggiungere queste regole del firewall a livello di server ai server logici che ospitano i database **Hub** e **Member** (che possono trovarsi in aree diverse)

Usare lo script di PowerShell seguente per generare gli indirizzi IP corrispondenti al tag di servizio Sql per l'area Stati Uniti occidentaliUse the following PowerShell script to generate the IP addresses corresponding to Sql service tag for West US region
```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> Get-AzNetworkServiceTag restituisce l'intervallo globale per il tag del servizio SQL nonostante specifichi il parametro Location. Assicurarsi di filtrarlo in base all'area che ospita il database Hub utilizzato dal gruppo di sincronizzazione

Si noti che l'output dello script di PowerShell è in notazione Classless Tra-Domain Routing (CIDR) e questo deve essere convertito in un formato di indirizzo IP iniziale e finale utilizzando [Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) in questo modo
```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26                                                                   
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Eseguire la procedura aggiuntiva seguente per convertire tutti gli indirizzi IP da CIDR al formato di indirizzo IP iniziale e finale.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}                                                                                                                
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```
È ora possibile aggiungerli come regole del firewall distinte e quindi impostare **Consenti ai servizi di Azure di accedere al server** su OFF.


## <a name="ip-firewall-rules"></a>Regole del firewall IP
Firewall basato su IP è una funzionalità di Azure SQL Server che impedisce l'accesso al server di database fino a quando non si aggiungono in modo esplicito [gli indirizzi IP](sql-database-server-level-firewall-rule.md) dei computer client.


## <a name="virtual-network-firewall-rules"></a>Regole del firewall della rete virtualeVirtual Network firewall rules

Oltre alle regole IP, il firewall di Azure SQL Server consente di definire le regole di *rete virtuale.*  
Per altre informazioni, vedere Endpoint e regole del servizio di rete virtuale per il database SQL di Azure o guardare questo video:To learn more, see [Virtual Network service endpoints and rules for Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md) or watch this video:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Terminologia di Rete di AzureAzure Networking terminology  
Tenere presente i seguenti termini di Rete di Azure durante l'esplorazione delle regole del firewall della rete virtuale

**Rete virtuale:** È possibile avere reti virtuali associate alla sottoscrizione di AzureYou can have virtual networks associated with your Azure subscription 

**Subnet:** una rete virtuale contiene **subnet**. Le macchine virtuali (VM) di Azure esistenti vengono assegnate a subnet. Una subnet può contenere varie VM o altri nodi di calcolo. I nodi di calcolo esterni alla rete virtuale non possono accedervi, a meno che non si configuri la sicurezza in modo da consentirne l'accesso.

**Endpoint servizio di rete virtuale:** un [endpoint servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md) è una subnet in cui i valori di proprietà includono uno o più nomi formali di tipi di servizi Azure. Questo articolo è incentrato sul nome del tipo **Microsoft.Sql**, che fa riferimento al servizio Azure denominato Database SQL.

**Regola di rete virtuale:** una regola di rete virtuale per il server di database SQL è una subnet presente nell'elenco di controllo di accesso (ACL) del server di database SQL. Per essere nell'elenco ACL del database SQL, la subnet deve contenere il nome del tipo **Microsoft.Sql**. Una regola di rete virtuale indica al server di database SQL di accettare le comunicazioni da ogni nodo che si trova nella subnet.


## <a name="ip-vs-virtual-network-firewall-rules"></a>Regole del firewall tra indirizzi IP e reti virtuali

Il firewall di SQL Server di Azure consente di specificare intervalli di indirizzi IP da cui le comunicazioni vengono accettate nel database SQL. Questo approccio è ideale per gli indirizzi IP stabili esterni alla rete privata di Azure, Tuttavia, le macchine virtuali (VM) all'interno della rete privata di Azure sono configurate con indirizzi IP *dinamici.* Gli indirizzi IP dinamici possono cambiare quando la macchina virtuale viene riavviata e a sua volta invalidare la regola del firewall basato su IP. Sarebbe inutile specificare un indirizzo IP dinamico in una regola del firewall, in un ambiente di produzione.

È possibile aggirare questa limitazione ottenendo un indirizzo IP *statico* per la macchina virtuale. Per i dettagli, vedere [Configurare indirizzi IP privati per una VM mediante il portale di Azure](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). Tuttavia, l'approccio IP statico può diventare difficile da gestire ed è dispendioso a livello di scalabilità. 

Le regole di rete virtuale sono un'alternativa più semplice per stabilire e gestire l'accesso da una subnet specifica che contiene le macchine virtuali.

> [!NOTE]
> Il database SQL non è ancora disponibile in una subnet. Se il server di database SQL di Azure è un nodo in una subnet nella rete virtuale, tutti i nodi all'interno della rete virtuale possono comunicare con il database SQL. In questo caso, le macchine virtuali possono comunicare con il database SQL senza aver bisogno di regole di rete virtuale o IP.

## <a name="private-link"></a>Collegamento privato 
Private Link consente di connettersi ad Azure SQL Server tramite un **endpoint privato.** Un endpoint privato è un indirizzo IP privato all'interno di una [rete virtuale](../virtual-network/virtual-networks-overview.md) e di una subnet specifiche.

## <a name="next-steps"></a>Passaggi successivi

- Per una guida introduttiva alla creazione di una regola del firewall IP a livello di server, vedere Creare un database SQL di [Azure.](sql-database-single-database-get-started.md)

- Per una guida introduttiva alla creazione di una regola del firewall Vnet a livello di server, vedere Endpoint e regole del servizio di rete virtuale per il database SQL di Azure.For a quickstart on creating a server-level Vnet firewall rule, see [Virtual Network service endpoints and rules for Azure SQL Database.](sql-database-vnet-service-endpoint-rule-overview.md)

- Per informazioni sulla connessione a un database SQL di Azure da applicazioni open source o di terze parti, vedere Esempi di codice delle guide rapide client [in Database SQL.](https://msdn.microsoft.com/library/azure/ee336282.aspx)

- Per informazioni sulle porte aggiuntive che si possono dover aprire vedere la sezione **Esterno rispetto all'interno** di [Porte successive alla 1433 per ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).

- Per una panoramica della connettività del database SQL di Azure, vedere Architettura della [connettività SQL](sql-database-connectivity-architecture.md) di AzureFor an overview of Azure SQL Database Connectivity, see Azure SQL Connectivity Architecture

- Per una panoramica della sicurezza del Database SQL di Azure vedere [Protezione del Database SQL](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png


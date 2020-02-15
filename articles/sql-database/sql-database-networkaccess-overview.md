---
title: Controlli di accesso alla rete
description: Panoramica dei controlli di accesso alla rete per il database SQL di Azure e data warehouse per gestire l'accesso e configurare un database singolo o in pool.
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
ms.date: 08/05/2019
ms.openlocfilehash: 16ba90aab52c00f77af590f854217cd989df53b3
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251907"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Controlli di accesso alla rete del database SQL di Azure e data warehouse

> [!NOTE]
> Questo articolo è applicabile al server SQL di Azure e ai database SQL e di SQL Data Warehouse creati nel server SQL di Azure. Per semplicità, "database SQL" viene usato per fare riferimento sia al database SQL che al database di SQL Data Warehouse.

> [!IMPORTANT]
> Le informazioni di questo articolo *non* sono valide per **Istanza gestita di database SQL di Azure**. Per ulteriori informazioni sulla configurazione di rete, vedere la pagina relativa [alla connessione a un istanza gestita](sql-database-managed-instance-connect-app.md) .

Quando si crea una nuova SQL Server [di Azure da portale di Azure](sql-database-single-database-get-started.md), il risultato è un endpoint pubblico nel formato *yourservername.database.Windows.NET*. Per impostazione predefinita, tutti gli accessi all'endpoint pubblico sono negati. È quindi possibile usare i seguenti controlli di accesso alla rete per consentire selettivamente l'accesso al database SQl tramite l'endpoint pubblico
- Consenti i servizi di Azure:-se impostato su ON, altre risorse all'interno del limite di Azure, ad esempio una macchina virtuale di Azure, possono accedere al database SQL

- Regole del firewall IP: usare questa funzionalità per consentire in modo esplicito le connessioni da un indirizzo IP specifico, ad esempio da computer locali.

- Regole del firewall della rete virtuale: usare questa funzionalità per consentire il traffico da una rete virtuale specifica entro il limite di Azure

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Consenti i servizi di Azure 
Durante la creazione di una nuova SQL Server di Azure [da portale di Azure](sql-database-single-database-get-started.md), questa impostazione rimane deselezionata.

 ![Screenshot della creazione di un nuovo server][1]

È anche possibile modificare questa impostazione tramite il riquadro Firewall dopo la creazione del SQL Server di Azure come indicato di seguito.
  
 ![Screenshot della gestione del firewall del server][2]

Quando è impostato **su in** Azure SQL Server consente le comunicazioni da tutte le risorse all'interno del limite di Azure, che possono o meno essere parte della sottoscrizione.

In molti casi, l'impostazione on è più permissiva rispetto **a** quella che la maggior parte dei clienti desidera. Potrebbe essere necessario impostare questa impostazione su **off** e sostituirla con regole del firewall IP più restrittive o regole del firewall della rete virtuale. Questa operazione influiscono sulle funzionalità seguenti che vengono eseguite in macchine virtuali in Azure che non fanno parte della VNet e quindi si connettono al database SQL tramite un indirizzo IP di Azure.

### <a name="import-export-service"></a>Servizio Importazione/Esportazione di Azure
Il servizio importazione/esportazione non funziona **consente ai servizi di Azure di accedere al server** impostato su disattivato. Tuttavia è possibile aggirare il problema eseguendo [manualmente SqlPackage. exe da una macchina virtuale di Azure o eseguendo l'esportazione](https://docs.microsoft.com/azure/sql-database/import-export-from-vm) direttamente nel codice usando l'API DACFx.

### <a name="data-sync"></a>Sincronizzazione dei dati
Per usare la funzionalità di sincronizzazione dati con **Consenti ai servizi di Azure di accedere al server** impostato su disattivato, è necessario creare singole voci di regole del firewall per [aggiungere indirizzi IP](sql-database-server-level-firewall-rule.md) dal tag del **servizio SQL** per l'area che ospita il database **Hub** .
Aggiungere queste regole del firewall a livello di server ai server logici che ospitano i database di **Hub** e **membri** (che possono trovarsi in aree diverse)

Usare lo script di PowerShell seguente per generare gli indirizzi IP corrispondenti al tag del servizio SQL per l'area Stati Uniti occidentali
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
> Get-AzNetworkServiceTag restituisce l'intervallo globale per il tag del servizio SQL, sebbene specifichi il parametro location. Assicurarsi di filtrarlo nell'area che ospita il database hub usato dal gruppo di sincronizzazione

Si noti che l'output dello script di PowerShell è in notazione CIDR (Inter-Domain Routing), che deve essere convertito in un formato di indirizzo IP iniziale e finale usando [Get-IPrangeStartEnd. ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) come questo
```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26                                                                   
start        end
-----        ---
52.229.17.64 52.229.17.127
```

Eseguire i passaggi aggiuntivi seguenti per convertire tutti gli indirizzi IP da CIDR al formato indirizzo IP iniziale e finale.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}                                                                                                                
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```
È ora possibile aggiungerli come regole del firewall distinte, quindi impostare **Consenti ai servizi di Azure di accedere al server** .


## <a name="ip-firewall-rules"></a>Regole del firewall IP
Il firewall basato su IP è una funzionalità di SQL Server di Azure che impedisce l'accesso al server di database finché non si aggiungono in modo esplicito [gli indirizzi IP](sql-database-server-level-firewall-rule.md) dei computer client.


## <a name="virtual-network-firewall-rules"></a>Regole del firewall della rete virtuale

Oltre alle regole IP, Azure SQL Server Firewall consente di definire *le regole della rete virtuale*.  
Per altre informazioni, vedere [endpoint del servizio di rete virtuale e regole per il database SQL di Azure](sql-database-vnet-service-endpoint-rule-overview.md) o guardare questo video:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Terminologia di rete di Azure  
Quando si esplorano le regole del firewall di rete virtuale, tenere presenti le condizioni di rete di Azure seguenti:

**Rete virtuale:** È possibile avere reti virtuali associate alla sottoscrizione di Azure 

**Subnet:** una rete virtuale contiene **subnet**. Le macchine virtuali (VM) di Azure esistenti vengono assegnate a subnet. Una subnet può contenere varie VM o altri nodi di calcolo. I nodi di calcolo esterni alla rete virtuale non possono accedervi, a meno che non si configuri la sicurezza in modo da consentirne l'accesso.

**Endpoint servizio di rete virtuale:** un [endpoint servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md) è una subnet in cui i valori delle proprietà includono uno o più nomi formali di tipi di servizi Azure. Questo articolo è incentrato sul nome del tipo **Microsoft.Sql**, che fa riferimento al servizio Azure denominato Database SQL.

**Regola di rete virtuale:** una regola di rete virtuale per il server di database SQL è una subnet presente nell'elenco di controllo di accesso (ACL) del server di database SQL. Per essere nell'elenco ACL del database SQL, la subnet deve contenere il nome del tipo **Microsoft.Sql**. Una regola di rete virtuale indica al server di database SQL di accettare le comunicazioni da ogni nodo che si trova nella subnet.


## <a name="ip-vs-virtual-network-firewall-rules"></a>Regole del firewall di rete virtuale e IP

Il firewall SQL Server di Azure consente di specificare gli intervalli di indirizzi IP da cui le comunicazioni vengono accettate nel database SQL. Questo approccio è ideale per gli indirizzi IP stabili esterni alla rete privata di Azure, Tuttavia, le macchine virtuali (VM) nella rete privata di Azure sono configurate con indirizzi IP *dinamici* . Gli indirizzi IP dinamici possono cambiare quando la macchina virtuale viene riavviata e, a sua volta, invalida la regola del firewall basata su IP. Sarebbe inutile specificare un indirizzo IP dinamico in una regola del firewall, in un ambiente di produzione.

Per ovviare a questa limitazione, è possibile ottenere un indirizzo IP *statico* per la macchina virtuale. Per informazioni dettagliate, vedere [configurare indirizzi IP privati per una macchina virtuale usando il portale di Azure](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). Tuttavia, l'approccio IP statico può diventare difficile da gestire ed è dispendioso a livello di scalabilità. 

Le regole della rete virtuale sono un'alternativa più semplice per definire e gestire l'accesso da una subnet specifica che contiene le macchine virtuali.

> [!NOTE]
> Il database SQL non è ancora disponibile in una subnet. Se il server di database SQL di Azure è un nodo in una subnet nella rete virtuale, tutti i nodi all'interno della rete virtuale possono comunicare con il database SQL. In questo caso, le macchine virtuali possono comunicare con il database SQL senza aver bisogno di regole di rete virtuale o IP.

## <a name="next-steps"></a>Passaggi successivi

- Per una guida introduttiva alla creazione di una regola del firewall IP a livello di server, vedere [creare un database SQL di Azure](sql-database-single-database-get-started.md).

- Per una guida introduttiva sulla creazione di una regola del firewall VNET a livello di server, vedere [endpoint del servizio di rete virtuale e regole per il database SQL di Azure](sql-database-vnet-service-endpoint-rule-overview.md).

- Per informazioni sulla connessione a un database SQL di Azure da applicazioni open source o di terze parti, vedere [esempi di codice di avvio rapido del client per il database SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).

- Per informazioni sulle porte aggiuntive che si possono dover aprire vedere la sezione **Esterno rispetto all'interno** di [Porte successive alla 1433 per ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).

- Per una panoramica della connettività del database SQL di Azure, vedere [architettura di connettività SQL di Azure](sql-database-connectivity-architecture.md)

- Per una panoramica della sicurezza del Database SQL di Azure vedere [Protezione del Database SQL](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png

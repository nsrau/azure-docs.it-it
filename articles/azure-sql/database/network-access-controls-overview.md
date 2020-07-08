---
title: Controlli di accesso alla rete
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Panoramica su come gestire e controllare l'accesso alla rete per il database SQL di Azure e Azure sinapsi Analytics (in precedenza Azure SQL Data Warehouse).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 435a5fe6f5900ffe742d4459e8e402d2e698ca9f
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085464"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-network-access-controls"></a>Controlli di accesso alla rete del database SQL di Azure e di Azure sinapsi Analytics

Quando si crea un server SQL logico dal [portale di Azure](single-database-create-quickstart.md) per il database SQL di Azure e Azure sinapsi Analytics, il risultato è un endpoint pubblico nel formato *yourservername.database.Windows.NET*.

È possibile utilizzare i seguenti controlli di accesso alla rete per consentire selettivamente l'accesso a un database tramite l'endpoint pubblico:

- Consenti i servizi di Azure: quando è impostato su ON, altre risorse all'interno del limite di Azure, ad esempio una macchina virtuale di Azure, possono accedere al database SQL
- Regole del firewall IP: usare questa funzionalità per consentire in modo esplicito le connessioni da un indirizzo IP specifico, ad esempio da computer locali

È anche possibile consentire l'accesso privato al database da [reti virtuali](../../virtual-network/virtual-networks-overview.md) tramite:

- Regole del firewall della rete virtuale: usare questa funzionalità per consentire il traffico da una rete virtuale specifica entro il limite di Azure
- Collegamento privato: usare questa funzionalità per creare un endpoint privato per [SQL Server logico](logical-servers.md) all'interno di una rete virtuale specifica

> [!IMPORTANT]
> Questo articolo *non* si applica a **SQL istanza gestita**. Per altre informazioni sulla configurazione di rete, vedere [connessione al istanza gestita SQL di Azure](../managed-instance/connect-application-instance.md) .

Vedere il video seguente per una spiegazione generale di questi controlli di accesso e delle operazioni eseguite:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Consenti i servizi di Azure

Durante la creazione di un nuovo SQL Server logico [dal portale di Azure](single-database-create-quickstart.md), questa impostazione rimane deselezionata.

È anche possibile modificare questa impostazione tramite il riquadro Firewall dopo aver creato il server SQL logico come indicato di seguito.
  
![Screenshot della gestione del firewall del server][2]

Quando è impostata **su on**, il server consente le comunicazioni da tutte le risorse all'interno del limite di Azure, che possono o meno far parte della sottoscrizione.

In molti casi, l'impostazione on è più permissiva rispetto **a** quella che la maggior parte dei clienti desidera. È consigliabile impostare questa impostazione su **off** e sostituirla con regole del firewall IP più restrittive o regole del firewall della rete virtuale. 

Questa operazione influisca tuttavia sulle funzionalità seguenti che vengono eseguite in macchine virtuali in Azure che non fanno parte della rete virtuale e quindi si connettono al database tramite un indirizzo IP di Azure:

### <a name="import-export-service"></a>Servizio Importazione/Esportazione di Azure

Il servizio importazione/esportazione non funziona quando **l'opzione Consenti l'accesso a servizi di Azure** è impostata su **disattivato**. Tuttavia, è possibile aggirare il problema eseguendo [manualmente sqlpackage.exe da una macchina virtuale di Azure o eseguendo l'esportazione](https://docs.microsoft.com/azure/sql-database/import-export-from-vm) direttamente nel codice usando l'API DACFx.

### <a name="data-sync"></a>Sincronizzazione dei dati

Per usare la funzionalità di sincronizzazione dei dati con **Consenti l'accesso a servizi di Azure** impostati su **disattivato**, è necessario creare singole voci di regole del firewall per [aggiungere indirizzi IP](firewall-create-server-level-portal-quickstart.md) dal **tag del servizio SQL** per l'area che ospita il database **Hub** .
Aggiungere queste regole del firewall a livello di server ai server che ospitano i database di **Hub** e **membri** (che possono trovarsi in aree diverse)

Usare lo script di PowerShell seguente per generare indirizzi IP corrispondenti al tag del servizio SQL per l'area Stati Uniti occidentali

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

Si noti che l'output dello script di PowerShell è in notazione CIDR (Inter-Domain Routing). Questa operazione deve essere convertita in un formato di indirizzo IP iniziale e finale usando [Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) di questo tipo:

```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26
start        end
-----        ---
52.229.17.64 52.229.17.127
```

È possibile usare questo script di PowerShell aggiuntivo per convertire tutti gli indirizzi IP da CIDR al formato indirizzo IP iniziale e finale.

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

Il firewall basato su IP è una funzionalità di SQL Server logico in Azure che impedisce l'accesso al server fino a quando non si aggiungono in modo esplicito [gli indirizzi IP](firewall-create-server-level-portal-quickstart.md) dei computer client.

## <a name="virtual-network-firewall-rules"></a>Regole del firewall della rete virtuale

Oltre alle regole IP, il firewall del server consente di definire *le regole della rete virtuale*.  
Per altre informazioni, vedere [endpoint del servizio di rete virtuale e regole per il database SQL di Azure](vnet-service-endpoint-rule-overview.md) o guardare questo video:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

### <a name="azure-networking-terminology"></a>Terminologia di rete di Azure

Quando si esplorano le regole del firewall di rete virtuale, tenere presenti le condizioni di rete di Azure seguenti:

**Rete virtuale:** È possibile avere reti virtuali associate alla sottoscrizione di Azure

**Subnet:** una rete virtuale contiene **subnet**. Le macchine virtuali (VM) di Azure esistenti vengono assegnate a subnet. Una subnet può contenere varie VM o altri nodi di calcolo. I nodi di calcolo esterni alla rete virtuale non possono accedere alla rete virtuale, a meno che non si configuri la sicurezza per consentire l'accesso.

**Endpoint servizio rete virtuale:** Un [endpoint del servizio di rete virtuale](../../virtual-network/virtual-network-service-endpoints-overview.md) è una subnet i cui valori di proprietà includono uno o più nomi formali di tipi di servizi di Azure. In questo articolo si è interessati al nome del tipo di **Microsoft. SQL**, che fa riferimento al servizio di Azure denominato database SQL.

**Regola della rete virtuale:** Una regola della rete virtuale per il server è una subnet elencata nell'elenco di controllo di accesso (ACL) del server. Per trovarsi nell'ACL del database nel database SQL, la subnet deve contenere il nome del tipo **Microsoft. SQL** . Una regola della rete virtuale indica al server di accettare le comunicazioni da ogni nodo che si trova nella subnet.

## <a name="ip-vs-virtual-network-firewall-rules"></a>Regole del firewall di rete virtuale e IP

Il firewall del database SQL di Azure consente di specificare gli intervalli di indirizzi IP da cui le comunicazioni vengono accettate nel database SQL. Questo approccio è ideale per gli indirizzi IP stabili esterni alla rete privata di Azure, Tuttavia, le macchine virtuali (VM) nella rete privata di Azure sono configurate con indirizzi IP *dinamici* . Gli indirizzi IP dinamici possono cambiare quando la macchina virtuale viene riavviata e, a sua volta, invalida la regola del firewall basata su IP. Sarebbe inutile specificare un indirizzo IP dinamico in una regola del firewall, in un ambiente di produzione.

Per ovviare a questa limitazione, è possibile ottenere un indirizzo IP *statico* per la macchina virtuale. Per informazioni dettagliate, vedere [creare una macchina virtuale con un indirizzo IP pubblico statico usando il portale di Azure](../../virtual-network/virtual-network-deploy-static-pip-arm-portal.md). Tuttavia, l'approccio IP statico può diventare difficile da gestire ed è costoso quando viene eseguito su larga scala.

Le regole della rete virtuale sono un'alternativa più semplice per definire e gestire l'accesso da una subnet specifica che contiene le macchine virtuali.

> [!NOTE]
> Il database SQL non è ancora disponibile in una subnet. Se il server è un nodo in una subnet nella rete virtuale, tutti i nodi all'interno della rete virtuale possono comunicare con il database SQL. In questo caso, le macchine virtuali possono comunicare con il database SQL senza aver bisogno di regole di rete virtuale o IP.

## <a name="private-link"></a>Collegamento privato

Collegamento privato consente di connettersi a un server tramite un **endpoint privato**. Un endpoint privato è un indirizzo IP privato all'interno di una [rete virtuale](../../virtual-network/virtual-networks-overview.md) e una subnet specifiche.

## <a name="next-steps"></a>Passaggi successivi

- Per una guida introduttiva alla creazione di una regola del firewall IP a livello di server, vedere [creare un database nel database SQL](single-database-create-quickstart.md).

- Per una guida introduttiva alla creazione di una regola del firewall di rete virtuale a livello di server, vedere [endpoint e regole del servizio rete virtuale per il database SQL di Azure](vnet-service-endpoint-rule-overview.md).

- Per informazioni sulla connessione a un database nel database SQL da applicazioni open source o di terze parti, vedere [esempi di codice di avvio rapido del client per il database SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).

- Per informazioni sulle porte aggiuntive che si possono dover aprire vedere la sezione **Esterno rispetto all'interno** di [Porte successive alla 1433 per ADO.NET 4.5](adonet-v12-develop-direct-route-ports.md).

- Per una panoramica della connettività del database SQL di Azure, vedere [architettura di connettività SQL di Azure](connectivity-architecture.md)

- Per una panoramica della sicurezza del Database SQL di Azure vedere [Protezione del Database SQL](security-overview.md)

<!--Image references-->
[1]: media/quickstart-create-single-database/new-server2.png
[2]: media/quickstart-create-single-database/manage-server-firewall.png
 

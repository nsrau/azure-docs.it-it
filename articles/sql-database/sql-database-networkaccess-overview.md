---
title: Controlli di accesso alla rete del database SQL di Azure e data warehouse | Microsoft Docs
description: Panoramica dei controlli di accesso alla rete per il database SQL di Azure e data warehouse per gestire l'accesso e configurare un database singolo o in pool.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 4722cda0506493671ff872d75e7376f3de74b1b8
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886650"
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


## <a name="allow-azure-services"></a>Consenti i servizi di Azure 
Durante la creazione di una nuova SQL Server di Azure [da portale di Azure](sql-database-single-database-get-started.md), questa impostazione rimane deselezionata.

 ![Screenshot della creazione di un nuovo server][1]

È anche possibile modificare questa impostazione tramite il riquadro Firewall dopo la creazione del SQL Server di Azure come indicato di seguito.
  
 ![Screenshot della gestione del firewall del server][2]

Quando è impostato **su in** Azure SQL Server consente le comunicazioni da tutte le risorse all'interno del limite di Azure, che possono o meno essere parte della sottoscrizione.

In molti casi, l'impostazione on è più permissiva rispetto **a** quella che la maggior parte dei clienti desidera. Potrebbe essere necessario impostare questa impostazione su **off** e sostituirla con regole del firewall IP più restrittive o regole del firewall della rete virtuale. Questa operazione influiscono sulle funzionalità seguenti:

### <a name="import-export-service"></a>Servizio Importazione/Esportazione di Azure

Il servizio Importazione/Esportazione del database SQL di Azure viene eseguito sulle VM in Azure. Queste VM non sono nella rete virtuale e quindi ottengono un IP di Azure quando si connettono al database. Rimuovendo **Consenti ai servizi di Azure di accedere al server**, queste macchine virtuali non potranno accedere ai database.
Per aggirare il problema, è possibile eseguire l'importazione o l'esportazione BACPAC direttamente nel codice usando l'API DACFx.

### <a name="sql-database-query-editor"></a>Editor di query del database SQL

L'editor di query del database SQL di Azure viene distribuito nelle VM in Azure. Queste VM non sono nella rete virtuale. Le VM ottengono quindi un IP di Azure quando si connettono al database. Rimuovendo **Consenti ai servizi di Azure di accedere al server**, queste macchine virtuali non potranno accedere ai database.

### <a name="table-auditing"></a>Controllo tabelle

Attualmente, esistono due modi per abilitare il controllo nel database SQL. Il controllo tabelle non riesce dopo avere abilitato gli endpoint di servizio nel server di Azure SQL. Per mitigare il problema, in questo caso passare al controllo BLOB.

### <a name="impact-on-data-sync"></a>Impatto sulla sincronizzazione dati

Il database SQL di Azure è dotato della funzionalità di sincronizzazione dei dati che si connette ai database dell'utente tramite gli indirizzi IP di Azure. Quando si usano gli endpoint di servizio, è possibile disattivare **Consenti ai servizi di Azure di accedere** al server di database SQL e interromperà la funzionalità di sincronizzazione dei dati.

## <a name="ip-firewall-rules"></a>Regole del firewall IP
Il firewall basato su IP è una funzionalità di SQL Server di Azure che impedisce l'accesso al server di database finché non si aggiungono in modo esplicito [gli indirizzi IP](sql-database-server-level-firewall-rule.md) dei computer client.


## <a name="virtual-network-firewall-rules"></a>Regole del firewall della rete virtuale

Oltre alle regole IP, Azure SQL Server Firewall consente di definire *le regole della rete virtuale*.  
Per altre informazioni, vedere [Endpoint servizio di rete virtuale e regole per il database SQL di Azure](sql-database-vnet-service-endpoint-rule-overview.md).

 ### <a name="azure-networking-terminology"></a>Terminologia di rete di Azure  
Quando si esplorano le regole del firewall di rete virtuale, tenere presenti le condizioni di rete di Azure seguenti:

**Rete virtuale:** È possibile avere reti virtuali associate alla sottoscrizione di Azure 

**Subnet:** una rete virtuale contiene una o più **subnet**. Le macchine virtuali (VM) di Azure esistenti vengono assegnate a subnet. Una subnet può contenere varie VM o altri nodi di calcolo. I nodi di calcolo esterni alla rete virtuale non possono accedervi, a meno che non si configuri la sicurezza in modo da consentirne l'accesso.

**Endpoint del servizio di rete virtuale:** Un [endpoint servizio rete virtuale] [VM-Virtual-Network-Service-Endpoints-Overview-649D] è una subnet i cui valori di proprietà includono uno o più nomi formali di tipi di servizi di Azure. Questo articolo è incentrato sul nome del tipo **Microsoft.Sql**, che fa riferimento al servizio Azure denominato Database SQL.

**Regola di rete virtuale:** una regola di rete virtuale per il server di database SQL è una subnet presente nell'elenco di controllo di accesso (ACL) del server di database SQL. Per essere nell'elenco ACL del database SQL, la subnet deve contenere il nome del tipo **Microsoft.Sql**. Una regola di rete virtuale indica al server di database SQL di accettare le comunicazioni da ogni nodo che si trova nella subnet.


## <a name="ip-vs-virtual-network-firewall-rules"></a>Confronto tra IP e Regole del firewall della rete virtuale

Il firewall SQL Server di Azure consente di specificare gli intervalli di indirizzi IP da cui le comunicazioni vengono accettate nel database SQL. Questo approccio è ideale per gli indirizzi IP stabili esterni alla rete privata di Azure, Tuttavia, le macchine virtuali (VM) nella rete privata di Azure sono configurate con indirizzi IP *dinamici* . Gli indirizzi IP dinamici possono cambiare quando la macchina virtuale viene riavviata e, a sua volta, invalida la regola del firewall basata su IP. Sarebbe inutile specificare un indirizzo IP dinamico in una regola del firewall, in un ambiente di produzione.

Per ovviare a questa limitazione, è possibile ottenere un indirizzo IP *statico* per la macchina virtuale. Per informazioni dettagliate, vedere [configurare indirizzi IP privati per una macchina virtuale usando il portale di Azure] [VM-Configure-Private-IP-Addresss-for-a-Virtual-Machine-using-the-Azure-Portal-321w]. Tuttavia, l'approccio IP statico può diventare difficile da gestire ed è costoso quando viene eseguito su larga scala. 

Le regole della rete virtuale sono un'alternativa più semplice per definire e gestire l'accesso da una subnet specifica che contiene le macchine virtuali.

> [!NOTE]
> Il database SQL non è ancora disponibile in una subnet. Se il server di database SQL di Azure è un nodo in una subnet nella rete virtuale, tutti i nodi all'interno della rete virtuale possono comunicare con il database SQL. In questo caso, le macchine virtuali possono comunicare con il database SQL senza aver bisogno di regole di rete virtuale o IP.

## <a name="next-steps"></a>Passaggi successivi

- Per una guida introduttiva alla creazione di una regola del firewall IP a livello di server, vedere [creare un database SQL di Azure](sql-database-single-database-get-started.md).

- Per una guida introduttiva sulla creazione di una regola del firewall VNET a livello di server, vedere [endpoint del servizio di rete virtuale e regole per il database SQL di Azure](sql-database-vnet-service-endpoint-rule-overview.md).

- Per informazioni sulla connessione a un database SQL di Azure da applicazioni open source o di terze parti, vedere [esempi di codice di avvio rapido del client per il database SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).

- Per altre informazioni sulle porte aggiuntive che può essere necessario aprire, vedere la sezione **Esterno rispetto all'interno** di [Porte successive alla 1433 per ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md)

- Per una panoramica della connettività del database SQL di Azure, vedere [architettura di connettività SQL di Azure](sql-database-connectivity-architecture.md)

- Per una panoramica della sicurezza del Database SQL di Azure vedere [Protezione del Database SQL](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png

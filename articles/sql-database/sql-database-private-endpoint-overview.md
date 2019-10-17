---
title: Collegamento privato per il database SQL di Azure e Azure SQL Data Warehouse | Microsoft Docs
description: Panoramica della funzionalità di endpoint privato
author: rohitnayakmsft
ms.author: rohitna
ms.service: sql-database
ms.topic: overview
ms.reviewer: vanto
ms.date: 09/17/2019
ms.openlocfilehash: 3adcccde89823f865535c9e1350ac4c3e541eef6
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "71066247"
---
# <a name="private-link-for-azure-sql-database-and-data-warehouse-preview"></a>Collegamento privato per il database SQL di Azure e Azure SQL Data Warehouse (anteprima)

Il servizio Collegamento privato consente di connettersi a diversi servizi PaaS in Azure tramite un **endpoint privato**. Per un elenco dei servizi PaaS che supportano la funzionalità Collegamento privato, visitare la pagina [Documentazione di Collegamento privato](../private-link/index.yml). Un endpoint privato è un indirizzo IP privato all'interno di una [rete virtuale](../virtual-network/virtual-networks-overview.md) e una subnet specifiche. 

> [!IMPORTANT]
> Questo articolo è applicabile al server SQL di Azure e ai database SQL e di SQL Data Warehouse creati nel server SQL di Azure. Per semplicità, "database SQL" viene usato per fare riferimento sia al database SQL che al database di SQL Data Warehouse. Le informazioni di questo articolo *non* sono valide per la distribuzione di un'**istanza gestita** nel database SQL di Azure.

## <a name="data-exfiltration-prevention"></a>Impedire l'esfiltrazione dei dati

L'esfiltrazione di dati nel database SQL di Azure si verifica quando un utente autorizzato, ad esempio un amministratore di database, è in grado di estrarre i dati da un sistema e spostarli in un'altra posizione o in un altro sistema all'esterno dell'organizzazione. Ad esempio, l'utente sposta i dati in un account di archiviazione di proprietà di terze parti.

Si consideri uno scenario con un utente che esegue SQL Server Management Studio (SSMS) all'interno di una macchina virtuale di Azure che si connette a un database SQL. Questo database SQL è nel data center Stati Uniti occidentali. L'esempio seguente illustra come limitare l'accesso con gli endpoint pubblici nel database SQL tramite i controlli di accesso alla rete.

1. Disabilitare tutto il traffico dei servizi di Azure verso il database SQL tramite l'endpoint pubblico disattivando l'opzione **Consenti ai servizi di Azure di accedere al server**. Verificare che non siano consentiti indirizzi IP nelle regole del firewall a livello di server e database. Per altre informazioni, vedere [Controllo di accesso alla rete del database SQL di Azure e SQL Data Warehouse](sql-database-networkaccess-overview.md).
1. Consentire solo il traffico al database SQL tramite l'indirizzo IP privato della macchina virtuale. Per altre informazioni, vedere gli articoli sull'[endpoint servizio](sql-database-vnet-service-endpoint-rule-overview.md) e le [regole del firewall della rete virtuale](sql-database-firewall-configure.md).
1. Nella macchina virtuale di Azure limitare l'ambito della connessione in uscita usando i [gruppi di sicurezza di rete (NSG)](../virtual-network/manage-network-security-group.md) e i tag del servizio come indicato di seguito.
    - Specificare una regola NSG per consentire il traffico per il tag di servizio = SQL.WestUs, che accetta solo le connessioni al database SQL negli Stati Uniti occidentali
    - Specificare una regola NSG (con una **priorità più alta**) per negare il traffico per il tag di servizio = SQL, che rifiuta le connessioni al database SQL in tutte le aree

Al termine di questa configurazione, la macchina virtuale di Azure può connettersi solo ai database SQL nell'area Stati Uniti occidentali. Tuttavia, la connettività non è limitata a un solo database SQL. La macchina virtuale può comunque connettersi a qualsiasi database SQL nell'area Stati Uniti occidentali, inclusi i database che non fanno parte della sottoscrizione. Sebbene l'ambito di esfiltrazione dei dati nello scenario precedente sia stato ridotto a un'area specifica, non è stato eliminato completamente.

Con Collegamento privato i clienti possono ora configurare controlli di accesso alla rete come gruppi di sicurezza di rete per limitare l'accesso all'endpoint privato. Viene quindi eseguito il mapping delle singole risorse PaaS di Azure a endpoint privati specifici. Un utente interno malintenzionato può accedere solo alla risorsa PaaS mappata, ad esempio un database SQL, e a nessun'altra risorsa. 

## <a name="on-premises-connectivity-over-private-peering"></a>Connettività locale tramite peering privato

Quando gli utenti si connettono all'endpoint pubblico da computer locali, il loro indirizzo IP deve essere aggiunto al firewall basato su IP mediante una[ regola del firewall a livello di server](sql-database-server-level-firewall-rule.md). Questo modello è adatto per consentire l'accesso ai singoli computer per i carichi di lavoro di sviluppo o test, ma è difficile da gestire in un ambiente di produzione.

Con Collegamento privato, gli utenti possono abilitare l'accesso cross-premise all'endpoint privato tramite [ExpressRoute](../expressroute/expressroute-introduction.md), il peering privato o il tunneling VPN. Possono quindi disabilitare tutti gli accessi tramite l'endpoint pubblico e non usare il firewall basato su IP per accettare gli indirizzi IP.

Con Collegamento privato, gli utenti possono abilitare l'accesso cross-premise all'endpoint privato tramite ExpressRoute, il peering privato o il tunneling VPN. Possono quindi disabilitare tutti gli accessi tramite l'endpoint pubblico e non usare il firewall basato su IP.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Come configurare Collegamento privato per il database SQL di Azure 

### <a name="creation-process"></a>Processo di creazione
È possibile creare endpoint privati usando il portale, PowerShell o l'interfaccia della riga di comando di Azure:
- [Portale](../private-link/create-private-endpoint-portal.md)
- [PowerShell](../private-link/create-private-endpoint-powershell.md)
- [CLI](../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Processo di approvazione
Dopo che l'amministratore di rete ha creato l'endpoint privato, l'amministratore di SQL può gestire la connessione dell'endpoint privato al database SQL.

1. Nel portale di Azure passare alla risorsa di SQL Server.

    - (1) Selezionare Connessioni endpoint privato nel riquadro sinistro
    - (2) Viene visualizzato un elenco di tutte le connessioni di endpoint privato
    - (3) Viene creato l'endpoint privato corrispondente ![Screenshot di tutte le connessioni di endpoint privato][3]

1. Selezionare una singola connessione di endpoint privato dall'elenco.
![Screenshot della connessione di endpoint privato selezionata][6]

1. L'amministratore di SQL può scegliere di approvare o rifiutare una connessione di endpoint privato e, facoltativamente, di aggiungere una breve risposta di testo.
![Screenshot dell'approvazione della connessione di endpoint privato][4]

1. Dopo l'approvazione o il rifiuto, l'elenco indicherà lo stato appropriato insieme al testo della risposta.
![Screenshot di tutte le connessioni di endpoint privato dopo l'approvazione][5]

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Casi d'uso di Collegamento privato per il database SQL di Azure 

I client possono connettersi all'endpoint privato dalla stessa rete virtuale, da una rete virtuale con peering nella stessa area o tramite una connessione da rete virtuale a rete virtuale tra aree diverse. Inoltre, i client possono connettersi dall'ambiente locale tramite ExpressRoute, peering privato o tunneling VPN. Di seguito è riportato un diagramma semplificato che mostra i casi d'uso comuni.

 ![Diagramma delle opzioni di connettività][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network-vnet"></a>Testare la connettività al database SQL da una macchina virtuale di Azure nella stessa rete virtuale

Per questo scenario si presuppone di aver creato una macchina virtuale (VM) di Azure che esegue Windows Server 2016. 

1. [Avviare una sessione di Desktop remoto (RDP) e connettersi alla macchina virtuale](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. È quindi possibile eseguire alcuni controlli di connettività di base per assicurarsi che la macchina virtuale si connetta al database SQL tramite l'endpoint privato usando gli strumenti seguenti:
    1. Telnet
    1. Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Verificare la connettività con Telnet

[Telnet Client](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) è una funzionalità di Windows che può essere usata per testare la connettività. A seconda della versione del sistema operativo Windows, potrebbe essere necessario abilitare questa funzionalità in modo esplicito. 

Aprire una finestra del prompt dei comandi dopo aver installato Telnet. Eseguire il comando Telnet e specificare l'indirizzo IP e l'endpoint privato del database SQL.

```
>telnet 10.1.1.5 1433
```

Se Telnet si connette correttamente, nella finestra di comando verrà visualizzata una schermata vuota simile all'immagine seguente:

 ![Diagramma di Telnet][2]

### <a name="check-connectivity-using-psping"></a>Verificare la connettività con Psping

[Psping](/sysinternals/downloads/psping) può essere usato come indicato di seguito per verificare che la connessione dell'endpoint privato sia in ascolto delle connessioni sulla porta 1433.

Eseguire Psping nel modo seguente specificando il nome di dominio completo del server di database SQL e la porta 1433:

```
>psping.exe mysqldbsrvr.database.windows.net:1433

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.6.1.4:1433:
5 iterations (warmup 1) ping test:
Connecting to 10.6.1.4:1433 (warmup): from 10.6.0.4:49953: 2.83ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49954: 1.26ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49955: 1.98ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49956: 1.43ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49958: 2.28ms
```

L'output mostra che Psping è riuscito a eseguire il ping dell'indirizzo IP privato associato alla connessione dell'endpoint privato.

### <a name="check-connectivity-using-nmap"></a>Verificare la connettività con Nmap

Nmap (Network Mapper) è uno strumento gratuito e open source usato per l'individuazione della rete e il controllo di sicurezza. Per altre informazioni e il collegamento per il download, visitare https://nmap.org. È possibile usare questo strumento per assicurarsi che l'endpoint privato sia in ascolto delle connessioni sulla porta 1433.

Eseguire Nmap come indicato di seguito, specificando l'intervallo di indirizzi della subnet che ospita l'endpoint privato.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

Il risultato mostra che un indirizzo IP è attivo, ossia quello corrispondente all'indirizzo IP dell'endpoint privato.


### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>Verificare la connettività con SQL Server Management Studio (SSMS)

L'ultimo passaggio consiste nell'usare [SSMS per connettersi al database SQL](sql-database-connect-query-ssms.md). Dopo aver stabilito la connessione al database SQL tramite SSMS, verificare che la connessione sia stata eseguita dall'indirizzo IP privato della macchina virtuale di Azure eseguendo la query seguente:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````
> [!NOTE]
> In anteprima, le connessioni all'endpoint privato supportano solo **Proxy** come [criterio di connessione](sql-database-connectivity-architecture.md#connection-policy)


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Connessione da una VM di Azure in una rete virtuale con peering 

Configurare il [peering della rete virtuale](../virtual-network/tutorial-connect-virtual-networks-powershell.md) per stabilire la connettività al database SQL da una macchina virtuale di Azure in una rete virtuale con peering.

## <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Connessione da una VM di Azure in un ambiente da rete virtuale a rete virtuale

Configurare la [connessione gateway VPN da rete virtuale a rete virtuale](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) per stabilire la connettività a un database SQL da una macchina virtuale di Azure in un'area o in una sottoscrizione diversa.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Connessione da un ambiente locale tramite VPN

Per stabilire la connettività da un ambiente locale al database SQL, scegliere e implementare una delle opzioni seguenti:
- [Connessione da punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Connessione VPN da sito a sito](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [Circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-an-azure-sql-data-warehouse-to-azure-storage-using-polybase"></a>Connessione da Azure SQL Data Warehouse ad Archiviazione di Azure tramite Polybase

PolyBase viene in genere usato per caricare i dati in Azure SQL Data Warehouse dagli account di archiviazione di Azure. Se l'account di archiviazione di Azure da cui si caricano i dati limita l'accesso solo a un set di subnet della rete virtuale tramite endpoint privati, endpoint servizio o firewall basati su IP, la connettività da PolyBase all'account verrà interrotta. Per poter usare scenari sia di importazione che di esportazione di PolyBase con Azure SQL Data Warehouse che si connette ad Archiviazione di Azure protetta con una rete virtuale, seguire la procedura illustrata [qui](sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). 



## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica della sicurezza del Database SQL di Azure vedere [Protezione del Database SQL](sql-database-security-overview.md)
- Per una panoramica della connettività del database SQL di Azure, vedere [Architettura della connettività di SQL di Azure](sql-database-connectivity-architecture.md).

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/pe-connect-overview.png
[2]: ./media/sql-database-get-started-portal/telnet-result.png
[3]: ./media/sql-database-get-started-portal/pec-list-before.png
[4]: ./media/sql-database-get-started-portal/pec-approve.png
[5]: ./media/sql-database-get-started-portal/pec-list-after.png
[6]: ./media/sql-database-get-started-portal/pec-select.png

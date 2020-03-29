---
title: Collegamento privato - Database di Azure per MariaDBPrivate Link - Azure Database for MariaDB
description: Informazioni sul funzionamento del collegamento privato per il database di Azure per MariaDB.Learn how Private link works for Azure Database for MariaDB.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: b05a202537492fe54a76cf40a3b15987e099a7e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367721"
---
# <a name="private-link-for-azure-database-for-mariadb"></a>Collegamento privato per il database di Azure per MariaDBPrivate Link for Azure Database for MariaDB

Private Link consente di creare endpoint privati per il database di Azure per MariaDB e pertanto porta i servizi di Azure all'interno della rete virtuale privata (VNet). L'endpoint privato espone un indirizzo IP privato che è possibile usare per connettersi al database di Azure per il server di database MariaDB come qualsiasi altra risorsa nella rete virtuale.

Per un elenco ai servizi PaaS che supportano la funzionalità Private Link, consultare la [documentazione](https://docs.microsoft.com/azure/private-link/index)relativa a Private Link . Un endpoint privato è un indirizzo IP privato all'interno di una [rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) e una subnet specifiche.

> [!NOTE]
> Questa funzionalità è disponibile in tutte le aree di Azure in cui Il database di Azure per MariaDB supporta i livelli dei prezzi Scopo generale e Ottimizzato per la memoria.

## <a name="data-exfiltration-prevention"></a>Impedire l'esfiltrazione dei dati

L'ex filtrazione dei dati nel database di Azure per MariaDB è quando un utente autorizzato, ad esempio un amministratore del database, è in grado di estrarre i dati da un sistema e spostarli in un'altra posizione o sistema esterno all'organizzazione. Ad esempio, l'utente sposta i dati in un account di archiviazione di proprietà di terze parti.

Si consideri uno scenario con un utente che esegue il workbench MariaDB all'interno di una macchina virtuale di Azure che si connette a un database di Azure per l'istanza MariaDB.Consider a scenario with a user running MariaDB workbench inside an Azure VM connecting to an Azure Database for MariaDB instance. Questa istanza MariaDB si trova nel data center degli Stati Uniti occidentali. L'esempio seguente mostra come limitare l'accesso con endpoint pubblici nel database di Azure per MariaDB usando i controlli di accesso alla rete.

* Disabilitare tutto il traffico del servizio di Azure al database di Azure per MariaDB tramite l'endpoint pubblico impostando Consenti servizi di Azure su OFF. Assicurarsi che non vi siano indirizzi IP o intervalli autorizzati ad accedere al server tramite [regole del firewall](https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules) o endpoint del servizio di rete [virtuale.](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)

* Consentire il traffico verso il database di Azure per MariaDB usando l'indirizzo IP privato della macchina virtuale. Per altre informazioni, vedere gli articoli sull'[endpoint servizio](concepts-data-access-security-vnet.md) e le [regole del firewall della rete virtuale](howto-manage-vnet-portal.md).

* Nella macchina virtuale di Azure, restringere l'ambito della connessione in uscita usando i gruppi di sicurezza di rete (NSG) e i tag del servizio come segue:

    * Specificare una regola del gruppo di sicurezza di rete per consentire il traffico per il tag di servizio e SQL. WestUs - che consente solo la connessione al database di Azure per MariaDB negli Stati Uniti occidentali
    * Specificare una regola del gruppo di sicurezza di rete (con una priorità più alta) per negare il traffico per il tag di servizio - SQL - negando le connessioni al database MariaDB in tutte le areeSpecify an NSG rule (with a higher priority) to deny traffic for Service Tag - SQL - denying connections to MariaDB Database in all regions</br></br>

Al termine di questa configurazione, la macchina virtuale di Azure può connettersi solo al database di Azure per MariaDB nell'area Degli Stati Uniti occidentali. Tuttavia, la connettività non è limitata a un singolo database di Azure per MariaDB.However, the connectivity isn't restricted to a single Azure Database for MariaDB. La macchina virtuale può comunque connettersi a qualsiasi database di Azure per MariaDB nell'area Stati Uniti occidentali, inclusi i database che non fanno parte della sottoscrizione. Sebbene l'ambito di esfiltrazione dei dati nello scenario precedente sia stato ridotto a un'area specifica, non è stato eliminato completamente.</br>

Con Private Link, è ora possibile impostare controlli di accesso alla rete come gruppi di sicurezza di rete per limitare l'accesso all'endpoint privato. Viene quindi eseguito il mapping delle singole risorse PaaS di Azure a endpoint privati specifici. Un insider dannoso può accedere solo alla risorsa PaaS mappata (ad esempio un database di Azure per MariaDB) e nessun'altra risorsa.

## <a name="on-premises-connectivity-over-private-peering"></a>Connettività locale tramite peering privato

Quando ci si connette all'endpoint pubblico da computer locali, l'indirizzo IP deve essere aggiunto al firewall basato su IP utilizzando una regola del firewall a livello di server. Questo modello è adatto per consentire l'accesso ai singoli computer per i carichi di lavoro di sviluppo o test, ma è difficile da gestire in un ambiente di produzione.

Con Private Link, è possibile abilitare l'accesso cross-premise all'endpoint privato tramite [Express Route](https://azure.microsoft.com/services/expressroute/) (ER), peering privato o [tunnel VPN.](https://docs.microsoft.com/azure/vpn-gateway/) Successivamente possono disabilitare tutti gli accessi tramite endpoint pubblici e non utilizzare il firewall basato su IP.

## <a name="configure-private-link-for-azure-database-for-mariadb"></a>Configurare il collegamento privato per il database di Azure per MariaDBConfigure Private Link for Azure Database for MariaDB

### <a name="creation-process"></a>Processo di creazione

Private endpoints are required to enable Private Link. Questo può essere fatto utilizzando le seguenti guide pratiche.

* [Portale di Azure](https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal)
* [Cli](https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Processo di approvazione

Dopo che l'amministratore di rete crea l'endpoint privato (PE), l'amministratore può gestire la connessione dell'endpoint privato (PEC) al database di Azure per MariaDB. Questa separazione dei compiti tra l'amministratore di rete e l'amministratore di database è utile per la gestione del database di Azure per la connettività MariaDB.This separation of duties between the network admin and the DBA is helpful for management of the Azure Database for MariaDB connectivity. 

* Passare alla risorsa server Database di Azure per MariaDB nel portale di Azure.Navigate to the Azure Database for MariaDB server resource in the Azure portal. 
    * Selezionare le connessioni endpoint privato nel riquadro sinistro
    * Visualizza un elenco di tutte le connessioni endpoint privati
    * Endpoint privato (PE) corrispondente creato

![selezionare il portale endpoint privato](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Selezionare una singola connessione di endpoint privato dall'elenco.

![selezionare l'endpoint privato in attesa di approvazione](media/concepts-data-access-and-security-private-link/select-private-link.png)

* L'amministratore del server MariaDB può scegliere di approvare o rifiutare un PEC e, facoltativamente, aggiungere una breve risposta di testo.

![selezionare il messaggio dell'endpoint privato](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Dopo l'approvazione o il rifiuto, l'elenco rifletterà lo stato appropriato insieme al testo della risposta

![selezionare lo stato finale dell'endpoint privato](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mariadb"></a>Usare casi di collegamento privato per il database di Azure per MariaDBUse cases of Private Link for Azure Database for MariaDB

I client possono connettersi all'endpoint privato dalla stessa rete virtuale, dalla rete virtuale con peered nella stessa area o tramite connessione da VNet a VNet tra aree. Inoltre, i client possono connettersi dall'ambiente locale tramite ExpressRoute, peering privato o tunneling VPN. Di seguito è riportato un diagramma semplificato che mostra i casi d'uso comuni.

![selezionare la panoramica dell'endpoint privato](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Connessione da una VM di Azure in una rete virtuale con peering
Configurare il [peering della rete virtuale](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) per stabilire la connettività al database di Azure per MariaDB da una macchina virtuale di Azure in una rete virtuale con peering.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Connessione da una VM di Azure in un ambiente da rete virtuale a rete virtuale
Configurare la connessione al [gateway VPN da VNet a VNet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) per stabilire la connettività a un database di Azure per MariaDB da una macchina virtuale di Azure in un'area o sottoscrizione diversa.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Connessione da un ambiente locale tramite VPN
Per stabilire la connettività da un ambiente locale al database di Azure per MariaDB, scegliere e implementare una delle opzioni seguenti:

* [Connessione da punto a sito](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Connessione VPN da sito a sito](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [Circuito ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Collegamento privato combinato con le regole del firewall

Le situazioni e i risultati seguenti sono possibili quando si utilizza Private Link in combinazione con le regole del firewall:

* Se non si configuraalcuna alcuna regola del firewall, per impostazione predefinita, nessun traffico sarà in grado di accedere al database di Azure per MariaDB.If you don't configure any firewall rules, then by default, no traffic will be able to access the Azure Database for MariaDB.

* Se si configura il traffico pubblico o un endpoint del servizio e si creano endpoint privati, i diversi tipi di traffico in ingresso vengono autorizzati in base al tipo corrispondente di regola del firewall.

* Se non si configura alcun endpoint di servizio o traffico pubblico e si creano endpoint privati, il database di Azure per MariaDB è accessibile solo tramite gli endpoint privati. Se non si configura il traffico pubblico o un endpoint del servizio, dopo che tutti gli endpoint privati approvati sono stati rifiutati o eliminati, nessun traffico sarà in grado di accedere al database di Azure per MariaDB.If you don't configure public traffic or a service endpoint, after all approved private endpoints are rejected or deleted, no traffic will be able to access the Azure Database for MariaDB.

## <a name="deny-public-access-for-azure-database-for-mariadb"></a>Negare l'accesso pubblico per il database di Azure per MariaDBDeny public access for Azure Database for MariaDB

Se si desidera fare affidamento completamente solo sugli endpoint privati per accedere al database di Azure per MariaDB, è possibile disabilitare l'impostazione di tutti gli endpoint pubblici[(regole del firewall](concepts-firewall-rules.md) ed endpoint del [servizio VNet](concepts-data-access-security-vnet.md)) impostando la configurazione **Nega accesso di rete pubblica** nel server di database. 

Quando questa impostazione è impostata su *YES*, solo le connessioni tramite endpoint privati sono consentite al database di Azure per MariaDB. Quando questa impostazione è impostata su *NO,* i client possono connettersi al database di Azure per MariaDB in base alle impostazioni del firewall o dell'endpoint del servizio VNet. Inoltre, una volta impostato il valore dell'accesso alla rete privata, non è possibile aggiungere e/o aggiornare le regole esistenti dell'endpoint del firewall e del servizio VNet.

> [!Note]
> Questa funzionalità è disponibile in tutte le aree di Azure in cui Database di Azure per PostgreSQL: singolo server supporta i livelli dei prezzi Scopo generale e Ottimizzato per la memoria.
>
> Questa impostazione non ha alcun impatto sulle configurazioni SSL e TLS per il database di Azure per MariaDB.This setting does not have any impact on the SSL and TLS configurations for your Azure Database for MariaDB.

Per informazioni su come impostare l'accesso **Nega accesso di rete pubblica** per il database di Azure per MariaDB dal portale di Azure, vedere Come [configurare Nega accesso di rete pubblica](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità di sicurezza di Azure Database for MariaDB, vedere gli articoli seguenti:To learn more about Azure Database for MariaDB security features, see the following articles:

* Per configurare un firewall per il database di Azure per MariaDB, vedere [Supporto del firewall](https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules).

* Per informazioni su come configurare un endpoint del servizio di rete virtuale per il database di Azure per MariaDB, vedere [Configurare l'accesso da reti virtuali.](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)

* Per una panoramica del database di Azure per la connettività MariaDB, vedere [Database di Azure per l'architettura della connettività MariaDBFor](https://docs.microsoft.com/azure/MariaDB/concepts-connectivity-architecture) an overview of Azure Database for MariaDB connectivity, see Azure Database for MariaDB Connectivity Architecture

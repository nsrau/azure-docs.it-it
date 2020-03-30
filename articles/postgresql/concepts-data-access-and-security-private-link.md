---
title: Collegamento privato - Database di Azure per PostgreSQL - Server singoloPrivate Link - Azure Database for PostgreSQL - Single server
description: Informazioni sul funzionamento del collegamento privato per il database di Azure per PostgreSQL - Singolo server.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4216abdf8cc8aae00e3ba0c57961c4b8b7403672
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371682"
---
# <a name="private-link-for-azure-database-for-postgresql-single-server"></a>Collegamento privato per il database di Azure per il server PostgreSQL-Single

Private Link consente di creare endpoint privati per il database di Azure per PostgreSQL - Singolo server e quindi porta i servizi di Azure all'interno della rete virtuale privata (VNet). L'endpoint privato espone un indirizzo IP privato che è possibile utilizzare per connettersi al server di database come qualsiasi altra risorsa nella rete virtuale.

Per un elenco ai servizi PaaS che supportano la funzionalità Private Link, consultare la [documentazione](https://docs.microsoft.com/azure/private-link/index)relativa a Private Link . Un endpoint privato è un indirizzo IP privato all'interno di una [rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) e una subnet specifiche.

> [!NOTE]
> Questa funzionalità è disponibile in tutte le aree di Azure in cui il database di Azure per PostgreSQL Singolo server supporta i livelli dei prezzi Scopo generale e Ottimizzazione della memoria.

## <a name="data-exfiltration-prevention"></a>Impedire l'esfiltrazione dei dati

L'ex filtrazione dei dati nel database di Azure per PostgreSQL Singolo server è quando un utente autorizzato, ad esempio un amministratore di database, è in grado di estrarre dati da un sistema e spostarli in un'altra posizione o sistema all'esterno dell'organizzazione. Ad esempio, l'utente sposta i dati in un account di archiviazione di proprietà di terze parti.

Si consideri uno scenario con un utente che esegue PGAdmin all'interno di una macchina virtuale di Azure (VM) che si connette a un database di Azure per PostgreSQL singolo server di cui è stato eseguito il provisioning negli Stati Uniti occidentali. L'esempio seguente mostra come limitare l'accesso con endpoint pubblici nel database di Azure per PostgreSQL Singolo server usando i controlli di accesso alla rete.

* Disabilitare tutto il traffico del servizio di Azure al database di Azure per PostgreSQL Singolo server tramite l'endpoint pubblico impostando Consenti servizi *di Azure* su OFF. Assicurarsi che non vi siano indirizzi IP o intervalli autorizzati ad accedere al server tramite [regole del firewall](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules) o endpoint del servizio di rete [virtuale.](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet)

* Consentire il traffico verso il database di Azure per PostgreSQL Singolo server usando l'indirizzo IP privato della macchina virtuale. Per altre informazioni, vedere gli articoli sulle regole del firewall [Dell'endpoint del servizio](concepts-data-access-and-security-vnet.md) e [della rete virtuale.](howto-manage-vnet-using-portal.md)

* Nella macchina virtuale di Azure limitare l'ambito della connessione in uscita usando i gruppi di sicurezza di rete (NSG) e i tag del servizio come indicato di seguito.

    * Specificare una regola del gruppo di sicurezza di rete per consentire il traffico per *il tag di servizio e SQL. WestUS* - che consente solo la connessione al database di Azure per PostgreSQL singolo server negli Stati Uniti occidentali
    * Specificare una regola del gruppo di sicurezza di rete (con una priorità più alta) per negare il traffico per il tag di servizio - SQL - negando le connessioni al database PostgreSQL in tutte le areeSpecify an NSG rule (with a higher priority) to deny traffic for *Service Tag - SQL* - denying connections to PostgreSQL Database in all regions</br></br>

Al termine di questa configurazione, la macchina virtuale di Azure può connettersi solo al database di Azure per PostgreSQL singolo server nell'area Stati Uniti occidentali. Tuttavia, la connettività non è limitata a un singolo database di Azure per PostgreSQL singolo server. La macchina virtuale può comunque connettersi a qualsiasi database di Azure per PostgreSQL Singolo server nell'area degli Stati Uniti occidentali, inclusi i database che non fanno parte della sottoscrizione. Sebbene l'ambito di esfiltrazione dei dati nello scenario precedente sia stato ridotto a un'area specifica, non è stato eliminato completamente.</br>

Con Private Link, è ora possibile impostare controlli di accesso alla rete come gruppi di sicurezza di rete per limitare l'accesso all'endpoint privato. Viene quindi eseguito il mapping delle singole risorse PaaS di Azure a endpoint privati specifici. Un insider dannoso può accedere solo alla risorsa PaaS mappata (ad esempio un database di Azure per PostgreSQL singolo server) e nessun'altra risorsa.

## <a name="on-premises-connectivity-over-private-peering"></a>Connettività locale tramite peering privato

Quando ci si connette all'endpoint pubblico da computer locali, l'indirizzo IP deve essere aggiunto al firewall basato su IP utilizzando una regola del firewall a livello di server. Questo modello è adatto per consentire l'accesso ai singoli computer per i carichi di lavoro di sviluppo o test, ma è difficile da gestire in un ambiente di produzione.

Con Private Link, è possibile abilitare l'accesso cross-premise all'endpoint privato tramite [Express Route](https://azure.microsoft.com/services/expressroute/) (ER), peering privato o [tunnel VPN.](https://docs.microsoft.com/azure/vpn-gateway/) Successivamente possono disabilitare tutti gli accessi tramite endpoint pubblici e non utilizzare il firewall basato su IP.

## <a name="configure-private-link-for-azure-database-for-postgresql-single-server"></a>Configurare il collegamento privato per il database di Azure per PostgreSQL singolo serverConfigure Private Link for Azure Database for PostgreSQL Single server

### <a name="creation-process"></a>Processo di creazione

Private endpoints are required to enable Private Link. Questo può essere fatto utilizzando le seguenti guide pratiche.

* [Portale di Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal)
* [Cli](https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Processo di approvazione
Dopo che l'amministratore di rete crea l'endpoint privato (PE), l'amministratore PostgreSQL può gestire la connessione dell'endpoint privato (PEC) al database di Azure per PostgreSQL.Once the network admin creates the private endpoint (PE), the PostgreSQL admin can manage the private endpoint Connection (PEC) to Azure Database for PostgreSQL. Questa separazione dei compiti tra l'amministratore di rete e l'amministratore di database è utile per la gestione del database di Azure per la connettività PostgreSQL.This separation of duties between the network admin and the DBA is helpful for management of the Azure Database for PostgreSQL connectivity. 

* Passare alla risorsa del server Database di Azure per PostgreSQL nel portale di Azure.Navigate to the Azure Database for PostgreSQL server resource in the Azure portal. 
    * Selezionare le connessioni endpoint privato nel riquadro sinistro
    * Visualizza un elenco di tutte le connessioni endpoint privati
    * Endpoint privato (PE) corrispondente creato

![selezionare il portale endpoint privato](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Selezionare una singola connessione di endpoint privato dall'elenco.

![selezionare l'endpoint privato in attesa di approvazione](media/concepts-data-access-and-security-private-link/select-private-link.png)

* L'amministratore del server PostgreSQL può scegliere di approvare o rifiutare un PEC e, facoltativamente, aggiungere una breve risposta di testo.

![selezionare il messaggio dell'endpoint privato](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Dopo l'approvazione o il rifiuto, l'elenco rifletterà lo stato appropriato insieme al testo della risposta

![selezionare lo stato finale dell'endpoint privato](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-postgresql"></a>Usare casi di collegamento privato per il database di Azure per PostgreSQLUse cases of Private Link for Azure Database for PostgreSQL

I client possono connettersi all'endpoint privato dalla stessa rete virtuale, dalla rete virtuale con peered nella stessa area o tramite connessione da VNet a VNet tra aree. Inoltre, i client possono connettersi dall'ambiente locale tramite ExpressRoute, peering privato o tunneling VPN. Di seguito è riportato un diagramma semplificato che mostra i casi d'uso comuni.

![selezionare la panoramica dell'endpoint privato](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Connessione da una VM di Azure in una rete virtuale con peering
Configurare il [peering della rete virtuale](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) per stabilire la connettività al database di Azure per PostgreSQL - Server singolo da una macchina virtuale di Azure in una rete virtuale con peering.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Connessione da una VM di Azure in un ambiente da rete virtuale a rete virtuale
Configurare la connessione al [gateway VPN da VNet a VNet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) per stabilire la connettività a un database di Azure per PostgreSQL - Server singolo da una macchina virtuale di Azure in un'area o sottoscrizione diversa.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Connessione da un ambiente locale tramite VPN
Per stabilire la connettività da un ambiente locale al database di Azure per PostgreSQL - Server singolo, scegliere e implementare una delle opzioni seguenti:

* [Connessione da punto a sito](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Connessione VPN da sito a sito](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [Circuito ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Collegamento privato combinato con le regole del firewall

Le situazioni e i risultati seguenti sono possibili quando si utilizza Private Link in combinazione con le regole del firewall:

* Se non si configuraalcuna alcuna regola del firewall, per impostazione predefinita, nessun traffico sarà in grado di accedere al database di Azure per PostgreSQL Singolo server.

* Se si configura il traffico pubblico o un endpoint del servizio e si creano endpoint privati, i diversi tipi di traffico in ingresso vengono autorizzati in base al tipo corrispondente di regola del firewall.

* Se non si configura alcun endpoint di servizio o traffico pubblico e si creano endpoint privati, il database di Azure per PostgreSQL Singolo server è accessibile solo tramite gli endpoint privati. Se non si configura il traffico pubblico o un endpoint del servizio, dopo che tutti gli endpoint privati approvati vengono rifiutati o eliminati, nessun traffico sarà in grado di accedere al database di Azure per PostgreSQL Singolo server.

## <a name="deny-public-access-for-azure-database-for-postgresql-single-server"></a>Nega accesso pubblico per il database di Azure per PostgreSQL Singolo server

Se si desidera fare affidamento solo sugli endpoint privati per accedere al database di Azure per PostgreSQL Singolo server, è possibile disabilitare l'impostazione di tutti gli endpoint pubblici[(regole del firewall](concepts-firewall-rules.md) ed endpoint del [servizio VNet](concepts-data-access-and-security-vnet.md)) impostando la configurazione **Nega accesso di rete pubblico** nel server di database. 

Quando questa impostazione è impostata su YES solo le connessioni tramite endpoint privati sono consentite al database di Azure per PostgreSQL.When this setting is set to *YES* only connections via private endpoints are allowed to your Azure Database for PostgreSQL. Quando questa impostazione è impostata su NO i client possono connettersi al database di Azure per PostgreSQL in base all'impostazione dell'endpoint del firewall o del servizio VNet.When this setting is set to *NO* clients can connect to your Azure Database for PostgreSQL based on your firewall or VNet service endpoint setting. Inoltre, una volta che il valore dell'accesso alla rete privata è impostato per i clienti non può aggiungere e/o aggiornare le regole esistenti 'Firewall rules' e 'VNet service endpoint rule

> [!Note]
> Questa funzionalità è disponibile in tutte le aree di Azure in cui Database di Azure per PostgreSQL: singolo server supporta i livelli dei prezzi Scopo generale e Ottimizzato per la memoria.
>
> Questa impostazione non ha alcun impatto sulle configurazioni SSL e TLS per il database di Azure per postgreSQL singolo server.

Per informazioni su come impostare l'accesso **Nega accesso di rete pubblica** per il database di Azure per PostgreSQL Singolo server dal portale di Azure, vedere Come [configurare Nega accesso di rete pubblica](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità di sicurezza di Azure Database for PostgreSQL singolo server, vedere gli articoli seguenti:To learn more about Azure Database for PostgreSQL Single server security features, see the following articles:

* Per configurare un firewall per il database di Azure per PostgreSQL Singolo server, vedere [Supporto del firewall](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules).

* Per informazioni su come configurare un endpoint del servizio di rete virtuale per il database di Azure per PostgreSQL Singolo server, vedere [Configurare l'accesso da reti virtuali.](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet)

* Per una panoramica del database di Azure per PostgreSQL Connettività server singolo, vedere Database di Azure per architettura di [connettività PostgreSQLFor](https://docs.microsoft.com/azure/postgresql/concepts-connectivity-architecture) an overview of Azure Database for PostgreSQL Single server connectivity, see Azure Database for PostgreSQL Connectivity Architecture
---
title: Collegamento privato - Database di Azure per MySQLPrivate Link - Azure Database for MySQL
description: Informazioni sul funzionamento del collegamento privato per il database di Azure per MySQL.Learn how Private link works for Azure Database for MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: c2cc4986542404281424286882c046dec39f5daf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371291"
---
# <a name="private-link-for-azure-database-for-mysql"></a>Collegamento privato per il database di Azure per MySQLPrivate Link for Azure Database for MySQL

Il servizio Collegamento privato consente di connettersi a diversi servizi PaaS in Azure tramite un endpoint privato. Azure Private Link porta essenzialmente i servizi di Azure all'interno della rete virtuale privata (VNet). È possibile accedere alle risorse PaaS utilizzando l'indirizzo IP privato come qualsiasi altra risorsa nella rete virtuale.

Per un elenco ai servizi PaaS che supportano la funzionalità Private Link, consultare la [documentazione](https://docs.microsoft.com/azure/private-link/index)relativa a Private Link . Un endpoint privato è un indirizzo IP privato all'interno di una [rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) e una subnet specifiche.

> [!NOTE]
> Questa funzionalità è disponibile in tutte le aree di Azure in cui Azure Database for MySQL supporta i livelli dei prezzi Scopo generale e Ottimizzato per la memoria.

## <a name="data-exfiltration-prevention"></a>Impedire l'esfiltrazione dei dati

L'ex filtrazione dei dati nel database di Azure per MySQL è quando un utente autorizzato, ad esempio un amministratore del database, è in grado di estrarre i dati da un sistema e spostarli in un'altra posizione o sistema esterno all'organizzazione. Ad esempio, l'utente sposta i dati in un account di archiviazione di proprietà di terze parti.

Si consideri uno scenario con un utente che esegue MySQL Workbench all'interno di una macchina virtuale di Azure (VM) che si connette a un database di Azure per il server MySQL di cui è stato eseguito il provisioning negli Stati Uniti occidentali. L'esempio seguente mostra come limitare l'accesso con endpoint pubblici nel database di Azure per MySQL usando i controlli di accesso alla rete.

* Disabilitare tutto il traffico del servizio di Azure al database di Azure per MySQL tramite l'endpoint pubblico impostando Consenti servizi *di Azure* su OFF. Assicurarsi che non vi siano indirizzi IP o intervalli autorizzati ad accedere al server tramite [regole del firewall](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) o endpoint del servizio di rete [virtuale.](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet)

* Consentire il traffico verso il database di Azure per MySQL usando l'indirizzo IP privato della macchina virtuale. Per altre informazioni, vedere gli articoli sull'[endpoint servizio](concepts-data-access-and-security-vnet.md) e le [regole del firewall della rete virtuale](howto-manage-vnet-using-portal.md).

* Nella macchina virtuale di Azure limitare l'ambito della connessione in uscita usando i gruppi di sicurezza di rete (NSG) e i tag del servizio come indicato di seguito.

    * Specificare una regola del gruppo di sicurezza di rete per consentire il traffico per *il tag di servizio e SQL. WestUs* - che consente solo la connessione al database di Azure per MySQL negli Stati Uniti occidentali
    * Specificare una regola del gruppo di sicurezza di rete (con una priorità più alta) per negare il traffico per il tag di servizio - SQL - negando le connessioni all'aggiornamento al database di Azure per MySQL in tutte le areeSpecify an NSG rule (with a higher priority) to deny traffic for *Service Tag - SQL* - denying connections to Update to Azure Database for MySQL in all regions</br></br>

Al termine di questa configurazione, la macchina virtuale di Azure può connettersi solo al database di Azure per MySQL nell'area Stati Uniti occidentali. Tuttavia, la connettività non è limitata a un singolo database di Azure per MySQL.However, the connectivity isn't restricted to a single Azure Database for MySQL. La macchina virtuale può comunque connettersi a qualsiasi database di Azure per MySQL nell'area Stati Uniti occidentali, inclusi i database che non fanno parte della sottoscrizione. Sebbene l'ambito di esfiltrazione dei dati nello scenario precedente sia stato ridotto a un'area specifica, non è stato eliminato completamente.</br>

Con Private Link, è ora possibile impostare controlli di accesso alla rete come gruppi di sicurezza di rete per limitare l'accesso all'endpoint privato. Viene quindi eseguito il mapping delle singole risorse PaaS di Azure a endpoint privati specifici. Un insider dannoso può accedere solo alla risorsa PaaS mappata (ad esempio un database di Azure per MySQL) e nessun'altra risorsa.

## <a name="on-premises-connectivity-over-private-peering"></a>Connettività locale tramite peering privato

Quando ci si connette all'endpoint pubblico da computer locali, l'indirizzo IP deve essere aggiunto al firewall basato su IP utilizzando una regola del firewall a livello di server. Questo modello è adatto per consentire l'accesso ai singoli computer per i carichi di lavoro di sviluppo o test, ma è difficile da gestire in un ambiente di produzione.

Con Private Link, è possibile abilitare l'accesso cross-premise all'endpoint privato tramite [Express Route](https://azure.microsoft.com/services/expressroute/) (ER), peering privato o [tunnel VPN.](https://docs.microsoft.com/azure/vpn-gateway/) Successivamente possono disabilitare tutti gli accessi tramite endpoint pubblici e non utilizzare il firewall basato su IP.

## <a name="configure-private-link-for-azure-database-for-mysql"></a>Configurare il collegamento privato per il database di Azure per MySQLConfigure Private Link for Azure Database for MySQL

### <a name="creation-process"></a>Processo di creazione

Private endpoints are required to enable Private Link. Questo può essere fatto utilizzando le seguenti guide pratiche.

* [Portale di Azure](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal)
* [Cli](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Processo di approvazione
Dopo che l'amministratore di rete crea l'endpoint privato (PE), l'amministratore di MySQL può gestire la connessione dell'endpoint privato (PEC) al database di Azure per MySQL.Once the network admin creates the private endpoint (PE), the MySQL admin can manage the private endpoint Connection (PEC) to Azure Database for MySQL. Questa separazione dei compiti tra l'amministratore di rete e l'amministratore di database è utile per la gestione del database di Azure per la connettività MySQL.This separation of duties between the network admin and the DBA is helpful for management of the Azure Database for MySQL connectivity. 

* Passare alla risorsa database di Azure per MySQL nel portale di Azure.Navigate to the Azure Database for MySQL server resource in the Azure portal. 
    * Selezionare le connessioni endpoint privato nel riquadro sinistro
    * Visualizza un elenco di tutte le connessioni endpoint privati
    * Endpoint privato (PE) corrispondente creato

![selezionare il portale endpoint privato](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Selezionare una singola connessione di endpoint privato dall'elenco.

![selezionare l'endpoint privato in attesa di approvazione](media/concepts-data-access-and-security-private-link/select-private-link.png)

* L'amministratore del server MySQL può scegliere di approvare o rifiutare un PEC e, facoltativamente, aggiungere una breve risposta di testo.

![selezionare il messaggio dell'endpoint privato](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Dopo l'approvazione o il rifiuto, l'elenco rifletterà lo stato appropriato insieme al testo della risposta

![selezionare lo stato finale dell'endpoint privato](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mysql"></a>Usare casi di collegamento privato per il database di Azure per MySQLUse cases of Private Link for Azure Database for MySQL

I client possono connettersi all'endpoint privato dalla stessa rete virtuale, dalla rete virtuale con peered nella stessa area o tramite connessione da VNet a VNet tra aree. Inoltre, i client possono connettersi dall'ambiente locale tramite ExpressRoute, peering privato o tunneling VPN. Di seguito è riportato un diagramma semplificato che mostra i casi d'uso comuni.

![selezionare la panoramica dell'endpoint privato](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Connessione da una VM di Azure in una rete virtuale con peering
Configurare il [peering della rete virtuale](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) per stabilire la connettività al database di Azure per MySQL da una macchina virtuale di Azure in una rete virtuale con peering.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Connessione da una VM di Azure in un ambiente da rete virtuale a rete virtuale
Configurare la connessione al [gateway VPN da rete a rete virtuale](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) per stabilire la connettività a un database di Azure per MySQL da una macchina virtuale di Azure in un'area o sottoscrizione diversa.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Connessione da un ambiente locale tramite VPN
Per stabilire la connettività da un ambiente locale al database di Azure per MySQL, scegliere e implementare una delle opzioni seguenti:

* [Connessione da punto a sito](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Connessione VPN da sito a sito](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [Circuito ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Collegamento privato combinato con le regole del firewall

Le situazioni e i risultati seguenti sono possibili quando si utilizza Private Link in combinazione con le regole del firewall:

* Se non si configurano regole del firewall, per impostazione predefinita, nessun traffico sarà in grado di accedere al database di Azure per MySQL.If you don't configure any firewall rules, then by default, no traffic will be able to access the Azure Database for MySQL.

* Se si configura il traffico pubblico o un endpoint del servizio e si creano endpoint privati, i diversi tipi di traffico in ingresso vengono autorizzati in base al tipo corrispondente di regola del firewall.

* Se non si configura alcun endpoint di servizio o traffico pubblico e si creano endpoint privati, il database di Azure per MySQL sarà accessibile solo tramite gli endpoint privati. Se non si configura il traffico pubblico o un endpoint del servizio, dopo che tutti gli endpoint privati approvati vengono rifiutati o eliminati, nessun traffico sarà in grado di accedere al database di Azure per MySQL.If you don't configure public traffic or a service endpoint, after all approved private endpoints are rejected or deleted, no traffic will be able to access the Azure Database for MySQL.

## <a name="deny-public-access-for-azure-database-for-mysql"></a>Nega accesso pubblico per il database di Azure per MySQL

Se si desidera fare affidamento solo sugli endpoint privati per accedere al database di Azure per MySQL, è possibile disabilitare l'impostazione di tutti gli endpoint pubblici (ad esempio [le regole del firewall](concepts-firewall-rules.md) e gli endpoint del servizio [VNet](concepts-data-access-and-security-vnet.md)) impostando la configurazione **Nega accesso di rete pubblico** nel server di database. 

Quando questa impostazione è impostata su YES , solo le connessioni tramite endpoint privati sono consentite al database di Azure per MySQL.When this setting is set to *YES*, only connections via private endpoints are allowed to your Azure Database for MySQL. Quando questa impostazione è impostata su *NO,* i client possono connettersi al database di Azure per MySQL in base alle impostazioni del firewall o dell'endpoint del servizio VNet. Inoltre, una volta impostato il valore dell'accesso alla rete privata, non è possibile aggiungere e/o aggiornare le regole esistenti dell'endpoint del firewall e del servizio VNet.

> [!Note]
> Questa funzionalità è disponibile in tutte le aree di Azure in cui Database di Azure per PostgreSQL: singolo server supporta i livelli dei prezzi Scopo generale e Ottimizzato per la memoria.
>
> Questa impostazione non ha alcun impatto sulle configurazioni SSL e TLS per il database di Azure per MySQL.This setting does not have any impact on the SSL and TLS configurations for your Azure Database for MySQL.

Per informazioni su come impostare l'accesso **Nega accesso di rete pubblica** per il database di Azure per MySQL dal portale di Azure, vedere Come [configurare Nega accesso di rete pubblica](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità di sicurezza di Azure Database for MySQL, vedere gli articoli seguenti:To learn more about Azure Database for MySQL security features, see the following articles:

* Per configurare un firewall per il database di Azure per MySQL, vedere [Supporto del firewall](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules).

* Per informazioni su come configurare un endpoint del servizio di rete virtuale per il database di Azure per MySQL, vedere [Configurare l'accesso da reti virtuali.](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet)

* Per una panoramica del database di Azure per la connettività MySQL, vedere Database di Azure per l'architettura della [connettività MySQLFor](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture) an overview of Azure Database for MySQL connectivity, see Azure Database for MySQL Connectivity Architecture

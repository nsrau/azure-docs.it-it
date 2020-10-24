---
title: Collegamento privato-database di Azure per PostgreSQL-server singolo
description: Informazioni sul funzionamento del collegamento privato per database di Azure per PostgreSQL-singolo server.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9b93e3f42c6b635ced7fdca61cb2ffe4f74d19bc
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489507"
---
# <a name="private-link-for-azure-database-for-postgresql-single-server"></a>Collegamento privato per il database di Azure per il server PostgreSQL-Single

Collegamento privato consente di creare endpoint privati per database di Azure per PostgreSQL-server singolo e di conseguenza fornisce servizi di Azure all'interno della rete virtuale privata (VNet). L'endpoint privato espone un indirizzo IP privato che è possibile usare per connettersi al server di database proprio come qualsiasi altra risorsa in VNet.

Per un elenco dei servizi PaaS che supportano la funzionalità di collegamento privato, vedere la [documentazione](../private-link/index.yml)del collegamento privato. Un endpoint privato è un indirizzo IP privato all'interno di una [rete virtuale](../virtual-network/virtual-networks-overview.md) e una subnet specifiche.

> [!NOTE]
> La funzionalità di collegamento privato è disponibile solo per i server di database di Azure per PostgreSQL nei piani tariffari per utilizzo generico o con ottimizzazione per la memoria. Verificare che il server di database sia in uno di questi piani tariffari.

## <a name="data-exfiltration-prevention"></a>Impedire l'esfiltrazione dei dati

L'ex-filtraggio dei dati nel server singolo del database di Azure per PostgreSQL si verifica quando un utente autorizzato, ad esempio un amministratore di database, è in grado di estrarre i dati da un sistema e spostarli in un'altra posizione o sistema all'esterno dell'organizzazione. Ad esempio, l'utente sposta i dati in un account di archiviazione di proprietà di terze parti.

Si consideri uno scenario con un utente che esegue PGAdmin all'interno di una macchina virtuale (VM) di Azure che si connette a un server singolo di database di Azure per PostgreSQL con provisioning negli Stati Uniti occidentali. L'esempio seguente mostra come limitare l'accesso con gli endpoint pubblici nel database di Azure per il server singolo PostgreSQL usando i controlli di accesso alla rete.

* Disabilitare tutto il traffico del servizio di Azure nel server singolo del database di Azure per PostgreSQL tramite l'endpoint pubblico impostando *Consenti i servizi di Azure* su disattivato. Assicurarsi che non siano consentiti indirizzi IP o intervalli per accedere al server tramite [le regole del firewall](./concepts-firewall-rules.md) o gli [endpoint di servizio della rete virtuale](./concepts-data-access-and-security-vnet.md).

* Consentire solo il traffico al database di Azure per il server singolo PostgreSQL usando l'indirizzo IP privato della macchina virtuale. Per altre informazioni, vedere gli articoli sulle regole del firewall per [endpoint di servizio](concepts-data-access-and-security-vnet.md) e [VNet.](howto-manage-vnet-using-portal.md)

* Nella macchina virtuale di Azure limitare l'ambito della connessione in uscita usando i gruppi di sicurezza di rete (NSG) e i tag del servizio come indicato di seguito.

    * Specificare una regola NSG per consentire il traffico per il *tag di servizio = SQL. Westus* : consente solo la connessione al server singolo database di Azure per PostgreSQL negli Stati Uniti occidentali
    * Specificare una regola di NSG (con una priorità più alta) per negare il traffico per il *tag di servizio = SQL* negando le connessioni al database PostgreSQL in tutte le aree</br></br>

Al termine di questa installazione, la macchina virtuale di Azure può connettersi solo al server singolo database di Azure per PostgreSQL nell'area Stati Uniti occidentali. Tuttavia, la connettività non è limitata a un singolo server di database di Azure per PostgreSQL. La macchina virtuale può comunque connettersi a un singolo server di database di Azure per PostgreSQL nell'area Stati Uniti occidentali, inclusi i database che non fanno parte della sottoscrizione. Sebbene l'ambito di esfiltrazione dei dati nello scenario precedente sia stato ridotto a un'area specifica, non è stato eliminato completamente.</br>

Con collegamento privato, è ora possibile configurare controlli di accesso alla rete come gruppi per limitare l'accesso all'endpoint privato. Viene quindi eseguito il mapping delle singole risorse PaaS di Azure a endpoint privati specifici. Un insider malintenzionato può accedere solo alla risorsa PaaS mappata, ad esempio un server singolo di database di Azure per PostgreSQL, e a nessun'altra risorsa.

## <a name="on-premises-connectivity-over-private-peering"></a>Connettività locale tramite peering privato

Quando ci si connette all'endpoint pubblico da computer locali, è necessario aggiungere l'indirizzo IP al firewall basato su IP usando una regola del firewall a livello di server. Questo modello è adatto per consentire l'accesso ai singoli computer per i carichi di lavoro di sviluppo o test, ma è difficile da gestire in un ambiente di produzione.

Con il collegamento privato, è possibile abilitare l'accesso cross-premise all'endpoint privato usando [Express Route](https://azure.microsoft.com/services/expressroute/) (ER), il peering privato o il [tunnel VPN](../vpn-gateway/index.yml). Possono quindi disabilitare tutti gli accessi tramite endpoint pubblico e non usare il firewall basato su IP.

> [!NOTE]
> In alcuni casi, Database di Azure per PostgreSQL e la subnet della rete virtuale sono in sottoscrizioni diverse. In questi casi è necessario garantire le configurazioni seguenti:
> - Assicurarsi che per entrambe le sottoscrizioni sia registrato il provider di risorse **Microsoft. DBforPostgreSQL** . Per altre informazioni, fare riferimento a [resource-manager-registration][resource-manager-portal].

## <a name="configure-private-link-for-azure-database-for-postgresql-single-server"></a>Configurare il collegamento privato per il server singolo del database di Azure per PostgreSQL

### <a name="creation-process"></a>Processo di creazione

Per abilitare il collegamento privato sono necessari endpoint privati. Questa operazione può essere eseguita usando le guide alle procedure seguenti.

* [Azure portal](./howto-configure-privatelink-portal.md)
* [CLI](./howto-configure-privatelink-cli.md)

### <a name="approval-process"></a>Processo di approvazione
Quando l'amministratore di rete crea l'endpoint privato (PE), l'amministratore di PostgreSQL può gestire la connessione all'endpoint privato (PEC) al database di Azure per PostgreSQL. Questa separazione dei compiti tra l'amministratore di rete e l'amministratore di database è utile per la gestione della connettività del database di Azure per PostgreSQL. 

* Passare alla risorsa del server database di Azure per PostgreSQL nel portale di Azure. 
    * Selezionare le connessioni all'endpoint privato nel riquadro sinistro
    * Mostra un elenco di tutte le connessioni a endpoint privato (PECs)
    * Endpoint privato (PE) corrispondente creato

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-portal.png" alt-text="Selezionare il portale dell'endpoint privato":::

* Selezionare una singola connessione di endpoint privato dall'elenco.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link.png" alt-text="Selezionare il portale dell'endpoint privato":::

* L'amministratore del server PostgreSQL può scegliere di approvare o rifiutare un PEC e, facoltativamente, di aggiungere una risposta di testo breve.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-message.png" alt-text="Selezionare il portale dell'endpoint privato":::

* Dopo l'approvazione o il rifiuto, l'elenco riflette lo stato appropriato insieme al testo della risposta

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png" alt-text="Selezionare il portale dell'endpoint privato":::

## <a name="use-cases-of-private-link-for-azure-database-for-postgresql"></a>Casi d'uso di collegamento privato per database di Azure per PostgreSQL

I client possono connettersi all'endpoint privato dallo stesso VNet, VNet con peering nella stessa area o tramite una connessione da VNet a VNet tra le aree. Inoltre, i client possono connettersi dall'ambiente locale tramite ExpressRoute, peering privato o tunneling VPN. Di seguito è riportato un diagramma semplificato che mostra i casi d'uso comuni.

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-overview.png" alt-text="Selezionare il portale dell'endpoint privato":::

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Connessione da una VM di Azure in una rete virtuale con peering
Configurare il [peering VNet](../virtual-network/tutorial-connect-virtual-networks-powershell.md) per stabilire la connettività al database di Azure per PostgreSQL: server singolo da una macchina virtuale di Azure in un VNet con peering.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Connessione da una VM di Azure in un ambiente da rete virtuale a rete virtuale
Configurare la [connessione gateway VPN da VNet a VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) per stabilire la connettività a un database di Azure per PostgreSQL: server singolo da una macchina virtuale di Azure in un'area o in una sottoscrizione diversa.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Connessione da un ambiente locale tramite VPN
Per stabilire la connettività da un ambiente locale al database di Azure per PostgreSQL-server singolo, scegliere e implementare una delle opzioni seguenti:

* [Connessione da punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Connessione VPN da sito a sito](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

## <a name="private-link-combined-with-firewall-rules"></a>Collegamento privato combinato con le regole del firewall

Quando si usa il collegamento privato in combinazione con le regole del firewall, sono possibili le situazioni e i risultati seguenti:

* Se non si configurano le regole del firewall, per impostazione predefinita nessun traffico sarà in grado di accedere al server singolo del database di Azure per PostgreSQL.

* Se si configura il traffico pubblico o un endpoint di servizio e si creano endpoint privati, i diversi tipi di traffico in ingresso sono autorizzati in base al tipo corrispondente di regola del firewall.

* Se non si configura alcun traffico pubblico o endpoint di servizio e si creano endpoint privati, il server singolo del database di Azure per PostgreSQL è accessibile solo tramite gli endpoint privati. Se non si configura il traffico pubblico o un endpoint del servizio, dopo che tutti gli endpoint privati approvati sono stati rifiutati o eliminati, nessun traffico sarà in grado di accedere al server singolo del database di Azure per PostgreSQL.

## <a name="deny-public-access-for-azure-database-for-postgresql-single-server"></a>Negare l'accesso pubblico per il server singolo del database di Azure per PostgreSQL

Se si vuole fare affidamento solo su endpoint privati per accedere al proprio server di database di Azure per PostgreSQL, è possibile disabilitare l'impostazione di tutti gli endpoint pubblici ([regole del firewall](concepts-firewall-rules.md) ed [endpoint di servizio VNet](concepts-data-access-and-security-vnet.md)) impostando la configurazione di **accesso negato alla rete pubblica** sul server di database. 

Quando questa impostazione è impostata su *Sì* , al database di Azure per PostgreSQL sono consentite solo le connessioni tramite endpoint privati. Quando questa impostazione è impostata su *Nessun* client può connettersi al database di Azure per PostgreSQL in base all'impostazione del firewall o dell'endpoint del servizio VNet. Inoltre, una volta impostato il valore di accesso alla rete privata, i clienti non possono aggiungere e/o aggiornare le regole ' firewall rules ' è VNet service endpoint rules ' esistenti.

> [!Note]
> Questa funzionalità è disponibile in tutte le aree di Azure in cui database di Azure per PostgreSQL-server singolo supporta i piani tariffari per utilizzo generico e con ottimizzazione per la memoria.
>
> Questa impostazione non ha alcun effetto sulle configurazioni SSL e TLS per il server singolo del database di Azure per PostgreSQL.

Per informazioni su come impostare l' **accesso negato alla rete pubblica** per il server singolo del database di Azure per PostgreSQL da portale di Azure, vedere [come configurare l'accesso negato alla rete pubblica](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità di sicurezza a server singolo di database di Azure per PostgreSQL, vedere gli articoli seguenti:

* Per configurare un firewall per il server singolo del database di Azure per PostgreSQL, vedere [supporto del firewall](./concepts-firewall-rules.md).

* Per informazioni su come configurare un endpoint di servizio di rete virtuale per il server singolo del database di Azure per PostgreSQL, vedere [configurare l'accesso da reti virtuali](./concepts-data-access-and-security-vnet.md).

* Per una panoramica della connettività a server singolo di database di Azure per PostgreSQL, vedere [architettura di connettività per database di Azure per PostgreSQL](./concepts-connectivity-architecture.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
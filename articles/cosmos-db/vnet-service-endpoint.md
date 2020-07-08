---
title: Proteggere l'accesso a un account Azure Cosmos DB usando l'endpoint servizio di rete virtuale
description: Questo documento descrive il controllo dell'accesso alla rete virtuale e alla subnet per un account Azure Cosmos.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: d264ead87e7fa638830bf25fdb07983b164334b7
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698674"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Accedere ad Azure Cosmos DB dalle reti virtuali (VNet)

È possibile configurare l'account Azure Cosmos DB per consentire l'accesso solo da una subnet specifica di rete virtuale di Azure (VNet). Abilitando [Endpoint del servizio](../virtual-network/virtual-network-service-endpoints-overview.md) per accedere ad Azure Cosmos DB nella subnet all'interno di una rete virtuale, il traffico da tale subnet viene inviato ad Azure Cosmos DB con l'identità della subnet e della rete virtuale. Dopo aver abilitato l'endpoint del servizio Azure Cosmos DB, è possibile limitare l'accesso alla subnet aggiungendola all'account Azure Cosmos.

Per impostazione predefinita, un account di Azure Cosmos è accessibile da qualsiasi provider di origine purché la richiesta sia accompagnata da un token di autorizzazione valido. Quando si aggiunge una o più subnet all'interno delle reti virtuali, solo le richieste provenienti da tali subnet otterranno una risposta valida. Le richieste provenienti da qualsiasi altra origine riceveranno una risposta 403 (Non consentito). 

## <a name="frequently-asked-questions"></a>Domande frequenti

Ecco alcune domande frequenti sulla configurazione dell'accesso da reti virtuali:

### <a name="are-notebooks-and-mongo-shell-currently-compatible-with-virtual-network-enabled-accounts"></a>Azure Notebooks e Mongo Shell sono attualmente compatibili con gli account abilitati per la rete virtuale?

Al momento, l'integrazione di [Mongo Shell in Esplora dati di Cosmos DB](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/) e il [servizio Jupyter Notebook](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-jupyter-notebooks) non sono supportati con l'accesso alla rete virtuale. Questa implementazione è attualmente in fase di sviluppo attivo.

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>È possibile specificare l'endpoint servizio di rete virtuale e i criteri di controllo di accesso IP per un account Azure Cosmos? 

È possibile abilitare l'endpoint servizio di rete virtuale e i criteri di controllo di accesso IP (ossia firewall) per un account Azure Cosmos. Queste due funzionalità sono complementari e garantiscono collettivamente isolamento e sicurezza dell'account di Azure Cosmos. L'utilizzo del firewall IP garantisce che gli indirizzi IP statici possano accedere all'account. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Come è possibile limitare l'accesso alla subnet all'interno di una rete virtuale? 

Esistono due passaggi necessari per limitare l'accesso all'account Azure Cosmos da una subnet. In primo luogo, si consente al traffico dalla subnet di portare le rispettive subnet e l'identità di rete virtuale ad Azure Cosmos DB. Questa operazione viene eseguita abilitando l'endpoint del servizio per Azure Cosmos DB nella subnet. Successivamente viene aggiunta una regola nell'account di Azure Cosmos che specifica questa subnet come un'origine da cui è possibile accedere all'account.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Gli elenchi di accesso virtuale della rete virtuale e il Firewall IP rifiutano le richieste o le connessioni? 

Quando vengono aggiunte regole di accesso al firewall IP o alla rete virtuale, solo le richieste provenienti da origini autorizzate ricevono risposte valide. Le altre richieste vengono rifiutate con un errore 403 (Non consentito). È importante distinguere il firewall dell'account Azure Cosmos da un firewall a livello di connessione. L'origine può comunque connettersi al servizio e le connessioni stesse non vengono rifiutate.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Le richieste hanno iniziato a bloccarsi dopo aver abilitato l'endpoint del servizio ad Azure Cosmos DB nella subnet. Che cosa è successo?

Dopo aver abilitato l'endpoint di servizio per Azure Cosmos DB in una subnet, l'origine del traffico che raggiunge l'account passa dall'indirizzo IP pubblico alla rete virtuale e alla subnet. Se l'account Azure Cosmos dispone solo di un firewall basato su protocollo IP, il traffico dalla subnet del servizio abilitato non corrisponderebbe alle regole del firewall IP e pertanto sarebbe rifiutato. Esaminare i passaggi per eseguire facilmente la migrazione dal firewall basato su protocollo IP per il controllo di accesso basato sulla rete virtuale.

### <a name="are-additional-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>Sono necessarie autorizzazioni di Controllo degli accessi in base al ruolo aggiuntive per gli account Azure Cosmos con endpoint del servizio di rete virtuale?

Dopo aver aggiunto gli endpoint del servizio di rete virtuale a un account Azure Cosmos, per apportare qualsiasi modifica alle impostazioni dell'account è necessario accedere all'azione `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` per tutte le reti virtuali configurate nell'account Azure Cosmos. Questa operazione è necessaria perché il processo di autorizzazione convalida l'accesso alle risorse (incluse le risorse di database e di rete virtuale) prima di valutare le proprietà.
 
Viene convalidata l'autorizzazione per l'azione della risorsa di rete virtuale anche se l'utente non specifica gli elenchi di controllo di accesso di rete virtuale usando l'interfaccia della riga di comando di Azure. Attualmente, il piano di controllo di un account Azure Cosmos supporta l'impostazione dello stato completo dell'account Azure Cosmos. Uno dei parametri per le chiamate del piano di controllo è `virtualNetworkRules`. Se questo parametro non viene specificato, l'interfaccia della riga di comando di Azure esegue una chiamata Get Database per recuperare il parametro `virtualNetworkRules` e usa questo valore nella chiamata di aggiornamento.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Anche le reti virtuali con peering hanno accesso all'account Azure Cosmos? 
Solo la rete virtuale e le subnet aggiunte all'account Azure Cosmos possono avere accesso. Le reti virtuali con peering non possono accedere all'account fino a quando le subnet all'interno delle reti virtuali con peering non vengono aggiunte all'account.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Qual è il numero massimo di subnet consentite per accedere a un singolo account Cosmos? 
Attualmente, è consentito avere al massimo 256 subnet per un account Azure Cosmos.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>È possibile abilitare l'accesso da VPN ed Express Route? 
Per l'accesso all'account Azure Cosmos tramite ExpressRoute da locale, è necessario abilitare il peering Microsoft. Dopo avere inserito il firewall IP o le regole di accesso di rete virtuale, è possibile aggiungere gli indirizzi IP pubblici usati per il peering Microsoft sul firewall IP dell'account Azure Cosmos per consentire l'accesso dei servizi locali all'account Azure Cosmos. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>È necessario aggiornare le regole dei gruppi di sicurezza di rete (NSG)? 
Le regole NSG vengono utilizzate per limitare la connettività da e verso una subnet con la rete virtuale. Quando si aggiunge un endpoint del servizio per Azure Cosmos DB alla subnet, non è necessario aprire la connettività in uscita del gruppo di sicurezza di rete per l'account Azure Cosmos. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Gli endpoint di servizio sono disponibili per tutte le reti virtuali?
No, solo le reti virtuali di Azure Resource Manager hanno un endpoint di servizio abilitato. Le reti virtuali classiche non supportano gli endpoint di servizio.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>È possibile accettare le connessioni nei datacenter di Azure pubblici quando viene abilitato l'accesso all'endpoint del servizio per Azure Cosmos DB?  
Questa operazione è necessaria solo quando si vuole consentire l'accesso all'account Azure Cosmos DB da parte di altri servizi proprietari Azure come Azure Data Factory, Ricerca cognitiva di Azure o qualsiasi servizio distribuito in una specifica area di Azure.


## <a name="next-steps"></a>Passaggi successivi

* [Come limitare l'accesso all'account Azure Cosmos alle subnet nelle reti virtuali](how-to-configure-vnet-service-endpoint.md)
* [Come configurare il firewall IP per l'account Azure Cosmos](how-to-configure-firewall.md)


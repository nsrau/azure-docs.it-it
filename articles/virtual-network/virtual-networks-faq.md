---
title: Domande frequenti sulla rete virtuale di Azure | Documentazione Microsoft
description: "Risposte alle domande più frequenti sulla rete virtuale di Microsoft Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 54bee086-a8a5-4312-9866-19a1fba913d0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.openlocfilehash: 8800dc59306c349daba8f4d9703e0c713eed06ec
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Domande frequenti sulla rete virtuale di Azure

## <a name="virtual-network-basics"></a>Nozioni di base sulla rete virtuale

### <a name="what-is-an-azure-virtual-network-vnet"></a>Che cos'è una rete virtuale di Azure?
Una rete virtuale (VNet) di Azure è una rappresentazione della rete personalizzata nel cloud. È un isolamento logico del cloud di Azure dedicato alla sottoscrizione. È possibile usare le reti virtuali per eseguire il provisioning e gestire reti virtuali private (VPN) in Azure e facoltativamente collegare le reti virtuali con altre reti virtuali in Azure o con l'infrastruttura IT locale per creare soluzioni ibride o cross-premise. Ogni rete virtuale creata ha un blocco CIDR e può essere collegata ad altre reti virtuali e reti locali, purché i blocchi CIDR non si sovrappongano. È anche possibile controllare le impostazioni del server DNS per le reti virtuali e la segmentazione della rete virtuale in subnet.

Usare le reti virtuali per:

* Creare una rete virtuale privata dedicata solo cloud. Per una soluzione personalizzata, non è sempre necessaria una configurazione cross-premise. Quando si crea una rete virtuale, i servizi e le macchine virtuali all'interno della rete virtuale possono comunicare direttamente e in modo sicuro tra loro nel cloud. È ancora possibile configurare connessioni di endpoint per le VM e i servizi che richiedono la comunicazione Internet come parte della soluzione.
* Estendere in modo sicuro il data center. Con le reti virtuali è possibile creare VPN da sito a sito (S2S) tradizionali per la scalabilità sicura della capacità del data center. Le reti virtuali private S2S usano IPSEC per fornire una connessione sicura tra il gateway VPN della rete virtuale privata aziendale e Azure.
* Abilitare scenari cloud ibridi. Le reti virtuali offrono la flessibilità per supportare una gamma di scenari cloud ibridi. È possibile connettere applicazioni basate sul cloud in modo sicuro a qualsiasi tipo di sistema locale, come mainframe e sistemi Unix.

### <a name="how-do-i-get-started"></a>Come iniziare?
Per iniziare, vedere l'articolo [Documentazione sulla rete virtuale](https://docs.microsoft.com/azure/virtual-network/), che contiene una panoramica e informazioni sulla distribuzione per tutte le funzionalità di rete virtuale.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>È possibile usare reti virtuali senza connettività cross-premise?
Sì. È possibile usare una rete virtuale senza connetterla all'ambiente locale. Ad esempio, è possibile eseguire controller di dominio Active Directory di Windows Server e farm di SharePoint esclusivamente in una rete virtuale di Azure.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>È possibile eseguire l'ottimizzazione WAN tra reti virtuali o tra una rete virtuale e il data center locale?

Sì. È possibile distribuire [appliance virtuali di rete per l'ottimizzazione WAN](https://azure.microsoft.com/marketplace/?term=wan+optimization) di diversi fornitori tramite Azure Marketplace.

## <a name="configuration"></a>Configurazione

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Quali strumenti si usano per creare una rete virtuale?
Per creare o configurare una rete virtuale, è possibile usare gli strumenti seguenti:

* Portale di Azure
* PowerShell
* Interfaccia della riga di comando di Azure
* Un file di configurazione di rete (netcfg - solo per reti virtuali classiche). Vedere l'articolo [Configurare una rete virtuale usando un file di configurazione di rete](virtual-networks-using-network-configuration-file.md).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Quali intervalli di indirizzi è possibile usare nelle reti virtuali?
Qualsiasi intervallo di indirizzi IP definito in [RFC 1918](http://tools.ietf.org/html/rfc1918). Ad esempio, 10.0.0.0/16.

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>È possibile avere indirizzi IP pubblici nelle reti virtuali?
Sì. Per altre informazioni sugli intervalli di indirizzi IP pubblici, vedere [Create a virtual network](virtual-network-manage-network.md#create-a-virtual-network) (Creare una rete virtuale). Gli indirizzi IP pubblici non sono accessibili direttamente da Internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Esiste un limite al numero di subnet nella rete virtuale?
Sì. Vedere [Limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) per informazioni dettagliate. Gli spazi degli indirizzi della subnet non possono sovrapporsi.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?
Sì. Azure riserva alcuni indirizzi IP all'interno di ogni subnet. Il primo e l'ultimo indirizzo IP di ciascuna subnet sono riservati per motivi di conformità al protocollo, insieme agli indirizzi x.x.x.1-x.x.x.3 di ogni subnet, che vengono usati per i servizi di Azure.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Quanto piccole o grandi possono essere le reti virtuali e le subnet?
La subnet più piccola supportata è /29 e la più grande è /8 (in base alle definizioni di subnet CIDR).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>È possibile trasferire le reti VLAN in Azure usando reti virtuali?
No. Le reti virtuali sono sovrapposizioni di livello 3. Azure non supporta alcuna semantica di livello 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>È possibile specificare criteri di routing personalizzati nelle reti virtuali e nelle subnet?
Sì. È possibile creare una tabella di route e associarla a una subnet. Per altre informazioni sul routing in Azure, vedere [Panoramica sul routing](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Le reti virtuali supportano la distribuzione multicast o broadcast?
No. La distribuzione multicast o broadcast non è supportata.

### <a name="what-protocols-can-i-use-within-vnets"></a>Quali protocolli è possibile usare all'interno delle reti virtuali?
All'interno delle reti virtuali è possibile usare i protocolli TCP, UDP e ICMP TCP/IP. Unicast è supportato all'interno delle reti virtuali, fatta eccezione per Dynamic Host Configuration Protocol (DHCP) tramite Unicast (porta di origine UDP/68/porta di destinazione UDP/67). I pacchetti incapsulati IP in IP, multicast e broadcast e i pacchetti Generic Routing Encapsulation (GRE) sono bloccati all'interno delle reti virtuali. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>È possibile eseguire il ping dei router predefiniti all'interno di una rete virtuale?
No.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>È possibile usare tracert per diagnosticare la connettività?
No.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>È possibile aggiungere subnet dopo la creazione della rete virtuale?
Sì. Le subnet possono essere aggiunte alle reti virtuali in qualsiasi momento purché l'intervallo di indirizzi della subnet non faccia parte di un'altra subnet e vi sia sufficiente spazio disponibile nell'intervallo di indirizzi della rete virtuale.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>È possibile modificare le dimensioni della subnet dopo averla creata?
Sì. È possibile aggiungere, rimuovere, espandere o compattare una subnet se non sono presenti macchine virtuali o servizi distribuiti al suo interno.

### <a name="can-i-modify-subnets-after-i-created-them"></a>È possibile modificare le subnet dopo averle create?
Sì. È possibile aggiungere, rimuovere e modificare i blocchi CIDR utilizzati da una rete virtuale.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Se si eseguono i servizi in una rete virtuale, è possibile connettersi a Internet?
Sì. Tutti i servizi distribuiti all'interno di una rete virtuale possono effettuare la connessione in uscita a Internet. Per altre informazioni sulle connessioni Internet in uscita in Azure, vedere [Connessioni in uscita](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se si vuole effettuare la connessione in ingresso a una risorsa distribuita tramite Resource Manager, la risorsa deve avere un indirizzo IP pubblico assegnato. Per altre informazioni sugli indirizzi IP pubblici, vedere [Indirizzi IP pubblici](virtual-network-public-ip-address.md). A ciascun servizio cloud di Azure distribuito in Azure viene assegnato un indirizzo VIP indirizzabile pubblicamente. Per abilitare tali servizi in modo che accettino le connessioni da Internet, è necessario definire gli endpoint di input per i ruoli PaaS e gli endpoint per le macchine virtuali.

### <a name="do-vnets-support-ipv6"></a>Le reti virtuali supportano IPv6?
No. Al momento non è possibile usare IPv6 con le reti virtuali. È tuttavia possibile assegnare indirizzi IPv6 ai servizi di bilanciamento del carico di Azure per effettuare il bilanciamento del carico delle macchine virtuali. Per dettagli, vedere [Panoramica di IPv6 per Azure Load Balancer](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="can-a-vnet-span-regions"></a>Una rete virtuale può estendersi a più aree?
No. Una rete virtuale è limitata a una singola area. Tuttavia, una rete virtuale si estende su zone di disponibilità. Per altre informazioni sulle zone di disponibilità, vedere [Panoramica delle zone di disponibilità](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). È possibile connettere reti virtuali in diverse aree con il peering di rete virtuale. Per informazioni dettagliate, vedere [Panoramica del peering di rete virtuale](virtual-network-peering-overview.md).

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>È possibile connettere una rete virtuale a un'altra rete virtuale in Azure?
Sì. È possibile connettere una rete virtuale a un'altra usando:
- **Peering di rete virtuale**: per informazioni dettagliate, vedere [Panoramica del peering di rete virtuale](virtual-network-peering-overview.md).
- **Gateway VPN di Azure**: per informazioni dettagliate, vedere [Configurare una connessione da rete virtuale a rete virtuale](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Risoluzione del nome (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Quali sono le opzioni DNS per le reti virtuali?
Usare la tabella delle decisioni nella pagina [Risoluzione dei nomi per le VM e le istanze del ruolo](virtual-networks-name-resolution-for-vms-and-role-instances.md) come guida per tutte le opzioni DNS disponibili.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>È possibile specificare i server DNS per una rete virtuale?
Sì. È possibile specificare gli indirizzi IP del server DNS nelle impostazioni della rete virtuale. L'impostazione viene applicata come server DNS predefiniti per tutte le macchine virtuali nella rete virtuale.

### <a name="how-many-dns-servers-can-i-specify"></a>Quanti server DNS è possibile specificare?
Fare riferimento a [Limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>È possibile modificare i server DNS dopo aver creato la rete?
Sì. È possibile modificare l'elenco dei server DNS per la rete virtuale in qualsiasi momento. Se si modifica l'elenco dei server DNS, sarà necessario riavviare tutte le macchine virtuali nella rete virtuale affinché possano selezionare il nuovo server DNS.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Qual è il DNS fornito da Azure e funziona con le reti virtuali?
Il DNS fornito da Azure è un servizio DNS multi-tenant offerto da Microsoft. Azure registra tutte le macchine virtuali e le istanze del ruolo del servizio cloud in questo servizio. Questo servizio fornisce la risoluzione dei nomi dal nome host per le macchine virtuali e le istanze del ruolo contenute all'interno dello stesso servizio cloud e da FQDN per le macchine virtuali e le istanze del ruolo nella stessa rete virtuale. Per altre informazioni sul servizio DNS, vedere [Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo di Servizi cloud](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Esiste una limitazione ai primi 100 servizi cloud nella rete virtuale per la risoluzione dei nomi cross-tenant tramite il DNS fornito da Azure. Se si utilizza il proprio server DNS, questa limitazione non viene applicata.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>È possibile ignorare le impostazioni DNS in base alla macchina virtuale o al servizio cloud?
Sì. È possibile impostare i server DNS in base alla macchina virtuale o al servizio cloud per ignorare le impostazioni di rete predefinite. Tuttavia, è consigliabile usare quanto più possibile DNS a livello di rete.

### <a name="can-i-bring-my-own-dns-suffix"></a>È possibile trasferire il suffisso DNS personalizzato?
No. Non è possibile specificare un suffisso DNS personalizzato per le reti virtuali.

## <a name="connecting-virtual-machines"></a>Connessione di macchine virtuali

### <a name="can-i-deploy-vms-to-a-vnet"></a>È possibile distribuire le macchine virtuali su una rete virtuale?
Sì. Tutte le scheda di interfaccia di rete collegate a una macchina virtuale distribuita con il modello di distribuzione Resource Manager devono essere connesse a una rete virtuale. Facoltativamente, è possibile connettere le macchine virtuali distribuite con il modello di distribuzione classica a una rete virtuale.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Quali tipi di indirizzi IP è possibile assegnare alle macchine virtuali?
* **Privato:** assegnato a ogni scheda di interfaccia di rete all'interno di ogni macchina virtuale. L'indirizzo viene assegnato con il metodo statico o dinamico. Gli indirizzi IP privati vengono assegnati dall'intervallo specificato nelle impostazioni della subnet della rete virtuale. Alle risorse distribuite con il modello di distribuzione classica vengono assegnati indirizzi IP privati, anche se non sono connesse a una rete virtuale. Il comportamento del metodo di allocazione è diverso a seconda del fatto che una risorsa sia stata distribuita con il modello di distribuzione Resource Manager o classica: 

  - **Resource Manager**: un indirizzo IP privato assegnato con il metodo dinamico o statico rimane assegnato a una macchina virtuale (Resource Manager) finché la risorsa non viene eliminata. La differenza consiste nel fatto che quando si usa il metodo statico si seleziona l'indirizzo da assegnare, mentre quando si usa quello dinamico sceglie Azure. 
  - **Classica**: un indirizzo IP privato assegnato con il metodo dinamico può cambiare quando una macchina virtuale (classica) viene riavviata dopo essere stata arrestata (deallocata). Se è necessario assicurarsi che l'indirizzo IP privato per una risorsa distribuita tramite il modello di distribuzione classica non cambi mai, assegnare un indirizzo IP privato con il metodo statico.

* **Pubblico:** assegnato facoltativamente alle schede di interfaccia di rete collegate a macchine virtuali distribuite con il modello di distribuzione Azure Resource Manager. L'indirizzo può essere assegnato con il metodo di allocazione statica o dinamica. Tutte le macchine virtuali e le istanze del ruolo dei servizi cloud distribuite con il modello di distribuzione classica esistono all'interno di un servizio cloud, a cui viene assegnato un indirizzo IP virtuale (VIP) pubblico *dinamico*. Facoltativamente, è possibile assegnare un indirizzo IP pubblico *statico*, detto [indirizzo IP riservato](virtual-networks-reserved-public-ip.md), come indirizzo VIP. Gli indirizzi IP pubblici possono essere assegnati a singole macchine virtuali o istanze del ruolo dei servizi cloud distribuite con il modello di distribuzione classica. Questi sono detti [indirizzi IP pubblici a livello di istanza (ILPIP)](virtual-networks-instance-level-public-ip.md) e possono essere assegnati in modo dinamico.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>È possibile riservare un indirizzo IP privato per una macchina virtuale che verrà creata in un secondo momento?
No. Non è possibile riservare un indirizzo IP privato. Se un indirizzo IP privato è disponibile, viene assegnato a una macchina virtuale o a un'istanza del ruolo dal server DHCP, indipendentemente dal fatto che la macchina virtuale sia o meno quella a cui si vuole assegnare l'indirizzo IP privato. È possibile tuttavia cambiare l'indirizzo IP privato di una macchina virtuale già creata con qualsiasi indirizzo IP privato disponibile.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Gli indirizzi IP privati vengono modificati per le macchine virtuali in una rete virtuale?
Dipende. Se la macchina virtuale è stata distribuita tramite Resource Manager, non vengono modificati, indipendentemente dal fatto che l'indirizzo IP sia stato assegnato con il metodo di allocazione statica o dinamica. Se la macchina virtuale è stata distribuita tramite il modello di distribuzione classica, gli indirizzi IP possono essere modificati quando una macchina virtuale viene avviata dopo essere stata arrestata (deallocata). L'indirizzo viene rilasciato da una macchina virtuale distribuita tramite uno dei modelli di distribuzione quando la macchina virtuale viene eliminata.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>È possibile assegnare manualmente indirizzi IP alle schede di interfaccia di rete all'interno del sistema operativo di una macchina virtuale?
Sì, ma non è consigliabile a meno che non sia necessario, ad esempio quando si assegnano più indirizzi IP a una macchina virtuale. Per informazioni dettagliate, vedere [Aggiunta di più indirizzi IP a una macchina virtuale](virtual-network-multiple-ip-addresses-portal.md#os-config). Se l'indirizzo IP assegnato a una scheda di interfaccia di rete di Azure collegata a una macchina virtuale viene modificato e l'indirizzo IP all'interno del sistema operativo della macchina virtuale è differente, si perde la connessione alla macchina virtuale.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Se si arresta il funzionamento di uno slot di distribuzione del servizio cloud o di una macchina virtuale dall'interno del sistema operativo, cosa accade agli indirizzi IP?
Niente. Gli indirizzi IP, pubblici, privati o indirizzi VIP pubblici, rimangano assegnati alla relativa macchina virtuale o allo slot di distribuzione del servizio cloud.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>È possibile spostare le macchine virtuali da una subnet a un'altra in una rete virtuale senza ripetere la distribuzione?
Sì. Per altre informazioni, vedere l'articolo [Come spostare una macchina virtuale o un'istanza del ruolo in un'altra subnet](virtual-networks-move-vm-role-to-subnet.md).

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>È possibile configurare un indirizzo MAC statico per la macchina virtuale?
No. Un indirizzo MAC non può essere configurato in modo statico.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>L'indirizzo MAC rimarrà invariato per la macchina virtuale dopo averla creata?
Sì, l'indirizzo MAC rimane invariato sia che la macchina virtuale venga distribuita con il modello di distribuzione classica o Resource Manager, finché non viene eliminata. In precedenza se la macchina virtuale veniva arrestata, ovvero deallocata, l'indirizzo MAC veniva rilasciato. Ora viene mantenuto anche quando la macchina virtuale è in stato deallocato.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>È possibile eseguire la connessione a Internet da una macchina virtuale in una rete virtuale?
Sì. Tutte le macchine virtuali e le istanze del ruolo dei servizi cloud distribuite all'interno di una rete virtuale possono connettersi a Internet.

## <a name="azure-services-that-connect-to-vnets"></a>Servizi di Azure che si connettono a reti virtuali

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>È possibile usare le app Web del servizio app di Azure con una rete virtuale?
Sì. È possibile distribuire le app Web all'interno di una rete virtuale tramite un ambiente del servizio app. Se è stata configurata una connessione da punto a sito per la rete virtuale, tutte le app Web possono connettersi in modo sicuro e accedere alle risorse nella rete virtuale. Per altre informazioni, vedere gli articoli seguenti:

* [Creare app Web in un ambiente del servizio app](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integrare un'app in una rete virtuale di Azure](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Utilizzo dell’integrazione della rete virtuale e delle connessioni ibride con app Web](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json#hybrid-connections-and-app-service-environments)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>È possibile distribuire i servizi cloud con ruolo di lavoro e Web (PaaS) in una rete virtuale?
Sì. Facoltativamente, è possibile distribuire istanze del ruolo dei servizi cloud all'interno di reti virtuali. A tale scopo, specificare il nome della rete virtuale e i mapping dei ruoli e delle subnet nella sezione di configurazione della rete della configurazione del servizio. Non è necessario aggiornare i file binari.

### <a name="can-i-connect-a-virtual-machine-scale-set-vmss-to-a-vnet"></a>È possibile connettere un set di scalabilità di macchine virtuali (VMSS) a una rete virtuale?
Sì. È necessario connettere un set di scalabilità di macchine virtuali a una rete virtuale.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>È disponibile un elenco completo dei servizi di Azure da cui è possibile distribuire le risorse in una rete virtuale?

Sì. Per informazioni dettagliate, vedere [Integrazione della rete virtuale per i servizi di Azure](virtual-network-for-azure-services.md).

### <a name="which-azure-paas-resources-can-i-restrict-access-to-from-a-vnet"></a>A quali risorse PaaS di Azure è possibile limitare l'accesso da una rete virtuale?

Per le risorse distribuite tramite alcuni servizi PaaS di Azure (come Archiviazione di Azure e il database SQL di Azure), è possibile limitare l'accesso alla rete solo alle risorse in una rete virtuale tramite l'uso di endpoint del servizio Rete virtuale. Per informazioni dettagliate, vedere [Panoramica degli endpoint del servizio Rete virtuale](virtual-network-service-endpoints-overview.md).

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>È possibile spostare i servizi all’interno e all’esterno delle reti virtuali?
No. Non è possibile spostare i servizi all'interno e all'esterno delle reti virtuali. Per spostare una risorsa in un'altra rete virtuale, è necessario eliminarla e ridistribuirla.

## <a name="security"></a>Sicurezza

### <a name="what-is-the-security-model-for-vnets"></a>Che cos'è il modello di sicurezza per le reti virtuali?
Le reti virtuali sono isolate l'una dall'altra e gli altri servizi sono ospitati nell'infrastruttura di Azure. Una rete virtuale è un limite di attendibilità.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>È possibile limitare il flusso del traffico in ingresso o in uscita alle risorse connesse alla rete virtuale?
Sì. È possibile applicare [gruppi di sicurezza di rete](security-overview.md) a singole subnet all'interno di una rete virtuale e/o a schede di interfaccia di rete collegate a una rete virtuale.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>È possibile implementare un firewall tra le risorse connesse alla rete virtuale?
Sì. È possibile distribuire [appliance virtuali di rete](https://azure.microsoft.com/marketplace/?term=firewall) di diversi fornitori tramite Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Sono disponibili informazioni sulla protezione di reti virtuali?
Sì. Per informazioni dettagliate, vedere [Panoramica della sicurezza di rete di Azure](../security/security-network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>API, schemi e strumenti

### <a name="can-i-manage-vnets-from-code"></a>È possibile gestire le reti virtuali dal codice?
Sì. È possibile usare le API REST per le reti virtuali nei modelli di distribuzione [Azure Resource Manager](/rest/api/virtual-network) e [classica (Gestione dei servizi)](http://go.microsoft.com/fwlink/?LinkId=296833).

### <a name="is-there-tooling-support-for-vnets"></a>È disponibile il supporto degli strumenti per le reti virtuali?
Sì. Altre informazioni:
- Uso del portale di Azure per distribuire reti virtuali con i modelli di distribuzione [Azure Resource Manager](virtual-networks-create-vnet-arm-pportal.md) e [classica](virtual-networks-create-vnet-classic-pportal.md).
- Uso di PowerShell per gestire reti virtuali distribuite con i modelli di distribuzione [Resource Manager](/powershell/module/azurerm.network) e [classica](/powershell/module/azure/?view=azuresmps-3.7.0).
- Uso dell'interfaccia della riga di comando di Azure per distribuire e gestire reti virtuali distribuite con i modelli di distribuzione [Resource Manager](/cli/azure/network/vnet) e [classica](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-commands-to-manage-network-resources).  

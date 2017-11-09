---
title: Domande frequenti sulla rete virtuale di Azure | Documentazione Microsoft
description: "Risposte alle domande più frequenti sulla rete virtuale di Microsoft Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 54bee086-a8a5-4312-9866-19a1fba913d0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/18/2017
ms.author: jdial
ms.openlocfilehash: c71e188b74ebfd9420f840957e83190cf476b584
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Domande frequenti sulla rete virtuale di Azure

## <a name="virtual-network-basics"></a>Nozioni di base sulla rete virtuale

### <a name="what-is-an-azure-virtual-network-vnet"></a>Che cos'è una rete virtuale di Azure?
Una rete virtuale (VNet) di Azure è una rappresentazione della rete personalizzata nel cloud. È un isolamento logico del cloud di Azure dedicato alla sottoscrizione. È possibile usare le reti virtuali per eseguire il provisioning e gestire reti virtuali private (VPN) in Azure e facoltativamente collegare le reti virtuali con altre reti virtuali in Azure o con l'infrastruttura IT locale per creare soluzioni ibride o cross-premise. Ogni rete virtuale creata ha un blocco CIDR e può essere collegata ad altre reti virtuali e reti locali, purché i blocchi CIDR non si sovrappongano. È anche possibile controllare le impostazioni del server DNS per le reti virtuali e la segmentazione della rete virtuale in subnet.

Usare le reti virtuali per:

* Creare una rete virtuale privata dedicata solo cloud. Per una soluzione personalizzata, non è sempre necessaria una configurazione cross-premise. Quando si crea una rete virtuale, i servizi e le macchine virtuali all'interno della rete virtuale possono comunicare direttamente e in modo sicuro tra loro nel cloud. In questo modo il traffico viene mantenuto in modo sicuro all'interno della rete virtuale, ma è ancora possibile configurare connessioni di endpoint per le VM e i servizi che richiedono la comunicazione Internet come parte della soluzione.
* Estendere in modo sicuro il data center. Con le reti virtuali è possibile creare VPN da sito a sito (S2S) tradizionali per la scalabilità sicura della capacità del data center. Le reti virtuali private S2S usano IPSEC per fornire una connessione sicura tra il gateway VPN della rete virtuale privata aziendale e Azure.
* Abilitare scenari cloud ibridi. Le reti virtuali offrono la flessibilità per supportare una gamma di scenari cloud ibridi. È possibile connettere applicazioni basate sul cloud in modo sicuro a qualsiasi tipo di sistema locale, come mainframe e sistemi Unix.

### <a name="how-do-i-know-if-i-need-a-vnet"></a>Come scoprire se è necessaria una rete virtuale?
Per una tabella che consenta di scegliere la migliore opzione di progettazione della rete, vedere l'articolo [Reti virtuali](virtual-networks-overview.md).

### <a name="how-do-i-get-started"></a>Come iniziare?
Per iniziare, vedere l'articolo [Documentazione sulla rete virtuale](https://docs.microsoft.com/azure/virtual-network/), che contiene una panoramica e informazioni sulla distribuzione per tutte le funzionalità di rete virtuale.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>È possibile usare reti virtuali senza connettività cross-premise?
Sì. Si può usare una rete virtuale senza la connettività ibrida. Ciò è particolarmente utile se occorre eseguire controller di dominio Active Directory di Windows Server e farm di SharePoint in Azure.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>È possibile eseguire l'ottimizzazione WAN tra reti virtuali o tra una rete virtuale e il data center locale?

Sì. È possibile distribuire [appliance virtuali di rete per l'ottimizzazione WAN](https://azure.microsoft.com/marketplace/?term=wan+optimization) di diversi fornitori tramite Azure Marketplace.

## <a name="configuration"></a>Configurazione

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Quali strumenti si usano per creare una rete virtuale?
Per creare o configurare una rete virtuale, è possibile usare gli strumenti seguenti:

* Portale di Azure (per reti virtuali classiche e di Resource Manager).
* Un file di configurazione di rete (netcfg - solo per reti virtuali classiche). Vedere l'articolo [Configurare una rete virtuale usando un file di configurazione di rete](virtual-networks-using-network-configuration-file.md).
* PowerShell (per reti virtuali classiche e di Resource Manager).
* Interfaccia della riga di comando di Azure (per reti virtuali classiche e di Resource Manager).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Quali intervalli di indirizzi è possibile usare nelle reti virtuali?
Qualsiasi intervallo di indirizzi IP definito in [RFC 1918](http://tools.ietf.org/html/rfc1918). Ad esempio, 10.0.0.0/16.

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>È possibile avere indirizzi IP pubblici nelle reti virtuali?
Sì. Per altre informazioni sugli intervalli di indirizzi IP pubblici, vedere l'articolo [Spazio degli indirizzi IP pubblici in una rete virtuale](virtual-networks-public-ip-within-vnet.md). Gli indirizzi IP pubblici non saranno accessibili direttamente da Internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Esiste un limite al numero di subnet nella rete virtuale?
Sì. Per informazioni dettagliate, vedere l'articolo relativo ai [limiti di Azure](../azure-subscription-service-limits.md#networking-limits). Gli spazi degli indirizzi della subnet non possono sovrapporsi.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?
Sì. Azure riserva alcuni indirizzi IP all'interno di ogni subnet. Il primo e l’ultimo indirizzo IP delle subnet sono riservati per conformità al protocollo, con altri 3 indirizzi usati per i servizi di Azure.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Quanto piccole o grandi possono essere le reti virtuali e le subnet?
La subnet più piccola supportata è /29 e la più grande è /8 (usando le definizioni di subnet CIDR).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>È possibile trasferire le reti VLAN in Azure usando reti virtuali?
No. Le reti virtuali sono sovrapposizioni di livello 3. Azure non supporta alcuna semantica di livello 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>È possibile specificare criteri di routing personalizzati nelle reti virtuali e nelle subnet?
Sì. È possibile usare User Defined Routing (UDR). Per altre informazioni su UDR, vedere [Route e inoltro IP definiti dall'utente](virtual-networks-udr-overview.md).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Le reti virtuali supportano la distribuzione multicast o broadcast?
No. La distribuzione multicast o broadcast non è supportata.

### <a name="what-protocols-can-i-use-within-vnets"></a>Quali protocolli è possibile usare all'interno delle reti virtuali?
All'interno delle reti virtuali è possibile usare i protocolli TCP, UDP e ICMP TCP/IP. I pacchetti incapsulati IP in IP, multicast e broadcast e i pacchetti Generic Routing Encapsulation (GRE) sono bloccati all'interno delle reti virtuali. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>È possibile eseguire il ping dei router predefiniti all'interno di una rete virtuale?
No.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>È possibile usare tracert per diagnosticare la connettività?
No.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>È possibile aggiungere subnet dopo la creazione della rete virtuale?
Sì. Le subnet possono essere aggiunte alle reti virtuali in qualsiasi momento, purché l'indirizzo della subnet non faccia parte di un'altra subnet nella rete virtuale.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>È possibile modificare le dimensioni della subnet dopo averla creata?
Sì. È possibile aggiungere, rimuovere, espandere o compattare una subnet se non sono presenti macchine virtuali o servizi distribuiti al suo interno.

### <a name="can-i-modify-subnets-after-i-created-them"></a>È possibile modificare le subnet dopo averle create?
Sì. È possibile aggiungere, rimuovere e modificare i blocchi CIDR utilizzati da una rete virtuale.

### <a name="can-i-connect-to-the-internet-if-i-am-running-my-services-in-a-vnet"></a>È possibile effettuare la connessione a Internet se si eseguono i servizi in una rete virtuale?
Sì. Tutti i servizi distribuiti all'interno di una rete virtuale possono effettuare la connessione a Internet. A ciascun servizio cloud distribuito in Azure viene assegnato un indirizzo VIP indirizzabile pubblicamente. Per abilitare tali servizi in modo che accettino le connessioni da Internet, sarà necessario definire gli endpoint di input per i ruoli PaaS e gli endpoint per le macchine virtuali.

### <a name="do-vnets-support-ipv6"></a>Le reti virtuali supportano IPv6?
No. Al momento non è possibile usare IPv6 con le reti virtuali.

### <a name="can-a-vnet-span-regions"></a>Una rete virtuale può estendersi a più aree?
No. Una rete virtuale è limitata a una singola area.

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>È possibile connettere una rete virtuale a un'altra rete virtuale in Azure?
Sì. È possibile connettere una rete virtuale a un'altra usando quanto segue:
- Gateway VPN di Azure. Per informazioni dettagliate, vedere l'articolo [Configurare una connessione da rete virtuale a rete virtuale](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). 
- Peering di rete virtuale. Per informazioni dettagliate, vedere l'articolo [Peering di rete virtuale](virtual-network-peering-overview.md).

## <a name="name-resolution-dns"></a>Risoluzione del nome (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Quali sono le opzioni DNS per le reti virtuali?
Usare la tabella delle decisioni nella pagina [Risoluzione dei nomi per le VM e le istanze del ruolo](virtual-networks-name-resolution-for-vms-and-role-instances.md) come guida per tutte le opzioni DNS disponibili.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>È possibile specificare i server DNS per una rete virtuale?
Sì. È possibile specificare gli indirizzi IP del server DNS nelle impostazioni della rete virtuale. Questi verranno applicati come server DNS predefiniti per tutte le macchine virtuali nella rete virtuale.

### <a name="how-many-dns-servers-can-i-specify"></a>Quanti server DNS è possibile specificare?
Per informazioni dettagliate, vedere l'articolo relativo ai [limiti di Azure](../azure-subscription-service-limits.md#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>È possibile modificare i server DNS dopo aver creato la rete?
Sì. È possibile modificare l'elenco dei server DNS per la rete virtuale in qualsiasi momento. Se si modifica l'elenco dei server DNS, sarà necessario riavviare tutte le macchine virtuali nella rete virtuale affinché possano selezionare il nuovo server DNS.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Qual è il DNS fornito da Azure e funziona con le reti virtuali?
Il DNS fornito da Azure è un servizio DNS multi-tenant offerto da Microsoft. Azure registra tutte le macchine virtuali e le istanze del ruolo del servizio cloud in questo servizio. Questo servizio fornisce la risoluzione dei nomi dal nome host per le macchine virtuali e le istanze del ruolo contenute all'interno dello stesso servizio cloud e da FQDN per le macchine virtuali e le istanze del ruolo nella stessa rete virtuale. Per altre informazioni sul servizio DNS, vedere l'articolo [Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo](virtual-networks-name-resolution-for-vms-and-role-instances.md).

> [!NOTE]
> Al momento esiste una limitazione ai primi 100 servizi cloud nella rete virtuale per la risoluzione dei nomi cross-tenant tramite il DNS fornito da Azure. Se si utilizza il proprio server DNS, questa limitazione non viene applicata.
> 
> 

### <a name="can-i-override-my-dns-settings-on-a-per-vm--service-basis"></a>È possibile ignorare le impostazioni DNS in base alla macchina virtuale/al servizio?
Sì. È possibile impostare i server DNS in base al servizio cloud per ignorare le impostazioni di rete predefinite. Tuttavia, è consigliabile usare quanto più possibile DNS a livello di rete.

### <a name="can-i-bring-my-own-dns-suffix"></a>È possibile trasferire il suffisso DNS personalizzato?
No. Non è possibile specificare un suffisso DNS personalizzato per le reti virtuali.

## <a name="connecting-virtual-machines"></a>Connessione di macchine virtuali

### <a name="can-i-deploy-vms-to-a-vnet"></a>È possibile distribuire le macchine virtuali su una rete virtuale?
Sì. Tutte le scheda di interfaccia di rete collegate a una macchina virtuale distribuita con il modello di distribuzione Resource Manager devono essere connesse a una rete virtuale. Facoltativamente, è possibile connettere le macchine virtuali distribuite con il modello di distribuzione classica a una rete virtuale.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Quali tipi di indirizzi IP è possibile assegnare alle macchine virtuali?
* **Privato:** assegnato a ogni scheda di interfaccia di rete all'interno di ogni macchina virtuale. L'indirizzo viene assegnato con il metodo statico o dinamico. Gli indirizzi IP privati vengono assegnati dall'intervallo specificato nelle impostazioni della subnet della rete virtuale. Alle risorse distribuite con il modello di distribuzione classica vengono assegnati indirizzi IP privati, anche se non sono connesse a una rete virtuale. Un indirizzo IP privato assegnato con il metodo dinamico rimane assegnato a una risorsa finché questa non viene eliminata, nel caso di macchine virtuali o slot di distribuzione del servizio cloud. Un indirizzo IP privato assegnato con il metodo dinamico può cambiare quando una macchina virtuale viene riavviata dopo essere stata arrestata (deallocata). Un indirizzo IP privato assegnato con il metodo statico rimane assegnato a una risorsa finché questa non viene eliminata. Se è necessario assicurarsi che l'indirizzo IP privato per una risorsa non cambi mai fino a quando la risorsa non viene eliminata, assegnare un indirizzo IP privato con il metodo statico.
* **Pubblico:** assegnato facoltativamente alle schede di interfaccia di rete collegate a macchine virtuali distribuite con il modello di distribuzione Azure Resource Manager. L'indirizzo può essere assegnato con il metodo di allocazione statica o dinamica. Tutte le macchine virtuali e le istanze del ruolo dei servizi cloud distribuite con il modello di distribuzione classica esistono all'interno di un servizio cloud, a cui viene assegnato un indirizzo IP virtuale (VIP) pubblico *dinamico*. Facoltativamente, è possibile assegnare un indirizzo IP pubblico *statico*, detto [indirizzo IP riservato](virtual-networks-reserved-public-ip.md), come indirizzo VIP. Gli indirizzi IP pubblici possono essere assegnati a singole macchine virtuali o istanze del ruolo dei servizi cloud distribuite con il modello di distribuzione classica. Questi sono detti [indirizzi IP pubblici a livello di istanza (ILPIP)](virtual-networks-instance-level-public-ip.md) e possono essere assegnati in modo dinamico.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>È possibile riservare un indirizzo IP privato per una macchina virtuale che verrà creata in un secondo momento?
No. Non è possibile riservare un indirizzo IP privato. Se un indirizzo IP privato è disponibile, verrà assegnato a una macchina virtuale o a un'istanza del ruolo dal server DHCP, indipendentemente dal fatto che tale macchina virtuale sia o meno quella a cui si vuole assegnare l'indirizzo IP privato. È possibile, tuttavia, cambiare l'indirizzo IP privato di una macchina virtuale già creata con qualsiasi indirizzo IP privato disponibile.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Gli indirizzi IP privati vengono modificati per le macchine virtuali in una rete virtuale?
Dipende. Un indirizzo IP privato dinamico rimane assegnato a una macchina virtuale finché questa non viene arrestata, ovvero deallocata, o eliminata. Un indirizzo IP privato statico rimane associato a una macchina virtuale finché questa non viene eliminata.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>È possibile assegnare manualmente indirizzi IP alle schede di interfaccia di rete all'interno del sistema operativo di una macchina virtuale?
Sì, ma non è consigliabile. La modifica manuale dell'indirizzo IP di una scheda di interfaccia di rete all'interno del sistema operativo di una macchina virtuale può causare la perdita di connettività della macchina virtuale, se l'indirizzo IP assegnato a una scheda di interfaccia di rete nella macchina virtuale di Azure venisse modificato.

### <a name="what-happens-to-my-ip-addresses-if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system"></a>Cosa accade agli indirizzi IP se si arresta il funzionamento di uno slot di distribuzione del servizio cloud o di una macchina virtuale dall'interno del sistema operativo?
Niente. Gli indirizzi IP, pubblici, privati o indirizzi VIP pubblici, rimangano assegnati alla relativa macchina virtuale o allo slot di distribuzione del servizio cloud. Gli indirizzi dinamici vengono rilasciati solo se la macchina virtuale viene arrestata, ovvero deallocata, o eliminata oppure se lo slot di distribuzione del servizio cloud viene eliminato. Facendo clic sul pulsante **Arresta** di una macchina virtuale all'interno del portale di Azure, lo stato viene impostato su Arrestato (deallocato). In questo caso la macchina virtuale perde il relativo indirizzo IP.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-re-deploying"></a>È possibile spostare le macchine virtuali da una subnet a un'altra in una rete virtuale senza ripetere la distribuzione?
Sì. Per altre informazioni, vedere l'articolo [Come spostare una macchina virtuale o un'istanza del ruolo in un'altra subnet](virtual-networks-move-vm-role-to-subnet.md).

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>È possibile configurare un indirizzo MAC statico per la macchina virtuale?
No. Un indirizzo MAC non può essere configurato in modo statico.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-it-has-been-created"></a>L'indirizzo MAC rimarrà invariato per la macchina virtuale dopo averla creata?
Sì, l'indirizzo MAC rimane invariato sia che la macchina virtuale venga distribuita con il modello di distribuzione classica o Resource Manager, finché non viene eliminata. In precedenza se la macchina virtuale veniva arrestata, ovvero deallocata, l'indirizzo MAC veniva rilasciato. Ora viene mantenuto anche quando la macchina virtuale è in stato deallocato.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>È possibile eseguire la connessione a Internet da una macchina virtuale in una rete virtuale?
Sì. Tutte le macchine virtuali e le istanze del ruolo dei servizi cloud distribuite all'interno di una rete virtuale possono connettersi a Internet.

## <a name="azure-services-that-connect-to-vnets"></a>Servizi di Azure che si connettono a reti virtuali

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>È possibile usare le app Web del servizio app di Azure con una rete virtuale?
Sì. È possibile distribuire le app Web all'interno di una rete virtuale tramite un ambiente del servizio app. Tutte le app Web possono connettersi in modo sicuro e accedere alle risorse in una rete virtuale di Azure, se è stata configurata una connessione da punto a sito per la rete virtuale. Per altre informazioni, vedere gli articoli seguenti:

* [Creare app Web in un ambiente del servizio app](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Integrare un'app in una rete virtuale di Azure](../app-service/web-sites-integrate-with-vnet.md)
* [Utilizzo dell’integrazione della rete virtuale e delle connessioni ibride con app Web](../app-service/web-sites-integrate-with-vnet.md#hybrid-connections-and-app-service-environments)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>È possibile distribuire i servizi cloud con ruolo di lavoro e Web (PaaS) in una rete virtuale?
Sì. Facoltativamente, è possibile distribuire istanze del ruolo dei servizi cloud all'interno di reti virtuali. A tale scopo, specificare il nome della rete virtuale e i mapping dei ruoli e delle subnet nella sezione di configurazione della rete della configurazione del servizio. Non è necessario aggiornare i file binari.

### <a name="can-i-connect-a-virtual-machine-scale-set-vmss-to-a-vnet"></a>È possibile connettere un set di scalabilità di macchine virtuali (VMSS) a una rete virtuale?
Sì. È necessario connettere un set di scalabilità di macchine virtuali a una rete virtuale.

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>È possibile spostare i servizi all’interno e all’esterno delle reti virtuali?
No. Non è possibile spostare i servizi all'interno e all'esterno delle reti virtuali. Sarà necessario eliminare e ridistribuire il servizio per spostarlo in un'altra rete virtuale.

## <a name="security"></a>Sicurezza

### <a name="what-is-the-security-model-for-vnets"></a>Che cos'è il modello di sicurezza per le reti virtuali?
Le reti virtuali sono completamente isolate l’una dall’altra e gli altri servizi sono ospitati nell'infrastruttura di Azure. Una rete virtuale è un limite di attendibilità.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>È possibile limitare il flusso del traffico in ingresso o in uscita alle risorse connesse alla rete virtuale?
Sì. È possibile applicare [gruppi di sicurezza di rete](virtual-networks-nsg.md) a singole subnet all'interno di una rete virtuale e/o a schede di interfaccia di rete collegate a una rete virtuale.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>È possibile implementare un firewall tra le risorse connesse alla rete virtuale?
Sì. È possibile distribuire [appliance virtuali di rete](https://azure.microsoft.com/en-us/marketplace/?term=firewall) di diversi fornitori tramite Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Sono disponibili informazioni sulla protezione di reti virtuali?
Sì. Per informazioni dettagliate, vedere l'articolo [Panoramica della sicurezza di rete di Azure](../security/security-network-overview.md).

## <a name="apis-schemas-and-tools"></a>API, schemi e strumenti

### <a name="can-i-manage-vnets-from-code"></a>È possibile gestire le reti virtuali dal codice?
Sì. È possibile usare le API REST per le reti virtuali nei modelli di distribuzione [Azure Resource Manager](https://msdn.microsoft.com/library/mt163658.aspx) e [classica (Gestione dei servizi)](http://go.microsoft.com/fwlink/?LinkId=296833).

### <a name="is-there-tooling-support-for-vnets"></a>È disponibile il supporto degli strumenti per le reti virtuali?
Sì. Altre informazioni:
- Uso del portale di Azure per distribuire reti virtuali con i modelli di distribuzione [Azure Resource Manager](virtual-networks-create-vnet-arm-pportal.md) e [classica](virtual-networks-create-vnet-classic-pportal.md).
- Uso di PowerShell per gestire reti virtuali distribuite con i modelli di distribuzione [Resource Manager](/powershell/resourcemanager/azurerm.network/v3.1.0/azurerm.network.md) e [classica](/powershell/module/azure/?view=azuresmps-3.7.0).
- Uso dell'[Interfaccia della riga di comando di Azure](../virtual-machines/azure-cli-arm-commands.md#azure-network-commands-to-manage-network-resources) per gestire reti virtuali distribuite con entrambi i modelli di distribuzione.  

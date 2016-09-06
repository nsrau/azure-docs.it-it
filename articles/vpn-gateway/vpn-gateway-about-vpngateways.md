<properties 
   pageTitle="Informazioni sul gateway VPN | Microsoft Azure"
   description="Informazioni sulle connessione del gateway VPN per le reti virtuali di Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="cherylmc" />

# Informazioni sul gateway VPN


Il gateway VPN è una raccolta di risorse usata per inviare il traffico di rete tra reti virtuali e percorsi locali. I gateway vengono usati per connessioni da sito a sito, da punto a sito ed ExpressRoute. Viene usato anche per l'invio del traffico tra più reti virtuali in Azure, ovvero da rete virtuale a rete virtuale.

Per creare una connessione, si aggiunge un gateway di rete virtuale a una rete virtuale e si configurano le risorse aggiuntive e le relative impostazioni del gateway VPN. Ogni rete virtuale può avere un solo gateway di rete virtuale per tipo di gateway. Ad esempio, è possibile configurare un gateway di rete virtuale che usa -GatewayType Vpn e una che usa -GatewayType ExpressRoute.

Per informazioni sui requisiti del gateway, vedere la sezione relativa ai [requisiti del gateway](vpn-gateway-about-vpn-gateway-settings.md#requirements). Per la velocità effettiva aggregata stimata, vedere la sezione relativa alle [impostazioni del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#aggthroughput). Per informazioni sui prezzi, vedere [Gateway VPN Prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway). Per i limiti del servizio e delle sottoscrizioni, vedere [Limiti relativi alla rete](../articles/azure-subscription-service-limits.md#networking-limits).

Quando si configura un gateway VPN, le istruzioni seguite dipendono dal modello di distribuzione usato per creare la rete virtuale. Ad esempio, se la rete virtuale è stata creata usando il modello di distribuzione classica, per creare e configurare le impostazioni del gateway VPN si usano le linee guida e le istruzioni per il modello di distribuzione classica. Per altre informazioni, vedere [Confronto tra distribuzione di Azure Resource Manager e classica: comprensione dei modelli di implementazione e dello stato delle risorse](../resource-manager-deployment-model.md).

Le sezioni successive contengono tabelle che elencano le informazioni seguenti per la configurazione:

- Modello di distribuzione disponibile
- Strumenti di configurazione disponibili
- Collegamenti che visualizzano direttamente un articolo, se disponibile


Usare i diagrammi e le descrizioni forniti per selezionare la topologia di configurazione più adatta alle proprie esigenze. I diagrammi illustrano le principali topologie di base, ma è possibile creare configurazioni più complesse usando i diagrammi come riferimento. Ogni configurazione si basa sulle impostazioni del gateway VPN selezionate.

### Configurazione delle impostazioni del gateway VPN

Il gateway VPN è una raccolta di risorse ed è possibile configurarne alcune usando uno strumento e quindi passare a un altro per configurare altre risorse. Attualmente, non è possibile configurare ogni risorsa del gateway VPN nel portale di Azure. Le istruzioni riportate negli articoli per ogni configurazione indicano se è necessario uno strumento specifico. Se si adotta il modello di distribuzione classica, al momento è possibile lavorare nel portale classico o usare PowerShell. Per informazioni sulle singole impostazioni disponibili, vedere [About VPN Gateway settings](vpn-gateway-about-vpn-gateway-settings.md) (Informazioni sulle impostazioni del gateway VPN).



## Da sito a sito e multisito

### Da sito a sito

Una connessione da sito a sito (S2S) avviene tramite un tunnel VPN IPsec/IKE (IKEv1 o IKEv2). Questo tipo di connessione richiede un dispositivo VPN che si trova in locale con un indirizzo IP pubblico assegnato e non dietro una NAT. Le connessioni S2S possono essere usate per le configurazioni cross-premise e ibride.

![Connessione da sito a sito](./media/vpn-gateway-about-vpngateways/demos2s.png "Da sito a sito")


### Multisito

È possibile creare e configurare una connessione VPN tra la rete virtuale e più reti locali. Quando si lavora con più connessioni, è necessario usare un tipo di VPN basato su route (gateway dinamico per le reti virtuali classiche). Poiché una rete virtuale può avere un solo gateway di rete virtuale, tutte le connessioni che usano il gateway condividono la larghezza di banda disponibile. Questo tipo di configurazione è spesso definito connessione "multisito".
 

![Connessione multisito](./media/vpn-gateway-about-vpngateways/demomulti.png "multisito")

### Metodi e modelli di distribuzione

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## Tra reti virtuali

La connessione di una rete virtuale a un'altra rete virtuale (da rete virtuale a rete virtuale) è simile alla connessione di una rete virtuale a un percorso di sito locale. Entrambi i tipi di connettività usano un gateway VPN di Azure per fornire un tunnel sicuro tramite IPsec/IKE. È anche possibile combinare una comunicazione tra reti virtuali con configurazioni multisito. Questo permette di definire topologie di rete che consentono di combinare la connettività cross-premise con la connettività tra reti virtuali.

Le reti virtuali connesse possono essere:

- Nella stessa area o in aree diverse
- Nella stessa sottoscrizione o in sottoscrizioni diverse
- Nello stesso modello di distribuzione o in modelli diversi



![Connessione tra reti virtuali](./media/vpn-gateway-about-vpngateways/demov2v.png "tra reti virtuali")



### Connessioni tra modelli di distribuzione

Azure offre attualmente di due modelli di distribuzione: classica e Resource Manager. Se si usa Azure da qualche tempo, si dispone probabilmente di VM e istanze del ruolo di Azure in esecuzione su una rete virtuale classica. Le VM e le istanze del ruolo più recenti potrebbero invece essere in esecuzione su una rete virtuale creata in Resource Manager. Si crea una connessione tra le reti virtuali per consentire alle risorse di una rete virtuale di comunicare direttamente con le risorse di un'altra.


### Metodi e modelli di distribuzione

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

### Peering reti virtuali

È possibile usare il peering reti virtuali per creare la connessione, purché la configurazione delle reti virtuali soddisfi determinati requisiti. Peering reti virtuali non usa un gateway di rete virtuale. Il [peering reti virtuali](../virtual-network/virtual-network-peering-overview.md) è attualmente disponibile in versione di anteprima.



## Da punto a sito

Una configurazione da punto a sito (P2S) consente di creare una connessione sicura alla rete virtuale da un singolo computer client. P2S è una connessione VPN tramite SSTP (Secure Sockets Tunneling Protocol). Le connessioni P2S non richiedono un dispositivo VPN o un indirizzo IP pubblico per funzionare. Per stabilire la connessione VPN, avviarla dal computer client. È la soluzione ideale quando ci si vuole connettere alla rete virtuale da una posizione remota, ad esempio da casa o durante una riunione, oppure quando solo pochi client devono connettersi a una rete virtuale.


![Connessione da punto a sito](./media/vpn-gateway-about-vpngateways/demop2s.png "da punto a sito")

### Metodi e modelli di distribuzione

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]


## ExpressRoute

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

Per altre informazioni su ExpressRoute, vedere [Panoramica tecnica relativa a ExpressRoute](../expressroute/expressroute-introduction.md).


## Connessioni coesistenti da sito a sito ed ExpressRoute

ExpressRoute è una connessione dedicata diretta dalla rete WAN (non sulla rete Internet pubblica) a servizi Microsoft come Azure. Il traffico VPN da sito a sito viaggia crittografato sulla rete Internet pubblica. La possibilità di configurare connessioni VPN da sito a sito ed ExpressRoute per la stessa rete virtuale offre diversi vantaggi.

È possibile configurare una VPN da sito a sito come percorso di failover sicuro per ExpressRoute oppure usare VPN da sito a sito per connettersi a siti che non fanno parte della rete, ma che sono connessi tramite ExpressRoute. Questa configurazione richiede due gateway di rete virtuale per la stessa rete virtuale, uno che usa -GatewayType Vpn e l'altro che usa -GatewayType ExpressRoute.


![Connessioni coesistenti](./media/vpn-gateway-about-vpngateways/demoer.png "expressroute-da sito a sito")


### Metodi e modelli di distribuzione

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]


## Passaggi successivi

Per altre informazioni sui gateway VPN, vedere [Domande frequenti sul Gateway VPN](vpn-gateway-vpn-faq.md).

Connettere il percorso locale a una rete virtuale. Vedere [Creare una rete virtuale con una connessione VPN da sito a sito tramite il portale di Azure e Azure Resource Manager](vpn-gateway-howto-site-to-site-resource-manager-portal.md).





 

<!---HONumber=AcomDC_0831_2016-->
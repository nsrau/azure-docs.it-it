<properties 
   pageTitle="Pianificazione e progettazione per il gateway VPN | Microsoft Azure"
   description="Informazioni sulla pianificazione e progettazione del gateway VPN per le connessioni Da rete virtuale a rete virtuale cross-premise e ibride"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>  
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/21/2016"
   ms.author="cherylmc"/>  

# Pianificazione e progettazione per il gateway VPN

Le operazioni di pianificazione e progettazione di connessioni cross-premise e da rete virtuale a rete virtuale possono essere molto semplici o piuttosto complicate, a seconda delle esigenze di rete. Questo articolo illustra in dettaglio alcune considerazioni di base sulla progettazione e sulla pianificazione.

## Pianificazione


### <a name="compare"></a>Opzioni di connettività di più sedi locali

Se si desidera connettere i siti locali in modo sicuro a una rete virtuale, sono disponibili tre modi diversi: Da sito a sito, Da punto a sito ed ExpressRoute. Confrontare le diverse connessioni cross-premise disponibili. La scelta dell'opzione può dipendere da diversi fattori, ad esempio:


- Che tipo di velocità effettiva richiede la soluzione?
- Si desidera comunicare sulla rete Internet pubblica tramite VPN sicura o tramite una connessione privata?
- Si ha un indirizzo IP pubblico disponibile per l'utilizzo?
- Si prevede di utilizzare un dispositivo VPN? In tal caso, è compatibile?
- Si connette un numero limitato di computer o si desidera una connessione permanente per il sito?
- Che tipo di gateway VPN è necessario per la soluzione che si desidera creare?
- Quale SKU del gateway è opportuno usare?


La tabella seguente può aiutare nella scelta della migliore opzione di connettività per la soluzione.


[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]



### <a name="gwrequire"></a>Requisiti del gateway per tipo di VPN e SKU

[AZURE.INCLUDE [vpn-gateway-gwsku](../../includes/vpn-gateway-gwsku-include.md)]

Per informazioni sugli SKU del gateway, vedere [Impostazioni dello SKU del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

<br> La tabella seguente illustra i tipi di gateway e la velocità effettiva aggregata stimata. La velocità effettiva aggregata stimata può essere un fattore determinante per la progettazione. I prezzi variano a seconda dello SKU del gateway. Per informazioni sui prezzi, vedere [Gateway VPN Prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway/). La tabella è valida per entrambi i modelli di distribuzione classica e di Gestione risorse.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]



### <a name="wf"></a>Flusso di lavoro

L'elenco seguente descrive il flusso di lavoro comune per la connettività cloud:

1.	Progettare e pianificare la topologia di connettività ed elencare gli spazi di indirizzi per tutte le reti a cui connettersi.
2.	Creare una rete virtuale di Azure
3.	Creare un gateway VPN per la rete virtuale.
4.	Creare e configurare le connessioni a reti locali o altre reti virtuali, se necessario.
5.	Creare e configurare una connessione Da punto a sito per il gateway VPN di Azure, se necessario.
 

## Progettazione

### <a name="topologies"></a>Topologie di connessione

Per prima cosa, consultare i diagrammi nell'articolo [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md). L'articolo contiene i diagrammi di base, i modelli di distribuzione per ogni topologia (Azure Resource Manager o classica) e gli strumenti di distribuzione che è possibile usare per distribuire la configurazione.

### <a name="designbasics"></a>Nozioni di base sulla progettazione

Le sezioni seguenti illustrano le nozioni di base del gateway VPN. Inoltre, è importante prendere in considerazione le [limitazioni dei servizi di rete](../articles/azure-subscription-service-limits.md#networking-limits).


#### <a name="subnets"></a>Informazioni sulle subnet

Quando si creano le connessioni, è necessario considerare gli intervalli di indirizzi della subnet. Gli intervalli di indirizzi della subnet non possono sovrapporsi. Per subnet sovrapposta si intende una rete virtuale o una posizione locale che contiene lo stesso spazio di indirizzi dell'altra. È necessario che i tecnici di rete che si occupano delle reti locali definiscano un intervallo da usare per la subnet o lo spazio di indirizzi IP di Azure. È necessario avere uno spazio di indirizzi che non viene usato nella rete locale.

È importante evitare che le subnet si sovrappongano anche quando si utilizzano connessioni Da rete virtuale a rete virtuale. La creazione di una connessione Da rete virtuale a rete virtuale non riuscirà se le subnet si sovrappongono e un indirizzo IP è presente sia nella rete virtuale di origine che in quella di destinazione. Azure non potrà instradare i dati all'altra rete virtuale, perché l'indirizzo di destinazione fa parte della rete virtuale di origine.

I gateway VPN richiedono una subnet specifica chiamata subnet del gateway. Per poter funzionare correttamente, tutte le subnet del gateway devono essere denominate GatewaySubnet. Assicurarsi di non assegnare alla subnet gateway un nome diverso e non distribuire VM o altri elementi alla subnet gateway. Vedere [Subnet del gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="local"></a>Informazioni sui gateway di rete locali

Il gateway di rete locale in genere fa riferimento al percorso locale. Nel modello di distribuzione classica il gateway di rete locale è definito come sito di rete locale. Quando si configura un gateway di rete locale, si assegna un nome, si specifica l'indirizzo IP pubblico del dispositivo VPN locale e si indicano i prefissi di indirizzo che si trovano nel percorso locale. Azure esamina i prefissi di indirizzo di destinazione per il traffico di rete, consulta la configurazione specificata per il gateway di rete locale e indirizza i pacchetti di conseguenza. È possibile modificare i prefissi di indirizzo in base alle esigenze. Per altre informazioni, vedere [Gateway di rete locali](vpn-gateway-about-vpn-gateway-settings.md#lng).


#### <a name="gwtype"></a>Informazioni sui tipi di gateway

È importante selezione il tipo di gateway corretto per la topologia in uso. Se si seleziona il tipo errato, il gateway non funzionerà correttamente. Il tipo di gateway specifica il modo in cui il gateway stesso si connette ed è un'impostazione di configurazione necessaria per il modello di distribuzione di Azure Resource Manager.

Ecco i tipi di gateway VPN:

- VPN
- ExpressRoute

#### <a name="connectiontype"></a>Informazioni sui tipi di connessione

Ogni configurazione richiede un tipo di connessione specifico. Ecco i tipi di connessione:

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient


#### <a name="vpntype"></a>Informazioni sui tipi di VPN

Ogni configurazione richiede un tipo di VPN specifico. Se si combinano due configurazioni, ad esempio creando una connessione da sito a sito e una connessione da punto a sito alla stessa rete virtuale, è necessario usare un tipo di VPN che soddisfi i requisiti di entrambe le connessioni.

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Le tabelle seguenti mostrano il tipo di VPN e il relativo mapping a ogni configurazione di connessione. Assicurarsi che il tipo di VPN per il gateway corrisponda alla configurazione che si vuole creare.


[AZURE.INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="devices"></a>Dispositivi VPN per connessioni da sito a sito

Per configurare una connessione da sito a sito, indipendentemente dal modello di distribuzione, sono necessari gli elementi seguenti:

- Un dispositivo VPN compatibile con i gateway VPN di Azure
- Un indirizzo IP IPv4 pubblico che non si trovi dietro un NAT

È necessario avere esperienza nella configurazione del dispositivo VPN oppure chiedere a qualcuno di farlo al proprio posto. Per altre informazioni sui dispositivi VPN, vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md). L'articolo sui dispositivi VPN contiene informazioni su dispositivi convalidati e requisiti per i dispositivi che non sono stati convalidati, oltre a collegamenti a documenti di configurazione del dispositivo, se disponibili.

### <a name="forcedtunnel"></a>Prendere in considerazione il routing con tunneling forzato

Per la maggior parte delle configurazioni è possibile impostare il tunneling forzato. Il tunneling forzato consente di reindirizzare o "forzare" tutto il traffico associato a Internet verso la posizione locale tramite un tunnel VPN da sito a sito per l'ispezione e il controllo. Si tratta di un requisito di sicurezza critico per la maggior parte dei criteri IT aziendali.

Senza il tunneling forzato, il traffico associato a Internet dalle macchine virtuali in Azure raggiungerà direttamente Internet attraversando sempre l'infrastruttura di rete di Azure, senza l'opzione che consente di ispezionare o controllare il traffico. L'accesso a Internet non autorizzato potrebbe provocare la divulgazione di informazioni o altri tipi di violazioni della sicurezza.

**Diagramma tunneling forzato**

![Connessione di tunneling forzato](./media/vpn-gateway-plan-design/forced-tunnel.png "tunneling forzato")  

È possibile configurare una connessione di tunneling forzato in entrambi i modelli di distribuzione e tramite strumenti diversi. Per altre informazioni, vedere la tabella seguente. La tabella verrà aggiornata man mano che per questa configurazione risultano disponibili nuovi articoli, modelli di distribuzione e strumenti. Quando un articolo risulterà disponibile, nella tabella sarà presente un collegamento diretto.

[AZURE.INCLUDE [vpn-gateway-table-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)]



## Passaggi successivi

Per altre informazioni che aiutano a semplificare la progettazione, vedere gli articoli [Domande frequenti sul gateway VPN](vpn-gateway-vpn-faq.md) e [Informazioni sui gateway VPN](vpn-gateway-about-vpngateways.md).

Per altre informazioni sulle impostazioni specifiche del gateway, vedere [Informazioni sulle impostazioni del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md).

<!---HONumber=AcomDC_0928_2016-->
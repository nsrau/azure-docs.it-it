<properties 
   pageTitle="Informazioni sui gateway VPN per Rete virtuale | Microsoft Azure"
   description="Informazioni sugli SKU del gateway VPN Basic, Standard e Prestazioni elevate, sulla coesistenza del gateway VPN ed ExpressRoute, sui tipi di routing del gateway statico e dinamico e sui requisiti del gateway per la connettività della rete virtuale."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/10/2015"
   ms.author="cherylmc" />

# Informazioni sui gateway VPN

I gateway VPN vengono usati per inviare traffico di rete tra reti virtuali e percorsi locali o tra più reti virtuali. Quando si crea un gateway, è importante tenere in considerazione diversi fattori. È necessario sapere quale SKU del gateway si desidera usare, il tipo di routing necessario per la configurazione (dinamico o statico) e il dispositivo VPN che si intende usare se la configurazione ne richiede uno.

## SKU del gateway
Sono disponibili 3 SKU del gateway VPN: Basic, Standard e Prestazioni elevate. La tabella seguente illustra i tipi di gateway e la velocità effettiva aggregata stimata. I prezzi variano a seconda dello SKU del gateway. Per informazioni sui prezzi, vedere [Gateway VPN Prezzi](http://azure.microsoft.com/pricing/details/vpn-gateway/).

| SKU | Coesistenza gateway VPN ed ExpressRoute | Velocità effettiva del gateway di ExpressRoute | Velocità effettiva del gateway VPN | Tunnel IPsec massimi del gateway VPN |
|-------------|-----------------------------------|---------------------------------|------------------------|-------------------------------|
| Basic | No | 500 Mbps | 100 Mbps | 10 |
| Standard | Sì | 1000 Mbps | 100 Mbps | 10 |
| Prestazioni elevate | Sì | 2000 Mbps | 200 Mbps | 30 |

**Nota:** la velocità effettiva della VPN è una stima approssimativa basata sulle misurazioni tra reti virtuali nella stessa area di Azure. Tale dato non costituisce una garanzia di ciò che è possibile ottenere per connessioni cross-premise tramite Internet, ma deve essere considerato come una misurazione massima possibile.

## Tipi di gateway

Esistono due tipi di gateway: con *routing statico* (noto anche come VPN basata su criteri) e con *routing dinamico* (noto anche come VPN basata su route). Alcune configurazioni e alcuni dispositivi VPN funzionano solo con un determinato tipo di routing. Quando si crea un gateway VPN, selezionare il tipo di gateway necessario per la configurazione, assicurandosi che il dispositivo VPN che si seleziona supporti anche tale tipo di routing.

Se ad esempio si intende usare contemporaneamente una configurazione Site-to-Site e una configurazione Point-to-Site, è necessario configurare un gateway VPN con routing dinamico. Le configurazioni Site-to-Site funzionano con gateway con routing statico, mentre le configurazioni Point-to-Site richiedono un gateway con routing dinamico. Poiché entrambe le connessioni useranno lo stesso gateway, è necessario selezionare il tipo di gateway che supporta entrambe le configurazioni.

È opportuno verificare inoltre che il dispositivo VPN supporti il tipo di gateway, la configurazione e i parametri IPsec/IKE necessari. Se ad esempio si desidera creare un gateway dinamico e il dispositivo VPN non supporta le VPN basate su route, è necessario riesaminare le opzioni disponibili. È possibile decidere di acquisire un dispositivo VPN diverso che supporti i gateway dinamici oppure creare una connessione del gateway VPN che supporti un gateway con routing statico. Se in un secondo momento si acquisisce un dispositivo VPN in grado di supportare un gateway con routing dinamico, è sempre possibile ricreare il gateway come dinamico per usare il dispositivo. In tal caso, sarà necessario solo ricreare il gateway. Non sarà necessario ricreare la rete virtuale.

Di seguito sono riportati i due tipi di gateway:

- **Routing statico:** i gateway con routing statico supportano **le VPN basate su criteri**. Le VPN basate su criteri reindirizzano i pacchetti tramite tunnel IPsec con selettori di traffico basati sulle combinazioni di prefissi di indirizzo tra la rete locale e la rete virtuale di Azure. I selettori di traffico o criteri vengono in genere definiti come un elenco di accesso nelle configurazioni VPN.

	>[AZURE.NOTE]Non tutte le configurazioni sono compatibili con i gateway VPN con routing statico. Ad esempio, le configurazioni multisito, le configurazioni tra reti virtuali e le connessioni Point-to-Site richiedono tutte gateway con routing dinamico. Negli articoli sono illustrati i requisiti di gateway per ogni configurazione.

- **Routing dinamico:** i gateway con routing dinamico implementano **VPN basate su route**. Le VPN basate su route usano le "route" nella tabella di inoltro IP o di routing per reindirizzare i pacchetti nelle interfacce tunnel VPN corrispondenti. Le interfacce tunnel consentono quindi di crittografare o decrittografare i pacchetti all'interno e all'esterno dei tunnel. I criteri o selettori di traffico per le VPN basate su route vengono configurati come any-to-any (o caratteri jolly).

## Requisiti del gateway

Nella tabella seguente vengono elencati i requisiti per i gateway VPN statici e dinamici.


| **Proprietà** | **Gateway VPN con routing statico** | **Gateway VPN con routing dinamico** | **Gateway VPN standard** | **Gateway VPN con Prestazioni elevate** |
|-----------------------------------------|--------------------------------|-----------------------------------------------------------------------|-----------------------------------|----------------------------------|
| Connettività Site-to-Site | Configurazione VPN basata su criteri | Configurazione VPN basata su route | Configurazione VPN basata su route | Configurazione VPN basata su route |
| Connettività Point-to-Site | Non supportato | Supportato (può coesistere con Site-to-Site) | Supportato (può coesistere con Site-to-Site) | Supportato (può coesistere con Site-to-Site) |
| Metodo di autenticazione | Chiave condivisa | -Chiave condivisa per la connettività Site-to-Site -Certificati per la connettività Point-to-Site | -Chiave condivisa per la connettività Site-to-Site -Certificati per la connettività Point-to-Site | -Chiave condivisa per la connettività Site-to-Site -Certificati per la connettività Point-to-Site |
| Numero massimo di connessioni Site-to-Site | 1 | 10 | 10 | 30 |
| Numero massimo di connessioni Point-to-Site | Non supportato | 128 | 128 | 128 |
| Supporto routing attivo (BGP) | Non supportato | Non supportato | Non supportato | Non supportato |


## Passaggi successivi

Selezionare il dispositivo VPN per la configurazione. Vedere [Informazioni sui dispositivi e i gateway VPN per la connettività di rete virtuale](http://go.microsoft.com/fwlink/p/?LinkID=615934).

Configurare la rete virtuale. Per le connessioni cross-premise, vedere gli articoli seguenti:

- [Configurare una connessione cross-premise Site-to-Site a una rete virtuale di Azure](vpn-gateway-site-to-site-create.md)
- [Configurare una connessione VPN Point-to-Site a una rete virtuale di Azure](vpn-gateway-point-to-site-create.md)
- [Configurare una VPN Site-to-Site mediante il servizio RRAS (Routing and Remote Access Service) di Windows Server 2012](https://msdn.microsoft.com/library/dn636917.aspx)

Se si desidera configurare un gateway VPN, vedere [Connessione di più siti locali a una rete virtuale](http://go.microsoft.com/fwlink/p/?LinkId=615106).

Se si desidera modificare il tipo di gateway VPN, vedere [Modifica del tipo di routing di un gateway di rete virtuale](http://go.microsoft.com/fwlink/p/?LinkId=615109).

Se si desidera connettere più siti in una rete virtuale, vedere [Connessione di più siti locali a una rete virtuale](http://go.microsoft.com/fwlink/p/?LinkID=615106).

 

<!---HONumber=62-->
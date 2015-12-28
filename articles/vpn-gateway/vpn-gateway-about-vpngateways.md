<properties 
   pageTitle="Informazioni sui gateway VPN per la connettività cross-premises delle reti virtuali | Microsoft Azure"
   description="Informazioni sui gateway VPN, che possono essere utilizzati per le connessioni cross-premise per le configurazioni ibride. In questo articolo vengono descritti gli SKU del gateway (Basic, Standard e Prestazioni elevate), le configurazioni coesistenti di gateway VPN ed ExpressRoute, i tipi di routing del gateway (statico, dinamico, basato su criteri, basato su route) e i requisiti del gateway per la connettività della rete virtuale."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/15/2015"
   ms.author="cherylmc" />

# Informazioni sui gateway VPN

I gateway VPN vengono usati per inviare traffico di rete tra reti virtuali e percorsi locali. Vengono inoltre utilizzati per inviare il traffico tra più reti virtuali in Azure. Quando si crea un gateway, è importante tenere in considerazione alcuni fattori.
 
Durante la pianificazione, tenere presente quanto segue:

- La SKU del gateway che si desidera utilizzare
- Il tipo di routing del gateway per la connessione
- Il dispositivo VPN, se necessario per la connessione

## SKU del gateway

Sono disponibili 3 SKU del Gateway VPN:

- Basic
- Standard
- Prestazioni elevate

La tabella seguente illustra i tipi di gateway e la velocità effettiva aggregata stimata. I prezzi variano a seconda dello SKU del gateway. Per informazioni sui prezzi, vedere [Gateway VPN Prezzi](http://azure.microsoft.com/pricing/details/vpn-gateway/).

| SKU | Coesistenza gateway VPN ed ExpressRoute | Velocità effettiva del gateway di ExpressRoute | Velocità effettiva del gateway VPN | Tunnel IPsec massimi del gateway VPN |
|-------------|-----------------------------------|---------------------------------|------------------------|-------------------------------|
| Basic | No | 500 Mbps | 100 Mbps | 10 |
| Standard | Sì | 1000 Mbps | 100 Mbps | 10 |
| Prestazioni elevate | Sì | 2000 Mbps | 200 Mbps | 30 |

**Nota:** la velocità effettiva della VPN è una stima approssimativa basata sulle misurazioni tra reti virtuali nella stessa area di Azure. Tale dato non costituisce una garanzia di ciò che è possibile ottenere per connessioni cross-premise tramite Internet, ma deve essere considerato come una misurazione massima possibile.

## Tipi di routing del gateway

Esistono due tipi di routing del gateway:

- **Basato su criteri:** i gateway basati su criteri erano precedentemente chiamati *gateway statici*. La funzionalità di un gateway statico non è cambiata, anche se il nome è stato modificato. Questo tipo di gateway supporta le VPN basate su criteri. Le VPN basate su criteri reindirizzano i pacchetti tramite tunnel IPsec con selettori di traffico basati sulle combinazioni di prefissi di indirizzo tra la rete locale e la rete virtuale di Azure. I selettori di traffico o criteri vengono in genere definiti come un elenco di accesso nelle configurazioni VPN.
 
- **Basato su route:** i gateway basati su route erano precedentemente chiamati *gateway dinamici*. La funzionalità di un gateway dinamico non è cambiata, anche se il nome è stato modificato. I gateway basati su route implementano VPN basate su route. Le VPN basate su route usano le "route" nella tabella di inoltro IP o di routing per reindirizzare i pacchetti nelle interfacce tunnel VPN corrispondenti. Le interfacce tunnel consentono quindi di crittografare o decrittografare i pacchetti all'interno e all'esterno dei tunnel. I criteri o selettori di traffico per le VPN basate su route vengono configurati come any-to-any (o caratteri jolly).

Alcune connessioni (ad esempio da punto a sito e tra reti virtuali) funzionano solo con un tipo di routing del gateway specifico. Saranno visualizzati i requisiti di gateway elencati nell'articolo che corrisponde allo scenario di connessione che si desidera creare.

Anche i dispositivi VPN hanno limitazioni di configurazione. Quando si crea un gateway VPN, selezionare il tipo di routing del gateway necessario per la connessione, assicurandosi di verificare che il dispositivo VPN da utilizzare supporti anche tale tipo di routing. Per ulteriori informazioni, vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md).

Se, ad esempio, si intende usare contemporaneamente una connessione da sito a sito e una connessione da punto a sito, è necessario configurare un gateway VPN basato su route. Sebbene sia vero che le connessioni da sito a sito funzionano con gateway basati su criteri, le connessioni da punto a sito richiedono un tipo di gateway basato su route. Poiché entrambe le connessioni useranno lo stesso gateway, è necessario selezionare il tipo di gateway che le supporta entrambe. Inoltre, il dispositivo VPN utilizzato deve supportare anche le configurazioni basate su route.


## Requisiti del gateway

Nella tabella seguente vengono elencati i requisiti per i gateway VPN statici e dinamici.


| **Proprietà** | **Gateway VPN basato su criteri** | **Gateway VPN basato su route** | **Gateway VPN standard** | **Gateway VPN con Prestazioni elevate** |
|-----------------------------------------|--------------------------------|-----------------------------------------------------------------------|-----------------------------------|----------------------------------|
| Connettività Site-to-Site | Configurazione VPN basata su criteri | Configurazione VPN basata su route | Configurazione VPN basata su route | Configurazione VPN basata su route |
| Connettività Point-to-Site | Non supportato | Supportato (può coesistere con Site-to-Site) | Supportato (può coesistere con Site-to-Site) | Supportato (può coesistere con Site-to-Site) |
| Metodo di autenticazione | Chiave condivisa | -Chiave condivisa per la connettività Site-to-Site -Certificati per la connettività Point-to-Site | -Chiave condivisa per la connettività Site-to-Site -Certificati per la connettività Point-to-Site | -Chiave condivisa per la connettività Site-to-Site -Certificati per la connettività Point-to-Site |
| Numero massimo di connessioni Site-to-Site | 1 | 10 | 10 | 30 |
| Numero massimo di connessioni Point-to-Site | Non supportato | 128 | 128 | 128 |
| Supporto routing attivo (BGP) | Non supportato | Non supportato | Non supportato | Non supportato |


## Passaggi successivi

Selezionare il dispositivo VPN per la configurazione. Vedere [Informazioni sui dispositivi e i gateway VPN per la connettività di rete virtuale](vpn-gateway-about-vpn-devices.md).





 

<!---HONumber=AcomDC_1217_2015-->
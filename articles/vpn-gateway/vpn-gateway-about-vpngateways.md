<properties 
   pageTitle="Informazioni sui gateway VPN per la connettività cross-premises delle reti virtuali | Microsoft Azure"
   description="Informazioni sui gateway VPN, che possono essere utilizzati per le connessioni cross-premise per le configurazioni ibride. In questo articolo vengono descritti gli SKU del gateway (Basic, Standard e Prestazioni elevate), le configurazioni coesistenti di gateway VPN ed ExpressRoute, i tipi di routing del gateway (statico, dinamico, basato su criteri, basato su route) e i requisiti del gateway per la connettività della rete virtuale per entrambi i modelli di distribuzione classica e di Gestione risorse."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/19/2016"
   ms.author="cherylmc" />

# Informazioni sui gateway VPN

I gateway VPN vengono usati per inviare traffico di rete tra reti virtuali e percorsi locali. Vengono inoltre usati per inviare il traffico tra più reti virtuali in Azure. Quando si crea un gateway, è importante tenere in considerazione alcuni fattori.

- La SKU del gateway che si desidera utilizzare
- Il tipo di VPN della connessione
- Il dispositivo VPN, se necessario per la connessione

**Informazioni sui modelli di distribuzione**

[AZURE.INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]
 

## SKU del gateway

[AZURE.INCLUDE [vpn-gateway-table-sku](../../includes/vpn-gateway-table-sku-include.md)]

## Tipi di gateway VPN

Sono disponibili due tipi di VPN:

- **Basata su criteri:** i gateway basati su criteri sono chiamati *gateway statici* nel modello di distribuzione classica. La funzionalità di un gateway statico non è cambiata, anche se il nome è stato modificato. Questo tipo di gateway supporta le VPN basate su criteri. Le VPN basate su criteri reindirizzano i pacchetti tramite tunnel IPsec con selettori di traffico basati sulle combinazioni di prefissi di indirizzo tra la rete locale e la rete virtuale di Azure. I selettori di traffico o criteri vengono in genere definiti come un elenco di accesso nelle configurazioni VPN.
 
- **Basata su route:** i gateway basati su route sono chiamati *gateway dinamici* nel modello di distribuzione classica. La funzionalità di un gateway dinamico non è cambiata, anche se il nome è stato modificato. I gateway basati su route implementano VPN basate su route. Le VPN basate su route usano le "route" nella tabella di inoltro IP o di routing per reindirizzare i pacchetti nelle interfacce tunnel VPN corrispondenti. Le interfacce tunnel consentono quindi di crittografare o decrittografare i pacchetti all'interno e all'esterno dei tunnel. I criteri o selettori di traffico per le VPN basate su route vengono configurati come any-to-any (o caratteri jolly).

Alcune connessioni, ad esempio le connessioni da punto a sito e tra reti virtuali, funzionano solo con un tipo di VPN specifico. Saranno visualizzati i requisiti di gateway elencati nell'articolo che corrisponde allo scenario di connessione che si desidera creare.

Anche i dispositivi VPN hanno limitazioni di configurazione. Quando si crea un gateway VPN, selezionare il tipo di VPN necessario per la connessione, assicurandosi di verificare che il dispositivo VPN da usare supporti anche tale tipo di routing. Per ulteriori informazioni, vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md).

Se, ad esempio, si intende usare contemporaneamente una connessione da sito a sito e una connessione da punto a sito, è necessario configurare un gateway VPN basato su route. Sebbene sia vero che le connessioni da sito a sito funzionano con gateway basati su criteri, le connessioni da punto a sito richiedono un tipo di gateway basato su route. Poiché entrambe le connessioni useranno lo stesso gateway, è necessario selezionare il tipo di gateway che le supporta entrambe. Inoltre, il dispositivo VPN utilizzato deve supportare anche le configurazioni basate su route.


## Requisiti del gateway


[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## Passaggi successivi

Selezionare il dispositivo VPN per la configurazione. Vedere [Informazioni sui dispositivi e i gateway VPN per la connettività di rete virtuale](vpn-gateway-about-vpn-devices.md).





 

<!---HONumber=AcomDC_0302_2016-->
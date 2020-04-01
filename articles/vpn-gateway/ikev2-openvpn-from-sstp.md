---
title: Transizione a OpenVPN o IKEv2 da SSTP Azure VPN Gateway
description: Questo articolo consente di comprendere i modi per superare il limite di connessione simultanea di 128 di SSTP.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: alzam
ms.openlocfilehash: 5500d993a4bf3c664f14182d983f9abed8ebb08a
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398371"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>Transizione al protocollo OpenVPN o IKEv2 da SSTP

Una connessione gateway VPN da punto a sito (P2S) consente di creare una connessione sicura alla rete virtuale da un singolo computer client. Una connessione da punto a sito viene stabilita avviandola dal computer client. Questo articolo si applica al modello di distribuzione di Resource Manager e illustra i modi per superare il limite di 128 connessioni simultanee di SSTP passando al protocollo OpenVPN o IKEv2.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Protocollo usato nelle connessioni da punto a sito

Per la VPN da punto a sito può essere usato uno dei protocolli seguenti:

* **OpenVPN&reg; Protocol**, un protocollo VPN basato su SSL/TLS. Una soluzione VPN SSL può penetrare nei firewall, poiché la maggior parte dei firewall apre la porta TCP 443 in uscita, utilizzata da SSL. OpenVPN può essere utilizzato per connettersi da Android, iOS (versioni 11.0 e successive), Windows, Linux e dispositivi Mac (OSX versioni 10.13 e successive).

* **Secure Socket Tunneling Protocol (SSTP)**, un protocollo VPN proprietario basato su SSL. Una soluzione VPN SSL può penetrare nei firewall, poiché la maggior parte dei firewall apre la porta TCP 443 in uscita, utilizzata da SSL. SSTP è supportato solo nei dispositivi Windows. Azure supporta tutte le versioni di Windows che hanno SSTP (Windows 7 e versioni successive). **SSTP supporta fino a 128 connessioni simultanee solo indipendentemente dallo SKU del gateway.**

* VPN IKEv2, una soluzione VPN IPsec basata su standard. VPN IKEv2 può essere usato per connettersi da dispositivi Mac (versioni OSX 10.11 e successive).


>[!NOTE]
>IKEv2 e OpenVPN per la connessione da punto a sito sono disponibili solo per il modello di distribuzione Resource Manager. Non sono disponibili per il modello di distribuzione classica. Lo SKU del gateway di base non supporta i protocolli IKEv2 o OpenVPN. Se si usa lo SKU di base, sarà necessario eliminare e ricreare un gateway di rete virtuale SKU di produzione.
>

## <a name="migrating-from-sstp-to-ikev2-or-openvpn"></a>Migrazione da SSTP a IKEv2 o OpenVPN

In alcuni casi può essere necessario supportare più di 128 connessioni P2S simultanee a un gateway VPN, ma si utilizza SSTP. In tal caso, è necessario passare al protocollo IKEv2 o OpenVPN.

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>Opzione 1 - Aggiungere IKEv2 oltre a SSTP sul gatewayOption 1 - Add IKEv2 in addition to SSTP on the Gateway

Questa è l'opzione più semplice. SSTP e IKEv2 possono coesistere sullo stesso gateway e offrire un numero maggiore di connessioni simultanee. È sufficiente abilitare IKEv2 sul gateway esistente e scaricare nuovamente il client.

L'aggiunta di IKEv2 a un gateway VPN SSTP esistente non influirà sui client esistenti ed è possibile configurarli per l'utilizzo di IKEv2 in piccoli batch o semplicemente configurare i nuovi client per l'utilizzo di IKEv2. Se un client Windows è configurato sia per SSTP che per IKEv2, tenterà prima di connettersi utilizzando IKEV2 e, in caso di errore, verrà emesso il rollback a SSTP.

**IKEv2 utilizza porte UDP non standard, pertanto è necessario assicurarsi che queste porte non siano bloccate sul firewall dell'utente. Le porte in uso sono UDP 500 e 4500.**

Per aggiungere IKEv2 a un gateway esistente, è sufficiente passare alla scheda "Configurazione da punto a sito" nel gateway di rete virtuale nel portale e selezionare **IKEv2 e SSTP (SSL)** dalla casella di riepilogo a discesa.

![da punto a sito](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>Opzione 2 - Rimuovere SSTP e abilitare OpenVPN sul gatewayOption 2 - Remove SSTP and enable OpenVPN on the Gateway

Poiché SSTP e OpenVPN sono entrambi protocolli basati su TLS, non possono coesistere sullo stesso gateway. Se si decide di passare da SSTP a OpenVPN, sarà necessario disabilitare SSTP e abilitare OpenVPN sul gateway. Questa operazione causerà la perdita di connettività dei client esistenti al gateway VPN fino a quando il nuovo profilo non è stato configurato nel client.

È possibile abilitare OpenVPN insieme a IKEv2, se lo si desidera. OpenVPN è basato su TLS e utilizza la porta TCP 443 standard. Per passare a OpenVPN, passare alla scheda "Configurazione da punto a sito" sotto il gateway di rete virtuale nel portale e selezionare **OpenVPN (SSL)** o **IKEv2 e OpenVPN (SSL)** dalla casella di riepilogo a discesa.

![da punto a sito](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

Una volta configurato il gateway, i client esistenti non saranno in grado di connettersi fino a quando non si [distribuiscono e configurano i client OpenVPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn-clients).

Se si utilizza Windows 10, è anche possibile usare il client VPN di [Azure per Windows](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-client#to-download-the-azure-vpn-client)


## <a name="frequently-asked-questions"></a>Domande frequenti
### <a name="what-are-the-client-configuration-requirements"></a>Requisiti di configurazione per i client

>[!NOTE]
>Per i client Windows, è necessario avere i diritti di amministratore nel dispositivo client per avviare la connessione VPN dal dispositivo client ad Azure.
>

Gli utenti usano i client VPN nativi nei dispositivi Windows e Mac per la connessione da punto a sito. Azure fornisce un file ZIP per la configurazione del client VPN contenente le impostazioni necessarie per la connessione di questi client nativi ad Azure.

* Per i dispositivi Windows, la configurazione del client VPN è costituita da un pacchetto di installazione che gli utenti installano nei propri dispositivi.
* Per i dispositivi Mac è costituita dal file mobileconfig che gli utenti installano nei propri dispositivi.

Il file ZIP fornisce anche i valori di alcune impostazioni importanti sul lato Azure che è possibile usare per creare il proprio profilo per questi dispositivi. Alcuni dei valori includono l'indirizzo del gateway VPN, i tipi di tunnel configurati, le route e il certificato radice per la convalida del gateway.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Quali SKU del gateway supportano la VPN P2S?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Per consigli sugli SKU del gateway, vedere [Informazioni sulle impostazioni del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Lo SKU Basic non supporta l'autenticazione IKEv2 o RADIUS.
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Quali criteri IKE/IPsec sono configurati nei gateway VPN per P2S?


**IKEv2**

|**Cifrario** | **Integrità** | **PRF** | **Gruppo DH** |
|---        | ---            | ---        | ---     |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP256 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384        | SHA384    | GROUP_24 |
|AES256     |   SHA384        | SHA384    | GROUP_14 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_24 |
|AES256     |   SHA256        | SHA256    | GROUP_14 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_2 |

**IPsec**

|**Cifrario** | **Integrità** | **Gruppo PFS** |
|---        | ---            | ---        |
|GCM_AES256    | GCM_AES256 | GROUP_NONE |
|GCM_AES256    | GCM_AES256 | GROUP_24 |
|GCM_AES256    | GCM_AES256 | GROUP_14 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Quali criteri TLS sono configurati nei gateway VPN per P2S?
**TLS**

|**Criteri** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Come si configura una connessione da punto a sito?

La configurazione di una connessione da punto a sito richiede alcuni passaggi specifici. Gli articoli seguenti illustrano le procedure di configurazione di una connessione da punto a sito e contengono collegamenti per la configurazione dei dispositivi client VPN:

* [Configurare una connessione da punto a sito usando l'autenticazione RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurare una connessione da punto a sito usando l'autenticazione del certificato nativa di Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Configurare OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>Passaggi successivi

* [Configurare una connessione da punto a sito usando l'autenticazione RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurare una connessione da punto a sito usando l'autenticazione del certificato nativa di Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" è un marchio registrato di OpenVPN Inc.**

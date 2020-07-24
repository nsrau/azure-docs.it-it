---
title: Transizione a OpenVPN o IKEv2 da SSTP | Gateway VPN di Azure
description: Questo articolo consente di comprendere i modi per superare il limite di 128 connessione simultanea di SSTP.
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/30/2020
ms.author: alzam
ms.openlocfilehash: 1531bca6c56c159b7535536fb31a577f0d7253ef
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87064677"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>Transizione al protocollo OpenVPN o IKEv2 da SSTP

Una connessione gateway VPN da punto a sito (P2S) consente di creare una connessione sicura alla rete virtuale da un singolo computer client. Una connessione da punto a sito viene stabilita avviandola dal computer client. Questo articolo si applica al modello di distribuzione Gestione risorse e illustra i modi per superare il limite di 128 connessione simultanea di SSTP mediante la transizione al protocollo OpenVPN o a IKEv2.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Quale protocollo viene usato da P2S?

Per la VPN da punto a sito può essere usato uno dei protocolli seguenti:

* **OpenVPN &reg; Protocollo**, un protocollo VPN basato su SSL/TLS. Una soluzione VPN SSL può penetrare i firewall, perché la maggior parte dei firewall apre la porta TCP 443 in uscita, che usa SSL. OpenVPN può essere usato per la connessione da Android, iOS (versioni 11,0 e successive), da dispositivi Windows, Linux e Mac (versioni OSX 10,13 e successive).

* Protocollo **SSTP (Secure Socket Tunneling Protocol)**, un protocollo VPN proprietario basato su SSL. Una soluzione VPN SSL può penetrare i firewall, perché la maggior parte dei firewall apre la porta TCP 443 in uscita, che usa SSL. SSTP è supportato solo nei dispositivi Windows. Azure supporta tutte le versioni di Windows che hanno SSTP (Windows 7 e versioni successive). **SSTP supporta fino a 128 connessioni simultanee solo indipendentemente dallo SKU del gateway**.

* VPN IKEv2, una soluzione VPN IPsec basata su standard. VPN IKEv2 può essere usato per connettersi da dispositivi Mac (versioni OSX 10.11 e successive).


>[!NOTE]
>IKEv2 e OpenVPN per la connessione da punto a sito sono disponibili solo per il modello di distribuzione Resource Manager. Non sono disponibili per il modello di distribuzione classica. Lo SKU del gateway Basic non supporta i protocolli IKEv2 o OpenVPN. Se si usa lo SKU Basic, sarà necessario eliminare e ricreare un gateway di rete virtuale SKU di produzione.
>

## <a name="migrating-from-sstp-to-ikev2-or-openvpn"></a>Migrazione da SSTP a IKEv2 o OpenVPN

In alcuni casi è possibile che si voglia supportare più di 128 connessione P2S simultanea a un gateway VPN, ma che usi SSTP. In tal caso, è necessario passare a IKEv2 o al protocollo OpenVPN.

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>Opzione 1: aggiungere IKEv2 oltre al protocollo SSTP sul gateway

Questa è l'opzione più semplice. SSTP e IKEv2 possono coesistere nello stesso gateway e fornire un numero maggiore di connessioni simultanee. È semplicemente possibile abilitare IKEv2 sul gateway esistente e riscaricare il client.

L'aggiunta di IKEv2 a un gateway VPN SSTP esistente non influirà sui client esistenti ed è possibile configurarli per l'uso di IKEv2 in piccoli batch o solo per configurare i nuovi client per l'uso di IKEv2. Se un client Windows è configurato per SSTP e IKEv2, tenterà di connettersi usando prima IKEV2 e, in caso di errore, eseguirà il fallback al SSTP.

**IKEv2 utilizza porte UDP non standard, pertanto è necessario assicurarsi che queste porte non siano bloccate nel firewall dell'utente. Le porte in uso sono UDP 500 e 4500.**

Per aggiungere IKEv2 a un gateway esistente, passare semplicemente alla scheda "configurazione da punto a sito" sotto il gateway di rete virtuale nel portale e selezionare **IKEv2 e SSTP (SSL)** dalla casella di riepilogo a discesa.

![da punto a sito](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>Opzione 2: rimuovere SSTP e abilitare OpenVPN sul gateway

Poiché SSTP e OpenVPN sono entrambi protocolli basati su TLS, non possono coesistere nello stesso gateway. Se si decide di spostarsi da SSTP a OpenVPN, sarà necessario disabilitare SSTP e abilitare OpenVPN sul gateway. Questa operazione causerà la perdita della connettività del gateway VPN da parte dei client esistenti fino a quando il nuovo profilo non sarà stato configurato nel client.

Se lo si desidera, è possibile abilitare OpenVPN lungo il lato con IKEv2. OpenVPN è basato su TLS e usa la porta TCP 443 standard. Per passare a OpenVPN, passare alla scheda "configurazione da punto a sito" sotto il gateway di rete virtuale nel portale e selezionare **OpenVPN (SSL)** o **IKEv2 e OpenVPN (SSL)** dalla casella di riepilogo a discesa.

![da punto a sito](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

Una volta configurato il gateway, i client esistenti non saranno in grado di connettersi finché non si [distribuiscono e si configurano i client OpenVPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn-clients).

Se si usa Windows 10, è anche possibile usare il [client VPN di Azure per Windows](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-client#to-download-the-azure-vpn-client)


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

|**Crittografia** | **Integrità** | **PRF** | **Gruppo DH** |
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

|**Crittografia** | **Integrità** | **Gruppo PFS** |
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

**"OpenVPN" è un marchio di OpenVPN Inc.**

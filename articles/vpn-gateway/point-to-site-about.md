---
title: Informazioni sulle connessioni VPN da punto a sito di Azure | Microsoft Docs
description: Questo articolo descrive le connessioni da punto a sito e consente di stabilire quale tipo di autenticazione gateway VPN da punto a sito usare.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: f19b58ad8f12169acefd74bfe86a28ad950dad34
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="about-point-to-site-vpn"></a>Informazioni sulla VPN da punto a sito

Una connessione gateway VPN da punto a sito (P2S) consente di creare una connessione sicura alla rete virtuale da un singolo computer client. Una connessione da punto a sito viene stabilita avviandola dal computer client. Questa soluzione è utile per i telelavoratori che intendono connettersi alle reti virtuali di Azure da una posizione remota, ad esempio da casa o durante una riunione. Una VPN da punto a sito è anche una soluzione utile da usare al posto di una VPN da sito a sito quando solo pochi client devono connettersi a una rete virtuale.

## <a name="protocol"></a>Protocollo usato nelle connessioni da punto a sito

Per la VPN da punto a sito può essere usato uno dei protocolli seguenti:

* Secure Socket Tunneling Protocol (SSTP), un protocollo VPN di proprietà basato su SSL. Una soluzione VPN SSL può penetrare i firewall perché la maggior parte dei firewall apre la porta TCP 443 usata da SSL. SSTP è supportato solo nei dispositivi Windows. Azure supporta tutte le versioni di Windows che hanno SSTP (Windows 7 e versioni successive).

* VPN IKEv2, una soluzione VPN IPsec basata su standard. VPN IKEv2 può essere usato per connettersi da dispositivi Mac (versioni OSX 10.11 e successive).

In presenza di un ambiente client misto con dispositivi Windows e Mac, configurare sia SSTP che IKEv2.

>[!NOTE]
>IKEv2 per P2S è attualmente in fase di anteprima.
>

## <a name="authentication"></a>Modalità di autenticazione del client VPN da punto a sito

L'utente deve essere autenticato prima che Azure possa accettare una connessione VPN da punto a sito. Azure offre due meccanismi per autenticare un utente che esegue la connessione.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Autenticazione del certificato nativa di Azure

Quando si usa l'autenticazione del certificato nativa di Azure, per autenticare l'utente che esegue la connessione viene usato un certificato client presente nel dispositivo. I certificati client vengono generati da un certificato radice attendibile e quindi installati in ogni computer client. È possibile usare un certificato radice generato tramite una soluzione aziendale oppure generare un certificato autofirmato.

La convalida del certificato client viene eseguita dal gateway VPN quando viene stabilita la connessione VPN da punto a sito. Il certificato radice è necessario per la convalida e deve essere caricato in Azure. 

### <a name="authenticate-using-active-directory-ad-domain-server"></a>Autenticazione con server di dominio Active Directory (AD)

L'autenticazione con un dominio di AD consente agli utenti di connettersi ad Azure usando le credenziali di dominio dell'organizzazione. Richiede un server RADIUS che si integra con il server AD. Le organizzazioni possono anche sfruttare la distribuzione RADIUS esistente.   
  Il server RADIUS può essere distribuito in locale o nella rete virtuale di Azure. Durante l'autenticazione, il gateway VPN di Azure funge da pass-through e inoltra i messaggi di autenticazione tra il server RADIUS e il dispositivo che si connette e viceversa. È quindi importante che il gateway possa raggiungere il server RADIUS. Se il server RADIUS si trova in locale, per la raggiungibilità è necessaria una connessione VPN da sito a sito da Azure al sito locale.  
  È anche possibile integrare il server RADIUS con Servizi certificati AD. Ciò consente di usare il server RADIUS e la distribuzione di certificati dell'organizzazione per l'autenticazione del certificato da punto a sito in alternativa all'autenticazione del certificato di Azure. Il vantaggio è che non è necessario caricare i certificati radice e i certificati revocati in Azure.

Un server RADIUS può anche integrarsi con altri sistemi di identità esterni, offrendo così molte opzioni di autenticazione per le VPN da punto a sito, incluse le opzioni a più fattori.

>[!NOTE]
>L'autenticazione RADIUS per P2S è attualmente in fase di anteprima.
>

![point-to-site]](./media/point-to-site-about/p2s.png "Point-to-Site")

### <a name="configuration-requirements-for-client-devices"></a>Requisiti di configurazione per i dispositivi client

Gli utenti usano i client VPN nativi nei dispositivi Windows e Mac per la connessione da punto a sito. Azure fornisce un file ZIP per la configurazione del client VPN contenente le impostazioni necessarie per la connessione di questi client nativi ad Azure.

  * Per i dispositivi Windows, la configurazione del client VPN è costituita da un pacchetto di installazione che gli utenti installano nei propri dispositivi.
  * Per i dispositivi Mac è costituita dal file mobileconfig che gli utenti installano nei propri dispositivi.

Il file ZIP fornisce anche i valori di alcune impostazioni importanti sul lato Azure che è possibile usare per creare il proprio profilo per questi dispositivi. Alcuni dei valori includono l'indirizzo del gateway VPN, i tipi di tunnel configurati, le route e il certificato radice per la convalida del gateway.

### <a name="which-gateway-skus-support-p2s-vpn"></a>SKU del gateway che supportano la VPN da punto a sito

[!INCLUDE [p2s-skus](../../includes/vpn-gateway-table-point-to-site-skus-include.md)]

* Il benchmark della velocità effettiva aggregata si basa sulle misurazioni di più tunnel aggregati tramite un singolo gateway. Non è una velocità effettiva garantita, perché può variare anche in funzione delle condizioni del traffico Internet e dei comportamenti dell'applicazione.
* Le informazioni sui prezzi sono disponibili nella pagina Prezzi 
* Le informazioni sul contratto di servizio sono disponibili nella pagina Contratto di servizio.

## <a name="faqcert"></a>Domande frequenti per l'autenticazione del certificato di Azure nativo

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faqradius"></a>Domande frequenti per l'autenticazione RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Passaggi successivi

[Configurare connessioni da punto a sito - autenticazione RADIUS](point-to-site-how-to-radius-ps.md)

[Configurare connessioni da punto a sito - autenticazione del certificato nativa di Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5ef67580928a45609f50d3fe798eb9d054265c0a
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376111"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="what-should-i-do-if-im-getting-a-certificate-mismatch-when-connecting-using-certificate-authentication"></a>Che cosa è necessario fare se si verifica una mancata corrispondenza del certificato quando si effettua la connessione tramite autenticazione del certificato?

Deselezionare **"Verifica l'identità del server mediante convalida del certificato"** o **aggiungere il nome di dominio completo del server insieme al certificato** quando si crea un profilo manualmente. Per eseguire questa operazione, è possibile eseguire **rasphone** da un prompt dei comandi e selezionare il profilo dall'elenco a discesa.

Ignorare la convalida dell'identità del server non è consigliabile in generale, ma con l'autenticazione del certificato di Azure lo stesso certificato viene usato per la convalida del server nel protocollo di tunneling VPN (IKEv2/SSTP) e nel protocollo EAP. Poiché il certificato del server e il nome di dominio completo sono già convalidati dal protocollo di tunneling VPN, è ridondante convalidarli nuovamente in EAP.

![Autenticazione da punto a sito](./media/vpn-gateway-faq-p2s-all-include/servercert.png "Certificato server")

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>È possibile usare la CA radice della PKI interna per generare i certificati per la connettività da punto a sito?

Sì. In precedenza, era possibile utilizzare solo certificati radice autofirmati. È ancora possibile caricare 20 certificati radice.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>È possibile usare i certificati di Azure Key Vault?

No.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Quali strumenti è possibile usare per creare certificati?

È possibile usare la propria soluzione di infrastruttura a chiave pubblica aziendale (PKI interna), Azure PowerShell, MakeCert e OpenSSL.

### <a name="are-there-instructions-for-certificate-settings-and-parameters"></a><a name="certsettings"></a>Sono disponibili istruzioni per le impostazioni e i parametri dei certificati?

* **Soluzione PKI aziendale/PKI interna:** vedere la procedura per [generare i certificati](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** per la procedura, vedere l'articolo relativo ad [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md).

* **MakeCert:** per la procedura, vedere l'articolo relativo a [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md).

* **OpenSSL:** 

    * Quando si esportano certificati, assicurarsi di convertire il certificato radice in Base64.

    * Per il certificato client:

      * Quando si crea la chiave privata, specificare una lunghezza di 4096.
      * Quando si crea il certificato, per il parametro *-extensions* specificare *usr_cert*.

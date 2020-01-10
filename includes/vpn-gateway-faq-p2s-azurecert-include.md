---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f322803d3484b4ec2d5449e19d67d75b35d6d92f
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75752055"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="what-should-i-do-if-im-getting-a-certificate-mismatch-when-connecting-using-certificate-authentication"></a>Cosa è necessario fare se si verifica una mancata corrispondenza del certificato durante la connessione tramite l'autenticazione del certificato?

Deselezionare **"verifica l'identità del server convalidando il certificato"** o **aggiungere il nome di dominio completo del server insieme al certificato durante la** creazione manuale di un profilo. A tale scopo, è possibile eseguire **RASPHONE** da un prompt dei comandi e selezionare il profilo dall'elenco a discesa.

Il bypass della convalida dell'identità del server non è consigliato in generale, ma con l'autenticazione del certificato di Azure, lo stesso certificato viene usato per la convalida del server nel protocollo di tunneling VPN (IKEv2/SSTP) e nel protocollo EAP. Poiché il certificato del server e il nome di dominio completo sono già convalidati dal protocollo di tunneling VPN, è ridondante per convalidare di nuovo lo stesso in EAP.

![da punto a sito](./media/vpn-gateway-faq-p2s-all-include/servercert.png "Certificato server")

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>È possibile usare la CA radice PKI interna per generare certificati per la connettività da punto a sito?

Sì. In precedenza, era possibile utilizzare solo certificati radice autofirmati. È ancora possibile caricare 20 certificati radice.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>È possibile usare I certificati di Azure Key Vault?

No.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Quali strumenti è possibile usare per creare certificati?

È possibile usare la propria soluzione di infrastruttura a chiave pubblica aziendale (PKI interna), Azure PowerShell, MakeCert e OpenSSL.

### <a name="certsettings"></a>Sono disponibili istruzioni per le impostazioni e i parametri dei certificati?

* **Soluzione PKI aziendale/PKI interna:** vedere la procedura per [generare i certificati](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** per la procedura, vedere l'articolo relativo ad [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md).

* **MakeCert:** per la procedura, vedere l'articolo relativo a [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md).

* **OpenSSL:** 

    * Quando si esportano certificati, assicurarsi di convertire il certificato radice in Base64.

    * Per il certificato client:

      * Quando si crea la chiave privata, specificare una lunghezza di 4096.
      * Quando si crea il certificato, per il parametro *-extensions* specificare *usr_cert*.

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
ms.openlocfilehash: 93f6bc8533218af7f0e6dcd1c5f7be6fe8c00e29
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520850"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>È possibile usare la CA radice PKI interna per generare certificati per la connettività da punto a sito?

Sì. In precedenza, era possibile utilizzare solo certificati radice autofirmati. È ancora possibile caricare 20 certificati radice.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>È possibile usare I certificati di Azure Key Vault?

No.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Quali strumenti è possibile usare per creare certificati?

È possibile usare la propria soluzione di infrastruttura a chiave pubblica aziendale (PKI interna), Azure PowerShell, MakeCert e OpenSSL.

### <a name="certsettings"></a>Sono disponibili istruzioni per le impostazioni e i parametri dei certificati?

* **Soluzione PKI interna/PKI aziendale:** Vedere la procedura per [generare i certificati](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** Per la procedura, vedere l'articolo [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) .

* **Makecert** Per la procedura, vedere l'articolo [Makecert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) .

* **OpenSSL:** 

    * Quando si esportano certificati, assicurarsi di convertire il certificato radice in Base64.

    * Per il certificato client:

      * Quando si crea la chiave privata, specificare una lunghezza di 4096.
      * Quando si crea il certificato, per il parametro *-extensions* specificare *usr_cert*.

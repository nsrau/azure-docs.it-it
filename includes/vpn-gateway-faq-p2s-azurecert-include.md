---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 97d33bfcc8251b10ba121b7fb013800904450563
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>È possibile usare la CA radice della PKI interna per la connettività da punto a sito?

Sì. In precedenza, era possibile utilizzare solo certificati radice autofirmati. È ancora possibile caricare 20 certificati radice.

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
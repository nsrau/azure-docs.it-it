---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 03/21/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: e2e91dc91cf0fbe6827808785a4c3cc25b06542b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320149"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>È possibile usare la CA radice della PKI interna per la connettività da punto a sito?

Sì. In precedenza, era possibile utilizzare solo certificati radice autofirmati. È ancora possibile caricare 20 certificati radice.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Quali strumenti è possibile usare per creare certificati?

È possibile usare la propria soluzione di infrastruttura a chiave pubblica aziendale (PKI interna), Azure PowerShell, MakeCert e OpenSSL.

### <a name="certsettings"></a>Sono disponibili istruzioni per le impostazioni e i parametri dei certificati?

* **Soluzione PKI aziendale/PKI interna:** Vedere i passaggi necessari per [generare i certificati](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** Vedere le [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) procedura nell'articolo.

* **MakeCert:** Vedere le [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) procedura nell'articolo.

* **OpenSSL:** 

    * Quando si esportano certificati, assicurarsi di convertire il certificato radice in Base64.

    * Per il certificato client:

      * Quando si crea la chiave privata, specificare una lunghezza di 4096.
      * Quando si crea il certificato, per il parametro *-extensions* specificare *usr_cert*.
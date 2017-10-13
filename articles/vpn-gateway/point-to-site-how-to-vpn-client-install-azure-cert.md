---
title: Installare un certificato client per connessioni da punto a sito | Azure
description: Questo articolo illustra come installare un certificato client per l'autenticazione del certificato da punto a sito.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2017
ms.author: cherylmc
ms.openlocfilehash: fc0cc37794ef291c9d27b094211b38cec90da55c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-client-certificate-for-point-to-site-azure-certificate-authentication-connections"></a>Installare un certificato client per connessioni con autenticazione del certificato di Azure da punto a sito

Per tutti i client che si connettono a una rete virtuale usando l'autenticazione del certificato di Azure da punto a sito è necessario un certificato client. Questo articolo illustra come installare un certificato client che verrà usato per l'autenticazione durante la connessione a una rete virtuale con una configurazione da punto a sito.

## <a name="generate"></a>Generare ed esportare un certificato client

È possibile generare un certificato client da un certificato radice generato con una soluzione CA globale (enterprise) oppure da un certificato radice autofirmato. Per informazioni sulla procedura, vedere le istruzioni relative a [PowerShell](vpn-gateway-certificates-point-to-site.md) o [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Dopo aver generato i certificati client, esportarli come file PFX. Assicurarsi di includere l'intera catena di certificati nell'esportazione.

## <a name="installwin"></a>Installare un certificato in client Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Installare un certificato in client Mac

I client VPN Mac sono supportati solo per il modello di distribuzione Resource Manager. Non sono supportati per il modello di distribuzione classica.

> [!NOTE]
>  IKEv2 è attualmente in fase di anteprima.
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Continuare con la procedura di configurazione della connessione da punto a sito.

* [Portale di Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Portale di Azure (classico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
---
title: Modificare i prefissi degli indirizzi IP per il gateway di rete locale e l&quot;indirizzo IP del gateway VPN | Azure| PowerShell| Microsoft Docs
description: Questo articolo illustra in modo dettagliato la modifica dei prefissi degli indirizzi IP per il gateway di rete locale
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: a26cbe2172dc27c152246d70b7f6b504ec4a08a5
ms.lasthandoff: 04/12/2017


---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Modificare le impostazioni del gateway di rete locale usando PowerShell
In alcuni casi le impostazioni per il valore AddressPrefix o GatewayIPAddress del gateway di rete locale subiscono modifiche. Le istruzioni seguenti sono utili per la modifica delle impostazioni del gateway di rete locale. È anche possibile modificare queste impostazioni nel portale di Azure.

## <a name="before-you-begin"></a>Prima di iniziare
È necessario installare la versione più recente dei cmdlet di PowerShell per Gestione risorse di Azure. Per altre informazioni sull'installazione dei cmdlet di PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs) .

## <a name="to-modify-ip-address-prefixes"></a>Per modificare i prefissi degli indirizzi IP
[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address"></a>Per modificare l'indirizzo IP del gateway
[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Passaggi successivi
È possibile verificare la connessione al gateway. Vedere [Verificare una connessione al gateway](vpn-gateway-verify-connection-resource-manager.md).



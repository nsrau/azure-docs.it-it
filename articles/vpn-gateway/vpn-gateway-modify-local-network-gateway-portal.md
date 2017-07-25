---
title: Modificare i prefissi degli indirizzi IP per il gateway di rete locale e l'indirizzo IP del Gateway VPN | Azure| Portale | Microsoft Docs
description: Questo articolo illustra in modo dettagliato come modificare i prefissi degli indirizzi IP per il gateway di rete locale usando il portale di Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: ebe57f497a007c5219c3b7f543ffb3bae9d161a8
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017


---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modificare le impostazioni del gateway di rete locale usando il portale di Azure

In alcuni casi le impostazioni per il valore AddressPrefix o GatewayIPAddress del gateway di rete locale subiscono modifiche. Questo articolo illustra come modificare le impostazioni del gateway di rete locale. È anche possibile modificare queste impostazioni con un altro metodo selezionando un'opzione diversa nell'elenco seguente:

> [!div class="op_single_selector"]
> * [Portale di Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Interfaccia della riga di comando di Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="modify-ip-address-prefixes"></a>Modificare i prefissi degli indirizzi IP

Quando si modificano i prefissi degli indirizzi IP, la procedura seguita varia a seconda che il gateway di rete locale abbia una connessione oppure no.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="modify-the-gateway-ip-address"></a>Modificare l'indirizzo IP del gateway

Se l'indirizzo IP pubblico del dispositivo VPN a cui ci si vuole connettere è stato modificato, è necessario modificare il gateway di rete locale per riflettere tale modifica. Quando si modifica l'indirizzo IP pubblico, la procedura seguita varia a seconda che il gateway di rete locale abbia una connessione oppure no.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Passaggi successivi

È possibile verificare la connessione al gateway. Vedere [Verificare una connessione al gateway](vpn-gateway-verify-connection-resource-manager.md).

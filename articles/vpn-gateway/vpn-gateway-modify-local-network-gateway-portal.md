---
title: "Gateway VPN: modificare le impostazioni dell'indirizzo IP del gateway: portale di Azure"
description: Questo articolo illustra in modo dettagliato come modificare i prefissi degli indirizzi IP per il gateway di rete locale usando il portale di Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: fdb98242cede36f818604a7a5d713f5f7c75daad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75864027"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modificare le impostazioni del gateway di rete locale usando il portale di Azure

In alcuni casi le impostazioni per il valore AddressPrefix o GatewayIPAddress del gateway di rete locale subiscono modifiche. Questo articolo illustra come modificare le impostazioni del gateway di rete locale. È anche possibile modificare queste impostazioni con un altro metodo selezionando un'opzione diversa nell'elenco seguente:

Prima di eliminare la connessione, è opportuno scaricare la configurazione dei dispositivi usati per la connessione per ottenere la chiave precondivisa definita. In questo modo non sarà necessario ridefinirla sull'altro lato della connessione.

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Interfaccia della riga di comando di Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modificare i prefissi degli indirizzi IP

Quando si modificano i prefissi degli indirizzi IP, la procedura seguita varia a seconda che il gateway di rete locale abbia una connessione oppure no.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Modificare l'indirizzo IP del gateway

Se l'indirizzo IP pubblico del dispositivo VPN a cui ci si vuole connettere è stato modificato, è necessario modificare il gateway di rete locale per riflettere tale modifica. Quando si modifica l'indirizzo IP pubblico, la procedura seguita varia a seconda che il gateway di rete locale abbia una connessione oppure no.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Passaggi successivi

È possibile verificare la connessione al gateway. Vedere [Verificare una connessione al gateway](vpn-gateway-verify-connection-resource-manager.md).

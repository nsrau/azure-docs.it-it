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
ms.openlocfilehash: 865f4a963dfbaa7b8392865f8b0c468734673ba0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Per modificare il gateway di rete locale 'gatewayIpAddress'

Se l'indirizzo IP pubblico del dispositivo VPN a cui ci si vuole connettere è stato modificato, è necessario modificare il gateway di rete locale per riflettere tale modifica. L'indirizzo IP del gateway può essere modificato senza rimuovere una connessione gateway VPN esistente, se disponibile. Per modificare l'indirizzo IP del gateway, sostituire i valori 'Site2' e 'TestRG1' con i valori personalizzati usando il comando [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_update).

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Verificare che l'indirizzo IP sia corretto nell'output:

```
"gatewayIpAddress": "23.99.222.170",
```
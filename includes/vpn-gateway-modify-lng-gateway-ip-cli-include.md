---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 03/21/2018
ms.date: 03/04/2019
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: e368b1590f263618969423d57cdf0531fc2bb54d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60652871"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Per modificare il gateway di rete locale 'gatewayIpAddress'

Se l'indirizzo IP pubblico del dispositivo VPN a cui ci si vuole connettere è stato modificato, è necessario modificare il gateway di rete locale per riflettere tale modifica. L'indirizzo IP del gateway può essere modificato senza rimuovere una connessione gateway VPN esistente, se disponibile. Per modificare l'indirizzo IP del gateway, sostituire i valori 'Site2' e 'TestRG1' con i valori personalizzati usando il comando [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway).

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Verificare che l'indirizzo IP sia corretto nell'output:

```
"gatewayIpAddress": "23.99.222.170",
```

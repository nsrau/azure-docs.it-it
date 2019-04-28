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
ms.openlocfilehash: 0e009354e66ab13cdb9fbc3cf9e4b37e904bdfd1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60411814"
---
È possibile verificare se la connessione è riuscita usando il comando [az network vpn-connection show](/cli/azure/network/vpn-connection). Il valore " --name" nell'esempio fa riferimento al nome della connessione che si vuole testare. Mentre è in corso l'operazione per stabilire la connessione, lo stato della connessione è "Connecting". Dopo che la connessione è stata stabilita, lo stato diventa "Connected".

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```

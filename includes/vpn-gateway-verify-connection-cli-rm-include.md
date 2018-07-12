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
ms.openlocfilehash: 1c3103889bd11df45710cc0d6afaeeba022c9ace
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38763006"
---
È possibile verificare se la connessione è riuscita usando il comando [az network vpn-connection show](/cli/azure/network/vpn-connection#show). Il valore " --name" nell'esempio fa riferimento al nome della connessione che si vuole testare. Mentre è in corso l'operazione per stabilire la connessione, lo stato della connessione è "Connecting". Dopo che la connessione è stata stabilita, lo stato diventa "Connected".

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
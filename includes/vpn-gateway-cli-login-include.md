---
title: includere il file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 74d7bd087df4b00c0bafb5ec33fbbdfa5c57b379
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67179987"
---
Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/) e seguire le istruzioni visualizzate. Per altre informazioni sull'accesso, vedere [Introduzione all'interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli).

```azurecli
az login
```

Se si hanno più sottoscrizioni Azure, elencare le sottoscrizioni per l'account.

```azurecli
az account list --all
```

Specificare la sottoscrizione da usare.

```azurecli
az account set --subscription <replace_with_your_subscription_id>
```

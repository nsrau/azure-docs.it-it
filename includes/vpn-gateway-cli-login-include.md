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
ms.openlocfilehash: bc7367ca2162dde9a266341f0d7b3dd1e8b0da81
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313694"
---
Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#login) e seguire le istruzioni visualizzate. Per altre informazioni sull'accesso, vedere [Introduzione all'interfaccia della riga di comando di Azure 2.0](/cli/azure/get-started-with-azure-cli).

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
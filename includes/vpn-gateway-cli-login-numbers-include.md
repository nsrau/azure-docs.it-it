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
ms.openlocfilehash: 70a9e2a8f6327c0af92698b49d5b622bebba3661
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313609"
---
1. Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#login) e seguire le istruzioni visualizzate. Per altre informazioni sull'accesso, vedere [Introduzione all'interfaccia della riga di comando di Azure 2.0](/cli/azure/get-started-with-azure-cli).

  ```azurecli
  az login
  ```
2. Se si hanno più sottoscrizioni Azure, elencare le sottoscrizioni per l'account.

  ```azurecli
  az account list --all
  ```
3. Specificare la sottoscrizione da usare.

  ```azurecli
  az account set --subscription <replace_with_your_subscription_id>
  ```
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
ms.openlocfilehash: a72f5f5a1d8c64b347185827906f15d3cb9f55ad
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
1. Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#login) e seguire le istruzioni visualizzate. Per altre informazioni sulla registrazione, vedere [Introduzione all'interfaccia della riga di comando di Azure 2.0](/cli/azure/get-started-with-azure-cli).

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
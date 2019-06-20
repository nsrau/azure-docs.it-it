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
ms.openlocfilehash: 21dbec52dded5a195cc764741ab9e8d79737c549
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179999"
---
1. Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/) e seguire le istruzioni visualizzate. Per altre informazioni sull'accesso, vedere [Introduzione all'interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli).

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

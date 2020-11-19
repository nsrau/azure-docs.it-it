---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 94df457cc93a059de3f9bb294bd1c568b095781c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94885350"
---
<!-- List and set subscriptions -->

1. Ottenere un elenco di sottoscrizioni con il comando [az account list](/cli/azure/account#az-account-list):

    ```
    az account list --output table
    ```

2. Usare `az account set` con l'ID o il nome della sottoscrizione alla quale si intende passare.

    ```
    az account set --subscription "My Demos"
    ```

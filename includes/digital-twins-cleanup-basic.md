---
author: baanders
description: file di inclusione per la pulizia di un'istanza di base di Gemelli digitali di Azure e della registrazione dell'app
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 45d6a806e94ceca9574b0ed5f73c2b87ef438438
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88891497"
---
Se le risorse create in questa esercitazione non sono più necessarie, seguire questa procedura per eliminarle.

Usando [Azure Cloud Shell](https://shell.azure.com), è possibile eliminare tutte le risorse di Azure di un gruppo di risorse con il comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). Questo comando rimuove il gruppo di risorse e l'istanza di Gemelli digitali di Azure.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. 

Aprire un'istanza di Azure Cloud Shell ed eseguire il comando seguente per eliminare il gruppo di risorse e tutti gli elementi contenuti al suo interno.

```azurecli-interactive
az group delete --name <your-resource-group>
```

Eliminare quindi la registrazione dell'app di Azure Active Directory creata per l'app client con questo comando:

```azurecli-interactive
az ad app delete --id <your-application-ID>
```
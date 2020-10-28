---
author: baanders
description: file di inclusione per la pulizia di un'istanza di base di Gemelli digitali di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 2e095a7b286a8860535d6b58fa93098735b30c87
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372319"
---
Se le risorse create in questa esercitazione non sono più necessarie, seguire questa procedura per eliminarle.

Usando [Azure Cloud Shell](https://shell.azure.com), è possibile eliminare tutte le risorse di Azure di un gruppo di risorse con il comando [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete). Questo comando rimuove il gruppo di risorse e l'istanza di Gemelli digitali di Azure.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. 

Aprire un'istanza di Azure Cloud Shell ed eseguire il comando seguente per eliminare il gruppo di risorse e tutti gli elementi contenuti al suo interno.

```azurecli
az group delete --name <your-resource-group>
```
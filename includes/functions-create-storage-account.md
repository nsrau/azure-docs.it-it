---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 25cfcefb600bc12de3dad5b6fe2bcb76d00f0198
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444096"
---
## <a name="create-an-azure-storage-account"></a>Creare un account di Archiviazione di Azure

Funzioni usa un account di uso generico di Archiviazione di Azure per gestire lo stato e altre informazioni sulle funzioni. Creare un account di archiviazione di uso generico nel gruppo di risorse creato usando il comando [az storage account create](/cli/azure/storage/account).

Nel comando seguente sostituire il segnaposto `<storage_name>` con il nome globalmente univoco dell'account di archiviazione. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

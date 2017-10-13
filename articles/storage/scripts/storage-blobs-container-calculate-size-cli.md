---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Calcolare le dimensioni del contenitore BLOB | Microsoft Docs
description: Calcolare le dimensioni di un contenitore dell'Archiviazione BLOB di Azure sommando le dimensioni dei BLOB del contenitore.
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/28/2017
ms.author: marsma
ms.openlocfilehash: 21d49f1ef2f0f9e93e72dcd2a1667033b234bab6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Calcolare le dimensioni di un contenitore di archiviazione BLOB

Lo script consente di calcolare le dimensioni di un contenitore dell'Archiviazione BLOB di Azure sommando le dimensioni dei BLOB del contenitore.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/storage/calculate-container-size/calculate-container-size.sh?highlight=2-3 "Calculate container size")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire il comando seguente per rimuovere il gruppo di risorse, il contenitore e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Lo script usa i comandi seguenti per calcolare le dimensioni del contenitore dell'archiviazione BLOB. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az storage blob upload](/cli/azure/storage/account#create) | Carica file locali in un contenitore dell'Archiviazione BLOB di Azure. |
| [az storage blob list](/cli/azure/storage/account/keys#list) | Elenca i BLOB in un contenitore dell'Archiviazione BLOB di Azure. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando sono disponibili in [Azure CLI samples for Azure Blob storage](../blobs/storage-samples-blobs-cli.md) (Esempi dell'interfaccia della riga di comando di Azure per l'Archiviazione BLOB di Azure).

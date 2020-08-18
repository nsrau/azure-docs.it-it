---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Eliminare i contenitori mediante prefisso | Microsoft Docs
description: Eliminare i contenitori BLOB di Archiviazione di Azure in base a un prefisso del nome del contenitore e quindi eseguire la pulizia della distribuzione. Vedere i collegamenti della guida per i comandi usati nello script di esempio.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 06/22/2017
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: aeccf255004cd4512fbc591942324341504b20f7
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87901879"
---
# <a name="use-an-azure-cli-script-to-delete-containers-based-on-container-name-prefix"></a>Usare uno script dell'interfaccia della riga di comando di Azure per eliminare i contenitori in base al prefisso del nome

Per prima cosa, lo script crea alcuni contenitori di esempio nell'Archiviazione BLOB di Azure, quindi ne elimina alcuni in base a un prefisso nel nome dei contenitori stessi.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.sh?highlight=2-3 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire il comando seguente per rimuovere il gruppo di risorse, i contenitori rimanenti e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Lo script usa i comandi seguenti per eliminare i contenitori in base al prefisso contenuto nel nome dei contenitori stessi. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az storage account create](/cli/azure/storage/account) | Crea un account di Archiviazione di Azure nel gruppo di risorse specificato. |
| [az storage container create](/cli/azure/storage/container) | Crea un contenitore nell'Archiviazione BLOB di Azure. |
| [az storage container list](/cli/azure/storage/container) | Elenca i contenitori nell'account di Archiviazione di Azure. |
| [az storage container delete](/cli/azure/storage/container) | Elimina i contenitori nell'account di Archiviazione di Azure. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando sono disponibili in [Interfaccia della riga di comando di Azure](../blobs/storage-samples-blobs-cli.md).

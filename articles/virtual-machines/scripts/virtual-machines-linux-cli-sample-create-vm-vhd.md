---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare una macchina virtuale con un disco rigido virtuale | Microsoft Docs
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare una macchina virtuale Linux usando un disco rigido virtuale.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/09/2017
ms.author: allclark
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 9e62713be30a07ae00dac07de21009d2c379d609
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017

---

# <a name="create-a-vm-with-a-virtual-hard-disk"></a>Creare una macchina virtuale con un disco rigido virtuale

In questo esempio viene creata una macchina virtuale usando un disco rigido virtuale.
Vengono prima creati un gruppo di risorse, un account di archiviazione e un contenitore e quindi viene creata una macchina virtuale caricando il disco rigido virtuale nel contenitore.
Viene infine sostituita la chiave pubblica SSH con la chiave pubblica personale per poter accedere alla macchina virtuale.

È necessario un disco rigido virtuale di avvio.
È possibile scaricare il disco rigido virtuale usato nell'esempio da https://azclisamples.blob.core.windows.net/vhds/sample.vhd oppure usare il proprio disco rigido virtuale. Lo script cerca `~/sample.vhd`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[principale](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Creare una macchina virtuale usando un disco rigido virtuale")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli-interactive 
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, la macchina virtuale, il set di disponibilità, il bilanciamento del carico e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az storage account list](https://docs.microsoft.com/cli/azure/storage/account#list) | Elenca gli account di archiviazione |
| [az storage account check-name](https://docs.microsoft.com/cli/azure/storage/account#check-name) | Verifica che un nome di account di archiviazione sia valido e che non esista già |
| [az storage account keys list](https://docs.microsoft.com/cli/azure/storage/account/keys#list) | Elenca le chiavi per gli account di archiviazione |
| [az storage blob exists](https://docs.microsoft.com/cli/azure/storage/blob#exists) | Controlla se il BLOB esiste |
| [az storage container create](https://docs.microsoft.com/cli/azure/storage/container#create) | Crea un contenitore in un account di archiviazione. |
| [az storage blob upload](https://docs.microsoft.com/cli/azure/storage/blob#upload) | Crea un BLOB nel contenitore caricando il disco rigido virtuale. |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#list) | Usato con `--query`, controlla se il nome della macchina virtuale è in uso. | 
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#create) | Crea le macchine virtuali. |
| [az vm access set-linux-user](https://docs.microsoft.com/cli/azure/vm/access#set-linux-user) | Reimposta la chiave SSH per consentire all'utente corrente di accedere alla macchina virtuale. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#list-ip-addresses) | Ottiene l'indirizzo IP della macchina virtuale creata. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare una macchina virtuale con un disco rigido virtuale | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Creare una macchina virtuale Linux usando un disco rigido virtuale.
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
ms.openlocfilehash: 414ef43063cc48b7b9ae7be5fbccbb7906ae8c03
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-vm-with-a-virtual-hard-disk"></a>Creare una macchina virtuale con un disco rigido virtuale

In questo esempio viene creata una macchina virtuale usando un disco rigido virtuale.
Vengono prima creati un gruppo di risorse, un account di archiviazione e un contenitore e quindi viene creata una macchina virtuale caricando il disco rigido virtuale nel contenitore.
Viene infine sostituita la chiave pubblica SSH con la chiave pubblica personale per poter accedere alla macchina virtuale.

È necessario un disco rigido virtuale di avvio.
È possibile scaricare il disco rigido virtuale usato nell'esempio da https://azclisamples.blob.core.windows.net/vhds/sample.vhd oppure usare il proprio disco rigido virtuale. Lo script cerca `~/sample.vhd`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Create VM using a VHD")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli-interactive 
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, la macchina virtuale, il set di disponibilità, il bilanciamento del carico e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az storage account list](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_list) | Elenca gli account di archiviazione |
| [az storage account check-name](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_check_name) | Verifica che un nome di account di archiviazione sia valido e che non esista già |
| [az storage account keys list](https://docs.microsoft.com/cli/azure/storage/account/keys#az_storage_account_keys_list) | Elenca le chiavi per gli account di archiviazione |
| [az storage blob exists](https://docs.microsoft.com/cli/azure/storage/blob#az_storage_blob_exists) | Controlla se il BLOB esiste |
| [az storage container create](https://docs.microsoft.com/cli/azure/storage/container#az_storage_container_create) | Crea un contenitore in un account di archiviazione. |
| [az storage blob upload](https://docs.microsoft.com/cli/azure/storage/blob#az_storage_blob_upload) | Crea un BLOB nel contenitore caricando il disco rigido virtuale. |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Usato con `--query`, controlla se il nome della macchina virtuale è in uso. | 
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Crea le macchine virtuali. |
| [az vm access set-linux-user](https://docs.microsoft.com/cli/azure/vm/access#az_vm_access_set_linux_user) | Reimposta la chiave SSH per consentire all'utente corrente di accedere alla macchina virtuale. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#az_vm_list-ip-addresses) | Ottiene l'indirizzo IP della macchina virtuale creata. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

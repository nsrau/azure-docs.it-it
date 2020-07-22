---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare una VM Linux con WordPress
description: Esempio di script dell'interfaccia della riga di comando di Azure - Creare una VM Linux con WordPress
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: ff02c46422d4cb64365213e3b80aca532df9cecf
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86509733"
---
# <a name="create-a-vm-with-wordpress"></a>Creare una VM con WordPress

Questo script crea una macchina virtuale e quindi usa l'estensione di script personalizzata della macchina virtuale di Azure per installare WordPress. Dopo aver eseguito lo script, è possibile accedere al sito di configurazione di WordPress in `http://<public IP of VM>/wordpress`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-wordpress-mysql/create-wordpress-mysql.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, la macchina virtuale e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az vm create](/cli/azure/vm) | Consente di creare la macchina virtuale e la connette alla scheda di rete, alla rete virtuale, alla subnet e al gruppo di sicurezza di rete. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione.  |
| [az vm open-port](/cli/azure/vm) | Consente di creare una regola del gruppo di sicurezza di rete per consentire il traffico in ingresso. In questo esempio, la porta 80 è aperta al traffico HTTP. |
| [az vm extension set](/cli/azure/vm) | Consente di aggiungere l'estensione di script personalizzata alla macchina virtuale che richiama uno script per installare WordPress. |
| [az group delete](/cli/azure/vm/extension) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

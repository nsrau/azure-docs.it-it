---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare una macchina virtuale Windows Server 2016 con IIS usando DSC | Microsoft Docs
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare una macchina virtuale Windows Server 2016 con IIS usando DSC
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 1c72ea16ab760617db7ec10d946b60af74908275
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-vm-with-iis-using-dsc"></a>Creare una macchina virtuale con IIS usando DSC

Questo script crea una macchina virtuale e usa l'estensione dello script personalizzato DSC di Macchina virtuale di Azure per installare e configurare IIS. 

Se necessario, installare l'interfaccia della riga di comando di Azure usando l'istruzione presente nella [Guida all'installazione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e quindi eseguire `az login` per creare una connessione con Azure.

Questo esempio funziona in una shell Bash. Per le opzioni sull'esecuzione di script dell'interfaccia della riga di comando di Azure nel client Windows, vedere [Running the Azure CLI in Windows](../virtual-machines-windows-cli-options.md) (Esecuzione dell'interfaccia della riga di comando di Azure in Windows).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-windows-iis-using-dsc/create-windows-iis-using-dsc.sh "Creazione rapida della macchina virtuale")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, la macchina virtuale e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Consente di creare la macchina virtuale e la connette alla scheda di rete, alla rete virtuale, alla subnet e al gruppo di sicurezza di rete. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione.  |
| [az vm extension set](https://docs.microsoft.com/cli/azure/vm#create) | Consente di aggiungere l'estensione dello script personalizzato alla macchina virtuale che richiama uno script per installare IIS. |
| [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port) | Consente di creare una regola del gruppo di sicurezza di rete per consentire il traffico in ingresso. In questo esempio, la porta 80 è aperta al traffico HTTP. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della macchina virtuale Windows Azure](../virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

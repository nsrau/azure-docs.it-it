---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare un host Docker | Documentazione Microsoft
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare un host Docker
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: e5362b32a0f93433c77c27a6fc116960c45404f9
ms.openlocfilehash: 3693e6f78541caeca8fd1c276028c0f64ca71ddc
ms.lasthandoff: 03/01/2017

---

# <a name="create-a-vm-with-docker"></a>Creare una VM con Docker

Questo script di esempio crea una macchina virtuale e quindi usa l'estensione di VM Docker di Azure per configurare un host Docker. L'estensione di VM Docker crea quindi un contenitore che esegue NGINX. Infine, lo script consente di configurare un gruppo di sicurezza di rete di Azure per tutto il traffico in ingresso sulla porta 80. Una volta che lo script è stato eseguito correttamente, il server web NGINX può accedere tramite il nome di dominio completo della macchina virtuale di Azure. 

Prima di eseguire questo script, verificare che sia stata creata una connessione con Azure tramite il comando `az login`.

Questo esempio funziona in una shell Bash. Per le opzioni sull'esecuzione di script dell'interfaccia della riga di comando di Azure nel client Windows, vedere [Running the Azure CLI in Windows](../virtual-machines-windows-cli-options.md) (Esecuzione dell'interfaccia della riga di comando di Azure in Windows).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-docker-host/create-docker-host.sh "Docker Host")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo l'esecuzione dello script di esempio, eseguire il comando seguente per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare la distribuzione. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Consente di creare la macchina virtuale e la connette alla scheda di rete, alla rete virtuale, alla subnet e al gruppo di sicurezza di rete. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione.  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port) | Consente di creare una regola del gruppo di sicurezza di rete per consentire il traffico in ingresso. In questo esempio, la porta 80 è aperta al traffico HTTP. |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | Consente di aggiungere ed eseguire un'estensione di macchina virtuale in una VM. In questo esempio, l'estensione di VM Docker viene usata per configurare un host Docker.|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


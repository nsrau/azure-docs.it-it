---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare una VM Linux con NGINX | Documentazione Microsoft
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare una VM Linux con NGINX
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
ms.sourcegitcommit: 07d91dfa905d22334bd051f9d5f3d936d38efc88
ms.openlocfilehash: 94e2593271bd7828aab4dcefc0d0df47086e47ad
ms.lasthandoff: 02/28/2017

---

# <a name="create-a-vm-with-nginx"></a>Creare una VM con NGINX

Questo script crea una macchina virtuale e quindi usa l'estensione dello script personalizzato della macchina virtuale di Azure per installare NGINX. Dopo l'esecuzione dello script, un sito Web dimostrativo può essere raggiunto sull'indirizzo IP pubblico della macchina virtuale.

Prima di eseguire questo script, verificare che sia stata creata una connessione con Azure tramite il comando `az login`.

Questo esempio funziona in una shell Bash. Per le opzioni sull'esecuzione di script dell'interfaccia della riga di comando di Azure nel client Windows, vedere [Running the Azure CLI in Windows](../virtual-machines-windows-cli-options.md) (Esecuzione dell'interfaccia della riga di comando di Azure in Windows).

## <a name="sample-script"></a>Script di esempio

Lo script seguente crea la macchina virtuale e richiama l'estensione di script personalizzati.

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-nginx/create-vm-nginx.sh "Creazione rapida della macchina virtuale")]

## <a name="custom-script-extension"></a>Estensione di script personalizzati

Questa estensione di script personalizzata copia questo script nella macchina virtuale. Lo script viene quindi eseguito per installare e configurare un server web NGINX. 

```bash
#!/bin/bash
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo l'esecuzione dello script di esempio, eseguire il comando seguente per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, la macchina virtuale e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Creare la macchina virtuale. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione.  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/network/nsg/rule#create) | Consente di creare una regola del gruppo di sicurezza di rete per consentire il traffico in ingresso. In questo esempio, la porta 80 è aperta al traffico HTTP. |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | Consente di aggiungere ed eseguire un'estensione di macchina virtuale in una VM. In questo esempio, l'estensione dello script personalizzato viene usata per installare NGINX.|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


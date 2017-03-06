---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Installare IIS | Documentazione Microsoft
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Installare IIS
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/28/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: a172d73732354d31d717d8e2f3a5c5c43cbbd6dc
ms.openlocfilehash: 1c0d2de77acc4696cb0e274e14591a2925454dd6
ms.lasthandoff: 03/01/2017

---

# <a name="quick-create-a-virtual-machine-with-the-azure-cli"></a>Creare rapidamente una macchina virtuale con l'interfaccia della riga di comando di Azure

Questo script crea una macchina virtuale di Azure con Windows Server 2016 e quindi usa l'estensione dello script personalizzato della macchina virtuale di Azure per installare IIS. Dopo l'esecuzione dello script, il sito Web IIS predefinito può essere raggiunto sull'indirizzo IP pubblico della macchina virtuale.

Prima di eseguire questo script, verificare che sia stata creata una connessione con Azure tramite il comando `az login`. È anche necessario modificare la variabile $AdminPassword all'inizio dello script secondo i requisiti di complessità delle password univoci.

Questo esempio funziona in una shell Bash. Per le opzioni sull'esecuzione di script dell'interfaccia della riga di comando di Azure in Windows, vedere [Running the Azure CLI in Windows](../virtual-machines-windows-cli-options.md) (Esecuzione dell'interfaccia della riga di comando di Azure in Windows).

## <a name="create-vm-sample"></a>Creare una macchina virtuale di esempio

```azurecli
#!/bin/bash

# Update for your admin password
AdminPassword=ChangeYourAdminPassword1

# Create a resource group.
az group create --name myResourceGroup --location westeurope

# Create a virtual machine. 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password $AdminPassword

# Open port 80 to allow web traffic to host.
az vm open-port --port 80 --resource-group myResourceGroup --name myVM 

# Use CustomScript extension to install Apache.
az vm extension set \
  --publisher Microsoft.Compute \
  --version 1.8 \
  --name CustomScriptExtension \
  --vm-name myVM \
  --resource-group myResourceGroup \
  --settings '{"commandToExecute":"powershell.exe Install-WindowsFeature -Name Web-Server"}'
```

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo l'esecuzione dello script di esempio, eseguire il comando seguente per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, la macchina virtuale e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Consente di creare la macchina virtuale e la connette alla scheda di rete, alla rete virtuale, alla subnet e al gruppo di sicurezza di rete. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione.  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/network/nsg/rule#create) | Consente di creare una regola del gruppo di sicurezza di rete per consentire il traffico in ingresso. In questo esempio, la porta 80 è aperta al traffico HTTP. |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | Consente di aggiungere ed eseguire un'estensione di macchina virtuale in una VM. In questo esempio, l'estensione dello script personalizzato è usata per installare IIS.|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della macchina virtuale Windows Azure](../virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


---
title: Creare una VM Linux in Azure da un modello
description: Procedura per usare l'interfaccia della riga di comando di Azure per creare una VM Linux da un modello di Gestione risorse
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3e32e9f17b5c48e18453724eb683ba2e86dd0cdb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036494"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Procedura per creare una macchina virtuale Linux con i modelli di Azure Resource Manager

Informazioni su come creare una macchina virtuale (VM) Linux usando un modello di Azure Resource Manager e l'interfaccia della riga di comando di Azure da Azure cloud Shell. Per creare una macchina virtuale Windows, vedere [creare una macchina virtuale Windows da un modello di gestione risorse](../windows/ps-template.md).

## <a name="templates-overview"></a>Panoramica dei modelli

I modelli di Azure Resource Manager sono file JSON che definiscono l'infrastruttura e la configurazione della soluzione Azure. Usando il modello è possibile distribuire ripetutamente la soluzione nel corso del ciclo di vita garantendo al contempo che le risorse vengano distribuite in uno stato coerente. Per altre informazioni sul formato del modello e sulla relativa costruzione, vedere [Guida introduttiva: creare e distribuire modelli di Azure Resource Manager usando il portale di Azure](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md). Per visualizzare la sintassi JSON per i tipi di risorse, vedere [Define resources in Azure Resource Manager templates](/azure/templates/microsoft.compute/allversions) (Definire le risorse nei modelli di Azure Resource Manager).

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

La creazione di una macchina virtuale di Azure include in genere due passaggi:

1. Creare un gruppo di risorse. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Il gruppo di risorse deve essere creato prima della macchina virtuale.
1. Creare una macchina virtuale.

L'esempio seguente crea una macchina virtuale da un [modello di avvio rapido di Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Per questa distribuzione è consentita solo l'autenticazione SSH. Quando richiesto, specificare il valore della chiave pubblica SSH, ad esempio il contenuto di *~/.ssh/id_rsa.pub*. Se è necessario creare una coppia di chiavi SSH, vedere [Come creare e usare una coppia di chiavi SSH pubblica e privata per le VM Linux in Azure](mac-create-ssh-keys.md). Di seguito è riportata una copia del modello:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Per eseguire lo script dell'interfaccia della riga di comando, selezionare **prova** per aprire Azure cloud Shell. Per incollare lo script, fare clic con il pulsante destro del mouse sulla shell, quindi scegliere **Incolla**:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

L'ultimo comando dell'interfaccia della riga di comando di Azure Mostra l'indirizzo IP pubblico della macchina virtuale appena creata. Per connettersi alla macchina virtuale, è necessario l'indirizzo IP pubblico. Vedere la sezione successiva di questo articolo.

Nell'esempio precedente è stato specificato un modello archiviato in GitHub. È anche possibile scaricare o creare un modello e specificare il percorso locale con il parametro `--template-file`.

Altre risorse:

- Per informazioni su come sviluppare modelli di Resource Manager, vedere [Documentazione di Resource Manager in Azure](/azure/azure-resource-manager/).
- Per visualizzare gli schemi delle macchine virtuali di Azure, vedere informazioni di [riferimento sui modelli di Azure](/azure/templates/microsoft.compute/allversions).
- Per visualizzare altri esempi di modelli di macchina virtuale, vedere [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Connettersi alla macchina virtuale

È quindi possibile usare la connessione SSH alla VM nel modo usuale. Fornire il proprio indirizzo IP pubblico dal comando precedente:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Passaggi successivi

In questo esempio è stata creata una VM Linux di base. Per altre Gestione risorse modelli che includono Framework applicazioni o creano ambienti più complessi, esplorare i [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

Per altre informazioni sulla creazione di modelli, vedere la sintassi e le proprietà JSON dei tipi di risorse distribuiti:

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)

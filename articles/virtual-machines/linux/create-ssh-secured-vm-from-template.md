---
title: Creare una macchina virtuale Linux in Azure da un modelloCreate a Linux VM in Azure from a template
description: Procedura per usare l'interfaccia della riga di comando di Azure per creare una VM Linux da un modello di Gestione risorse
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.openlocfilehash: 581eadc60835b758f67ae616d4413800f1d6d718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969521"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Procedura per creare una macchina virtuale Linux con i modelli di Azure Resource Manager

Informazioni su come creare una macchina virtuale Linux usando un modello di Azure Resource Manager e l'interfaccia della riga di comando di Azure dalla shell del cloud di Azure.Learn how to create a Linux virtual machine (VM) by using an Azure Resource Manager template and the Azure CLI from the Azure Cloud shell. Per creare una macchina virtuale Windows, vedere [Creare una macchina virtuale Windows da un modello di Resource Manager.](../windows/ps-template.md)

## <a name="templates-overview"></a>Panoramica dei modelli

I modelli di Azure Resource Manager sono file JSON che definiscono l'infrastruttura e la configurazione della soluzione Azure. Usando il modello è possibile distribuire ripetutamente la soluzione nel corso del ciclo di vita garantendo al contempo che le risorse vengano distribuite in uno stato coerente. Per altre informazioni sul formato del modello e sulla sua costruzione, vedere [Guida introduttiva: Creare e distribuire modelli](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)di Azure Resource Manager tramite il portale di Azure. Per visualizzare la sintassi JSON per i tipi di risorse, vedere [Define resources in Azure Resource Manager templates](/azure/templates/microsoft.compute/allversions) (Definire le risorse nei modelli di Azure Resource Manager).

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

La creazione di una macchina virtuale di Azure include in genere due passaggi:Creating an Azure virtual machine usually includes two steps:

1. Creare un gruppo di risorse. Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Il gruppo di risorse deve essere creato prima della macchina virtuale.
1. Creare una macchina virtuale.

Nell'esempio seguente viene creata una macchina virtuale da un [modello di avvio rapido di Azure.The](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)following example creates a VM from an Azure Quickstart template . Per questa distribuzione è consentita solo l'autenticazione SSH. Quando richiesto, specificare il valore della chiave pubblica SSH, ad esempio il contenuto di *~/.ssh/id_rsa.pub*. Se è necessario creare una coppia di chiavi SSH, vedere [Come creare e usare una coppia di chiavi SSH pubblica e privata per le VM Linux in Azure](mac-create-ssh-keys.md). Di seguito è riportata una copia del modello:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Per eseguire lo script dell'interfaccia della riga di comando, selezionare Prova per aprire la shell di Cloud di Azure.To run the CLI script, Select **Try it** to open the Azure Cloud shell. Per incollare lo script, fare clic con il pulsante destro del mouse sulla shell, quindi scegliere **Incolla:**

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

L'ultimo comando dell'interfaccia della riga di comando di Azure mostra l'indirizzo IP pubblico della macchina virtuale appena creata. È necessario l'indirizzo IP pubblico per connettersi alla macchina virtuale. Vedere la sezione successiva di questo articolo.

Nell'esempio precedente è stato specificato un modello archiviato in GitHub. È anche possibile scaricare o creare un modello e specificare il percorso locale con il parametro `--template-file`.

Altre risorse:

- Per informazioni su come sviluppare modelli di Resource Manager, vedere [Documentazione di Resource Manager in Azure](/azure/azure-resource-manager/).
- Per visualizzare gli schemi delle macchine virtuali di Azure, vedere Informazioni di riferimento sui [modelli di Azure.To](/azure/templates/microsoft.compute/allversions)see the Azure virtual machine schemas, see Azure template reference .
- Per altri esempi di modelli di macchina virtuale, vedere [Modelli di avvio rapido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)di Azure.To see more virtual machine template samples, see Azure Quickstart templates .

## <a name="connect-to-virtual-machine"></a>Connettersi alla macchina virtuale

È quindi possibile usare la connessione SSH alla VM nel modo usuale. Fornire il proprio indirizzo IP pubblico dal comando precedente:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Passaggi successivi

In questo esempio è stata creata una VM Linux di base. Per altri modelli di Resource Manager che includono framework di applicazioni o per creare ambienti più complessi, esplorare i [modelli di Avvio veloce](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)di Azure.

Per altre informazioni sulla creazione di modelli, vedere la sintassi e le proprietà JSON dei tipi di risorse distribuiti:

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)

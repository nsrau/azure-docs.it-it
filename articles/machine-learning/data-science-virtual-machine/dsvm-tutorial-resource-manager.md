---
title: 'Avvio rapido: Creare una Data Science VM - Modello di Resource Manager'
titleSuffix: Azure Data Science Virtual Machine
description: Questa guida di avvio rapido illustra come usare un modello di Azure Resource Manager per distribuire rapidamente una Data Science Virtual Machine
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 06/10/2020
ms.service: machine-learning
ms.subservice: data-science-vm
ms.topic: quickstart
ms.openlocfilehash: 9b89c0a4135bf595991439dd47e57a870ea2b0d1
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855071"
---
# <a name="quickstart-create-an-ubuntu-data-science-virtual-machine-using-an-arm-template"></a>Avvio rapido: Creare una Data Science Virtual Machine Ubuntu con un modello di Resource Manager

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questa guida di avvio rapido illustra come creare una Data Science Virtual Machine Ubuntu 18.04 usando un modello di Azure Resource Manager. Le Data Science Virtual Machine sono macchine virtuali basate su cloud in cui è precaricata una suite di framework e strumenti di data science e Machine Learning. Quando vengono distribuiti in risorse di calcolo basate su GPU, tutti gli strumenti e le librerie sono configurati per l'utilizzo della GPU.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-ubuntu-DSVM-GPU-or-CPU%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/services/machine-learning/) prima di iniziare.

* Per usare i comandi dell'interfaccia della riga di comando in questo documento dall'**ambiente locale**, è necessaria l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/).

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json":::

Nel modello sono definite le risorse seguenti:

* [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
* [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): crea una macchina virtuale basata sul cloud. In questo modello la macchina virtuale è configurata come Data Science Virtual Machine che esegue Ubuntu 18.04.

## <a name="deploy-the-template"></a>Distribuire il modello

Per usare il modello dall'interfaccia della riga di comando di Azure, accedere e scegliere la sottoscrizione (vedere [Accedere tramite l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli)). Eseguire quindi:

```azurecli-interactive
read -p "Enter the name of the resource group to create:" resourceGroupName &&
read -p "Enter the Azure location (e.g., centralus):" location &&
read -p "Enter the authentication type (must be 'password' or 'sshPublicKey') :" authenticationType &&
read -p "Enter the login name for the administrator account (may not be 'admin'):" adminUsername &&
read -p "Enter administrator account secure string (value of password or ssh public key):" adminPasswordOrKey &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters adminUsername=$adminUsername authenticationType=$authenticationType adminPasswordOrKey=$adminPasswordOrKey &&
echo "Press [ENTER] to continue ..." &&
read
```

Quando si esegue il comando precedente, immettere:

1. Nome del gruppo di risorse che si vuole creare per contenere la Data Science Virtual Machine e le risorse associate.
1. La località di Azure in cui eseguire la distribuzione.
1. Il tipo di autenticazione da usare (immettere la stringa `password` o `sshPublicKey`).
1. L'ID di accesso dell'account amministratore (questo valore non può essere `admin`).
1. Il valore della password o della chiave pubblica SSH per l'account.

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

Per visualizzare la Data Science Virtual Machine:

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Accedere.
1. Scegliere il gruppo di risorse appena creato.

Verranno visualizzate le informazioni del gruppo di risorse:

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="Screenshot di un gruppo di risorse di base contenente una Data Science Virtual Machine":::

Fare clic sulla risorsa macchina virtuale per passare alla pagina delle informazioni corrispondente in cui sono visualizzate le informazioni sulla macchina virtuale, inclusi i dettagli della connessione.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si vuole usare questa macchina virtuale, eliminarla. Poiché la Data Science Virtual Machine è associata ad altre risorse, ad esempio un account di archiviazione, è probabile che si voglia eliminare l'intero gruppo di risorse creato. È possibile eliminare il gruppo di risorse tramite il portale facendo clic sul pulsante **Elimina** e confermando l'operazione. In alternativa, è possibile eliminare il gruppo di risorse dall'interfaccia della riga di comando con:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata creata una Data Science Virtual Machine da un modello di Azure Resource Manager.

> [!div class="nextstepaction"]
> [Programmi di esempio e procedure dettagliate per Machine Learning](dsvm-samples-and-walkthroughs.md)

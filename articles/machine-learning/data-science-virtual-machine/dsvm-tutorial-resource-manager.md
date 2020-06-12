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
ms.openlocfilehash: e89f3d85156081106f4e1dbb55ee6c895e7e7a2d
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636751"
---
# <a name="tutorial-create-an-ubuntu-data-science-virtual-machine-using-a-resource-manager-template"></a>Esercitazione: Creare una Data Science Virtual Machine Ubuntu con un modello di Resource Manager
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questa guida di avvio rapido illustra come creare una Data Science Virtual Machine Ubuntu 18.04 usando un modello di Azure Resource Manager. Le Data Science Virtual Machine sono macchine virtuali basate su cloud in cui è precaricata una suite di framework e strumenti di data science e Machine Learning. Quando vengono distribuiti in risorse di calcolo basate su GPU, tutti gli strumenti e le librerie sono configurati per l'utilizzo della GPU. 

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree) prima di iniziare

* Per usare i comandi dell'interfaccia della riga di comando in questo documento dall'**ambiente locale**, è necessaria l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-a-workspace"></a>Creare un'area di lavoro

### <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/). Il modello completo per questo articolo è troppo lungo per essere mostrato qui. Per visualizzare il modello completo, vedere il file [azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json). La parte che definisce le specifiche della Data Science Virtual Machine è mostrata qui:

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" range="235-276":::

Nel modello sono definite le risorse seguenti:

* [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): crea una macchina virtuale basata sul cloud. In questo modello la macchina virtuale è configurata come Data Science Virtual Machine che esegue Ubuntu 18.04.

### <a name="deploy-the-template"></a>Distribuire il modello 

Per usare il modello dall'interfaccia della riga di comando di Azure, accedere e scegliere la sottoscrizione (vedere [Accedere tramite l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)). Eseguire quindi:

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
1. Località di Azure in cui si vuole eseguire la distribuzione
1. Tipo di autenticazione che si vuole usare (immettere la stringa `password` o `sshPublicKey`)
1. Nome dell'account di accesso dell'account amministratore (questo valore non può essere `admin`)
1. Valore della password o della chiave pubblica SSH per l'account

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

Per visualizzare la Data Science Virtual Machine:

1. Passare a https://portal.azure.com. 
1. Accesso 
1. Scegliere il gruppo di risorse appena creato

Verranno visualizzate le informazioni del gruppo di risorse: 

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="Screenshot di un gruppo di risorse di base contenente una Data Science Virtual Machine":::

Fare clic sulla risorsa macchina virtuale per passare alla pagina delle informazioni corrispondente in cui sono visualizzate le informazioni sulla macchina virtuale, inclusi i dettagli della connessione. 

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si vuole usare questa macchina virtuale, eliminarla. Poiché la Data Science Virtual Machine è associata ad altre risorse, ad esempio un account di archiviazione, è probabile che si voglia eliminare l'intero gruppo di risorse creato. È possibile eliminare il gruppo di risorse tramite il portale facendo clic sul pulsante "Elimina" e confermando l'operazione. In alternativa, è possibile eliminare il gruppo di risorse dall'interfaccia della riga di comando con: 

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

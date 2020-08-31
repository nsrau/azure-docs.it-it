---
title: Creare un'automazione di sicurezza per specifici avvisi di sicurezza usando un modello di Azure Resource Manager
description: Informazioni su come creare un'automazione del Centro sicurezza di Azure per fare in modo che un'app per la logica venga attivata da avvisi specifici del Centro sicurezza usando un modello di Azure Resource Manager
services: azure-resource-manager
author: memildin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: memildin
ms.date: 08/20/2020
ms.openlocfilehash: ecef318978194ac3773c54e2d9c960781086de65
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88687218"
---
# <a name="quickstart-create-an-automatic-response-to-a-specific-security-alert-using-an-azure-resource-manager-template-arm-template"></a>Avvio rapido: Creare una risposta automatica a uno specifico avviso di sicurezza usando un modello di Azure Resource Manager

Questo argomento di avvio rapido illustra come usare un modello di Azure Resource Manager per creare un'automazione del flusso di lavoro che attiva un'app per la logica quando il Centro sicurezza di Azure riceve specifici avvisi di sicurezza.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)


## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Per un elenco delle autorizzazioni e dei ruoli necessari per usare la funzionalità di automazione del flusso di lavoro del Centro sicurezza di Azure, vedere [Automazione del flusso di lavoro](workflow-automation.md).


## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-securitycenter-create-automation-for-alertnamecontains/).

:::code language="json" source="~/quickstart-templates/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json":::

### <a name="relevant-resources"></a>Risorse pertinenti

- [**Microsoft.Security/automations**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-securitycenter-create-automation-for-alertnamecontains/#microsoftsecurity-resource-provider): l'automazione che attiverà l'app per la logica alla ricezione di un avviso del Centro sicurezza di Azure contenente una stringa specifica.
- [**Microsoft.Logic/workflows**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-securitycenter-create-automation-for-alertnamecontains/#microsoftlogic-resource-provider): un'app per la logica attivabile vuota.

Per altri modelli di avvio rapido del Centro sicurezza, vedere questi [modelli forniti dalla community](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Security).

## <a name="deploy-the-template"></a>Distribuire il modello

  - **PowerShell**:
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
    ```

  - **Interfaccia della riga di comando**:
    ```azurecli-interactive
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
    az group deployment create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
    ```

  - **Portale**:

    [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

    Per altre informazioni su questa opzione di distribuzione, vedere [Usare un pulsante di distribuzione per distribuire i modelli dal repository GitHub](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-to-azure-button).

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

Usare il portale di Azure per verificare che l'automazione del flusso di lavoro sia stata distribuita. 

1. Nel [portale di Azure](https://portal.azure.com) aprire **Centro sicurezza**.
1. Sulla barra dei menu superiore selezionare l'icona del filtro, quindi selezionare la sottoscrizione specifica in cui è stata distribuita la nuova automazione del flusso di lavoro.
1. Sulla barra laterale del Centro sicurezza aprire **Automazione del flusso di lavoro** e cercare la nuova automazione.

    :::image type="content" source="./media/quickstart-automation-alert/validating-template-run.png" alt-text="Testo alternativo immagine." lightbox="./media/quickstart-automation-alert/validating-template-run.png":::

    >[!TIP]
    > Se nella sottoscrizione sono presenti molte automazioni del flusso di lavoro, usare l'opzione del **filtro per nome**. 

## <a name="clean-up-resources"></a>Pulizia delle risorse

Quando non è più necessaria, eliminare l'automazione del flusso di lavoro usando il portale di Azure.

1. Nel [portale di Azure](https://portal.azure.com) aprire **Centro sicurezza**.
1. Sulla barra dei menu superiore selezionare l'icona del filtro, quindi selezionare la sottoscrizione specifica in cui è stata distribuita la nuova automazione del flusso di lavoro.
1. Sulla barra laterale del Centro sicurezza aprire **Automazione del flusso di lavoro** e cercare l'automazione da eliminare.

    :::image type="content" source="./media/quickstart-automation-alert/deleting-workflow-automation.png" alt-text="Testo alternativo immagine." lightbox="./media/quickstart-automation-alert/deleting-workflow-automation.png":::

1. Selezionare la casella di controllo per l'elemento da eliminare.
1. Sulla barra degli strumenti selezionare Elimina.


## <a name="next-steps"></a>Passaggi successivi

Per un'esercitazione dettagliata che illustra il processo di creazione di un modello, vedere:

> [!div class="nextstepaction"]
> [Esercitazione: Creare e distribuire il primo modello di Resource Manager](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)
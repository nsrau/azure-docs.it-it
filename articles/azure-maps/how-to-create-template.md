---
title: Creare un account Azure Maps usando un modello ARM | Mappe Microsoft Azure
description: Informazioni su come creare un account Azure Maps usando un modello di Azure Resource Manager (ARM).
author: philmea
ms.author: philmea
ms.date: 10/20/2020
ms.topic: how-to
ms.service: azure-maps
ms.openlocfilehash: c715c0639e962f76f669515c1d2c826c8cf6cc9e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92525119"
---
# <a name="create-your-azure-maps-account-using-an-arm-template"></a>Creare un account Azure Maps usando un modello ARM

È possibile creare l'account Azure Maps usando un modello di Azure Resource Manager (ARM). Dopo avere creato un account, è possibile implementare le API nel proprio sito Web o nella propria applicazione per dispositivi mobili.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-maps-create/).

:::code language="json" source="~/quickstart-templates/101-maps-create/azuredeploy.json":::

La risorsa account Azure Maps è definita in questo modello:

* [**Microsoft. Maps/accounts**](/azure/templates/microsoft.maps/accounts): creare un account Azure maps.

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Il modello crea un account Azure maps.

    [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

2. Selezionare o immettere i valori seguenti.

    ![Portale di distribuzione del modello ARM](./media/how-to-create-template/create-account-using-template-portal.png)

    A meno che non sia specificato, usare il valore predefinito per creare l'account Azure maps.

    * **Sottoscrizione**: selezionare una sottoscrizione di Azure.
    * **Gruppo di risorse**: selezionare **Crea nuovo**, immettere un nome univoco per il gruppo di risorse e quindi fare clic su **OK**.
    * **Località**: selezionare una località. Ad esempio, **Stati Uniti occidentali 2**.
    * **Nome account**: immettere un nome per l'account Azure Maps, che deve essere univoco a livello globale.
    * Piano **tariffario**: selezionare il piano tariffario appropriato, il valore predefinito per il modello è S0.

3. Selezionare **Rivedi e crea**. 
4. Confermare le impostazioni nella pagina Verifica e fare clic su **Crea**. Una volta distribuite le mappe di Azure, viene inviata una notifica:

    ![Notifica del portale per la distribuzione del modello ARM](./media/how-to-create-template/resource-manager-template-portal-deployment-notification.png)

Il portale di Azure viene usato per distribuire il modello. È anche possibile usare Azure PowerShell, l'interfaccia della riga di comando di Azure e un'API REST. Per informazioni sugli altri metodi di distribuzione, vedere [Distribuire modelli](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

È possibile usare la portale di Azure per verificare l'account Azure Maps e visualizzare le chiavi. È anche possibile usare lo script dell'interfaccia della riga di comando di Azure seguente per elencare le chiavi dell'account.

```azurecli-interactive
az maps account keys list --name MyMapsAccount --resource-group MyResourceGroup
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare il gruppo di risorse, che elimina anche l'account Azure maps. Per eliminare il gruppo di risorse usando l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su mappe e Azure Resource Manager di Azure, continuare con gli articoli seguenti.

- Creare un' [applicazione demo](quick-demo-map-app.md) di Azure Maps
- Altre informazioni sui [modelli ARM](../azure-resource-manager/templates/overview.md)
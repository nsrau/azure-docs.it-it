---
title: Creare una risorsa di Servizi cognitivi di Azure con i modelli di Resource Manager | Microsoft Docs
description: Creare una risorsa di Servizi cognitivi di Azure con un modello di Resource Manager.
keywords: servizi cognitivi, soluzioni cognitive, intelligenza cognitiva, intelligenza artificiale cognitiva
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: aahi
ms.custom: subject-armqs, devx-track-azurecli
ms.openlocfilehash: 4afaad77fdff7a1ba80a385e9f2c3d8ddef8ab96
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842108"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-an-arm-template"></a>Avvio rapido: Creare una risorsa di Servizi cognitivi con un modello di Resource Manager

Questo argomento di avvio rapido descrive come usare un modello di Azure Resource Manager (ARM) per creare un'istanza di Servizi cognitivi.

I Servizi cognitivi di Azure sono servizi basati sul cloud con API REST ed SDK delle librerie client che consentono agli sviluppatori di integrare l'intelligenza cognitiva nelle applicazioni senza la necessità di competenze o conoscenze dirette di intelligenza artificiale e data science. I Servizi cognitivi di Azure consentono agli sviluppatori di aggiungere facilmente funzionalità cognitive alle applicazioni con soluzioni cognitive in grado di vedere, ascoltare, parlare, comprendere e persino di iniziare a ragionare.

Creare una risorsa usando un modello di Azure Resource Manager (ARM). Questa risorsa multi-servizio consente di:

* Accedere a più Servizi cognitivi di Azure con una singola chiave o uno singolo endpoint.
* Consolidare la fatturazione dei servizi usati.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuire il servizio cognitivo in Azure](../media/template-deployments/deploy-to-azure.svg "Distribuire il servizio cognitivo in Azure")](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, [crearne una gratuitamente](https://azure.microsoft.com/free/cognitive-services).

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-cognitive-services-universalkey/).

:::code language="json" source="~/quickstart-templates/101-cognitive-services-universalkey/azuredeploy.json":::

Nel modello è definita una risorsa di Azure:
* [Microsoft.CognitiveServices/accounts](/azure/templates/microsoft.cognitiveservices/accounts): crea una risorsa di Servizi cognitivi.

## <a name="deploy-the-template"></a>Distribuire il modello

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Fare clic sul pulsante **Distribuisci in Azure**.

    [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

2. Immettere i valori seguenti.

    |valore  |Descrizione  |
    |---------|---------|
    | **Sottoscrizione** | Selezionare una sottoscrizione di Azure. |
    | **Gruppo di risorse** | Selezionare **Crea nuovo**, immettere un nome univoco per il gruppo di risorse e quindi fare clic su **OK**. |
    | **Area** | Scegliere un'area,  ad esempio **Stati Uniti orientali 2** |
    | **Nome del servizio cognitivo** | Sostituire con un nome univoco per la risorsa. Il nome sarà necessario nella sezione successiva quando si convalida la distribuzione. |
    | **Posizione** | Sostituire con l'area usata in precedenza. |
    | **Sku** | Il [piano tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/) per la risorsa. |

    :::image type="content" source="media/arm-template/universal-key-portal-template.png" alt-text="Schermata di creazione risorsa.":::

3. Selezionare **Rivedi e crea** e quindi **Crea**. Al termine della distribuzione della risorsa, verrà evidenziato il pulsante **Vai alla risorsa**.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/CLI)

> [!NOTE]
> `az deployment group` create richiede l'interfaccia della riga di comando di Azure 2.6 o versione successiva. Per visualizzare la versione, digitare `az --version`. Per altre informazioni, vedere la [documentazione](/cli/azure/deployment/group).

Eseguire lo script seguente usando l'interfaccia della riga di comando di Azure [nel computer locale](/cli/azure/install-azure-cli?view=azure-cli-latest) o in un browser con il pulsante **Prova**. Immettere un nome e una località, ad esempio `centralus`, per un nuovo gruppo di risorse in cui verrà distribuita una risorsa di Servizi cognitivi usando il modello di Resource Manager. Prendere nota del nome che si usa. Sarà necessario in seguito per convalidare la distribuzione.


```azurecli-interactive
read -p "Enter a name for your new resource group:" resourceGroupName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cognitive-services-universalkey/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]


## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

# <a name="portal"></a>[Portale](#tab/portal)

Al termine della distribuzione, sarà possibile fare clic sul pulsante **Vai alla risorsa** per visualizzare la nuova risorsa. Per trovare il gruppo di risorse, è anche possibile:

1. Selezionare **Gruppi di risorse** nel menu di spostamento sinistro.
2. Selezionare il nome del gruppo di risorse.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/CLI)

Usando l'interfaccia della riga di comando di Azure, eseguire lo script seguente e immettere il nome del gruppo di risorse creato in precedenza.

```azurecli-interactive
echo "Enter the resource group where the Cognitive Services resource exists:" &&
read resourceGroupName &&
az cognitiveservices account list -g $resourceGroupName
```

---


## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse al suo interno.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Nel portale di Azure espandere il menu a sinistra per aprire il menu dei servizi e scegliere **Gruppi di risorse** per visualizzare l'elenco dei gruppi di risorse.
2. Individuare il gruppo di risorse contenente la risorsa da eliminare
3. Fare clic con il pulsante destro del mouse sull'elenco dei gruppi di risorse. Scegliere **Elimina gruppo di risorse** e confermare.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/CLI)

Usando l'interfaccia della riga di comando di Azure, eseguire lo script seguente e immettere il nome del gruppo di risorse creato in precedenza.

```azurecli-interactive
echo "Enter the resource group name, for deletion:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Passaggi successivi

* [Autenticare le richieste a Servizi cognitivi di Azure](authentication.md)
* [Che cos'è la ricerca cognitiva di Azure?](./what-are-cognitive-services.md)
* [Supporto del linguaggio naturale](language-support.md)
* [Supporto di contenitori Docker](cognitive-services-container-support.md)

---
title: Distribuire modelli di app per la logica
description: Informazioni su come distribuire modelli di Azure Resource Manager creati per app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 73c4c8839bd1d9e37f3dc4f0802ad5a648b1026b
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793056"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Distribuire modelli di Azure Resource Manager per app per la logica di Azure

Dopo aver creato un modello di Azure Resource Manager per l'app per la logica, è possibile distribuire il modello nei modi seguenti:

* [Azure portal](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [interfaccia della riga di comando di Azure](#cli)
* [API REST di Gestione risorse di Azure](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Azure Pipelines di Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Eseguire la distribuzione tramite portale di Azure

Per distribuire automaticamente un modello di app per la logica in Azure, è possibile scegliere il pulsante **Distribuisci in Azure** seguente, che consente di accedere al portale di Azure e di richiedere informazioni sull'app per la logica. È quindi possibile apportare le modifiche necessarie al modello o ai parametri dell'app per la logica.

[![Distribuisci in Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Ad esempio, dopo aver effettuato l'accesso al portale di Azure vengono richieste le informazioni seguenti:

* Nome della sottoscrizione di Azure
* Gruppo di risorse da usare
* Percorso dell'app per la logica
* Nome dell'app per la logica
* Un URI di test
* Accettazione dei termini e delle condizioni specificati

Per altre informazioni, vedere gli argomenti seguenti:

* [Panoramica: automatizzare la distribuzione per le app per la logica con modelli di Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Distribuire con Visual Studio

Per distribuire un modello di app per la logica da un progetto di gruppo di risorse di Azure creato con Visual Studio, seguire questa [procedura per distribuire manualmente l'app per la logica](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) in Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Distribuire con Azure PowerShell

Per eseguire la distribuzione in un *gruppo di risorse di Azure*specifico, usare il comando seguente:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Per altre informazioni, vedere gli argomenti seguenti:

* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Eseguire la distribuzione con l'interfaccia della riga di comando di Azure

Per eseguire la distribuzione in un *gruppo di risorse di Azure*specifico, usare il comando seguente:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Per altre informazioni, vedere gli argomenti seguenti:

* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Eseguire la distribuzione con Azure DevOps

Per distribuire modelli di app per la logica e gestire gli ambienti, i team usano comunemente uno strumento come [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) in [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). Azure Pipelines fornisce un' [attività di distribuzione del gruppo di risorse di Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) che è possibile aggiungere a qualsiasi pipeline di compilazione o versione. Per l'autorizzazione per la distribuzione e la generazione della pipeline di rilascio, è necessaria anche un' [entità servizio](../active-directory/develop/app-objects-and-service-principals.md)Azure Active Directory (ad). Altre informazioni sull' [uso delle entità servizio con Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

Per ulteriori informazioni sull'integrazione continua e la distribuzione continua (CI/CD) per i modelli di Azure Resource Manager con Azure Pipelines, vedere gli argomenti e gli esempi seguenti:

* [Integrare modelli di Gestione risorse con Azure Pipelines](../azure-resource-manager/vs-resource-groups-project-devops-pipelines.md)
* [Esercitazione: integrazione continua per i modelli di Azure Resource Manager con Azure Pipelines](../azure-resource-manager/resource-manager-tutorial-use-azure-pipelines.md)
* [Esempio: connettersi alle code del bus di servizio di Azure da app per la logica di Azure e distribuirle con Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: connettersi agli account di archiviazione di Azure da app per la logica di Azure e distribuirli con Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: configurare un'azione dell'app per le funzioni per app per la logica di Azure e distribuirla con Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: connettersi a un account di integrazione da app per la logica di Azure e distribuirlo con Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

Ecco i passaggi generali generali per l'uso di Azure Pipelines:

1. In Azure Pipelines creare una pipeline vuota.

1. Scegliere le risorse necessarie per la pipeline, ad esempio il modello di app per la logica e i file dei parametri del modello, generati manualmente o come parte del processo di compilazione.

1. Per il processo di Agent, trovare e aggiungere l'attività di **distribuzione gruppo di risorse di Azure** .

   ![Aggiungere l'attività "distribuzione gruppo di risorse di Azure"](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Configurare con un' [entità servizio](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

1. Aggiungere i riferimenti ai file del modello e dei parametri del modello dell'app per la logica.

1. Continuare a compilare passaggi nel processo di rilascio per eventuali altri ambienti, test automatizzati o responsabili approvazione necessari.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Autorizzare le connessioni OAuth

Dopo la distribuzione, l'app per la logica funziona end-to-end con parametri validi. Tuttavia, è comunque necessario autorizzare le connessioni OAuth per generare token di accesso validi per [autenticare le credenziali](../active-directory/develop/authentication-scenarios.md). Ecco i modi in cui è possibile autorizzare le connessioni OAuth:

* Per le distribuzioni automatiche, è possibile usare uno script che fornisce il consenso per ogni connessione OAuth. Ecco uno script di esempio in GitHub nel progetto [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) .

* Per autorizzare manualmente le connessioni OAuth, aprire l'app per la logica in progettazione app per la logica, nel portale di Azure o in Visual Studio. Nella finestra di progettazione autorizzare le connessioni necessarie.

Se invece si usa un' [entità servizio](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory (Azure ad) per autorizzare le connessioni, informazioni su come [specificare i parametri dell'entità servizio nel modello di app per la logica](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Monitorare le app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md)

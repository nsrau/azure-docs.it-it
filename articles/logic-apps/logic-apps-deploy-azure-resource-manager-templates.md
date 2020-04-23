---
title: Distribuire modelli di app per la logica
description: Informazioni su come distribuire i modelli di Azure Resource Manager creati per le app per la logica di AzureLearn how to deploy Azure Resource Manager templates created for Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 432e22879ce0eba89f04a1084e2d4a93a487dd45
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086437"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Distribuire modelli di Azure Resource Manager per app per la logica di AzureDeploy Azure Resource Manager templates for Azure Logic Apps

Dopo aver creato un modello di Azure Resource Manager per l'app per la logica, è possibile distribuire il modello nei modi seguenti:After you create an Azure Resource Manager template for your logic app, you can deploy your template in these ways:

* [Azure portal](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Interfaccia della riga di comando di Azure](#cli)
* [API REST di Gestione risorse di Azure](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Distribuire tramite il portale di AzureDeploy through Azure portal

Per distribuire automaticamente un modello di app per la logica in Azure, è possibile scegliere il pulsante **Distribuisci** in Azure seguente, che consente di accedere al portale di Azure e richiede informazioni sull'app per la logica. È quindi possibile apportare le modifiche necessarie al modello o ai parametri dell'app per la logica.

[![Distribuzione in Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Ad esempio, vengono richieste le informazioni seguenti dopo l'accesso al portale di Azure:For example, you're prompted for the following information after you sign in to the Azure portal:

* Nome della sottoscrizione di Azure
* Gruppo di risorse da usare
* Percorso dell'app per la logica
* Nome dell'app per la logica
* Un URI di test
* Accettazione dei termini e delle condizioni specificati

Per altre informazioni, vedere gli argomenti seguenti:

* [Panoramica: automatizzare la distribuzione per le app per la logica con i modelli di Azure Resource ManagerOverview: Automate deployment for logic apps with Azure Resource Manager templates](logic-apps-azure-resource-manager-templates-overview.md)
* [Distribuire risorse con i modelli di Azure Resource Manager e il portale di AzureDeploy resources with Azure Resource Manager templates and the Azure portal](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Distribuire con Visual Studio

Per distribuire un modello di app per la logica da un progetto Gruppo di risorse di Azure creato tramite Visual Studio, seguire questi [passaggi per distribuire manualmente l'app per la logica](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) in Azure.To deploy a logic app template from an Azure Resource Group project that you created by using Visual Studio, follow these steps to manually deploy your logic app to Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Distribuire con Azure PowerShell

Per eseguire la distribuzione in un gruppo di risorse di Azure specifico, usare il comando seguente:To deploy to a specific *Azure resource group*, use the following command:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Per altre informazioni, vedere gli argomenti seguenti:

* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Distribuire con l'interfaccia della riga di comando di Azure

Per eseguire la distribuzione in un gruppo di risorse di Azure specifico, usare il comando seguente:To deploy to a specific *Azure resource group*, use the following command:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Per altre informazioni, vedere gli argomenti seguenti:

* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/templates/deploy-cli.md)
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Distribuzione con DevOps di AzureDeploy with Azure DevOps

Per distribuire modelli di app per la logica e gestire gli ambienti, i team usano in genere uno strumento come Le pipeline di Azure in DevOps di Azure.To deploy logic app templates and manage environments, teams commonly use a tool such as [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) in [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). Pipeline di Azure offre [un'attività](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) di distribuzione del gruppo di risorse di Azure che è possibile aggiungere a qualsiasi pipeline di compilazione o rilascio. Per l'autorizzazione a distribuire e generare la pipeline di rilascio, è necessaria anche [un'entità servizio](../active-directory/develop/app-objects-and-service-principals.md)di Azure Active Directory (AD). Altre informazioni sull'uso delle entità servizio con le pipeline di Azure.Learn more about [using service principals with Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

Per altre informazioni sull'integrazione continua e sulla distribuzione continua (CI/CD) per i modelli di Azure Resource Manager con le pipeline di Azure, vedere gli argomenti e gli esempi seguenti:For more information about continuous integration and continuous deployment (CI/CD) for Azure Resource Manager templates with Azure Pipelines, see these topics and samples:

* [Integrare i modelli di Resource Manager con le pipeline di AzureIntegrate Resource Manager templates with Azure Pipelines](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Esercitazione: Integrazione continua dei modelli di Azure Resource Manager con Azure Pipelines](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [Esempio: connettersi alle code del bus di servizio di Azure dalle app per la logica di Azure e distribuirle con pipeline di Azure in DevOps di AzureSample: Connect to Azure Service Bus queues from Azure Logic Apps and deploy with Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: connettersi agli account di archiviazione di Azure dalle app per la logica di Azure e distribuirle con pipeline di Azure in DevOps di AzureSample: Connect to Azure Storage accounts from Azure Logic Apps and deploy with Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: configurare un'azione dell'app per le funzioni per le app per la logica di Azure e distribuirla con le pipeline di Azure in DevOps di AzureSample: Set up a function app action for Azure Logic Apps and deploy with Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: connettersi a un account di integrazione da App per la logica di Azure e distribuirlo con le pipeline di Azure in DevOps di AzureSample: Connect to an integration account from Azure Logic Apps and deploy with Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Esempio: Orchestrare le pipeline di Azure usando le app per la logica di AzureSample: Orchestrate Azure Pipelines by using Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Ecco i passaggi generali di alto livello per l'uso delle pipeline di Azure:Here are the general high-level steps for using Azure Pipelines:

1. In Pipeline di Azure creare una pipeline vuota.

1. Scegliere le risorse necessarie per la pipeline, ad esempio i file di parametri del modello di app per la logica e del modello, generati manualmente o come parte del processo di compilazione.

1. Per il processo dell'agente, trovare e aggiungere l'attività di distribuzione del gruppo di risorse di **Azure.For** your agent job, find and add the Azure Resource Group Deployment task.

   ![Aggiungere l'attività "Distribuzione gruppo di risorse di Azure"Add "Azure Resource Group Deployment" task](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Configurare con [un'entità servizio](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

1. Aggiungere riferimenti ai file di parametri del modello e del modello dell'app per la logica.

1. Continuare a compilare passaggi nel processo di rilascio per eventuali altri ambienti, test automatizzati o responsabili approvazione necessari.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Autorizzare le connessioni OAuth

Dopo la distribuzione, l'app per la logica funziona end-to-end con parametri validi. Tuttavia, è comunque necessario autorizzare tutte le connessioni OAuth per generare token di accesso validi per [l'autenticazione delle credenziali.](../active-directory/develop/authentication-scenarios.md) Di seguito sono riportati i modi in cui è possibile autorizzare le connessioni OAuth:Here are ways that you can authorize OAuth connections:

* Per le distribuzioni automatiche, è possibile utilizzare uno script che fornisce il consenso per ogni connessione OAuth. Ecco uno script di esempio in GitHub nel progetto LogicAppConnectionAuth.Here's an example script in GitHub in the [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) project.

* Per autorizzare manualmente le connessioni OAuth, aprire l'app per la logica in Progettazione app per la logica, nel portale di Azure o in Visual Studio.To manually authorize OAuth connections, open your logic app in Logic App Designer, either in the Azure portal or in Visual Studio. Nella finestra di progettazione autorizzare tutte le connessioni necessarie.

Se invece si usa [un'entità servizio](../active-directory/develop/app-objects-and-service-principals.md) di Azure Active Directory (Azure AD) per autorizzare le connessioni, vedere come specificare i [parametri dell'entità servizio nel modello](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)di app per la logica.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Monitorare le app per la logica](../logic-apps/monitor-logic-apps.md)

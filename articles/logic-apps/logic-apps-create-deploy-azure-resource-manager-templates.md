---
title: Distribuire le App per la logica con i modelli di Azure Resource Manager - App per la logica di Azure
description: Distribuire le App per la logica usando modelli di Azure Resource Manager
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.date: 10/15/2017
ms.openlocfilehash: 21603ff213bc8babb4145209a76aee0b4150cc12
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60681756"
---
# <a name="deploy-logic-apps-with-azure-resource-manager-templates"></a>Distribuire le App per la logica con modelli Azure Resource Manager

Dopo aver creato un modello di Azure Resource Manager per la distribuzione di app per la logica, è possibile distribuire il modello nei modi seguenti:

* [Portale di Azure](#portal)
* [Azure PowerShell](#powershell)
* [Interfaccia della riga di comando di Azure](#cli)
* [API REST di Gestione risorse di Azure](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Pipeline di DevOps di Azure, Azure](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Distribuzione tramite il portale di Azure

Per distribuire automaticamente un modello di app per la logica in Azure, è possibile scegliere le opzioni seguenti **Distribuisci in Azure** pulsante che si accede al portale di Azure e richiede le informazioni sulle app per la logica. È quindi possibile apportare le modifiche necessarie per il modello di app per la logica o i parametri.

[![Distribuzione in Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Ad esempio, viene chiesto di queste informazioni dopo l'accesso al portale di Azure:

* Nome della sottoscrizione di Azure
* Gruppo di risorse da usare
* Percorso dell'app per la logica
* Nome dell'app per la logica
* Un URI di test
* Accettazione dei termini e delle condizioni specificati

Per altre informazioni, vedere [distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

## <a name="authorize-oauth-connections"></a>Autorizzare le connessioni OAuth

Una volta distribuite, le app per la logica funzionano end-to-end con parametri validi. Tuttavia, è necessario continuare ad autorizzare le connessioni OAuth per generare un token di accesso valido. Per le distribuzioni automatizzate, è possibile usare uno script che dà il consenso per ogni connessione OAuth, ad esempio ciò [script di esempio nel progetto GitHub LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth). È anche possibile autorizzare le connessioni OAuth tramite il portale di Azure o in Visual Studio, aprire l'app per la logica in Progettazione App per la logica.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Distribuire con Azure PowerShell

Per distribuire a uno specifico *gruppo di risorse Azure*, usare questo comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

Per distribuire in una sottoscrizione di Azure specifica, usare questo comando:

```powershell
New-AzDeployment -Location <location> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](/powershell/module/az.resources/new-azresourcegroupdeployment)
* [`New-AzDeployment`](/powershell/module/az.resources/new-azdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Distribuire con l'interfaccia della riga di comando di Azure

Per distribuire a uno specifico *gruppo di risorse Azure*, usare questo comando:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Per distribuire in una sottoscrizione di Azure specifica, usare questo comando:

```azurecli
az deployment create --location <location> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Per altre informazioni, vedere gli argomenti seguenti: 

* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)
* [`az deployment create`](https://docs.microsoft.com/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Distribuire con DevOps di Azure

Per distribuire modelli di app per la logica e la gestione degli ambienti, i team in genere usare uno strumento, ad esempio [pipeline di Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) nelle [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). Le pipeline di Azure fornisce un' [attività distribuzione gruppo di risorse di Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) che è possibile aggiungere a una compilazione o pipeline di rilascio.
Per l'autorizzazione distribuire e generare la pipeline di rilascio, è necessario anche un Azure Active Directory (AD) [entità servizio](../active-directory/develop/app-objects-and-service-principals.md). Altre informazioni sulle [usando le entità servizio con le pipeline di Azure](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

Ecco i passaggi di alto livello generali per l'uso di pipeline di Azure:

1. Nelle pipeline di Azure, creare una pipeline vuota.

1. Scegliere le risorse che necessarie per la pipeline, ad esempio il modello di app per la logica e i file di parametri di modello, che si generano manualmente o come parte del processo di compilazione.

1. Per il processo dell'agente, trovare e aggiungere il **distribuzione gruppo di risorse di Azure** attività.

   ![Aggiungere attività "Distribuzione gruppo di risorse di Azure"](./media/logic-apps-create-deploy-template/add-azure-resource-group-deployment-task.png)

1. Configurare con un [entità servizio](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

1. Aggiungere i riferimenti al modello di app per la logica e i file di parametri di modello.

1. Continuare a compilare passaggi nel processo di rilascio per eventuali altri ambienti, test automatizzati o responsabili approvazione necessari.

## <a name="get-support"></a>Supporto

In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Monitorare le app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md)

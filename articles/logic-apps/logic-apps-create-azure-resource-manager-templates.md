---
title: Creare modelli di app per la logica per la distribuzioneCreate logic app templates for deployment
description: Informazioni su come creare modelli di Azure Resource Manager per l'automazione della distribuzione nelle app per la logica di AzureLearn how to create Azure Resource Manager templates for automating deployment in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 1fdee9a5d90fc065e198d880f9d0dea10804b881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972640"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Creare modelli di Azure Resource Manager per automatizzare la distribuzione per le app per la logica di AzureCreate Azure Resource Manager templates to automate deployment for Azure Logic Apps

Per automatizzare la creazione e la distribuzione dell'app per la logica, in questo articolo vengono descritti i modi in cui è possibile creare un modello di [Azure Resource Manager](../azure-resource-manager/management/overview.md) per l'app per la logica. Per una panoramica della struttura e della sintassi di un modello che include la definizione del flusso di lavoro e altre risorse necessarie per la distribuzione, vedere [Panoramica: Automatizzare](logic-apps-azure-resource-manager-templates-overview.md)la distribuzione per le app per la logica con i modelli di Azure Resource Manager.

App per la logica di Azure offre un modello di [Azure Resource Manager per la logica predefinita](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) che è possibile riutilizzare, non solo per la creazione di app per la logica, ma anche per definire le risorse e i parametri da usare per la distribuzione. È possibile usare questo modello per i propri scenari aziendali o personalizzarlo in base alle esigenze.

> [!IMPORTANT]
> Assicurarsi che le connessioni nel modello usino lo stesso gruppo di risorse di Azure e lo stesso percorso dell'app per la logica.

Per altre informazioni sui modelli di Azure Resource Manager, vedere gli argomenti seguenti:For more about Azure Resource Manager templates, see these topics:

* [Struttura e sintassi dei modelli di Azure Resource ManagerAzure Resource Manager template structure and syntax](../azure-resource-manager/templates/template-syntax.md)
* [Creazione di modelli di Gestione risorse di Azure](../azure-resource-manager/templates/template-syntax.md)
* [I modelli di Azure Resource Manager possono essere sviluppati per la coerenza cloud](../azure-resource-manager/templates/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Creare modelli con Visual Studio

For the easiest way to create valid parameterized logic app templates that are mostly ready for deployment, use Visual Studio (free Community edition or greater) and the Azure Logic Apps Tools for Visual Studio. È quindi possibile [creare l'app per la logica in Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) oppure trovare e scaricare [un'app per la logica esistente dal portale](../logic-apps/manage-logic-apps-with-visual-studio.md)di Azure in Visual Studio .

Scaricando l'app per la logica, si ottiene un modello che include le definizioni per l'app per la logica e altre risorse, ad esempio le connessioni. Il modello *parametrizza*inoltre o definisce i parametri per i valori utilizzati per la distribuzione dell'app per la logica e di altre risorse. È possibile fornire i valori per questi parametri in un file di parametri separato. In questo modo, è possibile modificare più facilmente questi valori in base alle esigenze di distribuzione. Per altre informazioni, vedere gli argomenti seguenti:

* [Creare app per la logica con Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Gestire app per la logica con Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Creare modelli con Azure PowerShellCreate templates with Azure PowerShell

È possibile creare modelli di Resource Manager usando Azure PowerShell con il [modulo LogicAppTemplate](https://github.com/jeffhollan/LogicAppTemplateCreator). Questo modulo open source valuta innanzitutto l'app per la logica e tutte le connessioni utilizzate dall'app per la logica. Il modulo genera quindi le risorse modello con i parametri necessari per la distribuzione.

Si supponga, ad esempio, di disporre di un'app per la logica che riceve un messaggio da una coda del bus di servizio di Azure e carica i dati in un database SQL di Azure.For example, suppose you have a logic app that receives a message from an Azure Service Bus queue and uploads data to an Azure SQL database. Il modulo mantiene tutta la logica di orchestrazione e parametrizza le stringhe di connessione SQL e bus di servizio in modo che sia possibile fornire e modificare tali valori in base alle esigenze di distribuzione.

Questi esempi illustrano come creare e distribuire app per la logica usando i modelli di Azure Resource Manager, le pipeline di Azure in Azure DevOps e Azure PowerShell:These samples show how to create and deploy logic apps by using Azure Resource Manager templates, Azure Pipelines in Azure DevOps, and Azure PowerShell:

* [Esempio: Connettersi alle code del bus di servizio di Azure dalle app per la logica di AzureSample: Connect to Azure Service Bus queues from Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: Connettersi agli account di archiviazione di Azure dalle app per la logica di AzureSample: Connect to Azure Storage accounts from Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: configurare un'azione dell'app per le funzioni per le app per la logica di AzureSample: Set up a function app action for Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: Connettersi a un account di integrazione da App per la logica di AzureSample: Connect to an integration account from Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>Installare i moduli di PowerShell

1. Se non è già stato fatto, installare [Azure PowerShell.](https://docs.microsoft.com/powershell/azure/install-az-ps)

1. Per il modo più semplice per installare il modulo LogicAppTemplate da [PowerShell Gallery](https://www.powershellgallery.com/packages/LogicAppTemplate), eseguire questo comando:

   ```text
   PS> Install-Module -Name LogicAppTemplate
   ```

   Per eseguire l'aggiornamento alla versione più recente, eseguire questo comando:

   ```text
   PS> Update-Module -Name LogicAppTemplate
   ```

In alternativa, per eseguire l'installazione manuale, seguire i passaggi descritti in GitHub per [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator).

### <a name="install-azure-resource-manager-client"></a>Installare il client di Azure Resource ManagerInstall Azure Resource Manager client

Affinché il modulo LogicAppTemplate funzioni con qualsiasi tenant di Azure e token di accesso alla sottoscrizione, installare [lo strumento client di Azure Resource Manager,](https://github.com/projectkudu/ARMClient)un semplice strumento della riga di comando che chiama l'API di Azure Resource Manager.For the LogicAppTemplate module to work with any Azure tenant and subscription access token, install the Azure Resource Manager client tool , which is a simple command line tool that calls the Azure Resource Manager API.

Quando si `Get-LogicAppTemplate` esegue il comando con questo strumento, il comando ottiene innanzitutto un token di accesso tramite lo strumento ARMClient, il token viene reindirizzato allo script di PowerShell e crea il modello come file JSON. Per altre informazioni sullo strumento, vedere questo articolo sullo strumento client di Azure Resource Manager.For more information about the tool, see [this article about the Azure Resource Manager client tool](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html).

### <a name="generate-template-with-powershell"></a>Genera modello con PowerShell

Per generare il modello dopo l'installazione del modulo LogicAppTemplate e dell'interfaccia della riga di comando di [Azure,](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)eseguire il comando di PowerShell seguente:To generate your template after installing the LogicAppTemplate module and Azure CLI , run this PowerShell command:

```text
PS> Get-LogicAppTemplate -Token (az account get-access-token | ConvertFrom-Json).accessToken -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Per seguire la raccomandazione per il piping in un token dallo strumento client di [Azure Resource Manager,](https://github.com/projectkudu/ARMClient)eseguire questo comando invece dove si trova l'ID sottoscrizione di Azure:To follow the recommendation for piping in a token from the Azure Resource Manager client tool , run this command instead where `$SubscriptionId` is your Azure subscription ID:

```text
PS> armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Dopo l'estrazione, è possibile creare un file di parametri dal modello eseguendo questo comando:After extraction, you can then create a parameters file from your template by running this command:

```text
PS> Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Per l'estrazione con i riferimenti dell'insieme di chiavi di Azure (solo statico), eseguire questo comando:For extraction with Azure Key Vault references (static only), run this command:

```text
PS> Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| Parametri | Obbligatoria | Descrizione |
|------------|----------|-------------|
| TemplateFile | Sì | Il percorso del file modello |
| Insieme di credenziali delle chiavi | No | Enumerazione che descrive come gestire i possibili valori dell'insieme di credenziali delle chiavi. Il valore predefinito è `None`. |
||||

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuire modelli di app per la logica](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

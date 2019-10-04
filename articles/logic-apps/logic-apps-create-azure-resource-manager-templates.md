---
title: Creare modelli di app per la logica per la distribuzione-app per la logica di Azure
description: Informazioni su come creare modelli di Azure Resource Manager per l'automazione della distribuzione in app per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 57e9cec16326068cc7de74b8f7266fbe47808fed
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845444"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Creare modelli di Azure Resource Manager per automatizzare la distribuzione per le app per la logica di Azure

Per semplificare l'automazione della creazione e della distribuzione dell'app per la logica, in questo articolo vengono descritte le modalità di creazione di un [modello di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) per l'app per la logica. Per una panoramica della struttura e della sintassi di un modello che include la definizione del flusso di lavoro e altre risorse necessarie per [la distribuzione, vedere Panoramica: Automatizzare la distribuzione per le app per la](logic-apps-azure-resource-manager-templates-overview.md)logica con i modelli Azure Resource Manager.

App per la logica di Azure offre un [modello di Azure Resource Manager di app](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) per la logica predefinito che è possibile riutilizzare, non solo per la creazione di app per la logica, ma anche per definire le risorse e i parametri da usare per la distribuzione. È possibile usare questo modello per i propri scenari aziendali o personalizzarlo in base alle esigenze.

> [!IMPORTANT]
> Assicurarsi che le connessioni nel modello usino lo stesso gruppo di risorse e la stessa località di Azure dell'app per la logica.

Per ulteriori informazioni sui modelli di Azure Resource Manager, vedere gli argomenti seguenti:

* [Struttura e sintassi del modello di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Creazione di modelli di Gestione risorse di Azure](../azure-resource-manager/resource-group-authoring-templates.md)
* [Sviluppare i modelli di Azure Resource Manager per la coerenza cloud](../azure-resource-manager/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Creare modelli con Visual Studio

Per il modo più semplice per creare modelli di app per la logica con parametri validi che siano principalmente pronti per la distribuzione, usare Visual Studio (versione Community gratuita o versione successiva) e gli strumenti delle app per la logica di Azure per Visual Studio. È quindi possibile [creare l'app per la logica in Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) o [trovare e scaricare un'app per la logica esistente dal portale di Azure in Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

Scaricando l'app per la logica, si ottiene un modello che include le definizioni per l'app per la logica e altre risorse, ad esempio le connessioni. Il modello *parametrizza*anche o definisce i parametri per, i valori usati per la distribuzione dell'app per la logica e altre risorse. È possibile specificare i valori per questi parametri in un file di parametri separato. In questo modo, è possibile modificare questi valori in modo più semplice in base alle esigenze di distribuzione. Per altre informazioni, vedere gli argomenti seguenti:

* [Creare app per la logica con Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Gestire app per la logica con Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Creazione di modelli con Azure PowerShell

È possibile creare modelli di Gestione risorse usando Azure PowerShell con il [modulo LogicAppTemplate](https://github.com/jeffhollan/LogicAppTemplateCreator). Questo modulo Open Source valuta prima di tutto l'app per la logica e tutte le connessioni usate dall'app per la logica. Il modulo genera quindi le risorse del modello con i parametri necessari per la distribuzione.

Si supponga, ad esempio, di avere un'app per la logica che riceve un messaggio da una coda del bus di servizio di Azure e carica i dati in un database SQL di Azure. Il modulo conserva tutta la logica di orchestrazione e parametrizza le stringhe di connessione SQL e Service Bus, in modo da poter fornire e modificare i valori in base alle esigenze di distribuzione.

Questi esempi illustrano come creare e distribuire app per la logica usando modelli di Azure Resource Manager, Azure Pipelines in Azure DevOps e Azure PowerShell:

* [Esempio: Connettersi alle code del bus di servizio di Azure da app per la logica di Azure](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: Connettersi agli account di archiviazione di Azure da app per la logica di Azure](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: Configurare un'azione dell'app per le funzioni per app per la logica di Azure](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: Connettersi a un account di integrazione da app per la logica di Azure](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>Installare i moduli di PowerShell

1. Se non è già stato fatto, installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

1. Per il modo più semplice per installare il modulo LogicAppTemplate dalla [PowerShell Gallery](https://www.powershellgallery.com/packages/LogicAppTemplate), eseguire questo comando:

   ```text
   PS> Install-Module -Name LogicAppTemplate
   ```

   Per eseguire l'aggiornamento alla versione più recente, eseguire questo comando:

   ```text
   PS> Update-Module -Name LogicAppTemplate
   ```

In alternativa, per eseguire l'installazione manuale, seguire i passaggi in GitHub per l' [autore del modello di app](https://github.com/jeffhollan/LogicAppTemplateCreator)per la logica.

### <a name="install-azure-resource-manager-client"></a>Installare Azure Resource Manager client

Per usare il modulo LogicAppTemplate con qualsiasi token di accesso della sottoscrizione e del tenant di Azure, installare lo [strumento client di Azure Resource Manager](https://github.com/projectkudu/ARMClient), che è un semplice strumento da riga di comando che chiama l'API Azure Resource Manager.

Quando si esegue il `Get-LogicAppTemplate` comando con questo strumento, il comando ottiene prima di tutto un token di accesso tramite lo strumento ARMClient, invia tramite pipe il token allo script di PowerShell e crea il modello come file JSON. Per ulteriori informazioni sullo strumento, vedere questo [articolo sullo strumento client di Azure Resource Manager](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html).

### <a name="generate-template-with-powershell"></a>Generare un modello con PowerShell

Per generare il modello dopo aver installato il modulo LogicAppTemplate e l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), eseguire questo comando di PowerShell:

```text
PS> Get-LogicAppTemplate -Token (az account get-access-token | ConvertFrom-Json).accessToken -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Per seguire la raccomandazione per il piping in un token dallo [strumento client di Azure Resource Manager](https://github.com/projectkudu/ARMClient), eseguire questo comando, `$SubscriptionId` dove è l'ID sottoscrizione di Azure:

```text
PS> armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Dopo l'estrazione, è possibile creare un file di parametri dal modello eseguendo questo comando:

```text
PS> Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Per l'estrazione con riferimenti Azure Key Vault (solo statico), eseguire questo comando:

```text
PS> Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| Parametri | Obbligatoria | Descrizione |
|------------|----------|-------------|
| TemplateFile | Sì | Percorso del file del modello |
| Insieme di credenziali delle chiavi | No | Enumerazione che descrive come gestire i possibili valori di Key Vault. Il valore predefinito è `None`. |
||||

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Distribuire modelli di app per la logica](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

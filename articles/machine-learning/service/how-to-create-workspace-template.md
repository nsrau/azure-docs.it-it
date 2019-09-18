---
title: Usare un modello di Azure Resource Manager per creare un'area di lavoro
titleSuffix: Azure Machine Learning
description: Informazioni su come usare un modello di Azure Resource Manager per creare una nuova area di lavoro di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 07/16/2019
ms.custom: seoapril2019
ms.openlocfilehash: 7e0897f92dd5ead939cbae9d6bf269bd22152419
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034772"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Usare un modello di Azure Resource Manager per creare un'area di lavoro per Azure Machine Learning

In questo articolo vengono illustrati diversi modi per creare un'area di lavoro Azure Machine Learning usando modelli Azure Resource Manager. Il modello di Resource Manager consente di creare le risorse come un'unica operazione coordinata. Un modello è un documento JSON che definisce le risorse necessarie per una distribuzione. Può anche specificare i parametri di distribuzione. I parametri sono usati per fornire i valori di input quando si usa il modello.

Per altre informazioni, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Prerequisiti

* Una **sottoscrizione di Azure**. Se non si dispone di un, provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Per usare un modello da un'interfaccia della riga di comando, è necessario [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Modello di Resource Manager

Il modello di Gestione risorse seguente può essere usato per creare un'area di lavoro di Azure Machine Learning e le risorse di Azure associate:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Questo modello crea le risorse Azure seguenti:

* Gruppo di risorse di Azure
* Account di archiviazione di Azure
* Azure Key Vault
* Azure Application Insights
* Registro contenitori di Azure
* Area di lavoro di Azure Machine Learning

Il gruppo di risorse è il contenitore che contiene i servizi. I vari servizi sono necessari per l'area di lavoro di Azure Machine Learning.

Il modello di esempio ha due parametri:

* La **posizione** in cui verranno creati il gruppo di risorse e i servizi.

    Il modello utilizzerà la posizione selezionata per la maggior parte delle risorse. L'eccezione è il servizio Application Insights, che non è disponibile in tutte le posizioni in cui sono situati gli altri servizi. Se si seleziona una posizione in cui non è disponibile, il servizio verrà creato nella posizione Stati Uniti centro-meridionali.

* Il **nome dell'area di lavoro**, ovvero il nome descrittivo dell'area di lavoro Azure Machine Learning.

    I nomi degli altri servizi vengono generati in modo casuale.

> [!TIP]
> Sebbene il modello associato a questo documento crei una nuova Container Registry di Azure, è anche possibile creare una nuova area di lavoro senza creare un registro contenitori. Se nell'area di lavoro è presente un registro contenitori, ne verrà creato uno quando si esegue un'operazione che richiede un registro contenitori. Ad esempio, il training o la distribuzione di un modello.
>
> È anche possibile fare riferimento a un registro contenitori esistente o a un account di archiviazione nel modello di Azure Resource Manager, anziché crearne uno nuovo.

Per altre informazioni sui modelli, vedere gli articoli indicati di seguito:

* [Creazione di modelli di Gestione risorse di Azure](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Distribuire un'applicazione con i modelli di Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md)
* [Tipi di risorse Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Seguire i passaggi in [Distribuire risorse da un modello personalizzato](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Quando si arriva alla schermata __Modifica modello__, incollare il modello da questo documento.
1. Selezionare __Salva__ per usare il modello. Fornire le informazioni seguenti e accettare le condizioni elencate:

   * Sottoscrizione: Selezionare la sottoscrizione Azure da usare per queste risorse.
   * Gruppo di risorse: Selezionare o creare un gruppo di risorse per contenere i servizi.
   * Nome dell'area di lavoro: Il nome da utilizzare per l'area di lavoro Azure Machine Learning che verrà creato. Il nome dell'area di lavoro deve avere una lunghezza compresa tra 3 e 33 caratteri. Può contenere solo caratteri alfanumerici e '-'.
   * Percorso: Selezionare la posizione in cui verranno create le risorse.

Per altre informazioni, vedere [Distribuire risorse da un modello personalizzato](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Usare Azure PowerShell

In questo esempio si presuppone che sia stato salvato il modello in un file denominato `azuredeploy.json` nella directory corrente:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) e [Distribuire un modello di Resource Manager privato con un token di firma di accesso condiviso e Azure PowerShell](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

In questo esempio si presuppone che sia stato salvato il modello in un file denominato `azuredeploy.json` nella directory corrente:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus
```

Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md) e [Distribuire un modello di Resource Manager privato con un token di firma di accesso condiviso e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Criteri di accesso Azure Key Vault e modelli di Azure Resource Manager

Quando si usa un modello di Azure Resource Manager per creare l'area di lavoro e le risorse associate (incluso Azure Key Vault), più volte. Ad esempio, l'uso del modello più volte con gli stessi parametri come parte di una pipeline di integrazione e distribuzione continua.

La maggior parte delle operazioni di creazione di risorse tramite i modelli è idempotente, ma Key Vault Cancella i criteri di accesso ogni volta che viene usato il modello. La cancellazione dei criteri di accesso consente di suddividere l'accesso alla Key Vault per tutte le aree di lavoro esistenti che lo usano. Ad esempio, le funzionalità di arresto/creazione di Azure Notebooks macchina virtuale potrebbero non riuscire.  

Per evitare questo problema, è consigliabile usare uno degli approcci seguenti:

*  Non distribuire il modello più di una volta per gli stessi parametri. In alternativa, eliminare le risorse esistenti prima di usare il modello per ricrearle.
  
* Esaminare i criteri di accesso Key Vault e quindi usare questi criteri per impostare la proprietà accessPolicies del modello.
* Controllare se la risorsa Key Vault esiste già. In caso contrario, non ricrearlo tramite il modello. Ad esempio, aggiungere un parametro che consente di disabilitare la creazione della risorsa Key Vault se esiste già.

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire le risorse con i modelli Resource Manager e l'API REST di Resource Manager](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

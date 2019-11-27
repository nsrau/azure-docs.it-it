---
title: Cronologia di distribuzione
description: Questo articolo descrive come visualizzare le operazioni di distribuzione di Azure Resource Manager tramite il portale, PowerShell, l'interfaccia della riga di comando di Azure e l'API REST.
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 895704e5c4cb8acc60067809bdd7e7baa6f05142
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538865"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Visualizzare la cronologia delle distribuzioni con Azure Resource Manager

Azure Resource Manager consente di visualizzare la cronologia di distribuzione ed esaminare operazioni specifiche nelle distribuzioni precedenti. È possibile visualizzare le risorse che sono state distribuite e ottenere informazioni su eventuali errori.

Per informazioni sulla risoluzione di errori di distribuzione specifici vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Ottenere le distribuzioni e l'ID di correlazione

È possibile visualizzare i dettagli di una distribuzione tramite l'portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Ogni distribuzione ha un ID correlazione, che viene usato per tenere traccia degli eventi correlati. Può essere utile quando si utilizza il supporto tecnico per risolvere i problemi relativi a una distribuzione.

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

1. Selezionare il gruppo di risorse che si desidera esaminare.

1. Selezionare il collegamento in **distribuzioni**.

   ![Seleziona cronologia di distribuzione](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. Selezionare una delle distribuzioni dalla cronologia di distribuzione.

   ![Selezionare la distribuzione](./media/resource-manager-deployment-operations/select-details.png)

1. Viene visualizzato un riepilogo della distribuzione, incluso l'ID di correlazione. 

    ![Riepilogo della distribuzione](./media/resource-manager-deployment-operations/show-correlation-id.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Per elencare tutte le distribuzioni per un gruppo di risorse, usare il comando [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment) .

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Per ottenere una distribuzione specifica da un gruppo di risorse, aggiungere il parametro **DeploymentName** .

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Per ottenere l'ID di correlazione, usare:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per elencare la distribuzione per un gruppo di risorse, usare [AZ Group Deployment list](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-list).

```azurecli-interactive
az group deployment list --resource-group ExampleGroup
```

Per ottenere una distribuzione specifica, usare il comando [AZ Group Deployment Show](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-show).

```azurecli-interactive
az group deployment show --resource-group ExampleGroup --name ExampleDeployment
```
  
Per ottenere l'ID di correlazione, usare:

```azurecli-interactive
az group deployment show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="httptabhttp"></a>[HTTP](#tab/http)

Per elencare le distribuzioni per un gruppo di risorse, usare l'operazione seguente. Per il numero di versione più recente dell'API da usare nella richiesta, vedere [distribuzioni-elenca per gruppo di risorse](/rest/api/resources/deployments/listbyresourcegroup). 

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Per ottenere una distribuzione specifica. utilizzare l'operazione seguente. Per il numero di versione più recente dell'API da usare nella richiesta, vedere [distribuzioni-Get](/rest/api/resources/deployments/get).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

La risposta include l'ID di correlazione.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "47ff4228-bf2e-4ee5-a008-0b07da681230",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>Ottenere le operazioni di distribuzione e il messaggio di errore

Ogni distribuzione può includere più operazioni. Per visualizzare altri dettagli su una distribuzione, visualizzare le operazioni di distribuzione. Quando una distribuzione ha esito negativo, le operazioni di distribuzione includono un messaggio di errore.

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

1. Nel riepilogo per una distribuzione selezionare **Dettagli operazione**.

    ![Selezionare le operazioni di distribuzione](./media/resource-manager-deployment-operations/get-operation-details.png)

1. Vengono visualizzati i dettagli per il passaggio della distribuzione. Quando si verifica un errore, i dettagli includono il messaggio di errore.

    ![Mostra dettagli operazione](./media/resource-manager-deployment-operations/see-operation-details.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Per visualizzare le operazioni di distribuzione per la distribuzione in un gruppo di risorse, usare il comando [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azdeploymentoperation) .

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Per visualizzare le operazioni non riuscite, filtrare le operazioni con stato **non riuscito** .

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Per ottenere il messaggio di stato delle operazioni non riuscite, utilizzare il comando seguente:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per visualizzare le operazioni di distribuzione per la distribuzione in un gruppo di risorse, usare il comando [AZ Group Deployment Operation List](/cli/azure/group/deployment/operation?view=azure-cli-latest#az-group-deployment-operation-list) .

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeployment
```

Per visualizzare le operazioni non riuscite, filtrare le operazioni con stato **non riuscito** .

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Per ottenere il messaggio di stato delle operazioni non riuscite, utilizzare il comando seguente:

```azurecli-interactive
az group deployment operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="httptabhttp"></a>[HTTP](#tab/http)

Per ottenere le operazioni di distribuzione, usare l'operazione seguente. Per il numero di versione più recente dell'API da usare nella richiesta, vedere [Deployment Operations-list](/rest/api/resources/deploymentoperations/list).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

La risposta include un messaggio di errore.

```json
{
  "value": [
    {
      "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeploy/operations/13EFD9907103D640",
      "operationId": "13EFD9907103D640",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "9d3cdac4-54f8-486c-94bd-10c20867b8bc",
        "serviceRequestId": "01a9d0fe-896b-4c94-a30f-60b70a8f1ad9",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storageq2czadzfgizc2",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storageq2czadzfgizc2"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla risoluzione di errori di distribuzione specifici vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Per altre informazioni sull'uso dei log attività per monitorare altri tipi di azioni, vedere [Visualizzare i log attività per gestire le risorse di Azure](resource-group-audit.md).
* Per convalidare la distribuzione prima di eseguirla, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](resource-group-template-deploy.md).


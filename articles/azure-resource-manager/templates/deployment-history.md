---
title: Cronologia di distribuzione
description: Questo articolo descrive come visualizzare le operazioni di distribuzione di Azure Resource Manager tramite il portale, PowerShell, l'interfaccia della riga di comando di Azure e l'API REST.
tags: top-support-issue
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 00e0c51244d5c191d4c9f05f689b90ece81ec5a4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91284619"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Visualizzare la cronologia delle distribuzioni con Azure Resource Manager

Azure Resource Manager consente di visualizzare la cronologia di distribuzione. È possibile esaminare operazioni specifiche nelle distribuzioni precedenti e vedere quali risorse sono state distribuite. Questa cronologia contiene informazioni su eventuali errori.

La cronologia di distribuzione per un gruppo di risorse è limitata a 800 distribuzioni. Quando si avvicina il limite, le distribuzioni vengono eliminate automaticamente dalla cronologia. Per alter informazioni, vedere [Eliminazioni automatiche dalla cronologia delle distribuzioni](deployment-history-deletions.md).

Per informazioni sulla risoluzione di errori di distribuzione specifici, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Ottenere le distribuzioni e l'ID correlazione

È possibile visualizzare i dettagli di una distribuzione tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o l'API REST. Ad ogni distribuzione è associato un ID correlazione, che viene usato per tenere traccia degli eventi correlati. Se si [Crea una richiesta di supporto di Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md), il supporto potrebbe richiedere l'ID di correlazione. Il supporto utilizza l'ID di correlazione per identificare le operazioni per la distribuzione non riuscita.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Selezionare il gruppo di risorse da esaminare.

1. Selezionare il collegamento in **Distribuzioni**.

   ![Selezionare la cronologia delle distribuzioni](./media/deployment-history/select-deployment-history.png)

1. Selezionare una delle distribuzioni dalla cronologia delle distribuzioni.

   ![Selezionare la distribuzione](./media/deployment-history/select-details.png)

1. Viene visualizzato un riepilogo della distribuzione, incluso l'ID correlazione.

    ![Riepilogo della distribuzione](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per elencare tutte le distribuzioni per un gruppo di risorse, usare il comando [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment).

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Per ottenere una distribuzione specifica da un gruppo di risorse, aggiungere il parametro **DeploymentName**.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Per ottenere l'ID di correlazione, usare:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per elencare la distribuzione per un gruppo di risorse, usare [az deployment group list](/cli/azure/group/deployment#az-deployment-group-list).

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Per ottenere una distribuzione specifica, usare [az deployment group show](/cli/azure/group/deployment#az-deployment-group-show).

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

Per ottenere l'ID di correlazione, usare:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

Per elencare le distribuzioni per un gruppo di risorse, usare l'operazione seguente. Per il numero di versione dell'API più recente da usare nella richiesta, vedere [Distribuzioni: elencare per gruppo di risorse](/rest/api/resources/deployments/listbyresourcegroup).

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Per ottenere una distribuzione specifica, usare l'operazione seguente. Per il numero di versione dell'API più recente da usare nella richiesta, vedere [Distribuzioni: ottenere](/rest/api/resources/deployments/get).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

La risposta include l'ID correlazione.

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

Ogni distribuzione può includere più operazioni. Per visualizzare più dettagli su una distribuzione, vedere le operazioni di distribuzione. Quando una distribuzione ha esito negativo, le operazioni di distribuzione includono un messaggio di errore.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel riepilogo di una distribuzione selezionare **Dettagli operazione**.

    ![Selezionare i dettagli dell'operazione](./media/deployment-history/get-operation-details.png)

1. Vengono visualizzati i dettagli per il passaggio della distribuzione. Quando si verifica un errore, i dettagli includono il messaggio di errore.

    ![Visualizzare i dettagli dell'operazione](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per visualizzare le operazioni di distribuzione per la distribuzione in un gruppo di risorse, usare il comando [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azdeploymentoperation).

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Per visualizzare le operazioni non riuscite, filtrare le operazioni con lo stato **Non riuscito**.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Per ottenere il messaggio di stato delle operazioni non riuscite, usare il comando seguente:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per visualizzare le operazioni di distribuzione per la distribuzione in un gruppo di risorse, usare il comando [az deployment operation group list](/cli/azure/deployment/operation/group#az-deployment-operation-group-list). È necessario disporre dell'interfaccia della riga di comando di Azure 2.6.0 o versione successiva.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment
```

Per visualizzare le operazioni non riuscite, filtrare le operazioni con lo stato **Non riuscito**.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Per ottenere il messaggio di stato delle operazioni non riuscite, usare il comando seguente:

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

Per visualizzare le operazioni di distribuzione, usare l'operazione seguente. Per il numero di versione dell'API più recente da usare nella richiesta, vedere [Distribuzioni: elencare](/rest/api/resources/deploymentoperations/list).

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

* Per informazioni sulla risoluzione di errori di distribuzione specifici, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](common-deployment-errors.md).
* Per informazioni sul modo in cui vengono gestite le distribuzioni nella cronologia, vedere l'articolo relativo alle [eliminazioni automatiche dalla cronologia di distribuzione](deployment-history-deletions.md).
* Per convalidare la distribuzione prima di eseguirla, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](deploy-powershell.md).


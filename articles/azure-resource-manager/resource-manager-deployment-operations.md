---
title: Cronologia di distribuzione
description: Questo articolo descrive come visualizzare le operazioni di distribuzione di Azure Resource Manager tramite il portale, PowerShell, l'interfaccia della riga di comando di Azure e l'API REST.
tags: top-support-issue
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 23687ddcfb7911a999ee06ac8df8badf341b41d9
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484208"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Visualizzare la cronologia delle distribuzioni con Azure Resource Manager

Azure Resource Manager consente di visualizzare la cronologia di distribuzione ed esaminare operazioni specifiche nelle distribuzioni precedenti. È possibile visualizzare le risorse che sono state distribuite e ottenere informazioni su eventuali errori.

Per informazioni sulla risoluzione di errori di distribuzione specifici vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>di Microsoft Azure

Per ottenere informazioni dettagliate su una distribuzione dalla cronologia di distribuzione.

1. Selezionare il gruppo di risorse che si desidera esaminare.

1. Selezionare il collegamento in **distribuzioni**.

   ![Seleziona cronologia di distribuzione](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. Selezionare una delle distribuzioni dalla cronologia di distribuzione.

   ![Selezionare la distribuzione](./media/resource-manager-deployment-operations/select-details.png)

1. Viene visualizzato un riepilogo della distribuzione, incluso un elenco delle risorse che sono state distribuite.

    ![Riepilogo della distribuzione](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. Per visualizzare il modello utilizzato per la distribuzione, selezionare **modello**. È possibile scaricare il modello per riutilizzarlo.

    ![Mostrare il modello](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. Se la distribuzione non è riuscita, viene visualizzato un messaggio di errore. Per ulteriori informazioni, selezionare il messaggio di errore.

    ![Visualizzazione della distribuzione non riuscita](./media/resource-manager-deployment-operations/show-error.png)

1. Viene visualizzato il messaggio di errore dettagliato.

    ![Visualizza dettagli errore](./media/resource-manager-deployment-operations/show-details.png)

1. L'ID di correlazione viene utilizzato per tenere traccia degli eventi correlati e può essere utile quando si utilizza il supporto tecnico per risolvere i problemi relativi a una distribuzione.

    ![Ottenere l'ID di correlazione](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. Per ulteriori informazioni sul passaggio che ha avuto esito negativo, selezionare **Dettagli operazione**.

    ![Selezionare le operazioni di distribuzione](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. Vengono visualizzati i dettagli per il passaggio della distribuzione.

    ![Mostra dettagli operazione](./media/resource-manager-deployment-operations/show-operation-details.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per ottenere lo stato complessivo di una distribuzione, usare il comando **Get-AzResourceGroupDeployment**.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

In alternativa, è possibile filtrare i risultati per visualizzare solo le distribuzioni con esito negativo.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
```

L'ID di correlazione viene utilizzato per tenere traccia degli eventi correlati e può essere utile quando si utilizza il supporto tecnico per risolvere i problemi relativi a una distribuzione. Per ottenere l'ID di correlazione, usare:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
```

Ogni distribuzione include più operazioni, ognuna delle quali rappresenta un passaggio del processo di distribuzione. Per individuare eventuali problemi, solitamente è necessario visualizzare i dettagli relativi alle operazioni di distribuzione. Per visualizzare lo stato delle operazioni, usare il comando **Get-AzResourceGroupDeploymentOperation**.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName azuredeploy
```

Che restituisce più operazioni, ognuna nel formato seguente:

```powershell
Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
OperationId    : A3EB2DA598E0A780
Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2019-05-13T21:42:40.7151512Z;
                duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
```

Per ottenere altre informazioni sulle operazioni non riuscite, recuperare le proprietà per le operazioni con stato **Non riuscita** .

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

Vengono restituite tutte le operazioni non riuscite, ognuna nel formato seguente:

```powershell
provisioningOperation : Create
provisioningState     : Failed
timestamp             : 2019-05-13T21:42:40.7151512Z
duration              : PT3.1449887S
trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
statusCode            : BadRequest
statusMessage         : @{error=}
targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                       Microsoft.Network/publicIPAddresses/myPublicIP;
                       resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
```

Si notino gli elementi serviceRequestId e trackingId per l'operazione. L'elemento serviceRequestId può essere utile quando si interagisce con il supporto tecnico per risolvere i problemi relativi a una distribuzione, Il trackingId verrà usato nel passaggio successivo per concentrarsi su una determinata operazione.

Per ottenere il messaggio di stato di un'operazione non riuscita particolare, usare il comando seguente:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
```

Che restituisce:

```powershell
code           message                                                                        details
----           -------                                                                        -------
DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
```

Ogni operazione di distribuzione in Azure include il contenuto della richiesta e della risposta. Durante la distribuzione, è possibile usare il parametro **DeploymentDebugLogLevel** per specificare che la richiesta e/o la risposta vengono registrate.

Per ottenere tali informazioni dal log e salvarle in locale, usare i comandi PowerShell seguenti:

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per ottenere lo stato complessivo di una distribuzione, usare il comando **Azure Group Deployment Show** .

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
L'ID di correlazione viene utilizzato per tenere traccia degli eventi correlati e può essere utile quando si utilizza il supporto tecnico per risolvere i problemi relativi a una distribuzione.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

Per visualizzare le operazioni per una distribuzione, usare:

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST

Nell'esempio seguente viene illustrato come ottenere informazioni su una distribuzione. Per la documentazione relativa alla versione più recente dell'API, vedere l'operazione [Deployments-Get](/rest/api/resources/deployments/get) .

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Nella risposta si notino in particolare gli elementi **provisioningState**, **correlationId** ed **error**. Il valore **correlationId** viene usato per tenere traccia degli eventi correlati e può essere utile quando si interagisce con il supporto tecnico per risolvere i problemi relativi a una distribuzione.

```json
{ 
 ...
 "properties": {
   "provisioningState":"Failed",
   "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
   ...
   "error":{
     "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
     "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
   }  
 }
}
```

Nell'esempio seguente viene illustrato come ottenere le operazioni di distribuzione. Per la documentazione relativa alla versione più recente dell'API, vedere operazione di [distribuzione-elenco](/rest/api/resources/deploymentoperations/list) .

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```
   
La risposta include la richiesta e/o le informazioni sulla risposta in base a quanto specificato nella proprietà **debugSetting** durante la distribuzione.

```json
{
 ...
 "properties": 
 {
   ...
   "request":{
     "content":{
       "location":"West US",
       "properties":{
         "accountType": "Standard_LRS"
       }
     }
   },
   "response":{
     "content":{
       "error":{
         "message":"Conflict","code":"Conflict"
       }
     }
   }
 }
}
```

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sulla risoluzione di errori di distribuzione specifici vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Per altre informazioni sull'uso dei log attività per monitorare altri tipi di azioni, vedere [Visualizzare i log attività per gestire le risorse di Azure](resource-group-audit.md).
* Per convalidare la distribuzione prima di eseguirla, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](resource-group-template-deploy.md).


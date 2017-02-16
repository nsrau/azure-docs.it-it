---
title: Operazioni di distribuzione con Azure Resource Manager | Documentazione Microsoft
description: Questo articolo descrive come visualizzare le operazioni di distribuzione di Azure Resource Manager tramite il portale, PowerShell, l&quot;interfaccia della riga di comando di Azure e l&quot;API REST.
services: azure-resource-manager,virtual-machines
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 01/13/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: c2f30eecb62ac578e8635346e1f67d441f29f189
ms.openlocfilehash: e441e338a97c31b9131105ef08186c897d301ceb


---
# <a name="view-deployment-operations-with-azure-resource-manager"></a>Visualizzare le operazioni di distribuzione con Azure Resource Manager


È possibile visualizzare le operazioni per una distribuzione tramite il portale di Azure. È possibile che si sia più interessati a visualizzare le operazioni quando si riceve un errore durante la distribuzione, quindi questo articolo è incentrato sulla visualizzazione delle operazioni non riuscite. Il portale offre un'interfaccia che consente di individuare facilmente gli errori e determinare le potenziali correzioni.

[!INCLUDE [resource-manager-troubleshoot-introduction](../../includes/resource-manager-troubleshoot-introduction.md)]

## <a name="portal"></a>di Microsoft Azure
Per visualizzare le operazioni di distribuzione, attenersi alla procedura seguente:

1. Per il gruppo di risorse coinvolte nella distribuzione, si noti lo stato dell'ultima distribuzione. È possibile selezionare questo stato per ottenere altri dettagli.
   
    ![Stato della distribuzione](./media/resource-manager-deployment-operations/deployment-status.png)
2. Viene visualizzata la cronologia di distribuzione recente. Selezionare la distribuzione non riuscita.
   
    ![Stato della distribuzione](./media/resource-manager-deployment-operations/select-deployment.png)
3. Selezionare il collegamento per visualizzare una descrizione del motivo per cui la distribuzione non è riuscita. Nell'immagine seguente il record DNS non è univoco.  
   
    ![visualizzare la distribuzione non riuscita](./media/resource-manager-deployment-operations/view-error.png)
   
    Questo messaggio di errore dovrebbe essere sufficiente per iniziare la risoluzione dei problemi. Tuttavia, se sono necessari altri dettagli sulle attività completate, è possibile visualizzare le operazioni, come illustrato nei passaggi seguenti.
4. È possibile visualizzare tutte le operazioni di distribuzione nel pannello **Distribuzione** . Selezionare un'operazione per visualizzare altri dettagli.
   
    ![visualizzare operazioni](./media/resource-manager-deployment-operations/view-operations.png)
   
    In questo caso, si noterà che l'account di archiviazione, la rete virtuale e il set di disponibilità sono stati creati correttamente. L'indirizzo IP pubblico non è riuscito e non si sono state tentate altre risorse.
5. È possibile visualizzare gli eventi relativi alla distribuzione selezionando **venti**.
   
    ![visualizzare eventi](./media/resource-manager-deployment-operations/view-events.png)
6. Visualizzare tutti gli eventi per la distribuzione e selezionarne una per altri dettagli. Si notino anche gli ID di correlazione. Questo valore può essere utile quando si interagisce con il supporto tecnico per risolvere i problemi relativi a una distribuzione.
   
    ![vedere eventi](./media/resource-manager-deployment-operations/see-all-events.png)

## <a name="powershell"></a>PowerShell
1. Per ottenere lo stato complessivo di una distribuzione, usare il comando **Get-AzureRmResourceGroupDeployment** . 

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup
  ```

   In alternativa, è possibile filtrare i risultati per visualizzare solo le distribuzioni con esito negativo.

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
  ```
   
2. Ogni distribuzione include più operazioni, ognuna delle quali rappresenta un passaggio del processo di distribuzione. Per individuare eventuali problemi, solitamente è necessario visualizzare i dettagli relativi alle operazioni di distribuzione. Per visualizzare lo stato delle operazioni, usare il comando **Get-AzureRmResourceGroupDeploymentOperation**.

  ```powershell 
  Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName vmDeployment
  ```

    Che restituisce più operazioni, ognuna nel formato seguente:

  ```powershell
  Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
  OperationId    : A3EB2DA598E0A780
  Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                   duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                   serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
  PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                   serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
  ```

3. Per ottenere altre informazioni sulle operazioni non riuscite, recuperare le proprietà per le operazioni con stato **Non riuscita** .

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
  ```
   
    Vengono restituite tutte le operazioni non riuscite, ognuna nel formato seguente:

  ```powershell
  provisioningOperation : Create
  provisioningState     : Failed
  timestamp             : 2016-06-14T21:54:55.1468068Z
  duration              : PT3.1449887S
  trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
  serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
  statusCode            : BadRequest
  statusMessage         : @{error=}
  targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                          Microsoft.Network/publicIPAddresses/myPublicIP;
                          resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
  ```

    Si notino gli elementi serviceRequestId e trackingId per l'operazione. L'elemento serviceRequestId può essere utile quando si interagisce con il supporto tecnico per risolvere i problemi relativi a una distribuzione, mentre l'elemento trackingId viene usato nel passaggio successivo per concentrarsi su una particolare operazione.
4. Per ottenere il messaggio di stato di un'operazione non riuscita particolare, usare il comando seguente:

  ```powershell
  ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
  ```

    Che restituisce:

  ```powershell
  code           message                                                                        details
  ----           -------                                                                        -------
  DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
  ```


## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

1. Per ottenere lo stato complessivo di una distribuzione, è possibile usare il comando **azure group deployment show** .

  ```azurecli
  azure group deployment show --resource-group ExampleGroup --name ExampleDeployment --json
  ```
  
  Uno dei calori restituiti è **correlationId**. Tale valore viene usato per tenere traccia degli eventi correlati e può essere utile quando si interagisce con il supporto tecnico per risolvere i problema relativi a una distribuzione.

  ```azurecli
  "properties": {
    "provisioningState": "Failed",
    "correlationId": "4002062a-a506-4b5e-aaba-4147036b771a",
  ```

2. Per visualizzare le operazioni per una distribuzione, usare:

  ```azurecli
  azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json
  ```

## <a name="rest"></a>REST

1. Ottenere informazioni su una distribuzione con l'operazione [Ottenere informazioni su una distribuzione modello](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get).

  ```http
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
        "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
        "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
      }  
    }
  }
  ```

2. Ottenere informazioni sulle operazioni di distribuzione con l'operazione [Elencare tutte le operazioni di distribuzione modello](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_List). 

  ```http
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




<!--HONumber=Jan17_HO2-->



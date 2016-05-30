<properties
   pageTitle="Risoluzione dei problemi relativi alle distribuzioni con l'API REST | Microsoft Azure"
   description="Illustra come usare l'API REST di Azure Resource Manager per rilevare e correggere i problemi relativi alla distribuzione di Resource Manager."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="03/21/2016"
   ms.author="tomfitz"/>

# Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse con l'API REST di Azure Resource Manager

> [AZURE.SELECTOR]
- [Portale](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Interfaccia della riga di comando di Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Se è stato riscontrato un errore durante la distribuzione di risorse in Azure, è necessario risolvere la causa dell'errore. L'API REST fornisce operazioni che consentono di trovare gli errori e determinare le possibili soluzioni.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## Eseguire la risoluzione dei problemi con l'API REST

1. Distribuire le risorse con l'operazione [Creare una distribuzione modello](https://msdn.microsoft.com/library/azure/dn790564.aspx). Per mantenere informazioni che potrebbero essere utili per il debug, impostare la proprietà **debugSetting** nella richiesta JSON su **requestContent** e/o **responseContent**. 

        PUT https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",      
              },
              "debugSetting": {
                "detailLevel": "requestContent, responseContent"
              }
            }
          }

    Per impostazione predefinita, il valore **debugSetting** è impostato su **none**. Quando si specifica il valore **debugSetting**, è necessario valutare con attenzione il tipo di informazioni passate durante la distribuzione. La registrazione di informazioni sulla richiesta o sulla risposta può esporre dati riservati, che vengono recuperati tramite le operazioni di distribuzione.

2. Ottenere informazioni su una distribuzione con l'operazione [Ottenere informazioni su una distribuzione modello](https://msdn.microsoft.com/library/azure/dn790565.aspx).

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}

    Nella risposta si notino in particolare gli elementi **provisioningState**, **correlationId** ed **error**. Il valore **correlationId** viene usato per tenere traccia degli eventi correlati e può essere utile quando si interagisce con il supporto tecnico per risolvere un problema.
    
        { 
          ...
          "properties": {
            "provisioningState":"Failed",
            "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
            ...
            "error":{
              "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
              "details":[{"code":"Conflict","message":"{\r\n  "error": {\r\n    "message": "Conflict",\r\n    "code": "Conflict"\r\n  }\r\n}"}]
            }  
          }
        }

3. Ottenere informazioni sulle operazioni di distribuzione con l'operazione [Elencare tutte le operazioni di distribuzione modello](https://msdn.microsoft.com/library/azure/dn790518.aspx).

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}

    La risposta includerà la richiesta e/o le informazioni sulla risposta in base a quanto specificato nella proprietà **debugSetting** durante la distribuzione.
    
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

4. Ottenere eventi dai log di controllo per la distribuzione con l'operazione [Elencare gli eventi di gestione in una sottoscrizione](https://msdn.microsoft.com/library/azure/dn931934.aspx).

        GET https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version={api-version}&$filter={filter-expression}&$select={comma-separated-property-names}


## Passaggi successivi

- Per informazioni sulla risoluzione di errori di distribuzione specifici vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Per altre informazioni sull'uso dei log di controllo per monitorare altri tipi di azioni, vedere [Operazioni di controllo con Resource Manager](resource-group-audit.md).
- Per convalidare la distribuzione prima dell'esecuzione, vedere [Distribuire un gruppo di risorse con un modello di Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0518_2016-->
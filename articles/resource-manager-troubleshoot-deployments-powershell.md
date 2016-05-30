<properties
   pageTitle="Risoluzione dei problemi relativi alle distribuzioni con PowerShell | Microsoft Azure"
   description="Questa sezione descrive come usare Azure PowerShell per rilevare e correggere i problemi relativi alla distribuzione di Gestione risorse."
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

# Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse con Azure PowerShell

> [AZURE.SELECTOR]
- [Portale](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Interfaccia della riga di comando di Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Se è stato riscontrato un errore durante la distribuzione di risorse in Azure, è necessario risolvere la causa dell'errore. Azure PowerShell fornisce cmdlet che consentono di trovare facilmente gli errori e determinare potenziali correzioni.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## Usare i log di controllo per risolvere i problemi

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Per visualizzare gli errori per una distribuzione, seguire questa procedura:

1. Per recuperare le voci di log, eseguire il comando **Get-AzureRmLog**. È possibile usare i parametri **ResourceGroup** e **Status** per restituire solo gli eventi con esito negativo per un singolo gruppo di risorse. Se non si specifica un'ora di inizio e fine, vengono restituite le voci per l'ultima ora. Ad esempio, per recuperare le operazioni non riuscite per l'ultima ora, eseguire:

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    È possibile specificare un determinato intervallo di tempo. Nell'esempio successivo verranno cercate le azioni con esito negativo per gli ultimi 14 giorni.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Status Failed
      
    In alternativa, è possibile impostare un'ora di inizio e di fine specifica per le azioni con esito negativo:

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. Se questo comando restituisce troppe voci e proprietà, è possibile concentrare le attività di controllo recuperando la proprietà **properties**. Per vedere i messaggi di errore, si includerà anche il parametro **DetailedOutput**.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties
        
    Il comando restituisce le proprietà delle voci del log nel formato seguente:
        
        Content
        -------
        {}
        {[statusCode, Conflict], [statusMessage, {"Code":"Conflict","Message":"Website with given name mysite already exists...
        {[statusCode, Conflict], [serviceRequestId, ], [statusMessage, {"Code":"Conflict","Message":"Website with given name...

3. È anche possibile limitare i risultati cercando una voce specifica nel messaggio di stato.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json
        
    Il comando restituisce il messaggio di stato nel formato seguente:
        
        Code       : Conflict
        Message    : Website with given name mysite already exists.
        Target     :
        Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
        Innererror :


## Usare le operazioni di distribuzione per risolvere i problemi

1. Per ottenere lo stato complessivo di una distribuzione, usare il comando **Get-AzureRmResourceGroupDeployment**. È possibile filtrare i risultati per visualizzare solo le distribuzioni con esito negativo.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    Il comando restituisce le distribuzioni con esito negativo nel formato seguente:
        
        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleGroup
        ProvisioningState : Failed
        Timestamp         : 8/27/2015 8:03:34 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
        Name             Type                       Value
        ===============  =========================  ==========
        siteName         String                     ExampleSite
        hostingPlanName  String                     ExamplePlan
        siteLocation     String                     West US
        sku              String                     Free
        workerSize       String                     0
        
        Outputs           :

2. Ciascuna distribuzione in genere è costituita da più operazioni, ognuna delle quali rappresenta un passaggio del processo di distribuzione. Per individuare eventuali problemi, solitamente è necessario visualizzare i dettagli relativi alle operazioni di distribuzione. Per visualizzare lo stato delle operazioni, usare il comando **Get-AzureRmResourceGroupDeploymentOperation**.

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment | Format-List
        
    Il comando restituisce le operazioni nel formato seguente:
        
        Id          : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/ExampleDeployment/operations/8518B32868A437C8
        OperationId : 8518B32868A437C8
        Properties  : @{ProvisioningOperation=Create; ProvisioningState=Failed; Timestamp=2016-03-16T20:05:37.2638161Z;
                      Duration=PT2.8834832S; TrackingId=192fbfbf-a2e2-40d6-b31d-890861f78ed3; StatusCode=Conflict;
                      StatusMessage=; TargetResource=}

3. Per altri dettagli sull'operazione, recuperare l'oggetto **Properties**.

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties
        
    Il comando restituisce le proprietà dell'operazione nel formato seguente:
        
        ProvisioningOperation : Create
        ProvisioningState     : Failed
        Timestamp             : 2016-03-16T20:05:37.2638161Z
        Duration              : PT2.8834832S
        TrackingId            : 192fbfbf-a2e2-40d6-b31d-890861f78ed3
        StatusCode            : Conflict
        StatusMessage         : @{Code=Conflict; Message=Website with given name mysite already exists.; Target=;
        Details=System.Object[]; Innererror=}
        TargetResource        : @{Id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
        Microsoft.Web/Sites/mysite; ResourceType=Microsoft.Web/Sites; ResourceName=mysite}

4. Per concentrarsi sul messaggio di stato delle operazioni non riuscite, usare il comando seguente:

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage
        
    Il comando restituisce il messaggio di stato nel formato seguente:
        
        Code       : Conflict
        Message    : Website with given name mysite already exists.
        Target     :
        Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
        Innererror :

## Passaggi successivi

- Per informazioni sulla risoluzione di errori di distribuzione specifici vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Per informazioni sull'uso dei log di controllo per monitorare altri tipi di azioni vedere [Operazioni di controllo con Resource Manager](resource-group-audit.md).
- Per convalidare la distribuzione prima di eseguirla vedere [Distribuire un gruppo di risorse con un modello di Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0518_2016-->
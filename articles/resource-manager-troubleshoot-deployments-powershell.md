<properties
   pageTitle="Visualizzare le operazioni di distribuzione con PowerShell | Microsoft Azure"
   description="Questo articolo descrive come usare Azure PowerShell per rilevare i problemi relativi alla distribuzione di Resource Manager."
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
   ms.date="06/14/2016"
   ms.author="tomfitz"/>

# Visualizzare le operazioni di distribuzione con Azure PowerShell

> [AZURE.SELECTOR]
- [Portale](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Interfaccia della riga di comando di Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

È possibile visualizzare le operazioni per una distribuzione tramite Azure PowerShell. È possibile che si sia più interessati a visualizzare le operazioni quando si riceve un errore durante la distribuzione, quindi questo articolo è incentrato sulla visualizzazione delle operazioni non riuscite. PowerShell fornisce cmdlet che consentono di trovare facilmente gli errori e determinare le potenziali correzioni.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

È possibile evitare alcuni errori convalidando il modello e l'infrastruttura prima della distribuzione. Durante la distribuzione è inoltre possibile registrare ulteriori informazioni su richieste e risposte potenzialmente utili per la risoluzione di eventuali problemi successivi. Per ulteriori informazioni sulla convalida e su come registrare informazioni di richiesta e risposta, vedere come [distribuire un gruppo di risorse con il modello di Azure Resource Manager](resource-group-template-deploy.md).

## Usare le operazioni di distribuzione per risolvere i problemi

1. Per ottenere lo stato complessivo di una distribuzione, usare il comando **Get-AzureRmResourceGroupDeployment**. È possibile filtrare i risultati per visualizzare solo le distribuzioni con esito negativo.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    Il comando restituisce le distribuzioni con esito negativo nel formato seguente:
        
        DeploymentName          : Microsoft.Template
        ResourceGroupName       : ExampleGroup
        ProvisioningState       : Failed
        Timestamp               : 6/14/2016 9:55:21 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                    Name                Type                 Value
                    ===============     ===================  ==========
                    storageAccountName  String               tfstorage9855
                    adminUsername       String               tfadmin
                    adminPassword       SecureString
                    dnsNameforLBIP      String               dns
                    vmNamePrefix        String               myVM
                    imagePublisher      String               MicrosoftWindowsServer
                    imageOffer          String               WindowsServer
                    imageSKU            String               2012-R2-Datacenter
                    lbName              String               myLB
                    nicNamePrefix       String               nic
                    publicIPAddressName String               myPublicIP
                    vnetName            String               myVNET
                    vmSize              String               Standard_D1

        Outputs                 :
        DeploymentDebugLogLevel :

2. Ciascuna distribuzione in genere è costituita da più operazioni, ognuna delle quali rappresenta un passaggio del processo di distribuzione. Per individuare eventuali problemi, solitamente è necessario visualizzare i dettagli relativi alle operazioni di distribuzione. Per visualizzare lo stato delle operazioni, usare il comando **Get-AzureRmResourceGroupDeploymentOperation**.

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName Microsoft.Template
        
    Che restituisce più operazioni, ognuna nel formato seguente:
        
        Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
        OperationId    : A3EB2DA598E0A780
        Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                         duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                         serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
        PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                         serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}

3. Per ottenere altre informazioni sulle operazioni non riuscite, recuperare le proprietà per le operazioni con stato **Non riuscita**.

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
        
    Che restituisce tutte le operazioni non riuscite, ognuna nel formato seguente:
        
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

    Prendere nota dell'ID di traccia dell'operazione. Verrà usato nel passaggio successivo per concentrarsi su un'operazione particolare.

4. Per ottenere il messaggio di stato di un'operazione non riuscita particolare, usare il comando seguente:

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
        
    Che restituisce:
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}

## Usare i log di controllo per risolvere i problemi

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Per visualizzare gli errori per una distribuzione, seguire questa procedura:

1. Per recuperare le voci di log, eseguire il comando **Get-AzureRmLog**. È possibile usare i parametri **ResourceGroup** e **Status** per restituire solo gli eventi non riusciti per un singolo gruppo di risorse. Se non si specifica un'ora di inizio e fine, vengono restituite le voci per l'ultima ora. Ad esempio, per recuperare le operazioni non riuscite per l'ultima ora, eseguire:

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    È possibile specificare un determinato intervallo di tempo. Nell'esempio successivo si cercheranno le azioni non riuscite del giorno precedente.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -Status Failed
      
    In alternativa, è possibile impostare un'ora di inizio e di fine specifica per le azioni con esito negativo:

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. Se questo comando restituisce troppe voci e proprietà, è possibile concentrare le attività di controllo recuperando la proprietà **Properties**. Per vedere i messaggi di errore, si includerà anche il parametro **DetailedOutput**.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -DetailedOutput).Properties
        
    Il comando restituisce le proprietà delle voci del log nel formato seguente:
        
        Content
        -------
        {} 
        {[statusCode, BadRequest], [statusMessage, {"error":{"code":"DnsRecordInUse","message":"DNS record dns.westus.clouda...
        {[statusCode, BadRequest], [serviceRequestId, a426f689-5d5a-448d-a2f0-9784d14c900a], [statusMessage, {"error":{"code...

3. In base a questi risultati, si passerà all'osservazione del secondo elemento. È possibile limitare i risultati esaminando il messaggio di stato per una voce.

        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput -StartTime (Get-Date).AddDays(-1)).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
        
    Che restituisce:
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}



## Passaggi successivi

- Per informazioni sulla risoluzione di errori di distribuzione specifici, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Per informazioni sull'uso dei log di controllo per monitorare altri tipi di azioni, vedere [Operazioni di controllo con Resource Manager](resource-group-audit.md).
- Per convalidare la distribuzione prima di eseguirla vedere [Distribuire le risorse con i modelli di Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0622_2016-->
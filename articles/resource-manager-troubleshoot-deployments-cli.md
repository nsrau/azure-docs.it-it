<properties
   pageTitle="Risoluzione dei problemi relativi alle distribuzioni con l&#39;interfaccia della riga di comando di Azure | Microsoft Azure"
   description="Questa sezione descrive come usare l&#39;interfaccia della riga di comando di Azure per rilevare e correggere i problemi relativi alla distribuzione di Gestione risorse."
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

# Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse con l'interfaccia della riga di comando di Azure

> [AZURE.SELECTOR]
- [Portale](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Interfaccia della riga di comando di Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Se è stato riscontrato un errore durante la distribuzione di risorse in Azure, è necessario risolvere la causa dell'errore. L'interfaccia della riga di comando di Azure fornisce i comandi che consentono di trovare gli errori e determinare le possibili soluzioni.

È possibile risolvere i problemi relativi alla distribuzione esaminando i log di controllo o le operazioni di distribuzione. Questo argomento illustra entrambi i metodi.

È possibile evitare alcuni errori convalidando il modello e l'infrastruttura prima della distribuzione. Per altre informazioni, vedere [Distribuire un gruppo di risorse con un modello di Azure Resource Manager](resource-group-template-deploy.md).

## Usare i log di controllo per risolvere i problemi

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Per visualizzare gli errori per una distribuzione, seguire questa procedura:

1. Per visualizzare i log di controllo, eseguire il comando **azure group log show**. Includendo l'opzione **--last-deployment**, è possibile recuperare solo il log relativo alla distribuzione più recente.

        azure group log show ExampleGroup --last-deployment

2. Il comando **azure group log show** può restituire una notevole quantità di informazioni, ma per la risoluzione dei problemi in genere è necessario concentrarsi sulle operazioni che presentano errori. Lo script seguente usa l'opzione **--json** e l'utilità JSON [jq](https://stedolan.github.io/jq/) per cercare nel log gli errori di distribuzione.

        azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'
        
        {
        "claims": {
          "aud": "https://management.core.windows.net/",
          "iss": "https://sts.windows.net/<guid>/",
          "iat": "1442510510",
          "nbf": "1442510510",
          "exp": "1442514410",
          "ver": "1.0",
          "http://schemas.microsoft.com/identity/claims/tenantid": "{guid}",
          "http://schemas.microsoft.com/identity/claims/objectidentifier": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
          "puid": "XXXXXXXXXXXXXXXX",
          "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",
          "altsecid": "1:example.com:XXXXXXXXXXX",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string="">",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
          "name": "Tom FitzMacken",
          "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
          "groups": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
          "wids": "{guid}",
          "appid": "{guid}",
          "appidacr": "0",
          "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
          "http://schemas.microsoft.com/claims/authnclassreference": "1",
          "ipaddr": "000.000.000.000"
        },
        "properties": {
          "statusCode": "Conflict",
          "statusMessage": "{\"Code\":\"Conflict\",\"Message\":\"Website with given name mysite already exists.\",\"Target\":null,\"Details\":[{\"Message\":\"Website with given name
            mysite already exists.\"},{\"Code\":\"Conflict\"},{\"ErrorEntity\":{\"Code\":\"Conflict\",\"Message\":\"Website with given name mysite already exists.\",\"ExtendedCode\":
            \"54001\",\"MessageTemplate\":\"Website with given name {0} already exists.\",\"Parameters\":[\"mysite\"],\"InnerErrors\":null}}],\"Innererror\":null}"
        },
        ...

    In **properties** sono incluse informazioni in json sull'operazione non riuscita.

    È possibile usare le opzioni **--verbose** e **-vv** per visualizzare altre informazioni contenute nei log. L'opzione **--verbose** consente di visualizzare i passaggi eseguiti dall'operazione in `stdout`. L'opzione **-vv** invece consente di visualizzare la cronologia completa delle richieste. I messaggi spesso forniscono indicazioni importanti sulla causa degli errori.

3. Per concentrarsi sul messaggio di stato delle voci con esito negativo, usare il comando seguente:

        azure group log show ExampleGroup --json | jq -r ".[] | select(.status.value == \"Failed\") | .properties.statusMessage"


## Usare le operazioni di distribuzione per risolvere i problemi

1. Per ottenere lo stato complessivo di una distribuzione, è possibile usare il comando **azure group deployment show**. Nell'esempio seguente la distribuzione ha avuto esito negativo.

        azure group deployment show --resource-group ExampleGroup --name ExampleDeployment
        
        info:    Executing command group deployment show
        + Getting deployments
        data:    DeploymentName     : ExampleDeployment
        data:    ResourceGroupName  : ExampleGroup
        data:    ProvisioningState  : Failed
        data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
        data:    Mode               : Incremental
        data:    Name             Type    Value
        data:    ---------------  ------  ------------
        data:    siteName         String  ExampleSite
        data:    hostingPlanName  String  ExamplePlan
        data:    siteLocation     String  West US
        data:    sku              String  Free
        data:    workerSize       String  0
        info:    group deployment show command OK

2. Per visualizzare il messaggio relativo alle operazioni non riuscite per una distribuzione, usare:

        azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json  | jq ".[] | select(.properties.provisioningState == \"Failed\") | .properties.statusMessage.Message"


## Passaggi successivi

- Per altre informazioni sull'uso dei log di controllo per monitorare altri tipi di azioni, vedere [Operazioni di controllo con Resource Manager](resource-group-audit.md).
- Per convalidare la distribuzione prima dell'esecuzione, vedere [Distribuire un gruppo di risorse con un modello di Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0323_2016-->
---
title: Creare uno spazio dei nomi del bus di servizio con una coda tramite il modello di Azure Resource Manager | Microsoft Docs
description: Creare uno spazio dei nomi e una coda del bus di servizio tramite il modello di Azure Resource Manager
services: service-bus
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 07/11/2016
ms.author: sethm;shvija

---
# <a name="create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Creare uno spazio dei nomi e una coda del bus di servizio tramite il modello di Azure Resource Manager
Questo articolo illustra come usare un modello di Azure Resource Manager per creare uno spazio dei nomi e una coda del bus di servizio. Verrà illustrato come definire le risorse da distribuire e i parametri specificati quando viene eseguita la distribuzione. È possibile usare questo modello per le proprie distribuzioni o personalizzarlo in base alle esigenze.

Per altre informazioni sulla creazione di modelli, vedere [Creazione di modelli di Azure Resource Manager][Creazione di modelli di Azure Resource Manager].

Per il modello completo, vedere il [modello dello spazio dei nomi e della coda del bus di servizio][modello dello spazio dei nomi e della coda del bus di servizio] su GitHub.

> [!NOTE]
> Questi modelli di Azure Resource Manager sono disponibili per il download e la distribuzione.
> 
> * [Creare uno spazio dei nomi del bus di servizio con coda e regola di autorizzazione](service-bus-resource-manager-namespace-auth-rule.md)
> * [Creare uno spazio dei nomi del bus di servizio con argomento e sottoscrizione](service-bus-resource-manager-namespace-topic.md)
> * [Creare uno spazio dei nomi del bus di servizio](service-bus-resource-manager-namespace.md)
> * [Creare uno spazio dei nomi di hub eventi con un hub eventi e un gruppo di consumer](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
> 
> Per verificare gli ultimi modelli, visitare la raccolta [Modelli di avvio rapido di Azure][Modelli di avvio rapido di Azure] e cercare "service bus".
> 
> 

## <a name="what-will-you-deploy?"></a>Distribuzione
Questo modello consente di distribuire uno spazio dei nomi del bus di servizio con una coda.

Le [code del bus di servizio](service-bus-queues-topics-subscriptions.md#queues) consentono un recapito dei messaggi di tipo FIFO (First In, First Out) a uno o più consumer concorrenti.

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuire in Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parametri
Gestione risorse di Azure permette di definire i parametri per i valori da specificare durante la distribuzione del modello. Il modello include una sezione denominata `Parameters` che contiene tutti i valori dei parametri. È necessario definire un parametro per i valori che variano in base al progetto distribuito o all'ambiente in cui viene distribuito il progetto. Non definire i parametri per i valori che rimangono invariati. Ogni valore di parametro nel modello viene usato per definire le risorse distribuite.

Il modello definisce i parametri seguenti.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Nome dello spazio dei nomi del bus di servizio da creare.

```
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
Nome della coda creata nello spazio dei nomi del bus di servizio.

```
"serviceBusQueueName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion
Versione API del bus di servizio del modello.

```
"serviceBusApiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Risorse da distribuire
Crea uno spazio dei nomi del bus di servizio standard di tipo **Messaggistica**con una coda.

```
"resources ": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]",
            }
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Comandi per eseguire la distribuzione
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato e distribuito le risorse con Azure Resource Manager, è possibile imparare a gestire queste risorse. Leggere gli articoli seguenti:

* [Gestire Bus di servizio con PowerShell](service-bus-powershell-how-to-provision.md)
* [Gestire le risorse del bus di servizio con Service Bus Explorer](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

[Creazione di modelli di Azure Resource Manager]: ../resource-group-authoring-templates.md
[modello dello spazio dei nomi e della coda del bus di servizio]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Modelli di avvio rapido di Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Altre informazioni sulle code del bus di servizio]: service-bus-queues-topics-subscriptions.md
[Uso di Azure PowerShell con Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Azure Resource Manager]: ../xplat-cli-azure-resource-manager.md



<!--HONumber=Oct16_HO2-->



<properties
    pageTitle="Creare uno spazio dei nomi del bus di servizio con argomento e sottoscrizione usando un modello di Azure Resource Manager | Microsoft Azure"
    description="Creare uno spazio dei nomi del bus di servizio con argomento e sottoscrizione usando un modello di Azure Resource Manager"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="07/11/2016"
    ms.author="sethm;shvija"/>  

# Creare uno spazio dei nomi del bus di servizio con argomento e sottoscrizione usando un modello di Azure Resource Manager

Questo articolo illustra come usare un modello di Azure Resource Manager per creare uno spazio dei nomi del bus di servizio con un argomento e una sottoscrizione. Verrà illustrato come definire le risorse da distribuire e i parametri specificati quando viene eseguita la distribuzione. È possibile usare questo modello per la distribuzione o personalizzarlo in base alle esigenze.

Per altre informazioni sulla creazione di modelli, vedere [Creazione di modelli di Azure Resource Manager][].

Per il modello completo, vedere il [modello dello spazio dei nomi con argomento e sottoscrizione del bus di servizio][] su GitHub.

>[AZURE.NOTE] Questi modelli di Azure Resource Manager sono disponibili per il download e la distribuzione.
>
>-    [Creare uno spazio dei nomi del bus di servizio con coda e regola di autorizzazione](service-bus-resource-manager-namespace-auth-rule.md)
>-    [Creare uno spazio dei nomi del bus di servizio con coda](service-bus-resource-manager-namespace-queue.md)
>-    [Creare uno spazio dei nomi del bus di servizio](service-bus-resource-manager-namespace.md)
>-    [Creare uno spazio dei nomi del bus di servizio con Hub eventi e un gruppo di consumer usando un modello di Azure Resource Manager](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>
>Per verificare gli ultimi modelli, visitare la raccolta [Modelli di avvio rapido di Azure][] e cercare "service bus".

## Distribuzione

Questo modello consente di distribuire uno spazio dei nomi del bus di servizio con un argomento e una sottoscrizione.

Gli [argomenti e le sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) offrono una forma di comunicazione uno-a-molti, in un schema di *pubblicazione/sottoscrizione*.

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuzione in Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## Parametri

Gestione risorse di Azure permette di definire i parametri per i valori da specificare durante la distribuzione del modello. Il modello include una sezione denominata `Parameters` che contiene tutti i valori dei parametri. È necessario definire un parametro per i valori che variano in base al progetto distribuito o all'ambiente in cui viene distribuito il progetto. Non definire i parametri per i valori che rimangono invariati. Ogni valore di parametro nel modello viene usato per definire le risorse distribuite.

Il modello definisce i parametri seguenti.

### serviceBusNamespaceName

Nome dello spazio dei nomi del bus di servizio da creare.

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### serviceBusTopicName

Nome dell'argomento creato nello spazio dei nomi del bus di servizio.

```
"serviceBusTopicName": {
"type": "string"
}
```

### serviceBusSubscriptionName

Nome della sottoscrizione creata nello spazio dei nomi del bus di servizio.

```
"serviceBusSubscriptionName": {
"type": "string"
}
```

### serviceBusApiVersion

Versione API del bus di servizio del modello.

```
"serviceBusApiVersion": {
"type": "string"
}
```
## Risorse da distribuire

Crea uno spazio dei nomi del bus di servizio standard di tipo **Messaggistica** con un argomento e una sottoscrizione.

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
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

## Comandi per eseguire la distribuzione

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell

```
New-AzureResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## Interfaccia della riga di comando di Azure

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## Passaggi successivi

Dopo aver creato e distribuito le risorse con Azure Resource Manager, è possibile imparare a gestire queste risorse. Leggere gli articoli seguenti:

- [Gestire il bus di servizio di Azure mediante Automazione di Azure](service-bus-automation-manage.md)
- [Gestire Bus di servizio con PowerShell](service-bus-powershell-how-to-provision.md)
- [Gestire le risorse del bus di servizio con Service Bus Explorer](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Creazione di modelli di Azure Resource Manager]: ../resource-group-authoring-templates.md
  [Modelli di avvio rapido di Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [modello dello spazio dei nomi con argomento e sottoscrizione del bus di servizio]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/

<!---HONumber=AcomDC_0831_2016-->
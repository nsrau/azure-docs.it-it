<properties
    pageTitle="Creare una regola di autorizzazione del bus di servizio usando un modello di Azure Resource Manager | Microsoft Azure"
    description="Creare una regola di autorizzazione del bus di servizio per spazio dei nomi e coda usando un modello di Azure Resource Manager"
    services="service-bus-messaging"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>  

<tags
    ms.service="service-bus-messaging"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="07/11/2016"
    ms.author="sethm;shvija"/>

# Creare una regola di autorizzazione del bus di servizio per spazio dei nomi e coda usando un modello di Azure Resource Manager.

Questo articolo illustra come usare un modello di Azure Resource Manager per creare una [regola di autorizzazione](../service-bus/service-bus-authentication-and-authorization.md#shared-access-signature-authentication) per uno spazio dei nomi e una coda del bus di servizio. Verrà illustrato come definire le risorse da distribuire e i parametri specificati quando viene eseguita la distribuzione. È possibile usare questo modello per le proprie distribuzioni o personalizzarlo in base alle esigenze.

Per altre informazioni sulla creazione di modelli, vedere [Creazione di modelli di Azure Resource Manager][].

Per il modello completo, vedere il [modello di regola di autorizzazione del bus di servizio][] su GitHub.

>[AZURE.NOTE] Questi modelli di Azure Resource Manager sono disponibili per il download e la distribuzione.
>
>-    [Creare uno spazio dei nomi del bus di servizio con Hub eventi e un gruppo di consumer usando un modello di Azure Resource Manager](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>-    [Creare uno spazio dei nomi del bus di servizio con coda](service-bus-resource-manager-namespace-queue.md)
>-    [Creare uno spazio dei nomi del bus di servizio con argomento e sottoscrizione](service-bus-resource-manager-namespace-topic.md)
>-    [Creare uno spazio dei nomi del bus di servizio](service-bus-resource-manager-namespace.md)
>
>Per verificare gli ultimi modelli, visitare la raccolta [Modelli di avvio rapido di Azure][] e cercare "service bus".

## Distribuzione

Questo modello consente di distribuire una regola di autorizzazione del bus di servizio per uno spazio dei nomi e un'identità di messaggistica (in questo caso, una coda).

Il modello usa la [firma di accesso condiviso](../service-bus/service-bus-sas-overview.md) per l'autenticazione. La firma di accesso condiviso consente alle applicazione di eseguire l'autenticazione al bus di servizio usando una chiave di accesso configurata nello spazio dei nomi o nell'entità di messaggistica, ad esempio coda o argomento, a cui sono associati diritti specifici. È quindi possibile usare questa chiave per generare un token di firma di accesso condiviso di cui possono avvalersi i client per eseguire l'autenticazione al bus di servizio.

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuzione in Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

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

### namespaceAuthorizationRuleName 

Nome della regola di autorizzazione per lo spazio dei nomi.

```
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### serviceBusQueueName

Nome della coda nello spazio dei nomi del bus di servizio.

```
"serviceBusQueueName": {
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

Crea uno spazio dei nomi del bus di servizio standard di tipo **Messaggistica** e una regola di autorizzazione del bus di servizio per spazio dei nomi ed entità.

```
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "StandardSku",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

## Comandi per eseguire la distribuzione

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## Interfaccia della riga di comando di Azure

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## Passaggi successivi

Dopo aver creato e distribuito le risorse con Azure Resource Manager, è possibile imparare a gestire queste risorse. Leggere gli articoli seguenti:

- [Gestire il bus di servizio di Azure mediante Automazione di Azure](../service-bus/service-bus-automation-manage.md)
- [Gestire Bus di servizio con PowerShell](../service-bus/service-bus-powershell-how-to-provision.md)
- [Gestire le risorse del bus di servizio con Service Bus Explorer](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)
- [Autenticazione e autorizzazione del bus di servizio](../service-bus/service-bus-authentication-and-authorization.md)

  [Creazione di modelli di Azure Resource Manager]: ../resource-group-authoring-templates.md
  [Modelli di avvio rapido di Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [modello di regola di autorizzazione del bus di servizio]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/

<!---HONumber=AcomDC_0928_2016-->
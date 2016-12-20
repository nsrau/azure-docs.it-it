---
title: Creare uno spazio dei nomi del bus di servizio con argomento, sottoscrizione e regola usando un modello di Azure Resource Manager | Microsoft Docs
description: Creare uno spazio dei nomi del bus di servizio con argomento, sottoscrizione e regola usando un modello di Azure Resource Manager
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9e0aaf58-0214-4bca-bd00-d29c08f9b1bc
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/25/2016
ms.author: sethm;shvija
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a1a5d9d6fa62bee7b2b463ddc89fe6c99740e03d


---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Creare uno spazio dei nomi del bus di servizio con argomento, sottoscrizione e regola usando un modello di Azure Resource Manager
Questo articolo illustra come usare un modello di Azure Resource Manager per creare uno spazio dei nomi del bus di servizio con un argomento, una sottoscrizione e una regola (filtro). Illustra inoltre le modalità di definizione delle risorse da distribuire e dei parametri specificati durante l'esecuzione della distribuzione. È possibile usare questo modello per la distribuzione o personalizzarlo in base alle esigenze.

Per altre informazioni sulla creazione di modelli, vedere [Creazione di modelli di Azure Resource Manager][Creazione di modelli di Azure Resource Manager] (Creazione di modelli di Azure Resource Manager).

Per altre informazioni su procedure e modelli relativi alle convenzioni di denominazione delle risorse di Azure, vedere [Convenzioni di denominazione delle risorse Azure][Convenzioni di denominazione delle risorse Azure] (Convenzioni di denominazione delle risorse di Azure).

Per il modello completo, vedere il [modello dello spazio dei nomi del bus di servizio con argomento, sottoscrizione e regola][modello dello spazio dei nomi del bus di servizio con argomento, sottoscrizione e regola].

> [!NOTE]
> Questi modelli di Azure Resource Manager sono disponibili per il download e la distribuzione.
> 
> * [Creare uno spazio dei nomi del bus di servizio con coda e regola di autorizzazione](service-bus-resource-manager-namespace-auth-rule.md)
> * [Creare uno spazio dei nomi del bus di servizio con coda](service-bus-resource-manager-namespace-queue.md)
> * [Creare uno spazio dei nomi del bus di servizio](service-bus-resource-manager-namespace.md)
> * [Creare uno spazio dei nomi del bus di servizio con argomento e sottoscrizione](service-bus-resource-manager-namespace-topic.md)
> 
> Per cercare i modelli più recenti, visitare la raccolta [Modelli di avvio rapido di Azure][Modelli di avvio rapido di Azure] e cercare un bus di servizio.
> 
> 

## <a name="what-will-you-deploy"></a>Distribuzione
Questo modello consente di distribuire uno spazio dei nomi del bus di servizio con un argomento, una sottoscrizione e una regola (filtro).

Gli [argomenti e le sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) offrono una forma di comunicazione uno-a-molti, in un schema di *pubblicazione/sottoscrizione*. Quando si usano gli argomenti e le sottoscrizioni, i componenti di un'applicazione distribuita non comunicano direttamente tra loro, ma scambiano messaggi tramite un argomento, che agisce da intermediario. La sottoscrizione a un argomento è simile a una coda virtuale che riceve copie dei messaggi che sono stati inviati all'argomento. L'applicazione di un filtro a una sottoscrizione consente di specificare quali messaggi inviati a un argomento devono essere presenti in una specifica sottoscrizione dell'argomento.

## <a name="what-are-rules-filters"></a>Che cosa sono le regole (filtri)?
In molti scenari, i messaggi con caratteristiche specifiche devono essere elaborati in modi specifici. A questo scopo, è possibile configurare le sottoscrizioni in modo che trovino i messaggi che presentano le proprietà desiderate e apportare quindi alcune modifiche a tali proprietà. Mentre nelle sottoscrizioni del bus di servizio tutti i messaggi vengono inviati all'argomento, l'utente può copiare solo un subset di tali messaggi nella coda virtuale delle sottoscrizioni. Questa operazione viene eseguita usando i filtri della sottoscrizione. Per altre informazioni sulle regole (filtri), vedere [Code, argomenti e sottoscrizioni del bus di servizio][Code, argomenti e sottoscrizioni del bus di servizio].

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuzione in Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parametri
Con Azure Resource Manager è necessario definire i parametri per i valori da specificare durante la distribuzione del modello. Il modello include una sezione denominata `Parameters` che contiene tutti i valori dei parametri. È necessario definire un parametro per i valori che variano in base al progetto distribuito o all'ambiente in cui viene distribuito il progetto. Non definire i parametri per i valori che rimangono invariati. Ogni valore di parametro nel modello viene usato per definire le risorse distribuite.

Il modello definisce i parametri seguenti:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Nome dello spazio dei nomi del bus di servizio da creare.

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
Nome dell'argomento creato nello spazio dei nomi del bus di servizio.

```
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
Nome della sottoscrizione creata nello spazio dei nomi del bus di servizio.

```
"serviceBusSubscriptionName": {
"type": "string"
}
```
### <a name="servicebusrulename"></a>serviceBusRuleName
Nome della regola (filtro) creata nello spazio dei nomi del bus di servizio.

```
   "serviceBusRuleName": {
   "type": "string",
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
Crea uno spazio dei nomi del bus di servizio standard di tipo **Messaggistica** con argomento, sottoscrizione e regole.

```
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
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
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filter": {
                            "sqlExpression": "StoreName = 'Store1'"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Comandi per eseguire la distribuzione
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato e distribuito le risorse con Azure Resource Manager, è possibile imparare a gestire queste risorse. Leggere gli articoli seguenti:

* [Gestire il bus di servizio di Azure mediante Automazione di Azure](service-bus-automation-manage.md)
* [Gestire Bus di servizio con PowerShell](service-bus-powershell-how-to-provision.md)
* [Gestire le risorse del bus di servizio con Service Bus Explorer](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

[Creazione di modelli di Azure Resource Manager]: ../resource-group-authoring-templates.md
[Modelli di avvio rapido di Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Informazioni su argomenti e sottoscrizioni del bus di servizio]: service-bus-queues-topics-subscriptions.md
[Uso di Azure PowerShell con Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Azure Resource Manager]: ../xplat-cli-azure-resource-manager.md
[Convenzioni di denominazione delle risorse Azure]: https://azure.microsoft.com/en-us/documentation/articles/guidance-naming-conventions/
[modello dello spazio dei nomi del bus di servizio con argomento, sottoscrizione e regola]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Code, argomenti e sottoscrizioni del bus di servizio]:service-bus-queues-topics-subscriptions.md




<!--HONumber=Nov16_HO3-->



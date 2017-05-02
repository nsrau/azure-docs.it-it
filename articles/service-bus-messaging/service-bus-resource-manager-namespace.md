---
title: Creare uno spazio dei nomi del bus di servizio di Azure tramite un modello di Resource Manager | Documentazione Microsoft
description: Usare il modello di Azure Resource Manager per creare uno spazio dei nomi del bus di servizio
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/12/2017
ms.author: sethm;shvija
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: efd0ec90a1c0775661069faa323e56914dd4e032
ms.lasthandoff: 04/13/2017


---

# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Creare uno spazio dei nomi del bus di servizio tramite il modello di Azure Resource Manager

Questo articolo illustra come usare un modello di Azure Resource Manager per creare uno spazio dei nomi del bus di servizio di tipo **Messaging** con SKU Standard/Basic. L'articolo definisce anche i parametri specificati per eseguire la distribuzione. È possibile usare questo modello per le proprie distribuzioni o personalizzarlo in base alle esigenze.

Per altre informazioni sulla creazione di modelli, vedere [Creazione di modelli di Azure Resource Manager][Authoring Azure Resource Manager templates].

Per il modello completo, vedere il [modello dello spazio dei nomi del bus di servizio][Service Bus namespace template] su GitHub.

> [!NOTE]
> Questi modelli di Azure Resource Manager sono disponibili per il download e la distribuzione. 
> 
> * [Creare uno spazio dei nomi del bus di servizio con coda](service-bus-resource-manager-namespace-queue.md)
> * [Creare uno spazio dei nomi del bus di servizio con argomento e sottoscrizione](service-bus-resource-manager-namespace-topic.md)
> * [Creare uno spazio dei nomi del bus di servizio con coda e regola di autorizzazione](service-bus-resource-manager-namespace-auth-rule.md)
> * [Creare uno spazio dei nomi del bus di servizio con argomento, sottoscrizione e regola](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Per verificare la disponibilità di nuovi modelli, visitare la raccolta [Modelli di avvio rapido di Azure][Azure Quickstart Templates] e cercare "service bus".
> 
> 

## <a name="what-will-you-deploy"></a>Distribuzione
Questo modello consente di distribuire uno spazio dei nomi del bus di servizio con uno SKU [Basic, Standard o Premium](https://azure.microsoft.com/pricing/details/service-bus/).

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuzione in Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## <a name="parameters"></a>Parametri
Gestione risorse di Azure permette di definire i parametri per i valori da specificare durante la distribuzione del modello. Il modello include una sezione denominata `Parameters` che contiene tutti i valori dei parametri. È necessario definire un parametro per i valori che variano in base al progetto distribuito o all'ambiente in cui viene distribuito il progetto. Non definire i parametri per i valori che rimangono invariati. Ogni valore di parametro nel modello viene usato per definire le risorse distribuite.

Questo modello definisce i parametri seguenti.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Nome dello spazio dei nomi del bus di servizio da creare.

```json
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebussku"></a>serviceBusSKU
Nome dello [SKU](https://azure.microsoft.com/pricing/details/service-bus/) del bus di servizio da creare.

```json
"serviceBusSku": { 
    "type": "string", 
    "allowedValues": [ 
        "Basic", 
        "Standard",
        "Premium" 
    ], 
    "defaultValue": "Standard", 
    "metadata": { 
        "description": "The messaging tier for service Bus namespace" 
    } 

```

Il modello definisce i valori consentiti per il parametro (Basic, Standard o Premium) e assegna un valore predefinito (Standard) nel caso in cui non venga specificato alcun valore.

Per altre informazioni sui prezzi del bus di servizio, vedere [Service Bus pricing and billing][Service Bus pricing and billing] (Prezzi e fatturazione del bus di servizio).

### <a name="servicebusapiversion"></a>serviceBusApiVersion
Versione API del bus di servizio del modello.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2015-08-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       } 
```

## <a name="resources-to-deploy"></a>Risorse da distribuire
### <a name="service-bus-namespace"></a>Spazio dei nomi del bus di servizio
Crea uno spazio dei nomi del bus di servizio standard di tipo **Messaggistica**.

```json
"resources": [
    {
        "apiVersion": "[parameters('serviceBusApiVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "properties": {
        }
    }
]
```

## <a name="commands-to-run-deployment"></a>Comandi per eseguire la distribuzione
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
```CLI
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato e distribuito le risorse con Azure Resource Manager, imparare a gestire le risorse leggendo gli articoli seguenti:

* [Gestire Bus di servizio con PowerShell](service-bus-manage-with-ps.md)
* [Gestire le risorse del bus di servizio con Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: service-bus-pricing-billing.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md


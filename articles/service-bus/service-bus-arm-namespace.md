<properties
    pageTitle="Creare uno spazio dei nomi del bus di servizio usando un modello di Azure Resource Manager | Microsoft Azure"
    description="Usare il modello di Azure Resource Manager per creare uno spazio dei nomi del bus di servizio"
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
    ms.date="04/15/2016"
    ms.author="sethm;shvija"/>

# Creare uno spazio dei nomi del bus di servizio usando un modello di Azure Resource Manager

In questo articolo viene illustrato come usare un modello di Azure Resource Manager per creare uno spazio dei nomi "Messaggistica" del bus di servizio con SKU Standard/Basic. L'articolo definisce anche i parametri specificati per eseguire la distribuzione. È possibile usare questo modello per le proprie distribuzioni o personalizzarlo in base alle esigenze.

Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Azure Resource Manager][].

Per il modello completo, vedere il [modello dello spazio dei nomi del bus di servizio][] su GitHub.

>[AZURE.NOTE] I modelli di Azure Resource Manager seguenti sono disponibili per il download e la distribuzione.
>
>-    [Creare uno spazio dei nomi del bus di servizio con Hub eventi e un gruppo di consumer](service-bus-arm-namespace-event-hub.md)
>-    [Creare uno spazio dei nomi del bus di servizio con coda](service-bus-arm-namespace-queue.md)
>-    [Creare uno spazio dei nomi del bus di servizio con argomento e sottoscrizione](service-bus-arm-namespace-topic.md)
>-    [Creare uno spazio dei nomi del bus di servizio con coda e regola di autorizzazione](service-bus-arm-namespace-auth-rule.md)
>
>Per verificare gli ultimi modelli, vedere i [modelli di avvio rapido di Azure][] e cercare il bus di servizio.

## Distribuzione

Questo modello consente di distribuire uno spazio dei nomi del bus di servizio con SKU [Basic o Standard](https://azure.microsoft.com/pricing/details/service-bus/).

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuzione in Azure](./media/service-bus-arm-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## Parametri

Gestione risorse di Azure permette di definire i parametri per i valori da specificare durante la distribuzione del modello. Il modello include una sezione denominata `Parameters` che contiene tutti i valori dei parametri. È necessario definire un parametro per i valori che variano in base al progetto distribuito o all'ambiente in cui viene distribuito il progetto. Non definire i parametri per i valori che rimangono invariati. Ogni valore di parametro nel modello viene usato per definire le risorse distribuite.

Di seguito viene fornita la descrizione di ogni parametro del modello.

### serviceBusNamespaceName

Nome dello spazio dei nomi del bus di servizio da creare.

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### serviceBusSKU

Nome dello [SKU](https://azure.microsoft.com/pricing/details/service-bus/) del bus di servizio da creare.

```
"serviceBusSKU": {
"type": "string",
"allowedValues": ["Basic","Standard"],
"defaultValue": "Standard"
}
```

Il modello definisce i valori consentiti per questo parametro, ossia Basic o Standard, e assegna il valore predefinito Standard se non viene specificato alcun valore.

È previsto un costo base per il piano Standard di 10 USD al mese, che consente di eseguire fino a 12,5 milioni di operazioni al mese senza costi aggiuntivi. Il piano Basic ha un costo di 0,05 USD per milione di operazioni

Per altre informazioni sui prezzi del bus di servizio, vedere [Informazioni sul prezzo e la fatturazione del bus di servizio][].

### serviceBusApiVersion

Versione API del bus di servizio del modello.

```
"serviceBusApiVersion": {

"type": "string"

}
```

## Risorse da distribuire

### Spazio dei nomi del bus di servizio

Crea uno spazio dei nomi del bus di servizio standard di tipo **Messaggistica**.

```
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

## Comandi per eseguire la distribuzione

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### Interfaccia della riga di comando di Azure

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## Passaggi successivi

Dopo aver creato e distribuito le risorse con Azure Resource Manager, è possibile imparare a gestire queste risorse. Leggere gli articoli seguenti:

- [Gestire il bus di servizio di Azure mediante Automazione di Azure](service-bus-automation-manage.md)
- [Gestire Bus di servizio con PowerShell](service-bus-powershell-how-to-provision.md)
- [Gestire le risorse del bus di servizio con Service Bus Explorer](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Creazione di modelli di Azure Resource Manager]: ../resource-group-authoring-templates.md
  [modello dello spazio dei nomi del bus di servizio]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
  [modelli di avvio rapido di Azure]: https://azure.microsoft.com/documentation/templates/
  [Informazioni sul prezzo e la fatturazione del bus di servizio]: https://azure.microsoft.com/documentation/articles/service-bus-pricing-billing/
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md

<!---HONumber=AcomDC_0420_2016-->
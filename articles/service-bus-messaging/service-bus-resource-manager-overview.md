<properties
    pageTitle="Creare risorse del bus di servizio usando i modelli di Azure Resource Manager | Microsoft Azure"
    description="Usare i modelli di Azure Resource Manager per automatizzare la creazione di risorse del bus di servizio"
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
    ms.author="sethm"/>


# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Creare risorse del bus di servizio usando i modelli di Azure Resource Manager

Questo articolo illustra come creare e distribuire risorse del bus di servizio e dell'hub eventi usando i modelli di Azure Resource Manager, PowerShell e il provider di risorse del bus di servizio.

I modelli di Azure Resource Manager aiutano a definire le risorse da distribuire per una soluzione e a specificare i parametri e le variabili che consentono di immettere i valori per diversi ambienti. Il modello è composto da JSON ed espressioni che è possibile usare per creare valori per la distribuzione. Per informazioni dettagliate sulla creazione di modelli di Azure Resource Manager e per una descrizione del formato del modello, vedere [Creazione di modelli di Azure Resource Manager](../resource-group-authoring-templates.md). 

>[AZURE.NOTE] Gli esempi inclusi in questo articolo spiegano come usare Azure Resource Manager per creare uno spazio dei nomi del bus di servizio e una entità di messaggistica, ovvero una coda. Per altri esempi di modelli, vedere la raccolta [Modelli di avvio rapido di Azure][] e cercare "service bus".

## <a name="service-bus-and-event-hubs-resource-manager-templates"></a>Modelli di Resource Manager del bus di servizio e dell'hub eventi

Questi modelli di Azure Resource Manager del bus di servizio e dell'hub eventi sono disponibili per il download e la distribuzione. Fare clic sui collegamenti seguenti per informazioni dettagliate su ognuno di essi, con collegamenti ai modelli su GitHub: 

- [Creare uno spazio dei nomi del bus di servizio](service-bus-resource-manager-namespace.md)
- [Creare uno spazio dei nomi del bus di servizio con coda](service-bus-resource-manager-namespace-queue.md)
- [Creare uno spazio dei nomi del bus di servizio con argomento e sottoscrizione](service-bus-resource-manager-namespace-topic.md)
- [Creare uno spazio dei nomi del bus di servizio con coda e regola di autorizzazione](service-bus-resource-manager-namespace-auth-rule.md)
- [Creare uno spazio dei nomi di hub eventi con un hub eventi e un gruppo di consumer](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)

## <a name="deploy-with-powershell"></a>Distribuire con PowerShell

La procedura seguente illustra come usare PowerShell per distribuire un modello di Azure Resource Manager che crea uno spazio dei nomi del bus di servizio di livello **Standard** e una coda all'interno di tale spazio dei nomi. Questo esempio è basato sul modello di [Creare uno spazio dei nomi del bus di servizio con coda](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue). Il flusso di lavoro è all'incirca il seguente:

1. Installare PowerShell.
2. Creare il modello e, facoltativamente, un file di parametri.
2. In PowerShell accedere all'account Azure.
3. Se non esiste, creare un nuovo gruppo di risorse.
4. Testare la distribuzione.
5. Facoltativamente, è possibile impostare la modalità di distribuzione.
6. Distribuire il modello.

Per informazioni più complete sulla distribuzione dei modelli di Azure Resource Manager, vedere [Distribuire le risorse con i modelli di Azure Resource Manager][].

### <a name="install-powershell"></a>Installare PowerShell

Installare Azure PowerShell seguendo le istruzioni in [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

### <a name="create-a-template"></a>Creare un modello

Clonare o copiare il modello [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) da GitHub:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusQueueName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Queue"
            }
        },
        "serviceBusApiVersion": {
            "type": "string",
            "defaultValue": "2015-08-01",
            "metadata": {
                "description": "Service Bus ApiVersion used by the template"
            }
        }
    },
    "variables": {
        "location": "[resourceGroup().location]",
        "sbVersion": "[parameters('serviceBusApiVersion')]",
        "defaultSASKeyName": "RootManageSharedAccessKey",
        "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]"
    },
    "resources": [{
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
                "path": "[parameters('serviceBusQueueName')]"
            }
        }]
    }],
    "outputs": {
        "NamespaceConnectionString": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
        },
        "SharedAccessPolicyPrimaryKey": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
        }
    }
}
```

### <a name="create-a-parameters-file-(optional)"></a>Creare un file di parametri (facoltativo)

Per usare un file di parametri facoltativo, copiare il file [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json). Sostituire il valore di `serviceBusNamespaceName` con il nome dello spazio dei nomi del bus di servizio che si vuole creare in questa distribuzione e sostituire il valore di `serviceBusQueueName` con il nome della coda che si vuole creare. 

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2015-08-01"
        }
    }
}
```

Per altre informazioni, vedere l'argomento [File di parametri](../resource-group-template-deploy.md#parameter-file).

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Accedere ad Azure e impostare la sottoscrizione di Azure

Al prompt di PowerShell, eseguire il comando seguente:

```
Login-AzureRmAccount
```

Il sistema chiede di accedere all'account Azure. Dopo l'accesso, eseguire il comando seguente per visualizzare le sottoscrizioni disponibili.

```
Get-AzureRMSubscription
```

Questo comando restituisce un elenco delle sottoscrizioni di Azure disponibili. Scegliere una sottoscrizione per la sessione corrente eseguendo il comando seguente. Sostituire `<YourSubscriptionId>` con il GUID della sottoscrizione di Azure che si vuole usare.

```
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Impostare il gruppo di risorse

Se non è disponibile un gruppo di risorse, creare un nuovo gruppo di risorse con il comando **New-AzureRmResourceGroup**. Specificare il nome del gruppo di risorse e la posizione che si vuole usare, ad esempio:

```
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Se il nuovo gruppo di risorse è stato creato correttamente, viene visualizzato il relativo riepilogo.

```
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Test della distribuzione

Convalidare la distribuzione eseguendo il cmdlet `Test-AzureRmResourceGroupDeployment`. Durante il test della distribuzione, specificare esattamente gli stessi parametri di quando si esegue la distribuzione.

```
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Creare la distribuzione

Per creare la nuova distribuzione, eseguire il comando `New-AzureRmResourceGroupDeployment` e specificare i parametri necessari quando viene richiesto. I parametri includono il nome della distribuzione, il nome del gruppo di risorse e il percorso o l'URL del file di modello. Se il parametro **Mode** non è specificato, viene usato il valore predefinito **Incremental**. Per altre informazioni, vedere [Distribuzioni incrementali e complete](../resource-group-template-deploy.md#incremental-and-complete-deployments).

Il comando seguente richiede tre parametri nella finestra di PowerShell:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Per specificare invece un file di parametri, usare il comando seguente.

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

È anche possibile usare i parametri inline quando si esegue il cmdlet di distribuzione. Il comando è il seguente:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Per eseguire una distribuzione [completa](../resource-group-template-deploy.md#incremental-and-complete-deployments), impostare il parametro **Mode** su **Complete**:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

### <a name="verify-the-deployment"></a>Verificare la distribuzione

Se le risorse vengono distribuite correttamente, nella finestra di PowerShell viene visualizzato un riepilogo della distribuzione:

```
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2016 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2015-08-01

```

## <a name="next-steps"></a>Passaggi successivi

Finora sono stati illustrati il flusso di lavoro di base e i comandi per la distribuzione di un modello di Azure Resource Manager. Per informazioni più dettagliate, visitare i collegamenti seguenti:

- [Panoramica di Azure Resource Manager][]
- [Distribuire le risorse con i modelli di Azure Resource Manager][]
- [Creazione di modelli](../resource-group-authoring-templates.md)


[Panoramica di Azure Resource Manager]: ../resource-group-overview.md
[Distribuire le risorse con i modelli di Azure Resource Manager]: ../resource-group-template-deploy.md
[Raccolta di modelli di avvio rapido di Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus


<!--HONumber=Oct16_HO2-->



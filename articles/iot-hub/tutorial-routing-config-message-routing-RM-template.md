---
title: Configurare il routing dei messaggi per l'hub IoT di Azure usando un modello di Azure Resource Manager | Microsoft Docs
description: Configurare il routing dei messaggi per l'hub IoT di Azure usando un modello di Azure Resource Manager
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: d7b8c0685cf92341241575d3e67c09a759f5c190
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543749"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Esercitazione: Usare un modello di Azure Resource Manager per configurare il routing dei messaggi per l'hub IoT

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Routing dei messaggi

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>Scaricare il modello e il file dei parametri

Per la seconda parte di questa esercitazione, scaricare ed eseguire un'applicazione di Visual Studio per inviare messaggi all'hub IoT. Il download include una cartella contenente il modello di Azure Resource Manager e il file di parametri, oltre agli script dell'interfaccia della riga di comando di Azure e di PowerShell.

Procedere a scaricare gli [esempi in C# di IoT di Azure](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Estrarre il file master.zip. Il modello di Resource Manager e il file di parametri, denominati **template_iothub.json** e **template_iothub_parameters.json**, si trovano nel percorso /iot-hub/Tutorials/Routing/SimulatedDevice/resources/.

## <a name="create-your-resources"></a>Creare le risorse

Per creare tutte le risorse, verrà usato un modello di Azure Resource Manager. Gli script dell'interfaccia della riga di comando di Azure e di PowerShell possono essere eseguiti alcune righe alla volta. Il modello di Resource Manager viene distribuito in un unico passaggio. Questo articolo illustra le sezioni separatamente per una maggiore comprensibilità di ognuna. Quindi mostrerà come distribuire il modello e creare il dispositivo virtuale per i test. Dopo la distribuzione del modello, è possibile visualizzare la configurazione del routing dei messaggi nel portale.

Diversi nomi di risorse devono essere univoci globali, come il nome dell'hub IoT e il nome dell'account di archiviazione. Per semplificare la denominazione delle risorse, ai nomi viene aggiunto un valore alfanumerico casuale generato dalla data/ora corrente. 

Se si esamina il modello, si vedrà dove sono configurate le variabili per queste risorse che accettano il parametro passato e lo concatenano con *randomValue*. 

La sezione seguente descrive i parametri usati.

### <a name="parameters"></a>Parametri

La maggior parte di questi parametri hanno valori predefiniti. Quelli che terminano con **_in** sono concatenati con *randomValue* per renderli univoci globali. 

**randomValue**: un valore generato dalla data/ora corrente quando si distribuisce il modello. Questo campo non si trova nel file di parametri e viene generato dal modello stesso.

**subscriptionId:** viene impostato automaticamente sulla sottoscrizione in cui viene distribuito il modello. Non si trova nel file di parametri perché viene impostato automaticamente.

**IoTHubName_in**: il nome dell'hub IoT di base, concatenato con randomValue per renderlo univoco globale.

**location**: l'area di Azure in cui si esegue la distribuzione, ad esempio "westus".

**consumer_group**: il gruppo di consumer impostato per i messaggi trasferiti attraverso l'endpoint di routing. Viene usato per filtrare i risultati in Analisi di flusso di Azure. È ad esempio possibile scegliere di recuperare tutti i contenuti nell'intero flusso oppure, nel caso di dati in arrivo con consumer_group impostato su **Contoso**, configurare un flusso di Analisi di flusso di Azure (e un report di Power BI) per mostrare solo queste voci. Questo campo viene usato nella seconda parte dell'esercitazione.

**sku_name**: il livello per l'hub IoT. Il valore deve essere S1 o superiore; un livello gratuito non funziona con questa esercitazione perché non consente molteplici endpoint.

**sku_units**: associato a **sku_name**, rappresenta il numero di unità dell'hub IoT che è possibile usare.

**d2c_partitions**: il numero di partizioni usate per il flusso di eventi.

**storageAccountName_in**: il nome dell'account di archiviazione da creare. I messaggi vengono instradati a un contenitore nell'account di archiviazione. Questo campo è concatenato con randomValue per renderlo univoco globale.

**storageContainerName**: il nome del contenitore in cui vengono archiviati i messaggi instradati nell'account di archiviazione.

**storage_endpoint**: il nome dell'endpoint dell'account di archiviazione usato per il routing dei messaggi.

**service_bus_namespace_in**: il nome dello spazio dei nomi del bus di servizio da creare. Questo valore viene concatenato con randomValue per renderlo univoco globale.

**service_bus_queue_in**: il nome della coda del bus di servizio usata per il routing dei messaggi. Questo valore viene concatenato con randomValue per renderlo univoco globale.

**AuthRules_sb_queue**: indica le regole di autorizzazione per la coda del bus di servizio, usate per recuperare la stringa di connessione per la coda.

### <a name="variables"></a>variables

Questi valori vengono usati nel modello e derivano prevalentemente dai parametri.

**queueAuthorizationRuleResourceId**: il valore ResourceId per la regola di autorizzazione per la coda del bus di servizio. ResourceId viene a sua volta usato per recuperare la stringa di connessione per la coda.

**iotHubName**: il nome dell'hub IoT dopo la concatenazione con randomValue. 

**StorageAccountName**: il nome dell'account di archiviazione dopo la concatenazione con randomValue. 

**service_bus_namespace**: lo spazio dei nomi dopo la concatenazione con randomValue.

**service_bus_queue**: il nome della coda del bus di servizio dopo la concatenazione con randomValue.

**sbVersion**: la versione dell'API del bus di servizio da usare. In questo caso è "2017-04-01".

### <a name="resources-storage-account-and-container"></a>Risorse: account di archiviazione e contenitore

La prima risorsa da creare è l'account di archiviazione, insieme al contenitore in cui vengono instradati i messaggi. Il contenitore è una risorsa all'interno dell'account di archiviazione. Include una clausola `dependsOn` per l'account di archiviazione, che richiede che l'account di archiviazione venga creato prima del contenitore.

La sezione è illustrata di seguito:

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "properties": {},
    "resources": [
        {
        "type": "blobServices/containers",
        "apiVersion": "2018-07-01",
        "name": "[concat('default/', parameters('storageContainerName'))]",
        "properties": {
            "publicAccess": "None"
            } ,
        "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
            ]
        }
    ]
}
```

### <a name="resources-service-bus-namespace-and-queue"></a>Risorse: spazio dei nomi e coda del bus di servizio

La seconda risorsa da creare è lo spazio dei nomi del bus di servizio, insieme alla coda corrispondente in cui vengono instradati i messaggi. Lo SKU è impostato su Standard. La versione dell'API viene recuperata dalle variabili. È inoltre impostato per attivare lo spazio dei nomi del bus di servizio quando distribuisce questa sezione (stato: Active). 

```json
{
    "type": "Microsoft.ServiceBus/namespaces",
    "comments": "The Sku should be 'Standard' for this tutorial.",
    "sku": {
        "name": "Standard",
        "tier": "Standard"
    },
    "name": "[variables('service_bus_namespace')]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "properties": {
        "provisioningState": "Succeeded",
        "metricId": "[concat('a4295411-5eff-4f81-b77e-276ab1ccda12:', variables('service_bus_namespace'))]",
        "serviceBusEndpoint": "[concat('https://', variables('service_bus_namespace'),'.servicebus.windows.net:443/')]",
        "status": "Active"
    },
    "dependsOn": []
}
```

Questa sezione crea la coda del bus di servizio. Questa parte dello script include una clausola `dependsOn` che assicura che lo spazio dei nomi venga creato prima della coda.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {},
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]"
    ]
}
```

### <a name="resources-iot-hub-and-message-routing"></a>Risorse: hub IoT e routing dei messaggi

Dopo aver creato l'account di archiviazione e la coda del bus di servizio, creare l'hub IoT per il routing dei messaggi al loro interno. Il modello di Resource Manager usa la clausola `dependsOn` per evitare che l'hub venga creato prima della creazione delle risorse del bus di servizio e dell'account di archiviazione. 

Ecco la prima parte della sezione dell'hub IoT. Questa parte del modello configura le dipendenze e inizia dalle proprietà.

```json
{
    "apiVersion": "2018-04-01",
    "type": "Microsoft.Devices/IotHubs",
    "name": "[variables('IoTHubName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ],
    "properties": {
        "eventHubEndpoints": {}
            "events": {
                "retentionTimeInDays": 1,
                "partitionCount": "[parameters('d2c_partitions')]"
                }
            },
```

La sezione successiva riguarda la configurazione del routing dei messaggi per l'hub IoT. La prima sezione è relativa agli endpoint. Questa parte del modello configura gli endpoint di routing per la coda del bus di servizio e l'account di archiviazione, incluse le stringhe di connessione.

Per creare la stringa di connessione per la coda, è necessario il valore di queueAuthorizationRulesResourcedId, recuperato inline. Per creare la stringa di connessione per l'account di archiviazione, recuperare la chiave di archiviazione primaria e quindi usarla nel formato per la stringa di connessione.

Durante la configurazione degli endpoint viene anche impostato il formato del BLOB su `AVRO` o `JSON`.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

 ```json
"routing": {
    "endpoints": {
        "serviceBusQueues": [
        {
            "connectionString": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]",
            "name": "[parameters('service_bus_queue_endpoint')]",
            "subscriptionId": "[parameters('subscriptionId')]", 
            "resourceGroup": "[resourceGroup().Name]"
        }
        ],
        "serviceBusTopics": [],
        "eventHubs": [],
        "storageContainers": [
            {
                "connectionString": 
                "[Concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]",
                "containerName": "[parameters('storageContainerName')]",
                "fileNameFormat": "{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}",
                "batchFrequencyInSeconds": 100,
                "maxChunkSizeInBytes": 104857600,
                "encoding": "avro",
                "name": "[parameters('storage_endpoint')]",
                "subscriptionId": "[parameters('subscriptionId')]",
                "resourceGroup": "[resourceGroup().Name]"
            }
        ]
    },
```

La sezione successiva riguarda le route dei messaggi verso gli endpoint. È necessario configurarne una per ogni endpoint, quindi una per la coda del bus di servizio e una per il contenitore dell'account di archiviazione.

Tenere presente che la condizione di query per i messaggi da instradare nell'account di archiviazione è `level="storage"`, mentre quella per i messaggi da instradare nella coda del bus di servizio è `level="critical"`.

```json
"routes": [
    {
        "name": "contosoStorageRoute",
        "source": "DeviceMessages",
        "condition": "level=\"storage\"",
        "endpointNames": [
            "[parameters('storage_endpoint')]"
            ],
        "isEnabled": true
    },
    {
        "name": "contosoSBQueueRoute",
        "source": "DeviceMessages",
        "condition": "level=\"critical\"",
        "endpointNames": [
            "[parameters('service_bus_queue_endpoint')]"
            ],
        "isEnabled": true
    }
],
```

Questo codice JSON mostra il resto della sezione dell'hub IoT, che contiene le informazioni predefinite e lo SKU per l'hub.

```json
            "fallbackRoute": {
                "name": "$fallback",
                "source": "DeviceMessages",
                "condition": "true",
                "endpointNames": [
                    "events"
                ],
                "isEnabled": true
            }
        },
        "storageEndpoints": {
            "$default": {
                "sasTtlAsIso8601": "PT1H",
                "connectionString": "",
                "containerName": ""
            }
        },
        "messagingEndpoints": {
            "fileNotifications": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        },
        "enableFileUploadNotifications": false,
        "cloudToDevice": {
            "maxDeliveryCount": 10,
            "defaultTtlAsIso8601": "PT1H",
            "feedback": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        }
    },
    "sku": {
        "name": "[parameters('sku_name')]",
        "capacity": "[parameters('sku_units')]"
    }
}
```

### <a name="resources-service-bus-queue-authorization-rules"></a>Risorse: regole di autorizzazione per la coda del bus di servizio

La regola di autorizzazione per la coda del bus di servizio viene usata per recuperare la stringa di connessione per la coda del bus di servizio. Include una clausola `dependsOn` per assicurare che non venga creata prima dello spazio dei nomi del bus di servizio e della coda del bus di servizio.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues/authorizationRules",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'), '/', parameters('AuthRules_sb_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {
        "rights": [
            "Send"
        ]
    },
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ]
},
```

### <a name="resources-consumer-group"></a>Risorse: Gruppo di consumer

In questa sezione viene creato il gruppo di consumer per i dati dell'hub IoT che verranno usati da Analisi di flusso di Azure nella seconda parte dell'esercitazione.

```json
{
    "type": "Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups",
    "name": "[concat(variables('iotHubName'), '/events/',parameters('consumer_group'))]",
    "apiVersion": "2018-04-01",
    "dependsOn": [
        "[concat('Microsoft.Devices/IotHubs/', variables('iotHubName'))]"
    ]
}
```

### <a name="resources-outputs"></a>Risorse: Output

Se si vuole inviare un valore allo script di distribuzione da visualizzare, usare una sezione di output. Questa parte del modello restituisce la stringa di connessione per la coda del bus di servizio. Non è necessario che venga restituito un valore. L'esempio viene incluso solo per illustrare come vengono restituiti i risultati allo script chiamante.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>Distribuire il modello di Resource Manager

Per distribuire il modello in Azure, caricarlo insieme al file di parametri in Azure Cloud Shell, quindi eseguire uno script. Aprire Azure Cloud Shell e accedere. In questo esempio viene usato PowerShell.

Per caricare i file, selezionare l'icona **Carica/Scarica file** sulla barra dei menu, quindi scegliere Carica.

![Barra dei menu di Cloud Shell con l'icona Carica/Scarica file evidenziata](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

Usare la finestra di Esplora file visualizzata per trovare i file nel disco locale e selezionarli, quindi scegliere **Apri**.

Dopo il caricamento dei file, viene visualizzata una finestra di dialogo dei risultati simile a quella dell'immagine seguente.

![Barra dei menu di Cloud Shell con l'icona Carica/Scarica file evidenziata](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

I file vengono caricati nella condivisione usata dall'istanza di Cloud Shell. 

Eseguire lo script per avviare la distribuzione. L'ultima riga dello script recupera la variabile da restituire, ossia la stringa di connessione della coda del bus di servizio.

Nello script vengono impostate queste variabili.

**$RGName** è il nome del gruppo di risorse in cui viene distribuito il modello. Questo campo viene creato prima della distribuzione del modello.

**$location** è l'area di Azure da usare per il modello, ad esempio "westus".

**deploymentname** è un nome assegnato alla distribuzione per recuperare il valore della variabile da restituire.

Ecco lo script di PowerShell. Copiare questo script di PowerShell e incollarlo nella finestra Cloud Shell, quindi premere INVIO per eseguirlo.

```powershell
$RGName="ContosoResources"
$location = "westus"
$deploymentname="contoso-routing"

# Remove the resource group if it already exists. 
#Remove-AzResourceGroup -name $RGName 
# Create the resource group.
New-AzResourceGroup -name $RGName -Location $location 

# Set a path to the parameter file. 
$parameterFile = "$HOME/template_iothub_parameters.json"
$templateFile = "$HOME/template_iothub.json"

# Deploy the template.
New-AzResourceGroupDeployment `
    -Name $deploymentname `
    -ResourceGroupName $RGName `
    -TemplateParameterFile $parameterFile `
    -TemplateFile $templateFile `
    -verbose

# Get the returning value of the connection string.
(Get-AzResourceGroupDeployment -ResourceGroupName $RGName -Name $deploymentname).Outputs.sbq_connectionString.value
```

Se si verificano errori dello script, è possibile modificarlo in locale, ricaricarlo in Cloud Shell ed eseguirlo di nuovo. Al termine dell'esecuzione dello script, continuare con il passaggio successivo.

## <a name="create-simulated-device"></a>Creare un dispositivo simulato

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Visualizzare il routing dei messaggi nel portale

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato tutte le risorse e le route dei messaggi, procedere con l'esercitazione successiva per scoprire come elaborare e visualizzare le informazioni sui messaggi instradati.

> [!div class="nextstepaction"]
> [Parte 2 - Visualizzare i risultati del routing dei messaggi](tutorial-routing-view-message-routing-results.md)

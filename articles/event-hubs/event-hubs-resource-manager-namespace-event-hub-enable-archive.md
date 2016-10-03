<properties
    pageTitle="Creare uno spazio dei nomi dell'hub eventi con Hub eventi e abilitare l'archivio usando un modello di Azure Resource Manager | Microsoft Azure"
    description="Creare uno spazio dei nomi dell'hub eventi con Hub eventi e abilitare l'archivio usando un modello di Azure Resource Manager"
    services="event-hubs"
    documentationCenter=".net"
    authors="ShubhaVijayasarathy"
    manager="timlt"
    editor=""/> 

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="09/14/2016"
    ms.author="ShubhaVijayasarathy"/> 

# Creare uno spazio dei nomi dell'hub eventi con Hub eventi e abilitare l'archivio usando un modello di Azure Resource Manager

Questo articolo illustra come usare un modello di Azure Resource Manager per creare uno spazio dei nomi dell'hub eventi con un Hub eventi e abilitare l'archivio in Hub eventi. Illustra inoltre le modalità di definizione delle risorse da distribuire e dei parametri specificati durante l'esecuzione della distribuzione. È possibile usare questo modello per la distribuzione o personalizzarlo in base alle esigenze.

Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Azure Resource Manager][].

Per altre informazioni su procedure e modelli sulle convenzioni di denominazione delle risorse di Azure, vedere [Azure Resources Naming Conventions][] \(Convenzioni di denominazione delle risorse Azure).

Per il modello completo, vedere [Event Hub and enable Archive template][] \(Hub eventi e abilitazione del modello dell'archivio) su GitHub.

>[AZURE.NOTE]
Per verificare gli ultimi modelli, visitare la raccolta [Modelli di avvio rapido di Azure][] e cercare Hub eventi.

## Cosa viene distribuito?

Questo modello consente di distribuire uno spazio dei nomi dell'hub eventi con un Hub eventi e abilita l'archivio.

[Hub eventi](../event-hubs/event-hubs-what-is-event-hubs.md) è un servizio di elaborazione di eventi che viene usato per fornire eventi e dati di telemetria in entrata in Azure su larga scala, con bassa latenza ed elevata affidabilità. L'archivio di Hub eventi consentirà di distribuire automaticamente il flusso di dati dell'Hub di eventi nell'archivio BLOB di Azure scelto, entro un intervallo temporale o di dimensioni specificato.

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuzione in Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-archive%2Fazuredeploy.json)

## Parametri

Gestione risorse di Azure permette di definire i parametri per i valori da specificare durante la distribuzione del modello. Il modello include una sezione denominata `Parameters` che contiene tutti i valori dei parametri. È necessario definire un parametro per i valori che variano in base al progetto distribuito o all'ambiente in cui viene distribuito il progetto. Non definire i parametri per i valori che rimangono invariati. Ogni valore di parametro nel modello viene usato per definire le risorse distribuite.

Il modello definisce i parametri seguenti.

### eventHubNamespaceName

Nome dello spazio dei nomi dell'hub eventi da creare.

```
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### eventHubName

Nome dell'hub eventi creato nello spazio dei nomi dell'hub eventi.

```
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the Event Hub"
    }
}
```

### messageRetentionInDays

Il numero di giorni che si desidera che i messaggi vengano mantenuti in Hub eventi.

```
"messageRetentionInDays":{
	"type":"int",
	"defaultValue": 1,
	"minValue":"1",
	"maxValue":"7",
	"metadata":{
	   "description":"How long to retain the data in Event Hub"
	 }
 }
```

### partitionCount

Il numero di partizioni desiderato nell'Hub eventi.

```
"partitionCount":{
	"type":"int",
	"defaultValue":2,
	"minValue":2,
	"maxValue":32,
	"metadata":{
		"description":"Number of partitions chosen"
	}
 }
```

### archiveEnabled

Consente di abilitare l'archivio nell'Hub eventi.

```
"archiveEnabled":{
	"type":"string",
	"defaultValue":"true",
	"allowedValues": [
	"false",
	"true"],
	"metadata":{
		"description":"Enable or disable the Archive for your Event Hub"
	}
 }
```
### archiveEncodingFormat

Il formato di codifica specificato per serializzare i dati dell'evento.

```
"archiveEncodingFormat":{
	"type":"string",
	"defaultValue":"Avro",
	"allowedValues":[
	"Avro"],
	"metadata":{
		"description":"The encoding format Archive serializes the EventData"
	}
}
```

### archiveTime

L'intervallo temporale in cui l'archivio inizia l'archiviazione dei dati nell'archivio BLOB di Azure.

```
"archiveTime":{
	"type":"int",
	"defaultValue":300,
	"minValue":60,
	"maxValue":900,
	"metadata":{
		 "description":"the time window in seconds for the archival"
	}
}
```

### archiveSize

L'intervallo di dimensioni in cui l'archivio inizia l'archiviazione dei dati nell'archivio BLOB di Azure.

```
"archiveSize":{
	"type":"int",
	"defaultValue":314572800,
	"minValue":10485760,
	"maxValue":524288000,
	"metadata":{
		"description":"the size window in bytes for archival"
	}
}
```

### destinationStorageAccountResourceId

L'archivio richiede un ID risorsa dell'account di archiviazione per abilitare l'archivio verso l'archiviazione di Azure scelta.

```
 "destinationStorageAccountResourceId":{
	"type":"string",
	"metadata":{
		"description":"Your existing storage Account resource id where you want the blobs be archived"
	}
 }
```

### blobContainerName

Il contenitore BLOB in cui si desidera archiviare i dati dell'evento.

```
 "blobContainerName":{
	"type":"string",
	"metadata":{
		"description":"Your existing storage Container that you want the blobs archived in"
	}
}
```


### apiVersion

Versione API del modello.

```
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2015-08-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

## Risorse da distribuire

Crea uno spazio dei nomi di tipo **EventHubs** con Hub eventi e abilita l'archivio.

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]",
				  "MessageRetentionInDays":"[parameters('messageRetentionInDays')]",
				  "PartitionCount":"[parameters('partitionCount')]",
				  "ArchiveDescription":{
						"enabled":"[parameters('archiveEnabled')]",
						"encoding":"[parameters('archiveEncodingFormat')]",
						"intervalInSeconds":"[parameters('archiveTime')]",
						"sizeLimitInBytes":"[parameters('archiveSize')]",
						"destination":{
							"name":"EventHubArchive.AzureBlockBlob",
							"properties":{
								"StorageAccountResourceId":"[parameters('destinationStorageAccountResourceId')]",
								"BlobContainer":"[parameters('blobContainerName')]"
							}
						} 
				  }
				  
               }
               
            }
         ]
      }
   ]
```

## Comandi per eseguire la distribuzione

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json
```

## Interfaccia della riga di comando di Azure

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json][]
```

## Passaggi successivi

Dopo aver creato e distribuito le risorse con Azure Resource Manager, è possibile imparare a gestire queste risorse. Leggere gli articoli seguenti:

- [Gestire le risorse del bus di Hub eventi con Service Bus Explorer](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Creazione di modelli di Azure Resource Manager]: ../resource-group-authoring-templates.md
  [Modelli di avvio rapido di Azure]: https://azure.microsoft.com/documentation/templates/?term=event+hubs
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Event Hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-eventhubs-create-namespace-and-enable-archive/
  [Azure Resources Naming Conventions]: https://azure.microsoft.com/documentation/articles/guidance-naming-conventions/
  [Event Hub and enable Archive template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-archive

<!---HONumber=AcomDC_0921_2016-->
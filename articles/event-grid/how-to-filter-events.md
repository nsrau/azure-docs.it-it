---
title: Come filtrare gli eventi per Griglia di eventi di Azure
description: Questo articolo illustra come creare sottoscrizioni di Griglia di eventi di Azure che applicano filtri agli eventi.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: tomfitz
ms.openlocfilehash: fd0b2bda91ecb9b717f4cfe366c45bc95b21fd8e
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277563"
---
# <a name="filter-events-for-event-grid"></a>Filtrare gli eventi per Griglia di eventi

Questo articolo illustra come filtrare gli eventi quando si crea una sottoscrizione di Griglia di eventi. Per altre informazioni sulle opzioni per filtrare gli eventi, vedere [Informazioni sui filtri eventi per le sottoscrizioni di Griglia di eventi](event-filtering.md).

## <a name="filter-by-event-type"></a>Filtrare in base al tipo di evento

Quando si crea una sottoscrizione di Griglia di eventi, è possibile specificare quali [tipi di evento](event-schema.md) inviare all'endpoint. Gli esempi in questa sezione creano sottoscrizioni di eventi per un gruppo di risorse, ma limitano gli eventi che vengono inviati a `Microsoft.Resources.ResourceWriteFailure` e `Microsoft.Resources.ResourceWriteSuccess`. Se è necessaria maggiore flessibilità quando si filtrano gli eventi in base ai tipi di evento, vedere [Filtrare in base a campi dati e operatori avanzati](#filter-by-advanced-operators-and-data-fields).

Per PowerShell, usare il parametro `-IncludedEventType` quando si crea la sottoscrizione.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzureRmEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

Per l'interfaccia della riga di comando di Azure, usare il parametro `--included-event-types`. L'esempio seguente usa l'interfaccia della riga di comando di Azure in una shell Bash:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

Per un modello di Resource Manager, usare la proprietà `includedEventTypes`.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

## <a name="filter-by-subject"></a>Filtrare in base all'oggetto

È possibile filtrare gli eventi in base all'oggetto presente nei relativi dati. È possibile specificare un valore per cui identificare una corrispondenza in base all'inizio o alla fine dell'oggetto. Se è necessaria maggiore flessibilità quando si filtrano gli eventi in base all'oggetto, vedere [Filtrare in base a campi dati e operatori avanzati](#filter-by-advanced-operators-and-data-fields).

Nell'esempio seguente di PowerShell si crea una sottoscrizione di eventi che applica un filtro in base all'inizio dell'oggetto. Si usa il parametro `-SubjectBeginsWith` per limitare gli eventi a quelli relativi a una risorsa specifica. Si passa l'ID risorsa di un gruppo di sicurezza di rete.

```powershell
$resourceId = (Get-AzureRmResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzureRmEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

L'esempio successivo di PowerShell crea una sottoscrizione per una risorsa di archiviazione BLOB e limita gli eventi a quelli con un oggetto che termina con `.jpg`.

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzureRmEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

Nell'esempio seguente dell'interfaccia della riga di comando di Azure si crea una sottoscrizione di eventi che applica un filtro in base all'inizio dell'oggetto. Si usa il parametro `--subject-begins-with` per limitare gli eventi a quelli relativi a una risorsa specifica. Si passa l'ID risorsa di un gruppo di sicurezza di rete.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

L'esempio successivo dell'interfaccia della riga di comando di Azure crea una sottoscrizione per una risorsa di archiviazione BLOB e limita gli eventi a quelli con un oggetto che termina con `.jpg`.

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

Nell'esempio seguente di un modello di Resource Manager si crea una sottoscrizione di eventi che applica un filtro in base all'inizio dell'oggetto. Si usa la proprietà `subjectBeginsWith` per limitare gli eventi a quelli relativi a una risorsa specifica. Si passa l'ID risorsa di un gruppo di sicurezza di rete.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

L'esempio successivo di un modello di Resource Manager crea una sottoscrizione per una risorsa di archiviazione BLOB e limita gli eventi a quelli con un oggetto che termina con `.jpg`.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

## <a name="filter-by-operators-and-data"></a>Filtrare in base a operatori e dati

Per una maggiore flessibilità nell'applicazione di filtri è possibile usare gli operatori e le proprietà dei dati per filtrare gli eventi.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

### <a name="subscribe-with-advanced-filters"></a>Creare una sottoscrizione con filtri avanzati

Per altre informazioni sugli operatori e sulle chiavi che è possibile usare per i filtri avanzati, vedere [Filtri avanzati](event-filtering.md#advanced-filtering).

Questi esempi creano un argomento personalizzato. Sottoscrivono l'argomento personalizzato e filtrano in base a un valore nell'oggetto dati. Gli eventi che hanno la proprietà color impostata sul blue, red o green vengono inviati alla sottoscrizione.

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "<yyyy-mm-dd>"
```

Si noti che per la sottoscrizione è impostata una [data di scadenza](concepts.md#event-subscription-expiration).

Per PowerShell, usare:

```azurepowershell-interactive
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzureRmResourceGroup -Name gridResourceGroup -Location eastus2
New-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="test-filter"></a>Testare il filtro

Per testare il filtro, inviare un evento con il campo color impostato su green. Poiché il verde è uno dei valori nel filtro, l'evento viene recapitato all'endpoint.

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Per PowerShell, usare:

```azurepowershell-interactive
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Per testare uno scenario in cui non viene inviato l'evento, inviare un evento con il campo color impostato su yellow. Poiché yellow non è uno dei valori specificati nella sottoscrizione, l'evento non viene inviato alla sottoscrizione.

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```
Per PowerShell, usare:

```azurepowershell-interactive
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul monitoraggio dei recapiti degli eventi, vedere [Monitorare il recapito dei messaggi di Griglia di eventi di Azure](monitor-event-delivery.md).
* Per altre informazioni sulla chiave di autenticazione, vedere [Sicurezza e autenticazione di Griglia di eventi](security-authentication.md).
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).

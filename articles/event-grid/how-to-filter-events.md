---
title: Come filtrare gli eventi per Griglia di eventi di Azure
description: Questo articolo illustra come creare sottoscrizioni di Griglia di eventi di Azure che applicano filtri agli eventi.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: tomfitz
ms.openlocfilehash: 8bf7ac9daf928c35a3d6efcac528d3372fa87c8a
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50252043"
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

Per usare filtri avanzati, è necessario installare un'estensione di anteprima per l'interfaccia della riga di comando di Azure. È possibile usare [CloudShell](/azure/cloud-shell/quickstart) o installare l'interfaccia della riga di comando di Azure in locale.

### <a name="install-extension"></a>Installare l'estensione

In CloudShell:

* Se si è già installata l'estensione, aggiornarla con il comando `az extension update -n eventgrid`
* Se non si è ancora installata l'estensione, installarla con il comando `az extension add -n eventgrid`

Per un'installazione locale:

1. Disinstallare l'interfaccia della riga di comando di Azure in locale.
1. Installare la [versione più recente](/cli/azure/install-azure-cli) dell'interfaccia della riga di comando di Azure.
1. Avviare la finestra di comando.
1. Disinstallare le versioni precedenti dell'estensione con il comando `az extension remove -n eventgrid`
1. Installare l'estensione con il comando `az extension add -n eventgrid`

È ora possibile usare i filtri avanzati.

### <a name="subscribe-with-advanced-filters"></a>Creare una sottoscrizione con filtri avanzati

Per altre informazioni sugli operatori e sulle chiavi che è possibile usare per i filtri avanzati, vedere [Filtri avanzati](event-filtering.md#advanced-filtering).

L'esempio seguente crea un argomento personalizzato. Sottoscrive l'argomento personalizzato e filtra in base a un valore nell'oggetto dati. Gli eventi che hanno la proprietà color impostata sul blue, red o green vengono inviati alla sottoscrizione.

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
  --expiration-date "2018-11-30"
```

Si noti che per la sottoscrizione è impostata una data di scadenza. La sottoscrizione dell'evento scade automaticamente dopo tale data. Impostare una scadenza per le sottoscrizioni di eventi necessarie solo per un periodo di tempo limitato.

### <a name="test-filter"></a>Testare il filtro

Per testare il filtro, inviare un evento con il campo color impostato su green.

```azurecli-interactive
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

L'evento viene inviato all'endpoint.

Per testare uno scenario in cui non viene inviato l'evento, inviare un evento con il campo color impostato su yellow.

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Poiché yellow non è uno dei valori specificati nella sottoscrizione, l'evento non viene inviato alla sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul monitoraggio dei recapiti degli eventi, vedere [Monitorare il recapito dei messaggi di Griglia di eventi di Azure](monitor-event-delivery.md).
* Per altre informazioni sulla chiave di autenticazione, vedere [Sicurezza e autenticazione di Griglia di eventi](security-authentication.md).
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).

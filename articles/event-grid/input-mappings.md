---
title: Eseguire il mapping di campi personalizzati allo schema di Griglia di eventi di Azure
description: Questo articolo descrive come convertire uno schema personalizzato nello schema di Griglia di eventi di Azure.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: spelluru
ms.openlocfilehash: 2c768790421ac74fa483877f09e66b1b4b5d5f35
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54476615"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Eseguire il mapping di campi personalizzati allo schema di Griglia di eventi

Se i dati dell'evento non corrispondono allo [schema di Griglia di eventi](event-schema.md) previsto, è comunque possibile usare Griglia di eventi per instradare l'evento ai sottoscrittori. Questo articolo descrive come eseguire il mapping di uno schema personalizzato allo schema di Griglia di eventi.

## <a name="install-preview-feature"></a>Installare la funzionalità di anteprima

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Schema di eventi di origine

Si supponga che un'applicazione invii gli eventi nel formato seguente:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Anche se questo formato non corrisponde allo schema previsto, Griglia di eventi consente di eseguire il mapping dei campi allo schema. In alternativa è possibile ricevere i valori nello schema di origine.

## <a name="create-custom-topic-with-mapped-fields"></a>Creare un argomento personalizzato con i campi di cui è stato eseguito il mapping

Quando si crea un argomento personalizzato, specificare come eseguire il mapping dei campi dall'evento di origine allo schema di griglia di eventi. Per personalizzare il mapping, si usano tre valori:

* Il valore relativo allo **schema di input** specifica il tipo di schema. Le opzioni disponibili sono lo schema CloudEvents, lo schema evento personalizzato e lo schema griglia di eventi. Il valore predefinito è lo schema griglia di eventi. Quando si crea un mapping personalizzato tra lo schema personalizzato e lo schema griglia di eventi, usare lo schema evento personalizzato. Quando gli eventi si trovano nello schema CloudEvents, usare lo schema CloudEvents.

* La proprietà relativa ai **valori predefiniti di mapping** specifica i valori predefiniti per i campi nello schema griglia di eventi. È possibile impostare i valori predefiniti per `subject`, `eventtype` e `dataversion`. In genere, questo parametro si usa quando lo schema personalizzato non include un campo corrispondente a uno di questi tre campi. È ad esempio possibile specificare che la versione di dati sia sempre impostata su **1.0**.

* Il valore relativo ai **campi di mapping** esegue il mapping dei campi dallo schema in uso allo schema griglia di eventi. I valori vengono specificati in coppie chiave/valore separate da spazi. Per il nome della chiave usare il nome del campo della griglia di eventi. Per il valore usare il nome del campo personalizzato. È possibile usare i nomi delle chiavi `id`, `topic`, `eventtime`, `subject`, `eventtype` e `dataversion`.

Per creare un argomento personalizzato con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema \
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

Per PowerShell, usare:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName myResourceGroup `
  -Name demotopic `
  -Location eastus2 `
  -InputSchema CustomEventSchema `
  -InputMappingField @{eventType="myEventTypeField"} `
  -InputMappingDefaultValue @{subject="DefaultSubject"; dataVersion="1.0" }
```

## <a name="subscribe-to-event-grid-topic"></a>Eseguire la sottoscrizione all'argomento di griglia di eventi

Quando si esegue la sottoscrizione all'argomento personalizzato, specificare lo schema da usare per la ricezione di eventi. È necessario specificare lo schema CloudEvents, lo schema evento personalizzato o lo schema griglia di eventi. Il valore predefinito è lo schema griglia di eventi.

L'esempio seguente indica come eseguire la sottoscrizione a un argomento di griglia di eventi e usa lo schema griglia di eventi. Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

L'esempio successivo usa lo schema di input dell'evento:

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

L'esempio seguente indica come eseguire la sottoscrizione a un argomento di griglia di eventi e usa lo schema griglia di eventi. Per PowerShell, usare:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

L'esempio successivo usa lo schema di input dell'evento:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>Pubblicare l'evento nell'argomento

A questo punto, si è pronti a inviare un evento all'argomento personalizzato e visualizzare il risultato del mapping. Lo script seguente invia un evento nello [schema di esempio](#original-event-schema):

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

event='[ { "myEventTypeField":"Created", "resource":"Users/example/Messages/1000", "resourceData":{"someDataField1":"SomeDataFieldValue"} } ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

Per PowerShell, usare:

```azurepowershell-interactive
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demotopic).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName myResourceGroup -Name demotopic

$htbody = @{
    myEventTypeField="Created"
    resource="Users/example/Messages/1000"
    resourceData= @{
        someDataField1="SomeDataFieldValue"
    }
}

$body = "["+(ConvertTo-Json $htbody)+"]"
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

A questo punto, esaminare l'endpoint del WebHook. Le due sottoscrizioni hanno recapitato gli eventi in schemi diversi.

La prima sottoscrizione ha usato lo schema della griglia di eventi. Il formato dell'evento recapitato è:

```json
{
  "id": "aa5b8e2a-1235-4032-be8f-5223395b9eae",
  "eventTime": "2018-11-07T23:59:14.7997564Z",
  "eventType": "Created",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "subject": "DefaultSubject",
  "data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": {
      "someDataField1": "SomeDataFieldValue"
    }
  }
}
```

Questi campi contengono i mapping da un argomento personalizzato. Viene eseguito il mapping di **myEventTypeField** a **EventType**. Vengono usati i valori predefiniti di **DataVersion** e **Subject**. L'oggetto **Data** contiene i campi dello schema di eventi di origine.

La seconda sottoscrizione ha usato lo schema di eventi di input. Il formato dell'evento recapitato è:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

Si noti che i campi di origine sono stati recapitati.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul recapito di eventi e sui nuovi tentativi, vedere [Recapito di messaggi di Griglia di eventi e nuovi tentativi](delivery-and-retry.md).
* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per iniziare rapidamente a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).

---
title: Eseguire il mapping di campi personalizzati allo schema di Griglia di eventi di Azure
description: Questo articolo descrive come convertire uno schema personalizzato nello schema di Griglia di eventi di Azure.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: tomfitz
ms.openlocfilehash: 32f93f383ec4044afb0696fcef1705c9ed65d673
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301968"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Eseguire il mapping di campi personalizzati allo schema di Griglia di eventi

Se i dati dell'evento non corrispondono allo [schema di Griglia di eventi](event-schema.md) previsto, è comunque possibile usare Griglia di eventi per instradare l'evento ai sottoscrittori. Questo articolo descrive come eseguire il mapping di uno schema personalizzato allo schema di Griglia di eventi.

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

Quando si crea un argomento personalizzato, specificare come eseguire il mapping dei campi dall'evento di origine allo schema di griglia di eventi. Per personalizzare il mapping, si usano tre proprietà:

* Il parametro `--input-schema` specifica il tipo di schema. Le opzioni disponibili sono *cloudeventv01schema*, *customeventschema* ed *eventgridschema*. Il valore predefinito è eventgridschema. Quando si crea un mapping personalizzato tra lo schema personalizzato e lo schema di Griglia di eventi, usare customeventschema. Quando gli eventi si trovano nello schema CloudEvents, usare cloudeventv01schema.

* Il parametro `--input-mapping-default-values` specifica i valori predefiniti per i campi nello schema di Griglia di eventi. È possibile impostare i valori predefiniti per *subject*, *eventtype* e *dataversion*. In genere, questo parametro si usa quando lo schema personalizzato non include un campo corrispondente a uno di questi tre campi. Ad esempio, è possibile specificare che dataversion sia sempre impostato su **1.0**.

* Il parametro `--input-mapping-fields` esegue il mapping dei campi dello schema personalizzato allo schema di griglia di eventi. I valori vengono specificati in coppie chiave/valore separate da spazi. Per il nome della chiave usare il nome del campo della griglia di eventi. Per il valore usare il nome del campo personalizzato. È possibile usare i nomi di chiavi per *id*, *topic*, *eventtime*, *subject*, *eventtype* e *dataversion*.

Nell'esempio seguente si crea un argomento personalizzato con alcuni campi predefiniti e alcuni di cui è stato eseguito il mapping:

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

## <a name="subscribe-to-event-grid-topic"></a>Eseguire la sottoscrizione all'argomento di griglia di eventi

Quando si esegue la sottoscrizione all'argomento personalizzato, specificare lo schema da usare per la ricezione di eventi. Usare il parametro `--event-delivery-schema` e impostarlo su *cloudeventv01schema*, *eventgridschema* o *inputeventschema*. Il valore predefinito è eventgridschema.

Gli esempi in questa sezione usano un'archiviazione code per il gestore dell'evento. Per altre informazioni, vedere [Instradare eventi personalizzati ad Archiviazione code di Azure](custom-event-to-queue-storage.md).

Nell'esempio seguente si esegue la sottoscrizione a un argomento di griglia di eventi e si usa lo schema predefinito della griglia di eventi:

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub1 \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

L'esempio successivo usa lo schema di input dell'evento:

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub2 \
  --event-delivery-schema inputeventschema \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

## <a name="publish-event-to-topic"></a>Pubblicare l'evento nell'argomento

A questo punto si è pronti a inviare un evento all'argomento personalizzato e visualizzare il risultato del mapping. Lo script seguente invia un evento nello [schema di esempio](#original-event-schema):

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/mapeventfields.json)'")

curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

A questo punto esaminare Archiviazione code. Le due sottoscrizioni hanno recapitato gli eventi in schemi diversi.

La prima sottoscrizione ha usato lo schema della griglia di eventi. Il formato dell'evento recapitato è:

```json
{
  "Id": "016b3d68-881f-4ea3-8a9c-ed9246582abe",
  "EventTime": "2018-05-01T20:00:25.2606434Z",
  "EventType": "Created",
  "DataVersion": "1.0",
  "MetadataVersion": "1",
  "Topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "Subject": "DefaultSubject",
  "Data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": { "someDataField1": "SomeDataFieldValue" } 
  }
}
```

Questi campi contengono i mapping da un argomento personalizzato. Viene eseguito il mapping di **myEventTypeField** a **EventType**. Vengono usati i valori predefiniti di **DataVersion** e **Subject**. L'oggetto **Data** contiene i campi dello schema di eventi di origine.

La seconda sottoscrizione ha usato lo schema di eventi di input. Il formato dell'evento recapitato è:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": { "someDataField1": "SomeDataFieldValue" }
}
```

Si noti che i campi di origine sono stati recapitati.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul recapito di eventi e sui nuovi tentativi, vedere [Recapito di messaggi di Griglia di eventi e nuovi tentativi](delivery-and-retry.md).
* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per iniziare rapidamente a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).

---
title: Inserire i dati di telemetria dall'hub IoT
titleSuffix: Azure Digital Twins
description: Vedere come inserire i messaggi di telemetria del dispositivo dall'hub Internet.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 8/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8d720d77773e506a13f176723ab4583613f1e625
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291756"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Inserire dati di telemetria dell'hub Internet in dispositivi gemelli digitali di Azure

I dispositivi gemelli digitali di Azure sono basati sui dati dei dispositivi e di altre origini. Un'origine comune per i dati dei dispositivi da usare nei dispositivi gemelli digitali di Azure è l' [Hub](../iot-hub/about-iot-hub.md).

Il processo di inserimento dei dati nei dispositivi gemelli digitali di Azure consiste nel configurare una risorsa di calcolo esterna, ad esempio una [funzione di Azure](../azure-functions/functions-overview.md), che riceve i dati e usa le [API DigitalTwins](how-to-use-apis-sdks.md) per impostare le proprietà o generare eventi di telemetria sui dispositivi [gemelli digitali](concepts-twins-graph.md) di conseguenza. 

Questo documento illustra il processo di scrittura di una funzione di Azure in grado di inserire dati di telemetria dall'hub Internet.

## <a name="prerequisites"></a>Prerequisiti

Prima di continuare con questo esempio, è necessario configurare le risorse seguenti come prerequisiti:
* **Un hub**Internet. Per istruzioni, vedere la sezione *creare un hub* per gli Internet in [questa Guida introduttiva sull'hub](../iot-hub/quickstart-send-telemetry-cli.md).
* **Funzione di Azure** con le autorizzazioni corrette per chiamare l'istanza di Digital Twin. Per istruzioni, vedere [*procedura: configurare una funzione di Azure per l'elaborazione dei dati*](how-to-create-azure-function.md). 
* **Un'istanza di gemelli digitale di Azure** che riceverà i dati di telemetria del dispositivo. Per istruzioni, vedere [*procedura: configurare un'istanza di dispositivi gemelli digitali di Azure e l'autenticazione*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Scenario di telemetria di esempio

Questa procedura illustra come inviare messaggi dall'hub Internet ai dispositivi gemelli di Azure, usando una funzione di Azure. Sono disponibili molte possibili configurazioni e strategie di corrispondenza che è possibile usare per questa operazione, ma l'esempio per questo articolo contiene le parti seguenti:
* Un dispositivo a termometro nell'hub Internet delle cose, con un ID dispositivo noto.
* Un dispositivo gemello digitale per rappresentare il dispositivo, con un ID corrispondente

> [!NOTE]
> Questo esempio usa una corrispondenza con ID semplice tra l'ID del dispositivo e un ID del gemello digitale corrispondente, ma è possibile fornire mapping più sofisticati dal dispositivo al relativo gemello, ad esempio con una tabella di mapping.

Ogni volta che un evento di telemetria della temperatura viene inviato dal dispositivo termometro, la proprietà *temperature* del gemello digitale dovrebbe aggiornare. Questo scenario è illustrato in un diagramma seguente:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Diagramma che mostra un diagramma di flusso. Nel grafico un dispositivo hub Internet di Azure invia i dati di telemetria della temperatura attraverso l'hub delle cose a una funzione di Azure, che aggiorna una proprietà di temperatura in un gemello in Azure Digital gemelli." border="false":::

## <a name="add-a-model-and-twin"></a>Aggiungere un modello e un dispositivo gemello

È necessario un dispositivo gemello per l'aggiornamento con le informazioni sull'hub.

Il modello ha un aspetto simile al seguente:
```JSON
{
  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "contents": [
    {
      "@type": "Property",
      "name": "Temperature",
      "schema": "double"
    }
  ]
}
```

Per **caricare il modello nell'istanza di dispositivi gemelli**, aprire l'interfaccia della riga di comando di Azure ed eseguire il comando seguente:

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

Sarà quindi necessario **creare un gemello usando questo modello**. Usare il comando seguente per creare un dispositivo gemello e impostare 0,0 come valore di temperatura iniziale.

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

L'output di un comando di creazione gemello riuscito dovrebbe essere simile al seguente:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-an-azure-function"></a>Creare una funzione di Azure

Questa sezione usa gli stessi passaggi di avvio di Visual Studio e lo scheletro della funzione di Azure da [*procedura: configurare una funzione di Azure per l'elaborazione dei dati*](how-to-create-azure-function.md). Lo scheletro gestisce l'autenticazione e crea un client del servizio, pronto per l'elaborazione dei dati e per chiamare le API di Azure Digital gemelli in risposta. 

Nei passaggi seguenti si aggiungerà un codice specifico per l'elaborazione degli eventi di telemetria dell'it dall'hub Internet.  

### <a name="add-telemetry-processing"></a>Aggiungi elaborazione dati di telemetria
    
Gli eventi di telemetria provengono dal dispositivo sotto forma di messaggi. Il primo passaggio nell'aggiunta del codice di elaborazione della telemetria è l'estrazione della parte pertinente del messaggio del dispositivo dall'evento di griglia di eventi. 

Dispositivi diversi possono strutturare i messaggi in modo diverso, quindi il codice per **questo passaggio dipende dal dispositivo connesso.** 

Il codice seguente illustra un esempio per un dispositivo semplice che invia dati di telemetria come JSON. Questo esempio è completamente esplorato in [*esercitazione: connettere una soluzione end-to-end*](./tutorial-end-to-end.md). Il codice seguente consente di trovare l'ID del dispositivo che ha inviato il messaggio, nonché il valore della temperatura.

```csharp
JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
var temperature = deviceMessage["body"]["Temperature"];
```

L'esempio di codice successivo accetta l'ID e il valore della temperatura e li usa per "applicare patch" (effettuare gli aggiornamenti) al dispositivo gemello.

```csharp
//Update twin using device temperature
var uou = new UpdateOperationsUtility();
uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
...
```

### <a name="update-your-azure-function-code"></a>Aggiornare il codice della funzione di Azure

Ora che si è compreso il codice degli esempi precedenti, aprire Visual Studio e sostituire il codice della funzione di Azure con questo codice di esempio.

```csharp
using System;
using System.Net.Http;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace IotHubtoTwins
{
    public class IoTHubtoTwins
    {
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("IoTHubtoTwins")]
        public async void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");

            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(
                    new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions 
                    { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
            
                if (eventGridEvent != null && eventGridEvent.Data != null)
                {
                    log.LogInformation(eventGridEvent.Data.ToString());

                    // Reading deviceId and temperature for IoT Hub JSON
                    JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                    string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
                    var temperature = deviceMessage["body"]["Temperature"];
                    
                    log.LogInformation($"Device:{deviceId} Temperature is:{temperature}");

                    //Update twin using device temperature
                    var uou = new UpdateOperationsUtility();
                    uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
                    await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
                }
            }
            catch (Exception e)
            {
                log.LogError($"Error in ingest function: {e.Message}");
            }
        }
    }
}
```

## <a name="connect-your-function-to-iot-hub"></a>Connettere la funzione all'hub Internet

1. Configurare una destinazione evento per i dati dell'hub. Nella [portale di Azure](https://portal.azure.com/)passare all'istanza dell'hub Internet. In **eventi**creare una sottoscrizione per la funzione di Azure. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Screenshot del portale di Azure che mostra l'aggiunta di una sottoscrizione di eventi.":::

2. Nella pagina **Crea sottoscrizione evento** compilare i campi come indicato di seguito:
    1. In **nome**assegnare un nome alla sottoscrizione come si desidera.
    2. In **schema evento**scegliere **schema griglia di eventi**.
    3. In **nome argomento di sistema**scegliere un nome univoco.
    4. In **tipi di evento**scegliere **telemetria del dispositivo** come tipo di evento in base a cui filtrare.
    5. In **Dettagli endpoint**selezionare la funzione di Azure come endpoint.

    :::image type="content" source="media/how-to-ingest-iot-hub-data/event-subscription-2.png" alt-text="Screenshot del portale di Azure che mostra i dettagli della sottoscrizione di eventi":::

## <a name="send-simulated-iot-data"></a>Inviare dati dell'Internet delle cose simulati

Per testare la nuova funzione di ingresso, usare il simulatore di dispositivi da [*esercitazione: connettere una soluzione end-to-end*](./tutorial-end-to-end.md). Questa esercitazione è basata su un progetto di esempio scritto in C#. Il codice di esempio è disponibile qui: esempi di dispositivi [gemelli digitali di Azure](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples). Si userà il progetto **DeviceSimulator** in tale repository.

Nell'esercitazione end-to-end completare i passaggi seguenti:
1. [*Registrare il dispositivo simulato con l'hub IoT*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*Configurare ed eseguire la simulazione*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Convalidare i risultati

Quando si esegue il simulatore di dispositivi sopra riportato, il valore della temperatura del gemello digitale verrà modificato. Nell'interfaccia della riga di comando di Azure eseguire il comando seguente per visualizzare il valore della temperatura.

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

L'output dovrebbe contenere un valore di temperatura analogo al seguente:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

Per visualizzare la modifica del valore, eseguire ripetutamente il comando di query precedente.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sui dati in ingresso e in uscita con i dispositivi gemelli digitali di Azure:
* [*Concetti: integrazione con altri servizi*](concepts-integration.md)

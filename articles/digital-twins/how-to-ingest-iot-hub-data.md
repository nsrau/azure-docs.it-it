---
title: Inserire i dati di telemetria dall'hub IoT
titleSuffix: Azure Digital Twins
description: Vedere come inserire i messaggi di telemetria del dispositivo dall'hub Internet.
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7c73f007f85a963a09de4e05222082fd52f784c0
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131566"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Inserire dati di telemetria dell'hub Internet in dispositivi gemelli digitali di Azure

I dispositivi gemelli digitali di Azure sono basati sui dati dei dispositivi e di altre origini. Un'origine comune per i dati dei dispositivi da usare nei dispositivi gemelli digitali di Azure è l' [Hub](../iot-hub/about-iot-hub.md).

Durante l'anteprima, il processo di inserimento dei dati nei dispositivi gemelli digitali di Azure consiste nel configurare una risorsa di calcolo esterna, ad esempio una [funzione di Azure](../azure-functions/functions-overview.md), che riceve i dati e usa le [API DigitalTwins](how-to-use-apis-sdks.md) per impostare le proprietà o generare eventi di telemetria sui dispositivi [gemelli digitali](concepts-twins-graph.md) di conseguenza. 

Questo documento illustra il processo di scrittura di una funzione di Azure in grado di inserire dati di telemetria dall'hub Internet.

## <a name="example-telemetry-scenario"></a>Scenario di telemetria di esempio

Questa procedura illustra come inviare messaggi dall'hub Internet ai dispositivi gemelli di Azure, usando una funzione di Azure. Sono disponibili molte possibili configurazioni e strategie di corrispondenza che è possibile usare per questa operazione, ma l'esempio per questo articolo contiene le parti seguenti:
* Un dispositivo a termometro nell'hub Internet delle cose, con un ID dispositivo noto.
* Un dispositivo gemello digitale per rappresentare il dispositivo, con un ID corrispondente
* Un dispositivo gemello digitale che rappresenta una stanza

> [!NOTE]
> Questo esempio usa una corrispondenza con ID semplice tra l'ID del dispositivo e un ID del gemello digitale corrispondente, ma è possibile fornire mapping più sofisticati dal dispositivo al relativo gemello, ad esempio con una tabella di mapping.

Ogni volta che un evento di telemetria della temperatura viene inviato dal dispositivo termometro, è necessario aggiornare la proprietà *temperature* *del dispositivo gemello* . Per eseguire questa operazione, si eseguirà il mapping da un evento di telemetria su un dispositivo a un setter di proprietà nel dispositivo gemello digitale. Si utilizzeranno le informazioni sulla topologia del [grafo gemello](concepts-twins-graph.md) per trovare il gemello della *chat* , quindi è possibile impostare la proprietà del gemello. In altri scenari, gli utenti potrebbero voler impostare una proprietà nel gemello corrispondente (in questo esempio, il gemello con l'ID *123*). I dispositivi gemelli digitali di Azure offrono una grande flessibilità per decidere la modalità di mapping dei dati di telemetria ai dispositivi gemelli. 

Questo scenario è illustrato in un diagramma seguente:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Un dispositivo hub Internet di Azure Invia la telemetria della temperatura attraverso gli argomenti Hub, griglia di eventi o sistema a una funzione di Azure, che aggiorna una proprietà di temperatura nei dispositivi gemelli in dispositivi gemelli digitali di Azure." border="false":::

## <a name="prerequisites"></a>Prerequisiti

Prima di continuare con questo esempio, è necessario completare i prerequisiti seguenti.
1. Creare un hub IoT. Per istruzioni, vedere la sezione *creare un hub* di tutto il contenuto di [questa Guida introduttiva sull'hub](../iot-hub/quickstart-send-telemetry-cli.md) .
2. Creare almeno una funzione di Azure per elaborare gli eventi dall'hub. Vedere [*procedura: configurare una funzione di Azure per l'elaborazione dei dati*](how-to-create-azure-function.md) per creare una funzione di base di Azure in grado di connettersi ai dispositivi gemelli digitali di Azure e chiamare le funzioni API dei dispositivi gemelli digitali di Azure. Il resto di questa procedura verrà compilato su questa funzione.
3. Configurare una destinazione evento per i dati dell'hub. Nella [portale di Azure](https://portal.azure.com/)passare all'istanza dell'hub Internet. In *eventi*creare una sottoscrizione per la funzione di Azure. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Portale di Azure: aggiunta di una sottoscrizione di eventi":::

4. Nella pagina *Crea sottoscrizione evento* compilare i campi come indicato di seguito:
   * In *Dettagli sottoscrizione evento*assegnare un nome alla sottoscrizione come si desidera
   * In *tipi di evento*scegliere *telemetria del dispositivo* come tipo di evento in base a cui filtrare
      - Aggiungere i filtri ad altri tipi di evento, se si desidera.
   * In *Dettagli endpoint*selezionare la funzione di Azure come endpoint

## <a name="create-an-azure-function-in-visual-studio"></a>Creare una funzione di Azure in Visual Studio

Questa sezione usa gli stessi passaggi di avvio di Visual Studio e lo scheletro della funzione di Azure da [*procedura: configurare una funzione di Azure per l'elaborazione dei dati*](how-to-create-azure-function.md). Lo scheletro gestisce l'autenticazione e crea un client del servizio, pronto per l'elaborazione dei dati e per chiamare le API di Azure Digital gemelli in risposta. 

Il fulcro della funzione Skeleton è il seguente:

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Nei passaggi seguenti si aggiungerà un codice specifico per l'elaborazione degli eventi di telemetria dell'it dall'hub Internet.  

## <a name="add-telemetry-processing"></a>Aggiungi elaborazione dati di telemetria

Gli eventi di telemetria provengono dal dispositivo sotto forma di messaggi. Il primo passaggio nell'aggiunta del codice di elaborazione della telemetria è l'estrazione della parte pertinente del messaggio del dispositivo dall'evento di griglia di eventi. 

Dispositivi diversi possono strutturare i messaggi in modo diverso, quindi il codice per questo passaggio dipende dal dispositivo connesso. 

Il codice seguente illustra un esempio per un dispositivo semplice che invia dati di telemetria come JSON. L'esempio estrae l'ID del dispositivo che ha inviato il messaggio, nonché il valore della temperatura.

```csharp
JObject job = eventGridEvent.Data as JObject;
string devid = (string)job["systemProperties"].ToObject<JObject>().Property("IoT-hub-connection-device-ID").Value;
double temp = (double)job["body"].ToObject<JObject>().Property("temperature").Value;
```

Tenere presente che lo scopo di questo esercizio è aggiornare la temperatura di una *stanza* all'interno del grafo gemello. Ciò significa che la destinazione del messaggio non è il gemello digitale associato a questo dispositivo, ma il gemello della *stanza* padre. È possibile trovare il gemello padre usando il valore ID del dispositivo Estratto dal messaggio di telemetria usando il codice riportato sopra.

A tale scopo, usare le API gemelle digitali di Azure per accedere alle relazioni in ingresso al dispositivo che rappresenta il dispositivo gemello, che in questo caso ha lo stesso ID del dispositivo. Dalla relazione in ingresso è possibile trovare l'ID dell'elemento padre con il frammento di codice riportato di seguito.

Il frammento di codice seguente illustra come recuperare le relazioni in ingresso di un gemello:

```csharp
AsyncPageable<IncomingRelationship> res = client.GetIncomingRelationshipsAsync(twin_id);
await foreach (IncomingRelationship irel in res)
{
    Log.Ok($"Relationship: {irel.RelationshipName} from {irel.SourceId} | {irel.RelationshipId}");
}
```

L'elemento padre del gemello si trova nella proprietà *SourceID* della relazione.

È abbastanza comune per un modello di un dispositivo gemello che rappresenta un dispositivo per avere una sola relazione in ingresso. In questo caso, è possibile scegliere la prima (e solo) relazione restituita. Se i modelli consentono più tipi di relazioni a questo gemello, potrebbe essere necessario specificare ulteriormente per scegliere tra più relazioni in ingresso. Un modo comune per eseguire questa operazione è scegliere la relazione con `RelationshipName` . 

Una volta ottenuto l'ID del gemello padre che rappresenta la *chat*, è possibile applicare una patch a tale dispositivo, ovvero apportare aggiornamenti al dispositivo gemello. A tale scopo, usare il codice seguente:

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", temp);
try
{
    await client.UpdateDigitalTwinAsync(twin_id, uou.Serialize());
    Log.Ok($"Twin '{twin_id}' updated successfully!");
}
...
```

### <a name="full-azure-function-code"></a>Codice completo della funzione di Azure

Usando il codice degli esempi precedenti, di seguito è riportata l'intera funzione di Azure nel contesto:

```csharp
[FunctionName("ProcessHubToDTEvents")]
public async void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
{
    // After this is deployed, in order for this function to be authorized on Azure Digital Twins APIs,
    // you'll need to turn the Managed Identity Status to "On", 
    // grab the Object ID of the function, and assign the "Azure Digital Twins Owner (Preview)" role to this function identity.

    DigitalTwinsClient client = null;
    //log.LogInformation(eventGridEvent.Data.ToString());
    // Authenticate on Azure Digital Twins APIs
    try
    {
        
        ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
        client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
        log.LogInformation($"ADT service client connection created.");
    }
    catch (Exception e)
    {
        log.LogError($"ADT service client connection failed. " + e.ToString());
        return;
    }

    if (client != null)
    {
        try
        {
            if (eventGridEvent != null && eventGridEvent.Data != null)
            {
                #region Open this region for message format information
                // Telemetry message format
                //{
                //  "properties": { },
                //  "systemProperties": 
                // {
                //    "iothub-connection-device-id": "thermostat1",
                //    "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
                //    "iothub-connection-auth-generation-id": "637199981642612179",
                //    "iothub-enqueuedtime": "2020-03-18T18:35:08.269Z",
                //    "iothub-message-source": "Telemetry"
                //  },
                //  "body": "eyJUZW1wZXJhdHVyZSI6NzAuOTI3MjM0MDg3MTA1NDg5fQ=="
                //}
                #endregion

                // Reading deviceId from message headers
                log.LogInformation(eventGridEvent.Data.ToString());
                JObject job = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                string deviceId = (string)job["systemProperties"]["iothub-connection-device-id"];
                log.LogInformation($"Found device: {deviceId}");

                // Extracting temperature from device telemetry
                byte[] body = System.Convert.FromBase64String(job["body"].ToString());
                var value = System.Text.ASCIIEncoding.ASCII.GetString(body);
                var bodyProperty = (JObject)JsonConvert.DeserializeObject(value);
                var temperature = bodyProperty["Temperature"];
                log.LogInformation($"Device Temperature is:{temperature}");

                // Update device Temperature property
                await AdtUtilities.UpdateTwinProperty(client, deviceId, "/Temperature", temperature, log);

                // Find parent using incoming relationships
                string parentId = await AdtUtilities.FindParent(client, deviceId, "contains", log);
                if (parentId != null)
                {
                    await AdtUtilities.UpdateTwinProperty(client, parentId, "/Temperature", temperature, log);
                }

            }
        }
        catch (Exception e)
        {
            log.LogError($"Error in ingest function: {e.Message}");
        }
    }
}
```

Funzione di utilità per individuare le relazioni in ingresso:
```csharp
public static async Task<string> FindParent(DigitalTwinsClient client, string child, string relname, ILogger log)
{
    // Find parent using incoming relationships
    try
    {
        AsyncPageable<IncomingRelationship> rels = client.GetIncomingRelationshipsAsync(child);

        await foreach (IncomingRelationship ie in rels)
        {
            if (ie.RelationshipName == relname)
                return (ie.SourceId);
        }
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error in retrieving parent:{exc.Status}:{exc.Message}");
    }
    return null;
}
```

E la funzione di utilità per applicare la patch al dispositivo gemello:
```csharp
public static async Task UpdateTwinProperty(DigitalTwinsClient client, string twinId, string propertyPath, object value, ILogger log)
{
    // If the twin does not exist, this will log an error
    try
    {
        // Update twin property
        UpdateOperationsUtility uou = new UpdateOperationsUtility();
        uou.AppendAddOp(propertyPath, value);
        await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error:{exc.Status}/{exc.Message}");
    }
}
```

A questo punto si dispone di una funzione di Azure attrezzata per leggere e interpretare i dati dello scenario provenienti dall'hub Internet.

## <a name="debug-azure-function-apps-locally"></a>Eseguire il debug di app per le funzioni di Azure localmente

È possibile eseguire il debug di funzioni di Azure con un trigger di griglia di eventi in locale. Per altre informazioni, vedere [*trigger griglia di eventi di debug in locale*](../azure-functions/functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sui dati in ingresso e in uscita con i dispositivi gemelli digitali di Azure:
* [*Concetti: integrazione con altri servizi*](concepts-integration.md)

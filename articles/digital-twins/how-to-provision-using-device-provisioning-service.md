---
title: Gestione automatica dei dispositivi con DPS
titleSuffix: Azure Digital Twins
description: Vedere come configurare un processo automatizzato per eseguire il provisioning e il ritiro dei dispositivi Internet in Azure Digital gemelli usando il servizio Device provisioning (DPS).
author: baanders
ms.author: baanders
ms.date: 9/1/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b6dbcaf317efb8589a92275527f992029b7eb8a6
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494751"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Gestire automaticamente i dispositivi nei dispositivi gemelli digitali di Azure usando il servizio Device provisioning (DPS)

Questo articolo illustra come integrare i dispositivi gemelli digitali di Azure con il [servizio Device provisioning (DPS)](../iot-dps/about-iot-dps.md).

La soluzione descritta in questo articolo consente di automatizzare il processo di **_provisioning_** e **_ritiro_** dei dispositivi dell'hub Internet in Azure Digital Twins, usando il servizio Device provisioning. 

Per altre informazioni sulle fasi di _provisioning_ e _ritiro_ e per comprendere meglio il set di fasi di gestione dei dispositivi generali comuni a tutti i progetti dell'intero aziendale, vedere la [ *sezione ciclo* ](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) di vita dei dispositivi della documentazione di gestione dei dispositivi dell'hub.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter configurare il provisioning, è necessario avere un'istanza di **Azure Digital Twins** che contiene modelli e gemelli. Questa istanza deve essere configurata anche con la possibilità di aggiornare le informazioni sui dispositivi gemelli digitali in base ai dati. 

Se questa operazione non è già stata configurata, è possibile crearla seguendo l'esercitazione sui dispositivi gemelli di Azure Digital [*: connettere una soluzione end-to-end*](tutorial-end-to-end.md). Questa esercitazione illustra la configurazione di un'istanza di dispositivi gemelli digitali di Azure con modelli e dispositivi gemelli, un [Hub](../iot-hub/about-iot-hub.md)di Azure per le cose connesse e diverse [funzioni di Azure](../azure-functions/functions-overview.md) per propagare il flusso di dati.

I valori seguenti sono necessari più avanti in questo articolo quando si configura l'istanza. Se è necessario raccogliere nuovamente questi valori, usare i collegamenti seguenti per le istruzioni.
* **_Nome host_** dell'istanza di Gemelli digitali di Azure ([trova nel portale](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* **_Stringa_** di connessione della stringa di connessione di hub eventi di Azure ([trova nel portale](../event-hubs/event-hubs-get-connection-string.md#get-connection-string-from-the-portal))

Questo esempio usa anche un **simulatore di dispositivi** che include il provisioning usando il servizio Device provisioning. Il simulatore di dispositivi è disponibile qui: [esempio di integrazione di Azure Digital gemells e dell'hub](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)Internet. Ottenere il progetto di esempio nel computer passando al collegamento di esempio e selezionando il pulsante *Scarica zip* sotto il titolo. Decomprimere la cartella scaricata.

Il simulatore del dispositivo si basa su **Node.js**, versione 10.0. x o successiva. [*Preparare l'ambiente di sviluppo*](https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md) descrive come installare Node.js per questa esercitazione in Windows o Linux.

## <a name="solution-architecture"></a>Architettura della soluzione

L'immagine seguente illustra l'architettura di questa soluzione usando i dispositivi gemelli digitali di Azure con il servizio Device provisioning. Mostra sia il provisioning del dispositivo che il flusso di ritiro.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="Visualizzazione di un dispositivo e di diversi servizi di Azure in uno scenario end-to-end. I dati passano da un dispositivo termostato a un DP e viceversa. I dati passano anche da DPS nell'hub Internet e ai gemelli digitali di Azure tramite una funzione di Azure con etichetta &quot;Allocation&quot;. I dati provenienti da un'azione ' Elimina dispositivo ' manuale passano attraverso l'hub degli eventi > Hub eventi > funzioni di Azure > i dispositivi gemelli digitali di Azure.":::

Questo articolo è diviso in due sezioni:
* [*Eseguire il provisioning automatico del dispositivo con il servizio Device provisioning*](#auto-provision-device-using-device-provisioning-service)
* [*Ritirare automaticamente il dispositivo usando gli eventi del ciclo di vita dell'hub*](#auto-retire-device-using-iot-hub-lifecycle-events)

Per una spiegazione più approfondita di ogni passaggio dell'architettura, vedere le singole sezioni più avanti in questo articolo.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Eseguire il provisioning automatico del dispositivo con il servizio Device provisioning

In questa sezione si collegherà il servizio Device provisioning a dispositivi digitali gemelli di Azure per eseguire il provisioning automatico dei dispositivi tramite il percorso riportato di seguito. Si tratta di un estratto dall'architettura completa illustrata in [precedenza](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="Visualizzazione di un dispositivo e di diversi servizi di Azure in uno scenario end-to-end. I dati passano da un dispositivo termostato a un DP e viceversa. I dati passano anche da DPS nell'hub Internet e ai gemelli digitali di Azure tramite una funzione di Azure con etichetta &quot;Allocation&quot;. I dati provenienti da un'azione ' Elimina dispositivo ' manuale passano attraverso l'hub degli eventi > Hub eventi > funzioni di Azure > i dispositivi gemelli digitali di Azure.":::

Ecco una descrizione del flusso del processo:
1. Il dispositivo Contatta l'endpoint DPS, passando le informazioni di identificazione per dimostrare la propria identità.
2. DPS convalida l'identità del dispositivo convalidando l'ID e la chiave di registrazione nell'elenco di registrazione e chiama una [funzione di Azure](../azure-functions/functions-overview.md) per eseguire l'allocazione.
3. La funzione di Azure crea un nuovo [gemello](concepts-twins-graph.md) nei dispositivi gemelli digitali di Azure per il dispositivo.
4. DPS registra il dispositivo con un hub Internet e popola lo stato del dispositivo gemello desiderato.
5. L'hub Internet delle cose restituisce al dispositivo le informazioni relative all'ID del dispositivo e alle informazioni di connessione dell'hub. Il dispositivo può ora connettersi all'hub Internet delle cose.

Le sezioni seguenti illustrano i passaggi per configurare il flusso del dispositivo con provisioning automatico.

### <a name="create-a-device-provisioning-service"></a>Creare un servizio Device Provisioning

Quando viene effettuato il provisioning di un nuovo dispositivo usando il servizio Device provisioning, è possibile creare un nuovo dispositivo gemello in Azure Digital gemelli.

Creare un'istanza del servizio Device provisioning, che verrà usata per eseguire il provisioning dei dispositivi. È possibile usare le istruzioni dell'interfaccia della riga di comando di Azure riportate di seguito oppure usare la portale di Azure: [*Guida introduttiva: configurare il servizio Device provisioning in hub Internet con l'portale di Azure*](../iot-dps/quick-setup-auto-provision.md).

Il comando dell'interfaccia della riga di comando di Azure seguente creerà un servizio Device provisioning. Sarà necessario specificare un nome, un gruppo di risorse e un'area. Il comando può essere eseguito in [cloud Shell](https://shell.azure.com)o localmente se nel [computer è installata](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)l'interfaccia della riga di comando di Azure.

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region; for example, eastus>
```

### <a name="create-an-azure-function"></a>Creare una funzione di Azure

Si creerà quindi una funzione attivata da una richiesta HTTP all'interno di un'app per le funzioni. È possibile usare l'app per le funzioni creata nell'esercitazione end-to-end ([*esercitazione: connettere una soluzione end-to-end*](tutorial-end-to-end.md)) o personalizzata.

Questa funzione verrà usata dal servizio Device provisioning in un criterio di [allocazione personalizzato](../iot-dps/how-to-use-custom-allocation-policies.md) per eseguire il provisioning di un nuovo dispositivo. Per altre informazioni sull'uso di richieste HTTP con funzioni di Azure, vedere [*trigger di richiesta HTTP di Azure per funzioni di Azure*](../azure-functions/functions-bindings-http-webhook-trigger.md).

All'interno del progetto di app per le funzioni, aggiungere una nuova funzione. Aggiungere anche un nuovo pacchetto NuGet al progetto: `Microsoft.Azure.Devices.Provisioning.Service` .

Nel file di codice della funzione appena creato incollare il codice seguente.

```C#
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Devices.Shared;
using Microsoft.Azure.Devices.Provisioning.Service;
using System.Net.Http;
using Azure.Identity;
using Azure.DigitalTwins.Core;
using Azure.Core.Pipeline;
using Azure;
using System.Collections.Generic;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Samples.AdtIothub
{
    public static class DpsAdtAllocationFunc
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("DpsAdtAllocationFunc")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger log)
        {
            // Get request body
            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogDebug($"Request.Body: {requestBody}");
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Get registration ID of the device
            string regId = data?.deviceRuntimeContext?.registrationId;

            bool fail = false;
            string message = "Uncaught error";
            ResponseObj obj = new ResponseObj();

            // Must have unique registration ID on DPS request 
            if (regId == null)
            {
                message = "Registration ID not provided for the device.";
                log.LogInformation("Registration ID: NULL");
                fail = true;
            }
            else
            {
                string[] hubs = data?.linkedHubs.ToObject<string[]>();

                // Must have hubs selected on the enrollment
                if (hubs == null)
                {
                    message = "No hub group defined for the enrollment.";
                    log.LogInformation("linkedHubs: NULL");
                    fail = true;
                }
                else
                {
                    // Find or create twin based on the provided registration ID and model ID
                    dynamic payloadContext = data?.deviceRuntimeContext?.payload;
                    string dtmi = payloadContext.modelId;
                    log.LogDebug($"payload.modelId: {dtmi}");
                    string dtId = await FindOrCreateTwin(dtmi, regId, log);

                    // Get first linked hub (TODO: select one of the linked hubs based on policy)
                    obj.iotHubHostName = hubs[0];

                    // Specify the initial tags for the device.
                    TwinCollection tags = new TwinCollection();
                    tags["dtmi"] = dtmi;
                    tags["dtId"] = dtId;

                    // Specify the initial desired properties for the device.
                    TwinCollection properties = new TwinCollection();

                    // Add the initial twin state to the response.
                    TwinState twinState = new TwinState(tags, properties);
                    obj.initialTwin = twinState;
                }
            }

            log.LogDebug("Response: " + ((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message));

            return (fail)
                ? new BadRequestObjectResult(message)
                : (ActionResult)new OkObjectResult(obj);
        }

        public static async Task<string> FindOrCreateTwin(string dtmi, string regId, ILogger log)
        {
            // Create Digital Twins client
            var cred = new ManagedIdentityCredential(adtAppId);
            var client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });

            // Find existing twin with registration ID
            string dtId;
            string query = $"SELECT * FROM DigitalTwins T WHERE $dtId = '{regId}' AND IS_OF_MODEL('{dtmi}')";
            AsyncPageable<string> twins = client.QueryAsync(query);
            
            await foreach (string twinJson in twins)
            {
                // Get DT ID from the Twin
                JObject twin = (JObject)JsonConvert.DeserializeObject(twinJson);
                dtId = (string)twin["$dtId"];
                log.LogInformation($"Twin '{dtId}' with Registration ID '{regId}' found in DT");
                return dtId;
            }

            // Not found, so create new twin
            log.LogInformation($"Twin ID not found, setting DT ID to regID");
            dtId = regId; // use the Registration ID as the DT ID

            // Define the model type for the twin to be created
            Dictionary<string, object> meta = new Dictionary<string, object>()
            {
                { "$model", dtmi }
            };
            // Initialize the twin properties
            Dictionary<string, object> twinProps = new Dictionary<string, object>()
            {
                { "$metadata", meta }
            };
            twinProps.Add("Temperature", 0.0);
            await client.CreateDigitalTwinAsync(dtId, System.Text.Json.JsonSerializer.Serialize<Dictionary<string, object>>(twinProps));
            log.LogInformation($"Twin '{dtId}' created in DT");

            return dtId;
        }
    }

    public class ResponseObj
    {
        public string iotHubHostName { get; set; }
        public TwinState initialTwin { get; set; }
    }
}
```

Salvare il file e quindi pubblicare di nuovo l'app per le funzioni. Per istruzioni sulla pubblicazione dell'app per le funzioni, vedere la sezione [*pubblicare l'app*](tutorial-end-to-end.md#publish-the-app) dell'esercitazione end-to-end.

### <a name="configure-your-function"></a>Configurare la funzione

A questo punto, è necessario impostare le variabili di ambiente nell'app per le funzioni precedente, contenente il riferimento all'istanza di Azure Digital Twins creata. Se è stata usata l'esercitazione end-to-end ([*esercitazione: connettere una soluzione end-to-end*](tutorial-end-to-end.md)), l'impostazione sarà già configurata.

Aggiungere l'impostazione con questo comando dell'interfaccia della riga di comando di Azure:

```azurecli-interactive
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Assicurarsi che le autorizzazioni e l'assegnazione di ruolo identità gestita siano configurate correttamente per l'app per le funzioni, come descritto nella sezione [*assegnare le autorizzazioni all'app per le funzioni*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) nell'esercitazione end-to-end.

<!-- 
* Azure AD app registration **_Application (client) ID_** ([find in portal](../articles/digital-twins/how-to-set-up-instance-portal.md#collect-important-values))

```azurecli-interactive
az functionapp config appsettings set --settings "AdtAppId=<Application (client)" ID> -g <resource group> -n <your App Service (function app) name> 
``` -->

### <a name="create-device-provisioning-enrollment"></a>Creare la registrazione del provisioning dei dispositivi

Successivamente, sarà necessario creare una registrazione nel servizio Device provisioning usando una **funzione di allocazione personalizzata**. Seguire le istruzioni riportate nelle sezioni [*creare le*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) chiavi di registrazione e [*derivazione di chiavi univoche*](../iot-dps/how-to-use-custom-allocation-policies.md#derive-unique-device-keys) del dispositivo dei servizi Device provisioning sui criteri di allocazione personalizzati.

Durante l'esecuzione di tale flusso, si collegherà la registrazione alla funzione appena creata selezionando la funzione durante il passaggio per **selezionare come assegnare i dispositivi agli hub**. Dopo la creazione della registrazione, il nome della registrazione e la chiave di firma di accesso condiviso primaria o secondaria verranno usati in un secondo momento per configurare il simulatore di dispositivi per questo articolo.

### <a name="set-up-the-device-simulator"></a>Configurare il simulatore di dispositivi

Questo esempio usa un simulatore di dispositivi che include il provisioning tramite il servizio Device provisioning. Il simulatore di dispositivi è disponibile qui: [esempio di integrazione di Azure Digital gemells e dell'hub](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)Internet. Se l'esempio non è ancora stato scaricato, scaricarlo ora passando al collegamento di esempio e selezionando il pulsante *Scarica zip* sotto il titolo. Decomprimere la cartella scaricata.

Aprire una finestra di comando e passare alla cartella scaricata, quindi alla directory *Device-Simulator* . Installare le dipendenze per il progetto usando il comando seguente:

```cmd
npm install
```

Copiare quindi il file *. env. template* in un nuovo file denominato *. env*e compilare le impostazioni seguenti:

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary or secondary SAS key>"
```

Salvare e chiudere il file.

### <a name="start-running-the-device-simulator"></a>Avviare l'esecuzione del simulatore del dispositivo

Sempre nella directory *Device-Simulator* nella finestra di comando, avviare il simulatore di dispositivi usando il comando seguente:

```cmd
node .\adt_custom_register.js
```

Si noterà che il dispositivo è registrato e connesso all'hub Internet e quindi inizia a inviare messaggi.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="Visualizzazione di un dispositivo e di diversi servizi di Azure in uno scenario end-to-end. I dati passano da un dispositivo termostato a un DP e viceversa. I dati passano anche da DPS nell'hub Internet e ai gemelli digitali di Azure tramite una funzione di Azure con etichetta &quot;Allocation&quot;. I dati provenienti da un'azione ' Elimina dispositivo ' manuale passano attraverso l'hub degli eventi > Hub eventi > funzioni di Azure > i dispositivi gemelli digitali di Azure.":::

### <a name="validate"></a>Convalida

In seguito al flusso configurato in questo articolo, il dispositivo verrà registrato automaticamente nei dispositivi gemelli digitali di Azure. Usare il comando dell'interfaccia della riga di comando di [Azure Digital Twins](how-to-use-cli.md) seguente per trovare il dispositivo gemello nell'istanza di Azure Digital Twins creata.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Si noterà che il dispositivo gemello si trova nell'istanza di Azure Digital gemelli.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="Visualizzazione di un dispositivo e di diversi servizi di Azure in uno scenario end-to-end. I dati passano da un dispositivo termostato a un DP e viceversa. I dati passano anche da DPS nell'hub Internet e ai gemelli digitali di Azure tramite una funzione di Azure con etichetta &quot;Allocation&quot;. I dati provenienti da un'azione ' Elimina dispositivo ' manuale passano attraverso l'hub degli eventi > Hub eventi > funzioni di Azure > i dispositivi gemelli digitali di Azure.":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Ritirare automaticamente il dispositivo usando gli eventi del ciclo di vita dell'hub

In questa sezione verranno collegati gli eventi del ciclo di vita dell'hub per i dispositivi gemelli di Azure per disattivare automaticamente i dispositivi tramite il percorso riportato di seguito. Si tratta di un estratto dall'architettura completa illustrata in [precedenza](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="Visualizzazione di un dispositivo e di diversi servizi di Azure in uno scenario end-to-end. I dati passano da un dispositivo termostato a un DP e viceversa. I dati passano anche da DPS nell'hub Internet e ai gemelli digitali di Azure tramite una funzione di Azure con etichetta &quot;Allocation&quot;. I dati provenienti da un'azione ' Elimina dispositivo ' manuale passano attraverso l'hub degli eventi > Hub eventi > funzioni di Azure > i dispositivi gemelli digitali di Azure.":::

Ecco una descrizione del flusso del processo:
1. Un processo esterno o manuale attiva l'eliminazione di un dispositivo nell'hub.
2. L'hub Internet delle cose Elimina il dispositivo e genera un evento del ciclo di vita del [dispositivo](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) che verrà indirizzato a un [Hub eventi](../event-hubs/event-hubs-about.md).
3. Una funzione di Azure Elimina il gemello del dispositivo nei dispositivi gemelli digitali di Azure.

Le sezioni seguenti illustrano i passaggi per configurare questo flusso di dispositivo di ritiro automatico.

### <a name="create-an-event-hub"></a>Creare un hub eventi

A questo punto è necessario creare un [Hub eventi](../event-hubs/event-hubs-about.md)di Azure, che verrà usato per ricevere gli eventi del ciclo di vita dell'hub. 

Eseguire i passaggi descritti nella Guida introduttiva per [*creare un hub eventi*](../event-hubs/event-hubs-create.md) usando le informazioni seguenti:
* Se si sta usando l'esercitazione end-to-end ([*esercitazione: connettere una soluzione end-to-end*](tutorial-end-to-end.md)), è possibile riusare il gruppo di risorse creato per l'esercitazione end-to-end.
* Denominare il *lifecycleevents*dell'hub eventi o un altro elemento desiderato e ricordare lo spazio dei nomi creato. Questi elementi vengono usati quando si configura la funzione del ciclo di vita e la route dell'hub Internet nelle sezioni successive.

### <a name="create-an-azure-function"></a>Creare una funzione di Azure

Successivamente, verrà creata una funzione attivata da Hub eventi all'interno di un'app per le funzioni. È possibile usare l'app per le funzioni creata nell'esercitazione end-to-end ([*esercitazione: connettere una soluzione end-to-end*](tutorial-end-to-end.md)) o personalizzata. 

Denominare il trigger dell'hub eventi *lifecycleevents*e connettere il trigger dell'hub eventi all'hub eventi creato nel passaggio precedente. Se è stato usato un nome di hub eventi diverso, modificarlo in modo che corrisponda al nome del trigger riportato di seguito.

Questa funzione userà l'evento ciclo di vita del dispositivo hub Internet per ritirare un dispositivo esistente. Per altre informazioni sugli eventi del ciclo di vita, vedere [*eventi di non telemetria dell'hub*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)Internet. Per altre informazioni sull'uso di hub eventi con funzioni di Azure, vedere [*trigger di hub eventi di Azure per funzioni di Azure*](../azure-functions/functions-bindings-event-hubs-trigger.md).

All'interno dell'app per le funzioni pubblicata aggiungere una nuova classe di funzione di tipo *trigger Hub eventi*e incollare il codice seguente.

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Azure;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Samples.AdtIothub
{
    public static class DeleteDeviceInTwinFunc
    {
        private static string adtAppId = "https://digitaltwins.azure.net";
        private static readonly string adtInstanceUrl = System.Environment.GetEnvironmentVariable("ADT_SERVICE_URL", EnvironmentVariableTarget.Process);
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("DeleteDeviceInTwinFunc")]
        public static async Task Run(
            [EventHubTrigger("lifecycleevents", Connection = "EVENTHUB_CONNECTIONSTRING")] EventData[] events, ILogger log)
        {
            var exceptions = new List<Exception>();

            foreach (EventData eventData in events)
            {
                try
                {
                    //log.LogDebug($"EventData: {System.Text.Json.JsonSerializer.Serialize(eventData)}");

                    string opType = eventData.Properties["opType"] as string;
                    if (opType == "deleteDeviceIdentity")
                    {
                        string deviceId = eventData.Properties["deviceId"] as string;
                        
                        // Create Digital Twin client
                        var cred = new ManagedIdentityCredential(adtAppId);
                        var client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });

                        // Find twin based on the original Registration ID
                        string regID = deviceId; // simple mapping
                        string dtId = await GetTwinId(client, regID, log);
                        if (dtId != null)
                        {
                            await DeleteRelationships(client, dtId, log);

                            // Delete twin
                            await client.DeleteDigitalTwinAsync(dtId);
                            log.LogInformation($"Twin '{dtId}' deleted in DT");
                        }
                    }
                }
                catch (Exception e)
                {
                    // We need to keep processing the rest of the batch - capture this exception and continue.
                    exceptions.Add(e);
                }
            }

            if (exceptions.Count > 1)
                throw new AggregateException(exceptions);

            if (exceptions.Count == 1)
                throw exceptions.Single();
        }


        public static async Task<string> GetTwinId(DigitalTwinsClient client, string regId, ILogger log)
        {
            string query = $"SELECT * FROM DigitalTwins T WHERE T.$dtId = '{regId}'";
            AsyncPageable<string> twins = client.QueryAsync(query);
            await foreach (string twinJson in twins)
            {
                JObject twin = (JObject)JsonConvert.DeserializeObject(twinJson);
                string dtId = (string)twin["$dtId"];
                log.LogInformation($"Twin '{dtId}' found in DT");
                return dtId;
            }

            return null;
        }

        public static async Task DeleteRelationships(DigitalTwinsClient client, string dtId, ILogger log)
        {
            var relationshipIds = new List<string>();

            AsyncPageable<string> relationships = client.GetRelationshipsAsync(dtId);
            await foreach (var relationshipJson in relationships)
            {
                BasicRelationship relationship = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relationshipJson);
                relationshipIds.Add(relationship.Id);
            }

            foreach (var relationshipId in relationshipIds)
            {
                client.DeleteRelationship(dtId, relationshipId);
                log.LogInformation($"Twin '{dtId}' relationship '{relationshipId}' deleted in DT");
            }
        }
    }
}
```

Salvare il progetto, quindi pubblicare di nuovo l'app per le funzioni. Per istruzioni sulla pubblicazione dell'app per le funzioni, vedere la sezione [*pubblicare l'app*](tutorial-end-to-end.md#publish-the-app) dell'esercitazione end-to-end.

### <a name="configure-your-function"></a>Configurare la funzione

A questo punto, è necessario impostare le variabili di ambiente nell'app per le funzioni precedente, contenente il riferimento all'istanza di Azure Digital Twins creata e all'hub eventi. Se è stata usata l'esercitazione end-to-end ([*esercitazione: connettere una soluzione end-to-end*](./tutorial-end-to-end.md)), la prima impostazione sarà già configurata.

Aggiungere l'impostazione con questo comando dell'interfaccia della riga di comando di Azure. Il comando può essere eseguito in [cloud Shell](https://shell.azure.com)o localmente se nel [computer è installata](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)l'interfaccia della riga di comando di Azure.

```azurecli-interactive
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Successivamente, sarà necessario configurare la variabile di ambiente Function per la connessione all'hub eventi appena creato.

```azurecli-interactive
az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
```

Assicurarsi che le autorizzazioni e l'assegnazione di ruolo identità gestita siano configurate correttamente per l'app per le funzioni, come descritto nella sezione [*assegnare le autorizzazioni all'app per le funzioni*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) nell'esercitazione end-to-end.

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Creare una route dell'hub Internet per gli eventi del ciclo di vita

A questo punto è necessario configurare una route dell'hub Internet, per instradare gli eventi del ciclo di vita del dispositivo. In questo caso, si ascolteranno in modo specifico gli eventi Delete del dispositivo, identificati da `if (opType == "deleteDeviceIdentity")` . Questa operazione attiverà l'eliminazione dell'elemento del dispositivo gemello digitale, finalizzando il ritiro di un dispositivo e il suo gemello digitale.

Le istruzioni per la creazione di una route dell'hub Internet sono descritte in questo articolo: [*usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a diversi endpoint*](../iot-hub/iot-hub-devguide-messages-d2c.md). La sezione *eventi non di telemetria* spiega che è possibile usare **gli eventi del ciclo** di vita del dispositivo come origine dati per la route.

La procedura da seguire per questa configurazione è la seguente:
1. Creare un endpoint personalizzato dell'hub eventi dell'hub eventi. Questo endpoint deve avere come destinazione l'hub eventi creato nella sezione [*creare un hub eventi*](#create-an-event-hub) .
2. Aggiungere una route *degli eventi del ciclo* di vita dei dispositivi. Usare l'endpoint creato nel passaggio precedente. È possibile limitare gli eventi del ciclo di vita dei dispositivi per inviare solo gli eventi di eliminazione aggiungendo la query di routing `opType='deleteDeviceIdentity'` .
    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="Visualizzazione di un dispositivo e di diversi servizi di Azure in uno scenario end-to-end. I dati passano da un dispositivo termostato a un DP e viceversa. I dati passano anche da DPS nell'hub Internet e ai gemelli digitali di Azure tramite una funzione di Azure con etichetta &quot;Allocation&quot;. I dati provenienti da un'azione ' Elimina dispositivo ' manuale passano attraverso l'hub degli eventi > Hub eventi > funzioni di Azure > i dispositivi gemelli digitali di Azure.":::

Una volta attraversato questo flusso, tutti gli elementi vengono impostati per ritirare i dispositivi end-to-end.

### <a name="validate"></a>Convalida

Per attivare il processo di ritiro, è necessario eliminare manualmente il dispositivo dall'hub.

Nella [prima metà di questo articolo](#auto-provision-device-using-device-provisioning-service)è stato creato un dispositivo nell'hub Internet e un dispositivo gemello digitale corrispondente. 

A questo punto, passare all'hub Internet e quindi eliminare il dispositivo. è possibile eseguire questa operazione con un [comando dell'interfaccia della riga di comando di Azure](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest&preserve-view=true#ext-azure-cli-iot-ext-az-iot-hub-device-identity-delete) o nel [portale di Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Devices%2FIotHubs). 

Il dispositivo verrà rimosso automaticamente dai dispositivi gemelli digitali di Azure. 

Usare il comando dell'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md) seguente per verificare che il dispositivo gemello nell'istanza di Azure Digital Twins sia stato eliminato.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Si noterà che il gemello del dispositivo non è più disponibile nell'istanza di Azure Digital Twins.
:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="Visualizzazione di un dispositivo e di diversi servizi di Azure in uno scenario end-to-end. I dati passano da un dispositivo termostato a un DP e viceversa. I dati passano anche da DPS nell'hub Internet e ai gemelli digitali di Azure tramite una funzione di Azure con etichetta &quot;Allocation&quot;. I dati provenienti da un'azione ' Elimina dispositivo ' manuale passano attraverso l'hub degli eventi > Hub eventi > funzioni di Azure > i dispositivi gemelli digitali di Azure.":::

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse create in questo articolo non sono più necessarie, attenersi alla procedura seguente per eliminarle.

Usando l'interfaccia della riga di comando di Azure Azure Cloud Shell o locale è possibile eliminare tutte le risorse di Azure in un gruppo di risorse con il comando [AZ Group Delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) . Il gruppo di risorse verrà rimosso. istanza di Azure Digital Twins; l'hub Internet delle cose e la registrazione del dispositivo hub; argomento di griglia di eventi e sottoscrizioni associate; lo spazio dei nomi di hub eventi e entrambe le app di funzioni di Azure, incluse le risorse associate come l'archiviazione.

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Eliminare quindi la cartella di esempio del progetto scaricata dal computer locale.

## <a name="next-steps"></a>Passaggi successivi

I dispositivi gemelli digitali creati per i dispositivi vengono archiviati come una gerarchia semplice nei dispositivi gemelli digitali di Azure, ma possono essere arricchiti con le informazioni sul modello e una gerarchia a più livelli per l'organizzazione. Per ulteriori informazioni su questo concetto, vedere:

* [*Concetti: i dispositivi gemelli digitali e i grafici gemelli*](concepts-twins-graph.md)

È possibile scrivere logica personalizzata per fornire automaticamente queste informazioni usando i dati del modello e del grafo già archiviati nei dispositivi gemelli digitali di Azure. Per altre informazioni sulla gestione, l'aggiornamento e il recupero delle informazioni dal grafo gemello, vedere gli argomenti seguenti:

* [*Procedura: gestire un dispositivo gemello digitale*](how-to-manage-twin.md)
* [*Procedura: eseguire una query sul grafico gemello*](how-to-query-graph.md)
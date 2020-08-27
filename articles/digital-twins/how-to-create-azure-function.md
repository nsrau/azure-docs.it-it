---
title: Configurare una funzione di Azure per l'elaborazione dei dati
titleSuffix: Azure Digital Twins
description: Vedere come creare una funzione di Azure che possa accedere e essere attivata da dispositivi gemelli digitali.
author: baanders
ms.author: baanders
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 66f514f4c5d299ef11efda541f16f4ef2fe61aed
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930163"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>Connettere le app di funzioni di Azure per l'elaborazione dei dati

Durante l'anteprima, l'aggiornamento dei dispositivi gemelli digitali basati sui dati viene gestito usando le [**Route di eventi**](concepts-route-events.md) tramite risorse di calcolo, ad esempio [funzioni di Azure](../azure-functions/functions-overview.md). Una funzione di Azure può essere usata per aggiornare un dispositivo gemello digitale in risposta a:
* dati di telemetria del dispositivo provenienti dall'hub Internet
* modifica delle proprietà o altri dati provenienti da un altro dispositivo gemello digitale all'interno del grafico gemello

Questo articolo illustra la creazione di una funzione di Azure da usare con i dispositivi gemelli digitali di Azure. 

Ecco una panoramica dei passaggi che contiene:

1. Creare un'app funzioni di Azure in Visual Studio
2. Scrivere una funzione di Azure con un trigger di [griglia di eventi](../event-grid/overview.md)
3. Aggiungere il codice di autenticazione alla funzione (per poter accedere ai dispositivi gemelli digitali di Azure)
4. Pubblicare l'app per le funzioni in Azure
5. Configurare l'accesso alla [sicurezza](concepts-security.md) . Per consentire alla funzione di Azure di ricevere un token di accesso in fase di esecuzione per accedere al servizio, è necessario configurare identità del servizio gestita per l'app per le funzioni.

Il resto di questo articolo illustra i passaggi di installazione delle funzioni di Azure, uno alla volta.

## <a name="create-an-azure-functions-app-in-visual-studio"></a>Creare un'app funzioni di Azure in Visual Studio

In Visual Studio 2019 selezionare *File > nuovo progetto*. Cercare il modello *funzioni di Azure* , selezionarlo e premere "Avanti".

:::image type="content" source="media/how-to-create-azure-function/visual-studio-new-project.png" alt-text="Visual Studio: finestra di dialogo nuovo progetto":::

Specificare un nome per l'app per le funzioni e fare clic su "Crea".

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-config.png" alt-text="Visual Studio: finestra di dialogo Configura progetto":::

Selezionare il *trigger griglia di eventi* e premere "Crea".

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-trigger.png" alt-text="Visual Studio: finestra di dialogo trigger del progetto di funzione di Azure":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>Scrivere una funzione di Azure con un trigger di griglia di eventi

Il codice seguente crea una funzione di Azure breve che è possibile usare per registrare gli eventi: 

```csharp
// Default URL for triggering Event Grid function in the local environment
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;

namespace FunctionSample
{
    public static class FooFunction    {
        [FunctionName("Foo")]
        public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Si tratta della funzione di base di Azure.

### <a name="run-and-debug-the-azure-function-app"></a>Eseguire ed eseguire il debug dell'app per le funzioni di Azure

È ora possibile compilare ed eseguire la funzione. Mentre le funzioni di Azure sono destinate all'esecuzione nel cloud, è anche possibile eseguire ed eseguire il debug di funzioni di Azure in locale.

Per altre informazioni, vedere [*trigger griglia di eventi di debug in locale*](../azure-functions/functions-debug-event-grid-trigger-local.md).

### <a name="add-the-azure-digital-twins-sdk-to-your-azure-function-app"></a>Aggiungere Azure Digital Twins SDK all'app per le funzioni di Azure

L'app per le funzioni interagisce con i dispositivi gemelli digitali di Azure usando la [libreria client dei dispositivi digitali gemelli di Azure per .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Per usare l'SDK, è necessario includere i pacchetti seguenti nel progetto:
* `Azure.DigitalTwins.Core` (versione `1.0.0-preview.2` )
* `Azure.Identity`

Per la configurazione della pipeline di Azure SDK per configurare correttamente per funzioni di Azure, è necessario anche:
* `Azure.Net.Http`
* `Azure.Core`

A seconda degli strumenti scelti, è possibile eseguire questa operazione con gestione pacchetti di Visual Studio o lo `dotnet` strumento da riga di comando. 

Aggiungere le istruzioni using seguenti alla funzione di Azure.

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;
```

## <a name="add-authentication-code-to-the-azure-function"></a>Aggiungere il codice di autenticazione alla funzione di Azure

Aggiungere quindi il codice di autenticazione che consente alla funzione di accedere ai dispositivi gemelli digitali di Azure.

Aggiungere variabili alla classe Function per questi valori: 
* ID app per dispositivi digitali gemelli di Azure
* URL dell'istanza di Digital gemelli di Azure. È consigliabile leggere l'URL del servizio da una variabile di ambiente, anziché impostarlo come hardcoded nella funzione.
* Variabile statica per la quale deve essere contenuta un'istanza di HttpClient. HttpClient è relativamente costoso da creare ed è consigliabile evitare di eseguire questa operazione per ogni chiamata di funzione.

Aggiungere anche una variabile locale all'interno della funzione per ospitare l'istanza del client di Azure Digital Twins nel progetto di funzione. *Non* renderla una variabile statica all'interno della classe.

Infine, modificare la firma della funzione in modo che sia asincrona.

Una volta apportate queste modifiche, il codice della funzione dovrebbe essere simile al seguente:

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

Per consentire all'app per le funzioni di accedere ai dispositivi gemelli digitali di Azure, è necessario disporre di un'identità gestita dal sistema e avere le autorizzazioni per accedere all'istanza di Azure Digital gemelli.

Usare il comando seguente per creare l'identità gestita dal sistema. Prendere nota del campo *principalId* nell'output.

```azurecli
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Usare il valore *PrincipalId* nel comando seguente per assegnare l'identità dell'app per le funzioni al ruolo *proprietario digitale gemelli di Azure (anteprima)* per l'istanza di Azure Digital gemelli. In questo modo si concede all'app per le funzioni l'autorizzazione per l'esecuzione di attività del piano dati.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

Per altre informazioni sull'identità gestita, vedere [*come usare le identità gestite per il servizio app e funzioni di Azure*](../app-service/overview-managed-identity.md).

Infine, è possibile rendere accessibile l'URL dell'istanza di Azure Digital Twins alla funzione impostando una variabile di ambiente. Per altre informazioni, vedere variabili di [*ambiente*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables).

> [!TIP]
> L'URL dell'istanza di Azure Digital gemelli viene creato aggiungendo *https://* all'inizio del *nome host*dell'istanza di Azure Digital gemelli. Per visualizzare il nome host, insieme a tutte le proprietà dell'istanza, è possibile eseguire `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-URL>"
```

## <a name="publish-the-azure-function-app"></a>Pubblicare l'app per le funzioni di Azure

Per pubblicare l'app per le funzioni in Azure, fare clic con il pulsante destro del mouse sul progetto di funzione (non sulla soluzione) in Esplora soluzioni e scegliere *pubblica ()*.

Verrà visualizzata la scheda seguente:

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-1.png" alt-text="Visual Studio: finestra di dialogo pubblica funzione, pagina 1":::

Selezionare o creare un piano di servizio app da usare con funzioni di Azure. In caso di dubbi, iniziare usando il piano a consumo predefinito.

> [!IMPORTANT] 
> La pubblicazione di una funzione di Azure comporta costi aggiuntivi per la sottoscrizione, indipendentemente dai dispositivi gemelli digitali di Azure.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-2.png" alt-text="Visual Studio: finestra di dialogo pubblica funzione, pagina 2":::

Nella pagina seguente immettere il nome desiderato per la nuova app per le funzioni, per un gruppo di risorse e per altri dettagli.

## <a name="set-up-security-access-for-the-azure-function-app"></a>Configurare l'accesso di sicurezza per l'app per le funzioni di Azure

Per poter eseguire l'autenticazione con i dispositivi gemelli digitali di Azure, lo scheletro della funzione di Azure degli esempi precedenti richiede che venga passato un bearer token. Per assicurarsi che questo bearer token venga passato, è necessario configurare [identità del servizio gestita (MSI)](../active-directory/managed-identities-azure-resources/overview.md) per l'app per le funzioni. Questa operazione deve essere eseguita solo una volta per ogni app per le funzioni.

Per configurare questa impostazione, passare alla [portale di Azure](https://portal.azure.com/) e passare all'app per le funzioni.

Nella scheda *funzionalità della piattaforma* selezionare *Identity (identità*):

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-1.png" alt-text="Portale di Azure: selezione dell'identità per una funzione di Azure":::

Nella pagina identità impostare lo *stato* *su*attivato. 

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-2.png" alt-text="Portale di Azure: attivazione dello stato di identità":::

Si noti anche l' **ID oggetto** illustrato in questa pagina, che verrà usato nella sezione successiva.

### <a name="assign-access-roles"></a>Assegnare i ruoli di accesso

Poiché i dispositivi gemelli digitali di Azure usano il controllo degli accessi in base al ruolo per gestire l'accesso (vedere [*concetti: sicurezza per le soluzioni di dispositivi gemelli digitali di Azure*](concepts-security.md) per altre informazioni), è anche necessario aggiungere un ruolo per ogni app per le funzioni che si vuole consentire di accedere ai dispositivi gemelli digitali di Azure.

Per assegnare un ruolo, è necessario l' **ID risorsa** dell'istanza di Azure Digital Twins creata. Se non è stato registrato in precedenza durante la creazione dell'istanza, è possibile recuperarla utilizzando questo comando:

```bash
az dt show --name <your-instance-name> -g <your-resource-group-name>
```
L'ID risorsa sarà parte dell'output, come una stringa estesa denominata "ID" che inizia con le lettere "/subscriptions/...".

Usare l'ID risorsa con l'ID oggetto della funzione di Azure precedente nel comando seguente:

```azurecli
az role assignment create --role "Azure Digital Twins Owner (Preview)" --assignee <object-ID> --scope <resource-ID>
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati seguiti i passaggi per configurare una funzione di Azure da usare con i dispositivi gemelli digitali di Azure. Successivamente, è possibile sottoscrivere la funzione di Azure in griglia di eventi per restare in attesa su un endpoint. Questo endpoint può essere:
* Un endpoint di griglia di eventi collegato ai dispositivi gemelli digitali di Azure per elaborare i messaggi provenienti da gemelli digitali di Azure, ad esempio i messaggi di modifica delle proprietà, i messaggi di telemetria generati dai dispositivi [gemelli digitali](concepts-twins-graph.md) nel grafico gemello o i messaggi del ciclo di vita.
* Argomenti di sistema di Internet delle cose usati dall'hub Internet per inviare dati di telemetria e altri eventi del dispositivo
* Un endpoint di griglia di eventi che riceve messaggi da altri servizi

Vedere quindi come compilare la funzione di base di Azure per inserire i dati dell'hub Internet in dispositivi gemelli digitali di Azure:
* [*Procedura: inserire dati di telemetria dall'hub Internet*](how-to-ingest-iot-hub-data.md)
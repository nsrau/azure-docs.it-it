---
title: Uso del servizio Web Gestione modelli di Azure Machine Learning | Microsoft Docs
description: Questo documento descrive la procedura e i concetti coinvolti nell'uso dei servizi Web distribuiti usando la Gestione modelli in Azure Machine Learning.
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: fc4d793bcf402a3e742fae2b2c0052f2d60d1b47
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="consuming-web-services"></a>Uso dei servizi Web
Dopo aver distribuito un modello come un servizio Web in tempo reale è possibile inviare dati e ottenere stime da un'ampia gamma di piattaforme e applicazioni. Il servizio web in tempo reale espone un'API REST per l'ottenimento delle stime. È possibile inviare dati al servizio Web nel formato di riga singola o multipla per ottenere una o più stime contemporaneamente.

Con il servizio Web di Azure Machine Learning un'applicazione esterna comunica con un modello predittivo in modo sincrono effettuando una chiamata HTTP POST all'URL del servizio. Per chiamare un servizio Web, l'applicazione client deve specificare la chiave API che viene creata quando si distribuisce una stima e si inseriscono i dati della richiesta nel corpo della richiesta POST.

Si noti che le chiavi API sono disponibili solo in modalità di distribuzione di cluster. I servizi Web locali non dispongono di chiavi.

## <a name="service-deployment-options"></a>Opzioni di distribuzione di servizi
I servizi Web di Machine Learning di Azure possono essere distribuiti ai cluster basati sul cloud per gli scenari di produzione e di test e alle workstation locali usando il motore Docker. La funzionalità del modello predittivo in entrambi i casi rimane invariata. La distribuzione basata su cluster fornisce una soluzione scalabile ed efficiente basata sui servizi contenitore di Azure mentre la distribuzione locale può essere usata per il debug. 

L'interfaccia della riga di comando di Azure Machine Learning e l'API forniscono comandi utili per la creazione e la gestione di ambienti di calcolo per le distribuzioni dei servizi usando l'opzione ```az ml env```. 

## <a name="list-deployed-services-and-images"></a>Elenco dei servizi e delle immagini distribuite
È possibile elencare i servizi e le immagini Docker attualmente distribuiti usando il comando ```az ml service list realtime -o table``` dell'interfaccia della riga di comando. Si noti che questo comando opera sempre nel contesto dell'ambiente di calcolo corrente e non visualizza i servizi distribuiti in un ambiente che non è impostato come ambiente corrente. Per impostare l'ambiente usare ```az ml env set```. 

## <a name="get-service-information"></a>Ottenere informazioni sul servizio
Dopo che il servizio Web è stato distribuito correttamente, usare il comando seguente per ottenere l'URL del servizio e altri dettagli per chiamare l'endpoint di servizio. 

```
az ml service usage realtime -i <service name>
```

Questo comando stampa l'URL del servizio, le intestazioni di richiesta necessarie, l'URL Swagger e i dati di esempio per chiamare il servizio se lo schema API del servizio è stato fornito in fase di distribuzione.

È possibile testare il servizio direttamente dall'interfaccia della riga di comando senza la composizione di una richiesta HTTP immettendo il comando dell'interfaccia della riga di comando di esempio con i dati di input:

```
az ml service run realtime -i <service name> -d "Your input data"
```

## <a name="get-the-service-api-key"></a>Ottenere la chiave API del servizio
Per ottenere la chiave del servizio Web, usare il comando seguente:

```
az ml service keys realtime -i <web service id>
```
Quando si crea una richiesta HTTP, usare la chiave nell'intestazione di autorizzazione: "Authorization": "Bearer <key>"

## <a name="get-the-service-swagger-description"></a>Ottenere la descrizione del servizio Swagger
Se è stato fornito lo schema di API del servizio, l'endpoint di servizio espone un documento Swagger in ```http://<ip>/api/v1/service/<service name>/swagger.json```. Il documento Swagger può essere usato per generare automaticamente il client di servizio e analizzare i dati di input previsti e altri dettagli relativi al servizio.

## <a name="get-service-logs"></a>Ottenere i log del servizio
Per comprendere il comportamento del servizio e diagnosticare i problemi, esistono diversi modi per recuperare i log di servizio:
- Comando dell'interfaccia della riga di comando ```az ml service logs realtime -i <service id>```. Questo comando funziona sia nei cluster che in modalità locale.
- Se la registrazione del servizio è stata abilitata in fase di distribuzione anche i log dei servizi verranno inviati a AppInsight. Il comando dell'interfaccia della riga di comando ```az ml service usage realtime -i <service id>``` mostra l'URL di AppInsight. Si noti che i log di AppInsight possono essere ritardati da 2 a 5 minuti.
- I log dei cluster possono essere visualizzati attraverso la console Kubernetes connessa quando si imposta l'ambiente cluster corrente usando ```az ml env set```
- I log dei Docker locali sono disponibili tramite i log del motore Docker quando il servizio è in esecuzione localmente.

## <a name="call-the-service-using-c"></a>Chiamare il servizio usando il linguaggio C#
Usare l'URL del servizio per inviare una richiesta da un'applicazione Console C#. 

1. In Visual Studio creare una nuova applicazione Console: 
    * Nel menu fare clic su File > Nuovo > Progetto
    * In Visual Studio C#, fare clic su Windows Class Desktop (Desktop Windows Class) e selezionare l'applicazione Console.
2. Immettere _MyFirstService_ come nome del progetto e fare clic su OK.
3. In Riferimenti al progetto impostare i riferimenti su _System.Net_ e _System.Net.Http_.
4. Fare clic su Strumenti -> Gestione pacchetti NuGet -> Console di gestione pacchetti, quindi installare il pacchetto Microsoft.AspNet.WebApi.Client.
5. Aprire il file Program.cs e sostituire il codice con il codice seguente:
6. Aggiornare i parametri _SERVICE_URL_ e _API_KEY_ parametri con le informazioni del servizio Web.
7. Eseguire il progetto.

```csharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MyFirstService
{
    class Program
    {
        // Web Service URL (update it with your service url)
        private const string SERVICE_URL = "http://<service ip address>:80/api/v1/service/<service name>/score";
        private const string API_KEY = "your service key";

        static void Main(string[] args)
        {
            Program.PostRequest();
            Console.ReadLine();
        }

        private static void PostRequest()
        {
            HttpClient client = new HttpClient();
            client.BaseAddress = new Uri(SERVICE_URL);
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", API_KEY);

            var inputJson = new List<RequestPayload>();
            RequestPayload payload = new RequestPayload();
            List<InputDf> inputDf = new List<InputDf>();
            inputDf.Add(new InputDf()
            {
                feature1 = value1,
                feature2 = value2,
            });
            payload.Input_df_list = inputDf;
            inputJson.Add(payload);

            try
            {
                var request = new HttpRequestMessage(HttpMethod.Post, string.Empty);
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;

                Console.Write(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
        public class InputDf
        {
            public double feature1 { get; set; }
            public double feature2 { get; set; }
        }
        public class RequestPayload
        {
            [JsonProperty("input_df")]
            public List<InputDf> Input_df_list { get; set; }
        }
    }
}
```

## <a name="call-the-web-service-using-python"></a>Chiamare il servizio Web usando Python
Usare Python per inviare una richiesta al servizio Web in tempo reale. 

1. Copiare l'esempio di codice seguente in un nuovo file di Python.
2. Aggiornare i parametri dati, url e api_key
3. Eseguire il codice. 

```python
import requests
import json

data = "{\"input_df\": [{\"feature1\": value1, \"feature2\": value2}]}"
body = str.encode(json.dumps(data))

url = 'http://<service ip address>:80/api/v1/service/<service name>/score'
api_key = 'your service key' 
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

resp = requests.post(url, data, headers=headers)
resp.text
```

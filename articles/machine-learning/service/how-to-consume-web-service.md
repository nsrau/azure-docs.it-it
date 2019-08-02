---
title: Creare client per utilizzare un servizio Web distribuito
titleSuffix: Azure Machine Learning service
description: Informazioni su come utilizzare un servizio Web generato al momento della distribuzione di un modello con il modello di Azure Machine Learning. Il servizio Web espone un'API REST. Creare client per questa API usando il linguaggio di programmazione preferito.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 7799b62b2c330610663e361bbb3930340b1ebdaf
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726291"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>Come usare un modello di Azure Machine Learning distribuito come servizio Web

Quando si distribuisce un modello di Azure Machine Learning come servizio Web, viene creata un’API REST. È possibile inviare dati a questa API per ottenere la stima restituita dal modello. Questo documento illustra come creare client per il servizio Web usando C#, Go, Java e Python.

Si crea un servizio Web quando si distribuisce un'immagine in istanze di contenitore di Azure, servizio Azure Kubernetes o FPGA (Field-Programmable Gate Array). Le immagini vengono create a partire dai modelli registrati e dai file di assegnazione di punteggio. L'URI usato per accedere a un servizio Web viene recuperato tramite l'[SDK di Azure Machine Learning](https://aka.ms/aml-sdk). Se è abilitata l'autenticazione, è anche possibile usare l’SDK per ottenere le chiavi di autenticazione.

Il flusso di lavoro generale per creare un client che usa un servizio Web di Machine Learning è il seguente:

1. Ottenere le informazioni di connessione usando l'SDK.
1. Determinare il tipo di dati della richiesta usati dal modello.
1. Creare un'applicazione che chiama il servizio Web.

## <a name="connection-information"></a>Informazioni di connessione

> [!NOTE]
> Usare l'SDK di Azure Machine Learning per ottenere le informazioni sul servizio Web. Si tratta di un SDK per Python. È possibile usare qualsiasi linguaggio per creare un client per il servizio.

La classe [azureml.core.Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) fornisce le informazioni necessarie per creare un client. Per la creazione di un'applicazione client sono utili le proprietà `Webservice` seguenti:

* `auth_enabled`-Se è abilitata `True`l'autenticazione della chiave; in caso contrario,. `False`
* `token_auth_enabled`-Se è abilitata `True`l'autenticazione del token; in caso contrario,. `False`
* `scoring_uri` - L'indirizzo dell'API REST.


Sono disponibili tre modi per recuperare queste informazioni per servizi Web distribuiti:

* Quando si distribuisce un modello, viene restituito un `Webservice` oggetto con informazioni sul servizio:

    ```python
    service = Webservice.deploy_from_model(name='myservice',
                                           deployment_config=myconfig,
                                           models=[model],
                                           image_config=image_config,
                                           workspace=ws)
    print(service.scoring_uri)
    ```

* È possibile usare `Webservice.list` per recuperare un elenco dei servizi Web distribuiti per i modelli presenti nella propria area di lavoro. È possibile aggiungere filtri per restringere l'elenco delle informazioni restituite. Per altre informazioni sui dati che è possibile filtrare, vedere la documentazione di riferimento di [Webservice.list](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py).

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    ```

* Se si conosce il nome del servizio distribuito, è possibile creare una nuova istanza di `Webservice` e specificare il nome dell'area di lavoro e del servizio come parametri. Il nuovo oggetto contiene informazioni sul servizio distribuito.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    ```

### <a name="authentication-for-services"></a>Autenticazione per i servizi

Azure Machine Learning offre due modi per controllare l'accesso ai servizi Web. 

|Metodo di autenticazione|ACI|Servizio Azure Kubernetes|
|---|---|---|
|Chiave|Disabilitato per impostazione predefinita| Abilitato per impostazione predefinita|
|Token| Non disponibile| Disabilitato per impostazione predefinita |
#### <a name="authentication-with-keys"></a>Autenticazione con chiavi

Quando si abilita l'autenticazione per una distribuzione, si creano automaticamente le chiavi di autenticazione.

* L'autenticazione è abilitata per impostazione predefinita quando si esegue la distribuzione nel servizio Azure Kubernetes.
* L'autenticazione è disabilitata per impostazione predefinita quando si esegue la distribuzione in Istanze di Azure Container.

Per controllare l'autenticazione, usare il parametro `auth_enabled` quando si crea o si aggiorna una distribuzione.

Se è abilitata l'autenticazione, è possibile usare il metodo `get_keys` per recuperare una chiave di autenticazione primaria e una secondaria:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se è necessario rigenerare una chiave, usare [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).


#### <a name="authentication-with-tokens"></a>Autenticazione con token

Quando si Abilita l'autenticazione basata su token per un servizio Web, è necessario che un utente fornisca al servizio Web un token di Azure Machine Learning JWT per accedervi. 

* Per impostazione predefinita, l'autenticazione del token è disabilitata quando si esegue la distribuzione nel servizio Azure Kubernetes.
* L'autenticazione basata su token non è supportata quando si esegue la distribuzione in istanze di contenitore di Azure.

Per controllare l'autenticazione del token, `token_auth_enabled` usare il parametro durante la creazione o l'aggiornamento di una distribuzione.

Se l'autenticazione basata su token è abilitata, `get_token` è possibile usare il metodo per recuperare una Bearer token e l'ora di scadenza dei token:

```python
token, refresh_by = service.get_tokens()
print(token)
```

> [!IMPORTANT]
> Sarà necessario richiedere un nuovo token dopo l' `refresh_by` ora del token. 

## <a name="request-data"></a>Dati richiesta

L'API REST prevede che il corpo della richiesta sia un documento JSON con la struttura seguente:

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> La struttura dei dati deve corrispondere allo script di punteggio e al modello nelle stime del servizio. Lo script di punteggio può modificare i dati prima di trasferirli al modello.

Ad esempio, nel modello dell’esempio [Eseguire il training sul notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb) è prevista una matrice di 10 numeri. Lo script di assegnazione dei punteggi per questo esempio crea una matrice Numpy dalla richiesta e la passa al modello. Nell'esempio seguente sono visualizzati i dati che questo servizio prevede:

```json
{
    "data": 
        [
            [
                0.0199132141783263, 
                0.0506801187398187, 
                0.104808689473925, 
                0.0700725447072635, 
                -0.0359677812752396, 
                -0.0266789028311707, 
                -0.0249926566315915, 
                -0.00259226199818282, 
                0.00371173823343597, 
                0.0403433716478807
            ]
        ]
}
``` 

Il servizio Web può accettare più set di dati in un'unica richiesta. Restituisce un documento JSON contenente una matrice di risposte.

### <a name="binary-data"></a>Dati binari

Se il modello accetta dati binari, ad esempio un'immagine, è necessario modificare il file `score.py` usato per la distribuzione in modo da accettare richieste HTTP non elaborate. Di seguito è riportato un esempio `score.py` di un oggetto che accetta dati binari:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Lo spazio dei nomi `azureml.contrib` cambia di frequente perché Microsoft è attualmente impegnata a migliorare il servizio. Qualsiasi elemento in questo spazio dei nomi deve essere pertanto considerato come anteprima e non è completamente supportato da Microsoft.
>
> Se è necessario eseguire un test nell'ambiente di sviluppo locale, è possibile installare i componenti nello spazio dei nomi `contrib` usando il comando seguente:
> 
> ```shell
> pip install azureml-contrib-services
> ```

## <a name="call-the-service-c"></a>Chiamare il servizio (C#)

In questo esempio viene illustrato come utilizzare C# per chiamare il servizio Web creato dall’esempio [Eseguire il training sul notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb):

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key
            string scoringUri = "<your web service URI>";
            string authKey = "<your key>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

I risultati restituiti sono simili al seguente documento JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>Chiamare il servizio (Go)

In questo esempio viene illustrato come utilizzare Go per chiamare il servizio Web creato dall’esempio [Eseguire il training sul notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb):

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key (if any) for your service
var authKey string = "<your key>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

I risultati restituiti sono simili al seguente documento JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>Chiamare il servizio (Java)

In questo esempio viene illustrato come utilizzare Java per chiamare il servizio Web creato dall’esempio [Eseguire il training sul notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb):

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key
        String key = "<your key>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

I risultati restituiti sono simili al seguente documento JSON:

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>Chiamare il servizio (Python)

In questo esempio viene illustrato come utilizzare Python per chiamare il servizio Web creato dall’esempio [Eseguire il training sul notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb):

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key
key = '<your key>'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [
                0.0199132141783263,
                0.0506801187398187,
                0.104808689473925,
                0.0700725447072635,
                -0.0359677812752396,
                -0.0266789028311707,
                -0.0249926566315915,
                -0.00259226199818282,
                0.00371173823343597,
                0.0403433716478807
            ],
            [
                -0.0127796318808497,
                -0.044641636506989,
                0.0606183944448076,
                0.0528581912385822,
                0.0479653430750293,
                0.0293746718291555,
                -0.0176293810234174,
                0.0343088588777263,
                0.0702112981933102,
                0.00720651632920303]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

I risultati restituiti sono simili al seguente documento JSON:

```JSON
[217.67978776218715, 224.78937091757172]
```

## <a name="consume-the-service-from-power-bi"></a>Utilizzare il servizio da Power BI

Power BI supporta l'utilizzo di servizi Web Azure Machine Learning per arricchire i dati in Power BI con le stime. 

Per generare un servizio Web supportato per l'utilizzo in Power BI, lo schema deve supportare il formato richiesto da Power BI. [Informazioni su come creare uno schema supportato da Power bi](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#example-script-with-dictionary-input-support-consumption-from-power-bi).

Una volta distribuito, il servizio Web può essere utilizzato dai flussi di Power BI. [Informazioni su come utilizzare un servizio web Azure Machine Learning da Power bi](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

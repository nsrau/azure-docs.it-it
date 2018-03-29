---
title: Come usare un servizio Web di Azure Machine Learning | Microsoft Docs
description: Dopo la pubblicazione di un servizio di Machine Learning, è possibile usare il servizio Web RESTFul che viene reso disponibile come servizio di richiesta-risposta o come un servizio di esecuzione del batch.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 804f8211-9437-4982-98e9-ca841b7edf56
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/02/2017
ms.openlocfilehash: efb23500d8c718ff1dc7b59c4cfb2b2d5cdfaccb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-consume-an-azure-machine-learning-web-service"></a>Come usare un servizio Web di Azure Machine Learning

Dopo aver distribuito un modello predittivo di Azure Machine Learning come servizio Web, è possibile usare un'API REST per inviare dati e ottenere stime. È possibile inviare i dati in tempo reale o in modalità batch.

Per informazioni su come creare e distribuire un servizio Web di Machine Learning tramite Machine Learning Studio:

* Per un'esercitazione su come creare un esperimento in Machine Learning Studio, vedere l'articolo su come [creare il primo esperimento](create-experiment.md).
* Per dettagli su come distribuire un servizio Web, vedere [Distribuire un servizio Web di Azure Machine Learning](publish-a-machine-learning-web-service.md).
* Per altre informazioni su Machine Learning in generale, accedere alla [Documentazione su Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="overview"></a>Panoramica
Con il servizio Web di Azure Machine Learning, un'applicazione esterna comunica con un modello di valutazione del flusso di lavoro di Machine Learning in tempo reale. Una chiamata al servizio Web di Machine Learning restituisce i risultati della stima a un'applicazione esterna. Per effettuare una chiamata al servizio Web di Machine Learning, passare una chiave API creata quando si distribuisce una stima. Il servizio Web di Machine Learning è basato su REST, una scelta di architettura diffusa per progetti di programmazione Web.

Azure Machine Learning dispone di due tipi di servizi:

* Servizio di richiesta-risposta (RRS). Un servizio a latenza bassa e altamente scalabile che offre un'interfaccia ai modelli senza stato creati e distribuiti da Machine Learning Studio.
* Servizio esecuzione batch (BES). Un servizio asincrono che valuta un batch di record di dati.

Per altre informazioni sui servizi Web di Machine Learning, vedere [Distribuire un servizio Web di Azure Machine Learning](publish-a-machine-learning-web-service.md).

## <a name="get-an-azure-machine-learning-authorization-key"></a>Ottenere una chiave di autorizzazione Azure Machine Learning
Quando si distribuisce l'esperimento, vengono generate le chiavi API per il servizio Web, recuperabili da diverse posizioni.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Dal portale dei servizi Web di Microsoft Azure Machine Learning
Accedere al portale dei [servizi Web di Microsoft Azure Machine Learning](https://services.azureml.net).

Per recuperare la chiave API per un nuovo servizio Web Machine Learning:

1. Nel portale Servizi Web di Machine Learning di Azure, fare clic sul menu **Web Services** (Servizi Web) nel menu in alto.
2. Selezionare il servizio Web per il quale si desidera recuperare la chiave.
3. Nel menu in alto fare clic su **Consume**(Uso).
4. Copiare e salvare la **Chiave primaria**.

Per recuperare la chiave API per un nuovo servizio Web Machine Learning di tipo classico:

1. Nel portale Servizi Web di Machine Learning di Azure, fare clic sul menu **Classic Web Services** (Servizi Web classici) nel menu in alto.
2. Fare clic sul servizio Web in uso.
3. Selezionare l'endpoint per il quale si desidera recuperare la chiave.
4. Nel menu in alto fare clic su **Consume**(Uso).
5. Copiare e salvare la **Chiave primaria**.

### <a name="classic-web-service"></a>Servizio Web classico
 La chiave di un servizio Web di tipo classico può essere recuperata anche da Machine Learning Studio.

#### <a name="machine-learning-studio"></a>Machine Learning Studio
1. In Machine Learning Studio fare clic su **WEB SERVICES** (Servizi Web) a sinistra.
2. Fare clic su un servizio Web. La **chiave API** si trova nella scheda **DASHBOARD**.

## <a id="connect"></a>Connettersi a un servizio Web di Machine Learning
È possibile connettersi a un servizio Web di Machine Learning usando qualsiasi linguaggio di programmazione che supporta la risposta e la richiesta HTTP. È possibile visualizzare gli esempi in C#, Python e R da una pagina della guida del servizio Web di Machine Learning.

**Guida alle API di Machine Learning** Una Guida per l'API di Machine Learning viene creata quando si distribuisce un servizio Web. Vedere [Procedura dettagliata di Azure Machine Learning - Distribuire il servizio Web](walkthrough-5-publish-web-service.md).
La Guida per l'API di Machine Learning contiene i dettagli su un servizio Web di stima.

1. Fare clic sul servizio Web in uso.
2. Selezionare l'endpoint per il quale si desidera visualizzare la pagina della guida alle API.
3. Nel menu in alto fare clic su **Consume**(Uso).
4. Fare clic sulla pagina della **guida alle API** negli endpoint Request-Response o Esecuzione batch.

**Per visualizzare la guida alle API di Machine Learning per un nuovo servizio Web**

Nel [portale dei servizi Web di Azure Machine Learning](https://services.azureml.net/):

1. Fare clic su **WEB SERVICES** (Servizi Web) nel menu in alto.
2. Selezionare il servizio Web per il quale si desidera recuperare la chiave.

Fare clic su **Use Web Service** (Usa servizio Web) per ottenere l'URI per i servizi Richiesta/Risposta ed Esecuzione in batch, nonché il codice di esempio in C#, R e Python.

Fare clic su **Swagger API** (API Swagger) per ottenere la documentazione basata su Swagger per le API chiamate dagli URI specificati.

### <a name="c-sample"></a>Esempio C#
Per connettersi a un servizio Web di Machine Learning, usare un **HttpClient** che passa ScoreData. ScoreData contiene FeatureVector, un vettore n-dimensionale di funzioni numeriche che rappresentano ScoreData. Effettuare l'autenticazione al servizio di Machine Learning con una chiave API.

Per connettersi a un servizio Web di Machine Learning, è necessario installare il pacchetto NuGet **Microsoft.AspNet.WebApi.Client** .

**Installare il Nuget Microsoft.AspNet.WebApi.Client in Visual Studio**

1. Pubblicare il set di dati di download dal servizio Web UCI: Adult 2 class dataset.
2. Fare clic su **Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.
3. Scegliere **Install-Package Microsoft.AspNet.WebApi.Client**.

**Per eseguire l'esempio di codice**

1. Pubblicare l'esperimento "Sample 1: Download dataset from UCI: Adult 2 class dataset", che fa parte della raccolta di esempi di Machine Learning.
2. Assegnare ad apiKey la chiave di un servizio Web. Vedere la sezione precedente **Ottenere una chiave di autorizzazione di Azure Machine Learning** .
3. Assegnare l'URI del servizio con l'URI della richiesta.

**Di seguito è riportato l'aspetto di una richiesta completa.**
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Formatting;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

namespace CallRequestResponseService
{
    class Program
    {
        static void Main(string[] args)
        {
            InvokeRequestResponseService().Wait();
        }

        static async Task InvokeRequestResponseService()
        {
            using (var client = new HttpClient())
            {
                var scoreRequest = new
                {
                    Inputs = new Dictionary<string, List<Dictionary<string, string>>> () {
                        {
                            "input1",
                            // Replace columns labels with those used in your dataset
                            new List<Dictionary<string, string>>(){new Dictionary<string, string>(){
                                    {
                                        "column1", "value1"
                                    },
                                    {
                                        "column2", "value2"
                                    },
                                    {
                                        "column3", "value3"
                                    }
                                }
                            }
                        },
                    },
                    GlobalParameters = new Dictionary<string, string>() {}
                };

                // Replace these values with your API key and URI found on https://services.azureml.net/
                const string apiKey = "<your-api-key>"; 
                const string apiUri = "<your-api-uri>";
                
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
                client.BaseAddress = new Uri(apiUri);

                // WARNING: The 'await' statement below can result in a deadlock
                // if you are calling this code from the UI thread of an ASP.Net application.
                // One way to address this would be to call ConfigureAwait(false)
                // so that the execution does not attempt to resume on the original context.
                // For instance, replace code such as:
                //      result = await DoSomeTask()
                // with the following:
                //      result = await DoSomeTask().ConfigureAwait(false)

                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                if (response.IsSuccessStatusCode)
                {
                    string result = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("Result: {0}", result);
                }
                else
                {
                    Console.WriteLine(string.Format("The request failed with status code: {0}", response.StatusCode));

                    // Print the headers - they include the requert ID and the timestamp,
                    // which are useful for debugging the failure
                    Console.WriteLine(response.Headers.ToString());

                    string responseContent = await response.Content.ReadAsStringAsync();
                    Console.WriteLine(responseContent);
                }
            }
        }
    }
}
```

### <a name="python-sample"></a>Esempio Python
Per connettersi a un servizio Web di Machine Learning, usare la libreria **urllib2** libreria per Python 2.X e la libreria **urllib.request** per Python 3.X. Si passerà a ScoreData, che contiene FeatureVector, un vettore n-dimensionale di funzioni numeriche che rappresentano ScoreData. Effettuare l'autenticazione al servizio di Machine Learning con una chiave API.

**Per eseguire l'esempio di codice**

1. Distribuire l'esperimento "Sample 1: Download dataset from UCI: Adult 2 class dataset", che fa parte della raccolta di esempi di Machine Learning.
2. Assegnare ad apiKey la chiave di un servizio Web. Vedere la sezione **Ottenere una chiave di autorizzazione Azure Machine Learning** all'inizio di questo articolo.
3. Assegnare l'URI del servizio con l'URI della richiesta.

**Di seguito è riportato l'aspetto di una richiesta completa.**
```python
import urllib2 # urllib.request for Python 3.X
import json

data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",   
                'column2': "value2",   
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters":  {}
}

body = str.encode(json.dumps(data))

# Replace this with the URI and API Key for your web service
url = '<your-api-uri>'
api_key = '<your-api-key>'
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

# "urllib.request.Request(uri, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the requert ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>Esempio R

Per connettersi a un servizio Web di Machine Learning, usare le librerie **RCurl** e **rjson** per eseguire la richiesta ed elaborare la risposta JSON restituita. Si passerà a ScoreData, che contiene FeatureVector, un vettore n-dimensionale di funzioni numeriche che rappresentano ScoreData. Effettuare l'autenticazione al servizio di Machine Learning con una chiave API.

**Di seguito è riportato l'aspetto di una richiesta completa.**
```r
library("RCurl")
library("rjson")

# Accept SSL certificates issued by public Certificate Authorities
options(RCurlOptions = list(cainfo = system.file("CurlSSL", "cacert.pem", package = "RCurl")))

h = basicTextGatherer()
hdr = basicHeaderGatherer()

req = list(
    Inputs = list(
            "input1" = list(
                list(
                        'column1' = "value1",
                        'column2' = "value2",
                        'column3' = "value3"
                    )
            )
        ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "<your-api-key>" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

h$reset()
curlPerform(url = "<your-api-uri>",
httpheader=c('Content-Type' = "application/json", 'Authorization' = authz_hdr),
postfields=body,
writefunction = h$update,
headerfunction = hdr$update,
verbose = TRUE
)

headers = hdr$value()
httpStatus = headers["status"]
if (httpStatus >= 400)
{
print(paste("The request failed with status code:", httpStatus, sep=" "))

# Print the headers - they include the requert ID and the timestamp, which are useful for debugging the failure
print(headers)
}

print("Result:")
result = h$value()
print(fromJSON(result))
```

### <a name="javascript-sample"></a>Esempio di JavaScript

Per connettersi a un servizio Web di Machine Learning, usare il pacchetto npm **request** nel progetto. Si userà anche l'oggetto `JSON` per formattare l'input e analizzare il risultato. Installare usando `npm install request --save`, o aggiungere `"request": "*"` al file package.json in `dependencies` ed eseguire `npm install`.

**Di seguito è riportato l'aspetto di una richiesta completa.**
```js
let req = require("request");

const uri = "<your-api-uri>";
const apiKey = "<your-api-key>";

let data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",
                'column2': "value2",
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters": {}
}

const options = {
    uri: uri,
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + apiKey,
    },
    body: JSON.stringify(data)
}

req(options, (err, res, body) => {
    if (!err && res.statusCode == 200) {
        console.log(body);
    } else {
        console.log("The request failed with status code: " + res.statusCode);
    }
});
```
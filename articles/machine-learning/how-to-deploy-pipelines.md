---
title: Pubblicare pipeline ML
titleSuffix: Azure Machine Learning
description: Esegui i flussi di lavoro di Machine Learning con le pipeline di machine learning e il Azure Machine Learning SDK per Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: laobri
author: lobrien
ms.date: 8/25/2020
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.openlocfilehash: ae20b339ae3eec694140621b14db26606c9d5ab3
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145566"
---
# <a name="publish-and-track-machine-learning-pipelines"></a>Pubblicare e monitorare le pipeline di Machine Learning



Questo articolo illustra come condividere una pipeline di Machine Learning con i colleghi o i clienti.

Le pipeline di Machine Learning sono flussi di lavoro riutilizzabili per le attività di machine learning. Un vantaggio offerto dalle pipeline è una maggiore collaborazione. È anche possibile eseguire la versione delle pipeline, consentendo ai clienti di usare il modello corrente mentre si lavora a una nuova versione. 

## <a name="prerequisites"></a>Prerequisiti

* Creare un' [area di lavoro di Azure Machine Learning](how-to-manage-workspace.md) per conservare tutte le risorse della pipeline

* [Configurare l'ambiente di sviluppo](how-to-configure-environment.md) per l'installazione di Azure Machine Learning SDK oppure usare un' [istanza di calcolo Azure Machine Learning](concept-compute-instance.md) con l'SDK già installato

* Creare ed eseguire una pipeline di Machine Learning, ad esempio [l'esercitazione seguente: creare una pipeline di Azure Machine Learning per il Punteggio batch](tutorial-pipeline-batch-scoring-classification.md). Per altre opzioni, vedere [creare ed eseguire pipeline di Machine Learning con Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)

## <a name="publish-a-pipeline"></a>Pubblicare una pipeline

Una volta che la pipeline è in esecuzione, è possibile pubblicare una pipeline in modo che venga eseguita con input diversi. Affinché l'endpoint REST di una pipeline già pubblicata accetti parametri, è necessario configurare la pipeline in modo `PipelineParameter` che usi oggetti per gli argomenti che variano.

1. Per creare un parametro per la pipeline, usare un oggetto [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py&preserve-view=true) con un valore predefinito.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Aggiungere l'oggetto `PipelineParameter` come parametro a uno dei passaggi della pipeline, come indicato di seguito:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Pubblicare la pipeline che accetterà un parametro quando richiamata.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

## <a name="run-a-published-pipeline"></a>Eseguire una pipeline pubblicata

Tutte le pipeline pubblicate hanno un endpoint REST Con l'endpoint della pipeline, è possibile attivare un'esecuzione della pipeline da qualsiasi sistema esterno, inclusi i client non Python. Questo endpoint consente la "ripetibilità gestita" in scenari di ripetizione del training e assegnazione di punteggi in batch.

> [!IMPORTANT]
> Se si usa il controllo degli accessi in base al ruolo (RBAC) per gestire l'accesso alla pipeline, [impostare le autorizzazioni per lo scenario della pipeline (training o assegnazione del punteggio)](how-to-assign-roles.md#q-what-are-the-permissions-needed-to-perform-some-common-scenarios-in-the-azure-machine-learning-service).

Per richiamare l'esecuzione della pipeline precedente, è necessario un token di intestazione di autenticazione Azure Active Directory. Il recupero di un token di questo tipo è descritto nel riferimento alla [classe AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py&preserve-view=true) e nell' [autenticazione in Azure Machine Learning](https://aka.ms/pl-restep-auth) notebook.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

L' `json` argomento della richiesta post deve contenere, per la `ParameterAssignments` chiave, un dizionario contenente i parametri della pipeline e i relativi valori. Inoltre, l' `json` argomento può contenere le chiavi seguenti:

| Chiave | Descrizione |
| --- | --- | 
| `ExperimentName` | Nome dell'esperimento associato a questo endpoint |
| `Description` | Testo a mano libera che descrive l'endpoint | 
| `Tags` | Coppie chiave-valore FreeForm che possono essere usate per etichettare e annotare le richieste  |
| `DataSetDefinitionValueAssignments` | Dizionario usato per la modifica dei set di impostazioni senza ripetizione del training (vedere la discussione riportata di seguito) | 
| `DataPathAssignments` | Dizionario usato per la modifica dei percorsi di DataPath senza ripetizione del training (vedere la discussione riportata di seguito) | 

### <a name="run-a-published-pipeline-using-c"></a>Eseguire una pipeline pubblicata con C # 

Il codice seguente illustra come chiamare una pipeline in modo asincrono da C#. Il frammento di codice parziale Mostra solo la struttura di chiamata e non fa parte di un esempio Microsoft. Non Mostra le classi complete o la gestione degli errori. 

```csharp
[DataContract]
public class SubmitPipelineRunRequest
{
    [DataMember]
    public string ExperimentName { get; set; }

    [DataMember]
    public string Description { get; set; }

    [DataMember(IsRequired = false)]
    public IDictionary<string, string> ParameterAssignments { get; set; }
}

// ... in its own class and method ... 
const string RestEndpoint = "your-pipeline-endpoint";

using (HttpClient client = new HttpClient())
{
    var submitPipelineRunRequest = new SubmitPipelineRunRequest()
    {
        ExperimentName = "YourExperimentName", 
        Description = "Asynchronous C# REST api call", 
        ParameterAssignments = new Dictionary<string, string>
        {
            {
                // Replace with your pipeline parameter keys and values
                "your-pipeline-parameter", "default-value"
            }
        }
    };

    string auth_key = "your-auth-key"; 
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", auth_key);

    // submit the job
    var requestPayload = JsonConvert.SerializeObject(submitPipelineRunRequest);
    var httpContent = new StringContent(requestPayload, Encoding.UTF8, "application/json");
    var submitResponse = await client.PostAsync(RestEndpoint, httpContent).ConfigureAwait(false);
    if (!submitResponse.IsSuccessStatusCode)
    {
        await WriteFailedResponse(submitResponse); // ... method not shown ...
        return;
    }

    var result = await submitResponse.Content.ReadAsStringAsync().ConfigureAwait(false);
    var obj = JObject.Parse(result);
    // ... use `obj` dictionary to access results
}
```

### <a name="run-a-published-pipeline-using-java"></a>Eseguire una pipeline pubblicata con Java

Il codice seguente illustra una chiamata a una pipeline che richiede l'autenticazione (vedere [configurare l'autenticazione per risorse e flussi di lavoro Azure Machine Learning](how-to-setup-authentication.md)). Se la pipeline viene distribuita pubblicamente, non sono necessarie le chiamate che producono `authKey` . Il frammento di codice parziale non Mostra la classe Java e lo standard di gestione delle eccezioni. Il codice usa `Optional.flatMap` per concatenare le funzioni che possono restituire un oggetto vuoto `Optional` . L'uso di `flatMap` abbrevia e chiarisce il codice, ma si noti che le `getRequestBody()` eccezioni vengono inghiottite.

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.Optional;
// JSON library
import com.google.gson.Gson;

String scoringUri = "scoring-endpoint";
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";
String dataToBeScored = "{ \"ExperimentName\" : \"My_Pipeline\", \"ParameterAssignments\" : { \"pipeline_arg\" : \"20\" }}";

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);
Optional<String> authBody = getRequestBody(client, tokenAuthenticationRequest);
Optional<String> authKey = authBody.flatMap(body -> Optional.of(gson.fromJson(body, AuthenticationBody.class).access_token);;
Optional<HttpRequest> scoringRequest = authKey.flatMap(key -> Optional.of(scoringRequest(key, scoringUri, dataToBeScored)));
Optional<String> scoringResult = scoringRequest.flatMap(req -> getRequestBody(client, req));
// ... etc (`scoringResult.orElse()`) ... 

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl)
{
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create(authUrl))
        .POST(HttpRequest.BodyPublishers.ofString(bodyString))
        .build();
    return request;
}

static HttpRequest scoringRequest(String authKey, String scoringUri, String dataToBeScored)
{
    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create(scoringUri))
        .header("Authorization", String.format("Token %s", authKey))
        .POST(HttpRequest.BodyPublishers.ofString(dataToBeScored))
        .build();
    return request;

}

static Optional<String> getRequestBody(HttpClient client, HttpRequest request) {
    try {
        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
        if (response.statusCode() != 200) {
            System.out.println(String.format("Unexpected server response %d", response.statusCode()));
            return Optional.empty();
        }
        return Optional.of(response.body());
    }catch(Exception x)
    {
        System.out.println(x.toString());
        return Optional.empty();
    }
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

### <a name="changing-datasets-and-datapaths-without-retraining"></a>Modifica di DataSet e percorsi di DataPath senza ripetizione del training

Potrebbe essere necessario eseguire il training e l'inferenza su set di impostazioni e percorsi di DataSet diversi. È ad esempio possibile che si desideri eseguire il training su un set di dati più piccolo, ma inferenza sul set di dati completo. I set di impostazioni vengono passati con la `DataSetDefinitionValueAssignments` chiave nell'argomento della richiesta `json` . È possibile cambiare i percorsi di DataPath con `DataPathAssignments` . La tecnica per entrambi è simile:

1. Nello script di definizione della pipeline creare un oggetto `PipelineParameter` per il set di dati. Creare un oggetto `DatasetConsumptionConfig` o `DataPath` da `PipelineParameter` :

    ```python
    tabular_dataset = Dataset.Tabular.from_delimited_files('https://dprepdata.blob.core.windows.net/demo/Titanic.csv')
    tabular_pipeline_param = PipelineParameter(name="tabular_ds_param", default_value=tabular_dataset)
    tabular_ds_consumption = DatasetConsumptionConfig("tabular_dataset", tabular_pipeline_param)
    ```

1. Nello script ML accedere al set di dati specificato in modo dinamico usando `Run.get_context().input_datasets` :

    ```python
    from azureml.core import Run
    
    input_tabular_ds = Run.get_context().input_datasets['tabular_dataset']
    dataframe = input_tabular_ds.to_pandas_dataframe()
    # ... etc ...
    ```

    Si noti che lo script ML accede al valore specificato per `DatasetConsumptionConfig` ( `tabular_dataset` ) e non al valore di `PipelineParameter` ( `tabular_ds_param` ).

1. Nello script di definizione della pipeline impostare `DatasetConsumptionConfig` come parametro su `PipelineScriptStep` :

    ```python
    train_step = PythonScriptStep(
        name="train_step",
        script_name="train_with_dataset.py",
        arguments=["--param1", tabular_ds_consumption],
        inputs=[tabular_ds_consumption],
        compute_target=compute_target,
        source_directory=source_directory)
    
    pipeline = Pipeline(workspace=ws, steps=[train_step])
    ```

1. Per cambiare i set di impostazioni in modo dinamico nella chiamata REST di inferenza, usare `DataSetDefinitionValueAssignments` :
    
    ```python
    tabular_ds1 = Dataset.Tabular.from_delimited_files('path_to_training_dataset')
    tabular_ds2 = Dataset.Tabular.from_delimited_files('path_to_inference_dataset')
    ds1_id = tabular_ds1.id
    d22_id = tabular_ds2.id
    
    response = requests.post(rest_endpoint, 
                             headers=aad_token, 
                             json={
                                "ExperimentName": "MyRestPipeline",
                               "DataSetDefinitionValueAssignments": {
                                    "tabular_ds_param": {
                                        "SavedDataSetReference": {"Id": ds1_id #or ds2_id
                                    }}}})
    ```

I notebook che [mostrano il set di dati e PipelineParameter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb) e la [presentazione di DataPath e PipelineParameter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) presentano esempi completi di questa tecnica.

## <a name="create-a-versioned-pipeline-endpoint"></a>Creare un endpoint della pipeline con versione

È possibile creare un endpoint della pipeline con più pipeline pubblicate. Questa tecnica fornisce un endpoint REST fisso durante l'iterazione e l'aggiornamento delle pipeline di ML.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

## <a name="submit-a-job-to-a-pipeline-endpoint"></a>Inviare un processo a un endpoint della pipeline

È possibile inviare un processo alla versione predefinita di un endpoint della pipeline:

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

È anche possibile inviare un processo a una versione specifica:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

La stessa operazione può essere eseguita usando l'API REST:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

## <a name="use-published-pipelines-in-the-studio"></a>Usare le pipeline pubblicate in studio

È anche possibile eseguire una pipeline pubblicata da studio:

1. Accedere ad [Azure Machine Learning Studio](https://ml.azure.com).

1. [Visualizzare l'area di lavoro](how-to-manage-workspace.md#view).

1. A sinistra selezionare **endpoint** .

1. Nella parte superiore selezionare **endpoint della pipeline** .
 ![elenco di pipeline pubblicate di Machine Learning](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Consente di selezionare una pipeline specifica per l'esecuzione, l'utilizzo o la verifica dei risultati delle esecuzioni precedenti dell'endpoint della pipeline.

## <a name="disable-a-published-pipeline"></a>Disabilitare una pipeline pubblicata

Per nascondere una pipeline dall'elenco di pipeline pubblicate, è possibile disabilitarla, in studio o nell'SDK:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

È possibile abilitarla di nuovo con `p.enable()` . Per altre informazioni, vedere riferimento alla [classe PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py&preserve-view=true) .

## <a name="next-steps"></a>Passaggi successivi

- Usare [questi notebook di Jupyter in GitHub](https://aka.ms/aml-pipeline-readme) per esplorare più in dettaglio le pipeline di Machine Learning.
- Vedere la Guida di riferimento per l'SDK per il pacchetto [azureml-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py&preserve-view=true) e il pacchetto [azureml-Pipelines-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py&preserve-view=true) .
- Vedere le [procedure](how-to-debug-pipelines.md) consigliate per il debug e la risoluzione dei problemi relativi alle pipeline.

---
title: Distribuire un modello come servizio Web in un FPGA con Azure Machine Learning
description: Informazioni su come distribuire un servizio Web con un modello in esecuzione in un FPGA con Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 09/24/2018
ms.openlocfilehash: ee67585a523ab96b1442d9eee3e9dfd55a758d32
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971485"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Distribuire un modello come servizio Web in un FPGA con Azure Machine Learning

È possibile distribuire un modello come servizio web in [Field programmable gate arrays (FPGA)](concept-accelerate-with-fpgas.md).  L'uso di FPGA offre inferenze a latenza estremamente bassa, anche con una singola dimensione batch.   

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Un'area di lavoro di Azure Machine Learning e Azure Machine Learning SDK per Python installata. Informazioni su come ottenere questi prerequisiti usando il documento [Come configurare un ambiente di sviluppo](how-to-configure-environment.md).
 
  - L'area di lavoro deve trovarsi nell'area degli *Stati Uniti orientali 2*.

  - Installare le funzionalità aggiuntive contrib:

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```  

## <a name="create-and-deploy-your-model"></a>Creare e distribuire un modello
Creare una pipeline per pre-elaborare l'immagine di input, trasformare utilizzando ResNet 50 in un FPGA e quindi eseguire le funzionalità attraverso una classificazione sottoposto con training sul set di dati ImageNet.

Seguire le istruzioni per:

* Definire il modello di pipeline
* Distribuire il modello
* Utilizzare il modello distribuito
* Eliminare i servizi distribuiti

> [!IMPORTANT]
> Per ottimizzare la latenza e la velocità effettiva, il client deve trovarsi nella stessa area di Azure in cui si trova l'endpoint.  Le API vengono attualmente create nell'area Stati Uniti orientali.

### <a name="get-the-notebook"></a>Ottenere il notebook

Per comodità, questa esercitazione è disponibile anche come notebook di Jupyter. Usare uno di questi metodi per eseguire il notebook `project-brainwave/project-brainwave-quickstart.ipynb`:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

### <a name="preprocess-image"></a>Pre-elaborare l'immagine
La prima fase della pipeline consiste nella pre-elaborazione delle immagini.

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```
### <a name="add-featurizer"></a>Aggiungere utilità di funzioni
Inizializzare il modello e scaricare un checkpoint TensorFlow della versione quantizzata ResNet50 da usare come utilità di funzioni.

```python
from azureml.contrib.brainwave.models import QuantizedResnet50, Resnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Aggiungere il classificatore
Questo classificatore è stato eseguito con training sul set di dati ImageNet.

```python
classifier_input, classifier_output = Resnet50.get_default_classifier(feature_tensor, model_path)
```

### <a name="create-service-definition"></a>Creare la definizione del servizio
Ora che avete definiti l'immagine di pre-elaborazione, l'utilità di funzioni e il classificatore eseguiti sul servizio, è possibile creare una definizione di servizio. La definizione del servizio è un set di file generato dal modello che viene distribuito nel servizio FPGA. La definizione del servizio è costituita da una pipeline. La pipeline è una serie di fasi che vengono eseguite in ordine.  Le fasi di TensorFlow, fasi Keras e fasi di BrainWave sono supportate.  Le fasi vengono eseguite nell'ordine nel servizio, con l'output di ogni input di fase in quello della fase successiva.

Per creare una fase TensorFlow, specificare una sessione che contiene il grafico (in questo caso viene utilizzato il grafico predefinito) e l'input e output di tensors per questa fase.  Queste informazioni consentono di salvare il grafico in modo che possa essere eseguito nel servizio.

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'service_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, classifier_input, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>Distribuzione del modello
Creare un servizio dalla definizione del servizio.  L'area di lavoro deve trovarsi nell'area degli Stati Uniti orientali 2.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(true)
```

### <a name="test-the-service"></a>Testare il servizio
Per inviare un'immagine per l'API e testare la risposta, aggiungere un mapping dall'ID di classe di output per il nome della classe ImageNet.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

Chiamare il servizio e sostituire il nome del file "your-Image. jpg" di seguito con un'immagine dal computer. 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>Pulizia del servizio
Eliminare il servizio Web.

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>Proteggere i servizi web FPGA

Azure Machine Learning eseguito su FPGA fornisce il supporto SSL e l'autenticazione basata su chiavi. In questo modo è possibile limitare l'accesso al servizio e proteggere i dati inviati dai client.

> [!IMPORTANT]
> L'autenticazione è abilitata solo per i servizi che forniscono un certificato SSL e una chiave. 
>
> Se non si abilita SSL, qualsiasi utente in Internet sarà in grado di effettuare chiamate al servizio.
>
> Se si abilita SSL è necessaria una chiave di autenticazione quando si accede al servizio.

SSL crittografa i dati scambiati tra il client e il servizio. Viene inoltre usato dal client per verificare l'identità del server.

È possibile distribuire un servizio con SSL abilitato oppure aggiornare un servizio già distribuito per abilitare SSL. La procedura è la stessa:

1. Acquisire un nome di dominio.

2. Acquisire un certificato SSL.

3. Distribuire o aggiornare il servizio con SSL abilitato.

4. Aggiornare il DNS in modo che punti al servizio.

### <a name="acquire-a-domain-name"></a>Acquisire un nome di dominio

Se non è già disponibile un nome di dominio, è possibile acquistarne uno da un __registrar__. La procedura e i costi variano a seconda del registrar. Il registrar fornisce anche gli strumenti per gestire il nome di dominio. Questi strumenti vengono usati per eseguire il mapping di un nome di dominio completo (ad esempio www.contoso.com) all'indirizzo IP che ospita il servizio.

### <a name="acquire-an-ssl-certificate"></a>Acquisire un certificato SSL

Esistono diversi modi per ottenere un certificato SSL. Il più comune consiste nell'acquistarne uno da un'__autorità di certificazione__ (CA). Indipendentemente da dove si ottiene il certificato, sono necessari i file seguenti:

* Un __certificato__. Il certificato deve contenere la catena di certificati completa e avere la codifica PEM.
* Una __chiave__. La chiave deve avere la codifica PEM.

> [!TIP]
> Se l'autorità di certificazione non può fornire il certificato e la chiave come file con codifica PEM, è possibile usare un'utilità come [OpenSSL](https://www.openssl.org/) per modificare il formato.

> [!IMPORTANT]
> Usare i certificati autofirmati solo per lo sviluppo. Non usarli nell'ambiente di produzione.
>
> Se si usa un certificato autofirmato, vedere la sezione [Utilizzo di servizi con certificati autofirmati](#self-signed) per istruzioni specifiche.

> [!WARNING]
> Quando si richiede un certificato, è necessario fornire il nome di dominio completo (FQDN) dell'indirizzo che si intende usare per il servizio, ad esempio www.contoso.com. L'indirizzo indicato nel certificato e l'indirizzo usato dai client vengono confrontati quando viene convalidata l'identità del servizio.
>
> Se gli indirizzi non corrispondono, i client ricevono un errore. 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>Distribuire o aggiornare il servizio con SSL abilitato

Per distribuire il servizio con SSL abilitato, impostare il parametro `ssl_enabled` su `True`. Impostare il parametro `ssl_certificate` sul valore del file di __certificato__ e `ssl_key` sul valore del file di __chiave__. L'esempio seguente illustra la distribuzione di un servizio con SSL abilitato:

```python
from amlrealtimeai import DeploymentClient

subscription_id = "<Your Azure Subscription ID>"
resource_group = "<Your Azure Resource Group Name>"
model_management_account = "<Your AzureML Model Management Account Name>"
location = "eastus2"

model_name = "resnet50-model"
service_name = "quickstart-service"

deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)

with open('cert.pem','r') as cert_file:
    with open('key.pem','r') as key_file:
        cert = cert_file.read()
        key = key_file.read()
        service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
```

La risposta dell'operazione `create_service` contiene l'indirizzo IP del servizio. L'indirizzo IP viene usato durante il mapping del nome DNS all'indirizzo IP del servizio.

La risposta contiene anche una __chiave primaria__ e una __chiave secondaria__ che consentono di utilizzare il servizio.

### <a name="update-your-dns-to-point-to-the-service"></a>Aggiornare il DNS in modo che punti al servizio

Usare gli strumenti forniti dal registrar per aggiornare il record DNS per il nome di dominio. Il record deve puntare all'indirizzo IP del servizio.

> [!NOTE]
> A seconda del registrar e della durata (TTL) configurata per il nome di dominio, possono essere necessari da alcuni minuti a diverse ore prima che i client possano risolvere il nome di dominio.

### <a name="consume-authenticated-services"></a>Utilizzo di servizi autenticati

Gli esempi seguenti illustrano come utilizzare un servizio autenticato con Python e C#:

> [!NOTE]
> Sostituire `authkey` con la chiave primaria o secondaria restituita durante la creazione del servizio.

```python
from amlrealtimeai import PredictionClient
client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
image_file = R'C:\path_to_file\image.jpg'
results = client.score_image(image_file)
```

```csharp
var client = new ScoringClient(host, 50051, useSSL, "authKey");
float[,] result;
using (var content = File.OpenRead(image))
    {
        IScoringRequest request = new ImageRequest(content);
        result = client.Score<float[,]>(request);
    }
```

Gli altri client gRPC possono autenticare le richieste impostando un'intestazione dell'autorizzazione. L'approccio generale consiste nel creare un oggetto `ChannelCredentials` che combina `SslCredentials` con `CallCredentials`. L'oggetto viene aggiunto all'intestazione dell'autorizzazione della richiesta. Per altre informazioni sull'implementazione del supporto per intestazioni specifiche, vedere [https://grpc.io/docs/guides/auth.html](https://grpc.io/docs/guides/auth.html).

Gli esempi seguenti illustrano come impostare l'intestazione in C# e Go:

```csharp
creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                      async (context, metadata) =>
                      {
                          metadata.Add(new Metadata.Entry("authorization", "authKey"));
                          await Task.CompletedTask;
                      }));

```

```go
conn, err := grpc.Dial(serverAddr, 
    grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
    grpc.WithPerRPCCredentials(&authCreds{
    Key: "authKey"}))

type authCreds struct {
    Key string
}

func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
    return map[string]string{
        "authorization": c.Key,
    }, nil
}

func (c *authCreds) RequireTransportSecurity() bool {
    return true
}
```

### <a id="self-signed"></a>Utilizzo di servizi con certificati autofirmati

Esistono due modi per consentire ai client di autenticarsi presso un server protetto con un certificato autofirmato:

* Nel sistema client impostare la variabile di ambiente `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` in modo che punti al file del certificato.

* Quando si costruisce un oggetto `SslCredentials`, passare il contenuto del file del certificato al costruttore.

Con entrambi i metodi, gRPC userà il certificato come certificato radice.

> [!IMPORTANT]
> gRPC non accetta certificati non attendibili. Se si usa un certificato non attendibile, si verificherà un errore con un codice di stato `Unavailable`. I dettagli dell'errore contengono `Connection Failed`.

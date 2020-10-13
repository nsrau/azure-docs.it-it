---
title: Distribuire un modello di Machine Learning in funzioni di Azure con cache di Azure per Redis
description: "In questo articolo verrà distribuito un modello da Azure Machine Learning come app per le funzioni in funzioni di Azure usando una cache di Azure per l'istanza di Redis. Cache di Azure per Redis è estremamente efficiente e scalabile: quando abbinato a un modello di Azure Machine Learning, è possibile ottenere bassa latenza e velocità effettiva elevata nell'applicazione."
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: 54109d5889ae2c08f444a3a089386d413bf4262b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650188"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-with-azure-cache-for-redis"></a>Distribuire un modello di Machine Learning in funzioni di Azure con cache di Azure per Redis 

In questo articolo verrà distribuito un modello da Azure Machine Learning come app per le funzioni in funzioni di Azure usando una cache di Azure per l'istanza di Redis.  

Cache di Azure per Redis è estremamente efficiente e scalabile: quando abbinato a un modello di Azure Machine Learning, è possibile ottenere bassa latenza e velocità effettiva elevata nell'applicazione. Un paio di scenari in cui una cache è particolarmente utile è quando si inferiscono i dati e per i risultati effettivi dell'inferenza del modello. In entrambi gli scenari, i metadati o i risultati vengono archiviati in memoria, il che comporta un miglioramento delle prestazioni. 

> [!NOTE]
> Sebbene sia la Azure Machine Learning che funzioni di Azure siano disponibili a livello generale, la possibilità di creare un pacchetto di un modello dal servizio Machine Learning per le funzioni è in anteprima.  
>

## <a name="prerequisites"></a>Prerequisiti
* Sottoscrizione di Azure- [crearne una gratuitamente](https://azure.microsoft.com/free/).
* Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere l'articolo [creare un'area di lavoro](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) .
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).
* Un modello di apprendimento automatico sottoposto a training registrato nell'area di lavoro. Se non si dispone di un modello, usare l' [esercitazione relativa alla classificazione delle immagini: Train Model](https://docs.microsoft.com/azure/machine-learning/tutorial-train-models-with-aml) per eseguire il training e la registrazione di un modello.

> [!IMPORTANT]
> I frammenti di codice in questo articolo presuppongono che siano state impostate le variabili seguenti:
>
> * `ws` -L'area di lavoro Azure Machine Learning.
> * `model` : Modello registrato che verrà distribuito.
> * `inference_config` -Configurazione dell'inferenza per il modello.
>
> Per altre informazioni sull'impostazione di queste variabili, vedere [distribuire modelli con Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where).

## <a name="create-an-azure-cache-for-redis-instance"></a>Creare un'istanza di Azure Cache per Redis 
Sarà possibile distribuire un modello di Machine Learning in funzioni di Azure con qualsiasi istanza di cache Basic, standard o Premium. Per creare un'istanza della cache, attenersi alla seguente procedura.  

1. Passare alla Home page di portale di Azure o aprire il menu sidebar, quindi selezionare **Crea una risorsa**. 
   
1. Nella pagina **Nuovo** selezionare **Database** e quindi **Cache di Azure per Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selezionare Cache di Azure per Redis.":::
   
1. Nella pagina **Nuova cache Redis** configurare le impostazioni per la nuova cache.
   
   | Impostazione      | Valore consigliato  | Descrizione |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Immettere un nome univoco globale. | Il nome della cache deve essere una stringa compresa tra 1 e 63 caratteri contenente solo numeri, lettere o trattini. Il nome deve iniziare e terminare con un numero o una lettera e non può contenere trattini consecutivi. Il *nome host* dell'istanza della cache sarà *\<DNS name>.redis.cache.windows.net*. | 
   | **Sottoscrizione** | Nell'elenco a discesa selezionare la sottoscrizione. | Sottoscrizione in cui creare la nuova istanza della cache di Azure per Redis. | 
   | **Gruppo di risorse** | Nell'elenco a discesa selezionare un gruppo di risorse oppure scegliere **Crea nuovo** e immettere il nome di un nuovo gruppo di risorse. | Nome del gruppo di risorse in cui creare la cache e altre risorse. L'inserimento di tutte le risorse di un'app in un unico gruppo di risorse ne semplifica la gestione o l'eliminazione. | 
   | **Posizione** | Nell'elenco a discesa selezionare una località. | Selezionare un'[area](https://azure.microsoft.com/regions/) in prossimità di altri servizi che useranno la cache. |
   | **Piano tariffario** | Nell'elenco a discesa selezionare un [piano tariffario](https://azure.microsoft.com/pricing/details/cache/). |  Il piano tariffario determina le dimensioni, le prestazioni e le funzionalità disponibili per la cache. Per altre informazioni, vedere la [panoramica su Cache Redis di Azure](cache-overview.md). |

1. Selezionare la scheda **Rete** o fare clic sul pulsante **Rete** nella parte inferiore della pagina.

1. Nella scheda **Rete** selezionare il metodo di connettività.

1. Fare clic sul pulsante **Avanti: Avanzate** oppure fare clic sulla scheda **Avanti: Avanzate** nella parte inferiore della pagina.

1. Nella scheda **Avanzate** per un'istanza della cache Basic o Standard selezionare l'interruttore Abilita se si vuole abilitare una porta non TLS.

1. Nella scheda **Avanzate** per l'istanza della cache Premium configurare le impostazioni per la porta non TLS, il clustering e la persistenza dei dati.

1. Fare clic sul pulsante **Avanti: Tag** o fare clic sulla scheda **Avanti: Tag** nella parte inferiore della pagina.

1. Facoltativamente, nella scheda **Tag** immettere il nome e il valore se si vuole categorizzare la risorsa. 

1. Selezionare **Rivedi e crea**. Si viene reindirizzati alla scheda Rivedi e crea in cui Azure convalida la configurazione.

1. Quando viene visualizzato il messaggio di convalida verde, selezionare **Crea**.

La creazione della cache richiede un po' di tempo. È possibile monitorare lo stato di avanzamento nella pagina  **Panoramica**  della cache di Azure per Redis. Quando la voce  **Stato**  indica  **In esecuzione**, la cache è pronta per l'uso. 

## <a name="prepare-for-deployment"></a>Preparare la distribuzione

Prima di distribuire, è necessario definire gli elementi necessari per eseguire il modello come servizio Web. Nell'elenco seguente vengono descritti gli elementi principali necessari per una distribuzione:

* Uno __script di immissione__. Questo script accetta richieste, assegna punteggi alla richiesta utilizzando il modello e restituisce i risultati.

    > [!IMPORTANT]
    > Lo script di immissione è specifico del modello. deve comprendere il formato dei dati della richiesta in ingresso, il formato dei dati previsti dal modello e il formato dei dati restituiti ai client.
    >
    > Se i dati della richiesta sono in un formato non utilizzabile dal modello, lo script può trasformarlo in un formato accettabile. Può anche trasformare la risposta prima di restituirla al client.
    >
    > Per impostazione predefinita, quando si esegue il packaging per le funzioni, l'input viene considerato come testo. Se si è interessati a utilizzare i byte non elaborati dell'input (ad esempio per i trigger BLOB), è necessario utilizzare [AMLRequest per accettare dati non elaborati](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-advanced-entry-script#binary-data).

Per la funzione Run, assicurarsi che si connetta a un endpoint Redis.

```python
import json
import numpy as np
import os
import redis
from sklearn.externals import joblib

def init():
    global model
    global azrediscache
    azrediscache = redis.StrictRedis(host='<host_url>', port=6380, password="<access_key>", ssl=True)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        input = azrediscache.get(data)
        result = model.predict(input)
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Per altre informazioni sullo script di immissione, vedere definire il codice di assegnazione dei [punteggi.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where?tabs=python#define-an-entry-script)

* **Dipendenze**, ad esempio gli script helper o i pacchetti Python/conda necessari per eseguire lo script di immissione o il modello

Queste entità sono incapsulate in una __configurazione di inferenza__. La configurazione di inferenza fa riferimento allo script di avvio e ad altre dipendenze.

> [!IMPORTANT]
> Quando si crea una configurazione di inferenza da usare con funzioni di Azure, è necessario usare un oggetto [Environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true) . Si noti che se si definisce un ambiente personalizzato, è necessario aggiungere azureml-defaults con Version >= 1.0.45 come dipendenza PIP. Questo pacchetto contiene le funzionalità necessarie per ospitare il modello come servizio Web. Nell'esempio seguente viene illustrata la creazione di un oggetto ambiente e il relativo utilizzo con una configurazione di inferenza:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults', 'redis'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Per altre informazioni sugli ambienti, vedere [creare e gestire ambienti per il training e la distribuzione](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments).

Per ulteriori informazioni sulla configurazione dell'inferenza, vedere [distribuire modelli con Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where?tabs=python#define-an-inference-configuration).

> [!IMPORTANT]
> Quando si esegue la distribuzione in funzioni, non è necessario creare una __configurazione di distribuzione__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Installare il pacchetto di anteprima SDK per il supporto delle funzioni

Per compilare pacchetti per funzioni di Azure, è necessario installare il pacchetto di anteprima dell'SDK.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Creare l'immagine

Per creare l'immagine Docker distribuita in funzioni di Azure, usare [azureml. contrib. Functions. Package](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py&preserve-view=true) o la funzione specifica del pacchetto per il trigger che si vuole usare. Il frammento di codice seguente illustra come creare un nuovo pacchetto con un trigger HTTP dal modello e dalla configurazione dell'inferenza:

> [!NOTE]
> Il frammento di codice presuppone che `model` contenga un modello registrato e che `inference_config` contenga la configurazione per l'ambiente di inferenza. Per altre informazioni, vedere [distribuire modelli con Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import HTTP_TRIGGER
model_package = package(ws, [model], inference_config, functions_enabled=True, trigger=HTTP_TRIGGER)
model_package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(model_package.location)
```

Quando `show_output=True` viene visualizzato l'output del processo di compilazione docker. Al termine del processo, l'immagine è stata creata nel Container Registry di Azure per l'area di lavoro. Una volta compilata l'immagine, viene visualizzata la località nel Container Registry di Azure. Il percorso restituito è nel formato `<acrinstance>.azurecr.io/package@sha256:<imagename>` .

> [!NOTE]
> Il packaging per le funzioni supporta attualmente trigger HTTP, trigger di BLOB e trigger del bus di servizio. Per altre informazioni sui trigger, vedere [binding di funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob-trigger#blob-name-patterns).

> [!IMPORTANT]
> Salvare le informazioni sul percorso, così come vengono usate durante la distribuzione dell'immagine.

## <a name="deploy-image-as-a-web-app"></a>Distribuire un'immagine come app Web

1. Usare il comando seguente per ottenere le credenziali di accesso per il Container Registry di Azure che contiene l'immagine. Sostituire `<myacr>` con il valore restituito in precedenza da `package.location` : 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    L'output di questo comando è simile al documento JSON seguente:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Salvare il valore per __username__ e una delle __password__.

1. Se non si dispone già di un gruppo di risorse o di un piano di servizio app per distribuire il servizio, i comandi seguenti illustrano come creare entrambi:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    In questo esempio viene usato un piano tariffario _Basic Linux_ ( `--sku B1` ).

    > [!IMPORTANT]
    > Le immagini create da Azure Machine Learning usano Linux, quindi è necessario usare il `--is-linux` parametro.

1. Creare l'account di archiviazione da usare per l'archiviazione dei processi Web e ottenere la relativa stringa di connessione. Sostituire `<webjobStorage>` con il nome che si vuole usare.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. Per creare l'app per le funzioni, usare il comando seguente. Sostituire `<app-name>` con il nome che si vuole usare. Sostituire `<acrinstance>` e `<imagename>` con i valori restituiti in `package.location` precedenza. Sostituire `<webjobStorage>` con il nome dell'account di archiviazione nel passaggio precedente:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > A questo punto è stata creata l'app per le funzioni. Tuttavia, poiché non è stata specificata la stringa di connessione per il trigger HTTP o le credenziali per il Container Registry di Azure che contiene l'immagine, l'app per le funzioni non è attiva. Nei passaggi successivi vengono fornite la stringa di connessione e le informazioni di autenticazione per il registro contenitori. 

1. Per fornire all'app per le funzioni le credenziali necessarie per accedere al registro contenitori, usare il comando seguente. Sostituire `<app-name>` con il nome dell'app per le funzioni. Sostituire `<acrinstance>` e `<imagetag>` con i valori della chiamata AZ cli nel passaggio precedente. Sostituire `<username>` e `<password>` con le informazioni di accesso di ACR recuperate in precedenza:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Questo comando restituisce informazioni simili al documento JSON seguente:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

A questo punto, l'app per le funzioni inizia a caricare l'immagine.

> [!IMPORTANT]
> Potrebbero essere necessari alcuni minuti prima che l'immagine venga caricata. È possibile monitorare lo stato di avanzamento usando il portale di Azure.

## <a name="test-azure-function-http-trigger"></a>Testare il trigger HTTP della funzione di Azure 

A questo punto verrà eseguito e testato il trigger HTTP della funzione di Azure.

1. Passare all'app per le funzioni di Azure nel portale di Azure.
1. In Developer selezionare **codice + test**. 
1. Sul lato destro selezionare la scheda **input** . 
1. Fare clic sul pulsante **Run (Esegui** ) per testare il trigger http della funzione di Azure. 

Un modello è stato distribuito correttamente da Azure Machine Learning come app per le funzioni usando una cache di Azure per l'istanza di Redis. Per altre informazioni su cache di Azure per Redis, vedere i collegamenti nella sezione seguente.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prosegue con l'esercitazione successiva, è possibile conservare le risorse create in questa guida introduttiva e riutilizzarle.

In caso contrario, se si è terminato di usare la Guida introduttiva, è possibile eliminare le risorse di Azure create in questa Guida introduttiva per evitare addebiti. 

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Quando si elimina un gruppo di risorse, tutte le risorse in esso contenute vengono eliminate in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se le risorse per questo esempio sono state create all'interno di un gruppo di risorse esistente che contiene anche elementi da mantenere, è possibile eliminare ogni elemento singolarmente dai rispettivi pannelli anziché eliminare il gruppo di risorse.

### <a name="to-delete-a-resource-group"></a>Per eliminare un gruppo di risorse

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **Gruppi di risorse**.

2. Nella casella **Filtra per nome** immettere il nome del gruppo di risorse. Nel gruppo di risorse, nell'elenco dei risultati, selezionare **...** e quindi **Elimina gruppo di risorse**.

Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Digitare il nome del gruppo di risorse per confermare e quindi selezionare **Elimina**.

Dopo qualche istante, il gruppo di risorse e tutte le risorse che contiene vengono eliminati.

## <a name="next-steps"></a>Passaggi successivi 

* Scopri di più su [cache di Azure per Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-overview)
* Informazioni su come configurare l'app per le funzioni nella documentazione di [funzioni](/azure/azure-functions/functions-create-function-linux-custom-image) .
* [Riferimento API](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py&preserve-view=true) 
* Creare un' [app Python che usa cache di Azure per Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-python-get-started)


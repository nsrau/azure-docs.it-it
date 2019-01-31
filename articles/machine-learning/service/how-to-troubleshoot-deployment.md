---
title: Guida alla risoluzione dei problemi di distribuzione
titleSuffix: Azure Machine Learning service
description: Informazioni su come risolvere o trovare soluzioni alternative per i più comuni errori di distribuzione di Docker con il servizio Azure Kubernetes e Istanze di Azure Container quando si usa il servizio Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 83ee548befdc7ef0a4e7ed2d4b4e61b42a217f12
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247069"
---
# <a name="troubleshooting-azure-machine-learning-service-aks-and-aci-deployments"></a>Risoluzione dei problemi di distribuzione del servizio Azure Kubernetes e di Istanze di Azure Container con il servizio Azure Machine Learning

Questo articolo illustra come risolvere o trovare soluzioni alternative per i più comuni errori di distribuzione di Docker con il servizio Azure Kubernetes e Istanze di Azure Container quando si usa il servizio Azure Machine Learning.

Durante la distribuzione di un modello nel servizio Azure Machine Learning, il sistema esegue una serie di attività. Si tratta di una sequenza complessa di eventi, che a volte genera alcuni problemi. Le attività di distribuzione sono le seguenti:

1. Registrare il modello nel registro dei modelli dell'area di lavoro.

2. Creare un'immagine Docker seguendo questa procedura:
    1. Scaricare il modello registrato dal registro. 
    2. Creare un Dockerfile, con un ambiente Python basato sulle dipendenze specificate nel file YAML di ambiente.
    3. Aggiungere i file di modello e lo script di assegnazione dei punteggi fornito nel Dockerfile.
    4. Creare una nuova immagine Docker usando il Dockerfile.
    5. Registrare l'immagine Docker con il Registro Azure Container associato all'area di lavoro.

3. Distribuire l'immagine Docker al servizio Istanze di Azure Container o al servizio Azure Kubernetes.

4. Avviare uno o più nuovi contenitori in uno di questi due servizi. 

Per altre informazioni su questa procedura, vedere [Gestire e distribuire modelli con il servizio Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="before-you-begin"></a>Prima di iniziare

Se si verifica un problema, la prima cosa da fare è suddividere l'attività di distribuzione (descritta in precedenza) in singoli passaggi per isolare il problema. 

Questo modo di procedere è particolarmente utile se si usa l'API `Webservice.deploy` o l'API `Webservice.deploy_from_model`, in quanto tali funzioni raggruppano i suddetti passaggi in una sola azione. In genere queste API sono piuttosto comode, ma è utile suddividere i passaggi durante la risoluzione dei problemi sostituendole con le chiamate API riportate di seguito.

1. Registrare il modello. Ecco del codice di esempio:

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Compilare l'immagine. Ecco del codice di esempio:

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      execution_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. Distribuire l'immagine come servizio. Ecco del codice di esempio:

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

Una volta suddiviso il processo di distribuzione in singole attività, è possibile esaminare alcuni degli errori più comuni.

## <a name="image-building-fails"></a>Errore di compilazione dell'immagine
Se il sistema non è in grado di compilare l'immagine Docker, la chiamata a `image.wait_for_creation()` non riesce e genera alcuni messaggi di errore che possono fornire qualche indizio. Anche nel log di compilazione dell'immagine possono essere disponibili altri dettagli sugli errori. Di seguito è riportato del codice di esempio che mostra come individuare l'URI del log di compilazione dell'immagine.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print (img.name, img.version, img.image_build_log_uri)
```
L'URI del log è un URL SAS che punta a un file di log archiviato nell'Archivio BLOB di Azure. Basta copiare e incollare l'URI in una finestra del browser per poter scaricare e visualizzare il file di log.


## <a name="service-launch-fails"></a>Errore di avvio del servizio
Dopo aver creato correttamente l'immagine, il sistema tenta di avviare un contenitore nel servizio Istanza di contenitore di Azure o nel servizio Azure Kubernetes, a seconda della configurazione della distribuzione. È generalmente consigliabile provare prima con la distribuzione nel servizio Istanza di contenitore di Azure in quanto, coinvolgendo un solo contenitore, risulta più semplice. In questo modo è possibile escludere qualsiasi problema specifico del servizio AKS.

Nell'ambito del processo di avvio del contenitore, il sistema richiama la funzione `init()` nello script di assegnazione dei punteggi. Se la funzione `init()` contiene eccezioni non rilevate, nel messaggio di errore potrebbe essere visualizzato l'errore **CrashLoopBackOff**. Ecco alcuni suggerimenti utili per risolvere il problema.

### <a name="inspect-the-docker-log"></a>Esaminare il log di Docker
È possibile visualizzare i messaggi dettagliati del log del motore Docker dall'oggetto di servizio.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

### <a name="debug-the-docker-image-locally"></a>Eseguire il debug dell'immagine di Docker in locale
A volte il log di Docker non fornisce informazioni sufficienti sul problema. In questo caso è possibile recuperare l'immagine di Docker compilata, avviare un contenitore locale ed eseguire il debug direttamente all'interno del contenitore in modo interattivo. Per avviare un contenitore locale, deve essere in esecuzione in locale un motore Docker. Per semplificare notevolmente il processo è inoltre consigliabile installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Prima di tutto occorre trovare la posizione dell'immagine:

```python
# print image location
print(image.image_location)
```

Il percorso dell'immagine ha questo formato: `<acr-name>.azurecr.io/<image-name>:<version-number>`, ad esempio `myworkpaceacr.azurecr.io/myimage:3`. 

Ora passare alla finestra della riga di comando. Se l'interfaccia della riga di comando di Azure è installata, è possibile digitare i comandi seguenti per accedere al Registro Azure Container associato all'area di lavoro in cui è archiviata l'immagine. 

```sh
# log on to Azure first if you haven't done so before
$ az login

# make sure you set the right subscription in case you have access to multiple subscriptions
$ az account set -s <subscription_name_or_id>

# now let's log in to the workspace ACR
# note the acr-name is the domain name WITHOUT the ".azurecr.io" postfix
# e.g.: az acr login -n myworkpaceacr
$ az acr login -n <acr-name>
```
Se l'interfaccia della riga di comando di Azure non è installata, è possibile usare il comando `docker login` per accedere al Registro Azure Container. Prima però occorre recuperare il nome utente e la password di tale registro dal portale di Azure.

Dopo aver eseguito l'accesso al Registro Azure Container, è possibile recuperare l'immagine di Docker e avviare un contenitore localmente, quindi avviare una sessione di Bash per il debug usando il comando `docker run`:

```sh
# note the image_id is <acr-name>.azurecr.io/<image-name>:<version-number>
# for example: myworkpaceacr.azurecr.io/myimage:3
$ docker run -it <image_id> /bin/bash
```

Dopo aver avviato una sessione di Bash sul contenitore in esecuzione, è possibile trovare gli script di assegnazione dei punteggi nella cartella `/var/azureml-app`. È quindi possibile avviare una sessione Python per eseguire il debug degli script di assegnazione dei punteggi. 

```sh
# enter the directory where scoring scripts live
cd /var/azureml-app

# find what Python packages are installed in the python environment
pip freeze

# sanity-check on score.py
# you might want to edit the score.py to trigger init().
# as most of the errors happen in init() when you are trying to load the model.
python score.py
```
Se serve un editor di testo per modificare gli script, è possibile installare vim, nano, Emacs o un altro editor a scelta.

```sh
# update package index
apt-get update

# install a text editor of your choice
apt-get install vim
apt-get install nano
apt-get install emacs

# launch emacs (for example) to edit score.py
emacs score.py

# exit the container bash shell
exit
```

Si può anche avviare il servizio Web in locale e inviargli il traffico HTTP. Il server Flask nel contenitore Docker è in esecuzione sulla porta 5001. È possibile eseguire il mapping a qualsiasi altra porta disponibile nel computer host.
```sh
# you can find the scoring API at: http://localhost:8000/score
$ docker run -p 8000:5001 <image_id>
```

## <a name="function-fails-getmodelpath"></a>Errore della funzione: get_model_path()
Spesso nella funzione `init()` dello script di assegnazione dei punteggi viene chiamata la funzione `Model.get_model_path()` per individuare un file di modello o una cartella di file di modello nel contenitore. Se non si riesce a trovare il file o la cartella, in genere la funzione non riesce. Il modo più semplice per eseguire il debug di questo errore consiste nell'eseguire il codice Python seguente nella shell del contenitore:

```python
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

Questo codice consente di individuare il percorso locale (relativo a `/var/azureml-app`) nel contenitore in cui lo script di assegnazione dei punteggi prevede di trovare il file di modello o la cartella. È quindi possibile verificare se il file o la cartella si trova effettivamente dove dovrebbe essere.


## <a name="function-fails-runinputdata"></a>Errore della funzione: run(input_data)
Se il servizio viene distribuito correttamente, ma si arresta in modo anomalo quando si pubblicano dati nell'endpoint di assegnazione dei punteggi, è possibile aggiungere un'istruzione di rilevamento degli errori nella funzione `run(input_data)` in modo che restituisca il messaggio di errore dettagliato. Ad esempio: 

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```
**Nota**: la restituzione di messaggi di errore dalla chiamata a `run(input_data)` dovrebbe essere eseguita solo a scopo di debug. Per motivi di sicurezza, non è consigliabile farlo in un ambiente di produzione.


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla distribuzione: 
* [Come e dove distribuire modelli](how-to-deploy-and-where.md)

* [Esercitazione: Eseguire il training e la distribuzione di modelli](tutorial-train-models-with-aml.md)

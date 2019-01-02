---
title: Distribuire modelli come servizi Web
titleSuffix: Azure Machine Learning service
description: Informazioni su come e dove distribuire i modelli del servizio Azure Machine Learning, inclusi Istanze di Azure Container, il servizio Azure Kubernetes, Azure IoT Edge e dispositivi FPGA.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 17193bf3285a2052a913293ec3adc6f9b8884f72
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435947"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Distribuire modelli con il servizio Azure Machine Learning

Il servizio Azure Machine Learning offre diversi modi per distribuire un modello con training usando l'SDK. In questo documento viene descritto come distribuire il modello come servizio Web nel cloud di Azure o nei dispositivi periferici IoT.

È possibile distribuire i modelli per le destinazioni di calcolo seguenti:

| Destinazione di calcolo | Tipo di distribuzione | DESCRIZIONE |
| ----- | ----- | ----- |
| [Istanze di Azure Container](#aci) | Servizio Web | Distribuzione rapida. Soluzione ideale per lo sviluppo o il test. |
| [Servizio Azure Kubernetes](#aks) | Servizio Web | Soluzione ideale per le distribuzioni di produzione su vasta scala. Fornisce la scalabilità automatica e tempi di risposta rapidi. |
| [Azure IoT Edge](#iotedge) | Modulo IoT | Distribuzione di modelli nei dispositivi IoT. Inferenza nel dispositivo. |
| [Dispositivo FPGA (Field-Programmable Gate Array)](#fpga) | Servizio Web | Latenza estremamente bassa per inferenza in tempo reale. |

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]

## <a name="prerequisites"></a>Prerequisiti

- Un'area di lavoro del servizio di Azure Machine Learning e Azure Machine Learning SDK per Python installato. Informazioni su come ottenere questi prerequisiti usando la [Guida introduttiva di Azure Machine Learning](quickstart-get-started.md).

- Un modello con training in formato pickle (`.pkl`) o ONNX (`.onnx`). Se non si ha un modello con training, usare la procedura descritta nell'esercitazione [Eseguire il training di modelli](tutorial-train-models-with-aml.md) per eseguire il training di un modello e registrarlo con il servizio Azure Machine Learning.

- Le sezioni di codice presuppongono che `ws` faccia riferimento all'area di lavoro di Machine Learning. Ad esempio: `ws = Workspace.from_config()`.

## <a name="deployment-workflow"></a>Flusso di lavoro per la distribuzione

Il processo di distribuzione di un modello è simile per tutte le destinazioni di calcolo:

1. Eseguire il training di un modello.
1. Registrare il modello.
1. Creare una configurazione di immagine.
1. Creare l'immagine.
1. Distribuire l'immagine in una destinazione di calcolo.
1. Eseguire il test della distribuzione.
1. (Facoltativo) Eseguire la pulizia degli artefatti.

    * Quando si esegue la **distribuzione come servizio Web**, sono disponibili tre opzioni di distribuzione:

        * [deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-): quando si usa questo metodo, non è necessario registrare il modello né creare l'immagine. Non è tuttavia possibile controllare il nome del modello o dell'immagine o le descrizioni e i tag associati.
        * [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-): quando si usa questo metodo, non è necessario creare un'immagine. Non si ha tuttavia alcun controllo sul nome dell'immagine creata.
        * [deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-): registrare il modello e creare un'immagine prima di usare questo metodo.

        Gli esempi in questo documento usano `deploy_from_image`.

    * Quando si esegue la **distribuzione come modulo IoT Edge**, è necessario registrare il modello e creare l'immagine.

## <a name="register-a-model"></a>Registrare un modello

È possibile distribuire solo i modelli con training. Il modello può essere sottoposto a training usando Azure Machine Learning o un altro servizio. Per registrare un modello da file, usare il codice seguente:

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl",
                       model_name = "Mymodel",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

> [!NOTE]
> Anche se l'esempio mostra l'uso di un modello archiviato come file pickle, è possibile usare anche modelli ONNX. Per altre informazioni sull'uso di modelli ONNX, vedere il documento [ONNX e Azure Machine Learning](how-to-build-deploy-onnx.md).

Per altre informazioni, vedere la documentazione di riferimento per la [classe Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a id="configureimage"></a> Creare una configurazione di immagine

I modelli distribuiti vengono inseriti in pacchetti come immagine. L'immagine contiene le dipendenze necessarie per eseguire il modello.

Per le distribuzioni di **Istanze di Azure Container**, del **servizio Azure Kubernetes** e di **Azure IoT Edge**, viene usata la classe `azureml.core.image.ContainerImage` per creare una configurazione di immagine. Tale configurazione viene quindi usata per creare una nuova immagine Docker. 

Il codice seguente illustra come creare una nuova configurazione di immagine:

```python
from azureml.core.image import ContainerImage

# Image configuration
image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                 runtime = "python",
                                                 conda_file = "myenv.yml",
                                                 description = "Image with ridge regression model",
                                                 tags = {"data": "diabetes", "type": "regression"}
                                                 )
```

Questa configurazione usa un file `score.py` per passare le richieste al modello. Il file contiene due funzioni:

* `init()`: questa funzione carica in genere il modello in un oggetto globale. Viene eseguita una sola volta all'avvio del contenitore Docker. 

* `run(input_data)`: questa funzione usa il modello per stimare un valore in base ai dati di input. Per la serializzazione e la deserializzazione, gli input e gli output dell'esecuzione usano in genere JSON, ma sono supportati anche altri formati.

Per un file `score.py` di esempio, vedere l'[esercitazione sulla classificazione delle immagini](tutorial-deploy-models-with-aml.md#make-script). Per un esempio che usa un modello ONNX, vedere il documento [ONNX e Azure Machine Learning](how-to-build-deploy-onnx.md).

Per specificare un file di ambiente Conda viene usato il parametro `conda_file`. Questo file definisce l'ambiente Conda per il modello distribuito. Per altre informazioni sulla creazione di questo file, vedere [Creare un file di ambiente (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file).

Per altre informazioni, vedere la documentazione di riferimento per la [classe ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

## <a id="createimage"></a> Creare l'immagine

Dopo aver creato la configurazione di immagine, è possibile usarla per creare un'immagine. Questa immagine viene archiviata nel registro contenitori relativo all'area di lavoro. Una volta creata, la stessa immagine può essere distribuita a più servizi.

```python
# Create the image from the image configuration
image = ContainerImage.create(name = "myimage", 
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**Tempo stimato**: circa 3 minuti.

Quando si registrano più immagini con lo stesso nome, alle immagini vengono assegnate automaticamente diverse versioni. Ad esempio, alla prima immagine registrata come `myimage` viene assegnato un ID `myimage:1`. La volta successiva che si registra un'immagine come `myimage`, l'ID della nuova immagine è `myimage:2`.

Per altre informazioni, vedere la documentazione di riferimento per la [classe ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

## <a name="deploy-the-image"></a>Distribuire l'immagine

Quando si raggiunge la fase di distribuzione, il processo varia leggermente a seconda della destinazione di calcolo. Vedere le sezioni seguenti per informazioni su come eseguire la distribuzione in:

* [Istanze di Azure Container](#aci)
* [Servizio Azure Kubernetes](#aks)
* [Project Brainwave (dispositivi FPGA)](#fpga)
* [Dispositivi Azure IoT Edge](#iotedge)

### <a id="aci"></a> Eseguire la distribuzione in Istanze di Azure Container

Usare Istanze di Azure Container per distribuire i modelli come servizio Web se una o più delle condizioni seguenti sono vere:

- È necessario distribuire e convalidare rapidamente il modello. La distribuzione di Istanze di Azure Container viene completata in meno di 5 minuti.
- Si sta eseguendo il test di un modello in fase di sviluppo. Per informazioni sulla disponibilità di quote e aree per Istanze di Azure Container, vedere il documento [Quote e aree disponibili per Istanze di Azure Container](https://docs.microsoft.com/azure/container-instances/container-instances-quotas).

Per eseguire la distribuzione in Istanze di Azure Container, seguire questa procedura:

1. Definire la configurazione della distribuzione. L'esempio seguente definisce una configurazione che usa un core CPU e 1 GB di memoria:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. Per distribuire l'immagine creata nella sezione [Creare l'immagine](#createimage) di questo documento, usare il codice seguente:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **Tempo stimato**: circa 3 minuti.

    > [!TIP]
    > Se si verificano errori durante la distribuzione, usare `service.get_logs()` per visualizzare i log del servizio Azure Kubernetes. Le informazioni registrate potrebbero indicare la causa dell'errore.

Per altre informazioni, vedere la documentazione di riferimento per le classi [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) e [Webservice](https://docs.microsoft.comS/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py).

### <a id="aks"></a> Eseguire la distribuzione nel servizio Azure Kubernetes

Per distribuire il modello come servizio Web in uno scenario di produzione su vasta scala, usare il servizio Azure Kubernetes. È possibile usare un cluster del servizio Azure Kubernetes esistente o crearne uno nuovo tramite il SDK di Azure Machine Learning, l'interfaccia della riga di comando o il portale di Azure.

La creazione di un cluster del servizio Azure Kubernetes si esegue una sola volta per l'area di lavoro. È possibile riutilizzare questo cluster per più distribuzioni. Se si elimina il cluster, per eseguire la distribuzione successiva sarà necessario creare un nuovo cluster.

Il servizio Kubernetes di Azure offre le funzionalità seguenti:

* Scalabilità automatica
* Registrazione
* Raccolta di dati del modello
* Tempi di risposta rapidi per i servizi Web

Per eseguire la distribuzione nel servizio Azure Kubernetes, seguire questa procedura:

1. Per creare un cluster del servizio Azure Kubernetes, usare il codice seguente:

    > [!IMPORTANT]
    > La creazione del cluster del servizio Azure Kubernetes si esegue una sola volta per l'area di lavoro. Dopo aver creato il cluster, è possibile riutilizzarlo per più distribuzioni. Se si elimina il cluster o il gruppo di risorse che lo contiene, per eseguire la distribuzione successiva sarà necessario creare un nuovo cluster.
    > Per [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), se si selezionano valori personalizzati per agent_count e vm_size, è necessario verificare che il valore di agent_count moltiplicato per il valore di vm_size sia maggiore o uguale a 12 CPU virtuali. Ad esempio, se come vm_size si usa "Standard_D3_v2", che dispone di 4 CPU virtuali, per agent_count è consigliabile selezionare 3 o un valore superiore.

    ```python
    from azureml.core.compute import AksCompute, ComputeTarget

    # Use the default configuration (you can also provide parameters to customize this)
    prov_config = AksCompute.provisioning_configuration()

    aks_name = 'aml-aks-1' 
    # Create the cluster
    aks_target = ComputeTarget.create(workspace = ws, 
                                        name = aks_name, 
                                        provisioning_configuration = prov_config)

    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
    print(aks_target.provisioning_state)
    print(aks_target.provisioning_errors)
    ```

    **Tempo stimato**: circa 20 minuti.

    > [!TIP]
    > Se nella sottoscrizione di Azure è già incluso un cluster del servizio Azure Kubernetes, nella versione 1.11.*, è possibile usarlo per distribuire l'immagine. Il codice seguente illustra come collegare un cluster alla propria area di lavoro:
    >
    > ```python
    > # Set the resource group that contains the AKS cluster and the cluster name
    > resource_group = 'myresourcegroup'
    > cluster_name = 'mycluster'
    > 
    > # Attatch the cluster to your workgroup
    > attach_config = AksCompute.attach_configuration(resource_group = resource_group,
    >                                          cluster_name = cluster_name)
    > compute = ComputeTarget.attach(ws, 'mycompute', attach_config)
    > 
    > # Wait for the operation to complete
    > aks_target.wait_for_completion(True)
    > ```

2. Per distribuire l'immagine creata nella sezione [Creare l'immagine](#createimage) di questo documento, usare il codice seguente:

    ```python
    from azureml.core.webservice import Webservice, AksWebservice

    # Set configuration and service name
    aks_config = AksWebservice.deploy_configuration()
    aks_service_name ='aks-service-1'
    # Deploy from image
    service = Webservice.deploy_from_image(workspace = ws, 
                                                name = aks_service_name,
                                                image = image,
                                                deployment_config = aks_config,
                                                deployment_target = aks_target)
    # Wait for the deployment to complete
    service.wait_for_deployment(show_output = True)
    print(service.state)
    ```

    > [!TIP]
    > Se si verificano errori durante la distribuzione, usare `service.get_logs()` per visualizzare i log del servizio Azure Kubernetes. Le informazioni registrate potrebbero indicare la causa dell'errore.

Per altre informazioni, vedere la documentazione di riferimento per le classi [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) e [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py).

### <a id="fpga"></a> Eseguire la distribuzione in dispositivi FPGA

Project Brainwave consente di ottenere una latenza estremamente bassa per le richieste di inferenza in tempo reale. Project Brainwave accelera reti neurali profonde (DNN) distribuite in dispositivi FPGA nel cloud di Azure. I DNN comunemente usati sono disponibili come featurizers per il trasferimento induttivo o sottoposti a training personalizzabile con pesi formati a partire dai propri dati.

Per una procedura dettagliata sulla distribuzione di un modello con Project Brainwave, vedere il documento [Distribuire un modello in un FPGA](how-to-deploy-fpga-web-service.md).

### <a id="iotedge"></a> Eseguire la distribuzione in Azure IoT Edge

Un dispositivo Azure IoT Edge è un dispositivo Linux o Windows che esegue il runtime di Azure IoT Edge. È possibile distribuire i modelli di apprendimento automatico in tali dispositivi come moduli di IoT Edge. La distribuzione di un modello in un dispositivo IoT Edge consente al dispositivo di usare il modello direttamente invece di inviare i dati al cloud per l'elaborazione. Si ottengono tempi di risposta più rapidi e un trasferimento dei dati inferiore.

I moduli Azure IoT Edge vengono distribuiti nel dispositivo da un registro contenitori. Quando si crea un'immagine dal modello, questa viene archiviata nel registro contenitori dell'area di lavoro.

#### <a name="set-up-your-environment"></a>Configurazione dell'ambiente

* Un ambiente di sviluppo. Per altre informazioni, vedere il documento [Come configurare un ambiente di sviluppo](how-to-configure-environment.md).

* Un [hub IoT di Azure](../../iot-hub/iot-hub-create-through-portal.md) nella sottoscrizione di Azure. 

* Un modello con training. Per un esempio di esecuzione del training di un modello, vedere il documento [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md). Nel [repository GitHub di AI Toolkit per Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial) è disponibile un modello con training preliminare.

#### <a name="prepare-the-iot-device"></a>Preparare il dispositivo IoT
È necessario creare un hub IoT e registrare un dispositivo o riutilizzarne uno già esistente con [questo script](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/createNregister).

``` bash
ssh <yourusername>@<yourdeviceip>
sudo wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/createNregister
sudo chmod +x createNregister
sudo ./createNregister <The Azure subscriptionID you wnat to use> <Resourcegroup to use or create for the IoT hub> <Azure location to use e.g. eastus2> <the Hub ID you want to use or create> <the device ID you want to create>
```

Salvare la stringa di connessione risultante dopo "cs":"{copiare questa stringa}".

Inizializzare il dispositivo scaricando [questo script](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/installIoTEdge) in un nodo IoT Edge UbuntuX64 o in una Data Science Virtual Machine per eseguire i comandi seguenti:

```bash
ssh <yourusername>@<yourdeviceip>
sudo wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/installIoTEdge
sudo chmod +x installIoTEdge
sudo ./installIoTEdge
```

Il nodo IoT Edge è pronto a ricevere la stringa di connessione per l'hub IoT. Cercare la riga ```device_connection_string:``` e incollare la stringa di connessione precedente tra le virgolette.

È anche possibile imparare a registrare il dispositivo e installare il runtime di IoT seguendo la procedura passo a passo descritta nel documento [Avvio rapido: Distribuire il primo modulo IoT Edge in un dispositivo Linux x64](../../iot-edge/quickstart-linux.md).


#### <a name="get-the-container-registry-credentials"></a>Acquisire le credenziali del registro contenitori
Per distribuire un modulo IoT Edge nel dispositivo, Azure IoT richiede le credenziali per il registro contenitori in cui il servizio Azure Machine Learning archivia le immagini Docker.

È possibile recuperare con facilità le credenziali necessarie per il registro contenitori in due modi:

+ **Nel portale di Azure**:

  1. Accedere al [portale di Azure](https://portal.azure.com/signin/index).

  1. Passare all'area di lavoro del servizio Azure Machine Learning e selezionare __Panoramica__. Per passare alle impostazioni del registro contenitori selezionare il collegamento __Registro__.

     ![Immagine della voce nel registro contenitori](./media/how-to-deploy-and-where/findregisteredcontainer.png)

  1. Una volta aperto il registro contenitori, selezionare **Chiavi di accesso** e quindi abilitare l'utente amministratore.
 
     ![Immagine della schermata delle chiavi di accesso](./media/how-to-deploy-and-where/findaccesskey.png)

  1. Salvare i valori per **Server di accesso**, **Nome utente** e **Password**. 

+ **Con uno script Python**:

  1. Usare lo script Python seguente dopo il codice che è stato eseguito per creare un contenitore:

     ```python
     # Getting your container details
     container_reg = ws.get_details()["containerRegistry"]
     reg_name=container_reg.split("/")[-1]
     container_url = "\"" + image.image_location + "\","
     subscription_id = ws.subscription_id
     from azure.mgmt.containerregistry import ContainerRegistryManagementClient
     from azure.mgmt import containerregistry
     client = ContainerRegistryManagementClient(ws._auth,subscription_id)
     result= client.registries.list_credentials(resource_group_name, reg_name, custom_headers=None, raw=False)
     username = result.username
     password = result.passwords[0].value
     print('ContainerURL{}'.format(image.image_location))
     print('Servername: {}'.format(reg_name))
     print('Username: {}'.format(username))
     print('Password: {}'.format(password))
     ```
  1. Salvare i valori per ContainerURL, Servername, Username e Password. 

     Queste credenziali sono necessarie per consentire al dispositivo IoT Edge di accedere alle immagini nel registro contenitori privato.

#### <a name="deploy-the-model-to-the-device"></a>Distribuire il modello nel dispositivo

È possibile distribuire facilmente un modello eseguendo [questo script](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/deploymodel) e specificando le informazioni seguenti dai passaggi precedenti: nome del registro contenitori, nome utente, password, percorso URL dell'immagine, nome della distribuzione desiderata, nome dell'hub IoT e ID del dispositivo creato. È possibile eseguire questa operazione nella macchina virtuale seguendo questa procedura: 

```bash 
wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/deploymodel
sudo chmod +x deploymodel
sudo ./deploymodel <ContainerRegistryName> <username> <password> <imageLocationURL> <DeploymentID> <IoTHubname> <DeviceID>
```

In alternativa, è possibile seguire la procedura descritta nel documento [Distribuire i moduli di Azure IoT Edge dal portale di Azure](../../iot-edge/how-to-deploy-modules-portal.md) per distribuire l'immagine nel dispositivo. Quando si configurano le __impostazioni del Registro di sistema__ per il dispositivo, usare il __server di accesso__, il __nome utente__ e la __password__ dell'area di lavoro del registro contenitori.

> [!NOTE]
> Se non si ha familiarità con Azure IoT, vedere i documenti seguenti per informazioni introduttive al servizio:
>
> * [Avvio rapido: Distribuire il primo modulo IoT Edge in un dispositivo Linux](../../iot-edge/quickstart-linux.md)
> * [Avvio rapido: Distribuire il primo modulo IoT Edge in un dispositivo Windows](../../iot-edge/quickstart.md)


## <a name="testing-web-service-deployments"></a>Test delle distribuzioni di servizio Web

Per eseguire il test di una distribuzione di servizio Web, è possibile usare il metodo `run` dell'oggetto Webservice. Nell'esempio seguente, un documento JSON è impostato su un servizio Web e il risultato viene visualizzato. I dati inviati devono corrispondere a quelli previsti dal modello. In questo esempio, il formato dei dati corrisponde all'input previsto dal modello diabetes.

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data = test_sample)
print(prediction)
```

## <a name="update-the-web-service"></a>Aggiornare il servizio Web

Per aggiornare il servizio Web, usare il metodo `update`. Il codice seguente illustra come aggiornare il servizio Web in modo da usare una nuova immagine:

```python
from azureml.core.webservice import Webservice

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)
# Update the image used by the service
service.update(image = new-image)
print(service.state)
```

> [!NOTE]
> Quando si aggiorna un'immagine, il servizio Web non viene aggiornato automaticamente. È necessario aggiornare manualmente ogni servizio per cui si vuole usare la nuova immagine.

## <a name="clean-up"></a>Eseguire la pulizia

Per eliminare un servizio Web distribuito, usare `service.delete()`.

Per eliminare un'immagine, usare `image.delete()`.

Per eliminare un modello registrato, usare `model.delete()`.

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere i servizi Web di Azure Machine Learning con SSL](how-to-secure-web-service.md)
* [Usare un modello di Machine Learning distribuito come servizio Web](how-to-consume-web-service.md)
* [Come eseguire previsioni in batch](how-to-run-batch-predictions.md)

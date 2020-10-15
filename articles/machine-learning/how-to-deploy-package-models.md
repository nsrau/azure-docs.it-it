---
title: Modelli di pacchetto
titleSuffix: Azure Machine Learning
description: Creare un pacchetto di un modello come Dockerfile
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: edaae4f4c06f038d12267e35a2c356af430e1555
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998817"
---
# <a name="how-to-package-a-registered-model-with-docker"></a>Come creare un pacchetto di un modello registrato con Docker

Questo articolo illustra come creare un pacchetto di un modello di Azure Machine Learning registrato con Docker.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che sia già stato eseguito il training e la registrazione di un modello nell'area di lavoro di machine learning. Per informazioni su come eseguire il training e la registrazione di un modello Scikit-learn, [seguire questa esercitazione](how-to-train-scikit-learn.md).


## <a name="package-models"></a>Modelli di pacchetto

In alcuni casi, potrebbe essere necessario creare un'immagine Docker senza distribuire il modello (se, ad esempio, si prevede di eseguire la [distribuzione nel servizio app Azure](how-to-deploy-app-service.md)). In alternativa, potrebbe essere necessario scaricare l'immagine ed eseguirla in un'installazione locale di Docker. Potrebbe anche essere necessario scaricare i file usati per compilare l'immagine, esaminarli, modificarli e compilare l'immagine manualmente.

La creazione di pacchetti di modelli consente di eseguire queste operazioni. Inserisce tutti gli asset necessari per ospitare un modello come servizio Web e consente di scaricare un'immagine Docker completamente compilata o i file necessari per crearne uno. Esistono due modi per usare la creazione di pacchetti di modelli:

**Scaricare un modello di pacchetto:** Scaricare un'immagine Docker contenente il modello e gli altri file necessari per ospitarla come servizio Web.

**Generare un Dockerfile:** Scaricare Dockerfile, Model, entry script e altri asset necessari per creare un'immagine docker. È quindi possibile ispezionare i file o apportare modifiche prima di compilare l'immagine localmente.

Entrambi i pacchetti possono essere usati per ottenere un'immagine Docker locale.

> [!TIP]
> La creazione di un pacchetto è simile alla distribuzione di un modello. Si usano un modello registrato e una configurazione di inferenza.

> [!IMPORTANT]
> Per scaricare un'immagine completamente compilata o creare un'immagine localmente, è necessario che [Docker](https://www.docker.com) sia installato nell'ambiente di sviluppo.

### <a name="download-a-packaged-model"></a>Scaricare un modello di pacchetto

Nell'esempio seguente viene compilata un'immagine, registrata nel registro contenitori di Azure per l'area di lavoro:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Dopo aver creato un pacchetto, è possibile usare `package.pull()` per eseguire il pull dell'immagine nell'ambiente Docker locale. L'output di questo comando visualizzerà il nome dell'immagine. Ad esempio: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Dopo aver scaricato il modello, utilizzare il `docker images` comando per elencare le immagini locali:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Per avviare un contenitore locale basato su questa immagine, usare il comando seguente per avviare un contenitore denominato dalla shell o dalla riga di comando. Sostituire il `<imageid>` valore con l'ID immagine restituito dal `docker images` comando.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Questo comando avvia la versione più recente dell'immagine denominata `myimage` . Esegue il mapping della porta locale 6789 alla porta nel contenitore in cui il servizio Web è in ascolto (5001). Assegna anche il nome `mycontainer` al contenitore, rendendo più semplice l'arresto del contenitore. Dopo l'avvio del contenitore è possibile inviare richieste a `http://localhost:6789/score` .

### <a name="generate-a-dockerfile-and-dependencies"></a>Genera Dockerfile e dipendenze

Nell'esempio seguente viene illustrato come scaricare il Dockerfile, il modello e gli altri asset necessari per compilare un'immagine localmente. Il `generate_dockerfile=True` parametro indica che si desidera che i file non siano un'immagine completamente compilata.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Questo codice Scarica i file necessari per compilare l'immagine nella `imagefiles` Directory. Il Dockerfile incluso nei file salvati fa riferimento a un'immagine di base archiviata in un registro contenitori di Azure. Quando si compila l'immagine nell'installazione Docker locale, è necessario usare l'indirizzo, il nome utente e la password per l'autenticazione al registro di sistema. Usare la procedura seguente per compilare l'immagine usando un'installazione locale di Docker:

1. Da una shell o da una sessione della riga di comando, usare il comando seguente per autenticare Docker con il registro contenitori di Azure. Sostituire `<address>` , `<username>` e `<password>` con i valori recuperati da `package.get_container_registry()` .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Per compilare l'immagine, usare il comando seguente. Sostituire `<imagefiles>` con il percorso della directory in cui `package.save()` sono stati salvati i file.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Questo comando imposta il nome dell'immagine su `myimage` .

Per verificare che l'immagine sia compilata, usare il `docker images` comando. L'immagine dovrebbe essere visualizzata `myimage` nell'elenco:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Per avviare un nuovo contenitore basato su questa immagine, usare il comando seguente:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Questo comando avvia la versione più recente dell'immagine denominata `myimage` . Esegue il mapping della porta locale 6789 alla porta nel contenitore in cui il servizio Web è in ascolto (5001). Assegna anche il nome `mycontainer` al contenitore, rendendo più semplice l'arresto del contenitore. Dopo l'avvio del contenitore è possibile inviare richieste a `http://localhost:6789/score` .

### <a name="example-client-to-test-the-local-container"></a>Client di esempio per il test del contenitore locale

Il codice seguente è un esempio di un client Python che può essere usato con il contenitore:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Per i client di esempio in altri linguaggi di programmazione, vedere [utilizzo di modelli distribuiti come servizi Web](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Arrestare il contenitore Docker

Per arrestare il contenitore, usare il comando seguente da una shell o riga di comando diversa:

```bash
docker kill mycontainer
```

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi relativi a una distribuzione non riuscita](how-to-troubleshoot-deployment.md)
* [Distribuire nel servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md)
* [Creazione di applicazioni client per l'utilizzo di servizi Web](how-to-consume-web-service.md)
* [Aggiornare un servizio Web](how-to-deploy-update-web-service.md)
* [Come distribuire un modello usando un'immagine Docker personalizzata](how-to-deploy-custom-docker-image.md)
* [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](how-to-secure-web-service.md)
* [Monitorare i modelli di Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md)
* [Creare avvisi e trigger per gli eventi per le distribuzioni di modelli](how-to-use-event-grid.md)

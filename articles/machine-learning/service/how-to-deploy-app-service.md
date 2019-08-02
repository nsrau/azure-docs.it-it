---
title: Distribuire modelli di Machine Learning in app Azure Service (anteprima)
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il servizio Azure Machine Learning per distribuire un modello in un'app Web nel servizio app Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/01/2019
ms.openlocfilehash: 11e8b26c81d3a8f672abbeafc153df73d063cb6e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68612236"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Distribuire un modello di machine learning nel servizio app Azure (anteprima)

Informazioni su come distribuire un modello dal servizio Azure Machine Learning come app Web nel servizio app Azure.

> [!IMPORTANT]
> Sebbene sia il servizio Azure Machine Learning sia il servizio app Azure sono disponibili a livello generale, la possibilità di distribuire un modello dal servizio Machine Learning al servizio app è in anteprima.

Con Azure Machine Learning servizio è possibile creare immagini Docker da modelli di apprendimento automatico sottoposti a training. Questa immagine contiene un servizio Web che riceve i dati, li invia al modello e quindi restituisce la risposta. App Azure servizio può essere usato per distribuire l'immagine e fornisce le funzionalità seguenti:

* [Supporto SSL](/azure/app-service/app-service-web-ssl-cert-load) per le comunicazioni sicure tra i client e il servizio.
* [Scalabilità orizzontale](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) in più istanze senza dover ridistribuire.
* [Autenticazione avanzata](/azure/app-service/configure-authentication-provider-aad) per la sicurezza avanzata.

Per altre informazioni sulle funzionalità fornite dal servizio app Azure, vedere [Panoramica del servizio app](/azure/app-service/overview).

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro del servizio Azure Machine Learning. Per altre informazioni, vedere l'articolo [creare un'area di lavoro](setup-create-workspace.md) .
* Un modello di apprendimento automatico sottoposto a training registrato nell'area di lavoro. Se non si dispone di un modello, usare l' [esercitazione relativa alla classificazione delle immagini: Train Model](tutorial-train-models-with-aml.md) per eseguire il training e la registrazione di un modello.
* Immagine Docker creata dal modello. Se non si dispone di un'immagine, usare la [classificazione delle immagini: Distribuisci modello](tutorial-deploy-models-with-aml.md) per crearne una.

## <a name="deploy-image-as-a-web-app"></a>Distribuire un'immagine come app Web

1. Dal [portale di Azure](https://portal.azure.com)selezionare l'area di lavoro Azure Machine Learning. Nella sezione __Panoramica__ usare il collegamento al __Registro di sistema__ per accedere al container Registry di Azure per l'area di lavoro.

    ![Screenshot della panoramica dell'area di lavoro](media/how-to-deploy-app-service/workspace-overview.png)

2. Dal Container Registry di Azure selezionare __repository__, quindi selezionare il __nome dell'immagine__ che si desidera distribuire. Per la versione che si vuole distribuire, selezionare la voce __...__ e quindi eseguire la __distribuzione nell'app Web__.

    ![Screenshot della distribuzione da ACR a un'app Web](media/how-to-deploy-app-service/deploy-to-web-app.png)

3. Per creare l'app Web, specificare un nome di sito, una sottoscrizione, un gruppo di risorse e selezionare il piano di servizio app/percorso. Infine, selezionare __Crea__.

    ![Screenshot della finestra di dialogo nuova app Web](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>Usare l'app Web

Dal [portale di Azure](https://portal.azure.com)selezionare l'app Web creata nel passaggio precedente. Nella sezione __Panoramica__ copiare l' __URL__. Questo valore è l' __URL di base__ del servizio.

![Screenshot della panoramica per l'app Web](media/how-to-deploy-app-service/web-app-overview.png)

Il servizio Web che passa le richieste al modello si trova in `{baseurl}/score`. Ad esempio `https://mywebapp.azurewebsites.net/score`. Il codice Python seguente illustra come inviare dati all'URL e visualizzare la risposta:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sulla configurazione dell'app Web, vedere la documentazione relativa al [servizio app in Linux](/azure/app-service/containers/) .
* Per altre informazioni sul ridimensionamento, vedere Introduzione a scalabilità automatica [in Azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* Per altre informazioni sul supporto SSL, vedere [usare un certificato SSL nel servizio app Azure](/azure/app-service/app-service-web-ssl-cert-load).
* Per altre informazioni sull'autenticazione, vedere [configurare l'app del servizio app per usare Azure Active Directory l'accesso](/azure/app-service/configure-authentication-provider-aad).
---
title: Configurare l'autenticazione per i modelli distribuiti come servizi Web
titleSuffix: Azure Machine Learning
description: Informazioni su come configurare l'autenticazione per i modelli di Machine Learning distribuiti nei servizi Web in Azure Machine Learning.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/06/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bd938fce347d439c2acb4e3fcace04d5b27d770
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447651"
---
# <a name="configure-authentication-for-models-deployed-as-web-services"></a>Configurare l'autenticazione per i modelli distribuiti come servizi Web

Azure Machine Learning consente di distribuire i modelli di apprendimento automatico sottoposti a training come servizi Web. In questo articolo viene illustrato come configurare l'autenticazione per queste distribuzioni.

Le distribuzioni di modelli create da Azure Machine Learning possono essere configurate per usare uno dei due metodi di autenticazione seguenti:

* **basato su chiavi** : una chiave statica viene usata per l'autenticazione nel servizio Web.
* **basato su token** : è necessario ottenere un token temporaneo dall'area di lavoro Azure Machine Learning (usando Azure Active Directory) e usarlo per l'autenticazione al servizio Web. Questo token scade dopo un periodo di tempo e deve essere aggiornato per continuare a usare il servizio Web.

    > [!NOTE]
    > L'autenticazione basata su token è disponibile solo quando si esegue la distribuzione nel servizio Azure Kubernetes.

## <a name="key-based-authentication"></a>Autenticazione basata su chiave

I servizi Web distribuiti in Azure Kubernetes Service (AKS) hanno l'autenticazione basata su chiavi *abilitata* per impostazione predefinita.

Per i servizi distribuiti da istanze di contenitore di Azure (ACI) l'autenticazione basata su chiavi è *disabilitata* per impostazione predefinita, ma è possibile abilitarla impostando `auth_enabled=True` quando si crea il servizio Web ACI. Il codice seguente è un esempio di creazione di una configurazione di distribuzione ACI con l'autenticazione basata su chiavi abilitata.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

È quindi possibile usare la configurazione personalizzata di Istanze di Azure Container nella distribuzione usando la classe `Model`.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Per recuperare il token di autenticazione usare `aci_service.get_keys()`. Per rigenerare una chiave, usare la funzione `regen_key()` e passare **Primaria** o **Secondaria**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

## <a name="token-based-authentication"></a>Autenticazione basata su token

Quando si abilita l'autenticazione tramite token per un servizio Web, gli utenti devono presentare un token JSON Web di Azure Machine Learning al servizio Web per accedervi. Il token scade dopo un periodo di tempo specificato e deve essere aggiornato per continuare a eseguire chiamate.

* L'autenticazione tramite token è **disabilitata per impostazione predefinita** quando si esegue la distribuzione al servizio Azure Kubernetes.
* L'autenticazione tramite token **non è supportata** quando si esegue la distribuzione a Istanze di Azure Container.
* **Non è possibile usare l'autenticazione basata su token allo stesso tempo dell'autenticazione basata su chiavi**.

Per controllare l'autenticazione del token, usare il `token_auth_enabled` parametro quando si crea o si aggiorna una distribuzione:

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

Se l'autenticazione tramite token è abilitata, è possibile usare il metodo `get_token` per recuperare un token JSON Web (JWT) e la data di scadenza del token:

> [!TIP]
> Se si utilizza un'entità servizio per ottenere il token e si desidera che disponga dell'accesso minimo richiesto per recuperare un token, assegnarlo al ruolo **Reader** per l'area di lavoro.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> Al termine della durata del token `refresh_by`, sarà necessario richiedere un nuovo token. Se è necessario aggiornare i token al di fuori di Python SDK, un'opzione consiste nell'usare l'API REST con l'autenticazione dell'entità servizio per effettuare periodicamente la chiamata `service.get_token()`, come illustrato in precedenza.
>
> È consigliabile creare l'area di lavoro Azure Machine Learning nella stessa area del cluster del servizio Azure Kubernetes.
>
> Per eseguire l'autenticazione con un token, il servizio Web effettuerà una chiamata all'area in cui viene creata l'area di lavoro Azure Machine Learning. Se l'area dell'area di lavoro non è disponibile, non sarà possibile recuperare un token per il servizio Web, anche se il cluster si trova in un'area diversa dall'area di lavoro. Il risultato è che Autenticazione di Azure AD non è disponibile fino a quando l'area dell'area di lavoro non sarà nuovamente disponibile.
>
> Inoltre, maggiore è la distanza tra l'area del cluster e l'area dell'area di lavoro, più tempo sarà necessario per recuperare un token.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'autenticazione a un modello distribuito, vedere [creare un client per un modello distribuito come servizio Web](how-to-consume-web-service.md).
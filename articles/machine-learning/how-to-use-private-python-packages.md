---
title: Usare pacchetti Python privati
titleSuffix: Azure Machine Learning
description: Accedere ai pacchetti Python privati in modo sicuro da ambienti Azure Machine Learning.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: laobri
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 58bb08cad111e0744f7831783169901cd76caef4
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91772635"
---
# <a name="use-private-python-packages-with-azure-machine-learning"></a>Usare pacchetti Python privati con Azure Machine Learning


Questo articolo illustra come usare i pacchetti Python privati in modo sicuro all'interno Azure Machine Learning. I casi d'uso per i pacchetti Python privati includono:

 * È stato sviluppato un pacchetto privato che non si vuole condividere pubblicamente.
 * Si vuole usare un repository curato di pacchetti archiviati in un firewall aziendale.

L'approccio consigliato varia a seconda che si disponga di pochi pacchetti per una singola area di lavoro Azure Machine Learning o di un intero repository di pacchetti per tutte le aree di lavoro all'interno di un'organizzazione.

I pacchetti privati vengono utilizzati tramite la classe [Environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment) . All'interno di un ambiente, si dichiarano i pacchetti Python da usare, inclusi quelli privati. Per informazioni sull'ambiente in Azure Machine Learning in generale, vedere [come usare gli ambienti](how-to-use-environments.md). 

## <a name="prerequisites"></a>Prerequisiti

 * [SDK Azure Machine Learning per Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
 * [Area di lavoro Azure Machine Learning](how-to-manage-workspace.md)

## <a name="use-small-number-of-packages-for-development-and-testing"></a>Usare un numero ridotto di pacchetti per lo sviluppo e il test

Per un numero ridotto di pacchetti privati per una singola area di lavoro, usare il [`Environment.add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#&preserve-view=trueadd-private-pip-wheel-workspace--file-path--exist-ok-false-) metodo statico. Questo approccio consente di aggiungere rapidamente un pacchetto privato all'area di lavoro ed è particolarmente adatto a scopo di sviluppo e test.

Puntare l'argomento del percorso del file a un file della rotellina locale ed eseguire il ```add_private_pip_wheel``` comando. Il comando restituisce un URL utilizzato per tenere traccia del percorso del pacchetto all'interno dell'area di lavoro. Acquisire l'URL di archiviazione e passargli il `add_pip_package()` metodo.

```python
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

Internamente, Azure Machine Learning servizio sostituisce l'URL con l'URL di firma di accesso condiviso protetto, in modo che il file della rotellina venga mantenuto privato e sicuro.

## <a name="use-a-repository-of-packages-from-azure-devops-feed"></a>Usare un repository di pacchetti dal feed di DevOps di Azure

Se si stanno sviluppando attivamente pacchetti Python per l'applicazione di Machine Learning, è possibile ospitarli in un repository DevOps di Azure come elementi e pubblicarli come feed. Questo approccio consente di integrare il flusso di lavoro DevOps per la compilazione di pacchetti con i area di lavoro di Azure Machine Learning. Per informazioni su come configurare i feed Python usando Azure DevOps, vedere [Introduzione ai pacchetti Python in Azure Artifacts](https://docs.microsoft.com/azure/devops/artifacts/quickstarts/python-packages?view=azure-devops&preserve-view=true)

Questo approccio usa il token di accesso personale per l'autenticazione nel repository. Lo stesso approccio è applicabile ad altri repository con autenticazione basata su token, ad esempio repository GitHub privati. 

 1. [Creare un token di accesso personale (Pat)](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&preserve-view=true&tabs=preview-page#create-a-pat) per l'istanza di Azure DevOps. Consente di impostare l'ambito del token per la creazione di __pacchetti > lettura__. 

 2. Aggiungere le proprietà URL DevOps di Azure e PAT come area di lavoro usando il metodo [Workspace.set_Connection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true#&preserve-view=trueset-connection-name--category--target--authtype--value-) .

     ```python
    from azureml.core import Workspace
    
    pat_token = input("Enter secret token")
    ws = Workspace.from_config()
    ws.set_connection(name="connection-1", 
        category = "PythonFeed",
        target = "https://<my-org>.pkgs.visualstudio.com", 
        authType = "PAT", 
        value = pat_token) 
     ```

 3. Creare un ambiente di Azure Machine Learning e aggiungere pacchetti Python dal feed.
    
    ```python
    from azureml.core import Environment
    from azureml.core.conda_dependencies import CondaDependencies
    
    env = Environment(name="my-env")
    cd = CondaDependencies()
    cd.add_pip_package("<my-package>")
    cd.set_pip_option("--extra-index-url https://<my-org>.pkgs.visualstudio.com/<my-project>/_packaging/<my-feed>/pypi/simple")
    env.python.conda_dependencies=cd
    ```

L'ambiente è ora pronto per essere usato nelle esecuzioni di training o nelle distribuzioni di endpoint del servizio Web. Quando si compila l'ambiente, Azure Machine Learning servizio usa il PAT per eseguire l'autenticazione nel feed con l'URL di base corrispondente.

## <a name="use-a-repository-of-packages-from-private-storage"></a>Usare un repository di pacchetti dalla risorsa di archiviazione privata

È possibile utilizzare i pacchetti da un account di archiviazione di Azure all'interno del firewall dell'organizzazione. L'account di archiviazione può avere un set curato di pacchetti o un mirror interno dei pacchetti disponibili pubblicamente.

Per configurare tale archiviazione privata, vedere [proteggere un'area di lavoro di Azure Machine Learning e le risorse associate](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints). È anche necessario [inserire il container Registry di Azure (ACR) dietro la VNet](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr).

> [!IMPORTANT]
> È necessario completare questo passaggio per essere in grado di eseguire il training o la distribuzione di modelli tramite il repository dei pacchetti privati.

Al termine di queste configurazioni, è possibile fare riferimento ai pacchetti nella definizione dell'ambiente Azure Machine Learning in base all'URL completo nell'archivio BLOB di Azure.

## <a name="next-steps"></a>Passaggi successivi

 * Scopri di più sulla [sicurezza aziendale in Azure Machine Learning](concept-enterprise-security.md)

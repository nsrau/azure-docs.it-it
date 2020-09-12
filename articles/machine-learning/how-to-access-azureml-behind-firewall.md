---
title: Usare un firewall
titleSuffix: Azure Machine Learning
description: Controllare l'accesso alle aree di lavoro Azure Machine Learning con i firewall di Azure. Informazioni sugli host che è necessario consentire attraverso il firewall per il corretto funzionamento di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 443649826e821014e0e9918526a363a944b5eceb
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89660010"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Usare l'area di lavoro dietro un firewall per Azure Machine Learning

Questo articolo illustra come configurare il firewall di Azure per controllare l'accesso all'area di lavoro Azure Machine Learning e alla rete Internet pubblica.   Per ulteriori informazioni sulla protezione di Azure Machine Learning, vedere [sicurezza aziendale per Azure Machine Learning](concept-enterprise-security.md)

Mentre le informazioni contenute in questo documento si basano sull'uso del [firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md), è possibile usarle con altri prodotti firewall. Per domande su come consentire la comunicazione attraverso il firewall, consultare la documentazione relativa al firewall in uso.

## <a name="application-rules"></a>Regole di applicazione

Nel firewall creare una _regola dell'applicazione_ che consenta il traffico da e verso gli indirizzi in questo articolo.

> [!TIP]
> Quando si aggiunge la regola di rete, impostare il __protocollo__ su Any e le porte su `*` .
>
> Per altre informazioni sulla configurazione del firewall di Azure, vedere [distribuire e configurare il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

## <a name="microsoft-hosts"></a>Host Microsoft

Se non è configurato correttamente, il firewall può causare problemi usando l'area di lavoro. Sono disponibili diversi nomi host usati sia dall'area di lavoro Azure Machine Learning.

Gli host in questa sezione sono di proprietà di Microsoft e forniscono i servizi richiesti per il corretto funzionamento dell'area di lavoro.

| **Nome host** | **Scopo** |
| ---- | ---- |
| **\*. batchai.core.windows.net** | Cluster di training |
| **ml.azure.com** | Azure Machine Learning Studio |
| **default.exp-tas.com** | Usato da Azure Machine Learning Studio |
| **\*. azureml.ms** | Usato dalle API Azure Machine Learning |
| **\*. experiments.azureml.net** | Usato da esperimenti eseguiti in Azure Machine Learning |
| **\*. modelmanagement.azureml.net** | Usato per registrare e distribuire i modelli|
| **mlworkspace.azure.ai** | Utilizzato dal portale di Azure durante la visualizzazione di un'area di lavoro |
| **\*. aether.ms** | Usato durante l'esecuzione di pipeline di Azure Machine Learning |
| **\*. instances.azureml.net** | Azure Machine Learning istanze di calcolo |
| **\*. instances.azureml.ms** | Azure Machine Learning istanze di calcolo quando l'area di lavoro dispone di collegamento privato abilitato |
| **windows.net** | Archiviazione BLOB di Azure |
| **vault.azure.net** | Insieme di credenziali chiave di Azure |
| **azurecr.io** | Registro Azure Container |
| **mcr.microsoft.com** | Microsoft Container Registry per le immagini Docker di base |
| **your-acr-server-name.azurecr.io** | È necessario solo se il Container Registry di Azure è dietro la rete virtuale. In questa configurazione, viene creato un collegamento privato dall'ambiente Microsoft all'istanza di ACR nella sottoscrizione. Usare il nome del server ACR per l'area di lavoro Azure Machine Learning. |
| **\*. notebooks.azure.net** | Necessaria per i notebook in Azure Machine Learning Studio. |
| **graph.windows.net** | Necessaria per i notebook |

## <a name="python-hosts"></a>Host Python

Gli host in questa sezione vengono usati per installare i pacchetti Python. Sono necessari durante lo sviluppo, il training e la distribuzione. 

| **Nome host** | **Scopo** |
| ---- | ---- |
| **anaconda.com** | Utilizzato per installare i pacchetti predefiniti. |
| **\*. anaconda.org** | Usato per ottenere i dati del repository. |
| **pypi.org** | Utilizzato per elencare le dipendenze dall'indice predefinito, se presente, e l'indice non viene sovrascritto dalle impostazioni utente. Se l'indice viene sovrascritto, è necessario consentire anche ** \* . pythonhosted.org**. |

## <a name="r-hosts"></a>Host R

Gli host in questa sezione vengono usati per installare i pacchetti R. Sono necessari durante lo sviluppo, il training e la distribuzione.

> [!IMPORTANT]
> Internamente, R SDK per Azure Machine Learning usa i pacchetti Python. È quindi necessario consentire gli host Python anche attraverso il firewall.

| **Nome host** | **Scopo** |
| ---- | ---- |
| **cloud.r-project.org** | Usato quando si installano i pacchetti CRAN. |

## <a name="azure-government-region"></a>Area di Azure per enti pubblici

URL richiesti per le aree di Azure per enti pubblici.

| **Nome host** | **Scopo** |
| ---- | ---- |
| **usgovarizona.api.ml.azure.us** | Area US-Arizona |
| **usgovvirginia.api.ml.azure.us** | Regione US-Virginia |

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure](../firewall/tutorial-firewall-deploy-portal.md)
* [Proteggere i processi di sperimentazione e inferenza di Azure ML in una rete virtuale di Azure](how-to-network-security-overview.md)

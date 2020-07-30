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
ms.custom: how-to, tracking-python
ms.openlocfilehash: 63e2ba93ecdc1131be6bd291fe436b42a2a2d19c
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407031"
---
# <a name="use-workspace-behind-azure-firewall-for-azure-machine-learning"></a>Usare l'area di lavoro dietro il firewall di Azure per Azure Machine Learning

Questo articolo illustra come configurare il firewall di Azure per l'uso con un'area di lavoro Azure Machine Learning.

Il firewall di Azure può essere usato per controllare l'accesso all'area di lavoro Azure Machine Learning e alla rete Internet pubblica. Se non è configurato correttamente, il firewall può causare problemi usando l'area di lavoro. Sono disponibili diversi nomi host usati entrambi dall'area di lavoro Azure Machine Learning, descritti in questo articolo.

## <a name="network-rules"></a>Regole di rete

Nel firewall creare una regola di rete che consenta il traffico da e verso gli indirizzi di questo articolo.

> [!TIP]
> Quando si aggiunge la regola di rete, impostare il __protocollo__ su Any e le porte su `*` .
>
> Per altre informazioni sulla configurazione del firewall di Azure, vedere [distribuire e configurare il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule).

## <a name="microsoft-hosts"></a>Host Microsoft

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

Passaggi successivi

* [[Distribuire e configurare il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)]
* [Proteggere i processi di sperimentazione e inferenza di Azure ML in una rete virtuale di Azure](how-to-enable-virtual-network.md)

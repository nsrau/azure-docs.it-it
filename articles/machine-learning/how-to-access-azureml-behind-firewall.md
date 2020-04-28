---
title: Usare Azure Machine Learning protetti da un firewall
titleSuffix: Azure Machine Learning
description: Usare in modo sicuro Azure Machine Learning dietro il firewall di Azure. Informazioni sugli host che è necessario consentire attraverso il firewall per il corretto funzionamento di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/27/2020
ms.openlocfilehash: 2c5fe9d46ef408775cc4b99d4820a652a91683c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196322"
---
# <a name="use-azure-machine-learning-workspace-behind-azure-firewall"></a>Usare Azure Machine Learning area di lavoro dietro il firewall di Azure

Questo articolo contiene informazioni sulla configurazione del firewall di Azure per l'uso con Azure Machine Learning.

Il firewall di Azure può essere usato per controllare l'accesso all'area di lavoro Azure Machine Learning e alla rete Internet pubblica. Se non è configurato correttamente, il firewall può causare problemi usando l'area di lavoro.

## <a name="network-rules"></a>Regole di rete

Nel firewall creare una regola di rete che consenta il traffico da e verso gli indirizzi di questo articolo.

> [!TIP]
> Quando si aggiunge la regola di rete, impostare il __protocollo__ su Any e le porte `*`su.
>
> Per altre informazioni sulla configurazione del firewall di Azure, vedere [distribuire e configurare il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule).

## <a name="microsoft-hosts"></a>Host Microsoft

Gli host in questa sezione sono di proprietà di Microsoft e forniscono i servizi richiesti per il corretto funzionamento dell'area di lavoro.

| **Nome host** | **Scopo** |
| ---- | ---- |
| **\*. batchai.core.windows.net** | Cluster di training |
| **ml.azure.com** | Azure Machine Learning Studio |
| **\*. azureml.ms** | Usato dalle API Azure Machine Learning |
| **\*. experiments.azureml.net** | Usato da esperimenti eseguiti in Azure Machine Learning|
| **\*. modelmanagement.azureml.net** | Usato per registrare e distribuire i modelli|
| **mlworkspace.azure.ai** | Utilizzato dal portale di Azure durante la visualizzazione di un'area di lavoro |
| **\*. aether.ms** | Usato durante l'esecuzione di pipeline di Azure Machine Learning |
| **\*. instances.azureml.net** | Azure Machine Learning istanze di calcolo |
| **windows.net** | Archiviazione BLOB di Azure |
| **vault.azure.net** | Insieme di credenziali chiave di Azure |
| **microsoft.com** | Immagini Docker di base |
| **azurecr.io** | Registro Azure Container |

## <a name="python-hosts"></a>Host Python

Gli host in questa sezione vengono usati per installare i pacchetti Python. Sono necessari durante lo sviluppo, il training e la distribuzione. 

| **Nome host** | **Scopo** |
| ---- | ---- |
| **anaconda.com** | Usato durante l'installazione di pacchetti conda |
| **pypi.org** | Usato durante l'installazione di pacchetti PIP |

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
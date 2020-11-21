---
title: Usare un firewall
titleSuffix: Azure Machine Learning
description: Controllare l'accesso alle aree di lavoro Azure Machine Learning con i firewall di Azure. Informazioni sugli host che è necessario consentire attraverso il firewall.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/18/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: cf89532fc41b10d6fbcba57963ebe30a361a2e6f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95012975"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Usare l'area di lavoro dietro un firewall per Azure Machine Learning

Questo articolo illustra come configurare il firewall di Azure per controllare l'accesso all'area di lavoro Azure Machine Learning e alla rete Internet pubblica. Per ulteriori informazioni sulla protezione di Azure Machine Learning, vedere [sicurezza aziendale per Azure Machine Learning](concept-enterprise-security.md)

## <a name="azure-firewall"></a>Firewall di Azure

Quando si usa il firewall di Azure, usare __Network Address Translation di destinazione (DNAT)__ per creare regole NAT per il traffico in ingresso. Per il traffico in uscita, creare regole di __rete__ e/o __applicazione__ . Queste raccolte di regole sono descritte più dettagliatamente in informazioni sui [concetti di Azure firewall](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts).

### <a name="inbound-configuration"></a>Configurazione in ingresso

Se si usa un' __istanza di calcolo__ Azure Machine Learning o un __cluster di calcolo__, aggiungere le [route definite dall'utente (UDR)](../virtual-network/virtual-networks-udr-overview.md) per la subnet che contiene le risorse Azure Machine Learning. Questa route forza il traffico __dagli__ indirizzi IP delle `BatchNodeManagement` risorse e `AzureMachineLearning` all'indirizzo IP pubblico dell'istanza di calcolo e del cluster di elaborazione.

Queste route consentono al servizio Batch di comunicare con i nodi di calcolo per la pianificazione delle attività. Aggiungere anche l'indirizzo IP per il servizio Azure Machine Learning in cui si trovano le risorse, poiché è necessario per l'accesso alle istanze di calcolo. Per ottenere un elenco di indirizzi IP del servizio Batch e del servizio Azure Machine Learning, usare uno dei metodi seguenti:

* Scaricare [gli intervalli IP e i tag del servizio di Azure](https://www.microsoft.com/download/details.aspx?id=56519) e cercare `BatchNodeManagement.<region>` e `AzureMachineLearning.<region>` nel file, dove `<region>` è l'area di Azure.

* Usare l['interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) per scaricare le informazioni. L'esempio seguente scarica le informazioni relative all'indirizzo IP e filtra le informazioni per l'area Stati Uniti orientali 2:

    ```azurecli-interactive
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
    ```

    > [!TIP]
    > Se si usano le aree Stati Uniti-Virginia, US-Arizona o Cina-Est-2, questi comandi non restituiscono indirizzi IP. Usare invece uno dei collegamenti seguenti per scaricare un elenco di indirizzi IP:
    >
    > * [Intervalli IP di Azure e tag dei servizi per Azure per enti pubblici](https://www.microsoft.com/download/details.aspx?id=57063)
    > * [Intervalli IP di Azure e tag dei servizi per Azure Cina](https://www.microsoft.com//download/details.aspx?id=57062)

Quando si aggiungono le route definite dall'utente, definire la route per ogni prefisso dell'indirizzo IP di Batch correlato e impostare __Tipo hop successivo__ su __Internet__. La figura seguente illustra un esempio di route definita dall'utente nel portale di Azure:

![Esempio di route definita dall'utente per un prefisso di indirizzo](./media/how-to-enable-virtual-network/user-defined-route.png)

> [!IMPORTANT]
> Gli indirizzi IP possono cambiare nel tempo.

Per altre informazioni, vedere [Creare un pool di Azure Batch in una rete virtuale](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="outbound-configuration"></a>Configurazione in uscita

1. Aggiungere le __regole di rete__, consentendo __il traffico__ da e verso i tag __di__ servizio seguenti:

    * AzureActiveDirectory
    * AzureMachineLearning
    * AzureResourceManager
    * Storage. Region
    * Vault. Region
    * ContainerRegistry. Region

    Se si prevede di usare le immagini Docker predefinite fornite da Microsoft e di abilitare le dipendenze gestite dall'utente, è necessario aggiungere anche i tag di servizio seguenti:

    * MicrosoftContainerRegistry. Region
    * AzureFrontDoor.FirstParty

    Per le voci che contengono `region` , sostituire con l'area di Azure in uso. Ad esempio: `keyvault.westus`.

    Per il __protocollo__ selezionare `TCP` . Per le __porte__ di origine e di destinazione, selezionare `*` .

1. Aggiungere __regole applicazione__ per gli host seguenti:

    > [!NOTE]
    > Non si tratta di un elenco completo degli host richiesti per tutte le risorse Python su Internet, ma solo dei più usati. Ad esempio, se è necessario accedere a un repository GitHub o ad altri host, è necessario identificare e aggiungere gli host necessari per tale scenario.

    | **Nome host** | **Scopo** |
    | ---- | ---- |
    | **anaconda.com**</br>**\*. anaconda.com** | Utilizzato per installare i pacchetti predefiniti. |
    | **\*. anaconda.org** | Usato per ottenere i dati del repository. |
    | **pypi.org** | Utilizzato per elencare le dipendenze dall'indice predefinito, se presente, e l'indice non viene sovrascritto dalle impostazioni utente. Se l'indice viene sovrascritto, è necessario consentire anche **\* . pythonhosted.org**. |
    | **cloud.r-project.org** | Usato quando si installano pacchetti CRAN per lo sviluppo R. |
    | **\*pytorch.org** | Utilizzato da alcuni esempi basati su PyTorch. |
    | **\*. tensorflow.org** | Utilizzato da alcuni esempi basati su Tensorflow. |

    Per __protocollo: porta__ selezionare Usa __http, HTTPS__.

    Per altre informazioni sulla configurazione delle regole dell'applicazione, vedere [distribuire e configurare il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

1. Per limitare l'accesso ai modelli distribuiti in Azure Kubernetes Service (AKS), vedere [limitare il traffico in uscita nel servizio Kubernetes di Azure](../aks/limit-egress-traffic.md).

## <a name="other-firewalls"></a>Altri firewall

Le linee guida in questa sezione sono generiche, perché ogni firewall ha una propria terminologia e configurazioni specifiche. Per domande su come consentire la comunicazione attraverso il firewall, consultare la documentazione relativa al firewall in uso.

Se non è configurato correttamente, il firewall può causare problemi usando l'area di lavoro. Sono disponibili diversi nomi host usati sia dall'area di lavoro Azure Machine Learning. Nelle sezioni seguenti sono elencati gli host necessari per Azure Machine Learning.

### <a name="microsoft-hosts"></a>Host Microsoft

Gli host in questa sezione sono di proprietà di Microsoft e forniscono i servizi richiesti per il corretto funzionamento dell'area di lavoro. Le tabelle seguenti elencano i nomi host per le aree 21Vianet pubblico di Azure, Azure per enti pubblici e Azure Cina.

**Host di Azure generali**

| **Necessario per** | **Pubblico di Azure** | **Azure per enti pubblici** | **Azure Cina (21Vianet)** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Portale di Azure | management.azure.com | management.azure.us | management.azure.cn |

**Host Azure Machine Learning**

| **Necessario per** | **Pubblico di Azure** | **Azure per enti pubblici** | **Azure Cina (21Vianet)** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*. azureml.ms | \*. ml.azure.us | \*. ml.azure.cn |
| Sperimentazione, cronologia, iperguida, assegnazione di etichette | \*. experiments.azureml.net | \*. ml.azure.us | \*. ml.azure.cn |
| Gestione di modelli | \*. modelmanagement.azureml.net | \*. ml.azure.us | \*. ml.azure.cn |
| Pipeline | \*. aether.ms | \*. ml.azure.us | \*. ml.azure.cn |
| Finestra di progettazione (servizio Studio) | \*. studioservice.azureml.com | \*. ml.azure.us | \*. ml.azure.cn |
| Notebook integrato | \*. notebooks.azure.net | \*. notebooks.usgovcloudapi.net |\*. notebooks.chinacloudapi.cn |
| Notebook integrato | \*. file.core.windows.net | \*. file.core.usgovcloudapi.net | \*. file.core.chinacloudapi.cn |
| Notebook integrato | \*.dfs.core.windows.net | \*. dfs.core.usgovcloudapi.net | \*. dfs.core.chinacloudapi.cn |
| Notebook integrato | \*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net | \*. blob.core.chinacloudapi.cn |
| Notebook integrato | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| Notebook integrato | \*. aznbcontent.net |  | |

**Azure Machine Learning istanze di calcolo e host del cluster di calcolo**

| **Necessario per** | **Pubblico di Azure** | **Azure per enti pubblici** | **Azure Cina (21Vianet)** |
| ----- | ----- | ----- | ----- |
| Cluster/istanza di calcolo | \*. batchai.core.windows.net | \*. batchai.core.usgovcloudapi.net |\*. batchai.ml.azure.cn |
| Istanza di calcolo | \*. instances.azureml.net | \*. instances.azureml.us | \*. instances.azureml.cn |
| Istanza di calcolo | \*. instances.azureml.ms |  |  |

**Risorse associate utilizzate da Azure Machine Learning**

| **Necessario per** | **Pubblico di Azure** | **Azure per enti pubblici** | **Azure Cina (21Vianet)** |
| ----- | ----- | ----- | ----- |
| Account di archiviazione di Azure | core.windows.net | core.usgovcloudapi.net | core.chinacloudapi.cn |
| Insieme di credenziali chiave di Azure | vault.azure.net | vault.usgovcloudapi.net | vault.azure.cn |
| Registro Azure Container | azurecr.io | azurecr.us | azurecr.cn |
| Registro Container Microsoft | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |


> [!TIP]
> Se si prevede di usare l'identità federata, seguire le procedure consigliate [per la protezione di Active Directory Federation Services](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) articolo.

Usare inoltre le informazioni del [tunneling forzato](how-to-secure-training-vnet.md#forced-tunneling) per aggiungere indirizzi IP per `BatchNodeManagement` e `AzureMachineLearning` .

Per informazioni su come limitare l'accesso ai modelli distribuiti in Azure Kubernetes Service (AKS), vedere [limitare il traffico in uscita nel servizio Kubernetes di Azure](../aks/limit-egress-traffic.md).

### <a name="python-hosts"></a>Host Python

Gli host in questa sezione vengono usati per installare i pacchetti Python. Sono necessari durante lo sviluppo, il training e la distribuzione. 

> [!NOTE]
> Non si tratta di un elenco completo degli host richiesti per tutte le risorse Python su Internet, ma solo dei più usati. Ad esempio, se è necessario accedere a un repository GitHub o ad altri host, è necessario identificare e aggiungere gli host necessari per tale scenario.

| **Nome host** | **Scopo** |
| ---- | ---- |
| **anaconda.com**</br>**\*. anaconda.com** | Utilizzato per installare i pacchetti predefiniti. |
| **\*. anaconda.org** | Usato per ottenere i dati del repository. |
| **pypi.org** | Utilizzato per elencare le dipendenze dall'indice predefinito, se presente, e l'indice non viene sovrascritto dalle impostazioni utente. Se l'indice viene sovrascritto, è necessario consentire anche **\* . pythonhosted.org**. |
| **\*pytorch.org** | Utilizzato da alcuni esempi basati su PyTorch. |
| **\*. tensorflow.org** | Utilizzato da alcuni esempi basati su Tensorflow. |

### <a name="r-hosts"></a>Host R

Gli host in questa sezione vengono usati per installare i pacchetti R. Sono necessari durante lo sviluppo, il training e la distribuzione.

> [!NOTE]
> Non si tratta di un elenco completo degli host richiesti per tutte le risorse R su Internet, ma solo dei più usati. Ad esempio, se è necessario accedere a un repository GitHub o ad altri host, è necessario identificare e aggiungere gli host necessari per tale scenario.

| **Nome host** | **Scopo** |
| ---- | ---- |
| **cloud.r-project.org** | Usato quando si installano i pacchetti CRAN. |

> [!IMPORTANT]
> Internamente, R SDK per Azure Machine Learning usa i pacchetti Python. È quindi necessario consentire gli host Python anche attraverso il firewall.
## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure](../firewall/tutorial-firewall-deploy-portal.md)
* [Proteggere i processi di sperimentazione e inferenza di Azure ML in una rete virtuale di Azure](how-to-network-security-overview.md)

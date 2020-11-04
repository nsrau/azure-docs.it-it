---
title: Che cosa sono le destinazioni di calcolo
titleSuffix: Azure Machine Learning
description: Definire il punto in cui si desidera eseguire il training o distribuire il modello con Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/29/2020
ms.openlocfilehash: 72cdfe2ebe79abb9649ac497a6ecddda8918b951
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322273"
---
# <a name="what-are-compute-targets-in-azure-machine-learning"></a>Cosa sono le destinazioni di calcolo in Azure Machine Learning?

Una *destinazione di calcolo* è una risorsa o un ambiente di calcolo designato in cui si esegue lo script di training o si ospita la distribuzione del servizio. Questo percorso potrebbe essere il computer locale o una risorsa di calcolo basata sul cloud. L'uso di destinazioni di calcolo semplifica la modifica dell'ambiente di calcolo in un secondo momento senza dover modificare il codice.

In un ciclo di vita di sviluppo di modelli tipico, è possibile:

1. Inizia sviluppando e sperimentando una piccola quantità di dati. In questa fase, usare l'ambiente locale, ad esempio un computer locale o una macchina virtuale (VM) basata sul cloud, come destinazione di calcolo.
1. Ridimensionare fino a dati più grandi o eseguire il training distribuito usando una di queste [destinazioni di calcolo di training](#train).
1. Quando il modello è pronto, è possibile distribuirlo in un ambiente di hosting Web o in un dispositivo tutto con una di queste [destinazioni di calcolo della distribuzione](#deploy).

Le risorse di calcolo usate per le destinazioni di calcolo sono collegate a un' [area di lavoro](concept-workspace.md). Le risorse di calcolo diverse dal computer locale sono condivise dagli utenti dell'area di lavoro.

## <a name="training-compute-targets"></a><a name="train"></a> Training delle destinazioni di calcolo

Azure Machine Learning offre un supporto variabile per le diverse destinazioni di calcolo. Un ciclo di vita di sviluppo di modelli tipico inizia con lo sviluppo o la sperimentazione su una piccola quantità di dati. In questa fase usare un ambiente locale come il computer locale o una macchina virtuale basata sul cloud. Quando si aumenta il training su set di impostazioni più grandi o si esegue un training distribuito, usare Azure Machine Learning calcolo per creare un cluster a nodo singolo o a più nodi che esegue la scalabilità automatica ogni volta che si invia un'esecuzione. È anche possibile aggiungere la propria risorsa di calcolo, sebbene il supporto per scenari diversi possa variare.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Altre informazioni su come [inviare un'esecuzione di training a una destinazione di calcolo](how-to-set-up-training-targets.md).

## <a name="compute-targets-for-inference"></a><a name="deploy"></a> Destinazioni di calcolo per l'inferenza

Per ospitare la distribuzione del modello, è possibile usare le risorse di calcolo seguenti.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Quando si esegue l'inferenza, Azure Machine Learning crea un contenitore Docker che ospita il modello e le risorse associate necessarie per usarlo. Questo contenitore viene quindi utilizzato in uno degli scenari di distribuzione seguenti:

* Come *servizio Web* utilizzato per l'inferenza in tempo reale. Le distribuzioni di servizi Web usano una delle seguenti destinazioni di calcolo:

    * [Computer locale](how-to-attach-compute-targets.md#local)
    * [Istanza di calcolo di Azure Machine Learning](how-to-create-manage-compute-instance.md)
    * [Istanze di Azure Container](how-to-attach-compute-targets.md#aci)
    * [Servizio Azure Kubernetes](how-to-create-attach-kubernetes.md)
    * Funzioni di Azure (anteprima). La distribuzione in funzioni si basa solo su Azure Machine Learning per compilare il contenitore docker. Da qui viene distribuito usando le funzioni. Per altre informazioni, vedere [distribuire un modello di Machine Learning in funzioni di Azure (anteprima)](how-to-deploy-functions.md).

* Come endpoint di _inferenza batch_ usato per elaborare periodicamente i batch di dati. Le inferenze batch usano [Azure Machine Learning cluster di elaborazione](how-to-create-attach-compute-cluster.md).

* In un _dispositivo_ Internet (anteprima). La distribuzione in un dispositivo Internet è basata solo su Azure Machine Learning per compilare il contenitore docker. Da qui viene distribuito usando Azure IoT Edge. Per altre informazioni, vedere [deploy As a IOT Edge Module (Preview)](../iot-edge/tutorial-deploy-machine-learning.md).

Informazioni su [dove e come distribuire il modello in una destinazione di calcolo](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Calcolo Azure Machine Learning (gestito)

Una risorsa di calcolo gestita viene creata e gestita da Azure Machine Learning. Questo calcolo è ottimizzato per i carichi di lavoro di machine learning. Azure Machine Learning i cluster di calcolo e le [istanze di calcolo](concept-compute-instance.md) sono gli unici calcoli gestiti.

È possibile creare Azure Machine Learning istanze di calcolo o cluster di calcolo da:

* [Azure Machine Learning Studio](how-to-create-attach-compute-studio.md).
* Python SDK e l'interfaccia della riga di comando:
    * [Istanza di calcolo](how-to-create-manage-compute-instance.md).
    * [Cluster di elaborazione](how-to-create-attach-compute-cluster.md).
* [SDK R](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets) (anteprima).
* Un modello di Azure Resource Manager. Per un modello di esempio, vedere [creare un cluster di calcolo Azure Machine Learning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-amlcompute).
* Un' [estensione di machine learning per l'interfaccia della riga di comando di Azure](reference-azure-machine-learning-cli.md#resource-management).

Quando vengono create, queste risorse di calcolo fanno automaticamente parte dell'area di lavoro, a differenza di altri tipi di destinazioni di calcolo.


|Funzionalità  |Cluster di calcolo  |Istanza di calcolo  |
|---------|---------|---------|
|Cluster a nodo singolo o a più nodi     |    **&check;**       |         |
|Scalabilità automatica ogni volta che si invia un'esecuzione     |     **&check;**      |         |
|Gestione automatica dei cluster e pianificazione dei processi     |   **&check;**        |     **&check;**      |
|Include il supporto per le risorse di CPU e GPU     |  **&check;**         |    **&check;**       |


> [!NOTE]
> Quando un *cluster* di calcolo è inattivo, viene ridimensionato automaticamente a 0 nodi, quindi non si paga se non è in uso. Un' *istanza* di calcolo è sempre attiva e non esegue la scalabilità automatica. È necessario [arrestare l'istanza di calcolo](how-to-create-manage-compute-instance.md#manage) quando non viene usata per evitare costi aggiuntivi.

### <a name="supported-vm-series-and-sizes"></a>Serie e dimensioni di macchine virtuali supportate

Quando si selezionano le dimensioni di un nodo per una risorsa di calcolo gestita in Azure Machine Learning, è possibile scegliere tra le dimensioni di VM selezionate disponibili in Azure. Azure offre una gamma di dimensioni per Linux e Windows per carichi di lavoro diversi. Per altre informazioni, vedere [tipi e dimensioni delle macchine virtuali](../virtual-machines/sizes.md).

Esistono alcune eccezioni e limitazioni nella scelta delle dimensioni delle macchine virtuali:

* Alcune serie di macchine virtuali non sono supportate in Azure Machine Learning.
* Alcune serie di macchine virtuali sono limitate. Per usare una serie con restrizioni, contattare il supporto tecnico e richiedere un aumento della quota per la serie. Per informazioni su come contattare il supporto tecnico, vedere [Opzioni di supporto di Azure](https://azure.microsoft.com/support/options/).

Vedere la tabella seguente per altre informazioni sulle restrizioni e sulle serie supportate.

| **Serie di macchine virtuali supportate**  | **Restrizioni** |
|------------|------------|
| D | Nessuno. |
| Dv2 | Nessuno. |  
| Dv3 | Nessuno.|
| DSv2 | Nessuno. | 
| DSv3 | Nessuno.|
| FSv2 | Nessuno. | 
| HBv2 | Richiede l'approvazione. |  
| Servizio di elaborazione host | Richiede l'approvazione. |  
| M | Richiede l'approvazione. |
| NC | Nessuno. |    
| NCsv2 | Richiede l'approvazione. |
| NCsv3 | Richiede l'approvazione. |  
| NDs | Richiede l'approvazione. |
| NDv2 | Richiede l'approvazione. |
| NV | Nessuno. |
| NVv3 | Richiede l'approvazione. | 


Mentre Azure Machine Learning supporta queste serie di macchine virtuali, potrebbero non essere disponibili in tutte le aree di Azure. Per verificare se sono disponibili serie di macchine virtuali, vedere [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

### <a name="compute-isolation"></a>Isolamento del calcolo

Azure Machine Learning calcolo offre dimensioni di VM isolate a un tipo di hardware specifico e dedicate a un singolo cliente. Le dimensioni delle VM isolate sono ideali per i carichi di lavoro che richiedono un elevato livello di isolamento rispetto ai carichi di lavoro di altri clienti per motivi che includono requisiti di conformità e normativi. L'uso di una dimensione isolata garantisce che la macchina virtuale sia l'unica in esecuzione nell'istanza del server specifica.

Le offerte di VM isolate correnti includono:

* Standard_M128ms
* Standard_F72s_v2
* Standard_NC24s_v3
* Standard_NC24rs_v3*

*Con supporto di RDMA

Per altre informazioni sull'isolamento, vedere [isolamento nel cloud pubblico di Azure](../security/fundamentals/isolation-choices.md).

## <a name="unmanaged-compute"></a>Calcolo non gestito

Una destinazione di calcolo *non gestita non* è gestita da Azure Machine Learning. Questo tipo di destinazione di calcolo viene creato all'esterno Azure Machine Learning e quindi collegato all'area di lavoro. Le risorse di calcolo non gestite possono richiedere passaggi aggiuntivi per la manutenzione o per migliorare le prestazioni dei carichi di lavoro di machine learning.

## <a name="next-steps"></a>Passaggi successivi

Viene illustrato come:
* [Usare una destinazione di calcolo per eseguire il training del modello](how-to-set-up-training-targets.md)
* [Distribuire il modello in una destinazione di calcolo](how-to-deploy-and-where.md)
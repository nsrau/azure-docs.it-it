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
ms.date: 03/30/2020
ms.openlocfilehash: ed65d69c18f2dbcd53324fe3cc18af8c51c546b2
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780114"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Che cosa sono le destinazioni di calcolo in Azure Machine Learning? 

Una **destinazione di calcolo** è una risorsa o un ambiente di calcolo designato in cui si esegue lo script di training o si ospita la distribuzione del servizio. Questo percorso può essere il computer locale o una risorsa di calcolo basata sul cloud. L'uso di destinazioni di calcolo semplifica la modifica dell'ambiente di calcolo in un secondo momento senza dover modificare il codice.  

In un ciclo di vita di sviluppo di modelli tipico, è possibile:
1. Inizia sviluppando e sperimentando una piccola quantità di dati. In questa fase è consigliabile usare l'ambiente locale (computer locale o macchina virtuale basata sul cloud) come destinazione di calcolo. 
2. Scalabilità verticale a dati più grandi oppure Esegui il training distribuito usando una di queste [destinazioni di calcolo](#train)per il training.  
3. Quando il modello è pronto, è possibile distribuirlo in un ambiente di hosting Web o in un dispositivo tutto con una di queste [destinazioni di calcolo della distribuzione](#deploy).

Le risorse di calcolo usate per le destinazioni di calcolo sono collegate a un' [area di lavoro](concept-workspace.md). Le risorse di calcolo diverse dal computer locale sono condivise dagli utenti dell'area di lavoro.

## <a name="training-compute-targets"></a><a name="train"></a>Training delle destinazioni di calcolo

Azure Machine Learning offre un supporto variabile tra le diverse risorse di calcolo.  È anche possibile aggiungere una risorsa di calcolo personalizzata, anche se il supporto per vari scenari può variare.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Altre informazioni sulla [configurazione e l'uso di una destinazione di calcolo per il training del modello](how-to-set-up-training-targets.md).

## <a name="deployment-targets"></a><a name="deploy"></a>Destinazioni della distribuzione

Per ospitare la distribuzione del modello, è possibile usare le risorse di calcolo seguenti.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Informazioni su [dove e come distribuire il modello in una destinazione di calcolo](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Calcolo Azure Machine Learning (gestito)

Una risorsa di calcolo gestita viene creata e gestita da Azure Machine Learning. Questo calcolo è ottimizzato per i carichi di lavoro di machine learning. Azure Machine Learning i cluster di calcolo e le [istanze di calcolo](concept-compute-instance.md) sono gli unici calcoli gestiti. In futuro potrebbero essere aggiunte risorse di calcolo gestite aggiuntive.

È possibile creare Azure Machine Learning istanze di calcolo (anteprima) o cluster di calcolo da:
* Azure Machine Learning Studio
* Portale di Azure
* Classi [ComputeInstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py) e [AMLCOMPUTE](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py) per Python SDK
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets)
* Modello di Resource Manager

È anche possibile creare cluster di calcolo usando l' [estensione di machine learning per l'interfaccia della riga di comando di Azure](tutorial-train-deploy-model-cli.md#create-the-compute-target-for-training).

Quando vengono create, queste risorse di calcolo fanno automaticamente parte dell'area di lavoro, a differenza di altri tipi di destinazioni di calcolo.

### <a name="compute-clusters"></a>Cluster di elaborazione

È possibile usare Azure Machine Learning cluster di calcolo per il training e l'inferenza in batch (anteprima).  Con questa risorsa di calcolo sono disponibili:

* Cluster a nodo singolo o a più nodi
* Ridimensionamento automatico ogni volta che si invia un'esecuzione 
* Gestione automatica dei cluster e pianificazione dei processi 
* Include il supporto per le risorse di CPU e GPU

### <a name="supported-vm-series-and-sizes"></a>Serie e dimensioni delle VM supportate

Quando si selezionano le dimensioni di un nodo per una risorsa di calcolo gestita in Azure Machine Learning, è possibile scegliere tra le dimensioni di VM selezionate disponibili in Azure. Azure offre una gamma di dimensioni per Linux e Windows per carichi di lavoro diversi. Vedere qui per altre informazioni sui diversi [tipi di VM e dimensioni](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

Esistono alcune eccezioni e limitazioni nella scelta delle dimensioni delle macchine virtuali:
* Alcune serie di macchine virtuali non sono supportate in Azure Machine Learning.
* Alcune serie di macchine virtuali sono limitate. Per usare una serie con restrizioni, contattare il supporto tecnico e richiedere un aumento della quota per la serie. Per informazioni su come contattare il supporto tecnico, vedere [Opzioni di supporto di Azure](https://azure.microsoft.com/support/options/)

Vedere la tabella seguente per altre informazioni sulle restrizioni e sulle serie supportate. 

| **Serie di macchine virtuali supportate**  | **Restrizioni** |
|------------|------------|
| D | Nessuno |
| Dv2 | Nessuno |  
| DSv2 | Nessuno |  
| FSv2 | Nessuno |  
| M | Richiede approvazione |
| NC | Nessuno |    
| NCsv2 | Richiede approvazione |
| NCsv3 | Richiede approvazione |  
| NDs | Richiede approvazione |
| NDv2 | Richiede approvazione |
| NV | Nessuno |
| NVv3 | Richiede approvazione | 


Mentre Azure Machine Learning supporta queste serie di macchine virtuali, potrebbero non essere disponibili in tutte le aree di Azure. È possibile verificare con le serie di VM disponibili qui: [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

## <a name="unmanaged-compute"></a>Calcolo non gestito

Una destinazione di calcolo *non gestita non* è gestita da Azure Machine Learning. Questo tipo di destinazione di calcolo viene creato all'esterno Azure Machine Learning, quindi collegato all'area di lavoro. Le risorse di calcolo non gestite possono richiedere passaggi aggiuntivi per la manutenzione o per migliorare le prestazioni dei carichi di lavoro di machine learning.

## <a name="next-steps"></a>Passaggi successivi

Viene illustrato come:
* [Configurare una destinazione di calcolo per il training del modello](how-to-set-up-training-targets.md)
* [Distribuire il modello in una destinazione di calcolo](how-to-deploy-and-where.md)

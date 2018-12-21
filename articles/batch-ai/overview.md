---
title: Servizio Azure Batch per intelligenza artificiale - Training dell'intelligenza artificiale | Microsoft Docs
description: Informazioni sull'uso del servizio gestito Azure Batch per intelligenza artificiale per eseguire il training dell'intelligenza artificiale e di altri modelli di apprendimento automatico in cluster di GPU e CPU.
services: batch-ai
documentationcenter: ''
author: alexsuttonms
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/01/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 37f1bf5dc20d097f7f5f560e3bf1fdd25dc38045
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408052"
---
# <a name="what-is-azure-batch-ai"></a>Informazioni su Azure Batch per intelligenza artificiale

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Azure Batch per intelligenza artificiale è un servizio gestito che consente ai data scientist e ai ricercatori in ambito di intelligenza artificiale di eseguire il training e il test di modelli di Machine Learning e intelligenza artificiale su larga scala, senza dover gestire infrastrutture complesse. È sufficiente descrivere le risorse di calcolo, i requisiti del processo, i processi da eseguire, dove archiviare gli input e gli output del modello. Tutto il resto viene gestito da Batch per intelligenza artificiale.

È possibile usare Batch per intelligenza artificiale in modo autonomo o per eseguire il training di modelli nell'ambito di un flusso di lavoro di sviluppo di grandi dimensioni:

* Usare Batch per intelligenza artificiale in modo autonomo per eseguire il training, il test e l'assegnazione del punteggio batch dei modelli di apprendimento automatico e di intelligenza artificiale nei cluster di [GPU](../virtual-machines/linux/sizes-gpu.md) o CPU. 

* Usare un cluster Batch per intelligenza artificiale in un flusso di lavoro con [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) o altri [strumenti della piattaforma di intelligenza artificiale di Azure](https://azure.microsoft.com/overview/ai-platform/). Azure Machine Learning offre un'esperienza avanzata per la preparazione dei dati, la sperimentazione e la cronologia processo. La soluzione è in grado di creare un pacchetto di un modello con training in un contenitore e di distribuire un modello per inferenza o su dispositivi IoT.  

## <a name="train-machine-learning-and-ai-models"></a>Eseguire il training di modelli di apprendimento automatico e di intelligenza artificiale

Usare Batch per intelligenza artificiale per eseguire il training di modelli di apprendimento automatico, di reti neurali profonde e per altri approcci di intelligenza artificiale. Batch per intelligenza artificiale include il supporto predefinito per i framework e le librerie di intelligenza artificiale open source più diffuse, tra cui [TensorFlow](https://github.com/tensorflow/tensorflow), [PyTorch](https://github.com/pytorch/pytorch), [Chainer](https://github.com/chainer/chainer) e [Microsoft Cognitive Toolkit (CNTK)](https://github.com/Microsoft/CNTK).

Dopo aver individuato l'area problematica e aver preparato i dati, è possibile operare in modo interattivo con Batch per intelligenza artificiale per testare le idee di modello. Quando si è pronti per sperimentare su larga scala, avviare i processi in più GPU con MPI o altre librerie di comunicazione ed eseguire più esperimenti in parallelo.

Batch per intelligenza artificiale consente di eseguire il training di modelli su larga scala in diversi modi. Ad esempio:  

|  |  |
|---------|---------|
| **Distribuire il training**<br/>![Training distribuito](./media/overview/distributed-training.png)  | Aumentare il training di un singolo processo su più GPU connessi in rete per eseguire il training di reti di grandi dimensioni con volumi elevati di dati.|
| **Esperimento**<br/>![Sperimentazione](./media/overview/experimentation.png) | Scalare orizzontalmente training con più processi. Eseguire sweep di parametri per testare nuove idee, oppure regolare gli iperparametri per ottimizzare accuratezza e prestazioni. |
| **Esecuzione in parallelo**![Esecuzione parallela](./media/overview/parallel-execution.png) | Eseguire il training o assegnare punteggi simultaneamente a più modelli eseguiti in parallelo su più server, per accelerare il completamento dei processi.|

Dopo aver eseguito il training di un modello, usare Batch per intelligenza artificiale per testarlo se questo non è già previsto dallo script di training, oppure assegnare il punteggio batch.

## <a name="how-it-works"></a>Funzionamento

Usare gli SDK di Batch per intelligenza artificiale, gli script della riga di comando o il portale di Azure per gestire le risorse di calcolo e pianificare i processi per il training e il test dell'intelligenza artificiale: 

* **Effettuare il provisioning e ridimensionare i cluster di VM** - Scegliere il numero di nodi (VM) e selezionare una VM con supporto per GPU o altre dimensioni di VM che soddisfino le esigenze di training. Aumentare o diminuire il numero di nodi in modo automatico o manuale, così da usare le risorse solo quando necessario. 

* **Gestire dipendenze e contenitori** - Per impostazione predefinita, i cluster Batch per intelligenza artificiale eseguono immagini di VM Linux con dipendenze pre-installate per l'esecuzione di framework di training basati su contenitori, su GPU o CPU. Per una configurazione aggiuntiva, usare immagini personalizzate o eseguire gli script di avvio.

* **Distribuire dati** - Scegliere una o più opzioni di archiviazione per gestire script, dati di input e output di processo: File di Azure, Archiviazione BLOB di Azure o un server NFS gestito. Batch per intelligenza artificiale supporta anche soluzioni di archiviazione personalizzate, inclusi file system paralleli a prestazioni elevate. Montare i file system di archiviazione nei nodi del cluster e nei contenitori processo usando semplici file di configurazione.

* **Pianificare i processi**  - Inviare i processi a una coda di processi basata sulla priorità per condividere i cluster e sfruttare le VM con priorità bassa e istanze riservate.

* **Gestire gli errori** - Monitorare gli stati dei processi e riavviare i processi in caso di errori riscontrati nella macchina virtuale durante i processi con esecuzione lunga.

* **Raccolta dei risultati** - Accedere a log di output, Stdout, Stderr e modelli con training. Configurare i processi di Batch per intelligenza artificiale per eseguire il push dell'output direttamente sull'archivio montato.

In quanto servizio di Azure, Batch per intelligenza artificiale supporta gli strumenti di sicurezza più diffusi, come il controllo degli accessi in base al ruolo (RBAC) e le reti virtuali di Azure.  

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [risorse di Batch per intelligenza artificiale](resource-concepts.md) per eseguire il training di modelli di apprendimento automatico o di intelligenza artificiale.

* Introduzione al [training di un modello di apprendimento avanzato di esempio](quickstart-tensorflow-training-cli.md) con Batch per intelligenza artificiale.

* Vedere le [recipe di training](https://github.com/Azure/BatchAI/blob/master/recipes) di esempio per i framework di intelligenza artificiale più diffusi.

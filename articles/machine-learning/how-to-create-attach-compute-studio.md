---
title: Creazione di training & la distribuzione di calcoli (Studio)
titleSuffix: Azure Machine Learning
description: Usare Studio per creare risorse di calcolo per training e distribuzione (destinazioni di calcolo) per Machine Learning
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.openlocfilehash: 37253ccfb249eb8b510af5e314c3167ddd979a87
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661958"
---
# <a name="create-compute-targets-for-model-training-and-deployment-in-azure-machine-learning-studio"></a>Creare destinazioni di calcolo per il training e la distribuzione di modelli in Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come creare e gestire le destinazioni di calcolo in Azure Machine Studio.  È anche possibile creare e gestire le destinazioni di calcolo con:

* [SDK di Azure Machine Learning Learning](how-to-create-attach-compute-sdk.md), 
* [Estensione dell'interfaccia](reference-azure-machine-learning-cli.md#resource-management) della riga di comando per Azure Machine Learning
* [Estensione vs code](how-to-manage-resources-vscode.md#compute-clusters) per Azure Machine Learning.


## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Prova subito la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree)
* [Area di lavoro Azure Machine Learning](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>Che cos'è una destinazione di calcolo? 

Con Azure Machine Learning è possibile eseguire il training del modello in un'ampia gamma di risorse o ambienti, collettivamente definiti [__destinazioni di calcolo__](concept-azure-machine-learning-architecture.md#compute-targets). Una destinazione di calcolo può essere un computer locale o una risorsa cloud, come un ambiente di calcolo di Azure Machine Learning, Azure HDInsight o una macchina virtuale remota.  È possibile anche creare destinazioni di calcolo per la distribuzione del modello, come descritto in ["Dove e come distribuire i modelli"](how-to-deploy-and-where.md).

## <a name="view-compute-targets"></a><a id="portal-view"></a>Vista destinazioni di calcolo

Per visualizzare tutte le destinazioni di calcolo per l'area di lavoro, seguire questa procedura:

1. Passare ad [Azure Machine Learning Studio](https://ml.azure.com).
 
1. In __Gestisci__selezionare __calcolo__.

1. Selezionare le schede nella parte superiore per visualizzare ogni tipo di destinazione di calcolo.

    :::image type="content" source="media/how-to-create-attach-studio/view-compute-targets.png" alt-text="Visualizza l'elenco delle destinazioni di calcolo":::

## <a name="create-compute-target"></a><a id="portal-create"></a>Creare una destinazione di calcolo

Seguire i passaggi precedenti per visualizzare l'elenco delle destinazioni di calcolo. Usare quindi questi passaggi per creare una destinazione di calcolo:

1. Selezionare la scheda nella parte superiore corrispondente al tipo di calcolo che si creerà.

1. Se non si dispone di destinazioni di calcolo, selezionare  **Crea** al centro della pagina.
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="Creare una destinazione di calcolo":::

1. Se viene visualizzato un elenco di risorse di calcolo, selezionare **+ nuovo** sopra l'elenco.

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="Seleziona nuovo":::


1. Compilare il modulo per il tipo di calcolo:

  * [Istanza di calcolo](#compute-instance)
  * [Cluster di elaborazione](#amlcompute)
  * [Inferenza cluster](#inference-clusters)
  * [Calcolo collegato](#attached-compute)

1. Selezionare __Create__ (Crea).

1. Visualizzare lo stato dell'operazione di creazione selezionando la destinazione di calcolo dall'elenco:

    :::image type="content" source="media/how-to-create-attach-studio/view-list.png" alt-text="Visualizzare lo stato di calcolo da un elenco":::


### <a name="compute-instance"></a>Istanza di calcolo

Usare la [procedura precedente](#portal-create) per creare l'istanza di calcolo.  Compilare quindi il modulo come segue:

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="Creare una nuova istanza di calcolo":::


|Campo  |Descrizione  |
|---------|---------|
|Nome del calcolo     |  <li>Il nome è obbligatorio e deve avere una lunghezza compresa tra 3 e 24 caratteri.</li><li>I caratteri validi sono lettere maiuscole e minuscole, cifre e il  **-** carattere.</li><li>Il nome deve iniziare con una lettera</li><li>Il nome deve essere univoco in tutti i calcoli esistenti all'interno di un'area di Azure. Se il nome scelto non è univoco, verrà visualizzato un avviso</li><li>Se **-**  viene usato il carattere, deve essere seguito da almeno una lettera in un secondo momento nel nome</li>     |
|Tipo di macchina virtuale |  Scegliere CPU o GPU. Questo tipo non può essere modificato dopo la creazione     |
|Dimensioni della macchina virtuale     |  Le dimensioni delle macchine virtuali supportate possono essere limitate nella propria area. Controllare l' [elenco di disponibilità](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Abilitare/disabilitare l'accesso SSH     |   L'accesso SSH è disabilitato per impostazione predefinita.  L'accesso SSH non può essere. modificato dopo la creazione. Assicurarsi di abilitare l'accesso se si prevede di eseguire il debug in modo interattivo con [vs code remoto](how-to-set-up-vs-code-remote.md)   |
|Impostazioni avanzate     |  Facoltativa. Configurare una rete virtuale. Specificare il **gruppo di risorse**, la **rete virtuale**e la **subnet** per creare l'istanza di calcolo all'interno di una rete virtuale di Azure (VNET). Per ulteriori informazioni, vedere i [requisiti di rete](how-to-enable-virtual-network.md#compute-instance) per vnet.  |

### <a name="compute-clusters"></a><a name="amlcompute"></a> Cluster di calcolo

Creare un cluster di calcolo a nodo singolo o a più nodi per i carichi di lavoro di formazione, inferenza in batch o rinforzo. Per creare il cluster di calcolo, [attenersi alla procedura](#portal-create) descritta in precedenza.  Compilare quindi il modulo come segue:


|Campo  |Descrizione  |
|---------|---------|
|Nome del calcolo     |  <li>Il nome è obbligatorio e deve avere una lunghezza compresa tra 3 e 24 caratteri.</li><li>I caratteri validi sono lettere maiuscole e minuscole, cifre e il  **-** carattere.</li><li>Il nome deve iniziare con una lettera</li><li>Il nome deve essere univoco in tutti i calcoli esistenti all'interno di un'area di Azure. Se il nome scelto non è univoco, verrà visualizzato un avviso</li><li>Se **-**  viene usato il carattere, deve essere seguito da almeno una lettera in un secondo momento nel nome</li>     |
|Tipo di macchina virtuale |  Scegliere CPU o GPU. Questo tipo non può essere modificato dopo la creazione     |
|Priorità macchina virtuale | Scegliere **priorità bassa**o **dedicata** .  Le macchine virtuali con priorità bassa sono più economiche, ma non garantiscono i nodi di calcolo. Il processo potrebbe essere interrotto.
|Dimensioni della macchina virtuale     |  Le dimensioni delle macchine virtuali supportate possono essere limitate nella propria area. Controllare l' [elenco di disponibilità](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Numero minimo di nodi | Numero minimo di nodi di cui si vuole eseguire il provisioning. Se si desidera un numero dedicato di nodi, impostare il conteggio qui. Per risparmiare sui costi, impostare il valore minimo su 0, in modo da non pagare per alcun nodo quando il cluster è inattivo. |
|Numero massimo di nodi | Numero massimo di nodi di cui si vuole eseguire il provisioning. Il calcolo verrà ridimensionato automaticamente fino a un numero massimo di questo nodo quando viene inviato un processo. |
|Impostazioni avanzate     |  Facoltativa. Configurare una rete virtuale. Specificare il **gruppo di risorse**, la **rete virtuale**e la **subnet** per creare l'istanza di calcolo all'interno di una rete virtuale di Azure (VNET). Per ulteriori informazioni, vedere i [requisiti di rete](how-to-enable-virtual-network.md#compute-instance) per vnet.   Connetti anche le [identità gestite](#managed-identity) per concedere l'accesso alle risorse     |

#### <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Configurare l'identità gestita

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Durante la creazione del cluster o quando si modificano i dettagli del cluster di calcolo, nelle **Impostazioni avanzate**, impostare **assegna un'identità gestita** e specificare un'identità assegnata dal sistema o un'identità assegnata dall'utente.

#### <a name="managed-identity-usage"></a>Utilizzo identità gestite

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

### <a name="inference-clusters"></a>Inferenza cluster

> [!IMPORTANT]
> L'uso del servizio Azure Kubernetes con Azure Machine Learning ha più opzioni di configurazione. Alcuni scenari, ad esempio la rete, richiedono un'installazione e una configurazione aggiuntive. Per altre informazioni sull'uso di AKS con Azure ML, vedere [creare e associare un cluster del servizio Azure Kubernetes](how-to-create-attach-kubernetes.md).

Creare o alleghi un cluster Azure Kubernetes Service (AKS) per l'inferenza su larga scala. Usare i [passaggi precedenti](#portal-create) per creare il cluster AKS.  Compilare quindi il modulo come segue:


|Campo  |Descrizione  |
|---------|---------|
|Nome del calcolo     |  <li>Il nome è obbligatorio. Il nome deve avere una lunghezza compresa tra 2 e 16 caratteri. </li><li>I caratteri validi sono lettere maiuscole e minuscole, cifre e il  **-** carattere.</li><li>Il nome deve iniziare con una lettera</li><li>Il nome deve essere univoco in tutti i calcoli esistenti all'interno di un'area di Azure. Se il nome scelto non è univoco, verrà visualizzato un avviso</li><li>Se **-**  viene usato il carattere, deve essere seguito da almeno una lettera in un secondo momento nel nome</li>     |
|Servizio Kubernetes | Selezionare **Crea nuovo** e compilare il resto del modulo.  In alternativa, selezionare **Usa esistente** e quindi selezionare un cluster AKS esistente dalla sottoscrizione.
|Region |  Selezionare l'area in cui verrà creato il cluster |
|Dimensioni della macchina virtuale     |  Le dimensioni delle macchine virtuali supportate possono essere limitate nella propria area. Controllare l' [elenco di disponibilità](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Scopo del cluster  | Seleziona **produzione** o **sviluppo/test** |
|Numero di nodi | Il numero di nodi moltiplicato per il numero di core della macchina virtuale (vCPU) deve essere maggiore o uguale a 12. |
| Configurazione di rete | Selezionare **Avanzate** per creare il calcolo in una rete virtuale esistente. Per altre informazioni su AKS in una rete virtuale, vedere [isolamento rete durante il training e l'inferenza con endpoint privati e reti virtuali](how-to-enable-virtual-network.md#aksvnet). |
| Abilitare la configurazione SSL | Usare questa opzione per configurare il certificato SSL nel calcolo |

### <a name="attached-compute"></a>Calcolo collegato

Per usare le destinazioni di calcolo create al di fuori dell'area di lavoro di Azure Machine Learning, è necessario collegarle. Il collegamento di una destinazione di calcolo rende quest'ultima disponibile nell'area di lavoro.  Usare il **calcolo collegato** per collegare una destinazione di calcolo per il **Training**.  Usare i **cluster di inferenza** per alleghi un cluster AKS per l' **inferenza.**

Per alleghi un calcolo, attenersi alla procedura descritta in [precedenza](#portal-create) .  Compilare quindi il modulo come segue:

1. Immettere un nome per la destinazione di calcolo. 
1. Consente di selezionare il tipo di calcolo da aggiungere. Non tutti i tipi di calcolo possono essere collegati usando Azure Machine Learning Studio. I tipi di calcolo che possono essere allegati per il training sono:
    * Macchina virtuale remota
    * Azure Databricks (per l'uso nelle pipeline di machine learning)
    * Azure Data Lake Analytics (per l'uso nelle pipeline di machine learning)
    * HDInsight di Azure

1. Compilare il modulo e fornire i valori per le proprietà necessarie.

    > [!NOTE]
    > Microsoft consiglia di usare le chiavi SSH, che sono più sicure rispetto alle password. Le password sono intuibili e vulnerabili ad attacchi di forza bruta. Le chiavi SSH si basano sulle firme di crittografia. Per informazioni sulla creazione di chiavi SSH per l'uso con Macchine virtuali di Azure, vedere i documenti seguenti:
    >
    > * [Create and use SSH keys on Linux or macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) (Creare e usare chiavi SSH in Linux o macOS)
    > * [Create and use SSH keys on Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) (Creare e usare chiavi SSH in Windows)

1. Selezionare __Allega__. 


## <a name="next-steps"></a>Passaggi successivi

Dopo che una destinazione è stata creata e collegata all'area di lavoro, è possibile usarla nella [configurazione di esecuzione](how-to-set-up-training-targets.md) con un `ComputeTarget` oggetto:

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

* Usare la risorsa di calcolo per [inviare un'esecuzione di training](how-to-set-up-training-targets.md).
* [Esercitazione: eseguire il training di un modello](tutorial-train-models-with-aml.md) usa una destinazione di calcolo gestita per eseguire il training di un modello.
* Consultare le informazioni su come [ottimizzare in modo efficiente gli iperparametri](how-to-tune-hyperparameters.md) per creare modelli migliori.
* Dopo aver creato un modello con training, consultare le informazioni su [come e dove distribuire i modelli](how-to-deploy-and-where.md).
* [Usare Azure Machine Learning con le reti virtuali di Azure](how-to-enable-virtual-network.md)

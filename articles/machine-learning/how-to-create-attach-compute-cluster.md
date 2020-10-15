---
title: Creazione di cluster di elaborazione
titleSuffix: Azure Machine Learning
description: Informazioni su come creare cluster di calcolo nell'area di lavoro Azure Machine Learning. Usare il cluster di calcolo come destinazione di calcolo per il training o l'inferenza.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: d33af7a9c2d48ded84bd675364469dab09a79d3a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711472"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>Creare un cluster di elaborazione di Azure Machine Learning

Informazioni su come creare e gestire un [cluster di calcolo](concept-compute-target.md#azure-machine-learning-compute-managed) nell'area di lavoro Azure Machine Learning.

È possibile usare Azure Machine Learning cluster di calcolo per distribuire un processo di inferenza di training o batch in un cluster di nodi di calcolo CPU o GPU nel cloud. Per altre informazioni sulle dimensioni delle macchine virtuali che includono GPU, consultare il documento [Dimensioni delle macchine virtuali ottimizzate per GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu). 

In questo articolo viene spiegato come:

* Creare un cluster di elaborazione
* Abbassare il costo del cluster di calcolo
* Configurare un' [identità gestita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) per il cluster

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning. Per altre informazioni, vedere [creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

* Estensione dell'interfaccia della riga [di comando di Azure per il servizio Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)o l' [estensione di Visual Studio code Azure Machine Learning](tutorial-setup-vscode-extension.md).

## <a name="what-is-a-compute-cluster"></a>Che cos'è un cluster di calcolo?

Azure Machine Learning cluster di calcolo è un'infrastruttura di calcolo gestito che consente di creare facilmente un calcolo a nodo singolo o a più nodi. Il calcolo viene creato all'interno dell'area di lavoro ed è una risorsa che può essere condivisa con altri utenti dell'area. Il calcolo si ridimensiona verticalmente in modo automatico quando viene inviato un processo e può essere inserito in una Rete virtuale di Azure. Il calcolo viene eseguito in un ambiente basato su contenitori, con la creazione di un pacchetto delle dipendenze del modello in un [contenitore Docker](https://www.docker.com/why-docker).

I cluster di calcolo possono eseguire processi in modo sicuro in un [ambiente di rete virtuale](how-to-secure-training-vnet.md), senza richiedere alle aziende di aprire porte SSH. Il processo viene eseguito in un ambiente in contenitori e inserisce le dipendenze del modello in un contenitore docker. 

## <a name="limitations"></a>Limitazioni

* **Non creare più allegati simultanei nello stesso calcolo** dall'area di lavoro. Ad esempio, se si connette un cluster di calcolo a un'area di lavoro usando due nomi diversi. Ogni nuovo allegato interromperà gli allegati esistenti precedenti.

    Se si desidera ricollegare una destinazione di calcolo, ad esempio per modificare le impostazioni di configurazione del cluster, è necessario innanzitutto rimuovere l'allegato esistente.

* Alcuni degli scenari elencati in questo documento sono contrassegnati come __Anteprima__. La funzionalità di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Per alcuni aspetti, l'ambiente di calcolo di Azure Machine Learning prevede limiti predefiniti, ad esempio il numero di core che possono essere allocati. Per altre informazioni, consultare il documento [Gestire e richiedere quote per risorse di Azure](how-to-manage-quotas.md).

> [!TIP]
> I cluster possono in genere essere ridimensionati fino a 100 nodi, purché si disponga di una quota sufficiente per il numero di core necessari. Per impostazione predefinita, i cluster sono impostati con la comunicazione tra i nodi abilitata, ad esempio per supportare i processi MPI. È tuttavia possibile ridimensionare i cluster a migliaia di nodi semplicemente [generando un ticket di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)e richiedendo di consentire l'elenco della sottoscrizione o dell'area di lavoro o di un cluster specifico per la disabilitazione della comunicazione tra nodi. 


## <a name="create"></a>Crea

**Tempo stimato**: circa 5 minuti.

Un ambiente di calcolo di Azure Machine Learning può essere usato su più esecuzioni. L'ambiente di calcolo può essere condiviso con altri utenti nell'area di lavoro e mantenuto da un'esecuzione all'altra, ridimensionando automaticamente i nodi in base al numero di esecuzioni inviate e all'impostazione max_nodes definita nel cluster. L'impostazione min_nodes controlla il numero minimo di nodi disponibili.

Le memorie centrali dedicate per area per ogni quota della famiglia di VM e la quota locale totale, applicabile alla creazione del cluster di calcolo, sono unificate e condivise con Azure Machine Learning quota dell'istanza di calcolo del training. 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Il calcolo viene ridimensionato automaticamente fino a zero nodi quando non viene usato.   Le macchine virtuali dedicate vengono create per eseguire i processi in base alle esigenze.
    
# <a name="python"></a>[Python](#tab/python)

Per creare una risorsa permanente di ambiente di calcolo di Machine Learning in Python, specificare le proprietà **vm_size** e **max_nodes**. Azure Machine Learning quindi usa valori predefiniti intelligenti per le altre proprietà. 
    
* **vm_size**: la famiglia di macchine virtuali dei nodi creati dall'ambiente di calcolo di Azure Machine Learning.
* **max_nodes**: il numero massimo di nodi per la scalabilità automatica durante l'esecuzione di un processo in un ambiente di calcolo di Machine Learning.


[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

Durante la creazione di un ambiente di calcolo di Machine Learning è anche possibile configurare diverse proprietà avanzate. Le proprietà consentono di creare un cluster permanente di dimensione fissa o all'interno di una Rete virtuale di Azure esistente nella sottoscrizione.  Per informazioni dettagliate, consultare [AmlCompute class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py&preserve-view=true) (Classe AmlCompute).


# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
```

Per altre informazioni, vedere [az ml computetarget create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute&preserve-view=true).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Per informazioni sulla creazione di un cluster di calcolo in studio, vedere [creare destinazioni di calcolo in Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#amlcompute).

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Abbassare il costo del cluster di calcolo

È anche possibile scegliere di usare [macchine virtuali con priorità bassa](concept-plan-manage-cost.md#low-pri-vm) per eseguire alcuni o tutti i carichi di lavoro. Queste macchine virtuali non hanno una disponibilità garantita e possono essere terminate durante l'uso. Un processo interrotto viene riavviato, non ripreso. 

Usare uno di questi modi per specificare una macchina virtuale con priorità bassa:
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Impostare `vm-priority` :
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[Studio](#tab/azure-studio)

In Studio scegliere **priorità bassa** quando si crea una macchina virtuale.

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Configurare l'identità gestita

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* Configurare l'identità gestita nella configurazione del provisioning:  

    * Identità gestita assegnata dal sistema:
        ```python
        # configure cluster with a system-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="SystemAssigned",
                                                                )
        ```
    
    * Identità gestita assegnata dall'utente:
    
        ```python
        # configure cluster with a user-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="UserAssigned",
                                                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```

* Aggiungere identità gestite a un cluster di calcolo esistente 
    
    * Identità gestita assegnata dal sistema:
    
        ```python
        # add a system-assigned managed identity
        cpu_cluster.add_identity(identity_type="SystemAssigned")
        ````
    
    * Identità gestita assegnata dall'utente:
    
        ```python
        # add a user-assigned managed identity
        cpu_cluster.add_identity(identity_type="UserAssigned", 
                                    identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
        ```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

* Creare un nuovo cluster di calcolo gestito con identità gestita

  * Identità gestita assegnata dall'utente

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  * Identità gestita assegnata dal sistema

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
* Aggiungere un'identità gestita a un cluster esistente:

    * Identità gestita assegnata dall'utente
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    * Identità gestita assegnata dal sistema

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

# <a name="studio"></a>[Studio](#tab/azure-studio)

Vedere [configurare l'identità gestita in studio](how-to-create-attach-compute-studio.md#managed-identity).

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>Utilizzo identità gestite

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="next-steps"></a>Passaggi successivi

Usare il cluster di calcolo per:

* [Inviare un'esecuzione di training](how-to-set-up-training-targets.md) 
* [Eseguire l'inferenza batch](how-to-use-parallel-run-step.md).
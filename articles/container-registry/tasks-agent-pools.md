---
title: Usa pool dedicato per eseguire attività-attività
description: Configurare un pool di risorse di calcolo dedicato (pool di agenti) nel registro di sistema per eseguire un'attività Container Registry di Azure.
ms.topic: article
ms.date: 10/12/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 94956af14aad2b62e6455f443329bcd3232095c0
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844915"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>Eseguire un'attività ACR in un pool di agenti dedicato

Configurare un pool di macchine virtuali gestito da Azure (*pool di agenti*) per abilitare l'esecuzione delle attività di container Registry di [Azure][acr-tasks] in un ambiente di calcolo dedicato. Dopo aver configurato uno o più pool nel registro, è possibile scegliere un pool per eseguire un'attività al posto dell'ambiente di calcolo predefinito del servizio.

Un pool di agenti fornisce:

- **Supporto per reti virtuali** : assegnare un pool di agenti a una VNet di Azure, fornendo l'accesso alle risorse in VNet, ad esempio un registro contenitori, Key Vault o archiviazione.
- **Ridimensionare in base alle esigenze** : aumentare il numero di istanze in un pool di agenti per le attività a elevato utilizzo di calcolo oppure ridimensionarle a zero. La fatturazione è basata sull'allocazione del pool. Per informazioni dettagliate, vedere [prezzi](https://azure.microsoft.com/pricing/details/container-registry/).
- **Opzioni flessibili** : è possibile scegliere tra diversi [livelli di pool](#pool-tiers) e opzioni di scalabilità per soddisfare le esigenze del carico di lavoro delle attività.
- **Gestione di Azure** : i pool di attività vengono corretti e gestiti da Azure, offrendo l'allocazione riservata senza la necessità di gestire le singole macchine virtuali.

Questa funzionalità è disponibile per il livello di servizio **Premium** del registro contenitori. Per informazioni sui limiti e i livelli di servizio del registro di sistema, vedere [sku container Registry di Azure][acr-tiers].

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima e [si applicano alcune limitazioni](#preview-limitations). Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.
>

## <a name="preview-limitations"></a>Limiti di anteprima

- I pool di agenti attività attualmente supportano i nodi Linux. I nodi di Windows non sono attualmente supportati.
- I pool di agenti delle attività sono disponibili in anteprima nelle aree seguenti: Stati Uniti occidentali 2, Stati Uniti centro-meridionali, Stati Uniti orientali 2, Stati Uniti orientali, Stati Uniti centrali, US gov Arizona, US gov Texas e US gov Virginia.
- Per ogni registro di sistema, la quota predefinita totale vCPU (Core) è 16 per tutti i pool di agenti standard e è 0 per i pool di agenti isolati. Aprire una [richiesta di supporto][open-support-ticket] per l'allocazione aggiuntiva.
- Attualmente non è possibile annullare un'attività eseguita in un pool di agenti.

## <a name="prerequisites"></a>Prerequisiti

* Per usare i passaggi dell'interfaccia della riga di comando di Azure in questo articolo è necessaria l'interfaccia della riga di comando di Azure 2.3.1 o successiva Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli]. In alternativa, eseguire la procedura in [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Se non si dispone già di un registro contenitori, [crearne uno][create-reg-cli] (livello Premium obbligatorio) in un'area di anteprima.

## <a name="pool-tiers"></a>Livelli del pool

I livelli del pool di agenti forniscono le risorse seguenti per ogni istanza nel pool.

|Livello    | Type  |  CPU  |Memoria (GB)  |
|---------|---------|---------|---------|
|S1     |  standard    | 2       |    3     |
|S2     |  standard    | 4       |    8     |
|S3     |  standard    | 8       |   16     |
|I6     |  isolati    | 64     |   216     |


## <a name="create-and-manage-a-task-agent-pool"></a>Creare e gestire un pool di agenti attività

### <a name="set-default-registry-optional"></a>Imposta Registro di sistema predefinito (facoltativo)

Per semplificare i comandi dell'interfaccia della riga di comando di Azure che seguono, impostare il registro di sistema predefinito eseguendo il comando [AZ Configure][az-configure] :

```azurecli
az configure --defaults acr=<registryName>
```

Gli esempi seguenti presuppongono che sia stato impostato il registro di sistema predefinito. In caso contrario, passare un `--registry <registryName>` parametro in ogni `az acr` comando.

### <a name="create-agent-pool"></a>Crea pool di agenti

Creare un pool di agenti usando il comando [AZ ACR agentpool create][az-acr-agentpool-create] . Nell'esempio seguente viene creato un pool S2 di livello (4 CPU/istanza). Per impostazione predefinita, il pool contiene 1 istanza.

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

> [!NOTE]
> Per il completamento della creazione di un pool di agenti e di altre operazioni di gestione del pool sono necessari alcuni minuti.

### <a name="scale-pool"></a>Ridimensionare il pool

Ridimensionare le dimensioni del pool con il comando [AZ ACR agentpool Update][az-acr-agentpool-update] . Nell'esempio seguente il pool viene ridimensionato a 2 istanze. È possibile ridimensionare le istanze di 0.

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>Creare un pool in una rete virtuale

### <a name="add-firewall-rules"></a>Aggiungere regole di firewall

Per i pool di agenti attività è necessario l'accesso ai servizi di Azure seguenti. È necessario aggiungere le regole del firewall seguenti a tutti i gruppi di sicurezza di rete o le route definite dall'utente esistenti.

| Direzione | Protocollo | Source (Sorgente)         | Porta di origine | Destination          | Porta dest | Usata    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| In uscita  | TCP      | VirtualNetwork | Qualsiasi         | AzureKeyVault        | 443       | Predefinito |
| In uscita  | TCP      | VirtualNetwork | Qualsiasi         | Archiviazione              | 443       | Predefinito |
| In uscita  | TCP      | VirtualNetwork | Qualsiasi         | Hub eventi             | 443       | Predefinito |
| In uscita  | TCP      | VirtualNetwork | Qualsiasi         | AzureActiveDirectory | 443       | Predefinito |
| In uscita  | TCP      | VirtualNetwork | Qualsiasi         | AzureMonitor         | 443       | Predefinito |

> [!NOTE]
> Se le attività richiedono risorse aggiuntive dalla rete Internet pubblica, aggiungere le regole corrispondenti. Ad esempio, sono necessarie regole aggiuntive per eseguire un'attività di compilazione Docker che estrae le immagini di base dall'hub Docker o ripristina un pacchetto NuGet.

### <a name="create-pool-in-vnet"></a>Crea pool in VNet

Nell'esempio seguente viene creato un pool di agenti *nella subnet subnet* della rete *myvnet*:

```azurecli
# Get the subnet ID
subnetId=$(az network vnet subnet show \
        --resource-grop myresourcegroup \
        --vnet-name myvnet \
        --name mysubnetname \
        --query id --output tsv)

az acr agentpool create \
    --name myagentpool \
    --tier S2 \
    --subnet-id $subnetId
```

## <a name="run-task-on-agent-pool"></a>Esegui attività nel pool di agenti

Negli esempi seguenti viene illustrato come specificare un pool di agenti durante l'accodamento di un'attività.

> [!NOTE]
> Per usare un pool di agenti in un'attività ACR, verificare che il pool includa almeno un'istanza.
>

### <a name="quick-task"></a>Attività rapida

Accodare un'attività rapida nel pool di agenti usando il comando [AZ ACR Build][az-acr-build] e passare il `--agent-pool` parametro:

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

### <a name="automatically-triggered-task"></a>Attività attivata automaticamente

Ad esempio, creare un'attività pianificata nel pool di agenti con [AZ ACR task create][az-acr-task-create], passando il `--agent-pool` parametro.

```azurecli
az acr task create \
    --name mytask \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --schedule "0 21 * * *" \
    --file Dockerfile \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --commit-trigger-enabled false
```

Per verificare l'impostazione dell'attività, eseguire [AZ ACR task run][az-acr-task-run]:

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>Stato pool di query

Per trovare il numero di esecuzioni attualmente pianificate nel pool di agenti, eseguire [AZ ACR agentpool Show][az-acr-agentpool-show].

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di compilazione e manutenzione di immagini del contenitore nel cloud, vedere la [serie di esercitazioni sulle attività ACR](container-registry-tutorial-quick-task.md).



[acr-tasks]:           container-registry-tasks-overview.md
[acr-tiers]:           container-registry-skus.md
[azure-cli]:           /cli/azure/install-azure-cli
[open-support-ticket]: https://aka.ms/acr/support/create-ticket
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[az-configure]: /cli/azure#az-configure
[az-acr-agentpool-create]: /cli/azure/acr/agentpool#az-acr-agentpool-create
[az-acr-agentpool-update]: /cli/azure/acr/agentpool#az-acr-agentpool-update
[az-acr-agentpool-show]: /cli/azure/acr/agentpool#az-acr-agentpool-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[create-reg-cli]: container-registry-get-started-azure-cli.md

---
title: Usare il componente di scalabilità automatica del cluster nel servizio Azure Kubernetes (AKS)
description: Informazioni su come usare il componente di scalabilità automatica per ridimensionare automaticamente il cluster e soddisfare le richieste delle applicazioni in un cluster del servizio Azure Kubernetes (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/29/2019
ms.author: iainfou
ms.openlocfilehash: d8e095303161002d10914ca7c3213ac0c6894e5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467145"
---
# <a name="preview---automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Anteprima - ridimensionare automaticamente un cluster per soddisfare le esigenze dell'applicazione in Azure Kubernetes Service (AKS)

Per tenere il passo delle richieste delle applicazioni nel servizio Azure Kubernetes (AKS), potrebbe essere necessario regolare il numero di nodi che eseguono i carichi di lavoro. Il componente di scalabilità automatica del cluster può cercare i pod del cluster che non possono essere pianificati a causa di vincoli delle risorse. Quando vengono rilevati problemi, il numero di nodi viene aumentato in modo da soddisfare la richiesta delle applicazioni. Inoltre i nodi vengono controllati regolarmente per rilevare la mancanza di pod in esecuzione, con la riduzione del numero di nodi in base alle esigenze. Questa capacità di incrementare o ridurre il numero di nodi nel cluster AKS permette di gestire il cluster in modo efficace e conveniente.

Questo articolo illustra come abilitare e gestire il componente di scalabilità automatica in un cluster AKS.

> [!IMPORTANT]
> Funzionalità di anteprima del servizio contenitore di AZURE sono self-service e fornire il consenso esplicito. Le anteprime sono fornite per raccogliere commenti e suggerimenti e bug dalla community. Tuttavia, non sono supportati dal supporto tecnico di Azure. Se si crea un cluster o aggiungere queste funzionalità in cluster esistenti, tale cluster non è supportato fino a quando la funzionalità non è più disponibile in anteprima e passano a livello generale (GA).
>
> Se si verificano problemi con funzionalità di anteprima [segnalare un problema nel repository GitHub di AKS] [ aks-github] con il nome della funzionalità Anteprima nel titolo del bug.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede che sia in esecuzione l'interfaccia della riga di comando di Azure versione 2.0.55 o successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga comando di aks-preview

I cluster AKS che supportano il componente di scalabilità automatica del cluster devono usare set di scalabilità di macchine virtuali ed eseguire Kubernetes versione *1.12.4* o successive. Il supporto di questo set di scalabilità è in anteprima. Per acconsentire esplicitamente e creare cluster che usano set di scalabilità, installare prima l'estensione dell'interfaccia della riga di comando di Azure *aks-preview* usando il comando [az extension add][az-extension-add], come illustrato nell'esempio seguente:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Se è già stata installata il *aks-preview* estensione, installare le aggiorna usando il `az extension update --name aks-preview` comando.

### <a name="register-scale-set-feature-provider"></a>Registrare il provider della funzionalità dei set di scalabilità

Per creare un servizio AKS che usi set di scalabilità, è anche necessario abilitare un flag della funzionalità per la sottoscrizione. Per registrare il flag della funzionalità *VMSSPreview*, usare il comando [az feature register][az-feature-register] come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllare lo stato di registrazione usando il comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft.ContainerService* usando il comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="about-the-cluster-autoscaler"></a>Informazioni sul componente di scalabilità automatica

Per adattarsi alle variazioni delle richieste delle applicazioni, ad esempio tra l’orario lavorativo diurno e la sera o il fine settimana, è spesso necessario trovare il modo di adattare automaticamente i cluster. La scalabilità dei cluster AKS può essere ottenuta in uno dei due modi seguenti:

* Il **componente di scalabilità automatica** cerca i pod che non possono essere pianificati nei nodi a causa di vincoli delle risorse. Il cluster aumenta quindi automaticamente il numero di nodi.
* Il **componente di scalabilità automatica orizzontale dei pod** usa il server delle metriche in un cluster Kubernetes per monitorare la richiesta di risorse dei pod. Se un servizio richiede più risorse, il numero di pod viene aumentato automaticamente per soddisfare la domanda.

![Il componente di scalabilità automatica del cluster e il componente di scalabilità automatica orizzontale dei pod spesso interagiscono per soddisfare le esigenze delle applicazioni obbligatorie](media/autoscaler/cluster-autoscaler.png)

Sia il componente di scalabilità automatica orizzontale dei pod sia il componente di scalabilità automatica del cluster possono anche ridurre il numero di pod e nodi in base alle esigenze. Il componente di scalabilità automatica del cluster riduce il numero di nodi quando una certa quantità di capacità è rimasta inutilizzata per un periodo di tempo. I pod in un nodo che devono essere rimossi dal componente di scalabilità automatica del cluster sono pianificati in modo sicuro in un'altra posizione nel cluster. Il componente di scalabilità automatica del cluster potrebbe non essere in grado di effettuare la riduzione qualora non fosse possibile spostare i pod, ad esempio nelle situazioni seguenti:

* Un pod creato direttamente e non supportato da un oggetto controller, come un set di distribuzioni o di repliche.
* Un budget di interruzione dei pod (PDB) troppo restrittivo e che non permette la riduzione del numero di pod sotto una determinata soglia.
* Un pod che utilizza selettori di nodo o anti-affinità impossibili da rispettare se pianificati in un nodo diverso.

Per altre informazioni sui casi in cui il componente di scalabilità automatica del cluster potrebbe non essere in grado di effettuare la riduzione, vedere [Quali tipi di pod possono impedire al componente di scalabilità automatica del cluster di rimuovere un nodo?][autoscaler-scaledown]

Il componente di scalabilità automatica del cluster usa i parametri di avvio per elementi come gli intervalli di tempo tra gli eventi di scalabilità e le soglie delle risorse. Questi parametri vengono definiti dalla piattaforma Azure e non sono attualmente esposti per la modifica da parte dell’utente. Per altre informazioni sui parametri usati dal componente di scalabilità automatica del cluster, vedere [Quali sono i parametri del componente di scalabilità automatica del cluster?][autoscaler-parameters].

I due componenti di scalabilità automatica possono essere usati insieme e spesso sono entrambi distribuiti in un cluster. Nell’uso combinato, il componente di scalabilità automatica orizzontale dei pod è dedicato all’esecuzione del numero di pod necessari per soddisfare le richieste delle applicazioni. Il componente di scalabilità automatica del cluster è dedicato all’esecuzione del numero di nodi richiesto per supportare i pod pianificati.

> [!NOTE]
> Il ridimensionamento manuale è disabilitato quando si usa il componente di scalabilità automatica del cluster. È il componente di scalabilità automatica del cluster che determina il numero di nodi richiesto. Se si desidera ridimensionare manualmente il cluster [disabilitare il componente di scalabilità automatica del cluster](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Creare un cluster AKS e abilitare il componente di scalabilità automatica del cluster

Se è necessario creare un cluster AKS, usare il comando [az aks create][az-aks-create]. Specificare una *--kubernetes-version* che soddisfi o superi il numero di versione minima richiesto, come descritto nella sezione [Prima di iniziare](#before-you-begin) precedente. Per abilitare e configurare il componente di scalabilità automatica del cluster, usare il parametro *--enable-cluster-autoscaler* e specificare un *--min-count* e un *--max-count* di nodi.

> [!IMPORTANT]
> Il ridimensionamento automatico del cluster è un componente di Kubernetes. Anche se il cluster AKS usa un set di scalabilità per i nodi di macchine virtuali, non abilitare o modificare manualmente le impostazioni di scalabilità per il ridimensionamento automatico nel portale di Azure o tramite la CLI di Azure. Consentire il ridimensionamento automatico del cluster Kubernetes di gestire le impostazioni di scalabilità necessaria. Per altre informazioni, consultare [È possibile modificare le risorse del servizio Azure Kubernetes nel gruppo di risorse MC_*?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc_-resource-group)

Nell'esempio seguente viene creato un cluster AKS con set di scalabilità di macchine virtuali e componente di scalabilità automatica del cluster abilitati e viene utilizzato un minimo di *1* e un massimo di *3* nodi:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location canadaeast

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --kubernetes-version 1.12.6 \
  --node-count 1 \
  --enable-vmss \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Sono necessari alcuni minuti per creare il cluster e configurare le impostazioni del componente di scalabilità automatica del cluster.

### <a name="enable-the-cluster-autoscaler-on-an-existing-aks-cluster"></a>Abilitare il componente di scalabilità automatica del cluster in un cluster AKS esistente

È possibile abilitare il componente di scalabilità automatica del cluster in un cluster AKS esistente che soddisfi i requisiti descritti nella sezione [Prima di iniziare](#before-you-begin) precedente. Usare il comando [az aks update][az-aks-update] e scegliere *--enable-cluster-autoscaler*, quindi specificare un *--min-count* e un *--max-count* di nodi. Nell’esempio seguente viene abilitato un componente di scalabilità automatica del cluster su un cluster esistente che usa un minimo di *1* e un massimo di *3* nodi:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Se il numero minimo di nodi è maggiore del numero di nodi esistenti nel cluster, saranno necessari alcuni minuti per creare i nodi aggiuntivi.

## <a name="change-the-cluster-autoscaler-settings"></a>Modificare le impostazioni del componente di scalabilità automatica del cluster

Nel passaggio precedente per creare o aggiornare un cluster AKS esistente, il numero minimo di nodi del componente di scalabilità automatica del cluster è stato impostato su *1* e il numero massimo di nodi è stato impostato su *3*. Se le richieste delle applicazioni cambiano, potrebbe essere necessario regolare il numero di nodi del componente di scalabilità automatica del cluster.

Per modificare il numero di nodi, usare il comando [az aks update][az-aks-update] e specificare un valore minimo e massimo. Nell’esempio seguente viene impostato il *--min-count* su *1* e il *--max-count* su *5*:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

> [!NOTE]
> Durante l'anteprima, non è possibile impostare un numero minimo di nodi superiore rispetto a quello attualmente impostato per il cluster. Ad esempio, se il numero minimo è attualmente impostato su *1*, non è possibile aggiornarlo a *3*.

Monitorare le prestazioni delle applicazioni e dei servizi e modificare il numero di nodi del componente di scalabilità automatica del cluster per ottenere le prestazioni necessarie.

## <a name="disable-the-cluster-autoscaler"></a>Disabilitare il componente di scalabilità automatica del cluster

Se non si desidera più usare il componente di scalabilità automatica del cluster, è possibile disabilitarlo usando il comando [az aks update][az-aks-update]. I nodi non vengono rimossi quando il componente di scalabilità automatica del cluster è disabilitato.

Per rimuovere il componente di scalabilità automatica del cluster, specificare il parametro *--disable-cluster-autoscaler*, come illustrato nell'esempio seguente:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

È possibile ridimensionare manualmente il cluster usando il [az aks scale][az-aks-scale]. Se si usa il componente di scalabilità automatica orizzontale dei pod, tale funzionalità continua a essere eseguita con il componente di scalabilità automatica del cluster disabilitato, ma potrebbe non essere possibile pianificare i pod se tutte le risorse del nodo sono in uso.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha descritto come ridimensionare automaticamente il numero di nodi AKS. È anche possibile usare il componente di scalabilità automatica orizzontale dei pod per regolare automaticamente il numero di pod che eseguono l'applicazione. Per istruzioni sull'uso del componente di scalabilità automatica orizzontale dei pod, vedere [Ridimensionare le applicazioni nel servizio Azure Kubernetes][aks-scale-apps].

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-github]: https://github.com/azure/aks/issues

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca

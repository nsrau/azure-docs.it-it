---
title: Usare il componente di scalabilità automatica del cluster nel servizio Azure Kubernetes (AKS)
description: Informazioni su come usare il componente di scalabilità automatica per ridimensionare automaticamente il cluster e soddisfare le richieste delle applicazioni in un cluster del servizio Azure Kubernetes (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/18/2019
ms.author: mlearned
ms.openlocfilehash: dc5e862109a766f708338ebddb91a75ffc550306
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69031925"
---
# <a name="preview---automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Anteprima: ridimensiona automaticamente un cluster per soddisfare le richieste dell'applicazione in Azure Kubernetes Service (AKS)

Per tenere il passo delle richieste delle applicazioni nel servizio Azure Kubernetes (AKS), potrebbe essere necessario regolare il numero di nodi che eseguono i carichi di lavoro. Il componente di scalabilità automatica del cluster può cercare i pod del cluster che non possono essere pianificati a causa di vincoli delle risorse. Quando vengono rilevati problemi, il numero di nodi in un pool di nodi viene aumentato per soddisfare la richiesta dell'applicazione. Inoltre i nodi vengono controllati regolarmente per rilevare la mancanza di pod in esecuzione, con la riduzione del numero di nodi in base alle esigenze. Questa capacità di incrementare o ridurre il numero di nodi nel cluster AKS permette di gestire il cluster in modo efficace e conveniente.

Questo articolo illustra come abilitare e gestire il componente di scalabilità automatica in un cluster AKS. Il servizio di scalabilità automatica del cluster deve essere testato solo in anteprima nei cluster AKS.

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono il consenso esplicito self-service. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto tecnico per il massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per ulteriori informazioni, vedere gli articoli di supporto seguenti:
>
> * [Criteri di supporto AKS][aks-support-policies]
> * [Domande frequenti relative al supporto tecnico Azure][aks-faq]

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede la versione 2.0.65 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga comando di aks-preview

Per usare il servizio di scalabilità automatica del cluster, è necessaria l'estensione dell'interfaccia della riga di comando *AKS-Preview* 0.4.4 o versione successiva. Installare l'estensione dell'interfaccia della riga di comando di Azure *AKS-Preview* usando il comando [AZ Extension Add][az-extension-add] , quindi verificare la disponibilità di eventuali aggiornamenti tramite il comando [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-scale-set-feature-provider"></a>Registrare il provider della funzionalità dei set di scalabilità

Per creare un servizio AKS che usi set di scalabilità, è anche necessario abilitare un flag della funzionalità per la sottoscrizione. Per registrare il flag della funzionalità *VMSSPreview* , usare il comando [AZ feature Register][az-feature-register] , come illustrato nell'esempio seguente:

> [!CAUTION]
> Quando si registra una funzionalità in una sottoscrizione, attualmente non è possibile annullare la registrazione di tale funzionalità. Dopo aver abilitato alcune funzionalità di anteprima, è possibile usare i valori predefiniti per tutti i cluster AKS, quindi creati nella sottoscrizione. Non abilitare le funzionalità di anteprima nelle sottoscrizioni di produzione. Usare una sottoscrizione separata per testare le funzionalità di anteprima e raccogliere commenti e suggerimenti.

```azurecli-interactive
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllare lo stato della registrazione usando il comando [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft. servizio contenitore* usando il comando [AZ provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Limitazioni

Quando si creano e si gestiscono i cluster AKS che usano il ridimensionamento automatico del cluster, si applicano le limitazioni seguenti:

* Non è possibile usare il componente aggiuntivo routing applicazione HTTP.

## <a name="about-the-cluster-autoscaler"></a>Informazioni sul componente di scalabilità automatica

Per adattarsi alle variazioni delle richieste delle applicazioni, ad esempio tra l’orario lavorativo diurno e la sera o il fine settimana, è spesso necessario trovare il modo di adattare automaticamente i cluster. La scalabilità dei cluster AKS può essere ottenuta in uno dei due modi seguenti:

* Il **componente di scalabilità automatica** cerca i pod che non possono essere pianificati nei nodi a causa di vincoli delle risorse. Il cluster aumenta quindi automaticamente il numero di nodi.
* Il **componente di scalabilità automatica orizzontale dei pod** usa il server delle metriche in un cluster Kubernetes per monitorare la richiesta di risorse dei pod. Se un servizio richiede più risorse, il numero di pod viene aumentato automaticamente per soddisfare la domanda.

![Il componente di scalabilità automatica del cluster e il componente di scalabilità automatica orizzontale dei pod spesso interagiscono per soddisfare le esigenze delle applicazioni obbligatorie](media/autoscaler/cluster-autoscaler.png)

Sia il componente di scalabilità automatica orizzontale dei pod sia il componente di scalabilità automatica del cluster possono anche ridurre il numero di pod e nodi in base alle esigenze. Il componente di scalabilità automatica del cluster riduce il numero di nodi quando una certa quantità di capacità è rimasta inutilizzata per un periodo di tempo. I pod in un nodo che devono essere rimossi dal componente di scalabilità automatica del cluster sono pianificati in modo sicuro in un'altra posizione nel cluster. Il componente di scalabilità automatica del cluster potrebbe non essere in grado di effettuare la riduzione qualora non fosse possibile spostare i pod, ad esempio nelle situazioni seguenti:

* Un pod creato direttamente e non supportato da un oggetto controller, come un set di distribuzioni o di repliche.
* Un budget di interruzione dei pod (PDB) troppo restrittivo e che non permette la riduzione del numero di pod sotto una determinata soglia.
* Un pod che utilizza selettori di nodo o anti-affinità impossibili da rispettare se pianificati in un nodo diverso.

Per altre informazioni su come il ridimensionamento automatico del cluster potrebbe non essere in grado di ridurre, vedere [quali tipi di Pod possono impedire al cluster di scalabilità automatica di rimuovere un nodo?][autoscaler-scaledown]

Il componente di scalabilità automatica del cluster usa i parametri di avvio per elementi come gli intervalli di tempo tra gli eventi di scalabilità e le soglie delle risorse. Questi parametri vengono definiti dalla piattaforma Azure e non sono attualmente esposti per la modifica da parte dell’utente. Per ulteriori informazioni sui parametri utilizzati dal servizio di scalabilità automatica del cluster, vedere informazioni sui parametri del servizio di scalabilità automatica [del cluster][autoscaler-parameters].

I due componenti di scalabilità automatica possono essere usati insieme e spesso sono entrambi distribuiti in un cluster. Nell’uso combinato, il componente di scalabilità automatica orizzontale dei pod è dedicato all’esecuzione del numero di pod necessari per soddisfare le richieste delle applicazioni. Il componente di scalabilità automatica del cluster è dedicato all’esecuzione del numero di nodi richiesto per supportare i pod pianificati.

> [!NOTE]
> Il ridimensionamento manuale è disabilitato quando si usa il componente di scalabilità automatica del cluster. È il componente di scalabilità automatica del cluster che determina il numero di nodi richiesto. Se si desidera ridimensionare manualmente il cluster [disabilitare il componente di scalabilità automatica del cluster](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Creare un cluster AKS e abilitare il componente di scalabilità automatica del cluster

Se è necessario creare un cluster AKS, usare il comando [AZ AKS create][az-aks-create] . Per abilitare e configurare il servizio di scalabilità automatica del cluster nel pool di nodi per il cluster, usare il parametro *--Enable-cluster-* AutoScaler e specificare un nodo *--min-count* e *--Max-count*.

> [!IMPORTANT]
> Il ridimensionamento automatico del cluster è un componente di Kubernetes. Anche se il cluster AKS usa un set di scalabilità per i nodi di macchine virtuali, non abilitare o modificare manualmente le impostazioni di scalabilità per il ridimensionamento automatico nel portale di Azure o tramite la CLI di Azure. Consentire il ridimensionamento automatico del cluster Kubernetes di gestire le impostazioni di scalabilità necessaria. Per altre informazioni, vedere è [possibile modificare le risorse AKS nel gruppo di risorse del nodo?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

L'esempio seguente crea un cluster AKS con un pool a nodo singolo supportato da un set di scalabilità di macchine virtuali. Abilita anche il ridimensionamento automatico del cluster nel pool di nodi per il cluster e imposta un minimo di *1* e un massimo di *3* nodi:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-vmss \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!NOTE]
> Se si specifica *--kubernetes-Version* durante l'esecuzione `az aks create`, tale versione deve soddisfare o superare il numero di versione minimo richiesto come indicato nella sezione precedente [prima di iniziare](#before-you-begin) .

Sono necessari alcuni minuti per creare il cluster e configurare le impostazioni del componente di scalabilità automatica del cluster.

### <a name="update-the-cluster-autoscaler-on-an-existing-node-pool-in-a-cluster-with-a-single-node-pool"></a>Aggiornare il ridimensionamento automatico del cluster in un pool di nodi esistente in un cluster con un pool a nodo singolo

È possibile aggiornare le impostazioni di scalabilità automatica del cluster precedenti in un cluster che soddisfi i requisiti indicati nella sezione precedente [prima di iniziare](#before-you-begin) . Usare il comando [AZ AKS Update][az-aks-update] per abilitare il ridimensionamento automatico del cluster nel cluster con un pool a nodo *singolo* .

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Successivamente, è possibile abilitare o disabilitare il ridimensionamento automatico del `az aks update --enable-cluster-autoscaler` cluster `az aks update --disable-cluster-autoscaler` con i comandi o.

### <a name="enable-the-cluster-autoscaler-on-an-existing-node-pool-in-a-cluster-with-multiple-node-pools"></a>Abilitare il ridimensionamento automatico del cluster in un pool di nodi esistente in un cluster con più pool di nodi

Il ridimensionamento automatico del cluster può essere usato anche con la [funzionalità di anteprima di più pool di nodi](use-multiple-node-pools.md) abilitata. È possibile abilitare il ridimensionamento automatico del cluster nei singoli pool di nodi all'interno di un cluster AKS che include più pool di nodi e soddisfa i requisiti indicati nella sezione precedente [prima di iniziare](#before-you-begin) . Usare il comando [AZ AKS nodepool Update][az-aks-nodepool-update] per abilitare il ridimensionamento automatico del cluster in un singolo pool di nodi.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Successivamente, è possibile abilitare o disabilitare il ridimensionamento automatico del `az aks nodepool update --enable-cluster-autoscaler` cluster `az aks nodepool update --disable-cluster-autoscaler` con i comandi o.

## <a name="change-the-cluster-autoscaler-settings"></a>Modificare le impostazioni del componente di scalabilità automatica del cluster

Nel passaggio precedente per creare un cluster AKS o aggiornare un pool di nodi esistente, il numero minimo di nodi del cluster AutoScaler è stato impostato su *1*e il numero massimo di nodi è stato impostato su *3*. Se le richieste delle applicazioni cambiano, potrebbe essere necessario regolare il numero di nodi del componente di scalabilità automatica del cluster.

Per modificare il numero di nodi, usare il comando [AZ AKS nodepool Update][az-aks-nodepool-update] .

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

L'esempio precedente aggiorna la scalabilità automatica del cluster nel pool di nodi *mynodepool* in *myAKSCluster* fino a un minimo di *1* e un massimo di *5* nodi.

> [!NOTE]
> Durante l'anteprima, non è possibile impostare un numero di nodi minimo superiore a quello attualmente impostato per il pool di nodi. Ad esempio, se il numero minimo è attualmente impostato su *1*, non è possibile aggiornarlo a *3*.

Monitorare le prestazioni delle applicazioni e dei servizi e modificare il numero di nodi del componente di scalabilità automatica del cluster per ottenere le prestazioni necessarie.

## <a name="disable-the-cluster-autoscaler"></a>Disabilitare il componente di scalabilità automatica del cluster

Se non si vuole più usare il servizio di scalabilità automatica del cluster, è possibile disabilitarlo usando il comando [AZ AKS nodepool Update][az-aks-nodepool-update] , specificando il parametro *--Disable-cluster-* AutoScaler. I nodi non vengono rimossi quando il componente di scalabilità automatica del cluster è disabilitato.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --disable-cluster-autoscaler
```

È possibile ridimensionare manualmente il cluster usando il comando [AZ AKS scale][az-aks-scale] . Se si usa il componente di scalabilità automatica orizzontale dei pod, tale funzionalità continua a essere eseguita con il componente di scalabilità automatica del cluster disabilitato, ma potrebbe non essere possibile pianificare i pod se tutte le risorse del nodo sono in uso.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha descritto come ridimensionare automaticamente il numero di nodi AKS. È anche possibile usare il componente di scalabilità automatica orizzontale dei pod per regolare automaticamente il numero di pod che eseguono l'applicazione. Per i passaggi relativi all'uso del ridimensionamento automatico del Pod orizzontale, vedere [ridimensionare le applicazioni in AKS][aks-scale-apps].

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
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca

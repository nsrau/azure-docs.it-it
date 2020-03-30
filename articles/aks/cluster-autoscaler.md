---
title: Usare il componente di scalabilità automatica del cluster nel servizio Azure Kubernetes (AKS)
description: Informazioni su come usare il componente di scalabilità automatica per ridimensionare automaticamente il cluster e soddisfare le richieste delle applicazioni in un cluster del servizio Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: 0b94865d81afc56c24d470012c668662f003a1b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596250"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Ridimensionare automaticamente un cluster per soddisfare le richieste delle applicazioni nel servizio Azure Kubernetes (AKS)

Per tenere il passo delle richieste delle applicazioni nel servizio Azure Kubernetes (AKS), potrebbe essere necessario regolare il numero di nodi che eseguono i carichi di lavoro. Il componente di scalabilità automatica del cluster può cercare i pod del cluster che non possono essere pianificati a causa di vincoli delle risorse. Quando vengono rilevati problemi, il numero di nodi in un pool di nodi viene aumentato per soddisfare la richiesta dell'applicazione. Inoltre i nodi vengono controllati regolarmente per rilevare la mancanza di pod in esecuzione, con la riduzione del numero di nodi in base alle esigenze. Questa capacità di incrementare o ridurre il numero di nodi nel cluster del servizio Azure Kubernetes permette di gestire il cluster in modo efficace e conveniente.

Questo articolo illustra come abilitare e gestire il componente di scalabilità automatica in un cluster del servizio Azure Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.0.76 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If][azure-cli-install]you need to install or upgrade, see Install Azure CLI.

## <a name="limitations"></a>Limitazioni

Le limitazioni seguenti si applicano quando si creano e si gestiscono cluster AKS che utilizzano il cluster autoscaler:

* Non è possibile utilizzare il componente aggiuntivo di routing dell'applicazione HTTP.

## <a name="about-the-cluster-autoscaler"></a>Informazioni sul componente di scalabilità automatica

Per adattarsi alle variazioni delle richieste delle applicazioni, ad esempio tra l’orario lavorativo diurno e la sera o il fine settimana, è spesso necessario trovare il modo di adattare automaticamente i cluster. La scalabilità dei cluster del servizio Azure Kubernetes può essere ottenuta in uno dei due modi seguenti:

* Il **componente di scalabilità automatica** cerca i pod che non possono essere pianificati nei nodi a causa di vincoli delle risorse. Il cluster aumenta automaticamente il numero di nodi.
* Il **componente di scalabilità automatica orizzontale dei pod** usa il server delle metriche in un cluster Kubernetes per monitorare la richiesta di risorse dei pod. Se un'applicazione necessita di più risorse, il numero di pod viene aumentato automaticamente per soddisfare la domanda.

![Il componente di scalabilità automatica del cluster e il componente di scalabilità automatica orizzontale dei pod spesso interagiscono per soddisfare le esigenze delle applicazioni obbligatorie](media/autoscaler/cluster-autoscaler.png)

Sia il pod orizzontale autoscaler che il cluster autoscaler possono anche ridurre il numero di pod e nodi in base alle esigenze. Il componente di scalabilità automatica del cluster riduce il numero di nodi quando una certa quantità di capacità è rimasta inutilizzata per un periodo di tempo. I pod in un nodo che devono essere rimossi dal componente di scalabilità automatica del cluster sono pianificati in modo sicuro in un'altra posizione nel cluster. Il componente di scalabilità automatica del cluster potrebbe non essere in grado di effettuare la riduzione qualora non fosse possibile spostare i pod, ad esempio nelle situazioni seguenti:

* Un pod viene creato direttamente e non è supportato da un oggetto controller, ad esempio una distribuzione o un set di repliche.
* Un budget di interruzione dei pod (PDB) troppo restrittivo e che non permette la riduzione del numero di pod sotto una determinata soglia.
* Un pod che utilizza selettori di nodo o anti-affinità impossibili da rispettare se pianificati in un nodo diverso.

Per altre informazioni sui casi in cui il componente di scalabilità automatica del cluster potrebbe non essere in grado di effettuare la riduzione, vedere [Quali tipi di pod possono impedire al componente di scalabilità automatica del cluster di rimuovere un nodo?][autoscaler-scaledown]

Il componente di scalabilità automatica del cluster usa i parametri di avvio per elementi come gli intervalli di tempo tra gli eventi di scalabilità e le soglie delle risorse. Questi parametri vengono definiti dalla piattaforma Azure e non sono attualmente esposti per la modifica da parte dell’utente. Per altre informazioni sui parametri usati dal componente di scalabilità automatica del cluster, vedere [Quali sono i parametri del componente di scalabilità automatica del cluster?][autoscaler-parameters].

I cluster e i contenitori automatici orizzontali possono funzionare insieme e sono spesso entrambi distribuiti in un cluster. Nell’uso combinato, il componente di scalabilità automatica orizzontale dei pod è dedicato all’esecuzione del numero di pod necessari per soddisfare le richieste delle applicazioni. Il componente di scalabilità automatica del cluster è dedicato all’esecuzione del numero di nodi richiesto per supportare i pod pianificati.

> [!NOTE]
> Il ridimensionamento manuale è disabilitato quando si usa il componente di scalabilità automatica del cluster. È il componente di scalabilità automatica del cluster che determina il numero di nodi richiesto. Se si desidera ridimensionare manualmente il cluster [disabilitare il componente di scalabilità automatica del cluster](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Creare un cluster del servizio Azure Kubernetes e abilitare il componente di scalabilità automatica del cluster

Se è necessario creare un cluster AKS, usare il comando [az aks create][az-aks-create]. Per abilitare e configurare il cluster autoscaler nel pool di nodi per il cluster, utilizzare il parametro *--enable-cluster-autoscaler* e specificare un nodo *--min-count* e *--max-count*.

> [!IMPORTANT]
> Il ridimensionamento automatico del cluster è un componente di Kubernetes. Anche se il cluster del servizio Azure Kubernetes usa un set di scalabilità per i nodi di macchine virtuali, non abilitare o modificare manualmente le impostazioni di scalabilità per il ridimensionamento automatico nel portale di Azure o tramite l'interfaccia della riga di comando di Azure. Consentire il ridimensionamento automatico del cluster Kubernetes di gestire le impostazioni di scalabilità necessaria. Per altre informazioni, vedere [È possibile modificare le risorse AKS nel gruppo](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group) di risorse del nodo?

Nell'esempio seguente viene creato un cluster AKS con un pool a nodo singolo supportato da un set di scalabilità di macchine virtuali. Abilita inoltre il cluster autoscaler nel pool di nodi per il cluster e imposta un minimo di *1* e un massimo di *3* nodi:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Sono necessari alcuni minuti per creare il cluster e configurare le impostazioni del componente di scalabilità automatica del cluster.

## <a name="change-the-cluster-autoscaler-settings"></a>Modificare le impostazioni del componente di scalabilità automatica del cluster

> [!IMPORTANT]
> Se nel cluster AKS sono presenti più pool di nodi, passare alla [sezione Scalabilità automatica con più pool di agenti](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled). I cluster con più pool `az aks nodepool` di agenti richiedono l'utilizzo `az aks`del set di comandi per modificare le proprietà specifiche del pool di nodi anziché .

Nel passaggio precedente per creare un cluster AKS o aggiornare un pool di nodi esistente, il numero minimo di nodi del cluster autoscaler è stato impostato su *1*e il numero massimo di nodi è stato impostato su *3*. Se le richieste delle applicazioni cambiano, potrebbe essere necessario regolare il numero di nodi del componente di scalabilità automatica del cluster.

Per modificare il numero di nodi, utilizzare il comando [az aks update.][az-aks-update]

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

L'esempio precedente aggiorna il cluster autoscaler nel pool a nodo singolo in *myAKSCluster* a un minimo di *1* e massimo di *5* nodi.

> [!NOTE]
> Non è possibile impostare un numero di nodi minimo superiore rispetto a quello attualmente impostato per il pool di nodi. Ad esempio, se il numero minimo è attualmente impostato su *1*, non è possibile aggiornarlo a *3*.

Monitorare le prestazioni delle applicazioni e dei servizi e modificare il numero di nodi del componente di scalabilità automatica del cluster per ottenere le prestazioni necessarie.

## <a name="using-the-autoscaler-profile"></a>Utilizzo del profilo autoscaler

È inoltre possibile configurare dettagli più granulari del cluster autoscaler modificando i valori predefiniti nel profilo di scalabilità automatica a livello di cluster. Ad esempio, un evento di scalabilità verticale si verifica dopo che i nodi sono sottoutilizzati dopo 10 minuti. Se si dispone di carichi di lavoro che vengono eseguiti ogni 15 minuti, è possibile modificare il profilo di scalabilità automatica per ridurre le prestazioni nei nodi utilizzati dopo 15 o 20 minuti. Quando si abilita il cluster autoscaler, viene utilizzato un profilo predefinito a meno che non si specifichino impostazioni diverse. Il profilo di scalabilità automatica del cluster ha le seguenti impostazioni che è possibile aggiornare:

| Impostazione                          | Descrizione                                                                              | Valore predefinito |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| intervallo di scansione                    | Frequenza di rivalutazione del cluster per la scalabilità verticale o verticale                                    | 10 secondi    |
| scale-down-delay-after-add       | Per quanto tempo dopo la scalabilità verticale di questa scala verso il basso la valutazione di ripresa                               | 10 minuti    |
| scale-down-delay-after-delete    | Durata della ripresa della valutazione della valutazione con scalabilità verticale                          | intervallo di scansione |
| scale-down-delay-after-failure   | Quanto tempo dopo il fallimento della scalabilità verso il basso che scala verso il basso la valutazione riprende                     | 3 minuti     |
| tempo di scala verso il basso non necessario         | Per quanto tempo un nodo deve non essere necessario prima di essere idoneo per la scalabilità verticale                  | 10 minuti    |
| tempo di ridimensionamento verso il basso          | Per quanto tempo un nodo non pronto deve non essere necessario prima di essere idoneo per la scalabilità verso il basso         | 20 minuti    |
| soglia di utilizzo verso il basso | Livello di utilizzo del nodo, definito come somma delle risorse richieste diviso per la capacità, al di sotto del quale un nodo può essere considerato per la scalabilità ridottaNode utilization level, defined as sum of requested resources divided by capacity, below which a node can be considered for scale down | 0.5 |
| max-graceful-termination-sec     | Numero massimo di secondi che il cluster autoscaler attende la terminazione del pod durante il tentativo di ridurre un nodo. | 600 secondi   |

> [!IMPORTANT]
> Il profilo di scalabilità automatica del cluster influisce su tutti i pool di nodi che utilizzano il cluster autoscaler. Non è possibile impostare un profilo di scalabilità automatica per ogni pool di nodi.

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga comando di aks-preview

Per impostare il profilo delle impostazioni del cluster autoscaler, è necessario il profilo dell'estensione dell'interfaccia della riga di comando *di aks-preview* 0.4.30 o successiva. Installare l'estensione aks-preview di Azure CLI usando il comando [az extension add,][az-extension-add] quindi verificare la presenza di eventuali aggiornamenti disponibili usando il comando [az extension update:Install][az-extension-update] the *aks-preview* Azure CLI extension using the az extension add command, then check for any available updates using the az extension update command:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>Impostare il profilo di scalabilità automatica del cluster in un cluster AKS esistenteSet the cluster autoscaler profile on an existing AKS cluster

Utilizzare il comando [az aks update][az-aks-update] con il parametro *cluster-autoscaler-profile* per impostare il profilo di scalabilità automatica del cluster nel cluster. Nell'esempio seguente l'impostazione dell'intervallo di scansione viene configurata su 30nel profilo.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

Quando si abilita il cluster autoscaler nei pool di nodi del cluster, tali cluster utilizzeranno anche il profilo di scalabilità automatica del cluster. Ad esempio:

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!IMPORTANT]
> Quando si imposta il profilo di scalabilità automatica del cluster, tutti i pool di nodi esistenti con il cluster autoscaler abilitato inizieranno a usare immediatamente il profilo.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>Impostare il profilo di scalabilità automatica del cluster durante la creazione di un cluster AKSSet the cluster autoscaler profile when creating an AKS cluster

È anche possibile usare il parametro *cluster-autoscaler-profile* quando si crea il cluster. Ad esempio:

```azurecli-interactive
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3 \
  --cluster-autoscaler-profile scan-interval=30s
```

Il comando precedente crea un cluster AKS e definisce l'intervallo di analisi come 30 secondi per il profilo di scalabilità automatica a livello di cluster. Il comando abilita inoltre il cluster autoscaler nel pool di nodi iniziale, imposta il numero minimo di nodi su 1 e il numero massimo di nodi su 3.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Reimpostare i valori predefiniti del profilo di scalabilità automatica del cluster

Utilizzare il comando [az aks update][az-aks-update] per reimpostare il profilo di scalabilità automatica del cluster nel cluster.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>Disabilitare il componente di scalabilità automatica del cluster

Se non si desidera più utilizzare il cluster autoscaler, è possibile disabilitarlo utilizzando il comando [az aks update,][az-aks-update] specificando il parametro *--disable-cluster-autoscaler.* I nodi non vengono rimossi quando il componente di scalabilità automatica del cluster è disabilitato.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

È possibile ridimensionare manualmente il cluster dopo aver disabilitato il ridimensionamento automatico del cluster usando il comando [az aks scale.][az-aks-scale] Se si utilizza il scalatore automatico del pod orizzontale, tale funzionalità continua a essere eseguita con il ridimensionamento automatico del cluster disabilitato, ma i pod potrebbero finire per non essere pianificati se tutte le risorse del nodo sono in uso.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Riabilitare un cluster abilitato abilitato

Se si desidera riattivare il cluster autoscaler in un cluster esistente, è possibile riabilitarlo utilizzando il comando [az aks update,][az-aks-update] specificando i parametri *--enable-cluster-autoscaler*, *--min-count*e *--max-count* .

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Recuperare i registri e lo stato di autoscaler del clusterRetrieve cluster autoscaler logs and status

Per diagnosticare ed eseguire il debug degli eventi di autoscaler, i log e lo stato possono essere recuperati dal componente aggiuntivo di scalabilità automatica.

AKS gestisce il cluster autoscaler per tuo conto e lo esegue nel piano di controllo gestito. I log del nodo master devono essere configurati per essere visualizzati come risultato.

Per configurare i log da eseguire il push dal cluster autoscaler in Log Analytics, attenersi alla seguente procedura.

1. Configurare una regola per i log di diagnostica per eseguire il push dei log di scalabilità automatica del cluster in Log Analytics.Set up a rule for diagnostic logs to push cluster-autoscaler logs to Log Analytics. [Le istruzioni sono descritte in dettaglio qui, assicurarsi](https://docs.microsoft.com/azure/aks/view-master-logs#enable-diagnostics-logs)di selezionare la casella per `cluster-autoscaler` quando si selezionano le opzioni per "Registri".
1. Fare clic sulla sezione "Logs" nel cluster tramite il portale di Azure.Click on the "Logs" section on your cluster via the Azure portal.
1. Immettere la query di esempio seguente in Log Analytics:Input the following example query into Log Analytics:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Dovrebbero essere visualizzati registri simili all'esempio seguente, purché siano presenti registri da recuperare.

![Log di Log Analytics](media/autoscaler/autoscaler-logs.png)

Il cluster autoscaler scriverà anche lo stato `cluster-autoscaler-status`di integrità in una configmap denominata . Per recuperare questi registri, `kubectl` eseguire il comando seguente. Verrà segnalato uno stato di integrità per ogni pool di nodi configurato con il cluster autoscaler.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Per ulteriori informazioni su ciò che viene registrato dal scaler automatico, leggere le domande frequenti sul [progetto Kubernetes/autoscaler GitHub](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why).

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Usare il cluster autoscaler con più pool di nodi abilitatiUse the cluster autoscaler with multiple node pools enabled

Il cluster autoscaler può essere utilizzato con più pool di [nodi](use-multiple-node-pools.md) abilitati. Seguire il documento per informazioni su come abilitare pool di più nodi e aggiungere pool di nodi aggiuntivi a un cluster esistente. Quando si usano entrambe le funzionalità insieme, si abilita il cluster autoscaler in ogni pool di singoli nodi del cluster e si possono passare a ciascuno regole di scalabilità automatica univoche.

Il comando seguente presuppone che siano state seguite [le istruzioni iniziali](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) in questo documento e che si desideri aggiornare il numero massimo di un pool di nodi esistente da *3* a *5.* Utilizzare il comando [az aks nodepool update][az-aks-nodepool-update] per aggiornare le impostazioni di un pool di nodi esistente.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Il cluster autoscaler può essere disabilitato con az `--disable-cluster-autoscaler` [aks nodepool update][az-aks-nodepool-update] e passando il parametro.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Se si desidera riattivare il cluster autoscaler in un cluster esistente, è possibile riabilitarlo utilizzando il comando [az aks nodepool update,][az-aks-nodepool-update] specificando i parametri *--enable-cluster-autoscaler*, *--min-count*e *--max-count* .

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha descritto come ridimensionare automaticamente il numero di nodi del servizio Azure Kubernetes. È anche possibile usare il componente di scalabilità automatica orizzontale dei pod per regolare automaticamente il numero di pod che eseguono l'applicazione. Per istruzioni sull'uso del componente di scalabilità automatica orizzontale dei pod, vedere [Ridimensionare le applicazioni nel servizio Azure Kubernetes][aks-scale-apps].

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca

---
title: Anteprima - usare un servizio di bilanciamento del carico dello SKU Standard in Azure Kubernetes Service (AKS)
description: Informazioni su come usare un servizio di bilanciamento del carico con SKU Standard per esporre i servizi con Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/25/2019
ms.author: zarhoads
ms.openlocfilehash: a9cf3db3a15fab5a2f067a146950e02923a20379
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476815"
---
# <a name="preview---use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Anteprima - usare un servizio di bilanciamento del carico dello SKU Standard in Azure Kubernetes Service (AKS)

Per fornire accesso alle applicazioni in Azure Kubernetes Service (AKS), è possibile creare e usare un servizio di bilanciamento del carico di Azure. Un servizio di bilanciamento del carico in esecuzione nel servizio contenitore di AZURE può essere utilizzato come interna o un servizio di bilanciamento del carico esterno. Un servizio di bilanciamento del carico interno rende accessibile solo alle applicazioni in esecuzione nella stessa rete virtuale del cluster servizio contenitore di AZURE un servizio Kubernetes. Un servizio di bilanciamento del carico esterno riceve uno o più indirizzi IP pubblici per l'ingresso e rende accessibile esternamente tramite gli indirizzi IP pubblici di un servizio Kubernetes.

Azure Load Balancer è disponibile in due SKU: *Basic* e *Standard*. Per impostazione predefinita, il *base* SKU viene usato quando un manifesto del servizio viene usato per creare un servizio di bilanciamento del carico nel servizio contenitore di AZURE. Usando un *Standard* SKU load balancer offre altre caratteristiche e funzionalità, quali dimensioni del pool back-end più grande e le zone di disponibilità. È importante comprendere le differenze tra *Standard* e *base* bilanciamenti del carico prima di scegliere quale usare. Dopo aver creato un cluster AKS, è possibile modificare il bilanciamento del carico SKU per il cluster. Per altre informazioni sul *base* e *Standard* SKU, vedere [confronto tra gli SKU del servizio di bilanciamento carico di Azure][azure-lb-comparison].

Questo articolo illustra come creare e usare un servizio di bilanciamento del carico di Azure con il *Standard* SKU con Azure Kubernetes Service (AKS).

Questo articolo presuppone una conoscenza di base dei concetti relativi a Kubernetes e Azure Load Balancer. Per altre informazioni, vedere [Kubernetes per Azure Kubernetes Service (AKS) concetti fondamentali][kubernetes-concepts] and [What is Azure Load Balancer?][azure-lb].

Questa funzionalità è attualmente in anteprima.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo è necessario eseguire il comando di Azure versione 2.0.59 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="before-you-begin"></a>Prima di iniziare

L'entità servizio cluster AKS richiede l'autorizzazione per gestire le risorse di rete se si usa una subnet esistente o un gruppo di risorse. In generale, assegnare il *collaboratore rete* ruolo all'entità servizio per le risorse Delegate. Per altre informazioni sulle autorizzazioni, vedere [AKS delegato accedere ad altre risorse di Azure][aks-sp].

È necessario creare un cluster servizio contenitore di AZURE che consente di impostare lo SKU per il bilanciamento del carico *Standard* anziché il valore predefinito *base*. Creazione di un cluster AKS viene trattata in un passaggio successivo, ma è innanzitutto necessario abilitare alcune funzionalità di anteprima.

> [!IMPORTANT]
> Funzionalità di anteprima del servizio contenitore di AZURE sono self-service, fornire il consenso esplicito. Vengono fornite per raccogliere commenti e suggerimenti e bug dalla community. In fase di anteprima, queste funzionalità non sono destinate all'uso di produzione. Le funzionalità in anteprima pubblica rientrano nel supporto "best effort". Assistenza dai team di supporto tecnico di AKS è disponibile durante le ore lavorative Pacifico (PST) solo timezone. Per altre informazioni, vedere i seguenti articoli di supporto:
>
> * [Criteri di supporto servizio contenitore di AZURE][aks-support-policies]
> * [Domande frequenti relative al supporto tecnico Azure][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga comando di aks-preview

Usare il servizio di bilanciamento del carico di Azure lo SKU standard, è necessario il *aks-preview* CLI versione dell'estensione 0.4.1 o versione successiva. Installare il *aks-preview* estensione di comando di Azure usando la [Aggiungi estensione az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] comando::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-aksazurestandardloadbalancer-preview-feature"></a>Registrare AKSAzureStandardLoadBalancer anteprima funzionalità

Per creare un cluster servizio contenitore di AZURE che è possibile usare un servizio di bilanciamento del carico con il *Standard* SKU, è necessario abilitare il *AKSAzureStandardLoadBalancer* flag per la sottoscrizione di funzionalità. Il *AKSAzureStandardLoadBalancer* anche funzionalità vengono utilizzati *VMSSPreview* durante la creazione di un cluster usando il set di scalabilità di macchine virtuali. Questa funzionalità fornisce il set più recente di miglioramenti del servizio quando si configura un cluster. Sebbene non sia obbligatorio, si consiglia di abilitare la *VMSSPreview* anche flag delle funzionalità.

> [!CAUTION]
> Quando si registra una funzionalità in una sottoscrizione, attualmente non è possibile annullare la registrazione tale funzionalità. Dopo aver abilitato alcune funzionalità di anteprima, potrebbero essere utilizzati i valori predefiniti per tutti i cluster AKS quindi creati nella sottoscrizione. Non abilitare la funzionalità di anteprima nella sottoscrizione di produzione. Usare una sottoscrizione separata per le funzionalità di anteprima di test e raccogliere commenti e suggerimenti.

Registrare il *VMSSPreview* e *AKSAzureStandardLoadBalancer* i flag funzionalità tramite il [register funzionalità az][az-feature-register] seguente come mostrato nell'esempio seguente:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "VMSSPreview"
az feature register --namespace "Microsoft.ContainerService" --name "AKSAzureStandardLoadBalancer"
```

> [!NOTE]
> Tutti i cluster servizio contenitore di AZURE create dopo aver correttamente registrato il *VMSSPreview* oppure *AKSAzureStandardLoadBalancer* questa esperienza di cluster di anteprima di usare i flag funzionalità. Per continuare a creare i cluster normali, completamente supportato, non abilitare la funzionalità di anteprima nella sottoscrizione di produzione. Usare un test separate o sviluppo sottoscrizione di Azure per verificare le funzionalità di anteprima.

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllarne lo stato di registrazione usando il [elenco delle funzionalità az][az-feature-list] comando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione dei *containerservice* provider di risorse usando la [register di az provider][az-provider-register] comando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Limitazioni

Le limitazioni seguenti si applicano quando si creano e gestire i cluster servizio contenitore di AZURE che supportano un servizio di bilanciamento del carico con il *Standard* SKU:

* Quando si usa la *Standard* SKU per un servizio di bilanciamento del carico, è necessario consentire gli indirizzi pubblici ed evitare la creazione di qualsiasi criterio di Azure che esclude la creazione di IP. Il cluster AKS crea automaticamente un *Standard* indirizzo IP pubblico dello SKU nello stesso gruppo di risorse creato per il cluster AKS, che in genere denominati con *MC _* all'inizio. Servizio contenitore di AZURE assegna l'indirizzo IP pubblico per il *Standard* bilanciamento del carico dello SKU. L'indirizzo IP pubblico è necessario per consentire il traffico in uscita dal cluster AKS. Questo indirizzo IP pubblico è anche necessario per mantenere la connettività tra i piano e agente di nodi di controllo anche tale da mantenere la compatibilità con le versioni precedenti del servizio contenitore di AZURE.
* Quando si usa la *Standard* SKU per un servizio di bilanciamento del carico, è necessario usare Kubernetes versione 1.13.5 o versione successiva.

Quando questa funzionalità è disponibile in anteprima, si applicano le limitazioni aggiuntive seguenti:

* Quando si usa la *Standard* SKU per un servizio di bilanciamento del carico nel servizio contenitore di AZURE, non è possibile impostare il proprio indirizzo IP pubblico in uscita per il bilanciamento del carico. È necessario usare l'indirizzo IP che servizio contenitore di AZURE assegna al servizio di bilanciamento del carico.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un gruppo logico in cui le risorse di Azure vengono distribuite e gestite. Quando si crea un gruppo di risorse, viene chiesto di specificare una posizione. Questo è il percorso in cui vengono archiviati i metadati del gruppo di risorse, ma anche quello in cui vengono eseguite le risorse in Azure se non si specifica un'altra area durante la creazione della risorsa. Creare un gruppo di risorse con il comando [az group create][az-group-create].

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

L'output di esempio seguente mostra il gruppo di risorse creato correttamente:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-aks-cluster"></a>Creare un cluster del servizio Azure Container
Per eseguire un cluster AKS che supporta un servizio di bilanciamento del carico con il *Standard* SKU, il cluster deve impostare la *sku di load balancer* parametro *standard*. Questo parametro consente di creare un servizio di bilanciamento del carico con il *Standard* SKU quando viene creato il cluster. Quando si esegue una *LoadBalancer* nel cluster, la configurazione del servizio il *Standard* servizio di bilanciamento del carico SK viene aggiornato con la configurazione del servizio. Usare la [az aks create][az-aks-create] comando per creare un cluster del servizio contenitore di AZURE denominato *myAKSCluster*.

> [!NOTE]
> Il *--sku di load balancer* proprietà può essere utilizzata solo quando viene creato il cluster. Dopo aver creato un cluster AKS, è possibile modificare il servizio di bilanciamento del carico SKU. Inoltre, è possibile usare solo un tipo di bilanciamento del carico SKU in un singolo cluster.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --kubernetes-version 1.14.0 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

Il comando viene completato dopo pochi minuti e vengono restituite informazioni in formato JSON sul cluster.

## <a name="connect-to-the-cluster"></a>Connettersi al cluster

Per gestire un cluster Kubernetes, si utilizza [kubectl][kubectl], il client della riga di comando di Kubernetes. Se si usa Azure Cloud Shell, `kubectl` è già installato. Per installare `kubectl` in locale, usare il [az aks install-cli][az-aks-install-cli] comando:

```azurecli
az aks install-cli
```

Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az aks get-credentials][az-aks-get-credentials]. Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarli.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Per verificare la connessione al cluster, usare il comando [kubectl get][kubectl-get] per restituire un elenco dei nodi del cluster.

```azurecli-interactive
kubectl get nodes
```

L'esempio di output seguente mostra il nodo singolo creato nei passaggi precedenti. Assicurarsi che lo stato del nodo sia impostato su *Pronto*:

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.14.0
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>Verificare il cluster Usa la *Standard* SKU

Usare la [show di az aks][az-aks-show] per visualizzare la configurazione del cluster.

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster

{
  "aadProfile": null,
  "addonProfiles": null,
   ...
   "networkProfile": {
    "dnsServiceIp": "10.0.0.10",
    "dockerBridgeCidr": "172.17.0.1/16",
    "loadBalancerSku": "standard",
    ...
```

Verificare i *loadBalancerSku* indicato per la proprietà *standard*.

## <a name="use-the-load-balancer"></a>Usare il servizio di bilanciamento del carico

Per usare il bilanciamento del carico nel cluster, creare un manifesto del servizio con il tipo di servizio *LoadBalancer*. Per mostrare i servizio di bilanciamento del carico di lavoro, creare il manifesto di un altro con un'applicazione di esempio per l'esecuzione nel cluster. Questa applicazione di esempio viene esposta tramite il servizio di bilanciamento del carico e può essere visualizzata tramite un browser.

Creare un manifesto denominato `sample.yaml` come illustrato nell'esempio seguente:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
```

Il manifesto precedente consente di configurare due distribuzioni: *azure-vote-front* e *azure-vote-back*. Per configurare *azure-vote-front* distribuzione deve essere esposta tramite il bilanciamento del carico, creare un manifesto denominato `standard-lb.yaml` come illustrato nell'esempio seguente:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Il servizio *azure-vote-front* Usa le *LoadBalancer* tipo per configurare il bilanciamento del carico nel cluster servizio contenitore di AZURE per connettersi al *azure-vote-front* distribuzione.

Distribuire l'applicazione di esempio e il carico del servizio di bilanciamento con la [kubectl applicare][kubectl-apply] e specificare il nome dei manifesti YAML:

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

Il *Standard* servizio di bilanciamento del carico SKU è configurata per esporre l'applicazione di esempio. Visualizzare i dettagli del servizio del *azure-vote-front* utilizzando [kubectl get][kubectl-get] per visualizzare l'indirizzo IP pubblico del bilanciamento del carico. L'indirizzo IP pubblico del bilanciamento del carico viene visualizzato nei *EXTERNAL-IP* colonna. Potrebbe richiedere un minuto o due per l'indirizzo IP da modificare *\<in sospeso\>* a un indirizzo IP esterno effettivo, come illustrato nell'esempio seguente:

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

Passare all'IP pubblico in un browser e verificare che sia presente l'applicazione di esempio. Nell'esempio precedente, l'indirizzo IP pubblico è `52.179.23.131`.

![Immagine del passaggio ad Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> È possibile anche configurare il bilanciamento del carico per essere interni e non esporre un indirizzo IP pubblico. Per configurare il bilanciamento del carico come interni, aggiungere `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` come un'annotazione per la *LoadBalancer* servizio. È possibile visualizzare un esempio yaml manifesto anche come altri dettagli su un servizio di bilanciamento del carico interno [qui][internal-lb-yaml].

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>Pulire la configurazione di bilanciamento del carico dello SKU Standard

Per rimuovere la configurazione di bilanciamento del carico e applicazione di esempio, utilizzare [kubectl eliminare][kubectl-delete]:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui servizi di Kubernetes nel [documentazione di servizi di Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

---
title: Usare un servizio di bilanciamento del carico con SKU standard in Azure Kubernetes Service (AKS)
description: Informazioni su come usare un servizio di bilanciamento del carico con uno SKU standard per esporre i servizi con Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/05/2019
ms.author: zarhoads
ms.openlocfilehash: 42323af40ee18a965363321196a04aa75c00aa40
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996947"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Usare un servizio di bilanciamento del carico con SKU standard in Azure Kubernetes Service (AKS)

Per consentire l'accesso alle applicazioni in Azure Kubernetes Service (AKS), è possibile creare e usare un Azure Load Balancer. Un servizio di bilanciamento del carico in esecuzione su AKS può essere usato come servizio di bilanciamento del carico interno o esterno. Un servizio di bilanciamento del carico interno rende accessibile un servizio Kubernetes solo alle applicazioni in esecuzione nella stessa rete virtuale del cluster AKS. Un servizio di bilanciamento del carico esterno riceve uno o più indirizzi IP pubblici per il traffico in ingresso e rende accessibile esternamente un servizio Kubernetes usando gli indirizzi IP pubblici.

Azure Load Balancer è disponibile in due SKU: *Basic* e *Standard*. Per impostazione predefinita, viene usato lo SKU *Basic* quando viene usato un manifesto del servizio per creare un servizio di bilanciamento del carico in AKS. L'uso di un servizio di bilanciamento del carico con SKU *standard* fornisce funzionalità e funzionalità aggiuntive, ad esempio dimensioni del pool back-end maggiori e zone di disponibilità. Prima di scegliere quale usare, è importante comprendere le differenze tra i bilanciamenti del carico *standard* e *Basic* . Dopo aver creato un cluster AKS, non è possibile modificare lo SKU del servizio di bilanciamento del carico per quel cluster. Per altre informazioni sugli SKU *Basic* e *standard* , vedere [confronto tra SKU di bilanciamento del carico di Azure][azure-lb-comparison].

Questo articolo illustra come creare e usare un Azure Load Balancer con lo SKU *standard* con Azure Kubernetes Service (AKS).

Questo articolo presuppone una conoscenza di base dei concetti relativi a Kubernetes e Azure Load Balancer. Per altre informazioni, vedere [Kubernetes Core Concepts for Azure Kubernetes Service (AKS)][kubernetes-concepts] e informazioni su [Azure Load Balancer][azure-lb].

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.59 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="before-you-begin"></a>Prima di iniziare

L'entità servizio cluster AKS necessita dell'autorizzazione per gestire le risorse di rete se si usa una subnet o un gruppo di risorse esistente. In generale, assegnare il ruolo *collaboratore rete* all'entità servizio per le risorse Delegate. Per altre informazioni sulle autorizzazioni, vedere [delega dell'accesso AKS ad altre risorse di Azure][aks-sp].

È necessario creare un cluster AKS che imposta lo SKU per il servizio di bilanciamento del carico su *standard* anziché quello di *base*predefinito.

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga comando di aks-preview

Per usare lo SKU di Azure Load Balancer standard, è necessaria l'estensione dell'interfaccia della riga di comando *AKS-Preview* versione 0.4.12 o successiva. Installare l'estensione dell'interfaccia della riga di comando di Azure *AKS-Preview* usando il comando [AZ Extension Add][az-extension-add] , quindi verificare la disponibilità di eventuali aggiornamenti tramite il comando [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Limitazioni

Quando si creano e si gestiscono cluster AKS che supportano un servizio di bilanciamento del carico con lo SKU *standard* , si applicano le limitazioni seguenti:

* È necessario almeno un IP pubblico o un prefisso IP per consentire il traffico in uscita dal cluster AKS. L'IP pubblico o il prefisso IP è necessario anche per mantenere la connettività tra il piano di controllo e i nodi dell'agente, nonché per mantenere la compatibilità con le versioni precedenti di AKS. Sono disponibili le opzioni seguenti per specificare gli indirizzi IP pubblici o i prefissi IP con un servizio di bilanciamento del carico SKU *standard* :
    * Fornire gli indirizzi IP pubblici personali.
    * Fornire i prefissi IP pubblici.
    * Specificare un numero massimo di 100 per consentire al cluster AKS di creare che molti indirizzi IP pubblici dello SKU *standard* nello stesso gruppo di risorse creato come cluster AKS, che in genere è denominato con *MC_* all'inizio. AKS assegna l'indirizzo IP pubblico al servizio di bilanciamento del carico dello SKU *standard* . Per impostazione predefinita, un IP pubblico verrà creato automaticamente nello stesso gruppo di risorse del cluster AKS, se non è specificato alcun indirizzo IP pubblico, prefisso IP pubblico o numero di indirizzi IP. È anche necessario consentire indirizzi pubblici ed evitare di creare criteri di Azure che vietino la creazione di IP.
* Quando si usa lo SKU *standard* per un servizio di bilanciamento del carico, è necessario usare Kubernetes versione 1,13 o successiva.
* La definizione dello SKU del servizio di bilanciamento del carico può essere eseguita solo quando si crea un cluster AKS. Non è possibile modificare lo SKU del servizio di bilanciamento del carico dopo che è stato creato un cluster AKS.
* È possibile usare un solo SKU del servizio di bilanciamento del carico in un singolo cluster.

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
Per eseguire un cluster AKS che supporta un servizio di bilanciamento del carico con lo SKU *standard* , il cluster deve impostare il parametro *Load-Balancer-SKU* su *standard*. Questo parametro crea un servizio di bilanciamento del carico con lo SKU *standard* quando viene creato il cluster. Quando si esegue un servizio *LoadBalancer* nel cluster, la configurazione del servizio di bilanciamento del carico dello SKU *standard* viene aggiornata con la configurazione del servizio. Usare il comando [AZ AKS create][az-aks-create] per creare un cluster AKS denominato *myAKSCluster*.

> [!NOTE]
> La proprietà *Load-Balancer-SKU* può essere usata solo quando si crea il cluster. Non è possibile modificare lo SKU del servizio di bilanciamento del carico dopo che è stato creato un cluster AKS. Inoltre, è possibile usare un solo tipo di SKU del servizio di bilanciamento del carico in un singolo cluster.
> 
> Se si vogliono usare gli indirizzi IP pubblici, usare i parametri *Load-Balancer-Outbound-IPSS*o *Load-Balancer-Outbound-IP-Prefixes* . Entrambi questi parametri possono essere usati anche quando si [Aggiorna il cluster](#optional---provide-your-own-public-ips-or-prefixes-for-egress).

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

Il comando viene completato dopo pochi minuti e vengono restituite informazioni in formato JSON sul cluster.

## <a name="connect-to-the-cluster"></a>Connettersi al cluster

Per gestire un cluster Kubernetes, usare [kubectl][kubectl], il client da riga di comando di Kubernetes. Se si usa Azure Cloud Shell, `kubectl` è già installato. Per installare `kubectl` in locale, usare il comando [az aks install-cli][az-aks-install-cli]:

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
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.13.10
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>Verificare che il cluster usi lo SKU *standard*

Usare il comando [AZ AKS Show][az-aks-show] per visualizzare la configurazione del cluster.

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

Verificare che la proprietà *loadBalancerSku* sia visualizzata come *standard*.

## <a name="use-the-load-balancer"></a>Usare il servizio di bilanciamento del carico

Per usare il servizio di bilanciamento del carico nel cluster, creare un manifesto del servizio con il tipo di servizio *LoadBalancer*. Per visualizzare il servizio di bilanciamento del carico, creare un altro manifesto con un'applicazione di esempio da eseguire nel cluster. Questa applicazione di esempio viene esposta tramite il servizio di bilanciamento del carico e può essere visualizzata tramite un browser.

Creare un manifesto denominato `sample.yaml` , come illustrato nell'esempio seguente:

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

Il manifesto precedente configura due distribuzioni: *Azure-vote-front* e *Azure-vote-back*. Per configurare la distribuzione di *Azure-vote-front* per l'esposizione tramite il servizio di bilanciamento del carico, `standard-lb.yaml` creare un manifesto denominato come illustrato nell'esempio seguente:

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

Il servizio *Azure-vote-front* usa il tipo *LoadBalancer* per configurare il servizio di bilanciamento del carico nel cluster AKS per connettersi alla distribuzione *Azure-vote-front* .

Distribuire l'applicazione di esempio e il servizio di bilanciamento del carico usando [kubectl applicare][kubectl-apply] e specificare il nome dei manifesti YAML:

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

Il servizio di bilanciamento del carico SKU *standard* è ora configurato per esporre l'applicazione di esempio. Visualizzare i dettagli del servizio *Azure-vote-front* con [kubectl Get][kubectl-get] per visualizzare l'indirizzo IP pubblico del servizio di bilanciamento del carico. L'indirizzo IP pubblico del servizio di bilanciamento del carico viene visualizzato nella colonna *External-IP* . La modifica dell'indirizzo IP da *\<pending\>* a un indirizzo IP esterno effettivo può richiedere un paio di minuti, come illustrato nell'esempio seguente:

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

Passare all'indirizzo IP pubblico in un browser e verificare che sia visualizzata l'applicazione di esempio. Nell'esempio precedente l'indirizzo IP pubblico è `52.179.23.131`.

![Immagine del passaggio ad Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> È anche possibile configurare il servizio di bilanciamento del carico in modo che sia interno e non esponga un indirizzo IP pubblico. Per configurare il servizio di bilanciamento del carico come interno `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` , aggiungere come annotazione al servizio *LoadBalancer* . [Qui][internal-lb-yaml]è possibile visualizzare un manifesto YAML di esempio, oltre a informazioni dettagliate su un servizio di bilanciamento del carico interno.

## <a name="optional---scale-the-number-of-managed-public-ips"></a>Facoltativo: ridimensionare il numero di indirizzi IP pubblici gestiti

Quando si usa un servizio di bilanciamento del carico con SKU *standard* con indirizzi IP pubblici in uscita gestiti, che vengono creati per impostazione predefinita, è possibile ridimensionare il numero di indirizzi IP pubblici in uscita gestiti usando il parametro *Load-Balancer-Managed-IP-count* .

Per aggiornare un cluster esistente, eseguire il comando seguente. Questo parametro può essere impostato anche in fase di creazione del cluster per avere più indirizzi IP pubblici in uscita gestiti.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

Nell'esempio precedente il numero di indirizzi IP pubblici in uscita gestiti viene impostato su *2* per il cluster *myAKSCluster* in *myResourceGroup*. 

È anche possibile usare il parametro *Load-Balancer-Managed-IP-count* per impostare il numero iniziale di indirizzi IP pubblici in uscita gestiti durante la creazione del cluster mediante `--load-balancer-managed-outbound-ip-count` l'aggiunta del parametro e l'impostazione del valore desiderato. Il numero predefinito di indirizzi IP pubblici in uscita gestiti è 1.

## <a name="optional---provide-your-own-public-ips-or-prefixes-for-egress"></a>Facoltativo: specificare gli indirizzi IP pubblici o i prefissi per l'uscita

Quando si usa un servizio di bilanciamento del carico con SKU *standard* , il cluster AKS crea automaticamente un indirizzo IP pubblico nello stesso gruppo di risorse creato per il cluster AKS e assegna l'indirizzo IP pubblico al servizio di bilanciamento del carico dello SKU *standard* . In alternativa, è possibile assegnare un indirizzo IP pubblico personalizzato al momento della creazione del cluster oppure aggiornare le proprietà del servizio di bilanciamento del carico di un cluster esistente.

Con l'introduzione di più indirizzi IP o prefissi, è possibile definire più servizi di backup quando si definisce l'indirizzo IP dietro un singolo oggetto di bilanciamento del carico. L'endpoint in uscita di nodi specifici dipenderà dal servizio a cui sono associati.

> [!IMPORTANT]
> È necessario usare gli indirizzi IP pubblici dello SKU *standard* per l'uscita con lo SKU *standard* del servizio di bilanciamento del carico. È possibile verificare lo SKU degli indirizzi IP pubblici usando il comando [AZ Network Public-IP Show][az-network-public-ip-show] :
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Usare il comando [AZ Network Public-IP Show][az-network-public-ip-show] per elencare gli ID degli indirizzi IP pubblici.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Il comando precedente mostra l'ID per l'indirizzo IP pubblico di *myPublicIP* nel gruppo di risorse *myResourceGroup* .

Usare il comando *AZ AKS Update* con il parametro *Load-Balancer-Outbound-IPS* per aggiornare il cluster con gli indirizzi IP pubblici.

Nell'esempio seguente viene usato il parametro *Load-Balancer-Outbound-IPS* con gli ID del comando precedente.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

È anche possibile usare i prefissi IP pubblici per l'uscita con il servizio di bilanciamento del carico dello SKU *standard* . L'esempio seguente usa il comando [AZ Network Public-IP prefix Show][az-network-public-ip-prefix-show] per elencare gli ID dei prefissi IP pubblici:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Il comando precedente mostra l'ID del prefisso IP pubblico *myPublicIPPrefix* nel gruppo di risorse *myResourceGroup* .

Nell'esempio seguente viene usato il parametro *Load-Balancer-Outbound-IP-Prefixes* con gli ID del comando precedente.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> Gli IP pubblici e i prefissi IP devono trovarsi nella stessa area e nella stessa sottoscrizione del cluster AKS.

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Definire un indirizzo IP pubblico o prefissi in fase di creazione del cluster

Per supportare scenari come gli endpoint in uscita dell'elenco elementi consentiti, è possibile importare indirizzi IP o prefissi IP per l'uscita al momento della creazione del cluster. Aggiungere gli stessi parametri mostrati in precedenza al passaggio di creazione del cluster per definire gli IP pubblici e i prefissi IP all'inizio del ciclo di vita di un cluster.

Usare il comando *AZ AKS create* con il parametro *Load-Balancer-Outbound-IPS* per creare un nuovo cluster con gli indirizzi IP pubblici all'inizio.

```
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Usare il comando *AZ AKS create* con il parametro *Load-Balancer-Outbound-IP-Prefixes* per creare un nuovo cluster con i prefissi IP pubblici all'inizio.

```
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>Pulire la configurazione del servizio di bilanciamento del carico SKU standard

Per rimuovere l'applicazione di esempio e la configurazione del servizio di bilanciamento del carico, usare [kubectl Delete][kubectl-delete]:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui servizi Kubernetes, vedere la [documentazione relativa ai servizi di Kubernetes][kubernetes-services].

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
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update


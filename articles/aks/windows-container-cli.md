---
title: 'Anteprima: creare un contenitore di Windows Server in un cluster di Azure Kubernetes Service (AKS)'
description: Informazioni su come creare rapidamente un cluster Kubernetes, distribuire un'applicazione in un contenitore di Windows Server in Azure Kubernetes Service (AKS) usando l'interfaccia della riga di comando di Azure.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/17/2019
ms.author: mlearned
ms.openlocfilehash: ff4367194f06a8a6895c9c16252b01c3b94995d3
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241249"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Anteprima: creare un contenitore di Windows Server in un cluster Azure Kubernetes Service (AKS) usando l'interfaccia della riga di comando di Azure

Il servizio Azure Kubernetes è un servizio Kubernetes gestito che permette di distribuire e gestire rapidamente i cluster. In questo articolo si distribuisce un cluster AKS usando l'interfaccia della riga di comando di Azure. Si distribuisce anche un'applicazione di esempio ASP.NET in un contenitore di Windows Server nel cluster.

Questa funzionalità è attualmente in anteprima.

![Immagine di esplorazione dell'applicazione di esempio ASP.NET](media/windows-container/asp-net-sample-app.png)

Questo articolo presuppone una conoscenza di base dei concetti relativi a Kubernetes. Per altre informazioni, vedere [Concetti di base relativi a Kubernetes per il servizio Azure Kubernetes][kubernetes-concepts].

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.61 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="before-you-begin"></a>Prima di iniziare

È necessario aggiungere un pool di nodi aggiuntivo dopo aver creato il cluster in grado di eseguire i contenitori di Windows Server. L'aggiunta di un pool di nodi aggiuntivo viene analizzata in un passaggio successivo, ma è prima necessario abilitare alcune funzionalità di anteprima.

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono il consenso esplicito self-service. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto tecnico per il massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per ulteriori informazioni, vedere gli articoli di supporto seguenti:
>
> * [Criteri di supporto AKS][aks-support-policies]
> * [Domande frequenti relative al supporto tecnico Azure][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga comando di aks-preview

Per usare i contenitori di Windows Server, è necessaria l'estensione dell'interfaccia della riga di comando *AKS-Preview* 0.4.12 o versione successiva. Installare l'estensione dell'interfaccia della riga di comando di Azure *AKS-Preview* usando il comando [AZ Extension Add][az-extension-add] , quindi verificare la presenza di eventuali aggiornamenti disponibili usando il comando [AZ Extension Update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>Registrare la funzionalità di anteprima di Windows

Per creare un cluster AKS che può usare più pool di nodi ed eseguire i contenitori di Windows Server, abilitare prima di tutto i flag delle funzionalità *WindowsPreview* nella sottoscrizione. La funzionalità *WindowsPreview* usa anche cluster di pool multinodo e set di scalabilità di macchine virtuali per gestire la distribuzione e la configurazione dei nodi Kubernetes. Registrare il flag della funzionalità *WindowsPreview* usando il comando [AZ feature Register][az-feature-register] , come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Qualsiasi cluster AKS creato dopo aver registrato correttamente il flag della funzionalità *WindowsPreview* usa questa esperienza del cluster di anteprima. Per continuare a creare cluster regolari e completamente supportati, non abilitare le funzionalità di anteprima nelle sottoscrizioni di produzione. Usare una sottoscrizione di Azure di test o sviluppo separata per testare le funzionalità in anteprima.

Sono necessari alcuni minuti per il completamento della registrazione. Verificare lo stato della registrazione usando il comando [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Quando lo stato di registrazione è `Registered`, premere CTRL + C per arrestare il monitoraggio dello stato.  Aggiornare quindi la registrazione del provider di risorse *Microsoft. servizio contenitore* usando il comando [AZ provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Limitazioni

Quando si creano e si gestiscono cluster AKS che supportano più pool di nodi, si applicano le limitazioni seguenti:

* Sono disponibili più pool di nodi per i cluster creati dopo aver registrato correttamente il *WindowsPreview*. Se si registra la funzionalità *MultiAgentpoolPreview* per la sottoscrizione, sono disponibili anche più pool di nodi. Non è possibile aggiungere o gestire pool di nodi con un cluster AKS esistente creato prima che questa funzionalità sia stata registrata correttamente.
* Non è possibile eliminare il primo pool di nodi.

Quando questa funzionalità è in anteprima, si applicano le seguenti limitazioni aggiuntive:

* Il cluster AKS può avere un massimo di otto pool di nodi.
* Il cluster AKS può avere un massimo di 400 nodi tra questi otto pool di nodi.
* Il nome del pool di nodi di Windows Server ha un limite di 6 caratteri.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un gruppo logico in cui le risorse di Azure vengono distribuite e gestite. Quando si crea un gruppo di risorse, viene chiesto di specificare una posizione. Questo è il percorso in cui vengono archiviati i metadati del gruppo di risorse, ma anche quello in cui vengono eseguite le risorse in Azure se non si specifica un'altra area durante la creazione della risorsa. Creare un gruppo di risorse con il comando [az group create][az-group-create].

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *stati uniti orientali*.

> [!NOTE]
> Questo articolo usa la sintassi bash per i comandi in questa esercitazione.
> Se si usa Azure Cloud Shell, assicurarsi che l'elenco a discesa in alto a sinistra della finestra di Cloud Shell sia impostato su **bash**.

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

## <a name="create-an-aks-cluster"></a>Creare un cluster del servizio Azure Container

Per eseguire un cluster AKS che supporta i pool di nodi per i contenitori di Windows Server, il cluster deve usare un criterio di rete che usa il plug-in di rete [Azure CNI][azure-cni-about] (Advanced). Per informazioni più dettagliate che consentono di pianificare gli intervalli di subnet e le considerazioni sulla rete richiesti, vedere Configurare la rete [CNI di Azure][use-advanced-networking]. Usare il comando [AZ AKS create][az-aks-create] per creare un cluster AKS denominato *myAKSCluster*. Questo comando creerà le risorse di rete necessarie se non esistono.
  * Il cluster è configurato con due nodi
  * I parametri *Windows-admin-password* e *Windows-admin-username* impostano le credenziali di amministratore per tutti i contenitori di Windows Server creati nel cluster.

> [!NOTE]
> Per garantire il funzionamento affidabile del cluster, è consigliabile eseguire almeno 2 (due) nodi nel pool di nodi predefinito.

Fornire il proprio *PASSWORD_WIN* sicuro (tenere presente che i comandi in questo articolo sono stati immessi in una shell bash):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.6 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
```

> [!Note]
> Se viene ricevuto un errore di convalida della password, provare a creare il gruppo di risorse in un'altra area.
> Quindi provare a creare il cluster con il nuovo gruppo di risorse.

Il comando viene completato dopo pochi minuti e vengono restituite informazioni in formato JSON sul cluster. Occasionalmente, il provisioning del cluster può richiedere più di pochi minuti. In questi casi è consentito un massimo di 10 minuti. 

## <a name="add-a-windows-server-node-pool"></a>Aggiungere un pool di nodi di Windows Server

Per impostazione predefinita, un cluster AKS viene creato con un pool di nodi che può eseguire contenitori Linux. Usare il comando `az aks nodepool add` per aggiungere un pool di nodi aggiuntivo che può eseguire i contenitori di Windows Server.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.6
```

Il comando precedente crea un nuovo pool di nodi denominato *npwin* e lo aggiunge al *myAKSCluster*. Quando si crea un pool di nodi per l'esecuzione di contenitori di Windows Server, il valore predefinito per *node-VM-size* è *Standard_D2s_v3*. Se si sceglie di impostare il parametro *node-VM-size* , controllare l'elenco delle [dimensioni delle macchine virtuali limitate][restricted-vm-sizes]. La dimensione minima consigliata è *Standard_D2s_v3*. Il comando precedente usa anche la subnet predefinita nel VNET predefinito creato durante l'esecuzione di `az aks create`.

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

L'output di esempio seguente mostra tutti i nodi del cluster. Verificare che lo stato di tutti i nodi sia *pronto*:

```
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.6
aksnpwin987654                      Ready    agent   108s   v1.14.6
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Un file manifesto di Kubernetes definisce uno stato desiderato per il cluster, ad esempio le immagini del contenitore da eseguire. In questo articolo viene usato un manifesto per creare tutti gli oggetti necessari per eseguire l'applicazione di esempio ASP.NET in un contenitore di Windows Server. Questo manifesto include una [distribuzione Kubernetes][kubernetes-deployment] per l'applicazione di esempio ASP.NET e un [servizio Kubernetes][kubernetes-service] esterno per accedere all'applicazione da Internet.

L'applicazione di esempio ASP.NET viene fornita come parte dell' [.NET Framework esempi][dotnet-samples] ed è in esecuzione in un contenitore di Windows Server. AKS richiede che i contenitori di Windows Server siano basati su immagini di *Windows server 2019* o versione successiva. Il file manifesto Kubernetes deve anche definire un [selettore di nodo][node-selector] per indicare al cluster AKS di eseguire il Pod dell'applicazione di esempio ASP.NET in un nodo che può eseguire i contenitori di Windows Server.

Creare un file denominato `sample.yaml` e copiarlo nella definizione YAML seguente. Se si usa Azure Cloud Shell, questo file può essere creato usando `vi` o `nano` come se si usasse un sistema virtuale o fisico:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Distribuire l'applicazione usando il comando [kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```azurecli-interactive
kubectl apply -f sample.yaml
```

L'output di esempio seguente mostra la distribuzione e il servizio creati correttamente:

```
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Test dell'applicazione

Durante l'esecuzione dell'applicazione, un servizio Kubernetes espone il front-end dell'applicazione a Internet. Il processo potrebbe richiedere alcuni minuti. Occasionalmente, il provisioning del servizio può richiedere più di pochi minuti. In questi casi è consentito un massimo di 10 minuti.

Per monitorare lo stato, usare il comando [kubectl get service][kubectl-get] con l'argomento `--watch`.

```azurecli-interactive
kubectl get service sample --watch
```

Inizialmente l' *IP esterno* per il servizio di *esempio* viene visualizzato in *sospeso*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando *EXTERNAL-IP* passa da *pending* a un effettivo indirizzo IP pubblico, usare `CTRL-C` per arrestare il processo di controllo `kubectl`. L'output di esempio seguente mostra un indirizzo IP pubblico valido assegnato al servizio:

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Per visualizzare l'app di esempio in azione, aprire un Web browser per l'indirizzo IP esterno del servizio.

![Immagine di esplorazione dell'applicazione di esempio ASP.NET](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>Eliminare il cluster

Quando il cluster non è più necessario, usare il comando [az group delete][az-group-delete] per rimuovere il gruppo di risorse, il servizio contenitore e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando si elimina il cluster, l'entità servizio di Azure Active Directory utilizzata dal cluster servizio Azure Kubernetes non viene rimossa. Per istruzioni su come rimuovere l'entità servizio, vedere le [considerazioni sull'entità servizio servizio Azure Kubernetes e la sua eliminazione][sp-delete].

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato distribuito un cluster Kubernetes e ne è stata distribuita un'applicazione di esempio ASP.NET in un contenitore di Windows Server. [Accedere al dashboard Web di Kubernetes][kubernetes-dashboard] per il cluster appena creato.

Per altre informazioni sul servizio Azure Container e l'analisi del codice completo per un esempio di distribuzione, passare all'esercitazione sul cluster Kubernetes.

> [!div class="nextstepaction"]
> [Esercitazione sul servizio Azure Container][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-advanced-networking.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

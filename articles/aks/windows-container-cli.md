---
title: Eseguire il contenitore di Windows Server nel cluster del servizio Azure KubernetesRun Windows Server container in Azure Kubernetes Service cluster
description: Informazioni su come creare rapidamente un cluster Kubernetes, distribuire un'applicazione in un contenitore di Windows Server nel servizio Kubernetes di Azure usando l'interfaccia della riga di comando di Azure.Learn how to quickly create a Kubernetes cluster, deploy an application in a Windows Server container in Azure Kubernetes Service (AKS) using the Azure CLI.
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 2aecebcc45cb24c9ab3a594aa4d74b1584c7ffa7
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392668"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Anteprima- Creare un contenitore di Windows Server in un cluster del servizio Azure Kubernetes (AKS) usando l'interfaccia della riga di comando di AzurePreview - Create a Windows Server container on an Azure Kubernetes Service (AKS) cluster using the Azure CLI

Il servizio Azure Kubernetes è un servizio Kubernetes gestito che permette di distribuire e gestire rapidamente i cluster. In this article, you deploy an AKS cluster using the Azure CLI. Distribuire anche un'applicazione di esempio ASP.NET in un contenitore di Windows Server al cluster.

Questa funzionalità è attualmente in anteprima.

![Immagine della navigazione nellASP.NETapplicazione di esempio](media/windows-container/asp-net-sample-app.png)

Questo articolo presuppone una conoscenza di base dei concetti di Kubernetes. Per altre informazioni, vedere [Concetti di base relativi a Kubernetes per il servizio Azure Kubernetes][kubernetes-concepts].

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo richiede l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.0.61 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="before-you-begin"></a>Prima di iniziare

È necessario aggiungere un pool di nodi aggiuntivo dopo aver creato il cluster in grado di eseguire contenitori di Windows Server.You must add an additional node pool after you create your cluster that can run Windows Server containers. L'aggiunta di un pool di nodi aggiuntivo viene descritta in un passaggio successivo, ma è innanzitutto necessario abilitare alcune funzionalità di anteprima.

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono l'opt-in self-service. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto clienti in base al massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per ulteriori informazioni, vedere i seguenti articoli di supporto:
>
> * [Criteri di supporto AKS][aks-support-policies]
> * [Domande frequenti relative al supporto tecnico Azure][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga comando di aks-preview

Per usare i contenitori di Windows Server, è necessaria *l'estensione aks-preview* CLI versione 0.4.12 o successiva. Installare l'estensione aks-preview di Azure CLI usando il comando [az extension add,][az-extension-add] quindi verificare la presenza di eventuali aggiornamenti disponibili usando il comando az extension update:Install the *aks-preview* Azure CLI extension using the az extension add command, then check for any available updates using the [az extension update][az-extension-update] command::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>Registrare la funzionalità di anteprima di Windows

Per creare un cluster AKS che può usare più pool di nodi ed eseguire contenitori di Windows Server, abilitare innanzitutto i flag di funzionalità *WindowsPreview* nella sottoscrizione. La funzionalità *WindowsPreview* usa anche cluster di pool a più nodi e set di scalabilità di macchine virtuali per gestire la distribuzione e la configurazione dei nodi Kubernetes. Registrare il flag di funzionalità *WindowsPreview* utilizzando il comando [az feature register][az-feature-register] come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Qualsiasi cluster AKS creato dopo aver registrato correttamente il flag di funzionalità *WindowsPreview* usa questa esperienza di anteprima del cluster. Per continuare a creare cluster regolari e completamente supportati, non abilitare le funzionalità di anteprima nelle sottoscrizioni di produzione. Usare una sottoscrizione di Azure di test o sviluppo separata per testare le funzionalità di anteprima.

Il completamento della registrazione richiede alcuni minuti. Controllare lo stato della registrazione utilizzando il comando [elenco funzionalità az:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Quando lo stato `Registered`di registrazione è , premere Ctrl-C per interrompere il monitoraggio dello stato.  Aggiornare quindi la registrazione del provider di risorse *Microsoft.ContainerService* utilizzando il comando [az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Limitazioni

Quando si creano e si gestiscono cluster AKS che supportano pool di più nodi, si applicano le limitazioni seguenti:The following limitations apply when you create and manage AKS clusters that support multiple node pools:

* Non è possibile eliminare il primo pool di nodi.

Mentre questa funzione è in anteprima, si applicano le seguenti limitazioni aggiuntive:

* Il cluster AKS può avere un massimo di otto pool di nodi.
* Il cluster AKS può avere un massimo di 400 nodi in questi otto pool di nodi.
* Il nome del pool di nodi di Windows Server ha un limite di 6 caratteri.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un gruppo logico in cui le risorse di Azure vengono distribuite e gestite. Quando si crea un gruppo di risorse, viene chiesto di specificare una posizione. Questo è il percorso in cui vengono archiviati i metadati del gruppo di risorse, ma anche quello in cui vengono eseguite le risorse in Azure se non si specifica un'altra area durante la creazione della risorsa. Creare un gruppo di risorse con il comando [az group create][az-group-create].

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nel percorso *eastus.*

> [!NOTE]
> Questo articolo usa la sintassi Bash per i comandi in questa esercitazione.
> Se si usa Azure Cloud Shell, verificare che l'elenco a discesa nell'angolo superiore sinistro della finestra di Cloud Shell sia impostato su **Bash**.

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

## <a name="create-an-aks-cluster"></a>Creare un cluster AKS

Per eseguire un cluster AKS che supporta i pool di nodi per i contenitori di Windows Server, il cluster deve usare un criterio di rete che usa il [plug-in][azure-cni-about] di rete CNI (avanzato) di Azure.In order to run an AKS cluster that supports node pools for Windows Server containers, your cluster needs to use a network policy that uses Azure CNI (advanced) network plugin. Per informazioni più dettagliate su come pianificare gli intervalli di subnet necessari e le considerazioni sulla rete, vedere configurare la rete CNI di Azure.For more detailed information to help plan out the required subnet ranges and network considerations, see [configure Azure CNI networking][use-advanced-networking]. Utilizzare il comando [az aks create][az-aks-create] per creare un cluster AKS denominato *myAKSCluster*. Questo comando creerà le risorse di rete necessarie se non esistono.
  * Il cluster è configurato con due nodi
  * I parametri *windows-admin-password* e *windows-admin-username* impostano le credenziali di amministratore per tutti i contenitori di Windows Server creati nel cluster.

> [!NOTE]
> Per garantire che il cluster funzioni in modo affidabile, è consigliabile eseguire almeno 2 (due) nodi nel pool di nodi predefinito.

Fornire il proprio *PASSWORD_WIN* sicuro (ricordare che i comandi in questo articolo vengono inseriti in una shell BASH):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --kubernetes-version 1.15.7 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --network-plugin azure
```

> [!Note]
> Se viene visualizzato un errore di convalida della password, provare a creare il gruppo di risorse in un'altra area.
> Provare quindi a creare il cluster con il nuovo gruppo di risorse.

> [!Note]
> Se non è possibile creare il cluster AKS perché la versione non è supportata in questa area, è possibile utilizzare il comando [az aks get-versions --location eastus] per trovare l'elenco delle versioni supportate per questa area.


Il comando viene completato dopo pochi minuti e vengono restituite informazioni in formato JSON sul cluster. Occasionalmente il cluster può richiedere più di pochi minuti per il provisioning. In questi casi attendere fino a 10 minuti. 

## <a name="add-a-windows-server-node-pool"></a>Aggiungere un pool di nodi di Windows ServerAdd a Windows Server node pool

Per impostazione predefinita, un cluster AKS viene creato con un pool di nodi in grado di eseguire contenitori Linux.By default, an AKS cluster is created with a node pool that can run Linux containers. Utilizzare `az aks nodepool add` il comando per aggiungere un pool di nodi aggiuntivo in grado di eseguire contenitori di Windows Server.Use command to add an additional node pool that can run Windows Server containers.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.15.7
```

Il comando precedente crea un nuovo pool di nodi denominato *npwin* e lo aggiunge a *myAKSCluster*. Quando si crea un pool di nodi per l'esecuzione di contenitori di Windows Server, il valore predefinito per *node-vm-size* è *Standard_D2s_v3*. Se si sceglie di impostare il parametro *node-vm-size,* controllare l'elenco delle dimensioni limitate delle [macchine virtuali.][restricted-vm-sizes] La dimensione minima consigliata è *Standard_D2s_v3*. Il comando precedente utilizza anche la subnet predefinita `az aks create`nella rete virtuale predefinita creata durante l'esecuzione di .

## <a name="connect-to-the-cluster"></a>Stabilire la connessione al cluster

Per gestire un cluster Kubernetes, usare [kubectl][kubectl], il client da riga di comando di Kubernetes. Se si usa Azure Cloud Shell, `kubectl` è già installato. Per installare `kubectl` in locale, usare il comando [az aks install-cli][az-aks-install-cli]:

```azurecli
az aks install-cli
```

Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az servizio Azure Kubernetes get-credentials][az-aks-get-credentials]. Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarli.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Per verificare la connessione al cluster, usare il comando [kubectl get][kubectl-get] per restituire un elenco dei nodi del cluster.

```console
kubectl get nodes
```

L'output di esempio seguente mostra tutti i nodi del cluster. Assicurarsi che lo stato di tutti i nodi sia *Pronto:*

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.15.7
aksnpwin987654                      Ready    agent   108s   v1.15.7
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Un file manifesto di Kubernetes definisce uno stato desiderato per il cluster, ad esempio le immagini del contenitore da eseguire. In questo articolo viene usato un manifesto per creare tutti gli oggetti necessari per eseguire l'applicazione di esempio ASP.NET in un contenitore di Windows Server.In this article, a manifest is used to create all objects needed to run the ASP.NET sample application in a Windows Server container. Questo manifesto include una [distribuzione Kubernetes][kubernetes-deployment] per l'applicazione di esempio ASP.NET e un servizio Kubernetes esterno per accedere all'applicazione da Internet.This manifest includes a Kubernetes deployment for the ASP.NET sample application and an external [Kubernetes service][kubernetes-service] to access the application from the internet.

L'applicazione di esempio ASP.NET viene fornita come parte di Esempi di [.NET Framework][dotnet-samples] e viene eseguita in un contenitore di Windows Server. AKS richiede che i contenitori di Windows Server siano basati su immagini di *Windows Server 2019* o versione successiva. Il file manifesto Kubernetes deve inoltre definire un selettore di nodo per indicare al cluster AKS di eseguire il pod dell'applicazione di esempio ASP.NET in un nodo in grado di eseguire contenitori di Windows Server.The Kubernetes manifest file must also define a [node selector][node-selector] to tell your AKS cluster to run your ASP.NET sample application's pod on a node that can run Windows Server containers.

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

```console
kubectl apply -f sample.yaml
```

L'output di esempio seguente mostra la distribuzione e il servizio creati correttamente:The following example output shows the Deployment and Service created successfully:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Test dell'applicazione

Durante l'esecuzione dell'applicazione, un servizio Kubernetes espone il front-end dell'applicazione a Internet. Il processo potrebbe richiedere alcuni minuti. Occasionalmente il servizio può richiedere più di pochi minuti per il provisioning. In questi casi attendere fino a 10 minuti.

Per monitorare lo stato, usare il comando [kubectl get service][kubectl-get] con l'argomento `--watch`.

```console
kubectl get service sample --watch
```

Inizialmente *l'EXTERNAL-IP* per il servizio *di esempio* viene visualizzato come *in sospeso*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando *EXTERNAL-IP* passa da *pending* a un effettivo indirizzo IP pubblico, usare `CTRL-C` per arrestare il processo di controllo `kubectl`. L'output di esempio seguente mostra un indirizzo IP pubblico valido assegnato al servizio:

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Per visualizzare l'app di esempio in azione, aprire un Web browser all'indirizzo IP esterno del servizio.

![Immagine della navigazione nellASP.NETapplicazione di esempio](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Se si riceve un timeout di connessione quando si tenta di caricare la pagina, è necessario verificare che l'app di esempio sia pronta con il seguente comando [kubectl get pods --watch]. A volte il contenitore di finestre non viene avviato nel momento in cui l'indirizzo IP esterno è disponibile.

## <a name="delete-cluster"></a>Eliminare il cluster

Quando il cluster non è più necessario, usare il comando [az group delete][az-group-delete] per rimuovere il gruppo di risorse, il servizio contenitore e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando si elimina il cluster, l'entità servizio di Azure Active Directory utilizzata dal cluster servizio Azure Kubernetes non viene rimossa. Per istruzioni su come rimuovere l'entità servizio, vedere le [considerazioni sull'entità servizio servizio Azure Kubernetes e la sua eliminazione][sp-delete]. Se è stata utilizzata un'identità gestita, l'identità viene gestita dalla piattaforma e non richiede la rimozione.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato distribuito un cluster Kubernetes e vi è stata distribuita un'applicazione di esempio di ASP.NET in un contenitore di Windows Server.In this article, you deployed a Kubernetes cluster and deployed an ASP.NET sample application in a Windows Server container to it. [Accesso al dashboard Web di Kubernetes][kubernetes-dashboard] per il cluster appena creato.

Per altre informazioni sul servizio Azure Container e l'analisi del codice completo per un esempio di distribuzione, passare all'esercitazione sul cluster Kubernetes.

> [!div class="nextstepaction"]
> [Esercitazione su AKS][aks-tutorial]

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

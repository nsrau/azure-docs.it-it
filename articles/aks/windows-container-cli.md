---
title: Anteprima - creare un contenitore di Windows Server in un cluster Azure Kubernetes Service (AKS)
description: Informazioni su come creare un cluster Kubernetes rapidamente, distribuire un'applicazione in un contenitore di Windows Server in Azure Kubernetes Service (AKS) tramite la CLI di Azure.
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 06/06/2019
ms.author: twhitney
ms.openlocfilehash: cdcc1b985c570d1af4bbb33ac29a37e63b1dfa90
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752383"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Anteprima - creare un contenitore di Windows Server in un cluster Azure Kubernetes Service (AKS) tramite la CLI di Azure

Il servizio Azure Kubernetes è un servizio Kubernetes gestito che permette di distribuire e gestire rapidamente i cluster. In questo articolo, si distribuisce un cluster AKS usando il comando di Azure. È anche possibile distribuire un'applicazione di esempio ASP.NET in un contenitore di Windows Server al cluster.

Questa funzionalità è attualmente in anteprima.

![Immagine del passaggio all'applicazione di esempio ASP.NET](media/windows-container/asp-net-sample-app.png)

Questo articolo presuppone una conoscenza di base dei concetti relativi a Kubernetes. Per altre informazioni, vedere [Concetti di base relativi a Kubernetes per il servizio Azure Kubernetes][kubernetes-concepts].

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo è necessario eseguire il comando di Azure versione 2.0.61 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="before-you-begin"></a>Prima di iniziare

Dopo aver creato il cluster che può eseguire contenitori Windows Server, è necessario aggiungere un pool di nodi aggiuntivi. Aggiunta di un pool di nodi aggiuntivi viene trattato in un passaggio successivo, ma è innanzitutto necessario abilitare alcune funzionalità di anteprima.

> [!IMPORTANT]
> Funzionalità di anteprima del servizio contenitore di AZURE sono self-service, fornire il consenso esplicito. Vengono fornite per raccogliere commenti e suggerimenti e bug dalla community. In fase di anteprima, queste funzionalità non sono destinate all'uso di produzione. Le funzionalità in anteprima pubblica rientrano nel supporto "best effort". Assistenza dai team di supporto tecnico di AKS è disponibile durante le ore lavorative Pacifico (PST) solo timezone. Per altre informazioni, vedere i seguenti articoli di supporto:
>
> * [Criteri di supporto servizio contenitore di AZURE][aks-support-policies]
> * [Domande frequenti sul supporto di Azure][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga comando di aks-preview
    
I comandi dell'interfaccia della riga per creare e gestire più pool di nodi sono disponibili nel *aks-preview* estensione dell'interfaccia della riga. Installare il *aks-preview* estensione di comando di Azure usando la [Aggiungi estensione az] [ az-extension-add] comando, come illustrato nell'esempio seguente:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Se in precedenza sono stati installati il *aks-preview* estensione, installare le aggiorna usando il `az extension update --name aks-preview` comando.

### <a name="register-windows-preview-feature"></a>Registra funzionalità di anteprima di Windows

Per creare un cluster AKS che può usare più pool di nodi ed eseguire contenitori Windows Server, abilitare prima la *WindowsPreview* flag nella sottoscrizione di funzionalità. Il *WindowsPreview* funzionalità Usa anche i cluster a più nodi del pool e per gestire la distribuzione e la configurazione dei nodi Kubernetes set di scalabilità di macchine virtuali. Registrare il *WindowsPreview* flag funzionalità tramite il [register funzionalità az] [ az-feature-register] seguente come mostrato nell'esempio seguente:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Tutti i cluster servizio contenitore di AZURE create dopo aver correttamente registrato il *WindowsPreview* flag funzionalità usano questa esperienza di cluster di anteprima. Per continuare a creare i cluster normali, completamente supportato, non abilitare la funzionalità di anteprima nella sottoscrizione di produzione. Usare un test separate o sviluppo sottoscrizione di Azure per verificare le funzionalità di anteprima.

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllare lo stato di registrazione usando il comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft.ContainerService* usando il comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Limitazioni

Le limitazioni seguenti si applicano quando si creano e gestire i cluster servizio contenitore di AZURE che supportano più pool di nodi:

* Più pool di nodi sono disponibili per i cluster creati dopo aver correttamente registrato il *WindowsPreview*. Più pool di nodi disponibili anche se si registra il *MultiAgentpoolPreview* e *VMSSPreview* funzionalità per la sottoscrizione. Non è possibile aggiungere o gestire i pool di nodi con un cluster servizio contenitore di AZURE esistente creato prima che queste funzionalità sono state registrate correttamente.
* È possibile eliminare il primo pool di nodi.

Quando questa funzionalità è disponibile in anteprima, si applicano le limitazioni aggiuntive seguenti:

* Il cluster AKS può avere un massimo di otto pool di nodi.
* Il cluster AKS può avere un massimo di 400 nodi tra tali pool di otto nodi.
* Il nome del pool di nodi Windows Server ha un limite di 6 caratteri.

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
Per eseguire un cluster servizio contenitore di AZURE che supportano pool di nodi per i contenitori di Windows Server, il cluster deve utilizzare un criterio di rete che utilizza [Azure CNI] [ azure-cni-about] plug-in rete (avanzate). Per altre informazioni per pianificare gli intervalli di subnet necessarie e considerazioni sulla rete, vedere [configurare la rete di Azure CNI][use-advanced-networking]. Usare la [az aks create] [ az-aks-create] comando per creare un cluster del servizio contenitore di AZURE denominato *myAKSCluster*. Questo comando crea le risorse di rete necessarie se non sono presenti.
  * Il cluster viene configurato con un solo nodo
  * Il *windows-admin-password* e *nome utente amministratore di windows* parametri impostare le credenziali di amministratore per tutti i contenitori Windows Server creati nel cluster.

Fornire il proprio sicure *PASSWORD_WIN*.

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.0 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
```

Il comando viene completato dopo pochi minuti e vengono restituite informazioni in formato JSON sul cluster.

## <a name="add-a-windows-server-node-pool"></a>Aggiungere un pool di nodi di Windows Server

Per impostazione predefinita, viene creato un cluster AKS con un pool di nodi che può eseguire contenitori Linux. Usare `az aks nodepool add` comando per aggiungere un pool di nodi aggiuntivi che è possibile eseguire contenitori Windows Server.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.0
```

Il comando precedente crea un nuovo pool di nodi denominato *npwin* e lo aggiunge alle *myAKSCluster*. Quando si crea un pool di nodi per eseguire contenitori Windows Server, il valore predefinito per *nodo-vm-size* viene *Standard_D2s_v3*. Se si sceglie di impostare il *nodo-vm-size* parametro,. controllare l'elenco dei [limitato dimensioni delle macchine Virtuali][restricted-vm-sizes]. È il valore minimo consigliato *Standard_D2s_v3*. Il comando precedente Usa anche la subnet predefinita nella rete virtuale predefinita creata durante l'esecuzione `az aks create`.

## <a name="connect-to-the-cluster"></a>Connettersi al cluster

Per gestire un cluster Kubernetes, usare [kubectl][kubectl], il client da riga di comando di Kubernetes. Se si usa Azure Cloud Shell, `kubectl` è già installato. Per installare `kubectl` in locale, usare il comando [az aks install-cli][az-aks-install-cli]:

```azurecli
az aks install-cli
```

Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az servizio Azure Kubernetes get-credentials][az-aks-get-credentials]. Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarli.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Per verificare la connessione al cluster, usare il comando [kubectl get][kubectl-get] per restituire un elenco dei nodi del cluster.

```azurecli-interactive
kubectl get nodes
```

L'esempio di output seguente mostra il nodo singolo creato nei passaggi precedenti. Assicurarsi che lo stato del nodo sia impostato su *Pronto*:

```
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.0
aksnpwin987654                      Ready    agent   108s   v1.14.0
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Un file manifesto di Kubernetes definisce uno stato desiderato per il cluster, ad esempio le immagini del contenitore da eseguire. In questo articolo viene usato un manifesto per creare tutti gli oggetti necessari per eseguire l'applicazione di esempio ASP.NET in un contenitore di Windows Server. Questo manifesto include un' [distribuzione di Kubernetes] [ kubernetes-deployment] per l'applicazione di esempio ASP.NET e un riferimento esterno [servizio Kubernetes] [ kubernetes-service] a accedere all'applicazione da internet.

L'applicazione di esempio ASP.NET viene fornito come parte di [esempi di .NET Framework] [ dotnet-samples] e viene eseguito in un contenitore di Windows Server. Servizio contenitore di AZURE richiede sia basata su immagini di contenitori di Windows Server *Windows Server 2019* o versione successiva. Il Kubernetes file manifesto deve anche definire un [selettore nodo] [ node-selector] per indicare a del cluster servizio contenitore di AZURE per l'esecuzione di pod dell'applicazione di esempio ASP.NET su un nodo che può eseguire contenitori Windows Server.

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
            memory: 800m
          requests:
            cpu: .1
            memory: 300m
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

L'output di esempio seguente mostra la distribuzione e il servizio è stato creato:

```
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Test dell'applicazione

Durante l'esecuzione dell'applicazione, un servizio Kubernetes espone il front-end dell'applicazione a Internet. Il processo potrebbe richiedere alcuni minuti.

Per monitorare lo stato, usare il comando [kubectl get service][kubectl-get] con l'argomento `--watch`.

```azurecli-interactive
kubectl get service sample --watch
```

Inizialmente il *EXTERNAL-IP* per il *campione* servizio viene visualizzato come *in sospeso*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando *EXTERNAL-IP* passa da *pending* a un effettivo indirizzo IP pubblico, usare `CTRL-C` per arrestare il processo di controllo `kubectl`. L'output di esempio seguente mostra un indirizzo IP pubblico valido assegnato al servizio:

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Per visualizzare l'app di esempio in azione, aprire un web browser all'indirizzo IP esterno del servizio.

![Immagine del passaggio all'applicazione di esempio ASP.NET](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>Eliminare il cluster

Quando il cluster non è più necessario, usare il comando [az group delete][az-group-delete] per rimuovere il gruppo di risorse, il servizio contenitore e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando si elimina il cluster, l'entità servizio di Azure Active Directory utilizzata dal cluster servizio Azure Kubernetes non viene rimossa. Per istruzioni su come rimuovere l'entità servizio, vedere le [considerazioni sull'entità servizio servizio Azure Kubernetes e la sua eliminazione][sp-delete].

## <a name="next-steps"></a>Passaggi successivi

In questo articolo, viene distribuito un cluster Kubernetes e distribuita un'applicazione di esempio ASP.NET in un contenitore di Windows Server ad esso. [Accesso al dashboard Web di Kubernetes][kubernetes-dashboard] per il cluster appena creato.

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

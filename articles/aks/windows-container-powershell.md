---
title: Creare un contenitore di Windows Server in un cluster Azure Kubernetes Service (AKS)
description: Informazioni su come creare rapidamente un cluster Kubernetes, distribuire un'applicazione in un contenitore di Windows Server nel servizio Azure Kubernetes tramite PowerShell.
services: container-service
ms.topic: article
ms.date: 05/26/2020
ms.openlocfilehash: 036c97d406e37e038474287daf39182ddce194a1
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86250873"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-powershell"></a>Creare un contenitore di Windows Server in un cluster del servizio Azure Kubernetes tramite PowerShell

Il servizio Azure Kubernetes è un servizio Kubernetes gestito che permette di distribuire e gestire rapidamente i cluster. In questo articolo viene distribuito un cluster del servizio Azure Kubernetes tramite PowerShell. Viene distribuita anche un'applicazione di esempio `ASP.NET` in un contenitore di Windows Server nel cluster.

![Immagine del passaggio all'applicazione ASP.NET di esempio](media/windows-container-powershell/asp-net-sample-app.png)

Questo articolo presuppone una conoscenza di base dei concetti relativi a Kubernetes. Per altre informazioni, vedere [Concetti di base relativi a Kubernetes per il servizio Azure Kubernetes][kubernetes-concepts].

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Se si sceglie di usare PowerShell in locale, per questo articolo è necessario installare il modulo Az PowerShell e connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount). Per altre informazioni sull'installazione del modulo Az PowerShell, vedere [Installare Azure PowerShell][install-azure-powershell].

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se si possiedono più sottoscrizioni di Azure, scegliere quella appropriata in cui verranno fatturate le risorse. Selezionare un ID sottoscrizione specifico usando il cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="limitations"></a>Limitazioni

Quando si creano e si gestiscono cluster del servizio Azure Kubernetes che supportano pool di nodi multipli, si applicano le limitazioni seguenti:

* Non è possibile eliminare il primo pool di nodi.

Ai pool di nodi di Windows Server si applicano le limitazioni aggiuntive seguenti:

* Il cluster del servizio Azure Kubernetes può avere un massimo di 10 pool di nodi.
* Il cluster del servizio Azure Kubernetes può avere un massimo di 100 nodi in ogni pool di nodi.
* Il nome del pool di nodi di Windows Server ha un limite di 6 caratteri.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un [gruppo di risorse](../azure-resource-manager/management/overview.md) di Azure è un gruppo logico in cui le risorse di Azure vengono distribuite e gestite. Quando si crea un gruppo di risorse, viene chiesto di specificare una posizione. Questo è il percorso in cui vengono archiviati i metadati del gruppo di risorse, ma anche quello in cui vengono eseguite le risorse in Azure se non si specifica un'altra area durante la creazione della risorsa. Creare un gruppo di risorse con il cmdlet [New-AzResourceGroup][new-azresourcegroup].

L'esempio seguente crea un gruppo di risorse denominato **myResourceGroup** nella località **stati uniti orientali**.

> [!NOTE]
> Questo articolo usa la sintassi di PowerShell per i comandi in questa esercitazione. Se si usa Azure Cloud Shell, assicurarsi che l'elenco a discesa in alto a sinistra della finestra di Cloud Shell sia impostato su **PowerShell**.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

L'output di esempio seguente mostra il gruppo di risorse creato correttamente:

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-an-aks-cluster"></a>Creare un cluster AKS

Usare l'utilità della riga di comando `ssh-keygen` per generare una coppia di chiavi SSH. Per altre informazioni, vedere i [passaggi rapidi: Creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

Per eseguire un cluster del servizio Azure Kubernetes che supporti i pool di nodi per i contenitori di Windows Server, il cluster deve usare un criterio di rete che usi il plug-in di rete [Azure CNI][azure-cni-about] (avanzato). Per informazioni più dettagliate sulla pianificazione degli intervalli di subnet richiesti e sulle considerazioni sulla rete, vedere [Configurare le funzionalità di rete dell'interfaccia di rete dei contenitori di Azure][use-advanced-networking]. Usare il cmdlet [New-AzAks][new-azaks] seguente per creare un cluster del servizio Azure Kubernetes denominato **myAKSCluster**. Nell'esempio seguente vengono create le risorse di rete necessarie, se non esistenti.

> [!NOTE]
> Per garantire il funzionamento affidabile del cluster, è consigliabile eseguire almeno 2 (due) nodi nel pool di nodi predefinito.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzAKS -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 2 -KubernetesVersion 1.16.7 -NetworkPlugin azure -NodeVmSetType VirtualMachineScaleSets -WindowsProfileAdminUserName akswinuser -WindowsProfileAdminUserPassword $Password
```

> [!Note]
> Se non si riesce a creare il cluster del servizio Azure Kubernetes perché la versione non è supportata in questa area, è possibile usare il comando `Get-AzAksVersion -Location eastus` per trovare l'elenco delle versioni supportate per questa area.

Il comando viene completato dopo pochi minuti e vengono restituite informazioni sul cluster. Occasionalmente, il provisioning del cluster può richiedere più tempo. In questi casi, attendere al massimo 10 minuti.

## <a name="add-a-windows-server-node-pool"></a>Aggiungere un pool di nodi di Windows Server

Per impostazione predefinita, un cluster del servizio Azure Kubernetes viene creato con un pool di nodi in grado di eseguire contenitori Linux. Usare il cmdlet `New-AzAksNodePool` per aggiungere un pool di nodi in grado di eseguire contenitori di Windows Server insieme al pool di nodi Linux.

```azurepowershell-interactive
New-AzAksNodePool -ResourceGroupName myResourceGroup -ClusterName myAKSCluster -OsType Windows -Name npwin -KubernetesVersion 1.16.7
```

Il comando precedente crea un nuovo pool di nodi denominato **npwin** e lo aggiunge al **myAKSCluster**. Quando si crea un pool di nodi per l'esecuzione di contenitori di Windows Server, il valore predefinito per **VmSize** è **Standard_D2s_v3**. Se si sceglie di impostare il parametro **VmSize**, controllare l'elenco di [dimensioni delle macchine virtuali limitate][restricted-vm-sizes]. Le dimensioni minime consigliate sono **Standard_D2s_v3**. Il comando precedente usa anche la subnet predefinita nella rete virtuale predefinita creata durante l'esecuzione di `New-AzAks`.

## <a name="connect-to-the-cluster"></a>Stabilire la connessione al cluster

Per gestire un cluster Kubernetes, usare [kubectl][kubectl], il client da riga di comando di Kubernetes. Se si usa Azure Cloud Shell, `kubectl` è già installato. Per installare `kubectl` in locale, usare il cmdlet `Install-AzAksKubectl`:

```azurepowershell-interactive
Install-AzAksKubectl
```

Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il cmdlet [Import-AzAksCredential][import-azakscredential]. Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarli.

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

Per verificare la connessione al cluster, usare il comando [kubectl get][kubectl-get] per restituire un elenco di nodi del cluster.

```azurepowershell-interactive
kubectl get nodes
```

L'output di esempio seguente mostra tutti i nodi presenti nel cluster. Assicurarsi che lo stato di tutti i nodi sia impostato su **Pronto**:

```plaintext
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.7
aksnpwin987654                      Ready    agent   108s   v1.16.7
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Un file manifesto di Kubernetes definisce uno stato desiderato per il cluster, ad esempio le immagini del contenitore da eseguire. In questo articolo viene usato un manifesto per creare tutti gli oggetti necessari per eseguire l'applicazione di esempio ASP.NET in un contenitore di Windows Server. Questo manifesto include una [distribuzione di Kubernetes][kubernetes-deployment] per l'applicazione di esempio ASP.NET e un [servizio Kubernetes][kubernetes-service] esterno per accedere all'applicazione da Internet.

L'applicazione di esempio ASP.NET viene fornita nel quadro degli [esempi di .NET Framework][dotnet-samples] ed eseguita in un contenitore di Windows Server. Il servizio Azure Kubernetes prevede che i contenitori di Windows Server siano basati su immagini di **Windows Server 2019** o versione successiva. Il file manifesto di Kubernetes deve anche definire un [selettore di nodo][node-selector] per indicare al cluster del servizio Azure Kubernetes di eseguire il pod dell'applicazione di esempio ASP.NET in un nodo che sia in grado di eseguire contenitori di Windows Server.

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

```azurepowershell-interactive
kubectl apply -f sample.yaml
```

L'output di esempio seguente mostra la distribuzione e il servizio creati correttamente:

```plaintext
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Test dell'applicazione

Durante l'esecuzione dell'applicazione, un servizio Kubernetes espone il front-end dell'applicazione a Internet.
Il processo potrebbe richiedere alcuni minuti. Occasionalmente, il provisioning del servizio può richiedere più tempo. In questi casi, attendere al massimo 10 minuti.

Per monitorare lo stato, usare il comando [kubectl get service][kubectl-get] con l'argomento `--watch`.

```azurepowershell-interactive
kubectl get service sample --watch
```

**EXTERNAL-IP** per il servizio **di esempio** viene inizialmente visualizzato come **pending**.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando **EXTERNAL-IP** passa da **pending** a un effettivo indirizzo IP pubblico, usare `CTRL-C` per arrestare il processo di controllo `kubectl`. L'output di esempio seguente mostra un indirizzo IP pubblico valido assegnato al servizio:

```plaintext
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Per vedere in azione l'app di esempio, aprire un Web browser all'indirizzo IP esterno del servizio.

![Immagine del passaggio all'applicazione ASP.NET di esempio](media/windows-container-powershell/asp-net-sample-app.png)

> [!Note]
> Se si riceve un timeout di connessione quando si tenta di caricare la pagina, è necessario verificare che l'app di esempio sia pronta con il comando seguente `kubectl get pods --watch`. In alcuni casi, il contenitore di Windows non verrà avviato al momento in cui è disponibile l'indirizzo IP esterno.

## <a name="delete-cluster"></a>Eliminare il cluster

Quando il cluster non è più necessario, usare il cmdlet [Remove-AzResourceGroup][remove-azresourcegroup] per rimuovere il gruppo di risorse, il servizio contenitore e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> Quando si elimina il cluster, l'entità servizio di Azure Active Directory utilizzata dal cluster servizio Azure Kubernetes non viene rimossa. Per istruzioni su come rimuovere l'entità servizio, vedere le [considerazioni sull'entità servizio servizio Azure Kubernetes e la sua eliminazione][sp-delete]. Se è stata usata un'identità gestita, l'identità viene gestita dalla piattaforma e non richiede la rimozione.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato distribuito un cluster Kubernetes ed è stata distribuita un'applicazione `ASP.NET` di esempio in un contenitore di Windows Server. [Accedere al dashboard Web di Kubernetes][kubernetes-dashboard] per il cluster appena creato.

Per altre informazioni sul servizio Azure Container e l'analisi del codice completo per un esempio di distribuzione, passare all'esercitazione sul cluster Kubernetes.

> [!div class="nextstepaction"]
> [Esercitazione sul servizio Azure Container][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[use-advanced-networking]: configure-azure-cni.md
[new-azaks]: /powershell/module/az.aks/new-azaks
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md

---
title: 'Guida introduttiva: Creare un cluster del servizio Azure Kubernetes nel portale'
description: Informazioni su come creare rapidamente un cluster Kubernetes, distribuire un'applicazione e monitorare le prestazioni nel servizio Azure Kubernetes usando il portale di Azure.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: c43375afe7965475e84793ddcd54a38a2e9bd3cd
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "65073735"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Guida introduttiva: Distribuire un cluster del servizio Azure Kubernetes usando il portale di Azure

Il servizio Azure Kubernetes è un servizio Kubernetes gestito che permette di distribuire e gestire rapidamente i cluster. In questa guida introduttiva viene distribuito un cluster del servizio Azure Container usando il portale di Azure. Nel cluster verrà eseguita un'applicazione multi-contenitore che include un front-end Web e un'istanza di Redis. Verrà quindi descritto come monitorare l'integrità del cluster e dei pod che eseguono l'applicazione.

![Immagine del passaggio all'applicazione Azure Vote di esempio](media/container-service-kubernetes-walkthrough/azure-vote.png)

Questa guida introduttiva presuppone una comprensione di base dei concetti relativi a Kubernetes. Per altre informazioni, vedere [Concetti di base relativi a Kubernetes per il servizio Azure Kubernetes][kubernetes-concepts].

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Creare un cluster del servizio Azure Container

Nell'angolo in alto a sinistra nel portale di Azure, selezionare **+ Crea una risorsa** > **Servizio Kubernetes**.

Per creare un cluster del servizio Azure Kubernetes, seguire questa procedura:

1. **Informazioni di base** - Configurare le opzioni seguenti:
   - *DETTAGLI DEL PROGETTO*: Selezionare una sottoscrizione di Azure, quindi selezionare o creare un gruppo di risorse di Azure, ad esempio *myResourceGroup*. Immettere un **nome cluster Kubernetes**, ad esempio *myAKSCluster*.
   - *DETTAGLI DEI CLUSTER*: Selezionare un'area, una versione di Kubernetes e il prefisso di nome DNS per il cluster del servizio Azure Kubernetes.
   - *SCALABILITÀ*: Selezionare le dimensioni di macchina virtuale per i nodi del servizio Azure Kubernetes. Le dimensioni della macchina virtuale **non possono** essere modificate dopo che un cluster del servizio Azure Container è stato distribuito.
       - Selezionare il numero di nodi da distribuire nel cluster. Per questa guida introduttiva, impostare **Numero di nodi** su *1*. Il numero di nodi **può** essere modificato dopo che il cluster è stato distribuito.
    
     ![Creare un cluster del servizio Azure Kubernetes - fornire informazioni di base](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

     Selezionare **Avanti: Autenticazione** al termine.

1. **Autenticazione**: Configurare le opzioni seguenti:
   - Creare una nuova entità servizio o *configurare* l'uso di un'entità esistente. Quando si usa un nome dell'entità servizio esistente, è necessario specificarne l'ID client e il segreto.
   - Abilitare l'opzione per il controllo degli accessi in base al ruolo di Kubernetes, per ottenere un controllo più capillare sull'accesso alle risorse Kubernetes distribuite nel cluster del servizio Azure Kubernetes.

     Per impostazione predefinita, viene usata la rete di livello *Basic* e Monitoraggio di Azure per i contenitori è abilitato. Selezionare **Rivedi e crea** e quindi **Crea**.

Sono necessari alcuni minuti perché il cluster del servizio Azure Kubernetes venga creato e sia pronto per l'uso. Al termine, individuare il gruppo di risorse del cluster del servizio Azure Kubernetes, ad esempio *myResourceGroup*, quindi selezionare la risorsa del servizio Azure Kubernetes, ad esempio *myAKSCluster*. Viene visualizzato il dashboard del cluster del servizio Azure Kubernetes, come nello screenshot seguente:

![Esempio di dashboard del servizio Azure Kubernetes nel portale di Azure](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Connettersi al cluster

Per gestire un cluster Kubernetes, usare [kubectl][kubectl], il client da riga di comando di Kubernetes. Il client `kubectl` è preinstallato in Azure Cloud Shell.

Aprire Cloud Shell usando il pulsante nell'angolo in alto a destra del portale di Azure.

![Aprire Azure Cloud Shell nel portale](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az servizio Azure Kubernetes get-credentials][az-aks-get-credentials]. Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarli. L'esempio seguente ottiene le credenziali per il nome cluster *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Per verificare la connessione al cluster, usare il comando [kubectl get][kubectl-get] per restituire un elenco dei nodi del cluster.

```azurecli-interactive
kubectl get nodes
```

L'esempio di output seguente mostra il nodo singolo creato nei passaggi precedenti. Assicurarsi che lo stato del nodo sia impostato su *Pronto*:

```
NAME                       STATUS    ROLES     AGE       VERSION
aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Un file manifesto di Kubernetes definisce uno stato desiderato per il cluster, ad esempio le immagini del contenitore da eseguire. In questa guida introduttiva, viene usato un manifesto per creare tutti gli oggetti necessari per eseguire l'applicazione Azure Vote. Questo manifesto include due [distribuzioni Kubernetes][kubernetes-deployment], una per le applicazioni Python Azure Vote e l'altra per un'istanza di Redis. Vengono anche creati due [servizi Kubernetes][kubernetes-service], un servizio interno per l'istanza di Redis e un servizio esterno per l'accesso all'applicazione Azure Vote da Internet.

> [!TIP]
> In questa guida introduttiva, si creano e distribuiscono manualmente i manifesti dell'applicazione nel cluster servizio Azure Kubernetes. In altre situazioni reali, è possibile usare [Azure Dev Spaces][azure-dev-spaces] per eseguire rapidamente l'iterazione e il debug del codice direttamente nel cluster servizio Azure Kubernetes. È possibile usare Dev Spaces su piattaforme del sistema operativo e ambienti di sviluppo e collaborare con altri utenti nel team.

Creare un file denominato `azure-vote.yaml` e copiarlo nella definizione YAML seguente. In Azure Cloud Shell creare il file usando `vi` o `Nano` come se si lavorasse in un sistema virtuale o fisico:

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
---
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

Distribuire l'applicazione usando il comando [kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```azurecli-interactive
kubectl apply -f azure-vote.yaml
```

L'output di esempio seguente mostra le distribuzioni e i servizi creati correttamente:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Test dell'applicazione

Durante l'esecuzione dell'applicazione, un servizio Kubernetes espone il front-end dell'applicazione a Internet. Il processo potrebbe richiedere alcuni minuti.

Per monitorare lo stato, usare il comando [kubectl get service][kubectl-get] con l'argomento `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

*EXTERNAL-IP* per il servizio *azure-vote-front* viene inizialmente visualizzato come *pending*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando *EXTERNAL-IP* passa da *pending* a un effettivo indirizzo IP pubblico, usare `CTRL-C` per arrestare il processo di controllo `kubectl`. L'output di esempio seguente mostra un indirizzo IP pubblico valido assegnato al servizio:

```
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Per vedere in azione l'app Azure Vote, aprire un Web browser all'indirizzo IP esterno del servizio.

![Immagine del passaggio all'applicazione Azure Vote di esempio](media/container-service-kubernetes-walkthrough/azure-vote.png)

## <a name="monitor-health-and-logs"></a>Monitoraggio di stato e log

Dopo la creazione del cluster, Monitoraggio di Azure per i contenitori è stato abilitato. Questa funzione di monitoraggio fornisce metriche di integrità sia per il cluster del servizio Azure Kubernetes che per i pod in esecuzione nel cluster.

L'inserimento di questi dati nel portale di Azure potrebbe richiedere alcuni minuti. Per visualizzare lo stato corrente, il tempo di attività e l'utilizzo delle risorse per i pod di Azure Vote, tornare alla risorsa del servizio Azure Kubernetes nel portale di Azure, ad esempio *myservizio Azure KubernetesCluster*. È quindi possibile accedere allo stato di integrità nel modo seguente:

1. In **Monitoraggio** scegliere **Informazioni dettagliate** a sinistra.
1. Nella parte superiore scegliere **+ Aggiungi filtro**
1. Selezionare *Spazio dei nomi* come proprietà, quindi scegliere *\<All but kube-system\>* (Tutti tranne kube-system)
1. Scegliere di visualizzare i **Contenitori**.

Verranno visualizzati i contenitori *azure-vote-back* e *azure-vote-front*, come mostrato in questo esempio:

![Visualizzare l'integrità dei contenitori in esecuzione nel servizio Azure Kubernetes](media/kubernetes-walkthrough-portal/monitor-containers.png)

Per visualizzare i log per il pod `azure-vote-front`, selezionare il collegamento **View container logs** (Visualizza log contenitore) sul lato destro dell'elenco dei contenitori. Questi log includono i flussi *stdout* e *stderr* del contenitore.

![Visualizzare i log dei contenitori nel servizio Azure Kubernetes](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Eliminare il cluster

Quando il cluster non è più necessario, eliminare la risorsa del cluster, operazione che comporta l'eliminazione di tutte le risorse associate. Questa operazione può essere eseguita nel portale di Azure selezionando il pulsante **Elimina** nel dashboard del cluster del servizio Azure Kubernetes. In alternativa, è possibile usare il comando [az servizio Azure Kubernetes delete][az-aks-delete] in Cloud Shell:

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```

> [!NOTE]
> Quando si elimina il cluster, l'entità servizio di Azure Active Directory utilizzata dal cluster servizio Azure Kubernetes non viene rimossa. Per istruzioni su come rimuovere l'entità servizio, vedere le [considerazioni sull'entità servizio servizio Azure Kubernetes e la sua eliminazione][sp-delete].

## <a name="get-the-code"></a>Ottenere il codice

In questa guida introduttiva sono state usate immagini del contenitore già creato per creare una distribuzione di Kubernetes. Il codice dell'applicazione correlato, Dockerfile, e il file manifesto di Kubernetes sono disponibili su GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato distribuito un cluster Kubernetes in cui è stata quindi distribuita un'applicazione multicontenitore.

Per altre informazioni sul servizio Azure Container e l'analisi del codice completo per un esempio di distribuzione, passare all'esercitazione sul cluster Kubernetes.

> [!div class="nextstepaction"]
> [Esercitazione sul servizio Azure Container][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-delete]: /cli/azure/aks#az-aks-delete
[aks-monitor]: ../monitoring/monitoring-container-health.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
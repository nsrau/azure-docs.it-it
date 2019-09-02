---
title: Usare GPU nel servizio Azure Kubernetes
description: Informazioni su come usare le GPU per carichi di lavoro a elevato utilizzo di grafica o di calcolo ad alte prestazioni nel servizio Azure Kubernetes
services: container-service
author: zr-msft
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/16/2019
ms.author: zarhoads
ms.openlocfilehash: 4eef31a050072c0413421a5490b35b765cb9557d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68381824"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Usare le GPU per carichi di lavoro a elevato utilizzo di calcolo nel servizio Azure Kubernetes

Le unità di elaborazione grafica (GPU) sono spesso usate per carichi di lavoro a elevato utilizzo di calcolo, ad esempio i carichi di lavoro di visualizzazione o di grafica. Il servizio servizio Azure Kubernetes supporta la creazione di pool di nodi abilitati per la GPU per l'esecuzione di questi carichi di lavoro a elevato utilizzo di calcolo in Kubernetes. Per altre informazioni sulle macchine virtuali abilitate per GPU, vedere [dimensioni delle VM ottimizzate per GPU in Azure][gpu-skus]. Per i nodi servizio Azure Kubernetes è consigliabile una dimensione minima di *Standard_NC6*.

> [!NOTE]
> Le macchine virtuali abilitate per la GPU contengono hardware specializzato soggetto a prezzi maggiori e alla disponibilità a livello di area. Per altre informazioni, vedere lo strumento per i [prezzi][azure-pricing] e la [disponibilità di aree][azure-availability].

Attualmente, l'uso di pool di nodi abilitati per GPU è disponibile solo per i pool di nodi Linux.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente con nodi che supportano GPU. Il cluster servizio Azure Kubernetes deve eseguire Kubernetes 1.10 o versioni successive. Se occorre un cluster servizio Azure Kubernetes che soddisfi questi requisiti, vedere la prima sezione di questo articolo per [creare un cluster servizio Azure Kubernetes](#create-an-aks-cluster).

È necessaria anche l'interfaccia della riga di comando di Azure versione 2.0.64 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [installare l'interfaccia][install-azure-cli]della riga di comando di Azure.

## <a name="create-an-aks-cluster"></a>Creare un cluster del servizio Azure Container

Se occorre un cluster servizio Azure Kubernetes che soddisfi i requisiti minimi (nodo abilitato per la GPU e Kubernetes versione 1.10 o versioni successive), completare i passaggi seguenti. Se si dispone già di un cluster AKS che soddisfa questi requisiti, [passare alla sezione successiva](#confirm-that-gpus-are-schedulable).

Per prima cosa, creare un gruppo di risorse per il cluster usando il comando [AZ Group create][az-group-create] . L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nell'area *EastUS*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Creare ora un cluster AKS usando il comando [AZ AKS create][az-aks-create] . Nell'esempio seguente viene creato un cluster con un singolo nodo di `Standard_NC6`dimensioni:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

Ottenere le credenziali per il cluster AKS usando il comando [AZ AKS Get-credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-drivers"></a>Installare i driver nVidia

Prima di poter usare le GPU nei nodi, è necessario distribuire un DaemonSet per il plug-in del dispositivo NVIDIA. Il DaemonSet esegue un pod in ogni nodo per specificare i driver necessari per la GPU.

Per prima cosa, creare uno spazio dei nomi usando il comando [kubectl create namespace][kubectl-create] , ad esempio *GPU-* Resources:

```console
kubectl create namespace gpu-resources
```

Creare un file denominato *nvidia-device-plugin-ds.yaml* e incollare il manifesto YAML seguente. Questo manifesto viene fornito come parte del plug-in del [dispositivo Nvidia per il progetto Kubernetes][nvidia-github].

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: kube-system
spec:
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      - key: nvidia.com/gpu
        operator: Exists
        effect: NoSchedule
      containers:
      - image: nvidia/k8s-device-plugin:1.11
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
```

A questo punto usare il comando [kubectl Apply][kubectl-apply] per creare il DaemonSet e verificare che il plug-in NVIDIA Device venga creato correttamente, come illustrato nell'output di esempio seguente:

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="confirm-that-gpus-are-schedulable"></a>Verificare che le GPU siano pianificabili

Dopo avere creato il cluster servizio Azure Kubernetes, verificare la pianificabilità delle GPU in Kubernetes. Per prima cosa, elencare i nodi del cluster usando il comando [kubectl Get nodes][kubectl-get] :

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

Usare ora il comando [kubectl Descrivi nodo][kubectl-describe] per verificare che le GPU siano pianificabili. Nella sezione *Capacity* (Capacità) la GPU deve comparire in elenco come `nvidia.com/gpu:  1`.

L'esempio sintetico seguente mostra che nel nodo denominato *aks-nodepool1-18821093-0* è disponibile una GPU:

```console
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 attachable-volumes-azure-disk:  24
 cpu:                            6
 ephemeral-storage:              101584140Ki
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         57713784Ki
 nvidia.com/gpu:                 1
 pods:                           110
Allocatable:
 attachable-volumes-azure-disk:  24
 cpu:                            5916m
 ephemeral-storage:              93619943269
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         51702904Ki
 nvidia.com/gpu:                 1
 pods:                           110
System Info:
 Machine ID:                 b0cd6fb49ffe4900b56ac8df2eaa0376
 System UUID:                486A1C08-C459-6F43-AD6B-E9CD0F8AEC17
 Boot ID:                    f134525f-385d-4b4e-89b8-989f3abb490b
 Kernel Version:             4.15.0-1040-azure
 OS Image:                   Ubuntu 16.04.6 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.12.7
 Kube-Proxy Version:         v1.12.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  kube-system                nvidia-device-plugin-daemonset-bbjlq     0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m39s

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>Eseguire un carico di lavoro abilitato per la GPU

Per vedere la GPU in esecuzione, pianificare un carico di lavoro abilitato per la GPU con la richiesta di risorse appropriata. In questo esempio viene eseguito il processo [Tensorflow](https://www.tensorflow.org/) rispetto al [set di dati MNIST](http://yann.lecun.com/exdb/mnist/).

Creare un file denominato *samples-tf-mnist-demo.yaml* e incollare il manifesto YAML seguente. Il manifesto del processo seguente include un limite di risorse di `nvidia.com/gpu: 1`:

> [!NOTE]
> Se si riceve un errore di mancata corrispondenza tra versioni quando si chiamano i driver, ad esempio quando la versione del driver CUDA non è sufficiente per la versione di runtime CUDA, esaminare il grafico di compatibilità di matrice di driver NVIDIA- [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: microsoft/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

Usare il comando [kubectl Apply][kubectl-apply] per eseguire il processo. Questo comando analizza il file manifesto e crea gli oggetti Kubernetes definiti:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Visualizzare lo stato e l'output del carico di lavoro abilitato per la GPU

Monitorare lo stato di avanzamento del processo usando il comando [kubectl Get Jobs][kubectl-get] con `--watch` l'argomento. L'esecuzione del pull dell'immagine come prima cosa e l'elaborazione del set di dati possono richiedere alcuni minuti. Quando la colonna completes Visualizza *1/1*, il processo è stato completato correttamente. Uscire dal `kubetctl --watch` comando con *CTRL + C*:

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Per esaminare l'output del carico di lavoro abilitato per la GPU, ottenere innanzitutto il nome del Pod con il comando [kubectl Get Pod][kubectl-get] :

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

A questo punto usare il comando [kubectl logs][kubectl-logs] per visualizzare i log pod. I log dei pod di esempio seguenti confermano che è stato rilevato il dispositivo GPU appropriato, vale a dire `Tesla K80`. Specificare un nome per il proprio pod:

```console
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-05-16 16:08:31.258328: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-05-16 16:08:31.396846: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 2fd7:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2019-05-16 16:08:31.396886: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 2fd7:00:00.0, compute capability: 3.7)
2019-05-16 16:08:36.076962: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.1081
Accuracy at step 10: 0.7457
Accuracy at step 20: 0.8233
Accuracy at step 30: 0.8644
Accuracy at step 40: 0.8848
Accuracy at step 50: 0.8889
Accuracy at step 60: 0.8898
Accuracy at step 70: 0.8979
Accuracy at step 80: 0.9087
Accuracy at step 90: 0.9099
Adding run metadata for 99
Accuracy at step 100: 0.9125
Accuracy at step 110: 0.9184
Accuracy at step 120: 0.922
Accuracy at step 130: 0.9161
Accuracy at step 140: 0.9219
Accuracy at step 150: 0.9151
Accuracy at step 160: 0.9199
Accuracy at step 170: 0.9305
Accuracy at step 180: 0.9251
Accuracy at step 190: 0.9258
Adding run metadata for 199
Accuracy at step 200: 0.9315
Accuracy at step 210: 0.9361
Accuracy at step 220: 0.9357
Accuracy at step 230: 0.9392
Accuracy at step 240: 0.9387
Accuracy at step 250: 0.9401
Accuracy at step 260: 0.9398
Accuracy at step 270: 0.9407
Accuracy at step 280: 0.9434
Accuracy at step 290: 0.9447
Adding run metadata for 299
Accuracy at step 300: 0.9463
Accuracy at step 310: 0.943
Accuracy at step 320: 0.9439
Accuracy at step 330: 0.943
Accuracy at step 340: 0.9457
Accuracy at step 350: 0.9497
Accuracy at step 360: 0.9481
Accuracy at step 370: 0.9466
Accuracy at step 380: 0.9514
Accuracy at step 390: 0.948
Adding run metadata for 399
Accuracy at step 400: 0.9469
Accuracy at step 410: 0.9489
Accuracy at step 420: 0.9529
Accuracy at step 430: 0.9507
Accuracy at step 440: 0.9504
Accuracy at step 450: 0.951
Accuracy at step 460: 0.9512
Accuracy at step 470: 0.9539
Accuracy at step 480: 0.9533
Accuracy at step 490: 0.9494
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere gli oggetti Kubernetes associati creati in questo articolo, usare il comando [kubectl Delete Job][kubectl delete] come indicato di seguito:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>Passaggi successivi

Per eseguire i processi di Apache Spark, vedere [eseguire processi di Apache Spark su AKS][aks-spark].

Per altre informazioni sull'esecuzione di carichi di lavoro di Machine Learning (ML) in Kubernetes, vedere [Kubeflow Labs][kubeflow-labs].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/
[nvidia-github]: https://github.com/NVIDIA/k8s-device-plugin

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/linux/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli

---
title: Usare GPU nel servizio Azure Kubernetes
description: Informazioni su come usare le GPU per carichi di lavoro a elevato utilizzo di grafica o di calcolo ad alte prestazioni nel servizio Azure Kubernetes
services: container-service
author: lachie83
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 10/25/2018
ms.author: laevenso
ms.openlocfilehash: 683abd9bad93bff51bea84c8081d2b8f9d300cd4
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419249"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Usare le GPU per carichi di lavoro a elevato utilizzo di calcolo nel servizio Azure Kubernetes

Le unità di elaborazione grafica (GPU) sono spesso usate per carichi di lavoro a elevato utilizzo di calcolo, ad esempio i carichi di lavoro di visualizzazione o di grafica. Il servizio AKS supporta la creazione di pool di nodi abilitati per la GPU per l'esecuzione di questi carichi di lavoro a elevato utilizzo di calcolo in Kubernetes. Per altre informazioni sulle macchine virtuali abilitate per GPU disponibili, vedere [Dimensioni delle macchine virtuali ottimizzate per la GPU][gpu-skus]. Per i nodi AKS è consigliabile una dimensione minima di *Standard_NC6*.

> [!NOTE]
> Le macchine virtuali abilitate per la GPU contengono hardware specializzato soggetto a prezzi maggiori e alla disponibilità a livello di area. Per altre informazioni, vedere il calcolatore dei [prezzi][azure-pricing] e la [disponibilità a livello di area][azure-availability].

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster AKS esistente con nodi che supportano GPU. Il cluster AKS deve eseguire Kubernetes 1.10 o versioni successive. Se occorre un cluster AKS che soddisfi questi requisiti, vedere la prima sezione di questo articolo per [creare un cluster AKS](#create-an-aks-cluster).

È necessario anche che sia installata e configurata l'interfaccia della riga di comando di Azure versione 2.0.49 o successiva. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="create-an-aks-cluster"></a>Creare un cluster del servizio contenitore di Azure

Se occorre un cluster AKS che soddisfi i requisiti minimi (nodo abilitato per la GPU e Kubernetes versione 1.10 o versioni successive), completare i passaggi seguenti. Se si dispone già di un cluster AKS che soddisfa questi requisiti, passare alla sezione successiva.

Creare prima di tutto un gruppo di risorse per il cluster usando il comando [az group create][az-group-create]. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nell'area *EastUS*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Creare ora un cluster AKS usando il comando [az aks create][az-aks-create]. L'esempio seguente crea un cluster con un singolo nodo di dimensione `Standard_NC6` ed esegue Kubernetes versione 1.10.8:

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1 \
    --kubernetes-version 1.10.8
```

Ottenere le credenziali per il cluster AKS usando il comando [az aks get-credentials][az-aks-get-credentials]:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="confirm-that-gpus-are-schedulable"></a>Verificare che le GPU siano pianificabili

Dopo avere creato il cluster AKS, verificare la pianificabilità delle GPU in Kubernetes. Elencare prima di tutto i nodi nel cluster usando il comando [kubectl get nodes][kubectl-get]:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-18821093-0   Ready    agent   6m    v1.10.8
```

Usare a questo punto il comando [kubectl describe node][kubectl-describe] per verificare che le GPU siano pianificabili. Nella sezione *Capacity* (Capacità) la GPU deve comparire in elenco come `nvidia.com/gpu:  1`. Se le GPU non sono visibili, vedere la sezione [Risolvere i problemi di disponibilità della GPU](#troubleshoot-gpu-availability).

L'esempio sintetico seguente mostra che nel nodo denominato *aks-nodepool1-18821093-0* è disponibile una GPU:

```
$ kubectl describe node aks-nodepool1-18821093-0

Name:               aks-nodepool1-18821093-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 cpu:                6
 ephemeral-storage:  30428648Ki
 hugepages-1Gi:      0
 hugepages-2Mi:      0
 memory:             57713824Ki
 nvidia.com/gpu:     1
 pods:               110
Allocatable:
 cpu:                5940m
 ephemeral-storage:  28043041951
 hugepages-1Gi:      0
 hugepages-2Mi:      0
 memory:             53417120Ki
 nvidia.com/gpu:     1
 pods:               110
System Info:
 Machine ID:                 688e083d19554d4a9563bd138f4ca98b
 System UUID:                08162568-B987-A84D-8865-98D6EFC64B32
 Boot ID:                    7b440249-8a96-42eb-950f-08c9a3c530b7
 Kernel Version:             4.15.0-1023-azure
 OS Image:                   Ubuntu 16.04.5 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.10.8
 Kube-Proxy Version:         v1.10.8
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myGPUCluster_myGPUCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-18821093-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                    CPU Requests  CPU Limits  Memory Requests  Memory Limits
  ---------                  ----                                    ------------  ----------  ---------------  -------------
  gpu-resources              nvidia-device-plugin-9cfcf              0 (0%)        0 (0%)      0 (0%)           0 (0%)

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>Eseguire un carico di lavoro abilitato per la GPU

Per vedere la GPU in esecuzione, pianificare un carico di lavoro abilitato per la GPU con la richiesta di risorse appropriata. In questo esempio viene eseguito il processo [Tensorflow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) rispetto al [set di dati MNIST](http://yann.lecun.com/exdb/mnist/).

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

Usare il comando [kubectl apply][kubectl-apply] per eseguire il processo. Questo comando analizza il file manifesto e crea gli oggetti Kubernetes definiti:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Visualizzare lo stato e l'output del carico di lavoro abilitato per la GPU

Monitorare lo stato del processo usando il comando [kubectl get jobs][kubectl-get] con l'argomento `--watch`. L'esecuzione del pull dell'immagine come prima cosa e l'elaborazione del set di dati possono richiedere alcuni minuti. Quando la colonna *COMPLETIONS* (COMPLETAMENTI) mostra *1/1* significa che il processo è terminato con esito positivo:

```
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Per esaminare l'output del carico di lavoro abilitato per la GPU, ottenere prima di tutto il nome dei pod usando il comando [kubectl get pods][kubectl-get]:

```
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-smnr6   0/1     Completed   0          3m
```

Usare a questo punto il comando [kubectl logs][kubectl-logs] per visualizzare i log dei pod. I log dei pod di esempio seguenti confermano che è stato rilevato il dispositivo GPU appropriato, vale a dire `Tesla K80`. Specificare un nome per il proprio pod:

```
$ kubectl logs samples-tf-mnist-demo-smnr6

2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
Accuracy at step 40: 0.8685
Accuracy at step 50: 0.8864
Accuracy at step 60: 0.901
Accuracy at step 70: 0.905
Accuracy at step 80: 0.9103
Accuracy at step 90: 0.9126
Adding run metadata for 99
Accuracy at step 100: 0.9176
Accuracy at step 110: 0.9149
Accuracy at step 120: 0.9187
Accuracy at step 130: 0.9253
Accuracy at step 140: 0.9252
Accuracy at step 150: 0.9266
Accuracy at step 160: 0.9255
Accuracy at step 170: 0.9267
Accuracy at step 180: 0.9257
Accuracy at step 190: 0.9309
Adding run metadata for 199
Accuracy at step 200: 0.9272
Accuracy at step 210: 0.9321
Accuracy at step 220: 0.9343
Accuracy at step 230: 0.9388
Accuracy at step 240: 0.9408
Accuracy at step 250: 0.9394
Accuracy at step 260: 0.9412
Accuracy at step 270: 0.9422
Accuracy at step 280: 0.9436
Accuracy at step 290: 0.9411
Adding run metadata for 299
Accuracy at step 300: 0.9426
Accuracy at step 310: 0.9466
Accuracy at step 320: 0.9458
Accuracy at step 330: 0.9407
Accuracy at step 340: 0.9445
Accuracy at step 350: 0.9486
Accuracy at step 360: 0.9475
Accuracy at step 370: 0.948
Accuracy at step 380: 0.9516
Accuracy at step 390: 0.9534
Adding run metadata for 399
Accuracy at step 400: 0.9501
Accuracy at step 410: 0.9552
Accuracy at step 420: 0.9535
Accuracy at step 430: 0.9545
Accuracy at step 440: 0.9533
Accuracy at step 450: 0.9526
Accuracy at step 460: 0.9566
Accuracy at step 470: 0.9547
Accuracy at step 480: 0.9548
Accuracy at step 490: 0.9545
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere gli oggetti Kubernetes associati creati in questo articolo, usare il comando [kubectl delete job][kubectl delete] come segue:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="troubleshoot-gpu-availability"></a>Risolvere i problemi di disponibilità della GPU

Se nei nodi non sono visibili GPU disponibili, è possibile che sia necessario distribuire un DaemonSet per il plug-in del dispositivo NVIDIA. Il DaemonSet esegue un pod in ogni nodo per specificare i driver necessari per la GPU.

Creare prima di tutto uno spazio dei nomi usando il comando [kubectl create namespace][kubectl-create], ad esempio *gpu-resources*:

```console
kubectl create namespace gpu-resources
```

Creare un file denominato *nvidia-device-plugin-ds.yaml* e incollare il manifesto YAML seguente. Aggiornare `image: nvidia/k8s-device-plugin:1.10` a metà del file manifesto in modo che corrisponda alla versione di Kubernetes. Se ad esempio il cluster AKS esegue Kubernetes versione 1.11, aggiornare il tag a `image: nvidia/k8s-device-plugin:1.11`.

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  labels:
    kubernetes.io/cluster-service: "true"
  name: nvidia-device-plugin
  namespace: gpu-resources
spec:
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
      containers:
      - image: nvidia/k8s-device-plugin:1.10 # Update this tag to match your Kubernetes version
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
      nodeSelector:
        beta.kubernetes.io/os: linux
        accelerator: nvidia
```

Usare a questo punto il comando [kubectl apply][kubectl-apply] per creare il DaemonSet:

```
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

Eseguire di nuovo il comando [kubectl describe node][kubectl-describe] per verificare che la GPU sia disponibile nel nodo.

## <a name="next-steps"></a>Passaggi successivi

Per eseguire i processi Apache Spark, vedere [Eseguire un processo Apache Spark con il servizio Kubernetes di Azure][aks-spark].

Per altre informazioni sull'esecuzione di carichi di lavoro di Machine Learning (ML) in Kubernetes, vedere i [laboratori Kubeflow][kubeflow-labs].

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

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/linux/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli

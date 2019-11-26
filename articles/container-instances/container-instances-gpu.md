---
title: Deploy GPU-enabled container instance
description: Learn how to deploy Azure container instances to run compute-intensive container apps using GPU resources.
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: ea3b0ccba2d84487356f4bbd404cec3af1d0979a
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484179"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Distribuire le istanze di contenitore che usano risorse della GPU

Per eseguire determinati carichi di lavoro a elevato utilizzo di calcolo in istanze di Azure Container, distribuire i [gruppi di contenitori](container-instances-container-groups.md) con le *risorse della GPU*. Le istanze di contenitore nel gruppo possono accedere a una o più GPU NVIDIA Tesla durante l'esecuzione dei carichi di lavoro dei contenitori, ad esempio CUDA e applicazioni di Deep Learning.

This article shows how to add GPU resources when you deploy a container group by using a [YAML file](container-instances-multi-container-yaml.md) or [Resource Manager template](container-instances-multi-container-group.md). You can also specify GPU resources when you deploy a container instance using the Azure portal.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima e [si applicano alcune limitazioni](#preview-limitations). Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="preview-limitations"></a>Limiti di anteprima

In fase di anteprima, durante l'uso di risorse della GPU in gruppi di contenitori si applicano le limitazioni seguenti. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

In futuro verrà aggiunto il supporto di ulteriori aree.

**Supported OS types**: Linux only

**Additional limitations**: GPU resources can't be used when deploying a container group into a [virtual network](container-instances-vnet.md).

## <a name="about-gpu-resources"></a>Informazioni sulle risorse GPU

### <a name="count-and-sku"></a>Conteggio e SKU

Per usare le GPU in un'istanza di contenitore, specificare una *risorsa GPU* con le informazioni seguenti:

* **Count** - The number of GPUs: **1**, **2**, or **4**.
* **SKU** - The GPU SKU: **K80**, **P100**, or **V100**. Ogni SKU esegue il mapping alla GPU NVIDIA Tesla in una delle famiglie di macchine virtuali di Azure abilitate per la GPU seguenti:

  | SKU | Famiglia di macchine virtuali |
  | --- | --- |
  | K80 | [NC](../virtual-machines/linux/sizes-gpu.md#nc-series) |
  | P100 | [NCv2](../virtual-machines/linux/sizes-gpu.md#ncv2-series) |
  | V100 | [NCv3](../virtual-machines/linux/sizes-gpu.md#ncv3-series) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

When deploying GPU resources, set CPU and memory resources appropriate for the workload, up to the maximum values shown in the preceding table. These values are currently larger than the CPU and memory resources available in container groups without GPU resources.  

### <a name="things-to-know"></a>Informazioni importanti

* **Tempo di distribuzione**: la creazione di un gruppo di contenitori con le risorse della GPU richiede fino a **8-10 minuti**. Ciò è dovuto al tempo aggiuntivo necessario per il provisioning e la configurazione di una macchina virtuale della GPU in Azure. 

* **Prezzi**: simili ai gruppi di contenitori senza risorse della GPU, le fatture di Azure per le risorse usate per la *durata* di un gruppo di contenitori con risorse della GPU. La durata viene calcolata a partire dal pull dell'immagine del primo contenitore fino al termine del gruppo di contenitori. Non include il tempo necessario per distribuire il gruppo di contenitori.

  Vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/container-instances/).

* **Driver CUDA**: poiché viene effettuato il pre-provisioning delle istanze di contenitore con risorse della GPU con i driver NVIDIA CUDA e i runtime dei contenitori, è possibile usare le immagini dei contenitori sviluppati per carichi di lavoro CUDA.

  We support CUDA 9.0 at this stage. For example, you can use following base images for your Docker file:
  * [nvidia/cuda:9.0-base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-gpu-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>Esempio YAML

Un modo per aggiungere risorse GPU consiste nel distribuire un gruppo di contenitori tramite un [file YAML](container-instances-multi-container-yaml.md). Copiare il file YAML seguente in un nuovo file denominato *gpu-deploy-aci.yaml*, quindi salvare il file. Il file YAML crea un gruppo di contenitori denominato *gpucontainergroup* specificando un'istanza di contenitore con una GPU K80. L'istanza esegue un'applicazione di aggiunta vettore CUDA di esempio. Le richieste di risorse sono sufficienti per eseguire il carico di lavoro.

```YAML
additional_properties: {}
apiVersion: '2018-10-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
  osType: Linux
  restartPolicy: OnFailure
```

Deploy the container group with the [az container create][az-container-create] command, specifying the YAML file name for the `--file` parameter. È necessario specificare il nome di un gruppo di risorse e un percorso per il gruppo di contenitori come *eastus* che supporta le risorse della GPU.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

La distribuzione richiede alcuni minuti. Viene quindi avviato il contenitore che esegue un'operazione di aggiunta vettore CUDA. Run the [az container logs][az-container-logs] command to view the log output:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Output:

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>Esempio di modello di Resource Manager

Un altro modo per distribuire un gruppo di contenitori con le risorse GPU consiste nell'usare un [modello di Resource Manager](container-instances-multi-container-group.md). Per iniziare, creare un file denominato `gpudeploy.json` e quindi copiarvi il codice JSON seguente. This example deploys a container instance with a V100 GPU that runs a [TensorFlow](https://www.tensorflow.org/) training job against the MNIST dataset. Le richieste di risorse sono sufficienti per eseguire il carico di lavoro.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "microsoft/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2018-10-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

Distribuire il modello con il comando [az group deployment create][az-group-deployment-create]. È necessario specificare il nome di un gruppo di risorse creato in un'area come *eastus* che supporta le risorse della GPU.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

La distribuzione richiede alcuni minuti. Viene quindi avviato il contenitore che esegue il processo TensorFlow. Run the [az container logs][az-container-logs] command to view the log output:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergrouprm --container-name gpucontainer
```

Output:

```Console
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
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>Pulire le risorse

Poiché l'utilizzo delle risorse GPU può risultare costoso, assicurarsi che i contenitori non vengono eseguiti in modo imprevisto per lunghi periodi di tempo. Monitor your containers in the Azure portal, or check the status of a container group with the [az container show][az-container-show] command. ad esempio:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Dopo aver usato le istanze di contenitore create, eliminarle con i comandi seguenti:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Passaggi successivi

* Visualizzare altre informazioni sulla distribuzione di un gruppo di contenitori tramite un [file YAML](container-instances-multi-container-yaml.md) o un [modello di Resource Manager](container-instances-multi-container-group.md).
* Visualizzare altre informazioni sulle [dimensioni delle macchine virtuali ottimizzate per la GPU](../virtual-machines/linux/sizes-gpu.md) in Azure.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

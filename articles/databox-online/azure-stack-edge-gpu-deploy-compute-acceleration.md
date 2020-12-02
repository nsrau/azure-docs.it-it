---
title: Usare la GPU per l'accelerazione di calcolo o VPU nei dispositivi Azure Stack Edge per le distribuzioni di Kubernetes | Microsoft Docs
description: Viene descritto come usare la GPU per l'accelerazione delle risorse di calcolo o VPU sulla GPU Pro Azure Stack Edge Pro, Azure Stack Edge Pro R o Azure Stack Edge Mini ri per le distribuzioni Kubernetes.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/05/2020
ms.author: alkohli
ms.openlocfilehash: 0aaad18ba5bf98ca2ad53bd86605dfc6cce3e52c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467150"
---
# <a name="use-compute-acceleration-on-azure-stack-edge-pro-gpu-for-kubernetes-deployment"></a>Usare l'accelerazione di calcolo sulla GPU Pro Azure Stack Edge per la distribuzione di Kubernetes

Questo articolo descrive come usare l'accelerazione di calcolo nei dispositivi Azure Stack Edge quando si usano le distribuzioni Kubernetes. L'articolo si applica a Azure Stack GPU Pro Edge, Azure Stack Edge Pro R e Azure Stack dispositivi Mini R Edge.


## <a name="about-compute-acceleration"></a>Informazioni sull'accelerazione di calcolo 

L'unità di elaborazione centrale (CPU) è il calcolo per utilizzo generico predefinito per la maggior parte dei processi in esecuzione in un computer. Spesso viene usato un hardware specializzato per computer per eseguire alcune funzioni in modo più efficiente rispetto all'esecuzione di quelle nel software in una CPU. Ad esempio, è possibile usare un'unità di elaborazione grafica (GPU) per accelerare l'elaborazione dei dati in pixel.  

L'accelerazione di calcolo è un termine usato in modo specifico per i dispositivi Azure Stack Edge in cui un'unità di elaborazione grafica (GPU), un'unità di elaborazione della visione (VPU) o un FPGA (Field Programmable Gate Array) viene usato per l'accelerazione hardware. La maggior parte dei carichi di lavoro distribuiti nel dispositivo Azure Stack Edge implica tempi critici, più flussi di fotocamere e/o frequenze di frame elevate, che richiedono l'accelerazione hardware specifica.

Nell'articolo viene illustrata l'accelerazione del calcolo utilizzando solo GPU o VPU per i dispositivi seguenti:

- **Azure stack GPU Pro Edge** : questi dispositivi possono avere una GPU core del tensore NVIDIA T4 o 2. Per ulteriori informazioni, vedere [NVIDIA T4](https://www.nvidia.com/data-center/tesla-t4/).
- **Azure stack Edge Pro R** : questi dispositivi hanno una GPU NVIDIA T4 Tensor core. Per ulteriori informazioni, vedere [NVIDIA T4](https://www.nvidia.com/data-center/tesla-t4/).
- **Mini R di Azure stack Edge** : questi dispositivi hanno 1 Intel Movidius miriade X VPU. Per altre informazioni, vedere la pagina relativa a [Intel Movidius miriade X VPU](https://www.movidius.com/MyriadX).


## <a name="use-gpu-for-kubernetes-deployment"></a>USA GPU per la distribuzione di Kubernetes

L'esempio seguente `yaml` può essere usato per una GPU pro Azure stack Edge o un dispositivo R Azure stack Edge Pro con una GPU.

<!--In a production scenario, Pods are not used directly and these are wrapped around higher level constructs like Deployment, ReplicaSet which maintain the desired state in case of pod restarts, failures.-->

```yml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
    - name: cuda-container
      image: nvidia/cuda:9.0-devel
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
    - name: digits-container
      image: nvidia/digits:6.0
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
```


## <a name="use-vpu-for-kubernetes-deployment"></a>Usare VPU per la distribuzione di Kubernetes

L'esempio seguente `yaml` può essere usato per un dispositivo Mini R Azure stack Edge con un VPU.

```yml
apiVersion: batch/v1
kind: Job
metadata:
 name: intelvpu-demo-job
 labels:
   jobgroup: intelvpu-demo
spec:
 template:
   metadata:
     labels:
       jobgroup: intelvpu-demo
   spec:
     restartPolicy: Never
     containers:
       -
         name: intelvpu-demo-job-1
         image: ubuntu-demo-openvino:devel
         imagePullPolicy: IfNotPresent
         command: [ "/do_classification.sh" ]
         resources:
           limits:
             vpu.intel.com/hddl: 1
```


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [usare kubectl per eseguire un'applicazione con stato Kubernetes con un PersistentVolume sul dispositivo GPU Pro Azure stack Edge](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).

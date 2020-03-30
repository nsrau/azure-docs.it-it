---
title: File di inclusione
description: File di inclusione
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/23/2019
ms.openlocfilehash: ba1e0eee5de65364d8e5e762195f7de98384c2a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76122585"
---
| Destinazione del calcolo | Utilizzo | Supporto GPU | Supporto FPGA | Descrizione |
| ----- | ----- | ----- | ----- | ----- |
| [Servizio&nbsp;&nbsp;Web locale](../articles/machine-learning/how-to-deploy-and-where.md#local) | Test/debug | &nbsp; | &nbsp; | Utilizzare per test e risoluzione dei problemi limitati. L'accelerazione hardware dipende dall'utilizzo delle librerie nel sistema locale.
| [Servizio Web dell'istanza di calcolo di Azure Machine LearningAzure Machine Learning compute instance&nbsp;web&nbsp;service](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | Test/debug | &nbsp; | &nbsp; | Utilizzare per test e risoluzione dei problemi limitati.
| [Servizio Azure Kubernetes](../articles/machine-learning/how-to-deploy-and-where.md#aks) | Inferenza in tempo reale |  [Sì](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (distribuzione del servizio Web) | [Sì](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Utilizzare per distribuzioni di produzione su larga scala. Fornisce tempi di risposta rapidi e scalabilità automatica del servizio distribuito. La scalabilità automatica del cluster non è supportata tramite Azure Machine Learning SDK. Per modificare i nodi nel cluster AKS, usare l'interfaccia utente per il cluster AKS nel portale di Azure.To change the nodes in the AKS cluster, use the UI for your AKS cluster in the Azure portal. AKS è l'unica opzione disponibile per la finestra di progettazione. |
| [Istanze del contenitore di AzureAzure Container Instances](../articles/machine-learning/how-to-deploy-and-where.md#aci) | Test o sviluppo | &nbsp;  | &nbsp; | Utilizzare per carichi di lavoro basati su CPU a basso costo che richiedono meno di 48 GB di RAM. |
| [Cluster di elaborazione di Azure Machine Learning](../articles/machine-learning/how-to-use-parallel-run-step.md) | (Anteprima) Inferenza batch&nbsp; | [Sì](../articles/machine-learning/how-to-use-parallel-run-step.md) (pipeline di apprendimento automatico)Yes (machine learning pipeline) | &nbsp;  | Eseguire il punteggio batch su un calcolo senza server. Supporta macchine virtuali normali e con priorità bassa. |
| [Funzioni di AzureAzure Functions](../articles/machine-learning/how-to-deploy-functions.md) | (Anteprima) Inferenza in tempo reale | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | (Anteprima) Modulo IoT&nbsp; |  &nbsp; | &nbsp; | Distribuire e gestire modelli di Machine Learning su dispositivi IoT.Deploy and serve ML models on IoT devices. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | Tramite IoT Edge |  &nbsp; | Sì | Distribuire e gestire modelli di Machine Learning su dispositivi IoT.Deploy and serve ML models on IoT devices. |

> [!NOTE]
> Sebbene le destinazioni di calcolo come le destinazioni di calcolo locali, Azure Machine Learning e i cluster di calcolo di Azure Machine Learning supportino la GPU per la formazione e la sperimentazione, l'uso della GPU per l'inferenza __quando viene distribuito come servizio Web__ è supportato solo nel servizio Azure Kubernetes.Although compute targets like local, Azure Machine Learning compute instance, and Azure Machine Learning compute clusters support GPU for training and experimentation, using GPU for inference when a web service is supported only on Azure Kubernetes Service.
>
> L'uso di una GPU per l'inferenza __quando il punteggio con una pipeline__ di Machine Learning è supportato solo in Azure Machine Learning Compute.Using a GPU for inference when scoring with a machine learning pipeline is supported only on Azure Machine Learning Compute.
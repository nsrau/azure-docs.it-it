---
title: includere il file
description: Includere file
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/23/2019
ms.openlocfilehash: 9eedc0c3044717360494b222c88d73b3c8999e94
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82587806"
---
| Destinazione del calcolo | Utilizzo | Supporto GPU | Supporto per FPGA | Descrizione |
| ----- | ----- | ----- | ----- | ----- |
| [Servizio&nbsp;Web&nbsp;locale](../articles/machine-learning/how-to-deploy-and-where.md#local) | Test/debug | &nbsp; | &nbsp; | Usare per i test e la risoluzione dei problemi limitati. L'accelerazione hardware dipende dall'uso di librerie nel sistema locale.
| [Servizio Web&nbsp;dell'istanza&nbsp;di calcolo Azure Machine Learning](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | Test/debug | &nbsp; | &nbsp; | Usare per i test e la risoluzione dei problemi limitati.
| [Servizio Azure Kubernetes](../articles/machine-learning/how-to-deploy-and-where.md#aks) | Inferenza in tempo reale |  [Sì](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (distribuzione servizio Web) | [Sì](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Usare per le distribuzioni di produzione su vasta scala. Fornisce tempi di risposta rapidi e scalabilità automatica del servizio distribuito. La scalabilità automatica del cluster non è supportata tramite il Azure Machine Learning SDK. Per modificare i nodi nel cluster AKS, usare l'interfaccia utente per il cluster AKS nell'portale di Azure. AKS è l'unica opzione disponibile per la finestra di progettazione. |
| [Istanze di Azure Container](../articles/machine-learning/how-to-deploy-and-where.md#aci) | Test o sviluppo | &nbsp;  | &nbsp; | Usare per carichi di lavoro basati su CPU su scala ridotta che richiedono meno di 48 GB di RAM. |
| [Cluster di elaborazione di Azure Machine Learning](../articles/machine-learning/how-to-use-parallel-run-step.md) | Anteprima Inferenza batch&nbsp; | [Sì](../articles/machine-learning/how-to-use-parallel-run-step.md) (pipeline di machine learning) | &nbsp;  | Eseguire il Punteggio batch su calcolo senza server. Supporta le macchine virtuali normali e con priorità bassa. |
| [Funzioni di Azure](../articles/machine-learning/how-to-deploy-functions.md) | Anteprima Inferenza in tempo reale | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | Anteprima &nbsp;Modulo Internet delle cose |  &nbsp; | &nbsp; | Distribuire e gestire modelli di Machine Learning nei dispositivi Internet. |
| [Azure Data Box Edge](../articles/databox-online/azure-stack-edge-overview.md)   | Tramite IoT Edge |  &nbsp; | Sì | Distribuire e gestire modelli di Machine Learning nei dispositivi Internet. |

> [!NOTE]
> Sebbene le destinazioni di calcolo come locali, Azure Machine Learning istanza di calcolo e i cluster di calcolo Azure Machine Learning supportano la GPU per la formazione e la sperimentazione, l'uso della GPU per l'inferenza __quando viene distribuito come servizio Web__ è supportato solo nel servizio Azure Kubernetes.
>
> L'uso di una GPU per l'inferenza __quando il punteggio con una pipeline di Machine Learning__ è supportato solo nel calcolo Azure Machine Learning.
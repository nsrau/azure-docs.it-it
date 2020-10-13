---
title: includere il file
description: includere file
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 06/25/2020
ms.openlocfilehash: 944b96e7726f2b2becd5960a17a89c00d00c878a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91841953"
---
La destinazione di calcolo usata per ospitare il modello influirà sul costo e sulla disponibilità dell'endpoint distribuito. Usare questa tabella per scegliere una destinazione di calcolo appropriata.

| Destinazione del calcolo | Utilizzo | Supporto GPU | Supporto FPGA | Descrizione |
| ----- | ----- | ----- | ----- | ----- |
| [Servizio&nbsp;Web&nbsp;locale](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Test/debug | &nbsp; | &nbsp; | Usare per attività limitate di test e risoluzione dei problemi. L'accelerazione hardware dipende dall'uso di librerie nel sistema locale.
| [Servizio&nbsp;Web&nbsp;istanza di ambiente di calcolo di Azure Machine Learning](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Test/debug | &nbsp; | &nbsp; | Usare per attività limitate di test e risoluzione dei problemi.
| [Servizio Azure Kubernetes](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Inferenza in tempo reale |  [Sì](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (distribuzione del servizio Web) | [Sì](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Usare per distribuzioni di produzione su larga scala. Offre tempi di risposta rapidi e scalabilità automatica del servizio distribuito. La scalabilità automatica del cluster non è supportata tramite Azure Machine Learning SDK. Per cambiare i nodi nel cluster del servizio Azure Kubernetes, usare l'interfaccia utente del cluster nel portale di Azure. Il servizio Azure Kubernetes è l'unica opzione disponibile per la finestra di progettazione. |
| [Istanze di Azure Container](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Test o sviluppo | &nbsp;  | &nbsp; | Usare per carichi di lavoro basati su CPU su scala ridotta che richiedono meno di 48 GB di RAM. |
| [Cluster di elaborazione di Azure Machine Learning](../articles/machine-learning/how-to-use-parallel-run-step.md) | Inferenza&nbsp;batch | [Sì](../articles/machine-learning/how-to-use-parallel-run-step.md) (pipeline di Machine Learning) | &nbsp;  | Eseguire l'assegnazione di punteggi batch in un ambiente di calcolo serverless. Supporta VM con priorità normale e bassa. |
| [Funzioni di Azure](../articles/machine-learning/how-to-deploy-functions.md) | (Anteprima) Inferenza in tempo reale | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | (Anteprima) Modulo&nbsp;IoT |  &nbsp; | &nbsp; | Distribuire e gestire modelli di Machine Learning in dispositivi IoT. |
| [Azure Data Box Edge](../articles/databox-online/azure-stack-edge-overview.md)   | Tramite IoT Edge |  &nbsp; | Sì | Distribuire e gestire modelli di Machine Learning in dispositivi IoT. |

> [!NOTE]
> Anche se le destinazioni di calcolo, come l'ambiente di calcolo locale di Azure Machine Learning e i cluster di elaborazione di Azure Machine Learning, supportano l'uso di GPU per il training e la sperimentazione, l'uso di GPU per l'inferenza _se distribuita come servizio Web_ è supportato solo nel servizio Azure Kubernetes.
>
> L'uso di GPU per l'inferenza _per l'assegnazione di punteggi con una pipeline di Machine Learning_ è supportato solo nell'ambiente di calcolo di Machine Learning.

> [!NOTE]
> * Le istanze di contenitori sono indicate solo per modelli di dimensioni inferiori a 1 GB.
> * Usare cluster del servizio Azure Kubernetes a nodo singolo per attività di sviluppo/test di modelli più grandi.
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
ms.openlocfilehash: 0f519471ad9f3d451964ed70afea268946c14e59
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390620"
---
| Destinazione del calcolo | Utilizzato per | Supporto GPU | Supporto per FPGA | Descrizione |
| ----- | ----- | ----- | ----- | ----- |
| [Servizio&nbsp;Web&nbsp;locale](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Test/debug | &nbsp; | &nbsp; | Usare per i test e la risoluzione dei problemi limitati. L'accelerazione hardware dipende dall'uso di librerie nel sistema locale.
| [Servizio Web&nbsp;&nbsp;VM notebook](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Test/debug | &nbsp; | &nbsp; | Usare per i test e la risoluzione dei problemi limitati.
| [Servizio Azure Kubernetes](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Inferenza in tempo reale |  [Sì](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [Sì](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Usare per le distribuzioni di produzione su vasta scala. Fornisce tempi di risposta rapidi e scalabilità automatica del servizio distribuito. La scalabilità automatica del cluster non è supportata tramite il Azure Machine Learning SDK. Per modificare i nodi nel cluster AKS, usare l'interfaccia utente per il cluster AKS nell'portale di Azure. AKS è l'unica opzione disponibile per l'interfaccia visiva. |
| [Istanze di Azure Container](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Test o sviluppo | &nbsp;  | &nbsp; | Usare per carichi di lavoro basati su CPU su scala ridotta che richiedono meno di 48 GB di RAM. |
| [Ambiente di calcolo di Machine Learning](../articles/machine-learning/service/how-to-run-batch-predictions.md) | Anteprima Inferenza batch&nbsp; | &nbsp; | &nbsp;  | Eseguire il Punteggio batch su calcolo senza server. Supporta le macchine virtuali normali e con priorità bassa. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | Anteprima &nbsp;Modulo Internet delle cose |  &nbsp; | &nbsp; | Distribuire e gestire modelli di Machine Learning nei dispositivi Internet. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | Tramite IoT Edge |  &nbsp; | Sì | Distribuire e gestire modelli di Machine Learning nei dispositivi Internet. |

> [!NOTE]
> Sebbene le destinazioni di calcolo, ad esempio le VM locali, notebook e Azure Machine Learning il supporto di calcolo GPU per la formazione e la sperimentazione, l'uso della GPU per l'inferenza è supportato solo nel servizio Azure Kubernetes.
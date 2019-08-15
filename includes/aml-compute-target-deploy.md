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
ms.date: 05/30/2019
ms.openlocfilehash: c5a6f957cd3f799f9d7eff1df22df2200779ac30
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946023"
---
| Destinazione del calcolo | Utilizzo | Supporto GPU | Supporto per FPGA | Descrizione |
| ----- | ----- | ----- | ----- | ----- |
| [Servizio&nbsp;Web&nbsp;locale](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Test/debug | Forse | &nbsp; | Valido per test e risoluzione dei problemi limitati. L'accelerazione hardware dipende dall'uso di librerie nel sistema locale.
| [Servizio Web&nbsp;&nbsp;VM notebook](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Test/debug | Forse | &nbsp; | Valido per test e risoluzione dei problemi limitati. 
| [Servizio Azure Kubernetes](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Inferenza in tempo reale |  [sì](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [sì](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Soluzione ideale per le distribuzioni di produzione su vasta scala. Fornisce tempi di risposta rapidi e scalabilità automatica del servizio distribuito. La scalabilità automatica del cluster non è supportata tramite il Azure Machine Learning SDK. Per modificare i nodi nel cluster AKS, usare l'interfaccia utente per il cluster AKS nell'portale di Azure. AKS è l'unica opzione disponibile per l'interfaccia visiva. |
| [Istanze di Azure Container](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Test o sviluppo | &nbsp;  | &nbsp; | Ideale per carichi di lavoro basati su CPU e su scala ridotta che richiedono < 48 GB di RAM |
| [Ambiente di calcolo di Machine Learning](../articles/machine-learning/service/how-to-run-batch-predictions.md) | Anteprima Inferenza batch&nbsp; | sì | &nbsp;  | Eseguire il Punteggio batch su calcolo senza server. Supporta le macchine virtuali normali e con priorità bassa. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | Anteprima &nbsp;Modulo Internet delle cose |  &nbsp; | &nbsp; | Distribuire & di gestire i modelli ML nei dispositivi Internet delle cose. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | tramite IoT Edge |  &nbsp; | sì | Distribuire & di gestire i modelli ML nei dispositivi Internet delle cose. |

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
ms.openlocfilehash: 05082544830bad543d6743d016a91ef8565767f4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331662"
---
| Destinazione del calcolo | Uso | Supporto per GPU | Supporto FPGA | Descrizione |
| ----- | ----- | ----- | ----- | ----- |
| [Locale&nbsp;web&nbsp;servizio](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Test/debug | Forse | &nbsp; | Soluzione idonea per limitate di test e risoluzione dei problemi. L'accelerazione hardware dipende usando le librerie nel sistema locale.
| [Servizio Azure Kubernetes](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Inferenza in tempo reale |  [Sì](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [Sì](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Soluzione ideale per le distribuzioni di produzione su vasta scala. Offre tempi di risposta e la scalabilità automatica del servizio distribuito. La scalabilità automatica di cluster non è supportata tramite il SDK di Azure Machine Learning. Per modificare i nodi del cluster servizio contenitore di AZURE, usare l'interfaccia utente per il cluster servizio contenitore di AZURE nel portale di Azure. Servizio contenitore di AZURE è l'unica opzione disponibile per l'interfaccia visiva. |
| [Istanze di Azure Container](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Il test o sviluppo | &nbsp;  | &nbsp; | Ideale per scala ridotta, basate sulla CPU i carichi di lavoro che richiedono < 48 GB di RAM |
| [Ambiente di calcolo di Machine Learning](../articles/machine-learning/service/how-to-run-batch-predictions.md) | (Anteprima) Batch&nbsp;inferenza | sì | &nbsp;  | Esecuzione batch di assegnazione dei punteggi a risorse di calcolo senza server. Supporta le macchine virtuali con priorità bassa o normale. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | (Anteprima) IoT&nbsp;modulo |  &nbsp; | &nbsp; | Distribuire e gestire modelli di Machine Learning nei dispositivi IoT. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | tramite IoT Edge |  &nbsp; | sì | Distribuire e gestire modelli di Machine Learning nei dispositivi IoT. |

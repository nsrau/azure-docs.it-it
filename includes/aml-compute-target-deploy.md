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
ms.openlocfilehash: a463ac9f9584cb13cadbcf79674d56b2f8e47c2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66753092"
---
| Destinazione del calcolo | Uso | Supporto per GPU | Supporto FPGA | Descrizione |
| ----- | ----- | ----- | ----- | ----- |
| [Servizio web locale](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Test/debug | Forse | &nbsp; | Soluzione idonea per limitate di test e risoluzione dei problemi. L'accelerazione hardware dipende usando le librerie nel sistema locale.
| [Servizio Azure Kubernetes](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Inferenza in tempo reale |  [Sì](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [Sì](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Soluzione ideale per le distribuzioni di produzione su vasta scala. Fornisce la scalabilità automatica e tempi di risposta rapidi.  Servizio contenitore di AZURE è l'unica opzione disponibile per l'interfaccia visiva. |
| [Istanze di Azure Container](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Il test o sviluppo | &nbsp;  | &nbsp; | Ideale per scala ridotta, basate sulla CPU i carichi di lavoro che richiedono < 48 GB di RAM |
| [Ambiente di calcolo di Machine Learning](../articles/machine-learning/service/how-to-run-batch-predictions.md) | (Anteprima) Inferenza del batch | sì | &nbsp;  | Esecuzione batch di assegnazione dei punteggi a risorse di calcolo senza server. Supporta le macchine virtuali con priorità bassa o normale. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | (Anteprima) Modulo di IoT |  &nbsp; | &nbsp; | Distribuire e gestire modelli di Machine Learning nei dispositivi IoT. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | tramite IoT Edge |  &nbsp; | sì | Distribuire e gestire modelli di Machine Learning nei dispositivi IoT. |
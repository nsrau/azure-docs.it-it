---
title: Che cosa accadrà ad Azure Batch per intelligenza artificiale? | Microsoft Docs
description: Informazioni su ciò che accadrà ad Azure Batch per intelligenza artificiale e all'opzione di calcolo del servizio Azure Machine Learning.
services: batch-ai
author: garyericson
ms.service: batch-ai
ms.topic: overview
ms.date: 12/14/2018
ms.author: garye
ms.openlocfilehash: 6803a47ae77c072eff05df65e37156c90aabf3e6
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436875"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Che cosa accadrà ad Azure Batch per intelligenza artificiale?

**Il servizio Azure Batch per intelligenza artificiale verrà ritirato nel mese di marzo.** Le funzionalità di Batch per intelligenza artificiale per il training e l'assegnazione dei punteggi su larga scala sono ora disponibili nel [servizio Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md), diventato disponibile a livello generale il 4 dicembre 2018.

Oltre a molte altre funzionalità di apprendimento automatico, il servizio Azure Machine Learning include una destinazione di calcolo gestita e basata sul cloud per il training, la distribuzione e l'assegnazione dei punteggi ai modelli di Machine Learning. Questa destinazione di calcolo viene chiamata [ambiente di calcolo di Azure Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Avviare la migrazione e usarlo subito](#migrate). È possibile interagire con il servizio Azure Machine Learning tramite gli [SDK di Python](../machine-learning/service/quickstart-create-workspace-with-python.md), l'interfaccia della riga di comando e il [portale di Azure](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Sequenza temporale del supporto

| Data | Dettagli relativi al supporto per il servizio Batch per intelligenza artificiale |
| ---- |-----------------|
| Dicembre&nbsp;14&#x2c;&nbsp;2018| È possibile continuare a usare le sottoscrizioni esistenti di Azure Batch per intelligenza artificiale come prima. Tuttavia, la registrazione di **nuove sottoscrizioni** non è più possibile e non sono previsti nuovi investimenti per questo servizio.|
| Marzo&nbsp;31&#x2c;&nbsp;2019 | Dopo questa data, le sottoscrizioni esistenti di Batch per intelligenza artificiale non funzioneranno più. |

<a name="migrate"></a>
## <a name="how-do-i-migrate"></a>Come si esegue la migrazione?

Per evitare interruzioni delle applicazioni e trarre vantaggio dalle funzionalità più recenti, eseguire i passaggi di seguito prima del 31 marzo 2019:

1. Creare un'area di lavoro del servizio Azure Machine Learning e iniziare subito:
    + [Guida introduttiva basata su Python](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Guida introduttiva basata sul portale di Azure](../machine-learning/service/quickstart-get-started.md)

1. Configurare un [ambiente di calcolo di Azure Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute) per il training del modello.

1. Aggiornare gli script per l'utilizzo dell'ambiente di calcolo di Azure Machine Learning.

## <a name="support"></a>Supporto

Per i clienti esistenti che desiderano eseguire la migrazione al più completo [servizio Azure Machine Learning](https://aka.ms/aml-docs), è disponibile una sezione di supporto.

Se il servizio Azure Machine Learning non è in grado di soddisfare le esigenze ed esiste una funzionalità supportata nel servizio Batch per intelligenza artificiale, aprire una richiesta di supporto per il servizio Batch per intelligenza artificiale affinché il team di supporto possa autorizzare la sottoscrizione all'uso di Batch per intelligenza artificiale fino al ritiro del servizio.

## <a name="next-steps"></a>Passaggi successivi

+ Leggere la [panoramica del servizio Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Configurare una destinazione di calcolo per il training del modello](../machine-learning/service/how-to-set-up-training-targets.md) con il servizio Azure Machine Learning.

+ Rivedere la [roadmap per Azure](https://azure.microsoft.com/updates/) per informazioni su altri aggiornamenti del servizio di Azure.

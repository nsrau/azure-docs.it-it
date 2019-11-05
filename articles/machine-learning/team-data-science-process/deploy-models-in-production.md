---
title: Distribuire modelli nell'ambiente di produzione - Processo di data science per i team
description: Informazioni su come distribuire modelli nell'ambiente di produzione a supporto del processo decisionale dell'azienda.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/30/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 94843bfc30b0d0d44284d533c715ff9632e6a6bd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492365"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Distribuire modelli nell'ambiente di produzione a supporto del processo decisionale dell'azienda

La distribuzione nell'ambiente di produzione consente di usare attivamente un modello in un'azienda. Le stime effettuate da un modello distribuito possono essere usate per prendere decisioni aziendali.

## <a name="production-platforms"></a>Piattaforme di produzione

Ci sono diversi approcci e piattaforme per usare un modello in ambiente di produzione. Ecco alcune opzioni:

- [Dove distribuire i modelli con Azure Machine Learning](../service/how-to-deploy-and-where.md)
- [Distribuzione di un modello in SQL-server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Prima della distribuzione, è necessario assicurarsi che la latenza del punteggio del modello sia sufficientemente bassa per l'uso nell'ambiente di produzione.
>

>[!NOTE]
>Per la distribuzione con Azure Machine Learning Studio, vedere [Distribuire un servizio Web di Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>Test A/B

Quando nell'ambiente di produzione ci sono più modelli, può essere utile eseguire un [test A/B](https://en.wikipedia.org/wiki/A/B_testing) per confrontare le prestazioni dei modelli. 
 
## <a name="next-steps"></a>Passaggi successivi

Sono anche disponibili esercitazioni dettagliate che illustrano tutti i passaggi del processo per **scenari specifici**. Le esercitazioni sono elencate, con collegamenti e brevi descrizioni, nell'articolo [Procedure dettagliate di esempio](walkthroughs.md). Le esercitazioni spiegano come combinare strumenti cloud e locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente. 

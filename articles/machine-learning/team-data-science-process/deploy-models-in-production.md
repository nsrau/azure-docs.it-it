---
title: Distribuire modelli nell'ambiente di produzione - Azure Machine Learning | Microsoft Docs
description: Informazioni su come distribuire modelli nell'ambiente di produzione a supporto del processo decisionale dell'azienda.
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: bradsev
ms.openlocfilehash: 681f227a04802c4e27e42083c1eb63f620a8fc77
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2018
---
# <a name="deploy-models-in-production"></a>Distribuire modelli nell'ambiente di produzione

La distribuzione nell'ambiente di produzione consente di usare attivamente un modello in un'azienda. Le stime effettuate da un modello distribuito possono essere usate per prendere decisioni aziendali.

## <a name="production-platforms"></a>Piattaforme di produzione
Ci sono diversi approcci e piattaforme per usare un modello in ambiente di produzione. Ecco alcune opzioni:


- [Distribuzione di modelli in Azure Machine Learning](../desktop-workbench/model-management-overview.md)
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
 



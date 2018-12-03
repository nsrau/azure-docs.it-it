---
title: Distribuire modelli nell'ambiente di produzione - Azure Machine Learning | Microsoft Docs
description: Informazioni su come distribuire modelli nell'ambiente di produzione a supporto del processo decisionale dell'azienda.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/30/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 5b1614f92f7633e008f4f7176723002dc7730b15
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495990"
---
# <a name="deploy-models-in-production"></a>Distribuire modelli nell'ambiente di produzione

La distribuzione nell'ambiente di produzione consente di usare attivamente un modello in un'azienda. Le stime effettuate da un modello distribuito possono essere usate per prendere decisioni aziendali.

## <a name="production-platforms"></a>Piattaforme di produzione

Ci sono diversi approcci e piattaforme per usare un modello in ambiente di produzione. Ecco alcune opzioni:

- [Dove distribuire modelli con il servizio Azure Machine Learning](../service/how-to-deploy-and-where.md)
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

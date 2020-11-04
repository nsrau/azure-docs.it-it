---
title: Distribuire modelli nell'ambiente di produzione - Processo di data science per i team
description: Informazioni su come distribuire modelli nell'ambiente di produzione a supporto del processo decisionale dell'azienda.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 89ea1e991df46b4e4d23305b6118980b80c2f917
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321187"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Distribuire modelli nell'ambiente di produzione a supporto del processo decisionale dell'azienda

La distribuzione nell'ambiente di produzione consente di usare attivamente un modello in un'azienda. Le stime effettuate da un modello distribuito possono essere usate per prendere decisioni aziendali.

## <a name="production-platforms"></a>Piattaforme di produzione

Ci sono diversi approcci e piattaforme per usare un modello in ambiente di produzione. Ecco alcune opzioni:

- [Dove distribuire i modelli con Azure Machine Learning](../how-to-deploy-and-where.md)
- [Distribuzione di un modello in SQL-server](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Prima della distribuzione, è necessario assicurarsi che la latenza del punteggio del modello sia sufficientemente bassa per l'uso nell'ambiente di produzione.
>

>[!NOTE]
>Per la distribuzione con Azure Machine Learning Studio, vedere [Distribuire un servizio Web di Azure Machine Learning](../classic/deploy-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>Test A/B

Quando più modelli sono in produzione, è possibile usare [un test a/B](https://en.wikipedia.org/wiki/A/B_testing) per confrontare le prestazioni del modello. 
 
## <a name="next-steps"></a>Passaggi successivi

Sono anche disponibili esercitazioni dettagliate che illustrano tutti i passaggi del processo per **scenari specifici**. Le esercitazioni sono elencate, con collegamenti e brevi descrizioni, nell'articolo [Procedure dettagliate di esempio](walkthroughs.md). Le esercitazioni spiegano come combinare strumenti cloud e locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente.
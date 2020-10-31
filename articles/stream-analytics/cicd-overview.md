---
title: Integrazione e distribuzione continue per analisi di flusso di Azure
description: Questo articolo fornisce una panoramica di una pipeline di integrazione e distribuzione continua (CI/CD) per analisi di flusso di Azure.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: b5057eb8c84e839f504060228986ea759c8bdc3d
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123168"
---
# <a name="continuous-integration-and-deployment-cicd-for-azure-stream-analytics"></a>Integrazione e distribuzione continua (CI/CD) per analisi di flusso di Azure

È possibile distribuire il processo di analisi di flusso di Azure in modo continuo usando l'integrazione del controllo del codice sorgente. L'integrazione del controllo del codice sorgente consente un flusso di lavoro in cui un aggiornamento del codice attiva una distribuzione di risorse in Azure. Questo articolo illustra i passaggi di base per la creazione di una pipeline di integrazione e distribuzione continua (CI/CD).

Se non si ha familiarità con analisi di flusso di Azure, iniziare con la [Guida introduttiva di analisi di flusso di Azure](stream-analytics-quick-create-portal.md).

## <a name="create-a-cicd-pipeline"></a>Creare una pipeline CI/CD

Seguire i passaggi illustrati in questa guida per creare una pipeline di integrazione continua/distribuzione continua per analisi di flusso.

1. Sviluppare una query di analisi di flusso di Azure.

   Usare gli strumenti di analisi di flusso di Azure per [Visual Studio Code](./quick-create-visual-studio-code.md) o [Visual Studio](stream-analytics-quick-create-vs.md) per [sviluppare e testare le query in locale](develop-locally.md). È anche possibile [esportare un processo esistente](visual-studio-code-explore-jobs.md#export-a-job-to-a-local-project) in un progetto locale.

2. Eseguire il commit dei progetti di analisi di flusso di Azure nel sistema di controllo del codice sorgente, ad esempio un repository git.

3. Usare [gli strumenti](cicd-tools.md) di integrazione continua/distribuzione continua di analisi di flusso di Azure per compilare i progetti e generare modelli di Azure Resource Manager per la distribuzione.

4. Eseguire [test automatizzati](cicd-tools.md#automated-test) per la regressione della qualità.

5. [Distribuire automaticamente il processo](cicd-tools.md#deploy-to-azure) in Azure.

## <a name="auto-build-test-and-deploy"></a>Compilazione, test e distribuzione automatici

È possibile usare la riga di comando e [gli strumenti](cicd-tools.md) di integrazione continua/distribuzione continua di analisi di flusso di Azure per compilare, testare e distribuire automaticamente. È anche possibile configurare una pipeline CI/CD in [Azure Pipelines](set-up-cicd-pipeline.md). Azure Pipelines per abilitare funzionalità più avanzate, ad esempio la gestione delle pipeline, la visualizzazione e i trigger.

## <a name="next-steps"></a>Passaggi successivi

* [Automatizzare le compilazioni, i test e le distribuzioni di un processo di analisi di flusso di Azure usando gli strumenti CI/CD](cicd-tools.md)
* [Configurare una pipeline CI/CD per il processo di analisi di flusso usando Azure Pipelines](set-up-cicd-pipeline.md)
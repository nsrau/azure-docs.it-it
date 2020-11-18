---
title: Eseguire attività di data science - Processo di data science per i team
description: Informazioni su come un esperto di dati può eseguire un progetto di data science in modo tracciabile, collaborativo e con controllo della versione.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e47dad8498c48a5da5307517efe493fa5c1aa590
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94748064"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Eseguire attività di data science: esplorazione, modellazione e distribuzione

Le tipiche attività di data science includono esplorazione, modellazione e distribuzione. Questo articolo descrive le attività per completare diverse attività comuni di data science, ad esempio l'esplorazione interattiva dei dati, l'analisi dei dati, la creazione di report e la creazione di modelli. Le opzioni per la distribuzione di un modello in un ambiente di produzione possono includere:

- [Azure Machine Learning](../index.yml)
- [SQL-Server con Machine Learning Services](/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> esplorazione 

Un data scientist può esplorare i dati e creare report in diversi modi, usando le librerie e i pacchetti disponibili per Python (ad esempio matplotlib) o con R (ad esempio ggplot o reticolo). I data scientist possono personalizzare il codice in base alle esigenze di esplorazione dei dati per scenari specifici. Le esigenze quando si gestiscono i dati strutturati sono diverse rispetto a quelle per i dati non strutturati, ad esempio testo o immagini. 

I prodotti come Azure Machine Learning forniscono anche la [preparazione avanzata dei dati](../how-to-create-register-datasets.md) per data wrangling ed esplorazione, inclusa la creazione di funzionalità. L'utente deve scegliere gli strumenti, le librerie e i pacchetti più adatti alle proprie esigenze. 

Il risultato finale alla fine di questa fase è un report di esplorazione dei dati. Il report deve fornire una vista abbastanza completa dei dati da usare per la modellazione e una valutazione del fatto che i dati siano o meno adatti per procedere con il passaggio di modellazione. 

## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> modellazione

Ci sono numerosi toolkit e pacchetti per il training dei modelli in diversi linguaggi. I data scientist devono poter usare quelli che preferiscono, a condizione che vengano soddisfatte le considerazioni sulle prestazioni relative all'accuratezza e alla latenza per i casi d'uso aziendali e gli scenari di produzione pertinenti.

### <a name="model-management"></a>Gestione di modelli
Dopo aver creato più modelli, è in genere necessario usare un sistema per registrarli e gestirli. Solitamente, sono necessari una combinazione di script o API e un database back-end o un sistema di controllo delle versioni. Ecco alcune opzioni che è possibile prendere in considerazione per queste attività di gestione:

1. [Azure Machine Learning - servizio Gestione modelli](../index.yml)
2. [ModelDB del MIT](http://modeldb.csail.mit.edu:3000/projects) 
3. [SQL Server come sistema di gestione dei modelli](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> distribuzione

La distribuzione nell'ambiente di produzione consente di usare attivamente un modello in un'azienda. Le stime effettuate da un modello distribuito possono essere usate per prendere decisioni aziendali.

### <a name="production-platforms"></a>Piattaforme di produzione
Ci sono diversi approcci e piattaforme per usare un modello in ambiente di produzione. Ecco alcune opzioni:


- [Distribuzione di modelli in Azure Machine Learning](../how-to-deploy-and-where.md)
- [Distribuzione di un modello in SQL-server](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Prima della distribuzione, è necessario assicurarsi che la latenza del punteggio del modello sia sufficientemente bassa per l'uso nell'ambiente di produzione.
>
>

Sono disponibili altri esempi in esercitazioni dettagliate che illustrano tutti i passaggi del processo per **scenari specifici**. Le esercitazioni sono elencate, con collegamenti e brevi descrizioni, nell'articolo [Procedure dettagliate di esempio](walkthroughs.md). Le esercitazioni spiegano come combinare strumenti cloud e locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente.

> [!NOTE]
> Per la distribuzione con Azure Machine Learning Studio, vedere [Distribuire un servizio Web di Azure Machine Learning](../classic/deploy-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>Test A/B
Quando nell'ambiente di produzione ci sono più modelli, può essere utile eseguire un [test A/B](https://en.wikipedia.org/wiki/A/B_testing) per confrontare le prestazioni dei modelli. 

 
## <a name="next-steps"></a>Passaggi successivi

L'articolo [Track progress of data science projects](track-progress.md) (Monitorare i progetti di data science) illustra in che modo un data scientist può monitorare l'avanzamento di un progetto di data science.

L'articolo [Model operation and CI/CD](ci-cd-flask.md) (Operazione modello e CI/CD) illustra come eseguire la CI/CD con i modelli sviluppati.

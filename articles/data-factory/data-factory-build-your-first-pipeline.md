---
title: 'Esercitazione su Data Factory: prima pipeline di dati | Documentazione Microsoft'
description: Questa esercitazione di Azure Data Factory illustra come creare e pianificare una data factory che elabora i dati usando uno script Hive in un cluster Hadoop.
services: data-factory
keywords: esercitazione di azure data factory, cluster hadoop, hive di hadoop
documentationcenter: 
author: spelluru
manager: jhubbard
editor: 
ms.assetid: 81f36c76-6e78-4d93-a3f2-0317b413f1d0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: spelluru
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: b01a03fa362f8a9677e625945a2864c77d49f1bb
ms.contentlocale: it-it
ms.lasthandoff: 06/14/2017

---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Esercitazione: Creare la prima pipeline per trasformare i dati usando il cluster Hadoop
> [!div class="op_single_selector"]
> * [Panoramica e prerequisiti](data-factory-build-your-first-pipeline.md)
> * [Portale di Azure](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Modello di Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

In questa esercitazione si compila la prima data factory di Azure con una pipeline di dati. La pipeline trasforma i dati di input tramite l'esecuzione di script Hive su un cluster Azure HDInsight (Hadoop) per generare i dati di output.  

Questo articolo fornisce una panoramica e i prerequisiti per l'esercitazione. Dopo avere completato i prerequisiti, è possibile eseguire l'esercitazione usando uno degli strumenti/SDK seguenti: Portale di Azure, Visual Studio, PowerShell, modello di Resource Manager, API REST. Selezionare una delle opzioni nell'elenco a discesa all'inizio o i collegamenti alla fine di questo articolo per eseguire l'esercitazione sull'uso di una di queste opzioni.    

## <a name="tutorial-overview"></a>Panoramica dell'esercitazione
In questa esercitazione si segue questa procedura:

1. Creare una **data factory**. Una data factory può contenere una o più pipeline di dati che spostano e trasformano i dati. 

    In questa esercitazione si creerà una pipeline nella data factory. 
2. Creare una **pipeline**. Una pipeline può comprendere una o più attività (esempi: attività di copia, attività Hive HDInsight). In questo esempio viene usata un'attività Hive di HDInsight che esegue uno script Hive in un cluster Hadoop di HDInsight. Lo script crea prima di tutto una tabella che fa riferimento ai dati di blog non elaborati contenuti nell'archivio BLOB di Azure e quindi esegue il partizionamento dei dati non elaborati per anno e per mese.

    In questa esercitazione, la pipeline usa l'attività Hive per trasformare i dati eseguendo una query Hive in un cluster Hadoop di HDInsight di Azure. 
3. Creare **servizi collegati**. Creare un servizio collegato per collegare un archivio dati o un servizio di calcolo alla data factory. Un archivio dati come Archiviazione di Azure contiene i dati di input/output delle attività nella pipeline. Un servizio di calcolo come un cluster Hadoop di HDInsight elabora/trasforma i dati.

    In questa esercitazione guidata si creano due servizi collegati : **Archiviazione di Azure** e **Azure HDInsight**. Il servizio collegato Archiviazione di Azure collega un account di archiviazione di Azure contenente i dati di input/output alla data factory. Il servizio collegato Azure HDInsight collega un cluster HDInsight di Azure usato per trasformare i dati alla data factory. 
3. Creare **set di dati**di input e di output. Un set di dati di input rappresenta l'input per un'attività nella pipeline, un set di dati di output rappresenta l'output dell'attività.

    In questa esercitazione i set di dati di input e output specificano i percorsi dei dati di input e output nell'Archiviazione BLOB di Azure. Il servizio collegato Archiviazione di Azure specifica l'account di archiviazione di Azure che viene usato. Un set di dati di input specifica la posizione in cui si trovano i file di input e un set di dati di output specifica la posizione in cui verranno inseriti i file di output. 


Per una panoramica dettagliata di Azure Data Factory, vedere l'articolo [Introduzione al servizio Azure Data Factory](data-factory-introduction.md) .
  
Di seguito è riportata la **vista diagramma** della data factory di esempio creata in questa esercitazione. **MyFirstPipeline** ha un'attività di tipo Hive che usa i set di dati **AzureBlobInput** come input e produce set di dati **AzureBlobOutput** come output. 

![Vista diagramma nell'esercitazione su Data Factory](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


In questa esercitazione la cartella **inputdata** del contenitore BLOB di Azure **adfgetstarted** contiene un file denominato input.log. Questo file di log contiene voci relative ai tre mesi di gennaio, febbraio e marzo 2016. Ecco le righe di esempio per ogni mese nel file di input. 

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Quando il file viene elaborato dalla pipeline con attività Hive di HDInsight, l'attività esegue uno script Hive nel cluster HDInsight che esegue il partizionamento dei dati di input per anno e per mese. Lo script crea tre cartelle di output che contengono un file con le voci di ogni mese.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

Delle righe di esempio riportate sopra, la prima (con 2016-01-01) viene scritta nel file 000000_0 nella cartella month=1. Allo stesso modo, la seconda viene scritta nel file nella cartella month=2 e la terza viene scritta nel file nella cartella month=3.  

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, sono necessari i prerequisiti seguenti:

1. **Sottoscrizione di Azure** : se non è disponibile una sottoscrizione di Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Vedere l'articolo [Versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/) per informazioni su come ottenere un account di valutazione gratuito.
2. **Archiviazione di Azure** : in questa esercitazione si usa un account di archiviazione di Azure per archiviare i dati. Se non si dispone di un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione di Azure](../storage/storage-create-storage-account.md#create-a-storage-account) . Dopo aver creato l'account di archiviazione, annotare il **nome dell'account** e la **chiave di accesso**. Vedere [Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](../storage/storage-create-storage-account.md#view-and-copy-storage-access-keys).
3. Scaricare e leggere il file di query Hive (**HQL**) disponibile all'indirizzo: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql). Questa query trasforma i dati di input per generare i dati di output. 
4. Scaricare e leggere il file di input di esempio (**input.log**) disponibile all'indirizzo: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Creare un contenitore BLOB denominato **adfgetstarted** nell'Archiviazione BLOB di Azure. 
6. Caricare il file **partitionweblogs.hql** nella cartella **script** nel contenitore **adfgetstarted**. Usare strumenti come [Esplora archivi di Microsoft Azure](http://storageexplorer.com/). 
7. Caricare il file**input.log** nella cartella **inputdata** nel contenitore **adfgetstarted**. 

Dopo avere completato i prerequisiti, selezionare uno dei seguenti strumenti/SDK per eseguire l'esercitazione: 

- [Portale di Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modello di Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
- [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

Il portale di Azure e Visual Studio forniscono l'interfaccia utente grafica per la creazione di data factory. Le opzioni PowerShell, il modello di Resource Manager e l'API REST forniscono una modalità di programmazione/script per la creazione di data factory.

> [!NOTE]
> La pipeline di dati in questa esercitazione trasforma i dati di input per produrre dati di output. Non copia dati da un archivio dati di origine a un archivio dati di destinazione. Per un'esercitazione su come copiare dati usando Azure Data Factory, vedere [Copiare dati da un archivio BLOB al database SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> È possibile concatenare due attività, ovvero eseguire un'attività dopo l'altra, impostando il set di dati di output di un'attività come set di dati di input di altre attività. Per informazioni dettagliate, vedere [Pianificazione ed esecuzione con Data Factory](data-factory-scheduling-and-execution.md). 





  


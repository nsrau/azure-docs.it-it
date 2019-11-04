---
title: 'Importa dati: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Import Data (Importa dati) in Azure Machine Learning per caricare i dati in una pipeline di machine learning da servizi dati cloud esistenti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 5fa8d3984c758d0bf95372864f3bffeb6f302c83
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497816"
---
# <a name="import-data-module"></a>Modulo Import Data

Questo articolo descrive un modulo in Azure Machine Learning Designer (anteprima).

Usare questo modulo per caricare i dati in una pipeline di machine learning da servizi dati cloud esistenti. 

> [!Note]
> Tutte le funzionalità fornite da questo modulo possono essere eseguite da **archivio dati** e **set** di dati nella pagina di destinazione Workspace. Si consiglia di usare **datastore** e **DataSet** che include funzionalità aggiuntive, ad esempio il monitoraggio dei dati. Per altre informazioni, vedere [How to Access Data](../service/how-to-access-data.md) e [How to register DataSets](../service/how-to-create-register-datasets.md) article.
> Dopo aver registrato un set di dati, è possibile trovarlo nella categoria **datasets** -> **My Datasets** nell'interfaccia della finestra di progettazione. Questo modulo è riservato agli utenti di studio (classico) per un'esperienza familiare. 
>

Prima di tutto, scegliere l'origine da leggere e completare le impostazioni aggiuntive. Il modulo **Import Data** supporta la lettura dei dati dalle origini seguenti:

- URL tramite HTTP
- Archiviazione cloud di Azure tramite [**archivi dati**](../service/how-to-access-data.md)
    - Contenitore BLOB di Azure
    - Condivisione file di Azure
    - Azure Data Lake
    - Azure Data Lake Gen2
    - database SQL di Azure
    - PostgreSQL di Azure    

Prima di usare l'archiviazione cloud, prima di tutto è necessario registrare un archivio dati nell'area di lavoro Azure Machine Learning. Per ulteriori informazioni, vedere [la pagina relativa alla modalità di accesso ai dati](../service/how-to-access-data.md). 

Dopo aver definito i dati desiderati e averli connessi all'origine, l' **[importazione dei dati](./import-data.md)** deduce il tipo di dati di ogni colonna in base ai valori che contiene e carica i dati nella pipeline di progettazione. L'output dei **dati di importazione** è un set di dati che può essere usato con qualsiasi pipeline della finestra di progettazione.

Se i dati di origine cambiano, è possibile aggiornare il set di dati e aggiungere nuovi dati rieseguendo [Import Data](./import-data.md). Tuttavia, se non si vuole rileggere dall'origine ogni volta che si esegue la pipeline, impostare l'opzione **USA risultati memorizzati nella cache** su true. Quando questa opzione è selezionata, il modulo controlla se la pipeline è stata eseguita in precedenza utilizzando la stessa origine e le stesse opzioni di input. Se viene trovata un'esecuzione precedente, vengono usati i dati nella cache, invece di ricaricare i dati dall'origine.

## <a name="how-to-configure-import-data"></a>Come configurare i dati di importazione

1. Aggiungere il modulo **Import Data (Importa dati** ) alla pipeline. È possibile trovare questo modulo nella categoria **input e output dei dati** nella finestra di progettazione.

1. Fare clic su **Avvia importazione guidata dati** per configurare l'origine dati utilizzando una procedura guidata.

    La procedura guidata ottiene il nome e le credenziali dell'account e consente di configurare altre opzioni. Se si sta modificando una configurazione esistente, vengono caricati prima i valori correnti.

1. Selezionare **origine dati**e scegliere il tipo di origine dati. Potrebbe essere HTTP o archivio dati.

    Se si sceglie archivio dati, è possibile selezionare gli archivi dati esistenti che sono già stati registrati nell'area di lavoro di Azure Machine Learning o creare un nuovo archivio dati. Definire quindi il percorso dei dati da importare nell'archivio dati. È possibile esplorare facilmente il percorso facendo clic su **Esplora percorso** ![Import-Data-Path](media/module/import-data-path.png)

1. Selezionare lo schema di anteprima per filtrare le colonne che si desidera includere. È anche possibile definire impostazioni avanzate come delimitatore nelle opzioni di analisi.

    ![importazione-dati-anteprima](media/module/import-data.png)

1. Selezionare l'opzione **USA risultati memorizzati nella cache** se si desidera memorizzare nella cache il set di dati per il riutilizzo in esecuzioni successive.

    Supponendo che non siano state apportate altre modifiche ai parametri del modulo, la pipeline carica i dati solo la prima volta che il modulo viene eseguito e successivamente usa una versione memorizzata nella cache del set di dati.

    Deselezionare questa opzione se è necessario ricaricare i dati ogni volta che si esegue la pipeline.

1. Eseguire la pipeline.

    Quando Import data carica i dati nella finestra di progettazione, deduce il tipo di dati di ogni colonna in base ai valori che contiene, numerici o categorici.

    Se è presente un'intestazione, l'intestazione viene utilizzata per assegnare un nome alle colonne del set di dati di output.

    Se nei dati non sono presenti intestazioni di colonna, i nuovi nomi di colonna vengono generati utilizzando il formato col1, Col2,... , Coln *.

## <a name="results"></a>Risultati

Al termine dell'importazione, fare clic sul set di dati di output e selezionare **Visualizza** per verificare se i dati sono stati importati correttamente.

Se si desidera salvare i dati per riutilizzarli, anziché importare un nuovo set di dati ogni volta che viene eseguita la pipeline, fare clic con il pulsante destro del mouse sull'output e selezionare **Salva come DataSet**. Scegliere un nome per il set di dati. Il set di dati salvato conserva i dati al momento del salvataggio e i dati non vengono aggiornati quando viene rieseguita la pipeline, anche se il set di dati nella pipeline viene modificato. Questo può essere utile per acquisire snapshot dei dati.

Dopo aver importato i dati, potrebbero essere necessarie alcune preparazioni aggiuntive per la modellazione e l'analisi:

- Utilizzare [Modifica metadati](./edit-metadata.md) per modificare i nomi delle colonne, per gestire una colonna con un tipo di dati diverso o per indicare che alcune colonne sono etichette o funzionalità.

- Utilizzare [Seleziona colonne nel set di dati](./select-columns-in-dataset.md) per selezionare un subset di colonne da trasformare o utilizzare nella modellazione. È possibile riaggiungere facilmente le colonne trasformate o rimosse al set di dati originale usando il modulo [Add columns](./add-columns.md) .  

- Usare [Partition and Sample](./partition-and-sample.md) per dividere il set di dati, eseguire il campionamento o ottenere le prime n righe.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 

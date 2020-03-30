---
title: 'Importa dati: Riferimento modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Importa dati in Azure Machine Learning per caricare dati in una pipeline di Machine Learning dai servizi dati cloud esistenti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: d124fdc15bd34743b237985a66cc35625f5d9a4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456200"
---
# <a name="import-data-module"></a>Modulo Importa dati

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Usare questo modulo per caricare i dati in una pipeline di Machine Learning dai servizi dati cloud esistenti. 

> [!Note]
> Tutte le funzionalità fornite da questo modulo possono essere eseguite **dall'archivio dati** e dai **set di dati** nella pagina di destinazione di worksapce. È consigliabile usare **l'archivio dati** e **il set di dati** che includono funzionalità aggiuntive, ad esempio il monitoraggio dei dati. Per altre informazioni, vedere l'articolo [Come accedere ai dati](../how-to-access-data.md) e Come [registrare i set di dati.](../how-to-create-register-datasets.md)
> Dopo aver registrato un set di dati, è possibile trovarlo nella categoria **Dataset** -> **My Datasets** nell'interfaccia della finestra di progettazione. Questo modulo è riservato agli utenti di Studio (classico) per un'esperienza familiare. 
>

Il modulo Importa dati supporta la lettura dei dati dalle origini seguenti:The **Import Data** module support read data from following sources:

- URL tramite HTTP
- Archiviazione cloud di Azure tramite [**Datastore**](../how-to-access-data.md))
    - Contenitore BLOB di AzureAzure Blob Container
    - Condivisione file di Azure
    - Azure Data Lake
    - Azure Data Lake Gen2
    - database SQL di Azure
    - Azure PostgreSQL    

Prima di usare l'archiviazione cloud, è necessario registrare prima un archivio dati nell'area di lavoro di Azure Machine Learning.Before using cloud storage, you need to register a datastore in your Azure Machine Learning workspace. Per ulteriori informazioni, vedere [Come accedere ai dati](../how-to-access-data.md). 

Dopo aver definito i dati desiderati e averli connessi all'origine, **[Importa dati](./import-data.md)** deduce il tipo di dati di ogni colonna in base ai valori in essa contenuti e carica i dati nella pipeline di progettazione. L'output di **Importa dati** è un set di dati che può essere usato con qualsiasi pipeline di progettazione.

Se i dati di origine vengono modificati, è possibile aggiornare il set di dati e aggiungere nuovi dati eseguendo nuovamente [Importa dati](./import-data.md).

## <a name="how-to-configure-import-data"></a>Come configurare I dati di importazione

1. Aggiungere il modulo **Importa dati** alla pipeline. È possibile trovare questo modulo nella categoria **Input dati e output** nella finestra di progettazione.

1. Selezionare il modulo per aprire il riquadro di destra.

1. Selezionare **Origine dati**e scegliere il tipo di origine dati. Potrebbe essere HTTP o datastore.

    Se si sceglie l'archivio dati, è possibile selezionare archivi dati esistenti già registrati nell'area di lavoro di Azure Machine Learning o creare un nuovo archivio dati. Definire quindi il percorso dei dati da importare nell'archivio dati. È possibile sfogliare facilmente il percorso facendo clic su **Sfoglia percorso** ![import-data-path](media/module/import-data-path.png)

1. Selezionare lo schema di anteprima per filtrare le colonne da includere. È inoltre possibile definire impostazioni avanzate come Delimitatore nelle opzioni di analisi.

    ![importazione-dati-anteprima](media/module/import-data.png)

1. La casella di controllo **Rigenera output**, decide se eseguire il modulo per rigenerare l'output in fase di esecuzione. 

    Per impostazione predefinita è deselezionata, il che significa che se il modulo è stato eseguito con gli stessi parametri in precedenza, il sistema riutilizzerà l'output dell'ultima esecuzione per ridurre la fase di esecuzione. 

    Se è selezionata, il sistema eseguirà nuovamente il modulo per rigenerare l'output. Quindi selezionare questa opzione quando i dati sottostanti nell'archiviazione vengono aggiornati, può aiutare a ottenere i dati più recenti.


1. Inviare la pipeline.

    Quando Importa dati carica i dati nella finestra di progettazione, deduce il tipo di dati di ogni colonna in base ai valori in essa contenuti, numerici o categorici.

    Se è presente un'intestazione, questa viene usata per assegnare un nome alle colonne dal set di dati di output.

    Se nei dati non sono presenti intestazioni di colonna esistenti, i nuovi nomi di colonna vengono generati utilizzando il formato col1, col2,... , coln.

## <a name="results"></a>Risultati

Al termine dell'importazione, fare clic sul set di dati di output e selezionare **Visualizza** per verificare se i dati sono stati importati correttamente.

Se si desidera salvare i dati per riutilizzarli, anziché importare un nuovo set di dati ogni volta che viene eseguita la pipeline, selezionare l'icona **Registra set** di dati nella scheda **Output** nel riquadro destro del modulo. Scegliere un nome per il set di dati. Il set di dati salvato mantiene i dati al momento del salvataggio, il set di dati non viene aggiornato quando la pipeline viene rieseguita, anche se il set di dati nella pipeline viene modificato. Ciò può essere utile per l'acquisizione di snapshot di dati.

Dopo aver importato i dati, potrebbero essere necessari alcuni preparativi aggiuntivi per la modellazione e l'analisi:After importing the data, it might need some additional preparations for modeling and analysis:

- Utilizzare [Modifica metadati](./edit-metadata.md) per modificare i nomi delle colonne, per gestire una colonna come un tipo di dati diverso o per indicare che alcune colonne sono etichette o caratteristiche.

- Usare [Seleziona colonne nel set di dati](./select-columns-in-dataset.md) per selezionare un sottoinsieme di colonne da trasformare o usare nella modellazione. Le colonne trasformate o rimosse possono essere facilmente riunite al set di dati originale utilizzando il modulo [Aggiungi colonne.](./add-columns.md)  

- Usare [Partizione ed Esempio](./partition-and-sample.md) per dividere il set di dati, eseguire il campionamento o ottenere le prime n righe.

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 

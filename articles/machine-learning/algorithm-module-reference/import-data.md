---
title: 'Importa dati: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Import Data (Importa dati) in Azure Machine Learning per caricare i dati in una pipeline di machine learning da servizi dati cloud esistenti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 68bf47f370a57fe277d4ab9679af0b920a0d81ec
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658123"
---
# <a name="import-data-module"></a>Modulo Import Data

Questo articolo descrive un modulo disponibile nella finestra di progettazione di Azure Machine Learning (anteprima).

Usare questo modulo per caricare i dati in una pipeline di machine learning da servizi dati cloud esistenti. 

> [!Note]
> Tutte le funzionalità fornite da questo modulo possono essere eseguite da **archivio dati** e **set** di dati nella pagina di destinazione Workspace. Si consiglia di usare **datastore** e **DataSet** che include funzionalità aggiuntive, ad esempio il monitoraggio dei dati. Per altre informazioni, vedere [How to Access Data](../how-to-access-data.md) e [How to register DataSets](../how-to-create-register-datasets.md) article.
> Dopo aver registrato un set di dati, è possibile trovarlo nella categoria **DataSets**  ->  **My** DataSets dell'interfaccia della finestra di progettazione. Questo modulo è riservato agli utenti di studio (classico) per un'esperienza familiare. 
>

Il modulo **Import Data** supporta la lettura dei dati dalle origini seguenti:

- URL tramite HTTP
- Archiviazione cloud di Azure tramite [**archivi dati**](../how-to-access-data.md)
    - Contenitore BLOB di Azure
    - Condivisione file di Azure
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Database SQL di Azure
    - PostgreSQL di Azure    

Prima di usare l'archiviazione cloud, prima di tutto è necessario registrare un archivio dati nell'area di lavoro Azure Machine Learning. Per ulteriori informazioni, vedere [la pagina relativa alla modalità di accesso ai dati](../how-to-access-data.md). 

Dopo aver definito i dati desiderati e averli connessi all'origine, l' **[importazione dei dati](./import-data.md)** deduce il tipo di dati di ogni colonna in base ai valori che contiene e carica i dati nella pipeline di progettazione. L'output dei **dati di importazione** è un set di dati che può essere usato con qualsiasi pipeline della finestra di progettazione.

Se i dati di origine cambiano, è possibile aggiornare il set di dati e aggiungere nuovi dati rieseguendo [Import Data](./import-data.md).

> [!WARNING]
> Se l'area di lavoro si trova in una rete virtuale, è necessario configurare gli archivi dati per l'uso delle funzionalità di visualizzazione dei dati della finestra di progettazione. Per altre informazioni su come usare gli archivi dati e i set di dati in una rete virtuale, vedere [usare Azure Machine Learning Studio in una rete virtuale di Azure](../how-to-enable-studio-virtual-network.md).


## <a name="how-to-configure-import-data"></a>Come configurare i dati di importazione

1. Aggiungere il modulo **Import Data (Importa dati** ) alla pipeline. È possibile trovare questo modulo nella categoria **input e output dei dati** nella finestra di progettazione.

1. Selezionare il modulo per aprire il riquadro di destra.

1. Selezionare **origine dati**e scegliere il tipo di origine dati. Potrebbe essere HTTP o archivio dati.

    Se si sceglie archivio dati, è possibile selezionare gli archivi dati esistenti che sono già stati registrati nell'area di lavoro di Azure Machine Learning o creare un nuovo archivio dati. Definire quindi il percorso dei dati da importare nell'archivio dati. È possibile esplorare facilmente il percorso facendo clic su **Esplora percorso** ![ Import-Data-Path](media/module/import-data-path.png)

1. Selezionare lo schema di anteprima per filtrare le colonne che si desidera includere. È anche possibile definire impostazioni avanzate come delimitatore nelle opzioni di analisi.

    ![importazione-dati-anteprima](media/module/import-data.png)

1. La casella di controllo **Rigenera output**, decide se eseguire il modulo per rigenerare l'output in fase di esecuzione. 

    Per impostazione predefinita, è deselezionata, ovvero se il modulo è stato eseguito con gli stessi parametri in precedenza, il sistema riutilizzerà l'output dell'ultima esecuzione per ridurre il tempo di esecuzione. 

    Se questa opzione è selezionata, il sistema eseguirà di nuovo il modulo per rigenerare l'output. Quindi, selezionare questa opzione quando vengono aggiornati i dati sottostanti nell'archiviazione, che possono essere utili per ottenere i dati più recenti.


1. Inviare la pipeline.

    Quando Import data carica i dati nella finestra di progettazione, deduce il tipo di dati di ogni colonna in base ai valori che contiene, numerici o categorici.

    Se è presente un'intestazione, questa viene usata per assegnare un nome alle colonne dal set di dati di output.

    Se nei dati non sono presenti intestazioni di colonna, i nuovi nomi di colonna vengono generati utilizzando il formato col1, Col2,... , Coln *.

## <a name="results"></a>Risultati

Al termine dell'importazione, fare clic sul set di dati di output e selezionare **Visualizza** per verificare se i dati sono stati importati correttamente.

Se si desidera salvare i dati per riutilizzarli, anziché importare un nuovo set di dati ogni volta che viene eseguita la pipeline, selezionare l'icona **registra set** di dati nella scheda **output** nel riquadro di destra del modulo. Scegliere un nome per il set di dati. Il set di dati salvato conserva i dati al momento del salvataggio, il set di dati non viene aggiornato quando viene rieseguita la pipeline, anche se il set di dati nella pipeline viene modificato. Questa operazione può essere utile per acquisire snapshot dei dati.

Dopo aver importato i dati, potrebbero essere necessarie alcune preparazioni aggiuntive per la modellazione e l'analisi:

- Utilizzare [Modifica metadati](./edit-metadata.md) per modificare i nomi delle colonne, per gestire una colonna con un tipo di dati diverso o per indicare che alcune colonne sono etichette o funzionalità.

- Utilizzare [Seleziona colonne nel set di dati](./select-columns-in-dataset.md) per selezionare un subset di colonne da trasformare o utilizzare nella modellazione. È possibile riaggiungere facilmente le colonne trasformate o rimosse al set di dati originale usando il modulo [Add columns](./add-columns.md) .  

- Usare [Partition and Sample](./partition-and-sample.md) per dividere il set di dati, eseguire il campionamento o ottenere le prime n righe.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 

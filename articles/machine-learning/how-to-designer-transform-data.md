---
title: Trasformare i dati nella finestra di progettazione
titleSuffix: Azure Machine Learning
description: Informazioni su come trasformare i dati nella finestra di progettazione Azure Machine Learning per creare set di dati personalizzati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: peterclu
ms.author: peterlu
ms.date: 06/28/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: f08d0f1be166630d9cf4b0b9236d78228fd78aae
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93312812"
---
# <a name="transform-data-in-azure-machine-learning-designer"></a>Trasformare i dati in Azure Machine Learning Designer


Questo articolo illustra come trasformare e salvare i set di dati nella finestra di progettazione Azure Machine Learning in modo da poter preparare dati personalizzati per il Machine Learning.

Verrà usato il set di dati campione [Adult Census Income Binary Classification](./samples-designer.md) per preparare due set di dati: uno che include le informazioni sul censimento solo degli adulti statunitensi e un altro set di dati che include le informazioni sul censimento degli adulti non statunitensi.

In questo articolo vengono illustrate le operazioni seguenti:

1. Trasformare un set di dati per prepararlo per il training.
1. Esportare i set di dati risultanti in un archivio dati.
1. Visualizzare i risultati.

Questa procedura è un prerequisito per l'articolo [Come ripetere il training dei modelli di progettazione](how-to-retrain-designer.md). Questo articolo offre informazioni su come usare i set di dati trasformati per eseguire il training di più modelli con parametri pipeline.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="transform-a-dataset"></a>Trasformare un set di dati

Questa sezione descrive come importare il set di dati campione e suddividere i dati in set di dati statunitensi e non statunitensi. Per altre informazioni su come importare dati personali nella finestra di progettazione, vedere [Come importare i dati](how-to-designer-import-data.md).

### <a name="import-data"></a>Importa dati

Seguire i passaggi seguenti per importare il set di dati campione.

1. Accedere a <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a> e selezionare l'area di lavoro che si vuole usare.

1. Accedere alla finestra di progettazione. Selezionare **Moduli di precompilazione facili da usare** per creare una nuova pipeline.

1. Selezionare una destinazione di calcolo predefinita per eseguire la pipeline.

1. A sinistra del canvas della pipeline è presente un pannello di set di dati e moduli. Selezionare **Set di dati**. Quindi, visualizzare la sezione **Campioni**.

1. Trascinare e rilasciare il set di dati **Adult Census Income Binary classification** nel canvas.

1. Selezionare il modulo del set di dati **Adult Census Income**.

1. Nel riquadro dei dettagli che viene visualizzato a destra del canvas selezionare **Output**.

1. Selezionare l'icona di visualizzazione ![icona di visualizzazione](media/how-to-designer-transform-data/visualize-icon.png).

1. Usare la finestra di anteprima dei dati per esplorare il set di dati. Prendere nota speciale dei valori della colonna "native-country".

### <a name="split-the-data"></a>Dividere i dati

In questa sezione si usa il [modulo Split Data](algorithm-module-reference/split-data.md) per identificare e dividere le righe che contengono "United-States" nella colonna "native-country". 

1. Nel riquadro dei moduli a sinistra del canvas espandere la sezione **Trasformazione dati** e individuare il modulo **Split Data**.

1. Trascinare il modulo **Split Data** nel canvas e rilasciarlo sotto il modulo del set di dati.

1. Collegare il modulo del set di dati al modulo **Split Data**.

1. Selezionare il modulo **Split Data**.

1. Nel riquadro dei dettagli del modulo a destra del canvas impostare **Splitting mode** su **Espressione regolare**.

1. Immettere l' **Espressione regolare** : `\"native-country" United-States`.

    La modalità **Espressione regolare** esegue il test di una singola colonna per un valore. Per altre informazioni sul modulo Split Data, vedere la [pagina di riferimento del modulo algoritmo](algorithm-module-reference/split-data.md) correlata.

La pipeline dovrebbe avere un aspetto simile al seguente:

:::image type="content" source="./media/how-to-designer-transform-data/split-data.png"alt-text="Screenshot che illustra come configurare la pipeline e il modulo Split Data":::


## <a name="save-the-datasets"></a>Salvare i set di dati

Ora che la pipeline è configurata per dividere i dati, è necessario specificare la posizione in cui salvare i set di dati. Per questo esempio, usare il modulo **Export Data** per salvare il set di dati in un archivio dati. Per altre informazioni sugli archivi dati, vedere [Connettersi ai servizi di archiviazione di Azure](how-to-access-data.md)

1. Nel riquadro del modulo a sinistra del canvas espandere la sezione **Input e output dati** e individuare il modulo **Export Data**.

1. Trascinare e rilasciare due moduli **Export Data** sotto il modulo **Split Data**.

1. Connettere ogni porta di output del modulo **Split Data** a un diverso modulo **Export Data**.

    La pipeline avrà un aspetto simile al seguente:

    ![Screenshot che illustra come connettere i moduli Export Data](media/how-to-designer-transform-data/export-data-pipeline.png).

1. Seleziona il modulo **Export Data** connesso alla porta più a *sinistra* del modulo **Split Data**.

    L'ordine delle porte di output è rilevante per il modulo **Split Data**. La prima porta di output contiene le righe in cui l'espressione regolare è true. In questo caso, la prima porta contiene righe per il reddito basato su dati statunitensi e la seconda porta contiene righe per il reddito basato su dati non statunitensi.

1. Nel riquadro dei dettagli del modulo a destra del canvas selezionare le opzioni seguenti:
    
    **Tipo di archivio dati** : Archiviazione BLOB di Azure

    **Datastore** (Archivio dati): Selezionare un archivio dati esistente o selezionare "Nuovo archivio dati" per crearne uno.

    **Percorso** : `/data/us-income`

    **Formato file** : csv

    > [!NOTE]
    > Questo articolo presuppone che l'utente abbia accesso a un archivio dati registrato alla corrente area di lavoro di Azure Machine Learning. Per istruzioni su come configurare un archivio dati, vedere [Connettersi ai servizi di archiviazione di Azure](how-to-connect-data-ui.md#create-datastores).

    Se non è disponibile alcun archivio dati, è possibile crearne uno in questa fase. A titolo esemplificativo, in questo articolo i set di dati vengono salvati nell'account di archiviazione BLOB predefinito associato all'area di lavoro. I set di dati vengono salvati nel contenitore `azureml` in una nuova cartella denominata `data`.

1.  Selezionare il modulo **Export Data** connesso alla porta più a *destra* del modulo **Split Data**.

1. Nel riquadro dei dettagli del modulo a destra del canvas selezionare le opzioni seguenti:
    
    **Tipo di archivio dati** : Archiviazione BLOB di Azure

    **Datastore** (Archivio dati): Selezionare lo stesso archivio dati precedente

    **Percorso** : `/data/non-us-income`

    **Formato file** : csv

1. Confermare che il modulo **Export Data** connesso alla porta a sinistra del modulo **Split Data** presenti il **percorso** `/data/us-income`.

1. Confermare che il modulo **Export Data** connesso alla porta a destra presenti il **percorso** `/data/non-us-income`.

    La pipeline e le impostazioni dovrebbero avere un aspetto simile al seguente:
    
    ![Screenshot che illustra come configurare i moduli Export Data](media/how-to-designer-transform-data/us-income-export-data.png).

### <a name="submit-the-run"></a>Inviare l'esecuzione

Ora che la pipeline è stata configurata per dividere ed esportare i dati, inviare un'esecuzione della pipeline.

1. Nella parte superiore dell'area di disegno selezionare **Invia**.

1. Nella finestra di dialogo **Configura esecuzione della pipeline** selezionare **Crea nuova** per creare un esperimento.

    Gli esperimenti raggruppano logicamente le esecuzioni di pipeline correlate. Se si esegue questa pipeline in futuro, è consigliabile usare lo stesso esperimento a scopi di registrazione e verifica.

1. Specificare un nome di esperimento descrittivo, ad esempio "split-census-data".

1. Selezionare **Submit** (Invia).

## <a name="view-results"></a>Visualizzazione dei risultati

Al termine dell'esecuzione della pipeline è possibile visualizzare i risultati passando all'archivio BLOB nel portale di Azure. È possibile visualizzare anche i risultati intermedi del modulo **Split Data** per verificare che i dati siano stati divisi correttamente.

1. Selezionare il modulo **Split Data**.

1. Nel riquadro dei dettagli del modulo a destra del canvas selezionare **Output + log**. 

1. Selezionare l'icona di visualizzazione ![icona di visualizzazione](media/how-to-designer-transform-data/visualize-icon.png) accanto a **Risultati set di dati 1**. 

1. Verificare che la colonna "native-country" contenga solo il valore "United-States".

1. Selezionare l'icona di visualizzazione ![icona di visualizzazione](media/how-to-designer-transform-data/visualize-icon.png) accanto a **Risultati set di dati 2**. 

1. Verificare che la colonna "native-country" non contenga il valore "United-States".

## <a name="clean-up-resources"></a>Pulire le risorse

Ignorare questa sezione per continuare con la parte 2 di questa procedura, [Ripetere il training dei modelli con la finestra di progettazione di Azure Machine Learning](how-to-retrain-designer.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come trasformare un set di dati e salvarlo in un archivio dati registrato.

Passare alla parte successiva di questa serie di procedure con [Ripetere il training dei modelli con la finestra di progettazione di Azure Machine Learning](how-to-retrain-designer.md) per usare i set di dati trasformati e i parametri della pipeline per eseguire il training dei modelli di Machine Learning.
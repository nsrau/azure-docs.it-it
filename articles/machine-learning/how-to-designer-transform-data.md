---
title: Trasformare i dati
titleSuffix: Azure Machine Learning
description: Informazioni su come trasformare i dati nella finestra di progettazione Azure Machine Learning per creare set di dati personalizzati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 05/04/2020
ms.openlocfilehash: 5296ac54cab403ef78b3e8bd32fe5ebe6ea43119
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82842877"
---
# <a name="transform-data-in-azure-machine-learning-designer-preview"></a>Trasformare i dati in Azure Machine Learning Designer (anteprima)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Questo articolo illustra come trasformare e salvare i set di dati in Azure Machine Learning Designer in modo da poter preparare i propri dati per l'apprendimento automatico.

Per preparare due set di dati, si userà il set di dati relativo al censimento dei [redditi](sample-designer-datasets.md) per adulti di esempio, un set di dati che include le informazioni sul censimento degli adulti solo dal Stati Uniti e un altro set di dati che include le informazioni sul censimento degli adulti

In questo articolo vengono illustrate le operazioni seguenti:

1. Trasformare un set di dati per prepararlo per il training.
1. Esportare i set di dati risultanti in un archivio dati.
1. Visualizzare i risultati.

Questa procedura è un prerequisito per l'articolo [come](how-to-retrain-designer.md) ripetere il training dei modelli di progettazione. In questo articolo si apprenderà come usare i set di impostazioni trasformati per eseguire il training di più modelli con i parametri della pipeline.

## <a name="transform-a-dataset"></a>Trasformare un set di dati

In questa sezione viene illustrato come importare il set di dati di esempio e suddividere i dati in set di dati US e non Stati Uniti. Per ulteriori informazioni su come importare i dati nella finestra di progettazione, vedere [How to Import Data](how-to-designer-import-data.md).

### <a name="import-data"></a>Importa dati

Usare la procedura seguente per importare il set di dati di esempio.

1. Accedere a <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a> e selezionare l'area di lavoro che si vuole usare.

1. Passare alla finestra di progettazione. Selezionare **moduli di precompilazione facili da usare** per creare una nuova pipeline.

1. Selezionare una destinazione di calcolo predefinita per eseguire la pipeline.

1. A sinistra del canvas della pipeline è presente un pannello di set di dati e moduli. Selezionare **Set di dati**. Visualizzare quindi la sezione **Samples (esempi** ).

1. Trascinare e rilasciare il set di dati **per la classificazione binaria Adult Census income** nell'area di disegno.

1. Selezionare il modulo **Adult Census income** DataSet.

1. Nel riquadro dei dettagli visualizzato a destra dell'area di disegno selezionare **output**.

1. Selezionare l'icona Visualizza ![icona Visualizza](media/how-to-designer-transform-data/visualize-icon.png).

1. Utilizzare la finestra Anteprima dati per esplorare il set di dati. Prendere nota speciale dei valori di colonna "Native-Country".

### <a name="split-the-data"></a>Dividere i dati

In questa sezione viene usato il [modulo Split data](algorithm-module-reference/split-data.md) per identificare e dividere le righe che contengono "United-States" nella colonna "Native-Country". 

1. Nella tavolozza dei moduli a sinistra dell'area di disegno, espandere la sezione **trasformazione dati** e trovare il modulo **Split data** .

1. Trascinare il modulo **Split data** nell'area di disegno e rilasciare il modulo sotto il modulo DataSet.

1. Connettere il modulo DataSet al modulo **Split data** .

1. Selezionare il modulo **Split Data**.

1. Nel riquadro dei dettagli del modulo a destra dell'area di disegno impostare **modalità di suddivisione** su **espressione regolare**.

1. Immettere l' **espressione regolare**: `\"native-country" United-States`.

    La modalità di **espressione regolare** verifica una singola colonna per un valore. Per ulteriori informazioni sul modulo Split data, vedere la pagina di [riferimento del modulo algoritmo](algorithm-module-reference/split-data.md)correlato.

La pipeline dovrebbe avere un aspetto simile al seguente:

![Screenshot che illustra come configurare la pipeline e il modulo Split data](media/how-to-designer-transform-data/split-data.png).


## <a name="save-the-datasets"></a>Salvare i set di impostazioni

Ora che la pipeline è configurata per suddividere i dati, è necessario specificare la posizione in cui salvare i set di dati. Per questo esempio, usare il modulo **Export Data** per salvare il set di dati in un archivio dati. Per altre informazioni sugli archivi dati, vedere [connettersi ai servizi di archiviazione di Azure](how-to-access-data.md)

1. Nella tavolozza dei moduli a sinistra dell'area di disegno, espandere la sezione **input e output dei dati** e trovare il modulo **Export Data** .

1. Trascinare e rilasciare due moduli **Export Data** sotto il modulo **Split data** .

1. Connettere ogni porta di output del modulo **Split data** a un altro modulo **Export Data** .

    La pipeline dovrebbe avere un aspetto simile al seguente:

    ![Screenshot che illustra come connettere i moduli Export Data](media/how-to-designer-transform-data/export-data-pipeline.png).

1. Selezionare il modulo **Export Data (Esporta dati** ) connesso alla porta più a *sinistra*del modulo **Split data** .

    L'ordine delle porte di output è importante per il modulo **Split data** . La prima porta di output contiene le righe in cui l'espressione regolare è true. In questo caso, la prima porta contiene righe per il reddito basato su US e la seconda porta contiene righe per il reddito non basato su US.

1. Nel riquadro Dettagli modulo a destra dell'area di disegno impostare le opzioni seguenti:
    
    **Tipo di archivio dati**: archiviazione BLOB di Azure

    **Archivio dati**: selezionare un archivio dati esistente o selezionare "nuovo archivio dati" per crearne uno ora.

    **Percorso**:`/data/us-income`

    **Formato file**: CSV

    > [!NOTE]
    > Questo articolo presuppone che l'utente abbia accesso a un archivio dati registrato nell'area di lavoro Azure Machine Learning corrente. Per istruzioni su come configurare un archivio dati, vedere [connettersi ai servizi di archiviazione di Azure](how-to-access-data.md#azure-machine-learning-studio).

    Se non si dispone di un archivio dati, è possibile crearne uno ora. A scopo di esempio, in questo articolo i set di impostazioni vengono salvati nell'account di archiviazione BLOB predefinito associato all'area di lavoro. I set di impostazioni vengono salvati nel `azureml` contenitore in una nuova cartella denominata. `data`

1.  Selezionare il modulo **Export Data** connesso alla porta più a *destra*del modulo **Split data** .

1. Nel riquadro Dettagli modulo a destra dell'area di disegno impostare le opzioni seguenti:
    
    **Tipo di archivio dati**: archiviazione BLOB di Azure

    **Archivio dati**: selezionare lo stesso archivio dati precedente

    **Percorso**:`/data/non-us-income`

    **Formato file**: CSV

1. Verificare che il modulo **Export Data** connesso alla porta sinistra del **Split data** includa il **percorso** `/data/us-income`.

1. Verificare che il modulo **Export Data** connesso alla porta destra includa il **percorso** `/data/non-us-income`.

    La pipeline e le impostazioni dovrebbero essere simili alle seguenti:
    
    ![Screenshot che illustra come configurare i moduli Export Data](media/how-to-designer-transform-data/us-income-export-data.png).

### <a name="submit-the-run"></a>Invia l'esecuzione

Ora che la pipeline è stata impostata per suddividere ed esportare i dati, inviare un'esecuzione della pipeline.

1. Nella parte superiore dell'area di disegno selezionare **Invia**.

1. Nella finestra di dialogo **Configura esecuzione pipeline** selezionare **Crea nuovo** per creare un esperimento.

    Gli esperimenti raggruppano logicamente le esecuzioni di pipeline correlate. Se questa pipeline viene eseguita in futuro, è consigliabile usare lo stesso esperimento a scopo di registrazione e traccia.

1. Specificare un nome di esperimento descrittivo, ad esempio "Split-Census-Data".

1. Selezionare **Submit** (Invia).

## <a name="view-results"></a>Visualizzazione dei risultati

Al termine dell'esecuzione della pipeline, è possibile visualizzare i risultati passando all'archivio BLOB nella portale di Azure. È anche possibile visualizzare i risultati intermedi del modulo **Split data** per verificare che i dati siano stati suddivisi correttamente.

1. Selezionare il modulo **Split Data**.

1. Nel riquadro dei dettagli del modulo a destra del canvas selezionare **Output + log**. 

1. Selezionare l'icona](media/how-to-designer-transform-data/visualize-icon.png) Visualizza icona ![Visualizza accanto a **risultati DataSet1**. 

1. Verificare che la colonna "Native-Country" contenga solo il valore "United-States".

1. Selezionare l'icona](media/how-to-designer-transform-data/visualize-icon.png) Visualizza icona ![Visualizza accanto a **risultati Dataset2**. 

1. Verificare che la colonna "Native-Country" non contenga il valore "United-States".

## <a name="clean-up-resources"></a>Pulire le risorse

Ignorare questa sezione se si desidera continuare con la parte 2 di questa procedura, ripetere il [training dei modelli con Azure machine learning designer](how-to-retrain-designer.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come trasformare un set di dati e salvarlo in un archivio dati registrato.

Passare alla parte successiva di questa serie di procedure con il [training dei modelli con Azure machine learning designer](how-to-retrain-designer.md) per usare i set di impostazioni e i parametri della pipeline trasformati per il training dei modelli di machine learning.

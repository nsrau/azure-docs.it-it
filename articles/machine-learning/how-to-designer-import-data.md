---
title: Importa dati
titleSuffix: Azure Machine Learning
description: Informazioni su come importare i dati in Progettazione Azure Machine Learning da varie origini dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.openlocfilehash: 1ad7677607d625f673546a6ea29ea58b80a8d1b5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546745"
---
# <a name="import-your-data-into-azure-machine-learning-designer-preview"></a>Importare i dati nella finestra di progettazione di Azure Machine Learning (anteprima)Import your data into Azure Machine Learning designer (preview)

In questo articolo viene illustrato come importare i propri dati nella finestra di progettazione per creare soluzioni personalizzate. È possibile importare dati nella finestra di progettazione in due modi:There are two ways you can import data into the designer: 

* Set di dati di **Azure Machine Learning:** registrare i set di [dati](concept-data.md#datasets) in Azure Machine Learning per abilitare funzionalità avanzate che consentono di gestire i dati.
* **Modulo Importa dati:** usare il modulo [Importa dati](algorithm-module-reference/import-data.md) per accedere direttamente ai dati da origini dati online.

## <a name="use-azure-machine-learning-datasets"></a>Usare i set di dati di Azure Machine LearningUse Azure Machine Learning datasets

È consigliabile usare [i set](concept-data.md#datasets) di dati per importare i dati nella finestra di progettazione. Quando si registra un set di dati, è possibile sfruttare appieno le funzionalità avanzate dei dati, ad esempio il [controllo delle versioni,](how-to-version-track-datasets.md) il rilevamento e il monitoraggio dei [dati.](how-to-monitor-datasets.md)

### <a name="register-a-dataset"></a>Registrare un set di datiRegister a dataset

È possibile registrare i dataset esistenti [in modo programmato con l'SDK](how-to-create-register-datasets.md#use-the-sdk) o [visivamente in Azure Machine Learning Studio.](how-to-create-register-datasets.md#use-the-ui)

È inoltre possibile registrare l'output per qualsiasi modulo della finestra di progettazione come set di dati.

1. Selezionare il modulo che restituisce i dati che si desidera registrare.

1. Nel riquadro delle proprietà selezionare **Set** > di dati**Output registro**.

    ![Schermata che mostra come passare all'opzione Registra set di dati](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>Usare un set di datiUse a dataset

I set di dati registrati sono disponibili nella tavolozza dei moduli, in **Dataset,** > **dataset**personali . Per usare un set di dati, trascinarlo nell'area di disegno della pipeline. Quindi, collegare la porta di output del set di dati ad altri moduli nella tavolozza.

![Schermata che mostra la posizione dei set di dati salvati nella tavolozza della finestra di progettazione](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> La finestra di progettazione supporta attualmente solo l'elaborazione di [dataset tabulari.](how-to-create-register-datasets.md#dataset-types) Se si vuole usare i set di dati dei file, usare Azure Machine Learning SDK disponibile per Python e R.If you want to use [file datasets](how-to-create-register-datasets.md#dataset-types), use the Azure Machine Learning SDK available for Python and R.

## <a name="import-data-using-the-import-data-module"></a>Importare dati utilizzando il modulo Importa dati

Sebbene sia consigliabile usare i set di dati per importare i dati, è anche possibile usare il modulo [Importa dati.](algorithm-module-reference/import-data.md) Il modulo Importa dati ignora la registrazione del set di dati in Azure Machine Learning e importa i dati direttamente da un [archivio dati](concept-data.md#datastores) o da un URL HTTP.

Per informazioni dettagliate sull'utilizzo del modulo Importa dati, vedere la pagina di [riferimento Importa dati](algorithm-module-reference/import-data.md).

> [!NOTE]
> Se il set di dati contiene troppe colonne, è possibile che si verifichi il seguente errore: "Convalida non riuscita a causa di limitazioni di dimensione". Per evitare questo problema, [registrare il dataset nell'interfaccia Dataset.](how-to-create-register-datasets.md#use-the-ui)

## <a name="supported-sources"></a>Fonti supportate

In questa sezione sono elencate le origini dati supportate dalla finestra di progettazione. I dati vengono inseriti nella finestra di progettazione da un archivio dati o da un [set di dati tabulare.](how-to-create-register-datasets.md#dataset-types)

### <a name="datastore-sources"></a>Origini datastore
Per un elenco delle origini dell'archivio dati supportate, vedere [Accedere ai dati nei servizi di archiviazione](how-to-access-data.md#supported-data-storage-service-types)di Azure.For a list of supported datastore sources, see Access data in Azure storage services .

### <a name="tabular-dataset-sources"></a>Origini set di dati tabulariTabular dataset sources

La finestra di progettazione supporta i set di dati tabulari creati dalle origini seguenti:The designer supports tabular datas created from the following sources:
 * File delimitati
 * File JSON
 * File Parquet
 * Query SQL

## <a name="data-types"></a>Tipi di dati

La finestra di progettazione riconosce internamente i tipi di dati seguenti:The designer internally recognizes the following data types:

* string
* Integer
* Decimal
* Boolean
* Data

La finestra di progettazione utilizza un tipo di dati interno per passare i dati tra i moduli. È possibile convertire in modo esplicito i dati in formato tabella dati utilizzando il modulo Converti in [set di dati.](algorithm-module-reference/convert-to-dataset.md) Qualsiasi modulo che accetta formati diversi dal formato interno convertirà i dati in modo invisibile all'utente prima di passarli al modulo successivo.

## <a name="data-constraints"></a>Vincoli dati

I moduli nella finestra di progettazione sono limitati dalle dimensioni della destinazione di calcolo. Per set di dati di dimensioni maggiori, è consigliabile usare una risorsa di calcolo di Azure Machine Learning più grande. Per altre informazioni sul calcolo di Azure Machine Learning, vedere Quali sono le destinazioni di [calcolo in Azure Machine Learning?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="next-steps"></a>Passaggi successivi

Impara le basi del designer con [Tutorial: Predict automobile price con il designer.](tutorial-designer-automobile-price-train-score.md)

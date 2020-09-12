---
title: Importa i dati nella finestra di progettazione (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni su come importare dati in Azure Machine Learning Designer (anteprima) da varie origini dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 09/09/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: a7d0a1604b3bea1f11532639dbbc5102f4a243a6
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006985"
---
# <a name="import-data-into-azure-machine-learning-designer-preview"></a>Importare dati in Azure Machine Learning Designer (anteprima)

In questo articolo si spiega come importare dati nella finestra di progettazione per creare soluzioni personalizzate. È possibile importare i dati nella finestra di progettazione in due modi: 

* **Set di dati in Azure Machine Learning**: registrare i [set di dati](concept-data.md#datasets) in Azure Machine Learning per abilitare le funzionalità avanzate che consentono di gestire i dati.
* **Importare il modulo dati**: usare il modulo [Import Data](algorithm-module-reference/import-data.md) per accedere direttamente ai dati dalle origini dati online.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-azure-machine-learning-datasets"></a>Uso dei set di dati di Azure Machine Learning

Consigliamo di usare i [set di dati](concept-data.md#datasets) per importare i dati nella finestra di progettazione. Quando si registra un set di dati, è possibile sfruttare appieno funzionalità avanzate come [il controllo delle versioni e il rilevamento](how-to-version-track-datasets.md) e [il monitoraggio dei dati](how-to-monitor-datasets.md).

### <a name="register-a-dataset"></a>Registrazione di un set di dati

È possibile registrare i set di dati esistenti [a livello di codice con l'SDK](how-to-create-register-datasets.md#datasets-sdk) o [visivamente in Azure Machine Learning Studio](how-to-create-register-datasets.md#datasets-ui).

È anche possibile registrare l'output di qualsiasi modulo della finestra di progettazione come set di dati.

1. Selezionare il modulo che restituisce i dati da registrare.

1. Nel riquadro Proprietà selezionare **output + log**  >  **registra set di dati**.

    ![Screenshot che mostra come passare all'opzione Registra set di dati](media/how-to-designer-import-data/register-dataset-designer.png)

Se i dati di output del modulo sono in formato tabulare, è necessario scegliere di registrare l'output come set di dati di **file** o **tabulare**.

 - Il **set di dati del file** registra la cartella di output del modulo come set di dati di file. La cartella di output contiene un file di dati e i metadati che la finestra di progettazione utilizza internamente. Selezionare questa opzione se si desidera continuare a utilizzare il set di dati registrato nella finestra di progettazione. 

 - Il set di dati **tabulare** registra solo il file di dati di output del modulo come set di dati tabulare. Questo formato è facilmente utilizzabile da altri strumenti, ad esempio in Machine Learning automatizzato o Python SDK. Selezionare questa opzione se si prevede di utilizzare il set di dati registrato al di fuori della finestra di progettazione.  



### <a name="use-a-dataset"></a>Uso di un set di dati

I set di strumenti registrati sono disponibili nella tavolozza dei moduli, in **set di impostazioni**. Per usare un set di dati, trascinarlo e rilascialo nelle canvas della pipeline. Quindi, connettere la porta di output del set di dati ad altri moduli nell'area di disegno. 

![Screenshot che mostra la posizione dei set di dati salvati nella tavolozza della finestra di progettazione](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> La finestra di progettazione supporta il [controllo delle versioni dei DataSet](how-to-version-track-datasets.md). Specificare la versione del set di dati nel pannello Proprietà del modulo DataSet.


## <a name="import-data-using-the-import-data-module"></a>Importazione dei dati usando il modulo Importa dati

Sebbene sia consigliabile usare i set di dati per importare i dati, è anche possibile usare il modulo [Import Data](algorithm-module-reference/import-data.md). Il modulo Import Data salta la registrazione del set di dati in Azure Machine Learning e importa i dati direttamente da un [archivio dati](concept-data.md#datastores) o da un URL HTTP.

Per informazioni dettagliate su come usare il modulo Import Data, vedere la [pagina di riferimento di Importa dati](algorithm-module-reference/import-data.md).

> [!NOTE]
> Se nel set di dati sono presenti troppe colonne, potrebbe verificarsi il seguente errore: "Convalida non riuscita a causa della limitazione delle dimensioni". Per evitare che si verifichi, [registrare il set di dati nell'interfaccia Set di dati](how-to-create-register-datasets.md#datasets-ui).

## <a name="supported-sources"></a>Origini supportate

In questa sezione sono elencate le origini dati supportate dalla finestra di progettazione. I dati della finestra di progettazione provengono da un archivio dati o da un [set di dati tabulare](how-to-create-register-datasets.md#dataset-types).

### <a name="datastore-sources"></a>Origini di archivio dati
Per un elenco delle origini di archivio dati supportate, vedere [Accesso ai dati nei servizi di archiviazione di Azure](how-to-access-data.md#supported-data-storage-service-types).

### <a name="tabular-dataset-sources"></a>Origini di set di dati tabulari

La finestra di progettazione supporta i set di dati tabulari creati dalle origini seguenti:
 * File delimitati
 * File JSON
 * File Parquet
 * Query SQL

## <a name="data-types"></a>Tipi di dati

La finestra di progettazione riconosce internamente i tipi di dati seguenti:

* string
* Integer
* Decimal
* Boolean
* Data

La finestra di progettazione usa un tipo di dati interno per passare i dati tra i moduli. È possibile convertire in modo esplicito i dati in formato tabella dati tramite il modulo [Convert to Dataset](algorithm-module-reference/convert-to-dataset.md). I moduli che accettano formati diversi da quello interno convertono i dati in modo automatico prima di passare al modulo successivo.

## <a name="data-constraints"></a>Vincoli dei dati

I moduli della finestra di progettazione sono limitati dalle dimensioni della destinazione di calcolo. Per i set di dati più grandi è necessario usare una risorsa di calcolo Azure Machine Learning più grande. Per altre informazioni sulle destinazioni di calcolo di Azure Machine Learning vedere [Cosa sono le destinazioni di calcolo in Azure Machine Learning?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="access-data-in-a-virtual-network"></a>Accedere ai dati in una rete virtuale

Se l'area di lavoro si trova in una rete virtuale, è necessario eseguire passaggi di configurazione aggiuntivi per visualizzare i dati nella finestra di progettazione. Per altre informazioni su come usare gli archivi dati e i set di dati in una rete virtuale, vedere [usare Azure Machine Learning Studio in una rete virtuale di Azure](how-to-enable-studio-virtual-network.md).

## <a name="next-steps"></a>Passaggi successivi

Nozioni di base sulla finestra di progettazione con [Esercitazione: Stimare il prezzo di un'automobile con la finestra di progettazione](tutorial-designer-automobile-price-train-score.md).

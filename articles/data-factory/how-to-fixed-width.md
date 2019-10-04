---
title: Elaborare i file di testo a lunghezza fissa con i flussi di dati di mapping in Azure Data Factory
description: Informazioni su come elaborare i file di testo a lunghezza fissa in Azure Data Factory usando i flussi di dati di mapping.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: 9964aaf060c43cc3e9992f515bf272011e795043
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962121"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Elaborare file di testo a lunghezza fissa usando Data Factory i flussi di dati di mapping

Utilizzando i flussi di dati di mapping in Microsoft Azure Data Factory, è possibile trasformare i dati da file di testo a larghezza fissa. Nell'attività seguente verrà definito un set di dati per un file di testo senza un delimitatore e quindi verranno configurate le divisioni della sottostringa in base alla posizione ordinale.

## <a name="create-a-pipeline"></a>Creare una pipeline

1. Selezionare **+ nuova pipeline** per creare una nuova pipeline.

2. Aggiungere un'attività flusso di dati che verrà utilizzata per l'elaborazione di file a larghezza fissa:

    ![Pipeline a larghezza fissa](media/data-flow/fwpipe.png)

3. Nell'attività flusso di dati selezionare **nuovo flusso di dati di mapping**.

4. Aggiungere una trasformazione origine, colonna derivata, selezione e sink:

    ![Flusso di dati a larghezza fissa](media/data-flow/fw2.png)

5. Configurare la trasformazione di origine in modo da utilizzare un nuovo set di dati, che sarà del tipo di testo delimitato.

6. Non impostare delimitatori o intestazioni di colonna.

   A questo punto, impostare i punti di inizio e le lunghezze del campo per il contenuto del file:

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. Nella scheda **proiezione** della trasformazione origine dovrebbe essere visualizzata una colonna stringa denominata *column_1*.

8. Nella colonna derivata creare una nuova colonna.

9. Verranno fornite le colonne nomi semplici come *col1*.

10. Nel generatore di espressioni digitare quanto segue:

    ```substring(Column_1,1,4)```

    ![colonna derivata](media/data-flow/fwderivedcol1.png)

11. Ripetere il passaggio 10 per tutte le colonne che devono essere analizzate.

12. Selezionare la scheda **Controlla** per visualizzare le nuove colonne che verranno generate:

    ![ispezionare](media/data-flow/fwinspect.png)

13. Usare la trasformazione seleziona per rimuovere le colonne non necessarie per la trasformazione:

    ![selezione trasformazione](media/data-flow/fwselect.png)

14. Usare sink per restituire i dati in una cartella:

    ![sink a larghezza fissa](media/data-flow/fwsink.png)

    Ecco come appare l'output:

    ![output a larghezza fissa](media/data-flow/fxdoutput.png)

  I dati a larghezza fissa sono ora divisi, con quattro caratteri ciascuno e assegnati a col1, Col2, col3, col4 e così via. In base all'esempio precedente, i dati vengono suddivisi in quattro colonne.

## <a name="next-steps"></a>Passaggi successivi

* Compilare il resto della logica del flusso di dati tramite il mapping delle [trasformazioni](concepts-data-flow-overview.md)dei flussi di dati.

---
title: Elaborare file di testo a lunghezza fissa con mapping dei flussi di dati in Azure Data FactoryProcess fixed-length text files with mapping data flows in Azure Data Factory
description: Informazioni su come elaborare file di testo a lunghezza fissa in Azure Data Factory usando i flussi di dati di mapping.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: d629a9031f032a77efc953311a45b55996568191
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414384"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Elaborare file di testo a lunghezza fissa utilizzando i flussi di dati di mapping di Data FactoryProcess fixed-length text files by using Data Factory mapping data flows

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Usando i flussi di dati di mapping in Microsoft Azure Data Factory, è possibile trasformare i dati da file di testo a larghezza fissa. Nell'attività seguente verrà definito un set di dati per un file di testo senza delimitatore e quindi verranno impostate le suddivisioni di sottostringhe in base alla posizione ordinale.

## <a name="create-a-pipeline"></a>Creare una pipeline

1. Per creare una nuova pipeline, selezionare **Nuova pipeline.**

2. Aggiungere un'attività del flusso di dati, che verrà utilizzata per l'elaborazione di file a larghezza fissa:Add a data flow activity, which will be used for processing fixed-width files:

    ![Pipeline a larghezza fissa](media/data-flow/fwpipe.png)

3. Nell'attività del flusso di dati selezionare **Nuovo flusso di dati di mapping.**

4. Aggiungere una trasformazione Origine, Colonna derivata, Seleziona e Sink:Add a Source, Derived Column, Select, and Sink transformation:

    ![Flusso di dati a larghezza fissaFixed Width Data Flow](media/data-flow/fw2.png)

5. Configurare la trasformazione Origine per utilizzare un nuovo set di dati, che sarà di tipo Testo delimitato.

6. Non impostare alcun delimitatore di colonna o intestazioni.

   Ora imposteremo i punti di partenza del campo e le lunghezze per il contenuto di questo file:

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

7. Nella scheda **Proiezione** della trasformazione Origine dovrebbe essere visualizzata una colonna stringa denominata *Column_1*.

8. Nella colonna Derivato creare una nuova colonna.

9. Daremo alle colonne nomi semplici come *col1*.

10. Nel generatore di espressioni digitare quanto segue:

    ```substring(Column_1,1,4)```

    ![colonna derivata](media/data-flow/fwderivedcol1.png)

11. Ripetere il passaggio 10 per tutte le colonne da analizzare.

12. Selezionare la scheda **Controlla** per visualizzare le nuove colonne che verranno generate:

    ![Ispezionare](media/data-flow/fwinspect.png)

13. Utilizzare la trasformazione Seleziona per rimuovere le colonne non necessarie per la trasformazione:

    ![selezionare la trasformazione](media/data-flow/fwselect.png)

14. Utilizzare Sink per restituire i dati in una cartella:Use Sink to output the data to a folder:

    ![lavello a larghezza fissa](media/data-flow/fwsink.png)

    Ecco l'aspetto dell'output:Here's what the output looks like:

    ![uscita a larghezza fissa](media/data-flow/fxdoutput.png)

  I dati a larghezza fissa sono ora divisi, con quattro caratteri ciascuno e assegnati a Col1, Col2, Col3, Col4 e così via. In base all'esempio precedente, i dati vengono suddivisi in quattro colonne.

## <a name="next-steps"></a>Passaggi successivi

* Compilare il resto della logica del flusso di dati utilizzando il mapping delle trasformazioni dei [flussi](concepts-data-flow-overview.md)di dati .

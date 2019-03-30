---
title: Trasformare i dati con Databricks in Azure Data Factory | Microsoft Docs
description: Informazioni su come usare un modello di soluzione per trasformare i dati usando un notebook di Databricks in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 562ce675acc43002ce468d60f8a8c412410be86c
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630799"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>Trasformare i dati con Databricks in Azure Data Factory

In questa esercitazione si crea una pipeline end-to-end contenente le attività **Lookup**, **Copy** e **Databricks notebook** in Data Factory.

-   L'attività **Lookup** o GetMetadata viene usata per assicurarsi che il set di dati di origine sia pronto per l'utilizzo downstream, prima di attivare il processo di copia e analisi.

-   L'attività **Copy** copia il file/set di dati di origine nell'archivio sink. L'archivio sink è montato come DBFS nel notebook di Databricks, in modo che il set di dati possa essere utilizzato direttamente da Spark.

-   L'attività **Databricks notebook** attiva il notebook di Databricks che trasforma il set di dati e lo aggiunge a una cartella elaborata/SQL Data Warehouse.

Per mantenere semplice questo modello, non viene creato un trigger pianificato. Se necessario, è possibile aggiungerlo.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Prerequisiti

1.  Creare un **account di archiviazione BLOB** e un contenitore denominato `sinkdata` da usare come **sink**. Prendere nota del **nome dell'account di archiviazione**, del **nome del contenitore** e della **chiave di accesso**, che verranno usati più avanti nel modello.

2.  Verificare di avere un'**area di lavoro di Azure Databricks** o crearne una nuova.

1.  **Importare il notebook per estrazione, trasformazione e caricamento**. Importare il notebook di trasformazione seguente nell'area di lavoro di Databricks. Non è necessario che si trovi nella stessa posizione indicata di seguito, ma è necessario ricordarsi il percorso scelto, che verrà usato più avanti. Importare il notebook dall'URL seguente, immettendo l'URL nel relativo campo: `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`. Selezionare **Importa**.

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  Aggiornare quindi il notebook **Transformation** con le **informazioni di connessione all'archivio** (nome e chiave di accesso). Passare al **comando 5** nel notebook importato in precedenza e sostituirlo con il frammento di codice seguente dopo aver sostituito i valori evidenziati. Verificare che l'account corrisponda all'account di archiviazione creato in precedenza e contenga il contenitore `sinkdata`.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1.  Generare un **token di accesso di Databricks** per consentire a Data Factory di accedere a Databricks. **Salvare il token di accesso** per usarlo successivamente nella creazione di un servizio collegato Databricks. L'aspetto sarà simile a "dapi32db32cbb4w6eee18b7d87e45exxxxxx"

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>Creare set di dati e servizi collegati

1.  Creare nuovi **servizi collegati** nell'interfaccia utente di Data Factory passando a *Connections Linked services + new*(Servizi collegati connessioni + nuovo)

    1.  **Origine**: per l'accesso ai dati di origine. È possibile usare l'archivio BLOB pubblico contenente i file di origine per questo esempio.

        Selezionare **Archiviazione BLOB** e usare il valore dell'**URI di firma di accesso condiviso** indicato sotto per connettersi all'archivio di origine (accesso di sola lettura).

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Sink**: come destinazione per la copia dei dati.

        Selezionare un archivio creato nell'ambito del prerequisito 1, nel servizio collegato sink.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Databricks**: per la connessione al cluster Databricks

        Creare un servizio collegato Databricks usando la chiave di accesso generata nell'ambito del prerequisito 2.c. Se si ha un *cluster interattivo*, è possibile selezionarlo. Questo esempio usa l'opzione *New job cluster* (Nuovo cluster dei processi).

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  Creare i **set di dati**

    1.  Creare **"sourceAvailability_Dataset"** per controllare se i dati di origine sono disponibili

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **Set di dati di origine**: per la copia dei dati di origine (usando la copia binaria)

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **Set di dati sink**: per la copia nel sink/posizione di destinazione

        1.  Servizio collegato: selezionare il servizio "sinkBlob_LS" creato nel passaggio 1.b

        2.  Percorso file: "sinkdata/staged_sink"

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>Creare le attività

1.  Creare un'attività Lookup "**Availability flag**" (Flag di disponibilità) per eseguire il controllo della disponibilità dell'origine (è possibile usare un'attività Lookup o GetMetadata). Selezionare l'attività "sourceAvailability_Dataset" creata nel passaggio 2.a.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Creare un'attività Copy "**file-to-blob**" (Da file a BLOB) per copiare il set di dati dall'origine al sink. In questo caso, i dati sono costituiti da un file binario. Fare riferimento agli screenshot seguenti per le configurazioni dell'origine e del sink nell'attività Copy.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  Definire i **parametri della pipeline**

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  Creare un'**attività Databricks**

    Selezionare il servizio collegato creato in un passaggio precedente.

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    Configurare le **impostazioni**. Creare i **parametri di base** come illustrato nello screenshot e creare i parametri da passare al notebook di Databricks da Data Factory. Cercare e **selezionare** il **percorso del notebook appropriato** caricato nell'ambito del **prerequisito 2**.

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  **Eseguire la pipeline**. È possibile trovare il collegamento ai log di Databricks per log di Spark più dettagliati.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    È anche possibile verificare il file di dati usando lo strumento di esplorazione dell'archiviazione. Per la correlazione con le esecuzioni di pipeline di Data Factory, in questo esempio viene aggiunto l'ID di esecuzione della pipeline da Data Factory nella cartella di output. In questo modo è possibile risalire ai file generati da ogni esecuzione.

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al servizio Azure Data Factory](introduction.md)

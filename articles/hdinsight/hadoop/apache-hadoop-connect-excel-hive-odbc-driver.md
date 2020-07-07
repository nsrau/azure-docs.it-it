---
title: '& di Excel Apache Hadoop con driver Open Database Connectivity (ODBC)-Azure HDInsight'
description: Informazioni su come configurare e usare Microsoft Hive ODBC Driver per Excel per eseguire query su dati nei cluster HDInsight da Microsoft Excel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 388f59f5090be43469acfde5197a658942e817f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82182447"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Connettere Excel ad Apache Hadoop in HDInsight mediante Microsoft Hive ODBC Driver

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

La soluzione Microsoft per Big Data integra componenti di Microsoft Business Intelligence (BI) con Apache Hadoop cluster distribuiti in HDInsight. Un esempio è la possibilità di connettere Excel al data warehouse hive di un cluster Hadoop. Connettersi usando il driver Microsoft Hive Open Database Connectivity (ODBC).

È possibile connettere i dati associati a un cluster HDInsight da Excel con il componente aggiuntivo Microsoft Power Query per Excel. Per altre informazioni, vedere [connettere Excel a HDInsight con Power query](../hdinsight-connect-excel-power-query.md).

## <a name="prerequisites"></a>Prerequisiti

Per eseguire le procedure descritte nell'articolo sono necessari gli elementi seguenti:

* Un cluster in HDInsight Hadoop. Per crearne uno, vedere l'[introduzione ad Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Una workstation con Office 2010 Professional Plus o versione successiva oppure Excel 2010 o versione successiva.

## <a name="install-microsoft-hive-odbc-driver"></a>Installare Microsoft Hive ODBC driver

Scaricare e installare [Microsoft Hive ODBC driver](https://www.microsoft.com/download/details.aspx?id=40886). Scegliere la versione corrispondente alla versione dell'applicazione in cui si userà il driver ODBC.  Per questo articolo, il driver viene usato per Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Creare un'origine dati Apache Hive ODBC

I passaggi seguenti mostrano come creare un'origine dati Hive ODBC.

1. Da Windows, passare a **Start > strumenti di amministrazione di windows > origini dati ODBC (32 bit)/(64 bit)**.  Questa azione consente di aprire la finestra **Amministrazione origine dati ODBC** .

    ![Amministrazione origine dati ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "Configurare un DSN usando l'amministrazione origine dati ODBC")

1. Nella scheda **DSN utente** selezionare **Aggiungi** per aprire la finestra **Crea nuova origine dati**.

1. Selezionare **Microsoft Hive ODBC Driver** e quindi selezionare **Fine** per aprire la finestra di configurazione del **DSN per Microsoft Hive ODBC Driver.**

1. Digitare o selezionare i valori seguenti:

   | Proprietà | Descrizione |
   | --- | --- |
   |  Data Source Name |Assegnare un nome all'origine dati |
   |  Host |Immettere `HDInsightClusterName.azurehdinsight.net`. Ad esempio: `myHDICluster.azurehdinsight.net`. Nota: `HDInsightClusterName-int.azurehdinsight.net` è supportato purché la macchina virtuale del client venga peerata alla stessa rete virtuale. |
   |  Porta |Utilizzare **443**. Questa porta è passata da 563 a 443. |
   |  Database |Usare il **valore predefinito**. |
   |  Mechanism |Selezionare **Servizio HDInsight di Microsoft Azure** |
   |  Nome utente |Immettere il nome utente HTTP del cluster HDInsight. Il nome utente predefinito è **admin**. |
   |  Password |Immettere la password utente del cluster HDInsight. Selezionare la casella di controllo **Save Password (Encrypted)** (Salva password (crittografata)).|

1. Facoltativo: selezionare **Opzioni avanzate...**  

   | Parametro | Descrizione |
   | --- | --- |
   |  Use Native Query |Quando è selezionato, il driver ODBC NON cerca di convertire TSQL in HiveQL. È necessario usarlo solo se il 100% si sta inviando istruzioni HiveQL pure. Quando ci si connette al database SQL di Azure o SQL Server, è consigliabile lasciarlo deselezionato. |
   |  Rows fetched per block |Quando si recupera un numero elevato di record, potrebbe essere necessario ottimizzare questo parametro per assicurare prestazioni ottimali. |
   |  Default string column length, Binary column length, Decimal column scale |Le lunghezze e le precisioni del tipo di dati potrebbero avere effetto sulla visualizzazione dei dati. Provocano la restituzione di informazioni non corrette a causa della perdita di precisione e di troncamento. |

    ![Opzioni di configurazione del DSN avanzate](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "Opzioni di configurazione del DSN avanzate")

1. Selezionare **Test** per testare l'origine dati. Quando l'origine dati è configurata correttamente, il risultato del test indica **esito positivo.**

1. Selezionare **OK** per chiudere la finestra Test.  

1. Selezionare **OK** per chiudere la finestra di configurazione del **DSN per Microsoft Hive ODBC Driver.**  

1. Selezionare **OK** per chiudere la finestra **Amministratore origine dati ODBC**.  

## <a name="import-data-into-excel-from-hdinsight"></a>Importazione di dati in Excel da HDInsight

La procedura seguente descrive come importare dati da una tabella Hive in una cartella di lavoro di Excel usando l'origine dati ODBC creata nella sezione precedente.

1. Aprire una cartella di lavoro nuova o esistente in Excel.

2. Nella scheda **Dati** passare a **Carica dati** > **Da altre origini** > **Da ODBC** per aprire la finestra **Da ODBC**.

    ![Aprire la connessione guidata dati di Excel](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "Aprire la connessione guidata dati di Excel")

3. Nell'elenco a discesa selezionare il nome dell'origine dati creato nell'ultima sezione e quindi fare clic su **OK**.

4. Per il primo utilizzo, viene visualizzata una finestra di dialogo del **driver ODBC** . Selezionare **Windows** dal menu a sinistra. Selezionare quindi **Connetti** per aprire la finestra **strumento di navigazione** .

5. In **Strumento di spostamento** passare a **HIVE** > **default** > **hivesampletable** e quindi selezionare **Carica**. L'importazione dei dati in Excel potrebbe richiedere alcuni istanti.

    ![Strumento di navigazione ODBC hive HDInsight di Excel](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "Strumento di navigazione ODBC hive HDInsight di Excel")

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come usare Microsoft Hive ODBC Driver per recuperare dati dal servizio HDInsight in Excel. È analogamente possibile recuperare dati dal servizio HDInsight nel database SQL. È anche possibile caricare i dati in un servizio HDInsight. Per altre informazioni, vedere:

* [Visualizzare i dati di Apache hive con Microsoft Power bi in Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualizza i dati di hive Interactive query con Power bi in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Connettere Excel a Apache Hadoop tramite Power query](apache-hadoop-connect-excel-power-query.md).
* [Connettersi ad Azure HDInsight ed eseguire query Apache hive usando data Lake Tools per Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).

---
title: Excel & Apache Hadoop con driver ODBC (Open Database Connectivity) - Azure HDInsight
description: Informazioni su come configurare e usare Microsoft Hive ODBC Driver per Excel per eseguire query su dati nei cluster HDInsight da Microsoft Excel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/22/2020
ms.openlocfilehash: 5aefd2c344565bf6dcb384996c42c1bc30e7291d
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024932"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Connettere Excel ad Apache Hadoop in HDInsight mediante Microsoft Hive ODBC Driver

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

La soluzione Microsoft Big Data integra i componenti Microsoft Business Intelligence (BI) con i cluster Apache Hadoop distribuiti in HDInsight. Un esempio è la possibilità di connettere Excel al data warehouse Hive di un cluster Hadoop. Connettersi utilizzando il driver ODBC (Open Database Connectivity) di Microsoft Hive.

È possibile connettere i dati associati a un cluster HDInsight da Excel con il componente aggiuntivo Microsoft Power Query per Excel.You can connect the data associated with an HDInsight cluster from Excel with Microsoft Power Query add-in for Excel. Per ulteriori informazioni, vedere [Connettere Excel a HDInsight con Power Query.](../hdinsight-connect-excel-power-query.md)

## <a name="prerequisites"></a>Prerequisiti

Per eseguire le procedure descritte nell'articolo sono necessari gli elementi seguenti:

* Un cluster in HDInsight Hadoop. Per crearne uno, vedere l'[introduzione ad Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Una workstation con Office 2010 Professional Plus o versione successiva oppure Excel 2010 o versione successiva.

## <a name="install-microsoft-hive-odbc-driver"></a>Installare Microsoft Hive ODBC driver

Scaricare e installare [Microsoft Hive ODBC Driver](https://www.microsoft.com/download/details.aspx?id=40886). Scegliere la versione che corrisponde alla versione dell'applicazione in cui verrà utilizzato il driver ODBC.  Per questo articolo, il driver viene utilizzato per Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Creare un'origine dati Apache Hive ODBC

I passaggi seguenti mostrano come creare un'origine dati Hive ODBC.

1. Da Windows, passare a Avvia > Strumenti di amministrazione di **Windows > Origini dati ODBC (32 bit)/(64 bit)**.  Questa azione apre la finestra **Amministratore origine dati ODBC.**

    ![Amministrazione origine dati ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "Configurare un DSN usando l'amministrazione origine dati ODBC")

1. Nella scheda **DSN utente** selezionare **Aggiungi** per aprire la finestra **Crea nuova origine dati**.

1. Selezionare **Microsoft Hive ODBC Driver** e quindi selezionare **Fine** per aprire la finestra di configurazione del **DSN per Microsoft Hive ODBC Driver.**

1. Digitare o selezionare i valori seguenti:

   | Proprietà | Descrizione |
   | --- | --- |
   |  Data Source Name |Assegnare un nome all'origine dati |
   |  Host |Immettere `HDInsightClusterName.azurehdinsight.net`. Ad esempio: `myHDICluster.azurehdinsight.net`. Nota: `HDInsightClusterName-int.azurehdinsight.net` è supportato finché viene eseguito il peered della macchina virtuale client nella stessa rete virtuale. |
   |  Porta |Utilizzare **443**. Questa porta è passata da 563 a 443. |
   |  Database |Utilizzare **default**. |
   |  Mechanism |Selezionare **Servizio HDInsight di Microsoft Azure** |
   |  Nome utente |Immettere il nome utente HTTP del cluster HDInsight. Il nome utente predefinito è **admin**. |
   |  Password |Immettere la password utente del cluster HDInsight. Selezionare la casella di controllo **Save Password (Encrypted)** (Salva password (crittografata)).|

1. Facoltativo: selezionare **Opzioni avanzate...**  

   | Parametro | Descrizione |
   | --- | --- |
   |  Use Native Query |Quando è selezionato, il driver ODBC NON cerca di convertire TSQL in HiveQL. Si deve utilizzare solo se sei sicuro al 100% che stai inviando dichiarazioni HiveQL pure. Quando ci si connette al database SQL di Azure o SQL Server, è consigliabile lasciarlo deselezionato. |
   |  Rows fetched per block |Quando si recupera un numero elevato di record, potrebbe essere necessario ottimizzare questo parametro per assicurare prestazioni ottimali. |
   |  Default string column length, Binary column length, Decimal column scale |Le lunghezze e le precisioni del tipo di dati potrebbero avere effetto sulla visualizzazione dei dati. Causano la restituzione di informazioni errate a causa della perdita di precisione e, o troncamento. |

    ![Opzioni di configurazione del DSN avanzate](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "Opzioni di configurazione del DSN avanzate")

1. Selezionare **Test** per testare l'origine dati. Quando l'origine dati è configurata correttamente, il risultato del test mostra **SUCCESS!**

1. Selezionare **OK** per chiudere la finestra Test.  

1. Selezionare **OK** per chiudere la finestra di configurazione del **DSN per Microsoft Hive ODBC Driver.**  

1. Selezionare **OK** per chiudere la finestra **Amministratore origine dati ODBC**.  

## <a name="import-data-into-excel-from-hdinsight"></a>Importazione di dati in Excel da HDInsight

La procedura seguente descrive come importare dati da una tabella Hive in una cartella di lavoro di Excel usando l'origine dati ODBC creata nella sezione precedente.

1. Aprire una cartella di lavoro nuova o esistente in Excel.

2. Nella scheda **Dati** passare a **Carica dati** > **Da altre origini** > **Da ODBC** per aprire la finestra **Da ODBC**.

    ![Aprire la connessione guidata dati di Excel](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "Aprire la connessione guidata dati di Excel")

3. Nell'elenco a discesa selezionare il nome dell'origine dati creata nell'ultima sezione e quindi scegliere **OK**.

4. Per il primo utilizzo, si aprirà una finestra di dialogo **del driver ODBC.** Seleziona **Finestre** dal menu a sinistra. Quindi selezionare **Connetti** per aprire la finestra **Navigatore.**

5. In **Strumento di spostamento** passare a **HIVE** > **default** > **hivesampletable** e quindi selezionare **Carica**. L'importazione dei dati in Excel potrebbe richiedere alcuni istanti.

    ![Navigatore ODBC di Excel Hive HDInsight](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "Navigatore ODBC di Excel Hive HDInsight")

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come usare Microsoft Hive ODBC Driver per recuperare dati dal servizio HDInsight in Excel. È analogamente possibile recuperare dati dal servizio HDInsight nel database SQL. È anche possibile caricare dati in un servizio HDInsight.It's also possible to upload data into an HDInsight Service. Per altre informazioni, vedere:

* [Visualizzare i dati di Apache Hive con Microsoft Power BI in Azure HDInsight.](apache-hadoop-connect-hive-power-bi.md)
* Visualizzare i dati di [Interactive Query Hive con Power BI in Azure HDInsight.](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [Connettere Excel ad Apache Hadoop utilizzando Power Query](apache-hadoop-connect-excel-power-query.md).
* [Connettersi ad Azure HDInsight ed eseguire query Apache Hive utilizzando Data Lake Tools per Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).

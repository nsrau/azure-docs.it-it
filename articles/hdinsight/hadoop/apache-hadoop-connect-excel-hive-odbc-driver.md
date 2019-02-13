---
title: Connettere Excel ad Apache Hadoop mediante Hive ODBC Driver - Azure HDInsight
description: Informazioni su come configurare e usare Microsoft Hive ODBC Driver per Excel per eseguire query su dati nei cluster HDInsight da Microsoft Excel.
keywords: hadoop excel,hive excel,hive odbc
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: hrasheed
ms.openlocfilehash: 0b4e2d3e7c75e6d25c7d519d4179a635a7833b6b
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2019
ms.locfileid: "55695322"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Connettere Excel ad Apache Hadoop in HDInsight mediante Microsoft Hive ODBC Driver

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

La soluzione Microsoft per Big Data integra componenti di Microsoft Business Intelligence (BI) con i cluster Apache Hadoop sviluppati in Azure HDInsight. Un esempio di questa integrazione è la possibilità di connettere Excel al data warehouse Hive di un cluster Hadoop in HDInsight mediante il driver Microsoft Hive Open Database Connectivity (ODBC).

È inoltre possibile connettere i dati associati a un cluster HDInsight e ad altre origini dati, inclusi altri cluster Hadoop (non HDInsight), da Excel mediante il componente aggiuntivo Microsoft Power Query per Excel. Per informazioni sull'installazione e l'uso di Power Query, vedere l'articolo su come [connettere Excel a HDInsight mediante Power Query][hdinsight-power-query].


## <a name="prerequisites"></a>Prerequisiti

Per eseguire le procedure descritte nell'articolo sono necessari gli elementi seguenti:

* Un cluster in HDInsight Hadoop. Per crearne uno, vedere [Introduzione all'uso di Hadoop in HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Una workstation con Office 2010 Professional Plus o versione successiva oppure Excel 2010 o versione successiva.

## <a name="install-microsoft-hive-odbc-driver"></a>Installare Microsoft Hive ODBC driver
Scaricare e installare la versione di [Microsoft Hive ODBC Driver][hive-odbc-driver-download] corrispondente alla versione dell'applicazione in cui si userà il driver ODBC.  Per questa esercitazione, il driver viene usato per Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Creare un'origine dati Apache Hive ODBC
I passaggi seguenti mostrano come creare un'origine dati Hive ODBC.

1. In Windows scegliere Start > Strumenti di amministrazione Windows > Origine dati (ODBC) (32 bit)/(64 bit).  Verrà aperta la finestra **Amministratore origine dati ODBC**.
   
    ![Amministratore origine dati ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "Configurare un DSN usando l'amministrazione origine dati ODBC")

2. Nella scheda **DSN utente** selezionare **Aggiungi** per aprire la finestra **Crea nuova origine dati**.

3. Selezionare **Microsoft Hive ODBC Driver** e quindi selezionare **Fine** per aprire la finestra di configurazione del**** DSN per Microsoft Hive ODBC Driver.

4. Digitare o selezionare i valori seguenti:
   
   | Proprietà | Descrizione |
   | --- | --- |
   |  Nome origine dati |Assegnare un nome all'origine dati |
   |  Host |Immettere &lt;HDInsightClusterName&gt;.azurehdinsight.net. Ad esempio, myHDICluster.azurehdinsight.net |
   |  Porta |Utilizzare <strong>443</strong>. Questa porta è passata da 563 a 443. |
   |  Database |Usare l'<strong>impostazione predefinita</strong>. |
   |  Mechanism |Selezionare <strong>Azure HDInsight Service</strong> |
   |  User Name |Immettere il nome utente HTTP del cluster HDInsight. Il nome utente predefinito è <strong>admin</strong>. |
   |  Password |Immettere la password utente del cluster HDInsight. |

   
5. Facoltativo: selezionare **Opzioni avanzate**  
   
   | Parametro | Descrizione |
   | --- | --- |
   |  Use Native Query |Quando viene selezionato, il driver ODBC NON cerca di convertire TSQL in HiveQL. Deve essere usato solo se si è assolutamente certi di inviare istruzioni HiveQL pure. Quando ci si connette al database SQL Server o SQL di Azure, è consigliabile lasciarlo deselezionato. |
   |  Rows fetched per block |Quando si recupera un numero elevato di record, potrebbe essere necessario ottimizzare questo parametro per assicurare prestazioni ottimali. |
   |  Default string column length, Binary column length, Decimal column scale |Le lunghezze e le precisioni del tipo di dati potrebbero avere effetto sulla visualizzazione dei dati. In questo caso verranno restituite informazioni non corrette a causa della perdita di precisione e/o ai troncamenti. |

    ![Opzioni avanzate](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "Opzioni di configurazione DSN avanzate")

5. Selezionare **Test** per testare l'origine dati. Quando l'origine dati è configurata correttamente, come risultato del test è indicato **Operazione riuscita**.  

6. Selezionare **OK** per chiudere la finestra Test.  

7. Selezionare **OK** per chiudere la finestra di configurazione del **** DSN per Microsoft Hive ODBC Driver.  

8. Selezionare **OK** per chiudere la finestra **Amministratore origine dati ODBC**.  

## <a name="import-data-into-excel-from-hdinsight"></a>Importazione di dati in Excel da HDInsight
La procedura seguente descrive come importare dati da una tabella Hive in una cartella di lavoro di Excel usando l'origine dati ODBC creata nella sezione precedente.

1. Aprire una cartella di lavoro nuova o esistente in Excel.

2. Nella scheda **Dati** passare a **Carica dati** > **Da altre origini** > **Da ODBC** per aprire la finestra **Da ODBC**.
   
    ![Aprire la Connessione guidata dati](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "Aprire la Connessione guidata dati")

3. Dall'elenco a discesa selezionare il nome dell'origine dati creata nell'ultima sezione e quindi selezionare **OK**.

4. Immettere il nome utente (il nome predefinito è admin) e la password di Hadoop e quindi selezionare **Connetti** per aprire la finestra **Strumento di spostamento**.

5. In **Strumento di spostamento** passare a **HIVE** > **default** > **hivesampletable** e quindi selezionare **Carica**. L'importazione dei dati in Excel potrebbe richiedere alcuni istanti.

    ![Strumento di spostamento per Hive ODBC in HDInsight](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "Aprire Connessione guidata dati")

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come usare Microsoft Hive ODBC Driver per recuperare dati dal servizio HDInsight in Excel. È analogamente possibile recuperare dati dal servizio HDInsight nel database SQL. È inoltre possibile caricare dati in un servizio HDInsight. Per altre informazioni, vedere:

* [Visualizzare i dati Apache Hive con Microsoft Power BI in Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualize Interactive Query Hive data with Power BI in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Visualizzare i dati Hive di Interactive Query con Power BI in Azure HDInsight).
* [Usare Apache Zeppelin per eseguire query Apache Hive in Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Connettere Excel ad Apache Hadoop mediante Power Query](apache-hadoop-connect-excel-power-query.md).
* [Connettersi ad Azure HDInsight ed eseguire query Apache Hive usando Strumenti Data Lake per Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Usare gli strumenti di Azure HDInsight per Visual Studio Code](../hdinsight-for-vscode.md).
* [Caricare dati in HDInsight](./../hdinsight-upload-data.md).

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]:hdinsight-use-hive.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query.md
[hive-odbc-driver-download]: https://go.microsoft.com/fwlink/?LinkID=286698



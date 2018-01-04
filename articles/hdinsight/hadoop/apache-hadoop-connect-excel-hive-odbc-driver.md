---
title: 'Connettere Excel a Hadoop mediante Hive ODBC Driver: Azure HDInsight | Microsoft Docs'
description: Informazioni su come configurare e usare Microsoft Hive ODBC Driver per Excel per eseguire query su dati nei cluster HDInsight da Microsoft Excel.
keywords: hadoop excel,hive excel,hive odbc
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
tags: azure-portal
editor: cgronlun
ms.assetid: a7665a14-0211-4521-b3e7-3b26e8029cc0
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/06/2017
ms.author: jgao
ms.openlocfilehash: d169645b9b701e611d27e0d984bf44b7ca85d2ad
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="connect-excel-to-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Connettere Excel a Hadoop in HDInsight mediante Microsoft Hive ODBC Driver

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

La soluzione Microsoft per Big Data integra componenti di Microsoft Business Intelligence (BI) con i cluster Apache Hadoop sviluppati da Azure HDInsight. Un esempio di questa integrazione è la possibilità di connettere Excel al data warehouse Hive di un cluster Hadoop in HDInsight mediante il driver Microsoft Hive Open Database Connectivity (ODBC).

È inoltre possibile connettere i dati associati a un cluster HDInsight e ad altre origini dati, inclusi altri cluster Hadoop (non HDInsight), da Excel mediante il componente aggiuntivo Microsoft Power Query per Excel. Per informazioni sull'installazione e l'uso di Power Query, vedere l'articolo su come [connettere Excel a HDInsight mediante Power Query][hdinsight-power-query].

> [!NOTE]
> Mentre i passaggi descritti in questo articolo possono essere utilizzati con un cluster HDInsight basato su Windows o Linux, Windows è necessario per la workstation client.
> 
> 

**Prerequisiti**:

Per eseguire le procedure descritte nell'articolo sono necessari gli elementi seguenti:

* **Un cluster HDInsight**. Per crearne uno, vedere [Introduzione all'uso di Hadoop in HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* **Una workstation** con Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oppure Office 2010 Professional Plus.

## <a name="install-microsoft-hive-odbc-driver"></a>Installare Microsoft Hive ODBC driver
Scaricare e installare Microsoft Hive ODBC Driver dall'[Area download][hive-odbc-driver-download].

Questo driver può essere installato nelle versioni a 32 bit o 64 bit di Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 e Windows Server 2012. Il driver consente la connessione ad Azure HDInsight (versione 1.6 e successive) e Azure HDInsight Emulator (versione 1.0.0.0 e successive). È consigliabile installare la versione corrispondente alla versione dell'applicazione in cui si userà il driver ODBC. Per questa esercitazione, il driver verrà usato da Office Excel.

## <a name="create-hive-odbc-data-source"></a>Creare un'origine dati Hive ODBC
I passaggi seguenti mostrano come creare un'origine dati Hive ODBC.

1. In Windows 8 o Windows 10, premere il tasto Windows per aprire la schermata Start e quindi digitare **data sources**.
2. Fare clic su **Configura origini dati ODBC (32 bit)** o **Configura origini dati ODBC (64 bit)** a seconda della versione di Office. Se si usa Windows 7, scegliere **Configura origini dati ODBC (32 bit)** o **Configura origini dati ODBC (64 bit)** da **Strumenti di amministrazione**. Verrà aperta la finestra di dialogo **Amministrazione origine dati ODBC**.
   
    ![Amministrazione origine dati ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "Configurare un DSN usando l'amministrazione origine dati ODBC")

3. Nel DNS utente fare clic su **Aggiungi** per aprire la procedura guidata **Crea nuova origine dati**.
4. Selezionare **Microsoft Hive ODBC Driver** e quindi fare clic su **Fine**. Verrà aperta la finestra di dialogo **Microsoft Hive ODBC Driver DNS Setup** (Configurazione DNS Microsoft Hive ODBC Driver).
5. Digitare o selezionare i valori seguenti:
   
   | Proprietà | DESCRIZIONE |
   | --- | --- |
   |  Data Source Name |Assegnare un nome all'origine dati |
   |  Host |Immettere &lt;HDInsightClusterName>.azurehdinsight.net. Ad esempio, myHDICluster.azurehdinsight.net |
   |  Porta |Utilizzare <strong>443</strong>. Questa porta è passata da 563 a 443. |
   |  Database |Usare l'<strong>impostazione predefinita</strong>. |
   |  Mechanism |Selezionare <strong>Azure HDInsight Service</strong> |
   |  User Name |Immettere il nome utente HTTP del cluster HDInsight. Il nome utente predefinito è <strong>admin</strong>. |
   |  Password |Immettere la password utente del cluster HDInsight. |
   
    </table>
   
    Esistono alcuni importanti parametri a cui prestare attenzione quando si fa clic su **Advanced Options**:
   
   | Parametro | DESCRIZIONE |
   | --- | --- |
   |  Use Native Query |Quando viene selezionato, il driver ODBC NON cerca di convertire TSQL in HiveQL. Deve essere usato solo se si è assolutamente certi di inviare istruzioni HiveQL pure. Quando ci si connette al database SQL Server o SQL di Azure, è consigliabile lasciarlo deselezionato. |
   |  Rows fetched per block |Quando si recupera un numero elevato di record, potrebbe essere necessario ottimizzare questo parametro per assicurare prestazioni ottimali. |
   |  Default string column length, Binary column length, Decimal column scale |Le lunghezze e le precisioni del tipo di dati potrebbero avere effetto sulla visualizzazione dei dati. In questo caso verranno restituite informazioni non corrette a causa della perdita di precisione e/o ai troncamenti. |

    ![Opzioni avanzate](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "Opzioni di configurazione DSN avanzate")

1. Fare clic su **Test** per testare l'origine dati. Quando l'origine dati è configurata correttamente, viene visualizzato *TESTS COMPLETED SUCCESSFULLY!*
2. Fare clic su **OK** per chiudere la finestra di dialogo Test. La nuova origine dati sarà elencata in **Amministrazione origine dati ODBC**.
3. Fare clic su **OK** per chiudere la procedura guidata.

## <a name="import-data-into-excel-from-hdinsight"></a>Importazione di dati in Excel da HDInsight
La procedura seguente descrive come importare dati da una tabella Hive in una cartella di lavoro di Excel usando l'origine dati ODBC creata nella sezione precedente.

1. Aprire una cartella di lavoro nuova o esistente in Excel.
2. Nella scheda **Dati** fare clic su **Dati**, su **Da altre origini** e quindi su **Da ODBC** per avviare **Connessione guidata dati**.
   
    ![Aprire la Connessione guidata dati](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "Aprire la Connessione guidata dati")
4. Selezione il nome dell'origine dati creata nell'ultima sezione e quindi fare clic su **OK**.
5. Immettere il nome utente (il nome predefinito è admin) e la password di Hadoop e quindi fare clic su **Connetti**.
6. Nello strumento di spostamento espandere **HIVE** e **default** e quindi fare clic su **hivesampletable** e infine su **Carica**. L'importazione dei dati in Excel potrebbe richiedere alcuni secondi.

    ![Strumento di spostamento per Hive ODBC in HDInsight](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "Aprire Connessione guidata dati")


## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come usare Microsoft Hive ODBC Driver per recuperare dati dal servizio HDInsight in Excel. È analogamente possibile recuperare dati dal servizio HDInsight nel database SQL. È inoltre possibile caricare dati in un servizio HDInsight. Per altre informazioni, vedere:

* [Visualizzare i dati Hive con Microsoft Power BI in Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualizzazione dei dati interattiva Query Hive con Power BI in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Usare Zeppelin per eseguire query Hive in Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Connettere Excel a Hadoop mediante Power Query](apache-hadoop-connect-excel-power-query.md).
* [Connettersi ad Azure HDInsight ed eseguire query Hive usando Strumenti Data Lake per Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Usare gli strumenti di Azure HDInsight per Visual Studio Code](../hdinsight-for-vscode.md).
* [Caricare dati in HDInsight](./../hdinsight-upload-data.md).

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]:hdinsight-use-hive.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query.md
[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698



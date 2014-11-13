<properties urlDisplayName="Connect Excel to HDInsight" pageTitle="Connettere Excel a Hadoop mediante Hive ODBC Driver | Azure" metaKeywords="" description="Informazioni su come configurare e usare Microsoft Hive ODBC Driver per Excel per eseguire query su dati in cluster HDInsight." metaCanonical="" services="hdinsight" documentationCenter="" title="Connettere Excel a Hadoop mediante Microsoft Hive ODBC Driver" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Connettere Excel a Hadoop mediante Microsoft Hive ODBC Driver

Una funzionalità chiave della soluzione Big Data di Microsoft è l'integrazione dei componenti di Microsoft Business Intelligence (BI) con i cluster Apache Hadoop sviluppati da Azure HDInsight. Un esempio di questa integrazione è la possibilità di connettere Excel al data warehouse Hive di un cluster Hadoop in HDInsight mediante Microsoft Hive Open Database Connectivity (ODBC) Driver.

È inoltre possibile connettere i dati associati a un cluster HDInsight e ad altre origini dati, inclusi altri cluster Hadoop (non HDInsight), da Excel mediante il componente aggiuntivo Microsoft Power Query per Excel. Per informazioni sull'installazione e l'utilizzo di Power Query, vedere [Connettere Excel a HDInsight mediante Power Query][Connettere Excel a HDInsight mediante Power Query].

**Prerequisiti**:

Per eseguire le procedure descritte nell'articolo è necessario:

-   Un cluster HDInsight. Per configurarne uno, vedere [Introduzione all'utilizzo di Azure HDInsight][Introduzione all'utilizzo di Azure HDInsight].
-   Un computer che esegue Windows 8, Windows 7, Windows Server 2012 o Windows Server 2008 R2.
-   Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oppure Office 2010 Professional Plus.

## Contenuto dell'articolo

1.  [Installazione di Microsoft Hive ODBC Driver][Installazione di Microsoft Hive ODBC Driver]
2.  [Creazione di un'origine dati Hive ODBC][Creazione di un'origine dati Hive ODBC]
3.  [Importazione di dati in Excel da un cluster HDInsight][Importazione di dati in Excel da un cluster HDInsight]
4.  [Passaggi successivi][Passaggi successivi]

## <span id="InstallHiveODBCDriver"></span></a>Installazione di Microsoft Hive ODBC Driver

Scaricare e installare Microsoft Hive ODBC Driver dall'[Area download][Area download].

Questo driver può essere installato nelle versioni a 32 bit o a 64 bit di Windows 7, Windows 8, Windows Server 2008 R2 e Windows Server 2012 e consentirà la connessione ad Azure HDInsight (versione 1.6 e successive) e ad HDInsight Emulator per Azure (v.1.0.0.0 e versioni successive). È consigliabile installare la versione corrispondente alla versione dell'applicazione in cui si utilizzerà il driver ODBC. Per questa esercitazione, il driver verrà usato da Office Excel.

## <span id="CreateHiveODBCDataSource"></span></a>Creazione di un'origine dati Hive ODBC

I passaggi seguenti mostrano come creare un'origine dati Hive ODBC.

1.  In Windows 8 premere il tasto Windows per aprire la schermata Start e quindi digitare **data sources**.
2.  Fare clic su **Configura origini dati ODBC (32 bit)** o **Configura origini dati ODBC (64 bit)** a seconda della versione di Office. Se si usa Windows 7, scegliere **Origini dati ODBC (32 bit)** o **Origini dati ODBC (64 bit)** da **Strumenti di amministrazione**. Verrà aperta la finestra di dialogo **Amministrazione origine dati ODBC**.

    ![Amministrazione origine dati ODBC][Amministrazione origine dati ODBC]

3.  In DNS utente fare clic su **Aggiungi** per aprire la procedura guidata **Crea nuova origine dati**.
4.  Selezionare **Microsoft Hive ODBC Driver** e quindi fare clic su **Fine**. Verrà aperta la finestra di dialogo **Microsoft Hive ODBC Driver DNS Setup**.

5.  Digitare o selezionare i valori seguenti:

    |------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Proprietà**    | **Descrizione**                                                                                                                                                                                                           |
    | Data Source Name | Assegnare un nome all'origine dati                                                                                                                                                                                        |
    | Host             | Immettere <hdinsightclustername>.azurehdinsight.net. Ad esempio, myHDICluster.azurehdinsight.net                                                                                                                          |
    | Port             | Usare **443**. Questa porta è passata da 563 a 443.                                                                                                                                                                       |
    | Database         | Usare **Default**.                                                                                                                                                                                                        |
    | Hive Server Type | Selezionare **Hive Server 2**                                                                                                                                                                                             |
    | Mechanism        | Selezionare **Azure HDInsight Service**                                                                                                                                                                                   |
    | HTTP Path        | Lasciare vuoto.                                                                                                                                                                                                           |
    | User Name        | Immettere il nome utente del cluster HDInsight. Si tratta del nome utente creato durante il processo di provisioning del cluster. Se è stata usata l'opzione di creazione rapida, il nome utente predefinito è **admin**. |
    | Password         | Immettere la password utente del cluster HDInsight.                                                                                                                                                                       |

    Esistono alcuni importanti parametri a cui prestare attenzione quando si fa clic su **Advanced Options**:

    |-------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Use Native Query              | Quando viene selezionato, il driver ODBC NON cercherà di convertire TSQL in HiveQL. Deve essere usato solo se si è assolutamente certi di inviare istruzioni HiveQL pure. Quando ci si connette al database SQL Server o SQL di Azure, è consigliabile lasciarlo deselezionato. |
    | Rows fetched per block        | Quando si recupera una grande quantità di record, potrebbe essere necessario ottimizzare questo parametro per assicurare prestazioni ottimali.                                                                                                                                  |
    | Default string column length, 
      Binary column length,         
      Decimal column scale          | Le lunghezze e le precisioni del tipo di dati potrebbero avere effetto sulla visualizzazione dei dati. In questo caso verranno restituite informazioni non corrette a causa della perdita di precisione e/o ai troncamenti.                                                     |

    ![Advanced options][Advanced options]

6.  Fare clic su **Test** per testare l'origine dati. Quando l'origine dati è configurata correttamente, viene visualizzato *TESTS COMPLETED SUCCESSFULLY!*
7.  Fare clic su **OK** per chiudere la finestra di dialogo Test. La nuova origine dati sarà ora elencata in **Amministrazione origine dati ODBC**.
8.  Fare clic su **OK** per chiudere la procedura guidata.

## <span id="ImportData"></span></a>Importazione di dati in Excel da un cluster HDInsight

I passaggi seguenti descrivono come importare dati da una tabella hive in una cartella di lavoro di Excel usando l'origine dati ODBC creata nei passaggi precedenti.

1.  Aprire una cartella di lavoro nuova o esistente in Excel.
2.  Nella scheda **Dati** fare clic sul riquadro **Carica dati esterni**, quindi su **Da altre origini** e infine su **Da Connessione guidata dati** per avviare la **Connessione guidata dati**.

    ![Aprire la Connessione guidata dati][Aprire la Connessione guidata dati]

3.  Selezionare **Nome origine dati (DSN) ODBC** come origine dati e quindi fare clic su **Avanti**.
4.  Nelle origini dati ODBC selezionare il nome dell'origine dati creato nel passaggio precedente e quindi fare clic su **Avanti**.
5.  Immettere nuovamente la password per il cluster nella procedura guidata e quindi fare clic su **Test** per verificare la configurazione.
6.  Fare clic su **OK** per chiudere la finestra di dialogo Test.
7.  Fare clic su **OK**. Attendere l'apertura della finestra di dialogo **Seleziona database e tabella**. Questa operazione potrebbe richiedere alcuni secondi.
8.  Selezionare la tabella che si desidera importare e quindi fare clic su **Avanti**. *hivesampletable* è una tabella hive di esempio disponibile con i cluster HDInsight. È possibile sceglierla se non ne è stata creata una. Per altre informazioni sull'esecuzione di query Hive e sulla creazione di tabelle Hive, vedere [Usare Hive con HDInsight][Usare Hive con HDInsight].
9.  Fare clic su **Finish**.
10. Nella finestra di dialogo **Importa dati** è possibile modificare o specificare la query. A questo scopo, fare clic su **Proprietà**. Questa operazione potrebbe richiedere alcuni secondi.
11. Fare clic sulla scheda **Definizione** e quindi aggiungere **LIMIT 200** all'istruzione di selezione Hive nella casella di testo **Testo comando**. La modifica limiterà a 200 il set di record restituiti.

    ![Proprietà connessione][Proprietà connessione]

12. Fare clic su **OK** per chiudere la finestra di dialogo Proprietà connessione.
13. Fare clic su **OK** per chiudere la finestra di dialogo **Importa dati**.
14. Immettere nuovamente la password e quindi fare clic su **OK**. L'importazione dei dati in Excel potrebbe richiedere alcuni secondi.

## <span id="nextsteps"></span></a>Passaggi successivi

In questo articolo è stato illustrato come usare Microsoft Hive ODBC Driver per recuperare dati dal servizio HDInsight in Excel. È analogamente possibile recuperare dati dal servizio HDInsight nel database SQL. È inoltre possibile caricare dati in un servizio HDInsight. Per altre informazioni, vedere:

-   [Analizzare i dati sui ritardi dei voli usando HDInsight][Analizzare i dati sui ritardi dei voli usando HDInsight]
-   [Caricare i dati in HDInsight][Caricare i dati in HDInsight]
-   [Usare Sqoop con HDInsight][Usare Sqoop con HDInsight]

  [Connettere Excel a HDInsight mediante Power Query]: ../hdinsight-connect-excel-power-query/
  [Introduzione all'utilizzo di Azure HDInsight]: ../hdinsight-get-started/
  [Installazione di Microsoft Hive ODBC Driver]: #InstallHiveODBCDriver
  [Creazione di un'origine dati Hive ODBC]: #CreateHiveODBCDataSource
  [Importazione di dati in Excel da un cluster HDInsight]: #ImportData
  [Passaggi successivi]: #nextsteps
  [Area download]: http://go.microsoft.com/fwlink/?LinkID=286698
  [Amministrazione origine dati ODBC]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png
  [Advanced options]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png
  [Aprire la Connessione guidata dati]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png
  [Usare Hive con HDInsight]: ../hdinsight-use-hive/
  [Proprietà connessione]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png
  [Analizzare i dati sui ritardi dei voli usando HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Caricare i dati in HDInsight]: ../hdinsight-upload-data/
  [Usare Sqoop con HDInsight]: ../hdinsight-use-sqoop/

<properties  linkid="manage-services-hdinsight-connect-excel-with-hive-ODBC" urlDisplayName="Connect Excel to HDInsight" pageTitle="Connect Excel to HDInsight with the Hive ODBC Driver | Azure" metaKeywords="" description="Learn how to set up and use the Microsoft Hive ODBC driver for Excel to query data in an HDInsight cluster." metaCanonical="" services="hdinsight" documentationCenter="" title="Connect Excel to HDInsight with the Microsoft Hive ODBC Driver" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

# Connessione di Excel a HDInsight mediante Microsoft Hive ODBC Driver

Una funzionalità chiave della soluzione Big Data di Microsoft è l'integrazione dei componenti di Microsoft Business Intelligence (BI) con i cluster Apache Hadoop sviluppati da Azure HDInsight. Un esempio di questa integrazione è la possibilità di connettere Excel al data warehouse Hive di un cluster HDInsight Hadoop mediante Microsoft Hive Open Database Connectivity (ODBC) Driver.

È inoltre possibile connettere i dati associati a un cluster HDInsight e ad altre origini dati, inclusi altri cluster Hadoop (non HDInsight), da Excel mediante il componente aggiuntivo Microsoft Power Query per Excel. Per informazioni sull'installazione e l'utilizzo di Power Query, vedere [Connessione di Excel a HDInsight con Power
Query](/it-it/documentation/articles/hdinsight-connect-excel-power-query/).

**Prerequisiti**:

Per eseguire le procedure descritte nell'articolo, è necessario disporre di quanto segue:

* Un cluster HDInsight. Per configurarne uno, vedere [Introduzione
  all'utilizzo di Azure
  HDInsight](/it-it/documentation/articles/hdinsight-get-started/).
* Un computer che esegue Windows 8, Windows 7, Windows Server 2012 o
  Windows Server 2008 R2.
* Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013
  Standalone oppure Office 2010 Professional Plus.

## Contenuto dell'articolo

1.  [Installazione di Microsoft Hive ODBC
    Driver](#InstallHiveODBCDriver)
2.  [Creazione di un'origine dati Hive ODBC](#CreateHiveODBCDataSource) 3.  [Importazione di dati in Excel da un cluster HDInsight](#ImportData) 4.  [Passaggi successivi](#nextsteps)

## <a id="InstallHiveODBCDriver" ></a>Installazione di Microsoft Hive ODBC Driver

Scaricare e installare Microsoft Hive ODBC Driver dall'[Area download][1].

Questo driver può essere installato nelle versioni a 32 bit o a 64 bit di Windows 7, Windows 8, Windows Server 2008 R2 e Windows Server 2012 e consentirà la connessione ad Azure HDInsight (versione 1.6 e successive) e ad HDInsight Emulator per Azure (v.1.0.0.0 e versioni successive). È consigliabile installare la versione corrispondente alla versione dell'applicazione in cui si utilizzerà il driver ODBC. Per questa esercitazione, il driver verrà utilizzato da Office Excel.

## <a id="CreateHiveODBCDataSource" ></a>Creazione di un'origine dati Hive ODBC

I passaggi seguenti mostrano come creare un'origine dati Hive ODBC.

1.  In Windows 8 premere il tasto Windows per aprire la schermata Start e quindi digitare **data sources**.
2.  Fare clic su **Configura origini dati ODBC (32 bit)** o **Configura origini dati ODBC (64 bit)** a seconda della versione di Office. Se si utilizza Windows 7, scegliere **Origini dati ODBC (32 bit)** o **Origini dati ODBC (64 bit)** da **Strumenti di amministrazione**. Verrà aperta la finestra di dialogo **Amministrazione origine dati ODBC**.
    
    ![Amministrazione origine dati
    ODBC](./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png)

3.  In DNS utente fare clic su **Aggiungi** per aprire la procedura guidata **Crea nuova origine dati**.
4.  Selezionare **Microsoft Hive ODBC Driver** e quindi fare clic su **Fine**. Verrà aperta la finestra di dialogo **Microsoft Hive ODBC Driver DNS Setup**.

5.  Digitare o selezionare i valori seguenti:
    
    <table  border="1">
     <tr><td><strong>Proprietà</strong>
    </td>
    <td><strong>Descrizione</strong>
    </td>
    </tr>
    
     <tr><td>Data Source Name</td>
    <td>Assegnare un nome all'origine dati</td>
    </tr>
    
     <tr><td>Host</td>
    <td>Immettere <hdinsightclustername>.azurehdinsight.net. Ad esempio, myHDICluster.azurehdinsight.net
     <tr><td>Port</td>
    <td>Utilizzare <strong>443</strong>
    . Questa porta è passata da 563 a 443.</td>
    </tr>
    
     <tr><td>Database</td>
    <td>Utilizzare <strong>Default</strong>
    .</td>
    </tr>
    
     <tr><td>Hive Server Type</td>
    <td>Selezionare <strong>Hive Server 2</strong>
    </td>
    </tr>
    
     <tr><td>Mechanism</td>
    <td>Selezionare <strong>Azure HDInsight Service</strong>
    </td>
    </tr>
    
     <tr><td>HTTP Path</td>
    <td>Lasciare vuoto.</td>
    </tr>
    
     <tr><td>User Name</td>
    <td>Immettere il nome utente del cluster HDInsight. Si tratta del nome utente creato durante il processo di provisioning del cluster. Se è stata utilizzata l'opzione di creazione rapida, il nome utente predefinito è <strong>admin</strong>
    .</td>
    </tr>
    
     <tr><td>Password</td>
    <td>Immettere la password utente del cluster HDInsight.</td>
    </tr>
    
     
    
     Esistono alcuni importanti parametri a cui prestare attenzione quando si fa clic su **Advanced Options**:
    
     <table  border="1">
     <tr><td>Use Native Query</td>
    <td>Quando viene selezionato, il driver ODBC NON cercherà di convertire TSQL in HiveQL. Deve essere utilizzato solo se si è assolutamente certi di inviare istruzioni HiveQL pure. Quando ci si connette al database SQL Server o SQL di Azure, è consigliabile lasciarlo deselezionato.</td>
    </tr>
    
     <tr><td>Rows fetched per block</td>
    <td>Quando si recupera una grande quantità di record, potrebbe essere necessario ottimizzare questo parametro per assicurare prestazioni ottimali.</td>
    </tr>
    
     <tr><td>Default string column length, <br  />
     Binary column length,  <br  />
     Decimal column scale</td>
    <td>Le lunghezze e le precisioni del tipo di dati potrebbero avere effetto sulla visualizzazione dei dati. In questo caso verranno restituite informazioni non corrette a causa della perdita di precisione e/o ai troncamenti.</td>
    </tr>
    
     </table>
    
    
     ![Advanced options][img-HiveOdbc-DataSource-AdvancedOptions]
    
    </hdinsightclustername>
    </td>
    </tr>
    </table>

6.  Fare clic su **Test** per testare l'origine dati. Quando l'origine dati è configurata correttamente, viene visualizzato *TESTS COMPLETED SUCCESSFULLY!*
7.  Fare clic su **OK** per chiudere la finestra di dialogo Test. La nuova origine dati sarà ora elencata in **Amministrazione origine dati ODBC**.

8.  Fare clic su **OK** per chiudere la procedura guidata.

## <a id="ImportData" ></a>Importazione di dati in Excel da un cluster HDInsight

I passaggi seguenti descrivono come importare dati da una tabella hive in una cartella di lavoro di Excel utilizzando l'origine dati ODBC creata nei passaggi precedenti.

1.  Aprire una cartella di lavoro nuova o esistente in Excel. 2.  Nella scheda **Dati** fare clic sul riquadro **Carica dati esterni**, quindi su **Da altre origini** e infine su **Da Connessione guidata dati** per avviare la **Connessione guidata dati**.
    
    ![Aprire la Connessione guidata
    dati](./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png)

3.  Selezionare **Nome origine dati (DSN) ODBC** come origine dati e quindi fare clic su **Avanti**.
4.  Nelle origini dati ODBC selezionare il nome dell'origine dati creato nel passaggio precedente e quindi fare clic su **Avanti**.
5.  Immettere nuovamente la password per il cluster nella procedura guidata e quindi fare clic su **Test** per verificare la configurazione.
6.  Fare clic su **OK** per chiudere la finestra di dialogo Test. 7.  Fare clic su **OK**. Attendere l'apertura della finestra di dialogo **Seleziona database e tabella**. Questa operazione potrebbe richiedere alcuni secondi.
8.  Selezionare la tabella che si desidera importare e quindi fare clic su **Avanti**. *hivesampletable* è una tabella hive di esempio disponibile con i cluster HDInsight. È possibile sceglierla se non ne è stata creata una. Per ulteriori informazioni sull'esecuzione di query Hive e sulla creazione di tabelle Hive, vedere [Utilizzo di Hive con HDInsight](/it-it/documentation/articles/hdinsight-use-hive/).
9.  Fare clic su **Finish**. 10. Nella finestra di dialogo **Importa dati** è possibile modificare o specificare la query. A questo scopo, fare clic su **Proprietà**. Questa operazione potrebbe richiedere alcuni secondi.
11. Fare clic sulla scheda **Definizione** e quindi aggiungere **LIMIT 200** all'istruzione di selezione Hive nella casella di testo **Testo comando**. La modifica limiterà a 200 il set di record restituiti.
    
    ![Proprietà
    connessione](./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png)

12. Fare clic su **OK** per chiudere la finestra di dialogo Proprietà connessione.
13. Fare clic su **OK** per chiudere la finestra di dialogo **Importa dati**.
14. Immettere nuovamente la password e quindi fare clic su **OK**. L'importazione dei dati in Excel potrebbe richiedere alcuni secondi.

## <a id="nextsteps" ></a>Passaggi successivi

In questo articolo è stato illustrato come utilizzare Microsoft Hive ODBC Driver per recuperare dati dal servizio HDInsight in Excel. È analogamente possibile recuperare dati dal servizio HDInsight nel database SQL. È inoltre possibile caricare dati in un servizio HDInsight. Per ulteriori informazioni, vedere:

* [Analisi dei dati sui ritardi dei voli utilizzando
  HDInsight](/it-it/documentation/articles/hdinsight-analyze-flight-delay-data/)
* [Caricamento di dati in
  HDInsight](/it-it/documentation/articles/hdinsight-upload-data/)
* [Utilizzo di Sqoop con HDInsight.](../hdinsight-use-sqoop/)



[1]: http://go.microsoft.com/fwlink/?LinkID=286698
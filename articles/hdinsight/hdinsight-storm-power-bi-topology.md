<properties
 pageTitle="Usare Apache Storm con Power BI | Microsoft Azure"
 description="Creare un report di Power BI con i dati da una topologia C# in esecuzione in un cluster Apache Storm in HDInsight."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="08/16/2016"
 ms.author="larryfr"/>

# Usare Power BI per visualizzare i dati provenienti da una topologia Apache Storm

Power BI consente di mostrare visivamente i dati sotto forma di report. Con i modelli di Visual Studio per Storm in HDInsight è possibile usare facilmente in SQL Azure i dati archiviati da una topologia in esecuzione in Apache Storm in un cluster HDInsight e quindi visualizzarli tramite Power BI.

Questo documento illustra come usare Power BI per creare un report dai dati generati da una topologia Apache Storm e archiviati nel database SQL di Azure.

## Prerequisiti

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un utente di Azure Active Directory con accesso [Power BI](https://powerbi.com)

* Visual Studio (una delle versioni seguenti)

    * Visual Studio 2012 con [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)

    * Visual Studio 2013 con [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) o [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409)

    * [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)

* Gli strumenti HDInsight per Visual Studio. Per informazioni sull'installazione, vedere [Introduzione all'uso di HDInsight Tools per Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

## Funzionamento

Questo esempio contiene una topologia Storm C# che genera in modo casuale i dati di log di Internet Information Services (IIS). I dati vengono quindi scritti in un database SQL e da qui vengono usati per generare report in Power BI.

Di seguito è riportato un elenco dei file che implementano la funzionalità principale di questo esempio.

* **SqlAzureBolt.cs**: scrive nel database SQL le informazioni generate nella topologia Storm.

* **IISLogsTable.sql**: istruzioni Transact-SQL usate per generare il database in cui vengono archiviati i dati.

> [AZURE.WARNING] È necessario creare la tabella nel database SQL prima di avviare la topologia nel cluster HDInsight.

## Scaricare l'esempio

Scaricare l'[esempio di Power BI per C# Storm in HDInsight](https://github.com/Azure-Samples/hdinsight-dotnet-storm-powerbi). Per scaricarlo, biforcarlo o clonarlo mediante [git](http://git-scm.com/) oppure usare il collegamento **Download** per scaricare un file con estensione zip dell'archivio.

## Creare un database

1. Usare i passaggi nel documento [Esercitazione sul database SQL](../sql-database/sql-database-get-started.md) per creare un nuovo database SQL.

2. Connettersi al database seguendo i passaggi nel documento [Connettersi al database SQL con SQL Server Management Studio](../sql-database/sql-database-connect-query.md) per stabilire la connessione al database.

4. Fare clic con il pulsante destro del mouse sul database in Esplora oggetti e creare una __Nuova query__. Incollare il contenuto del file __IISLogsTable.sql__ incluso nel progetto scaricato nella finestra di query e quindi usare CTRL+MAIUSC+E per eseguire la query. Verrà visualizzato un messaggio per informare che i comandi sono stati eseguiti correttamente.

    Al termine, nel database sarà disponibile una nuova tabella denominata __IISLOGS__.

## Configurare l'esempio

1. Nel [portale di Azure](https://portal.azure.com) selezionare il database SQL. Nella sezione __Informazioni di base__ sezione del pannello Database SQL selezionare __Mostra stringhe di connessione del database__. Nell'elenco visualizzato copiare le informazioni relative a __ADO.NET (autenticazione SQL)__.

1. Aprire l'esempio in Visual Studio. In **Esplora soluzioni** aprire il file **App.config** e quindi trovare la voce seguente:

        <add key="SqlAzureConnectionString" value="##TOBEFILLED##" />
    
    Sostituire il valore __##TOBEFILLED##__ con la stringa di connessione del database copiata nel passaggio precedente. Sostituire __{your\_username}__ e __{your\_password}__ con il nome utente e la password per il database.

2. Salvare e chiudere i file.

## Distribuire l'esempio

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **StormToSQL** e quindi scegliere **Invia a Storm in HDInsight**. Selezionare il cluster HDInsight dall'elenco a discesa **Cluster Storm**.

    > [AZURE.NOTE] Il popolamento dell'elenco a discesa **Cluster Storm** con i nomi dei server potrebbe richiedere alcuni minuti.
    >
    > Se richiesto, immettere le credenziali di accesso per la sottoscrizione di Azure. Se si dispone di più di una sottoscrizione, accedere a quella che contiene il cluster Storm in HDInsight.

2. Dopo che la topologia è stata inviata correttamente, verrà visualizzato l'elenco Topologie Storm relativo al cluster. Selezionare dall'elenco la voce SqlAzureWriterTopology per visualizzare le informazioni sulla topologia in esecuzione.

    ![Topologie con la topologia selezionata](./media/hdinsight-storm-power-bi-topology/topologyview.png)

    È possibile usare questa visualizzazione per ottenere informazioni sulla topologia o fare doppio clic sulle voci, ad esempio SqlAzureBolt, per visualizzare informazioni specifiche di un componente della topologia.

3. Dopo l'esecuzione della topologia per alcuni minuti, tornare alla finestra di query SQL usata per creare il database. Sostituire le istruzioni esistenti con le seguenti:

        select * from iislogs;
    
    Usare CTRL+MAIUSC+E per eseguire la query. Verranno visualizzati risultati simili ai seguenti.
    
        1	2016-05-27 17:57:14.797	255.255.255.255	/bar	GET	200
        2	2016-05-27 17:57:14.843	127.0.0.1	/spam/eggs	POST	500
        3	2016-05-27 17:57:14.850	123.123.123.123	/eggs	DELETE	200
        4	2016-05-27 17:57:14.853	127.0.0.1	/foo	POST	404
        5	2016-05-27 17:57:14.853	10.9.8.7	/bar	GET	200
        6	2016-05-27 17:57:14.857	192.168.1.1	/spam	DELETE	200

    Si tratta di dati scritti dalla topologia Storm.

## Creare un report

1. Connettersi al [connettore Database SQL di Azure](https://app.powerbi.com/getdata/bigdata/azure-sql-database-with-live-connect) per Power BI.

2. In __Database__ selezionare __Recupera__.

3. Selezionare __Database SQL di Azure__ e quindi selezionare __Connetti__.

4. Immettere le informazioni per connettersi al database SQL di Azure. È possibile trovare queste informazioni nel [portale di Azure](https://portal.azure.com) selezionando il proprio database SQL.

    > [AZURE.NOTE] È anche possibile impostare l'intervallo di aggiornamento e i filtri personalizzati tramite __Abilita opzioni avanzate__ dalla finestra di dialogo di connessione.

5. Dopo avere stabilito la connessione, verrà visualizzato un nuovo set di dati con lo stesso nome del database al cui ci si è connessi. Selezionare il set di dati per iniziare a progettare un report.

3. In __ampi__, espandere la voce __IISLOGS__. Selezionare la casella di controllo __URISTEM__. Verrà creato un nuovo report che elenca le origini URI (/ foo, /bar e così via) registrate nel database.

    ![Creazione di un report](./media/hdinsight-storm-power-bi-topology/createreport.png)

5. Trascinare quindi __METHOD__ nel report. Il report verrà aggiornato per elencare le origini e il metodo HTTP corrispondente usato per la richiesta HTTP.

    ![aggiunta dei dati di method](./media/hdinsight-storm-power-bi-topology/uristemandmethod.png)

4. Nella colonna __Visualizzazioni__ selezionare l'icona __Campi__ e quindi selezionare la freccia in giù accanto a __METHOD__ nella sezione __Valori__. Nell'elenco visualizzato selezionare __Conteggio__. Il report verrà modificato per elencare un conteggio degli accessi a un URI specifico.

    ![Modifica di un conteggio di method](./media/hdinsight-storm-power-bi-topology/count.png)

6. Selezionare quindi __Istogramma a colonne in pila__ per modificare la modalità di visualizzazione delle informazioni.

    ![Modifica in un grafico in pila](./media/hdinsight-storm-power-bi-topology/stackedcolumn.png)

7. Una volta ottenuto il report desiderato, scegliere la voce __Salva__ dal menu per immettere un nome e salvare il report.

## Arrestare la topologia

La topologia rimarrà in esecuzione fino all'arresto o all'eliminazione del cluster Storm in HDInsight. Per arrestare la topologia, eseguire la procedura seguente.

1. In Visual Studio tornare al visualizzatore della topologia e selezionarla.

2. Fare clic sul pulsante **Termina** per arrestare la topologia.

    ![Pulsante Termina nel riepilogo della topologia](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## Eliminare il cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## Passaggi successivi

In questo documento è stato illustrato come inviare dati da una topologia Storm al database SQL e quindi visualizzare i dati tramite Power BI. Per informazioni su come usare altre tecnologie di Azure con Storm in HDInsight, vedere:

* [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md)

<!---HONumber=AcomDC_0914_2016-->
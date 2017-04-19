---
title: Usare Apache Storm con Power BI | Documentazione Microsoft
description: Creare un report di Power BI con i dati da una topologia C# in esecuzione in un cluster Apache Storm in HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 36fe3b9c-5232-4464-8d75-95403b6da7a1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: bedb6816e4f203687529e95ffa24688e86b5a3cb
ms.lasthandoff: 04/12/2017


---
# <a name="use-power-bi-to-visualize-data-from-an-apache-storm-topology"></a>Usare Power BI per visualizzare i dati provenienti da una topologia Apache Storm

Power BI consente di mostrare visivamente i dati sotto forma di report. Questo documento fornisce un esempio dell'uso di Apache Storm in HDInsight per la generazione di dati per Power BI.

> [!NOTE]
> Anche se i passaggi illustrati in questo documento si basano su un ambiente di sviluppo Windows con Visual Studio, il progetto compilato può essere inviato a un cluster HDInsight basato su Linux o su Windows. Solo i cluster basati su Linux creati dopo il 28/10/2016 supportano le topologie SCP.NET.
>
> Per usare una topologia C# con un cluster basato su Linux, aggiornare il pacchetto NuGet Microsoft.SCP.Net.SDK usato dal progetto alla versione 0.10.0.6 o successiva. La versione del pacchetto deve anche corrispondere alla versione principale di Storm installata in HDInsight. Ad esempio, le versioni 3.3 e 3.4 di Storm in HDInsight usano Storm versione 0.10.x, mentre HDInsight 3.5 usa Storm 1.0.x.
>
> Le topologie C# nei cluster basati su Linux devono usare .NET 4.5 e devono usare Mono per l'esecuzione nel cluster HDInsight. Anche se non dovrebbero verificarsi problemi, è consigliabile vedere il documento [Mono Compatibility](http://www.mono-project.com/docs/about-mono/compatibility/) (Compatibilità di Mono) per potenziali incompatibilità.
>
> Per una versione Java di questo progetto, che funziona con HDInsight basato su Linux o Windows, vedere [Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).

## <a name="prerequisites"></a>Prerequisiti

* Un utente di Azure Active Directory con accesso [Power BI](https://powerbi.com).
* Un cluster HDInsight. Per altre informazioni, vedere [Introduzione a Storm in HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md).

  > [!IMPORTANT]
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

* Visual Studio (una delle versioni seguenti)

  * Visual Studio 2012 con [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)
  * Visual Studio 2013 con [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) o [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
  * Visual Studio 2017, qualsiasi edizione

* Gli strumenti HDInsight per Visual Studio. Per informazioni sull'installazione, vedere [Introduzione all'uso di HDInsight Tools per Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

## <a name="how-it-works"></a>Funzionamento

Questo esempio contiene una topologia Storm C# che genera in modo casuale i dati di log di Internet Information Services (IIS). I dati vengono quindi scritti in un database SQL e da qui vengono usati per generare report in Power BI.

I file seguenti implementano la funzionalità principale di questo esempio:

* **SqlAzureBolt.cs**: scrive nel database SQL le informazioni generate nella topologia Storm.
* **IISLogsTable.sql**: istruzioni Transact-SQL usate per generare il database in cui vengono archiviati i dati.

> [!WARNING]
> Creare la tabella nel database SQL prima di avviare la topologia nel cluster HDInsight.

## <a name="download-the-example"></a>Scaricare l'esempio

Scaricare l' [esempio di Power BI per C# Storm in HDInsight](https://github.com/Azure-Samples/hdinsight-dotnet-storm-powerbi). Per scaricarlo, biforcarlo o clonarlo mediante [git](http://git-scm.com/)oppure usare il collegamento **Download** per scaricare un file con estensione zip dell'archivio.

## <a name="create-a-database"></a>Creare un database

1. Per creare un database, seguire la procedura riportata nel documento [Esercitazione sul database SQL](../sql-database/sql-database-get-started.md).

2. Connettersi al database seguendo la procedura riportata nel documento [Connettersi a un database SQL con Visual Studio](../sql-database/sql-database-connect-query.md).

3. In Esplora oggetti fare clic con il pulsante destro del mouse sul database e scegliere **Nuova query**. Incollare il contenuto del file **IISLogsTable.sql** incluso nel progetto scaricato nella finestra di query e quindi usare CTRL+MAIUSC+E per eseguire la query. Verrà visualizzato un messaggio per informare che i comandi sono stati eseguiti correttamente.

## <a name="configure-the-sample"></a>Configurare l'esempio

1. Nel [portale di Azure](https://portal.azure.com)selezionare il database SQL. Nella sezione **Informazioni di base** del pannello Database SQL selezionare **Mostra stringhe di connessione del database**. Nell'elenco visualizzato copiare le informazioni relative a **ADO.NET (autenticazione SQL)** .

2. Aprire l'esempio in Visual Studio. In **Esplora soluzioni** aprire il file **App.config** e quindi trovare la voce seguente:

        <add key="SqlAzureConnectionString" value="##TOBEFILLED##" />

    Sostituire il valore **##TOBEFILLED##** con la stringa di connessione del database copiata nel passaggio precedente. Sostituire **{your\_username}** e **{your\_password}** con il nome utente e la password per il database.

3. Salvare e chiudere i file.

## <a name="deploy-the-sample"></a>Distribuire l'esempio

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **StormToSQL** e quindi scegliere **Submit to Storm on HDInsight** (Invia a Storm in HDInsight). Selezionare il cluster HDInsight dall'elenco a discesa **Cluster Storm** .

   > [!NOTE]
   > Il popolamento dell'elenco a discesa **Cluster Storm** con i nomi dei server potrebbe richiedere alcuni minuti.
   >
   > Se richiesto, immettere le credenziali di accesso per la sottoscrizione di Azure. Se si dispone di più di una sottoscrizione, accedere a quella che contiene il cluster Storm in HDInsight.

2. Dopo l'invio della topologia, verrà aperto __Storm Topologies Viewer__ (Visualizzatore topologie Storm). Per visualizzare la topologia, selezionare dall'elenco la voce SqlAzureWriterTopology.

    ![Topologie con la topologia selezionata](./media/hdinsight-storm-power-bi-topology/topologyview.png)

    È possibile usare questa visualizzazione per ottenere informazioni sulla topologia. In alternativa, fare doppio clic su una voce, ad esempio SqlAzureBolt, per visualizzare informazioni specifiche di un componente della topologia.

3. Dopo l'esecuzione della topologia per alcuni minuti, tornare alla finestra di query SQL usata per creare il database. Sostituire le istruzioni esistenti con la query seguente:

        select * from iislogs;

    Usare CTRL+MAIUSC+E per eseguire la query. Verranno visualizzati risultati simili ai seguenti:

        1    2016-05-27 17:57:14.797    255.255.255.255    /bar    GET    200
        2    2016-05-27 17:57:14.843    127.0.0.1    /spam/eggs    POST    500
        3    2016-05-27 17:57:14.850    123.123.123.123    /eggs    DELETE    200
        4    2016-05-27 17:57:14.853    127.0.0.1    /foo    POST    404
        5    2016-05-27 17:57:14.853    10.9.8.7    /bar    GET    200
        6    2016-05-27 17:57:14.857    192.168.1.1    /spam    DELETE    200

    Si tratta di dati scritti dalla topologia Storm.

## <a name="create-a-report"></a>Creare un report

1. Connettersi al [connettore Database SQL di Azure](https://app.powerbi.com/getdata/bigdata/azure-sql-database-with-live-connect) per Power BI.

2. In **Database** selezionare **Recupera**.

3. Selezionare **Database SQL di Azure** e quindi **Connetti**.

4. Immettere le informazioni per connettersi al database SQL di Azure. È possibile trovare queste informazioni nel [portale di Azure](https://portal.azure.com) selezionando il proprio database SQL.

   > [!NOTE]
   > È anche possibile impostare l'intervallo di aggiornamento e i filtri personalizzati tramite **Abilita opzioni avanzate** dalla finestra di dialogo di connessione.

5. Dopo avere stabilito la connessione, verrà visualizzato un nuovo set di dati con lo stesso nome del database al cui ci si è connessi. Selezionare il set di dati per iniziare a progettare un report.

6. In **Campi** espandere la voce **IISLOGS**. Selezionare la casella di controllo **URISTEM**. Verrà creato un report che elenca le origini URI (/foo, /bar e così via) registrate nel database.

    ![Creazione di un report](./media/hdinsight-storm-power-bi-topology/createreport.png)

7. Trascinare quindi **METHOD** nel report. Il report verrà aggiornato per elencare le origini e il metodo HTTP corrispondente usato per la richiesta HTTP.

    ![aggiunta dei dati di method](./media/hdinsight-storm-power-bi-topology/uristemandmethod.png)

8. Nella colonna **Visualizzazioni** selezionare l'icona **Campi** e quindi selezionare la freccia a discesa accanto a **METHOD** nella sezione **Valori**. Nell'elenco visualizzato selezionare **Conteggio**. Il report viene modificato per elencare un conteggio degli accessi a un URI specifico.

    ![Modifica di un conteggio di method](./media/hdinsight-storm-power-bi-topology/count.png)

9. Selezionare quindi **Istogramma a colonne in pila** per modificare la modalità di visualizzazione delle informazioni.

    ![Modifica in un grafico in pila](./media/hdinsight-storm-power-bi-topology/stackedcolumn.png)

10. Per salvare il report, selezionare **Salva** e immettere un nome per il report.

## <a name="stop-the-topology"></a>Arrestare la topologia

La topologia rimane in esecuzione fino all'arresto o all'eliminazione del cluster Storm in HDInsight. Per arrestare la topologia, eseguire la procedura seguente.

1. In Visual Studio tornare al visualizzatore della topologia e selezionarla.

2. Fare clic sul pulsante **Termina** per arrestare la topologia.

    ![Pulsante Termina nel riepilogo della topologia](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Eliminare il cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo documento è stato illustrato come inviare dati da una topologia Storm al database SQL e quindi visualizzare i dati tramite Power BI. Per informazioni su come usare altre tecnologie di Azure con Storm in HDInsight, vedere:

* [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md)


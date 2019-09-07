---
title: Usare Grafana in Azure HDInsight
description: Informazioni su come accedere al dashboard di Grafana con cluster di Apache Hadoop in Azure HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: a8d79e15a0c967c4b00f337928f00e76f6d296fd
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70733225"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Accedere a Grafana in Azure HDInsight


[Grafana](https://grafana.com/) è un popolare generatore di dashboard e grafi open source. Ricco di funzionalità, Grafana non solo consente agli utenti di creare dashboard personalizzabili e condivisibili, ma offre anche dashboard con modelli/script, l'integrazione LDAP, più origini dati e altro ancora.

Attualmente, in Azure HDInsight, Grafana è supportato dai tipi di cluster Interactive Query e Hbase.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-an-apache-hadoop-cluster"></a>Creare un cluster Apache Hadoop

In questa sezione viene creato un cluster Interactive Query in HDInsight usando un modello di Azure Resource Manager. Per seguire questo articolo non è necessario conoscere il modello di Resource Manager. 

1. Fare clic sul pulsante **Deploy to Azure** (Distribuisci in Azure) seguente per accedere ad Azure e aprire il modello di Resource Manager nel portale di Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-grafana/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Immettere o selezionare i valori, come illustrato nello screenshot seguente:

    > [!NOTE]  
    > I valori immessi devono essere univoci e devono seguire le linee guida sulle convenzioni di denominazione. Il modello non esegue controlli di convalida. Se i valori immessi sono già in uso o non seguono le linee guida, viene visualizzato un errore dopo l'invio del modello.       
    > 
    >
    
    ![Modello iniziale di Resource Manager per HDInsight basato su Linux nel portale](./media/hdinsight-grafana/hdinsight-linux-get-started-arm-template-on-portal.png "Distribuire cluster Hadoop in HDInsight usando il portale di Azure e un modello di gestione del gruppo di risorse")

    Immettere o selezionare i valori seguenti:
    
    |Proprietà  |DESCRIZIONE  |
    |---------|---------|
    |**Sottoscrizione**     |  Selezionare la sottoscrizione di Azure. |
    |**Gruppo di risorse**     | Creare un gruppo di risorse o selezionarne uno esistente.  Un gruppo di risorse è un contenitore di componenti di Azure.  In questo caso, il gruppo di risorse contiene il cluster HDInsight e l'account di Archiviazione di Azure dipendente. |
    |**Posizione**     | Selezionare una posizione di Azure in cui si vuole creare il cluster.  Scegliere una località vicina all'utente per ottenere prestazioni migliori. |
    |**Tipo di cluster**     | Selezionare **hadoop**. |
    |**Nome del cluster**     | Immettere un nome per il cluster Apache Hadoop. Poiché tutti i cluster in HDInsight condividono lo stesso spazio dei nomi DNS, è necessario che questo nome sia univoco. Il nome può includere al massimo 59 caratteri, tra cui lettere, numeri e trattini. Si noti che il primo e l'ultimo carattere del nome non possono essere trattini. |
    |**Nome di accesso e password del cluster**     | Il nome di accesso predefinito è **admin**. La password deve avere una lunghezza minima di 10 caratteri e deve contenere almeno una cifra, una lettera maiuscola, una lettera minuscola e un carattere non alfanumerico, ad eccezione di ' " ` \). Assicurarsi di **non fornire** password comuni, ad esempio "Pass@word1".|
    |**Nome utente e password SSH**     | Il nome utente predefinito è **sshuser**.  È possibile rinominare il nome utente SSH.  Per la password utente SSH sono previsti gli stessi requisiti della password di accesso al cluster.|
       
    Alcune proprietà sono state impostate come hardcoded nel modello.  È possibile configurare questi valori dal modello. Per una spiegazione più approfondita di queste proprietà, vedere [Creare cluster Apache Hadoop in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Selezionare **Accetto le condizioni riportate sopra** e **Aggiungi al dashboard** e quindi selezionare **Acquisto**. Verrà visualizzato un nuovo riquadro denominato **Invio della distribuzione** nel dashboard del portale. La creazione di un cluster richiede circa 20 minuti.

    ![Stato di avanzamento della distribuzione del modello](./media/hdinsight-grafana/deployment-progress-tile.png "Stato di avanzamento della distribuzione del modello di Azure")

4. Dopo la creazione del cluster, la didascalia del riquadro viene cambiata nel nome del gruppo di risorse specificato. Il riquadro elenca inoltre il cluster HDInsight creato all'interno del gruppo di risorse. 
   
    ![Gruppo di risorse iniziale in HDInsight basato su Linux](./media/hdinsight-grafana/hdinsight-linux-get-started-resource-group.png "Gruppo di risorse cluster in Azure HDInsight")
    
5. Il riquadro elenca anche la risorsa di archiviazione predefinita associata al cluster. Ogni cluster ha una dipendenza da un [account di archiviazione di Azure](../hdinsight-hadoop-use-blob-storage.md) o da un [account Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Viene indicato come account di archiviazione predefinito. Il cluster HDInsight e l'account di archiviazione predefinito devono avere un percorso condiviso nella stessa area di Azure. L'eliminazione dei cluster non comporta l'eliminazione dell'account di archiviazione.
    

> [!NOTE]  
> Per altri metodi di creazione di cluster e per comprendere le proprietà usate in questo articolo, vedere [creare cluster HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="access-the-grafana-dashboard"></a>Accedere al dashboard di Grafana

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **Cluster HDInsight** e quindi selezionare il nome del cluster creato nell'ultima sezione.

3. In **Collegamenti rapidi** fare clic su **Dashboard cluster**.

    ![Portale del dashboard del cluster HDInsight](./media/hdinsight-grafana/hdinsight-portal-cluster-dashboard.png "Dashboard del cluster HDInsight nel portale")

4. Dal dashboard fare clic sul riquadro **Grafana**. In alternativa, individuare il percorso `/grafana/` dell'URL del cluster. Ad esempio `https://<clustername>.azurehdinsight.net/grafana/`.

5. Immettere le credenziali dell'utente del cluster Hadoop.

6. Il dashboard di Grafana è simile a questo esempio:

    ![Dashboard di HDInsight Grafana](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "Dashboard di HDInsight Grafana")

   

## <a name="clean-up-resources"></a>Pulire le risorse
Al termine dell'articolo, è consigliabile eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati. 

> [!NOTE]  
> Se si procede *subito* con l'esercitazione successiva per imparare come eseguire le operazioni ETL mediante Hadoop in HDInsight, è possibile mantenere il cluster in esecuzione, poiché nell'esercitazione è necessario creare nuovamente un cluster Hadoop. Se invece non si prevede di passare subito all'esercitazione successiva, è necessario eliminare il cluster ora.

**Per eliminare il cluster e/o l'account di archiviazione predefinito**

1. Tornare alla scheda del browser in cui è visualizzato il portale di Azure. Occorre visualizzare la pagina di panoramica del cluster. Se si vuole solo eliminare il cluster ma conservare l'account di archiviazione predefinito, scegliere **Elimina**.

    ![Eliminazione del cluster HDInsight](./media/hdinsight-grafana/hdinsight-delete-cluster.png "Eliminazione del cluster HDInsight")

2. Se si intende eliminare il cluster, nonché l'account di archiviazione predefinito, selezionare il nome del gruppo di risorse (evidenziato nello screenshot precedente) per aprire la pagina di gruppo di risorse.

3. Selezionare **Elimina gruppo di risorse** per eliminare il gruppo di risorse che contiene il cluster e l'account di archiviazione predefinito. Si noti che l'eliminazione del gruppo di risorse comporta l'eliminazione dell'account di archiviazione. Se si vuole mantenere l'account di archiviazione, scegliere di eliminare solo il cluster.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come creare un cluster HDInsight basato su Linux usando un modello di Resource Manager ed eseguire query Apache Hive di base. Passare all'articolo successivo per informazioni su come eseguire un'operazione di estrazione, trasformazione e caricamento (ETL) usando Hadoop in HDInsight.

> [!div class="nextstepaction"]
>[Estrarre, trasformare e caricare dati usando Interactive Query su HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)

Se si è pronti per iniziare a usare i dati, ma sono necessarie altre informazioni sulle modalità di archiviazione dei dati in HDInsight o sulle procedure di importazione dei dati in HDInsight, vedere gli articoli seguenti:

* Per informazioni sul modo in cui HDInsight usa Archiviazione di Azure, vedere [Usare Archiviazione di Azure con HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Per informazioni su come caricare i dati in HDInsight, vedere [Caricare dati in HDInsight](../hdinsight-upload-data.md).

Per altre informazioni sull'analisi dei dati con HDInsight, vedere gli articoli seguenti:

* Per altre informazioni sull'uso di Hive con HDInsight, incluse le procedure per eseguire query Hive da Visual Studio, vedere [Usare Apache Hive con HDInsight](../hdinsight-use-hive.md).
* Per informazioni su Pig, un linguaggio usato per la trasformazione dei dati, vedere [Usare Apache Pig con HDInsight](../hdinsight-use-pig.md).
* Per altre informazioni su MapReduce, un framework software che consente di scrivere programmi per l'elaborazione dei dati in Hadoop, vedere [Usare MapReduce con HDInsight](../hdinsight-use-mapreduce.md).
* Per altre informazioni sull'uso di HDInsight Tools per Visual Studio per analizzare i dati in HDInsight, vedere [Introduzione all'uso di Hadoop Tools per Visual Studio per HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).



Per altre informazioni sulla creazione o la gestione di un cluster HDInsight, vedere gli articoli seguenti:

* Per altre informazioni sulla gestione di cluster HDInsight basati su Linux, vedere [Gestire i cluster HDInsight tramite Ambari](../hdinsight-hadoop-manage-ambari.md).
* Per altre informazioni sulle opzioni che è possibile selezionare durante la creazione di un cluster HDInsight, vedere [Creare cluster Hadoop basati su Linux in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).


[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

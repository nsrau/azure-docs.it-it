---
title: Risolvere i problemi di Apache Spark in Azure HDInsight
description: Risposte alle domande frequenti sull'uso di Apache Spark e Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: 80bca2dab1d07d9b99e75e283068bff99335fa18
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79271941"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Risolvere i problemi di Apache Spark tramite Azure HDInsight

Informazioni sui problemi principali e le relative soluzioni quando si lavora con i payload Apache Spark in [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Come si configura un'applicazione Apache Spark tramite Apache Ambari nei cluster?

I valori di configurazione di Spark possono essere ottimizzati per evitare un'eccezione Apache Spark applicazione `OutofMemoryError` . I passaggi seguenti illustrano i valori di configurazione di Spark predefiniti in Azure HDInsight:

1. Accedere a Ambari `https://CLUSTERNAME.azurehdidnsight.net` con le credenziali del cluster. La schermata iniziale Visualizza un dashboard di panoramica. Sono presenti lievi differenze estetiche tra HDInsight 3,6 e 4,0.

1. Passare a **Spark2**  >  **configs**.

    ![Selezionare la scheda Configs (Configurazioni)](./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png)

1. Nell'elenco delle configurazioni selezionare ed espandere **Custom-spark2-defaults**.

1. Ricercare l'impostazione del valore che si desidera modificare, come **spark.executor.memory**. In questo caso, il valore di **9728m** è troppo elevato.

    ![Selezionare custom-spark-defaults](./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png)

1. Configurare il valore sull'impostazione consigliata. Il valore **2048m** è consigliato per questa impostazione.

1. Salvare il valore, quindi salvare la configurazione. Selezionare **Salva**.

    ![Cambiare il valore in 2048m](./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png)

    Immettere una nota sulle modifiche apportate alla configurazione, quindi selezionare **Save** (Salva).

    ![Immettere una nota sulle modifiche apportate](./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png)

    Si riceve una notifica se una delle configurazioni richiede attenzione. Annotare gli elementi e quindi selezionare **Proceed Anyway** (Continuare comunque).

    ![Selezionare Proceed Anyway (Continuare comunque)](./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png)

1. Ogni volta che viene salvata una configurazione, viene chiesto di riavviare il servizio. Selezionare **Restart** (Riavvia).

    ![Selezionare Restart (Riavvia)](./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png)

    Confermare il riavvio.

    ![Selezionare la conferma del riavvio completo](./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png)

    È possibile esaminare i processi in esecuzione.

    ![Esaminare i processi in esecuzione](./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png)

1. È possibile aggiungere configurazioni. Nell'elenco delle configurazioni selezionare **Custom-spark2-defaults** e quindi selezionare **Add Property** (Aggiungi proprietà).

    ![Selezionare Add property (Aggiungi proprietà)](./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png)

1. Definire una nuova proprietà. È possibile definire una singola proprietà usando una finestra di dialogo per impostazioni specifiche, ad esempio il tipo di dati. In alternativa, è possibile definire più proprietà usando una definizione per riga.

    In questo esempio la proprietà **spark.driver.memory** è stata definita con un valore di **4g**.

    ![Definire una nuova proprietà](./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png)

1. Salvare la configurazione e quindi riavviare il servizio come descritto nei passaggi 6 e 7.

Queste modifiche si applicano a tutto il cluster ma è possibile eseguirne l'override quando si invia il processo Spark.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Come si configura un'applicazione Apache Spark tramite un notebook di Jupyter nei cluster?

Nella prima cella del notebook Jupyter, dopo la direttiva **%%configure**, specificare le configurazioni di Spark in un formato JSON valido. Modificare i valori effettivi in base alla necessità:

![Aggiungere una configurazione](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Come si configura un'applicazione Apache Spark tramite Apache Livy nei cluster?

Inviare l'applicazione Spark a Livy usando un client REST come cURL. Usare un comando simile al seguente. Modificare i valori effettivi in base alla necessità:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Come si configura un'applicazione Apache Spark tramite lo script spark-submit nei cluster?

Avviare spark-shell usando un comando simile al seguente. Modificare il valore effettivo delle configurazioni in base alla necessità:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Informazioni aggiuntive

[Invio del processo Apache Spark nei cluster HDInsight](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* [Panoramica sulla gestione della memoria di Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Debug di un'applicazione Spark nei cluster HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Contattare [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Mette in contatto la community di Azure con le risorse giuste: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto**. Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).

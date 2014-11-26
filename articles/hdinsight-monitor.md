<properties linkid="manage-services-hdinsight-howto-monitor-hdinsight" urlDisplayName="Monitor" pageTitle="Monitor HDInsight | Azure" metaKeywords="" description="Learn how to monitor an HDInsight cluster and view Hadoop job history through the Azure management portal." metaCanonical="" services="hdinsight" documentationCenter="" title="How to Monitor HDInsight" authors="jgao" solutions="" manager="paulettm" editor="mollybos" />

Come monitorare HDInsight
=========================

In questo argomento verrà illustrato come monitorare un cluster HDInsight.

Sommario
--------

-   [Procedura: Monitorare un cluster HDInsight](#monitorcluster)
-   [Procedura: Visualizzare la cronologia dei processi Hadoop](#jobhistory)

Procedura: Monitorare un cluster HDInsight
------------------------------------------

Per monitorare un cluster HDInsight e i processi Hadoop in esecuzione sul cluster, è possibile connettersi al dashboard di HDInsight e fare clic sul riquadro Monitor Cluster.

![HDI.TileMonitorCluster](./media/hdinsight-monitor/HDI.TileMonitorCluster.PNG)

La pagina di monitoraggio è analoga alla seguente:

![HDI.MonitorPage](./media/hdinsight-monitor/HDI.MonitorPage.PNG)

Sulla destra viene mostrato che Namenode e l'analisi dei processi sono attivi e che i 4 nodi dati sono in esecuzione in stato di integrità.

Sulla sinistra sono visualizzate le metriche di MapReduce relative agli ultimi 30 minuti. La finestra di monitoraggio può essere impostata su 30 minuti, 1 ora, 3 ore, 12 ore, 1 giorno, 2 giorni, 1 settimana e 2 settimane.

Procedura: Visualizzare la cronologia dei processi Hadoop
---------------------------------------------------------

Per visualizzare la cronologia dei processi Hadoop, connettersi al dashboard di HDInsight e fare clic sul riquadro Job History.

![HDI.TileJobHistory](./media/hdinsight-monitor/HDI.TileJobHistory.PNG)

Nel riquadro viene visualizzato il numero di processi che sono stati eseguiti. L'immagine riportata sopra, ad esempio, indica che è disponibile la cronologia per 6 processi. La pagina della cronologia dei processi è analoga alla seguente:

![HDI.JobHistoryPage](./media/hdinsight-monitor/HDI.JobHistoryPage.PNG)

Vedere anche
------------

-   [Procedura: Amministrare HDInsight](/it-it/manage/services/hdinsight/howto-administer-hdinsight/)
-   [Procedura: Distribuire un cluster di HDInsight a livello di codice](/it-it/manage/services/hdinsight/howto-deploy-cluster/)
-   [Procedura: Eseguire processi remoti nel cluster di HDInsight a livello di codice](/it-it/manage/services/hdinsight/howto-execute-jobs-programmatically/)
-   [Esercitazione: Introduzione all'utilizzo di Azure HDInsight](/it-it/manage/services/hdinsight/get-started-hdinsight/)


[hdi-monitor-cluster-tile]: ./media/hdinsight-monitor/HDI.TileMonitorCluster.PNG
[hdi-monitor-page]: ./media/hdinsight-monitor/HDI.MonitorPage.PNG
[hdi-job-history-tile]: ./media/hdinsight-monitor/HDI.TileJobHistory.PNG
[hdi-job-history-page]: ./media/hdinsight-monitor/HDI.JobHistoryPage.PNG
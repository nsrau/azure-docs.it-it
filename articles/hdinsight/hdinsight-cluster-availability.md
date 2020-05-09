---
title: Come monitorare la disponibilità del cluster con Apache Ambari in Azure HDInsight
description: Informazioni su come usare Apache Ambari per monitorare l'integrità e la disponibilità del cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/01/2020
ms.openlocfilehash: 4b26128b794a6a667edc578f56ad0bc9fb8303a7
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691143"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-in-azure-hdinsight"></a>Come monitorare la disponibilità del cluster con Apache Ambari in Azure HDInsight

I cluster HDInsight includono Apache Ambari, che fornisce informazioni sull'integrità a colpo d'occhio e avvisi predefiniti.

Questo articolo illustra come usare Ambari per monitorare il cluster e illustra alcuni esempi per la configurazione di un avviso Ambari, la frequenza di disponibilità dei nodi di monitoraggio e la creazione di un avviso di monitoraggio di Azure che viene attivato quando non è stato ricevuto un heartbeat da uno o più nodi in cinque ore.

## <a name="dashboard"></a>Dashboard

È possibile accedere al dashboard di Ambari selezionando il collegamento **Ambari Home** nella sezione **Dashboard del cluster** della panoramica di HDInsight in portale di Azure come illustrato di seguito. In alternativa, è possibile accedervi passando a `https://CLUSTERNAME.azurehdinsight.net` in un browser in cui clustername è il nome del cluster.

![Visualizzazione del portale delle risorse di HDInsight](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

Verranno quindi richiesti il nome utente e la password di accesso al cluster. Immettere le credenziali selezionate durante la creazione del cluster.

Si passerà quindi al dashboard di Ambari, che contiene i widget che mostrano alcune metriche per offrire una rapida panoramica dell'integrità del cluster HDInsight. Questi widget mostrano le metriche, ad esempio il numero di nodi Live datanodes (nodi del ruolo di lavoro) e JournalNodes (nodo Zookeeper), NameNodes (nodi head), nonché metriche specifiche per determinati tipi di cluster, ad esempio il tempo di esecuzione di YARN ResourceManager per i cluster Spark e Hadoop.

![Apache Ambari usare la visualizzazione del dashboard](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

## <a name="hosts--view-individual-node-status"></a>Host: visualizzare lo stato di un singolo nodo

È anche possibile visualizzare le informazioni sullo stato per i singoli nodi. Selezionare la scheda **host** per visualizzare un elenco di tutti i nodi del cluster e visualizzare le informazioni di base su ogni nodo. Il segno di spunta verde a sinistra di ogni nome di nodo indica che tutti i componenti sono presenti nel nodo. Se un componente è inattivo in un nodo, verrà visualizzato un triangolo di avviso rosso anziché il segno di spunta verde.

![Visualizzazione host HDInsight Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

È quindi possibile selezionare il **nome** di un nodo per visualizzare le metriche host più dettagliate per quel determinato nodo. Questa vista Mostra lo stato e la disponibilità di ogni singolo componente.

![Apache Ambari ospita la visualizzazione a nodo singolo](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

## <a name="ambari-alerts"></a>Avvisi di Ambari

Ambari offre anche diversi avvisi configurabili che possono fornire notifiche di determinati eventi. Quando gli avvisi vengono attivati, vengono visualizzati nell'angolo in alto a sinistra di Ambari in una notifica rossa contenente il numero di avvisi. Se si seleziona questa notifica, viene visualizzato un elenco di avvisi correnti.

![Conteggio avvisi correnti di Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Per visualizzare un elenco di definizioni di avviso e i relativi stati, selezionare la scheda **avvisi** , come illustrato di seguito.

![Visualizzazione definizioni avvisi Ambari](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari offre molti avvisi predefiniti relativi alla disponibilità, tra cui:

| Nome avviso                        | Descrizione   |
|---|---|
| Riepilogo integrità dataNode           | Questo avviso a livello di servizio viene attivato se sono presenti nodi di tipo non integro|
| Stato disponibilità elevata NameNode | Questo avviso a livello di servizio viene attivato se il NameNode attivo o NameNode standby non sono in esecuzione.|
| Percentuale di JournalNodes disponibili    | Questo avviso viene generato se il numero di JournalNodes inattivi nel cluster è maggiore della soglia critica configurata. Aggrega i risultati dei controlli del processo JournalNode. |
| Percentuale di dataNode disponibili       | Questo avviso viene generato se il numero di nodi inattivi nel cluster è maggiore della soglia critica configurata. Aggrega i risultati dei controlli del processo dataNode.|

Un elenco completo degli avvisi Ambari che consentono di monitorare la disponibilità di un cluster è disponibile [qui](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui),

Per visualizzare i dettagli di un avviso o modificare i criteri, selezionare il **nome** dell'avviso. Come esempio, prendere il **Riepilogo di integrità dataNode** . È possibile visualizzare una descrizione dell'avviso, nonché i criteri specifici che attiveranno un avviso "avviso" o "critico" e l'intervallo di controllo per i criteri. Per modificare la configurazione, fare clic sul pulsante **Edit (modifica** ) nell'angolo superiore destro della casella di configurazione.

![Configurazione degli avvisi di Apache Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Qui è possibile modificare la descrizione e, ancora più importante, l'intervallo di controllo e le soglie per avvisi o avvisi critici.

![Visualizzazione di modifica configurazioni avvisi Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

In questo esempio, è possibile fare in modo che 2 nodi di tipo non integro attivino un avviso critico e 1 dataNode non integro attivi solo un avviso. Al termine della modifica, selezionare **Salva** .

## <a name="email-notifications"></a>Notifiche di posta elettronica

Facoltativamente, è anche possibile configurare le notifiche di posta elettronica per gli avvisi Ambari. A tale scopo, nella scheda **avvisi** fare clic sul pulsante **azioni** in alto a sinistra, quindi su **Gestisci notifiche.**

![Azione di gestione notifiche di Ambari](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Viene visualizzata una finestra di dialogo per la gestione delle notifiche di avviso. Selezionare nella **+** parte inferiore della finestra di dialogo e compilare i campi obbligatori per fornire a Ambari i dettagli del server di posta elettronica da cui inviare i messaggi di posta elettronica.

> [!TIP]
> La configurazione delle notifiche di posta elettronica di Ambari può essere un modo efficace per ricevere avvisi in un'unica posizione quando si gestiscono molti cluster HDInsight.

## <a name="next-steps"></a>Passaggi successivi

- [Disponibilità e affidabilità dei cluster Apache Hadoop in HDInsight](hdinsight-high-availability-linux.md)
- [Disponibilità dei cluster - Log di Monitoraggio di Azure](./cluster-availability-monitor-logs.md)
- [Usare i log di Monitoraggio di Azure](hdinsight-hadoop-oms-log-analytics-tutorial.md)
- [Notifiche tramite posta elettronica di Apache Ambari](apache-ambari-email.md)

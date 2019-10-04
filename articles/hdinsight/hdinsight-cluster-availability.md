---
title: Come monitorare la disponibilità del cluster con Ambari e i log di monitoraggio di Azure
description: Informazioni su come usare Ambari e i log di monitoraggio di Azure per monitorare l'integrità e la disponibilità del cluster.
keywords: monitoraggio, Ambari, monitoraggio, log Analytics, avviso, disponibilità, integrità
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tyfox
ms.openlocfilehash: 153c28dc8a06968dc9dd3cfda021496672a094d5
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076559"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>Come monitorare la disponibilità del cluster con Ambari e i log di monitoraggio di Azure

I cluster HDInsight includono sia Apache Ambari, che fornisce informazioni sull'integrità a colpo d'occhio e avvisi predefiniti, sia l'integrazione dei log di monitoraggio di Azure, che fornisce metriche e log in cui è possibile eseguire query, nonché avvisi configurabili.

Questo documento illustra come usare questi strumenti per monitorare il cluster e illustra alcuni esempi per la configurazione di un avviso Ambari, la frequenza di disponibilità dei nodi di monitoraggio e la creazione di un avviso di monitoraggio di Azure che viene attivato quando non è stato ricevuto un heartbeat da uno o più nodi in cinque ore.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>dashboard

È possibile accedere al dashboard di Ambari facendo clic sul collegamento **Ambari Home** nella sezione **Dashboard del cluster del pannello** panoramica di HDInsight in portale di Azure come illustrato di seguito. In alternativa, è possibile accedervi immettendo l'URL seguente in un [browser\<https://clustername\>. azurehdinsight.NET](https://clustername.azurehdinsight.net/)

![Visualizzazione del portale delle risorse di HDInsight](media/hdinsight-cluster-availability/portal-oms-overview1.png)

Verranno quindi richiesti il nome utente e la password di accesso al cluster. Immettere le credenziali selezionate durante la creazione del cluster.

Si passerà quindi al dashboard di Ambari, che contiene i widget che mostrano alcune metriche per offrire una rapida panoramica dell'integrità del cluster HDInsight. Questi widget mostrano le metriche, ad esempio il numero di nodi Live datanodes (nodi del ruolo di lavoro) e JournalNodes (nodo Zookeeper), NameNodes (nodi head), nonché metriche specifiche per determinati tipi di cluster, ad esempio il tempo di esecuzione di YARN ResourceManager per i cluster Spark e Hadoop.

![Apache Ambari usare la visualizzazione del dashboard](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Host: visualizzare lo stato di un singolo nodo

È anche possibile visualizzare le informazioni sullo stato per i singoli nodi. Fare clic sulla scheda **host** per visualizzare un elenco di tutti i nodi del cluster e visualizzare le informazioni di base su ogni nodo. Il segno di spunta verde a sinistra di ogni nome di nodo indica che tutti i componenti sono presenti nel nodo. Se un componente è inattivo in un nodo, verrà visualizzato un triangolo di avviso rosso anziché il segno di spunta verde.

![Visualizzazione host HDInsight Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

È quindi possibile fare clic sul **nome** di un nodo per visualizzare le metriche host più dettagliate per quel determinato nodo. Questa vista Mostra lo stato e la disponibilità di ogni singolo componente.

![Apache Ambari ospita la visualizzazione a nodo singolo](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Avvisi di Ambari

Ambari offre anche diversi avvisi configurabili che possono fornire notifiche di determinati eventi. Quando gli avvisi vengono attivati, vengono visualizzati nell'angolo in alto a sinistra di Ambari in una notifica rossa contenente il numero di avvisi. Se si fa clic su questa notifica, viene visualizzato un elenco di avvisi correnti.

![Conteggio avvisi correnti di Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Per visualizzare un elenco di definizioni di avviso e i relativi stati, fare clic sulla scheda **avvisi** , come illustrato di seguito.

![Visualizzazione definizioni avvisi Ambari](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari offre molti avvisi predefiniti relativi alla disponibilità, tra cui:

| Nome avviso                        | Descrizione                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Riepilogo integrità dataNode           | Questo avviso a livello di servizio viene attivato se sono presenti nodi di tipo non integro                                                                                                                |
| Stato disponibilità elevata NameNode | Questo avviso a livello di servizio viene attivato se il NameNode attivo NameNode o standby non è in esecuzione.                                                                              |
| Percentuale di JournalNodes disponibili    | Questo avviso viene generato se il numero di JournalNodes inattivi nel cluster è maggiore della soglia critica configurata. Aggrega i risultati dei controlli del processo JournalNode. |
| Percentuale di dataNode disponibili       | Questo avviso viene generato se il numero di nodi inattivi nel cluster è maggiore della soglia critica configurata. Aggrega i risultati dei controlli del processo dataNode.       |

Un elenco completo degli avvisi Ambari che consentono di monitorare la disponibilità di un cluster è disponibile [qui](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui),

Per visualizzare i dettagli di un avviso o modificare i criteri, fare clic sul **nome** dell'avviso. Come esempio, prendere il **Riepilogo di integrità dataNode** . È possibile visualizzare una descrizione dell'avviso, nonché i criteri specifici che attiveranno un avviso "avviso" o "critico" e l'intervallo di controllo per i criteri. Per modificare la configurazione, fare clic sul pulsante **Edit (modifica** ) nell'angolo superiore destro della casella di configurazione.

![Configurazione degli avvisi di Apache Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Qui è possibile modificare la descrizione e, ancora più importante, l'intervallo di controllo e le soglie per avvisi o avvisi critici.

![Visualizzazione di modifica della configurazione degli avvisi Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

In questo esempio, è possibile fare in modo che 2 nodi di tipo non integro attivino un avviso critico e 1 dataNode non integro attivi solo un avviso. Al termine della modifica, fare clic su **Salva** .

### <a name="email-notifications"></a>Notifiche tramite posta elettronica

Facoltativamente, è anche possibile configurare le notifiche di posta elettronica per gli avvisi Ambari. A tale scopo, nella scheda **avvisi** fare clic sul pulsante **azioni** in alto a sinistra, quindi su **Gestisci notifiche.**

![Azione di gestione notifiche di Ambari](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Viene visualizzata una finestra di dialogo per la gestione delle notifiche di avviso. **+** Fare clic su nella parte inferiore della finestra di dialogo e compilare i campi obbligatori per fornire a Ambari i dettagli del server di posta elettronica da cui inviare i messaggi di posta elettronica.

> [!TIP]
> La configurazione delle notifiche di posta elettronica di Ambari può essere un modo efficace per ricevere avvisi in un'unica posizione quando si gestiscono molti cluster HDInsight.

## <a name="azure-monitor-logs-integration"></a>Integrazione dei log di monitoraggio di Azure

Log di monitoraggio di Azure consente di raccogliere e aggregare i dati generati da più risorse, ad esempio i cluster HDInsight, in un'unica posizione per ottenere un'esperienza di monitoraggio unificata.

Come prerequisito, sarà necessaria un'area di lavoro Log Analytics per archiviare i dati raccolti. Se non ne è già stato creato uno, è possibile seguire le istruzioni riportate qui: [Creare un'area di lavoro log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Abilitare l'integrazione dei log di monitoraggio di Azure HDInsight

Dalla pagina delle risorse cluster HDInsight nel portale, fare clic sul pannello **Operations Management Suite** . Quindi, fare clic su **Abilita** e selezionare l'area di lavoro log Analytics dall'elenco a discesa.

![Pannello HDInsight Operations Management Suite](media/hdinsight-cluster-availability/hdi-portal-oms-enable.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>Eseguire query sulle metriche e sulle tabelle dei log nel pannello logs

Una volta abilitata l'integrazione dei log di monitoraggio di Azure (l'operazione potrebbe richiedere alcuni minuti), passare alla risorsa dell' **area di lavoro log Analytics** e fare clic sul pannello **log** .

![Pannello log dell'area di lavoro Log Analytics](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

Il pannello **logs** elenca una serie di query di esempio, ad esempio:

| Nome query                      | Descrizione                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Disponibilità di computer oggi    | Grafico numero di computer che inviano log, ogni ora                     |
| Elenca heartbeat                 | Elencare tutti gli heartbeat del computer nell'ultima ora                           |
| Ultimo heartbeat di ogni computer | Mostra l'ultimo heartbeat inviato da ogni computer                             |
| Computer non disponibili           | Elencare tutti i computer noti che non hanno inviato un heartbeat nelle ultime 5 ore |
| Frequenza di disponibilità               | Calcolare la frequenza di disponibilità di ogni computer connesso                |

Eseguire ad esempio la query di esempio sulla **frequenza di disponibilità** facendo clic su **Esegui** nella query, come illustrato nella schermata precedente. Verrà visualizzata la percentuale di disponibilità di ogni nodo nel cluster come percentuale. Se sono stati abilitati più cluster HDInsight per l'invio di metriche alla stessa area di lavoro Log Analytics, verrà visualizzata la tariffa di disponibilità per tutti i nodi presenti in tali cluster.

![Log Analytics query di esempio del pannello log dell'area di lavoro ' frequenza di disponibilità'](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> La frequenza di disponibilità viene misurata in un periodo di 24 ore, quindi il cluster deve essere eseguito per almeno 24 ore prima di visualizzare le tariffe di disponibilità accurate.

È possibile aggiungere questa tabella a un dashboard condiviso facendo clic su **Aggiungi** nell'angolo in alto a destra. Se non si dispone di dashboard condivisi scrivibili, è possibile vedere come crearne uno: [Creare e condividere i dashboard nel portale di Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

### <a name="azure-monitor-alerts"></a>Avvisi di monitoraggio di Azure

È anche possibile configurare gli avvisi di monitoraggio di Azure che si attiveranno quando il valore di una metrica o i risultati di una query soddisfano determinate condizioni. Si supponga, ad esempio, di creare un avviso per inviare un messaggio di posta elettronica quando uno o più nodi non hanno inviato un heartbeat in 5 ore, ovvero si presume che non sia disponibile.

Nel pannello **log** eseguire la query di esempio **computer non disponibili** facendo clic su **Esegui** nella query, come illustrato di seguito.

![Esempio ' computer non disponibili ' del pannello log dell'area di lavoro di Log Analytics](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Se tutti i nodi sono disponibili, la query restituirà 0 Risultati per il momento. Fare clic su **nuova regola di avviso** per iniziare la configurazione dell'avviso per la query.

![Nuova regola di avviso per l'area di lavoro Log Analytics](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Sono disponibili tre componenti per un avviso: la *risorsa* per la quale creare la regola (in questo caso l'area di lavoro log Analytics), la *condizione* per attivare l'avviso e i *gruppi di azioni* che determinano cosa accadrà quando l'avviso è attivato.

Fare clic sul **titolo della condizione**, come illustrato di seguito, per completare la configurazione della logica del segnale.

![Condizione di creazione della regola di avviso del portale](media/hdinsight-cluster-availability/portal-condition-title.png)

Verrà aperto il pannello **Configura logica del segnale** .

Impostare la sezione **logica avvisi** come segue:

*In base a: Numero di risultati, condizione: Maggiore di, soglia: 0.*

Poiché questa query restituisce solo i nodi non disponibili come risultati, se il numero di risultati è sempre maggiore di 0, l'avviso deve essere attivato.

Nella sezione **valutato in base a** impostare il **periodo** e la **frequenza** in base alla frequenza con cui si desidera controllare i nodi non disponibili.

Si noti che, ai fini di questo avviso, è necessario assicurarsi che **period = Frequency.** Altre informazioni su periodo, frequenza e altri parametri di avviso sono disponibili [qui](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Fare clic su **Done** al termine della configurazione per la logica di segnale.

![Regola di avviso configurare la logica del segnale](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Se non si dispone già di un gruppo di azioni esistente, fare clic su **Crea nuovo** nella sezione **gruppi di azioni** .

![Regola di avviso crea nuovo gruppo di azioni](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Verrà aperto il pannello **Aggiungi gruppo di azioni** . Scegliere un **nome del gruppo di azioni**, un **nome breve**, una **sottoscrizione**e un **gruppo di risorse.** Nella sezione **azioni** scegliere un nome di **azione** e selezionare **posta elettronica/SMS/push/Voice** come **tipo di azione.**

> [!NOTE]
> Esistono diverse altre azioni che possono essere attivate da un avviso oltre a un messaggio di posta elettronica/SMS/push/Voice, ad esempio una funzione di Azure, LogicApp, webhook, ITSM e Runbook di automazione. [Ulteriori informazioni.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Verrà aperto il pannello **e-mail/SMS/push/Voice** . Scegliere un **nome** per il destinatario, **selezionare** la casella **posta elettronica** e digitare un indirizzo di posta elettronica a cui si vuole inviare l'avviso. Fare clic su **OK** nel pannello **posta elettronica/SMS/push/voce** , quindi nel pannello **Aggiungi gruppo di azioni** per completare la configurazione del gruppo di azioni.

![Regola di avviso creazione Aggiungi gruppo di azione](media/hdinsight-cluster-availability/portal-add-action-group.png)

Dopo la chiusura di questi pannelli, il gruppo di azioni verrà visualizzato nella sezione **gruppi di azioni** . Infine, completare la sezione **Dettagli avviso** digitando un nome e una **Descrizione** della **regola di avviso** e scegliendo una **gravità**.
Fare clic su **Crea regola di avviso** per terminare.

![Fine della creazione della regola di avviso del portale](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> La possibilità di specificare la **gravità** è uno strumento potente che può essere usato durante la creazione di più avvisi. Ad esempio, è possibile creare un avviso per generare un avviso (gravità 1) se un singolo nodo head diventa inattivo e un altro avviso che genera Critical (gravità 0) nell'evento improbabile in cui entrambi i nodi head si arrestano.

Quando viene soddisfatta la condizione per questo avviso, l'avviso viene attivato e si riceverà un messaggio di posta elettronica con i dettagli dell'avviso, come indicato di seguito:

![Esempio di messaggio di posta elettronica di avviso monitoraggio di Azure](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

È anche possibile visualizzare tutti gli avvisi generati, raggruppati in base alla gravità, accedendo al pannello **avvisi** nell' **area di lavoro log Analytics**.

![Avvisi dell'area di lavoro Log Analytics](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

Se si fa clic su un raggruppamento di gravità (ad esempio, il valore di gravità **1,** come evidenziato in precedenza), i record per tutti gli avvisi di tale gravità vengono visualizzati come indicato di seguito:

![Avvisi di Log Analytics dell'area di lavoro 1](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Passaggi successivi
- [Disponibilità e affidabilità dei cluster Apache Hadoop in HDInsight](hdinsight-high-availability-linux.md)

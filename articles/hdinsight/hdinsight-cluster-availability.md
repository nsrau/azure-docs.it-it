---
title: 'Monitoraggio: Apache Ambari & i log di Monitoraggio di Azure - Azure HDInsightMonitoring: Apache Ambari & Azure Monitor logs - Azure HDInsight'
description: Informazioni su come usare i log di Ambari e Monitoraggio di Azure per monitorare l'integrità e la disponibilità del cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/06/2020
ms.openlocfilehash: 383366fa3e436c79bed28a7c47f1e9daa5f0d9de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060176"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-and-azure-monitor-logs"></a>Come monitorare la disponibilità del cluster con i log di Apache Ambari e Monitoraggio di AzureHow to monitor cluster availability with Apache Ambari and Azure Monitor logs

I cluster HDInsight includono sia Apache Ambari, che fornisce informazioni sull'integrità a colpo d'occhio e avvisi predefiniti, nonché l'integrazione dei log di Monitoraggio di Azure, che fornisce metriche e log queryable, nonché avvisi configurabili.

Questo documento illustra come usare questi strumenti per monitorare il cluster e illustra alcuni esempi per la configurazione di un avviso Ambari, il monitoraggio della frequenza di disponibilità dei nodi e la creazione di un avviso di Monitoraggio di Azure che viene generato quando un heartbeat non è stato ricevuto da uno o più nodi in cinque ore.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>Dashboard

È possibile accedere al dashboard di Ambari selezionando il collegamento **Home Ambari** nella sezione **Dashboard cluster** della panoramica di HDInsight nel portale di Azure, come illustrato di seguito. In alternativa, è possibile accedervi `https://CLUSTERNAME.azurehdinsight.net` in un browser in cui CLUSTERNAME è il nome del cluster.

![Visualizzazione del portale delle risorse HDInsightHDInsight resource portal view](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

Verrà quindi richiesto di immettere un nome utente e una password di accesso al cluster. Immettere le credenziali scelte durante la creazione del cluster.

Verrà quindi visualizzata la dashboard di Ambari, che contiene widget che mostrano una manciata di metriche per fornire una rapida panoramica dello stato del cluster HDInsight. Questi widget mostrano metriche quali il numero di DataNodes attivi (nodi di lavoro) e JournalNodes (nodo di guardiano), il tempo di attività NameNodes (nodi di intestazione), nonché metriche specifiche di determinati tipi di cluster, ad esempio i tempi di attività di YARN ResourceManager per i cluster Spark e Hadoop.

![Apache Ambari utilizzare visualizzazione del cruscotto](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Host: consente di visualizzare lo stato dei singoli nodi

È inoltre possibile visualizzare le informazioni sullo stato per i singoli nodi. Selezionare la scheda **Host** per visualizzare un elenco di tutti i nodi del cluster e visualizzare le informazioni di base su ogni nodo. Il segno di spunta verde a sinistra del nome di ogni nodo indica che tutti i componenti si trovano sul nodo. Se un componente è inattivo su un nodo, verrà visualizzato un triangolo di avviso rosso anziché il segno di spunta verde.

![Visualizzazione host HDInsight Apache Ambari](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

È quindi possibile selezionare il **nome** di un nodo per visualizzare metriche host più dettagliate per quel particolare nodo. Questa vista mostra lo stato/disponibilità di ogni singolo componente.

![Apache Ambari ospita la visualizzazione a nodo singolo](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Avvisi Ambari

Ambari offre anche diversi avvisi configurabili che possono fornire la notifica di determinati eventi. Quando gli avvisi vengono attivati, vengono visualizzati nell'angolo superiore sinistro di Ambari in un badge rosso contenente il numero di avvisi. Selezionando questo badge viene visualizzato un elenco di avvisi correnti.

![Apache Ambari attuali tàcanti gli avvisi](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

Per visualizzare un elenco di definizioni di avviso e i relativi stati, selezionare la scheda **Avvisi,** come illustrato di seguito.

![Visualizzazione definizioni avvisi Ambari](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari offre molti avvisi predefiniti relativi alla disponibilità, tra cui:

| Nome avviso                        | Descrizione   |
|---|---|
| Riepilogo dell'integrità di DataNode           | Questo avviso a livello di servizio viene attivato se sono presenti DataNode non integri|
| Integrità disponibilità elevata NameNodeNameNode High Availability Health | Questo avviso a livello di servizio viene attivato se Active NameNode o Standby NameNode non sono in esecuzione.|
| Percentuale JournalNodes Disponibile    | Questo avviso viene attivato se il numero di JournalNodes inattivi nel cluster è maggiore della soglia critica configurata. Aggrega i risultati dei controlli del processo JournalNode. |
| Percent DataNodes disponibili       | Questo avviso viene attivato se il numero di DataNodes inattivi nel cluster è maggiore della soglia critica configurata. Aggrega i risultati dei controlli del processo DataNode.|

Un elenco completo degli avvisi Ambari che aiutano a monitorare la disponibilità di un cluster è disponibile [qui,](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui)

Per visualizzare i dettagli di un avviso o modificare i criteri, selezionare il **nome** dell'avviso. Prendiamo ad esempio **DataNode Health Summary.** È possibile visualizzare una descrizione dell'avviso, nonché i criteri specifici che attiveranno un avviso 'avviso' o 'critico' e l'intervallo di controllo per i criteri. Per modificare la configurazione, selezionare il pulsante **Modifica** nell'angolo superiore destro della casella Configurazione.

![Configurazione degli avvisi Apache Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

Qui è possibile modificare la descrizione e, cosa ancora più importante, l'intervallo di controllo e le soglie per gli avvisi di avviso o critici.

![Visualizzazione di modifica delle configurazioni degli avvisi Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

In questo esempio, è possibile fare in modo che 2 DataNodes non integri attivino un avviso critico e 1 DataNode non integro attivi attivi solo un avviso. Al termine delle modifiche, seleziona **Salva.**

### <a name="email-notifications"></a>Notifiche di posta elettronica

Facoltativamente, è anche possibile configurare le notifiche e-mail per gli avvisi Ambari. A tale scopo, nella scheda **Avvisi** fare clic sul pulsante **Azioni** in alto a sinistra, quindi **su Gestisci notifiche.**

![Azione di gestione delle notifiche Ambari](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Si aprirà una finestra di dialogo per la gestione delle notifiche di avviso. Selezionare **+** il nella parte inferiore della finestra di dialogo e compilare i campi obbligatori per fornire Ad Ambari i dettagli del server di posta elettronica da cui inviare le e-mail.

> [!TIP]
> La configurazione delle notifiche di posta elettronica Di Ambari può essere un buon modo per ricevere avvisi in un'unica posizione durante la gestione di molti cluster HDInsight.Setting up Ambari email notifications can be a good way to receive alerts at one place when managing many HDInsight clusters.

## <a name="azure-monitor-logs-integration"></a>Integrazione dei log di Monitoraggio di AzureAzure Monitor logs integration

I log di Monitoraggio di Azure consentono di raccogliere e aggregare i dati generati da più risorse, ad esempio i cluster HDInsight, in un'unica posizione per ottenere un'esperienza di monitoraggio unificata.

Come prerequisito, è necessaria un'area di lavoro di Log Analytics per archiviare i dati raccolti. Se non ne è già stato creato uno, è possibile seguire le istruzioni riportate di seguito: [Creare un'area](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)di lavoro di Log Analytics .

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Abilitare l'integrazione dei log di Monitoraggio di Azure HDInsightEnable HDInsight Azure Monitor logs integration

Nella pagina delle risorse cluster HDInsight del portale selezionare **Monitoraggio di Azure.** Selezionare quindi **abilita** e seleziona l'area di lavoro di Log Analytics dall'elenco a discesa.

![Suite di gestione delle operazioni HDInsightHDInsight Operations Management Suite](media/hdinsight-cluster-availability/azure-portal-monitoring.png)

### <a name="query-metrics-and-logs-tables"></a>Tabelle dei log e metriche di query

Una volta abilitata l'integrazione del log di Monitoraggio di Azure (l'operazione potrebbe richiedere alcuni minuti), passare alla risorsa **Log Analytics Workspace** e selezionare **Logs**.

![Log dell'area di lavoro di Log Analytics](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

I registri elencano una serie di query di esempio, ad esempio:

| Nome query                      | Descrizione                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Disponibilità dei computer oggi    | Grafico del numero di computer che inviano registri, ogni ora                     |
| Elenco heartbeat                 | Elenca tutti gli heartbeat del computer dall'ultima ora                           |
| Ultimo battito cardiaco di ogni computer | Mostra l'ultimo heartbeat inviato da ogni computer                             |
| Computer non disponibili           | Elencare tutti i computer noti che non hanno inviato un heartbeat nelle ultime 5 ore |
| Tasso di disponibilità               | Calcolare il tasso di disponibilità di ogni computer collegato                |

Ad esempio, eseguire la query di esempio Tasso di **disponibilità** selezionando **Esegui** su tale query, come illustrato nella schermata precedente. Verrà visualizzata la frequenza di disponibilità di ogni nodo del cluster come percentuale. Se sono stati abilitati più cluster HDInsight per l'invio di metriche alla stessa area di lavoro di Log Analytics, verrà visualizzato il tasso di disponibilità per tutti i nodi di tali cluster.

![Query di esempio dei log dell'area di lavoro di Log Analytics 'tasso di disponibilità'](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE]  
> La tariffa di disponibilità viene misurata in un periodo di 24 ore, pertanto il cluster dovrà essere eseguito per almeno 24 ore prima di visualizzare tariffe di disponibilità accurate.

È possibile aggiungere questa tabella a un dashboard condiviso facendo clic su **Aggiungi** nell'angolo superiore destro. Se non si dispone di dashboard condivisi scrivibili, è possibile vedere come crearne uno qui: Creare e condividere dashboard nel portale di [Azure.](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard)

### <a name="azure-monitor-alerts"></a>Avvisi di Monitoraggio di AzureAzure Monitor alerts

È anche possibile configurare gli avvisi di Monitoraggio di Azure che verranno attivati quando il valore di una metrica o i risultati di una query soddisfano determinate condizioni. Ad esempio, creiamo un avviso per inviare un messaggio di posta elettronica quando uno o più nodi non hanno inviato un heartbeat in 5 ore (ad esempio, si presume che non siano disponibili).

In **Registri**eseguire la query di esempio **Computer non disponibili** selezionando **Esegui** su tale query, come illustrato di seguito.

![Esempio di log dell'area di lavoro di Log Analytics 'computer non disponibili'](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Se tutti i nodi sono disponibili, questa query deve restituire zero risultati per il momento. Fare clic su **Nuova regola** di avviso per iniziare a configurare l'avviso per questa query.

![Nuova regola di avviso dell'area di lavoro di Log AnalyticsLog Analytics workspace new alert rule](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Esistono tre componenti per un avviso: la *risorsa* per cui creare la regola (in questo caso l'area di lavoro di Log Analytics), la *condizione* per attivare l'avviso e i gruppi di *azioni* che determinano cosa accadrà quando l'avviso viene attivato.
Fare clic sul titolo della **condizione,** come illustrato di seguito, per completare la configurazione della logica del segnale.

![Condizione della regola di creazione dell'avviso del portale](media/hdinsight-cluster-availability/portal-condition-title.png)

Si aprirà **Configura logica del segnale**.

Impostare la sezione **Logica di avviso** come segue:Set the Alert logic section as follows:

*Basato su: Numero di risultati, Condizione: Maggiore di, Soglia: 0.*

Poiché questa query restituisce solo i nodi non disponibili come risultati, se il numero di risultati è sempre maggiore di 0, l'avviso deve essere generato.

Nella sezione **Valutato in base** a impostare il **periodo** e la **frequenza** in base alla frequenza con cui si desidera verificare la presenza di nodi non disponibili.

Ai fini del presente avviso, si desidera assicurarsi **che Periodo - Frequenza.** Ulteriori informazioni su periodo, frequenza e altri parametri di avviso sono disponibili [qui](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Al termine della configurazione della logica del segnale, selezionare **Fine.Select Done** when you're finished configuring the signal logic.

![La regola di avviso configura la logica del segnale](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Se non si dispone già di un gruppo di azioni esistente, fare clic su **Crea nuovo** nella sezione Gruppi **di azioni.**

![La regola di avviso crea un nuovo gruppo di azioni](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Verrà aperto **Aggiungi gruppo di azioni**. Scegliere un **gruppo di**azioni, **nome breve**, **Sottoscrizione**e **gruppo risorsa.** Nella sezione **Azioni,** scegliere un **Nome azione** e selezionare **E-mail/SMS/Push/Voice** come **Tipo di azione.**

> [!NOTE]
> Esistono diverse altre azioni che un avviso può attivare oltre a un messaggio di posta elettronica/SMS/Push/Voice, ad esempio una funzione di Azure, LogicApp, Webhook, ITSM e Runbook di automazione. [Ulteriori informazioni.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Si aprirà **Email/SMS/Push/Voice**. Scegliere un **Nome** per il destinatario, **selezionare** la casella **Posta elettronica** e digitare un indirizzo di posta elettronica a cui si desidera inviare l'avviso. Selezionare **OK** in **Email/SMS/Push/Voice**, quindi in **Aggiungi gruppo di azioni** per completare la configurazione del gruppo di azioni.

![La regola di avviso crea un gruppo di azioni di aggiunta](media/hdinsight-cluster-availability/portal-add-action-group.png)

Dopo la chiusura di questi pannelli, il gruppo di azioni dovrebbe essere elencato nella sezione **Gruppi di azioni.** Infine, completare la sezione **Dettagli avviso** digitando un Nome e una **descrizione** **della regola** di avviso e scegliendo un livello **di gravità**. Fare clic su Crea regola di avviso per completare **l'operazione.**

![Portale crea la fine della regola di avviso](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> La possibilità di specificare **Gravità** è uno strumento potente che può essere utilizzato durante la creazione di più avvisi. Ad esempio, è possibile creare un avviso per generare un avviso (Sev 1) se un singolo nodo testa scende e un altro avviso che genera Critical (Sev 0) nell'improbabile caso in cui entrambi i nodi head si arrestino.

Quando viene soddisfatta la condizione per questo avviso, l'avviso verrà generato e si riceverà un messaggio di posta elettronica con i dettagli dell'avviso come questo:

![Esempio di messaggio di posta elettronica di avviso di Azure MonitorAzure Monitor alert email example](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

È inoltre possibile visualizzare tutti gli avvisi generati, raggruppati in base alla gravità, accedendo a **Avvisi** nell'area di lavoro di **Log Analytics**.

![Avvisi dell'area di lavoro di Log AnalyticsLog Analytics workspace alerts](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

Selezionando un raggruppamento di gravità (ad esempio **Sev 1,** come evidenziato in precedenza) verranno visualizzati i record per tutti gli avvisi di tale gravità che sono stati attivati come di seguito:

![Area di lavoro di Log Analytics sev un avviso](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Passaggi successivi

- [Disponibilità e affidabilità dei cluster Apache Hadoop in HDInsight](hdinsight-high-availability-linux.md)

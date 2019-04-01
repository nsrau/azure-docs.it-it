---
title: Come monitorare la disponibilità di cluster con Ambari e monitoraggio di Azure log
description: Informazioni su come usare i log di Ambari e monitoraggio di Azure per monitorare l'integrità del cluster e la disponibilità.
keywords: monitoraggio, ambari, monitoraggio, log analitica, avviso, disponibilità, integrità
services: hdinsight
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tyfox
ms.openlocfilehash: 9bf0c0f45007f81312ac08230d571b727220bcd9
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58671332"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>Come monitorare la disponibilità di cluster con Ambari e monitoraggio di Azure log

I cluster HDInsight includono Apache Ambari, che fornisce informazioni sull'integrità immediatamente e avvisi predefiniti, nonché a integrazione di log di monitoraggio di Azure, che fornisce la metrica disponibile per query e i log, nonché offre avvisi configurabili.

Questo documento illustra come usare questi strumenti per monitorare il cluster e illustra alcuni esempi di configurazione di un avviso di Ambari, frequenza di disponibilità di nodo di monitoraggio e creazione di un avviso di monitoraggio di Azure che viene attivato quando non è stato ricevuto un heartbeat da uno o più nodi in cinque ore.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>dashboard

Il dashboard di Ambari è accessibile facendo clic la **Ambari domestica** clic sul collegamento nel **dashboard Cluster** sezione del pannello Panoramica di HDInsight nel portale di Azure come mostrato di seguito. In alternativa, è possibile accedervi immettendo l'URL seguente in un browser [https://\<clustername\>. azurehdinsight.net](https://clustername.azurehdinsight.net/)

![Visualizzazione nel portale di HDInsight risorsa](media/hdinsight-cluster-availability/portal-overview.png)

Verrà quindi richiesto un nome utente di accesso del cluster e la password. Immettere le credenziali è selezionata durante la creazione del cluster.

È quindi accederà al dashboard di Ambari, che contiene widget che mostrano un numero limitato di metriche per offrirti una rapida panoramica dell'integrità del cluster HDInsight. Questi widget mostrano le metriche, ad esempio il numero di live DataNodes (nodi di lavoro) e JournalNodes (nodo zookeeper), blocchi Namenode (nodi head) tempo di attività, come bene metriche specifiche per determinati tipi di cluster, come tempi di attività di ResourceManager YARN per i cluster Spark e Hadoop.

![Dashboard di Ambari](media/hdinsight-cluster-availability/ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>Hosts – nodo singolo di visualizzare lo stato

È anche possibile visualizzare le informazioni sullo stato per i singoli nodi. Scegliere il **host** pressione di tab per visualizzare un elenco di tutti i nodi nel cluster e visualizzare le informazioni di base su ogni nodo. Il segno di spunta verde a sinistra di ogni nome di nodo indica che tutti i componenti siano operativi nel nodo. Se un componente è attivo in un nodo, si noterà un triangolo di avviso rossa anziché il segno di spunta verde.

![Visualizzazione host di Ambari](media/hdinsight-cluster-availability/ambari-hosts.png)

È possibile fare clic sui **nome** di un nodo per visualizzare altre metriche host per quel determinato nodo. Questa visualizzazione mostra la stato/disponibilità di ogni singolo componente.

![Gli host di Ambari singola visualizzazione del nodo](media/hdinsight-cluster-availability/ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Avvisi di Ambari

Ambari offre anche diversi offre avvisi configurabili che è possano fornire la notifica di determinati eventi. Quando gli avvisi vengono attivati, essi vengono visualizzati nell'angolo superiore sinistro di Ambari in un badge rossa contenente il numero di avvisi. Facendo clic su questa notifica Mostra un elenco degli avvisi correnti.

![Numero di avvisi di Ambari](media/hdinsight-cluster-availability/ambari-alerts.png)

Per visualizzare un elenco di definizioni di avviso e i relativi stati, fare clic sui **avvisi** scheda, come illustrato di seguito.

![Visualizzare le definizioni degli avvisi di Ambari](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari offre molti avvisi predefiniti relativi alla disponibilità, tra cui:

| Nome avviso                        | DESCRIZIONE                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Riepilogo dell'integrità DataNode           | Questo avviso a livello di servizio viene attivato se sono presenti DataNodes non integri                                                                                                                |
| Stato di disponibilità elevata di NameNode | Questo avviso a livello di servizio viene attivato se NameNode attivi o Standby NameNode non è in esecuzione.                                                                              |
| Percentuale JournalNodes disponibili    | Questo avviso viene attivato se il numero di verso il basso JournalNodes nel cluster è superiore alla soglia critica configurata. Aggrega i risultati del processo di JournalNode viene verificato. |
| Percentuale DataNodes disponibili       | Questo avviso viene attivato se il numero di verso il basso DataNodes nel cluster è superiore alla soglia critica configurata. Aggrega i risultati del processo di DataNode viene verificato.       |

Un elenco completo di Ambari avvisa che monitora l'help la disponibilità di un cluster è reperibile [qui](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui),

Per visualizzare i dettagli per un avviso o modificare i criteri, scegliere il **nome** dell'avviso. Richiedere **riepilogo dell'integrità DataNode** come esempio. È possibile visualizzare una descrizione dell'avviso, nonché i criteri specifici che attiveranno un avviso 'warning' o 'critical' e l'intervallo di controllo per i criteri. Per modificare la configurazione, scegliere il **modifica** pulsante nell'angolo superiore destro della finestra di configurazione.

![Configurazione degli avvisi di Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

In questo caso, è possibile modificare la descrizione e, ancora più importante, il controllo di intervallo e le soglie per gli avvisi di avvertenza o critici.

![Visualizzazione di modifica di configurazione degli avvisi di Ambari](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

In questo esempio, è possibile apportare DataNodes non integri 2 attivano un avviso critico e 1 tipo non integro DataNode attivazione solo un avviso. Fare clic su **salvare** termine modifica.

### <a name="email-notifications"></a>Notifiche tramite posta elettronica

È possibile anche configurare notifiche tramite posta elettronica per gli avvisi di Ambari. A tale scopo, quando si utilizza l'il **gli avvisi** scheda, fare clic sui **azioni** pulsante in alto a sinistra, quindi **gestire le notifiche.**

![Azione di notifiche di gestione Ambari](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

Verrà aperta una finestra di dialogo per la gestione delle notifiche di avviso. Scegliere il **+** nella parte inferiore della finestra di dialogo e compilare i campi necessari per fornire Ambari con messaggio di posta elettronica i dettagli del server da cui inviare messaggi di posta elettronica.

> [!TIP]
> Configurazione di Ambari notifiche tramite posta elettronica possono essere un ottimo modo per ricevere gli avvisi in un'unica posizione quando si gestiscono molti cluster HDInsight.

## <a name="azure-monitor-logs-integration"></a>Monitoraggio di Azure log integration

Monitoraggio di Azure registra Abilita i dati generati da più risorse, ad esempio i cluster HDInsight, essere raccolti e aggregati in un'unica posizione per ottenere un'esperienza di monitoraggio unificata.

Come prerequisito, è necessario un'area di lavoro di Log Analitica per archiviare i dati raccolti. Se non già stato creato uno, è possibile seguire queste istruzioni: [Creare un'area di lavoro di Log Analitica](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Abilitare l'integrazione dei log di monitoraggio di Azure HDInsight

Nella pagina delle risorse di cluster HDInsight nel portale, fare clic sui **Operations Management Suite** pannello. Fare quindi clic **abilitare** e selezionare l'area di lavoro di Log Analitica dall'elenco a discesa.

![Pannello HDInsight Operations Management Suite](media/hdinsight-cluster-availability/portal-enable-oms.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>Eseguire query sulle tabelle di log e metriche nel pannello del log

Una volta attivata l'integrazione dei log di monitoraggio di Azure (l'operazione potrebbe richiedere alcuni minuti), passare al **dell'area di lavoro di Log Analitica** risorse e fare clic sul **log** pannello

![Pannello log di log Analitica dell'area di lavoro](media/hdinsight-cluster-availability/portal-logs.png)

Il **registri** pannello elenca un numero di query di esempio, ad esempio:

| Nome della query                      | DESCRIZIONE                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Oggi stesso la disponibilità di computer    | Il numero di computer che inviano i log, ogni ora del grafico                     |
| Elenco heartbeat                 | Elencare tutti gli heartbeat computer dall'ultima ora                           |
| Data ultimo heartbeat di ciascun computer | Mostra l'ultimo heartbeat inviato da ogni computer                             |
| Computer non disponibili           | Elencare tutti i computer noti che non inviano un heartbeat nelle ultime 5 ore |
| Frequenza di disponibilità               | Calcola la frequenza di disponibilità di ogni computer connesso                |

Ad esempio, eseguire la **frequenza di disponibilità** esempio di query, fare clic su **eseguire** su di essa, come illustrato nello screenshot precedente. La frequenza di disponibilità di ogni nodo nel cluster verranno visualizzati sotto forma di percentuale. Se è stato abilitato più cluster HDInsight inviare le metriche per la stessa area di lavoro di Log Analitica, si noterà la frequenza di disponibilità per tutti i nodi nel cluster visualizzati.

![Log di query di esempio 'frequenza di disponibilità' blade i log dell'area di lavoro Analitica](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> Frequenza di disponibilità viene misurata in un periodo di 24 ore, in modo che il cluster sarà necessario eseguire per almeno 24 ore prima di poter visualizzare tariffe di disponibilità accurate.

È possibile aggiungere questa tabella a un dashboard condiviso facendo **Pin** nell'angolo superiore destro. Se non si sono presenti dashboard condiviso accessibile in scrittura, è possibile visualizzare come crearne uno qui: [Creare e condividere i dashboard nel portale di Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-a-dashboard-and-manage-access-control).

### <a name="azure-monitor-alerts"></a>Avvisi di monitoraggio di Azure

È anche possibile impostare gli avvisi di monitoraggio di Azure che verranno attivata quando il valore di una metrica o i risultati di una query soddisfano determinate condizioni. Ad esempio, è possibile creare un avviso per inviare un messaggio di posta elettronica quando uno o più nodi non ha inviato un heartbeat in 5 ore (ad esempio si presuppone che sia disponibile).

Dal **registri** pannello, eseguire il **computer non disponibili** query di esempio facendo clic su **eseguire** su di essa, come illustrato di seguito.

![Log di query di esempio 'computer disponibile' blade log dell'area di lavoro Analitica](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

Se tutti i nodi sono disponibili, questa query deve restituire 0 risultati per il momento. Fare clic su **nuova regola di avviso** per iniziare a configurare l'avviso per questa query.

![Log Analitica dell'area di lavoro nuova regola di avviso](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

Sono presenti tre componenti a un avviso: il *resource* per il quale creare la regola (il Log Analitica dell'area di lavoro in questo caso), il *condizione* per attivare l'avviso e il *gruppi di azioni*  che determinare cosa succede quando viene generato l'avviso.

Scegliere il **titolo della condizione**, come illustrato di seguito per completare la configurazione per la logica di segnale.

![Condizione di regola di avviso](media/hdinsight-cluster-availability/portal-condition-title.png)

Verrà aperta la **configura la logica del segnale** pannello.

Impostare il **logica avvisi** sezione come indicato di seguito:

*Base: Numero di risultati, condizione: Maggiore di, soglia: 0.*

Poiché la query restituisce solo nodi non disponibili come risultati, se il numero di risultati è mai maggiore di 0, l'avviso deve essere attivata.

Nel **valutati in base** sezione, impostare il **periodo** e **frequenza** basato su quanto spesso si desidera verificare la presenza di nodi non disponibili.

Si noti che ai fini di questo avviso, si desidera assicurarsi **periodo = frequenza.** Altre informazioni sul periodo, la frequenza e altri parametri di avviso sono reperibili [qui](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Fare clic su **** al termine della configurazione per la logica di segnale.

![Regola di avviso Configura logica dei segnali](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

Se si dispone già di un gruppo di azione esistente, fare clic su **Crea nuovo** sotto il **gruppi di azioni** sezione.

![Nuovo gruppo di azioni regola di avviso](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

Verrà aperta la **Aggiungi gruppo di azione** pannello. Scegliere un' **nome gruppo di azione**, **nome breve**, **sottoscrizione**, e **gruppo di risorse.** Sotto il **azioni** keychains un **ActionName** e selezionare **posta elettronica/SMS/Push/voce** come il **tipo di azione.**

> [!NOTE]
> Esistono diverse altre operazioni che oltre a una messaggio di posta elettronica/SMS/Push/voce, ad esempio una funzione di Azure, App per la logica, Webhook, ITSM e Runbook di automazione possa attivare un avviso. [Ulteriori informazioni.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Verrà aperta la **messaggio di posta elettronica/SMS/Push/voce** pannello. Scegliere una **nome** per il destinatario **controllare** il **messaggio di posta elettronica** , quindi digitare un indirizzo di posta elettronica a cui si vuole inviare l'avviso. Fare clic su **OK** nel **indirizzo di posta elettronica/SMS/Push/voce** pannello, quindi nella **Aggiungi gruppo di azione** pannello per completare la configurazione del gruppo di azioni.

![Regola di avviso Aggiungi gruppo di azioni](media/hdinsight-cluster-availability/portal-add-action-group.png)

Dopo la chiusura questi pannelli, dovrebbe essere il gruppo di azioni elencato sotto la **gruppi di azioni** sezione. Infine, completare i **dettagli dell'avviso** sezione digitando un' **nome regola di avviso** e **descrizione** e scegliendo un **gravità**.
Fare clic su **Crea regola di avviso** alla fine.

![Creare una regola di avviso fine](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> La possibilità di specificare **gravità** è uno strumento potente che può essere utilizzato durante la creazione di più avvisi. Ad esempio, è possibile creare un avviso per generare un avviso (gravità 1) se un singolo nodo head va verso il basso e un altro avviso generato critico (gravità 0) nel caso improbabile che entrambi i nodi head diventano inattivi.

Quando viene soddisfatta la condizione per questo avviso, l'avviso viene attivato e si riceverà un messaggio di posta elettronica con i dettagli dell'avviso simile al seguente:

![Azure Monitor avviso tramite posta elettronica](media/hdinsight-cluster-availability/alert-email.png)

È anche possibile visualizzare tutti gli avvisi attivati, raggruppati in base alla gravità, passando il **gli avvisi** pannello nel **dell'area di lavoro di Log Analitica**.

![Gli avvisi dell'area di lavoro di Analitica del log](media/hdinsight-cluster-availability/portal-alerts.png)

Facendo clic su un raggruppamento di gravità (vale a dire **1, gravità** come evidenziato sopra) visualizzerà i record per tutti gli avvisi di quel livello di gravità che sono stati attivati come di seguito:

![Avvisi di gravità 1 Analitica dell'area di lavoro di log](media/hdinsight-cluster-availability/portal-alerts-sev-1.png)

## <a name="next-steps"></a>Passaggi successivi
- [Disponibilità e affidabilità dei cluster Apache Hadoop in HDInsight](hdinsight-high-availability-linux.md)
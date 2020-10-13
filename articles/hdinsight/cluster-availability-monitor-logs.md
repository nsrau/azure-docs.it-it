---
title: Come monitorare la disponibilità dei cluster con i log di monitoraggio di Azure in HDInsight
description: Informazioni su come usare i log di monitoraggio di Azure per monitorare l'integrità e la disponibilità del cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 08/12/2020
ms.openlocfilehash: 19e3f1a157ee2c042dfebfc96c9b51c3c4698ebc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88163731"
---
# <a name="how-to-monitor-cluster-availability-with-azure-monitor-logs-in-hdinsight"></a>Come monitorare la disponibilità dei cluster con i log di monitoraggio di Azure in HDInsight

I cluster HDInsight includono l'integrazione dei log di monitoraggio di Azure, che fornisce metriche e log Queryable, oltre a avvisi configurabili. Questo articolo illustra come usare monitoraggio di Azure per monitorare il cluster.

## <a name="azure-monitor-logs-integration"></a>Integrazione dei log di monitoraggio di Azure

I log di monitoraggio di Azure consentono di raccogliere e aggregare i dati generati da più risorse, ad esempio i cluster HDInsight, in un'unica posizione per ottenere un'esperienza di monitoraggio unificata.

Come prerequisito, è necessario disporre di un'area di lavoro Log Analytics per archiviare i dati raccolti. Se non è già stato creato, è possibile seguire le istruzioni riportate qui: [creare un'area di lavoro log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

## <a name="enable-hdinsight-azure-monitor-logs-integration"></a>Abilitare l'integrazione dei log di monitoraggio di Azure HDInsight

Dalla pagina delle risorse cluster HDInsight nel portale selezionare monitoraggio di **Azure**. Selezionare quindi **Abilita** e selezionare l'area di lavoro log Analytics dall'elenco a discesa.

![HDInsight Operations Management Suite](media/cluster-availability-monitor-logs/azure-portal-monitoring.png)

Per impostazione predefinita, l'agente OMS viene installato in tutti i nodi del cluster, ad eccezione dei nodi perimetrali. Poiché non è installato alcun agente OMS nei nodi perimetrali del cluster, per impostazione predefinita non sono presenti dati di telemetria sui nodi perimetrali presenti in Log Analytics.

## <a name="query-metrics-and-logs-tables"></a>Tabelle di metriche e log di query

Una volta abilitata l'integrazione dei log di monitoraggio di Azure (l'operazione potrebbe richiedere alcuni minuti), passare alla risorsa dell' **area di lavoro log Analytics** e selezionare **log**.

![Log dell'area di lavoro Log Analytics](media/cluster-availability-monitor-logs/hdinsight-portal-logs.png)

Log elenca una serie di query di esempio, ad esempio:

| Nome query                      | Descrizione                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| Disponibilità di computer oggi    | Grafico numero di computer che inviano log, ogni ora                     |
| Elenca heartbeat                 | Elencare tutti gli heartbeat del computer nell'ultima ora                           |
| Ultimo heartbeat di ogni computer | Mostra l'ultimo heartbeat inviato da ogni computer                             |
| Computer non disponibili           | Elencare tutti i computer noti che non hanno inviato un heartbeat nelle ultime 5 ore |
| Frequenza di disponibilità               | Calcolare la frequenza di disponibilità di ogni computer connesso                |

Eseguire ad esempio la query di esempio relativa alla **frequenza di disponibilità** selezionando **Esegui** nella query, come illustrato nella schermata precedente. Verrà visualizzata la percentuale di disponibilità di ogni nodo nel cluster come percentuale. Se sono stati abilitati più cluster HDInsight per l'invio di metriche alla stessa area di lavoro Log Analytics, viene visualizzata la tariffa di disponibilità per tutti i nodi (esclusi i nodi perimetrali) presenti in tali cluster.

![Query di esempio ' frequenza di disponibilità' dei log dell'area di lavoro Log Analytics](media/cluster-availability-monitor-logs/portal-availability-rate.png)

> [!NOTE]  
> La frequenza di disponibilità viene misurata in un periodo di 24 ore, quindi il cluster deve essere eseguito per almeno 24 ore prima di visualizzare le tariffe di disponibilità accurate.

È possibile aggiungere questa tabella a un dashboard condiviso facendo clic su **Aggiungi** nell'angolo in alto a destra. Se non sono presenti dashboard condivisi scrivibili, è possibile vedere come crearne uno qui: [creare e condividere i dashboard nel portale di Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard).

## <a name="azure-monitor-alerts"></a>Avvisi di Monitoraggio di Azure

È anche possibile configurare gli avvisi di monitoraggio di Azure che si attiveranno quando il valore di una metrica o i risultati di una query soddisfano determinate condizioni. Si supponga, ad esempio, di creare un avviso per inviare un messaggio di posta elettronica quando uno o più nodi non hanno inviato un heartbeat in 5 ore, ovvero si presume che non sia disponibile.

Da **log**eseguire la query di esempio **computer non disponibili** selezionando **Esegui** nella query, come illustrato di seguito.

![Esempio di log dell'area di lavoro di Log Analytics ' computer non disponibili '](media/cluster-availability-monitor-logs/portal-unavailable-computers.png)

Se tutti i nodi sono disponibili, per il momento questa query restituirà zero risultati. Fare clic su **nuova regola di avviso** per iniziare la configurazione dell'avviso per la query.

![Nuova regola di avviso per l'area di lavoro Log Analytics](media/cluster-availability-monitor-logs/portal-logs-new-alert-rule.png)

Sono disponibili tre componenti per un avviso: la *risorsa* per la quale creare la regola (in questo caso l'area di lavoro log Analytics), la *condizione* per attivare l'avviso e i *gruppi di azioni* che determinano cosa accadrà quando viene attivato l'avviso.
Fare clic sul **titolo della condizione**, come illustrato di seguito, per completare la configurazione della logica del segnale.

![Condizione di creazione della regola di avviso del portale](media/cluster-availability-monitor-logs/portal-condition-title.png)

Verrà visualizzata la **configurazione della logica del segnale**.

Impostare la sezione **logica avvisi** come segue:

*In base a: numero di risultati, condizione: maggiore di, soglia: 0.*

Poiché questa query restituisce solo i nodi non disponibili come risultati, se il numero di risultati è sempre maggiore di 0, l'avviso deve essere attivato.

Nella sezione **valutato in base a** impostare il **periodo** e la **frequenza** in base alla frequenza con cui si desidera controllare i nodi non disponibili.

Ai fini di questo avviso, è necessario assicurarsi che **periodo = frequenza.** Altre informazioni su periodo, frequenza e altri parametri di avviso sono disponibili [qui](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types).

Selezionare **fine** al termine della configurazione della logica del segnale.

![Regola di avviso configura la logica del segnale](media/cluster-availability-monitor-logs/portal-configure-signal-logic.png)

Se non si dispone già di un gruppo di azioni esistente, fare clic su **Crea nuovo** nella sezione **gruppi di azioni** .

![Regola di avviso crea un nuovo gruppo di azioni](media/cluster-availability-monitor-logs/portal-create-new-action-group.png)

Verrà aperto **Aggiungi gruppo di azione**. Scegliere un **nome del gruppo di azioni**, un **nome breve**, una **sottoscrizione**e un **gruppo di risorse.** Nella sezione **azioni** scegliere un nome di **azione** e selezionare **posta elettronica/SMS/push/Voice** come **tipo di azione.**

> [!NOTE]
> Esistono diverse altre azioni che possono essere attivate da un avviso oltre a un messaggio di posta elettronica/SMS/push/Voice, ad esempio una funzione di Azure, LogicApp, webhook, ITSM e Runbook di automazione. [Ulteriori informazioni.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

Verrà aperta la **posta elettronica/SMS/push/Voice**. Scegliere un **nome** per il destinatario, **selezionare** la casella **posta elettronica** e digitare un indirizzo di posta elettronica a cui si vuole inviare l'avviso. Selezionare **OK** in  **posta elettronica/SMS/push/voce**, quindi in **Aggiungi gruppo di azioni** per completare la configurazione del gruppo di azioni.

![Regola di avviso crea il gruppo di azioni Aggiungi](media/cluster-availability-monitor-logs/portal-add-action-group.png)

Dopo la chiusura di questi pannelli, il gruppo di azioni verrà visualizzato nella sezione **gruppi di azioni** . Infine, completare la sezione **Dettagli avviso** digitando un nome e una **Descrizione** della **regola di avviso** e scegliendo una **gravità**. Fare clic su **Crea regola di avviso** per terminare.

![Il portale crea una regola di avviso completa](media/cluster-availability-monitor-logs/portal-create-alert-rule-finish.png)

> [!TIP]
> La possibilità di specificare la **gravità** è uno strumento potente che può essere usato durante la creazione di più avvisi. Ad esempio, è possibile creare un avviso per generare un avviso (gravità 1) se un singolo nodo head diventa inattivo e un altro avviso che genera Critical (gravità 0) nell'evento improbabile in cui entrambi i nodi head si arrestano.

Quando viene soddisfatta la condizione per questo avviso, l'avviso viene attivato e si riceverà un messaggio di posta elettronica con i dettagli dell'avviso, come indicato di seguito:

![Esempio di messaggio di posta elettronica di avviso monitoraggio di Azure](media/cluster-availability-monitor-logs/portal-oms-alert-email.png)

È anche possibile visualizzare tutti gli avvisi generati, raggruppati in base alla gravità, passando ad **avvisi** nell' **area di lavoro log Analytics**.

![Avvisi dell'area di lavoro Log Analytics](media/cluster-availability-monitor-logs/hdi-portal-oms-alerts.png)

Selezionando un raggruppamento di gravità (ad esempio, il valore di gravità **1,** come evidenziato in precedenza), i record per tutti gli avvisi di tale gravità vengono visualizzati come indicato di seguito:

![Log Analytics l'area di lavoro SEV un avviso](media/cluster-availability-monitor-logs/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>Passaggi successivi

* [Disponibilità dei cluster - Apache Ambari](./hdinsight-cluster-availability.md)
* [Usare i log di monitoraggio di Azure](hdinsight-hadoop-oms-log-analytics-tutorial.md)

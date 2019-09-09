---
title: Ridimensionare automaticamente i cluster Azure HDInsight (anteprima)
description: Usare la funzionalità di scalabilità automatica di Azure HDInsight per Apache Hadoop automaticamente i cluster di scalabilità
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: hrasheed
ms.openlocfilehash: 877fc3a4e29fc1753d7b4f92091b34d4b2537846
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810321"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Ridimensionare automaticamente i cluster Azure HDInsight (anteprima)

> [!Important]
> La funzionalità di scalabilità automatica funziona solo per i cluster Spark, hive e MapReduce creati dopo l'8 maggio 2019. 

La funzionalità di scalabilità automatica del cluster di Azure HDInsight consente di ridimensionare automaticamente il numero di nodi del ruolo di lavoro in un cluster. Attualmente non è possibile ridimensionare altri tipi di nodi nel cluster.  Durante la creazione di un nuovo cluster HDInsight è possibile impostare un numero minimo e massimo di nodi del ruolo di lavoro. La scalabilità automatica monitora quindi i requisiti delle risorse del carico di analisi e ridimensiona il numero di nodi di lavoro verso l'alto o verso il basso. Non sono previsti costi aggiuntivi per questa funzionalità.

## <a name="cluster-compatibility"></a>Compatibilità del cluster

La tabella seguente descrive i tipi di cluster e le versioni compatibili con la funzionalità di scalabilità automatica.

| Versione | Spark | Hive | LLAP | hbase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3,6 senza ESP | Sì | Sì | No | No | No | No | No |
| HDInsight 4,0 senza ESP | Sì | Sì | No | No | No | No | No |
| HDInsight 3,6 con ESP | Sì | Sì | No | No | No | No | No |
| HDInsight 4,0 con ESP | Sì | Sì | No | No | No | No | No |

## <a name="how-it-works"></a>Funzionamento

È possibile scegliere il ridimensionamento basato sul carico o la scalabilità basata sulla pianificazione per il cluster HDInsight. Il ridimensionamento basato sul carico modifica il numero di nodi nel cluster, all'interno di un intervallo impostato, per garantire un utilizzo ottimale della CPU e ridurre al minimo i costi di esecuzione.

La scalabilità basata sulla pianificazione modifica il numero di nodi nel cluster in base a condizioni che hanno effetto in momenti specifici. Queste condizioni ridimensionano il cluster in base al numero desiderato di nodi.

### <a name="metrics-monitoring"></a>Monitoraggio delle metriche

La funzionalità di scalabilità automatica monitora continuamente il cluster e raccoglie le metriche seguenti:

* **Total Pending CPU** (Totale CPU in sospeso): numero totale di core necessari per avviare l'esecuzione di tutti i contenitori in sospeso.
* **Total Pending Memory** (Totale memoria in sospeso): memoria totale (in MB) necessaria per avviare l'esecuzione di tutti i contenitori in sospeso.
* **Total Free CPU** (Totale CPU disponibile): somma di tutti i core inutilizzati nei nodi del ruolo di lavoro attivi.
* **Total Free Memory** (Totale memoria disponibile): somma della memoria inutilizzata (in MB) nei nodi del ruolo di lavoro attivi.
* **Used Memory per Node** (Memoria utilizzata per nodo): carico su un nodo del ruolo di lavoro. Un nodo del ruolo di lavoro in cui sono utilizzati 10 GB di memoria è considerato come sottoposto a un carico superiore rispetto a un nodo del ruolo di lavoro con 2 GB di memoria utilizzata.
* **Number of Application Masters per Node** (Numero di master applicazioni per nodo): numero di contenitori di master applicazioni in esecuzione su un nodo del ruolo di lavoro. Un nodo del ruolo di lavoro che ospita due contenitori AM è considerato più importante di un nodo del ruolo di lavoro che ospita zero contenitori.

Le metriche riportate sopra vengono controllate ogni 60 secondi. La scalabilità automatica semplifica le decisioni di scalabilità verticale e verticale in base a queste metriche.

### <a name="load-based-cluster-scale-up"></a>Scalabilità verticale del cluster basato sul carico

Quando vengono rilevate le condizioni seguenti, la scalabilità automatica emette una richiesta di scalabilità verticale:

* Il totale della CPU in sospeso è maggiore del totale della CPU disponibile per più di 3 minuti.
* Il totale della memoria in sospeso è maggiore della memoria totale libera per più di 3 minuti.

Il servizio HDInsight calcola il numero di nuovi nodi di lavoro necessari per soddisfare i requisiti di CPU e memoria correnti, quindi emette una richiesta di scalabilità verticale per aggiungere il numero di nodi necessario.

### <a name="load-based-cluster-scale-down"></a>Ridimensionamento del cluster basato sul carico

Quando vengono rilevate le condizioni seguenti, la scalabilità automatica emette una richiesta di scalabilità verticale:

* Il totale CPU in sospeso è minore del totale CPU disponibile per più di 10 minuti.
* Il totale memoria in sospeso è minore del totale memoria disponibile per più di 10 minuti.

In base al numero di contenitori AM per nodo e ai requisiti di memoria e CPU correnti, la scalabilità automatica emette una richiesta di rimozione di un determinato numero di nodi. Il servizio rileva anche quali nodi sono candidati per la rimozione in base all'esecuzione del processo corrente. L'operazione di riduzione delle prestazioni consente innanzitutto di rimuovere le autorizzazioni dei nodi e quindi di rimuoverli dal cluster.

## <a name="get-started"></a>Attività iniziali

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Creare un cluster con scalabilità automatica basata sul carico

Per abilitare la funzionalità di scalabilità automatica con il ridimensionamento basato sul carico, completare i passaggi seguenti come parte del normale processo di creazione del cluster:

1. Selezionare **Custom (size, settings, apps)** (Impostazioni personalizzate (dimensione, impostazioni, app)) invece di **Quick create** (Creazione rapida).
1. In passaggio **personalizzato** 5 (**dimensioni cluster**) selezionare la casella di controllo **ridimensionamento** automatico del nodo di lavoro.
1. Selezionare l'opzione in **base al carico** in tipo di **scalabilità**automatica.
1. Immettere i valori desiderati per le proprietà seguenti:  

    * Il numero iniziale di nodi del ruolo di lavoro (**Number of Worker nodes**).  
    * Il numero minimo (**Minimum**) di nodi del ruolo di lavoro.  
    * Il numero massimo (**Maximum**) di nodi del ruolo di lavoro.  

    ![Abilitare la scalabilità automatica basata sul carico del nodo di lavoro](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

Il numero iniziale di nodi del ruolo di lavoro deve essere compreso tra il numero minimo e il numero massimo inclusi. Questo valore definisce le dimensioni iniziali del cluster al momento della creazione. Il numero minimo di nodi del ruolo di lavoro deve essere maggiore di zero.

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Creare un cluster con scalabilità automatica basata sulla pianificazione

Per abilitare la funzionalità di scalabilità automatica con scalabilità basata sulla pianificazione, completare i passaggi seguenti come parte del normale processo di creazione del cluster:

1. Selezionare **Custom (size, settings, apps)** (Impostazioni personalizzate (dimensione, impostazioni, app)) invece di **Quick create** (Creazione rapida).
1. In passaggio **personalizzato** 5 (**dimensioni cluster**) selezionare la casella di controllo **ridimensionamento** automatico del nodo di lavoro.
1. Immettere il **numero di nodi del ruolo di lavoro**, che controlla il limite per la scalabilità verticale del cluster.
1. Selezionare l'opzione **pianificazione-in base** al **tipo di scalabilità**automatica.
1. Fare clic su **Configura** per aprire la finestra **configurazione ridimensionamento** automatico.
1. Selezionare il fuso orario e quindi fare clic su **+ Aggiungi condizione** .
1. Consente di selezionare i giorni della settimana a cui deve essere applicata la nuova condizione.
1. Modificare il tempo di effetto della condizione e il numero di nodi a cui il cluster deve essere ridimensionato.
1. Se necessario, aggiungere altre condizioni.

    ![Abilitare la creazione basata sulla pianificazione del nodo di lavoro](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Il numero di nodi deve essere compreso tra 1 e il numero di nodi del ruolo di lavoro immesso prima di aggiungere condizioni.

### <a name="final-creation-steps"></a>Passaggi di creazione finali

Per il ridimensionamento basato sul carico e sulla pianificazione, selezionare il tipo di macchina virtuale per i nodi del ruolo di lavoro facendo clic su **dimensioni del nodo di lavoro** e dimensioni del **nodo head**. Dopo aver scelto il tipo di macchina virtuale per ogni tipo di nodo, è possibile visualizzare l'intervallo di costo stimato per l'intero cluster. Modificare i tipi di VM per adattarli al budget.

![Abilitare le dimensioni del nodo di scalabilità automatica basato su pianificazione del nodo di lavoro](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-node-size-selection.png)

La sottoscrizione in uso ha una quota di capacità per ogni area. Il numero totale di core dei nodi head combinato con il numero massimo di nodi del ruolo di lavoro non può superare la quota di capacità. Questa quota tuttavia è un limite flessibile, in quanto è sempre possibile creare un ticket di supporto per aumentarla facilmente.

> [!Note]  
> Se si supera il limite di quota di core totale, verrà visualizzato un messaggio di errore che indica che il nodo massimo ha superato i core disponibili in questa area. scegliere un'altra area oppure contattare il supporto tecnico per aumentare la quota.

Per altre informazioni sulla creazione del cluster HDInsight tramite il portale di Azure, vedere [Creare cluster basati su Linux in HDInsight tramite il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Creare un cluster con un modello di Resource Manager

#### <a name="load-based-autoscaling"></a>Ridimensionamento automatico basato sul carico

È possibile creare un cluster HDInsight con il ridimensionamento automatico basato sul carico di un modello di Azure Resource Manager `autoscale` , aggiungendo un `computeProfile` nodo  >  `workernode` alla sezione con `minInstanceCount` le `maxInstanceCount` proprietà e come mostrato nel frammento di codice JSON seguente.

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 2,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

Per altre informazioni sulla creazione di cluster con modelli di Resource Manager, vedere [Creare cluster Apache Hadoop in HDInsight mediante modelli di Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  

#### <a name="schedule-based-autoscaling"></a>Scalabilità automatica basata sulla pianificazione

È possibile creare un `autoscale` cluster HDInsight con la `computeProfile`  >  `workernode` scalabilità automatica basata sulla pianificazione di un modello di Azure Resource Manager, aggiungendo un nodo alla sezione. Il `autoscale` nodo contiene un `recurrence` oggetto che ha `timezone` un `schedule` oggetto e che descrive quando verrà eseguita la modifica.

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Abilitare e disabilitare la scalabilità automatica per un cluster in esecuzione

#### <a name="using-the-azure-portal"></a>Uso del portale di Azure
Per abilitare la scalabilità automatica in un cluster in esecuzione, selezionare **dimensioni del cluster** in **Impostazioni**. Fare quindi clic su **Abilita scalabilità**automatica. Selezionare il tipo di scalabilità automatica desiderata e immettere le opzioni per la scalabilità basata sul carico o sulla pianificazione. Infine, fare clic su **Salva**.

![Abilitare la scalabilità automatica basata sulla pianificazione del nodo di lavoro in esecuzione nel cluster](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

#### <a name="using-the-rest-api"></a>Utilizzo dell'API REST
Per abilitare o disabilitare la scalabilità automatica in un cluster in esecuzione usando l'API REST, effettuare una richiesta POST all'endpoint di scalabilità automatica, come illustrato nel frammento di codice seguente:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Usare i parametri appropriati nel payload della richiesta. Il payload JSON seguente può essere usato per abilitare la scalabilità automatica. Usare il payload `{autoscale: null}` per disabilitare la scalabilità automatica.

```json
{ autoscale: { capacity: { minInstanceCount: 1, maxInstanceCount: 2 } } }
```

Vedere la sezione precedente sull' [Abilitazione della scalabilità automatica basata sul carico](#load-based-autoscaling) per una descrizione completa di tutti i parametri del payload.

## <a name="best-practices"></a>Procedure consigliate

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Scelta del ridimensionamento basato sul carico o sulla pianificazione

Prima di prendere una decisione sulla modalità da scegliere, considerare i fattori seguenti:

* Varianza del carico: il carico del cluster segue un modello coerente in momenti specifici, in giorni specifici. In caso contrario, la pianificazione basata sul carico è un'opzione migliore.
* Requisiti del contratto di contratto: La scalabilità automatica è reattiva anziché predittiva. Si verifica un ritardo sufficiente tra il momento in cui il carico inizia ad aumentare e quando il cluster deve avere le dimensioni di destinazione? Se sono presenti requisiti rigorosi per il contratto di contratto e il carico è un modello noto fisso,' pianificazione basata ' è un'opzione migliore.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Prendere in considerazione la latenza delle operazioni di scalabilità verticale o verticale

Il completamento di un'operazione di ridimensionamento può richiedere da 10 a 20 minuti. Quando si configura una pianificazione personalizzata, pianificare questo ritardo. Se, ad esempio, è necessario che le dimensioni del cluster siano pari a 20 alle 9:00, impostare il trigger di pianificazione su un orario precedente, ad esempio 8:30 AM, in modo che l'operazione di ridimensionamento sia stata completata da 9:00 AM.

### <a name="preparation-for-scaling-down"></a>Preparazione per la scalabilità verticale

Durante il processo di ridimensionamento del cluster, la scalabilità automatica eliminerà le autorizzazioni dei nodi per soddisfare le dimensioni di destinazione. Se sono presenti attività in esecuzione su tali nodi, la scalabilità automatica resterà in attesa fino al completamento delle attività. Poiché ogni nodo di lavoro svolge anche un ruolo in HDFS, i dati temporanei vengono spostati nei nodi rimanenti. Quindi, è necessario assicurarsi che vi sia spazio sufficiente nei nodi rimanenti per ospitare tutti i dati temporanei. 

I processi in esecuzione continueranno a essere eseguiti e completati. I processi in sospeso rimarranno in attesa di essere pianificati normalmente con un minor numero di nodi di lavoro disponibili.

## <a name="monitoring"></a>Monitoraggio

### <a name="cluster-status"></a>Stato cluster

Lo stato del cluster elencato nell'portale di Azure consente di monitorare le attività di ridimensionamento automatico.

![Abilitare lo stato del cluster di scalabilità automatica basato sul carico del nodo di lavoro](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Tutti i messaggi di stato del cluster visualizzati potrebbero essere illustrati nell'elenco seguente.

| Stato cluster | Spiegazione |
|---|---|
| In esecuzione | Il cluster funziona normalmente. Tutte le attività di ridimensionamento automatico precedenti sono state completate correttamente. |
| Aggiornamento  | È in corso l'aggiornamento della configurazione di scalabilità automatica del cluster.  |
| Configurazione di HDInsight  | È in corso un'operazione di scalabilità verticale o verticale del cluster.  |
| Errore di aggiornamento  | HDInsight ha rilevato problemi durante l'aggiornamento della configurazione di scalabilità automatica. I clienti possono scegliere di ritentare l'aggiornamento o disabilitare la scalabilità automatica.  |
| Errore  | Si è verificato un errore nel cluster e non è utilizzabile. Eliminare questo cluster e crearne uno nuovo.  |

Per visualizzare il numero corrente di nodi nel cluster, passare al grafico delle **dimensioni del cluster** nella pagina **Panoramica** del cluster oppure fare clic su **dimensioni del cluster** in **Impostazioni**.

### <a name="operation-history"></a>Cronologia delle operazioni

È possibile visualizzare la cronologia di scalabilità orizzontale e verticale del cluster come parte della metrica del cluster. È anche possibile elencare tutte le azioni di scalabilità nel giorno precedente, settimana o altro periodo di tempo.

Selezionare le **metriche** in **monitoraggio**. Quindi fare clic su **Aggiungi metrica** e **numero di thread di lavoro attivi** nella casella a discesa **metrica** . Fare clic sul pulsante in alto a destra per modificare l'intervallo di tempo.

![Abilitare la metrica di scalabilità automatica basata sulla pianificazione del nodo di lavoro](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)


## <a name="next-steps"></a>Passaggi successivi

* Per le procedure consigliate relative al ridimensionamento manuale dei cluster, vedere [Ridimensionare i cluster HDInsight](hdinsight-scaling-best-practices.md)

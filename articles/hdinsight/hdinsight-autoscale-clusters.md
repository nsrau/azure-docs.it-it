---
title: Ridimensionare automaticamente i cluster Azure HDInsight
description: Usare la funzionalità di scalabilità automatica di Azure HDInsight per eseguire automaticamente i cluster con scalabilità Apache HadoopUse the Azure HDInsight Autoscale feature to automatically Apache Hadoop scale clusters
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/05/2020
ms.openlocfilehash: 68bc30d08d95fe8e3d20a8ecb7af6c9710951921
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399707"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Ridimensionare automaticamente i cluster Azure HDInsight

> [!Important]
> La funzionalità di scalabilità automatica di Azure HDInsight è stata rilasciata per la disponibilità generale il 7 novembre 2019 per i cluster Spark e Hadoop e include miglioramenti non disponibili nella versione di anteprima della funzionalità. Se è stato creato un cluster Spark prima del 7 novembre 2019 e si desidera utilizzare la funzionalità di scalabilità automatica nel cluster, il percorso consigliato consiste nel creare un nuovo cluster e abilitare la scalabilità automatica nel nuovo cluster.
>
> La scalabilità automatica per i cluster LLAP (Interactive Query) e HBase è ancora in anteprima. La scalabilità automatica è disponibile solo nei cluster Spark, Hadoop, Interactive Query e HBase.

La funzionalità di scalabilità automatica del cluster di Azure HDInsight ridimensiona automaticamente il numero di nodi di lavoro in un cluster verso l'alto e verso il basso. Non è possibile ridimensionare altri tipi di nodi nel cluster.  Durante la creazione di un nuovo cluster HDInsight è possibile impostare un numero minimo e massimo di nodi del ruolo di lavoro. La scalabilità automatica monitora quindi i requisiti delle risorse del carico di analisi e aumenta il numero di nodi di lavoro verso l'alto o verso il basso. Non sono previsti costi aggiuntivi per questa funzione.

## <a name="cluster-compatibility"></a>Compatibilità dei cluster

Nella tabella seguente vengono descritti i tipi di cluster e le versioni compatibili con la funzionalità di scalabilità automatica.

| Versione | Spark | Hive | LLAP (LLAP) | hbase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 senza ESP | Sì | Sì | Sì | Sì* | No | No | No |
| HDInsight 4.0 senza ESP | Sì | Sì | Sì | Sì* | No | No | No |
| HDInsight 3.6 con ESP | Sì | Sì | Sì | Sì* | No | No | No |
| HDInsight 4.0 con ESP | Sì | Sì | Sì | Sì* | No | No | No |

\*I cluster HBase possono essere configurati solo per la scalabilità basata sulla pianificazione, non per il carico.

## <a name="how-it-works"></a>Funzionamento

È possibile scegliere la scalabilità basata sul carico o la scalabilità basata sulla pianificazione per il cluster HDInsight.You can choose load-based scaling or schedule-based scaling for your HDInsight cluster. La scalabilità basata sul carico modifica il numero di nodi nel cluster, all'interno di un intervallo impostato, per garantire un utilizzo ottimale della CPU e ridurre al minimo i costi di esecuzione.

La scalabilità basata sulla pianificazione modifica il numero di nodi nel cluster in base alle condizioni che hanno effetto in orari specifici. Queste condizioni scalano il cluster a un numero desiderato di nodi.

### <a name="metrics-monitoring"></a>Monitoraggio delle metriche

La funzionalità di scalabilità automatica monitora continuamente il cluster e raccoglie le metriche seguenti:

|Metrica|Descrizione|
|---|---|
|CPU totale in sospeso|numero totale di core necessari per avviare l'esecuzione di tutti i contenitori in sospeso.|
|Memoria totale in sospeso|memoria totale (in MB) necessaria per avviare l'esecuzione di tutti i contenitori in sospeso.|
|CPU libera totale|somma di tutti i core inutilizzati nei nodi del ruolo di lavoro attivi.|
|Memoria libera totale|somma della memoria inutilizzata (in MB) nei nodi del ruolo di lavoro attivi.|
|Memoria utilizzata per nodo|carico su un nodo del ruolo di lavoro. Un nodo del ruolo di lavoro in cui sono utilizzati 10 GB di memoria è considerato come sottoposto a un carico superiore rispetto a un nodo del ruolo di lavoro con 2 GB di memoria utilizzata.|
|Numero di master applicazioni per nodo|numero di contenitori di master applicazioni in esecuzione su un nodo del ruolo di lavoro. Un nodo di lavoro che ospita due contenitori AM è considerato più importante di un nodo di lavoro che ospita zero contenitori AM.|

Le metriche riportate sopra vengono controllate ogni 60 secondi. La scalabilità automatica prende decisioni di scalabilità verticale e verticale in base a queste metriche.

### <a name="load-based-scale-conditions"></a>Condizioni di scalabilità basate sul carico

Quando vengono rilevate le seguenti condizioni, la scalabilità automatica emetterà una richiesta di scalabilità:When the following conditions are detected, Autoscale will issue a scale request:

|Aumentare|Scalabilità verticale|
|---|---|
|La CPU totale in sospeso è maggiore della CPU libera totale per più di 3 minuti.|Il totale CPU in sospeso è minore del totale CPU disponibile per più di 10 minuti.|
|La memoria totale in attesa è maggiore della memoria disponibile totale per più di 3 minuti.|Il totale memoria in sospeso è minore del totale memoria disponibile per più di 10 minuti.|

Per la scalabilità verticale, il servizio HDInsight calcola il numero di nuovi nodi di lavoro necessari per soddisfare i requisiti correnti di CPU e memoria, quindi invia una richiesta di scalabilità verticale per aggiungere il numero di nodi richiesto.

Per la scalabilità ridotta, in base al numero di contenitori AM per nodo e ai requisiti correnti di CPU e memoria, la scalabilità automatica invia una richiesta di rimozione di un determinato numero di nodi. Il servizio rileva inoltre quali nodi sono candidati per la rimozione in base all'esecuzione del processo corrente. L'operazione di ridimensionamento verso il basso rimuove innanzitutto le autorizzazioni dei nodi e quindi li rimuove dal cluster.

## <a name="get-started"></a>Introduzione

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Creare un cluster con scalabilità automatica basata sul caricoCreate a cluster with load-based Autoscaling

Per abilitare la funzionalità di scalabilità automatica con scalabilità basata sul carico, completare i passaggi seguenti come parte del normale processo di creazione del cluster:

1. Nella scheda **Configurazione e prezzi** selezionare la casella di controllo Abilita **scalabilità automatica.**
1. Selezionare **Basato su carico** in **Tipo di scalabilità automatica**.
1. Immettere i valori desiderati per le seguenti proprietà:  

    * Numero iniziale **di nodi** per il **nodo di lavoro**.
    * **Numero massimo** di nodi di lavoro.
    * **Numero massimo** di nodi di lavoro.

    ![Abilitare la scalabilità automatica basata sul carico del nodo di lavoroEnable worker node-based autoscale](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

Il numero iniziale di nodi del ruolo di lavoro deve essere compreso tra il numero minimo e il numero massimo inclusi. Questo valore definisce la dimensione iniziale del cluster quando viene creato. Il numero minimo di nodi di lavoro deve essere impostato su tre o più. La scalabilità del cluster a meno di tre nodi può causare il blocco in modalità provvisoria a causa di una replica dei file insufficiente.  Per ulteriori informazioni, consultate [Blocco in modalità provvisoria.](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Creare un cluster con scalabilità automatica basata sulla pianificazioneCreate a cluster with schedule-based Autoscaling

Per abilitare la funzionalità di scalabilità automatica con scalabilità basata sulla pianificazione, completare i passaggi seguenti come parte del normale processo di creazione del cluster:

1. Nella scheda **Configurazione e prezzi** selezionare la casella di controllo Abilita **scalabilità automatica.**
1. Immettere il **numero di nodi** per **il nodo Worker**, che controlla il limite per la scalabilità verticale del cluster.
1. Selezionare l'opzione **Basata su pianificazione** in **Tipo di scalabilità automatica**.
1. Selezionare **Configura** per aprire la finestra di configurazione della **scalabilità automatica.**
1. Selezionare il fuso orario e quindi fare clic su **Aggiungi condizione**
1. Selezionare i giorni della settimana a cui applicare la nuova condizione.
1. Modificare il tempo di emodifica della condizione e il numero di nodi a cui deve essere ridimensionato il cluster.
1. Se necessario, aggiungere altre condizioni.

    ![Abilitare la creazione basata sulla pianificazione del nodo di lavoroEnable worker node schedule-based creation](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Il numero di nodi deve essere compreso tra 3 e il numero massimo di nodi di lavoro immessi prima di aggiungere condizioni.

### <a name="final-creation-steps"></a>Passaggi di creazione finale

Per la scalabilità basata sul carico e sulla pianificazione, selezionare il tipo di macchina virtuale per i nodi di lavoro selezionando una macchina virtuale dall'elenco a discesa in **Dimensioni nodo.** Dopo aver scelto il tipo di macchina virtuale per ogni tipo di nodo, è possibile visualizzare l'intervallo di costi stimato per l'intero cluster. Modificare i tipi di macchina virtuale in base al budget.

![Abilitare le dimensioni dei nodi di scalabilità automatica basati sulla pianificazione del nodo di lavoroEnable worker node schedule-based autoscale node size](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

La sottoscrizione in uso ha una quota di capacità per ogni area. Il numero totale di core dei nodi head combinati con il numero massimo di nodi di lavoro non può superare la quota di capacità. Questa quota tuttavia è un limite flessibile, in quanto è sempre possibile creare un ticket di supporto per aumentarla facilmente.

> [!Note]  
> Se si supera il limite di quota principale totale, verrà visualizzato un messaggio di errore che indica che il nodo massimo ha superato i core disponibili in questa area, scegliere un'altra area o contattare il supporto per aumentare la quota.'

Per altre informazioni sulla creazione del cluster HDInsight tramite il portale di Azure, vedere [Creare cluster basati su Linux in HDInsight tramite il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Creare un cluster con un modello di Resource Manager

#### <a name="load-based-autoscaling"></a>Scalabilità automatica basata sul caricoLoad-based autoscaling

È possibile creare un cluster HDInsight con la scalabilità automatica `autoscale` basata `computeProfile`  >  `workernode` sul carico di un modello di Azure Resource Manager, aggiungendo un nodo alla sezione con le proprietà `minInstanceCount` e `maxInstanceCount` come illustrato nel frammento json seguente.

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
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

È possibile creare un cluster HDInsight con la scalabilità automatica `autoscale` basata `computeProfile`  >  `workernode` sulla pianificazione di un modello di Azure Resource Manager, aggiungendo un nodo alla sezione. Il `autoscale` nodo `recurrence` contiene un `timezone` `schedule` che ha a e che descrive quando avrà luogo la modifica.

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

Per abilitare la scalabilità automatica in un cluster in esecuzione, selezionare **Dimensioni cluster** in **Impostazioni**. Selezionare quindi **Abilita scalabilità automatica**. Selezionare il tipo di scalabilità automatica desiderato e immettere le opzioni per la scalabilità basata sul carico o sulla pianificazione. Infine, selezionare **Salva**.

![Abilitare il cluster in esecuzione basato sulla pianificazione automatica del nodo di lavoroEnable worker node schedule-based autoscale running cluster](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>Utilizzo dell'API REST

Per abilitare o disabilitare la scalabilità automatica in un cluster in esecuzione usando l'API REST, effettuare una richiesta POST all'endpoint di scalabilità automatica, come illustrato nel frammento di codice seguente:To enable or disable Autoscale on a running cluster using the REST API, make a POST request to the Autoscale endpoint as shown in the code snippet below:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Usare i parametri appropriati nel payload della richiesta. Il payload json riportato di seguito potrebbe essere usato per abilitare la scalabilità automatica. Utilizzare il `{autoscale: null}` payload per disabilitare la scalabilità automatica.

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

Vedere la sezione precedente sull'abilitazione della [scalabilità automatica basata](#load-based-autoscaling) sul carico per una descrizione completa di tutti i parametri del payload.

## <a name="best-practices"></a>Procedure consigliate

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Scelta del ridimensionamento basato sul carico o sulla pianificazioneChoosing load-based or schedule-based scaling

Considerare i seguenti fattori prima di prendere una decisione su quale modalità scegliere:

* Abilitare la scalabilità automatica durante la creazione del cluster.
* Il numero minimo di nodi deve essere almeno tre.
* Varianza del carico: il carico del cluster segue un modello coerente in momenti specifici, in giorni specifici. In caso contrario, la programmazione basata sul carico è un'opzione migliore.
* Requisiti del contratti di servizio: il ridimensionamento con scalabilità automatica è reattivo anziché predittivo. Ci sarà un ritardo sufficiente tra l'inizio del carico e il momento in cui il cluster deve essere alle dimensioni di destinazione? Se sono presenti requisiti sLA rigorosi e il carico è un modello noto fisso, 'basato sulla pianificazione' è un'opzione migliore.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Considerare la latenza delle operazioni di scalabilità verticale o verticale

Il completamento di un'operazione di ridimensionamento può richiedere da 10 a 20 minuti. Quando si imposta una pianificazione personalizzata, pianificare il ritardo. Ad esempio, se è necessario che la dimensione del cluster sia 20 alle 9:00, impostare il trigger di pianificazione su un'ora precedente, ad esempio 8:30 AM in modo che l'operazione di ridimensionamento sia stata completata entro le 9:00 AM.

### <a name="preparation-for-scaling-down"></a>Preparazione per la riduzione delle dimensioni

Durante il processo di ridimensionamento del cluster, la scalabilità automatica rimuoverà i nodi in base alle dimensioni di destinazione. Se sono presenti attività in esecuzione su tali nodi, la scalabilità automatica attenderà il completamento delle attività. Poiché ogni nodo di lavoro svolge anche un ruolo in HDFS, i dati temporanei verranno spostati sui nodi rimanenti. Quindi è necessario assicurarsi che ci sia abbastanza spazio sui nodi rimanenti per ospitare tutti i dati temporanei.

I processi in esecuzione continueranno a essere eseguiti e terminati. I processi in sospeso attenderanno di essere programmati normalmente con un numero inferiore di nodi di lavoro disponibili.

### <a name="minimum-cluster-size"></a>Dimensione minima cluster

Non ridurre il cluster a meno di tre nodi. La scalabilità del cluster a meno di tre nodi può causare il blocco in modalità provvisoria a causa di una replica dei file insufficiente.  Per ulteriori informazioni, consultate [Blocco in modalità provvisoria.](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)

## <a name="monitoring"></a>Monitoraggio

### <a name="cluster-status"></a>Stato del cluster

Lo stato del cluster elencato nel portale di Azure consente di monitorare le attività di scalabilità automatica.

![Abilitare lo stato del cluster di scalabilità automatica basato sul carico del nodo di lavoroEnable worker node-based autoscale cluster status](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Tutti i messaggi di stato del cluster che potrebbero essere visualizzati sono illustrati nell'elenco seguente.

| Stato del cluster | Descrizione |
|---|---|
| In esecuzione | Il cluster funziona normalmente. Tutte le precedenti attività di scalabilità automatica sono state completate correttamente. |
| Updating  | È in corso l'aggiornamento della configurazione di scalabilità automatica del cluster.  |
| Configurazione di HDInsightHDInsight configuration  | È in corso un'operazione di scalabilità verticale o verticale del cluster.  |
| Errore di aggiornamento  | Problemi di HDInsight durante l'aggiornamento della configurazione di scalabilità automatica. I clienti possono scegliere di ritentare l'aggiornamento o disabilitare la scalabilità automatica.  |
| Errore  | Si è verificato un problema con il cluster e non è utilizzabile. Eliminare il cluster e crearne uno nuovo.  |

Per visualizzare il numero corrente di nodi nel cluster, passare al grafico **Dimensioni cluster** nella pagina **Panoramica** del cluster oppure selezionare **Dimensioni cluster** in **Impostazioni**.

### <a name="operation-history"></a>Storico delle operazioni

È possibile visualizzare la cronologia di scalabilità verticale e discesa del cluster come parte delle metriche del cluster. È inoltre possibile elencare tutte le azioni di ridimensionamento nell'ultimo giorno, settimana o altro periodo di tempo.

Selezionare **Metriche** in **Monitoraggio**. Selezionare **quindi Aggiungi metrica** e **Numero di lavoratori attivi** dalla casella di riepilogo a discesa **Metrica.** Selezionare il pulsante in alto a destra per modificare l'intervallo di tempo.

![Abilitare la metrica di scalabilità automatica basata sulla pianificazione del nodo di lavoroEnable worker node schedule-based autoscale metric](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>Passaggi successivi

Per le procedure consigliate relative al ridimensionamento manuale dei cluster, vedere [Ridimensionare i cluster HDInsight](hdinsight-scaling-best-practices.md)

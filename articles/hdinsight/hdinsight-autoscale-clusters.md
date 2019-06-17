---
title: Ridimensionare automaticamente i cluster HDInsight di Azure (anteprima)
description: Usare la funzionalità di scalabilità automatica di HDInsight per ridimensionare i cluster automaticamente
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: hrasheed
ms.openlocfilehash: 6ec981164de0ff61b0e83d54255d046a1418ed96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66000095"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Ridimensionare automaticamente i cluster HDInsight di Azure (anteprima)

> [!Important]
> La funzionalità scalabilità automatica funziona solo per i cluster Spark, Hive e MapReduce creati dopo maggio 2019 l'8. 

Funzionalità di scalabilità automatica di cluster di HDInsight di Azure si adatta automaticamente il numero di nodi di lavoro in un cluster su e giù. Altri tipi di nodi del cluster non possono essere scalate attualmente.  Durante la creazione di un nuovo cluster HDInsight è possibile impostare un numero minimo e massimo di nodi del ruolo di lavoro. Scalabilità automatica può quindi monitora i requisiti di risorse del carico di analitica e scala il numero di nodi di lavoro verso l'alto o verso il basso. Non sono previsti addebiti aggiuntivi per questa funzionalità.

## <a name="cluster-compatibility"></a>Compatibilità di cluster

Nella tabella seguente descrive i tipi di cluster e le versioni compatibili con la funzionalità di scalabilità automatica.

| Version | Spark | Hive | LLAP | hbase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 senza ESP | Yes | Sì | No | No | No | No | No |
| HDInsight 4.0 senza ESP | Yes | Sì | No | No | No | No | No |
| HDInsight 3.6 con ESP | Yes | Sì | No | No | No | No | No |
| HDInsight 3.6 con ESP | Yes | Sì | No | No | No | No | No |

## <a name="how-it-works"></a>Funzionamento

È possibile scegliere la scalabilità in base al carico o ridimensionamento per il cluster HDInsight basato su pianificazione. La scalabilità in base al carico viene modificato il numero di nodi nel cluster, all'interno di un intervallo che è impostata, per garantire un utilizzo ottima della CPU e ridurre i costi in esecuzione.

Modifiche di ridimensionamento basata sulla pianificazione il numero di nodi nel cluster basato sulle condizioni che vengono applicate in momenti specifici. Queste condizioni la scalabilità del cluster per il numero desiderato di nodi.

### <a name="metrics-monitoring"></a>Monitoraggio delle metriche

La funzionalità di scalabilità automatica monitora continuamente il cluster e raccoglie le metriche seguenti:

* **Total Pending CPU** (Totale CPU in sospeso): numero totale di core necessari per avviare l'esecuzione di tutti i contenitori in sospeso.
* **Total Pending Memory** (Totale memoria in sospeso): memoria totale (in MB) necessaria per avviare l'esecuzione di tutti i contenitori in sospeso.
* **Total Free CPU** (Totale CPU disponibile): somma di tutti i core inutilizzati nei nodi del ruolo di lavoro attivi.
* **Total Free Memory** (Totale memoria disponibile): somma della memoria inutilizzata (in MB) nei nodi del ruolo di lavoro attivi.
* **Used Memory per Node** (Memoria utilizzata per nodo): carico su un nodo del ruolo di lavoro. Un nodo del ruolo di lavoro in cui sono utilizzati 10 GB di memoria è considerato come sottoposto a un carico superiore rispetto a un nodo del ruolo di lavoro con 2 GB di memoria utilizzata.
* **Number of Application Masters per Node** (Numero di master applicazioni per nodo): numero di contenitori di master applicazioni in esecuzione su un nodo del ruolo di lavoro. Un nodo di lavoro che ospita due contenitori AM, viene considerata più importante di un nodo di lavoro che ospita i contenitori zero AM.

Le metriche riportate sopra vengono controllate ogni 60 secondi. Ridimensionamento automatico prende decisioni di ampliamento e riduzione delle prestazioni basate su queste metriche.

### <a name="load-based-cluster-scale-up"></a>Scalabilità verticale in base al carico del cluster

Quando vengono rilevate le condizioni seguenti, scalabilità automatica genererà una richiesta di scalabilità verticale:

* In attesa di CPU totale è maggiore di CPU disponibile totale per più di 3 minuti.
* Totale memoria in sospeso è maggiore di memoria totale disponibile per più di 3 minuti.

Il servizio HDInsight calcola quanti nuovi nodi di lavoro necessari per soddisfare i requisiti di memoria e CPU corrente e quindi invia una richiesta di scalabilità verticale per aggiungere il numero di nodi necessari.

### <a name="load-based-cluster-scale-down"></a>Cluster in base al carico di scalabilità orizzontale

Quando vengono rilevate le condizioni seguenti, scalabilità automatica genererà una richiesta di riduzione delle prestazioni:

* Il totale CPU in sospeso è minore del totale CPU disponibile per più di 10 minuti.
* Il totale memoria in sospeso è minore del totale memoria disponibile per più di 10 minuti.

In base al numero di contenitori AM per ogni nodo e i requisiti di memoria e CPU corrente, la scalabilità automatica esegue una richiesta di rimozione di un determinato numero di nodi. Il servizio rileva anche che i nodi sono candidati per la rimozione in base all'esecuzione di processi corrente. L'operazione di riduzione decommissions innanzitutto i nodi e quindi li rimuove dal cluster.

## <a name="get-started"></a>Attività iniziali

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Creare un cluster con la scalabilità automatica in base al carico

Per abilitare la funzionalità di scalabilità automatica con il ridimensionamento in base al carico, completare i passaggi seguenti come parte del processo di creazione cluster normale:

1. Selezionare **Custom (size, settings, apps)** (Impostazioni personalizzate (dimensione, impostazioni, app)) invece di **Quick create** (Creazione rapida).
1. Sul **Custom** passaggio 5 (**dimensioni del Cluster**), controllare il **scalabilità automatica di nodo di lavoro** casella di controllo.
1. Selezionare l'opzione **in base al carico** sotto **tipo di scalabilità automatica**.
1. Immettere i valori desiderati per le proprietà seguenti:  

    * Il numero iniziale di nodi del ruolo di lavoro (**Number of Worker nodes**).  
    * Il numero minimo (**Minimum**) di nodi del ruolo di lavoro.  
    * Il numero massimo (**Maximum**) di nodi del ruolo di lavoro.  

    ![Abilitare l'opzione di scalabilità automatica in base al carico di lavoro nodo](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

Il numero iniziale di nodi del ruolo di lavoro deve essere compreso tra il numero minimo e il numero massimo inclusi. Questo valore definisce le dimensioni iniziali del cluster al momento della creazione. Il numero minimo di nodi del ruolo di lavoro deve essere maggiore di zero.

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Creare un cluster con la scalabilità automatica basate su pianificazione

Per abilitare la funzionalità di scalabilità automatica con ridimensionamento basato su pianificazione, completare i passaggi seguenti come parte del processo di creazione cluster normale:

1. Selezionare **Custom (size, settings, apps)** (Impostazioni personalizzate (dimensione, impostazioni, app)) invece di **Quick create** (Creazione rapida).
1. Sul **Custom** passaggio 5 (**dimensioni del Cluster**), controllare il **scalabilità automatica di nodo di lavoro** casella di controllo.
1. Immettere il **nodi di numero di lavoro**, che controlla il limite per il ridimensionamento del cluster.
1. Selezionare l'opzione **basate su pianificazione** sotto **tipo di scalabilità automatica**.
1. Fare clic su **Configure** per aprire il **configurazione di scalabilità automatica** finestra.
1. Selezionare il fuso orario e quindi fare clic su **+ Aggiungi condizione**
1. Selezionare i giorni della settimana in cui dovrà essere applicata la nuova condizione.
1. Modificare l'ora in cui che la condizione dovrebbe richiedere effetto e il numero di nodi che il cluster deve essere ridimensionato.
1. Se necessario, aggiungere più condizioni.

    ![Abilitare l'opzione di scalabilità automatica basate su pianificazione nodo ruolo di lavoro](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Il numero di nodi deve essere compreso tra 1 e il numero di nodi di lavoro che è stato immesso prima dell'aggiunta di condizioni.

### <a name="final-creation-steps"></a>Passaggi per la creazione finale

Per la scalabilità in base al carico sia basata sulla pianificazione, selezionare il tipo di macchina virtuale per i nodi di lavoro facendo **le dimensioni del nodo ruolo di lavoro** e **dimensioni nodo Head**. Dopo aver scelto il tipo di macchina virtuale per ogni tipo di nodo, è possibile visualizzare l'intervallo di costo stimato per l'intero cluster. Modificare i tipi VM per adattare il tuo budget.

![Abilitare l'opzione di scalabilità automatica basate su pianificazione nodo ruolo di lavoro](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-node-size-selection.png)

La sottoscrizione in uso ha una quota di capacità per ogni area. Il numero totale di core dei nodi head combinato con il numero massimo di nodi del ruolo di lavoro non può superare la quota di capacità. Questa quota tuttavia è un limite flessibile, in quanto è sempre possibile creare un ticket di supporto per aumentarla facilmente.

> [!Note]  
> Se si supera il limite di quota di core totali, si riceverà un messaggio di errore indicante che 'la massima dei nodi ha superato i core disponibili in questa area, scegliere un'altra area o contattare il supporto per aumentare la quota.'

Per altre informazioni sulla creazione del cluster HDInsight tramite il portale di Azure, vedere [Creare cluster basati su Linux in HDInsight tramite il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Creare un cluster con un modello di Resource Manager

#### <a name="load-based-autoscaling"></a>Scalabilità automatica in base al carico

È possibile creare un cluster HDInsight con la scalabilità automatica in base al carico di un modello di Azure Resource Manager, tramite l'aggiunta di un `autoscale` nodo il `computeProfile`  >  `workernode` sezione con le proprietà `minInstanceCount` e `maxInstanceCount` come illustrato nel frammento di codice json seguente.

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

#### <a name="schedule-based-autoscaling"></a>Scalabilità automatica basate su pianificazione

È possibile creare un cluster HDInsight con la scalabilità automatica basata sulla pianificazione di un modello di Azure Resource Manager, tramite l'aggiunta di un `autoscale` nodo il `computeProfile`  >  `workernode` sezione. Il `autoscale` nodo contiene una `recurrence` con un `timezone` e `schedule` che descrive quando la modifica verrà eseguita.

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

Per abilitare la scalabilità automatica in un cluster in esecuzione, selezionare **dimensioni del Cluster** sotto **impostazioni**. Quindi fare clic su **abilitare la scalabilità automatica**. Selezionare il tipo di scalabilità automatica che si desidera e immettere le opzioni per la scalabilità in base al carico o basate su pianificazione. Infine, fare clic su **Salva**.

![Abilitare l'opzione di scalabilità automatica basate su pianificazione nodo ruolo di lavoro](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

## <a name="best-practices"></a>Procedure consigliate

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Scegliere la scalabilità in base al carico o basate su pianificazione

Prima di prendere una decisione sulla modalità da scegliere, prendere in considerazione i fattori seguenti:

* Caricare la varianza: il carico del cluster segue un modello coerente in momenti specifici, in giorni specifici. In caso contrario, la programmazione di carico basato su un'opzione migliore.
* Requisiti del contratto di servizio: Il ridimensionamento di scalabilità automatica è reattivo anziché predittiva. Vi sarà un ritardo sufficiente tra quando viene avviato il caricamento di aumento e quando il cluster deve essere con le dimensioni di destinazione? Se sono presenti requisiti rigorosi di contratto di servizio e il carico è uno schema fisso noto, 'schedule basato su' è un'opzione migliore.

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Prendere in considerazione la latenza di scala backup o ridurre le operazioni

Può richiedere da 10 a 20 minuti per completare un'operazione di ridimensionamento. Quando si configura una pianificazione personalizzata, pianificare questo ritardo. Ad esempio, se è necessaria la dimensione del cluster a 20 dalle 09:00, impostare il trigger di pianificazione a un momento precedente, ad esempio 8:30 in modo che l'operazione di ridimensionamento è stata completata per le 9:00.

### <a name="preparation-for-scaling-down"></a>Preparazione per la scalabilità verso il basso

Durante il processo di ridimensionamento del cluster, ridimensionamento automatico verrà ritirati i nodi in modo da soddisfare le dimensioni di destinazione. Se si eseguono operazioni su tali nodi, la scalabilità automatica attenderà fino a quando non vengono completate le attività. Poiché ogni nodo di lavoro viene inoltre utilizzato un ruolo in HDFS, i nodi rimanenti verranno spostati i dati temporanei. Pertanto, assicurarsi che lo spazio sia sufficiente nei nodi rimanenti per ospitare tutti i dati temporanei. 

I processi in esecuzione continuerà a eseguire e completare. I processi in sospeso verranno attesa di essere pianificato come di consueto con meno nodi di lavoro disponibili.

## <a name="monitoring"></a>Monitoraggio

### <a name="cluster-status"></a>Stato del cluster

Lo stato del cluster elencato nel portale di Azure può aiutarti a monitorare le attività di ridimensionamento automatico.

![Abilitare l'opzione di scalabilità automatica in base al carico di lavoro nodo](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Tutti i messaggi di stato del cluster che è possibile riscontrare sono illustrati nell'elenco seguente.

| Stato del cluster | Spiegazione |
|---|---|
| In esecuzione | Il cluster stia funzionando normalmente. Tutte le attività di ridimensionamento automatico precedente è stata completata correttamente. |
| L'aggiornamento  | La configurazione di scalabilità automatica del cluster viene aggiornata.  |
| Configurazione di HDInsight  | Un cluster di aumentare le prestazioni o operazione di riduzione è in corso.  |
| Errore di aggiornamento  | HDInsight ha rilevato problemi durante l'aggiornamento della configurazione di scalabilità automatica. I clienti possono scegliere di riprovare l'aggiornamento o disabilitare la scalabilità automatica.  |
| Tipi di errore  | Si è verificato un problema con il cluster e non è utilizzabile. Eliminare il cluster e crearne uno nuovo.  |

Per il numero corrente di nodi nel cluster, vedere il **dimensioni del Cluster** del grafico nel **Panoramica** pagina per il cluster, oppure fare clic su **dimensioni del Cluster** in  **Impostazioni**.

### <a name="operation-history"></a>Cronologia delle operazioni

È possibile visualizzare la cronologia di ampliamento e riduzione delle prestazioni del cluster come parte delle metriche del cluster. È anche possibile elencare tutte le azioni di ridimensionamento tramite il giorno precedente, settimana o altro periodo di tempo.

Selezionare **metriche** sotto **monitoraggio**. Quindi fare clic su **Aggiungi metrica** e **numero di thread di lavoro attivi** dal **metrica** casella a discesa. Fare clic sul pulsante in alto a destra per modificare l'intervallo di tempo.

![Abilitare l'opzione di scalabilità automatica basate su pianificazione nodo ruolo di lavoro](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)


## <a name="next-steps"></a>Passaggi successivi

* Per le procedure consigliate relative al ridimensionamento manuale dei cluster, vedere [Ridimensionare i cluster HDInsight](hdinsight-scaling-best-practices.md)

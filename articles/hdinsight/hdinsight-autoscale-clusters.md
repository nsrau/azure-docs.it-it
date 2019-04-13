---
title: Ridimensionare automaticamente i cluster HDInsight di Azure (anteprima)
description: Usare la funzionalità di scalabilità automatica di HDInsight per ridimensionare i cluster automaticamente
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: hrasheed
ms.openlocfilehash: 6642f80a40343546285770531ac42423bee779b8
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526490"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Ridimensionare automaticamente i cluster HDInsight di Azure (anteprima)

>[!Important]
>La funzionalità di HDInsight Autoscale è attualmente in anteprima. Inviare un messaggio di posta elettronica hdiautoscalepm@microsoft.com disporre di scalabilità automatica abilitata per la sottoscrizione.

La funzionalità di scalabilità automatica di Azure HDInsight aumenta o diminuisce automaticamente il numero di nodi del ruolo di lavoro in un cluster in base al carico in un intervallo predefinito. Durante la creazione di un nuovo cluster HDInsight è possibile impostare un numero minimo e massimo di nodi del ruolo di lavoro. La funzionalità di scalabilità automatica monitora quindi i requisiti in termini di risorse del carico di analisi e aumenta o diminuisce il numero di nodi del ruolo di lavoro di conseguenza. Per l'uso di questa funzionalità non sono previsti costi aggiuntivi.

## <a name="getting-started"></a>Introduzione

### <a name="create-a-cluster-with-the-azure-portal"></a>Creare un cluster dal portale di Azure

> [!Note]
> La funzionalità di scalabilità automatica è attualmente supportata solo per i cluster Hive, MapReduce e Spark di Azure HDInsight versione 3.6.

Per abilitare la funzionalità scalabilità automatica, eseguire le operazioni seguenti come parte del processo di creazione cluster normale:

1. Selezionare **Custom (size, settings, apps)** (Impostazioni personalizzate (dimensione, impostazioni, app)) invece di **Quick create** (Creazione rapida).
2. Nella **Impostazioni personalizzate** eseguire il passaggio 5 (**Cluster size**), dimensioni del cluster, e controllare la casella di controllo **Worker node autoscale** (Scalabilità automatica del nodo del ruolo di lavoro).
3. Immettere i valori desiderati per le proprietà seguenti:  

    * Il numero iniziale di nodi del ruolo di lavoro (**Number of Worker nodes**).  
    * Il numero minimo (**Minimum**) di nodi del ruolo di lavoro.  
    * Il numero massimo (**Maximum**) di nodi del ruolo di lavoro.  

![Abilitare l'opzione Scalabilità automatica del nodo del ruolo di lavoro](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

Il numero iniziale di nodi del ruolo di lavoro deve essere compreso tra il numero minimo e il numero massimo inclusi. Questo valore definisce le dimensioni iniziali del cluster al momento della creazione. Il numero minimo di nodi del ruolo di lavoro deve essere maggiore di zero.

Dopo aver scelto il tipo di macchina virtuale per ogni tipo di nodo, sarà possibile vedere l'intervallo di costo stimato per l'intero cluster. È quindi possibile modificare queste impostazioni in base al budget a disposizione.

La sottoscrizione in uso ha una quota di capacità per ogni area. Il numero totale di core dei nodi head combinato con il numero massimo di nodi del ruolo di lavoro non può superare la quota di capacità. Questa quota tuttavia è un limite flessibile, in quanto è sempre possibile creare un ticket di supporto per aumentarla facilmente.

> [!Note]  
> Se si supera il limite di quota core totale, si riceve un messaggio di errore che informa che il numero massimo di nodi è superiore ai core disponibili nell'area e invita a scegliere un'altra area o a contattare il supporto per aumentare la quota.

Per altre informazioni sulla creazione del cluster HDInsight tramite il portale di Azure, vedere [Creare cluster basati su Linux in HDInsight tramite il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Creare un cluster con un modello di Resource Manager

Per creare un cluster HDInsight con un modello Azure Resource Manager, aggiungere un `autoscale` nodo alla sezione `computeProfile` > `workernode` con le proprietà `minInstanceCount` e `maxInstanceCount` come illustrato nel frammento di codice json seguente.

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

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Abilitare e disabilitare la scalabilità automatica per un cluster in esecuzione

È possibile solo abilitare o disabilitare la scalabilità automatica per i nuovi cluster HDInsight.

## <a name="monitoring"></a>Monitoraggio

È possibile visualizzare la cronologia di ampliamento e riduzione delle prestazioni del cluster come parte delle metriche del cluster. È possibile anche elencare tutte le azioni di ridimensionamento del giorno o della settimana precedente o di un periodo di tempo più lungo.

## <a name="how-it-works"></a>Funzionamento

### <a name="metrics-monitoring"></a>Monitoraggio delle metriche

La funzionalità di scalabilità automatica monitora continuamente il cluster e raccoglie le metriche seguenti:

1. **Total Pending CPU** (Totale CPU in sospeso): numero totale di core necessari per avviare l'esecuzione di tutti i contenitori in sospeso.
2. **Total Pending Memory** (Totale memoria in sospeso): memoria totale (in MB) necessaria per avviare l'esecuzione di tutti i contenitori in sospeso.
3. **Total Free CPU** (Totale CPU disponibile): somma di tutti i core inutilizzati nei nodi del ruolo di lavoro attivi.
4. **Total Free Memory** (Totale memoria disponibile): somma della memoria inutilizzata (in MB) nei nodi del ruolo di lavoro attivi.
5. **Used Memory per Node** (Memoria utilizzata per nodo): carico su un nodo del ruolo di lavoro. Un nodo del ruolo di lavoro in cui sono utilizzati 10 GB di memoria è considerato come sottoposto a un carico superiore rispetto a un nodo del ruolo di lavoro con 2 GB di memoria utilizzata.
6. **Number of Application Masters per Node** (Numero di master applicazioni per nodo): numero di contenitori di master applicazioni in esecuzione su un nodo del ruolo di lavoro. Un nodo di lavoro che ospita due contenitori AM, viene considerata più importante di un nodo di lavoro che ospita i contenitori zero AM.

Le metriche riportate sopra vengono controllate ogni 60 secondi. Ridimensionamento automatico renderà le decisioni di ampliamento e riduzione delle prestazioni basate su queste metriche.

### <a name="cluster-scale-up"></a>Scalabilità verticale del cluster

Quando vengono rilevate le condizioni seguenti, scalabilità automatica genererà una richiesta di scalabilità verticale:

* In attesa di CPU totale è maggiore di CPU disponibile totale per più di 3 minuti.
* Totale memoria in sospeso è maggiore di memoria totale disponibile per più di 3 minuti.

Si calcolerà un determinato numero di nuovi nodi di lavoro necessarie per soddisfare i requisiti di CPU e memoria correnti e quindi inviare una richiesta di scalabilità verticale che aggiunge tale numero di nuovi nodi di lavoro.

### <a name="cluster-scale-down"></a>Riduzione delle prestazioni del cluster

Quando vengono rilevate le condizioni seguenti, scalabilità automatica genererà una richiesta di riduzione delle prestazioni:

* Il totale CPU in sospeso è minore del totale CPU disponibile per più di 10 minuti.
* Il totale memoria in sospeso è minore del totale memoria disponibile per più di 10 minuti.

In base al numero di contenitori AM per ogni nodo e i requisiti di memoria e CPU corrente, la scalabilità automatica genererà una richiesta per rimuovere un determinato numero di nodi, che specifica quali nodi sono candidati potenziali per la rimozione. La riduzione delle prestazioni attiverà rimozione delle autorizzazioni dei nodi e dopo che i nodi vengono completamente rimossi, verranno rimossi.

## <a name="next-steps"></a>Passaggi successivi

* Per le procedure consigliate relative al ridimensionamento manuale dei cluster, vedere [Ridimensionare i cluster HDInsight](hdinsight-scaling-best-practices.md)

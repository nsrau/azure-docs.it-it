---
title: Ridimensionare automaticamente i cluster Azure HDInsight
description: Usare la funzionalità di scalabilità automatica di HDInsight per ridimensionare i cluster automaticamente
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: hrasheed
ms.openlocfilehash: 043c83e2039d87b1650ba17f770ce16a2ad2c13d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811163"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Ridimensionare automaticamente i cluster Azure HDInsight

La funzionalità di scalabilità automatica di Azure HDInsight aumenta o diminuisce automaticamente il numero di nodi del ruolo di lavoro in un cluster in base al carico in un intervallo predefinito. Durante la creazione di un nuovo cluster HDInsight è possibile impostare un numero minimo e massimo di nodi del ruolo di lavoro. La funzionalità di scalabilità automatica monitora quindi i requisiti in termini di risorse del carico di analisi e aumenta o diminuisce il numero di nodi del ruolo di lavoro di conseguenza. Per l'uso di questa funzionalità non sono previsti costi aggiuntivi.

## <a name="getting-started"></a>Introduzione

### <a name="create-cluster-with-azure-portal"></a>Creare il cluster con il portale di Azure

> [!Note]
> La funzionalità di scalabilità automatica è attualmente supportata solo per i cluster Hive, MapReduce e Spark di Azure HDInsight versione 3.6.

Seguire i passaggi illustrati in [Creare cluster basati su Linux in HDInsight tramite il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md) e al passaggio 5, **Dimensioni del cluster**, selezionare **Scalabilità automatica del nodo del ruolo di lavoro (anteprima)** come illustrato di seguito. 

![Abilitare l'opzione Scalabilità automatica del nodo del ruolo di lavoro](./media/hdinsight-autoscale-clusters/worker-node-autoscale-option.png)

La selezione di questa opzione consente di specificare:

* Il numero iniziale di nodi del ruolo di lavoro
* Il numero minimo di nodi del ruolo di lavoro
* Il numero massimo di nodi del ruolo di lavoro

Il numero iniziale di nodi del ruolo di lavoro deve essere compreso tra il numero minimo e il numero massimo inclusi. Questo valore definisce le dimensioni iniziali del cluster al momento della creazione. Il numero minimo di nodi del ruolo di lavoro deve essere maggiore di zero.

Dopo aver scelto il tipo di macchina virtuale per ogni tipo di nodo, sarà possibile vedere l'intervallo di costo stimato per l'intero cluster. È quindi possibile modificare queste impostazioni in base al budget a disposizione.

La sottoscrizione in uso ha una quota di capacità per ogni area. Il numero totale di core dei nodi head combinato con il numero massimo di nodi del ruolo di lavoro non può superare la quota di capacità. Questa quota tuttavia è un limite flessibile, in quanto è sempre possibile creare un ticket di supporto per aumentarla facilmente.

> [!Note]
> Se si supera il limite di quota core totale, si riceve un messaggio di errore che informa che il numero massimo di nodi è superiore ai core disponibili nell'area e invita a scegliere un'altra area o a contattare il supporto per aumentare la quota.

### <a name="create-cluster-with-an-resource-manager-template"></a>Creare il cluster con un modello di Resource Manager

Quando si crea un cluster HDInsight con un modello di Resource Manager, è necessario aggiungere le impostazioni seguenti nella sezione "computeProfile" "workernode":

```json
{                            
    "name": "workernode",
    "targetInstanceCount": 4,
    "autoscale": {
        "minInstanceCount": 2,
        "maxInstanceCount": 10
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

### <a name="enable-and-disabling-autoscale-for-a-running-cluster"></a>Abilitare e disabilitare la scalabilità automatica per un cluster in esecuzione

L'abilitazione della scalabilità automatica per un cluster in esecuzione non è supportata in anteprima privata. Deve essere abilitata durante la creazione del cluster.

La disabilitazione della scalabilità automatica o la modifica delle sue impostazioni per un cluster in esecuzione non è supportata in anteprima privata. Per eliminare o modificare le impostazioni è necessario eliminare il cluster e crearne uno nuovo.

## <a name="monitoring"></a>Monitoraggio

È possibile visualizzare la cronologia degli aumenti e delle riduzioni delle dimensioni del cluster come parte delle metriche del cluster. È possibile elencare tutte le azioni di ridimensionamento del giorno o della settimana precedente o di un periodo di tempo più lungo.

## <a name="how-it-works"></a>Funzionamento

### <a name="metrics-monitoring"></a>Monitoraggio delle metriche

La funzionalità di scalabilità automatica monitora continuamente il cluster e raccoglie le metriche seguenti:

1. **Total Pending CPU** (Totale CPU in sospeso): numero totale di core necessari per avviare l'esecuzione di tutti i contenitori in sospeso.
2. **Total Pending Memory** (Totale memoria in sospeso): memoria totale (in MB) necessaria per avviare l'esecuzione di tutti i contenitori in sospeso.
3. **Total Free CPU** (Totale CPU disponibile): somma di tutti i core inutilizzati nei nodi del ruolo di lavoro attivi.
4. **Total Free Memory** (Totale memoria disponibile): somma della memoria inutilizzata (in MB) nei nodi del ruolo di lavoro attivi.
5. **Used Memory per Node** (Memoria utilizzata per nodo): carico su un nodo del ruolo di lavoro. Un nodo del ruolo di lavoro in cui sono utilizzati 10 GB di memoria è considerato come sottoposto a un carico superiore rispetto a un nodo del ruolo di lavoro con 2 GB di memoria utilizzata.
6. **Number of Application Masters per Node** (Numero di master applicazioni per nodo): numero di contenitori di master applicazioni in esecuzione su un nodo del ruolo di lavoro. Un nodo del ruolo di lavoro che ospita 2 contenitori di master applicazioni è considerato più importante di un nodo che non ne ospita alcuno.

Le metriche riportate sopra vengono controllate ogni 60 secondi. La funzionalità di scalabilità automatica si baserà su queste metriche per decidere se aumentare o diminuire le dimensioni.

### <a name="cluster-scale-up"></a>Aumento delle dimensioni del cluster

Quando vengono rilevate le condizioni seguenti, la funzionalità di scalabilità automatica invia una richiesta di aumento delle dimensioni:

* Il totale CPU in sospeso è maggiore del totale CPU disponibile per più di 1 minuto.
* Il totale memoria in sospeso è maggiore del totale memoria disponibile per più di 1 minuto.

Viene calcolato che sono necessari N nuovi nodi del ruolo di lavoro per soddisfare gli attuali requisiti di CPU e memoria e quindi viene inviata una richiesta di aumento delle dimensioni nella misura di N nuovi nodi del ruolo di lavoro.

### <a name="cluster-scale-down"></a>Riduzione delle dimensioni del cluster

Quando vengono rilevate le condizioni seguenti, la funzionalità di scalabilità automatica invia una richiesta di riduzione delle dimensioni:

* Il totale CPU in sospeso è minore del totale CPU disponibile per più di 10 minuti.
* Il totale memoria in sospeso è minore del totale memoria disponibile per più di 10 minuti.

In base al numero di contenitori di master applicazioni per nodo e agli attuali requisiti di CPU e memoria, la funzionalità di scalabilità automatica invia una richiesta di rimozione di N nodi, specificando quali sono i potenziali candidati per la rimozione. Per impostazione predefinita, vengono rimossi due nodi in un ciclo.

## <a name="next-steps"></a>Passaggi successivi

* Per le procedure consigliate relative al ridimensionamento manuale dei cluster, vedere [Ridimensionare i cluster HDInsight](hdinsight-scaling-best-practices.md)

---
title: Riavviare le macchine virtuali per il cluster Azure HDInsight
description: Informazioni su come riavviare le macchine virtuali che non rispondono per il cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: c0f0bd9eb423b3de6a602647dff93fd9fce6e13e
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077015"
---
# <a name="reboot-vms-for-hdinsight-cluster"></a>Riavviare le macchine virtuali per il cluster HDInsight

I cluster HDInsight contengono gruppi di macchine virtuali come nodi del cluster. Per i cluster con esecuzione prolungata, è possibile che questi nodi non rispondono per vari motivi. Questo articolo descrive come riavviare le macchine virtuali che non rispondono in un cluster HDInsight.

## <a name="when-to-reboot"></a>Quando riavviare

> [!WARNING]  
> Il riavvio delle macchine virtuali nel cluster comporta un tempo di inattività del nodo e il riavvio dei servizi nel nodo. 

Durante il riavvio del nodo, il cluster potrebbe non essere integro, i processi potrebbero rallentare o non riuscire. Se si sta tentando di riavviare il nodo head attivo, verranno terminati tutti i processi in esecuzione e non sarà possibile inviare processi al cluster finché i servizi non saranno più operativi. È consigliabile riavviare le macchine virtuali solo quando necessario. Ecco alcune indicazioni su quando prendere in considerazione il riavvio delle macchine virtuali.

- Non è possibile eseguire SSH nel nodo, ma risponde ai ping.
- Il nodo del ruolo di lavoro è inattivo senza heartbeat nell'interfaccia utente di Ambari.
- Il disco temporaneo è pieno nel nodo.
- La tabella di processo nella macchina virtuale include molte voci in cui il processo è stato completato, ma è elencato con "stato terminato".

> [!WARNING]  
> Quando si riavviano le macchine virtuali per **HBase** e **Kafka** clustes, è opportuno prestare maggiore attenzione, perché potrebbe causare la perdita di dati.

## <a name="use-powershell-to-reboot-vms"></a>Usare PowerShell per riavviare le macchine virtuali

Per usare l'operazione di riavvio del nodo sono necessari due passaggi: elencare i nodi e riavviare i nodi.

1. Elencare i nodi. È possibile ottenere l'elenco dei nodi del cluster tramite [Get-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsighthost). 

  ```
  Get-AzHDInsightHost -ClusterName myclustername
  ```

2. Riavviare gli host. Dopo aver ricevuto i nomi dei nodi che si vuole riavviare, riavviare i nodi usando [Restart-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost).

  ```
  Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
  ```

## <a name="use-rest-api-to-reboot-vms"></a>Usare l'API REST per riavviare le macchine virtuali

È possibile usare la funzionalità **prova** nel documento API per inviare richieste a HDInsight. Per usare l'operazione di riavvio del nodo sono necessari due passaggi: elencare i nodi e riavviare i nodi.

1. Elencare i nodi. È possibile ottenere l'elenco dei nodi del cluster dall'API REST o in Ambari. Per altri dettagli, vedere l' [elenco HDInsight host operazione API REST](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

2. Riavviare gli host. Dopo aver ricevuto i nomi dei nodi che si vuole riavviare, usare l'API REST di restart Nodes per riavviare i nodi. Il nome del nodo segue il modello di **"NodeType (/HN/ZK/GW)" + "x" + "First 6 characters of cluster Name"**. Per ulteriori informazioni, vedere l' [operazione sull'API REST di HDInsight restart hosts](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

I nomi effettivi dei nodi che si desidera riavviare sono specificati in una matrice JSON nel corpo della richiesta.

```json
[
  "wn0-abcdef",
  "zk1-abcdef"
]
```

## <a name="next-steps"></a>Passaggi successivi

* [Riavvio-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [API REST di macchine virtuali HDInsight](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines)
* [API REST HDInsight](https://docs.microsoft.com/rest/api/hdinsight/)

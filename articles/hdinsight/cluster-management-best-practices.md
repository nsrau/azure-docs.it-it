---
title: Cluster management best practices - Azure HDInsight
description: Informazioni sulle procedure consigliate per la gestione dei cluster HDInsight.Learn best practices for managing HDInsight clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 3010c1d597907e7d5c7f82c8b42721dc1f934f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74782021"
---
# <a name="hdinsight-cluster-management-best-practices"></a>Procedure consigliate per la gestione dei cluster HDInsightHDInsight cluster management best practices

Informazioni sulle procedure consigliate per la gestione dei cluster HDInsight.Learn best practices for managing HDInsight clusters.

## <a name="how-do-i-create-hdinsight-clusters"></a>Come si creano cluster HDInsight?

| Opzione | Documenti |
|---|---|
| Data factory di Azure | [Creare cluster Apache Hadoop on demand in HDInsight con Azure Data Factory](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| Modello di Gestione risorse personalizzato | [Creare cluster Apache Hadoop in HDInsight mediante modelli di Resource Manager](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| Modelli di Guida introduttiva | [Modelli guida introduttiva di HDInsightHDInsight Quickstart templates](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| Esempi di AzureAzure samples | [Esempi di Azure HDInsightHDInsight Azure samples](https://docs.microsoft.com/samples/browse/?products=azure-hdinsight) |
| Portale di Azure | [Creare cluster basati su Linux in HDInsight tramite il portale di Azure](./spark/apache-spark-intellij-tool-plugin.md) |
| Interfaccia della riga di comando di Azure | [Creare cluster HDInsight tramite l'interfaccia della riga di comando di Azure](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Creare cluster basati su Linux in HDInsight tramite Azure PowerShell](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Creare i cluster Apache Hadoop tramite l'API REST di Azure](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| SDK (.NET, Python, Java) | [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet), [Python](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python), [Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable), [Go](https://docs.microsoft.com/azure/hdinsight/hdinsight-go-sdk-overview) |

> [!Note]
> Se si crea un cluster e si riutilizza il nome del cluster da un cluster creato in precedenza, attendere il completamento dell'eliminazione del cluster precedente prima di creare il cluster.

## <a name="how-do-i-customize-hdinsight-clusters"></a>Come si personalizzano i cluster HDInsight?

| Opzione | Documenti |
|---|---|
| Azioni script | [Personalizzare i cluster di Azure HDInsight usando le azioni scriptCustomize Azure HDInsight clusters by using script actions](./hdinsight-hadoop-customize-cluster-linux.md) |
| Bootstrap | [Personalizzare cluster HDInsight tramite Bootstrap](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| Metaarchivi esterni | [Usare gli archivi di metadati esterni in Azure HDInsight](./hdinsight-use-external-metadata-stores.md) |
| Database Ambari personalizzato | [Configurare i cluster HDInsight con un database Ambari personalizzatoSet up HDInsight clusters with a custom Ambari DB](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>Quali sono alcuni errori che possono verificarsi durante la creazione di cluster?

| Errore | Ulteriori informazioni |
|---|---|
| Nessuna quota | Esistono quote per il numero di quote che è possibile creare nella sottoscrizione in ogni area. Per ulteriori informazioni, vedere [Pianificazione della capacità: quote](./hdinsight-capacity-planning.md). |
| Non sono più disponibili indirizzi IP | Ogni rete virtuale ha un numero limitato di indirizzi IP. Quando si crea un cluster HDInsight, ogni nodo (inclusi i nodi zookeeper e gateway) usa alcuni di questi indirizzi IP assegnati. Quando tutti gli indirizzi IP sono in uso, si verificherà questo errore.  |
| Le regole del gruppo di sicurezza di rete non consentono la comunicazione con i provider di risorse HDInsightNetwork security group (NSG) rules don't allow communication with HDInsight resource providers | Se si usano gruppi di sicurezza di rete o route definite dall'utente (UDR) per controllare il traffico in ingresso nel cluster HDInsight, è necessario assicurarsi che il cluster possa comunicare con i servizi critici di integrità e gestione di Azure.If you use NSGs or user-defined routes (UDR) to control inbound traffic to your HDInsight cluster, you must ensure that your cluster can communicate with critical Azure health and management services. Per altre informazioni, vedere Tag del servizio gruppo di sicurezza di rete per Azure HDInsightFor more information, see [Network security group (NSG) service tags for Azure HDInsight](./hdinsight-service-tags.md) |
| Riutilizzo del nome del cluster | Quando si utilizza un nome di cluster usato in precedenza, è necessario attendere X numero di minuti prima di ricreare il cluster. In caso contrario, verrà visualizzato un messaggio che indica che la risorsa esiste già. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>Come si gestisce l'esecuzione di cluster HDInsight?

| Opzione | Documenti |
|---|---|
| Autoscale | [Ridimensionare automaticamente i cluster Azure HDInsight](./hdinsight-autoscale-clusters.md) |
| Ridimensionamento manuale | [Ridimensionare i cluster di Azure HDInsightScale Azure HDInsight clusters](./hdinsight-scaling-best-practices.md) |
| Monitoraggio con Ambari| [Monitorare le prestazioni del cluster in Azure HDInsightMonitor cluster performance in Azure HDInsight](./hdinsight-key-scenarios-to-monitor.md) |
| Monitoraggio con i log di Monitoraggio di AzureMonitoring with Azure Monitor logs | [Usare log di Monitoraggio di Azure per monitorare i cluster di HDInsight](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |

## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>Come si controllano i cluster HDInsight eliminati?

### <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure

È possibile usare la query seguente con i log di Monitoraggio di Azure per monitorare i cluster eliminati.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>Passaggi successivi

* [Pianificazione della capacità per cluster HDInsight](./hdinsight-capacity-planning.md)
* [Quali sono le configurazioni di nodi predefinite e consigliate per Azure HDInsight?](./hdinsight-supported-node-configuration.md)
---
title: Scalabilità elastica con l'API Cassandra in Azure Cosmos DB
description: Informazioni sulle opzioni disponibili per la scalabilità di un account dell'API Cassandra di Azure Cosmos DB e i relativi vantaggi e svantaggi
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: thvankra
ms.openlocfilehash: d6518767b0148828280071188c086e396401a6fc
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92277687"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Scalabilità elastica di un account dell'API Cassandra di Azure Cosmos DB

Sono disponibili diverse opzioni per esplorare la natura elastica dell'API di Azure Cosmos DB per Cassandra. Per comprendere come scalare in modo efficace in Azure Cosmos DB, è importante comprendere come effettuare il provisioning della quantità corretta di unità richiesta (UR/sec) per tenere conto delle esigenze di prestazioni del sistema. Per altre informazioni sulle unità richiesta, vedere l'articolo [Unità richiesta](request-units.md). 

Per l'API Cassandra, è possibile recuperare l'addebito delle unità richiesta per le singole query usando gli [SDK .NET e Java](https://docs.microsoft.com/azure/cosmos-db/find-request-unit-charge#cassandra-api). Questa operazione è utile per determinare la quantità di UR/s di cui è necessario eseguire il provisioning nel servizio.

:::image type="content" source="./media/request-units/request-units.png" alt-text="Utilizzo delle unità richiesta da parte delle operazioni di database" border="false":::

## <a name="handling-rate-limiting-429-errors"></a>Gestione della limitazione della frequenza (429 errori)

Azure Cosmos DB restituirà errori di limitazione della frequenza (429) se i client utilizzano più risorse (UR/s) rispetto al valore di cui è stato effettuato il provisioning. L'API Cassandra di Azure Cosmos DB converte queste eccezioni in errori di overload per il protocollo nativo Cassandra. 

Se il sistema non è sensibile alla latenza, potrebbe essere sufficiente gestire la limitazione della velocità effettiva usando i tentativi. Vedere l'[esempio di codice Java](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) per informazioni su come gestire in modo trasparente la limitazione della frequenza usando l'[estensione Azure Cosmos DB](https://github.com/Azure/azure-cosmos-cassandra-extensions) per i [criteri di ripetizione di Cassandra](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/) in Java. È anche possibile usare l'[estensione Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) per gestire la limitazione della frequenza.

## <a name="manage-scaling"></a>Gestire la scalabilità

Se è necessario ridurre al minimo la latenza, esiste una gamma di opzioni per la gestione della scalabilità e il provisioning della velocità effettiva (UR) nell'API Cassandra:

* [Manualmente usando il portale di Azure](#use-azure-portal)
* [A livello di codice usando le funzionalità del piano di controllo](#use-control-plane)
* [A livello di codice usando i comandi CQL con un SDK specifico](#use-cql-queries)
* [Dinamicamente usando la scalabilità automatica](#use-autoscale)

Nelle sezioni seguenti vengono descritti i vantaggi e gli svantaggi di ogni approccio. È quindi possibile scegliere la strategia migliore per bilanciare le esigenze di scalabilità del sistema, il costo complessivo e le esigenze di efficienza per la propria soluzione.

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>Usare il portale di Azure

È possibile dimensionare le risorse nell'account dell'API Cassandra di Azure Cosmos DB usando il portale di Azure. Per altre informazioni, vedere l'articolo sulla [provisioning della velocità effettiva nei contenitori e nei database](set-throughput.md). In questo articolo vengono illustrati i vantaggi relativi all'impostazione della velocità effettiva a livello di [database](set-throughput.md#set-throughput-on-a-database) o [contenitore](set-throughput.md#set-throughput-on-a-container) nel portale di Azure. I termini "database" e "contenitore" indicati in questi articoli vengono mappati rispettivamente a "keyspace" e "tabella" per l'API Cassandra.

Il vantaggio di questo metodo è che si tratta di un modo semplice per gestire la capacità della velocità effettiva nel database. Tuttavia, lo svantaggio è che, in molti casi, l'approccio alla scalabilità può richiedere che determinati livelli di automazione siano sia economici che ad alte prestazioni. Le sezioni successive illustrano gli scenari e i metodi pertinenti.

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>Usare il piano di controllo

L'API di Azure Cosmos DB per Cassandra consente di regolare la velocità effettiva a livello di codice usando le varie funzionalità del piano di controllo. Per informazioni ed esempi, vedere gli articoli [Azure Resource Manager](manage-cassandra-with-resource-manager.md), [PowerShell](powershell-samples.md) e [Interfaccia della riga di comando di Azure](cli-samples.md).

Il vantaggio di questo metodo è che è possibile automatizzare l'aumento o la riduzione delle risorse in base a un timer per tenere conto di picchi di attività o periodi di minore attività. Vedere l'esempio [qui](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) per informazioni su ottenere questo risultato usando Funzioni di Azure e PowerShell.

Uno svantaggio di questo approccio può essere dovuto al fatto che non è possibile rispondere a esigenze di scalabilità mutevoli non prevedibili in tempo reale. Al contrario, potrebbe essere necessario sfruttare il contesto dell'applicazione nel sistema, a livello di client/SDK, o usando la [scalabilità automatica](provision-throughput-autoscale.md).

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>Usare query CQL con un SDK specifico

È possibile dimensionare il sistema in modo dinamico con il codice eseguendo i [comandi CQL ALTER](cassandra-support.md#keyspace-and-table-options) per il database o il contenitore specificato.

Il vantaggio di questo approccio è che consente di rispondere in modo dinamico alle esigenze di scalabilità e in modo personalizzato per l'applicazione. Con questo approccio è comunque possibile sfruttare i costi e le tariffe standard di UR/s. Se le esigenze di scalabilità del sistema sono prevedibili (circa il 70% o più), l'uso dell'SDK con CQL può essere un metodo più conveniente per il dimensionamento automatico rispetto all'uso della scalabilità automatica. Lo svantaggio di questo approccio è che può essere piuttosto complesso implementare nuovi tentativi mentre la limitazione della frequenza può aumentare la latenza.

## <a name="use-autoscale-provisioned-throughput"></a><a id="use-autoscale"></a>Usare la velocità effettiva con provisioning a scalabilità automatica

Oltre alla modalità standard (manuale) o a livello di codice per il provisioning della velocità effettiva, è anche possibile configurare i contenitori di Azure Cosmos nella velocità effettiva con provisioning a scalabilità automatica. La scalabilità automatica verrà automaticamente dimensionata in base alle esigenze di consumo entro gli intervalli di UR specificati senza compromettere i contratti di servizio. Per altre informazioni, vedere l'articolo [Creare contenitori e database di Azure Cosmos in scalabilità automatica](provision-throughput-autoscale.md).

Il vantaggio di questo approccio è che è il modo più semplice per gestire le esigenze di scalabilità nel sistema. Non verrà applicata **la limitazione della frequenza negli intervalli di ur configurati**. Lo svantaggio è che, se le esigenze di dimensionamento nel sistema sono prevedibili, la scalabilità automatica può essere un modo meno conveniente per gestire le esigenze di scalabilità rispetto all'uso del piano di controllo o degli approcci a livello di SDK indicati in precedenza.

Per impostare o modificare la velocità effettiva massima (UR) per la scalabilità automatica usando CQL, usare il comando seguente (sostituendo il nome keyspace/tabella di conseguenza):

```Bash
# to set max throughput (RUs) for autoscale at keyspace level:
create keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at keyspace level:
alter keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=4000;

# to set max throughput (RUs) for autoscale at table level:
create table <keyspace name>.<table name> (pk int PRIMARY KEY, ck int) WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at table level:
alter table <keyspace name>.<table name> WITH cosmosdb_autoscale_max_throughput=4000;
```

## <a name="next-steps"></a>Passaggi successivi

- Introduzione alla [creazione di un account, database e una tabella API Cassandra](create-cassandra-api-account-java.md) usando un'applicazione Java

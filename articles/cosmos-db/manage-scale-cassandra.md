---
title: Scala elastica con l'API Cassandra in Azure Cosmos DBElastically scalewith with Cassandra API in Azure Cosmos DB
description: Informazioni sulle opzioni disponibili per la scalabilità di un account API Cassandra di Azure Cosmos DB e sui relativi vantaggi/svantaggi
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 10d81de48c0d8f56c7c3fd26e3fd82a8c3df84c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474680"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Scala elasticamente un account API Cassandra db di Azure

Sono disponibili diverse opzioni per esplorare la natura elastica dell'API Azure Cosmos DB per Cassandra.There are a variety of options to explore the elastic nature of the Azure Cosmos DB API for Cassandra. Per comprendere come scalare in modo efficace in Azure Cosmos DB, è importante comprendere come eseguire il provisioning della giusta quantità di unità richiesta (RU/s) per tenere conto delle richieste di prestazioni nel sistema. Per altre informazioni sulle unità richiesta, vedere l'articolo [Unità](request-units.md) richiesta. 

Per l'API Cassandra, è possibile recuperare l'addebito dell'unità di richiesta per singole query utilizzando [.NET e SDK Java](https://docs.microsoft.com/azure/cosmos-db/find-request-unit-charge#cassandra-api). Ciò è utile per determinare la quantità di RU/s che è necessario eseguire il provisioning nel servizio.

![Utilizzo delle unità richiesta da parte delle operazioni di database](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>Limitazione della velocità di gestione (429 errori)

Azure Cosmos DB restituirà errori limitati dalla frequenza (429) se i client utilizzano più risorse (RU/s) rispetto alla quantità di cui è stato eseguito il provisioning. L'API Cassandra di Azure Cosmos DB converte queste eccezioni in errori di overload per il protocollo nativo Cassandra. 

Se il sistema non è sensibile alla latenza, potrebbe essere sufficiente gestire la velocità effettiva limitando la velocità effettiva utilizzando i tentativi. Vedere [l'esempio](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) di codice Java per informazioni su come gestire la limitazione della frequenza in modo trasparente usando l'estensione Azure Cosmos DB per i criteri di ripetizione dei tentativi di Cassandra in Java.See the Java code sample for how to handle rate limiting limiting transparently by using the [Azure Cosmos DB extension](https://github.com/Azure/azure-cosmos-cassandra-extensions) for [Cassandra retry policy](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/) in Java. È inoltre possibile utilizzare [l'estensione Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) per gestire la limitazione delle frequenze.

## <a name="manage-scaling"></a>Gestire il ridimensionamento

Se è necessario ridurre al minimo la latenza, è disponibile una gamma di opzioni per la gestione della velocità effettiva di scalabilità e provisioning (RU) nell'API Cassandra:If you need to minimize latency, there is a spectrum of options for managing scale and provisioning throughput (RUs) in the Cassandra API:

* [Manualmente tramite il portale di AzureManually by using the Azure portal](#use-azure-portal)
* [A livello di codice tramite le funzionalità del piano di controlloProgrammatically using the control plane features](#use-control-plane)
* [A livello di codice tramite comandi CQL con un SDK specificoProgrammatically by using CQL commands with a specific SDK](#use-cql-queries)
* [Dinamicamente utilizzando Autopilot](#use-autopilot)

Nelle sezioni seguenti vengono illustrati i vantaggi e gli svantaggi di ogni approccio. È quindi possibile decidere la strategia migliore per bilanciare le esigenze di scalabilità del sistema, i costi complessivi e le esigenze di efficienza per la soluzione.

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>Usare il portale di AzureUse the Azure portal

È possibile ridimensionare le risorse nell'account API Cassandra di Azure Cosmos DB usando il portale di Azure.You can scale the resources in Azure Cosmos DB Cassandra API account by using Azure portal. Per altre informazioni, vedere l'articolo Provisioning della [velocità effettiva in contenitori e database](set-throughput.md). Questo articolo illustra i vantaggi relativi dell'impostazione della velocità effettiva a livello di database o [contenitore](set-throughput.md#set-throughput-on-a-container) nel portale di Azure.This article explains the relative benefits of setting throughput at either [database](set-throughput.md#set-throughput-on-a-database) or container level in the Azure portal. I termini "database" e "contenitore" menzionati in questi articoli vengono mappati rispettivamente a "keyspace" e "table" per l'API Cassandra.

Il vantaggio di questo metodo è che si tratta di un modo semplice per gestire la capacità di throughput nel database. Tuttavia, lo svantaggio è che in molti casi, l'approccio al ridimensionamento può richiedere determinati livelli di automazione per essere sia conveniente che ad alte prestazioni. Nelle sezioni successive vengono illustrati gli scenari e i metodi pertinenti.

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>Utilizzare il piano di controllo

L'API di Azure Cosmos DB per Cassandra offre la possibilità di regolare la velocità effettiva a livello di codice usando le varie funzionalità del piano di controllo. Per istruzioni ed esempi, vedere gli articoli di [Azure Resource Manager,](manage-cassandra-with-resource-manager.md)Powershell e [l'interfaccia della riga di comando](cli-samples-cassandra.md) di Azure.See the Azure Resource Manager , [Powershell,](powershell-samples-cassandra.md)and Azure CLI articles for guidance and samples.

Il vantaggio di questo metodo è che è possibile automatizzare la scalabilità verticale o verso il basso delle risorse in base a un timer per tenere conto dell'attività di picco o periodi di bassa attività. Dai un'occhiata al nostro esempio qui per come eseguire questa operazione usando Funzioni di Azure e Powershell.Take a look at our sample [here](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) for how to accomplish this using Azure Functions and Powershell.

Uno svantaggio di questo approccio può essere che non è possibile rispondere a esigenze di scala in evoluzione imprevedibili in tempo reale. Al contrario, potrebbe essere necessario sfruttare il contesto dell'applicazione nel sistema, a livello di client/SDK o utilizzando [Autopilot](provision-throughput-autopilot.md).

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>Usare le query CQL con un SDK specificoUse CQL queries with a specific SDK

È possibile ridimensionare il sistema in modo dinamico con il codice eseguendo i [comandi ALTER CQL](cassandra-support.md#keyspace-and-table-options) per il database o il contenitore specificato.

Il vantaggio di questo approccio è che consente di rispondere alle esigenze di scalabilità in modo dinamico e personalizzato in base alle esigenze dell'applicazione. Con questo approccio, è comunque possibile sfruttare gli oneri e le tariffe standard RU/s. Se le esigenze di scalabilità del sistema sono per lo più prevedibili (circa il 70% o più), l'utilizzo di SDK con CQL può essere un metodo di scalabilità automatica più conveniente rispetto all'utilizzo di Autopilot. Lo svantaggio di questo approccio è che può essere piuttosto complesso implementare nuovi tentativi, mentre la limitazione della frequenza può aumentare la latenza.

## <a name="use-autopilot"></a><a id="use-autopilot"></a>Usa pilota automatico

Oltre alla velocità effettiva manuale o a livello di codice per il provisioning, è anche possibile configurare i contenitori cosmo di Azure in modalità pilota automatico. La modalità pilota automatico verrà ridimensionata automaticamente e istantaneamente in base alle vostre esigenze di consumo entro intervalli RU specificati senza compromettere i contratti di sicurezza. Per altre informazioni, vedere l'articolo [Creare contenitori e database di Azure Cosmos in modalità pilota automatico.](provision-throughput-autopilot.md)

Il vantaggio di questo approccio è che è il modo più semplice per gestire le esigenze di scalabilità nel sistema. Garantisce di non applicare la limitazione delle tariffe **all'interno degli intervalli RU configurati.** Lo svantaggio è che, se le esigenze di ridimensionamento nel sistema sono prevedibili, Autopilot può essere un modo meno conveniente per gestire le esigenze di scalabilità rispetto all'utilizzo degli approcci a livello di SDK o piano di controllo su misura menzionati in precedenza.

## <a name="next-steps"></a>Passaggi successivi

- Introduzione alla creazione di [un account API Cassandra,](create-cassandra-api-account-java.md) un database e una tabella tramite un'applicazione Java

---
title: Scalabilità elastica con API Cassandra in Azure Cosmos DB
description: Informazioni sulle opzioni disponibili per la scalabilità di un account di API Cassandra Azure Cosmos DB e i relativi vantaggi e svantaggi
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: e2967a6d12fba2d81dad9de31e7476a027a39d1c
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77468831"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Ridimensionare in modo elastico un account di API Cassandra Azure Cosmos DB

Sono disponibili diverse opzioni per esplorare la natura elastica dell'API Azure Cosmos DB per Cassandra. Per comprendere come applicare la scalabilità in modo efficace in Azure Cosmos DB, è importante comprendere come effettuare il provisioning della quantità corretta di unità richiesta (UR/sec) per tenere conto delle richieste di prestazioni nel sistema. Per altre informazioni sulle unità richiesta, vedere l'articolo relativo alle [unità richiesta](request-units.md) . 

![Utilizzo delle unità richiesta da parte delle operazioni di database](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>Gestione della limitazione della frequenza (429 errori)

Azure Cosmos DB restituirà errori con limitazione della frequenza (429) se i client utilizzano più risorse (UR/sec) rispetto al valore di cui è stato effettuato il provisioning. L'API Cassandra di Azure Cosmos DB converte queste eccezioni in errori di overload per il protocollo nativo Cassandra. 

Se il sistema non è sensibile alla latenza, potrebbe essere sufficiente gestire la velocità effettiva, limitando l'utilizzo di tentativi. Vedere l' [esempio di codice Java](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) per informazioni su come gestire la limitazione della frequenza in modo trasparente usando l' [estensione Azure Cosmos DB](https://github.com/Azure/azure-cosmos-cassandra-extensions) per i [criteri di ripetizione dei tentativi di Cassandra](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/) in Java. È anche possibile usare l' [estensione Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) per gestire la limitazione della frequenza.

## <a name="manage-scaling"></a>Gestisci scalabilità

Se è necessario ridurre al minimo la latenza, esiste una gamma di opzioni per la gestione della scalabilità e della velocità effettiva del provisioning (UR) nel API Cassandra:

* [Manualmente usando il portale di Azure](#use-azure-portal)
* [A livello di codice tramite le funzionalità del piano di controllo](#use-control-plane)
* [A livello di codice usando i comandi CQL con un SDK specifico](#use-cql-queries)
* [In modo dinamico tramite Autopilot](#use-autopilot)

Le sezioni seguenti illustrano i vantaggi e gli svantaggi di ogni approccio. È quindi possibile scegliere la strategia migliore per bilanciare le esigenze di scalabilità del sistema, il costo complessivo e le esigenze di efficienza per la soluzione.

## <a id="use-azure-portal"></a>Usare il portale di Azure

È possibile ridimensionare le risorse in Azure Cosmos DB account API Cassandra utilizzando portale di Azure. Per altre informazioni, vedere l'articolo relativo al [provisioning della velocità effettiva nei contenitori e nei database](set-throughput.md). In questo articolo vengono illustrati i vantaggi relativi all'impostazione della velocità effettiva a livello di [database](set-throughput.md#set-throughput-on-a-database) o di [contenitore](set-throughput.md#set-throughput-on-a-container) nell'portale di Azure. I termini "database" e "container" indicati in questi articoli vengono mappati rispettivamente a "spazio dei dati" e "tabella" per la API Cassandra.

Il vantaggio di questo metodo è che si tratta di un modo semplice per gestire la capacità di velocità effettiva nel database. Tuttavia, lo svantaggio è che, in molti casi, l'approccio alla scalabilità potrebbe richiedere che determinati livelli di automazione siano sia convenienti che a prestazioni elevate. Le sezioni successive illustrano gli scenari e i metodi pertinenti.

## <a id="use-control-plane"></a>Usare il piano di controllo

L'API Azure Cosmos DB per Cassandra consente di regolare la velocità effettiva a livello di codice usando le varie funzionalità del piano di controllo. Per informazioni ed esempi, vedere gli articoli [Azure Resource Manager](manage-cassandra-with-resource-manager.md), [PowerShell](powershell-samples-cassandra.md)e dell'interfaccia della riga di comando di [Azure](cli-samples-cassandra.md) .

Il vantaggio di questo metodo è che è possibile automatizzare la scalabilità verso l'alto o verso il basso delle risorse in base a un timer per tenere conto di picchi di attività o periodi di bassa attività. Per ottenere questo risultato, vedere [questo esempio con funzioni di Azure](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) e PowerShell.

Uno svantaggio di questo approccio può essere dovuto al fatto che non è possibile rispondere a esigenze di scalabilità mutevoli non prevedibili in tempo reale. Al contrario, potrebbe essere necessario sfruttare il contesto dell'applicazione nel sistema, a livello di client/SDK o utilizzando [Autopilot](provision-throughput-autopilot.md).

## <a id="use-cql-queries"></a>Usare query CQL con un SDK specifico

È possibile ridimensionare il sistema in modo dinamico con il codice eseguendo i [comandi ALTER di CQL](cassandra-support.md#keyspace-and-table-options) per il database o il contenitore specificato.

Il vantaggio di questo approccio è che consente di rispondere in modo dinamico alle esigenze di scalabilità e in modo personalizzato per l'applicazione. Con questo approccio è comunque possibile sfruttare i costi e le tariffe standard di Ur/s. Se le esigenze di scalabilità del sistema sono prevedibili (circa il 70% o più), l'uso dell'SDK con CQL può essere un metodo più conveniente per la scalabilità automatica rispetto all'uso di Autopilot. Lo svantaggio di questo approccio è che può essere piuttosto complesso implementare nuovi tentativi mentre la limitazione della frequenza può aumentare la latenza.

## <a id="use-autopilot"></a>Usare Autopilot

Oltre al modo manuale o programmatico di provisioning della velocità effettiva, è anche possibile configurare i contenitori di Azure Cosmos in modalità Autopilot. La modalità Autopilot verrà ridimensionata automaticamente e immediatamente in base alle esigenze di consumo entro gli intervalli di ur specificati senza compromettere i contratti di contratto. Per altre informazioni, vedere l'articolo [creare contenitori e database di Azure Cosmos in modalità Autopilot](provision-throughput-autopilot.md) .

Il vantaggio di questo approccio è che è il modo più semplice per gestire le esigenze di scalabilità nel sistema. Garantisce di non applicare **la limitazione della frequenza negli intervalli di ur configurati**. Lo svantaggio è che, se le esigenze di ridimensionamento nel sistema sono prevedibili, Autopilot può essere un modo meno economico per gestire le esigenze di scalabilità rispetto all'uso del piano di controllo o degli approcci a livello di SDK indicati in precedenza.

## <a name="next-steps"></a>Passaggi successivi

- Introduzione alla [creazione di un account, database e una tabella API Cassandra](create-cassandra-api-account-java.md) usando un'applicazione Java

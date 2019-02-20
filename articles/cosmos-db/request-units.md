---
title: Unità richiesta e velocità effettiva in Azure Cosmos DB
description: Informazioni su come specificare e stimare i requisiti relativi alle unità richiesta in Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: rimman
ms.openlocfilehash: 3801c19fbef70bf5d67670c4d9acc950291853e6
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990156"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unità richiesta in Azure Cosmos DB

Con Azure Cosmos DB, viene addebitato un costo per la velocità effettiva sottoposta a provisioning e per le risorse di archiviazione utilizzate su base oraria. Il provisioning della velocità effettiva è necessario per assicurarsi che siano costantemente disponibili risorse di sistema sufficienti per il database di Azure Cosmos, in modo da soddisfare o superare le condizioni del contratto di servizio per Azure Cosmos DB.

Azure Cosmos DB supporta un'ampia gamma di API, come SQL, MongoDB, Cassandra, Gremlin e Tabella. Ogni API ha il proprio set di operazioni di database, da semplici operazioni di lettura e scrittura puntuali a query complesse. Ogni operazione di database utilizza le risorse di sistema a seconda della complessità. 

Il costo di tutte le operazioni di database viene normalizzato da Azure Cosmos DB ed è espresso in termini di unità richiesta (UR). Il costo per leggere un elemento da 1 KB è pari a 1 unità richiesta (UR). In modo analogo, a tutte le altre operazioni di database viene assegnato un costo in termini di UR. I costi vengono sempre misurati in UR, indipendentemente dall'API usata per interagire con il contenitore Azure Cosmos. Sia che l'operazione di database sia una scrittura, una lettura o una query, i costi sono sempre misurati in UR.

L'unità di misura UR al secondo può essere considerata come la valuta della velocità effettiva. UR al secondo è una valuta basata sulla frequenza, che viene determinata in base all'astrazione delle risorse di sistema, come CPU, operazioni di I/O al secondo e memoria, necessarie per eseguire le operazioni di database supportate da Azure Cosmos DB. L'immagine seguente mostra le unità richiesta utilizzate da diverse operazioni di database:

![Utilizzo delle unità richiesta da parte delle operazioni di database](./media/request-units/request-units.png)

Per gestire e pianificare la capacità, Azure Cosmos DB garantisce che il numero di UR per una specifica operazione di database su un determinato set di dati sia deterministico. Esaminare l'intestazione della risposta per tenere traccia del numero di UR utilizzate da qualsiasi operazione di database. Una volta identificati i fattori che influiscono sugli addebiti delle unità richiesta e i requisiti di velocità effettiva dell'applicazione, è possibile eseguire l'applicazione in modo economicamente conveniente.

I costi vengono addebitati su base oraria. Il provisioning del numero di UR per l'applicazione viene effettuato in base a incrementi di 100 UR al secondo. Per ridimensionare la velocità effettiva di cui viene effettuato il provisioning per l'applicazione, è possibile aumentare o diminuire il numero di UR in qualsiasi momento, in base a incrementi o decrementi di 100 UR, a livello di codice o tramite il portale di Azure.

È possibile effettuare il provisioning della velocità effettiva a due diversi livelli di granularità: 

* **Contenitori**. Per altre informazioni, vedere [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md).
* **Database**. Per altre informazioni, vedere [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Considerazioni sulle unità richiesta

Per stimare il numero di UR al secondo di cui effettuare il provisioning, considerare i fattori seguenti:

* **Dimensioni degli elementi**: con l'aumentare delle dimensioni di un elemento, aumenta anche il numero di UR utilizzate per leggere o scrivere l'elemento.

* **Indicizzazione degli elementi**: per impostazione predefinita, ogni elemento viene automaticamente indicizzato. Se si sceglie di non indicizzare alcuni degli elementi in un contenitore, viene utilizzato un numero inferiore di UR.

* **Numero di proprietà degli elementi**: supponendo che sia applicata l'indicizzazione predefinita a tutte le proprietà, il numero di UR utilizzate per scrivere un elemento aumenta proporzionalmente al numero delle proprietà dell'elemento.

* **Proprietà indicizzate**: i criteri di indicizzazione in ogni contenitore determinano le proprietà che vengono indicizzate per impostazione predefinita. Per ridurre l'utilizzo di UR per le operazioni di scrittura, limitare il numero di proprietà indicizzate.

* **Coerenza dei dati**: durante l'esecuzione di operazioni di lettura, i livelli di coerenza assoluta e con decadimento ristretto utilizzano un numero di UR di due volte maggiore rispetto ad altri livelli di coerenza meno rigidi.

* **Modelli di query**: la complessità di una query influisce sulla quantità di UR utilizzate per un'operazione. I fattori che influiscono sul costo delle operazioni di query sono: 
    
    - Il numero di risultati della query.
    - Il numero di predicati.
    - La natura dei predicati.
    - Il numero di funzioni definite dall'utente.
    - Le dimensioni dei dati di origine.
    - Le dimensioni del set di risultati.
    - Proiezioni.

  Azure Cosmos DB assicura che la stessa query sugli stessi dati costi sempre lo stesso numero di UR per esecuzioni ripetute.

* **Utilizzo di script**: come le query, le stored procedure e i trigger utilizzano le UR in base alla complessità delle operazioni da eseguire. Quando si sviluppa l'applicazione, controllare l'intestazione per l'addebito delle richieste per comprendere meglio quanta capacità in termini di UR viene utilizzata da ogni operazione.

## <a name="next-steps"></a>Passaggi successivi

* [Effettuare il provisioning della velocità effettiva per contenitori e database](set-throughput.md)
* [Partizionamento e scalabilità orizzontale in Azure Cosmos DB](partition-data.md)
* [Ridimensionamento a livello globale della velocità effettiva sottoposta a provisioning](scaling-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)

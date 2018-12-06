---
title: Output di Analisi di flusso di Azure in Cosmos DB
description: Questo articolo descrive come usare Analisi di flusso di Azure per salvare output JSON in Azure Cosmos DB, consentendo l'esecuzione di query di archiviazione dei dati e a bassa latenza su dati JSON non strutturati.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/21/2017
ms.openlocfilehash: 9bdb012db2e7502d765fd342a636591bbbcb2c6c
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311739"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Output di Analisi di flusso di Azure in Azure Cosmos DB  
L'analisi di flusso può usare [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) per l'output JSON, consentendo l'esecuzione di query di archiviazione dei dati e a bassa latenza su dati JSON non strutturati. Questo documento descrive alcune procedure consigliate per l'implementazione di questa configurazione.

Se non si ha familiarità con Cosmos DB, vedere l'articolo che descrive il [percorso di apprendimento di Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) per un’introduzione. 

> [!Note]
> Ad oggi, Analisi di flusso di Azure supporta solo la connessione ad Azure Cosmos DB tramite l'**API SQL**.
> Altre API di Azure Cosmos DB non sono ancora supportate. Se Analisi di flusso di Azure punta agli account Azure Cosmos DB creati con altre API, i dati potrebbero non essere archiviati correttamente. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Nozioni di base di Cosmos DB come destinazione di output
L'output di Azure Cosmos DB nell'analisi di flusso consente la scrittura dei risultati di elaborazione del flusso come output JSON nelle raccolte di Cosmos DB. Analisi di flusso di Azure non crea raccolte nel database, ma ne richiede la creazione anticipata da parte dell'utente. In questo modo, i costi di fatturazione delle raccolte di Cosmos DB vengono controllati dall'utente ed è possibile ottimizzare direttamente le prestazioni, la coerenza e la capacità delle raccolte usando le [API di Cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> È necessario aggiungere 0.0.0.0 all'elenco di indirizzi IP consentiti dal firewall di Azure Cosmos DB.

Di seguito sono descritte alcune delle opzioni per le raccolte di Cosmos DB.

## <a name="tune-consistency-availability-and-latency"></a>Ottimizzare coerenza, disponibilità e latenza
In base ai requisiti dell'applicazione, Azure Cosmos DB consente di ottimizzare il database e le raccolte e di bilanciare coerenza, disponibilità e latenza. A seconda dei livelli di coerenza di lettura richiesti dello scenario rispetto alla latenza di lettura e scrittura, è possibile scegliere un livello di coerenza per l'account del database. Per impostazione predefinita, Azure Cosmos DB consente anche l'indicizzazione sincrona per ogni operazione CRUD nella raccolta. Si tratta di un'altra opzione utile per controllare le prestazioni di lettura/scrittura di Azure Cosmos DB. Per altre informazioni, rivedere l'articolo relativo a come [modificare i livelli di coerenza del database e delle query](../cosmos-db/consistency-levels.md).

## <a name="upserts-from-stream-analytics"></a>Upsert di Analisi di flusso
L'integrazione di Analisi di flusso di Azure con Azure Cosmos DB consente di inserire o aggiornare i record nella raccolta in base a una determinata colonna ID documento. Questa implementazione è detta anche *upsert*.

Analisi di flusso di Azure usa un approccio upsert ottimistico in cui gli aggiornamenti vengono eseguiti solo quando l'inserimento non riesce a causa di un conflitto di ID documento. Questo aggiornamento viene eseguito come PATCH, consentendo aggiornamenti parziali al documento, ad esempio l'aggiunta di nuove proprietà o la sostituzione di una proprietà esistente eseguita in modo incrementale. Le modifiche apportate ai valori delle proprietà di matrice nel documento JSON comportano, tuttavia, la sovrascrittura dell'intera matrice, ovvero non viene eseguito il merge della matrice.

Se il documento JSON in ingresso include già un campo ID esistente, tale campo verrà usato automaticamente come colonna ID documento in Cosmos DB e le eventuali scritture successive verranno gestite come tali. Questo scenario sarà caratterizzato dalle situazioni seguenti:
- gli ID univoci generano operazioni di inserimento
- ID duplicati e 'ID documento' impostato su 'ID' generano operazioni di upsert
- ID duplicati e 'ID documento' non impostato generano un errore dopo il primo documento

Se si desidera salvare <i>tutti</i> i documenti inclusi quelli con un ID duplicato, rinominare il campo ID nella query (con la parola chiave AS) e consentire a Cosmos DB di creare il campo ID o sostituire l'ID con un altro valore della colonna (usando la parola chiave AS o tramite l'impostazione di 'ID documento').

## <a name="data-partitioning-in-cosmos-db"></a>Partizionamento dei dati in Cosmos DB
Azure Cosmos DB con partizionamento [senza limiti](../cosmos-db/partition-data.md) costituisce l'approccio consigliato per il partizionamento dei dati, in quanto Azure Cosmos DB ridimensiona le partizioni automaticamente in base al carico di lavoro. Durante la scrittura in contenitori senza limiti, Analisi di flusso di Azure usa un numero di writer paralleli uguale a quello usato nel passaggio di query precedente o nello schema di partizionamento di input.
> [!Note]
> A questo punto, Analisi di flusso di Azure supporta solo un numero illimitato di raccolte con chiavi di partizione di primo livello. Ad esempio, `/region` è supportata. Le chiavi di partizione annidate (ad esempio `/region/name`) non sono supportate. 

Per le raccolte di Azure Cosmos DB fisse, Analisi di flusso di Azure non consente alcuna possibilità di ridimensionamento se le raccolte sono al completo. Tali raccolte hanno un limite massimo di 10 GB e velocità effettiva di 10.000 UR al secondo.  Per eseguire la migrazione dei dati da un contenitore fisso a un contenitore senza limiti, ad esempio con una velocità effettiva di almeno 1000 UR al secondo e una chiave di partizione, è necessario usare l'[utilità di migrazione dati](../cosmos-db/import-data.md) o la [libreria di feed di modifiche](../cosmos-db/change-feed.md).

La scrittura in più contenitori fissi verrà deprecata e non costituisce l'approccio consigliato per il ridimensionamento del processo di Analisi di flusso di Azure. Per altre informazioni, vedere [Partizionamento e scalabilità in Cosmos DB](../cosmos-db/sql-api-partition-data.md).

## <a name="cosmos-db-settings-for-json-output"></a>Impostazioni di Cosmos DB per l'output JSON
La creazione di Cosmos DB come output nell'analisi di flusso genera una richiesta di informazioni, come illustrato di seguito. Questa sezione fornisce una spiegazione della definizione delle proprietà.


![documentdb analisi di flusso schermata di output](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

Campo           | DESCRIZIONE 
-------------   | -------------
Alias di output    | Alias per fare riferimento a questo output nella query ASA   
Nome account    | Nome o URI endpoint dell'account Azure Cosmos DB 
Chiave account     | Chiave di accesso condiviso per l'account Azure Cosmos DB
Database        | Nome del database Azure Cosmos DB
Nome raccolta | Nome di raccolta per le raccolte da usare. `MyCollection` è un esempio di input valido. Una raccolta denominata `MyCollection` deve essere presente.  
Document ID     | facoltativo. Nome della colonna negli eventi di output usato come chiave univoca su cui devono basarsi le operazioni di inserimento o aggiornamento. Se lasciato vuoto, tutti gli eventi verranno inseriti senza alcuna opzione di aggiornamento.

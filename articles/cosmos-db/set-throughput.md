---
title: Effettuare il provisioning della velocità effettiva per Azure Cosmos DB | Microsoft Docs
description: Informazioni su come configurare la velocità effettiva di provisioning per i container, le raccolte, i grafici e le tabelle di Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: sngun
ms.openlocfilehash: 99cd7fe6f9f46ff4d6dbbf6a6e024b3b32679724
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444266"
---
# <a name="set-and-get-throughput-for-azure-cosmos-db-containers-and-database"></a>Impostare e ottenere la velocità effettiva per i contenitori e il database Azure Cosmos DB

È possibile impostare la velocità effettiva per un contenitore o un set di contenitori di Azure Cosmos DB usando il portale di Azure o gli SDK client. 

**Effettuare il provisioning della velocità effettiva per un singolo contenitore:** quando si effettua il provisioning della velocità effettiva per un set di contenitori, tutti questi contenitori condividono la velocità effettiva di cui è stato effettuato il provisioning. Il provisioning della velocità effettiva per i singoli contenitori assicurerà la prenotazione della velocità effettiva per il contenitore specifico. Quando si assegnano UR/sec a livello di singolo contenitore, i contenitori possono essere creati come *fissi* oppure *senza limiti*. I contenitori a dimensione fissa hanno un limite massimo di 10 GB e velocità effettiva di 10.000 UR/s. Per creare un contenitore senza limiti, è necessario specificare una velocità effettiva minima di 1.000 UR/s e una [chiave di partizione](partition-data.md). Dal momento che i dati potrebbero essere stati suddivisi in più partizioni, è necessario scegliere una chiave di partizione che abbia un'elevata cardinalità (da 100 a milioni di valori distinti). La selezione di una chiave di partizione con molti valori distinti garantisce la scalabilità uniforme di contenitori/tabelle/grafi e richieste in Azure Cosmos DB. 

**Effettuare il provisioning della velocità effettiva per un set di contenitori o per un database:** il provisioning della velocità effettiva per un set di contenitori consente di condividere la velocità effettiva tra i contenitori che appartengono a tale database. Nel database Azure Cosmos DB è possibile avere un set di contenitori che condividono la velocità effettiva e contenitori con una velocità effettiva dedicata. Quando si assegnano UR/sec in un set di contenitori, i contenitori che appartengono a questo set sono considerati *senza limiti* ed è necessario specificare una chiave di partizione.

In base alla velocità effettiva con provisioning, Azure Cosmos DB allocherà partizioni fisiche per ospitare il contenitore e suddividerà/ribilancerà la crescita dei dati nelle partizioni. Il provisioning della velocità effettiva a livello di contenitore e di database costituisce due offerte separate e il passaggio tra le due richiede la migrazione dei dati dall'origine alla destinazione. È quindi necessario creare un nuovo database o una nuova raccolta e quindi eseguire la migrazione dei dati tramite la [libreria dell'executor bulk](bulk-executor-overview.md) oppure [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). L'immagine seguente illustra il provisioning della velocità effettiva a diversi livelli:

![Provisioning di unità richiesta per singoli contenitori e set di contenitori](./media/request-units/provisioning_set_containers.png)

Le sezioni seguenti illustrano i passaggi necessari per configurare la velocità effettiva ai vari livelli di un account Azure Cosmos DB. 

## <a name="provision-throughput-by-using-azure-portal"></a>Effettuare il provisioning della velocità effettiva usando il portale di Azure

### <a name="provision-throughput-for-a-container-collectiongraphtable"></a>Effettuare il provisioning della velocità effettiva per un contenitore (raccolta/grafo/tabella)

1. Accedere al [portale di Azure](https://portal.azure.com).  
2. Dal menu di spostamento a sinistra selezionare **Tutte le risorse** e individuare l'account Azure Cosmos DB.  
3. È possibile configurare la velocità effettiva durante la creazione di un contenitore (raccolta, grafo, tabella) o aggiornare la velocità effettiva per un contenitore esistente.  
4. Per assegnare la velocità effettiva durante la creazione di un contenitore, aprire il pannello **Esplora dati** e selezionare **Nuova raccolta** (Nuovo grafo, Nuova tabella per le altre API)  
5. Compilare il modulo nel pannello **Aggiungi raccolta**. I campi in questo pannello vengono descritti nella tabella seguente:  

   |**Impostazione**  |**Descrizione**  |
   |---------|---------|
   |ID database  |  Specificare un nome univoco per identificare il database. Un database è un contenitore logico di una o più raccolte. I nomi dei database devono avere una lunghezza compresa tra 1 e 255 caratteri e non possono contenere /, \\, #, ? o spazi finali. |
   |ID raccolta  | Specificare un nome univoco per identificare la raccolta. Gli ID delle raccolte prevedono gli stessi requisiti relativi ai caratteri dei nomi di database. |
   |Capacità di archiviazione   | Questo valore rappresenta la capacità di archiviazione del database. Quando si effettua il provisioning della velocità effettiva per una singola raccolta, la capacità di archiviazione può essere **fissa (10 GB)** oppure **illimitata**. La capacità di archiviazione illimitata richiede che venga impostata una chiave di partizione per i dati.  |
   |Velocità effettiva   | Ogni raccolta e database può avere una velocità effettiva espressa in unità richiesta al secondo.  Per la capacità di archiviazione fissa, la velocità effettiva minima è 400 unità richiesta al secondo (UR/s); per la capacità di archiviazione illimitata, la velocità effettiva minima è impostata su 1000 UR/s.|

6. Dopo avere immesso i valori per questi campi, selezionare **OK** per salvare le impostazioni.  

   ![Impostare la velocità effettiva per una raccolta](./media/set-throughput/set-throughput-for-container.png)

7. Per aggiornare la velocità effettiva per un contenitore esistente, espandere il database e il contenitore e quindi fare clic su **Impostazioni**. Nella nuova finestra digitare il nuovo valore per la velocità effettiva e quindi selezionare **Salva**.  

   ![Aggiornare la velocità effettiva per una raccolta](./media/set-throughput/update-throughput-for-container.png)

### <a name="provision-throughput-for-a-set-of-containers-or-at-the-database-level"></a>Effettuare il provisioning della velocità effettiva per un set di contenitori o a livello di database

1. Accedere al [portale di Azure](https://portal.azure.com).  
2. Dal menu di spostamento a sinistra selezionare **Tutte le risorse** e individuare l'account Azure Cosmos DB.  
3. È possibile configurare la velocità effettiva durante la creazione di un database o aggiornare la velocità effettiva per un database esistente.  
4. Per assegnare la velocità effettiva durante la creazione di un database, aprire il pannello **Esplora dati** e selezionare **Nuovo database**  
5. Compilare il valore **ID database**, selezionare l'opzione **Provisioning velocità effettiva** e configurare il valore della velocità effettiva. È possibile eseguire il provisioning di un database con un valore di velocità effettiva minima di 50.000 UR/s.  

   ![Impostare la velocità effettiva con l'opzione di nuovo database](./media/set-throughput/set-throughput-with-new-database-option.png)

6. Per aggiornare la velocità effettiva per un database esistente, espandere il database e il contenitore e quindi fare clic su **Ridimensiona**. Nella nuova finestra digitare il nuovo valore per la velocità effettiva e quindi selezionare **Salva**.  

   ![Aggiornare la velocità effettiva per un database](./media/set-throughput/update-throughput-for-database.png)

### <a name="provision-throughput-for-a-set-of-containers-as-well-as-for-an-individual-container-in-a-database"></a>Effettuare il provisioning della velocità effettiva per un set di contenitori così come per un singolo contenitore in un database

1. Accedere al [portale di Azure](https://portal.azure.com).  
2. Dal menu di spostamento a sinistra selezionare **Tutte le risorse** e individuare l'account Azure Cosmos DB.  
3. Creare un database e assegnargli la velocità effettiva. Aprire il pannello **Esplora dati** e selezionare **Nuovo database**  
4. Compilare il valore **ID database**, selezionare l'opzione **Provisioning velocità effettiva** e configurare il valore della velocità effettiva. È possibile eseguire il provisioning di un database con un valore di velocità effettiva minima di 50.000 UR/s.  

   ![Impostare la velocità effettiva con l'opzione di nuovo database](./media/set-throughput/set-throughput-with-new-database-option.png)

5. Successivamente creare una raccolta all'interno del database creato nel passaggio precedente. Per creare una raccolta, fare clic con il pulsante destro del mouse sul database e scegliere **Nuova raccolta**.  

6. Nel pannello **Aggiungi raccolta** immettere un nome per la raccolta e la chiave di partizione. Facoltativamente, è possibile effettuare il provisioning della velocità effettiva per tale contenitore specifico, se si sceglie di non assegnare un valore di velocità effettiva, la velocità effettiva assegnata al database viene condivisa con la raccolta.  

   ![Facoltativamente impostare la velocità effettiva per il contenitore](./media/set-throughput/optionally-set-throughput-for-the-container.png)

## <a name="considerations-when-provisioning-throughput"></a>Considerazioni durante il provisioning della velocità effettiva

Di seguito sono riportate alcune considerazioni utili per decidere una strategia di prenotazione della velocità effettiva.

### <a name="considerations-when-provisioning-throughput-at-the-database-level"></a>Considerazioni durante il provisioning della velocità effettiva a livello di database

Si prenda in considerazione il provisioning della velocità effettiva a livello di database, ovvero per un set di contenitori, nei casi seguenti:

* Se è disponibile almeno una decina di contenitori che possono condividere la velocità effettiva tra alcuni o tutti i contenitori.  

* Quando si esegue la migrazione da un database a tenant singolo che è stato progettato per l'esecuzione sulle macchine virtuali ospitate su IaaS o locali, ad esempio database relazionali o NoSQL, in Azure Cosmos DB e sono presenti molti contenitori.  

* Se si vuole tenere in considerazione i picchi imprevisti nei carichi di lavoro usando la velocità effettiva in pool a livello di database.  

* Anziché impostare la velocità effettiva per un singolo contenitore, si è interessati a ottenere la velocità effettiva aggregata in un set di contenitori all'interno del database.

### <a name="considerations-when-provisioning-throughput-at-the-container-level"></a>Considerazioni durante il provisioning della velocità effettiva a livello di contenitore

Tenere in considerazione il provisioning della velocità effettiva in un singolo contenitore nei seguenti casi:

* Se si dispone di un numero inferiore di contenitori Azure Cosmos DB.  

* Se si vuole ottenere la velocità effettiva garantita per un determinato contenitore con contratto di servizio.

## <a name="throughput-ranges"></a>Intervalli di velocità effettiva

La tabella seguente elenca la velocità effettiva disponibile per i contenitori:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Contenitore a partizione singola</strong></p></td>
            <td valign="top"><p><strong>Contenitore partizionato</strong></p></td>
            <td valign="top"><p><strong>Set di contenitori</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Velocità effettiva minima</p></td>
            <td valign="top"><p>400 unità richiesta al secondo</p></td>
            <td valign="top"><p>1.000 unità richiesta al secondo</p></td>
            <td valign="top"><p>50.000 unità richiesta al secondo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Velocità effettiva massima</p></td>
            <td valign="top"><p>10.000 unità richiesta al secondo</p></td>
            <td valign="top"><p>Illimitato</p></td>
            <td valign="top"><p>Illimitato</p></td>
        </tr>
    </tbody>
</table>

<a id="set-throughput-sdk"></a>

## <a name="set-throughput-by-using-sql-api-for-net"></a>Impostare la velocità effettiva usando l'API SQL per .NET

### <a name="set-throughput-at-the-container-level"></a>Impostare la velocità effettiva a livello di contenitore
Ecco un frammento di codice per la creazione di un contenitore con 3000 unità richiesta al secondo per singolo contenitore .NET SDK dell'API SQL:

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

### <a name="set-throughput-at-the-for-a-set-of-containers-or-at-the-database-level"></a>Impostare la velocità effettiva a livello di set di contenitori o di database

Ecco un frammento di codice per il provisioning di 100.000 unità richiesta al secondo per un set di contenitori usando .NET SDK dell'API SQL:

```csharp
// Provision 100,000 RU/sec at the database level. 
// sharedCollection1 and sharedCollection2 will share the 100,000 RU/sec from the parent database
// dedicatedCollection will have its own dedicated 4,000 RU/sec, independant of the 100,000 RU/sec provisioned from the parent database
Database database = client.CreateDatabaseAsync(new Database { Id = "myDb" }, new RequestOptions { OfferThroughput = 100000 }).Result;

DocumentCollection sharedCollection1 = new DocumentCollection();
sharedCollection1.Id = "sharedCollection1";
sharedCollection1.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection1, new RequestOptions())

DocumentCollection sharedCollection2 = new DocumentCollection();
sharedCollection2.Id = "sharedCollection2";
sharedCollection2.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, sharedCollection2, new RequestOptions())

DocumentCollection dedicatedCollection = new DocumentCollection();
dedicatedCollection.Id = "dedicatedCollection";
dedicatedCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(database.SelfLink, dedicatedCollection, new RequestOptions { OfferThroughput = 4000 )
```

Azure Cosmos DB usa un modello di prenotazione per la velocità effettiva, ovvero viene fatturata la quantità di velocità effettiva *riservata*, indipendentemente dalla quantità di tale velocità effettiva *usata* attivamente. A mano a mano che i modelli di carico, dati e utilizzo dell'applicazione cambiano, è possibile aumentare e ridurre facilmente il numero di unità riservate usando gli SDK o il [portale di Azure](https://portal.azure.com).

Viene eseguito il mapping di ogni contenitore o set di contenitori a una risorsa `Offer` in Azure Cosmos DB, che include metadati sulla velocità effettiva di cui è stato effettuato il provisioning. È possibile modificare la velocità effettiva allocata esaminando l'offerta corrispondente relativa alla risorsa per un contenitore, quindi aggiornarla con il nuovo valore per la velocità effettiva. Ecco un frammento di codice per la modifica della velocità effettiva di un contenitore fino a 5000 unità richiesta al secondo tramite .NET SDK. Dopo aver modificato la velocità effettiva, perché il valore aggiornato di questa venga visualizzato è necessario aggiornare eventuali finestre del portale di Azure esistenti. 

```csharp
// Fetch the resource to be updated
// For a updating throughput for a set of containers, replace the collection's self link with the database's self link
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

La modifica della velocità effettiva non influisce sulla disponibilità del contenitore o del set di contenitori. La nuova velocità effettiva riservata viene in genere applicata entro pochi secondi, in corrispondenza dell'applicazione della nuova velocità effettiva.

<a id="set-throughput-java"></a>

## <a name="to-set-the-throughput-by-using-the-sql-api-for-java"></a>Per impostare la velocità effettiva usando l'API SQL per Java

Il frammento di codice seguente recupera la velocità effettiva corrente e la cambia in 500 UR/s. Per un codice di esempio completo, vedere il file [OfferCrudSamples.java](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) su GitHub. 

```Java
// find offer associated with this collection
// To change the throughput for a set of containers, use the database's resource id instead of the collection's resource id
Iterator < Offer > it = client.queryOffers(
    String.format("SELECT * FROM r where r.offerResourceId = '%s'", collectionResourceId), null).getQueryIterator();
assertThat(it.hasNext(), equalTo(true));

Offer offer = it.next();
assertThat(offer.getString("offerResourceId"), equalTo(collectionResourceId));
assertThat(offer.getContent().getInt("offerThroughput"), equalTo(throughput));

// update the offer
int newThroughput = 500;
offer.getContent().put("offerThroughput", newThroughput);
client.replaceOffer(offer);
```

## <a id="GetLastRequestStatistics"></a>Ottenere la velocità effettiva con il comando GetLastRequestStatistics dell'API MongoDB

L'API MongoDB supporta un comando personalizzato, *getLastRequestStatistics*, per il recupero degli addebiti per le richieste per un'operazione specificata.

Ad esempio, nella shell Mongo eseguire l'operazione di cui si vuole verificare l'addebito per le richieste.
```
> db.sample.find()
```

Eseguire quindi il comando *getLastRequestStatistics*.
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

Un metodo per stimare la quantità di velocità effettiva riservata richiesta dall'applicazione consiste nel registrare l'addebito delle unità richiesta associato all'esecuzione di operazioni tipiche rispetto a un elemento rappresentativo usato dall'applicazione e quindi nello stimare il numero di operazioni che si prevede di eseguire al secondo.

> [!NOTE]
> Se sono presenti tipi di elementi che variano notevolmente in termini di dimensioni e numero di proprietà indicizzate, registrare l'addebito delle unità richiesta per le operazioni applicabili associato a ogni *tipo* di elemento tipico.
> 
> 

## <a name="get-throughput-by-using-mongodb-api-portal-metrics"></a>Ottenere la velocità effettiva usando le metriche del portale dell'API MongoDB

Il modo più semplice per ottenere una stima valida degli addebiti per le unità richiesta per il database dell'API MongoDB consiste nell'usare le metriche del [portale di Azure](https://portal.azure.com). I grafici *Numero di richieste* e *Richiedi addebito* consentono di ottenere una stima del numero di unità richiesta utilizzate da ogni operazione e del numero di unità richiesta utilizzate una rispetto all'altra.

![Metriche del portale dell'API MongoDB][1]

### <a id="RequestRateTooLargeAPIforMongoDB"></a> Superamento dei limiti della velocità effettiva riservata nell'API MongoDB
Le applicazioni che superano la velocità effettiva con provisioning per un contenitore o un set di contenitori vengono limitate nella velocità fino a quando il tasso di utilizzo non scende sotto la velocità effettiva con provisioning. In caso di limitazione della velocità, il back-end terminerà la richiesta con un codice errore `16500`, ovvero `Too Many Requests`. Per impostazione predefinita, l'API MongoDB ripeterà automaticamente i tentativi fino a 10 volte prima di restituire un codice errore `Too Many Requests`. Se si riceve un numero eccessivo di codici errore `Too Many Requests`, è possibile prendere in considerazione l'aggiunta di una logica di ripetizione dei tentativi nelle routine di gestione degli errori dell'applicazione oppure l'[aumento della velocità effettiva con provisioning per il contenitore](set-throughput.md).

## <a name="throughput-faq"></a>Domande frequenti sulla velocità effettiva

**È possibile impostare la velocità effettiva a meno di 400 UR/sec?**

Il valore di 400 UR/sec è la velocità effettiva minima disponibile nei contenitori di partizioni singole di Cosmos DB. 1000 UR/sec è il valore minimo per i contenitori partizionati. Le unità richieste sono impostate in intervalli di 100 UR/sec, ma la velocità effettiva non può essere impostata su 100 UR/sec o su qualsiasi valore inferiore a 400 UR/sec. Se si cerca un metodo conveniente per sviluppare e testare Cosmos DB, è possibile usare la versione gratuita dell'[emulatore di Azure Cosmos DB](local-emulator.md), distribuibile in locale senza alcun costo aggiuntivo. 

**Come si configura la velocità effettiva tramite l'API MongoDB?**

Non è disponibile alcuna estensione dell'API MongoDB per la configurazione della velocità effettiva. È consigliabile usare l'API SQL, come mostrato in [Per configurare la velocità effettiva usando l'API SQL per .NET](#set-throughput-sdk).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla stima della velocità effettiva e delle unità richiesta, vedere [Unità richiesta in Azure Cosmos DB](request-units.md)

* Per altre informazioni sul provisioning e sulla diffusione su scala globale di Cosmos DB, vedere [Partizionamento e scalabilità con Cosmos DB](partition-data.md).

[1]: ./media/set-throughput/api-for-mongodb-metrics.png

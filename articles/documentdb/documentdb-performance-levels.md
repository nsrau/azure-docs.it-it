---
title: Livelli di prestazioni in DocumentDB | Documentazione Microsoft
description: "Informazioni sul modo in cui i livelli di prestazioni in DocumentDB consentono di riservare la velocità effettiva in base a ogni raccolta."
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 7dc21c71-47e2-4e06-aa21-e84af52866f4
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: ed44ca2076860128b175888748cdaa8794c2310d
ms.openlocfilehash: 237a92713ee8dca72a09550c47519189f2fd23cc


---
# <a name="performance-levels-and-pricing-tiers-in-documentdb"></a>Livelli di prestazioni e piani tariffari in DocumentDB
Questo articolo fornisce una panoramica dei livelli di prestazioni in [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:  

* Che cos'è un livello di prestazioni?
* In che modo la velocità effettiva viene riservata per un account di database?
* Come usare i livelli di prestazioni?
* Come viene addebitato il costo per i livelli di prestazioni?

## <a name="introduction-to-performance-levels"></a>Introduzione ai livelli di prestazioni
Per ogni raccolta di DocumentDB creata in un account DocumentDB Standard viene effettuato il provisioning con un livello di prestazioni associato. Dato che ogni raccolta in un database può avere un livello di prestazioni diverso, è possibile designare una velocità effettiva maggiore per le raccolte a cui si accede di frequente e una velocità effettiva minore per quelle a cui si accede raramente. 

DocumentDB supporta sia livelli di prestazioni **definiti dall'utente** che livelli di prestazioni **predefiniti**, come illustrato nella tabella seguente.  Le prestazioni definite dall'utente supportano una velocità effettiva riservata in unità di 100 UR/sec e spazio di archiviazione illimitato, mentre i tre livelli di prestazioni predefiniti offrono opzioni di velocità effettiva specificate e una quota di archiviazione di 10 GB. La tabella seguente mette a confronto le prestazioni **definite dall'utente** e quelle **predefinite**.

|Tipo di prestazioni|Dettagli|Velocità effettiva|Archiviazione|Versione|API|
|----------------|-------|----------|-------|-------|----|
|Prestazioni definite dall'utente|L'utente imposta la velocità effettiva in unità di 100 UR/sec|Senza limiti|Senza limiti|V2|API 2015-12-16 e versioni successive|
|Prestazioni predefinite|10 GB di capacità di archiviazione riservata.<br><br>S1 = 250 UR/sec<br>S2 = 1000 UR/sec<br>S3 = 2500 UR/sec|2500 UR/sec|10 GB|V1|Qualsiasi|

La velocità effettiva è riservata per raccolta ed è usabile esclusivamente dalla raccolta specifica. La velocità effettiva viene misurata in [unità richiesta (UR)](documentdb-request-units.md), che identificano la quantità di risorse necessaria per eseguire diverse operazioni su database DocumentDB.

> [!NOTE]
> Il livello di prestazioni di una raccolta può essere modificato tramite gli [SDK](documentdb-sdk-dotnet.md) o il [portale di Azure](https://portal.azure.com/). Le modifiche a livello di prestazioni devono essere completate entro 3 minuti.
> 
> 

## <a name="setting-performance-levels-for-collections"></a>Impostazione dei livelli di prestazioni per le raccolte
Una volta creata una raccolta, l'allocazione completa di unità di richiesta in base al livello di prestazioni designato è riservata per la raccolta.

Si noti che, sia con i livelli di prestazioni predefiniti che con quelli definiti dall'utente, DocumentDB opera in base alla velocità effettiva riservata. Quando viene creata una raccolta, l'applicazione prenota una velocità effettiva e viene fatturata per la velocità effettiva riservata, indipendentemente dalla quantità usata effettivamente. Con i livelli di prestazioni definiti dall'utente l'archiviazione viene misurata in base al consumo, mentre con i livelli di prestazioni predefiniti viene riservato uno spazio di archiviazione di 10 GB al momento della creazione della raccolta.  

Dopo aver creato le raccolte, è possibile modificare il livello di prestazioni e/o la velocità effettiva usando gli [SDK](documentdb-sdk-dotnet.md) o il [portale di Azure](https://portal.azure.com/).

> [!IMPORTANT]
> Per le raccolte standard di DocumentDB viene fatturata una tariffa oraria e per ogni raccolta creata verrà fatturata minimo un'ora di utilizzo.
> 
> 

Se si modifica il livello di prestazioni di una raccolta nell'intervallo di un'ora, verrà addebitato il costo per il massimo livello di prestazioni impostato durante quell'ora. Ad esempio, se si aumenta il livello di prestazioni per una raccolta alle ore 8:53, verrà addebitato il costo per il nuovo livello a partire dalle ore 8:00. Analogamente, se si diminuisce il livello di prestazioni alle ore 8:53, la nuova tariffa verrà applicata alle ore 9:00.

Le unità di richiesta vengono riservate per ogni raccolta in base al livello delle prestazioni impostato. L'utilizzo di unità di richiesta viene valutato con una tariffa al secondo. Le applicazioni che superano il livello di unità di richiesta con provisioning (o il livello delle prestazioni) in una raccolta saranno limitate fino al ritorno del livello sotto il valore riservato per tale raccolta. Se l'applicazione necessita di un livello superiore di velocità effettiva, sarà possibile aumentare il livello delle prestazioni per ogni raccolta.

> [!NOTE]
> Quando l'applicazione supera i livelli delle prestazioni per una o più raccolte, le richieste saranno limitate in base a ogni raccolta. Ciò significa che alcune richieste di applicazione possono avere esito positivo mentre altre possono essere limitate. In questo caso, è consigliabile aggiungere un numero ridotto di tentativi per gestire i picchi di traffico delle richieste.
> 
> 

## <a name="working-with-performance-levels"></a>Utilizzo dei livelli delle prestazioni
Le raccolte di DocumentDB consentono di raggruppare i dati in base ai modelli di query e alle esigenze in termini di prestazioni dell'applicazione. Con l'indicizzazione automatica e il supporto delle query di DocumentDB, è molto comune collocare documenti eterogenei all'interno della stessa raccolta. Le considerazioni principali nello stabilire se è necessario usare raccolte separate per i dati includono:

* Query: una raccolta è l'ambito per l'esecuzione di query. Se è necessario eseguire una query in un set di documenti, i modelli di lettura più efficienti derivano dall'inserimento dei documenti in un'unica raccolta.
* Transazioni: tutte le transazioni sono limitate a una singola raccolta. Se sono presenti documenti da aggiornare in un singolo trigger o una stored procedure, devono essere archiviati nella stessa raccolta. In particolare, una chiave di partizione in una raccolta rappresenta il limite della transazione. Per altre informazioni, vedere l'articolo relativo al [partizionamento in DocumentDB](documentdb-partition-data.md) .
* Isolamento delle prestazioni: a ogni raccolta è associato un livello delle prestazioni. Ciò garantisce che ogni raccolta offra prestazioni prevedibili tramite unità di richiesta riservate. È possibile allocare dati in più raccolte, con diversi livelli di prestazioni, in base alla frequenza di accesso.

> [!IMPORTANT]
> È importante comprendere che la fatturazione avverrà alle tariffe standard complete in base al numero di raccolte create all'interno dell'applicazione.
> 
> 

È consigliabile che l'applicazione usi un numero ridotto di raccolte, a meno che non si dispone di requisiti di archiviazione o throughput elevati. Assicurarsi di avere ben compreso i modelli di applicazione per la creazione di nuove raccolte. È possibile scegliere di riservare la creazione della raccolta come un'azione di gestione gestita all'esterno dell'applicazione. Analogamente, la regolazione del livello delle prestazioni per una raccolta cambierà la tariffa oraria con la quale la raccolta viene fatturata. Se l'applicazione consente di regolarli in modo dinamico, è opportuno monitorare i livelli delle prestazioni della raccolta.

## <a name="a-idchanging-performance-levels-using-the-azure-portalachange-from-s1-s2-s3-to-user-defined-performance"></a><a id="changing-performance-levels-using-the-azure-portal"></a>Passare dal livello S1, S2, S3 alle prestazioni definite dall'utente
Seguire questa procedura per passare da livelli di velocità effettiva predefiniti a livelli di velocità effettiva definiti dall'utente nel portale di Azure. L'uso di livelli di velocità effettiva definiti dall'utente permette di personalizzare la velocità effettiva in base alle esigenze. Se si usa un account S1, con pochi clic è possibile aumentare la velocità effettiva predefinita da 250 UR/sec a 400 UR/sec. Si noti che dopo lo spostamento di una raccolta da S1, S2 o S3 al livello Standard (definito dall'utente), non è possibile tornare a S1, S2 o S3. La velocità effettiva di una raccolta Standard può tuttavia essere modificata in qualsiasi momento.

Per altre informazioni sulle modifiche ai prezzi della velocità effettiva definita dall'utente e predefinita, vedere il post del blog [DocumentDB: Everything you need to know about using the new pricing options](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/)(DocumentDB: tutto quello che occorre sapere sull'uso delle nuove opzioni relative ai prezzi).

> [!VIDEO https://channel9.msdn.com/Blogs/AzureDocumentDB/ChangeDocumentDBCollectionPerformance/player]
> 
> 

1. Nel [**portale di Azure**](https://portal.azure.com) fare clic su **NoSQL (DocumentDB)** e quindi selezionare l'account DocumentDB da modificare. 
 
    Se la voce **NoSQL (DocumentDB)** non è inclusa nell'indice, fare clic su >, scorrere fino a **Database** e selezionare **NoSQL (DocumentDB)**e quindi l'account DocumentDB.  

2. Nel menu delle risorse fare clic su **Piano** in **Raccolte**, selezionare la raccolta da modificare nell'elenco a discesa e quindi fare clic su **Piano tariffario**. Gli account che usano la velocità effettiva predefinita hanno un piano tariffario S1, S2 o S3.  Nel pannello **Scegliere il piano tariffario** fare clic su **Standard** per modificare la velocità effettiva definita dall'utente e quindi su **Seleziona** per salvare la modifica.

    ![Screenshot del pannello Impostazioni che mostra dove modificare il valore della velocità effettiva](./media/documentdb-performance-levels/documentdb-change-performance-set-thoughput.png)

3. Nel pannello **Piano** il **piano tariffario** è impostato su **Standard** e nella casella **Velocità effettiva (UR/sec)** viene visualizzato un valore predefinito di 400. Impostare la velocità effettiva tra 400 e 10.000 [unità richiesta](documentdb-request-units.md)al secondo (UR/sec). Il contenuto di **Fattura mensile stimata** nella parte inferiore della pagina viene aggiornato automaticamente per fornire una stima del costo mensile. Fare clic su **Salva** per salvare le modifiche.

    Se è necessaria una velocità effettiva maggiore di 10.000 UR/sec o uno spazio di archiviazione maggiore di 10 GB è possibile creare una raccolta partizionata. Per creare una raccolta partizionata, vedere [Come creare una raccolta DocumentDB usando il portale di Azure](documentdb-create-collection.md).

> [!NOTE]
> La modifica dei livelli di prestazioni di una raccolta può richiedere fino a 2 minuti.
> 
> 

## <a name="changing-performance-levels-using-the-net-sdk"></a>Modifica dei livelli di prestazioni tramite il SDK di .NET
Un'altra opzione per la modifica dei livelli di prestazioni degli insiemi è tramite SDK. Questa sezione illustra solo la modifica del livello di prestazioni di una raccolta con l'[SDK .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx), ma il processo per gli altri [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) è simile. Se non si conosce il SDK di .NET, visitare la nostra [esercitazione introduttiva](documentdb-get-started.md).

Di seguito è riportato un frammento di codice per la modifica della velocità effettiva dell'offerta a 50.000 unità richiesta al secondo:

    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);

    // Set the throughput to S2
    offer = new Offer(offer);
    offer.OfferType = "S2";

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);



> [!NOTE]
> Per le raccolte con provisioning di meno di 10.000 unità richiesta al secondo, è possibile eseguire la migrazione tra le offerte con velocità effettiva definita dall'utente e velocità effettiva predefinita (S1, S2, S3) in qualsiasi momento. Le raccolte con provisioning di oltre 10.000 unità richiesta al secondo non possono essere convertite in livelli di velocità effettiva predefiniti.
> 
> 

Visitare [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) per visualizzare ulteriori esempi e ulteriori informazioni sui metodi della nostra offerta:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

## <a name="a-idchange-throughputachanging-the-throughput-of-a-collection"></a><a id="change-throughput"></a>Modifica della velocità effettiva di una raccolta
Se si usano già prestazioni definite dall'utente, è possibile modificare la velocità effettiva della raccolta eseguendo le operazioni seguenti. Se è necessario passare da un livello di prestazioni S1, S2 o S3 (prestazioni predefinite) a prestazioni definite dall'utente, vedere [Passare dal livello S1, S2, S3 alle prestazioni definite dall'utente](#changing-performance-levels-using-the-azure-portal).

1. Nel [**portale di Azure**](https://portal.azure.com) fare clic su **NoSQL (DocumentDB)** e quindi selezionare l'account DocumentDB da modificare.    
2. Nel menu delle risorse fare clic su **Piano** in **Raccolte** e selezionare la raccolta da modificare nell'elenco a discesa.
3. Nella casella **Velocità effettiva (UR/sec)** digitare il nuovo livello di velocità effettiva. 
   
    Il contenuto di **Fattura mensile stimata** nella parte inferiore della pagina viene aggiornato automaticamente per fornire una stima del costo mensile. Fare clic su **Salva** per salvare le modifiche.

    Se non si è certi dell'aumento della velocità effettiva necessario, vedere [Stima delle esigenze di velocità effettiva](documentdb-request-units.md#estimating-throughput-needs) e il [calcolatore di unità richiesta](https://www.documentdb.com/capacityplanner).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se nel pannello **Scegliere il piano tariffario** non è presente l'opzione per passare ai livelli di prestazioni S1, S2 o S3, fare clic su **Visualizza tutto** per visualizzare i livelli di prestazioni Standard, S1, S2 e S3. Se si usa il piano tariffario Standard, non è possibile passare a S1, S2 e S3.

![Screenshot del pannello Scegliere il piano tariffario con opzione Visualizza tutto evidenziata](./media/documentdb-performance-levels/azure-documentdb-database-view-all-performance-levels.png)

Dopo lo spostamento di una raccolta da S1, S2 o S3 a Standard, non è possibile tornare a S1, S2 o S3.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui prezzi e sulla gestione dei dati con Azure DocumentDB, esplorare queste risorse:

* [Prezzi di DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/)
* [Modellazione dei dati in DocumentDB](documentdb-modeling-data.md)
* [Partizionamento dei dati in DocumentDB](documentdb-partition-data.md)
* [Unità richiesta](http://go.microsoft.com/fwlink/?LinkId=735027)

Per altre informazioni su DocumentDB, vedere la [documentazione](https://azure.microsoft.com/documentation/services/documentdb/)relativa ad Azure DocumentDB.

Per informazioni sulle attività iniziali relative al test delle prestazioni e della scalabilità con DocumentDB, vedere [Test delle prestazioni e della scalabilità con Azure DocumentDB](documentdb-performance-testing.md).

[1]: ./media/documentdb-performance-levels/documentdb-change-collection-performance7-9.png
[2]: ./media/documentdb-performance-levels/documentdb-change-collection-performance10-11.png



<!--HONumber=Jan17_HO2-->



<properties 
	pageTitle="Livelli di prestazioni in DocumentDB | Microsoft Azure" 
	description="Informazioni sul modo in cui i livelli di prestazioni in DocumentDB consentono di riservare la velocità effettiva in base a ogni raccolta." 
	services="documentdb" 
	authors="johnfmacintyre" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/01/2016" 
	ms.author="johnmac"/>

# Livelli di prestazioni in DocumentDB

Questo articolo fornisce una panoramica dei livelli di prestazioni in [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:

-	Che cos'è un livello di prestazioni?
-	In che modo la velocità effettiva viene riservata per un account di database?
-	Come usare i livelli di prestazioni?
-	Come viene addebitato il costo per i livelli di prestazioni?

## Introduzione ai livelli di prestazioni

Per ogni raccolta di DocumentDB creata con un account standard viene eseguito il provisioning con un livello di prestazioni associato. Ogni raccolta in un database può avere un livello di prestazioni diverso, consentendo di designare una velocità effettiva maggiore per le raccolte a cui si accede di frequente e una minore velocità effettiva per le raccolte a cui si accede raramente. DocumentDB supporta sia livelli di prestazioni definiti dall'utente che livelli di prestazioni predefiniti.

A ogni livello di prestazioni è associato un limite di velocità espresso in [unità richiesta (UR)](http://go.microsoft.com/fwlink/?LinkId=735027). Si tratta della velocità effettiva che sarà riservata per una raccolta basata sul relativo livello di prestazioni ed è disponibile esclusivamente per l'uso in tale raccolta.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
        	<td valign="top"><p></p></td>
            <td valign="top"><p>Dettagli</p></td>
            <td valign="top"><p>Limiti di velocità effettiva</p></td>
            <td valign="top"><p>Limiti relativi all'archiviazione</p></td>
            <td valign="top"><p>Versione</p></td>
 			<td valign="top"><p>API</p></td>            
        </tr>
        <tr>
        	<td valign="top"><p>Prestazioni definite dall'utente</p></td>
            <td valign="top"><p>Archiviazione misurata in base all'utilizzo in GB.</p><p>Velocità effettiva in unità di 100 UR/sec</p></td>
            <td valign="top"><p>Senza limiti. 400 - 250.000 RU/s per impostazione predefinita (maggiore su richiesta)</p></td>
            <td valign="top"><p>Senza limiti. 250 GB per impostazione predefinita (maggiore su richiesta) </p></td>
            <td valign="top"><p>V2</p></td>
 			<td valign="top"><p>API 2015-12-16 e versioni successive</p></td>  
        </tr>
        <tr>
        	<td valign="top"><p>Prestazioni predefinite</p></td>
            <td valign="top"><p>10 GB di capacità di archiviazione riservata.</p><p>S1 = 250 UR/sec, S2 = 1000 UR/sec, S3 = 2500 UR/sec</p></td>
            <td valign="top"><p>2500 UR/sec</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>V1</p></td>
 			<td valign="top"><p>Qualsiasi</p></td>  
        </tr>        
    </tbody>
</table>                


DocumentDB consente un'ampia gamma di operazioni di database tra cui query, query con funzioni definite dall'utente (UDF), stored procedure e trigger. Il costo di elaborazione associato a ognuna di queste operazioni dipende da CPU, I/O e memoria necessari per il completamento dell'operazione. Invece di occuparsi della pianificazione e della gestione delle risorse hardware, sarà possibile usare un'unità di richiesta come misura singola per le risorse necessarie per eseguire diverse operazioni di database e rispondere a una richiesta dell'applicazione.

È possibile creare le raccolte tramite il [portale di Microsoft Azure](https://portal.azure.com), l'[API REST](https://msdn.microsoft.com/library/azure/mt489078.aspx) o uno qualsiasi degli [SDK di DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). Le API di DocumentDB consentono di specificare il livello di prestazioni di una raccolta.

> [AZURE.NOTE] Il livello di prestazioni di una raccolta può essere modificato tramite le API o il [portale di Microsoft Azure](https://portal.azure.com/). Le modifiche a livello di prestazioni devono essere completate entro 3 minuti.

## Impostazione dei livelli di prestazioni per le raccolte
Una volta creata una raccolta, l'allocazione completa di unità di richiesta in base al livello di prestazioni designato è riservata per la raccolta.

Si noti che, sia con i livelli di prestazioni predefiniti che con quelli definiti dall'utente, DocumentDB opera in base alla velocità effettiva riservata. Quando viene creata una raccolta, l'applicazione prenota una velocità effettiva e viene fatturata per la velocità effettiva riservata, indipendentemente dalla quantità usata effettivamente. Con i livelli di prestazioni definiti dall'utente l'archiviazione viene misurata in base al consumo, mentre con i livelli di prestazioni predefiniti viene riservato uno spazio di archiviazione di 10 GB al momento della creazione della raccolta.

Dopo aver creato le raccolte, è possibile modificare il livello di prestazioni tramite gli SDK di DocumentDB o tramite il portale di Azure classico.

> [AZURE.IMPORTANT] Per le raccolte standard di DocumentDB viene fatturata una tariffa oraria e per ogni raccolta creata verrà fatturata minimo un'ora di utilizzo.

Se si modifica il livello di prestazioni di una raccolta nell'intervallo di un'ora, verrà addebitato il costo per il massimo livello di prestazioni impostato durante quell'ora. Ad esempio, se si aumenta il livello di prestazioni per una raccolta alle ore 8:53, verrà addebitato il costo per il nuovo livello a partire dalle ore 8:00. Analogamente, se si diminuisce il livello di prestazioni alle ore 8:53, la nuova tariffa verrà applicata alle ore 9:00.

Le unità di richiesta vengono riservate per ogni raccolta in base al livello delle prestazioni impostato. L'utilizzo di unità di richiesta viene valutato con una tariffa al secondo. Le applicazioni che superano il livello di unità di richiesta con provisioning (o il livello delle prestazioni) in una raccolta saranno limitate fino al ritorno del livello sotto il valore riservato per tale raccolta. Se l'applicazione necessita di un livello superiore di velocità effettiva, sarà possibile aumentare il livello delle prestazioni per ogni raccolta.

> [AZURE.NOTE] Quando l'applicazione supera i livelli delle prestazioni per una o più raccolte, le richieste saranno limitate in base a ogni raccolta. Ciò significa che alcune richieste di applicazione possono avere esito positivo mentre altre possono essere limitate. È consigliabile aggiungere una richiesta alla volta.

## Utilizzo dei livelli delle prestazioni
Le raccolte di DocumentDB permettono di raggruppare i dati sulla base di modelli di query e delle esigenze in termini di prestazioni dell'applicazione. Con l'indicizzazione automatica e il supporto delle query di DocumentDB, è molto comune collocare documenti eterogenei all'interno della stessa raccolta. Le considerazioni principali nello stabilire se è necessario usare raccolte separate per i dati includono:

- Query: una raccolta è l'ambito per l'esecuzione di query. Se è necessario eseguire una query in un set di documenti, i modelli di lettura più efficienti derivano dall'inserimento dei documenti in un'unica raccolta.
- Transazioni: tutte le transazioni sono limitate a una singola raccolta. Se sono presenti documenti da aggiornare in un singolo trigger o una stored procedure, devono essere archiviati nella stessa raccolta. In particolare, una chiave di partizione in una raccolta rappresenta il limite della transazione. Per altre informazioni, vedere l'articolo relativo al [partizionamento in DocumentDB](documentdb-partition-data.md).
- Isolamento delle prestazioni: a ogni raccolta è associato un livello delle prestazioni. Ciò garantisce che ogni raccolta offra prestazioni prevedibili tramite unità di richiesta riservate. È possibile allocare dati in più raccolte, con diversi livelli di prestazioni, in base alla frequenza di accesso.

> [AZURE.IMPORTANT] È importante comprendere che la fatturazione avverrà alle tariffe standard complete in base al numero di raccolte create all'interno dell'applicazione.

È consigliabile che l'applicazione usi un numero ridotto di raccolte, a meno che non si dispone di requisiti di archiviazione o throughput elevati. Assicurarsi di avere ben compreso i modelli di applicazione per la creazione di nuove raccolte. È possibile scegliere di riservare la creazione della raccolta come un'azione di gestione gestita all'esterno dell'applicazione. Analogamente, la regolazione del livello delle prestazioni per una raccolta cambierà la tariffa oraria con la quale la raccolta viene fatturata. Se l'applicazione consente di regolarli in modo dinamico, è opportuno monitorare i livelli delle prestazioni della raccolta.

## Modifica dei livelli di prestazioni tramite il portale di Azure

Il portale di Azure è una delle opzioni disponibili quando si gestiscono i livelli di prestazioni delle raccolte. Attenersi alla seguente procedura per modificare il livello di prestazioni di una raccolta dal portale di Azure classico.

1. Nel browser passare al [**portale di Microsoft Azure**](https://portal.azure.com).
2. Fare clic su **Sfoglia** dalla barra di spostamento sul lato sinistro.
3. Nello hub **Sfoglia**, fare clic su **account DocumentDB** sotto l’etichetta **filtrare in base a**.
4. Nel pannello **account DocumentDB**, fare clic sull'account DocumentDB che contiene la raccolta desiderata.
5. Nel pannello **account DocumentDB**, scorrere fino a visualizzare la lente **database** e fare clic sul database che contiene la raccolta desiderata. 
6. Nel pannello **Database** appena aperto, scorrere fino a visualizzare la lente **raccolte** e selezionare la raccolta desiderata.
7. Nel pannello **Gestisci raccolta** fare clic su **Piano tariffario**.

    ![Screenshot dei pannelli Gestisci raccolta e Scegliere il piano tariffario per Azure DocumentDB che mostrano dove modificare il piano tariffario per la raccolta][1]

8. Nel pannello **Scegliere il piano tariffario** selezionare **Standard**.

9. Nel pannello **Scegliere il piano tariffario** fare clic su **Seleziona**.

10. Nel pannello **Gestisci raccolta** il **Piano tariffario** è stato modificato in **Standard** e viene visualizzata la casella **Velocità effettiva (UR/sec)**.

    Modificare la **Velocità effettiva** specificando nella casella un valore compreso tra 400 e 10.000 [unità richiesta](documentdb-request-units.md) al secondo (UR/sec). Il **Riepilogo dei prezzi** nella parte inferiore della pagina si aggiorna automaticamente per fornire una stima del costo mensile.

    ![Screenshot del pannello Gestisci raccolta che mostra dove modificare il valore della velocità effettiva per la raccolta][2]

9. Nel pannello **Gestisci raccolta** fare clic su **OK** per aggiornare la raccolta con le prestazioni definite dall'utente.

Se è necessaria una velocità effettiva maggiore di 10.000 UR/sec o uno spazio di archiviazione maggiore di 10 GB è possibile creare una raccolta partizionata. Per creare una raccolta partizionata, vedere l'articolo relativo alla [creazione di una raccolta](documentdb-create-collection.md).

>[AZURE.NOTE] La modifica dei livelli di prestazioni di una raccolta può richiedere fino a 2 minuti.

## Modifica dei livelli di prestazioni tramite il SDK di .NET

Un'altra opzione per la modifica dei livelli di prestazioni degli insiemi è tramite SDK. Questa sezione riguarda solo la modifica delle prestazioni di una raccolta di livello usando [.NET SDK](https://msdn.microsoft.com/library/azure/dn948556.aspx) ma il processo è simile per le altre [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). Se non si conosce il SDK di .NET, visitare la nostra [esercitazione introduttiva](documentdb-get-started.md).

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



> [AZURE.NOTE] Per le raccolte con provisioning di meno di 10.000 unità richiesta al secondo, è possibile eseguire la migrazione tra le offerte con velocità effettiva definita dall'utente e velocità effettiva predefinita (S1, S2, S3) in qualsiasi momento. Le raccolte con provisioning di oltre 10.000 unità richiesta al secondo non possono essere convertite in livelli di velocità effettiva predefiniti.

Visitare [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) per visualizzare ulteriori esempi e ulteriori informazioni sui metodi della nostra offerta:

- [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
- [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
- [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
- [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx) 

## Passaggi successivi

Per altre informazioni sui prezzi e sulla gestione dei dati con Azure DocumentDB, esplorare queste risorse:
 
- [Prezzi di DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/)
- [Gestire la capacità e le prestazioni di DocumentDB](documentdb-manage.md) 
- [Modellazione dei dati in DocumentDB](documentdb-modeling-data.md)
- [Partizionamento dei dati in DocumentDB](documentdb-partition-data.md)
- [Unità richiesta](http://go.microsoft.com/fwlink/?LinkId=735027)

Per altre informazioni su DocumentDB, vedere la [documentazione](https://azure.microsoft.com/documentation/services/documentdb/) relativa ad Azure DocumentDB.

[1]: ./media/documentdb-performance-levels/documentdb-change-collection-performance7-9.png
[2]: ./media/documentdb-performance-levels/documentdb-change-collection-performance10-11.png

<!---HONumber=AcomDC_0406_2016-->
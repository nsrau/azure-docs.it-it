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
	ms.date="07/21/2015" 
	ms.author="johnmac"/>

#Livelli di prestazioni in DocumentDB

Questo articolo fornisce una panoramica dei livelli di prestazioni in [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/).

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:

-	Che cos'è un livello di prestazioni?
-	In che modo la velocità effettiva viene riservata per un account di database?
-	Come usare i livelli di prestazioni?
-	Come viene addebitato il costo per i livelli di prestazioni?

##Introduzione ai livelli di prestazioni

Per ogni raccolta di DocumentDB creata con un account standard viene eseguito il provisioning con un livello di prestazioni associato. I livelli di prestazioni sono designati come S1, S2 o S3, classificati dal minimo al massimo delle prestazioni. Il livello di prestazioni della raccolta determina la quantità di risorse di servizio riservate per l'applicazione. Ogni raccolta in un database può avere un livello di prestazioni diverso, consentendo di designare una velocità effettiva maggiore per le raccolte a cui si accede di frequente e una minore velocità effettiva per le raccolte a cui si accede raramente.

A ogni livello di prestazioni è associato un limite di frequenza dell'unità di richiesta (RU). Si tratta della velocità effettiva che sarà riservata per una raccolta basata sul relativo livello di prestazioni ed è disponibile esclusivamente per l'uso in tale raccolta. È possibile creare le raccolte tramite il [portale di Azure](http://portal.azure.com) o uno qualsiasi degli [SDK di DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx). Le API di DocumentDB consentono di specificare il livello di prestazioni di una raccolta.

<table> 
<tbody>
<tr>
<td valign="top" ><p><b>Livello di prestazioni della raccolta</b></p></td>
<td valign="top" ><p><b>Velocità effettiva riservata</b></p></td>
</tr>

<tr>
<td valign="top" ><p>S1</p></td>
<td valign="top" ><p>250 RU/sec</p></td>
</tr>

<tr>
<td valign="top" ><p>S2</p></td>
<td valign="top" ><p>1000 RU/sec</p></td>
</tr>

<tr>
<td valign="top" ><p>S3</p></td>
<td valign="top" ><p>2500 RU/sec</p></td>
</tr>

</tbody>
</table>

DocumentDB consente un'ampia gamma di operazioni di database tra cui query, query con funzioni definite dall'utente (UDF), stored procedure e trigger. Il costo di elaborazione associato a ognuna di queste operazioni dipende da CPU, I/O e memoria necessari per il completamento dell'operazione. Invece di occuparsi della pianificazione e della gestione delle risorse hardware, sarà possibile usare un'unità di richiesta come misura singola per le risorse necessarie per eseguire diverse operazioni di database e rispondere a una richiesta dell'applicazione.

> [AZURE.NOTE]I livelli di prestazioni vengono misurati in unità di richiesta. A ogni livello di prestazioni è associata una velocità massima di unità di richiesta al secondo. Il livello di prestazioni di una raccolta può essere modificato tramite le API o il [portale di Azure](https://portal.azure.com/).

##Impostazione dei livelli di prestazioni per le raccolte
Una volta creata una raccolta, l'allocazione completa di unità di richiesta in base al livello di prestazioni designato è riservata per la raccolta. Ad esempio, se una raccolta è impostata come S3: la raccolta è in grado di elaborare 2500 RU al secondo. Ogni raccolta riserva la velocità effettiva designata e 10 GB di archiviazione del database. Il prezzo della raccolta varia a seconda del livello delle prestazioni prescelto (S1, S2, S3). Si noti che DocumentDB opera in base alla prenotazione della capacità; con la creazione di una raccolta, un'applicazione riserva la velocità effettiva e riceve l'addebito per l'archiviazione di database e la velocità effettiva riservate, indipendentemente dalla quantità di archiviazione e velocità effettiva usata attivamente.

Dopo aver creato le raccolte, è possibile modificare il livello di prestazioni tramite gli SDK di DocumentDB o tramite il portale di gestione di Azure.

> [AZURE.IMPORTANT]Per le raccolte standard di DocumentDB viene fatturata una tariffa oraria e per ogni raccolta creata verrà fatturata minimo un'ora di utilizzo.

Se si modifica il livello di prestazioni di una raccolta nell'intervallo di un'ora, verrà addebitato il costo per il massimo livello di prestazioni impostato durante quell'ora. Ad esempio, se si aumenta il livello di prestazioni per una raccolta alle ore 8:53, verrà addebitato il costo per il nuovo livello a partire dalle ore 8:00. Analogamente, se si diminuisce il livello di prestazioni alle ore 8:53, la nuova tariffa verrà applicata alle ore 9:00.

Le unità di richiesta vengono riservate per ogni raccolta in base al livello delle prestazioni impostato. L'utilizzo di unità di richiesta viene valutato con una tariffa al secondo. Le applicazioni che superano il livello di unità di richiesta con provisioning (o il livello delle prestazioni) in una raccolta saranno limitate fino al ritorno del livello sotto il valore riservato per tale raccolta. Se l'applicazione necessita di un livello superiore di velocità effettiva, sarà possibile aumentare il livello delle prestazioni per ogni raccolta.

> [AZURE.NOTE]Quando l'applicazione supera i livelli delle prestazioni per una o più raccolte, le richieste saranno limitate in base a ogni raccolta. Ciò significa che alcune richieste di applicazione possono avere esito positivo mentre altre possono essere limitate.

##Utilizzo dei livelli delle prestazioni
Le raccolte di DocumentDB consentono di partizionare i dati basati su modelli di query e sulle esigenze in termini di prestazioni dell'applicazione. Per altre informazioni dettagliate sul [partizionamento dei dati](documentdb-partition-data.md) con DocumentDB consultare la documentazione relativa al partizionamento dei dati. Con l'indicizzazione automatica e il supporto delle query di DocumentDB, è molto comune collocare documenti eterogenei all'interno della stessa raccolta. Le considerazioni principali nello stabilire se è necessario usare raccolte separate per i dati includono:

- Query: una raccolta è l'ambito per l'esecuzione di query. Se è necessario eseguire una query in un set di documenti, i modelli di lettura più efficienti derivano dall'inserimento dei documenti in un'unica raccolta.
- Transazioni: una raccolta è il dominio di transazione per stored procedure e trigger. Tutte le transazioni sono limitate a una singola raccolta. 
- Prestazioni: a ogni raccolta è associato un livello delle prestazioni. Ciò garantisce che ogni raccolta offra prestazioni prevedibili tramite unità di richiesta riservate. È possibile allocare dati in più raccolte, con diversi livelli di prestazioni, in base alla frequenza di accesso.

> [AZURE.IMPORTANT]È importante comprendere che la fatturazione avverrà alle tariffe standard complete in base al numero di raccolte create all'interno dell'applicazione.

È consigliabile che l'applicazione usi un numero ridotto di raccolte, a meno che non si dispone di requisiti di archiviazione o throughput elevati. Assicurarsi di avere ben compreso i modelli di applicazione per la creazione di nuove raccolte. È possibile scegliere di riservare la creazione della raccolta come un'azione di gestione gestita all'esterno dell'applicazione. Analogamente, la regolazione del livello delle prestazioni per una raccolta cambierà la tariffa oraria con la quale la raccolta viene fatturata. Se l'applicazione consente di regolarli in modo dinamico, è opportuno monitorare i livelli delle prestazioni della raccolta.

##Modifica dei livelli di prestazioni tramite il portale di anteprima di Azure

Il portale di anteprima di Azure è una delle opzioni disponibili quando si gestiscono i livelli di prestazioni degli insiemi. Attenersi alla seguente procedura per modificare il livello di prestazioni di una raccolta dal portale di Azure.

1. Per esplorare il [**portale di anteprima di Azure**](https://portal.azure.com) dal browser.
2. Fare clic su **Sfoglia** dalla barra di spostamento sul lato sinistro.
3. Nello hub **Sfoglia**, fare clic su **account DocumentDB** sotto l’etichetta **filtrare in base a**.
4. Nel pannello **account DocumentDB**, fare clic sull'account DocumentDB che contiene la raccolta desiderata.
5. Nel pannello **account DocumentDB**, scorrere fino a visualizzare la lente **database** e fare clic sul database che contiene la raccolta desiderata. 
6. Nel pannello **Database** appena aperto, scorrere fino a visualizzare la lente **raccolte** e selezionare la raccolta desiderata.
7. Infine, all'interno del pannello **Insieme**, trovare e fare clic sul riquadro **Livello di prezzo** nella lente **Utilizzo**.
8. Nel pannello **scegliere il livello di prezzo**, scegliere il livello di prestazioni desiderato, quindi fare clic su **selezionare** nella parte inferiore del pannello. 

>[AZURE.NOTE]La modifica dei livelli di prestazioni di una raccolta può richiedere fino a 2 minuti.

![Modifica il livello di prezzo][1]

##Modifica dei livelli di prestazioni tramite il SDK di .NET

Un'altra opzione per la modifica dei livelli di prestazioni degli insiemi è tramite SDK. Questa sezione riguarda solo la modifica delle prestazioni di una raccolta di livello usando [.NET SDK](https://msdn.microsoft.com/library/azure/dn948556.aspx) ma il processo è simile per le altre [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). Se non si conosce il SDK di .NET, visitare la nostra [esercitazione introduttiva](documentdb-get-started.md).

Di seguito è riportato un frammento di codice per la modifica del tipo di offerta:

	//Fetch the resource to be updated
	Offer offer = client.CreateOfferQuery()
	                          .Where(r => r.ResourceLink == "collection selfLink")    
	                          .AsEnumerable()
	                          .SingleOrDefault();
	                          
	//Change the user mode to All
	offer.OfferType = "S3";
	                    
	//Now persist these changes to the database by replacing the original resource
	Offer updated = await client.ReplaceOfferAsync(offer);

Visitare [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) per visualizzare ulteriori esempi e ulteriori informazioni sui metodi della nostra offerta:

- [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
- [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
- [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
- [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx) 

##Passaggi successivi

Per altre informazioni sui prezzi e sulla gestione dei dati con Azure DocumentDB, esplorare queste risorse:
 
- [Prezzi di DocumentDB](http://azure.microsoft.com/pricing/details/documentdb/)
- [Gestire la capacità e le prestazioni di DocumentDB](documentdb-manage.md) 
- [Modellazione dei dati in DocumentDB](documentdb-modeling-data.md)
- [Partizionamento dei dati in DocumentDB](documentdb-partition-data.md)

Per altre informazioni su DocumentDB, vedere la [documentazione](http://azure.microsoft.com/documentation/services/documentdb/) relativa ad Azure DocumentDB.

[1]: ./media/documentdb-performance-levels/img1.png

<!---HONumber=August15_HO6-->
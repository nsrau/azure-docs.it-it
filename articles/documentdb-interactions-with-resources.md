<properties 
	pageTitle="Interazioni RESTful con risorse di DocumentDB | Azure" 
	description="Informazioni su come eseguire le interazioni RESTful con risorse di Microsoft Azure DocumentDB usando verbi HTTP." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="mimig"/>

#Interazioni RESTful con risorse di DocumentDB 

DocumentDB supporta l'uso di metodi HTTP per creare, leggere, sostituire, ottenere ed eliminare risorse di DocumentDB.

Dopo aver letto questo articolo, si riuscirà a rispondere alle domande seguenti:

- Come funzionano i metodi HTTP standard con le risorse di DocumentDB?
- Come si crea una nuova risorsa con POST?
- Come si registra una stored procedure con POST?
- In che modo DocumentDB supporta il controllo della concorrenza?
- Quali sono le opzioni di connettività per HTTPS e TCP?

##Panoramica dei verbi HTTP
Le risorse di DocumentDB supportano i seguenti verbi HTTP con la relativa interpretazione standard:

1.	POST significa creare una nuova risorsa elemento. 
2.	GET significa leggere un elemento esistente o una risorsa feed 
3.	PUT significa sostituire una risorsa elemento esistente 
4.	DELETE significa eliminare una risorsa elemento esistente
5.	HEAD significa GET senza il payload di risposta (cioè solo le intestazioni) 

>[AZURE.NOTE] In futuro è prevista l'aggiunta del supporto per gli aggiornamenti selettivi tramite PATCH.  

Come illustrato nel diagramma seguente, POST può essere rilasciato solo per una risorsa feed. PUT e DELETE possono essere rilasciati solo per una risorsa elemento. GET e HEAD possono essere rilasciati per risorse feed o per risorse elemento. 

![][1]  

**Modello di interazione che usa i metodi HTTP standard**

##Creare una nuova risorsa con POST 
Per acquisire maggiore familiarità con il modello di interazione, si consideri l'eventualità di creare una nuova risorsa (INSERT). Per creare una nuova risorsa è necessario inviare una richiesta HTTP POST con il corpo della richiesta contenente la rappresentazione della risorsa rispetto all'URI del feed contenitore a cui questa appartiene. L'unica proprietà obbligatoria per la richiesta è l'ID della risorsa.  

Per creare un nuovo database, ad esempio, si esegue il POST di una risorsa database (impostando la proprietà ID su un nome univoco) a /dbs. Analogamente, per creare una nuova raccolta, si esegue il POST di una risorsa raccolta a /dbs/_rid/colls/ e così via. La risposta contiene la risorsa completamente impegnata insieme alle proprietà generate dal sistema, compreso il collegamento _self della risorsa, che può essere usato per passare ad altre risorse. Come esempio di modello di interazione semplice basato su HTTP, un client può inviare una richiesta HTTP per creare un nuovo database all'interno di un account.  

	POST http://fabrikam.documents.azure.net/dbs
	{
	      "id":"MyDb"
	}

Il servizio DocumentDB risponde con un esito positivo e un codice di stato che indica l'avvenuta creazione del database.  

	[201 Created]
	{
	      "id": "MyDb",
	      "_rid": "UoEi5w==",
	      "_self": "dbs/MyDb/",
	      "_ts": 1407370063,
	      "_etag": "\"00002100-0000-0000-0000-50e71fda0000\"",
	      "_colls": "colls/",
	      "_users": "users/"
	}
  
##Registrare una stored procedure con POST
Per un altro esempio di creazione ed esecuzione di una risorsa, valutare la seguente stored procedure scritta interamente in JavaScript.   

	function sproc(docToCreate, addedPropertyName, addedPropertyValue) {
	    var collectionManager = getContext().getCollection();
	    collectionManager.createDocument(collectionManager.getSelfLink(), docToCreate,   
	    function(err, docCreated) {
	        if(err) throw new Error('Error while creating document: ' + err.message);
	        
	        docCreated.addedPropertyName = addedPropertyValue;
	        getContext().getResponse().setBody(docCreated);
	    });
	}

La stored procedure può essere registrata in una raccolta in MyDb mediante l'invio di un POST a /dbs/_rid-db/colls/_rid-coll/sprocs. 

	POST /dbs/MyDb/colls/MyColl/sprocs HTTP/1.1
	
	{
	  "id": "sproc1",
	  "body": "function (docToCreate, addedPropertyName, addedPropertyValue) {
	                var collectionManager = getContext().getCollection();
	                collectionManager.createDocument(collectionManager.getSelfLink(), 
	                            docToCreate, function(err, docCreated) {
	                    if(err) throw new Error('Error while creating document: ' + 
	                                                     err.message);
	                    
	                    docCreated.addedPropertyName = addedPropertyValue;
	                    getContext().getResponse().setBody(docCreated);
	                });
	            }"
	}
Il servizio DocumentDB risponde con un esito positivo e un codice di stato che indica l'avvenuta registrazione della stored procedure.  

	[201 Created]
	{
	      "id": "sproc1",
	      "_rid": "EoEi5w==",
	      "_self": "dbs/MyDb/colls/MyColl/sprocs/sproc1",
	      "_etag": "\"00002100-0000-0000-0000-50f71fda0000\"",
	       ...
	}

##Eseguire una stored procedure con POST
Infine, per eseguire la stored procedure nell'esempio riportato sopra, è necessario inviare un POST all'URI della risorsa della stored procedure (/dbs/_rid-db/colls/_rid-coll/sprocs/sproc1). Tale condizione è illustrata nel codice seguente.  

	POST /dbs/MyDb/colls/MyColl/sprocs/sproc1 HTTP/1.1
	 [ { "id": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]
 

Il servizio DocumentDB risponde nel modo seguente.  

	HTTP/1.1 200 OK
	 { 
	  "id": "TestDocument",  
	  "_rid": "ZTlcANiwqwIBAAAAAAAAAA==",
	  "_ts": 1407370063,
	  "_self": "dbs/ZTlcAA==/colls/ZTlcANiwqwI=/docs/ZTlcANiwqwIBAAAAAAAAAA==/",
	  "_etag": "00000900-0000-0000-0000-53e2c34f0000",
	  "_attachments": "attachments/",
	  "book": "Autumn of the Patriarch", 
	  "price": 200
	}

Si noti che il verbo POST può essere usato per creare una nuova risorsa, per eseguire una stored procedure e per inviare una query SQL. Per illustrare l'esecuzione della query SQL, considerare quanto segue.  

	POST /dbs/MyDb/colls/MyColl/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/sql
	
	SELECT * FROM root.children

Il servizio risponde con i risultati della query SQL.   

	HTTP/1.1 200 Ok
	x-ms-activity-id: 83f9992c-abae-4eb1-b8f0-9f2420c520d2
	x-ms-item-count: 2
	x-ms-session-token: 81
	x-ms-request-charge: 1.43
	Content-Length: 287
	
	{"_rid":"sehcAIE2Qy4=","Documents":[[{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}],[{"familyName":"Merriam","givenName":"Jesse","gender":"female","grade":1},{"familyName":"Miller","givenName":"Lisa","gender":"female","grade":8}]],"count":2}


##Uso di PUT, GET e DELETE
La sostituzione o la lettura di una risorsa si eseguono inviando rispettivamente verbi PUT (con un corpo di richiesta valido) e GET al collegamento _self della risorsa. Analogamente, l'eliminazione di una risorsa si ottiene inviando un verbo DELETE al collegamento _self della risorsa. Vale la pena di evidenziare che l'organizzazione gerarchica delle risorse nel modello di risorse di DocumentDB necessita del supporto per le eliminazioni propagate, grazie a cui l'eliminazione della risorsa proprietaria causa l'eliminazione delle risorse dipendenti. Le risorse dipendenti possono essere distribuite su nodi diversi da quelli delle risorse proprietarie e pertanto l'eliminazione potrebbe avvenire in modo differito. A prescindere dal meccanismo di garbage collection, nel momento in cui una risorsa viene eliminata, la quota viene istantaneamente liberata e resa disponibile per l'uso. Si noti che l'integrità referenziale viene conservata da parte del sistema. Ad esempio, non è possibile inserire una raccolta in un database che è stato eliminato o sostituito né eseguire query su un documento di una raccolta che non esiste più.  
 
L'invio di un verbo GET a un feed di risorse o la query su una raccolta potrebbe potenzialmente restituire milioni di elementi, rendendone ingestibile la materializzazione da parte del server e l'uso da parte dei client, nell'ambito di un singolo scambio di richiesta e risposta/round trip. Per risolvere il problema, DocumentDB consente ai client di impaginare i grandi feed una pagina per volta. I client possono usare l'intestazione di risposta [x-ms-continuationToken] come cursore per passare alla pagina successiva.   

##Controllo della concorrenza ottimistica
La maggior parte delle applicazioni Web si affida al controllo della concorrenza ottimistica basato su tag di entità per evitare i fastidiosi problemi legati alla perdita di aggiornamenti o alla perdita di eliminazioni. Il tag di entità è un timestamp logico e compatibile con HTTP associato a una risorsa. DocumentDB supporta nativamente il controllo della concorrenza ottimistica garantendo che ogni risposta HTTP contenga la versione associata (durevolmente) alla risorsa specifica. I conflitti del controllo della concorrenza vengono correttamente rilevati nei casi seguenti:  

1.	Se due client inviano simultaneamente richieste diverse mediante verbi PUT/DELETE a una risorsa con l'ultima versione della risorsa stessa (specificata tramite l'intestazione di richiesta [if-match]), il motore di database di DocumentDB le sottopone al controllo della concorrenza transazionale.
2.	Se un client presenta una versione non aggiornata della risorsa (specificata tramite l'intestazione di richiesta [if-match]), la richiesta verrà rifiutata.  

##Opzioni di connettività
DocumentDB espone un modello di indirizzamento logico secondo cui ogni risorsa ha un URI logico e stabile identificato nel proprio collegamento _self. Man mano che un sistema di archiviazione distribuita si estende nelle varie aree, le risorse dei vari account del database in DocumentDB vengono partizionate su numerosi computer e ogni partizione viene replicata per la disponibilità elevata. Le repliche che gestiscono le risorse di una determinata partizione effettuano la registrazione di indirizzi fisici. Anche se gli indirizzi fisici cambiano nel corso del tempo a causa di errori, i relativi indirizzi logici restano stabili e costanti. La traduzione da indirizzo fisico a logico viene mantenuta in una tabella di routing disponibile anche internamente come risorsa. DocumentDB espone due modalità di connettività:  

1.	**Modalità gateway:** i client sono esclusi dalla traduzione tra indirizzi logici e fisici e non ricevono dettagli relativi al routing. Essi si limitano a gestire gli URI e gli spostamenti RESTful nel modello di risorsa. I client inviano le richieste usando URI logici, mentre i computer perimetrali traducono l'URI logico nell'indirizzo fisico della replica che gestisce la risorsa e inoltra la richiesta. Con i computer perimetrali che memorizzano nella cache (e periodicamente aggiornano) la tabella di routing, il routing risulta estremamente efficiente. 
2.	**Modalità connettività diretta:** i client gestiscono direttamente la tabella di routing nel proprio spazio di elaborazione e la aggiornano periodicamente. Un client può connettersi direttamente alle repliche e ignorare i computer perimetrali.   


<table width="300">
    <tbody>
        <tr>
            <td width="120" valign="top">
                <p>
                    <strong>Modalità di connettività</strong>
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    <strong>Protocollo</strong>
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    <strong>Dettagli</strong>
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    <strong>SDK di DocumentDB</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="120" valign="top">
                <p>
                    Gateway
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    HTTPS
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    Le applicazioni si connettono direttamente con i nodi perimetrali usando URI logici. Ciò determina un hop di rete aggiuntivo
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    API REST
                </p>
                <p>
                    .NET, JavaScript, Node.js, Python
                </p>
            </td>
        </tr>
        <tr>
            <td width="120" valign="top">
                <p>
                    Connettività diretta
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    HTTPS e
                </p>
                <p>
                    TCP
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    Le applicazioni possono accedere direttamente alla tabella di routing ed eseguire il routing lato client per connettersi direttamente con le repliche.
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    .NET
                </p>
            </td>
        </tr>
    </tbody>
</table>

##Passaggi successivi
Vedere [Informazioni di riferimento sulle API REST di Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) per altre informazioni su come lavorare con le risorse usando l'API REST.

##Riferimenti
-   [Informazioni di riferimento sulle API REST di Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) 
-	REST [http://en.wikipedia.org/wiki/Representational_state_transfer](http://en.wikipedia.org/wiki/Representational_state_transfer)
-	Specifica JSON  [http://www.ietf.org/rfc/rfc4627.txt](http://www.ietf.org/rfc/rfc4627.txt)
-	Specifica HTTP [http://www.w3.org/Protocols/rfc2616/rfc2616.html](http://www.w3.org/Protocols/rfc2616/rfc2616.html)
-	Tag di entità [http://en.wikipedia.org/wiki/HTTP_ETag](http://en.wikipedia.org/wiki/HTTP_ETag)
-	[Esecuzione di query con DocumentDB](documentdb-sql-query.md)
-	[Riferimento SQL DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx)
-	[Programmazione con DocumentDB: Stored procedure, trigger e funzioni definite dall'utente](../documentdb-programming/)
-	[Documentazione di riferimento di DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx)


[1]: ./media/documentdb-interactions-with-resources/interactions-with-resources2.png

<!--HONumber=49-->
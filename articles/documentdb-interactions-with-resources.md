<properties title="Interact with DocumentDB resources" pageTitle="Interagire con risorse di DocumentDB | Azure" description="DocumentDB provides client SDKs for .NET, Python, Node.js and JavaScript - all of which are simple wrappers over the underlying REST APIs." metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />

#Interagire con risorse di DocumentDB 
DocumentDB offre un modello di programmazione RESTful su HTTP semplice e aperto. Nella versione di anteprima DocumentDB fornisce SDK del client per .NET, Python, Node.js e JavaScript. Tutti questi SDK sono semplici wrapper per le API REST sottostanti. In versioni future saranno disponibili anche SDK per C++ e Java. Microsoft incoraggia gli sviluppatori a scrivere SDK specifici per i propri ambienti di programmazione e a condividerli con la community, ora che gli SDK sono stati resi generalmente disponibili. 

>[AZURE.NOTE] DocumentDB offre anche un protocollo TCP a efficienza elevata, con un modello di comunicazione di tipo RESTful disponibile tramite l'SDK del client .NET.  

##Risorse
Le entità gestite da DocumentDB sono definite **risorse**, identificate in modo univoco dal rispettivo URI logico. Gli sviluppatori possono interagire con le risorse usando verbi HTTP standard, intestazioni di richiesta/risposta e codici di stato. Come illustrato nel diagramma seguente, il **modello di risorse** di DocumentDB è costituito da un insieme di risorse disponibili in un account di database e indirizzabili singolarmente tramite un URI logico e stabile. Un insieme di risorse viene definito **feed** nel presente documento.  

![][1]  

##Modello di risorse gerarchico in un account di database ##

I clienti di DocumentDB eseguono prima di tutto il provisioning di un **account di database** di DocumentDB usando la sottoscrizione di Azure. Un account di database è costituito da un insieme di **database**, ognuno dei quali include più **raccolte**, che possono contenere **stored procedure, trigger, UDF, documenti** e **allegati correlati**. Un database include anche **utenti** associati, ognuno dei quali ha un insieme di **autorizzazioni** per accedere alle diverse raccolte, stored procedure, trigger, UDF, documenti o allegati disponibili. Mentre i database, gli utenti, le autorizzazioni e le raccolte sono risorse definite dal sistema con schemi noti, i documenti e gli allegati includono contenuto JSON arbitrario definito dagli utenti.  

|Risorsa 	|Descrizione
|-----------|-----------
|Account di database	|Un account di database è associato a una o più unità di capacità, che rappresentano il provisioning di archiviazione documenti e la velocità effettiva, ovvero un insieme di database e di archiviazione BLOB. È possibile creare uno o più account di database usando la sottoscrizione di Azure.
|Database	|Un database è un contenitore logico di archiviazione documenti partizionato nelle raccolte. Un database è anche un contenitore degli utenti
|Utente	|Spazio dei nomi logico per le autorizzazioni relative alla definizione dell'ambito e al partizionamento. 
|Autorizzazione	|Token di autorizzazione associato a un utente per l'accesso autorizzato a una risorsa specifica.
|Raccolta	|Una raccolta è un contenitore di documenti JSON e di logica dell'applicazione JavaScript associata.
|Stored Procedure	|Logica dell'applicazione scritta in JavaScript, registrata con una raccolta ed eseguita a livello di transazione all'interno del motore di database.
|Trigger	|Logica dell'applicazione scritta in JavaScript per la modellazione degli effetti collaterali associati a operazioni di inserimento, sostituzione o eliminazione.
|UDF	|Logica dell'applicazione senza effetti collaterali scritta in JavaScript. Consente di modellare un operatore query personalizzato e quindi di estendere il linguaggio di query di base di DocumentDB.
|Documento	|Contenuto JSON definito dall'utente (arbitrario). Per impostazione predefinita, non è necessario definire alcuno schema o fornire alcun indice secondario per tutti i documenti aggiunti a una raccolta.
|Allegato	|Gli allegati sono documenti speciali contenenti riferimenti e metadati associati a supporti o a una risorsa di archiviazione BLOB esterna. Lo sviluppatore può scegliere se gestire il BLOB tramite DocumentDB o se archiviarlo presso un provider di servizi BLOB esterno come OneDrive, Dropbox e così via. 

###Confronto tra risorse definite dal sistema e risorse definite dall'utente
Tutte le risorse quali account di database, database, raccolte, utenti, autorizzazioni, stored procedure, trigger e funzioni UDF hanno uno schema fisso e sono definite *risorse di sistema*. Per le risorse quali documenti e allegati, invece, non sono previste restrizioni a livello di schema. Si tratta di *risorse definite dall'utente*. In DocumentDB le risorse definite dal sistema e definite dall'utente sono rappresentate e gestite come risorse JSON conformi allo standard. Tutte le risorse, di sistema o definite dall'utente, presentano le proprietà comuni indicate di seguito.

>[AZURE.NOTE] Si noti che, nell'implementazione JSON, tutte le proprietà generate dal sistema in una risorsa hanno come prefisso un carattere di sottolineatura (_).  


<table width="500"> 
<tbody>
<tr>
<td valign="top" ><p><b>Proprietà </b></p></td>
<td valign="top" ><p><b>Impostabile dall'utente o generata dal sistema?</b></p></td>
<td valign="top" ><p><b>Scopo</b></p></td>
</tr>

<tr>
<td valign="top" ><p>_rid</p></td>
<td valign="top" ><p>Generata dal sistema</p></td>
<td valign="top" ><p>Identificatore gerarchico della risorsa, univoco e generato dal sistema</p></td>
</tr>

<tr>
<td valign="top" ><p>_etag</p></td>
<td valign="top" ><p>Generata dal sistema</p></td>
<td valign="top" ><p>etag della risorsa richiesta per il controllo della concorrenza ottimistica</p></td>
</tr>

<tr>
<td valign="top" ><p>_ts</p></td>
<td valign="top" ><p>Generata dal sistema</p></td>
<td valign="top" ><p>Ultimo timestamp aggiornato della risorsa</p></td>
</tr>

<tr>
<td valign="top" ><p>_self</p></td>
<td valign="top" ><p>Generata dal sistema</p></td>
<td valign="top" ><p>URI univoco indirizzabile della risorsa </p></td>
</tr>

<tr>
<td valign="top" ><p>id</p></td>
<td valign="top" ><p>Impostabile dall'utente</p></td>
<td valign="top" ><p>Nome univoco della risorsa impostabile dall'utente </p></td>
</tr>

</tbody>
</table>


###Rappresentazione delle risorse
DocumentDB non impone estensioni proprietarie o codifiche speciali allo standard JSON e funziona con i documenti JSON conformi a tale standard.  
 
###Indirizzamento di una risorsa
Tutte le risorse sono indirizzabili mediante URI. Il valore della proprietà **_self** di una risorsa rappresenta l'URI relativo di tale risorsa. Il formato dell'URI è dato dai segmenti del percorso /<feed>/{_rid}:  

|Value of the _self	|Description
|-------------------|-----------
|/dbs	|feed of databases under a database account
|/dbs/{_rid-db}	|Database with the unique id property with the value {_rid-db}
|/dbs/{_rid-db}/colls/	|feed of collections under a database 
|/dbs/{_rid-db}/colls/{_rid-coll}	|Collection with the unique id property with the value {_rid-coll}
|/dbs/{_rid-db}/users/	|feed of users under a database 
|/dbs/{_rid-db}/users/{_rid-user}	|User with the unique id property with the value {_rid-user}
|/dbs/{_rid-db}/users/{_rid-user}/permissions	|feed of permissions under a database 
|/dbs/{_rid-db}/users/{_rid-user}/permissions/{_rid-permission}	|Permission with the unique id property with the value {_rid-permission}  
  

Una risorsa ha anche un nome utente univoco esposto mediante la proprietà ID della risorsa stessa. L'ID è una stringa definita dall'utente di una lunghezza fino a 256 caratteri ed è univoco nell'ambito del contesto di una risorsa padre specifica. I valori della proprietà ID di tutti i documenti di una raccolta specificata, ad esempio, sono univoci ma non vi è garanzia che lo siano per tutte le raccolte. Analogamente, i valori della proprietà ID di tutte le autorizzazioni per un determinato utente sono univoci ma non vi è garanzia che lo siano per tutti gli utenti. La proprietà _rid viene usata per costruire il collegamento _self indirizzabile di una risorsa.   

Ogni risorsa ha anche un identificatore della risorsa gerarchico generato dal sistema (anche definito RID) disponibile tramite la proprietà _rid. Il RID codifica l'intera gerarchia di una determinata risorsa ed è una rappresentazione interna molto utile da usare per imporre l'integrità referenziale secondo un metodo distribuito. Il RID è univoco all'interno di un account di database e viene usato internamente da DocumentDB per un routing efficiente senza necessità di eseguire ricerche su più partizioni. 

I valori delle proprietà _self e  _rid sono entrambi rappresentazioni alternate e canoniche di una risorsa.  
 
##Modello di interazione di base
Le risorse supportano i seguenti verbi HTTP  con la relativa interpretazione standard:

>[AZURE.NOTE] In futuro è prevista l'aggiunta del supporto per gli aggiornamenti selettivi tramite PATCH  

1.	POST significa creare una nuova risorsa elemento. 
2.	GET significa leggere un elemento esistente o una risorsa feed 
3.	PUT significa sostituire una risorsa elemento esistente 
4.	DELETE significa eliminare una risorsa  elemento esistente
5.	HEAD significa GET senza il payload di risposta (cioè solo le intestazioni)

In versioni future, le risorse elemento supporteranno anche il verbo PATCH.  

Come illustrato nella figura seguente, POST può essere rilasciato solo per una risorsa feed. PUT e DELETE possono essere rilasciati per una risorsa elemento. GET e HEAD possono essere rilasciati sia per risorse feed che per risorse elemento. 

![][2]  

**Modello di interazione che usa i verbi HTTP standard**

Per acquisire maggiore familiarità con il modello di interazione, si consideri l'eventualità di creare una nuova risorsa (INSERT). Per creare una nuova risorsa è necessario inviare una richiesta HTTP POST con il corpo della richiesta contenente la rappresentazione della risorsa rispetto all'URI del feed contenitore a cui questa appartiene. L'unica proprietà obbligatoria per la richiesta è l'ID della risorsa.  

Per creare un nuovo database, ad esempio, si esegue il POST di una risorsa database (impostando la proprietà ID su un nome univoco) a /dbs. Analogamente, per creare una nuova raccolta, si esegue il POST di una risorsa raccolta a /dbs/_rid/colls/ e così via. La risposta conterrà la risorsa completamente impegnata insieme alle proprietà generate dal sistema, compreso il collegamento _self della risorsa, che può essere usato per passare ad altre risorse. Come esempio di modello di interazione semplice basato su HTTP, un client può inviare una richiesta HTTP per creare un nuovo database all'interno di un account:  

	POST http://fabrikam.documents.azure.net/dbs
	{
	      "id":"MyDb"
	}
DocumentDB risponde con un esito positivo e un codice di stato che indica l'avvenuta creazione del database.  

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

Il servizio DocumentDB risponde con un esito positivo e un codice di stato che indica l'avvenuta creazione del database.  

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

Infine, per eseguire la stored procedure nell'esempio riportato sopra, è necessario inviare un POST all'URI della risorsa della stored procedure (/dbs/_rid-db/colls/_rid-coll/sprocs/sproc1). L'operazione è illustrata di seguito:  

	POST /dbs/MyDb/colls/MyColl/sprocs/sproc1 HTTP/1.1
	 [ { "id": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]
 

Il servizio DocumentDB risponderà nel modo seguente:  

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

Si noti che il verbo POST può essere usato per creare una nuova risorsa, per eseguire una stored procedure o per inviare una query SQL. Per illustrare l'esecuzione della query SQL, considerare quanto segue:  

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


La sostituzione o la lettura di una risorsa si eseguono inviando rispettivamente verbi PUT (con un corpo di richiesta valido) e GET al collegamento _self della risorsa. Analogamente, l'eliminazione di una risorsa si ottiene inviando un verbo DELETE al collegamento _self della risorsa. Vale la pena di evidenziare che l'organizzazione gerarchica delle risorse nel modello di risorse di DocumentDB necessita del supporto per le eliminazioni propagate, grazie a cui l'eliminazione della risorsa proprietaria causa l'eliminazione delle risorse dipendenti. Le risorse dipendenti possono essere distribuite su nodi diversi da quelli delle risorse proprietarie e pertanto l'eliminazione potrebbe avvenire in modo differito. A prescindere dal meccanismo di garbage collection, nel momento in cui una risorsa viene eliminata, la quota viene istantaneamente liberata e resa disponibile per l'uso. Si noti che l'integrità referenziale viene conservata da parte del sistema. Ad esempio, non è possibile inserire una raccolta in un database che è stato eliminato o sostituito né eseguire query su un documento di una raccolta che non esiste più.  
 
L'invio di un verbo GET a un feed di risorse, o la query su una raccolta, potrebbe potenzialmente restituire milioni di elementi, rendendone ingestibile la materializzazione da parte del server e l'uso da parte dei client, nell'ambito di un singolo scambio round trip/richiesta. Per risolvere il problema, DocumentDB consente ai client di impaginare i grandi feed una pagina per volta. I client possono usare l'intestazione di risposta [x-ms-continuationToken] come cursore per passare per passare alla pagina successiva.   

##Controllo della concorrenza ottimistica
La maggior parte delle applicazioni Web si affida al controllo della concorrenza ottimistica basato su tag di entità per evitare i fastidiosi problemi legati alla perdita di aggiornamenti o alla perdita di eliminazioni. Il tag di entità è un timestamp logico e compatibile con HTTP associato a una risorsa. DocumentDB supporta nativamente il controllo della concorrenza ottimistica garantendo che ogni risposta HTTP contenga la versione associata (durevolmente) alla risorsa specifica. I conflitti del controllo della concorrenza vengono correttamente rilevati nei casi seguenti:  

1.	Se due clienti inviano simultaneamente richieste diverse mediante verbi PUT/DELETE a una risorsa con l'ultima versione della risorsa stessa (specificata tramite l'intestazione di richiesta [if-match]) il motore di database di DocumentDB le sottopone al controllo della concorrenza transazionale.
2.	Se un client presenta una versione non aggiornata della risorsa (specificata tramite l'intestazione di richiesta [if-match]), la richiesta verrà rifiutata.  

##Opzioni di connettività
DocumentDB espone un modello di indirizzamento logico secondo cui ogni risorsa ha un URI logico e stabile identificato nel proprio collegamento _self. Man mano che un sistema di archiviazione distribuita si estende nelle varie aree, le risorse dei vari account del database in DocumentDB vengono partizionate su numerosi computer e ogni partizione viene replicata per la disponibilità elevata. Le repliche che gestiscono le risorse di una determinata partizione effettuano la registrazione di indirizzi fisici. Anche se gli indirizzi fisici cambiano nel corso del tempo a causa di errori, i relativi indirizzi logici restano stabili e costanti. La traduzione da indirizzo fisico a logico viene mantenuta in una tabella di routing disponibile anche internamente come risorsa. DocumentDB espone due modalità di connettività:  

1.	**Modalità gateway:** I client sono esclusi dalla traduzione tra indirizzi logici e fisici e non ricevono dettagli relativi al routing. Essi si limitano a gestire gli URI e gli spostamenti RESTful nel modello di risorsa. I client inviano le richieste usando URI logici, mentre i computer perimetrali traducono l'URI logico nell'indirizzo fisico della replica che gestisce la risorsa e inoltra la richiesta. Con i computer perimetrali che memorizzano nella cache (e periodicamente aggiornano) la tabella di routing, il routing risulta estremamente efficiente. 
2.	**Modalità connettività diretta:** I client gestiscono direttamente la tabella di routing nel proprio spazio di elaborazione e la aggiornano periodicamente. Un client può connettersi direttamente alle repliche e ignorare i computer perimetrali.   



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
                    <strong>Protocol</strong>
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

##Riferimenti
-	REST [http://en.wikipedia.org/wiki/Representational_state_transfer](http://en.wikipedia.org/wiki/Representational_state_transfer)
-	Specifica JSON  [http://-www.ietf.org/rfc/rfc4627.txt](http://-www.ietf.org/rfc/rfc4627.txt)
-	Specifica HTTP [http://www.w3.org/Protocols/rfc2616/rfc2616.html](http://www.w3.org/Protocols/rfc2616/rfc2616.html)
-	Tag di entità [http://en.wikipedia.org/wiki/HTTP_ETag](http://en.wikipedia.org/wiki/HTTP_ETag)
-	[Query DocumentDB](/documentation/articles/documentdb-sql-query/)
-	[Riferimento SQL DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=510612)
-	[Programmazione con DocumentDB: stored procedure, trigger e UDF](/documentation/articles/documentdb-programming/)
-	[Documentazione di riferimento di DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402384)


 [1]: ./media/documentdb-interactions-with-resources/interactions-with-resources1.png
 [2]: ./media/documentdb-interactions-with-resources/interactions-with-resources2.png

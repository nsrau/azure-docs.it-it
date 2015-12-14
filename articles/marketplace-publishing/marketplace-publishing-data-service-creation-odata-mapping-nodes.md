<properties
   pageTitle="Guida alla creazione di un servizio dati per il Marketplace | Microsoft Azure"
   description="Istruzioni dettagliate su come creare, certificare e distribuire un servizio dati per l'acquisto in Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

   <tags
      ms.service="marketplace-publishing"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="11/30/2015"
      ms.author="hascipio; avikova" />

# Informazioni sullo schema di nodi per il mapping di un servizio Web esistente a OData tramite CSDL
Questo documento chiarisce la struttura dei nodi per il mapping di un protocollo OData a CSDL. È importante notare che la struttura dei nodi è costituita da XML ben formato. Pertanto, durante la progettazione del mapping a OData è possibile applicare gli schemi radice, padre e figlio.

## Elementi ignorati
Di seguito sono elencati gli elementi CSDL di alto livello (nodi XML) che non vengono usati dal back-end di Azure Marketplace durante l'importazione dei metadati del servizio Web. Tali elementi possono essere presenti, ma vengono ignorati.

| Elemento | Ambito |
|----|----|
| Using | Nodo, sottonodi e tutti gli attributi |
| Documentation | Nodo, sottonodi e tutti gli attributi |
| ComplexType | Nodo, sottonodi e tutti gli attributi |
| Association | Nodo, sottonodi e tutti gli attributi |
| Property esteso | Nodo, sottonodi e tutti gli attributi |
| EntityContainer | Vengono ignorati solo i seguenti attributi: *extends* e *AssociationSet* |
| Schema | Vengono ignorati solo i seguenti attributi: *Namespace* |
| FunctionImport | Vengono ignorati solo i seguenti attributi: *Mode* (si presume il valore predefinito ln) |
| EntityType | Vengono ignorati solo i seguenti sottonodi: *Key* e *PropertyRef* |

Di seguito vengono illustrate in dettaglio le modifiche (elementi aggiunti e ignorati) apportate ai diversi nodi XML CSDL.

## Nodo FunctionImport
Un nodo FunctionImport rappresenta un solo URL (punto di ingresso) che espone un servizio all'utente finale. Il nodo consente di descrivere la modalità di indirizzamento dell'URL, i parametri disponibili per l'utente finale e il modo in cui questi parametri vengono forniti.

Informazioni dettagliate su questo nodo sono disponibili all'indirizzo [http://msdn.microsoft.com/library/cc716710(v=vs.100).aspx][MSDNFunctionImportLink]

[MSDNFunctionImportLink]: 'http://msdn.microsoft.com/library/cc716710(v=vs.100).aspx'

Di seguito sono descritti gli attributi aggiuntivi o le aggiunte agli attributi esposte dal nodo FunctionImport:

**d:BaseUri**: modello URI per la risorsa REST esposto a Marketplace. Marketplace utilizza il modello per costruire query a fronte del servizio Web REST. Il modello URI contiene segnaposti per i parametri nel formato {nomeParametro}, dove nomeParametro corrisponde al nome del parametro. Esempio: versioneAPI={versioneAPI}. I parametri possono comparire come parametri URI o come parte del percorso URI. Se compaiono all'interno del percorso sono sempre obbligatori (non possono essere contrassegnati come nullable). *Esempio:* `d:BaseUri="http://api.MyWeb.com/Site/{url}/v1/visits?start={start}&amp;end={end}&amp;ApiKey=3fadcaa&amp;Format=XML"`

**Name**: il nome della funzione importata. Non può corrispondere ad altri nomi definiti nel codice CSDL. Esempio: Name="GetModelUsageFile"

**EntitySet** *(facoltativo)*: se la funzione restituisce una raccolta di tipi di entità, il valore di **EntitySet** deve corrispondere al set di entità a cui la raccolta appartiene. In caso contrario, l'attributo **EntitySet** non deve essere utilizzato. *Esempio:* `EntitySet="GetUsageStatisticsEntitySet"`

**ReturnType** *(facoltativo)*: specifica il tipo di elementi restituiti dall'URI. Non utilizzare questo attributo se la funzione non restituisce un valore. Di seguito sono elencati i tipi supportati:

 - **Collection (<Entity type name>)**: specifica una raccolta di tipi di entità definiti. Il nome è presente nell'attributo Name del nodo EntityType. Un esempio è Collection(WXC.HourlyResult).
 - **Raw (<mime type>)**: specifica un documento o un BLOB non elaborato restituito all'utente. Un esempio è Raw(image/jpeg). Altri esempi:

  - ReturnType="Raw(text/plain)"
  - ReturnType="Collection(sage.DeleteAllUsageFilesEntity)"*

**d:Paging**: specifica la modalità di gestione della funzionalità di paging da parte della risorsa REST. I valori dei parametri sono espressi all'interno di parentesi graffe, ad esempio page={$page}&itemsperpage={$size}. Le opzioni disponibili sono:

- **None:** il paging non è disponibile
- **Skip:** il paging è espresso tramite “skip” e “take” logici (top). Skip ignora M elementi, quindi take restituisce gli N elementi successivi. Valore parametro: $skip
- **Take:** restituisce gli N elementi successivi. Valore parametro: $take
- **PageSize:** il paging viene espresso tramite una pagina e una dimensione logiche (elementi per pagina). La pagina corrisponde alla pagina corrente restituita. Valore parametro: $page
- **Size:**: rappresenta il numero di elementi restituiti per ciascuna pagina. Valore parametro: $size

**d:AllowedHttpMethods** *(facoltativo)*: specifica quale verbo viene gestito dalla risorsa REST. Limita inoltre il verbo accettato al valore specificato. Valore predefinito = POST. *Esempio:* `d:AllowedHttpMethods="GET"` Le opzioni disponibili sono:

- **GET:** utilizzato di solito per restituire dati
- **POST:** utilizzato di solito per inserire nuovi dati
- **PUT:** utilizzato di solito per aggiornare dati
- **DELETE:** utilizzato di solito per eliminare dati

Ulteriori nodi figlio, non trattati dalla documentazione CSDL, all'interno del nodo FunctionImport sono:

**d:RequestBody** *(facoltativo)*: il corpo della richiesta è utilizzato per indicare che la richiesta prevede l'invio di un corpo. I parametri possono essere specificati nel corpo della richiesta e sono espressi all'interno di parentesi graffe, ad esempio {nomeParametro}. Di questi parametri viene eseguito il mapping dall'input dell'utente all'interno del corpo che viene trasferito al servizio del provider di contenuti. L'elemento requestBody ha un attributo denominato httpMethod. L'attributo consente due valori:

- **POST**: utilizzato se la richiesta è una richiesta HTTP POST
- **GET**: utilizzato se la richiesta è una richiesta HTTP GET

	Esempio:

        `<d:RequestBody d:httpMethod="POST">
        <![CDATA[
        <req1:Request xmlns:r1="http://schemas.mysite.com//generic/requests/1" Version="1.0">
        <req1:Query>{Query}</req1:Query><req1:AppId>D453474</req1:AppId>
        <req:DestinationSchemas><req1:Schema>Generic.RequestResponse[1.0]</req1:Schema>
        </req1:DestinationSchemas>
        </req1: Request>
        ]]>
        </d:RequestBody>`

**d:Namespaces** e **d:Namespace**: questo nodo descrive gli spazi dei nomi definiti nel codice XML restituito dall'importazione della funzione (endpoint URI). Il codice XML restituito dal servizio back-end può contenere un numero qualsiasi di spazi dei nomi per distinguere il contenuto che viene restituito. **Tutti gli spazi dei nomi, se utilizzati in query XPath d:Map o d:Match devono essere elencati.** Il nodo d:Namespaces contiene un set o un elenco di nodi d:Namespace. Ognuno di questi elenca un solo spazio dei nomi utilizzato nella risposta del servizio back-end. Di seguito sono elencati gli attributi del nodo d:Namespace:

-	**d:Prefix**: prefisso dello spazio dei nomi, come appare nei risultati XML restituiti dal servizio, ad esempio f:FirstName, f:LastName, dove f è il prefisso.
- **d:Uri**: URI completo dello spazio dei nomi utilizzato nel documento di risultati. Rappresenta il valore in cui il prefisso viene risolto in runtime.

**d:ErrorHandling** *(facoltativo)*: questo nodo contiene le condizioni per la gestione degli errori. Ciascuna condizione viene convalidata rispetto al risultato restituito dal servizio del provider di contenuti. Se una condizione corrisponde al codice di errore HTTP proposto, all'utente finale viene restituito un messaggio di errore.

**d:ErrorHandling** *(facoltativo)* e **d:Condition** *(facoltativo)*: un nodo di condizione contiene una condizione che viene verificata a fronte del risultato restituito dal servizio del provider di contenuti. Di seguito sono riportati gli attributi **obbligatori**:

- **d:Match**: espressione XPath che verifica se un nodo o un valore specifico è presente nel codice XML di output del provider di contenuti. L'espressione XPath viene eseguita a fronte dell'output e deve restituire true se la condizione è una corrispondenza o false in caso contrario.
- **d:HttpStatusCode**: codice di stato HTTP che deve essere restituito da Marketplace nel caso in cui la condizione corrisponda. Marketplace segnala gli errori all'utente tramite codici di stato HTTP. Un elenco di codici di stato HTTP è disponibile all'indirizzo http://en.wikipedia.org/wiki/HTTP_status_code
- **d:ErrorMessage**: il messaggio di errore restituito, con il codice di stato HTTP, all'utente finale. Deve trattarsi di un messaggio di errore descrittivo che non contiene segreti.

**d:Title** *(facoltativo)*: consente di descrivere il titolo della funzione. Il valore per il titolo proviene:

- Dall'attributo map facoltativo (xpath) che specifica dove trovare il titolo nella risposta restituita dalla richiesta di servizio.
- In alternativa, dal titolo specificato come valore del nodo.

**d:Rights** *(facoltativo)*: i diritti (ad esempio il copyright) associati alla funzione. Il valore per i diritti proviene:

- Dall'attributo map facoltativo (xpath) che specifica dove trovare i diritti nella risposta restituita dalla richiesta di servizio.
-	In alternativa, dai diritti specificati come valore del nodo.

**d:Description** *(facoltativo)*: breve descrizione della funzione. Il valore per la descrizione proviene:

- Dall'attributo map facoltativo (xpath) che specifica dove trovare la descrizione nella risposta restituita dalla richiesta di servizio.
- In alternativa, dalla descrizione specificata come valore del nodo.

**d:EmitSelfLink**: *vedere l'esempio riportato in precedenza relativo a FunctionImport per 'Paging' tramite i dati restituiti*

**d:EncodeParameterValue**: estensione facoltativa di OData

**d:QueryResourceCost**: estensione facoltativa di OData

**d:Map**: estensione facoltativa di OData

**d:Headers**: estensione facoltativa di OData

**d:Headers**: estensione facoltativa di OData

**d:Value**: estensione facoltativa di OData

**d:HttpStatusCode**: estensione facoltativa di OData

**d:ErrorMessage**: estensione facoltativa di OData

## Nodo Parameter

Questo nodo rappresenta un solo parametro esposto come parte del modello URI o del corpo della richiesta specificato nel nodo FunctionImport.

Una pagina dettagliata molto utile sul nodo "Elemento Parameter"è disponibile all'indirizzo [http://msdn.microsoft.com/library/ee473431.aspx](http://msdn.microsoft.com/library/ee473431.aspx). Utilizzare l'elenco a discesa **Other Version** per selezionare una versione diversa se necessario per visualizzare la documentazione. *Esempio:* `<Parameter Name="Query" Nullable="false" Mode="In" Type="String" d:Description="Query" d:SampleValues="Rudy Duck" d:EncodeParameterValue="true" MaxLength="255" FixedLength="false" Unicode="false" annotation:StoreGeneratedPattern="Identity"/>`

| Attributo | Obbligatorio | Valore |
|----|----|----|
| Nome | Sì | Nome del parametro. Fa distinzione tra maiuscole e minuscole. Seguire maiuscole e minuscole di BaseUri. **Esempio:** `<Property Name="IsDormant" Type="Byte" />` |
| Tipo | Sì | Tipo di parametro. Il valore deve essere di tipo **EDMSimpleType** o di un tipo complesso compreso nell'ambito del modello. Per ulteriori informazioni, vedere "6 Tipi di parametri e proprietà supportati". Fa distinzione tra maiuscole e minuscole. Il primo carattere è maiuscolo, gli altri sono minuscoli. Vedere anche [http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx][MSDNParameterLink]. **Esempio:** `<Property Name="LimitedPartnershipID " Type="Int32" />` |
| Mode | No | **In**, Out o InOut, a seconda che il parametro sia un parametro di input, di output o di input/output. In Azure Marketplace è disponibile solo "In". **Esempio:** `<Parameter Name="StudentID" Mode="In" Type="Int32" />` |
| MaxLength | No | Lunghezza massima consentita per il parametro. **Esempio:** `<Property Name="URI" Type="String" MaxLength="100" FixedLength="false" Unicode="false" />` |
| Precision | No | Precisione del parametro **Esempio:** `<Property Name="PreviousDate" Type="DateTime" Precision="0" />` |
| Scale | No | Scalabilità del parametro **Esempio:** `<Property Name="SICCode" Type="Decimal" Precision="10" Scale="0" />` |

[MSDNParameterLink]: 'http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx'

Di seguito sono riportati gli attributi che sono stati aggiunti alla specifica CSDL:

| Attributo | Descrizione |
|----|----|
| **d:Regex** *(facoltativo)* | Istruzione regex utilizzata per convalidare il valore di input per il parametro. Se il valore di input non corrisponde all'istruzione il valore viene rifiutato. Questo attributo consente inoltre di specificare un set di valori possibili, ad esempio ^[0-9]+?$ per consentire solo numeri. **Esempio:** `<Parameter Name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters" d:SampleValues="George|John|Thomas|James"/>` |
| **d:Enum** *(facoltativo)* | Elenco di valori validi per il parametro, separati da una barra verticale. Il tipo dei valori deve corrispondere al tipo definito per il parametro. Esempio: `english|metric|raw`. All'interno dell'interfaccia utente (service explorer) l'attributo Enum viene visualizzato come un elenco a discesa selezionabile di parametri. **Esempio:** `<Parameter Name="Duration" Type="String" Mode="In" Nullable="true" d:Enum="1year|5years|10years"/>` |
| **d: Nullable** *(facoltativo)* | Consente di definire se un parametro può essere null. Il valore predefinito è true. I parametri che vengono esposti come parte del percorso nel modello URI, tuttavia, non possono essere null. Se l'attributo è impostato su false per questi parametri, l'input dell'utente viene ignorato. Consente di definire se un parametro può essere null. Il valore predefinito è true. I parametri che vengono esposti come parte del percorso nel modello URI, tuttavia, non possono essere null. Se l'attributo è impostato su false per questi parametri, l'input dell'utente viene ignorato. **Esempio:** `<Parameter Name="BikeType" Type="String" Mode="In" Nullable="false"/>` |
| **d:SampleValue** *(facoltativo)* | Valore di esempio da visualizzare come nota nell'interfaccia utente del client. È possibile aggiungere più valori, separati da barra verticale, "|”. **Esempio:** `<Parameter Name="BikeOwner" Type="String" Mode="In" d:SampleValues="George|John|Thomas|James"/>` |
| **d:UriTemplate** | |

## Nodo EntityType

Questo nodo rappresenta uno dei tipi restituiti da Marketplace all'utente finale. Il nodo contiene inoltre il mapping dell'output restituito dal servizio del provider di contenuti ai valori restituiti all'utente finale.

Informazioni dettagliate su questo nodo sono disponibili all'indirizzo [http://msdn.microsoft.com/library/bb399206.aspx](http://msdn.microsoft.com/library/bb399206.aspx). Utilizzare l'elenco a discesa **Other Version** per selezionare una versione diversa se necessario per visualizzare la documentazione.

| Nome attributo | Obbligatorio | Valore |
|----|----|----|
| Nome | Sì | Nome del tipo di entità. **Esempio:** `<EntityType Name="ListOfAllEntities" d:Map="//EntityModel">` |
| BaseType | No | Nome di un altro tipo di entità, che rappresenta il tipo di base del tipo di entità in corso di definizione. **Esempio:** `<EntityType Name="PhoneRecord" BaseType="dqs:RequestRecord">` |

Di seguito sono riportati gli attributi che sono stati aggiunti alla specifica CSDL:

**d:Map**: espressione XPath eseguita a fronte dell'output del servizio. Si presuppone che l'output del servizio contenga un set di elementi che si ripetono, analogamente a un feed ATOM in cui è presente un set di nodi di voce che si ripetono. Ognuno di questi nodi ripetuti contiene un record. L'espressione XPath viene quindi specificata in modo che punti al singolo nodo ripetuto nel risultato del servizio del provider di contenuti che contiene i valori per un singolo record. Esempio di output del servizio:

        `<foo>
          <bar> … content … </bar>
          <bar> … content … </bar>
          <bar> … content … </bar>
        </foo>`

In questo caso l'espressione XPath sarebbe /foo/bar, poiché ciascun nodo della barra corrisponde al nodo ripetuto nell'output e contiene il contenuto effettivo restituito all'utente finale.

**Key**: questo attributo viene ignorato da Marketplace. I servizi Web basati su REST, in genere, non espongono una chiave primaria.


## Nodo Property

Questo nodo contiene una proprietà del record.

Informazioni dettagliate su questo nodo sono disponibili all'indirizzo [http://msdn.microsoft.com/library/bb399546.aspx](http://msdn.microsoft.com/library/bb399546.aspx). Utilizzare l'elenco a discesa **Other Version** per selezionare una versione diversa se necessario per visualizzare la documentazione. *Esempio:* `<EntityType Name="MetaDataEntityType" d:Map="/MyXMLPath">
        <Property Name="Name" 	Type="String" Nullable="true" d:Map="./Service/Name" d:IsPrimaryKey="true" DefaultValue=”Joe Doh” MaxLength="25" FixedLength="true" />
		...
        </EntityType>`

| AttributeName | Obbligatorio | Valore |
|----|----|----|
| Nome | Sì | Nome della proprietà. |
| Tipo | Sì | Tipo del valore della proprietà. Il tipo del valore della proprietà deve corrispondere a **EDMSimpleType** o a un tipo complesso (indicato da un nome completo) nell'ambito del modello. Per ulteriori informazioni, vedere i tipi di modello concettuale (CSDL). |
| Nullable | No | **True** (valore predefinito) o **False**, a seconda che la proprietà possa avere valore null. Nota: nella versione di CSDL indicata dallo spazio dei nomi [http://schemas.microsoft.com/ado/2006/04/edm](http://schemas.microsoft.com/ado/2006/04/edm), una proprietà di tipo complesso deve avere Nullable="False". |
| DefaultValue | No | Valore predefinito della proprietà. |
|MaxLength | No | Lunghezza massima del valore della proprietà. |
| FixedLength | No | **True** o **False**, a seconda che il valore della proprietà venga archiviato come stringa di lunghezza fissa. |
| Precision | No | Indica il numero massimo di cifre da mantenere nel valore numerico. |
| Scale | No | Numero massimo di posizioni decimali da mantenere nel valore numerico. |
| Unicode | No | **True** o **False**, a seconda che il valore della proprietà venga archiviato come stringa Unicode. |
| Collation | No | Stringa che specifica la sequenza di collazione da utilizzare nell'origine dati. |
| ConcurrencyMode | No | **None** (valore predefinito) o **Fixed**. Se il valore è impostato su **Fixed**, il valore della proprietà verrà utilizzato nei controlli della concorrenza ottimistica. |

Di seguito sono riportati altri attributi che sono stati aggiunti alla specifica CSDL:

**d:Map**: espressione XPath eseguita a fronte dell'output del servizio. Estrae una sola proprietà dell'output. L'espressione XPath specificata è relativa al nodo ripetuto che è stato selezionato nell'XPath del nodo EntityType. È inoltre possibile specificare un XPath assoluto per consentire l'inclusione di una risorsa statica in ciascuno dei nodi di output, come, ad esempio, le informazioni di copyright, che vengono trovate solo una volta nell'output originale del servizio ma che devono essere presenti in ogni riga nell'output OData. Esempio dal servizio:

        `<foo>
          <bar>
           <baz0>… value …</baz0>
           <baz1>… value …</baz1>
           <baz2>… value …</baz2>
          </bar>
        </foo>`

Per ottenere il nodo baz0 dal servizio del provider di contenuti, l'espressione XPath qui deve corrispondere a ./bar/baz0.

**d:CharMaxLength**: consente di specificare la lunghezza massima per il tipo stringa. Vedere Esempio di CSDL di DataService

**d:IsPrimaryKey**: indica se la colonna è la chiave primaria nella tabella o della vista. Vedere Esempio di CSDL di DataService.

**d:isExposed**: determina se lo schema della tabella è esposto. In genere questo attributo è impostato su true. Vedere Esempio di CSDL di DataService

**d:IsView** *(facoltativo)*: true se si basa su una vista anziché su una tabella. Vedere Esempio di CSDL di DataService

**d:Tableschema**: vedere Esempio di CSDL di DataService

**d:ColumnName**: nome della colonna nella tabella o nella vista. Vedere Esempio di CSDL di DataService

**d:IsReturned**: valore booleano che determina se il servizio espone questo valore al client. Vedere Esempio di CSDL di DataService

**d:IsQueryable**: valore booleano che determina se la colonna può essere utilizzata in una query di database. Vedere Esempio di CSDL di DataService

**d:OrdinalPosition**: posizione numerica della colonna nella tabella o nella vista. Corrisponde a x, dove x è compreso tra 1 e il numero di colonne nella tabella. Vedere Esempio di CSDL di DataService

**d:DatabaseDataType**: tipo di dati della colonna nel database, ad esempio tipo di dati SQL. Vedere Esempio di CSDL di DataService

## Tipi di parametri e proprietà supportati
Di seguito sono indicati i tipi supportati per i parametri e le proprietà. C'è distinzione tra maiuscole e minuscole.

| Tipi primitivi | Descrizione |
|----|----|
| Null | Rappresenta l'assenza di un valore |
| Boolean | Rappresenta il concetto matematico di logica a valori binari|
| Byte | Valore intero senza segno a 8 bit|
|DateTime| Rappresenta una data e un'ora con valori compresi tra le ore 0.00.00 del 1° gennaio 1753 d. C. e le 23.59.59 del dicembre 9999 d.C.|
|Decimal | Rappresenta valori numerici con precisione e scala fisse. Questo tipo può descrivere un valore numerico compreso tra - 10 ^ 255 + 1 e + 10 ^ 255 -1|
| Double | Rappresenta un numero a virgola mobile con precisione a 15 cifre che può rappresentare valori compresi approssimativamente tra approssimativo compreso tra ± 2,23e -308 e ± 1,79e +308. **Utilizzare Decimal a causa di un problema di esportazione di Excel**|
| Single | Rappresenta un numero a virgola mobile con precisione a 7 cifre che può rappresentare valori compresi approssimativamente tra approssimativo compreso tra ± 1,18e -38 e ± 3,40e +38.|
|Guid |Rappresenta un valore di identificatore univoco a 16 byte (128 bit) |
|Int16|Rappresenta un valore intero con segno a 16 bit |
|Int32|Rappresenta un valore intero con segno a 32 bit |
|Int64|Rappresenta un valore intero con segno a 64 bit |
|String | Rappresenta dati di tipo carattere a lunghezza fissa o variabile |


## Vedere anche
- Per comprendere meglio nel suo complesso il processo del mapping OData e il suo scopo, nonché per esaminare le definizioni, le strutture e le istruzioni, leggere l'articolo [relativo al mapping OData del servizio dati](marketplace-publishing-data-service-creation-odata-mapping.md).
- Per esaminare gli esempi, consultare il codice di esempio e apprendere la sintassi del codice e il contesto, leggere l'articolo [relativo agli esempi di mapping OData del servizio dati](marketplace-publishing-data-service-creation-odata-mapping-examples.md).
- Per ripristinare il percorso prescritto per la pubblicazione di un servizio dati in Azure Marketplace, leggere l'articolo di [guida alla pubblicazione del servizio dati](marketplace-publishing-data-service-creation.md).

<!---HONumber=AcomDC_1203_2015-->
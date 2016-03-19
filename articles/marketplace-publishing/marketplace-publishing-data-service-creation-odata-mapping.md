<properties
   pageTitle="Guida alla creazione di un servizio dati per il Marketplace | Microsoft Azure"
   description="Istruzioni dettagliate su come creare, certificare e distribuire un servizio dati per l'acquisto in Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="01/04/2016"
      ms.author="hascipio; avikova" />

# Mapping di un servizio Web esistente in OData tramite CSDL

In questo articolo viene fornita una panoramica sull'utilizzo di un CSDL per eseguire il mapping di un servizio esistente in uno compatibile con OData. Viene spiegato come creare il documento di mapping (CSDL) che trasforma la richiesta di input dal client tramite una chiamata del servizio e restituisce l'output (dati) al client tramite un feed compatibile con OData. Microsoft Azure Marketplace espone i servizi agli utenti finali mediante il protocollo OData. I servizi dei provider di contenuti (proprietari di dati) vengono esposti in varie forme, ad esempio REST, SOAP e così via.

## Che cos'è un CSDL e la relativa struttura?
Un CSDL (Conceptual Schema Definition Language) è una specifica che definisce la modalità di descrizione del servizio Web o del servizio di database nel linguaggio XML comune in Azure Marketplace.

Panoramica semplice del **flusso della richiesta:**

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

Il **flusso di dati** è nella direzione opposta:

  `Client <- Azure Marketplace <- Content Provider’s WebService`

La **figura 1** illustra come un client ottiene i dati da un provider di contenuti (il servizio dell'utente) navigando attraverso Azure Marketplace. Il CSDL viene utilizzato dal componente di mapping o trasformazione per gestire la richiesta e trasmettere i dati tra servizi del provider di contenuti e il client che ha effettuato la richiesta.

*Figura 1: flusso dettagliato dal client che effettua la richiesta al provider di contenuti tramite Azure Marketplace*

  ![disegno](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

Per informazioni generali su Atom, Atom Pub e il protocollo OData su cui vengono sviluppate le estensioni di Azure Marketplace, consultare: [http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)

Estratto dal collegamento precedente: *"Lo scopo del protocollo Open Data (d'ora in avanti indicato come OData) è fornire un protocollo basato su REST per operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete) su risorse esposte come servizi dati. Un "servizio dati" è un endpoint in cui i dati sono esposti da una o più "raccolte", ciascuna con zero o più "voci", costituite da coppie nome-valore tipizzate. OData è pubblicato da Microsoft secondo gli standard OASIS (Organization for the Advancement of Structured Information Standards, Organizzazione per la promozione delle norme sulle informazioni strutturate) affinché chiunque possa creare server, client o strumenti senza restrizioni o il pagamento di diritti".*

### Tre componenti critici che devono essere definiti in CSDL sono:

- L'**endpoint** del provider di servizi, l'indirizzo Web (URI) del servizio
- I **parametri dei dati** trasmessi come input per il provider di servizi, le definizioni dei parametri inviati al servizio del provider di contenuti fino al tipo di dati.
- Lo **schema** dei dati restituiti al servizio che effettua la richiesta, lo schema dei dati inviati dal servizio del provider di contenuti, compresi i tipi di contenitore, raccolte/tabelle, variabili/colonne e dati.

Nel diagramma seguente viene illustrata una panoramica del flusso dal quale il client immette l'istruzione di OData (chiamata al servizio Web del provider di contenuti) per ottenere risultati o dati.

  ![disegno](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### Passaggi:

1. Il client invia la richiesta tramite chiamata del servizio con parametri di input definiti in XML in Azure Marketplace
2. CSDL viene utilizzato per convalidare la chiamata del servizio.
	- La chiamata del servizio formattata viene quindi inviata al servizio del provider di contenuti da Azure Marketplace
3. Viene avviato WebService, che esegue le azioni del verbo HTTP, ad esempio GET, i dati vengono restituiti ad Azure Marketplace in cui i dati richiesti (se presenti) vengono esposti in formato XML al client tramite il mapping definito nel CSDL.
4. Al client vengono inviati i dati (se presenti) in formato JSON o XML

## Definizioni

### OData ATOM Pub

Un'estensione di ATOM Pub in cui ciascuna voce rappresenta una riga di un risultato. La parte del contenuto della voce è stata migliorata per contenere i valori della riga, come coppie chiave-valore. Sono disponibili ulteriori informazioni all'indirizzo: [https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)

### CSDL - Conceptual Schema Definition Language

Consente di definire le funzioni (SPROC) e le entità che vengono esposte tramite un database. Sono disponibili ulteriori informazioni all'indirizzo: [http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)

> [AZURE.TIP]Se l'articolo non viene visualizzato, fare clic sull'elenco a discesa **Altre versioni** e selezionare una versione.

### EDM - Entry Data Model, Modello di dati di movimento
- Panoramica: [http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx][OverviewLink]
[OverviewLink]:http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx
- Anteprima: [http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx][PreviewLink]
[PreviewLink]:http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx
- Tipi di dati: [http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx][DataTypesLink]
[DataTypesLink]:http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx

Di seguito viene illustrato il flusso di dettagli da sinistra a destra da cui il client immette l'istruzione di OData (chiamata al servizio Web del provider di contenuti) per ottenere risultati o dati:

  ![disegno](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)


## Nozioni di base su CSDL

Un CSDL (Conceptual Schema Definition Language) è una specifica che definisce la modalità di descrizione del servizio Web o del servizio di database nel linguaggio XML comune in Azure Marketplace. CSDL descrive i componenti cruciali che **consentono la trasmissione dei dati dall'origine dati ad Azure Marketplace.** I componenti principali sono descritti di seguito:

- Informazioni sull'interfaccia che descrivono tutte le funzioni disponibili pubblicamente (nodo FunctionImport)
- Informazioni sul tipo di dati per tutte le richieste di messaggi (input) e risposte ai messaggi (output) (nodi EntityContainer/EntitySet/EntityType)
- Informazioni sull'associazione circa il protocollo di trasporto utilizzato (nodo Header)
- Informazioni sull'indirizzo per l'individuazione del servizio specificato (attributo BaseURI)

In breve, il CSDL rappresenta un contratto indipendente dalla piattaforma e dal linguaggio tra chi richiede il servizio e il provider del servizio stesso. Il CSDL consente al client di individuare un servizio Web o del database e richiamare una qualsiasi delle relative funzioni disponibili pubblicamente.

### Correlazione di un CSDL a un database o a una raccolta
**La specifica CSDL**

CSDL è la grammatica XML che descrive un servizio Web. La specifica stessa è suddivisa in 4 elementi principali: EntitySet, FunctionImport, NameSpace ed EntityType.

Rendere questa astrazione più comprensibile consente di correlare un CSDL a una tabella.

Tenere presente:

  CSDL rappresenta un contratto indipendente dalla piattaforma e dal linguaggio tra chi **richiede il servizio** e il **provider del servizio stesso**. Il CSDL consente al **client** di individuare un **servizio Web o del database** e richiamare una qualsiasi delle relative **funzioni** disponibili pubblicamente.

Per un servizio dati, le quattro parti di un CSDL possono essere considerate in termini di un database, una tabella, una colonna e una Stored Procedure.

Con riferimento a quanto segue per un servizio dati:

- EntityContainer ~ = database
- EntitySet ~ = tabella
- EntityType ~ = colonne
- FunctionImport ~ = Stored Procedure

**Verbi HTTP consentiti**: GET restituisce i valori del database (restituzione di una raccolta). POST viene utilizzato per trasmettere i dati al database e, facoltativamente, restituire i valori da esso provenienti (creazione di una nuova voce nella raccolta, restituzione di ID/URI). DELETE elimina i dati dal database (eliminazione di una raccolta). PUT aggiorna i dati in un database (sostituzione o creazione di una raccolta)

## Documento metadati/mapping

Il documento metadati/mapping viene utilizzato per il mapping dei servizi Web esistenti del provider di contenuti, affinché vengano esposti come servizio Web OData dal sistema di Azure Marketplace. Tale documento si basa su CSDL e implementa alcune estensioni in quest'ultimo per soddisfare le esigenze dei servizi Web basati su REST esposti tramite Azure Marketplace. Le estensioni sono disponibili nello spazio dei nomi [http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04).

A seguire, un esempio di CSDL: (copiare e incollare il CSDL di esempio seguente in un editor XML e modificarlo affinché corrisponda al proprio servizio. Quindi incollare il contenuto nel mapping CSDL, nella scheda DataService, durante la creazione del servizio nel [portale di pubblicazione di Azure Marketplace](https://publish.windowsazure.com)).

**Termini:** riguardo la correlazione fra i termini di CSDL e quelli dell'interfaccia utente del [portale di pubblicazione](https://publish.windowsazure.com) (PPUI) - il "Titolo" dell'offerta in PPUI si riferisce a MyWebOffer - MyCompany in PPUI si riferisce al **nome visualizzato dell'editore** nell'interfaccia utente del [Centro per sviluppatori Microsoft](http://dev.windows.com/registration?accountprogram=azure) - l'API si riferisce a un servizio dati o Web (un piano nella PPUI)

**Gerarchia:** una società (provider di contenuti) è titolare delle offerte con piani, ossia servizi, che si allineano a un'API.

### Esempio di CSDL di WebService

Si connette a un servizio che espone un endpoint dell'applicazione Web (ad esempio un'applicazione C#)

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
        	<!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

        	<!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID"	d:IsPrimaryKey="True" Type="Int32"	Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount"	Type="Double"	Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"	Type="String"	Nullable="false" d:Map="./City"/>
        <Property Name="State"	Type="String"	Nullable="false" d:Map="./State"/>
        <Property Name="Zip"	Type="Int32"	Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"	Type="DateTime"	Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [AZURE.TIP]Altri esempi di CSDL di servizi Web sono disponibili nell'articolo relativo agli [esempi di mapping di un servizio Web esistente in OData tramite CSDL](marketplace-publishing-data-service-creation-odata-mapping-examples.md)

###Esempio di CSDL di DataService

Si connette a un servizio che espone una vista o tabella di database come un endpoint. L'esempio seguente illustra due API per CSDL API basato su database (possibilità di utilizzare viste anziché tabelle).

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        	Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
        	Map is the schema.tabel or schema.view
        	dals.TableName is the table Name
        	Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
        	dals:IsExposed determines if the table schema is exposed (generally true).
        	dals:IsView (optional) true if this is based on a view rather than a table
        	dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
        	dals:ColumnName is the name of the column in the table /view.
        	Type is the emd.SimpleType
        	Nullable determines if NULL is a valid output value
        	dals.CharMaxLenght is the maximum length of the output value
        	Name is the name of the Property and is exposed to the client facing UI
        	dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
        	IsQueryable is the Boolean that determines if the column can be used in a database query
        	(For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
        	dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
        	dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## Vedere anche
- Per ricevere formazione e informazioni sui nodi specifici e i relativi parametri, leggere l'articolo relativo ai [nodi di mapping di OData del servizio dati](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) per definizioni, spiegazioni, esempi e casi di utilizzo contestuali.
- Per esaminare gli esempi, leggere l'articolo relativo agli [esempi di mapping di OData del servizio dati](marketplace-publishing-data-service-creation-odata-mapping-examples.md) per consultare il codice di esempio e comprendere il contesto e la sintassi del codice.
- Per ripristinare il percorso prescritto per la pubblicazione di un servizio dati in Azure Marketplace, leggere questo articolo [Guida alla pubblicazione del servizio dati](marketplace-publishing-data-service-creation.md).

<!---HONumber=AcomDC_0114_2016--->
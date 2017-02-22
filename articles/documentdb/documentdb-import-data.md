---
title: Strumento di migrazione del database per DocumentDB | Documentazione Microsoft
description: Informazioni sull&quot;uso degli strumenti open source di migrazione dati di DocumentDB per importare dati in DocumentDB da varie origini, tra cui file JSON, CSV, MongoDB, SQL Server, Archivio tabelle e Amazon DynamoDB. Conversione da CSV a JSON.
keywords: da csv a json, strumenti di migrazione dei database, convertire un csv in jso
services: documentdb
author: andrewhoh
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: d173581d-782a-445c-98d9-5e3c49b00e25
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: ed44ca2076860128b175888748cdaa8794c2310d
ms.openlocfilehash: fd3ebcaa82952815ad31decd1b44cf6d41365d2f


---
# <a name="import-data-to-documentdb-with-the-database-migration-tool"></a>Importare dati in DocumentDB con lo strumento di migrazione del database
Questo articolo mostra come usare lo strumento di migrazione dati di DocumentDB open source ufficiale per importare dati in [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) da diverse origini, tra cui file JSON, file CSV, SQL, MongoDB, archiviazione tabelle di Azure e raccolte DocumentDB.

Se si importano dati in un account DocumentDB con supporto per MongoDB, seguire le istruzioni disponibili in [Migrate data to DocumentDB with protocol support for MongoDB](documentdb-mongodb-migrate.md) (Eseguire la migrazione di dati in DocumentDB con supporto del protocollo per MongoDB).

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:  

* Come è possibile importare i dati di file JSON, file CSV, di SQL Server o di MongoDB in DocumentDB?
* Come è possibile importare i dati da archiviazione tabelle di Azure, Amazon DynamoDB e HBasein DocumentDB?
* Come è possibile eseguire la migrazione dei dati tra le raccolte DocumentDB?

## <a name="a-idprerequisitesaprerequisites"></a><a id="Prerequisites"></a>Prerequisiti
Prima di seguire le istruzioni di questo articolo, verificare che siano installati i seguenti elementi:

* [Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) o versione successiva.

## <a name="a-idoverviewlaoverview-of-the-documentdb-data-migration-tool"></a><a id="Overviewl"></a>Panoramica dello strumento di migrazione dati di DocumentDB
Lo strumento di migrazione dati di DocumentDB è una soluzione open source che importa dati in DocumentDB da svariate origini, tra cui:

* File JSON
* MongoDB
* SQL Server
* File CSV
* Archiviazione tabelle di Azure
* DynamoDB Amazon
* HBase
* Raccolte DocumentDB

Lo strumento di importazione, anche se include un'interfaccia utente grafica (dtui.exe), può essere gestito anche dalla riga di comando (dt.exe). Infatti, una speciale opzione consente di inviare il comando associato dopo aver configurato un'operazione di importazione nell'interfaccia utente. I dati di origine tabulari (ad esempio, file CSV o SQL Server) possono essere trasformati in modo da poter creare relazioni gerarchiche (documenti secondari) durante l'importazione. Continuare a leggere per saperne di più sulle opzioni di origine, sulle righe di comando di esempio per l'importazione da ogni origine, sulle opzioni di destinazione e sulla visualizzazione dei risultati di importazione.

## <a name="a-idinstallainstalling-the-documentdb-data-migration-tool"></a><a id="Install"></a>Installazione dello strumento di migrazione dati di DocumentDB
Il codice sorgente dello strumento di migrazione è disponibile su GitHub in [questo repository](https://github.com/azure/azure-documentdb-datamigrationtool) e una versione compilata è disponibile nell'[Area download Microsoft](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d). È possibile compilare la soluzione o semplicemente scaricare ed estrarre la versione compilata nella directory desiderata. Eseguire quindi:

* **Dtui.exe**: versione con interfaccia grafica dello strumento
* **Dt.exe**: versione con riga di comando dello strumento

## <a name="a-idjsonaimport-json-files"></a><a id="JSON"></a>Importare file JSON
L'opzione dell'utilità di importazione dell'origine file JSON consente di importare uno o più file JSON di singoli documenti o file JSON contenenti ciascuno una matrice di documenti JSON. Quando si aggiungono le cartelle contenenti i file JSON da importare, è possibile eseguire una ricerca ricorsiva dei file nelle sottocartelle.

![Schermata delle opzioni dell'origine file JSON - Strumenti di migrazione del database](./media/documentdb-import-data/jsonsource.png)

Ecco alcuni esempi di riga di comando per importare file JSON:

    #Import a single JSON file
    dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory of JSON files
    dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

    #Import a directory (including sub-directories) of JSON files
    dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

    #Import a directory (single), directory (recursive), and individual JSON files
    dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

    #Import a single JSON file and partition the data across 4 collections
    dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500

## <a name="a-idmongodbaimport-from-mongodb"></a><a id="MongoDB"></a>Importare da MongoDB

> [!IMPORTANT]
> Se si importano dati in un account DocumentDB con supporto per MongoDB, seguire queste [istruzioni](documentdb-mongodb-migrate.md).
> 
> 

L'opzione dell'utilità di importazione dell'origine MongoDB consente di importare da una singola raccolta MongoDB e, facoltativamente, di filtrare i documenti usando una query e/o di modificare la struttura di documenti usando una proiezione.  

![Schermata delle opzioni dell'origine MongoDB - Confronto tra documentdb e mongodb](./media/documentdb-import-data/mongodbsource.png)

La stringa di connessione è nel formato standard di MongoDB:

    mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>

> [!NOTE]
> Usare il comando Verify per assicurarsi che l'istanza di MongoDB specificata nel campo della stringa di connessione sia accessibile.
> 
> 

Immettere il nome della raccolta da cui verranno importati i dati. Se si preferisce, si può specificare o fornire un file per una query (ad esempio, {pop: {$gt:5000}}) e/o la proiezione (ad esempio, {loc:0}) sia per filtrare che per determinare i dati da importare.

Ecco alcuni esempi di riga di comando per importare da MongoDB:

    #Import all documents from a MongoDB collection
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

    #Import documents from a MongoDB collection which match the query and exclude the loc field
    dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500

## <a name="a-idmongodbexportaimport-mongodb-export-files"></a><a id="MongoDBExport"></a>Importare file di esportazione MongoDB

> [!IMPORTANT]
> Se si importano dati in un account DocumentDB con supporto per MongoDB, seguire queste [istruzioni](documentdb-mongodb-migrate.md).
> 
> 

L'opzione dell'utilità di importazione dell'origine file JSON di esportazione MongoDB consente di importare uno o più file JSON prodotti dall'utilità mongoexport.  

![Schermata delle opzioni dell'origine di esportazione MongoDB - Confronto tra documentdb e mongodb](./media/documentdb-import-data/mongodbexportsource.png)

Quando si aggiungono le cartelle contenenti i file JSON di esportazione MongoDB da importare, è possibile eseguire una ricerca ricorsiva dei file nelle sottocartelle.

Ecco un esempio di riga di comando per importare dai file JSON di esportazione MongoDB:

    dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500

## <a name="a-idsqlaimport-from-sql-server"></a><a id="SQL"></a>Importare da SQL Server
L'opzione dell'utilità di importazione dell'origine SQL consente di importare da un singolo database SQL Server e, facoltativamente, di filtrare i record da importare usando una query. Inoltre, è possibile modificare la struttura di documenti specificando un separatore di annidamento, di cui si parlerà tra poco.  

![Schermata delle opzioni dell'origine SQL - Strumenti di migrazione del database](./media/documentdb-import-data/sqlexportsource.png)

Il formato della stringa di connessione è il formato della stringa di connessione SQL standard.

> [!NOTE]
> Usare il comando Verify per assicurarsi che l'istanza di SQL Server specificata nel campo della stringa di connessione sia accessibile.
> 
> 

La proprietà del separatore di annidamento viene usata per creare relazioni gerarchiche (documenti secondari) durante l'importazione. Considerare la query SQL seguente:

*select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'*

Che restituisce i risultati (parziali) seguenti:

![Schermata dei risultati della query SQL](./media/documentdb-import-data/sqlqueryresults.png)

Si notino gli alias come Address.AddressType e Address.Location.StateProvinceName. Specificando un separatore di annidamento ".", lo strumento di importazione crea i documenti secondari Address e Address.Location durante l'importazione. Ecco un esempio di un documento risultante in DocumentDB:

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*

Ecco alcuni esempi di riga di comando per importare da SQL Server:

    #Import records from SQL which match a query
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

    #Import records from sql which match a query and create hierarchical relationships
    dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500

## <a name="a-idcsvaimport-csv-files---convert-csv-to-json"></a><a id="CSV"></a>Importare file CSV - Convertire file CSV in formato JSON
L'opzione dell'utilità di importazione dell'origine file CSV consente di importare uno o più file CSV. Quando si aggiungono le cartelle contenenti i file CSV da importare, è possibile eseguire una ricerca ricorsiva dei file nelle sottocartelle.

![Schermata delle opzioni dell'origine CSV - Da CSV a JSON](media/documentdb-import-data/csvsource.png)

Come per l'origine SQL, la proprietà del separatore di annidamento può essere usata per creare relazioni gerarchiche (documenti secondari) durante l'importazione. Si consideri la seguente riga di intestazione CSV e le righe di dati:

![Schermata dei record di esempio CSV - Da CSV a JSON](./media/documentdb-import-data/csvsample.png)

Si notino gli alias come DomainInfo.Domain_Name e RedirectInfo.Redirecting. Specificando un separatore di annidamento ".", lo strumento di importazione creerà i documenti secondari DomainInfo e RedirectInfo durante l'importazione. Ecco un esempio di un documento risultante in DocumentDB:

*{ "DomainInfo": { "Domain_Name": "ACUS.GOV", "Domain_Name_Address": "http://www.ACUS.GOV" }, "Federal Agency": "Administrative Conference of the United States", "RedirectInfo": { "Redirecting": "0", "Redirect_Destination": "" }, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d" }*

Lo strumento di importazione proverà a dedurre le informazioni sul tipo per i valori non racchiusi tra virgolette nei file CSV (i valori tra virgolette vengono sempre considerati come stringhe).  I tipi vengono identificati nell'ordine seguente: tipo numerico, datetime, booleano.  

Esistono altri due aspetti da considerare per l'importazione CSV:

1. Per impostazione predefinita, nei valori non racchiusi tra virgolette vengono sempre rimossi spazi e tabulazioni, mentre i valori tra virgolette vengono mantenuti così come sono. È possibile eseguire l'override di questo comportamento con la casella di controllo Taglia valori tra virgolette o con l'opzione della riga di comando /s.TrimQuoted.
2. Per impostazione predefinita, un valore Null non racchiuso tra virgolette viene considerato come valore Null. È possibile eseguire l'override di questo comportamento (ad esempio considerando un valore Null non racchiuso tra virgolette come stringa "Null") con la casella di controllo Considera valore NULL non racchiuso tra virgolette come stringa o con l'opzione della riga di comando /s.NoUnquotedNulls.

Ecco un esempio di riga di comando per l'importazione CSV:

    dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500

## <a name="a-idazuretablesourceaimport-from-azure-table-storage"></a><a id="AzureTableSource"></a>Importare da Archiviazione tabelle di Azure
L'opzione dell'utilità di importazione dell'origine Archiviazione tabelle di Azure consente di importare da una singola tabella di Archiviazione tabelle di Azure e, facoltativamente, di filtrare le entità tabelle da importare.  

![Schermata delle opzioni dell'origine Archiviazione tabelle di Azure](./media/documentdb-import-data/azuretablesource.png)

Il formato della stringa di connessione di Archiviazione tabelle di Azure è:

    DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;

> [!NOTE]
> Usare il comando Verify per assicurarsi che l'istanza di Archiviazione tabelle di Azure specificata nel campo della stringa di connessione sia accessibile.
> 
> 

Immettere il nome della tabella di Azure da cui verranno importati i dati. Se si preferisce, si può specificare un [filtro](https://msdn.microsoft.com/library/azure/ff683669.aspx).

L'opzione dell'utilità di importazione dell'origine Archiviazione tabelle di Azure presenta le seguenti opzioni aggiuntive:

1. Include Internal Fields
   1. All: include tutti i campi interni (PartitionKey, RowKey e Timestamp)
   2. None: esclude tutti i campi interni
   3. RowKey: include solo il campo RowKey
2. Select Columns
   1. I filtri di Archiviazione tabelle di Azure non supportano le proiezioni. Per importare solo specifiche proprietà delle entità tabelle di Azure, aggiungerle all'elenco Select Columns. Tutte le altre proprietà delle entità verranno ignorate.

Ecco un esempio di riga di comando per importare da Archiviazione tabelle di Azure:

    dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500

## <a name="a-iddynamodbsourceaimport-from-amazon-dynamodb"></a><a id="DynamoDBSource"></a>Importa da Amazon DynamoDB
L'opzione dell'utilità di importazione DynamoDB Amazon origine consente di importare da una singola tabella DynamoDB Amazon e filtrare le entità da importare. Sono disponibili vari modelli in modo che l'impostazione di un'importazione è più semplice possibile.

![Schermata delle opzioni dell'origine Amazon DynamoDB - Strumenti di migrazione del database](./media/documentdb-import-data/dynamodbsource1.png)

![Schermata delle opzioni dell'origine Amazon DynamoDB - Strumenti di migrazione del database](./media/documentdb-import-data/dynamodbsource2.png)

Il formato della stringa di connessione DynamoDB di Amazon è:

    ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;

> [!NOTE]
> Usare il comando Verify per assicurarsi che l'istanza di MongoDB specificata nel campo della stringa di connessione sia accessibile.
> 
> 

Ecco un esempio di riga di comando per importare da Amazon DynamoDB:

    dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500

## <a name="a-idblobimportaimport-files-from-azure-blob-storage"></a><a id="BlobImport"></a>Importare i file dall'archiviazione BLOB di Azure
Il file JSON, i file di esportazione MongoDB e le opzioni dell'utilità di importazione di codice sorgente file CSV consentono di importare uno o più file dall'archiviazione Blob di Azure. Dopo aver specificato una URL del contenitore Blob e una chiave di Account, è sufficiente fornire un'espressione regolare per selezionare i file da importare.

![Schermata delle opzioni dell'origine file JSON](./media/documentdb-import-data/blobsource.png)

Ecco un esempio di riga di comando per importare file JSON dall'archiviazione Blob di Azure:

    dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:doctest

## <a name="a-iddocumentdbsourceaimport-from-documentdb"></a><a id="DocumentDBSource"></a>Importare da DocumentDB
L'opzione dell'utilità di importazione dell'origine DocumentDB consente di importare i dati da una o più raccolte DocumentDB e, facoltativamente, di filtrare i documenti usando una query.  

![Schermata delle opzioni dell'origine DocumentDB](./media/documentdb-import-data/documentdbsource.png)

Il formato della stringa di connessione di DocumentDB è:

    AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

La stringa di connessione dell'account DocumentDB può essere recuperata dal pannello Chiavi del portale di Azure, come descritto in [Come gestire un account DocumentDB](documentdb-manage-account.md), tuttavia il nome del database deve essere aggiunto alla stringa di connessione nel formato seguente:

    Database=<DocumentDB Database>;

> [!NOTE]
> Usare il comando Verify per assicurarsi che l'istanza di DocumentDB specificata nel campo della stringa di connessione sia accessibile.
> 
> 

Per importare da un'unica raccolta DocumentDB, immettere il nome della raccolta da cui verranno importati i dati. Per importare da più raccolte DocumentDB, fornire un'espressione regolare che corrisponda a uno o più nomi di raccolta (ad esempio, collection01 | collection02 | collection03). Se si preferisce, si può specificare o fornire un file per una query sia per filtrare che per determinare i dati da importare.

> [!NOTE]
> Poiché il campo della raccolta accetta le espressioni regolari, se si importa da un'unica raccolta il cui nome contiene caratteri di espressioni regolari, tali caratteri dovranno essere preceduti da un carattere di escape.
> 
> 

L'opzione dell'utilità di importazione dell'origine DocumentDB presenta le seguenti opzioni avanzate:

1. Include Internal Fields: specifica se includere o no le proprietà di sistema dei documenti DocumentDB nell'esportazione (ad esempio, _rid, _ts). 
2. Number of Retries on Failure: specifica quante volte ritentare la connessione a DocumentDB in caso di errori temporanei (ad esempio, un'interruzione della connettività di rete).
3. Retry Interval: specifica quanto attendere prima di ritentare la connessione a DocumentDB in caso di errori temporanei (ad esempio, un'interruzione della connettività di rete).
4. Connection Mode: specifica la modalità di connessione da usare con DocumentDB. Le scelte disponibili sono DirectTcp, DirectHttps e Gateway. Le modalità di connessione diretta sono più veloci, mentre la modalità gateway si integra più facilmente con il firewall perché usa solo la porta 443.

![Schermata delle opzioni avanzate dell'origine DocumentDB](./media/documentdb-import-data/documentdbsourceoptions.png)

> [!TIP]
> Per impostazione predefinita, la modalità di connessione dello strumento di importazione è DirectTcp. Se si verificano problemi con il firewall, passare alla modalità di connessione Gateway, che richiede solo la porta 443.
> 
> 

Ecco alcuni esempi di riga di comando per importare da DocumentDB:

    #Migrate data from one DocumentDB collection to another DocumentDB collections
    dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

    #Migrate data from multiple DocumentDB collections to a single DocumentDB collection
    dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

    #Export a DocumentDB collection to a JSON file
    dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionThroughput:2500

> [!TIP]
> Lo strumento di importazione dei dati di DocumentDB supporta anche l'importazione di dati dall'[emulatore DocumentDB](documentdb-nosql-local-emulator.md). Quando si importano dati da un emulatore locale, impostare l'endpoint su https://localhost:<port>. 
> 
> 

## <a name="a-idhbasesourceaimport-from-hbase"></a><a id="HBaseSource"></a>Importa da HBase
L'opzione dell'utilità di importazione HBase origine consente di importare dati da una tabella HBase e filtrare i dati. Sono disponibili vari modelli in modo che l'impostazione di un'importazione è più semplice possibile.

![Schermata di HBase opzioni del codice sorgente](./media/documentdb-import-data/hbasesource1.png)

![Schermata di HBase opzioni del codice sorgente](./media/documentdb-import-data/hbasesource2.png)

Il formato della stringa di connessione HBase Stargate è:

    ServiceURL=<server-address>;Username=<username>;Password=<password>

> [!NOTE]
> Utilizzare il comando verifica per garantire che l'istanza di HBase specificato nel campo della stringa di connessione sia accessibile.ssione sia accessibile.
> 
> 

Ecco un esempio di riga di comando per importare da HBase:

    dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;" /t.Collection:hbaseimport

## <a name="a-iddocumentdbbulktargetaimport-to-documentdb-bulk-import"></a><a id="DocumentDBBulkTarget"></a>Importare in DocumentDB (importazione in blocco)
L'utilità di importazione in blocco di DocumentDB consente di importare da qualsiasi opzione di origine disponibile, usando una stored procedure di DocumentDB per una maggiore efficienza. Lo strumento supporta l'importazione in una raccolta DocumentDB a partizione singola e l'importazione partizionata in cui i dati vengono partizionati in più raccolte DocumentDB a partizione singola. Per altre informazioni sul partizionamento dei dati, vedere [Dati di partizione e di scalabilità in DocumentDB](documentdb-partition-data.md). Lo strumento creerà, eseguirà e quindi eliminerà la stored procedure dalla raccolta o dalle raccolte di destinazione.  

![Schermata delle opzioni in blocco DocumentDB](./media/documentdb-import-data/documentdbbulk.png)

Il formato della stringa di connessione di DocumentDB è:

    AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

La stringa di connessione dell'account DocumentDB può essere recuperata dal pannello Chiavi del portale di Azure, come descritto in [Come gestire un account DocumentDB](documentdb-manage-account.md), tuttavia il nome del database deve essere aggiunto alla stringa di connessione nel formato seguente:

    Database=<DocumentDB Database>;

> [!NOTE]
> Usare il comando Verify per assicurarsi che l'istanza di DocumentDB specificata nel campo della stringa di connessione sia accessibile.
> 
> 

Per importare in un'unica raccolta, immettere il nome della raccolta in cui verranno importati i dati e fare clic sul pulsante Aggiungi. Per importare in più raccolte, immettere il nome di ogni raccolta singolarmente o usare la sintassi seguente per specificare più raccolte: *prefisso_raccolta*[indice iniziale - indice finale]. Quando si specificano più raccolte tramite la sintassi menzionata in precedenza, tenere presente quanto segue:

1. Sono supportati solo criteri di denominazione con intervalli interi. Ad esempio, se si specifica collection[0-3], verranno restituite le raccolte seguenti: collection0, collection1, collection2, collection3.
2. È possibile usare una sintassi abbreviata: collection[3] restituirà lo stesso set di raccolte citato nel passaggio 1.
3. È possibile specificare più di una sostituzione. Ad esempio, collection[0-1] [0-9] genererà 20 nomi di raccolte con zeri iniziali (collection01, ..02, ..03).

Dopo aver specificato il nome della raccolta, scegliere la velocità effettiva desiderata della raccolta, da 400 UR a 10.000 UR. Per ottimizzare le prestazioni di importazione, scegliere una velocità effettiva superiore. Per altre informazioni sui livelli di prestazioni, vedere [Livelli di prestazioni in DocumentDB](documentdb-performance-levels.md).

> [!NOTE]
> L'impostazione della velocità effettiva delle prestazioni si applica solo alla creazione di raccolte. Se la raccolta specificata esiste già, la velocità effettiva non verrà modificata.
> 
> 

Quando si importa in più raccolte, lo strumento di importazione supporta il partizionamento orizzontale basato su hash. In questo scenario, specificare la proprietà di documento da usare come chiave di partizione (se la chiave di partizione viene lasciata vuota, i documenti verranno partizionati in modo casuale tra le raccolte di destinazione).

Se si preferisce, si può specificare quale campo dell'origine di importazione deve essere usato come proprietà ID documento di DocumentDB durante l'importazione. Si noti che, se i documenti non contengono questa proprietà, lo strumento di importazione genererà un GUID come valore della proprietà ID.

Durante l'importazione sono disponibili numerose opzioni avanzate. Innanzitutto, anche se lo strumento include una stored procedure di importazione in blocco predefinita (BulkInsert.js), è possibile scegliere di specificare la propria stored procedure di importazione:

 ![Schermata dell'opzione della stored procedure di inserimento in blocco DocumentDB](./media/documentdb-import-data/bulkinsertsp.png)

Inoltre, quando si importano i tipi di data (ad esempio, da SQL Server o MongoDB), è possibile scegliere tra tre opzioni di importazione:

 ![Schermata delle opzioni di importazione di data e ora DocumentDB](./media/documentdb-import-data/datetimeoptions.png)

* String: persiste come valore di stringa.
* Epoch: persiste come valore numerico di periodo.
* Both: persiste come valore di stringa e numerico di periodo. Questa opzione creerà un documento secondario, ad esempio: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

L'utilità di importazione in blocco di DocumentDB presenta le seguenti opzioni avanzate aggiuntive:

1. Batch Size: per impostazione predefinita, le dimensioni batch dello strumento sono pari a 50.  Se i documenti da importare sono di grandi dimensioni, provare a ridurre le dimensioni batch. Se invece i documenti da importare sono di piccole dimensioni, provare ad aumentare le dimensioni batch.
2. Max Script Size (bytes): per impostazione predefinita, le dimensioni script massime dello strumento sono pari a 512 KB
3. Disable Automatic Id Generation: se ogni documento da importare contiene un campo ID, selezionando questa opzione, le prestazioni possono migliorare. I documenti in cui manca un campo ID univoco non verranno importati.
4. Update Existing Documents: per impostazione predefinita, lo strumento non sostituisce i documenti esistenti con conflitti tra ID. Questa opzione consentirà di sovrascrivere i documenti esistenti con ID corrispondenti. Questa funzionalità è utile per le migrazioni dei dati pianificate che aggiornano i documenti esistenti.
5. Number of Retries on Failure: specifica quante volte ritentare la connessione a DocumentDB in caso di errori temporanei (ad esempio, un'interruzione della connettività di rete).
6. Retry Interval: specifica quanto attendere prima di ritentare la connessione a DocumentDB in caso di errori temporanei (ad esempio, un'interruzione della connettività di rete).
7. Connection Mode: specifica la modalità di connessione da usare con DocumentDB. Le scelte disponibili sono DirectTcp, DirectHttps e Gateway. Le modalità di connessione diretta sono più veloci, mentre la modalità gateway si integra più facilmente con il firewall perché usa solo la porta 443.

![Schermata delle opzioni avanzate di importazione in blocco DocumentDB](./media/documentdb-import-data/docdbbulkoptions.png)

> [!TIP]
> Per impostazione predefinita, la modalità di connessione dello strumento di importazione è DirectTcp. Se si verificano problemi con il firewall, passare alla modalità di connessione Gateway, che richiede solo la porta 443.
> 
> 

## <a name="a-iddocumentdbseqtargetaimport-to-documentdb-sequential-record-import"></a><a id="DocumentDBSeqTarget"></a>Importazione in DocumentDB (importazione di record sequenziali)
L'utilità di importazione di record sequenziali di DocumentDB consente di importare un record alla volta da qualsiasi opzione di origine disponibile. È possibile scegliere questa opzione se si sta importando in una raccolta esistente che ha raggiunto la quota di stored procedure. Lo strumento supporta l'importazione in un'unica raccolta DocumentDB, sia a partizione singola sia a partizione multipla, e l'importazione partizionata in cui i dati vengono partizionati in più raccolte DocumentDB a partizione singola e/o a partizione multipla. Per altre informazioni sul partizionamento dei dati, vedere [Dati di partizione e di scalabilità in DocumentDB](documentdb-partition-data.md).

![Schermata delle opzioni di importazione di record sequenziali DocumentDB](./media/documentdb-import-data/documentdbsequential.png)

Il formato della stringa di connessione di DocumentDB è:

    AccountEndpoint=<DocumentDB Endpoint>;AccountKey=<DocumentDB Key>;Database=<DocumentDB Database>;

La stringa di connessione dell'account DocumentDB può essere recuperata dal pannello Chiavi del portale di Azure, come descritto in [Come gestire un account DocumentDB](documentdb-manage-account.md), tuttavia il nome del database deve essere aggiunto alla stringa di connessione nel formato seguente:

    Database=<DocumentDB Database>;

> [!NOTE]
> Usare il comando Verify per assicurarsi che l'istanza di DocumentDB specificata nel campo della stringa di connessione sia accessibile.
> 
> 

Per importare in un'unica raccolta, immettere il nome della raccolta in cui verranno importati i dati e fare clic sul pulsante Aggiungi. Per importare in più raccolte, immettere il nome di ogni raccolta singolarmente o usare la sintassi seguente per specificare più raccolte: *prefisso_raccolta*[indice iniziale - indice finale]. Quando si specificano più raccolte tramite la sintassi menzionata in precedenza, tenere presente quanto segue:

1. Sono supportati solo criteri di denominazione con intervalli interi. Ad esempio, se si specifica collection[0-3], verranno restituite le raccolte seguenti: collection0, collection1, collection2, collection3.
2. È possibile usare una sintassi abbreviata: collection[3] restituirà lo stesso set di raccolte citato nel passaggio 1.
3. È possibile specificare più di una sostituzione. Ad esempio, collection[0-1] [0-9] genererà 20 nomi di raccolte con zeri iniziali (collection01, ..02, ..03).

Dopo aver specificato il nome della raccolta, scegliere la velocità effettiva desiderata della raccolta, da 400 UR a 250.000 UR. Per ottimizzare le prestazioni di importazione, scegliere una velocità effettiva superiore. Per altre informazioni sui livelli di prestazioni, vedere [Livelli di prestazioni in DocumentDB](documentdb-performance-levels.md). Eventuali importazioni nelle raccolte con una velocità effettiva >&10;.000 UR richiederanno una chiave di partizione. Se si sceglie di avere più di 250.000 UR, sarà necessario inviare una richiesta di nel portale per incrementare l'account.

> [!NOTE]
> L'impostazione della velocità effettiva si applica solo alla creazione di raccolte. Se la raccolta specificata esiste già, la velocità effettiva non verrà modificata.
> 
> 

Quando si importa in più raccolte, lo strumento di importazione supporta il partizionamento orizzontale basato su hash. In questo scenario, specificare la proprietà di documento da usare come chiave di partizione (se la chiave di partizione viene lasciata vuota, i documenti verranno partizionati in modo casuale tra le raccolte di destinazione).

Se si preferisce, si può specificare quale campo dell'origine di importazione deve essere usato come proprietà ID documento di DocumentDB durante l'importazione. Si noti che, se i documenti non contengono questa proprietà, lo strumento di importazione genererà un GUID come valore della proprietà ID.

Durante l'importazione sono disponibili numerose opzioni avanzate. Innanzitutto, quando si importano i tipi di data (ad esempio, da SQL Server o MongoDB), è possibile scegliere tra tre opzioni di importazione:

 ![Schermata delle opzioni di importazione di data e ora DocumentDB](./media/documentdb-import-data/datetimeoptions.png)

* String: persiste come valore di stringa.
* Epoch: persiste come valore numerico di periodo.
* Both: persiste come valore di stringa e numerico di periodo. Questa opzione creerà un documento secondario, ad esempio: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

L'utilità di importazione di record sequenziali di DocumentDB presenta le seguenti opzioni avanzate aggiuntive:

1. Number of Parallel Requests: per impostazione predefinita, nello strumento le richieste parallele sono 2. Se i documenti da importare sono di piccole dimensioni, provare ad aumentare il numero di richieste parallele. Si noti che, se questo numero è troppo alto, durante l'importazione potrebbe venire applicata la limitazione delle richieste.
2. Disable Automatic Id Generation: se ogni documento da importare contiene un campo ID, selezionando questa opzione, le prestazioni possono migliorare. I documenti in cui manca un campo ID univoco non verranno importati.
3. Update Existing Documents: per impostazione predefinita, lo strumento non sostituisce i documenti esistenti con conflitti tra ID. Questa opzione consentirà di sovrascrivere i documenti esistenti con ID corrispondenti. Questa funzionalità è utile per le migrazioni dei dati pianificate che aggiornano i documenti esistenti.
4. Number of Retries on Failure: specifica quante volte ritentare la connessione a DocumentDB in caso di errori temporanei (ad esempio, un'interruzione della connettività di rete).
5. Retry Interval: specifica quanto attendere prima di ritentare la connessione a DocumentDB in caso di errori temporanei (ad esempio, un'interruzione della connettività di rete).
6. Connection Mode: specifica la modalità di connessione da usare con DocumentDB. Le scelte disponibili sono DirectTcp, DirectHttps e Gateway. Le modalità di connessione diretta sono più veloci, mentre la modalità gateway si integra più facilmente con il firewall perché usa solo la porta 443.

![Schermata delle opzioni avanzate di importazione di record sequenziali DocumentDB](./media/documentdb-import-data/documentdbsequentialoptions.png)

> [!TIP]
> Per impostazione predefinita, la modalità di connessione dello strumento di importazione è DirectTcp. Se si verificano problemi con il firewall, passare alla modalità di connessione Gateway, che richiede solo la porta 443.
> 
> 

## <a name="a-idindexingpolicyaspecify-an-indexing-policy-when-creating-documentdb-collections"></a><a id="IndexingPolicy"></a>Specificare un criterio di indicizzazione durante la creazione di raccolte di DocumentDB
Quando si consente all'utilità di migrazione di creare raccolte durante l'importazione, è possibile specificare i criteri di indicizzazione delle raccolte. Nelle opzioni avanzate importare sezione del blocco DocumentDB e opzioni DocumentDB sequenziale di record, passare alla sezione criteri l'indicizzazione.

![Schermata di DocumentDB indicizzazione criteri opzioni avanzate](./media/documentdb-import-data/indexingpolicy1.png)

Utilizzando i criteri di indicizzazione opzione avanzata, è possibile selezionare un file di criteri di indicizzazione, manualmente immettere un criterio di indicizzazione o selezionare da un set di modelli predefiniti (facendo clic nella casella di testo di indicizzazione criteri destro).

I modelli dei criteri che lo strumento fornisce sono:

* Default. Questo criterio è migliore quando si eseguono query di uguaglianza su stringhe e utilizzando ORDER BY, l'intervallo e le query di uguaglianza per i numeri. Questo criterio ha un overhead di archiviazione indice inferiore rispetto a intervallo.
* Intervallo. Questo criterio è consigliabile quando si sta utilizzando query ORDER BY, intervallo e uguaglianza su stringhe e numeri. Questo criterio ha un overhead di archiviazione indice superiore rispetto a predefinito o Hash.

![Schermata di DocumentDB indicizzazione criteri opzioni avanzate](./media/documentdb-import-data/indexingpolicy2.png)

> [!NOTE]
> Se non si specifica un criterio di indicizzazione, verrà applicato il criterio predefinito. Per altre informazioni sui criteri di indicizzazione, vedere [Criteri di indicizzazione di DocumentDB](documentdb-indexing-policies.md).
> 
> 

## <a name="export-to-json-file"></a>Esportare in file JSON
L'utilità di esportazione JSON di DocumentDB consente di esportare qualsiasi opzione di origine disponibile in un file JSON contenente una matrice di documenti JSON. Sarà lo strumento a gestire l'esportazione, ma è anche possibile scegliere di visualizzare il comando di migrazione risultante ed eseguire il comando manualmente. Il file JSON risultante può essere archiviato in locale o nel servizio di archiviazione Blob di Azure.

![Opzione di esportazione del file locale schermata di DocumentDB JSON](./media/documentdb-import-data/jsontarget.png)

![Opzione di esportazione archiviazione schermata di DocumentDB JSON Blob di Azure](./media/documentdb-import-data/jsontarget2.png)

Se si preferisce, si può scegliere di modificare il file JSON risultante, aumentando così le dimensioni del documento risultante e rendendone il contenuto più leggibile.

    Standard JSON export
    [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in DocumentDB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]

    Prettified JSON export
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in DocumentDB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]

## <a name="advanced-configuration"></a>Configurazione avanzata
Nella schermata Configurazione avanzata specificare il percorso del file di log in cui scrivere gli errori. In questa pagina vengono applicate le regole seguenti:

1. Se non viene fornito un nome di file, tutti gli errori verranno restituiti nella pagina dei risultati.
2. Se viene fornito un nome di file senza una directory, il file sarà creato (o sovrascritto) nella directory dell'ambiente corrente.
3. Se si seleziona un file esistente, il file verrà sovrascritto. Non è prevista l'opzione di accodamento.

Quindi, scegliere se registrare tutti i messaggi di errore, quelli critici o nessuno. Infine, definire la frequenza con cui il messaggio di trasferimento su schermo verrà aggiornato con lo stato di avanzamento.

    ![Screenshot of Advanced configuration screen](./media/documentdb-import-data/AdvancedConfiguration.png)

## <a name="confirm-import-settings-and-view-command-line"></a>Confermare le impostazioni di importazione e visualizzare la riga di comando
1. Dopo avere specificato le informazioni di origine e di destinazione e la configurazione avanzata, esaminare il riepilogo della migrazione e, facoltativamente, visualizzare/copiare il comando di migrazione risultante (copiare il comando è utile per automatizzare le operazioni di importazione):
   
    ![Schermata della pagina di riepilogo](./media/documentdb-import-data/summary.png)
   
    ![Schermata della pagina di riepilogo](./media/documentdb-import-data/summarycommand.png)
2. Una volta verificate le opzioni di origine e di destinazione, fare clic su **Import**. Il tempo trascorso, il conteggio degli elementi trasferiti e le informazioni sugli errori (se non è stato fornito un nome file nella pagina Configurazione avanzata) verranno aggiornati mentre l'importazione è in corso. Una volta completata, è possibile esportare i risultati (ad esempio, per gestire gli eventuali errori di importazione).
   
    ![Schermata dell'opzione di esportazione JSON DocumentDB](./media/documentdb-import-data/viewresults.png)
3. È anche possibile avviare una nuova importazione, mantenendo le impostazioni esistenti (ad esempio, le informazioni della stringa di connessione, la scelta dell'origine e della destinazione, ecc.) o reimpostando tutti i valori.
   
    ![Schermata dell'opzione di esportazione JSON DocumentDB](./media/documentdb-import-data/newimport.png)

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su DocumentDB, visitare il [percorso di apprendimento](https://azure.microsoft.com/documentation/learning-paths/documentdb/).




<!--HONumber=Jan17_HO2-->



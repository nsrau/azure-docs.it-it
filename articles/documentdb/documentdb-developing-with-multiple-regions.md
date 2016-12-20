---
title: "Sviluppo con più aree in DocumentDB | Documentazione Microsoft"
description: "Informazioni su come accedere ai dati in più aree da Azure DocumentDB, un servizio database NoSQL completamente gestito."
services: documentdb
documentationcenter: 
author: kiratp
manager: jhubbard
editor: 
ms.assetid: d4579378-0b3a-44a5-9f5b-630f1fa4c66d
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2016
ms.author: kipandya
translationtype: Human Translation
ms.sourcegitcommit: f480b8155c7bee797f1fed0f80200eec500e95a2
ms.openlocfilehash: b188c8d8d66f0af1afa6f357f4d67db7b778ec86


---
# <a name="developing-with-multi-region-documentdb-accounts"></a>Sviluppo con account DocumentDB in più aree
> [!NOTE]
> La distribuzione globale di database di DocumentDB è disponibile a livello generale ed è abilitata automaticamente per tutti gli account DocumentDB appena creati. Stiamo lavorando per abilitare la distribuzione globale per tutti gli account esistenti, ma nel frattempo, se si vuole che la distribuzione globale sia abilitata per il proprio account, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .
>
>

Per sfruttare la [distribuzione globale](documentdb-distribute-data-globally.md), le applicazioni client possono specificare un elenco di aree, nell'ordine preferito, da usare per eseguire operazioni sui documenti. Questa operazione può essere eseguita impostando il criterio di connessione. A seconda della configurazione dell'account Azure DocumentDB, della disponibilità corrente delle aree e dell'elenco delle preferenze specificato, l'SDK sceglierà l'endpoint ottimale per eseguire le operazioni di scrittura e lettura.

L'elenco delle preferenze viene specificato nella fase di inizializzazione di una connessione tramite gli SDK del client DocumentDB. Gli SDK accettano il parametro facoltativo "PreferredLocations", ovvero un elenco ordinato di aree di Azure.

L'SDK invia automaticamente tutte le scritture all'area di scrittura corrente.

Tutte le letture verranno inviate alla prima area disponibile nell'elenco PreferredLocations. Se la richiesta ha esito negativo, il client trasferisce l'elenco all'area successiva e così via.

Gli SDK del client tenteranno solo di leggere dalle aree specificate nell'elenco PreferredLocations. Quindi se l'Account di database è disponibile ad esempio in tre aree, ma il client specifica solo due delle aree di non scrittura per PreferredLocations, le letture non verranno distribuite fuori dall'area di scrittura, anche in caso di failover.

L'applicazione può verificare l'endpoint di scrittura e lettura corrente scelto dall'SDK controllando due proprietà, WriteEndpoint e ReadEndpoint, disponibili nella versione dell'SDK 1.8 e nelle versioni successive.

Se la proprietà PreferredLocations non è impostata, tutte le richieste verranno distribuite dall'area di scrittura corrente.

## <a name="net-sdk"></a>.NET SDK
L'SDK può essere usato senza alcuna modifica del codice. In questo caso l'SDK reindirizza automaticamente sia le operazioni di lettura che di scrittura all'area di scrittura corrente.

Nella versione 1.8 e nelle versioni successive di .NET SDK, il parametro ConnectionPolicy per il costruttore DocumentClient dispone di una proprietà denominata Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Questa proprietà è di tipo raccolta `<string>` e deve contenere un elenco di nomi di aree. I valori della stringa vengono formattati secondo la colonna Nome dell'area nella pagina [Regioni di Azure][regions], senza spazi prima o dopo il primo e l'ultimo carattere.

Gli endpoint di lettura e scrittura correnti sono disponibili rispettivamente in DocumentClient.WriteEndpoint e DocumentClient.ReadEndpoint.

> [!NOTE]
> Gli URL per gli endpoint non devono essere considerati come costanti di lunga durata. Il servizio può aggiornare gli URL in qualsiasi momento. L'SDK gestisce la modifica in modo automatico.
>
>

    // Getting endpoints from application settings or other configuration location
    Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
    string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

    //Setting read region selection preference
    connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
    connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
    connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

    // initialize connection
    DocumentClient docClient = new DocumentClient(
        accountEndPoint,
        accountKey,
        connectionPolicy);

    // connect to DocDB
    await docClient.OpenAsync().ConfigureAwait(false);


## <a name="nodejs-javascript-and-python-sdks"></a>SDK di NodeJS, JavaScript e Python
L'SDK può essere usato senza alcuna modifica del codice. In questo caso, l'SDK reindirizzerà automaticamente sia le letture sia le scritture all'area di scrittura corrente.

Nella versione 1.8 e nelle versioni successive di ogni SDK, il parametro ConnectionPolicy per il costruttore DocumentClient dispone di una nuova proprietà denominata DocumentClient.ConnectionPolicy.PreferredLocations. Questo parametro è una matrice di stringhe che accetta un elenco di nomi di aree. I nomi vengono formattati secondo la colonna Nome dell'area nella pagina [Regioni di Azure][regions]. È anche possibile usare costanti predefinite nell'oggetto AzureDocuments.Regions

Gli endpoint di lettura e scrittura correnti sono disponibili rispettivamente in DocumentClient.getWriteEndpoint e DocumentClient.getReadEndpoint.

> [!NOTE]
> Gli URL per gli endpoint non devono essere considerati come costanti di lunga durata. Il servizio può aggiornare gli URL in qualsiasi momento. L'SDK gestirà questa modifica in automatico.
>
>

Di seguito è riportato un esempio di codice per NodeJS/Javascript. Python e Java seguiranno lo stesso modello.

    // Creating a ConnectionPolicy object
    var connectionPolicy = new DocumentBase.ConnectionPolicy();

    // Setting read region selection preference, in the following order -
    // 1 - West US
    // 2 - East US
    // 3 - North Europe
    connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];

    // initialize the connection
    var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);


## <a name="rest"></a>REST
Dopo aver reso disponibile un account di database in più aree, i client possono eseguire query sulla relativa disponibilità tramite una richiesta GET all'URI seguente.

    https://{databaseaccount}.documents.azure.com/

Il servizio restituirà un elenco delle aree e i relativi URI dell'endpoint DocumentDB per le repliche. L'area di scrittura corrente verrà indicata nella risposta. Il client può quindi selezionare l'endpoint appropriato per tutte le altre richieste dell'API REST come indicato di seguito.

Risposta di esempio

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


* Tutte le richieste PUT, POST e DELETE devono passare all'URI di scrittura indicato.
* Tutte le richieste di sola lettura e GET, ad esempio le query, possono passare a qualsiasi endpoint scelto dal client.

Le richieste di scrittura per le aree di sola lettura avranno esito negativo con codice di errore HTTP 403 ("Non consentito").

Se l'area di scrittura viene modificata dopo la fase di individuazione iniziale del client, le scritture successive all'area di scrittura precedente avranno esito negativo con codice di errore HTTP 403 ("Non consentito"). Il client deve quindi OTTENERE nuovamente l'elenco delle aree per aggiornare l'area di scrittura.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla distribuzione globale dei dati con DocumentDB, vedere gli articoli seguenti:

* [Distribuire i dati a livello globale con DocumentDB](documentdb-distribute-data-globally.md)
* [Livelli di coerenza](documentdb-consistency-levels.md)
* [Informazioni sull'archiviazione e sul provisioning con prestazioni stimabili in DocumentDB](documentdb-manage.md)
* [Aggiungere aree tramite il portale di Azure](documentdb-portal-global-replication.md)

[regions]: https://azure.microsoft.com/regions/



<!--HONumber=Nov16_HO3-->



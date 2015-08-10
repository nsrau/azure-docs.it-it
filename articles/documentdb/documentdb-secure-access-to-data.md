<properties 
	pageTitle="Informazioni su come proteggere l'accesso ai dati in DocumentDB | Azure" 
	description="Informazioni sui concetti di controllo di accesso in DocumentDB, tra cui chiavi master, chiavi di sola lettura, utenti e autorizzazioni." 
	services="documentdb" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/22/2015" 
	ms.author="stbaro"/>

# Protezione dell'accesso ai dati in DocumentDB #

Questo articolo fornisce una panoramica della protezione dell'accesso ai dati archiviati in [Microsoft Azure DocumentDB](../../services/documentdb/).

Dopo la lettura di questa panoramica, si potrà rispondere alle domande seguenti:

-	Cosa sono le chiavi master di DocumentDB?
-	Cosa sono le chiavi di sola lettura di DocumentDB?
-	Cosa sono i token delle risorse di DocumentDB?
-	Come è possibile usare gli utenti e le autorizzazioni di DocumentDB per proteggere l'accesso ai dati in DocumentDB?

##<a id="Sub1"></a>Concetti di controllo di accesso in DocumentDB##

DocumentDB offre concetti avanzati per controllare l'accesso alle risorse in DocumentDB. Nell'ambito di questo argomento, le risorse di DocumentDB sono raggruppate in due categorie:

- Risorse amministrative
	- Account
	- Database
	- Utente
	- Autorizzazione
- Risorse dell'applicazione
	- Raccolta
	- Documento
	- Allegato
	- Stored procedure
	- Trigger
	- Funzione definita dall'utente

Nel contesto di queste due categorie, DocumentDB supporta tre tipi di utente tipo di controllo di accesso: amministratore account, amministratore di sola lettura e utente database. I diritti di ogni utente tipo di controllo di accesso sono:
 
- Amministratore account: accesso completo a tutte le risorse (amministrative e dell'applicazione) in un determinato account di DocumentDB.
- Amministratore di sola lettura: accesso di sola lettura a tutte le risorse (amministrative e dell'applicazione) in un determinato account di DocumentDB. 
- Utente database: risorsa utente di DocumentDB associata a uno specifico set di risorse database di DocumentDB (ad esempio, raccolte, documenti, script). Una o più risorse utente possono essere associate a un determinato database e a ogni risorsa utente può essere associate una o più autorizzazioni.

Tenendo presenti le suddette categorie e risorse, il modello di controllo di accesso di DocumentDB definisce tre tipi di costrutto di accesso:

- Chiavi master: al momento della creazione di un account di DocumentDB, vengono create due chiavi master (primaria e secondaria). Queste chiavi consentono l'accesso amministrativo completo a tutte le risorse nell'account di DocumentDB.

![Illustrazione di chiavi master di DocumentDB](./media/documentdb-secure-access-to-data/masterkeys.png)

- Chiavi di sola lettura: al momento della creazione di un account di DocumentDB, vengono create due chiavi di sola lettura (primaria e secondaria). Queste chiavi consentono l'accesso di sola lettura a tutte le risorse nell'account di DocumentDB.

![Illustrazione di chiavi di sola lettura DocumentDB](./media/documentdb-secure-access-to-data/readonlykeys.png)

- Token risorsa: un token di una risorsa è associato a una risorsa autorizzazione di DocumentDB e acquisisce la relazione tra l'utente di un database e l'autorizzazione dell'utente per una risorsa applicazione di DocumentDB specifica (ad esempio, raccolta, documento).

![Illustrazione di token DocumentDB risorse](./media/documentdb-secure-access-to-data/resourcekeys.png)

##<a id="Sub2"></a>Uso delle chiavi master e di sola lettura di DocumentDB ##
Come accennato in precedenza, le chiavi master di DocumentDB forniscono l'accesso amministrativo completo a tutte le risorse in un account di DocumentDB, mentre le chiavi di sola lettura consentono l'accesso in lettura a tutte le risorse nell'account. Il seguente frammento di codice illustra come usare un endpoint dell'account di DocumentDB e la chiave master per creare un'istanza di DocumentClient e per creare un nuovo database.

    //Read the DocumentDB endpointUrl and authorization keys from config.
    //These values are available from the Azure Management Portal on the DocumentDB Account Blade under "Keys".
    //NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.
    
	private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
    private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);
        
    client = new DocumentClient(new Uri(endpointUrl), authorizationKey);
    
	//Create Database
    Database database = await client.CreateDatabaseAsync(
        new Database
        {
            Id = databaseName
        });


##<a id="Sub3"></a>Panoramica dei token delle risorse di DocumentDB ##
È possibile usare un token delle risorse (creando utenti e autorizzazioni di DocumentDB) quando si desidera fornire l'accesso alle risorse dell'account di DocumentDB a un client al quale non si vuole fornire la chiave master. Le chiavi master di DocumentDB includono una chiave primaria e una chiave secondaria, ciascuna delle quali garantisce accesso amministrativo all'account e a tutte le risorse in esso presenti. Se si espone una delle chiavi master, è possibile che l'account venga usato in modo dannoso o non appropriato.

Allo stesso modo, le chiavi di sola lettura di DocumentDB forniscono l'accesso in lettura a tutte le risorse (a eccezione ovviamente delle risorse autorizzazione) in un account di DocumentDB e non possono essere usate per fornire un accesso più granulare a risorse specifiche di DocumentDB.

Il token delle risorse di DocumentDB costituisce un'alternativa sicura per consentire ai client di leggere, scrivere ed eliminare risorse nell'account di DocumentDB sulla base delle autorizzazioni concesse e senza richiedere la chiave master o di sola lettura.

Ecco un tipico schema progettuale in cui i token delle risorse possono essere richiesti, generati e forniti ai client:

1. Un servizio di livello intermedio viene configurato per gestire un'applicazione per dispositivi mobili per condividere le foto dell'utente.
2. Il servizio di livello intermedio possiede la chiave master dell'account di DocumentDB.
3. L'app per le foto viene installata nei dispositivi mobili dell'utente finale. 
4. All'accesso, l'app per le foto stabilisce l'identità dell'utente con il servizio di livello intermedio. Questo meccanismo per stabilire l'identità dipende completamente dall'applicazione.
5. Una volta stabilita l'identità, il servizio di livello intermedio richiede le autorizzazioni in base all'identità.
6. Il servizio di livello intermedio invia di nuovo un token delle risorse all'app per il telefono.
7. L'app per il telefono può continuare a usare il token delle risorse per accedere direttamente alle risorse di DocumentDB con le autorizzazioni definite dal token delle risorse e per l'intervallo consentito dal token delle risorse. 
8. Quando il token delle risorse scade, le richieste successive riceveranno un'eccezione 401 Non autorizzato. A questo punto, l'app per il telefono ristabilisce l'identità e richiede un nuovo token delle risorse.

![Flusso di lavoro di DocumentDB risorse token](./media/documentdb-secure-access-to-data/resourcekeyworkflow.png)

##<a id="Sub4"></a>Uso di utenti e autorizzazioni di DocumentDB ##
Una risorsa utente di DocumentDB è associata a un database di DocumentDB. Ogni database può includere zero o più utenti di DocumentDB. Il frammento di codice seguente mostra come creare una risorsa utente di DocumentDB.

	//Create a user.
    User docUser = new User
    {
        Id = "mobileuser"
    };

    docUser = await client.CreateUserAsync(database.SelfLink, docUser);

> [AZURE.NOTE]Ogni utente di DocumentDB dispone di una proprietà PermissionsLink che può essere usata per recuperare l'elenco di autorizzazioni associate all'utente.

Una risorsa autorizzazione di DocumentDB è associata a un utente di DocumentDB. Ogni utente può includere zero o più autorizzazioni di DocumentDB. Una risorsa autorizzazione fornisce l'accesso a un token di sicurezza di cui l'utente ha bisogno quando deve accedere a una risorsa applicazione specifica. Sono disponibili due livelli di accesso che possono essere forniti da una risorsa autorizzazione:

- Tutto: l'utente dispone dell'autorizzazione completa sulla risorsa.
- Lettura: l'utente può solo leggere i contenuti della risorsa, ma non può eseguire operazioni di scrittura, aggiornamento o eliminazione sulla risorsa.


> [AZURE.NOTE]Per eseguire le stored procedure di DocumentDB, l'utente deve disporre dell'autorizzazione All per la raccolta in cui verrà eseguita la stored procedure.


Il frammento di codice seguente illustra come creare una risorsa autorizzazione, leggere il token delle risorse (token) della risorsa autorizzazione e associare le autorizzazioni all'utente creato in precedenza.

	//Create a permission.

    Permission docPermission = new Permission
    {
        PermissionMode = PermissionMode.Read,
        ResourceLink = documentCollection.SelfLink,
        Id = "readperm"
    };
            
	docPermission = await client.CreatePermissionAsync(docUser.SelfLink, docPermission);
	Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);

Per ottenere facilmente tutte le risorse autorizzazione associate a un particolare utente, DocumentDB rende disponibile un feed di autorizzazioni per ogni oggetto utente. Il frammento di codice seguente illustra come recuperare l'autorizzazione associata all'utente creato in precedenza, costruire un elenco di autorizzazioni e creare un'istanza di un nuovo DocumentClient per conto dell'utente.

	//Read a permission feed.
    FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(docUser.SelfLink);
	
	List<Permission> permList = new List<Permission>();
    
	foreach (Permission perm in permFeed)
    {
        permList.Add(perm);
    }
            
    DocumentClient userClient = new DocumentClient(new Uri(endpointUrl),permList);

> [AZURE.TIP]I token delle risorse hanno un intervallo di tempo valido predefinito di 1 ora. La durata del token, tuttavia, può essere specificata in modo esplicito, fino a un massimo di 5 ore.

##<a name="NextSteps"></a>Passaggi successivi

- Per altre informazioni su DocumentDB, vedere [qui](http://azure.com/docdb).
- Per informazioni sulla gestione delle chiavi master e di sola lettura, fare clic [qui](documentdb-manage-account.md).
- Per informazioni su come costruire i token di autorizzazione di DocumentDB, fare clic [qui](https://msdn.microsoft.com/library/azure/dn783368.aspx)
 

<!---HONumber=July15_HO5-->
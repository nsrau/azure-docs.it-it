---
title: Informazioni su come proteggere l&quot;accesso ai dati in DocumentDB | Documentazione Microsoft
description: Informazioni sui concetti di controllo di accesso in DocumentDB, tra cui chiavi master, chiavi di sola lettura, utenti e autorizzazioni.
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 8641225d-e839-4ba6-a6fd-d6314ae3a51c
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: e4cbc9b0c9532d56376c4fabebcde8c64cb0474b
ms.lasthandoff: 03/24/2017


---
# <a name="securing-access-to-documentdb-data"></a>Protezione dell'accesso ai dati in DocumentDB
Questo articolo fornisce una panoramica della protezione dell'accesso ai dati archiviati in [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

DocumentDB usa due tipi di chiavi per autenticare gli utenti e fornire accesso ai relativi dati e risorse. 

|Tipo di chiave|Risorse|
|---|---|
|[Chiavi master](#master-keys) |Usate per risorse amministrative, ovvero account di database, database, utenti e autorizzazioni|
|[Token delle risorse](#resource-tokens)|Usati per le risorse dell'applicazione, ovvero raccolte, documenti, allegati, stored procedure, trigger e funzioni definite dall'utente|

<a id="master-keys"></a>

## <a name="master-keys"></a>Chiavi master 

Le chiavi master consentono di accedere a tutte le risorse amministrative per l'account del database. Chiavi master:  
- Consentono di accedere ad account, database, utenti e autorizzazioni. 
- Non possono essere usate per fornire l'accesso granulare a raccolte e documenti.
- Vengono create durante la creazione di un account.
- Possono essere rigenerate in qualsiasi momento.

Ogni account è costituito da due chiavi master: una chiave primaria e una chiave secondaria. Lo scopo delle due chiavi è consentire la rigenerazione o la rotazione delle chiavi mantenendo l'accesso continuo ai dati e all'account. 

Oltre alle due chiavi master per l'account DocumentDB, sono disponibili due chiavi di sola lettura. Queste chiavi consentono solo operazioni di lettura per l'account. Le chiavi di sola lettura non forniscono l'accesso in lettura alle risorse di autorizzazione.

Le chiavi master primaria, secondaria, di sola lettura e di lettura/scrittura possono essere recuperate e rigenerate nel portale di Azure. Per istruzioni, vedere [Visualizzare, copiare e rigenerare le chiavi di accesso](documentdb-manage-account.md#a-idkeysaview-copy-and-regenerate-access-keys).

![Controllo di accesso (IAM) nel portale di Azure: dimostrazione della sicurezza del database NoSQL](./media/documentdb-secure-access-to-data/nosql-database-security-master-key-portal.png)

Il processo di rotazione della chiave master è semplice. Passare al portale di Azure per recuperare la chiave secondaria, quindi sostituire la chiave primaria con la chiave secondaria nell'applicazione e ruotare la chiave primaria nel portale di Azure.

![Rotazione delle chiavi master nel portale di Azure: dimostrazione della sicurezza del database NoSQL](./media/documentdb-secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Esempio di codice per l'uso di una chiave master

Il frammento di codice seguente illustra come usare un endpoint dell'account di DocumentDB e la chiave master per creare un'istanza di DocumentClient e un database. 

```csharp
//Read the DocumentDB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the NOSQL (DocumentDB) account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);

client = new DocumentClient(new Uri(endpointUrl), authorizationKey);

// Create Database
Database database = await client.CreateDatabaseAsync(
    new Database
    {
        Id = databaseName
    });
```

<a id="resource-tokens"></a>

## <a name="resource-tokens"></a>Token delle risorse

I token delle risorse consentono di accedere alle risorse dell'applicazione all'interno di un database. Token delle risorse:
- Consentono di accedere a raccolte, chiavi di partizioni, documenti, allegati, stored procedure, trigger e funzioni definite dall'utente.
- Vengono creati quando a un [utente](#users) vengono concesse [autorizzazioni](#permissions) per una risorsa specifica.
- Vengono ricreati quando si interviene su una risorsa di autorizzazione tramite chiamata POST, GET o PUT.
- Usano un token di risorsa hash costruito specificamente per l'utente, la risorsa e l'autorizzazione.
- Hanno un limite di tempo con un periodo di validità personalizzabile. L'intervallo di tempo predefinito è un'ora. La durata del token può essere tuttavia specificata in modo esplicito, fino a un massimo di cinque ore.
- Rappresentano un'alternativa sicura alla divulgazione della chiave master. 
- Consentono ai client di leggere, scrivere ed eliminare risorse nell'account di DocumentDB in base alle autorizzazioni concesse.

È possibile usare un token delle risorse (creando utenti e autorizzazioni di DocumentDB) quando si desidera fornire l'accesso alle risorse dell'account di DocumentDB a un client al quale non si vuole fornire la chiave master.  

I token delle risorse di DocumentDB costituiscono un'alternativa sicura che consente ai client di leggere, scrivere ed eliminare risorse nell'account di DocumentDB sulla base delle autorizzazioni concesse e senza richiedere la chiave master o di sola lettura.

Di seguito è riportato un tipico schema progettuale in cui i token delle risorse possono essere richiesti, generati e forniti ai client:

1. Un servizio di livello intermedio viene configurato per gestire un'applicazione per dispositivi mobili per condividere le foto dell'utente. 
2. Il servizio di livello intermedio possiede la chiave master dell'account di DocumentDB.
3. L'app per le foto viene installata nei dispositivi mobili dell'utente finale. 
4. All'accesso, l'app per le foto stabilisce l'identità dell'utente con il servizio di livello intermedio. Questo meccanismo per stabilire l'identità dipende completamente dall'applicazione.
5. Una volta stabilita l'identità, il servizio di livello intermedio richiede le autorizzazioni in base all'identità.
6. Il servizio di livello intermedio invia di nuovo un token delle risorse all'app per il telefono.
7. L'app per il telefono può continuare a usare il token delle risorse per accedere direttamente alle risorse di DocumentDB con le autorizzazioni definite dal token delle risorse e per l'intervallo consentito dal token delle risorse. 
8. Quando il token delle risorse scade, le richieste successive ricevono un'eccezione 401 Non autorizzato.  A questo punto, l'app per il telefono ristabilisce l'identità e richiede un nuovo token delle risorse.

    ![Flusso di lavoro di DocumentDB risorse token](./media/documentdb-secure-access-to-data/resourcekeyworkflow.png)

La generazione e la gestione dei token delle risorse vengono gestite tramite le librerie client di DocumentDB native. Se tuttavia si usa REST è necessario creare le intestazioni di richiesta/autenticazione. Per altre informazioni sulla creazione di intestazioni di autenticazione per REST, vedere [Access Control on DocumentDB Resources](https://docs.microsoft.com/en-us/rest/api/documentdb/access-control-on-documentdb-resources) (Controllo di accesso per risorse DocumentDB) oppure il [codice sorgente per gli SDK di Microsoft](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js).

Per un esempio di servizio di livello intermedio usato per generare o negoziare i token delle risorse, vedere l'app [ResourceTokenBroker](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

<a id="users"></a>

## <a name="users"></a>Utenti
Gli utenti di DocumentDB sono associati a un database di DocumentDB.  Ogni database può contenere zero o più utenti di DocumentDB.  Il frammento di codice seguente illustra come creare una risorsa utente di DocumentDB.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Ogni utente di DocumentDB ha una proprietà PermissionsLink che può essere usata per recuperare l'elenco di [autorizzazioni](#permissions) associate all'utente.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>autorizzazioni
Una risorsa autorizzazione di DocumentDB è associata a un utente di DocumentDB.  Ogni utente può includere zero o più autorizzazioni di DocumentDB.  Una risorsa autorizzazione fornisce l'accesso a un token di sicurezza di cui l'utente ha bisogno quando deve accedere a una risorsa applicazione specifica.
Sono disponibili due livelli di accesso che possono essere forniti da una risorsa di autorizzazione:

* Tutto: l'utente ha l'autorizzazione completa per la risorsa.
* Lettura: l'utente può solo leggere i contenuti della risorsa, ma non può eseguire operazioni di scrittura, aggiornamento o eliminazione sulla risorsa.

> [!NOTE]
> Per eseguire le stored procedure di DocumentDB, l'utente deve disporre dell'autorizzazione All per la raccolta in cui verrà eseguita la stored procedure.
> 
> 

### <a name="code-sample-to-create-permission"></a>Esempio di codice per la creazione dell'autorizzazione

L'esempio di codice seguente illustra come creare una risorsa di autorizzazione, leggere il token delle risorse della risorsa di autorizzazione e associare le autorizzazioni all'[utente](#users) creato in precedenza.

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = documentCollection.SelfLink,
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

Se è stato specificata una chiave di partizione per la raccolta, l'autorizzazione per le risorse di raccolta, documenti e allegati deve includere anche ResourcePartitionKey oltre a ResourceLink.

### <a name="code-sample-to-read-permissions-for-user"></a>Codice di esempio per la lettura di autorizzazioni dell'utente

Per ottenere facilmente tutte le risorse di autorizzazione associate a un particolare utente, DocumentDB rende disponibile un feed di autorizzazioni per ogni oggetto utente.  Il frammento di codice seguente illustra come recuperare l'autorizzazione associata all'utente creato in precedenza, costruire un elenco di autorizzazioni e creare un'istanza di un nuovo DocumentClient per conto dell'utente.

```csharp
//Read a permission feed.
FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
  UriFactory.CreateUserUri("db", "myUser"));
 List<Permission> permList = new List<Permission>();

foreach (Permission perm in permFeed)
{
    permList.Add(perm);
}

DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);
```

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla sicurezza dei database di DocumentDB, vedere [DocumentDB NoSQL database security](documentdb-nosql-database-security.md) (Sicurezza dei database DocumentDB NoSQL).
* Per informazioni sulla gestione delle chiavi master e di sola lettura, vedere [Come gestire un account DocumentDB](documentdb-manage-account.md#a-idkeysaview-copy-and-regenerate-access-keys).
* Per informazioni su come costruire i token di autorizzazione di DocumentDB, vedere [Access Control on DocumentDB Resources](https://docs.microsoft.com/en-us/rest/api/documentdb/access-control-on-documentdb-resources) (Controllo di accesso per risorse DocumentDB).


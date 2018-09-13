---
title: Chiavi univoche in Azure Cosmos DB | Microsoft Docs
description: Informazioni su come usare chiavi univoche nel database Azure Cosmos DB.
services: cosmos-db
keywords: vincolo di chiave univoca, violazione del vincolo di chiave univoca
author: rafats
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: rafats
ms.openlocfilehash: 5811cb1e08ed5d02038da2a4460ae4b63580833b
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696140"
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Chiavi univoche in Azure Cosmos DB

Le chiavi univoche consentono agli sviluppatori di aggiungere un livello di integrità dei dati nel database. Se si definiscono criteri di chiave univoca quando si crea un contenitore, si ha la sicurezza che uno o più valori siano univoci per ogni [chiave di partizione](partition-data.md). Dopo aver creato un contenitore con criteri di chiave univoca, non è infatti possibile creare o aggiornare elementi con valori che duplicano quelli specificati dal vincolo di chiave univoca.   

> [!NOTE]
> Le chiavi univoche sono supportate dalle versioni più recenti degli SDK di SQL per [.NET](sql-api-sdk-dotnet.md) e [.NET Core](sql-api-sdk-dotnet-core.md) e dell'[API MongoDB](mongodb-feature-support.md#unique-indexes), ma non sono attualmente supportate dalle API Gremlin e Table. 
> 
>

## <a name="use-case"></a>Caso d'uso

A titolo di esempio, si esaminerà in che modo un database utente associato a un'[applicazione social](use-cases.md#web-and-mobile-applications) può trarre vantaggio dalla definizione di criteri di chiave univoca negli indirizzi di posta elettronica. Impostando l'indirizzo di posta elettronica dell'utente come chiave univoca, è possibile assicurarsi che ogni record abbia un indirizzo di posta elettronica univoco e che nessun nuovo record possa essere creato con indirizzi duplicati. 

Se si vuole consentire agli utenti di creare più record con lo stesso indirizzo di posta elettronica, ma non con la stessa combinazione di nome, cognome e indirizzo di posta elettronica, è possibile aggiungere altri percorsi ai criteri di chiave univoca. Pertanto, anziché creare una chiave univoca basata su un indirizzo di posta elettronica, è possibile crearne una composta da nome, cognome e indirizzo di posta elettronica. In questo modo, è consentita ogni combinazione univoca dei tre percorsi e il database può quindi contenere elementi con i valori di percorso seguenti. Ognuno di questi record sarà in grado di soddisfare i criteri di chiave univoca.  

**Valori consentiti per la chiave univoca firstName, lastName e email**

|Nome|Cognome|Indirizzo di posta elettronica|
|---|---|---|
|Gaby|Duperre|gaby@contoso.com |
|Gaby|Duperre|gaby@fabrikam.com|
|Ivan|Duperre|gaby@fabrikam.com|
|    |Duperre|gaby@fabrikam.com|
|    |       |gaby@fabraikam.com|

Se si prova a inserire un altro record con una qualsiasi delle combinazioni elencate nella tabella, verrà visualizzato un messaggio di errore per segnalare che il vincolo di chiave univoca non è stato soddisfatto. Azure Cosmos DB ha restituito uno degli errori seguenti: "Resource with specified id or name already exists" (La risorsa con l'ID o il nome specificato esiste già). o "Resource with specified id, name, or unique index already exists." (La risorsa con l'ID, il nome o l'indice univoco specificato esiste già.) 

## <a name="using-unique-keys"></a>Uso delle chiavi univoche

Le chiavi univoche devono essere definite al momento della creazione del contenitore e hanno un ambito limitato alla chiave di partizione. Riprendendo l'esempio precedente, se le partizioni sono definite in base al codice postale, i record dalla tabella possono essere duplicati in ogni partizione.

Non è possibile aggiornare un contenitore esistente per l'uso di chiavi univoche.

Una volta creato un contenitore con criteri di chiave univoca, è possibile modificare i criteri solo se si crea un nuovo contenitore. Se si hanno dati per i quali si desidera implementare chiavi univoche, creare il nuovo contenitore e quindi usare lo strumento di migrazione appropriato per spostare i dati nel contenitore. Per i contenitori di SQL, usare l'[Utilità di migrazione dati](import-data.md). Per i contenitori di MongoDB, usare [mongoimport.exe o mongorestore.exe](mongodb-migrate.md).

In ogni chiave univoca è possibile includere un massimo di 16 valori di percorso, ad esempio /firstName, /lastName, /address/zipCode e così via. 

I singoli criteri di chiave univoca possono avere un massimo di 10 vincoli o combinazioni di vincoli di chiave univoca e i percorsi combinati per tutte le proprietà di indice univoco non devono superare i 60 caratteri. Pertanto, l'esempio precedente, in cui sono usati nome, cognome e indirizzo di posta elettronica, definisce un solo vincolo e usa tre dei 16 possibili percorsi disponibili. 

Quando sono definiti criteri di chiave univoca per il contenitore, i costi delle unità richiesta per la creazione, l'aggiornamento e l'eliminazione di un elemento sono leggermente più elevati. 

Le chiavi univoche di tipo sparse non sono supportate. Se per alcuni percorsi univoci non sono definiti valori, questi vengono trattati come valori null speciali, che fanno parte del vincolo di univocità.

## <a name="sql-api-sample"></a>Esempio di API SQL

L'esempio di codice seguente illustra come creare un nuovo contenitore di SQL con due vincoli di chiave univoca. Il primo vincolo è quello dell'esempio precedente, composto da firstName, lastName e email. Il secondo vincolo è costituito da address/zipCode, ovvero l'indirizzo e il codice postale degli utenti. Questo esempio di codice viene seguito da un file JSON di esempio che usa i percorsi definiti in questi criteri di chiave univoca. 

```csharp
// Create a collection with two separate UniqueKeys, one compound key for /firstName, /lastName,
// and /email, and another for /address/zipCode.
private static async Task CreateCollectionIfNotExistsAsync(string dataBase, string collection)
{
    try
    {
        await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(dataBase, collection));
    }
    catch (DocumentClientException e)
    {
        if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
        {
            DocumentCollection myCollection = new DocumentCollection();
            myCollection.Id = collection;
            myCollection.PartitionKey.Paths.Add("/pk");
            myCollection.UniqueKeyPolicy = new UniqueKeyPolicy
            {
                UniqueKeys =
                new Collection<UniqueKey>
                {
                    new UniqueKey { Paths = new Collection<string> { "/firstName" , "/lastName" , "/email" }}
                    new UniqueKey { Paths = new Collection<string> { "/address/zipcode" } },
          }
            };
            await client.CreateDocumentCollectionAsync(
                UriFactory.CreateDatabaseUri(dataBase),
                myCollection,
                new RequestOptions { OfferThroughput = 2500 });
        }
        else
        {
            throw;
        }
    }
```

Documento JSON di esempio.

```json
{
    "id": "1",
    "pk": "1234",
    "firstName": "Gaby",
    "lastName": "Duperre",
    "email": "gaby@contoso.com",
    "address": 
        {            
            "line1": "100 Some Street",
            "line2": "Unit 1",
            "city": "Seattle",
            "state": "WA",
            "zipcode": 98012
        }
    
}
```
> [!NOTE]
> I nome di chiave univoca fanno distinzione tra maiuscole e minuscole. Come illustrato nell'esempio precedente, il nome univoco è impostato per /address/zipcode. Se i dati avranno ZipCode, sarà inserito "null" nella chiave univoca in quanto zipcode non è uguale a ZipCode. Inoltre, a causa di questa distinzione tra maiuscole e minuscole, tutti gli altri record con ZipCode non potranno essere inseriti, in quanto i "null" duplicati violeranno il vincolo di chiave univoca.

## <a name="mongodb-api-sample"></a>Esempio di API MongoDB

L'esempio di comando seguente illustra come creare un indice univoco in base ai campi firstName, lastName e email della raccolta users per l'API MongoDB. In questo modo si ha la certezza che la combinazione dei tre campi sia univoca in tutti i documenti della raccolta. Per le raccolte dell'API MongoDB, l'indice univoco viene creato dopo che la raccolta è stata creata, ma prima che venga popolata.

> [!NOTE]
> Il formato della chiave univoca per gli account API MongoDB è diverso da quello degli account API SQL, in cui non è necessario specificare il carattere barra rovesciata (/) prima del nome del campo. 

```
db.users.createIndex( { firstName: 1, lastName: 1, email: 1 }, { unique: true } )
```
## <a name="configure-unique-keys-by-using-azure-portal"></a>Configurare chiavi univoche mediante il portale di Azure

Nelle sezioni riportate sopra sono presenti esempi di codice che mostrano come è possibile definire vincoli di chiave univoca quando viene creata una raccolta usando l'API di SQL o l'API di MongoDB. Ma è anche possibile definire chiavi univoche quando si crea una raccolta tramite l'interfaccia utente Web nel portale di Azure. 

- Passare a **Esplora dati** nell'account di DB Cosmos
- Fare clic su **Nuova raccolta**
- Nella sezione relativa alle chiavi univoche ** è possibile aggiungere i vincoli di chiave univoca desiderati facendo clic su **Add unique key** (Aggiungi chiave univoca)

![Definire chiavi univoche in Esplora dati](./media/unique-keys/unique-keys-azure-portal.png)

- Se si vuole creare un vincolo di chiave univoca nel percorso lastName, aggiungere `/lastName`.
- Se si vuole creare un vincolo di chiave univoca per la combinazione lastName firstName, aggiungere `/lastName,/firstName`

Al termine, fare clic su **OK** per creare la raccolta.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come creare chiavi univoche per gli elementi di un database. Se si crea un contenitore per la prima volta, vedere [Partizionamento dei dati in Azure Cosmos DB](partition-data.md) poiché le chiavi univoche e le chiavi di partizione sono interrelate. 



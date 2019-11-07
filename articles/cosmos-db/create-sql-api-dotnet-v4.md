---
title: Gestire le risorse dell'API SQL di Azure Cosmos DB tramite .NET V4 SDK
description: Esercitazione dell'avvio rapido su come creare un'app console con .NET V4 SDK per gestire le risorse dell'account API SQL di Azure Cosmos DB.
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: c927ef071dec6e87e82a9634ccfc7097f2f187e6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488317"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Guida introduttiva: Compilare un'app console con .NET V4 SDK per gestire le risorse dell'account API SQL di Azure Cosmos DB.

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Per iniziare a usare la libreria client dell'API SQL di Azure Cosmos DB per .NET, seguire i passaggi descritti in questo documento per installare il pacchetto .NET V4 (Azure.Cosmos), compilare un'app e provare il codice di esempio per le operazioni CRUD di base sui dati archiviati in Azure Cosmos DB. 

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile usare Azure Cosmos DB per creare rapidamente database di chiavi/valori, di documenti e a grafo ed eseguire query su di essi. Usare la libreria client dell'API SQL di Azure Cosmos DB per .NET per:

* Creare un database e un contenitore Azure Cosmos
* Aggiungere dati di esempio al contenitore
* Eseguire query sui dati 
* eliminare il database

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [crearne una gratuitamente](https://azure.microsoft.com/free/) oppure [provare gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) senza una sottoscrizione di Azure e senza impegno. 
* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Per verificare la versione disponibile nell'ambiente corrente, è possibile eseguire `dotnet --version`.

## <a name="setting-up"></a>Configurazione

Questa sezione illustra la creazione di un account Azure Cosmos e la configurazione di un progetto che usa la libreria client dell'API SQL di Azure Cosmos DB per .NET per gestire le risorse. Il codice di esempio descritto in questo articolo crea un database `FamilyDatabase` e i membri della famiglia (ogni membro della famiglia è un elemento) all'interno del database. Ogni membro della famiglia ha proprietà come `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`. La proprietà `LastName` viene usata come chiave di partizione per il contenitore. 

### <a id="create-account"></a>Creare un account Azure Cosmos

Se si usa l'opzione [Prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) per creare un account Azure Cosmos, è necessario creare un account Azure Cosmos DB di tipo **API SQL**. È già stato creato automaticamente un account di test Azure Cosmos DB. Non è necessario creare l'account in modo esplicito, quindi è possibile ignorare questa sezione e passare a quella successiva.

Se si dispone di una propria sottoscrizione di Azure o si è provveduto a creare gratuitamente una sottoscrizione, è necessario creare un account Azure Cosmos in modo esplicito. Il codice seguente creerà un account Azure Cosmos con coerenza di sessione. L'account viene replicato in `South Central US` e `North Central US`.  

È possibile usare Azure Cloud Shell per creare l'account Azure Cosmos. Azure Cloud Shell è una shell interattiva, autenticata e accessibile tramite browser per la gestione delle risorse di Azure. Offre la flessibilità necessaria per scegliere l'esperienza shell più adatta al proprio modo di lavorare, ovvero Bash o PowerShell. Per questo argomento di avvio rapido, scegliere **Bash**. Azure Cloud Shell richiede anche un account di archiviazione, che è possibile creare quando richiesto.

Fare clic sul pulsante **Prova** accanto al codice riportato di seguito, scegliere la modalità **Bash**, selezionare **Crea un account di archiviazione** e accedere a Cloud Shell. Copiare e incollare il codice seguente in Azure Cloud Shell ed eseguirlo. Il nome dell'account Azure Cosmos deve essere globalmente univoco, pertanto assicurarsi di aggiornare il valore `mysqlapicosmosdb` prima di eseguire il comando.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-master enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

La creazione dell'account Azure Cosmos richiede un po' di tempo. Al termine dell'operazione, è possibile visualizzare l'output di conferma. Dopo che il comando viene completato correttamente, accedere al [portale di Azure](https://portal.azure.com/) e verificare che sia presente l'account Azure Cosmos con il nome specificato. È possibile chiudere la finestra di Azure Cloud Shell dopo la creazione della risorsa. 

### <a id="create-dotnet-core-app"></a>Creare una nuova app .NET

Creare una nuova applicazione .NET nell'ambiente di sviluppo integrato o nell'editor preferito. Aprire il prompt dei comandi di Windows o una finestra del terminale dal computer locale. Tutti i comandi delle sezioni successive dovranno essere eseguiti dal prompt dei comandi o dal terminale.  Eseguire il comando dotnet new riportato di seguito per creare una nuova app denominata `todo`. Il parametro --langVersion imposta la proprietà LangVersion nel file di progetto creato.

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

Spostarsi nella cartella dell'app appena creata. È possibile compilare l'applicazione con il comando seguente:

   ```bash
   cd todo
   dotnet build
   ```

L'output previsto al termine della creazione dovrebbe essere simile al seguente:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a id="install-package"></a>Installare il pacchetto di Azure Cosmos DB

Sempre nella directory dell'applicazione, installare la libreria client di Azure Cosmos DB per .NET Core usando il comando dotnet add package.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Copiare le credenziali dell'account Azure Cosmos dal portale di Azure

L'applicazione di esempio deve eseguire l'autenticazione all'account Azure Cosmos. Per eseguire l'autenticazione, è necessario passare le credenziali dell'account Azure Cosmos all'applicazione. Ottenere le credenziali dell'account Azure Cosmos seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Accedere all'account Azure Cosmos.

1. Aprire il riquadro **Chiavi** e copiare l'**URI** e la **CHIAVE PRIMARIA** dell'account. I valori dell'URI e delle chiavi verranno aggiunti a una variabile di ambiente nel passaggio successivo.

## <a id="object-model"></a>Modello a oggetti

Prima di iniziare a compilare l'applicazione, verranno esaminati la gerarchia di risorse in Azure Cosmos DB e il modello a oggetti usato per creare e accedere a queste risorse. Azure Cosmos DB crea le risorse nell'ordine seguente:

* Account Azure Cosmos 
* Database 
* Contenitori 
* Items

Per altre informazioni sulla gerarchia delle diverse entità, vedere l'articolo sull'[uso di database, contenitori ed elementi in Azure Cosmos DB](databases-containers-items.md). Per interagire con queste risorse, si useranno le classi .NET seguenti:

* CosmosClient: questa classe fornisce una rappresentazione logica lato client per il servizio Azure Cosmos DB. L'oggetto client viene usato per configurare ed eseguire richieste nel servizio.
* CreateDatabaseIfNotExistsAsync: questo metodo crea (se non esiste) o ottiene (se esiste già) una risorsa database come operazione asincrona. 
* CreateContainerIfNotExistsAsync: questo metodo crea (se non esiste) o ottiene (se esiste già) un contenitore come operazione asincrona. È possibile controllare il codice di stato della risposta per determinare se il contenitore è stato appena creato (201) o se è stato restituito un contenitore esistente (200). 
* CreateItemAsync: questo metodo crea un elemento nel contenitore.
* UpsertItemAsync: questo metodo crea un elemento all'interno del contenitore se non esiste già oppure sostituisce l'elemento se è già esistente. 
* GetItemQueryIterator: questo metodo crea una query per gli elementi in un contenitore di un database di Azure Cosmos usando un'istruzione SQL contenente valori con parametri. 
* DeleteAsync: elimina il database specificato dall'account Azure Cosmos. Il metodo `DeleteAsync` elimina solo il database.

 ## <a id="code-examples"></a>Esempi di codice

Il codice di esempio descritto in questo articolo crea un database relativo alla famiglia in Azure Cosmos DB. Il database contiene i dettagli della famiglia, ad esempio nome, indirizzo, località e genitori, figli e animali domestici associati. Prima di popolare i dati nell'account Azure Cosmos, definire le proprietà di un elemento della famiglia. Creare una nuova classe denominata `Family.cs` al livello radice dell'applicazione di esempio e aggiungervi il codice seguente:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>Aggiungere le direttive using e definire l'oggetto client

Dalla directory del progetto aprire il file `Program.cs` nell'editor e aggiungere le direttive using seguenti all'inizio dell'applicazione:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Aggiungere le variabili globali seguenti nella classe `Program`. Sono inclusi l'endpoint e le chiavi di autorizzazione, il nome del database e del contenitore che verranno creati. Assicurarsi di sostituire i valori dell'endpoint e delle chiavi di autorizzazione in base all'ambiente in uso. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Infine, sostituire il metodo `Main`:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Creare un database 

Definire il metodo `CreateDatabaseAsync` nella classe `program.cs`. Questo metodo crea `FamilyDatabase` se non esiste già.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Creare un contenitore

Definire il metodo `CreateContainerAsync` nella classe `Program`. Questo metodo crea `FamilyContainer` se non esiste già. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Creare un elemento

Creare un elemento della famiglia aggiungendo il metodo `AddItemsToContainerAsync` con il codice seguente. Per creare un elemento, è possibile usare il metodo `CreateItemAsync` o `UpsertItemAsync`:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Eseguire una query sugli elementi

Dopo aver inserito un elemento, è possibile eseguire una query per ottenere i dettagli della famiglia "Andersen". Il codice seguente illustra come eseguire la query usando direttamente la query SQL. La query SQL per ottenere i dettagli della famiglia "Anderson" è: `SELECT * FROM c WHERE c.LastName = 'Andersen'`. Definire il metodo `QueryItemsAsync` nella classe `Program` e aggiungervi il codice seguente:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Sostituire un elemento 

Eseguire la lettura di un elemento della famiglia e quindi aggiornarlo aggiungendo il metodo `ReplaceFamilyItemAsync` con il codice seguente.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Eliminare un elemento 

Eliminare un elemento della famiglia aggiungendo il metodo `DeleteFamilyItemAsync` con il codice seguente.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>eliminare il database 

È infine possibile eliminare il database aggiungendo il metodo `DeleteDatabaseAndCleanupAsync` con il codice seguente:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

Dopo aver aggiunto tutti i metodi necessari, salvare il file `Program`. 

## <a name="run-the-code"></a>Eseguire il codice

Successivamente, compilare ed eseguire l'applicazione per creare le risorse di Azure Cosmos DB.

   ```bash
   dotnet run
   ```

Quando si esegue l'applicazione, viene generato l'output seguente. È anche possibile accedere al portale di Azure e verificare che le risorse siano state create:

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
   End of demo, press any key to exit.
   ```

È possibile verificare che i dati vengano creati accedendo al portale di Azure e visualizzando gli elementi necessari nell'account Azure Cosmos. 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile usare l'interfaccia della riga di comando di Azure o Azure PowerShell per rimuovere l'account Azure Cosmos e il gruppo di risorse corrispondente. Il comando seguente mostra come eliminare il gruppo di risorse usando l'interfaccia della riga di comando di Azure:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come creare un account Azure Cosmos e come creare un database e un contenitore con un'app .NET Core. È ora possibile importare dati aggiuntivi nell'account Azure Cosmos con le istruzioni incluse nell'articolo seguente. 

> [!div class="nextstepaction"]
> [Importare dati in Azure Cosmos DB](import-data.md)

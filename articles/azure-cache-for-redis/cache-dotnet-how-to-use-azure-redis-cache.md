---
title: 'Guida introduttiva: Come usare Cache Redis di Azure con le app .NET Core | Microsoft Docs'
description: Questa guida introduttiva illustra come accedere a Cache Redis di Azure dalle app .NET
services: azure-cache-for-redis,app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: c502f74c-44de-4087-8303-1b1f43da12d5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/18/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 6b2ab1ba89fbc2b271ede177a906a1919c14023d
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557666"
---
# <a name="quickstart-use-azure-cache-for-redis-with-a-net-application"></a>Guida introduttiva: Usare Cache Redis di Azure con un'applicazione .NET



Questa guida introduttiva illustra come iniziare a usare Cache Redis di Microsoft Azure con .NET. Cache Redis di Microsoft Azure si basa sulla popolare cache Redis open source e consente di accedere a una cache Redis sicura e dedicata gestita da Microsoft. Una cache creata con Cache Redis di Azure sarà accessibile da qualsiasi applicazione all'interno di Microsoft Azure.

In questa guida introduttiva si userà il client [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) con codice C\# in un'app console. Verrà creata una cache e poi verrà configurata l'app client .NET. Verranno quindi aggiunti e aggiornati gli oggetti nella cache. 

![App console completata](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-complete.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* [Visual Studio](https://www.visualstudio.com/downloads/)
* Con il client StackExchange.Redis è richiesto [.NET Framework 4 o versione successiva](https://www.microsoft.com/net/download/dotnet-framework-runtime).

## <a name="create-a-cache"></a>Creare una cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Creare un file nel computer denominato *CacheSecrets.config* e inserirlo in una posizione in cui non verrà archiviato con il codice sorgente dell'applicazione di esempio. Per questo avvio rapido, il file *CacheSecrets.config* si trova nel percorso *C:\AppSecrets\CacheSecrets.config*.

Modificare il file *CacheSecrets.config* e aggiungere il contenuto seguente:

```xml
<appSettings>
    <add key="CacheConnection" value="<cache-name>.redis.cache.windows.net,abortConnect=false,ssl=true,password=<access-key>"/>
</appSettings>
```

Sostituire `<cache-name>` con il nome host della cache.

Sostituire `<access-key>` con la chiave primaria per la cache.


## <a name="create-a-console-app"></a>Creare un'app console

In Visual Studio fare clic su **File** > **Nuovo** > **Progetto**.

In **Visual C#** fare clic su **Desktop classico di Windows** e quindi fare clic su **App console** e su **OK** per creare una nuova applicazione console.


<a name="configure-the-cache-clients"></a>

## <a name="configure-the-cache-client"></a>Configurare il client della cache

In questa sezione, si configurerà l'applicazione console per usare il client [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) per .NET.

In Visual Studio fare clic su **Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti** ed eseguire il comando seguente dalla finestra Console di Gestione pacchetti.

```powershell
Install-Package StackExchange.Redis
```

Una volta completata l'installazione, il client della cache *StackExchange.Redis* è disponibile per l'uso con il progetto.


## <a name="connect-to-the-cache"></a>Connettersi alla cache

In Visual Studio aprire il file *App.config* e aggiornarlo per includere un attributo `appSettings` `file` che fa riferimento al file *CacheSecrets.config*.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.1" />
    </startup>

    <appSettings file="C:\AppSecrets\CacheSecrets.config"></appSettings>  

</configuration>
```

In Esplora soluzioni fare clic con il pulsante destro del mouse su **Riferimenti** e scegliere **Aggiungi riferimento**. Aggiungere un riferimento all'assembly **System.Configuration**.

Aggiungere le istruzioni `using` seguenti a *Program.cs*:

```csharp
using StackExchange.Redis;
using System.Configuration;
```

La connessione a Cache Redis di Azure è gestita dalla classe `ConnectionMultiplexer`. Questa classe deve essere condivisa e riutilizzata in tutta l'applicazione client. Non creare una nuova connessione per ogni operazione. 

Non archiviare mai le credenziali nel codice sorgente. Per semplificare questo esempio, viene usato solo un file di configurazione dei segreti esterno. Un approccio migliore potrebbe consistere nell'usare [Azure Key Vault con certificati](https://docs.microsoft.com/rest/api/keyvault/certificate-scenarios).

In *Program.cs* aggiungere i membri seguenti alla classe `Program` dell'applicazione console:

```csharp
        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });

        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }
```


Questo approccio per la condivisione di un'istanza di `ConnectionMultiplexer` nell'applicazione usa una proprietà statica che restituisce un'istanza connessa. Il codice offre un modo thread-safe per inizializzare solo una singola istanza di `ConnectionMultiplexer` connessa. La proprietà `abortConnect` è impostata su false, a indicare che la chiamata riuscirà anche se non viene stabilita una connessione a Cache Redis di Azure. Una delle funzionalità principali di `ConnectionMultiplexer` è il ripristino automatico della connettività alla cache non appena l'errore di rete o eventuali altri problemi vengono risolti.

Il valore dell'appSetting *CacheConnection* viene usato per fare riferimento alla stringa di connessione della cache dal portale di Azure come parametro password.

## <a name="executing-cache-commands"></a>Esecuzione dei comandi di cache

Aggiungere il codice seguente per la routine `Main` della classe `Program` per l'applicazione console:

```csharp
        static void Main(string[] args)
        {
            // Connection refers to a property that returns a ConnectionMultiplexer
            // as shown in the previous example.
            IDatabase cache = lazyConnection.Value.GetDatabase();

            // Perform cache operations using the cache object...

            // Simple PING command
            string cacheCommand = "PING";
            Console.WriteLine("\nCache command  : " + cacheCommand);
            Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

            // Simple get and put of integral data types into the cache
            cacheCommand = "GET Message";
            Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
            Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

            cacheCommand = "SET Message \"Hello! The cache is working from a .NET console app!\"";
            Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
            Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET console app!").ToString());

            // Demonstrate "SET Message" executed as expected...
            cacheCommand = "GET Message";
            Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
            Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

            // Get the client list, useful to see if connection list is growing...
            cacheCommand = "CLIENT LIST";
            Console.WriteLine("\nCache command  : " + cacheCommand);
            Console.WriteLine("Cache response : \n" + cache.Execute("CLIENT", "LIST").ToString().Replace("id=", "id="));

            lazyConnection.Value.Dispose();
        }
```

Cache Redis di Azure ha un numero configurabile di database (16 per impostazione predefinita) che è possibile usare per separare in modo logico i dati all'interno di un'istanza di Cache Redis di Azure. Il codice si connette al database predefinito, DB 0. Per altre informazioni, vedere [Informazioni sui database Redis](cache-faq.md#what-are-redis-databases) e [Configurazione predefinita del server Redis](cache-configure.md#default-redis-server-configuration).

Per archiviare e recuperare gli elementi nella cache, è possibile usare i metodi `StringSet` e `StringGet`.

Redis archivia la maggior parte dei dati come stringhe Redis, tuttavia queste stringhe possono contenere molti tipi di dati, inclusi dati binari serializzati, utilizzabili durante la memorizzazione di oggetti .NET nella cache.

Premere **CTRL+F5** per compilare ed eseguire l'app console.

Nell'esempio seguente è possibile notare che la chiave `Message` in precedenza aveva un valore nella cache che era stato impostato usando la Console Redis nel portale di Azure. L'app ha aggiornato questo valore memorizzato nella cache. L'app ha anche eseguito i comandi `PING` e `CLIENT LIST`.

![App console parziale](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>Gestire gli oggetti .NET nella cache

Cache Redis di Azure è in grado di memorizzare nella cache sia oggetti .NET che tipi di dati primitivi, ma prima della memorizzazione nella cache un oggetto .NET deve essere serializzato. La serializzazione di un oggetto .NET spetta allo sviluppatore dell'applicazione, che può scegliere liberamente il serializzatore da usare.

Un modo semplice per serializzare gli oggetti prevede l'uso dei metodi di serializzazione `JsonConvert` in [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) e la serializzazione a e da JSON. In questa sezione si aggiungerà un oggetto .NET alla cache.

In Visual Studio fare clic su **Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti** ed eseguire il comando seguente dalla finestra Console di Gestione pacchetti.

```powershell
Install-Package Newtonsoft.Json
```

Aggiungere l'istruzione `using` seguente all'inizio del file *Program.cs*:

```csharp
using Newtonsoft.Json;
```

Aggiungere la definizione di classe `Employee` seguente a *Program.cs*:

```csharp
        class Employee
        {
            public string Id { get; set; }
            public string Name { get; set; }
            public int Age { get; set; }

            public Employee(string EmployeeId, string Name, int Age)
            {
                this.Id = EmployeeId;
                this.Name = Name;
                this.Age = Age;
            }
        }
```

Alla fine della routine `Main()` in *Program.cs* e prima della chiamata a `Dispose()`, aggiungere le righe di codice seguenti alla cache e recuperare un oggetto .NET serializzato:

```csharp
            // Store .NET object to cache
            Employee e007 = new Employee("007", "Davide Columbo", 100);
            Console.WriteLine("Cache response from storing Employee .NET object : " + 
                cache.StringSet("e007", JsonConvert.SerializeObject(e007)));

            // Retrieve .NET object from cache
            Employee e007FromCache = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e007"));
            Console.WriteLine("Deserialized Employee .NET object :\n");
            Console.WriteLine("\tEmployee.Name : " + e007FromCache.Name);
            Console.WriteLine("\tEmployee.Id   : " + e007FromCache.Id);
            Console.WriteLine("\tEmployee.Age  : " + e007FromCache.Age + "\n");
```

Premere **CTRL+F5** per compilare ed eseguire l'app console per testare la serializzazione degli oggetti .NET. 

![App console completata](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>Pulire le risorse

Se si proseguirà con l'esercitazione successiva, sarà possibile conservare le risorse create in questo avvio rapido e riutilizzarle.

In caso contrario, se si è terminato il lavoro con l'applicazione di esempio di avvio rapido, è possibile eliminare le risorse di Azure create in questo avvio rapido per evitare i costi correlati. 

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile e comporta l'eliminazione definitiva del gruppo di risorse e di tutte le risorse incluse nel gruppo. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se le risorse per questo esempio sono state create all'interno di un gruppo di risorse esistente che contiene anche elementi da mantenere, è possibile eliminare ogni elemento singolarmente dai rispettivi pannelli anziché eliminare il gruppo di risorse.
>

Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Gruppi di risorse**.

Nella casella di testo **Filtra per nome...** immettere il nome del gruppo di risorse. Le istruzioni di questo articolo usano un gruppo di risorse denominato *TestResources*. Nel gruppo di risorse nell'elenco dei risultati fare clic su **...** quindi su **Elimina gruppo di risorse**.

![Delete](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-delete-resource-group.png)

Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Immettere il nome del gruppo di risorse per confermare e fare clic su **Elimina**.

Dopo qualche istante il gruppo di risorse e tutte le risorse che contiene vengono eliminati.



<a name="next-steps"></a>

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è visto come usare Cache Redis di Azure da un'applicazione .NET. Continuare con la guida introduttiva successiva per usare Cache Redis di Azure con un'app Web ASP.NET.

> [!div class="nextstepaction"]
> [Creare un'app Web ASP.NET che usa Cache Redis di Azure.](./cache-web-app-howto.md)



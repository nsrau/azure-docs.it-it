---
title: Creare un'app Web ASP.NET con Cache Redis | Microsoft Docs
description: In questo avvio rapido si apprenderà come creare un'app Web ASP.NET con Cache Redis
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 454e23d7-a99b-4e6e-8dd7-156451d2da7c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: quickstart
ms.date: 03/26/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: baaa53b04f608e2cb3546fcac6a6eb4eda4d3c4b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640755"
---
# <a name="quickstart-create-a-aspnet-web-app-with-redis-cache"></a>Avvio rapido: Creare un'app Web ASP.NET con Cache Redis


## <a name="introduction"></a>Introduzione

Questo avvio rapido illustra come creare e distribuire un'applicazione Web ASP.NET in un'app Web nel Servizio app di Azure usando Visual Studio 2017. L'applicazione di esempio si connette a un'istanza di Cache Redis di Azure per archiviare e recuperare i dati dalla cache. Al termine dell'avvio rapido si otterrà un'app Web in esecuzione, ospitata in Azure, che legge e scrive in un'istanza di Cache Redis di Azure.

![Test semplice completato in Azure](./media/cache-web-app-howto/cache-simple-test-complete-azure.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>prerequisiti

Per completare l'avvio rapido, sono necessari i prerequisiti seguenti:

* Installare [Visual Studio 2017](https://www.visualstudio.com/downloads/) con i carichi di lavoro seguenti:
    * Sviluppo Web e ASP.NET
    * Sviluppo di Azure

## <a name="create-the-visual-studio-project"></a>Creare il progetto di Visual Studio

Aprire Visual Studio e fare clic su **File**, **Nuovo**, **Progetto**.

![Crea progetto](./media/cache-web-app-howto/cache-create-project.png)

Nella finestra di dialogo Nuovo progetto seguire questa procedura:

1. Espandere il nodo **Visual C#** nell'elenco **Modelli**.
1. Selezionare **Cloud**.
1. Fare clic su **Applicazione Web ASP.NET**.
1. Assicurarsi che sia selezionata l'opzione **.NET Framework 4.5.2** o versione successiva.
1. Assegnare un nome al progetto nella casella di testo **Nome**, in questo esempio **ContosoTeamStats**.
1. Fare clic su **OK**.

Verrà visualizzata una schermata Nuova applicazione Web ASP.NET:

![Seleziona modello progetto](./media/cache-web-app-howto/cache-select-template.png)

Selezionare **MVC** come tipo di progetto.

Assicurarsi che per l'impostazione **Autenticazione** sia specificato **Nessuna autenticazione**. A seconda della versione di Visual Studio, il valore predefinito può essere diverso. Per modificarlo, fare clic su **Modifica autenticazione** e selezionare **Nessuna autenticazione**.

Fare clic su **OK** per creare il progetto.

## <a name="create-a-cache"></a>Creare una cache

A questo punto creare la cache per l'app.

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

> [!TIP]
> La chiave di accesso secondaria viene usata nella rotazione delle chiavi come chiave alternativa mentre si rigenera la chiave di accesso primaria.
>

Salvare il file.

## <a name="update-the-mvc-application"></a>Aggiornare l'applicazione MVC

In questa sezione si aggiornerà l'applicazione per supportare una nuova visualizzazione con un test semplice con un'istanza di Cache Redis di Azure.

* [Aggiornare il file web.config con un'impostazione di app per la cache](#Update-the-webconfig-file-with-an-app-setting-for-the-cache)
* [Configurare l'applicazione per l'uso del client StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
* [Aggiornare HomeController e Layout](#update-the-homecontroller-and-layout)
* [Aggiungere una nuova visualizzazione di RedisCache](#add-a-new-rediscache-view)

### <a name="update-the-webconfig-file-with-an-app-setting-for-the-cache"></a>Aggiornare il file web.config con un'impostazione di app per la cache

Quando si esegue l'applicazione in locale, queste informazioni in *CacheSecrets.config* vengono usate per connettersi all'istanza di Cache Redis di Azure. In un secondo momento si distribuirà l'applicazione in Azure. A questo punto si configurerà un'impostazione di app in Azure che l'applicazione userà per recuperare le informazioni di connessione nella cache anziché usare questo file. Poiché *CacheSecrets.config* non viene distribuito in Azure con l'applicazione, si userà solo se si esegue l'applicazione in locale. È opportuno mantenere queste informazioni nel modo più sicuro possibile per evitare accessi non autorizzati ai dati della cache.

In **Esplora soluzioni** aprire il file *web.config* facendo doppio clic su di esso.

![Web.config](./media/cache-web-app-howto/cache-web-config.png)

Nel file *web.config* trovare l'elemento `<appSetting>` e aggiungere il seguente attributo `file`. Se è stato usato un nome o un percorso di file diverso, sostituire i valori dell'esempio con questi valori.

* Prima: `<appSettings>`
* Dopo: ` <appSettings file="C:\AppSecrets\CacheSecrets.config">`

Il runtime ASP.NET unisce il contenuto del file esterno con il markup nell'elemento `<appSettings>` . Il runtime ignora l'attributo del file, se non è possibile trovare il file specificato. I segreti, ovvero la stringa di connessione per la cache, non sono inclusi come parte del codice sorgente per l'applicazione. Quando si distribuisce l'app Web in Azure, il file *CacheSecrests.config* non verrà distribuito.

### <a name="configure-the-application-to-use-stackexchangeredis"></a>Configurare l'applicazione per l'uso di StackExchange.Redis

Per configurare un'app per usare il pacchetto NuGet [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) per Visual Studio, fare clic su **Strumenti > Gestione pacchetti NuGet > Console di Gestione pacchetti**.

Eseguire questo comando nella finestra `Package Manager Console`:

```powershell
Install-Package StackExchange.Redis
```

Il pacchetto NuGet scarica e aggiunge i riferimenti ad assembly necessari per consentire all'applicazione client di accedere a Cache Redis di Azure con il client della cache StackExchange.Redis. Se si preferisce usare una versione con nome sicuro della libreria client `StackExchange.Redis`, installare il pacchetto `StackExchange.Redis.StrongName`.

### <a name="update-the-homecontroller-and-layout"></a>Aggiornare HomeController e Layout

In **Esplora soluzioni** espandere la cartella **Controller** e aprire il file *HomeController.cs*.

Aggiungere le due istruzioni `using` seguenti all'inizio del file per supportare le impostazioni dell'app e del client della cache.

```csharp
using System.Configuration;
using StackExchange.Redis;
```

Aggiungere il metodo seguente alla classe `HomeController` per supportare una nuova azione `RedisCache` che esegue alcuni comandi sulla nuova cache.

```csharp
    public ActionResult RedisCache()
    {
        ViewBag.Message = "A simple example with Azure Redis Cache on ASP.NET.";

        var lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });

        // Connection refers to a property that returns a ConnectionMultiplexer
        // as shown in the previous example.
        IDatabase cache = lazyConnection.Value.GetDatabase();

        // Perform cache operations using the cache object...

        // Simple PING command
        ViewBag.command1 = "PING";
        ViewBag.command1Result = cache.Execute(ViewBag.command1).ToString();

        // Simple get and put of integral data types into the cache
        ViewBag.command2 = "GET Message";
        ViewBag.command2Result = cache.StringGet("Message").ToString();

        ViewBag.command3 = "SET Message \"Hello! The cache is working from ASP.NET!\"";
        ViewBag.command3Result = cache.StringSet("Message", "Hello! The cache is working from ASP.NET!").ToString();

        // Demostrate "SET Message" executed as expected...
        ViewBag.command4 = "GET Message";
        ViewBag.command4Result = cache.StringGet("Message").ToString();

        // Get the client list, useful to see if connection list is growing...
        ViewBag.command5 = "CLIENT LIST";
        ViewBag.command5Result = cache.Execute("CLIENT", "LIST").ToString().Replace(" id=", "\rid=");

        lazyConnection.Value.Dispose();

        return View();
    }
```

In **Esplora soluzioni** espandere la cartella **Views**>**Shared** e aprire il file *_Layout.cshtml*.

Sostituire:

```csharp
@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
```

Con:

```csharp
@Html.ActionLink("Azure Redis Cache Test", "RedisCache", "Home", new { area = "" }, new { @class = "navbar-brand" })
```

### <a name="add-a-new-rediscache-view"></a>Aggiungere una nuova visualizzazione di RedisCache

In **Esplora soluzioni** espandere la cartella **Views** e fare clic con il pulsante destro del mouse sula cartella **Home**. Scegliere **Aggiungi** > **Visualizza...** .

Nella finestra di dialogo Aggiungi visualizzazione immettere **RedisCache** per il nome della visualizzazione e fare clic su **Aggiungi**.

Sostituire il codice nel file *RedisCache.cshtml* con il codice seguente:

```csharp
@{
    ViewBag.Title = "Azure Redis Cache Test";
}

<h2>@ViewBag.Title.</h2>
<h3>@ViewBag.Message</h3>
<br /><br />
<table border="1" cellpadding="10">
    <tr>
        <th>Command</th>
        <th>Result</th>
    </tr>
    <tr>
        <td>@ViewBag.command1</td>
        <td><pre>@ViewBag.command1Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command2</td>
        <td><pre>@ViewBag.command2Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command3</td>
        <td><pre>@ViewBag.command3Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command4</td>
        <td><pre>@ViewBag.command4Result</pre></td>
    </tr>
    <tr>
        <td>@ViewBag.command5</td>
        <td><pre>@ViewBag.command5Result</pre></td>
    </tr>
</table>
```

## <a name="run-the-app-locally"></a>Eseguire l'app in locale

Per impostazione predefinita il progetto è configurato per ospitare l'applicazione in locale in [IIS Express](https://docs.microsoft.com/iis/extensions/introduction-to-iis-express/iis-express-overview) per i test e il debug.

Nel menu di Visual Studio fare clic su **Debug** > **Avvia debug** per compilare e avviare l'app in locale per i test e il debug.

Nel browser fare clic sull'opzione di **test di Cache Redis di Azure** sulla barra di spostamento.

Nell'esempio seguente è possibile notare che la chiave `Message` in precedenza aveva un valore nella cache che era stato impostato usando la Console Redis nel portale. L'app ha aggiornato questo valore memorizzato nella cache. L'app ha anche eseguito i comandi `PING` e `CLIENT LIST`.

![Testo semplice completato in locale](./media/cache-web-app-howto/cache-simple-test-complete-local.png)

## <a name="publish-and-run-in-azure"></a>Pubblicare ed eseguire l'app in Azure

Dopo avere testato correttamente l'app in locale, l'app verrà distribuita in Azure ed eseguita nel cloud.

### <a name="publish-the-app-to-azure"></a>Pubblicare l'app in Azure

In Visual Studio fare clic con il pulsante destro del mouse sul nodo del progetto in Esplora soluzioni e scegliere **Pubblica**.

![Pubblica](./media/cache-web-app-howto/cache-publish-app.png)

Fare clic su **Servizio app di Microsoft Azure**, scegliere **Crea nuovo** e fare clic su **Pubblica**.

![Pubblica nel servizio app](./media/cache-web-app-howto/cache-publish-to-app-service.png)

Nella finestra di dialogo **Crea servizio app** apportare le seguenti modifiche:

| Impostazione | Valore consigliato | DESCRIZIONE |
| ------- | :---------------: | ----------- |
| **Nome app** | Usare l'impostazione predefinita. | Il nome dell'app sarà il nome host per l'app quando viene distribuita in Azure. Se necessario, per rendere il nome univoco è possibile aggiungere un suffisso timestamp. |
| **Sottoscrizione** | Scegliere la sottoscrizione di Azure | Eventuali addebiti di hosting correlati verranno addebitati a questa sottoscrizione. Se sono presenti più sottoscrizioni di Azure, verificare che sia selezionata la sottoscrizione desiderata.|
| **Gruppo di risorse** | Usare lo stesso gruppo di risorse in cui è stata creata la cache, ad esempio *TestResourceGroup*. | Il gruppo di risorse consente di gestire tutte le risorse come gruppo. In un secondo momento quando si vuole eliminare l'app, è sufficiente eliminare il gruppo. |
| **Piano di servizio app** | Fare clic su **Nuovo** e creare un nuovo piano di servizio app denominato *TestingPlan*. <br />Usare lo stesso **percorso** usato durante la creazione della cache. <br />Scegliere il livello **Gratuito** per le dimensioni. | Un piano di servizio app definisce un set di risorse di calcolo per l'esecuzione di un'app Web. |

![Finestra di dialogo Servizio app](./media/cache-web-app-howto/cache-create-app-service-dialog.png)

Dopo avere configurato le impostazioni di hosting del servizio app, fare clic su **Crea** per creare un nuovo servizio app per l'app.

Monitorare la finestra **Output** in Visual Studio per esaminare lo stato della pubblicazione in Azure. Al termine delle operazioni di pubblicazione, l'URL per il servizio app viene registrato come illustrato di seguito:

![Pubblicazione dell'output](./media/cache-web-app-howto/cache-publishing-output.png)

### <a name="add-the-app-setting-for-the-cache"></a>Aggiungere l'impostazione dell'app per la cache

Al termine della pubblicazione per il nuovo servizio app, aggiungere una nuova impostazione dell'app. Questa impostazione verrà usata per archiviare le informazioni di connessione della cache. Digitare il nome dell'app nella barra di ricerca nella parte superiore del portale di Azure per individuare il nuovo servizio app appena creato.

![Ricerca del servizio app](./media/cache-web-app-howto/cache-find-app-service.png)

Aggiungere una nuova impostazione dell'app denominata **CacheConnection** per l'app da usare per connettersi alla cache. Usare lo stesso valore configurato per `CacheConnection` nel file *CacheSecrets.config*. Il valore contiene la chiave di accesso e il nome host della cache.

![Aggiungi impostazione app](./media/cache-web-app-howto/cache-add-app-setting.png)

### <a name="run-the-app-in-azure"></a>Eseguire l'app in Azure

Nel browser passare all'URL per il servizio app. L'URL viene visualizzato nei risultati dell'operazione di pubblicazione nella finestra Output in Visual Studio. È disponibile anche nel portale di Azure nella pagina Panoramica del Servizio app creato.

Fare clic sull'opzione del **test di Cache Redis di Azure** sulla barra di spostamento per testare l'accesso alla cache.

![Test semplice completato in Azure](./media/cache-web-app-howto/cache-simple-test-complete-azure.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si proseguirà con l'esercitazione successiva, sarà possibile conservare le risorse create in questo avvio rapido e riutilizzarle.

In caso contrario, se si è terminato il lavoro con l'applicazione di esempio di avvio rapido, è possibile eliminare le risorse di Azure create in questo avvio rapido per evitare i costi correlati. 

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile e comporta l'eliminazione definitiva del gruppo di risorse e di tutte le risorse incluse nel gruppo. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se le risorse per questo esempio sono state create all'interno di un gruppo di risorse esistente che contiene anche elementi da mantenere, è possibile eliminare ogni elemento singolarmente dai rispettivi pannelli anziché eliminare il gruppo di risorse.
>

Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Gruppi di risorse**.

Nella casella di testo **Filtra per nome...** immettere il nome del gruppo di risorse. Le istruzioni di questo articolo usano un gruppo di risorse denominato *TestResources*. Nel gruppo di risorse nell'elenco dei risultati fare clic su **...** quindi su **Elimina gruppo di risorse**.

![Delete](./media/cache-web-app-howto/cache-delete-resource-group.png)

Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Immettere il nome del gruppo di risorse per confermare e fare clic su **Elimina**.

Dopo qualche istante il gruppo di risorse e tutte le risorse che contiene vengono eliminati.

## <a name="next-steps"></a>Passaggi successivi

Nell'esercitazione successiva si userà Cache Redis di Azure in uno scenario più realistico per migliorare le prestazioni di un'app. È possibile aggiornare l'applicazione per inserire i risultati del tabellone punteggi nella cache usando il modello cache-aside con ASP.NET e un database.

> [!div class="nextstepaction"]
> [Creare un tabellone punteggi cache-aside in ASP.NET](cache-web-app-cache-aside-leaderboard.md)

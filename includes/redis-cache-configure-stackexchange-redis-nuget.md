Le applicazioni .NET possono usare il client della cache **StackExchange.Redis**, che può essere configurato in Visual Studio con un pacchetto NuGet per semplificare la configurazione delle applicazioni client della cache.

> [!NOTE]
> Per ulteriori informazioni, vedere la pagina di Github [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis) e la documentazione del client della cache [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis#documentation).
> 
> 

Per configurare un'applicazione client in Visual Studio con il pacchetto NuGet StackExchange.Redis, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**.

![Manage NuGet packages](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Digitare **StackExchange.Redis** o **StackExchange.Redis.StrongName** nella casella di testo di ricerca, selezionare la versione desiderata nei risultati e fare clic su **Installa**.

> [!NOTE]
> Se si preferisce usare una versione con nome sicuro della libreria client **StackExchange.Redis**, scegliere **StackExchange.Redis.StrongName**; in caso contrario, scegliere **StackExchange.Redis**.
> 
> 

![StackExchange.Redis NuGet package](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

Il pacchetto NuGet scarica e aggiunge i riferimenti ad assembly necessari per consentire all'applicazione client di accedere a Cache Redis di Azure con il client della cache StackExchange.Redis.

> [!NOTE]
> Se il progetto è stato configurato per utilizzare StackExchange.Redis, è possibile controllare la presenza di aggiornamenti per il pacchetto da **Gestione pacchetti NuGet**. Per controllare e installare le versioni aggiornate del pacchetto NuGet StackExchange.Redis, fare clic su **Aggiornamenti** nella finestra di **Gestione pacchetti NuGet**. Se è disponibile un aggiornamento per il pacchetto NuGet StackExchange.Redis, è possibile aggiornare il progetto in modo da utilizzare la versione aggiornata.
> 
> 

<!---HONumber=AcomDC_0622_2016-->
Le applicazioni .NET possono usare il client della cache **StackExchange.Redis**, che può essere configurato in Visual Studio con un pacchetto NuGet per semplificare la configurazione delle applicazioni client della cache.

>[AZURE.NOTE] Per ulteriori informazioni, vedere la pagina di Github [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis) e la documentazione del client della cache [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis#documentation).

Per configurare un'applicazione client in Visual Studio con il pacchetto NuGet StackExchange.Redis, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**.

![Manage NuGet packages](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Digitare **StackExchange.Redis** o **StackExchange.Redis.StrongName** nella casella di testo di ricerca, selezionare la versione desiderata nei risultati e fare clic su **Installa**.

>[AZURE.NOTE] Se si preferisce usare una versione con nome sicuro della libreria client **StackExchange.Redis**, scegliere **StackExchange.Redis.StrongName**; in caso contrario, scegliere **StackExchange.Redis**.

![StackExchange.Redis NuGet package](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

Il pacchetto NuGet scarica e aggiunge i riferimenti ad assembly necessari per consentire all'applicazione client di accedere a Cache Redis di Azure con il client della cache StackExchange.Redis.

<!---HONumber=AcomDC_0615_2016-->
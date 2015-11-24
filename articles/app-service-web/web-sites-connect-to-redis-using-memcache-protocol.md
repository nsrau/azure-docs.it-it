<properties
	pageTitle="Connettere un'app Web in Azure App Service a Cache Redis mediante il protocollo Memcache | Microsoft Azure"
	description="Connettere un'app Web nel servizio app di Azure a Cache Redis mediante il protocollo Memcache"
	services="app-service\web"
	documentationCenter="php"
	authors="SyntaxC4"
	manager="wpickett"
	editor="riande"/>

<tags
	ms.service="app-service-web"
	ms.devlang="php"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="windows"
	ms.workload="na"
	ms.date="09/16/2015"
	ms.author="cfowler"/>

# Connettere un'app Web nel servizio app di Azure a Cache Redis mediante il protocollo Memcache

Questo articolo illustrerà come connettere un'app Web di WordPress in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) a [Cache Redis di Azure][12] mediante il protocollo [Memcache][13]. Se si dispone di un'app Web esistente che usa un server Memcached per l'inserimento nella cache in memoria, è possibile sottoporla a migrazione in Servizio app di Azure e usare la soluzione di memorizzazione nella cache del produttore in Microsoft Azure apportando modifiche minime o addirittura nessuna modifica al codice dell'applicazione. È anche possibile usare la familiarità già acquisita con Memcache per creare app distribuite e con scalabilità elevata in Azure App Service con Cache Redis di Azure per l'inserimento nella cache in memoria, usando framework di applicazioni comuni, ad esempio .NET, PHP, Node.js, Java e Python.

App Web del servizio app di Azure rende possibile questo scenario di applicazione con lo shim Memcache per app Web, ovvero un server locale Memcached che funziona come proxy Memcache per memorizzare nella cache le chiamate a Cache Redis di Azure. In questo modo qualsiasi app può comunicare usando il protocollo Memcache per memorizzare i dati nella cache con Cache Redis. Questo shim Memcache funziona a livello di protocollo, in modo da poter essere usato da qualsiasi applicazione o framework di applicazioni, purché comunichi usando il protocollo Memcache.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Prerequisiti

Lo shim Memcache per app Web può essere usato con qualsiasi applicazione, purché comunichi tramite il protocollo Memcache. Per questo particolare esempio, l'applicazione di riferimento è un sito WordPress scalabile di cui è possibile eseguire il provisioning da Azure Marketplace.

Seguire la procedura descritta in questi articoli:

* [Provisioning di un'istanza del servizio Cache Redis di Azure][1]
* [Distribuzione di un sito WordPress scalabile in Azure][0]

Dopo aver distribuito il sito WordPress scalabile e aver eseguito il provisioning di un'istanza di Cache Redis, si è pronti per abilitare lo shim Memcache in App Web del servizio app di Azure.

## Abilitare lo shim Memcache per app Web

Per configurare lo shim Memcache, è necessario creare tre impostazioni di app. Questa operazione può essere eseguita usando un'ampia gamma di metodi, inclusi il [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715), il [portale precedente][3], i [cmdlet PowerShell di Azure][5] o l'[interfaccia della riga di comando di Azure][5]. Ai fini di questo post, per configurare le impostazioni dell'app verrà usato il [portale di Azure][4]. I seguenti valori possono essere recuperati dal pannello **Impostazioni** dell'istanza di Cache Redis.

![Pannello Impostazioni di Cache Redis di Azure](./media/web-sites-connect-to-redis-using-memcache-protocol/1-azure-redis-cache-settings.png)

### Aggiungere l'impostazione di app REDIS\_HOST

La prima impostazione di app che è necessario creare è **REDIS\_HOST**. Questa impostazione definisce la destinazione a cui lo shim inoltra le informazioni della cache. Il valore richiesto per l'impostazione di app REDIS\_HOST può essere recuperato dal pannello **Proprietà** dell'istanza di Cache Redis.

![Nome host di Cache Redis di Azure](./media/web-sites-connect-to-redis-using-memcache-protocol/2-azure-redis-cache-hostname.png)

Impostare la chiave dell'impostazione di app su **REDIS\_HOST** e il valore dell'impostazione di app sul **nome host** dell'istanza di Cache Redis.

![Impostazione REDIS\_HOST per app Web](./media/web-sites-connect-to-redis-using-memcache-protocol/3-azure-website-appsettings-redis-host.png)

### Aggiungere l'impostazione di app REDIS\_KEY

La seconda impostazione di app che è necessario creare è **REDIS\_KEY**. Questa impostazione fornisce il token di autenticazione necessario per accedere in modo sicuro all'istanza di Cache Redis. È possibile recuperare il valore richiesto per l'impostazione di app REDIS\_KEY dal pannello **Chiavi di accesso** dell'istanza di Cache Redis.

![Chiave primaria di Cache Redis di Azure](./media/web-sites-connect-to-redis-using-memcache-protocol/4-azure-redis-cache-primarykey.png)

Impostare la chiave dell'impostazione di app su **REDIS\_KEY** e il valore dell'impostazione di app sulla **chiave primaria** dell'istanza di Cache Redis.

![Impostazione REDIS\_KEY per siti Web di Azure](./media/web-sites-connect-to-redis-using-memcache-protocol/5-azure-website-appsettings-redis-primarykey.png)

### Aggiungere l'impostazione di app MEMCACHESHIM\_REDIS\_ENABLE

L'ultima impostazione di app viene usata per abilitare lo shim Memcache in app Web che usano REDIS\_HOST e REDIS\_KEY per la connessione a Cache Redis di Azure e per l'inoltro delle chiamate alla cache. Impostare la chiave dell'impostazione di app su **MEMCACHESHIM\_REDIS\_ENABLE** e il valore su **true**.

![Impostazione MEMCACHESHIM\_REDIS\_ENABLE per app Web](./media/web-sites-connect-to-redis-using-memcache-protocol/6-azure-website-appsettings-enable-shim.png)

Dopo aver aggiunto le tre (3) impostazioni di app, fare clic su **Salva**.

## Abilitare l'estensione Memcache per PHP

Affinché l'applicazione supporti il protocollo Memcache, è necessario installare l'estensione Memcache per PHP, il framework del linguaggio per il sito WordPress.

### Scaricare l'estensione php\_memcache

Passare a [PECL][6]. Nella categoria di memorizzazione nella cache fare clic su [memcache][7]. Nella colonna dei download fare clic sul collegamento relativo alle DLL.

![Sito Web di PHP PECL](./media/web-sites-connect-to-redis-using-memcache-protocol/7-php-pecl-website.png)

Scaricare il link Non-Thread Safe (NTS) x86 per la versione di PHP abilitata nelle app Web (il valore predefinito è PHP 5.4).

![Pacchetto Memcache sul sito Web di PHP PECL](./media/web-sites-connect-to-redis-using-memcache-protocol/8-php-pecl-memcache-package.png)

### Abilitare l'estensione php\_memcache

Dopo il download del file, decomprimerlo e caricare il file **php\_memcache.dll** nella directory **d:\\home\\site\\wwwroot\\bin\\ext**. Dopo il caricamento del file php\_memcache.dll nell'app Web, è necessario abilitare l'estensione nel runtime PHP. Per abilitare l'estensione Memcache nel portale di Azure, aprire il pannello **Impostazioni applicazione** per l'app Web e quindi aggiungere una nuova impostazione di app con chiave **PHP\_EXTENSIONS** e valore **bin\\ext\\php\_memcache.dll**.


> [AZURE.NOTE]Se l'app Web deve caricare più estensioni PHP, il valore di PHP\_EXTENSIONS deve essere un elenco di percorsi relativi di file DLL delimitati da virgole.

![Impostazione PHP\_EXTENSIONS per app Web](./media/web-sites-connect-to-redis-using-memcache-protocol/9-azure-website-appsettings-php-extensions.png)

Al termine, fare clic su **Salva**.

## Installare il plug-in Memcache WordPress

> [AZURE.NOTE]È anche possibile scaricare il [plug-in Memcached Object Cache](https://wordpress.org/plugins/memcached/) da WordPress.org.

Nella pagina dei plug-in di WordPress fare clic su **Aggiungi nuovo**.

![Pagina del plug-in WordPress](./media/web-sites-connect-to-redis-using-memcache-protocol/10-wordpress-plugin.png)

Nella casella di ricerca digitare **memcached** e premere **Invio**.

![Aggiungere il nuovo plug-in WordPress](./media/web-sites-connect-to-redis-using-memcache-protocol/11-wordpress-add-new-plugin.png)

Trovare **Memcached Object Cache** nell'elenco e quindi fare clic su **Installa ora**.

![Installare il plug-in Memcache WordPress](./media/web-sites-connect-to-redis-using-memcache-protocol/12-wordpress-install-memcache-plugin.png)

### Abilitare il plug-in Memcache WordPress

>[AZURE.NOTE]Seguire le istruzioni in questo blog su come [abilitare un'estensione del sito nelle app Web][8] per installare Visual Studio Team Services.

Nel file `wp-config.php` aggiungere il seguente frammento di codice al di sopra del commento di arresto della modifica verso la fine del file.

```php
$memcached_servers = array(
	'default' => array('localhost:' . getenv("MEMCACHESHIM_PORT"))
);
```

Dopo che il frammento di codice è stato incollato, il documento verrà salvato automaticamente.

Il passaggio successivo consiste nell'abilitare il plug-in della cache di oggetti. Questa operazione viene eseguita trascinando **object-cache.php** dalla cartella **wp-content/memcached** sulla cartella **wp-content** per abilitare la funzionalità Memcached Object Cache.

![Individuare il plug-in object-cache.php di memcache](./media/web-sites-connect-to-redis-using-memcache-protocol/13-locate-memcache-object-cache-plugin.png)

Ora che il file **object-cache.php** si trova nella cartella **wp-content**, la funzionalità Memcached Object Cache è abilitata.

![Abilitare il plug-in object-cache.php di memcache](./media/web-sites-connect-to-redis-using-memcache-protocol/14-enable-memcache-object-cache-plugin.png)

## Verificare il corretto funzionamento del plug-in della cache di oggetti Memcached

Tutti i passaggi per abilitare lo shim Memcache per app Web sono stati completati. Resta solo da verificare che i dati popolino l'istanza di Cache Redis.

### Abilitare il supporto della porta non SSL in Cache Redis di Azure

>[AZURE.NOTE]Alla data in cui viene scritto questo articolo, l'interfaccia della riga di comando Redis non supporta la connettività SSL e pertanto sono necessarie le operazioni seguenti.

Nel portale di Azure passare all'istanza di Cache Redis creata per questa app Web. Dopo aver aperto il pannello della cache, fare clic sull'icona **Impostazioni**.

![Pulsante Impostazioni di Cache Redis di Azure](./media/web-sites-connect-to-redis-using-memcache-protocol/15-azure-redis-cache-settings-button.png)

Selezionare **Porte di accesso** dall'elenco.

![Porta di accesso di Cache Redis di Azure](./media/web-sites-connect-to-redis-using-memcache-protocol/16-azure-redis-cache-access-port.png)

Fare clic su **No** per **Consenti accesso solo tramite SSL**.

![Porta di accesso solo SSL di Cache Redis di Azure](./media/web-sites-connect-to-redis-using-memcache-protocol/17-azure-redis-cache-access-port-ssl-only.png)

La porta non SSL ora risulta impostata. Fare clic su **Salva**.

![Portale di accesso non SSL di Cache Redis di Azure](./media/web-sites-connect-to-redis-using-memcache-protocol/18-azure-redis-cache-access-port-non-ssl.png)

### Connettersi a Cache Redis di Azure da redis-cli

>[AZURE.NOTE]In questo passaggio si presuppone che Redis sia installato in locale nel computer di sviluppo. [Installare Redis in locale seguendo queste istruzioni][9].

Aprire la console della riga di comando desiderata e digitare il seguente comando:

```shell
redis-cli –h <hostname-for-redis-cache> –a <primary-key-for-redis-cache> –p 6379
```

Sostituire **<hostname-for-redis-cache>** con il nome host effettivo xxxxx.redis.cache.windows.net e **<primary-key-for-redis-cache>** con la chiave di accesso della cache, quindi premere **INVIO**. Dopo che l'interfaccia della riga di comando si è connessa all'istanza di Cache Redis, inviare un comando Redis. Nella seguente schermata si è scelto di elencare le chiavi.

![Connettersi a Cache Redis di Azure dall'interfaccia della riga di comando di Redis in Terminal](./media/web-sites-connect-to-redis-using-memcache-protocol/19-redis-cli-terminal.png)

La chiamata per elencare le chiavi deve restituire un valore. In caso contrario, provare a passare all'app Web e ripetere l'operazione.

## Conclusioni

Congratulazioni. L'app di WordPress dispone ora di una cache in memoria centralizzata per garantire una velocità effettiva maggiore. Tenere presente che lo shim Memcache per app Web può essere usato con qualsiasi client Memcache, indipendentemente dal linguaggio di programmazione o dal framework di applicazioni. Pubblicare eventuali commenti o domande sullo shim Memcache per app Web sui [forum MSDN][10] o su [Stackoverflow][11].

>[AZURE.NOTE]Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web ad App Service, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)


[0]: http://bit.ly/1F0m3tw
[1]: http://bit.ly/1t0KxBQ
[2]: http://manage.windowsazure.com
[3]: http://portal.azure.com
[4]: ../powershell-install-configure.md
[5]: /downloads
[6]: http://pecl.php.net
[7]: http://pecl.php.net/package/memcache
[8]: http://blog.syntaxc4.net/post/2015/02/05/how-to-enable-a-site-extension-in-azure-websites.aspx
[9]: http://redis.io/download#installation
[10]: https://social.msdn.microsoft.com/Forums/home?forum=windowsazurewebsitespreview
[11]: http://stackoverflow.com/questions/tagged/azure-web-sites
[12]: /services/cache/
[13]: http://memcached.org

<!---HONumber=Nov15_HO4-->
<properties
   pageTitle="Usare efficacemente gli ambienti DevOps per l'app Web"
   description="Informazioni su come usare gli slot di distribuzione per configurare e gestire più ambienti di sviluppo per l'applicazione"
   services="app-service\web"
   documentationCenter=""
   authors="sunbuild"
   manager="yochayk"
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="web"
   ms.date="12/24/2015"
   ms.author="sumuth"/>

# Usare efficacemente gli ambienti DevOps per le app Web

Questo articolo illustra come configurare e gestire le distribuzioni di applicazioni Web per più versioni dell'applicazione, ad esempio sviluppo, staging, controllo qualità e produzione. Ogni versione dell'applicazione può essere considerata come un ambiente di sviluppo per un'esigenza specifica del processo di distribuzione. Ad esempio, l'ambiente di controllo qualità può essere usato dal team di sviluppatori per testare la qualità dell'applicazione prima di eseguire il push delle modifiche in produzione. La configurazione di più ambienti di sviluppo può essere un'attività complessa, perché prevede il monitoraggio e la gestione delle risorse (calcolo, app Web, database, cache e così via) in questi ambienti e la distribuzione del contenuto da un ambiente all'altro.

## Configurazione di un ambiente non di produzione (staging, sviluppo, controllo qualità)
Una volta che l'app Web è operativa, il passaggio successivo prevede la creazione di un ambiente non di produzione. Per usare gli slot di distribuzione, assicurarsi che sia in esecuzione la modalità del piano di servizio app **Standard** o **Premium**. Gli slot di distribuzione sono in realtà app Web dal vivo con nomi host specifici. È possibile scambiare il contenuto dell'app Web e gli elementi delle configurazioni tra i due slot di distribuzione, incluso lo slot di produzione. La distribuzione dell'applicazione in uno slot di distribuzione presenta i seguenti vantaggi:

1. È possibile convalidare le modifiche alle app Web in uno slot di distribuzione di staging prima di scambiarlo con quello di produzione.
2. La distribuzione preliminare di un'app Web in uno slot e la successiva implementazione in un ambiente di produzione garantiscono che tutte le istanze dello slot vengano effettivamente eseguite prima di passare alla fase di produzione. Ciò consente di evitare i tempi di inattività al momento della distribuzione dell'app Web. Il reindirizzamento del traffico è lineare e nessuna richiesta viene eliminata in seguito alle operazioni di scambio. L'intero flusso di lavoro può essere automatizzata tramite la configurazione di [scambio automatico](web-sites-staged-publishing.md#configure-auto-swap-for-your-web-app) quando non è necessario spazio di pre-swapping convalida.
3. Dopo uno scambio, lo slot con l'app Web gestita temporaneamente includerà l'app Web di produzione precedente. Se le modifiche applicate nello slot di produzione non risultano corrette, è possibile ripetere immediatamente lo scambio dei due slot per recuperare l'ultima app Web con i dati corretti.

Per configurare uno slot di distribuzione di staging, vedere [Configurare ambienti di staging per le app Web nel servizio app di Azure](web-sites-staged-publishing.md). Ogni ambiente deve includere un set di risorse dedicato, ad esempio, se l'app Web usa un database, sia l'app Web di produzione che quella di staging devono usare database diversi. Aggiungere le risorse dell'ambiente di sviluppo di staging, ad esempio il database, le risorse di archiviazione o la cache per impostare l'ambiente di sviluppo di staging.

## Esempi di utilizzo di più ambienti di sviluppo

Per tutti i progetti è necessario prevedere la gestione del codice sorgente con almeno due ambienti, uno di sviluppo e uno di produzione. Tuttavia, quando si usano sistemi di gestione del contenuto (CMS), framework applicazioni e così via, le applicazioni potrebbero non supportare questo scenario predefinito. Questo accade per alcuni dei framework più diffusi descritti di seguito. Quando si usano framework o sistemi di gestione del contenuto, possono sorgere gli interrogativi seguenti:

1. Come eseguire la suddivisione in ambienti diversi
2. Quali sono i file che si possono modificare senza alcun impatto sugli aggiornamenti della versione del framework
3. Come gestire la configurazione per ambiente
4. Come gestire gli aggiornamenti delle versioni dei moduli/plug-in e gli aggiornamenti delle versioni principali dei framework

Esistono diversi modi per configurare più ambienti per il progetto. Gli esempi seguenti rappresentano solo uno di questi metodi per le rispettive applicazioni.

### WordPress
Questa sezione descrive come configurare un flusso di lavoro di distribuzione usando gli slot per WordPress. WordPress, come la maggior parte delle soluzioni CMS, non supporta per impostazione predefinita l'utilizzo di più ambienti di sviluppo. Il servizio app Web del servizio app offre alcune funzionalità che semplificano l'archiviazione delle impostazioni di configurazione all'esterno del codice.

Prima di creare uno slot di staging, configurare il codice dell'applicazione in modo che supporti più ambienti. Per supportare più ambienti in WordPress, è necessario modificare `wp-config.php` nell'app Web di sviluppo locale e aggiungere il codice seguente all'inizio del file. In questo modo, l'applicazione selezionerà la configurazione corretta in base all'ambiente selezionato.


	// Support multiple environments
	// set the config file based on current environment
	/**/
	if (strpos(filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING),'localhost') !== false) {
	    // local development
	    $config_file = 'config/wp-config.local.php';
	}
	elseif  ((strpos(getenv('WP_ENV'),'stage') !== false) ||  (strpos(getenv('WP_ENV'),'prod' )!== false )){
	      //single file for all azure development environments
	      $config_file = 'config/wp-config.azure.php';
	}
	$path = dirname(__FILE__) . '/';
	if (file_exists($path . $config_file)) {
	    // include the config file if it exists, otherwise WP is going to fail
	    require_once $path . $config_file;
	}



Nella directory radice dell'app Web creare una cartella denominata `config` e aggiungere due file: `wp-config.azure.php` e `wp-config.local.php` che rappresentano rispettivamente l'ambiente Azure e l'ambiente locale.

Copiare quanto segue in `wp-config.local.php`:

```
	
	<?php
	
	// MySQL settings
	/** The name of the database for WordPress */
	
	define('DB_NAME', 'yourdatabasename');
	
	/** MySQL database username */
	define('DB_USER', 'yourdbuser');
	
	/** MySQL database password */
	define('DB_PASSWORD', 'yourpassword');
	
	/** MySQL hostname */
	define('DB_HOST', 'localhost');
	/**
	 * For developers: WordPress debugging mode.
	 * * Change this to true to enable the display of notices during development.
	 * It is strongly recommended that plugin and theme developers use WP_DEBUG
	 * in their development environments.
	 */
	define('WP_DEBUG', true);
	
	//Security key settings
	define('AUTH_KEY',         'put your unique phrase here');
	define('SECURE_AUTH_KEY',  'put your unique phrase here');
	define('LOGGED_IN_KEY',    'put your unique phrase here');
	define('NONCE_KEY',        'put your unique phrase here');
	define('AUTH_SALT',        'put your unique phrase here');
	define('SECURE_AUTH_SALT', 'put your unique phrase here');
	define('LOGGED_IN_SALT',   'put your unique phrase here');
	define('NONCE_SALT',       'put your unique phrase here');
	
	/**
	 * WordPress Database Table prefix.
	 *
	 * You can have multiple installations in one database if you give each a unique
	 * prefix. Only numbers, letters, and underscores please!
	 */
	$table_prefix  = 'wp_';
```

Impostando le chiavi di sicurezza è possibile prevenire potenziali attacchi all'app Web, quindi usare valori univoci. Per generare la stringa per le chiavi di sicurezza indicate sopra, fare clic su questo [collegamento](https://api.wordpress.org/secret-key/1.1/salt) per passare al generatore automatico per creare nuove coppie di chiavi-valori.

Copiare il codice seguente in `wp-config.azure.php`:


```

	<?php
	// MySQL settings
	/** The name of the database for WordPress */
	
	define('DB_NAME', getenv('DB_NAME'));
	
	/** MySQL database username */
	define('DB_USER', getenv('DB_USER'));
	
	/** MySQL database password */
	define('DB_PASSWORD', getenv('DB_PASSWORD'));
	
	/** MySQL hostname */
	define('DB_HOST', getenv('DB_HOST'));
	
	/**
	* For developers: WordPress debugging mode.
	*
	* Change this to true to enable the display of notices during development.
	* It is strongly recommended that plugin and theme developers use WP_DEBUG
	* in their development environments.
	* Turn on debug logging to investigate issues without displaying to end user. For WP_DEBUG_LOG to
	* do anything, WP_DEBUG must be enabled (true). WP_DEBUG_DISPLAY should be used in conjunction
	* with WP_DEBUG_LOG so that errors are not displayed on the page */
	
	*/
	define('WP_DEBUG', getenv('WP_DEBUG'));
	define('WP_DEBUG_LOG', getenv('TURN_ON_DEBUG_LOG'));
	define('WP_DEBUG_DISPLAY',false);
	
	//Security key settings
	/** If you need to generate the string for security keys mentioned above, you can go the automatic generator to create new keys/values: https://api.wordpress.org/secret-key/1.1/salt **/
	define('AUTH_KEY' ,getenv('DB_AUTH_KEY'));
	define('SECURE_AUTH_KEY',  getenv('DB_SECURE_AUTH_KEY'));
	define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
	define('NONCE_KEY', getenv('DB_NONCE_KEY'));
	define('AUTH_SALT',  getenv('DB_AUTH_SALT'));
	define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
	define('LOGGED_IN_SALT',   getenv('DB_LOGGED_IN_SALT'));
	define('NONCE_SALT',   getenv('DB_NONCE_SALT'));
	
	/**
	* WordPress Database Table prefix.
	*
	* You can have multiple installations in one database if you give each a unique
	* prefix. Only numbers, letters, and underscores please!
	*/
	$table_prefix  = getenv('DB_PREFIX');
```

#### Usare percorsi relativi
Da ultimo, consentire all'app WordPress di usare i percorsi relativi. WordPress archivia le informazioni relative all'URL nel database. Questo rende più difficile lo spostamento del contenuto da un ambiente a un altro, perché è necessario aggiornare il database ogni volta che si passa dall'ambiente locale a quello di staging o da quello di staging a quello di produzione. Per ridurre i rischi associati alla distribuzione del database ogni volta che si esegue la distribuzione da un ambiente a un altro, usare il [plug-in per i collegamenti relativi alla radice](https://wordpress.org/plugins/root-relative-urls/) che può essere installato dal dashboard di amministrazione di WordPress o scaricato manualmente da [qui](https://downloads.wordpress.org/plugin/root-relative-urls.zip).

Aggiungere le voci seguenti al file `wp-config.php` prima del commento `That's all, stop editing!`:

```

    define('WP_HOME', 'http://' . filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	define('WP_SITEURL', 'http://' . filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	define('WP_CONTENT_URL', '/wp-content');
	define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
```

Attivare il plug-in dal menu `Plugins` nel dashboard di amministrazione di WordPress. Salvare le impostazioni relative al collegamento permanente per l'app WordPress.

#### Il file `wp-config.php` finale
Gli eventuali aggiornamenti principali di WordPress non avranno effetto sui file `wp-config.php`, `wp-config.azure.php` e `wp-config.local.php`. Questo è l'aspetto finale che avrà il file `wp-config.php`

```

	<?php
	/**
	 * The base configurations of the WordPress.
	 *
	 * This file has the following configurations: MySQL settings, Table Prefix,
	 * Secret Keys, and ABSPATH. You can find more information by visiting
	 *
	 * Codex page. You can get the MySQL settings from your web host.
	 *
	 * This file is used by the wp-config.php creation script during the
	 * installation. You don't have to use the web web app, you can just copy this file
	 * to "wp-config.php" and fill in the values.
	 *
	 * @package WordPress
	 */
	
	// Support multiple environments
	// set the config file based on current environment
	if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) { // local development
	    $config_file = 'config/wp-config.local.php';
	}
	elseif  ((strpos(getenv('WP_ENV'),'stage') !== false) ||  (strpos(getenv('WP_ENV'),'prod' )!== false )){
	    $config_file = 'config/wp-config.azure.php';
	}
	
	
	$path = dirname(__FILE__) . '/';
	if (file_exists($path . $config_file)) {
	    // include the config file if it exists, otherwise WP is going to fail
	    require_once $path . $config_file;
	}
	
	/** Database Charset to use in creating database tables. */
	define('DB_CHARSET', 'utf8');
	
	/** The Database Collate type. Don't change this if in doubt. */
	define('DB_COLLATE', '');
	
	
	/* That's all, stop editing! Happy blogging. */
	
	define('WP_HOME', 'http://' . filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	define('WP_SITEURL', 'http://' . filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	define('WP_CONTENT_URL', '/wp-content');
	define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	
	/** Absolute path to the WordPress directory. */
	if ( !defined('ABSPATH') )
		define('ABSPATH', dirname(__FILE__) . '/');
	
	/** Sets up WordPress vars and included files. */
	require_once(ABSPATH . 'wp-settings.php');
```

#### Configurare un ambiente di staging
Partendo dal presupposto che l'app Web WordPress sia già in esecuzione nell'app Web di Azure, accedere al [portale di Azure](https://portal.azure.com/) e passare all'app Web WordPress. In caso contrario, è possibile crearne una dal Marketplace. Per altre informazioni, fare clic [qui](web-sites-php-web-site-gallery.md). Fare clic su **Impostazioni** -> **Slot di distribuzione** -> **Aggiungi** per creare uno slot di distribuzione con nome stage. Uno slot di distribuzione è un'altra applicazione Web che condivide le stesse risorse dell'app Web primaria creata in precedenza.

![Creare lo slot di distribuzione "stage"](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

Aggiungere un altro database MySQL, `wordpress-stage-db`, al gruppo di risorse `wordpressapp-group`.

 ![Aggiungere il database MySQL al gruppo di risorse](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

Aggiornare le stringhe di connessione per lo slot di distribuzione "stage" affinché facciano riferimento al database appena creato, `wordpress-stage-db`. Si noti che l'app Web di produzione, `wordpressprodapp`, e l'app Web di staging `wordpressprodapp-stage` devono fare riferimento a database diversi.

#### Configurare impostazioni app specifiche dell'ambiente
Gli sviluppatori possono archiviare le coppie di stringhe chiave-valore in Azure come parte delle informazioni di configurazione associate a un'app Web, denominate Impostazioni app. In fase di esecuzione, le app Web del servizio app recuperano automaticamente questi valori e li rendono disponibili al codice in esecuzione nell'app Web. Dal punto di vista della sicurezza, si tratta di un vantaggio significativo, perché le informazioni sensibili, ad esempio le stringhe di connessione di database e le relative password, non devono essere mai visualizzate come testo normale in un file come `wp-config.php`.

È utile eseguire il processo illustrato sotto durante gli aggiornamenti perché include sia le modifiche dei file che le modifiche di database per l'app WordPress:

- Aggiornamento della versione di WordPress
- Aggiungere, modificare o aggiornare i plug-in
- Aggiungere, modificare o aggiornare i temi

Configurare le impostazioni app per:

- informazioni sul database
- attivazione/disattivazione della registrazione per WordPress
- impostazioni di sicurezza di WordPress

![Impostazioni app per l'app Web WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

Assicurarsi di aver aggiunto le impostazioni app seguenti per l'app Web di produzione e lo slot di staging. Si noti che l'app Web di produzione e quella di staging usano database diversi. Deselezionare la casella di controllo **Impostazione slot** per tutti i parametri delle impostazioni, ad eccezione di WP\_ENV. In questo modo, la configurazione per l'app Web verrà scambiata, insieme al contenuto del file e al database. Se la casella di controllo **Impostazione slot** è **Controllati** nelle impostazioni app dell'app Web, la configurazione delle stringhe di connessione NON verrà spostata da un ambiente all'altro durante le operazioni di SCAMBIO e quindi, se sono state apportate modifiche al database, queste non influiranno negativamente sull'app Web di produzione.

Distribuire l'app Web dell'ambiente di sviluppo locale nell'app Web e nel database di staging usando WebMatrix o altri strumenti, ad esempio FTP, Git o PhpMyAdmin.

![Finestra di dialogo di pubblicazione di WebMatrix per l'app Web WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

Esplorare e testare l'app Web di staging. Si consideri uno scenario in cui è necessario aggiornare il tema dell'app Web. Ecco l'app Web di staging.

![Esplorare l'app Web di staging prima dello scambio degli slot](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)


 Se tutto sembra corretto, fare clic sul pulsante **Scambia** nell'app Web di staging per spostare il contenuto all'ambiente di produzione. In questo caso, l'app Web e il database vengono scambiati da un ambiente all'altro nel corso di ogni operazione di **scambio**.

![Anteprima modifiche dello scambio per WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)

 >[AZURE.NOTE]
 >Se invece è presente uno scenario in cui è solo necessario eseguire il push dei file (senza aggiornamenti del database), **selezionare** la casella di controllo **Impostazione slot** per tutte le *impostazioni app* e le *impostazioni delle stringhe di connessione* relative al database nel pannello delle impostazioni dell'app Web nel portale di Azure prima di fare clic su SCAMBIA. In questo caso, le impostazioni DB\_NAME, DB\_HOST, DB\_PASSWORD, DB\_USER e la stringa di connessione predefinita non verranno visualizzate nell'anteprima modifiche quando si esegue un'operazione di **scambio**. In questa fase, al termine dell'operazione di **scambio** l'app Web WordPress conterrà **SOLO** i file aggiornati.

Ecco l'app Web WordPress di produzione ![App Web di produzione prima dello scambio degli slot](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png) prima dell'operazione di SCAMBIO

Dopo l'operazione di SCAMBIO, il tema è stato aggiornato nell'app Web di produzione.

![App Web di produzione dopo lo scambio degli slot](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

Se è necessario eseguire il **rollback**, è possibile passare alle impostazioni dell'app Web di produzione e fare clic su pulsante **Scambia** per eseguire lo scambio dell'app Web e del database dallo slot di produzione a quello di staging. È importante ricordare che se un'operazione di **scambio** include modifiche del database in un determinato momento, alla ridistribuzione successiva nell'app Web di staging sarà necessario distribuire le modifiche del database nel database corrente per l'app Web di staging che può essere un database di produzione o di staging precedente.

#### Riepilogo
Per generalizzare il processo per tutte le applicazioni con un database

1. Installare l'applicazione nell'ambiente locale
2. Includere la configurazione specifica dell'ambiente (locale e app Web di Azure)
3. Configurare gli ambienti (staging e produzione) nell'app Web del servizio app
4. Se è già presente un'applicazione di produzione in esecuzione in Azure, sincronizzare il contenuto di produzione (file/codice e database) con l'ambiente locale e di staging.
5. Sviluppare l'applicazione nell'ambiente locale
6. Impostare l'app Web di produzione nella modalità di manutenzione o bloccata e sincronizzare il contenuto del database dall'ambiente di produzione agli ambienti di staging e di sviluppo
7. Eseguire la distribuzione nell'ambiente di staging e procedere al test
8. Eseguire la distribuzione nell'ambiente di produzione
9. Ripetere i passaggi da 4 a 6

### Umbraco
In questa sezione verrà illustrato il modo in cui Umbraco CMS usa un modulo personalizzato per la distribuzione da più ambienti DevOps. Questo esempio illustra un approccio diverso alla gestione di più ambienti di sviluppo.

[Umbraco CMS](http://umbraco.com/) è una delle soluzioni CMS .NET più usate dagli sviluppatori che offre il modulo [Courier2](http://umbraco.com/products/more-add-ons/courier-2) per la distribuzione dall'ambiente di sviluppo all'ambiente di staging e infine a quello di produzione. Per creare un ambiente di sviluppo locale per un'app Web Umbraco CMS è possibile usare Visual Studio o WebMatrix.

1. Per creare un'app Web Umbraco con Visual Studio, fare clic [qui](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget) .
2. Per creare un'app Web Umbraco con WebMatrix, fare clic [qui](http://umbraco.com/help-and-support/video-tutorials/getting-started/working-with-webmatrix) .

Ricordare sempre di rimuovere la cartella `install` nell'applicazione e di non caricarla mai nelle app Web di staging o di produzione. Per questa esercitazione, si userà WebMatrix

#### Configurare un ambiente di staging
Creare uno slot di distribuzione come indicato in precedenza per un'app Web Umbraco CMS, presupponendo esista già un'app Web Umbraco CMS operativa. In caso contrario, è possibile crearne una dal Marketplace.

Aggiornare le stringhe di connessione per lo slot di distribuzione "stage" perché facciano riferimento al database appena creato, **umbraco-stage-db**. L'app Web di produzione, (umbraositecms-1), e l'app Web di staging (umbracositecms-1-stage) **DEVONO** fare riferimento a database diversi.

![Aggiornare la stringa di connessione per l'app Web di staging con il nuovo database di staging](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

Fare clic su **Ottieni impostazioni di pubblicazione** per lo slot di distribuzione **stage**. Verrà scaricato un file di impostazioni di pubblicazione in cui sono archiviate tutte le informazioni richieste da Visual Studio o WebMatrix per pubblicare l'applicazione dall'app Web di sviluppo locale all'app Web di Azure.

 ![Ottenere le impostazioni di pubblicazione per l'app Web di staging](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)

- Aprire l'app Web di sviluppo locale in **WebMatrix** o **Visual Studio**. Per questa esercitazione si usa WebMatrix ed è prima necessario importare il file di impostazioni di pubblicazione per l'app Web di staging

![Importare le impostazioni di pubblicazione per Umbraco tramite WebMatrix](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

- Esaminare le modifiche nella finestra di dialogo e distribuire l'app Web locale nell'app Web di Azure, *umbracositecms-1-stage*. Quando si distribuiscono i file direttamente nell'app Web di staging è necessario omettere i file nella cartella `~/app_data/TEMP/` perché questi verranno rigenerati al primo avvio dell'app Web di staging. Omettere il file `~/app_data/umbraco.config` perché anche questo verrà rigenerato.

![Rivedere le modifiche da pubblicare in WebMatrix](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

- Dopo aver pubblicato l'app Web locale Umbraco nell'app Web di staging, esplorarla ed eseguire alcuni test per rilevare eventuali problemi.

#### Configurare il modulo di distribuzione Courier2
Grazie al modulo [Courier2](http://umbraco.com/products/more-add-ons/courier-2) è possibile fare clic con il pulsante destro del mouse per eseguire il push di contenuto, fogli di stile, moduli di sviluppo e altro ancora da un'app Web di staging a un'app Web di produzione in modo da semplificare le operazioni di distribuzione e ridurre il rischio di interrompere l'app Web di produzione durante la distribuzione di aggiornamenti. Acquistare una licenza di Courier2 per il dominio `*.azurewebsites.net` e il dominio personalizzato, (ad esempio http://abc.com). Dopo aver acquistato la licenza, salvare il file di licenza (file LIC) nella cartella `bin`.

![Salvare il file di licenza nella cartella bin](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

Scaricare il pacchetto di Courier2 da [qui](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/). Accedere all'app Web di staging, ad esempio http://umbracocms-site-stage.azurewebsites.net/umbraco, fare clic sul menu **Developer** e quindi selezionare **Packages** . Fare clic sul pacchetto locale **Install**

![Programma di installazione del pacchetto Umbraco](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

Caricare il pacchetto Courier2 usando il programma di installazione.

![Caricare il pacchetto per il modulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

Per procedere con la configurazione è necessario aggiornare il file courier.config nella cartella **Config** dell'app Web.

```xml
<!-- Repository connection settings -->
  <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
  <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear:  -->
        <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
            <url>http://umbracositecms-1.azurewebsites.net</url>
            <user>0</user>
            <!--<login>user@email.com</login> -->
            <!-- <password>user_password</password>-->
           <!-- <passwordEncoding>Clear</passwordEncoding>-->
           </repository>
  </repositories>
 ```

Under `<repositories>`, enter the production site URL and user information. If you are using default Umbraco Membership provider, then add the ID for the Administration user in <user> section . If you are using a custom Umbraco membership provider, use `<login>`,`<password>` to Courier2 module know how to connect to the production site. For more details, review the [documentation](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation) for Courier module.

Similarly, install Courier module on your production site and configure it point to stage web app in its respective courier.config file as shown here

```xml
  <!-- Repository connection settings -->
  <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
  <repositories> <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear:  --> <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true"> <url>http://umbracositecms-1-stage.azurewebsites.net</url> <user>0</user> </repository> </repositories> ```

Fare clic sulla scheda Courier2 nel dashboard dell'app Web Umbraco CMS e selezionare la località. Verrà visualizzato il nome del repository, come indicato in `courier.config`. Eseguire questa operazione nell'app Web di produzione e di staging.

![Visualizzare il repository dell'app Web di destinazione](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

Si passerà ora alla distribuzione del contenuto dal sito di staging al sito di produzione. Passare a Content e selezionare una pagina esistente o crearne una nuova. In questo caso, verrà selezionata una pagina esistente dell'app Web in cui il titolo della pagina è stato modificato e sostituito con **Getting Started – new** e quindi fare clic su **Save and Publish**.

![Modificare il titolo della pagina e pubblicare](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

Selezionare la pagina modificata e quindi *fare clic con il pulsante destro del mouse* per visualizzare tutte le opzioni. Fare clic su **Courier** per visualizzare la finestra di dialogo per la distribuzione. Fare clic su **Deploy** per avviare la distribuzione

![Finestra di dialogo per la distribuzione del modulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

Esaminare le modifiche e fare clic su Continue.

![Verifica delle modifiche nella finestra di dialogo per la distribuzione del modulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

Il log di distribuzione indica se la distribuzione è riuscita.

 ![Visualizzare i log di distribuzione del modulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

Passare all'app Web di produzione per vedere se riflette le modifiche.

 ![Esplorare l'app Web di produzione](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

Per altre informazioni su come usare Courier, vedere la documentazione.

#### Come aggiornare la versione di Umbraco CMS

Con Courier non verrà distribuita la guida durante l'aggiornamento da una versione di Umbraco CMS a un'altra. Durante l'aggiornamento della versione di Umbraco CMS, è necessario controllare le eventuali incompatibilità con i moduli personalizzati o i moduli di terze parti e le librerie di base di Umbraco. Procedure consigliate:

1. Eseguire SEMPRE un backup dell'app Web e del database prima di installare un aggiornamento. Nell'app Web di Azure è possibile configurare i backup automatici per i siti Web tramite la funzionalità di backup e ripristinare il sito, se necessario, con la funzionalità di ripristino. Per altri dettagli, vedere [Come eseguire il backup dell'app Web](web-sites-backup.md) e [Come ripristinare l'app Web](web-sites-restore.md).

2. Verificare se i pacchetti di terze parti in uso sono compatibili con la versione a cui si esegue l'aggiornamento. Nella pagina di download del pacchetto, vedere le informazioni sulla compatibilità del progetto con la versione di Umbraco CMS.

Per altri dettagli su come aggiornare l'app Web in locale, seguire le indicazioni disponibili [qui](https://our.umbraco.org/documentation/getting-started/setup/upgrading/general).

Dopo aver aggiornato il sito di sviluppo locale, pubblicare le modifiche nell'app Web di staging. Testare l'applicazione e, se tutto sembra corretto, fare clic sul pulsante **Scambia** per **scambiare** il sito di staging con l'app Web di produzione. Durante l'operazione di **scambio**, è possibile visualizzare le modifiche che determinano un impatto nella configurazione dell'app Web. Con l'operazione di **scambio** vengono scambiati i database e le app Web. Questo significa che dopo l'operazione di SCAMBIO, l'app Web di produzione farà riferimento al database umbraco-stage-db e l'app Web di staging farà riferimento al database umbraco-prod-db.

![Anteprima dello scambio per la distribuzione di Umbraco CMS.](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

Vantaggi dello scambio dell'app Web e del database: 1. È possibile eseguire il rollback alla versione precedente dell'app Web con un'altra operazione di **scambio** in caso di errori dell'applicazione. 2. Per gli aggiornamenti, è necessario distribuire i file e il database dall'app Web di staging all'app Web e al database di produzione. Durante la distribuzione di file e database, esistono molti aspetti che potrebbero non funzionare come previsto. La funzionalità di **scambio** degli slot consente di ridurre i tempi di inattività durante gli aggiornamenti e ridurre i rischi di errori che possono verificarsi durante la distribuzione delle modifiche. 3. Consente di eseguire il **test A/B** tramite la funzionalità di [test in produzione](https://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

Questo esempio illustra la flessibilità della piattaforma in cui è possibile creare moduli personalizzati simili al modulo Umbraco Courier per gestire la distribuzione in più ambienti.

## Riferimenti
[Agile Software Development con il servizio app di Azure](app-service-agile-software-development.md)

[Configurare ambienti di staging per le app Web nel servizio app di Azure](web-sites-staged-publishing.md)

[Come bloccare l'accesso Web agli slot di distribuzione non di produzione](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

<!---HONumber=AcomDC_0128_2016-->
---
title: Usare efficacemente gli ambienti DevOps per l&quot;App Web | Microsoft Docs
description: "Informazioni su come usare gli slot di distribuzione per configurare e gestire più ambienti di sviluppo per l&quot;applicazione"
services: app-service\web
documentationcenter: 
author: sunbuild
manager: yochayk
editor: 
ms.assetid: 16a594dc-61f5-4984-b5ca-9d5abc39fb1e
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 10/24/2016
ms.author: sumuth
translationtype: Human Translation
ms.sourcegitcommit: 385eb87ec32f5f605b28cc8c76b1c89c7e90bfec
ms.openlocfilehash: 5284022ea473db893800b0f64b5bf4f811d994aa
ms.lasthandoff: 12/20/2016


---
# <a name="use-devops-environments-effectively-for-your-web-apps"></a>Usare efficacemente gli ambienti DevOps per le app Web
Questo articolo illustra come configurare e gestire le distribuzioni di applicazioni Web per più versioni dell'applicazione, ad esempio sviluppo, gestione temporanea, controllo qualità e produzione. Ogni versione dell'applicazione può essere considerata come un ambiente di sviluppo per lo scopo specifico del processo di distribuzione. Ad esempio, gli sviluppatori possono usare l'ambiente di controllo di qualità per testare la qualità dell'applicazione prima di effettuare il push delle modifiche in produzione.
La presenza di più ambienti di sviluppo può creare difficoltà, perché prevede il tracciamento del codice, la gestione delle risorse (calcolo, App Web, database, cache e così via) e la distribuzione del codice in diversi ambienti.

## <a name="set-up-a-non-production-environment-stage-dev-qa"></a>Configurare un ambiente non di produzione (gestione temporanea, sviluppo, controllo qualità)
Quando l'App Web è operativa, il passaggio successivo prevede la creazione di un ambiente non di produzione. Per usare gli slot di distribuzione, assicurarsi che sia in esecuzione la modalità Standard o Premium del piano di servizio app di Azure. Gli slot di distribuzione sono App Web live con i propri nomi host. È possibile scambiare il contenuto dell'app Web e gli elementi delle configurazioni tra i due slot di distribuzione, incluso lo slot di produzione. Quando si distribuisce l'applicazione in uno slot di distribuzione, si ottengono i vantaggi seguenti:

- È possibile convalidare le modifiche alle App Web in uno slot di distribuzione di gestione temporanea prima di scambiare l'app con lo slot di produzione.
- Quando si esegue la distribuzione preliminare di un'App Web in uno slot e poi la si implementa in un ambiente di produzione, tutte le istanze dello slot vengano effettivamente eseguite prima di passare alla fase di produzione. Questo processo consente di evitare i tempi di inattività al momento della distribuzione dell'App Web. Il reindirizzamento del traffico è lineare e nessuna richiesta viene eliminata in seguito alle operazioni di scambio. Per automatizzare l'intero flusso di lavoro, configurare lo [scambio automatico](web-sites-staged-publishing.md#configure-auto-swap) quando non è necessaria la convalida preliminare.
- Dopo uno scambio, lo slot con l'App Web gestita temporaneamente include l'App Web di produzione precedente. Se le modifiche applicate nello slot di produzione non sono quelle previste, è possibile ripetere immediatamente lo scambio per recuperare l'ultima App Web con i dati corretti.

Per configurare uno slot di distribuzione di staging, vedere [Configurare gli ambienti di staging per le app Web nel Servizio app di Azure](web-sites-staged-publishing.md). Ogni ambiente deve includere un proprio set di risorse. Ad esempio, se l'App Web usa un database, allora sia l'App Web di gestione temporanea che quella di produzione devono usare database diversi. Aggiungere le risorse dell'ambiente di sviluppo di gestione temporanea, ad esempio il database, l'archiviazione o la cache, per impostare l'ambiente di sviluppo di gestione temporanea.

## <a name="examples-of-using-multiple-development-environments"></a>Esempi di utilizzo di più ambienti di sviluppo
Qualsiasi progetto deve seguire la gestione del codice sorgente con almeno due ambienti: sviluppo e produzione. Se si usano sistemi di gestione dei contenuti (CMS), framework di applicazione e così via, l'applicazione potrebbe non supportare questo scenario senza personalizzazione. Questa eventualità è vera per alcuni dei framework più diffusi di cui si parla nelle sezioni successive. Quando si usano framework o CMS, possono sorgere gli interrogativi seguenti:

- Come si suddivide il contenuto in ambienti diversi?
- Quali file è possibile modificare senza influire sugli aggiornamenti di versione del framework?
- Come si gestiscono le configurazioni per ambiente?
- Come si gestiscono gli aggiornamenti delle versioni per i moduli, i plug-in e il framework di base?

Esistono diversi modi per configurare più ambienti per il progetto. Negli esempi seguenti viene illustrato un metodo per ogni applicazione.

### <a name="wordpress"></a>WordPress
Questa sezione descrive come configurare un flusso di lavoro di distribuzione usando gli slot per WordPress. Come la maggior parte delle soluzioni CMS, WordPress non supporta l'uso di più ambienti di sviluppo senza personalizzazione. La funzionalità App Web del servizio app di Azure offre alcune funzionalità che semplificano l'archiviazione delle impostazioni di configurazione all'esterno del codice.

1. Prima di creare uno slot di gestione temporanea, configurare il codice dell'applicazione in modo che supporti più ambienti. Per supportare più ambienti in WordPress, è necessario modificare `wp-config.php` nell'App Web di sviluppo locale e aggiungere il codice seguente all'inizio del file. Con questo processo, l'applicazione selezionerà la configurazione corretta in base all'ambiente selezionato.

    ```
    // Support multiple environments
    // set the config file based on current environment
    if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) {
    // local development
     $config_file = 'config/wp-config.local.php';
    }
    elseif ((strpos(getenv('WP_ENV'),'stage') !== false) || (strpos(getenv('WP_ENV'),'prod' )!== false ))
    //single file for all azure development environments
     $config_file = 'config/wp-config.azure.php';
    }
    $path = dirname(__FILE__). '/';
    if (file_exists($path. $config_file)) {
    // include the config file if it exists, otherwise WP is going to fail
    require_once $path. $config_file;
    ```

2. Nella directory radice dell'App Web creare una cartella denominata `config` e aggiungere i file `wp-config.azure.php` e `wp-config.local.php`, che rappresentano rispettivamente l'ambiente Azure e l'ambiente locale.

3. Copiare quanto segue in `wp-config.local.php`:

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
    define('AUTH_KEY', 'put your unique phrase here');
    define('SECURE_AUTH_KEY','put your unique phrase here');
    define('LOGGED_IN_KEY','put your unique phrase here');
    define('NONCE_KEY', 'put your unique phrase here');
    define('AUTH_SALT', 'put your unique phrase here');
    define('SECURE_AUTH_SALT', 'put your unique phrase here');
    define('LOGGED_IN_SALT', 'put your unique phrase here');
    define('NONCE_SALT', 'put your unique phrase here');

    /**
     * WordPress Database Table prefix.
     *
     * You can have multiple installations in one database if you give each a unique
     * prefix. Only numbers, letters, and underscores please!
     */
    $table_prefix = 'wp_';
    ```

    Impostando le chiavi di sicurezza come illustrato nel codice precedente è possibile impedire eventuali attacchi all'App Web, quindi è necessario usare valori univoci. Per generare la stringa per le chiavi di sicurezza indicate nel codice, [accedere al generatore automatico](https://api.wordpress.org/secret-key/1.1/salt) e creare nuove coppie chiave-valore.

4. Copiare il codice seguente in `wp-config.azure.php`:

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
    define('AUTH_KEY',getenv('DB_AUTH_KEY'));
    define('SECURE_AUTH_KEY', getenv('DB_SECURE_AUTH_KEY'));
    define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
    define('NONCE_KEY', getenv('DB_NONCE_KEY'));
    define('AUTH_SALT', getenv('DB_AUTH_SALT'));
    define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
    define('LOGGED_IN_SALT',  getenv('DB_LOGGED_IN_SALT'));
    define('NONCE_SALT',  getenv('DB_NONCE_SALT'));

    /**
    * WordPress Database Table prefix.
    *
    * You can have multiple installations in one database if you give each a unique
    * prefix. Only numbers, letters, and underscores please!
    */
    $table_prefix = getenv('DB_PREFIX');
    ```

#### <a name="use-relative-paths"></a>Usare percorsi relativi
Infine è necessario configurare i percorsi relativi nell'app WordPress. WordPress archivia le informazioni relative all'URL nel database. Questo tipo di archiviazione rende più difficile il trasferimento dei contenuti da un ambiente all'altro. È necessario aggiornare il database ogni volta che si spostano contenuti da ambiente locale ad ambiente di gestione temporanea o da ambiente di gestione temporanea ad ambiente di produzione. Per ridurre i rischi associati alla distribuzione del database ogni volta che si esegue la distribuzione da un ambiente a un altro, usare il [plug-in per i collegamenti relativi alla radice](https://wordpress.org/plugins/root-relative-urls/), che può essere installato usando il dashboard di amministrazione di WordPress.

Aggiungere le voci seguenti al file `wp-config.php` prima del commento `That's all, stop editing!`:

```

  define('WP_HOME', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_SITEURL', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_CONTENT_URL', '/wp-content');
    define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
```

Attivare il plug-in dal menu `Plugins` nel dashboard di amministrazione di WordPress. Salvare le impostazioni relative al collegamento permanente per l'app WordPress.

#### <a name="the-final-wp-configphp-file"></a>Il file `wp-config.php` finale
Gli eventuali aggiornamenti principali di WordPress non avranno effetto sui file `wp-config.php`, `wp-config.azure.php` e `wp-config.local.php`. Di seguito è riportata la versione finale del file `wp-config.php`:

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
elseif ((strpos(getenv('WP_ENV'),'stage') !== false) ||(strpos(getenv('WP_ENV'),'prod' )!== false )){
  $config_file = 'config/wp-config.azure.php';
}


$path = dirname(__FILE__). '/';
if (file_exists($path. $config_file)) {
  // include the config file if it exists, otherwise WP is going to fail
  require_once $path. $config_file;
}

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/* That's all, stop editing! Happy blogging. */

define('WP_HOME', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_SITEURL', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_CONTENT_URL', '/wp-content');
define('DOMAIN_CURRENT_SITE', $_SERVER['HTTP_HOST']);

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
    define('ABSPATH', dirname(__FILE__). '/');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH. 'wp-settings.php');
```

#### <a name="set-up-a-staging-environment"></a>Configurare un ambiente di staging
1. Se l'App Web WordPress è già in esecuzione nella sottoscrizione di Azure, accedere al [Portale di Azure](http://portal.azure.com) e passare all'App Web WordPress. Se non si dispone di un'App Web WordPress, è possibile crearne una da Azure Marketplace. Per altre informazioni, vedere [Creare un'App Web WordPress nel servizio app di Azure](web-sites-php-web-site-gallery.md).
Fare clic su **Impostazioni** > **Slot di distribuzione** > **Aggiungi** per creare uno slot di distribuzione con il nome *stage*. Uno slot di distribuzione è un'altra applicazione Web che condivide le stesse risorse dell'App Web primaria creata in precedenza.

    ![Creare lo slot di distribuzione "stage"](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

2. Aggiungere un altro database MySQL, `wordpress-stage-db`, al gruppo di risorse `wordpressapp-group`.

    ![Aggiungere il database MySQL al gruppo di risorse](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

3. Aggiornare le stringhe di connessione per lo slot di distribuzione di gestione temporanea affinché scelgano il nuovo database, `wordpress-stage-db`. L'App Web di produzione `wordpressprodapp` e l'App Web di gestione temporanea `wordpressprodapp-stage` devono scegliere database diversi.

#### <a name="configure-environment-specific-app-settings"></a>Configurare impostazioni app specifiche dell'ambiente
Gli sviluppatori possono archiviare le coppie di stringhe chiave-valore in Azure come parte delle informazioni di configurazione associate a un'App Web, denominate **Impostazioni app**. In fase di esecuzione, le App Web recuperano automaticamente questi valori e li rendono disponibili al codice in esecuzione nell'App Web. Dal punto di vista della sicurezza, si tratta di un vantaggio significativo, perché le informazioni sensibili, ad esempio le stringhe di connessione di database e le relative password, non vengono mai visualizzate come testo normale in un file come `wp-config.php`.

Questo processo, spiegato nei paragrafi seguenti, è utile perché include le modifiche sia ai file che ai database per l'app WordPress:

* Aggiornamento della versione di WordPress
* Aggiungere, modificare o aggiornare i plug-in
* Aggiungere, modificare o aggiornare i temi

Configurare le impostazioni app per:

* Informazioni sul database
* Attivazione/disattivazione della registrazione a WordPress
* impostazioni di sicurezza di WordPress

![Impostazioni app per l'app Web WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

Assicurarsi di aver aggiunto le impostazioni app seguenti per l'App Web di produzione e lo slot di gestione temporanea. Si noti che l'App Web di produzione e quella di gestione temporanea usano database diversi.

1. Deselezionare la casella di controllo **Impostazione slot** per tutti i parametri delle impostazioni, ad eccezione di WP_ENV. Grazie a questo processo, la configurazione per l'App Web, il contenuto del file e il database verrà scambiata. Se la casella di controllo **Impostazione slot** è selezionata, le impostazioni app dell'App Web e la configurazione delle stringhe di connessione *non* verranno spostate da un ambiente all'altro durante un'operazione di **scambio**. Eventuali modifiche al database non interrompono l'App Web di produzione.

2. Distribuire l'App Web dell'ambiente di sviluppo locale nell'App Web e nel database dell'area di gestione temporanea usando WebMatrix o altri strumenti, ad esempio FTP, Git o PhpMyAdmin.

    ![Finestra di dialogo di pubblicazione di WebMatrix per l'app Web WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

3. Esplorare e testare l'app Web di staging. Si consideri uno scenario in cui è necessario aggiornare il tema dell'app Web. Ecco l'app Web di staging.

    ![Esplorare l'app Web di staging prima dello scambio degli slot](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)

4. Se tutto sembra corretto, fare clic sul pulsante **Scambia** nell'App Web di gestione temporanea per spostare il contenuto all'ambiente di produzione. In questo caso l'App Web e il database vengono scambiati da un ambiente all'altro nel corso di ogni operazione di **scambio**.

    ![Anteprima modifiche dello scambio per WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)

    > [!NOTE]
    > Se invece è presente uno scenario in cui è solo necessario effettuare il push dei file (senza aggiornamenti del database), selezionare **Impostazione slot** per tutte le *impostazioni app* e le *impostazioni delle stringhe di connessione* relative al database nel pannello **Impostazioni app Web** del Portale di Azure prima di procedere con lo **scambio**. In questo caso le impostazioni DB_NAME, DB_HOST, DB_PASSWORD, DB_USER e la stringa di connessione predefinita non verranno visualizzate nell'anteprima modifiche quando si esegue un'operazione di **scambio**. In questa fase, al termine dell'operazione di **scambio** l'App Web WordPress conterrà solo i file aggiornati.
    >
    >

    Ecco l'App Web WordPress di produzione prima dell'operazione di **scambio**.
    ![App Web di produzione prima dello scambio degli slot](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png)

    Dopo l'operazione di **scambio**, il tema è stato aggiornato nell'App Web di produzione.

    ![App Web di produzione dopo lo scambio degli slot](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

5. Quando è necessario eseguire il rollback, è possibile accedere alle **impostazioni dell'App** Web di produzione e fare clic su pulsante **Scambia** per eseguire lo scambio dell'App Web e del database dallo slot di produzione a quello di gestione temporanea. È importante ricordare che se un'operazione di **scambio** include modifiche del database, alla ridistribuzione successiva nell'App Web di gestione temporanea sarà necessario distribuire le modifiche del database nel database corrente per l'App Web di gestione temporanea, che può essere un database di produzione o di gestione temporanea precedente.

#### <a name="summary"></a>Riepilogo
Di seguito è illustrato un processo generalizzato per qualsiasi applicazione con un database:

1. Installare l'applicazione nell'ambiente locale.
2. Includere le configurazioni specifiche dell'ambiente (locale e App Web di Azure).
3. Configurare l'ambiente di produzione e di gestione temporanea per le App Web.
4. Se è già presente un'applicazione di produzione in esecuzione in Azure, sincronizzare il contenuto di produzione (file/codice e database) con l'ambiente locale e di gestione temporanea.
5. Sviluppare l'applicazione nell'ambiente locale.
6. Impostare l'App Web di produzione nella modalità di manutenzione o di blocco e sincronizzare il contenuto del database dall'ambiente di produzione agli ambienti di gestione temporanea e di sviluppo.
7. Eseguire la distribuzione nell'ambiente di gestione temporanea e procedere al test.
8. Eseguire la distribuzione nell'ambiente di produzione.
9. Ripetere i passaggi da 4 a 6.

### <a name="umbraco"></a>Umbraco
In questa sezione verrà illustrato il modo in cui Umbraco CMS usa un modulo personalizzato per la distribuzione in più ambienti DevOps. Questo esempio illustra un approccio diverso alla gestione di più ambienti di sviluppo.

[Umbraco CMS](http://umbraco.com/) è una soluzione .NET CMS diffusa che viene usata da molti sviluppatori. Offre il modulo [Courier2](http://umbraco.com/products/more-add-ons/courier-2) per la distribuzione dall'ambiente di sviluppo a quello di gestione temporanea a quello di produzione. Per creare facilmente un ambiente di sviluppo locale per un'App Web Umbraco CMS è possibile usare Visual Studio o WebMatrix.

- [Creare un'App Web Umbraco con Visual Studio](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget)
- [Creare un'App Web Umbraco con WebMatrix](http://umbraco.tv/videos/umbraco-v7/implementor/fundamentals/installation/creating-umbraco-site-from-webmatrix-web-gallery/)

È necessario ricordare sempre di rimuovere la cartella `install` nell'applicazione e di non caricarla mai nelle App Web di gestione temporanea o di produzione. In questa esercitazione viene usato WebMatrix.

#### <a name="set-up-a-staging-environment"></a>Configurare un ambiente di staging
1. Creare uno slot di distribuzione come indicato in precedenza per un'App Web Umbraco CMS, presupponendo esista già un'App Web Umbraco CMS operativa. In caso contrario, è possibile crearne una dal Marketplace.
2. Aggiornare la stringa di connessione per lo slot di distribuzione di gestione temporanea perché scelga il nuovo database, **umbraco-stage-db**. L'App Web di produzione (umbraositecms-1) e l'App Web di gestione temporanea (umbracositecms-1-stage) *devono* scegliere database diversi.

    ![Aggiornare la stringa di connessione per l'app Web di staging con il nuovo database di staging](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

3. Fare clic su **Get Publish settings** (Ottieni impostazioni di pubblicazione) per lo slot di distribuzione di **gestione temporanea**. Con questo processo verrà scaricato un file di impostazioni di pubblicazione in cui sono archiviate tutte le informazioni richieste da Visual Studio o WebMatrix per pubblicare l'applicazione dall'App Web di sviluppo locale all'App Web di Azure.

    ![Ottenere le impostazioni di pubblicazione per l'app Web di staging](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)
4. Aprire l'App Web di sviluppo locale in WebMatrix o Visual Studio. In questa esercitazione viene usato WebMatrix. È innanzitutto necessario importare il file di impostazioni di pubblicazione per l'App Web di gestione temporanea.

    ![Importare le impostazioni di pubblicazione per Umbraco tramite WebMatrix](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

5. Esaminare le modifiche nella finestra di dialogo e distribuire l'App Web locale nell'App Web di Azure, *umbracositecms-1-stage*. Quando si distribuiscono i file direttamente nell'App Web di gestione temporanea è necessario omettere i file nella cartella `~/app_data/TEMP/`, perché verranno rigenerati al primo avvio dell'App Web di gestione temporanea. Omettere il file `~/app_data/umbraco.config` perché verrà rigenerato anch'esso.

    ![Rivedere le modifiche da pubblicare in WebMatrix](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

6. Dopo aver pubblicato l'App Web locale Umbraco nell'App Web di gestione temporanea, esplorarla ed eseguire alcuni test per rilevare eventuali problemi.

#### <a name="set-up-the-courier2-deployment-module"></a>Configurare il modulo di distribuzione Courier2
Con il modulo [Courier2](http://umbraco.com/products/more-add-ons/courier-2) è possibile semplicemente fare doppio clic per effettuare il push di contenuti, fogli di stile e moduli di sviluppo da un'App Web di gestione temporaea a un'App Web di produzione. Questo processo riduce il rischio di interrompere l'App Web di produzione quando si distribuisce un aggiornamento.
Acquistare una licenza per Courier2 per il dominio `*.azurewebsites.net` e il dominio personalizzato (ad esempio http://abc.com). Dopo aver acquistato la licenza, posizionare la licenza scaricata (file .LIC) nella cartella `bin`.

![Salvare il file di licenza nella cartella bin](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

1. [Scaricare il pacchetto di Courier2](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/). Accedere all'App Web di gestione temporanea, ad esempio http://umbracocms-site-stage.azurewebsites.net/umbraco, fare clic sul menu **Sviluppatore**, quindi fare clic su **Pacchetti** > **Install local package** (Installa pacchetto locale).

    ![Programma di installazione del pacchetto Umbraco](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

2. Caricare il pacchetto Courier2 usando il programma di installazione.

    ![Caricare il pacchetto per il modulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

3. Per configurare il pacchetto, è necessario aggiornare il file courier.config nella cartella **Config** dell'App Web.

    ```xml
    <!-- Repository connection settings -->
     <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
     <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
        <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
          <url>http://umbracositecms-1.azurewebsites.net</url>
          <user>0</user>
          <!--<login>user@email.com</login> -->
          <!-- <password>user_password</password>-->
          <!-- <passwordEncoding>Clear</passwordEncoding>-->
          </repository>
     </repositories>
     ```

4. In `<repositories>`immettere l’URL del sito di produzione e le informazioni dell’utente.
    Se si usa il provider di appartenenze Umbraco predefinito, aggiungere l'ID per l'utente Administration nella sezione &lt;user&gt;.
    Se si usa un provider di appartenenze Umbraco personalizzato, usare `<login>`,`<password>` nel modulo Courier2 per connettersi al sito di produzione.
    Per altre informazioni, [consultare la documentazione relativa al modulo Courier2](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation).

5. Analogamente, installare il modulo Courier2 nel sito di produzione e configurarlo affinché scelga l'App Web di gestione temporanea nel relativo file courier.config, come illustrato di seguito.

    ```xml
     <!-- Repository connection settings -->
     <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
     <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
        <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
          <url>http://umbracositecms-1-stage.azurewebsites.net</url>
          <user>0</user>
          </repository>
     </repositories>
    ```

6. Fare clic sulla scheda **Courier2** nel dashboard dell'App Web Umbraco CMS, quindi fare clic su **Posizioni**. Verrà visualizzato il nome del repository, come indicato in `courier.config`. Eseguire questo processo nell'App Web di produzione e di gestione temporanea.

    ![Visualizzare il repository dell'app Web di destinazione](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

7. Per distribuire il contenuto dal sito di gestione temporanea al sito di produzione, andare su **Contenuto**e selezionare una pagina esistente oppure crearne una nuova. In questo caso, verrà selezionata una pagina esistente dell'App Web in cui il titolo della pagina è **Getting Started – new** (Introduzione - Nuovo) e quindi fare clic su **Salva e pubblica**.

    ![Modificare il titolo della pagina e pubblicare](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

8. Fare clic con il pulsante destro del mouse sulla pagina modificata per visualizzare tutte le opzioni. Fare clic su **Courier** per aprire la finestra di dialogo **Distribuzione**. Fare clic su **Distribuisci** per avviare la distribuzione.

    ![Finestra di dialogo per la distribuzione del modulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

9. Esaminare le modifiche, quindi fare clic su **Continua**.

    ![Verifica delle modifiche nella finestra di dialogo per la distribuzione del modulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

    Il log di distribuzione indica se la distribuzione ha avuto esito positivo.

     ![Visualizzare i log di distribuzione del modulo Courier](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

10. Passare all'app Web di produzione per vedere se riflette le modifiche.

     ![Esplorare l'app Web di produzione](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

Per altre informazioni su come usare Courier, vedere la documentazione.

#### <a name="how-to-upgrade-the-umbraco-cms-version"></a>Come aggiornare la versione di Umbraco CMS
Courier non aiuta nell'aggiornamento da una versione di Umbraco CMS a un'altra. Durante l'aggiornamento della versione di Umbraco CMS, è necessario controllare le eventuali incompatibilità con i moduli personalizzati o i moduli dei partner e le librerie di base di Umbraco. Alcune procedure consigliate:

* Eseguire sempre un backup dell'App Web e del database prima di un aggiornamento. Nelle App Web di Azure è possibile configurare i backup automatici per i siti Web tramite la funzionalità di backup e ripristinare il sito, se necessario, con l'apposita funzionalità. Per altri dettagli, vedere [Come eseguire il backup dell'app Web](web-sites-backup.md) e [Come ripristinare l'app Web](web-sites-restore.md).
* Verificare se i pacchetti dei partner sono compatibili con la versione a cui si esegue l'aggiornamento. Nella pagina di download del pacchetto vedere le informazioni sulla compatibilità del progetto con la versione di Umbraco CMS.

Per altre informazioni su come aggiornare l'App Web locale, [vedere le indicazioni generali sull'aggiornamento](https://our.umbraco.org/documentation/getting-started/setup/upgrading/general).

Dopo aver aggiornato il sito di sviluppo locale, pubblicare le modifiche nell'App Web di gestione temporanea. Testare l'applicazione. Se tutto sembra corretto, fare clic sul pulsante **Scambia** per scambiare il sito di gestione temporanea con l'App Web di produzione. Durante l'operazione di **scambio**, è possibile visualizzare le modifiche alla configurazione dell'App Web. Con l'operazione di **scambio** vengono scambiati i database e le App Web. Dopo lo **scambio**, l'App Web di produzione sceglie il database umbraco-stage-db e l'App Web di gestione temporanea sceglie il database umbraco-prod-db.

![Anteprima dello scambio per la distribuzione di Umbraco CMS.](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

I vantaggi dello scambio dell'App Web e del database sono:

* È possibile eseguire il rollback alla versione precedente dell'App Web con un'altra operazione di **scambio** in caso di errori dell'applicazione.
* Per gli aggiornamenti, è necessario distribuire i file e i database dall'App Web di gestione temporanea all'App Web e al database di produzione. Quando si distribuiscono file e database, molti aspetti potrebbero andare per il verso sbagliato. La funzionalità di **scambio** degli slot consente di ridurre i tempi di inattività durante gli aggiornamenti e di ridurre i rischi di errori che possono verificarsi durante la distribuzione delle modifiche.
* È possibile eseguire **test A/B** usando la funzionalità [Testing in production](https://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/).

Questo esempio illustra la flessibilità della piattaforma in cui è possibile creare moduli personalizzati simili al modulo Umbraco Courier per gestire la distribuzione in più ambienti.

## <a name="references"></a>Riferimenti
[Agile Software Development con il servizio app di Azure](app-service-agile-software-development.md)

[Configurare ambienti di staging per le app Web nel servizio app di Azure](web-sites-staged-publishing.md)

[Come bloccare l'accesso Web agli slot di distribuzione non di produzione](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)


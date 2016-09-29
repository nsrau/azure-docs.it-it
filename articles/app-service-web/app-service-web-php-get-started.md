<properties
	pageTitle="Creare, configurare e distribuire un'app Web PHP in Azure"
	description="Un'esercitazione che illustra come eseguire un'app Web di PHP (Laravel) nel Servizio app di Azure. Informazioni su come configurare il Servizio app di Azure per soddisfare i requisiti del framework PHP scelto."
	services="app-service\web"
	documentationCenter="php"
	authors="cephalin"
	manager="wpickett"
	editor=""
	tags="mysql"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="06/03/2016" 
	ms.author="cephalin"/>

# Creare, configurare e distribuire un'app Web PHP in Azure

[AZURE.INCLUDE [schede](../../includes/app-service-web-get-started-nav-tabs.md)]

Questa esercitazione illustra come creare, configurare e distribuire un'app Web PHP per Azure e come configurare il Servizio app di Azure per soddisfare i requisiti dell'app Web di PHP. Al termine dell'esercitazione, sarà disponibile un'app Web [Laravel](https://www.laravel.com/) funzionante in esecuzione in [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md).

Gli sviluppatori PHP possono integrare trasferire il proprio framework PHP preferito in Azure. Questa esercitazione usa Laravel semplicemente come un esempio concreto di app. Si acquisiranno le nozioni seguenti:

- Distribuire con Git
- Impostare la versione di PHP
- Usare un file di avvio che non si trova nella directory dell'applicazione radice
- Accedere a variabili specifiche dell'ambiente
- Aggiornare l'app in Azure

È possibile applicare i contenuti appresi in questo documento ad altre app Web PHP che vengono distribuite in Azure.

## Prerequisiti

- Installare [PHP 5.6.x](http://php.net/downloads.php) (il supporto PHP 7 è beta)
- Installare [Composer](https://getcomposer.org/download/)
- Installare l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md)
- Installare [Git](http://www.git-scm.com/downloads)
- Ottenere un account Microsoft Azure. Se non è disponibile un account, è possibile [iscriversi per ottenere una versione di valutazione gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) oppure [attivare i benefici della sottoscrizione di Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Verificare il funzionamento di un'app Web. È possibile [provare il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751) immediatamente e creare un'app iniziale temporanea, senza necessità di fornire una carta di credito e senza impegni.

## Creare un'app PHP (Laravel) nel computer di sviluppo

1. Aprire un nuovo prompt dei comandi di Windows, una finestra di PowerShell, una shell di Linux o un terminale di OS X. Eseguire i comandi seguenti per verificare che gli strumenti necessari siano installati correttamente nel computer.

        php --version
        composer --version
        azure --version
        git --version

    ![Installazione degli strumenti di test prima di creare l'app PHP (Laravel) per Azure](./media/app-service-web-php-get-started/test-tools.png)

    Se gli strumenti non sono stati installati, vedere i collegamenti di download nella sezione [Prerequisiti](#Prerequisites).
    
2. Installare Laravel nel modo seguente:

        composer global require "laravel/installer

3. Eseguire `CD` in una directory di lavoro e creare una nuova applicazione Laravel come segue:

        cd <working_directory>
        laravel new <app_name>

4. Eseguire `CD` nella directory `<app_name>` appena creata e testare l'app come segue:

        cd <app_name>
        php artisan serve
        
    Ora dovrebbe essere possibile passare a http://localhost:8000 in un browser e vedere la schermata iniziale di Laravel.
    
    ![Testare l'app PHP (Laravel) in locale prima di distribuirla in Azure](./media/app-service-web-php-get-started/laravel-splash-screen.png)
    
Fino a questo momento è stato descritto il normale flusso di lavoro di Lavarel, che può essere approfondito <a href="https://laravel.com/docs/5.2" rel="nofollow">in questa pagina</a>. Ora verranno esaminati altri argomenti.

## Creare un'app Web di Azure e configurare una distribuzione Git

>[AZURE.NOTE] Se si volesse distribuire con il protocollo FTP è disponibile un'[esercitazione FTP](web-sites-php-mysql-deploy-use-ftp.md).

Con l'interfaccia della riga di comando di Azure è possibile creare un'app Web nel Servizio app di Azure e configurarla per la distribuzione Git con una singola riga di comando. Ora verrà eseguita questa operazione.

1. Passare in modalità ASM e accedere ad Azure:

        azure config mode asm
        azure login
    
    Seguire le istruzioni del messaggio della Guida per continuare il processo di accesso.
    
    ![Accedere ad Azure per distribuire l'app PHP (Laravel) in Azure](./media/app-service-web-php-get-started/log-in-to-azure-cli.png)

4. Eseguire il comando per creare l'app Web di Azure con la distribuzione Git. Quando richiesto, specificare il numero dell'area desiderata.

        azure site create --git <app_name>
    
    ![Creare la risorsa di Azure per la prima app PHP (Lavarel) in Azure](./media/app-service-web-php-get-started/create-site-cli.png)
    
    >[AZURE.NOTE] Se non sono mai state configurate credenziali di distribuzione per la sottoscrizione di Azure, verrà chiesto di crearle. Queste credenziali, e non le credenziali dell'account Azure, vengono usate dal servizio app solo per le distribuzioni Git e gli accessi FTP.
    
    Questo comando crea un nuovo repository Git nella directory corrente (con `git init`) e lo connette al repository in Azure come Git remoto (con `git remote add`).

<a name="configure"/>
## Configurare l'app Web di Azure

Affinché l'app Laravel funzioni in Azure, è necessario prestare attenzione a diversi aspetti. Un esercizio simile verrà eseguito per il framework PHP scelto.

- Configurare PHP 5.5.9 o versione successiva. Vedere i [requisiti del server di Laravel 5.2 più recenti](https://laravel.com/docs/5.2#server-requirements) per l'elenco completo dei requisiti del server. Il resto dell'elenco è costituito da estensioni che sono già abilitate dalle installazioni di PHP di Azure.
- Impostare le variabili di ambiente necessarie per l'app. Laravel usa il file `.env` per impostare in modo semplice le variabili di ambiente. Tuttavia, considerato che non deve essere eseguito il commit nel controllo del codice sorgente (vedere la sezione relativa alla [configurazione dell'ambiente Laravel](https://laravel.com/docs/5.2/configuration#environment-configuration)), verranno definite le impostazioni dell'app Web di Azure.
- Assicurarsi che il punto di ingresso dell'app Laravel, `public/index.php`, venga caricato per primo. Vedere la [panoramica del ciclo di vita di Laravel](https://laravel.com/docs/5.2/lifecycle#lifecycle-overview). In altre parole, è necessario impostare l'URL radice dell'app Web per indicare la directory `public`.
- Abilitare l'estensione Composer in Azure perché è presente un file composer.json. In questo modo, è possibile consentire a Composer di ottenere i pacchetti necessari quando si distribuisce con `git push`. Si tratta di una questione di praticità. Se non si abilita l'automazione Composer, è sufficiente rimuovere `/vendor` dal file `.gitignore` in modo che Git includa (cioè, non ignori) tutti gli elementi della directory `vendor` durante il commit e la distribuzione del codice.

Ora queste attività verranno configurate in sequenza.

4. Impostare la versione di PHP necessaria per l'app Laravel.

        azure site set --php-version 5.6

    L'impostazione della versione di PHP è terminata.
    
4. Generare un nuovo valore `APP_KEY` per l'app Web di Azure e definirlo come un'impostazione di app per l'app Web di Azure.

        php artisan key:generate --show
        azure site appsetting add APP_KEY="<output_of_php_artisan_key:generate_--show>"

4. Inoltre, attivare il debug di Laravel per impedire la creazione di un'eventuale pagina `Whoops, looks like something went wrong.` di difficile interpretazione.

        azure site appsetting add APP_DEBUG=true

    L'impostazione delle variabili di ambiente è terminata.
    
    >[AZURE.NOTE] Ora verranno descritte le operazioni eseguite da Laravel e da Azure in questo contesto. Laravel usa il file `.env` nella directory radice per fornire le variabili di ambiente per l'applicazione, dove è presente la riga `APP_DEBUG=true` (e anche `APP_KEY=...`). Questa variabile è accessibile in `config/app.php` dal codice `'debug' => env('APP_DEBUG', false),`. [env()](https://laravel.com/docs/5.2/helpers#method-env) è un metodo helper Laravel che usa [getenv()](http://php.net/manual/en/function.getenv.php) PHP dietro le quinte.
    >
    >Tuttavia, `.env` viene ignorato da Git perché viene chiamato dal file `.gitignore` nella directory radice. In poche parole, `.env` nel repository Git locale non viene inserito in Azure con il resto dei file. Naturalmente, è possibile rimuovere la riga da `.gitignore`; tuttavia, come è già stato visto, il commit di questo file in controllo del codice sorgente non è consigliato. È comunque necessario un modo per specificare queste variabili di ambiente in Azure.
    >
    >L'aspetto positivo è che le impostazioni dell'app nel Servizio app di Azure supportano [getenv()](http://php.net/manual/en/function.getenv.php) in PHP. Quindi, anche se è possibile usare FTP o altri metodi per caricare manualmente un file `.env` in Azure, è possibile specificare solo le variabili desiderate come impostazioni app di Azure senza un file `.env` in Azure, come è stato appena fatto. Inoltre, se una variabile si trova sia in un file `.env` che nelle impostazioni dell'app di Azure, prevale l'impostazione dell'app di Azure.

4. Per le ultime due attività (impostazione della directory virtuale e attivazione di Composer) è necessario il [portale di Azure](https://portal.azure.com), quindi accedere al [portale](https://portal.azure.com) con il proprio account Azure.

4. A partire dal menu a sinistra, fare clic su **Servizi app** > **& lt; nome\_app >** > **Strumenti**.

    ![Abilitare Composer per l'app PHP (Laravel) in Azure](./media/app-service-web-php-get-started/configure-composer-tools.png)
    
    >[AZURE.TIP] Se si fa clic su **Impostazioni** anziché su **Strumenti**, sarà possibile accedere al pannello **Impostazioni applicazione**, che consente di impostare le versioni PHP, le impostazioni dell'app e le directory virtuali, come è stato appena fatto.
    
4. Fare clic su **Estensioni** > **Aggiungi** per aggiungere un'estensione.

4. Selezionare **Composer** nel pannello [pannello](../azure-portal-overview.md) **Scegli estensione** (*pannello*: una pagina del portale visualizzata in orizzontale).

4. Fare clic su **OK** nel pannello **Accettare le note legali**.

5. Fare clic su **OK** nel pannello **Aggiungi estensione**.

    Al termine dell'aggiunta dell'estensione in Azure, nell'angolo verrà visualizzato un messaggio popup descrittivo e **Composer** sarà visualizzato nel pannello **Estensioni**.

    ![Pannello Estensioni dopo avere abilitato la creazione dell'app PHP (Laravel) in Azure](./media/app-service-web-php-get-started/configure-composer-end.png)

    L'abilitazione di Composer è terminata.
    
4. Nel pannello dell'app Web fare clic su **Impostazioni** > **Impostazioni applicazione**.

    ![Accedere al pannello Impostazioni per impostare la directory virtuale per l'app PHP (Laravel) in Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings.png)

    Nel pannello **Impostazioni applicazione** prendere nota della versione di PHP impostata in precedenza:

    ![Versione PHP nel pannello Impostazioni dell'app PHP (Laravel) in Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-a.png)

    e delle impostazioni dell'app aggiunte:
    
    ![Impostazioni dell'app nel pannello Impostazioni dell'app PHP (Laravel) in Azure](./media/app-service-web-php-get-started/configure-virtual-dir-settings-b.png)

4. Scorrere fino alla parte inferiore del pannello e modificare la directory virtuale radice in modo che punti a **site\\wwwroot\\public** anziché a **site\\wwwroot**.

    ![Impostare la directory virtuale per l'app PHP (Laravel) in Azure](./media/app-service-web-php-get-started/configure-virtual-dir-public.png)

4. Fare clic su **Salva** nella parte superiore del pannello.

    L'impostazione della directory virtuale è terminata.

## Distribuire l'app web con Git (e impostare le variabili di ambiente)

È ora possibile distribuire il codice. A questo scopo è necessario usare di nuovo il prompt dei comandi o il terminal.

4. Eseguire il commit di tutte le modifiche e distribuire il codice nell'app Web di Azure, analogamente a qualsiasi repository Git:

        git add .
        git commit -m "Hurray! My first commit for my Azure app!"
        git push azure master 

    Quando si esegue `git push`, verrà richiesto di fornire la password di distribuzione di Git. Se in precedenza è stato richiesto di creare le credenziali di distribuzione in `azure site create`, digitare la password che è stata usata.
    
5. Ora verrà eseguito nel browser usando il comando seguente:

        azure site browse

    Il browser dovrebbe visualizzare la schermata iniziale di Laravel.
    
    ![Schermata iniziale di Laravel dopo la distribuzione dell'app Web in Azure](./media/app-service-web-php-get-started/laravel-azure-splash-screen.png)
    
    Ora un'app Web di Lavarel viene eseguita in Azure.
             
## Risoluzione dei problemi comuni

Ecco alcuni errori che possono verificarsi durante questa esercitazione:

- [L'interfaccia della riga di comando di Azure visualizza il messaggio "'site' is not an azure command"](#clierror)
- [L'app Web visualizza l'errore HTTP 403](#http403)
- [L'app Web visualizza il messaggio "Whoops, looks like something went wrong." (C'è stato un problema.)](#whoops)
- [L'app Web visualizza il messaggio "No supported encryptor found." (Non è stato trovato alcun componente di crittografia.)](#encryptor)

<a name="clierror"></a>
### L'interfaccia della riga di comando di Azure visualizza il messaggio "'site' is not an azure command"

Quando si esegue `azure site *` nel terminal della riga di comando, viene visualizzato l'errore `error:   'site' is not an azure command. See 'azure help'.`

L'errore è in genere causato dal passaggio alla modalità "ARM" (Azure Resource Manager). Per risolvere questo problema, tornare alla modalità "ASM" (Azure Service Management) eseguendo `azure config mode asm`.

<a name="http403"></a>
### L'app Web visualizza l'errore HTTP 403

L'app Web è stata distribuita correttamente in Azure, ma quando si passa all'app Web di Azure viene visualizzato un errore `HTTP 403` o `You do not have permission to view this directory or page.`

Ciò è probabilmente dovuto al fatto che l'app Web non riesce a trovare il punto di ingresso all'app Laravel. Assicurarsi di aver modificato la directory virtuale radice in modo che punti a `site\wwwroot\public`, dove il file `index.php` di Laravel è (vedere [Configurare l'app Web di Azure](#configure)).

<a name="whoops"></a>
### L'app Web visualizza il messaggio "Whoops, looks like something went wrong." (C'è stato un problema.)

L'app Web è stata distribuita correttamente in Azure, ma quando si passa all'app Web di Azure viene visualizzato il messaggio `Whoops, looks like something went wrong.` di difficile interpretazione.

Per ottenere un errore più descrittivo, abilitare il debug di Laravel impostando la variabile di ambiente `APP_DEBUG` su `true` (vedere [Configurare l'app Web di Azure](#configure)).

<a name="encryptor"></a>
### L'app Web visualizza il messaggio "No supported encryptor found." (Non è stato trovato alcun componente di crittografia.)

L'app Web è stata distribuita correttamente in Azure, ma quando si passa all'app Web di Azure viene visualizzato il messaggio di errore seguente:

![APP\_KEY mancante nell'app PHP (Laravel) in Azure](./media/app-service-web-php-get-started/laravel-error-APP_KEY.png)
    
Si tratta di un errore grave, ma almeno non è di difficile interpretazione perché è stato attivato il debug di Laravel. Effettuando una ricerca sommaria della stringa di errore nei forum Laravel si scoprirà che il problema è causato dalla mancata impostazione di APP\_KEY in `.env`, o in questo caso, dalla totale assenza del file `.env` in Azure. È possibile risolvere il problema aggiungendo l'impostazione `APP_KEY` come impostazione dell'app di Azure (vedere [Configurare l'app Web di Azure](#configure)).
    
## Passaggi successivi

Scoprire come aggiungere dati all'app [creando un database MySQL in Azure](../store-php-create-mysql-database.md). Consultare anche di seguito alcuni collegamenti più utili per PHP in Azure:

- [Centro sviluppatori PHP](/develop/php/).
- [Creare un'app Web da Azure Marketplace](app-service-web-create-web-app-from-marketplace.md)
- [Configurare PHP in App Web del servizio app di Azure](web-sites-php-configure.md)
- [Conversione di WordPress in un multisito nel servizio app di Azure](web-sites-php-convert-wordpress-multisite.md)
- [WordPress di livello aziendale nel servizio app di Azure](web-sites-php-enterprise-wordpress.md)

<!---HONumber=AcomDC_0914_2016-->
---
title: Creare un'app Web PHP e MySQL nel servizio app di Azure in Linux | Microsoft Docs
description: Informazioni su come ottenere un'app PHP che è possibile usare in Azure con connessione a un database MySQL in Azure.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 11/28/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 5d1e1e448ce4cf6f1b6f617a68eb4d89333c180d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="build-a-php-and-mysql-web-app-in-azure-app-service-on-linux"></a>Creare un'app Web PHP e MySQL nel servizio app di Azure in Linux

> [!NOTE]
> Questo articolo consente di distribuire un'app nel servizio app in Linux. Per la distribuzione nel servizio app in _Windows_, vedere [Creare un'app Web PHP e MySQL in Azure](../app-service-web-tutorial-php-mysql.md).
>

Il [Servizio app in Linux](app-service-linux-intro.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione basato sul sistema operativo Linux. Questa esercitazione illustra come creare un'app Web PHP e connetterla a un database MySQL. Al termine, sarà disponibile un'app [Laravel](https://laravel.com/) in esecuzione nel Servizio app in Linux.

![App PHP in esecuzione nel Servizio app di Azure](./media/tutorial-php-mysql-app/complete-checkbox-published.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un database MySQL in Azure
> * Connettere un'app PHP a MySQL
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log di diagnostica in Azure
> * Gestire l'app nel portale di Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione:

* [Installare Git](https://git-scm.com/)
* [Installare PHP 5.6.4 o versione successiva](http://php.net/downloads.php)
* [Installare Composer](https://getcomposer.org/doc/00-intro.md)
* Abilitare le estensioni PHP seguenti necessarie per Laravel: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
* [Installare e avviare MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

## <a name="prepare-local-mysql"></a>Preparare MySQL in locale

In questo passaggio, si crea un database nel server locale di MySQL da usare in questa esercitazione.

### <a name="connect-to-local-mysql-server"></a>Connettersi al server MySQL locale

In una finestra terminale connettersi al server MySQL locale. È possibile usare questa finestra del terminale per eseguire tutti i comandi presenti in questa esercitazione.

```bash
mysql -u root -p
```

Se viene richiesto di immettere una password, immettere la password per l'account `root`. Se non si ricorda la password dell'account radice, vedere [MySQL: procedura per reimpostare la password radice](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Se il comando viene eseguito correttamente, il server MySQL è in esecuzione. In caso contrario, assicurarsi che il server MySQL locale sia stato avviato seguendo la [procedura successiva all'installazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database-locally"></a>Creare un database in locale

Al prompt `mysql` creare un database.

```sql 
CREATE DATABASE sampledb;
```

Chiudere la connessione al server digitando `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>Creare un'app PHP in locale
In questo passaggio si ottiene un'applicazione di esempio Laravel, se ne configurare la connessione al database e la si esegue in locale. 

### <a name="clone-the-sample"></a>Clonare l'esempio

Nella finestra del terminale usare il comando `cd` per passare a una directory di lavoro.

Eseguire il comando seguente per clonare l'archivio di esempio.

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

Eseguire `cd` nella directory clonata.
Installare i pacchetti necessari.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>Configurare la connessione di MySQL

Nella radice del repository creare un file denominato *.env*. Copiare le variabili seguenti nel file *.env*. Sostituire il segnaposto _&lt;root_password>_ con la password dell'utente ROOT MySQL.

```txt
APP_ENV=local
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

Per informazioni su come viene usato il file _.env_ da Laravel, vedere [Laravel Environment Configuration](https://laravel.com/docs/5.4/configuration#environment-configuration) (Configurazione dell'ambiente Laravel).

### <a name="run-the-sample-locally"></a>Eseguire l'esempio in locale

Eseguire le [migrazioni del database di Laravel](https://laravel.com/docs/5.4/migrations) per creare le tabelle necessarie all'applicazione. Per visualizzare le tabelle create nelle migrazioni, esaminare la directory _database/migrations_ nel repository Git.

```bash
php artisan migrate
```

Generare una nuova chiave per l'applicazione Laravel.

```bash
php artisan key:generate
```

Eseguire l'applicazione.

```bash
php artisan serve
```

Andare a `http://localhost:8000` in un browser. Nella pagina aggiungere alcune attività.

![PHP si connette correttamente a MySQL](./media/tutorial-php-mysql-app/mysql-connect-success.png)

Per arrestare PHP, digitare `Ctrl + C` nel terminale.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Creare MySQL in Azure

In questo passaggio viene creato un database MySQL nel [database di Azure per MySQL (anteprima)](/azure/mysql). Successivamente viene configurata l'applicazione PHP per la connessione al database.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-mysql-server"></a>Creare un server MySQL

Creare un server in Database di Azure per MySQL (anteprima) con il comando [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create).

Nel comando seguente sostituire il segnaposto _&lt;mysql_server_name>_ con il nome del server MySQL (i caratteri validi sono `a-z`, `0-9` e `-`). Poiché questo nome fa parte del nome host del server MySQL (`<mysql_server_name>.database.windows.net`) è necessario che sia univoco a livello globale.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd!
```

Al termine della creazione del server MySQL, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Configurare il firewall del server

Creare una regola del firewall per il server MySQL per consentire le connessioni client usando il comando [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create).

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Database di Azure per MySQL (anteprima) attualmente non limita le connessioni solo ai servizi di Azure. Poiché gli indirizzi IP in Azure sono assegnati dinamicamente, è consigliabile abilitare tutti gli indirizzi IP. Il servizio è disponibile in anteprima. Verranno resi disponibili metodi migliori per la protezione del database.
>

### <a name="connect-to-production-mysql-server-locally"></a>Connettersi al server MySQL di produzione in locale

Nella finestra terminale connettersi al server MySQL in Azure. Usare il valore specificato in precedenza per _&lt;mysql_server_name>_.

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.database.windows.net -P 3306 -p
```

Quando viene richiesta una password, usare _$tr0ngPa$w0rd!_, ossia la password specificata al momento della creazione del server di database.

### <a name="create-a-production-database"></a>Creare un database di produzione

Al prompt `mysql` creare un database.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Creare un utente con autorizzazioni

Creare un utente di database denominato _phpappuser_ e assegnare all'utente tutti i privilegi per il database `sampledb`.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Chiudere la connessione al server digitando `quit`.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Connettere l'app a MySQL di Azure

In questo passaggio l'applicazione PHP viene connessa al database MySQL creato in Database di Azure per MySQL (anteprima).

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Configurare la connessione al database

Nella radice del repository creare un file _.env.production_ e copiare le variabili seguenti nel file. Sostituire il segnaposto _&lt;mysql_server_name>_.

```txt
APP_ENV=production
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql_server_name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Salvare le modifiche.

> [!TIP]
> Per proteggere le informazioni di connessione MySQL, il file è già escluso dal repository Git (vedere _.gitignore_ nella radice del repository). Successivamente verrà illustrato come configurare le variabili di ambiente nel servizio app per connettere il database in Database di Azure per MySQL (anteprima). Con le variabili di ambiente non è necessario il file *.env* nel servizio app.
>

### <a name="configure-ssl-certificate"></a>Configurare il certificato SSL

Per impostazione predefinita, Database di Azure per MySQL impone le connessioni SSL dai client. Per la connessione al database MySQL in Azure, è necessario usare il [certificato con estensione _pem_ fornito da Database di Azure per MySQL](../../mysql/howto-configure-ssl.md).

Aprire _config/database.php_ e aggiungere i parametri _sslmode_ e _options_ a `connections.mysql`, come illustrato nel codice seguente.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem', 
    ] : []
],
```

Per praticità, nel repository è disponibile il certificato `BaltimoreCyberTrustRoot.crt.pem` per questa esercitazione. 

### <a name="test-the-application-locally"></a>Testare l'applicazione in locale

Eseguire le migrazioni del database di Laravel con _.env.production_ come file dell'ambiente per creare le tabelle nel proprio database MySQL nel database di Azure per MySQL (anteprima). Tenere presente che _. env.production_ include le informazioni di connessione al database MySQL in Azure.

```bash
php artisan migrate --env=production --force
```

_.env.production_ non dispone ancora di una chiave applicazione valida. Generarne una nuova nel terminale.

```bash
php artisan key:generate --env=production --force
```

Eseguire l'applicazione di esempio con _.env.production_ come file dell'ambiente.

```bash
php artisan serve --env=production
```

Accedere a `http://localhost:8000`. Se la pagina viene caricata senza errori, l'applicazione PHP si connette al database MySQL in Azure.

Nella pagina aggiungere alcune attività.

![PHP si connette correttamente al database di Azure per MySQL (anteprima)](./media/tutorial-php-mysql-app/mysql-connect-success.png)

Per arrestare PHP, digitare `Ctrl + C` nel terminale.

### <a name="commit-your-changes"></a>Eseguire il commit delle modifiche

Eseguire i comandi Git seguenti per eseguire il commit delle modifiche:

```bash
git add .
git commit -m "database.php updates"
```

L'app è pronta per la distribuzione.

## <a name="deploy-to-azure"></a>Distribuisci in Azure

In questo passaggio viene distribuita l'applicazione PHP connessa a MySQL nel servizio app di Azure.

L'applicazione Laravel viene avviata nella directory _/public_. L'immagine Docker PHP predefinita per il servizio app usa Apache e non consente di personalizzare `DocumentRoot` per Laravel. È tuttavia possibile usare `.htaccess` per riscrivere tutte le richieste in modo da puntare a _/public_ anziché alla directory radice. Nella radice del repository è già stato aggiunto un elemento `.htaccess` a tale scopo. In questo modo, l'applicazione Laravel è pronta per essere distribuita.

> [!NOTE] 
> Se si preferisce non usare la riscrittura con _.htaccess_, si può distribuire l'applicazione Laravel con un'[immagine Docker personalizzata](quickstart-docker-go.md).
>
>

### <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Creare un'app Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-php-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Configurare le impostazioni del database

Nel servizio app, le variabili di ambiente vengono configurate come _impostazioni dell'app_ usando il comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

Il comando seguente configura le impostazioni dell'app `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` e `DB_PASSWORD`. Sostituire i segnaposto _&lt;appname>_ e _&lt;mysql_server_name>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.database.windows.net" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

È possibile usare il metodo [getenv](http://www.php.net/manual/function.getenv.php) di PHP per accedere alle impostazioni. Il codice Laravel usa un wrapper [env](https://laravel.com/docs/5.4/helpers#method-env) su `getenv` di PHP. Ad esempio, la configurazione di MySQL in _config/database.php_ è simile al codice seguente:

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Configurare le variabili di ambiente di Laravel

Laravel richiede una chiave di applicazione nel servizio app. È possibile configurarla con le impostazioni dell'app.

Usare `php artisan` per generare una nuova chiave applicazione senza salvarla in _.env_.

```bash
php artisan key:generate --show
```

Impostare la chiave applicazione nell'app Web del servizio app usando il comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set). Sostituire i segnaposto _&lt;appname>_ e _&lt;outputofphpartisankey:generate>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` indica a Laravel di restituire le informazioni di debug quando l'app Web distribuita rileva errori. Quando si esegue un'applicazione di produzione, è consigliabile impostarla su `false`.

### <a name="push-to-azure-from-git"></a>Effettuare il push in Azure da Git

Aggiungere un'istanza remota di Azure al repository Git locale.

```bash
git remote add azure <paste_copied_url_here>
```

Effettuare il push a un'istanza remota di Azure per distribuire l'applicazione PHP. Verrà richiesta la password specificata in precedenza durante la creazione dell'utente di distribuzione.

```bash
git push azure master
```

Durante la distribuzione, Servizio app di Azure comunica lo stato con Git.

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
```

> [!NOTE]
> Si può notare che, alla fine, il processo di distribuzione installa i pacchetti [Composer](https://getcomposer.org/). Il servizio app non esegue tali automazioni durante la distribuzione predefinita, pertanto questo repository di esempio include tre file aggiuntivi nella directory radice per abilitarlo:
>
> - `.deployment` - Questo file indica al servizio app di eseguire `bash deploy.sh` come script di distribuzione personalizzato.
> - `deploy.sh` - Script di distribuzione personalizzato. Se si esamina il file, si noterà che esegue `php composer.phar install` dopo `npm install`.
> - `composer.phar` - Gestione pacchetti Composer.
>
> È possibile usare questo approccio per aggiungere uno o più passaggi alla distribuzione basata su Git al servizio app. Per altre informazioni, vedere [Custom Deployment Script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) (Script di distribuzione personalizzata).
>

### <a name="browse-to-the-azure-web-app"></a>Passare all'app Web di Azure

Passare a `http://<app_name>.azurewebsites.net` e aggiungere alcune attività all'elenco.

![App PHP in esecuzione nel Servizio app di Azure](./media/tutorial-php-mysql-app/php-mysql-in-azure.png)

L'app PHP basata su dati è in esecuzione nel servizio app di Azure.

## <a name="update-model-locally-and-redeploy"></a>Aggiornare il modello in locale e rieseguire la distribuzione

In questo passaggio viene apportata una modifica semplice al modello di dati `task` e all'app Web e quindi viene pubblicato l'aggiornamento in Azure.

Per lo scenario delle attività, l'applicazione viene modificata per poter contrassegnare un'attività come completata.

### <a name="add-a-column"></a>Aggiungere una colonna

Nel terminale passare alla radice del repository Git.

Generare una nuova migrazione di database per la tabella `tasks`:

```bash
php artisan make:migration add_complete_column --table=tasks
```

Questo comando mostra il nome del file di migrazione che viene generato. Trovare il file in _database/migrations_ e aprirlo.

Sostituire il metodo `up` con il codice seguente:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

Il codice precedente aggiunge una colonna booleana nella tabella `tasks` denominata `complete`.

Sostituire il metodo `down` con il codice seguente per l'azione di ripristino dello stato precedente:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

Nel terminale eseguire le migrazioni del database Laravel per apportare la modifica nel database locale.

```bash
php artisan migrate
```

In base alla [convenzione di denominazione Laravel](https://laravel.com/docs/5.4/eloquent#defining-models), il modello `Task` (vedere _app/Task.php_) esegue il mapping alla tabella `tasks` per impostazione predefinita.

### <a name="update-application-logic"></a>Aggiornare la logica dell'applicazione

Aprire il file *routes/web.php*. L'applicazione definisce qui le route e la logica di business.

Alla fine del file aggiungere una route con il codice seguente:

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

Il codice precedente esegue un semplice aggiornamento del modello di dati tramite l'attivazione e la disattivazione del valore di `complete`.

### <a name="update-the-view"></a>Aggiornare la visualizzazione

Aprire il file *resources/views/tasks.blade.php*. Trovare il tag di apertura `<tr>` e sostituirlo con:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

Il codice precedente modifica il colore di riga in base al completamento dell'attività.

Nella riga successiva il codice è quello riportato di seguito:

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Sostituire l'intera riga con il codice seguente:

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

Il codice precedente aggiunge il pulsante di invio che fa riferimento alla route definita in precedenza.

### <a name="test-the-changes-locally"></a>Testare le modifiche in locale

Dalla directory radice del repository Git eseguire il server di sviluppo.

```bash
php artisan serve
```

Per visualizzare la modifica dello stato dell'attività, passare a `http://localhost:8000` e selezionare la casella di controllo.

![Casella di controllo aggiuntiva all'attività](./media/tutorial-php-mysql-app/complete-checkbox.png)

Per arrestare PHP, digitare `Ctrl + C` nel terminale.

### <a name="publish-changes-to-azure"></a>Pubblicare le modifiche in Azure

Nel terminale eseguire le migrazioni del database Laravel con la stringa di connessione di produzione per apportare la modifica nel database di Azure.

```bash
php artisan migrate --env=production --force
```

Eseguire il commit di tutte le modifiche in Git e quindi effettuare il push delle modifiche al codice in Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Dopo aver completato `git push`, passare all'app Web di Azure e testare la nuova funzionalità.

![Modifiche al modello e al database pubblicate in Azure](media/tutorial-php-mysql-app/complete-checkbox-published.png)

Le attività aggiunte vengono mantenute nel database. Gli aggiornamenti allo schema di dati non modificano i dati esistenti.

## <a name="manage-the-azure-web-app"></a>Gestire l'app Web di Azure

Accedere al [portale di Azure](https://portal.azure.com) per gestire l'app Web creata.

Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Web di Azure.

![Passare all'app Web di Azure nel portale](./media/tutorial-php-mysql-app/access-portal.png)

Verrà visualizzata la pagina di panoramica dell'app Web. Nella pagina è possibile eseguire attività di gestione di base come l'arresto, l'avvio, il riavvio e l'eliminazione.

Il menu a sinistra consente di visualizzare le pagine di configurazione dell'app.

![Pagina del servizio app nel portale di Azure](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un database MySQL in Azure
> * Connettere un'app PHP a MySQL
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log di diagnostica in Azure
> * Gestire l'app nel portale di Azure

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato a un'app Web.

> [!div class="nextstepaction"]
> [Eseguire il mapping di un nome DNS personalizzato esistente ad app Web di Azure](../app-service-web-tutorial-custom-domain.md)

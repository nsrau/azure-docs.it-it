---
title: Creare un&quot;app Web PHP e MySQL in Azure | Microsoft Docs
description: "Informazioni su come ottenere un&quot;app PHP che è possibile usare in Azure con connessione a un database MySQL in Azure."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/05/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 3ad716fab4f5084c38c83f4bc90a616856666b38
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017

---
# <a name="build-a-php-and-mysql-web-app-in-azure"></a>Creare un'app Web PHP e MySQL in Azure
Questa esercitazione illustra come creare un'app Web PHP in Azure e connetterla a un database MySQL. Al termine, si avrà un'applicazione [Laravel](https://laravel.com/) in esecuzione su [app Web del Servizio app di Azure](app-service-web-overview.md).

![App PHP in esecuzione nel Servizio app di Azure](./media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un database MySQL in Azure
> * Connettere un'app PHP a MySQL
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log di diagnostica in Azure
> * Gestire l'app nel portale di Azure

## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire l'esempio, installare in locale questi prerequisiti:

1. [Scaricare e installare Git](https://git-scm.com/)
1. [Scaricare e installare PHP 5.6.4 o versione successiva](http://php.net/downloads.php)
1. [Scaricare e installare Composer](https://getcomposer.org/doc/00-intro.md)
1. Abilitare le estensioni PHP seguenti necessarie per Laravel: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
1. [Scaricare, installare e avviare MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 
1. [Scaricare e installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-local-mysql"></a>Preparare MySQL in locale

In questo passaggio, si crea un database nel server locale di MySQL da usare in questa esercitazione.

### <a name="connect-to-mysql-server"></a>Connettersi al server MySQL
In una finestra terminale connettersi al server MySQL locale.

```bash
mysql -u root -p
```

Se viene richiesto di immettere una password, immettere la password per l'account `root`. Se non si ricorda la password dell'account radice, vedere [MySQL: procedura per reimpostare la password radice](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Se il comando viene eseguito correttamente, il server MySQL è già in esecuzione. In caso contrario, assicurarsi che il server MySQL locale sia stato avviato seguendo la [procedura successiva all'installazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database"></a>Creare un database

Nel prompt `mysql` creare un database.

```sql
CREATE DATABASE sampledb;
```

Chiudere la connessione al server digitando `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-local-php-app"></a>Creare un'app PHP locale
In questo passaggio si ottiene un'applicazione di esempio Laravel, se ne configurare la connessione al database e la si esegue in locale. 

### <a name="clone-the-sample"></a>Clonare l'esempio

Aprire la finestra del terminale e usare il comando `cd` per passare a una directory di lavoro.  

Eseguire i comandi seguenti per clonare il repository di esempio. 

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

`cd` nella directory clonata e installare i pacchetti necessari.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>Configurare la connessione di MySQL

Nella radice del repository creare un file _.env_ e copiare le variabili seguenti al suo interno. Sostituire il segnaposto _&lt;root_password>_ con la password dell'utente ROOT.

```
APP_ENV=local
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

> [!NOTE]
> Per informazioni su come viene usato il file _.env_ da Laravel, vedere l'articolo sulla [configurazione dell'ambiente di Laravel](https://laravel.com/docs/5.4/configuration#environment-configuration).
>
>

### <a name="run-the-sample"></a>Eseguire l'esempio

Eseguire le [migrazioni del database di Laravel](https://laravel.com/docs/5.4/migrations) per creare le tabelle necessarie all'applicazione. Per visualizzare le tabelle create nelle migrazioni, esaminare la directory del _database/migrazioni_ nell'archivio Git.

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

![PHP si connette correttamente a MySQL](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Per arrestare PHP in qualsiasi momento, digitare `Ctrl`+`C` nel terminale. 

## <a name="create-production-mysql-in-azure"></a>Creare MySQL di produzione in Azure

In questo passaggio viene creato un database MySQL nel [database di Azure per MySQL (anteprima)](/azure/mysql). Successivamente verrà configurata l'applicazione PHP per la connessione al database.

### <a name="log-in-to-azure"></a>Accedere ad Azure

Verrà ora usata l'interfaccia della riga di comando di Azure 2.0 in una finestra terminale per creare le risorse necessarie a ospitare l'applicazione PHP nel Servizio app di Azure. Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#login) e seguire le istruzioni visualizzate. 

```azurecli-interactive 
az login 
``` 

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md) con il comando [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un contenitore logico in cui vengono distribuite e gestite risorse di Azure come app Web, database e account di archiviazione. 

L'esempio seguente crea un gruppo di risorse nell'area Europa settentrionale:

```azurecli-interactive
az group create --name myResourceGroup --location "North Europe"
```

Per visualizzare i possibili valori utilizzabili per `--location`, usare il comando [az appservice list-locations](/cli/azure/appservice#list-locations).

### <a name="create-a-mysql-server"></a>Creare un server MySQL

Creare un server nel database di Azure per MySQL (anteprima) con il comando [az mysql server create](/cli/azure/mysql/server#create).

Nel comando seguente sostituire il nome del server MySQL univoco dove viene visualizzato il segnaposto _&lt;mysql_server_name>_. Questo nome fa parte del nome host del server MySQL, `<mysql_server_name>.database.windows.net`, pertanto deve essere univoco a livello globale. Analogamente, sostituire _&lt;admin_user>_ e _&lt;admin_password>_ con i propri valori.

```azurecli-interactive
az mysql server create \
    --name <mysql_server_name> \
    --resource-group myResourceGroup \
    --location "North Europe" \
    --user <admin_user> \
    --password <admin_password>
```

Al termine della creazione del server MySQL, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente:

```json
{
  "administratorLogin": "<admin_user>",
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

Creare una regola del firewall per il server MySQL per consentire le connessioni client tramite il comando [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create). 

```azurecli-interactive
az mysql server firewall-rule create \
    --name allIPs \
    --server <mysql_server_name> \
    --resource-group myResourceGroup \
    --start-ip-address 0.0.0.0 \
    --end-ip-address 255.255.255.255
```

> [!NOTE]
> Il database di Azure per MySQL (anteprima) non consente ancora di abilitare le connessioni solo dai servizi di Azure. Poiché gli indirizzi IP in Azure vengono assegnati dinamicamente, per il momento è preferibile abilitarli tutti. Poiché il servizio è in anteprima, presto verranno abilitati metodi migliori per la protezione del database.
>
>

### <a name="connect-to-production-mysql-server"></a>Connettersi al server MySQL di produzione

Nella finestra terminale connettersi al server MySQL in Azure. Usare il valore specificato in precedenza per _&lt;admin_user>_ e _&lt;mysql_server_name>_.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.database.windows.net -P 3306 -p
```

### <a name="create-a-production-database"></a>Creare un database di produzione

Nel prompt `mysql` creare un database.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Creare un utente con autorizzazioni

Creare un utente del database e concedergli tutti i privilegi nel database `sampledb`. Sostituire i segnaposto _&lt;phpapp_user>_ e _&lt;phpapp_password>_ con il nome dell'app univoco.

```sql
CREATE USER '<phpapp_user>' IDENTIFIED BY '<phpapp_password>'; 
GRANT ALL PRIVILEGES ON sampledb.* TO '<phpapp_user>';
```

Chiudere la connessione al server digitando `quit`.

```sql
quit
```

## <a name="connect-app-to-production-mysql"></a>Connettere l'app a MySQL di produzione

In questo passaggio l'applicazione PHP viene connessa al database MySQL appena creato nel database di Azure per MySQL (anteprima). 

<a name="devconfig"></a>
### <a name="configure-the-connection"></a>Configurare la connessione 

Nella radice del repository creare un file _.env.production_ e copiare le variabili seguenti al suo interno. Sostituire i segnaposto _&lt;mysql_server_name>_, _&lt;phpapp_user>_ e _&lt;phpapp_password>_.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.database.windows.net
DB_DATABASE=sampledb
DB_USERNAME=<phpapp_user>@<mysql_server_name>
DB_PASSWORD=<phpapp_password>
```

Salvare le modifiche.

### <a name="test-the-application"></a>Test dell'applicazione

Eseguire le migrazioni del database di Laravel con _.env.production_ come file dell'ambiente per creare le tabelle nel proprio database MySQL nel database di Azure per MySQL (anteprima).

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

Andare a `http://localhost:8000` in un browser. Se la pagina viene caricata senza errori, l'applicazione PHP si connette al database MySQL in Azure. 

Nella pagina aggiungere alcune attività.

![PHP si connette correttamente al database di Azure per MySQL (anteprima)](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Per arrestare PHP in qualsiasi momento, digitare `Ctrl`+`C` nel terminale. 

### <a name="secure-sensitive-data"></a>Proteggere i dati sensibili

È necessario assicurarsi che per i dati sensibili in _.env.production_ non venga eseguito il commit in Git.

A tale scopo, aprire _gitignore_ dalla radice del repository e aggiungere il nome del file in una nuova riga:

```
.env.production
```

Salvare le modifiche, quindi eseguire il commit delle modifiche in Git.

```bash
git add .gitignore
git commit -m "keep sensitive data out of git"
```

Successivamente, verrà illustrato come configurare le variabili di ambiente nel servizio app per la connessione al database nel database di Azure per MySQL (anteprima), pertanto non sarà necessario alcune file `.env` nel servizio app. 

## <a name="deploy-php-app-to-azure"></a>Distribuire l'app PHP in Azure
In questo passaggio si distribuisce l'applicazione PHP connessa a MySQL nel Servizio app di Azure.

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

Creare un piano di servizio app con il comando [az appservice plan create](/cli/azure/appservice/plan#create). 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

L'esempio seguente crea un piano di servizio app denominato _myAppServicePlan_ usando il piano tariffario **GRATUITO**:

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \
    --resource-group myResourceGroup \
    --sku FREE
```

Al termine della creazione del piano di servizio app, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente:

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-a-web-app"></a>Creare un'app Web

Dopo aver creato un piano di servizio app, occorre creare un'app Web all'interno del piano di servizio app _myAppServicePlan_. L'app Web fornisce uno spazio host per distribuire il codice e un URL in cui visualizzare l'applicazione distribuita. Usare il comando [az appservice web create](/cli/azure/appservice/web#create) per creare l'app Web. 

Nel comando seguente sostituire il segnaposto _&lt;appname>_ con il nome univoco dell'app. Questo nome univoco verrà usato come parte del nome di dominio predefinito per l'app Web, pertanto è necessario che sia univoco rispetto a tutte le app presenti in Azure. In un secondo momento è possibile eseguire il mapping di qualsiasi voce DNS personalizzata all'app Web prima di esporla agli utenti. 

```azurecli-interactive
az appservice web create \
    --name <app_name> \
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

Al termine della creazione dell'app Web, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="set-the-php-version"></a>Impostare la versione di PHP

Impostare la versione PHP necessaria all'applicazione usando il comando [az appservice web config update](/cli/azure/appservice/web/config#update).

Il comando seguente imposta la versione PHP su _7.0_.

```azurecli-interactive
az appservice web config update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --php-version 7.0
```

### <a name="configure-database-settings"></a>Configurare le impostazioni del database

Come evidenziato in precedenza, è possibile connettersi al database MySQL di Azure mediante le variabili di ambiente nel servizio app.

Nel servizio app si impostano le variabili di ambiente come _impostazioni dell'app_ usando il comando [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). 

Il comando seguente consente di configurare le impostazioni dell'app `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` e `DB_PASSWORD`. Sostituire i segnaposto _&lt;appname>_, _&lt;mysql_server_name>_, _&lt;phpapp_user>_ e _&lt;phpapp_password>_.

```azurecli-interactive
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings DB_HOST="<mysql_server_name>.database.windows.net" DB_DATABASE="sampledb" DB_USERNAME="<phpapp_user>@<mysql_server_name>" DB_PASSWORD="<phpapp_password>"
```

È possibile usare il metodo [getenv()](http://www.php.net/manual/function.getenv.php) di PHP per accedere alle impostazioni. Il codice Laravel usa un wrapper [env()](https://laravel.com/docs/5.4/helpers#method-env) su `getenv()` di PHP. Ad esempio, la configurazione di MySQL in _config/database.php_ è simile alla seguente:

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

Analogamente a quanto avviene per il computer locale, Laravel necessita di una chiave applicazione nel servizio app. È possibile configurarla anche con le impostazioni app.

Usare `php artisan` per generare una nuova chiave applicazione senza salvarla in _.env_.

```bash
php artisan key:generate --show
```

Impostare la chiave dell'applicazione nell'app Web del servizio app usando il comando [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update). Sostituire i segnaposto _&lt;appname>_ e _&lt;outputofphpartisankey:generate>_.

```azurecli-interactive
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

> [!NOTE]
> `APP_DEBUG="true"` indica a Laravel di restituire le informazioni di debug, se l'app Web distribuita incontra errori. Quando si esegue un'applicazione di produzione, è necessario impostarla su `false` per motivi di sicurezza.
>
>

### <a name="set-the-virtual-application-path"></a>Impostare il percorso virtuale dell'applicazione

Impostare il percorso virtuale dell'applicazione per l'app Web. Questo passaggio è necessario solo perché il [ciclo di vita dell'applicazione Laravel](https://laravel.com/docs/5.4/lifecycle) inizia nella directory _pubblica_ anziché nella directory radice dell'applicazione. Gli altri framework PHP il cui ciclo di vita si avvia nella directory radice funzionano anche senza la configurazione manuale del percorso dell'applicazione virtuale.

Impostare il percorso dell'applicazione virtuale tramite il comando il [az resource update](/cli/azure/resource#update). Sostituire il segnaposto _&lt;appname>_ .

```bash
az resource update \
    --name web \
    --resource-group myResourceGroup \
    --namespace Microsoft.Web \
    --resource-type config \
    --parent sites/<app_name> \
    --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" \
    --api-version 2015-06-01
```

> [!NOTE]
> Per impostazione predefinita, il Servizio app di Azure fa in modo che il percorso dell'applicazione virtuale radice (_/_) punti alla directory radice dei file dell'applicazione distribuiti (_sites\wwwroot_). 
>
>

### <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione

Per la distribuzione con FTP e l'istanza Git locale, è necessario che nel server sia configurato un utente della distribuzione per l'autenticazione. 

> [!NOTE] 
> Un utente della distribuzione è necessario per la distribuzione con FTP e l'istanza Git locale in un'app Web. Il nome utente e la password sono a livello di account e sono quindi diversi dalle credenziali della sottoscrizione di Azure.

Se in precedenza è stato creato un nome utente e una password di distribuzione, è possibile usare il comando seguente per visualizzare il nome utente:

```azurecli-interactive
az appservice web deployment user show
```

Se non si dispone già di un utente di distribuzione, eseguire il comando [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) per creare le credenziali di distribuzione. 

```azurecli-interactive
az appservice web deployment user set \
    --user-name <username> \
    --password <minimum-8-char-capital-lowercase-number>
```

Il nome utente deve essere univoco e la password deve essere complessa. Se viene visualizzato un errore `'Conflict'. Details: 409`, modificare il nome utente. Se viene visualizzato un errore `'Bad Request'. Details: 400`, usare una password più complessa.

È necessario creare l'utente di distribuzione una sola volta; è possibile usarlo per tutte le distribuzioni di Azure.

Registrare il nome utente e la password, poiché verranno usate in un secondo momento, quando si distribuisce l'applicazione.

### <a name="configure-local-git-deployment"></a>Configurare la distribuzione con l'istanza Git locale 

È possibile distribuire l'applicazione nel Servizio app di Azure attraverso varie soluzioni, tra cui FTP, istanza Git locale, GitHub, Visual Studio Team Services e BitBucket. 

Usare il comando [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) per configurare l'accesso dell'istanza Git locale ad app Web di Azure. 

```azurecli-interactive
az appservice web source-control config-local-git \
    --name <app_name> \
    --resource-group myResourceGroup
```

Dopo che l'utente della distribuzione è stato configurato, l'interfaccia della riga di comando di Azure visualizza l'URL della distribuzione per l'app Web di Azure nel formato seguente:

```bash 
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git 
``` 

Copiare l'output dal terminale, perché verrà usato nel passaggio successivo. 

### <a name="push-to-azure-from-git"></a>Effettuare il push in Azure da Git

Aggiungere un'istanza remota di Azure al repository Git locale. 

```bash
git remote add azure <paste_copied_url_here> 
```

Effettuare il push di Azure in remoto per distribuire l'applicazione PHP. Verrà richiesta la password specificata in precedenza nel corso della creazione dell'utente della distribuzione. 

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
>

### <a name="browse-to-the-azure-web-app"></a>Passare all'app Web di Azure

Passare a `http://<app_name>.azurewebsites.net` e aggiungere alcune attività all'elenco. 

![App PHP in esecuzione nel Servizio app di Azure](./media/app-service-web-tutorial-php-mysql/php-mysql-in-azure.png)

**Congratulazioni.** L'app PHP basata su dati è in esecuzione nel Servizio app di Azure.

## <a name="update-data-model-and-redeploy"></a>Aggiornare il modello di dati e ridistribuire

In questo passaggio si apportano alcune modifiche al modello di dati `task` e le si pubblica in Azure.

Per lo scenario di attività, si desidera modificare l'applicazione in modo da poter contrassegnare un'attività come completata. 

### <a name="add-a-column"></a>Aggiungere una colonna

Nel terminale verificare di trovarsi nella radice del repository Git, quindi generare una nuova migrazione di database per la tabella `tasks`.

```bash
php artisan make:migration add_complete_column --table=tasks
```

Questo comando mostra il nome del file di migrazione che viene generato. Trovare il file nel _database/migrazioni_ e aprirlo in un editor di testo.

Sostituire il metodo up() con il codice seguente:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

Questo codice aggiunge una colonna booleana nella tabella `tasks` denominata `complete`.

Sostituire il metodo down() con il codice seguente per l'azione di ripristino dello stato precedente:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

Nel terminale eseguire le migrazioni del database Laravel in locale per apportare modifiche al database locale.

```bash
php artisan migrate
```

In base alla [convenzione di denominazione Laravel](https://laravel.com/docs/5.4/eloquent#defining-models), il modello `Task` (vedere _app/Task.php_) esegue il mapping per la tabella `tasks` per impostazione predefinita, in modo da non dover più eseguire l'aggiornamento del modello di dati.

### <a name="update-application-logic"></a>Aggiornare la logica dell'applicazione

Aprire _routes/web.php_. L'applicazione di esempio definisce una route e la logica di business qui.

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

Questo codice consente un semplice aggiornamento per il modello di dati tramite l'attivazione e la disattivazione del valore di `complete`.

### <a name="update-the-view"></a>Aggiornare la visualizzazione

Aprire _resources/views/tasks.blade.php_. Trovare il tag di apertura `<tr>` e sostituirlo con:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

Questo modifica il colore di riga se l'attività è stata completata.

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

Questo codice aggiunge il pulsante di invio che fa riferimento alla route definita in precedenza.

### <a name="test-your-changes-locally"></a>Testare le modifiche in locale

Dalla directory radice del repository Git eseguire nuovamente il server di sviluppo.

```bash
php artisan serve
```

Passare a `http://localhost:8000` in un browser e selezionare la casella di controllo per visualizzare la modifica dello stato dell'attività di conseguenza.

![Casella di controllo aggiuntiva all'attività](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

### <a name="publish-changes-to-azure"></a>Pubblicare le modifiche in Azure

Nel terminale eseguire le migrazioni del database Laravel con la stringa di connessione di produzione per apportare la modifica nel database di produzione in Azure.

```bash
php artisan migrate --env=production --force
```

Eseguire il commit di tutte le modifiche in Git e quindi effettuare il push delle modifiche al codice in Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Dopo aver completato il comando `git push`, passare di nuovo all'app Web di Azure e provare la nuova funzionalità.

![Modifiche al modello e al database pubblicate in Azure](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

> [!NOTE]
> Eventuali attività aggiunte in precedenza sono ancora visibili. Gli aggiornamenti allo schema di dati lasciano intatti i dati esistenti.
>
>

## <a name="stream-diagnostic-logs"></a>Eseguire lo streaming dei log di diagnostica 

Mentre l'applicazione PHP è in esecuzione nel Servizio app di Azure, è possibile fare in modo che i log di console siano inviati tramite pipe direttamente al terminale. Ciò consente di ottenere gli stessi messaggi di diagnostica per il debug degli errori dell'applicazione.

Per avviare lo streaming dei log, usare il comando [az appservice web log tail](/cli/azure/appservice/web/log#tail).

```azurecli-interactive 
az appservice web log tail \
    --name <app_name> \
    --resource-group myResourceGroup 
``` 

Dopo avere avviato lo streaming del log, aggiornare l'app Web di Azure nel browser per ottenere traffico Web. I log di console dovrebbero a questo punto essere inviati tramite pipe al terminale.

Per interrompere lo streaming dei log in qualsiasi momento, digitare `Ctrl`+`C`. 

> [!TIP]
> Un'applicazione PHP può usare lo standard [error_log()](http://php.net/manual/function.error-log.php) da inviare alla console. L'applicazione di esempio usa questo approccio in _app/Http/routes.php_.
>
> Come framework Web, [Laravel usa Monolog](https://laravel.com/docs/5.4/errors) come provider di registrazione. Per informazioni su come fare in modo che Monolog invii messaggi alla console, vedere [PHP: How to use monolog to log to console (php://out)](http://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out) (PHP: procedura per usare Monolog per registrarsi alla console (php://out)).
>
>

## <a name="manage-your-azure-web-app"></a>Gestire l'app Web di Azure

Accedere al portale di Azure per visualizzare l'app Web creata.

A tale scopo, accedere a [https://portal.azure.com](https://portal.azure.com).

Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Web di Azure.

![Passare all'app Web di Azure nel portale](./media/app-service-web-tutorial-php-mysql/access-portal.png)

Si accede così al _pannello_, ovvero una pagina del portale visualizzata in orizzontale, dell'app Web.

Per impostazione predefinita, nel pannello dell'app Web viene aperta la pagina **Panoramica**, che offre una visualizzazione dello stato dell'app. In questa pagina è anche possibile eseguire attività di gestione di base come esplorare, arrestare, avviare, riavviare ed eliminare. Le schede sul lato sinistro del pannello mostrano le diverse pagine di configurazione che è possibile aprire.

![Pannello del servizio app nel portale di Azure](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

Queste schede del pannello mostrano le numerose utili funzionalità che è possibile aggiungere all'app Web. Nell'elenco seguente sono riportate solo alcune delle possibilità:

* Eseguire il mapping di un nome DNS personalizzato
* Associare un certificato SSL personalizzato
* Configurare la distribuzione continua
* Aumentare le prestazioni e il numero di istanze
* Aggiungere l'autenticazione utente

## <a name="clean-up-resources"></a>Eseguire la pulizia delle risorse
 
Se queste risorse non sono necessarie per un'altra esercitazione (vedere [Passaggi successivi](#next)), è possibile eliminarle eseguendo questo comando: 
  
```azurecli-interactive
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un database MySQL in Azure
> * Connettere un'app PHP a MySQL
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log di diagnostica in Azure
> * Gestire l'app nel portale di Azure

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato.

> [!div class="nextstepaction"] 
> [Eseguire il mapping di un nome DNS personalizzato esistente ad app Web di Azure](app-service-web-tutorial-custom-domain.md)


---
title: Compilare un'app web Ruby e MySQL in Azure App Service in Linux | Documenti Microsoft
description: Informazioni su come ottenere un'app Ruby Usa Azure, con connessione a un database MySQL in Azure.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/21/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 03b2b4e8f8827a08e1414512d848bd5bed48d674
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="build-a-ruby-and-mysql-web-app-in-azure-app-service-on-linux"></a>Compilare un'app web Ruby e MySQL in Azure App Service su Linux

Il [Servizio app in Linux](app-service-linux-intro.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione basato sul sistema operativo Linux. In questa esercitazione viene illustrato come creare un'app web Ruby e connetterla a un database MySQL. Al termine dell'operazione, è necessario un [Ruby su Guide](http://rubyonrails.org/) app in esecuzione nel servizio App in Linux.

![Ruby Guide App in esecuzione in Azure App Service](./media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un database MySQL in Azure
> * Connettere un Ruby sull'app Guide per MySQL
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log di diagnostica in Azure
> * Gestire l'app nel portale di Azure

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

* [Installare Git](https://git-scm.com/)
* [Installare Ruby 2.3](https://www.ruby-lang.org/documentation/installation/)
* [Installare Ruby in Guide 5.1](http://guides.rubyonrails.org/v5.1/getting_started.html)
* [Installare e avviare MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql"></a>Preparare MySQL in locale

In questo passaggio, si crea un database nel server locale di MySQL da usare in questa esercitazione.

### <a name="connect-to-local-mysql-server"></a>Connettersi al server MySQL locale

In una finestra terminale connettersi al server MySQL locale. È possibile usare questa finestra del terminale per eseguire tutti i comandi presenti in questa esercitazione.

```bash
mysql -u root -p
```

Se viene richiesto di immettere una password, immettere la password per l'account `root`. Se non si ricorda la password dell'account radice, vedere [MySQL: procedura per reimpostare la password radice](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Se il comando viene eseguito correttamente, il server MySQL è in esecuzione. In caso contrario, assicurarsi che il server MySQL locale sia stato avviato seguendo la [procedura successiva all'installazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

Chiudere la connessione al server digitando `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Creare un Ruby in app Guide localmente
In questo passaggio, ottenere un Ruby sull'applicazione di esempio guide, configurare la connessione di database ed eseguirlo in locale. 

### <a name="clone-the-sample"></a>Clonare l'esempio

Nella finestra del terminale usare il comando `cd` per passare a una directory di lavoro.

Eseguire il comando seguente per clonare l'archivio di esempio.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

Eseguire `cd` nella directory clonata. Installare i pacchetti necessari.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="configure-mysql-connection"></a>Configurare la connessione di MySQL

Il repository, aprire _config/database.yml_ e fornire il nome utente principale di MySQL locale e la password (riga 13). Se è stato installato utilizzando uno strumento come MySQL [Homebrew](https://brew.sh/), quindi le credenziali nel file sono già impostate sui valori predefiniti (ovvero `root` e una password vuota).

```txt
default: &default
  adapter: mysql2
  encoding: utf8
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password:
  socket: /tmp/mysql.sock
```

### <a name="run-the-sample-locally"></a>Eseguire l'esempio in locale

Eseguire [le migrazioni Guide](http://guides.rubyonrails.org/active_record_migrations.html#running-migrations) per creare le tabelle dell'applicazione, è necessario. Per visualizzare le tabelle vengono create le migrazioni, esaminare il _db/migrazione_ directory del repository Git.

```bash
rake db:create
rake db:migrate
```

Eseguire l'applicazione.

```bash
rails server
```

Andare a `http://localhost:3000` in un browser. Nella pagina aggiungere alcune attività.

![Ruby su Guide si connette correttamente a MySQL](./media/tutorial-ruby-mysql-app/mysql-connect-success.png)

Per arrestare il server di guide, digitare `Ctrl + C` nei Servizi terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Creare MySQL in Azure

In questo passaggio viene creato un database MySQL nel [database di Azure per MySQL (anteprima)](/azure/mysql). In un secondo momento, configurare il Ruby nell'applicazione Guide per connettersi al database.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Creare un server MySQL

Creare un server nel database di Azure per MySQL (anteprima) con il comando [az mysql server create](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create).

Nel comando seguente sostituire il segnaposto _&lt;mysql_server_name>_ con il nome del server MySQL (i caratteri validi sono `a-z`, `0-9` e `-`). Poiché questo nome fa parte del nome host del server MySQL (`<mysql_server_name>.mysql.database.azure.com`) è necessario che sia univoco a livello globale.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd!
```

Al termine della creazione del server MySQL, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Configurare il firewall del server

Creare una regola del firewall per il server MySQL per consentire le connessioni client tramite il comando [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create).

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Database di Azure per MySQL (anteprima) attualmente non limita le connessioni solo ai servizi di Azure. Poiché gli indirizzi IP in Azure sono assegnati dinamicamente, è consigliabile abilitare tutti gli indirizzi IP. Il servizio è disponibile in anteprima. Verranno resi disponibili metodi migliori per la protezione del database.
>

### <a name="connect-to-production-mysql-server-locally"></a>Connettersi al server MySQL di produzione in locale

Nella finestra terminale connettersi al server MySQL in Azure. Usare il valore specificato in precedenza per _&lt;mysql_server_name>_.

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

Quando viene richiesto di immettere una password, utilizzare _My5up3r tr0ngPa$ $w0rd!_, specificato quando è stato creato il server di database.

### <a name="create-a-production-database"></a>Creare un database di produzione

Al prompt `mysql` creare un database.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Creare un utente con autorizzazioni

Creare un utente di database denominato _railsappuser_ e assegnare tutti i privilegi `sampledb` database.

```sql
CREATE USER 'railsappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'railsappuser';
```

Chiudere la connessione al server digitando `quit`.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Connettere l'app a MySQL di Azure

In questo passaggio è connettersi il Ruby nell'applicazione Guide per il database MySQL creato nel Database di Azure per MySQL (anteprima).

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Configurare la connessione al database

Il repository, aprire _config/database.yml_. Nella parte inferiore del file, sostituire le variabili di produzione con il codice seguente. Per il  _&lt;mysql_server_name >_ segnaposto, utilizzare il nome del server MySQL è stato creato.

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  port: 3306
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
  sslca: ssl/BaltimoreCyberTrustRoot.crt.pem
```

Salvare le modifiche.

> [!NOTE]
> Il `sslca` viene aggiunto e punta a un oggetto esistente _PEM_ file nel repository di esempio. Per impostazione predefinita, Database di Azure per MySQL impone le connessioni SSL dai client. Questo `.pem` certificato è come eseguire connessioni SSL nel Database di Azure per MySQL. Per altre informazioni, vedere [Configurare la connettività SSL nell'applicazione per la connessione sicura a Database di Azure per MySQL](../../mysql/howto-configure-ssl.md).
>

### <a name="test-the-application-locally"></a>Testare l'applicazione in locale

Nei Servizi terminal locale, impostare le variabili di ambiente seguenti:

```bash
export DB_HOST=<mysql_server_name>.mysql.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<mysql_server_name>
export DB_PASSWORD=MySQLAzure2017
```

Eseguire migrazioni database guide con i valori di produzione che appena configurato per creare le tabelle del database MySQL nel Database di Azure per MySQL (anteprima). 

```bash
rake db:migrate RAILS_ENV=production
```

Quando si esegue l'ambiente di produzione, l'applicazione di guide deve asset precompilato. Generare le risorse necessarie con il comando seguente:

```bash
rake assets:precompile
```

Inoltre, l'ambiente di produzione Guide Usa i segreti per gestire la sicurezza. Generare una chiave privata.

```bash
rails secret
```

Salvare la chiave privata per le rispettive variabili utilizzate dall'ambiente di produzione Guide. Per praticità, utilizzare la stessa chiave per entrambe le variabili.

```bash
export RAILS_MASTER_KEY=<output_of_rails_secret>
export SECRET_KEY_BASE=<output_of_rails_secret>
```

Abilitare l'ambiente di produzione Guide trasferisca i file JavaScript e CSS.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Eseguire l'applicazione di esempio nell'ambiente di produzione.

```bash
rails server -e production
```

Accedere a `http://localhost:3000`. Se la pagina viene caricata senza errori, il Ruby sull'applicazione delle Guide si connette al database MySQL in Azure.

Nella pagina aggiungere alcune attività.

![Ruby su Guide si connette correttamente al Database di Azure per MySQL (anteprima)](./media/tutorial-ruby-mysql-app/azure-mysql-connect-success.png)

Per arrestare il server di guide, digitare `Ctrl + C` nei Servizi terminal.

### <a name="commit-your-changes"></a>Eseguire il commit delle modifiche

Eseguire i comandi Git seguenti per eseguire il commit delle modifiche:

```bash
git add .
git commit -m "database.yml updates"
```

L'app è pronta per la distribuzione.

## <a name="deploy-to-azure"></a>Distribuisci in Azure

In questo passaggio, si distribuisce l'applicazione di guide MySQL connessi al servizio App di Azure.

### <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Creare un'app Web

In Cloud Shell creare un'app Web nel piano di servizio app `myAppServicePlan` con il comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). 

Nell'esempio seguente sostituire `<app_name>` con un nome app univoco globale. I caratteri validi sono `a-z`, `0-9` e `-`. Il runtime è impostato su `RUBY|2.3`, che distribuisce il [Ruby immagine predefinita](https://hub.docker.com/r/appsvc/ruby/). Per visualizzare tutti i runtime supportati, eseguire [az webapp list-runtimes](/cli/azure/webapp?view=azure-cli-latest#az_webapp_list_runtimes). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "RUBY|2.3" --deployment-local-git
```

Dopo la creazione dell'app Web, l'interfaccia della riga di comando di Azure mostra un output simile all'esempio seguente:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

È stata creata una nuova app Web vuota, con la distribuzione Git abilitata.

> [!NOTE]
> L'URL dell'elemento Git remoto è riportato nella proprietà `deploymentLocalGitUrl`, con il formato `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Salvare questo URL, perché sarà necessario in un secondo momento.
>

### <a name="configure-database-settings"></a>Configurare le impostazioni del database

Nel servizio App, impostare le variabili di ambiente come _impostazioni app_ utilizzando il [az webapp configurazione appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) comando nella Shell di Cloud.

Il seguente comando di Shell Cloud consente di configurare le impostazioni dell'app `DB_HOST`, `DB_DATABASE`, `DB_USERNAME`, e `DB_PASSWORD`. Sostituire i segnaposto _&lt;appname>_ e _&lt;mysql_server_name>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Configurare le variabili di ambiente Guide

Nei Servizi terminal locale, è possibile generare una nuova chiave segreta per l'ambiente di produzione di guide in Azure.

```bash
rails secret
```

Configurare le variabili richieste dall'ambiente di produzione Guide.

Il comando di Shell Cloud seguente, sostituire le due  _&lt;output_of_rails_secret >_ segnaposto con la nuova chiave segreta generato nel terminale locale.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output_of_rails_secret>" SECRET_KEY_BASE="<output_of_rails_secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"`indica il valore predefinito Ruby contenitore per la precompilazione asset in ogni distribuzione Git.

### <a name="push-to-azure-from-git"></a>Effettuare il push in Azure da Git

Nei Servizi terminal locale, aggiungere una Azure remoto per il repository Git locale.

```bash
git remote add azure <paste_copied_url_here>
```

Effettuare il push di Azure remoto per la distribuzione della Ruby sull'applicazione Guide. Verrà richiesta la password specificata in precedenza durante la creazione dell'utente di distribuzione.

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

### <a name="browse-to-the-azure-web-app"></a>Passare all'app Web di Azure

Passare a `http://<app_name>.azurewebsites.net` e aggiungere alcune attività all'elenco.

![Ruby Guide App in esecuzione in Azure App Service](./media/tutorial-ruby-mysql-app/ruby-mysql-in-azure.png)

Complimenti, si esegue un Ruby basati sui dati in app di Guide nel servizio App di Azure.

## <a name="update-model-locally-and-redeploy"></a>Aggiornare il modello in locale e rieseguire la distribuzione

In questo passaggio viene apportata una modifica semplice al modello di dati `task` e all'app Web e quindi viene pubblicato l'aggiornamento in Azure.

Per lo scenario delle attività, l'applicazione viene modificata per poter contrassegnare un'attività come completata.

### <a name="add-a-column"></a>Aggiungere una colonna

Nel terminale passare alla radice del repository Git.

Generare una nuova migrazione che aggiunge una colonna booleana denominata `Done` per il `Tasks` tabella:

```bash
rails generate migration add_Done_to_Tasks Done:boolean
```

Questo comando genera un nuovo file di migrazione di _db/migrazione_ directory.


I servizi terminal, eseguire le migrazioni database Guide per apportare la modifica nel database locale.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Aggiornare la logica dell'applicazione

Aprire il *app/controllers/tasks_controller.rb* file. Alla fine del file, cercare la riga seguente:

```rb
params.require(:task).permit(:Description)
```

Modificare questa riga per includere il nuovo `Done` parametro.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>Aggiornare le visualizzazioni

Aprire il *app/views/tasks/_form.html.erb* file, ovvero il modulo di modifica.

Trovare la riga `<%=f.error_span(:Description) %>` e inserire il codice direttamente sotto il seguente:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Aprire il *app/views/tasks/show.html.erb* file, ovvero la pagina di visualizzazione di record singolo. 

Trovare la riga `<dd><%= @task.Description %></dd>` e inserire il codice direttamente sotto il seguente:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Aprire il *app/views/tasks/index.html.erb* file, ovvero la pagina di indice per tutti i record.

Trovare la riga `<th><%= model_class.human_attribute_name(:Description) %></th>` e inserire il codice direttamente sotto il seguente:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

Nello stesso file, individuare la riga `<td><%= task.Description %></td>` e inserire il codice direttamente sotto il seguente:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>Testare le modifiche in locale

Nei Servizi terminal locale, eseguire il server Guide.

```bash
rails server
```

Per visualizzare lo stato dell'attività di modifica, passare a `http://localhost:3000` e aggiungere o modificare voci.

![Casella di controllo aggiuntiva all'attività](./media/tutorial-ruby-mysql-app/complete-checkbox.png)

Per arrestare il server di guide, digitare `Ctrl + C` nei Servizi terminal.

### <a name="publish-changes-to-azure"></a>Pubblicare le modifiche in Azure

I servizi terminal, eseguire le migrazioni di database Guide per l'ambiente di produzione apportare le modifiche nel database di Azure.

```bash
rake db:migrate RAILS_ENV=production
```

Eseguire il commit di tutte le modifiche in Git e quindi effettuare il push delle modifiche al codice in Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Dopo aver completato `git push`, passare all'app Web di Azure e testare la nuova funzionalità.

![Modifiche al modello e al database pubblicate in Azure](media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

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
> * Connettere un Ruby sull'app Guide per MySQL
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log di diagnostica in Azure
> * Gestire l'app nel portale di Azure

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato a un'app Web.

> [!div class="nextstepaction"]
> [Eseguire il mapping di un nome DNS personalizzato esistente ad app Web di Azure](../app-service-web-tutorial-custom-domain.md)

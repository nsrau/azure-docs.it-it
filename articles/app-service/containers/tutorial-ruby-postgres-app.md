---
title: Creare un'app Web Ruby e Postgres nel servizio app di Azure in Linux | Microsoft Docs
description: Informazioni su come ottenere un'app Ruby da usare in Azure con connessione a un database PostgreSQL in Azure.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: ruby
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 927c8f6d0fefbc592999487217c41aeecc96b0d9
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37950982"
---
# <a name="build-a-ruby-and-postgres-web-app-in-azure-app-service-on-linux"></a>Creare un'app Web Ruby e Postgres nel servizio app di Azure in Linux

Il [Servizio app in Linux](app-service-linux-intro.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione basato sul sistema operativo Linux. Questa esercitazione illustra come creare un'app Web Ruby e connetterla a un database PostgreSQL. Al termine, si avrà un'app [Ruby on Rails](http://rubyonrails.org/) in esecuzione nel servizio app in Linux.

![App Ruby on Rails in esecuzione nel servizio app di Azure](./media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un database PostgreSQL in Azure
> * Connettere un'app Ruby on Rails a PostgreSQL
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log di diagnostica in Azure
> * Gestire l'app nel portale di Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione:

* [Installare Git](https://git-scm.com/)
* [Installare Ruby 2.3](https://www.ruby-lang.org/en/documentation/installation/)
* [Installare Ruby on Rails 5.1](http://guides.rubyonrails.org/v5.1/getting_started.html)
* [Installare ed eseguire PostgreSQL](https://www.postgresql.org/download/)

## <a name="prepare-local-postgres"></a>Preparare PostgreSQL in locale

In questo passaggio, si crea un database nel server locale di Postgres da usare in questa esercitazione.

### <a name="connect-to-local-postgres-server"></a>Connettersi al server Postgres locale

Aprire la finestra terminale ed eseguire `psql` per connettersi al server Postgres locale.

```bash
sudo -u postgres psql
```

Se la connessione ha esito positivo, il database Postgres è in esecuzione. In caso contrario, assicurarsi che il database Postgres locale venga avviato seguendo la procedura descritta in [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/) (Download - Distribuzione di base di PostgreSQL).

Digitare `\q` per uscire dal client Postgres. 

Creare un utente Postgres abilitato per la creazione di database eseguendo il comando seguente, usando il nome utente Linux connesso.

```bash
sudo -u postgres createuser -d <signed_in_user>
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Creare un'app Ruby on Rails in locale
In questo passaggio si ottiene un'applicazione Ruby on Rails di esempio, se ne configura la connessione al database e si esegue l'applicazione in locale. 

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

### <a name="run-the-sample-locally"></a>Eseguire l'esempio in locale

Eseguire le [migrazioni di Rails](http://guides.rubyonrails.org/active_record_migrations.html#running-migrations) per creare le tabelle necessarie all'applicazione. Per verificare le tabelle create nelle migrazioni, esaminare la directory _db/migrate_ nel repository Git.

```bash
rake db:create
rake db:migrate
```

Eseguire l'applicazione.

```bash
rails server
```

Andare a `http://localhost:3000` in un browser. Nella pagina aggiungere alcune attività.

![Connessione di Ruby on Rails a Postgres riuscita](./media/tutorial-ruby-postgres-app/postgres-connect-success.png)

Per arrestare il server Rails, digitare `Ctrl + C` nel terminale.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-postgres-in-azure"></a>Creare Postgres in Azure

In questo passaggio viene creato un database Postgres in [Database di Azure per PostgreSQL](/azure/postgresql/). Successivamente si configurerà l'applicazione Ruby on Rails per la connessione a questo database.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-postgres-server"></a>Creare un server Postgres

Creare un server PostgreSQL con il comando [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create).

Eseguire il comando seguente in Cloud Shell e sostituire con il segnaposto *\<postgres_server_name>* con un nome di server univoco. Il nome del server deve essere univoco in tutti i server di Azure. 

```azurecli-interactive
az postgres server create --location "West Europe" --resource-group myResourceGroup --name <postgres_server_name> --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd! --sku-name GP_Gen4_2
```

Quando il database di Azure termina la creazione del server PostgreSQL, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente:

```json
{
  "administratorLogin": "adminuser",
  "earliestRestoreDate": "2018-06-15T12:38:25.280000+00:00",
  "fullyQualifiedDomainName": "<postgres_server_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgres_server_name>",
  "location": "westeurope",
  "name": "<postgres_server_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Configurare il firewall del server

In Cloud Shell creare una regola del firewall per il server Postgres per consentire le connessioni client usando il comando [`az postgres server firewall-rule create`](/cli/azure/postgres/server/firewall-rule?view=azure-cli-latest#az_postgres_server_firewall_rule_create). Quando sia l'IP iniziale che l'IP finale sono impostati su 0.0.0.0, il firewall viene aperto solo per le altre risorse di Azure. Sostituire il segnaposto *\<postgres_server_name>* con un nome di server univoco.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server <postgres_server_name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!TIP] 
> È possibile rendere ancora più restrittiva la regola del firewall [usando solo gli indirizzi IP in uscita usati dall'app](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="connect-to-production-postgres-server-locally"></a>Connettersi al server Postgres di produzione in locale

In Cloud Shell connettersi al server Postgres in Azure. Usare il valore specificato in precedenza per i segnaposto _&lt;postgres_server_name>_.

```bash
psql -U adminuser@<postgres_server_name> -h <postgres_server_name>.postgres.database.azure.com postgres
```

Quando viene richiesta una password, usare _My5up3r$tr0ngPa$w0rd!_, ossia la password specificata al momento della creazione del server di database.

### <a name="create-a-production-database"></a>Creare un database di produzione

Al prompt `postgres` creare un database.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Creare un utente con autorizzazioni

Creare un utente di database denominato _railsappuser_ e assegnare all'utente tutti i privilegi per il database `sampledb`.

```sql
CREATE USER railsappuser WITH PASSWORD 'MyPostgresAzure2017'; 
GRANT ALL PRIVILEGES ON DATABASE sampledb TO railsappuser;
```

Chiudere la connessione al server digitando `\q`.

## <a name="connect-app-to-azure-postgres"></a>Connettere l'app a Postgres in Azure

In questo passaggio si connette l'applicazione Ruby on Rails al database Postgres creato in Database di Azure per PostgreSQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Configurare la connessione al database

Nel repository aprire _config/database.yml_. Alla fine del file sostituire le variabili dell'ambiente di produzione con il codice seguente. 

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
```

Salvare le modifiche.

### <a name="test-the-application-locally"></a>Testare l'applicazione in locale

Tornare nel terminale locale e impostare le variabili di ambiente seguenti:

```bash
export DB_HOST=<postgres_server_name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<postgres_server_name>
export DB_PASSWORD=MyPostgresAzure2017
```

Eseguire le migrazioni del database Rails con i valori dell'ambiente di produzione appena configurati per creare le tabelle del database Postgres in Database di Azure per PostgreSQL. 

```bash
rake db:migrate RAILS_ENV=production
```

Quando viene eseguita nell'ambiente di produzione, l'applicazione Rails necessita di asset precompilati. Generare gli asset necessari con il comando seguente:

```bash
rake assets:precompile
```

L'ambiente di produzione di Rails usa anche segreti per gestire la sicurezza. Generare una chiave privata.

```bash
rails secret
```

Salvare la chiave privata nelle rispettive variabili usate dall'ambiente di produzione di Rails. Per praticità, usare la stessa chiave per entrambe le variabili.

```bash
export RAILS_MASTER_KEY=<output_of_rails_secret>
export SECRET_KEY_BASE=<output_of_rails_secret>
```

Abilitare la gestione di file JavaScript e CSS nell'ambiente di produzione di Rails.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Eseguire l'applicazione di esempio nell'ambiente di produzione.

```bash
rails server -e production
```

Accedere a `http://localhost:3000`. Se la pagina viene caricata senza errori, l'applicazione Ruby on Rails si connette al database Postgres in Azure.

Nella pagina aggiungere alcune attività.

![Connessione di Ruby on Rails a Database di Azure per PostgreSQL riuscita](./media/tutorial-ruby-postgres-app/azure-postgres-connect-success.png)

Per arrestare il server Rails, digitare `Ctrl + C` nel terminale.

### <a name="commit-your-changes"></a>Eseguire il commit delle modifiche

Eseguire i comandi Git seguenti per eseguire il commit delle modifiche:

```bash
git add .
git commit -m "database.yml updates"
```

L'app è pronta per la distribuzione.

## <a name="deploy-to-azure"></a>Distribuisci in Azure

In questo passaggio si distribuisce l'applicazione Ruby on Rails connessa a Postgres nel servizio app di Azure.

### <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Creare un'app Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Configurare le impostazioni del database

Nel servizio app, le variabili di ambiente vengono configurate come _impostazioni dell'app_ usando il comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) in Cloud Shell.

Il comando di Cloud Shell seguente configura le impostazioni dell'app `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` e `DB_PASSWORD`. Sostituire i segnaposto _&lt;appname>_ e _&lt;postgres_server_name>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<postgres_server_name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<postgres_server_name>" DB_PASSWORD="MyPostgresAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Configurare le variabili di ambiente di Rails

Nel terminale locale generare una nuova chiave privata per l'ambiente di produzione di Rails in Azure.

```bash
rails secret
```

Configurare le variabili necessarie per l'ambiente di produzione di Rails.

Nel comando di Cloud Shell seguente sostituire i due segnaposto _&lt;output_of_rails_secret>_ con la nuova chiave privata generata nel terminale locale.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output_of_rails_secret>" SECRET_KEY_BASE="<output_of_rails_secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"` indica al contenitore Ruby predefinito di precompilare gli asset a ogni distribuzione Git.

### <a name="push-to-azure-from-git"></a>Effettuare il push in Azure da Git

Nel terminale locale aggiungere un'istanza remota di Azure al repository Git locale.

```bash
git remote add azure <paste_copied_url_here>
```

Eseguire il push all'istanza remota di Azure per distribuire l'applicazione Ruby on Rails. Verrà richiesta la password specificata in precedenza durante la creazione dell'utente di distribuzione.

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

![App Ruby on Rails in esecuzione nel servizio app di Azure](./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png)

L'app Ruby on Rails basata sui dati è ora in esecuzione nel servizio app di Azure.

## <a name="update-model-locally-and-redeploy"></a>Aggiornare il modello in locale e rieseguire la distribuzione

In questo passaggio viene apportata una modifica semplice al modello di dati `task` e all'app Web e quindi viene pubblicato l'aggiornamento in Azure.

Per lo scenario delle attività, l'applicazione viene modificata per poter contrassegnare un'attività come completata.

### <a name="add-a-column"></a>Aggiungere una colonna

Nel terminale passare alla radice del repository Git.

Generare una nuova migrazione che aggiunge una colonna booleana denominata `Done` alla tabella `Tasks`:

```bash
rails generate migration AddDoneToTasks Done:boolean
```

Questo comando genera nuovo file di migrazione nella directory _db/migrate_.


Nel terminale eseguire le migrazioni del database Rails per apportare la modifica nel database locale.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Aggiornare la logica dell'applicazione

Aprire il file *app/controllers/tasks_controller.rb*. Alla fine del file trovare la riga seguente:

```rb
params.require(:task).permit(:Description)
```

Modificare questa riga per includere il nuovo parametro `Done`.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>Aggiornare le visualizzazioni

Aprire il file *app/views/tasks/_form.html.erb*, che è il modulo di modifica.

Trovare la riga `<%=f.error_span(:Description) %>` e inserire il codice seguente direttamente sotto di essa:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Aprire il file *app/views/tasks/show.html.erb*, che è la pagina della visualizzazione a record singolo. 

Trovare la riga `<dd><%= @task.Description %></dd>` e inserire il codice seguente direttamente sotto di essa:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Aprire il file *app/views/tasks/index.html.erb*, che è la pagina di indice per tutti i record.

Trovare la riga `<th><%= model_class.human_attribute_name(:Description) %></th>` e inserire il codice seguente direttamente sotto di essa:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

Nello stesso file trovare la riga `<td><%= task.Description %></td>` e inserire il codice seguente direttamente sotto di essa:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>Testare le modifiche in locale

Nel terminale locale eseguire il server Rails.

```bash
rails server
```

Per visualizzare la modifica dello stato delle attività, passare a `http://localhost:3000` e aggiungere o modificare elementi.

![Casella di controllo aggiuntiva all'attività](./media/tutorial-ruby-postgres-app/complete-checkbox.png)

Per arrestare il server Rails, digitare `Ctrl + C` nel terminale.

### <a name="publish-changes-to-azure"></a>Pubblicare le modifiche in Azure

Nel terminale eseguire le migrazioni del database Rails per l'ambiente di produzione per apportare la modifica nel database di Azure.

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

![Modifiche al modello e al database pubblicate in Azure](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

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
> * Creare un database Postgres in Azure
> * Connettere un'app Ruby on Rails a Postgres
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log di diagnostica in Azure
> * Gestire l'app nel portale di Azure

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato a un'app Web.

> [!div class="nextstepaction"]
> [Eseguire il mapping di un nome DNS personalizzato esistente ad app Web di Azure](../app-service-web-tutorial-custom-domain.md)

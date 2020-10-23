---
title: 'Esercitazione: App Ruby Linux con Postgres'
description: Informazioni su come usare un'app Ruby Linux in Servizio app di Azure, con connessione a un database PostgreSQL in Azure. Rails verrà usato nell'esercitazione.
ms.devlang: ruby
ms.topic: tutorial
ms.date: 06/18/2020
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: c2baccec75c7b525c0837cebd9d828dff3a79543
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150173"
---
# <a name="build-a-ruby-and-postgres-app-in-azure-app-service-on-linux"></a>Compilare un'app Ruby e Postgres nel servizio app di Azure in Linux

[Servizio app di Azure](overview.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione. Questa esercitazione illustra come creare un'app Ruby e connetterla a un database PostgreSQL. Al termine, si avrà un'app [Ruby on Rails](https://rubyonrails.org/) in esecuzione nel servizio app in Linux.

:::image type="content" source="./media/tutorial-ruby-postgres-app/complete-checkbox-published.png" alt-text="Screenshot di un esempio di app Ruby on Rails denominato Attività.":::

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un database PostgreSQL in Azure
> * Connettere un'app Ruby on Rails a PostgreSQL
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log di diagnostica in Azure
> * Gestire l'app nel portale di Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

* [Installare Git](https://git-scm.com/)
* [Installare Ruby 2.6](https://www.ruby-lang.org/en/documentation/installation/)
* [Installare Ruby on Rails 5.1](https://guides.rubyonrails.org/v5.1/getting_started.html)
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
sudo -u postgres createuser -d <signed-in-user>
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

Eseguire le [migrazioni di Rails](https://guides.rubyonrails.org/active_record_migrations.html#running-migrations) per creare le tabelle necessarie all'applicazione. Per verificare le tabelle create nelle migrazioni, esaminare la directory _db/migrate_ nel repository Git.

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

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-postgres-in-azure"></a>Creare Postgres in Azure

In questo passaggio viene creato un database Postgres in [Database di Azure per PostgreSQL](../postgresql/index.yml). Successivamente si configurerà l'applicazione Ruby on Rails per la connessione a questo database.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

## <a name="create-postgres-database-in-azure"></a>Creare un database Postgres in Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

In questa sezione verranno creati un database e un server di Database di Azure per PostgreSQL. Per iniziare, installare l'estensione `db-up` con il comando seguente:

```azurecli
az extension add --name db-up
```

Creare il database Postgres in Azure con il comando [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up), come illustrato nell'esempio seguente. Sostituire *\<postgresql-name>* con un nome *univoco* (l'endpoint server è *https://\<postgresql-name>.postgres.database.azure.com*). Per *\<admin-username>* e *\<admin-password>* specificare le credenziali per creare un utente amministratore per questo server Postgres.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westeurope --server-name <postgresql-name> --database-name sampledb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Questo comando può richiedere alcuni minuti perché esegue le operazioni seguenti:

- Crea un [gruppo di risorse](../azure-resource-manager/management/overview.md#terminology) denominato `myResourceGroup` se non esiste. Ogni risorsa di Azure deve trovarsi in uno di questi gruppi. `--resource-group` è facoltativo.
- Crea un server Postgres con l'utente amministratore.
- Crea un database `sampledb`.
- Consente l'accesso dall'indirizzo IP locale.
- Consente l'accesso dai servizi di Azure.
- Crea un utente del database con accesso al database `sampledb`.

È possibile eseguire tutti i passaggi separatamente con altri comandi `az postgres` e `psql`, ma `az postgres up` li esegue tutti in un unico passaggio.

Al termine del comando, trovare le righe di output che iniziano con `Ran Database Query:`. Mostrano l'utente del database creato con il nome utente `root` e la password `Sampledb1`. Verranno usati in un secondo momento per connettere l'app al database.

<!-- not all locations support az postgres up -->
> [!TIP]
> `--location <location-name>`, può essere impostato su una delle [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/). È possibile ottenere le aree disponibili per la sottoscrizione con il comando [`az account list-locations`](/cli/azure/account#az-account-list-locations). Per le app di produzione, inserire il database e l'app nella stessa posizione.

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
export DB_HOST=<postgres-server-name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=root@<postgres-server-name>
export DB_PASSWORD=Sampledb1
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
export RAILS_MASTER_KEY=<output-of-rails-secret>
export SECRET_KEY_BASE=<output-of-rails-secret>
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

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Creare un'app Web

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Configurare le impostazioni del database

Nel servizio app, le variabili di ambiente vengono configurate come _impostazioni dell'app_ usando il comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest&preserve-view=true#az-webapp-config-appsettings-set) in Cloud Shell.

Il comando di Cloud Shell seguente configura le impostazioni dell'app `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` e `DB_PASSWORD`. Sostituire i segnaposto _&lt;appname>_ e _&lt;postgres-server-name>_ .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<postgres-server-name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="root@<postgres-server-name>" DB_PASSWORD="Sampledb1"
```

### <a name="configure-rails-environment-variables"></a>Configurare le variabili di ambiente di Rails

Nel terminale locale [generare un nuovo segreto](configure-language-ruby.md#set-secret_key_base-manually) per l'ambiente di produzione di Rails in Azure.

```bash
rails secret
```

Configurare le variabili necessarie per l'ambiente di produzione di Rails.

Nel comando di Cloud Shell seguente sostituire i due segnaposto _&lt;output-of-rails-secret>_ con la nuova chiave privata generata nel terminale locale.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output-of-rails-secret>" SECRET_KEY_BASE="<output-of-rails-secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"` indica al contenitore Ruby predefinito di precompilare gli asset a ogni distribuzione Git. Per altre informazioni, vedere [Precompilare gli asset](configure-language-ruby.md#precompile-assets) e [Gestire asset statici](configure-language-ruby.md#serve-static-assets).

### <a name="push-to-azure-from-git"></a>Effettuare il push in Azure da Git

Nel terminale locale aggiungere un'istanza remota di Azure al repository Git locale.

```bash
git remote add azure <paste-copied-url-here>
```

Eseguire il push all'istanza remota di Azure per distribuire l'applicazione Ruby on Rails. Verrà richiesta la password specificata in precedenza durante la creazione dell'utente di distribuzione.

```bash
git push azure master
```

Durante la distribuzione, Servizio app di Azure comunica lo stato con Git.

<pre>
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
&lt; Output has been truncated for readability &gt;
</pre>

### <a name="browse-to-the-azure-app"></a>Passare all'app Azure

Passare a `http://<app-name>.azurewebsites.net` e aggiungere alcune attività all'elenco.

:::image type="content" source="./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png" alt-text="Screenshot di un esempio di app Ruby on Rails denominato Attività.":::

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

Dopo aver completato `git push`, passare all'app Azure e testare la nuova funzionalità.

![Modifiche al modello e al database pubblicate in Azure](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Le attività aggiunte vengono mantenute nel database. Gli aggiornamenti allo schema di dati non modificano i dati esistenti.

## <a name="stream-diagnostic-logs"></a>Eseguire lo streaming dei log di diagnostica

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-the-azure-app"></a>Gestire l'app Azure

Accedere al [portale di Azure](https://portal.azure.com) per gestire l'app creata.

Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Azure.

![Passaggio all'app di Azure nel portale](./media/tutorial-php-mysql-app/access-portal.png)

Verrà visualizzata la pagina Panoramica dell'app. Nella pagina è possibile eseguire attività di gestione di base come l'arresto, l'avvio, il riavvio e l'eliminazione.

Il menu a sinistra consente di visualizzare le pagine di configurazione dell'app.

![Pagina del servizio app nel portale di Azure](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare un database Postgres in Azure
> * Connettere un'app Ruby on Rails a Postgres
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log di diagnostica in Azure
> * Gestire l'app nel portale di Azure

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato all'app.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il mapping di un nome DNS personalizzato all'app Web](app-service-web-tutorial-custom-domain.md)

In alternativa, consultare altre risorse:

> [!div class="nextstepaction"]
> [Configurare l'app Ruby](configure-language-ruby.md)
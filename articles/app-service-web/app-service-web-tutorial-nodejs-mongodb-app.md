---
title: Creare un'app Web Node.js e MongoDB in Azure | Microsoft Docs
description: Informazioni su come usare un'app Node.js in Azure, con connessione a un database Cosmos DB tramite una stringa di connessione MongoDB.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/04/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: f7479260c7c2e10f242b6d8e77170d4abe8634ac
ms.openlocfilehash: 14e399c06b21d9cabab28bdb195b02eb422f2d00
ms.contentlocale: it-it
ms.lasthandoff: 06/21/2017

---
# <a name="build-a-nodejs-and-mongodb-web-app-in-azure"></a>Creare un'app Web Node.js e MongoDB in Azure

Le app Web di Azure offrono un servizio di hosting Web ad alta scalabilità e con funzioni di auto-correzione. Questa esercitazione illustra come creare un'app Web Node.js in Azure e connetterla a un database MongoDB. Al termine, si avrà un'applicazione MEAN (MongoDB, Express, AngularJS e Node.js) in esecuzione nel [servizio app di Azure](app-service-web-overview.md). Per semplicità, l'applicazione di esempio usa il [framework Web MEAN.js](http://meanjs.org/).

![App MEAN.js in esecuzione nel Servizio app di Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Creare un database MongoDB in Azure
> * Connettere un'app Node.js a MongoDB
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log di diagnostica in Azure
> * Gestire l'app nel portale di Azure

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

1. [Installare Git](https://git-scm.com/)
1. [Installare Node.js e NPM](https://nodejs.org/)
1. [Installare Gulp.js](http://gulpjs.com/), richiesto da [MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started)
1. [Installare ed eseguire MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/) 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="test-local-mongodb"></a>Testare il MongoDB locale

Aprire la finestra del terminale e usare il comando `cd` per passare alla directory `bin` dell'installazione di MongoDB. È possibile usare questa finestra del terminale per eseguire tutti i comandi presenti in questa esercitazione.

Eseguire `mongo` nel terminale per connettersi al server MongoDB locale.

```bash
mongo
```

Se la connessione ha esito positivo significa che il database MongoDB è già in esecuzione. In caso contrario, assicurarsi che il database MongoDB locale venga avviato seguendo la procedura descritta in [Install MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/) (Installare MongoDB Community Edition). Spesso, anche se MongoDB è stato installato, è necessario avviare il servizio eseguendo `mongod`. 

Dopo avere testato il database MongoDB, digitare `Ctrl+C` nel terminale. 

## <a name="create-local-nodejs-app"></a>Creare l'app Node.js locale

In questo passaggio si imposta il progetto Node.js locale.

### <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Nella finestra del terminale usare il comando `cd` per passare a una directory di lavoro.  

Eseguire il comando seguente per clonare l'archivio di esempio. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Questo repository contiene una copia del [repository MEAN.js](https://github.com/meanjs/mean), che è stato modificato per poter essere eseguito nel servizio app. Per altre informazioni, vedere il [file LEGGIMI](https://github.com/Azure-Samples/meanjs/blob/master/README.md) del repository MEAN.js.

### <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire i comandi seguenti per installare i pacchetti necessari e avviare l'applicazione.

```bash
cd meanjs
npm install
npm start
```

Al termine del caricamento dell'app viene visualizzato un messaggio simile al seguente:

```
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

Passare a http://localhost:3000 in un browser. Fare clic su **Iscriviti** nel menu in alto e creare un utente test. 

L'applicazione di esempio MEAN.js archivia i dati utente nel database. Dopo aver creato un utente e aver eseguito l'accesso, l'app scriverà i dati nel database MongoDB locale.

![MEAN.js si connette correttamente a MongoDB](./media/app-service-web-tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Selezionare **Admin > Manage Articles** (Amministrazione > Gestione articoli) per aggiungere articoli.

Per arrestare Node.js in qualsiasi momento, premere `Ctrl+C` nel terminale. 

## <a name="create-production-mongodb"></a>Creare MongoDB di produzione

In questo passaggio si crea un database MongoDB in Azure. Quando viene distribuita in Azure, l'app usa questo database basato sul cloud.

Per MongoDB, questa esercitazione usa [Azure Cosmos DB](/azure/documentdb/). COSMOS DB supporta le connessioni client MongoDB.

### <a name="log-in-to-azure"></a>Accedere ad Azure

Usare l'interfaccia della riga di comando di Azure 2.0 per creare le risorse necessarie per ospitare l'app in Azure. Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#login) e seguire le istruzioni visualizzate.

```azurecli-interactive
az login
```   

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create).

[!INCLUDE [Resource group intro](../../includes/resource-group.md)]

L'esempio seguente crea un gruppo di risorse nell'area Europa occidentale.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Usare il comando dell'interfaccia della riga di comando di Azure [az appservice list-locations](/cli/azure/appservice#list-locations) per visualizzare un elenco dei percorsi disponibili. 

### <a name="create-a-cosmos-db-account"></a>Creare un account Cosmos DB

Creare un account Cosmos DB con il comando [az cosmosdb create](/cli/azure/cosmosdb#create).

Nel comando seguente sostituire il segnaposto *\<cosmosdb_name>* con un nome Cosmos DB univoco. Poiché questo nome è incluso nell'endpoint Cosmos DB, `https://<cosmosdb_name>.documents.azure.com/`, è necessario che sia univoco in tutti gli account Cosmos DB in Azure. Il nome deve contenere solo lettere minuscole, numeri e il carattere (-) e deve avere una lunghezza compresa tra 3 e 50 caratteri.

```azurecli-interactive
az cosmosdb create \
    --name <cosmosdb_name> \
    --resource-group myResourceGroup \
    --kind MongoDB
```

Il parametro *--kind MongoDB* abilita le connessioni client MongoDB.

Dopo la creazione dell'account Cosmos DB, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente:

```json
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  < Output truncated for readability >
}
```

## <a name="connect-app-to-production-mongodb"></a>Connettere l'app a MongoDB di produzione

In questo passaggio si usa una stringa di connessione MongoDB per connettere l'applicazione di esempio MEAN.js al database Cosmos DB appena creato. 

### <a name="retrieve-the-database-key"></a>Recuperare la chiave del database

Per connettersi al database Cosmos DB, è necessario disporre della chiave database. Usare il comando [az cosmosdb list-keys](/cli/azure/cosmosdb#list-keys) per recuperare la chiave primaria.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb_name> --resource-group myResourceGroup
```

L'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente:

```json
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
```

Copiare il valore di `primaryMasterKey`. Queste informazioni saranno necessarie durante il passaggio successivo.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configurare la stringa di connessione nell'applicazione Node.js

Nell'archivio MEAN.js aprire _config/env/production.js_.

Nell'oggetto `db` aggiornare il valore di `uri`:

* Sostituire i due segnaposto *\<cosmosdb_name>* con il nome del database Cosmos DB.
* Sostituire il segnaposto *\<primary_master_key>* con la chiave copiata nel passaggio precedente.

Il codice seguente mostra l'oggetto `db`:

```javascript
db: {
  uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false',
  ...
},
```

L'opzione `ssl=true` è obbligatoria poiché [Cosmos DB richiede l'SSL](../cosmos-db/connect-mongodb-account.md#connection-string-requirements). 

Salvare le modifiche.

### <a name="test-the-application-in-production-mode"></a>Testare l'applicazione nella modalità di produzione 

Eseguire il comando seguente per minimizzare e aggregare gli script per l'ambiente di produzione. Questo processo genera i file necessari per l'ambiente di produzione.

```bash
gulp prod
```

Eseguire il comando seguente per usare la stringa di connessione configurata in _config/env/production.js_.

```bash
NODE_ENV=production node server.js
```

`NODE_ENV=production` imposta la variabile di ambiente che richiede a Node.js l'esecuzione nell'ambiente di produzione.  `node server.js` avvia il server Node.js con `server.js` nella radice del repository. Questo è il modo in cui l'applicazione Node.js viene caricata in Azure. 

Dopo aver caricato l'app, verificare che sia in esecuzione nell'ambiente di produzione:

```
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

Passare a http://localhost:8443 in un browser. Fare clic su **Iscriviti** nel menu in alto e creare un utente test. Dopo aver creato un utente e aver eseguito l'accesso, l'app scriverà i dati nel database Cosmos DB in Azure. 

Nel terminale arrestare Node.js digitando `Ctrl+C`. 

## <a name="deploy-app-to-azure"></a>Distribuire l'app in Azure

In questo passaggio si distribuisce l'applicazione Node.js connessa a MongoDB nel Servizio app di Azure.

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

Creare un piano di servizio app con il comando [az appservice plan create](/cli/azure/appservice/plan#create). 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

L'esempio seguente crea un piano di servizio app denominato _myAppServicePlan_ usando il piano tariffario **GRATUITO**:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Al termine della creazione del piano di servizio app, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente:

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
```

### <a name="create-a-web-app"></a>Creare un'app Web

Creare un'app Web nel piano di servizio app `myAppServicePlan` con il comando [az webapp create](/cli/azure/webapp#create). 

L'app Web fornisce uno spazio host per distribuire il codice e un URL in cui visualizzare l'applicazione distribuita. Usarlo per creare l'app Web. 

Nel comando seguente sostituire il segnaposto *\<app_name>* con un nome di app univoco. Poiché questo nome è incluso nell'URL predefinito dell'app Web, è necessario che sia univoco in tutte le app del servizio app di Azure. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
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

### <a name="configure-an-environment-variable"></a>Configurare una variabile di ambiente

In una fase precedente dell'esercitazione è stata impostata come hardcoded la stringa di connessione al database in _config/env/production.js_. In linea con la procedura consigliata per la sicurezza, si intende mantenere i dati sensibili all'esterno del repository Git. Per l'app in esecuzione in Azure, si userà invece una variabile di ambiente.

Nel servizio app le variabili di ambiente vengono impostate come _impostazioni dell'app_ usando il comando [az webapp config appsettings update](/cli/azure/webapp/config/appsettings#update). 

L'esempio seguente configura l'impostazione dell'app `MONGODB_URI` nell'app Web di Azure. Sostituire i segnaposto *\<app_name>*, *\<cosmosdb_name>* e *\<primary_master_key>*.

```azurecli-interactive
az webapp config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

Nel codice Node.js si accede all'impostazione dell'app con `process.env.MONGODB_URI`, esattamente come si accede a una variabile di ambiente qualsiasi. 

Annullare a questo punto le modifiche a _config/env/production.js_ usando il comando seguente:

```bash
git checkout -- .
```

Aprire di nuovo _config/env/production.js_. Si noti che l'app MEAN.js predefinita è già configurata per l'uso della variabile di ambiente `MONGODB_URI` creata.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="configure-local-git-deployment"></a>Configurare la distribuzione con l'istanza Git locale 

Usare il comando [az webapp deployment user set](/cli/azure/webapp/deployment/user#set) per creare le credenziali per la distribuzione.

È possibile distribuire l'applicazione nel Servizio app di Azure attraverso varie soluzioni, tra cui FTP, istanza Git locale, GitHub, Visual Studio Team Services e BitBucket. Per la distribuzione con FTP e l'istanza Git locale, è necessario che nel server sia configurato un utente della distribuzione per l'autenticazione. L'utente di distribuzione è a livello di account ed è diverso dall'account della sottoscrizione di Azure. L'utente della distribuzione deve essere configurato una sola volta.

Nel comando seguente sostituire *\<user-name>* e *\<password>* con un nuovo nome utente e una nuova password. Il nome utente deve essere univoco. La password deve essere composta da almeno otto caratteri, con due dei tre elementi seguenti: lettere, numeri e simboli. Se viene visualizzato un errore ` 'Conflict'. Details: 409`, modificare il nome utente. Se viene visualizzato un errore ` 'Bad Request'. Details: 400`, usare una password più complessa.

```azurecli-interactive
az appservice web deployment user set --user-name <username> --password <password>
```

Registrare nome utente e password da usare nei passaggi successivi durante la distribuzione dell'app.

Usare il comando [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#config-local-git) per configurare l'accesso Git locale all'app Web di Azure. 

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup
```

Dopo che l'utente della distribuzione è stato configurato, l'interfaccia della riga di comando di Azure visualizza l'URL della distribuzione per l'app Web di Azure nel formato seguente:

```bash 
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git 
``` 

Copiare l'output dal terminale che verrà usato nel passaggio successivo. 

### <a name="push-to-azure-from-git"></a>Effettuare il push in Azure da Git

Aggiungere un'istanza remota di Azure al repository Git locale. 

```bash
git remote add azure <paste_copied_url_here> 
```

Effettuare il push all'istanza remota di Azure per distribuire l'applicazione Node.js. Verrà richiesta la password specificata in precedenza nel corso della creazione dell'utente della distribuzione. 

```bash
git push azure master
```

Durante la distribuzione, Servizio app di Azure comunica lo stato con Git.

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

Il processo di distribuzione esegue [Gulp](http://gulpjs.com/) dopo `npm install`. Il servizio app non esegue attività Gulp o Grunt durante la distribuzione, pertanto questo repository di esempio include due file aggiuntivi nella directory radice per abilitarlo: 

- _.deployment_: questo file indica al servizio app di eseguire `bash deploy.sh` come script di distribuzione personalizzato.
- _deploy.sh_: script di distribuzione personalizzato. Se si esamina il file, si noterà che esegue `gulp prod` dopo `npm install` e `bower install`. 

È possibile usare questo approccio per aggiungere uno o più passaggi alla distribuzione basata su Git. Se si riavvia l'app Web di Azure in qualsiasi momento, il servizio app non esegue di nuovo queste attività di automazione.

### <a name="browse-to-the-azure-web-app"></a>Passare all'app Web di Azure 

Passare all'app Web distribuita usando il Web browser. 

```bash 
http://<app_name>.azurewebsites.net 
``` 

Fare clic su **Registrati** nel menu in alto e creare un utente fittizio. 

Se l'operazione ha esito positivo e l'app accede automaticamente all'utente creato, l'app MEAN.js in Azure sarà connessa al database MongoDB, ovvero Cosmos DB. 

![App MEAN.js in esecuzione nel Servizio app di Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Selezionare **Admin > Manage Articles** (Amministrazione > Gestione articoli) per aggiungere articoli. 

**Congratulazioni.** L'app Node.js basata su dati è in esecuzione nel Servizio app di Azure.

## <a name="update-data-model-and-redeploy"></a>Aggiornare il modello di dati e ridistribuire

In questo passaggio viene modificato il modello di dati `article` e viene pubblicata la modifica in Azure.

### <a name="update-the-data-model"></a>Aggiornare il modello di dati

Aprire _modules/articles/server/models/article.server.model.js_.

In `ArticleSchema` aggiungere un tipo `String` denominato `comment`. Al termine, il codice dello schema dovrebbe avere un aspetto simile al seguente:

```javascript
var ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

### <a name="update-the-articles-code"></a>Aggiornare il codice di articoli

Aggiornare la parte rimanente del codice `articles` per usare `comment`.

È necessario modificare cinque file: il controller server e le quattro visualizzazioni client. 

Aprire _modules/articles/server/controllers/articles.server.controller.js_.

Nella funzione `update` aggiungere un'assegnazione per `article.comment`. Il codice seguente mostra la funzione `update` completata:

```javascript
exports.update = function (req, res) {
  var article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Aprire _modules/articles/client/views/view-article.client.view.html_.

Appena sopra il tag di chiusura `</section>` aggiungere la riga seguente per visualizzare `comment` insieme al resto dei dati dell'articolo:

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

Aprire _modules/articles/client/views/list-articles.client.view.html_.

Appena sopra il tag di chiusura `</a>` aggiungere la riga seguente per visualizzare `comment` insieme al resto dei dati dell'articolo:

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Aprire _modules/articles/client/views/admin/list-articles.client.view.html_.

Nell'elemento `<div class="list-group">` sopra il tag di chiusura `</a>` aggiungere la riga seguente per visualizzare `comment` con la parte rimanente dei dati dell'articolo:

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Aprire _modules/articles/client/views/admin/form-article.client.view.html_.

Trovare l'elemento `<div class="form-group">` che contiene un pulsante di invio simile al seguente:

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Sopra il tag aggiungere un altro elemento `<div class="form-group">` che consente agli utenti di modificare il campo `comment`. Il nuovo elemento dovrebbe avere un aspetto simile al seguente:

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Testare le modifiche in locale

Salvare tutte le modifiche.

Testare di nuovo le modifiche in modalità di produzione.

```bash
gulp prod
NODE_ENV=production node server.js
```

> [!NOTE]
> Tenere presente che _config/env/production.js_ è stato ripristinato e che la variabile di ambiente `MONGODB_URI` è impostata solo nell'app Web di Azure e non nel computer locale. Se si esamina il file di configurazione, si noterà che la configurazione di produzione usa per impostazione predefinita un database MongoDB locale. Ciò garantisce che non si modifichino i dati di produzione quando si testano modifiche al codice in locale.

Andare a `http://localhost:8443` in un browser e assicurarsi di avere eseguito l'accesso.

Selezionare **Admin > Manage Articles** (Admin > Gestione articoli), quindi aggiungere un articolo selezionando il pulsante **+**.

La nuova casella di testo `Comment` è ora visibile.

![Campo di commento aggiunto ad Articles (Articoli)](./media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field.png)

Nel terminale arrestare Node.js digitando `Ctrl+C`. 

### <a name="publish-changes-to-azure"></a>Pubblicare le modifiche in Azure

Eseguire il commit delle modifiche in Git e quindi effettuare il push delle modifiche al codice in Azure.

```bash
git commit -am "added article comment"
git push azure master
```

Dopo aver completato `git push`, passare all'app Web di Azure e provare la nuova funzionalità.

![Modifiche al modello e al database pubblicate in Azure](media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Eventuali articoli aggiunti in precedenza sono ancora visibili. I dati esistenti in Cosmos DB non vengono persi. Gli aggiornamenti allo schema di dati inoltre lasciano intatti i dati esistenti.

## <a name="stream-diagnostic-logs"></a>Eseguire lo streaming dei log di diagnostica 

Mentre l'applicazione Node.js è in esecuzione nel servizio app di Azure, è possibile fare in modo che i log di console siano inviati tramite pipe al terminale. Ciò consente di ottenere gli stessi messaggi di diagnostica per il debug degli errori dell'applicazione.

Per avviare lo streaming dei log, usare il comando [az webapp log tail](/cli/azure/webapp/log#tail).

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
``` 

Dopo avere avviato lo streaming del log, aggiornare l'app Web di Azure nel browser per ottenere traffico Web. I log di console vengono inviati tramite pipe al terminale.

Arrestare il flusso dei log in qualsiasi momento digitando `Ctrl+C`. 

## <a name="manage-your-azure-web-app"></a>Gestire l'app Web di Azure

Accedere al [portale di Azure](https://portal.azure.com) per visualizzare l'app Web creata.

Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Web di Azure.

![Passare all'app Web di Azure nel portale](./media/app-service-web-tutorial-nodejs-mongodb-app/access-portal.png)

Per impostazione predefinita, il portale visualizza la pagina **Panoramica** dell'app Web. che offre una visualizzazione dello stato dell'app. In questa pagina è anche possibile eseguire attività di gestione di base come esplorare, arrestare, avviare, riavviare ed eliminare. Le schede sul lato sinistro della pagina mostrano le diverse pagine di configurazione che è possibile aprire.

![Pagina del servizio app nel portale di Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Passaggi successivi

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Creare un database MongoDB in Azure
> * Connettere un'app Node.js a MongoDB
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log da Azure al terminale
> * Gestire l'app nel portale di Azure

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato all'app Web.

> [!div class="nextstepaction"] 
> [Eseguire il mapping di un nome DNS personalizzato esistente ad app Web di Azure](app-service-web-tutorial-custom-domain.md)


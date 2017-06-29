---
title: App per le API Node.js nel Servizio app di Azure | Microsoft Docs
description: Informazioni su come creare un'API RESTful Node.js e distribuirla in un'app per le API nel servizio app di Azure.
services: app-service\api
documentationcenter: node
author: bradygaster
manager: erikre
editor: 
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: get-started-article
ms.date: 06/13/2017
ms.author: rachelap
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 19d6d0e1df185746a8ea289db9ac18e5614c09c7
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017


---
# <a name="build-a-nodejs-restful-api-and-deploy-it-to-an-api-app-in-azure"></a>Compilare un'API RESTful Node.js e distribuirla a un'app per le API in Azure
[!INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Questa guida introduttiva illustra come creare un'API REST Node.js per il framework di [Express](http://expressjs.com/) usando una definizione [Swagger](http://swagger.io/) e distribuirla in Azure come un'[app per le API](app-service-api-apps-why-best-platform.md). Si creerà l'app usando gli strumenti da riga di comando, si configureranno le risorse con l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)e si distribuirà l'app tramite Git.  Al termine, si avrà un'API REST di esempio in esecuzione in Azure.

## <a name="prerequisites"></a>Prerequisiti

* [Git](https://git-scm.com/)
* [Node.js e NPM](https://nodejs.org/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="prepare-your-environment"></a>Preparare l'ambiente

1. In una finestra del terminale eseguire il comando seguente per clonare il codice di esempio nel computer locale.

    ```bash
    git clone https://github.com/Azure-Samples/app-service-api-node-contact-list
    ```

2. Passare alla directory contenente il codice di esempio.

    ```bash
    cd app-service-api-node-contact-list
    ```

3. Installare [Swaggerize](https://www.npmjs.com/package/swaggerize-express) nel computer locale. Swaggerize è uno strumento che genera codice Node.js per l'API REST da una definizione Swagger.

    ```bash
    npm install -g yo
    npm install -g generator-swaggerize
    ```

## <a name="generate-nodejs-code"></a>Generare codice Node.js 

In questa sezione dell'esercitazione viene modellato un flusso di lavoro di sviluppo di API che crea prima i metadati di Swagger e li usa per lo scaffolding (generazione automatica) del codice server per l'API. 

Modificare la directory per impostarla sulla cartella di *avvio* e quindi eseguire `yo swaggerize`. Swaggerize crea un progetto Node.js per l'API dalla definizione Swagger in *api.json*.

    ```bash
    cd start
    yo swaggerize --apiPath api.json --framework express
    ```

     When Swaggerize asks for a project name, use *ContactList*.
   
   ```bash
   Swaggerize Generator
   Tell us a bit about your application
   ? What would you like to call this project: ContactList
   ? Your name: Francis Totten
   ? Your github user name: fabfrank
   ? Your email: frank@fabrikam.net
   ```
   
## <a name="customize-the-project-code"></a>Personalizzare il codice del progetto

1. Copiare la cartella *lib* nella cartella *ContactList* creata da `yo swaggerize` e quindi modificare la directory in *ContactList*.

    ```bash
    cp -r lib/ ContactList/
    cd ContactList
    ```

2. Installare i moduli NPM `jsonpath` e `swaggerize-ui`. 

    ```bash
    npm install --save jsonpath swaggerize-ui
    ```

3. Sostituire il codice nel file *handlers/contacts.js* con il codice seguente: 
    ```javascript
    'use strict';

    var repository = require('../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.all())
        }
    };
    ```
    Questo codice usa i dati JSON archiviati nel file *lib/contacts.json* servito da *lib/contactRepository.js*. Il nuovo codice *contacts.js* restituisce tutti i contatti nel repository come un payload JSON. 

4. Sostituire il codice nel file **handlers/contacts/{id}.js** con il codice seguente:

    ```javascript
    'use strict';

    var repository = require('../../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.get(req.params['id']));
        }    
    };
    ```

    Questo codice consente di usare una variabile di percorso per restituire solo il contatto con un ID specifico.

5. Sostituire il codice nel file **server.js** con il codice seguente:

    ```javascript
    'use strict';

    var port = process.env.PORT || 8000; 

    var http = require('http');
    var express = require('express');
    var bodyParser = require('body-parser');
    var swaggerize = require('swaggerize-express');
    var swaggerUi = require('swaggerize-ui'); 
    var path = require('path');

    var app = express();

    var server = http.createServer(app);

    app.use(bodyParser.json());

    app.use(swaggerize({
        api: path.resolve('./config/swagger.json'),
        handlers: path.resolve('./handlers'),
        docspath: '/swagger' 
    }));

    // change four
    app.use('/docs', swaggerUi({
        docs: '/swagger'  
    }));

    server.listen(port, function () { 
    });
    ```   

    Questo codice introduce alcune piccole modifiche che consentono di usarlo con il servizio app di Azure ed espone un'interfaccia Web interattiva per l'API.

### <a name="test-the-api-locally"></a>Testare l'API in locale

1. Avviare l'app Node.js
    ```bash
    npm start
    ```
    
2. Accedere a http://localhost:8000/contacts per visualizzare il codice JSON per l'intero elenco di contatti.
   
   ```json
    {
        "id": 1,
        "name": "Barney Poland",
        "email": "barney@contoso.com"
    },
    {
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    },
    {
        "id": 3,
        "name": "Lora Riggs",
        "email": "lora@contoso.com"
    }
   ```

3. Accedere a http://localhost:8000/contacts/2 per visualizzare il contatto con un `id` su due.
   
    ```json
    { 
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    }
    ```

4. Testare l'API usando l'interfaccia Web di Swagger all'indirizzo http://localhost:8000/docs.
   
    ![Interfaccia Web di Swagger](media/app-service-api-nodejs-api-app/swagger-ui.png)

## <a id="createapiapp"></a> Creare un'app per le API

In questa sezione si usa l'interfaccia della riga di comando di Azure 2.0 per creare le risorse necessarie per ospitare l'API nel servizio app di Azure. 

1.  Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#login) e seguire le istruzioni visualizzate.

    ```azurecli-interactive
    az login
    ```

2. [!INCLUDE [Create resource group](../../includes/app-service-api-create-resource-group.md)] 

3. [!INCLUDE [Create app service plan](../../includes/app-service-api-create-app-service-plan.md)]

4. [!INCLUDE [Create API app](../../includes/app-service-api-create-api-app.md)] 


## <a name="deploy-the-api-with-git"></a>Distribuire l'API con Git

Distribuire il codice nell'app per le API effettuando il push dei commit dal repository Git locale al servizio app di Azure.

1. [!INCLUDE [Configure your deployment credentials](../../includes/app-service-api-configure-local-git.md)] 

2. Inizializzare un nuovo repository nella directory *ContactList*. 

    ```bash
    git init .
    ```

3. Escludere la directory *node_modules* creata da npm in un passaggio precedente dell'esercitazione in Git. Creare un nuovo file `.gitignore` nella directory corrente e aggiungere il testo seguente in una nuova riga in un punto qualsiasi del file.

    ```
    node_modules/
    ```
    Confermare che la cartella `node_modules` venga ignorata con `git status`.

4. Eseguire il commit delle modifiche nel repository.
    ```bash
    git add .
    git commit -m "initial version"
    ```

5. [!INCLUDE [Push to Azure](../../includes/app-service-api-git-push-to-azure.md)]  
 
## <a name="test-the-api--in-azure"></a>Testare l'API in Azure

1. Aprire una finestra del browser all'indirizzo http://app_name.azurewebsites.net/contacts. Verrà visualizzato lo stesso codice JSON restituito quando è stata eseguita la richiesta in locale in un passaggio precedente dell'esercitazione.

   ```json
   {
       "id": 1,
       "name": "Barney Poland",
       "email": "barney@contoso.com"
   },
   {
       "id": 2,
       "name": "Lacy Barrera",
       "email": "lacy@contoso.com"
   },
   {
       "id": 3,
       "name": "Lora Riggs",
       "email": "lora@contoso.com"
   }
   ```

2. In un browser accedere all'endpoint `http://app_name.azurewebsites.net/docs` per provare l'interfaccia utente di Swagger in esecuzione in Azure.

    ![Interfaccia utente di Swagger](media/app-service-api-nodejs-api-app/swagger-azure-ui.png)

    È ora possibile distribuire gli aggiornamenti nell'API di esempio in Azure eseguendo il push dei commit nel repository Git di Azure.

## <a name="clean-up"></a>Eseguire la pulizia

Per eliminare tutte le risorse create nella presente guida introduttiva, eseguire questo comando dell'interfaccia della riga di comando di Azure:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-step"></a>Passaggio successivo 
> [!div class="nextstepaction"]
> [Usare app per le API da client JavaScript con CORS](app-service-api-cors-consume-javascript.md)



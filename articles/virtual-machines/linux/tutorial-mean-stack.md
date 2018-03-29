---
title: Creare uno stack MEAN in una VM Linux in Azure | Microsoft Docs
description: Informazioni su come creare uno stack MongoDB, Express, AngularJS e Node.js (MEAN) in una VM Linux in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/08/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 2bd89bf25f619caef07ae099232add55dbe0cda7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-mongodb-express-angularjs-and-nodejs-mean-stack-on-a-linux-vm-in-azure"></a>Creare uno stack MongoDB, Express, AngularJS e Node.js (MEAN) in una VM Linux in Azure

Questa esercitazione illustra come implementare uno stack MongoDB, Express, AngularJS e Node.js (MEAN) in una VM Linux in Azure. Lo stack MEAN creato consente di aggiungere, eliminare ed elencare libri in un database. Si apprenderà come:

> [!div class="checklist"]
> * Creare una macchina virtuale Linux
> * Installare Node.js
> * Installare MongoDB e configurare il server
> * Installare Express e configurare le route per il server
> * Accedere alle route con AngularJS
> * Eseguire l'applicazione

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, in questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).


## <a name="create-a-linux-vm"></a>Creare una macchina virtuale Linux

Creare un gruppo di risorse con il comando [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) e creare una VM Linux con il comando [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente usa l'interfaccia della riga di comando di Azure per creare un gruppo di risorse denominato *myResourceGroupMEAN* nella località *eastus*. Viene creata una VM denominata *myVM* con le chiavi SSH se non esistono già in una posizione predefinita. Per usare un set specifico di chiavi, usare l'opzione --ssh-key-value option.

```azurecli-interactive
az group create --name myResourceGroupMEAN --location eastus
az vm create \
    --resource-group myResourceGroupMEAN \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password 'Azure12345678!' \
    --generate-ssh-keys
az vm open-port --port 3300 --resource-group myResourceGroupMEAN --name myVM
```

Dopo che la VM è stata creata, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente: 

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroupMEAN/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.72.77.9",
  "resourceGroup": "myResourceGroupMEAN"
}
```
Prendere nota di `publicIpAddress`. Questo indirizzo viene usato per accedere alla VM.

Usare il comando seguente per creare una sessione SSH con la VM. Assicurarsi di usare l'indirizzo IP corretto. Nell'esempio riportato sopra l'indirizzo IP era 13.72.77.9.

```bash
ssh azureuser@13.72.77.9
```

## <a name="install-nodejs"></a>Installare Node.js

[Node.js](https://nodejs.org/en/) è un runtime JavaScript basato sul motore V8 JavaScript di Chrome. Node.js viene usato in questa esercitazione per configurare le route Express e i controller AngularJS.

Nella VM, usando la shell di Bash aperta con SSH, installare Node.js.

```bash
sudo apt-get install -y nodejs
```

## <a name="install-mongodb-and-set-up-the-server"></a>Installare MongoDB e configurare il server
[MongoDB](http://www.mongodb.com) archivia i dati in documenti flessibili simili a JSON. I campi di un database possono essere diversi da un documento all'altro e la struttura dei dati può essere modificata nel tempo. Per l'applicazione di esempio, verranno aggiunti a MongoDB i record dei libri contenenti titolo del libro, numero isbn, autore e numero di pagine. 

1. Nella VM, usando la shell di Bash aperta con SSH, impostare la chiave MongoDB.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
    echo "deb [ arch=amd64 ] http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
    ```

2. Aggiornare l'utilità di gestione pacchetti con la chiave.
  
    ```bash
    sudo apt-get update
    ```

3. Installare MongoDB.

    ```bash
    sudo apt-get install -y mongodb
    ```

4. Avviare il server.

    ```bash
    sudo service mongodb start
    ```

5. È anche necessario installare il pacchetto [body-parser](https://www.npmjs.com/package/body-parser-json) per elaborare il codice JSON passato nelle richieste al server.

    Installare l'utilità di gestione pacchetti npm.

    ```bash
    sudo apt-get install npm
    ```

    Installare il pacchetto del parser corpo.
    
    ```bash
    sudo npm install body-parser
    ```

6. Creare una cartella denominata *Books* e aggiungervi un file denominato *server.js* contenente la configurazione per il server Web.

    ```node.js
    var express = require('express');
    var bodyParser = require('body-parser');
    var app = express();
    app.use(express.static(__dirname + '/public'));
    app.use(bodyParser.json());
    require('./apps/routes')(app);
    app.set('port', 3300);
    app.listen(app.get('port'), function() {
        console.log('Server up: http://localhost:' + app.get('port'));
    });
    ```

## <a name="install-express-and-set-up-routes-to-the-server"></a>Installare Express e configurare le route per il server

[Express](https://expressjs.com) è un framework applicazione Web Node.js minimo e flessibile che fornisce funzionalità per le applicazioni Web e per dispositivi mobili. In questa esercitazione Express viene usato per passare le informazioni sui libri verso e dal database MongoDB. [Mongoose](http://mongoosejs.com) fornisce una soluzione semplice basata su schema per modellare i dati dell'applicazione. In questa esercitazione Mongoose viene usato per fornire uno schema dei libri per il database.

1. Installare Express e Mongoose.

    ```bash
    sudo npm install express mongoose
    ```

2. Nella cartella *Books* creare una cartella denominata *apps* e aggiungere un file denominato *routes.js* con le route Express definite.

    ```node.js
    var Book = require('./models/book');
    module.exports = function(app) {
      app.get('/book', function(req, res) {
        Book.find({}, function(err, result) {
          if ( err ) throw err;
          res.json(result);
        });
      }); 
      app.post('/book', function(req, res) {
        var book = new Book( {
          name:req.body.name,
          isbn:req.body.isbn,
          author:req.body.author,
          pages:req.body.pages
        });
        book.save(function(err, result) {
          if ( err ) throw err;
          res.json( {
            message:"Successfully added book",
            book:result
          });
        });
      });
      app.delete("/book/:isbn", function(req, res) {
        Book.findOneAndRemove(req.query, function(err, result) {
          if ( err ) throw err;
          res.json( {
            message: "Successfully deleted the book",
            book: result
          });
        });
      });
      var path = require('path');
      app.get('*', function(req, res) {
        res.sendfile(path.join(__dirname + '/public', 'index.html'));
      });
    };
    ```

3. Nella cartella *apps* creare una cartella denominata *models* e aggiungere un file denominato *book.js* con la configurazione del modello di libro definita.  

    ```node.js
    var mongoose = require('mongoose');
    var dbHost = 'mongodb://localhost:27017/test';
    mongoose.connect(dbHost);
    mongoose.connection;
    mongoose.set('debug', true);
    var bookSchema = mongoose.Schema( {
      name: String,
      isbn: {type: String, index: true},
      author: String,
      pages: Number
    });
    var Book = mongoose.model('Book', bookSchema);
    module.exports = mongoose.model('Book', bookSchema); 
    ```

## <a name="access-the-routes-with-angularjs"></a>Accedere alle route con AngularJS

[AngularJS](https://angularjs.org) fornisce un framework Web per creare visualizzazioni dinamiche nelle applicazioni Web. In questa esercitazione AngularJS viene usato per connettere la pagina Web a Express ed eseguire azioni sul database dei libri.

1. Tornare alla directory *Books* (`cd ../..`) e quindi creare una cartella denominata *public* e aggiungere un file denominato *script.js* con la configurazione del controller definita.

    ```node.js
    var app = angular.module('myApp', []);
    app.controller('myCtrl', function($scope, $http) {
      $http( {
        method: 'GET',
        url: '/book'
      }).then(function successCallback(response) {
        $scope.books = response.data;
      }, function errorCallback(response) {
        console.log('Error: ' + response);
      });
      $scope.del_book = function(book) {
        $http( {
          method: 'DELETE',
          url: '/book/:isbn',
          params: {'isbn': book.isbn}
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
      $scope.add_book = function() {
        var body = '{ "name": "' + $scope.Name + 
        '", "isbn": "' + $scope.Isbn +
        '", "author": "' + $scope.Author + 
        '", "pages": "' + $scope.Pages + '" }';
        $http({
          method: 'POST',
          url: '/book',
          data: body
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
    });
    ```
    
2. Nella cartella *public* creare un file denominato *index.html* con la pagina Web definita.

    ```html
    <!doctype html>
    <html ng-app="myApp" ng-controller="myCtrl">
      <head>
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
        <script src="script.js"></script>
      </head>
      <body>
        <div>
          <table>
            <tr>
              <td>Name:</td> 
              <td><input type="text" ng-model="Name"></td>
            </tr>
            <tr>
              <td>Isbn:</td>
              <td><input type="text" ng-model="Isbn"></td>
            </tr>
            <tr>
              <td>Author:</td> 
              <td><input type="text" ng-model="Author"></td>
            </tr>
            <tr>
              <td>Pages:</td>
              <td><input type="number" ng-model="Pages"></td>
            </tr>
          </table>
          <button ng-click="add_book()">Add</button>
        </div>
        <hr>
        <div>
          <table>
            <tr>
              <th>Name</th>
              <th>Isbn</th>
              <th>Author</th>
              <th>Pages</th>
            </tr>
            <tr ng-repeat="book in books">
              <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
              <td>{{book.name}}</td>
              <td>{{book.isbn}}</td>
              <td>{{book.author}}</td>
              <td>{{book.pages}}</td>
            </tr>
          </table>
        </div>
      </body>
    </html>
    ```

##  <a name="run-the-application"></a>Eseguire l'applicazione

1. Tornare alla directory *Books* (`cd ..`) e avviare il server eseguendo questo comando:

    ```bash
    nodejs server.js
    ```

2. Aprire un Web browser all'indirizzo registrato per la VM. Ad esempio: *http://13.72.77.9:3300*. Verrà visualizzata una pagina simile alla seguente:

    ![Record del libro](media/tutorial-mean/meanstack-init.png)

3. Immettere i dati nelle caselle di testo e fare clic su **Add**, Ad esempio: 

    ![Aggiungere il record di un libro](media/tutorial-mean/meanstack-add.png)

4. Dopo avere aggiornato la pagina, verrà visualizzata una pagina simile alla seguente:

    ![Elencare i record dei libro](media/tutorial-mean/meanstack-list.png)

5. È possibile fare clic su **Delete** e rimuovere il record dei libri dal database.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è creata un'applicazione Web che tiene traccia dei record dei libri usando uno stack MEAN in una VM Linux. Si è appreso come:

> [!div class="checklist"]
> * Creare una macchina virtuale Linux
> * Installare Node.js
> * Installare MongoDB e configurare il server
> * Installare Express e configurare le route per il server
> * Accedere alle route con AngularJS
> * Eseguire l'applicazione

Passare all'esercitazione successiva per apprendere come proteggere i server Web con i certificati SSL.

> [!div class="nextstepaction"]
> [Secure web server with SSL (Proteggere il server Web con SSL)](tutorial-secure-web-server.md)

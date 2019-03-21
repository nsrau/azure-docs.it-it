---
title: Creare un'app Web Flask usando l'API Azure Cosmos DB per MongoDB e Python SDK
description: Illustra un esempio di codice Python Flask che è possibile usare per la connessione e l'esecuzione di query usando l'API Azure Cosmos DB per MongoDB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 35159a1630c7f1268119cd55cc6e0ac99dfd7b5f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58119208"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Guida introduttiva: Creare un'app Python usando l'API Azure Cosmos DB per MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile creare rapidamente database di documenti, chiave/valore e a grafo ed eseguire query su di essi sfruttando in ognuno dei casi i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Cosmos DB.

Questa guida di avvio rapido usa l'[esempio Flask](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample) seguente e illustra come creare una semplice app Flask di elenco attività con l'[emulatore di Azure Cosmos DB](local-emulator.md) e l'API Azure Cosmos DB per MongoDB.

## <a name="prerequisites"></a>Prerequisiti

- Scaricare l'[emulatore di Azure Cosmos DB](local-emulator.md). L'emulatore è attualmente supportato solo in Windows. Viene illustrato come è possibile usare l'esempio con una chiave di produzione di Azure in qualsiasi piattaforma.

- Se necessario, è possibile installare rapidamente [Visual Studio Code](https://code.visualstudio.com/Download) per la piattaforma in uso (Windows, Mac, Linux).

- Assicurarsi di aggiungere il supporto per il linguaggio Python installando una delle più diffuse estensioni Python.
  1. Selezionare un'estensione.
  2. Installare l'estensione digitando `ext install` nel riquadro comandi `Ctrl+Shift+P`.

     Gli esempi di questo documento usano la popolare [estensione Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) con funzionalità complete di Don Jayamanne.

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Clonare ora un'app MongoDB Flask da GitHub, impostare la stringa di connessione ed eseguirla. Come si noterà, è facile usare i dati a livello di codice.

1. Aprire un prompt dei comandi, creare una nuova cartella denominata git-samples e quindi chiudere il prompt dei comandi.

    ```bash
    md "C:\git-samples"
    ```

2. Aprire una finestra del terminale Git, ad esempio Git Bash, ed eseguire il comando `cd` per passare a una nuova cartella in cui installare l'app di esempio.

    ```bash
    cd "C:\git-samples"
    ```

3. Eseguire il comando seguente per clonare l'archivio di esempio. Questo comando crea una copia dell'app di esempio nel computer in uso.

    ```bash
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. Eseguire il comando seguente per installare i moduli di Python.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Aprire la cartella in Visual Studio Code.

## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Per scoprire in che modo le risorse del database vengono create nel codice, è possibile esaminare i frammenti di codice seguenti. Altrimenti è possibile passare direttamente a [Eseguire l'app Web](#run-the-web-app). 

Tutti i frammenti di codice seguenti sono tratti dal file app.py e usano la stringa di connessione per l'emulatore di Azure Cosmos DB locale. La password deve essere suddivisa come illustrato sotto per contenere le barre che non possono essere analizzate in altro modo.

* Inizializzare il client MongoDB, recuperare il database ed eseguire l'autenticazione.

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* Recuperare la raccolta o crearla, se non esiste già.

    ```python
    todos = db.todo #Select the collection
    ```

* Creare l'app

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>Eseguire l'app Web

1. Verificare che l'emulatore di Azure Cosmos DB sia in esecuzione.

2. Aprire una finestra del terminale e usare il comando `cd` per accedere alla directory in cui l'app è stata salvata.

3. Impostare quindi la variabile di ambiente per l'app Flask con `set FLASK_APP=app.py` o `export FLASK_APP=app.py` se si usa un Mac.

4. Eseguire l'app con `flask run` e passare a [http://127.0.0.1:5000/](http://127.0.0.1:5000/).

5. Aggiungere e rimuovere le attività e verificare che siano state aggiunte e modificate nella raccolta.

## <a name="create-a-database-account"></a>Creare un account di database

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Aggiornare la stringa di connessione

Per testare il codice in un account Cosmos live, passare al portale di Azure per creare un account e ottenere le informazioni della stringa di connessione. Copiarle quindi nell'app.

1. Nell'account Cosmos nel [portale di Azure](https://portal.azure.com/) fare clic su **Stringa di connessione** nel riquadro di spostamento sinistro e quindi fare clic su **Chiavi di lettura/scrittura**. Usare i pulsanti di copia sul lato destro dello schermo per copiare nome utente, password e host nel file Dal.cs nel passaggio seguente.

2. Aprire il file **app.py** nella directory radice.

3. Copiare il valore di **nomeutente** dal portale (usando il pulsante di copia) e impostarlo come valore di **name** nel file **app.py**.

4. Copiare quindi il valore della **stringa di connessione** dal portale e impostarlo come valore di MongoClient nel file **app.py**.

5. Copiare infine il valore di **password** dal portale e impostarlo come valore di **password** nel file **app.py**.

L'app è stata ora aggiornata con tutte le informazioni necessarie per comunicare con Cosmos DB. È possibile eseguirla esattamente come prima.

## <a name="deploy-to-azure"></a>Distribuisci in Azure

Per distribuire l'app, è possibile creare una nuova app Web in Azure e abilitare la distribuzione continua con un fork di questo repository GitHub. Seguire questa [esercitazione](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment) per configurare la distribuzione continua con GitHub in Azure.

Quando si esegue la distribuzione in Azure, è consigliabile rimuovere le chiavi dell'applicazione e verificare che la sezione seguente non venga impostata come commento:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

È quindi necessario aggiungere gli elementi MONGOURL, MONGO_PASSWORD e MONGO_USERNAME alle impostazioni applicazione. È possibile seguire questa [esercitazione](https://docs.microsoft.com/azure/app-service-web/web-sites-configure#application-settings) per altre informazioni sulle impostazioni applicazione nelle app Web di Azure.

Se non si vuole creare un fork di questo repository, è anche possibile fare clic sul pulsante Distribuisci in Azure sotto. Si passerà quindi ad Azure per configurare le impostazioni applicazione con le informazioni dell'account Cosmos DB.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Se si prevede di archiviare il codice in GitHub o in altre opzioni di controllo del codice sorgente, assicurarsi di rimuovere dal codice le stringhe di connessione. che possono essere invece impostate con le impostazioni applicazione per l'app web.

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come creare un account Cosmos ed eseguire un'app Flask. È ora possibile importare dati aggiuntivi nel database Cosmos. 

> [!div class="nextstepaction"]
> [Importare i dati di MongoDB in Azure Cosmos DB](mongodb-migrate.md)

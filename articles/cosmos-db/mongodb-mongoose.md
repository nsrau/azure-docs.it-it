---
title: Uso del framework Mongoose con Azure Cosmos DB | Microsoft Docs
description: Informazioni su come connettere un'app Node.js Mongoose ad Azure Cosmos DB
services: cosmos-db
documentationcenter: 
author: romitgirdhar
manager: jhubbard
editor: 
ms.assetid: de5eea58-ee7c-4609-b1c9-4af3e61a5883
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 01/08/2018
ms.author: rogirdh
ms.openlocfilehash: fb6db6555171b65767a715c6b4c8ff37f42c94ef
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="azure-cosmos-db-using-the-mongoose-framework-with-azure-cosmos-db"></a>Azure Cosmos DB: uso del framework Mongoose con Azure Cosmos DB

Questa esercitazione dimostra come usare il [framework Mongoose](http://mongoosejs.com/) quando si archiviano dati in Azure Cosmos DB. Per questa procedura dettagliata viene usata l'API MongoDB di Azure Cosmos DB. Mongoose è un framework di modellazione a oggetti per MongoDB in Node.js che offre una soluzione semplice e basata su schemi per modellare i dati dell'applicazione.

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile creare ed eseguire rapidamente query su database di documenti, coppie chiave-valore e grafi, sfruttando in ognuno dei casi i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Azure Cosmos DB.

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) v0.10.29 o versioni successive.

## <a name="create-an-azure-cosmos-db-account"></a>Creare un account Azure Cosmos DB

Creare prima di tutto un account Azure Cosmos DB. Se è già disponibile un account da usare, è possibile passare a [Configurare l'applicazione Node.js](#SetupNode). Se si usa l'emulatore Azure Cosmos DB, seguire la procedura descritta nell'articolo [Emulatore di Azure Cosmos DB](local-emulator.md) per configurare l'emulatore e proseguire con il passaggio [Configurare l'applicazione Node.js](#SetupNode).

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="set-up-your-nodejs-application"></a>Configurare l'applicazione Node.js

>[!Note]
> Se si desidera solo esaminare il codice di esempio senza configurare l'applicazione, clonare l'[esempio](https://github.com/Azure-Samples/Mongoose_CosmosDB) usato per questa esercitazione e compilare l'applicazione Node.js Mongoose in Azure Cosmos DB.

1. Per creare un'applicazione Node.js nella cartella scelta, eseguire questo comando in un prompt dei comandi del nodo.

    ```npm init```

    Rispondere alle domande e il progetto sarà pronto per l'utilizzo.

1. Aggiungere un nuovo file alla cartella e denominarlo ```index.js```.
1. Installare i pacchetti necessari usando una delle opzioni ```npm install``` seguenti:
    * Mongoose: ```npm install mongoose --save```
    * Dotenv (se si desidera caricare i segreti da un file con estensione env):```npm install dotenv --save```

    >[!Note]
    > Il flag ```--save``` aggiunge la dipendenza al file package.json.

1. Importare le dipendenze nel file index.js.
    ```JavaScript
    var mongoose = require('mongoose');
    var env = require('dotenv').load();    //Use the .env file to load the variables
    ```

1. Aggiungere la stringa di connessione Cosmos DB e il nome Cosmos DB al file ```.env```.

    ```JavaScript
    COSMOSDB_CONNSTR={Your MongoDB Connection String Here}
    COSMOSDB_DBNAME={Your DB Name Here}
    ```

1. Eseguire la connessione all'istanza di Azure Cosmos DB tramite il framework Mongoose, aggiungendo il codice seguente alla fine di index.js.
    ```JavaScript
    mongoose.connect(process.env.COSMOSDB_CONNSTR+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb"); //Creates a new DB, if it doesn't already exist

    var db = mongoose.connection;
    db.on('error', console.error.bind(console, 'connection error:'));
    db.once('open', function() {
    console.log("Connected to DB");
    });
    ```
    >[!Note]
    > In questo caso, le variabili di ambiente vengono caricate usando process.env. {variableName} mediante il pacchetto npm 'dotenv'.

    Dopo aver eseguito la connessione ad Azure Cosmos DB, è possibile avviare la configurazione dei modelli a oggetti in Mongoose.

## <a name="caveats-to-using-mongoose-with-azure-cosmos-db"></a>Precisazioni sull'utilizzo di Mongoose con Azure Cosmos DB

Per ogni modello creato, Mongoose crea una nuova raccolta MongoDB. Poiché tuttavia a Azure Cosmos DB è associato un modello di fatturazione per raccolta, questo potrebbe non risultare il modo più conveniente per procedere qualora si disponga di più modelli a oggetti popolati in modo sparse.

Questa procedura dettagliata prende in considerazione entrambi i modelli. In primo luogo, verrà descritta la procedura dettagliata che prevede l'archiviazione di un tipo di dati per raccolta. Si tratta del comportamento de facto di Mongoose.

In Mongoose esiste anche il concetto di [discriminatori](http://mongoosejs.com/docs/discriminators.html), che rappresentano un meccanismo di ereditarietà degli schemi. I discriminatori consentono di disporre di più modelli con schemi sovrapposti nella stessa raccolta MongoDB sottostante.

È possibile archiviare vari modelli di dati nella stessa raccolta e quindi usare una clausola di filtro in fase di query per visualizzare solo i dati necessari.

### <a name="one-collection-per-object-model"></a>Una raccolta per modello a oggetti

Il comportamento predefinito di Mongoose consiste nel creare una raccolta MongoDB ogni volta che si crea un modello a oggetti. Questa sezione illustra come eseguire tale operazione con MongoDB per Azure Cosmos DB. È consigliabile usare questo metodo con Azure Cosmos DB quando si dispone di modelli a oggetti con grandi quantità di dati. Si tratta del modello di funzionamento predefinito per Mongoose. È pertanto necessario conoscerlo se si ha familiarità con Mongoose.

1. Aprire di nuovo ```index.js```.

1. Creare la definizione dello schema per 'Family' (Famiglia).

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

1. Creare un oggetto per 'Family' (Famiglia).

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. Salvare infine l'oggetto in Azure Cosmos DB. Verrà creata una raccolta.

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. Creare a questo punto un altro schema e un altro oggetto, ad esempio 'Vacation Destinations' (Destinazioni vacanze) che può essere interessante per le famiglie.
    1. Come in precedenza, creare lo schema.
    ```JavaScript
    const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
        name: String,
        country: String
    }));
    ```

    1. Creare un oggetto di esempio e salvarlo. È possibile aggiungere più oggetti a questo schema.
    ```JavaScript
    const vacaySpot = new VacationDestinations({
        name: "Honolulu",
        country: "USA"
    });

    vacaySpot.save((err, saveVacay) => {
        console.log(JSON.stringify(saveVacay));
    });
    ```

1. Nel portale di Azure saranno a questo punto disponibili due raccolte create in Azure Cosmos DB.

    ![Esercitazione su Node.js - Screenshot del portale di Azure in cui è mostrato un account Azure Cosmos DB, con i nomi di più raccolte evidenziati - Database del nodo][mutiple-coll]

1. È infine possibile leggere i dati da Azure Cosmos DB. Poiché è stato usato il modello di funzionamento predefinito di Mongoose, le letture avvengono come di consueto in Mongoose.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Utilizzo dei discriminatori di Mongoose per archiviare i dati in una singola raccolta

Per questo metodo si usano i [discriminatori di Mongoose](http://mongoosejs.com/docs/discriminators.html) per ottimizzare i costi di ogni raccolta di Azure Cosmos DB. I discriminatori consentono di definire una chiave ('Key') di differenziazione, che consente di eseguire operazioni di archiviazione, differenziazione e filtro su modelli a oggetti diversi.

In questo caso, si creerà un modello a oggetti di base, si definirà una chiave di differenziazione e si aggiungeranno 'Family' e 'VacationDestinations' come estensione del modello di base.

1. Definire la configurazione di base e la chiave discriminatore.

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

1. Definire quindi il modello a oggetti comune.

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

1. Definire a questo punto il modello 'Family'. Si noti che in questo caso viene usato ```commonModel.discriminator``` anziché ```mongoose.model```. Si aggiungerà inoltre la configurazione di base allo schema mongoose. La chiave discriminatore (discriminatorKey) in questo caso è ```FamilyType```.

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

1. Analogamente, procedere con l'aggiunta di un altro schema, questa volta per 'VacationDestinations'. La chiave discriminatore (discriminatorKey) in questo caso è ```VacationDestinationsType```.

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. Creare infine gli oggetti per il modello ed eseguire il salvataggio.
    1. Aggiungere uno o più oggetti al modello 'Family'.
    ```JavaScript
    const family_common = new Family_common({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });

    family_common.save((err, saveFamily) => {
        console.log("Saved: " + JSON.stringify(saveFamily));
    });
    ```

    1. Successivamente, aggiungere uno o più oggetti al modello 'VacationDestinations' ed eseguire il salvataggio.
    ```JavaScript
    const vacay_common = new Vacation_common({
        name: "Honolulu",
        country: "USA"
    });

    vacay_common.save((err, saveVacay) => {
        console.log("Saved: " + JSON.stringify(saveVacay));
    });
    ```

1. Passando di nuovo al portale di Azure, si noterà che è disponibile una sola raccolta denominata ```alldata``` con i dati di 'Family' e 'VacationDestinations'.

    ![Esercitazione su Node.js - Screenshot del portale di Azure in cui è mostrato un account Azure Cosmos DB, con il nome della raccolta evidenziato - Database del nodo][alldata]

1. Si noti inoltre che ogni oggetto dispone di un altro attributo denominato ```__type```, che consente la differenziazione dei due diversi modelli a oggetti.

1. Leggere infine i dati archiviati in Azure Cosmos DB. Mongoose filtrerà i dati in base al modello. Non sarà pertanto necessario eseguire altre operazioni durante la lettura dei dati. Sarà sufficiente specificare il modello (in questo caso, ```Family_common```) e Mongoose gestirà l'applicazione dei filtri in base a 'DiscriminatorKey'.

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

Usare i discriminatori di Mongoose è molto facile. Se pertanto si dispone di un'app che usa il framework Mongoose, questa esercitazione è utile per rendere l'applicazione operativa sull'API MongoDB in Azure Cosmos DB, senza dover apportare troppe modifiche.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su operazioni, operatori, fasi, comandi e opzioni MongoDB supportati dall'API MongoDB di Azure Cosmos DB, vedere [Supporto dell'API MongoDB per le funzionalità e la sintassi MongoDB](mongodb-feature-support.md).

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[mutiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png
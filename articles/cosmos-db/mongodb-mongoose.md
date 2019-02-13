---
title: Connettere un'applicazione Node.js Mongoose ad Azure Cosmos DB
description: Informazioni su come usare il framework Mongoose per archiviare e gestire i dati in Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.custom: seodec18
ms.openlocfilehash: 26eccfd2a144d5bfcb285e6b52af246c3eefde75
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55822035"
---
# <a name="connect-a-nodejs-mongoose-application-to-azure-cosmos-db"></a>Connettere un'applicazione Node.js Mongoose ad Azure Cosmos DB

Questa esercitazione dimostra come usare il [framework Mongoose](https://mongoosejs.com/) quando si archiviano dati in Cosmos DB. Per questa procedura dettagliata viene usata l'API di Azure Cosmos DB per MongoDB. Mongoose è un framework di modellazione a oggetti per MongoDB in Node.js che offre una soluzione semplice e basata su schemi per modellare i dati dell'applicazione.

Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile creare rapidamente database di documenti, chiave/valore e a grafo ed eseguire query su di essi sfruttando in ognuno dei casi i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Cosmos DB.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) v0.10.29 o versioni successive.

## <a name="create-a-cosmos-account"></a>Creare un account Cosmos

Di seguito viene descritta la procedura per creare un account Cosmos. Se è già disponibile un account da usare, è possibile passare a Configurare l'applicazione Node.js. Se si usa l'emulatore Azure Cosmos DB, seguire i passaggi descritti in [Emulatore di Azure Cosmos DB](local-emulator.md) per configurare l'emulatore e proseguire con il passaggio Configurare l'applicazione Node.js.

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="set-up-your-nodejs-application"></a>Configurare l'applicazione Node.js

>[!Note]
> Se si desidera solo esaminare il codice di esempio senza configurare l'applicazione, clonare l'[esempio](https://github.com/Azure-Samples/Mongoose_CosmosDB) usato per questa esercitazione e compilare l'applicazione Node.js Mongoose in Azure Cosmos DB.

1. Per creare un'applicazione Node.js nella cartella scelta, eseguire questo comando in un prompt dei comandi del nodo.

    ```npm init```

    Rispondere alle domande e il progetto sarà pronto per l'utilizzo.

1. Aggiungere un nuovo file alla cartella e denominarlo ```index.js```.
1. Installare i pacchetti necessari usando una delle opzioni ```npm install``` seguenti:
    * Mongoose: ```npm install mongoose@5 --save```

    > [!Note]
    > La connessione di esempio Mongoose riportata di seguito si basa su Mongoose 5+, che è stata modificata rispetto alle versioni precedenti.
    
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
    COSMOSDB_CONNSTR=mongodb://{cosmos-user}.documents.azure.com:10255/{dbname}
    COSMODDB_USER=cosmos-user
    COSMOSDB_PASSWORD=cosmos-secret
    ```

1. Connettersi a Cosmos DB tramite il framework Mongoose, aggiungendo il codice seguente alla fine di index.js.
    ```JavaScript
    mongoose.connect(process.env.COSMOSDB_CONNSTR+"?ssl=true&replicaSet=globaldb", {
      auth: {
        user: process.env.COSMODDB_USER,
        password: process.env.COSMOSDB_PASSWORD
      }
    })
    .then(() => console.log('Connection to CosmosDB successful'))
    .catch((err) => console.error(err));
    ```
    >[!Note]
    > In questo caso, le variabili di ambiente vengono caricate usando process.env. {variableName} mediante il pacchetto npm 'dotenv'.

    Dopo aver eseguito la connessione ad Azure Cosmos DB, è possibile avviare la configurazione dei modelli a oggetti in Mongoose.

## <a name="caveats-to-using-mongoose-with-cosmos-db"></a>Precisazioni sull'uso di Mongoose con Cosmos DB

Per ogni modello creato, Mongoose crea una nuova raccolta. Poiché tuttavia a Cosmos DB è associato un modello di fatturazione per raccolta, questo potrebbe non risultare il modo più conveniente per procedere qualora si disponga di più modelli a oggetti popolati in modo sparse.

Questa procedura dettagliata prende in considerazione entrambi i modelli. In primo luogo, verrà descritta la procedura dettagliata che prevede l'archiviazione di un tipo di dati per raccolta. Si tratta del comportamento de facto di Mongoose.

In Mongoose esiste anche il concetto di [discriminatori](https://mongoosejs.com/docs/discriminators.html), che rappresentano un meccanismo di ereditarietà degli schemi. I discriminatori consentono di disporre di più modelli con schemi sovrapposti nella stessa raccolta MongoDB sottostante.

È possibile archiviare vari modelli di dati nella stessa raccolta e quindi usare una clausola di filtro in fase di query per visualizzare solo i dati necessari.

### <a name="one-collection-per-object-model"></a>Una raccolta per modello a oggetti

Il comportamento predefinito di Mongoose consiste nel creare una raccolta MongoDB ogni volta che si crea un modello a oggetti. Questa sezione illustra come eseguire tale operazione con l'API di Azure Cosmos DB per MongoDB. È consigliabile usare questo metodo in presenza di modelli a oggetti con grandi quantità di dati. Si tratta del modello di funzionamento predefinito per Mongoose. È pertanto necessario conoscerlo se si ha familiarità con Mongoose.

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

1. Salvare infine l'oggetto in Cosmos DB. Verrà creata una raccolta.

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

1. Nel portale di Azure saranno a questo punto disponibili due raccolte create in Cosmos DB.

    ![Esercitazione su Node.js - Screenshot del portale di Azure in cui è mostrato un account Azure Cosmos DB, con i nomi di più raccolte evidenziati - Database del nodo][multiple-coll]

1. È infine possibile leggere i dati da Cosmos DB. Poiché è stato usato il modello di funzionamento predefinito di Mongoose, le letture avvengono come di consueto in Mongoose.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Utilizzo dei discriminatori di Mongoose per archiviare i dati in una singola raccolta

Per questo metodo si usano i [discriminatori di Mongoose](https://mongoosejs.com/docs/discriminators.html) per ottimizzare i costi di ogni raccolta. I discriminatori consentono di definire una chiave ('Key') di differenziazione, che consente di eseguire operazioni di archiviazione, differenziazione e filtro su modelli a oggetti diversi.

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

Usare i discriminatori di Mongoose è molto facile. In presenza di un'app che usa il framework Mongoose, pertanto, questa esercitazione è utile per rendere l'applicazione operativa usando l'API di Azure Cosmos per MongoDB, senza dover apportare troppe modifiche.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare Studio 3T](mongodb-mongochef.md) con l'API di Azure Cosmos DB per MongoDB.
- Informazioni su come [usare Robo 3T](mongodb-robomongo.md) con l'API di Azure Cosmos DB per MongoDB.
- Esplorare gli [esempi](mongodb-samples.md) di MongoDB con l'API di Azure Cosmos DB per MongoDB.

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[multiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png

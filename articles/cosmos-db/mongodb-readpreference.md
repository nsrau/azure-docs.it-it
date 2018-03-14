---
title: Uso delle preferenze di lettura di MongoDB con l'API MongoDB di Azure Cosmos DB | Microsoft Docs
description: Informazioni su come usare le preferenze di lettura di MongoDB con l'API MongoDB di Azure Cosmos DB
services: cosmos-db
documentationcenter: 
author: vidhoonv
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: 
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: 
ms.topic: article
ms.date: 02/26/2018
ms.author: viviswan
ms.openlocfilehash: b28285695f52d6dbcc33d9fb0efe06b43cdf1cab
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="how-to-globally-distribute-reads-using-read-preference-with-the-azure-cosmos-db-mongodb-api"></a>Come distribuire le letture a livello globale usando le preferenze di lettura con l'API MongoDB di Azure Cosmos DB 

Questo articolo illustra come distribuire a livello globale le operazioni di lettura usando le impostazioni relative alle [preferenze di lettura di MongoDB](https://docs.mongodb.com/manual/core/read-preference/) con l'API MongoDB di Azure Cosmos DB. 

## <a name="prerequisites"></a>prerequisiti 
Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Fare riferimento a questa [Guida introduttiva](tutorial-global-distribution-mongodb.md) per istruzioni su come usare il portale di Azure per configurare un account Azure Cosmos DB con distribuzione globale e quindi connettersi usando l'API MongoDB.

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Aprire una finestra del terminale Git, ad esempio Git Bash, ed eseguire il comando `cd` per passare a una directory di lavoro.  

Eseguire i comandi seguenti per clonare il repository di esempio. In base alla piattaforma rilevante, usare uno dei repository di esempio seguenti:

1. [Applicazione di esempio .NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [Applicazione di esempio NodeJS]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Applicazione di esempio Mongoose](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Applicazione di esempio Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [Applicazione di esempio SpringBoot](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Eseguire l'applicazione

In base alla piattaforma in uso, installare i pacchetti necessari e avviare l'applicazione. Per installare le dipendenze, seguire le istruzioni nel file README incluso nel repository dell'applicazione di esempio. Ad esempio, nell'applicazione di esempio NodeJS eseguire i comandi seguenti per installare i pacchetti necessari e avviare l'applicazione.

```bash
cd mean
npm install
node index.js
```
L'applicazione prova a connettersi a un'origine MongoDB e l'operazione ha esito negativo perché la stringa di connessione non è valida. Seguire i passaggi nel file README per aggiornare la stringa di connessione `url`. Inoltre, aggiornare `readFromRegion` a un'area di lettura nell'account Azure Cosmos DB. Le istruzioni seguenti sono tratte dall'esempio NodeJS:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos DB account's values. 
```

Dopo aver completato questi passaggi, l'applicazione di esempio viene eseguita e produce l'output seguente:

```
connected!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromSecondaryfunc query completed!
```

## <a name="read-using-read-preference-mode"></a>Leggere usando le modalità relative alle preferenze di lettura

MongoDB fornisce le modalità seguenti relative alle preferenze di lettura, per l'uso da parte dei client:

1. PRIMARY
2. PRIMARY_PREFERRED
3. SECONDARY
4. SECONDARY_PREFERRED
5. NEAREST

Vedere la documentazione sul [preferenze di lettura di MongoDB](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) per informazioni dettagliate sul comportamento delle singole modalità delle preferenze di lettura. In Azure Cosmos DB, la modalità primary esegue il mapping all'area di scrittura e la modalità secondary esegue il mapping all'area di lettura.

In base agli scenari comuni, è consigliabile usare le impostazioni seguenti:

1. Se sono necessarie **letture a bassa latenza**, usare la modalità **NEAREST**. Questa impostazione indirizza le operazioni di lettura all'area disponibile più vicina. Si noti che se l'area più vicina è l'area di scrittura, queste operazioni vengono indirizzate a tale area.
2. Se sono necessarie **disponibilità elevata e distribuzione geografica delle letture** (senza vincoli di latenza), usare quindi la modalità **SECONDARY PREFERRED**. Questa impostazione indirizza le operazioni di lettura all'area di lettura disponibile più vicina. Se sono disponibili aree di lettura, le richieste vengono indirizzate all'area di scrittura.

Il frammento di codice seguente tratto dall'applicazione di esempio mostra come configurare le preferenze di lettura NEAREST in NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Analogamente, il frammento di codice seguente mostra come configurare le preferenze di lettura SECONDARY_PREFERRED in NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

Fare riferimento al repository dell'applicazione di esempio corrispondente per altre piattaforme, ad esempio [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) e [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Leggere usando i tag

Oltre alle modalità relative alle preferenze di lettura, MongoDB consente l'uso di tag per indirizzare le operazioni di lettura. Nell'API MongoDB di Azure Cosmos DB, il tag `region` è incluso per impostazione predefinita come parte della risposta `isMaster`:

```json
"tags": {
         "region": "West US"
      }
```

Di conseguenza, MongoClient può usare il tag `region` insieme al nome dell'area per indirizzare le operazioni di lettura ad aree specifiche. Per gli account Azure Cosmos DB, è possibile trovare i nomi delle aree nel portale di Azure a sinistra in **Impostazioni -> Replica i dati a livello globale**. Questa impostazione è utile per ottenere l'**isolamento di lettura**, nei casi in cui l'applicazione client vuole indirizzare le operazioni di lettura solo a una determinata area. Questa impostazione è ideale per scenari non di produzione/di analisi, che vengono eseguiti in background e non sono servizi di produzione critici.

Il frammento di codice seguente tratto dall'applicazione di esempio mostra come configurare le preferenze di lettura mediante i tag in NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Fare riferimento al repository dell'applicazione di esempio corrispondente per altre piattaforme, ad esempio [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) e [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

In questo articolo si è appreso come distribuire a livello globale le operazioni di lettura usando le preferenze di lettura con l'API MongoDB di Azure Cosmos DB.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare l'app, eliminare tutte le risorse create tramite questo articolo nel portale di Azure eseguendo questi passaggi:

1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi fare clic sul nome della risorsa creata. 
2. Nella pagina del gruppo di risorse fare clic su **Elimina**, digitare il nome della risorsa da eliminare nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

* [Importare i dati di MongoDB in Azure Cosmos DB](mongodb-migrate.md)
* [Configurare un account Azure Cosmos DB con replica a livello globale e usarlo con l'API MongoDB](tutorial-global-distribution-mongodb.md)
* [Sviluppare in locale con l'emulatore](local-emulator.md)

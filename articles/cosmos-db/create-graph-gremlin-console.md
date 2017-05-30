---
title: 'Esercitazione su Azure Cosmos DB: Creare, eseguire query e attraversare nella console Gremlin di Apache TinkerPop | Microsoft Docs'
description: Guida di avvio rapido ad Azure Cosmos DB per creare vertici, archi e query usando l&quot;API Graph di Azure Cosmos DB.
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: bf08e031-718a-4a2a-89d6-91e12ff8797d
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: terminal
ms.topic: hero-article
ms.date: 05/19/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: caf3b69b25ccd15322054a0bbf95fc2a5816e00a
ms.contentlocale: it-it
ms.lasthandoff: 05/20/2017


---
# <a name="azure-cosmos-db-create-query-and-traverse-a-graph-in-the-gremlin-console"></a>Azure Cosmos DB: Creare, eseguire query e attraversare un grafo nella console Gremlin

Azure Cosmos DB è il servizio di database multimodello distribuito a livello globale di Microsoft. È possibile creare ed eseguire rapidamente query su database di documenti, coppie chiave/valore e grafi, sfruttando in ognuno dei casi i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Azure Cosmos DB. 

Questa guida introduttiva illustra come creare un account Azure Cosmos DB, un database e un grafo (contenitore) usando il portale di Azure e quindi usare la [console di Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) da [Apache TinkerPop](http://tinkerpop.apache.org) per usare i dati dell'API Graph (anteprima). Questa esercitazione descrive come creare ed eseguire query su vertici e archi, aggiornare la proprietà di un vertice, eseguire query sui vertici, attraversare il grafo ed eliminare un vertice.

![Azure Cosmos DB dalla console Apache Gremlin](./media/create-graph-gremlin-console/gremlin-console.png)

La console Gremlin è basata su Groovy/Java e può essere eseguita su Linux, Mac e Windows. È possibile scaricare la console dal [sito Apache TinkerPop](https://www.apache.org/dyn/closer.lua/tinkerpop/3.2.4/apache-tinkerpop-gremlin-console-3.2.4-bin.zip).

## <a name="prerequisites"></a>Prerequisiti

Per creare un account Azure Cosmos DB per questa guida di avvio rapido, è necessaria una sottoscrizione di Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

È anche necessario installare la [console Gremlin](http://tinkerpop.apache.org/). Usare la versione 3.2.4 o successive.

## <a name="create-a-database-account"></a>Creare un account di database

[!INCLUDE [cosmosdb-create-dbaccount-graph](../../includes/cosmosdb-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Aggiungere un grafo

[!INCLUDE [cosmosdb-create-graph](../../includes/cosmosdb-create-graph.md)]

## <a id="ConnectAppService"></a>Connettersi al servizio app
1. Prima di avviare la console Gremlin, creare o modificare il file di configurazione *remote-secure.yaml* nella directory *apache-tinkerpop-gremlin-console-3.2.4/conf*.
2. Immettere le configurazioni per *Hosts*, *Port*, *Username*, *Password*, *ConnectionPool* e *Serializer*:

    Impostazione|Valore consigliato|Descrizione
    ---|---|---
    Hosts|***.graphs.azure.com|URI del servizio Graph, che può essere recuperato dal portale di Azure
    Port|443|Impostare su 443
    Username|*Nome utente*|Risorsa in formato `/dbs/<db>/colls/<coll>`.
    Password|*Chiave master primaria*|Chiave master primaria per Azure Cosmos DB
    ConnectionPool|{enableSsl: true}|Impostazione del pool di connessione per SSL
    Serializer|{ className:org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|Impostare su questo valore

3. Nel terminale eseguire *bin/gremlin.bat* o *bin/gremlin.sh* per avviare la [console Gremlin](http://tinkerpop.apache.org/docs/3.2.4/tutorials/getting-started/).
4. Nel terminale eseguire *:remote connect tinkerpop.server conf/remote-secure.yaml* per connettersi al servizio app.

L'installazione è riuscita. Al termine della configurazione, è possibile iniziare a eseguire alcuni comandi della console.

## <a name="create-vertices-and-edges"></a>Creare vertici e archi

Per iniziare, aggiungere quattro vertici per le persone *Thomas*, *Mary Kay*, *Robin* e *Ben*.

Input (Thomas):

```
:> g.addV('person').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Output:

```
==>[id:1eb91f79-94d7-4fd4-b026-18f707952f21,label:person,type:vertex,properties:[firstName:[[id:ec5fcfbe-040e-48c3-b961-31233c8b1801,value:Thomas]],lastName:[[id:86e5b580-0bca-4bc2-bc53-a46f92c1a182,value:Andersen]],age:[[id:2caeab3c-c66d-4098-b673-40a8101bb72a,value:44]]]]
```
Input (Mary Kay):

```
:> g.addV('person').property('firstName', 'Mary Kay').property('lastName', 'Andersen').property('age', 39)
```

Output:

```
==>[id:899a9d37-6701-48fc-b0a1-90950be7e0f4,label:person,type:vertex,properties:[firstName:[[id:c79c5599-8646-47d1-9a49-3456200518ce,value:Mary Kay]],lastName:[[id:c1362095-9dcc-479d-ab21-86c1b6d4ffc1,value:Andersen]],age:[[id:0b530408-bfae-4e8f-98ad-c160cd6e6a8f,value:39]]]]
```

Input (Robin):

```
:> g.addV('person').property('firstName', 'Robin').property('lastName', 'Wakefield')
```

Output:

```
==>[id:953aefd9-5a54-4033-9b3a-d4dc3049f720,label:person,type:vertex,properties:[firstName:[[id:bbda02e0-8a96-4ca1-943e-621acbb26824,value:Robin]],lastName:[[id:f0291ad3-05a3-40ec-aabb-6538a7c331e3,value:Wakefield]]]]
```

Input (Ben):

```
:> g.addV('person').property('firstName', 'Ben').property('lastName', 'Miller')
```

Output:

```
==>[id:81c891d9-beca-4c87-9009-13a826c9ed9a,label:person,type:vertex,properties:[firstName:[[id:3a3b53d3-888c-46da-bb54-1c42194b1e18,value:Ben]],lastName:[[id:48c6dd50-79c4-4585-ab71-3bf998061958,value:Miller]]]]
```

Aggiungere quindi gli archi per le relazioni tra le persone.

Input (Thomas -> Mary Kay):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Mary Kay'))
```

Output:

```
==>[id:c12bf9fb-96a1-4cb7-a3f8-431e196e702f,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:0d1fa428-780c-49a5-bd3a-a68d96391d5c,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Input (Thomas -> Robin):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Robin'))
```

Output:

```
==>[id:58319bdd-1d3e-4f17-a106-0ddf18719d15,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:3e324073-ccfc-4ae1-8675-d450858ca116,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Input (Robin -> Ben):

```
:> g.V().hasLabel('person').has('firstName', 'Robin').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Ben'))
```

Output:

```
==>[id:889c4d3c-549e-4d35-bc21-a3d1bfa11e00,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:40fd641d-546e-412a-abcc-58fe53891aab,outV:3e324073-ccfc-4ae1-8675-d450858ca116]
```

## <a name="update-a-vertex"></a>Aggiornare un vertice

Aggiornare il vertice *Thomas* con la nuova età *45*.

Input:
```
:> g.V().hasLabel('person').has('firstName', 'Thomas').property('age', 45)
```
Output:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

## <a name="query-your-graph"></a>Eseguire query sul grafo

È ora possibile eseguire diverse query sul grafo.

Prima di tutto, provare una query con un filtro per restituire solo le persone con età maggiore di 40 anni.

Input (query con filtro):

```
:> g.V().hasLabel('person').has('age', gt(40))
```

Output:

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

Proiettare quindi il nome delle persone con età maggiore di 40 anni.

Input (query con filtro + query di proiezione):

```
:> g.V().hasLabel('person').has('age', gt(40)).values('firstName')
```

Output:

```
==>Thomas
```

## <a name="traverse-your-graph"></a>Attraversare il grafo

È ora possibile attraversare il grafo per restituire tutti gli amici di Thomas.

Input (amici di Thomas):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person')
```

Output: 

```
==>[id:f04bc00b-cb56-46c4-a3bb-a5870c42f7ff,label:person,type:vertex,properties:[firstName:[[id:14feedec-b070-444e-b544-62be15c7167c,value:Mary Kay]],lastName:[[id:107ab421-7208-45d4-b969-bbc54481992a,value:Andersen]],age:[[id:4b08d6e4-58f5-45df-8e69-6b790b692e0a,value:39]]]]
==>[id:91605c63-4988-4b60-9a30-5144719ae326,label:person,type:vertex,properties:[firstName:[[id:f760e0e6-652a-481a-92b0-1767d9bf372e,value:Robin]],lastName:[[id:352a4caa-bad6-47e3-a7dc-90ff342cf870,value:Wakefield]]]]
```

Ottenere quindi il livello successivo dei vertici. Attraversare il grafo per restituire tutti gli amici degli amici di Thomas.

Input (amici degli amici di Thomas):

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```
Output:

```
==>[id:a801a0cb-ee85-44ee-a502-271685ef212e,label:person,type:vertex,properties:[firstName:[[id:b9489902-d29a-4673-8c09-c2b3fe7f8b94,value:Ben]],lastName:[[id:e084f933-9a4b-4dbc-8273-f0171265cf1d,value:Miller]]]]
```

## <a name="drop-a-vertex"></a>Eliminare un vertice

Verrà ora eliminato un vertice dal database del grafo.

Input (eliminazione del vertice Robin):

```
:> g.V().hasLabel('person').has('firstName', 'Robin').drop()
```

## <a name="clear-your-graph"></a>Cancellare il grafo

Infine, è possibile cancellare il database di tutti i vertici e gli archi.

Input:

```
:> g.V().drop()
```

Congratulazioni. Questa esercitazione sull'API Graph di Azure Cosmos DB è stata completata.

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare l'app, eliminare tutte le risorse create tramite questa guida di avvio rapido nel portale di Azure eseguendo questi passaggi:  

1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi fare clic sul nome della risorsa creata. 
2. Nella pagina del gruppo di risorse fare clic su **Elimina**, digitare il nome della risorsa da eliminare nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un account Azure Cosmos DB, come creare un grafo con Esplora dati, come creare vertici e archi e come attraversare il grafo con la console Gremlin. È ora possibile creare query più complesse e implementare la potente logica di attraversamento dei grafi usando Gremlin. 

> [!div class="nextstepaction"]
> [Eseguire query con Gremlin](tutorial-query-graph.md)

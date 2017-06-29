---
title: 'Esercitazione su Azure Cosmos DB: Creare, eseguire query e attraversare nella console Gremlin di Apache TinkerPop | Microsoft Docs'
description: Guida di avvio rapido ad Azure Cosmos DB per creare vertici, archi e query usando l&quot;API Graph di Azure Cosmos DB.
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: bf08e031-718a-4a2a-89d6-91e12ff8797d
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: terminal
ms.topic: hero-article
ms.date: 06/10/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: 44972270a13f5ab5b3aa22557b36e80ae406a4a6
ms.contentlocale: it-it
ms.lasthandoff: 06/13/2017

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

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Aggiungere un grafo

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

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

Provare un comando count () semplice. Digitare quanto segue al prompt nella console:
```
:> g.V().count()
```

> [!TIP]
> Si noti il prefisso ***:>*** che precede il testo e g.V().count(). 
>
> Si tratta di una parte del comando che è necessario digitare. È importante quando si usa la console Gremlin con Azure Cosmos DB.  
>
> Se il prefisso :> viene omesso, la console esegue il comando in locale, spesso su un grafo in memoria.
> L'uso di ***:>*** indica alla console di eseguire un comando remoto, in questo caso su Cosmos DB (l'emulatore localhost o un'istanza di Azure).


## <a name="create-vertices-and-edges"></a>Creare vertici e archi

Per iniziare, aggiungere cinque vertici per le persone per *Thomas*, *Mary Kay*, *Robin*, *Ben* e *Jack*.

Input (Thomas):

```
:> g.addV('person').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44).property('userid', 1)
```

Output:

```
==>[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d,label:person,type:vertex,properties:[firstName:[[id:f02a749f-b67c-4016-850e-910242d68953,value:Thomas]],lastName:[[id:f5fa3126-8818-4fda-88b0-9bb55145ce5c,value:Andersen]],age:[[id:f6390f9c-e563-433e-acbf-25627628016e,value:44]],userid:[[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d|userid,value:1]]]]
```
Input (Mary Kay):

```
:> g.addV('person').property('firstName', 'Mary Kay').property('lastName', 'Andersen').property('age', 39).property('userid', 2)

```

Output:

```
==>[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e,label:person,type:vertex,properties:[firstName:[[id:ea0604f8-14ee-4513-a48a-1734a1f28dc0,value:Mary Kay]],lastName:[[id:86d3bba5-fd60-4856-9396-c195ef7d7f4b,value:Andersen]],age:[[id:bc81b78d-30c4-4e03-8f40-50f72eb5f6da,value:39]],userid:[[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e|userid,value:2]]]]

```

Input (Robin):

```
:> g.addV('person').property('firstName', 'Robin').property('lastName', 'Wakefield').property('userid', 3)
```

Output:

```
==>[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e,label:person,type:vertex,properties:[firstName:[[id:ec65f078-7a43-4cbe-bc06-e50f2640dc4e,value:Robin]],lastName:[[id:a3937d07-0e88-45d3-a442-26fcdfb042ce,value:Wakefield]],userid:[[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e|userid,value:3]]]]
```

Input (Ben):

```
:> g.addV('person').property('firstName', 'Ben').property('lastName', 'Miller').property('userid', 4)

```

Output:

```
==>[id:ee86b670-4d24-4966-9a39-30529284b66f,label:person,type:vertex,properties:[firstName:[[id:a632469b-30fc-4157-840c-b80260871e9a,value:Ben]],lastName:[[id:4a08d307-0719-47c6-84ae-1b0b06630928,value:Miller]],userid:[[id:ee86b670-4d24-4966-9a39-30529284b66f|userid,value:4]]]]
```

Input (Jack):

```
:> g.addV('person').property('firstName', 'Jack').property('lastName', 'Connor').property('userid', 5)
```

Output:

```
==>[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469,label:person,type:vertex,properties:[firstName:[[id:4250824e-4b72-417f-af98-8034aa15559f,value:Jack]],lastName:[[id:44c1d5e1-a831-480a-bf94-5167d133549e,value:Connor]],userid:[[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469|userid,value:5]]]]
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

Input (eliminazione del vertice Jack):

```
:> g.V().hasLabel('person').has('firstName', 'Jack').drop()
```

## <a name="clear-your-graph"></a>Cancellare il grafo

Infine, è possibile cancellare il database di tutti i vertici e gli archi.

Input:

```
:> g.E().drop()
:> g.V().drop()
```

Congratulazioni. Questa esercitazione sull'API Graph di Azure Cosmos DB è stata completata.

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare l'app, eliminare tutte le risorse create tramite questa guida di avvio rapido nel portale di Azure eseguendo questi passaggi:  

1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi fare clic sul nome della risorsa creata. 
2. Nella pagina del gruppo di risorse fare clic su **Elimina**, digitare il nome della risorsa da eliminare nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un account Azure Cosmos DB, come creare un grafo con Esplora dati, come creare vertici e archi e come attraversare il grafo con la console Gremlin. È ora possibile creare query più complesse e implementare la potente logica di attraversamento dei grafi usando Gremlin. 

> [!div class="nextstepaction"]
> [Eseguire query con Gremlin](tutorial-query-graph.md)


---
title: 'Guida introduttiva: Come usare Cache Redis di Azure con Java | Microsoft Docs'
description: In questa guida introduttiva si creerà una nuova app Java che usa Cache Redis di Azure
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 29275a5e-2e39-4ef2-804f-7ecc5161eab9
ms.service: cache
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: azure-cache-for-redis
ms.workload: tbd
ms.date: 05/23/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 0a407cdfd6786848825a114ff5667ccaff91fe71
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556874"
---
# <a name="quickstart-how-to-use-azure-cache-for-redis-with-java"></a>Guida introduttiva: Come usare Cache Redis di Azure con Java


Cache Redis di Azure consente di accedere a un'istanza di Cache Redis di Azure dedicata, gestita da Microsoft. È possibile accedere alla cache da qualsiasi applicazione in Microsoft Azure.

Questo articolo illustra come iniziare a usare Cache Redis di Azure con il client Redis [Jedis](https://github.com/xetorthio/jedis) per Java.

![App cache completata](./media/cache-java-get-started/cache-app-complete.png)

Per completare i passaggi descritti in questa guida di avvio rapido è possibile usare qualsiasi editor di codice. Tuttavia, [Visual Studio Code](https://code.visualstudio.com/) è un'ottima scelta per le piattaforme Windows, macOS e Linux.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Prerequisiti

[Apache Maven](http://maven.apache.org/)



## <a name="create-an-azure-cache-for-redis"></a>Creare un'istanza di Cache Redis di Azure

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Aggiungere le variabili di ambiente per il **nome host** e la chiave di accesso **primaria**. Queste variabili verranno usate dal codice invece di includere le informazioni riservate direttamente nel codice.

```
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

## <a name="create-a-new-java-app"></a>Creare una nuova app Java

Usare Maven e generare una nuova app per la guida introduttiva:

```
mvn archetype:generate -DarchetypeGroupId=org.apache.maven.archetypes -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.3 -DgroupId=example.demo -DartifactId=redistest -Dversion=1.0
```

Passare alla nuova directory di progetto *redistest*.

Aprire il file *pom.xml* e aggiungere una dipendenza per [Jedis](https://github.com/xetorthio/jedis):

```xml
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>2.9.0</version>
        <type>jar</type>
        <scope>compile</scope>
    </dependency>
```

Salvare il file *pom.xml* .

Aprire *App.java* e sostituire il codice con il codice seguente:

```java
package example.demo;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisShardInfo;

/**
 * Redis test
 *
 */
public class App 
{
    public static void main( String[] args )
    {

        boolean useSsl = true;
        String cacheHostname = System.getenv("REDISCACHEHOSTNAME");
        String cachekey = System.getenv("REDISCACHEKEY");

        // Connect to the Azure Cache for Redis over the SSL port using the key.
        JedisShardInfo shardInfo = new JedisShardInfo(cacheHostname, 6380, useSsl);
        shardInfo.setPassword(cachekey); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);      

        // Perform cache operations using the cache connection object...

        // Simple PING command        
        System.out.println( "\nCache Command  : Ping" );
        System.out.println( "Cache Response : " + jedis.ping());

        // Simple get and put of integral data types into the cache
        System.out.println( "\nCache Command  : GET Message" );
        System.out.println( "Cache Response : " + jedis.get("Message"));

        System.out.println( "\nCache Command  : SET Message" );
        System.out.println( "Cache Response : " + jedis.set("Message", "Hello! The cache is working from Java!"));

        // Demonstrate "SET Message" executed as expected...
        System.out.println( "\nCache Command  : GET Message" );
        System.out.println( "Cache Response : " + jedis.get("Message"));

        // Get the client list, useful to see if connection list is growing...
        System.out.println( "\nCache Command  : CLIENT LIST" );
        System.out.println( "Cache Response : " + jedis.clientList());

        jedis.close();
    }
}
```

Questo codice mostra come connettersi a un'istanza di Cache Redis di Azure usando il nome host della cache e le variabili di ambiente chiave. Il codice, inoltre, archivia e recupera un valore stringa nella cache. Vengono anche eseguiti i comandi `PING` e `CLIENT LIST`. 

Salvare *App.java*.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

Eseguire il comando Maven seguente per compilare ed eseguire l'app:

```
mvn compile
mvn exec:java -D exec.mainClass=example.demo.App
```

Nell'esempio seguente è possibile notare che la chiave `Message` in precedenza aveva un valore nella cache che era stato impostato usando la Console Redis nel portale di Azure. L'app ha aggiornato questo valore memorizzato nella cache. L'app ha anche eseguito i comandi `PING` e `CLIENT LIST`.

![App cache completata](./media/cache-java-get-started/cache-app-complete.png)


## <a name="clean-up-resources"></a>Pulire le risorse

Se si proseguirà con l'esercitazione successiva, sarà possibile conservare le risorse create in questo avvio rapido e riutilizzarle.

In caso contrario, se si è terminato il lavoro con l'applicazione di esempio di avvio rapido, è possibile eliminare le risorse di Azure create in questo avvio rapido per evitare i costi correlati. 

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile e comporta l'eliminazione definitiva del gruppo di risorse e di tutte le risorse incluse nel gruppo. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. Se le risorse per questo esempio sono state create all'interno di un gruppo di risorse esistente che contiene anche elementi da mantenere, è possibile eliminare ogni elemento singolarmente dai rispettivi pannelli anziché eliminare il gruppo di risorse.
>

Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Gruppi di risorse**.

Nella casella di testo **Filtra per nome...** immettere il nome del gruppo di risorse. Le istruzioni di questo articolo usano un gruppo di risorse denominato *TestResources*. Nel gruppo di risorse nell'elenco dei risultati fare clic su **...** quindi su **Elimina gruppo di risorse**.

![Delete](./media/cache-java-get-started/cache-delete-resource-group.png)

Verrà chiesto di confermare l'eliminazione del gruppo di risorse. Immettere il nome del gruppo di risorse per confermare e fare clic su **Elimina**.

Dopo qualche istante il gruppo di risorse e tutte le risorse che contiene vengono eliminati.



## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è visto come usare Cache Redis di Azure da un'applicazione Java. Continuare con la guida introduttiva successiva per usare Cache Redis di Azure con un'app Web ASP.NET.

> [!div class="nextstepaction"]
> [Creare un'app Web ASP.NET che usa Cache Redis di Azure.](./cache-web-app-howto.md)




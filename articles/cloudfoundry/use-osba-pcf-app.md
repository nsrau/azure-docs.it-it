---
title: Usare un Open Service Broker per il database di Azure con un'applicazione in Pivotal Cloud Foundry
author: zr-msft
manager: jeconnoc
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 01/11/2019
ms.topic: tutorial
description: Viene illustrato come configurare un'applicazione di Pivotal Cloud Foundry per usare un Open Service Broker per il database di Azure
keywords: Pivotal Cloud Foundry, Cloud Foundry, Open Service Broker, Open Service Broker for Azure
tags: Cloud-Foundry
ms.openlocfilehash: d553cd09ef42e47e3a10fb96039063b8aae665cb
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258873"
---
# <a name="tutorial-use-an-open-service-broker-for-azure-database-with-an-application-on-pivotal-cloud-foundry"></a>Esercitazione: Usare un Open Service Broker per il database di Azure con un'applicazione in Pivotal Cloud Foundry

L'uso di Open Service Broker for Azure con un'istanza di Pivotal Cloud Foundry consente di effettuare il provisioning di servizi, quali ad esempio i database, in Azure direttamente dall'interfaccia della riga di comando di Cloud Foundry e dall'istanza di Pivotal Cloud Foundry. È anche possibile associare tali servizi a un'applicazione in esecuzione nell'istanza di Pivotal Cloud Foundry. Quando si esegue l'associazione di un'applicazione a un servizio in questo modo, non è necessario aggiornare alcun codice o configurazione all'interno dell'applicazione. Questo articolo illustra come usare un servizio Open Service Broker for Azure per un database con un'applicazione in Pivotal Cloud Foundry.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Preparare lo spazio dell'applicazione in Pivotal Cloud Foundry.
> * Clonare l'origine di un'applicazione di esempio da GitHub.
> * Preparare l'applicazione per la distribuzione.
> * Distribuire l'applicazione.
> * Creare un database usando Open Service Broker for Azure.
> * Associare il database all'applicazione.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter procedere, è necessario:

* [Avere installato e configurato Pivotal Cloud Foundry](create-cloud-foundry-on-azure.md)
* [Avere installato e configurato Open Service Broker for Azure](https://network.pivotal.io/products/azure-open-service-broker-pcf) con l'istanza di Cloud Foundry

Di seguito è riportato un esempio della schermata di Pivotal Cloud Foundry Ops Manager con il riquadro di Open Service Broker for Azure installato e configurato:

![Pivotal Cloud Foundry con Open Service Broker for Azure installato](media/pcf-ops-manager.png)

## <a name="prepare-your-application-space-in-pivotal-cloud-foundry"></a>Preparare lo spazio dell'applicazione in Pivotal Cloud Foundry

Per distribuire l'applicazione nell'istanza di Pivotal Cloud Foundry, è necessario avere eseguito l'accesso con lo strumento da riga di comando `cf`. È inoltre necessario disporre dell'organizzazione e dello spazio desiderati.

Per verificare di aver eseguito l'accesso e di visualizzare lo spazio desiderato, usare `cf target`. L'esempio seguente mostra l'utente già connesso come *admin*, con l'organizzazione *myorg* e lo spazio *dev*:

```cmd
$ cf target

api endpoint:   https://api.system.40.85.111.222.cf.pcfazure.com
api version:    2.120.0
user:           admin
org:            myorg
space:          dev
```

Per effettuare l'accesso, usare `cf login`:

```cmd
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Per creare una nuova organizzazione e un nuovo spazio, usare `cf create-org` e `cf create-space`:

```cmd 
cf create-org myorg
cf create-space dev -o myorg
```

Per specificare uno spazio, usare `cf target`:

```cmd
cf target -o myorg -s dev
```

## <a name="get-application-code"></a>Ottenere il codice dell'applicazione

Affinché possa usare Open Service Broker for Azure, un'applicazione deve usare un'origine dati come ad esempio un database. In questo articolo si userà l'[applicazione di esempio spring music in Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) per illustrare un'applicazione che usa un'origine dati.

Clonare l'applicazione da GitHub e passare alla relativa directory:

```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring music
```

> [!TIP]
> Per visualizzare le origini dati per questa applicazione, aprire il file [src/main/resources/application.yml](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/application.yml).


## <a name="prepare-the-application-for-deployment"></a>Preparare l'applicazione per la distribuzione

Prima di poter distribuire l'applicazione nell'istanza di Pivotal Cloud Foundry, è necessario compilarla. A scopo dimostrativo, viene anche abilitata una registrazione *DEBUG* per registrare le informazioni di connessione dell'origine dati.

Per abilitare la registrazione *DEBUG* per i dettagli di connessione del database, aggiungere la proprietà yaml seguente alla fine del file *application.yml*:

```yaml
---
logging:
  level:
    com.zaxxer.hikari: DEBUG
```

L'applicazione di esempio usa gradle per compilare l'applicazione in un file jar eseguibile di Spring Boot. Il file jar eseguibile verrà distribuito nell'istanza di Pivotal Cloud Foundry. Per compilare l'applicazione:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```


## <a name="deploy-your-application"></a>Distribuire l'applicazione

Usare il comando `cf push` per distribuire l'applicazione nell'istanza di Pivotal Cloud Foundry:

```cmd
cf push

Pushing from manifest to org myorg / space dev as admin...
Using manifest file /path/to/spring-music/manifest.yml
Getting app info...
Creating app with these attributes...
+ name:       spring-music
...
Waiting for app to start...

name:              spring-music
requested state:   started
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
     state     since                  cpu    memory         disk           details
#0   running   2018-12-06T21:24:06Z   0.0%   313.3M of 1G   170.7M of 1G
```

Copiare il valore di *routes* visualizzato nell'output di `cf push`. La route è l'URL che verrà usato per accedere all'applicazione in esecuzione. Ad esempio: 

```cmd
...
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
```


Dopo aver distribuito l'applicazione, è possibile visualizzare i log dell'applicazione per vedere l'URL di connessione usato dall'applicazione. Il comando seguente consente di visualizzare i log dell'applicazione e usa `grep` per cercare la configurazione *jdbcUrl*.

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
```

Si noti che l'applicazione usa *h2:mem:testdb*, ovvero il database in memoria. Un'applicazione Spring viene configurata automaticamente per usare un database in memoria quando nel classpath è presente una dipendenza dal database in memoria ed è abilitata la [configurazione automatica](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-auto-configuration.html). L'applicazione di esempio ha la [dipendenza dal database in memoria h2 configurata](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L49) e la configurazione automatica abilitata in [src/main/java/org/cloudfoundry/samples/music/Application.java](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/java/org/cloudfoundry/samples/music/Application.java#L8).

Usare la route dell'applicazione aprirla in un browser. La route, o l'URL, viene visualizzata nell'output del comando `cf push`.

> [!TIP]
> È inoltre possibile visualizzare l'URL dell'applicazione eseguendo `cf apps`.

Quando si apre l'applicazione usando il browser, interagire con l'applicazione eliminando alcuni album esistenti e creandone di nuovi. L'applicazione di esempio usa il database in memoria per salvare le modifiche. Si noterà anche che l'applicazione è stata popolata con alcuni [dati predefiniti](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). 

![App Spring Music con i dati predefiniti](media/music-app.png)

Poiché l'applicazione usa un database in memoria, le modifiche non saranno salvate in modo permanente se l'applicazione viene riavviata o ridistribuita. Per mostrare che le modifiche non vengono salvate in modo permanente, dopo avere apportato alcune modifiche, ripristinare l'applicazione con `cf restage`:

```cmd
cf restage spring-music
```

Dopo il ripristino dell'applicazione, aprirla in un browser usando lo stesso URL. Si noti che le modifiche apportate non ci sono più e vengono visualizzati i dati predefiniti.

![App Spring Music con i dati predefiniti](media/music-app.png)

## <a name="create-a-database"></a>Creare un database

Per creare un database permanente in Azure con Open Service Broker, usare il comando `cf create-service`. Il comando seguente effettua il provisioning di un database PostgreSQL in Azure nel gruppo di risorse *MyResourceGroup* nell'area *eastus*. Altre informazioni sui parametri *resourceGroup*, *location* e altri parametri JSON specifici di Azure sono disponibili nella [documentazione di riferimento del modulo PostgreSQL](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md#provision):

```cmd
cf create-service azure-postgresql-9-6 general-purpose mypgsql -c '{"resourceGroup":"MyResourceGroup", "location":"eastus"}'
```

Il database viene esposto nell'istanza di Pivotal Cloud Foundry come servizio denominato *mypgsql*. Dopo avere completato il provisioning del database, operazione che dovrebbe richiedere alcuni minuti, è possibile associarlo all'applicazione. Per verificare il completamento del provisioning del database, usare il comando `cf services`:

```cmd
cf services

Getting services in org myorg / space dev as admin...

name      service                plan              bound apps     last operation
mypgsql   azure-postgresql-9-6   general-purpose                  create succeeded
```

Dopo il completamento del provisioning il valore dell'*ultima operazione* del servizio sarà *creazione completata*.

## <a name="bind-the-database-to-your-application"></a>Associare il database all'applicazione

Usare il comando `bind-service` per associare il servizio all'applicazione.

```cmd
cf bind-service spring-music mypgsql
```

Dopo aver associato il servizio all'applicazione, è necessario ripristinare l'applicazione per rendere effettive le modifiche.

```cmd
cf restage spring-music
```

L'applicazione è [configurata per l'uso di Spring Cloud Connectors](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L45...L46), che consente all'istanza di Pivotal Cloud Foundry di usare la [riconfigurazione automatica](https://docs.cloudfoundry.org/buildpacks/java/configuring-service-connections/spring-service-bindings.html#auto) nell'origine dati dell'applicazione. In questo caso, quando l'applicazione viene ripristinata, l'istanza di Pivotal Cloud Foundry riconfigurerà automaticamente l'applicazione per usare un servizio a cui è associata per un'origine dati.

Dopo il ripristino l'applicazione userà *mypgsql* per l'archiviazione dei dati anziché il database in memoria.

Le modifiche apportate verranno ora salvate in modo permanente e non andranno perse in caso di riavvio o ridistribuzione. È anche possibile vedere l'URL di connessione usato dall'applicazione visualizzando di nuovo i log dell'applicazione.

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
2018-12-07T14:48:58.10-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:48:58.107 DEBUG 16 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:postgresql://12345678-aaaa-bbbb-cccc-1234567890ab.postgres.database.azure.com:5432/123456789?user=aabbcc1122@12345678-aaaa-bbbb-cccc-1234567890ab&password=<masked>&&sslmode=require
```

Si noti che sono presenti due voci:

* Il valore originale di *h2:mem:testdb*.
* Il valore aggiornato per il database PostgreSQL quando l'applicazione è stata ripristinata.

Per verificare che i dati vengono salvati in modo permanente nel database PostgreSQL, aprire l'applicazione nel browser, apportare alcune modifiche, quindi ripristinare l'applicazione:

```cmd
cf restage spring-music
```

Dopo il ripristino dell'applicazione, aprirla in un browser usando lo stesso URL. Si noti che le modifiche apportate sono ancora presenti.

## <a name="cleanup"></a>Pulizia

Se si vuole disconnettere l'applicazione dal database, è possibile annullarne l'associazione con il comando `cf unbind-service`.

```cmd
cf unbind-service spring-music mypgsql
```

Analogamente all'associazione di un'applicazione a un servizio, è necessario ripristinare l'applicazione per rendere effettive queste modifiche. Questa azione lascerà intatti sia *mypgsql* che l'applicazione, ma l'applicazione inizierà a usare il database in memoria anziché *mypgsql*.

Per eliminare il database, è possibile usare il comando `cf delete-service`. *Non è possibile annullare questa azione, pertanto assicurarsi di voler eliminare il database prima di procedere.*

```cmd
cf delete-service mypgsql
```

Per rimuovere l'applicazione dall'istanza di Pivotal Cloud Foundry:
 
```cmd
cf delete spring-music
```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato la distribuzione di un'applicazione in Pivotal Cloud Foundry, nonché la creazione di un database usando Open Service Broker for Azure. Ha illustrato anche l'associazione del database a un'applicazione all'interno dell'istanza di Pivotal Cloud Foundry. Per altre informazioni sulla distribuzione di applicazioni in Cloud Foundry in Azure, vedere:

* [Cloud Foundry in Azure](../virtual-machines/linux/cloudfoundry-get-started.md)
* [Distribuire la prima app a Cloud Foundry in Microsoft Azure](../virtual-machines/linux/cloudfoundry-deploy-your-first-app.md)
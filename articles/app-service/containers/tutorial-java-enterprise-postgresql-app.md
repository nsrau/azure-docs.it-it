---
title: Creare un'app Web Java Enterprise nel servizio app di Azure in Linux | Microsoft Docs
description: Informazioni su come ottenere un'app Java Enterprise da usare in Wildfly nel servizio app di Azure in Linux.
author: JasonFreeberg
manager: routlaw
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.openlocfilehash: 40bee31b7880a323a48e92912ee323c43c3a97da
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634782"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>Esercitazione: Creare un'app Web Java EE e Postgres in Azure

Questa esercitazione illustra come creare un'app Web Java Enterprise Edition (EE) nel servizio app di Azure e connetterla a un database Postgres. Al termine, si avrà un'applicazione [WildFly](http://www.wildfly.org/about/) che archivia i dati in [Database di Azure per PostgreSQL](https://azure.microsoft.com/services/postgresql/) in esecuzione nel [servizio app di Azure per Linux](app-service-linux-intro.md).

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Distribuire un'app Java EE in Azure con Maven
> * Creare un database Postgres in Azure
> * Configurare il server WildFly per l'uso di Postgres
> * Aggiornare e ridistribuire l'app
> * Eseguire unit test in WildFly

## <a name="prerequisites"></a>Prerequisiti

1. [Scaricare e installare Git](https://git-scm.com/)
1. [Scaricare e installare Maven 3](https://maven.apache.org/install.html)
1. [Scaricare e installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>Clonare e modificare l'app di esempio

In questo passaggio si clonerà l'applicazione di esempio e si configurerà il modello a oggetti di progetto (pom.xml o POM, Project Object Model) di Maven per la distribuzione.

### <a name="clone-the-sample"></a>Clonare l'esempio

Nella finestra del terminale passare alla directory di lavoro e clonare il [repository di esempio](https://github.com/Azure-Samples/wildfly-petstore-quickstart).

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>Aggiornare il modello a oggetti di progetto di Maven

Aggiornare il modello a oggetti di progetto di Maven con il nome e il gruppo di risorse del servizio app desiderati. Questi valori verranno inseriti nel plug-in Azure, riportato più avanti nel file _pom.xml_. Non è necessario creare prima l'istanza o il piano di servizio app. Il gruppo di risorse e il servizio app verranno creati dal plug-in Maven, se non esistono già.

Sostituire i segnaposto con i nomi di risorsa desiderati:
```xml
<azure.plugin.appname>YOUR_APP_NAME</azure.plugin.appname>
<azure.plugin.resourcegroup>YOUR_RESOURCE_GROUP</azure.plugin.resourcegroup>
```

È possibile scorrere verso il basso fino alla sezione `<plugins>` di _pom.xml_ per controllare il plug-in Azure.

## <a name="build-and-deploy-the-application"></a>Compilare e distribuire l'applicazione

Si userà ora Maven per compilare l'applicazione e distribuirla nel servizio app.

### <a name="build-the-war-file"></a>Compilare il file con estensione war

Il modello a oggetti di questo progetto è configurato per creare il pacchetto dell'applicazione in un file WAR (Web Archive). Compilare l'applicazione con Maven:

```bash
mvn clean install -DskipTests
```

I test case in questa applicazione sono progettati per essere eseguiti quando l'applicazione viene distribuita in WildFly. Verranno ora ignorati per eseguire la compilazione in locale e verranno eseguiti al momento della distribuzione dell'applicazione nel servizio app.

### <a name="deploy-to-app-service"></a>Eseguire la distribuzione nel servizio app

Ora che il file WAR è pronto, è possibile usare il plug-in Azure per la distribuzione nel servizio app:

```bash
mvn azure-webapp:deploy
```

Al termine della distribuzione, procedere al passaggio successivo.

### <a name="create-a-record"></a>Creare un record

Aprire un browser e passare a `https://<your_app_name>.azurewebsites.net/`. La distribuzione di un'applicazione Java EE nel servizio app di Azure è stata completata.

A questo punto, l'applicazione usa un database H2 in memoria. Fare clic su "Admin" (Amministrazione) sulla barra di spostamento e creare una nuova categoria. Il record nel database in memoria andrà perso in caso di riavvio dell'istanza del servizio app. Nei passaggi seguenti si risolverà questo problema effettuando il provisioning di un database Postgres in Azure e configurando WildFly per usare tale database.

![Posizione del pulsante Admin (Amministrazione)](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>Effettuare il provisioning di un database Postgres

Per effettuare il provisioning di un server di database Postgres, aprire un terminale ed eseguire il comando seguente con i valori desiderati per nome del server, nome utente, password e località. Usare lo stesso gruppo di risorse in cui si trova il servizio app. Prendere nota della password per usarla in seguito.

```bash
az postgres server create -n <desired-name> -g <same-resource-group> --sku-name GP_Gen4_2 -u <desired-username> -p <desired-password> -l <location>
```

Passare al portale e cercare il database Postgres. Quando viene visualizzato il pannello, copiare i valori di "Nome server" e "Nome di accesso dell'amministratore server", perché saranno necessari in seguito.

### <a name="allow-access-to-azure-services"></a>Consentire l'accesso ai servizi di Azure

Nel pannello **Sicurezza delle connessioni** del pannello del database di Azure, impostare il pulsante di "Consenti l'accesso a Servizi di Azure" su **SÌ**.

![Consentire l'accesso ai servizi di Azure](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>Aggiornare l'app Java per Postgres

Si apporteranno ora alcune modifiche all'applicazione Java per consentire l'uso del database Postgres.

### <a name="add-postgres-credentials-to-the-pom"></a>Aggiungere le credenziali di Postgres al modello a oggetti di progetto

In _pom.xml_ sostituire i valori segnaposto con il nome del server, il nome di accesso dell'amministratore e la password di Postgres. Questi valori verranno inseriti come variabili di ambiente nell'istanza del servizio app durante la ridistribuzione dell'applicazione.

```xml
<azure.plugin.postgres-server-name>SERVER_NAME</azure.plugin.postgres-server-name>
<azure.plugin.postgres-username>USERNAME@FIRST_PART_OF_SERVER_NAME</azure.plugin.postgres-username>
<azure.plugin.postgres-password>PASSWORD</azure.plugin.postgres-password>
```

### <a name="update-the-java-transaction-api"></a>Aggiornare Java Transaction API

Successivamente, è necessario modificare la configurazione di Java Transaction API (JPA) in modo che l'applicazione Java comunichi con Postgres anziché con il database H2 in memoria usato in precedenza. Aprire _src/main/resources/META-INF/persistence.xml_ in un editor. Sostituire il valore di `<jta-data-source>` con `java:jboss/datasources/postgresDS`. Il file XML di JTA conterrà ora questa impostazione:

```xml
...
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
...
```

## <a name="configure-the-wildfly-application-server"></a>Configurare il server applicazioni WildFly

Prima di distribuire l'applicazione riconfigurata, è necessario aggiornare il server applicazioni WildFly con il modulo Postgres e le relative dipendenze. Per configurare il server, saranno necessari i quattro file presenti nella directory `wildfly_config/`.

- **postgresql-42.2.5.jar**: questo file JAR è il driver JDBC per Postgres. Per altre informazioni, vedere il [sito Web ufficiale](https://jdbc.postgresql.org/index.html).
- **postgres-module.xml**: questo file XML dichiara un nome per il modulo Postgres (org.postgres). Specifica anche le risorse e le dipendenze necessarie per usare il modulo.
- **jboss_cli_commands.cl**: questo file contiene i comandi di configurazione che verranno eseguiti dall'interfaccia della riga di comando di JBoss. I comandi aggiungono il modulo Postgres al server applicazioni WildFly, specificano le credenziali, dichiarano un nome JNDI, impostano la soglia di timeout e così via. Se non si ha familiarità con l'interfaccia della riga di comando di JBoss, vedere la [documentazione ufficiale](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- **startup_script.sh**: questo script della shell, infine, verrà eseguito a ogni avvio dell'istanza del servizio app. Lo script esegue una sola funzione: il piping dei comandi di `jboss_cli_commands.cli` all'interfaccia della riga di comando di JBoss.

È consigliabile leggere il contenuto di questi file, soprattutto di _jboss_cli_commands.cli_.

### <a name="ftp-the-configuration-files"></a>Distribuire tramite FTP i file di configurazione

È necessario distribuire tramite FTP il contenuto di `wildfly_config/` all'istanza del servizio app. Per ottenere le credenziali FTP, fare clic sul pulsante **Recupera profilo di pubblicazione** nel pannello del servizio app nel portale di Azure. Il nome utente e la password FTP saranno riportati nel documento XML scaricato. Per altre informazioni sul profilo di pubblicazione, vedere [questo documento](https://docs.microsoft.com/azure/app-service/app-service-deployment-credentials).

Usando uno strumento FTP di propria scelta, trasferire i quattro file di `wildfly_config/` in `/home/site/deployments/tools/`. Si noti che si devono trasferire solo i file, non la directory.


### <a name="finalize-app-service"></a>Finalizzare il servizio app

Nel pannello del servizio app passare al pannello "Impostazione applicazione". In "Runtime" impostare il campo "File di avvio" su `/home/site/deployments/tools/startup_script.sh`. Questo garantirà l'esecuzione dello script della shell dopo la creazione dell'istanza del servizio app, ma prima dell'avvio del server WildFly.

Riavviare infine il servizio app. Il pulsante è disponibile nel pannello "Panoramica".

## <a name="redeploy-the-application"></a>Ridistribuire l'applicazione

In una finestra del terminale ricompilare e ridistribuire l'applicazione.

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

Congratulazioni! L'applicazione usa ora un database Postgres e tutti i record creati nell'applicazione verranno archiviati in Postgres, anziché nel precedente database H3 in memoria. Per verificare, è possibile creare un record e riavviare il servizio app. I record saranno ancora presenti al riavvio dell'applicazione.

## <a name="clean-up"></a>Eseguire la pulizia

Se queste risorse non sono necessarie per un'altra esercitazione (vedere Passaggi successivi), è possibile eliminarle eseguendo questo comando:

```bash
az group delete --name <your_resource_group> 
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si ha un'applicazione Java EE distribuita nel servizio app, vedere la [guida per sviluppatori Java Enterprise](https://aka.ms/wildfly-quickstart) per altre informazioni sulla configurazione dei servizi, la risoluzione dei problemi e il ridimensionamento dell'applicazione.
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
ms.custom: seodec18
ms.openlocfilehash: dcd1ef5c54885b758ac9a301616d79a163999bc9
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509627"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>Esercitazione: Compilare un'app Web Java EE e Postgres in Azure

Questa esercitazione illustra come creare un'app Web Java Enterprise Edition (EE) nel servizio app di Azure e connetterla a un database Postgres. Al termine, si avrà un'applicazione [WildFly](https://www.wildfly.org/about/) che archivia i dati in [Database di Azure per PostgreSQL](https://azure.microsoft.com/services/postgresql/) in esecuzione nel [servizio app di Azure in Linux](app-service-linux-intro.md).

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Distribuire un'app Java EE in Azure con Maven
> * Creare un database Postgres in Azure
> * Configurare il server WildFly per l'uso di Postgres
> * Aggiornare e ridistribuire l'app
> * Eseguire unit test in WildFly

## <a name="prerequisites"></a>Prerequisiti

1. [Scaricare e installare Git](https://git-scm.com/)
2. [Scaricare e installare Maven 3](https://maven.apache.org/install.html)
3. [Scaricare e installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>Clonare e modificare l'app di esempio

In questo passaggio si clonerà l'applicazione di esempio e si configurerà il modello a oggetti di progetto (*pom.xml* o POM, Project Object Model) di Maven per la distribuzione.

### <a name="clone-the-sample"></a>Clonare l'esempio

Nella finestra del terminale passare alla directory di lavoro e clonare il [repository di esempio](https://github.com/Azure-Samples/wildfly-petstore-quickstart).

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>Aggiornare il modello a oggetti di progetto di Maven

Aggiornare il plug-in Maven Azure con il nome e il gruppo di risorse del servizio app desiderati. Non è necessario creare prima l'istanza o il piano di servizio app. Il gruppo di risorse e il servizio app verranno creati dal plug-in Maven, se non esistono già.

È possibile scorrere verso il basso fino alla sezione `<plugins>` di *pom.xml*, riga 200, per apportare le modifiche.

```xml
<!-- Azure App Service Maven plugin for deployment -->
<plugin>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-webapp-maven-plugin</artifactId>
  <version>${version.maven.azure.plugin}</version>
  <configuration>
    <appName>YOUR_APP_NAME</appName>
    <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup>
    <linuxRuntime>wildfly 14-jre8</linuxRuntime>
  ...
</plugin>  
```

Sostituire `YOUR_APP_NAME` e `YOUR_RESOURCE_GROUP` con i nomi del servizio app e del gruppo di risorse.

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

Per eseguire il provisioning di un server di database Postgres, aprire un terminale e usare il comando [az postgres server create](https://docs.microsoft.com/cli/azure/postgres/server) come illustrato nell'esempio seguente. Sostituire i segnaposto (incluse le parentesi graffe) con valori di propria scelta, usando lo stesso gruppo di risorse specificato in precedenza per l'istanza del Servizio app di Azure. Prendere nota delle credenziali di amministratore specificate, in quanto saranno necessarie per gli accessi futuri.

```bash
az postgres server create \
    --name <server name> \
    --resource-group <resource group> \
    --location <location>
    --sku-name GP_Gen5_2 \
    --admin-user <administrator username> \
    --admin-password <administrator password> \
```

Dopo aver eseguito questo comando, andare sul portale di Azure e passare al database Postgres. Quando viene visualizzato il pannello, copiare i valori di "Nome server" e "Nome di accesso dell'amministratore server", perché saranno necessari in seguito.

### <a name="allow-access-to-azure-services"></a>Consentire l'accesso ai servizi di Azure

Nel pannello **Sicurezza delle connessioni** del pannello del database di Azure, impostare il pulsante di "Consenti l'accesso a Servizi di Azure" su **SÌ**.

![Consentire l'accesso ai servizi di Azure](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>Aggiornare l'app Java per Postgres

Si apporteranno ora alcune modifiche all'applicazione Java per consentire l'uso del database Postgres.

### <a name="add-postgres-credentials-to-the-pom"></a>Aggiungere le credenziali di Postgres al modello a oggetti di progetto

In *pom.xml* sostituire i valori segnaposto in maiuscolo con il nome del server, il nome di accesso dell'amministratore e la password di Postgres. Questi campi si trovano all'interno del plug-in Maven Azure. Assicurarsi di sostituire `YOUR_SERVER_NAME`, `YOUR_PG_USERNAME` e `YOUR_PG_PASSWORD` nei tag `<value>`, non all'interno dei tag `<name>`.

```xml
<plugin>
      ...
      <appSettings>
      <property>
        <name>POSTGRES_CONNECTIONURL</name>
        <value>jdbc:postgresql://YOUR_SERVER_NAME:5432/postgres?ssl=true</value>
      </property>
      <property>
        <name>POSTGRES_USERNAME</name>
        <value>YOUR_PG_USERNAME</value>
      </property>
      <property>
        <name>POSTGRES_PASSWORD</name>
        <value>YOUR_PG_PASSWORD</value>
      </property>
    </appSettings>
  </configuration>
</plugin>
```

### <a name="update-the-java-transaction-api"></a>Aggiornare Java Transaction API

Successivamente, è necessario modificare la configurazione di Java Transaction API (JPA) in modo che l'applicazione Java comunichi con Postgres anziché con il database H2 in memoria usato in precedenza. Aprire *src/main/resources/META-INF/persistence.xml* in un editor. Sostituire il valore di `<jta-data-source>` con `java:jboss/datasources/postgresDS`. Il file XML di JTA conterrà ora questa impostazione:

```xml
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
```

## <a name="configure-the-wildfly-application-server"></a>Configurare il server applicazioni WildFly

Prima di distribuire l'applicazione riconfigurata, è necessario aggiornare il server applicazioni WildFly con il modulo Postgres e le relative dipendenze. Altre informazioni di configurazione sono reperibili in [Configure WildFly server](configure-language-java.md#configure-java-ee-wildfly) (Configurare il server WildFly).

Per configurare il server, saranno necessari i quattro file presenti nella directory *wildfly_config/* :

- **postgresql-42.2.5.jar**: questo file JAR è il driver JDBC per Postgres. Per altre informazioni, vedere il [sito Web ufficiale](https://jdbc.postgresql.org/index.html).
- **postgres-module.xml**: questo file XML dichiara un nome per il modulo Postgres (org.postgres). Specifica anche le risorse e le dipendenze necessarie per usare il modulo.
- **jboss_cli_commands.cl**: questo file contiene i comandi di configurazione che verranno eseguiti dall'interfaccia della riga di comando di JBoss. I comandi aggiungono il modulo Postgres al server applicazioni WildFly, specificano le credenziali, dichiarano un nome JNDI, impostano la soglia di timeout e così via. Se non si ha familiarità con l'interfaccia della riga di comando di JBoss, vedere la [documentazione ufficiale](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- **startup_script.sh**: questo script della shell, infine, verrà eseguito a ogni avvio dell'istanza del servizio app. Lo script esegue una sola funzione: il piping dei comandi di *jboss_cli_commands.cli* all'interfaccia della riga di comando di JBoss.

È consigliabile leggere il contenuto di questi file, soprattutto di *jboss_cli_commands.cli*.

### <a name="ftp-the-configuration-files"></a>Distribuire tramite FTP i file di configurazione

È necessario distribuire tramite FTP il contenuto di *wildfly_config/* all'istanza del servizio app. Per ottenere le credenziali FTP, fare clic sul pulsante **Recupera profilo di pubblicazione** nel pannello del servizio app nel portale di Azure. Il nome utente e la password FTP saranno riportati nel documento XML scaricato. Per altre informazioni sul profilo di pubblicazione, vedere [questo documento](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

Con uno strumento FTP di propria scelta, trasferire i quattro file da *wildfly_config /* a */home/site/deployments/tools/* . Si noti che si devono trasferire solo i file, non la directory.

### <a name="finalize-app-service"></a>Finalizzare il servizio app

Nel pannello del servizio app passare al pannello "Impostazione applicazione". In "Runtime", impostare il campo "File di avvio" su */home/site/deployments/tools/startup_script.sh*. Questo garantirà l'esecuzione dello script della shell dopo la creazione dell'istanza del servizio app, ma prima dell'avvio del server WildFly.

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
az group delete --name <your-resource-group>
```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Distribuire un'app Java EE in Azure con Maven
> * Creare un database Postgres in Azure
> * Configurare il server WildFly per l'uso di Postgres
> * Aggiornare e ridistribuire l'app
> * Eseguire unit test in WildFly

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato all'app.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il mapping di un nome DNS personalizzato all'app Web](../app-service-web-tutorial-custom-domain.md)

In alternativa, consultare altre risorse:

> [!div class="nextstepaction"]
> [Configurare l'app Java](configure-language-java.md)

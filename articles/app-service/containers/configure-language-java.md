---
title: Configurare le app Linux Java - servizio App di Azure | Microsoft Docs
description: Informazioni su come configurare app Java in esecuzione nel Servizio app di Azure in Linux.
keywords: servizio app di Azure, app web, linux, oss, java, java ee, jee, javaee
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/28/2019
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: 91368ac3b1d7948257fa9e55debc862567593425
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341378"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Configurare un'app Linux Java per servizio App di Azure

Il Servizio app di Azure in Linux consente agli sviluppatori Java di compilare, distribuire e ridimensionare rapidamente le applicazioni Web in pacchetto Tomcat o Java Standard Edition (SE) in un servizio basato su Linux completamente gestito. Distribuire le applicazioni con i plug-in Maven dalla riga di comando o in editor come IntelliJ, Eclipse o Visual Studio Code.

Questa guida fornisce i concetti chiave e le istruzioni per gli sviluppatori Java che usano un contenitore Linux incorporato nel servizio App. Se non si è mai usato il servizio App di Azure, seguire le [Guida introduttiva a Java](quickstart-java.md) e [Java con l'esercitazione PostgreSQL](tutorial-java-enterprise-postgresql-app.md) prima.

## <a name="deploying-your-app"></a>Distribuzione dell'app

È possibile usare [plug-in Maven per il servizio App di Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) per distribuire file con estensione jar sia con estensione WAR. La distribuzione con gli IDE più comuni è anche supportata con [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) oppure [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

In caso contrario, il metodo di distribuzione variano in base al tipo di archivio:

- Per distribuire file con estensione war in Tomcat, usare l'endpoint `/api/wardeploy/` per eseguire il comando POST per il file di archivio. Per altre informazioni su questa API, vedere [questa documentazione](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Per distribuire file con estensione jar nelle immagini di Java SE, usare l'endpoint `/api/zipdeploy/` del sito Kudu. Per altre informazioni su questa API, vedere [questa documentazione](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Non distribuire file con estensione war o jar tramite FTP. Lo strumento FTP è stato progettato per caricare script di avvio, dipendenze o altri file di runtime. Non è la scelta ottimale per la distribuzione di app Web.

## <a name="logging-and-debugging-apps"></a>Registrazione e debug delle app

Nel portale di Azure sono disponibili report sulle prestazioni, visualizzazioni del traffico e controlli dell'integrità per ogni app. Per altre informazioni, vedere [Panoramica della diagnostica del servizio App di Azure](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Accesso alla console SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Eseguire lo streaming dei log di diagnostica

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Per altre informazioni, vedere [Streaming dei log con l'interfaccia della riga di comando di Azure](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Registrazione dell'app

Abilitare la [registrazione dell'applicazione](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag) tramite il portale di Azure o l'[interfaccia della riga di comando di Azure](/cli/azure/webapp/log#az-webapp-log-config) per configurare il servizio app per scrivere l'output della console standard dell'applicazione e i flussi di errori della console standard nel file system locale o in Archiviazione BLOB di Azure. La registrazione nell'istanza del file system del servizio app locale viene disabilitata 12 ore dopo la configurazione. Se è necessario un periodo di conservazione più lungo, configurare l'applicazione per scrivere l'output in un contenitore di archiviazione BLOB. I registri delle applicazioni Java e Tomcat sono reperibile nella `/home/LogFiles/Application/` directory.

Se l'applicazione usa [Logback](https://logback.qos.ch/) o [Log4j](https://logging.apache.org/log4j) per la traccia, è possibile inoltrare queste tracce per la revisione ad Azure Application Insights usando le istruzioni di configurazione del framework di registrazione illustrate in [Esplorare i log di traccia Java in Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Strumenti di risoluzione dei problemi

Immagini predefinite Java si basano le [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) del sistema operativo. Usare il `apk` package manager di installare qualsiasi risoluzione dei problemi degli strumenti o comandi.

### <a name="flight-recorder"></a>Utilità traccia eventi

Tutte le immagini Linux Java nel servizio App hanno registratore di volo Zulu installata, pertanto è possibile facilmente connettersi a JVM e avviare un profiler di registrazione o generare un dump di heap.

#### <a name="timed-recording"></a>Registrazione programmata

Per ottenere avviato, il servizio App di connettersi tramite SSH ed eseguire il `jcmd` comando per visualizzare un elenco di tutti i processi di Java in esecuzione. Oltre a jcmd stesso, verrà visualizzata l'applicazione Java in esecuzione con un numero ID del processo (pid).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Eseguire il comando seguente per avviare una registrazione di 30 secondi di JVM. Verrà JVM di profilo e creare un file JFR denominato `jfr_example.jfr` nella home directory. (Sostituire 116 con il numero di serie dell'app Java).

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Durante l'intervallo di 30 secondi, è possibile convalidare la registrazione avviene eseguendo `jcmd 116 JFR.check`. Verranno visualizzate tutte le registrazioni per il processo Java specificato.

#### <a name="continuous-recording"></a>Registrazione continua

È possibile usare utilità traccia eventi Zulu continuamente profilare l'applicazione Java con un impatto minimo sulle prestazioni di runtime ([origine](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). A tale scopo, eseguire il comando di Azure per creare un'impostazione dell'App denominata JAVA_OPTS con la configurazione necessaria. Il contenuto dell'impostazione App JAVA_OPTS viene passato al `java` comando quando viene avviata l'app.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Per altre informazioni, vedere la [riferimenti ai comandi Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analyzing Recordings

Uso [FTPS](../deploy-ftp.md) per scaricare il file JFR nel computer locale. Per analizzare il file JFR, scaricare e installare [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Per istruzioni su Zulu Mission Control, vedere la [documentazione di Azul](https://docs.azul.com/zmc/) e il [le istruzioni di installazione](https://docs.microsoft.com/en-us/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Personalizzazione e ottimizzazione

Servizio App di Azure per Linux supporta la casella di ottimizzazione e personalizzazione tramite il portale di Azure e della riga di comando. Esaminare gli articoli seguenti per la configurazione dell'app web non Java specifico:

- [Configurare le impostazioni dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [Configurare un nome di dominio](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Abilitare SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Aggiungere una rete CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Configurare il sito Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Impostare le opzioni di runtime Java

Per impostare la memoria allocata o ad altre opzioni di runtime JVM negli ambienti di Tomcat e Java SE, creare un [impostazione dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) denominata `JAVA_OPTS` con le opzioni. Il servizio app in Linux passa questa impostazione come variabile di ambiente al runtime Java quando viene avviato.

Nel portale di Azure, sotto **Impostazioni applicazione** per l'app Web creare una nuova impostazione dell'app denominata `JAVA_OPTS` che includa le impostazioni aggiuntive, ad esempio `-Xms512m -Xmx1204m`.

Per configurare l'impostazione dell'app dal plug-in Maven, aggiungere i tag/valore dell'impostazione della sezione plug-in Azure. L'esempio seguente imposta una specifiche minime e massime dimensioni dell'heap Java:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Gli sviluppatori che eseguono una singola applicazione con uno slot di distribuzione nel piano di servizio app possono usare le opzioni seguenti:

- Istanze di file B1 e S1: `-Xms1024m -Xmx1024m`
- Istanze B2 e S2: `-Xms3072m -Xmx3072m`
- Istanze B3 e S3: `-Xms6144m -Xmx6144m`

Quando si ottimizzano le impostazioni heap delle applicazioni, esaminare i dettagli del piano di servizio app e tenere in considerazione le esigenze di più applicazioni e slot di distribuzione per trovare l'allocazione ottimale della memoria.

Se si sta distribuendo un'applicazione con estensione JAR, devono essere denominato `app.jar` in modo che l'immagine incorporata possa identificare correttamente l'app. (Il plug-in Maven esegue automaticamente questa ridenominazione). Se non si desidera rinominare un file JAR per `app.jar`, è possibile caricare uno script shell con il comando per eseguire un file JAR. Quindi incollare il percorso completo per questo script nella [File di avvio](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#startup-file) nella casella di testo nella sezione di configurazione del portale.

### <a name="turn-on-web-sockets"></a>Attivare i Web Socket

Attivare il supporto per i Web Socket nel portale di Azure in **Impostazioni applicazione** per l'applicazione. Sarà necessario riavviare l'applicazione per rendere effettiva l'impostazione.

Attivare il supporto per i Web Socket usando l'interfaccia della riga di comando di Azure con il comando seguente:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Riavviare quindi l'applicazione:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Impostare la codifica dei caratteri predefinita

Nel portale di Azure, sotto **Impostazioni applicazione** per l'app Web creare una nuova impostazione dell'app denominata `JAVA_OPTS` con il valore `-Dfile.encoding=UTF-8`.

In alternativa, è possibile configurare l'impostazione dell'app usando il plug-in Maven del servizio app. Aggiungere i tag name e value dell'impostazione nella configurazione del plug-in:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>Modificare il timeout di esecuzione automatica

Se l'applicazione Java è particolarmente grande, è necessario aumentare il limite di tempo di avvio. A questo scopo, creare un'impostazione dell'applicazione, `WEBSITES_CONTAINER_START_TIME_LIMIT` e impostarla sul numero di secondi che il servizio App deve attendere prima del timeout. Il valore massimo è `1800` secondi.

## <a name="secure-applications"></a>Proteggere le applicazioni

Le applicazioni Java in esecuzione nel servizio app per Linux hanno lo stesso set di [procedure consigliate per la sicurezza](/azure/security/security-paas-applications-using-app-services) delle altre applicazioni. 

### <a name="authenticate-users"></a>Autenticare gli utenti

Configurare l'autenticazione di app nel portale di Azure con il **autenticazione e autorizzazione** opzione. che consente di abilitare l'autenticazione usando Azure Active Directory o gli account di accesso ai social network, ad esempio Facebook, Google o GitHub. La configurazione nel portale di Azure funziona solo quando si configura un singolo provider di autenticazione. Per altre informazioni, vedere [Configurare un'applicazione dei servizi app per usare l'account di accesso di Azure Active Directory](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) e gli articoli correlati per gli altri provider di identità. Se è necessario abilitare più provider di accesso, seguire le istruzioni dell'articolo [Personalizzare l'autenticazione nel servizio app](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

#### <a name="tomcat"></a>Tomcat

L'applicazione di Tomcat possa accedere l'utente attestazioni direttamente dal servlet Tomcat eseguendo il cast all'entità dell'oggetto a un oggetto Map. L'oggetto Map eseguirà il mapping a una raccolta di attestazioni per il tipo di ogni tipo di attestazione. Nel codice seguente, `request` è un'istanza di `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

A questo punto è possibile esaminare il `Map` oggetto per tutte le attestazioni specifiche. Ad esempio, il frammento di codice seguente scorre tutti i tipi di attestazione e stampa il contenuto di ogni raccolta.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Per disconnettersi utenti ed eseguire altre azioni, vedere la documentazione relativa [utilizzo di autenticazione del servizio App e l'autorizzazione](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to). È anche disponibile documentazione ufficiale su Tomcat [HttpServletRequest interfaccia](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) e i relativi metodi. Il servlet seguente metodi sono inoltre idratati in base di configurazione del servizio App:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Per disabilitare questa funzionalità, creare un'impostazione applicazione denominata `WEBSITE_AUTH_SKIP_PRINCIPAL` con il valore `1`. Per disabilitare tutti i filtri di servlet aggiunti dal servizio App, creare un'impostazione denominata `WEBSITE_SKIP_FILTERS` con il valore `1`.

#### <a name="spring-boot"></a>Spring Boot

Gli sviluppatori Spring Boot possono usare l'[utilità di avvio Spring Boot per Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) per proteggere le applicazioni usando le familiari annotazioni e API di Spring Security. Assicurarsi di aumentare le dimensioni massime dell'intestazione nel file `application.properties`. È consigliabile il valore `16384`.

### <a name="configure-tlsssl"></a>Configurare TLS/SSL

Seguire le istruzioni illustrate in [Associare un certificato SSL personalizzato esistente](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) per caricare un certificato SSL esistente e associarlo al nome di dominio dell'applicazione. Per impostazione predefinita, l'applicazione consentirà ancora le connessioni HTTP. Seguire i passaggi specifici dell'esercitazione per applicare SSL e TLS.

### <a name="use-keyvault-references"></a>Usare i riferimenti di Key Vault

[Azure Key Vault](../../key-vault/key-vault-overview.md) consente una gestione centralizzata segreto con la cronologia di controllo e i criteri di accesso. È possibile archiviare segreti (ad esempio le password o le stringhe di connessione) in Key Vault e accedere a questi segreti dell'applicazione tramite le variabili di ambiente.

In primo luogo, seguire le istruzioni relative [concedere all'app l'accesso a Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e [effettua un riferimento KeyVault per il segreto in un'impostazione dell'applicazione](../app-service-key-vault-references.md#reference-syntax). È possibile convalidare che il riferimento alla chiave privata viene risolto mediante la stampa la variabile di ambiente durante l'accesso in modalità remota il terminale di servizio App.

Per inserire questi segreti nel file di configurazione Spring o Tomcat, usare la sintassi di inserimento variabile di ambiente (`${MY_ENV_VAR}`). Per i file di configurazione Spring, vedere la documentazione sulla [esternalizzati configurazioni](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

## <a name="configure-apm-platforms"></a>Configurare le piattaforme di Application Performance Monitoring

Questa sezione illustra come connettere le applicazioni Java distribuite nel servizio App di Azure in Linux con le prestazioni dell'applicazione NewRelic e AppDynamics monitoraggio piattaforme (APM).

[Configura New Relic](#configure-new-relic)
[Configura AppDynamics](#configure-appdynamics)

### <a name="configure-new-relic"></a>Configurare New Relic

1. Creare un account NewRelic in [NewRelic.com](https://newrelic.com/signup)
2. Scaricare l'agente Java da NewRelic; il nome sarà simile a `newrelic-java-x.x.x.zip`.
3. Copiare il codice di licenza, che sarà necessario per configurare l'agente in un secondo momento.
4. [Usare SSH per connettersi all'istanza del servizio App](app-service-linux-ssh-support.md) e creare una nuova directory `/home/site/wwwroot/apm`.
5. Caricare i file dell'agente Java NewRelic decompressi in una directory in `/home/site/wwwroot/apm`. I file per l'agente devono essere presenti in `/home/site/wwwroot/apm/newrelic`.
6. Modificare il file YAML in `/home/site/wwwroot/apm/newrelic/newrelic.yml` e sostituire il valore della licenza segnaposto con il proprio codice di licenza.
7. Nel portale di Azure passare all'applicazione nel servizio app e creare una nuova impostazione dell'applicazione.
    - Se l'app usa **Java SE**, creare una variabile di ambiente denominata `JAVA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se si usa **Tomcat**, creare una variabile di ambiente denominata `CATALINA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se si usa **WildFly**, vedere la documentazione di New Relic [qui](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) per indicazioni sull'installazione l'agente Java e la configurazione JBoss.
    - Se si dispone già di una variabile di ambiente per `JAVA_OPTS` oppure `CATALINA_OPTS`, aggiungere l'opzione `javaagent` alla fine del valore corrente.

### <a name="configure-appdynamics"></a>Configurare AppDynamics

1. Creare un account AppDynamics in [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Scaricare l'agente Java dal sito Web di AppDynamics, il nome file sarà simile a `AppServerAgent-x.x.x.xxxxx.zip`
1. [Usare SSH per connettersi all'istanza del servizio App](app-service-linux-ssh-support.md) e creare una nuova directory `/home/site/wwwroot/apm`.
1. Caricare i file dell'agente Java in una directory in `/home/site/wwwroot/apm`. I file per l'agente devono essere presenti in `/home/site/wwwroot/apm/appdynamics`.
1. Nel portale di Azure passare all'applicazione nel servizio app e creare una nuova impostazione dell'applicazione.
    - Se si usa **Java SE**, creare una variabile di ambiente denominata `JAVA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` in cui `<app-name>` è il nome del servizio app.
    - Se si usa **Tomcat**, creare una variabile di ambiente denominata `CATALINA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` in cui `<app-name>` è il nome del servizio app.
    - Se si usa **WildFly**, vedere la documentazione di AppDynamics [qui](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) per indicazioni sull'installazione l'agente Java e la configurazione JBoss.
    
## <a name="configure-jar-applications"></a>Configurare le applicazioni con estensione JAR

### <a name="starting-jar-apps"></a>Avvio delle App con estensione JAR

Per impostazione predefinita, servizio App si aspetta che l'applicazione con estensione JAR denominato `app.jar`. Se è presente il nome specificato, verrà eseguito automaticamente. Per gli utenti di Maven, è possibile impostare il nome del file JAR, includendo `<finalName>app</finalName>` nella `<build>` sezione il `pom.xml`. [È possibile eseguire la stessa in Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) impostando la `archiveFileName` proprietà.

Se si desidera utilizzare un nome diverso per un file JAR, è necessario fornire anche il [avvio comando](app-service-linux-faq.md#built-in-images) che esegue il file con estensione JAR. Ad esempio: `java -jar my-jar-app.jar`. È possibile impostare il valore per il comando di avvio nel portale di configurazione > Impostazioni generali, o con un'impostazione applicazione denominata `STARTUP_COMMAND`.

### <a name="server-port"></a>Porta del server

Servizio App Linux instrada le richieste in ingresso alla porta 80, in modo che l'applicazione deve essere in ascolto sulla porta 80. È possibile eseguire questa operazione nella configurazione dell'applicazione (ad esempio di Spring `application.properties` file), o il comando di avvio (ad esempio, `java -jar spring-app.jar --server.port=80`). Vedere la documentazione seguente relativa comuni Framework Java:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Play Framework](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Origini dati

### <a name="tomcat"></a>Tomcat

Queste istruzioni si applicano a tutte le connessioni di database. È necessario sostituire i segnaposto con il nome della classe del driver del database scelto e il file JAR. Viene fornita una tabella con i nomi delle classi e i download dei driver per i database più comuni.

| Database   | Nome della classe del driver                             | Driver JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Scaricare](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Scaricare](https://dev.mysql.com/downloads/connector/j/) (selezionare "Indipendente dalla piattaforma") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Scaricare](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Per configurare Tomcat per l'uso di Java Database Connectivity (JDBC) o l'API di persistenza di Java (JPA), personalizzare il `CATALINA_OPTS` variabile di ambiente che viene letto Tomcat all'avvio. Impostare questi valori tramite un'impostazione app nel [plug-in Maven del servizio app](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

O impostare le variabili di ambiente nel **Configuration** > **le impostazioni dell'applicazione** pagina nel portale di Azure.

Determinare quindi se l'origine dati deve essere disponibile per un'applicazione o per tutte le applicazioni in esecuzione nel servlet Tomcat.

#### <a name="application-level-data-sources"></a>Origini dati a livello di applicazione

1. Creare un file `context.xml` nella directory `META-INF/` del progetto. Creare la directory `META-INF/` se non esiste.

2. In `context.xml` aggiungere un elemento `Context` per collegare l'origine dati a un indirizzo JNDI. Sostituire il segnaposto `driverClassName` con il nome della classe del driver indicato nella tabella precedente.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection" 
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}" 
            password="${connURL}"
        />
    </Context>
    ```

3. Aggiornare il file `web.xml` dell'applicazione per usare l'origine dati nell'applicazione.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Risorse condivise a livello di server

1. Copiare il contenuto di `/usr/local/tomcat/conf` in `/home/tomcat/conf` nell'istanza del servizio app in Linux usando SSH se non è già disponibile una configurazione.
    ```
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. Aggiungere un elemento Context nell'elemento `<Server>` del file `server.xml`.

    ```xml
    <Server>
    ...
    <Context>
        <Resource
            name="jdbc/dbconnection" 
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}" 
            password="${connURL}"
        />
    </Context>
    ...
    </Server>
    ```

3. Aggiornare il file `web.xml` dell'applicazione per usare l'origine dati nell'applicazione.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Completare la configurazione

Infine, inserire i file di driver con estensione jar in classpath Tomcat e riavviare il servizio App.

1. Assicurarsi che i file driver JDBC siano disponibili per il caricatore di classe Tomcat inserendoli nella directory `/home/tomcat/lib`. Creare questa directory se non esiste già. Per caricare questi file nell'istanza del servizio app, seguire questa procedura:
    1. Nel [Cloud Shell](https://shell.azure.com), installare l'estensione App Web:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Eseguire il comando seguente per creare un tunnel SSH dal sistema locale nel servizio App:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Connettersi alla porta di tunneling locale con il client SFTP e caricare i file nella cartella `/home/tomcat/lib`.

    In alternativa, è possibile usare un client FTP per caricare il driver JDBC. Seguire queste [istruzioni per ottenere le credenziali FTP](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

2. Se è stata creata un'origine dati a livello di server, riavviare l'applicazione Linux del Servizio app. Tomcat reimposterà `CATALINA_HOME` su `/home/tomcat/conf` e userà la configurazione aggiornata.

### <a name="spring-boot"></a>Spring Boot

Per connettersi alle origini dati in applicazioni Spring Boot, è consigliabile creare stringhe di connessione e l'inserimento nel `application.properties` file.

1. Nella sezione "Configurazione" della pagina del servizio App, impostare un nome per la stringa, incollare la stringa di connessione JDBC nel campo del valore e impostare il tipo su "Custom". È facoltativamente possibile impostare questa stringa di connessione come impostazione slot.

    Questa stringa di connessione sia accessibile all'applicazione come variabile di ambiente denominata `CUSTOMCONNSTR_<your-string-name>`. Ad esempio, la stringa di connessione creata in precedenza verrà denominata `CUSTOMCONNSTR_exampledb`.

2. Nel `application.properties` file, fare riferimento a questa stringa di connessione con il nome di variabile di ambiente. Per questo esempio, utilizziamo la seguente.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Vedere le [documentazione sull'accesso ai dati di Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) e [esternalizzati configurazioni](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) per altre informazioni su questo argomento.

## <a name="configure-java-ee-wildfly"></a>Configurare Java EE (WildFly)

> [!NOTE]
> Java Enterprise Edition in servizio App Linux è attualmente in anteprima. Lo stack è **non** consigliato per la produzione rivolte al lavoro. informazioni sul nostro stack Java SE e Tomcat.

Servizio App di Azure in Linux consente agli sviluppatori Java per creare, distribuire e scalare le applicazioni aziendali Java (EE Java) in un servizio completamente gestito basato su Linux.  L'ambiente di runtime Java Enterprise sottostante è il server applicazioni open source [Wildfly](https://wildfly.org/).

[Scalabilità con il servizio App](#scale-with-app-service)
[configurazione del server applicazioni Personalizza](#customize-application-server-configuration)
[moduli e le dipendenze](#modules-and-dependencies)
[dati origini](#data-sources)
[consentire ai provider di messaggistica](#enable-messaging-providers)
[configurare la memorizzazione nella cache di gestione della sessione](#configure-session-management-caching)

### <a name="scale-with-app-service"></a>Ridimensionare con il Servizio app di Azure

Il server applicazioni WildFly del Servizio app di Azure per Linux viene eseguito in modalità autonoma, non in una configurazione di dominio. Quando si amplia il piano di Servizio app, ogni istanza di WildFly viene configurata come server autonomo.

 Per scalare l'applicazione orizzontalmente o verticalmente, si usano le [regole di scalabilità](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) e si [aumenta il numero di istanze](../web-sites-scale.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="customize-application-server-configuration"></a>Personalizzare la configurazione del server applicazioni

Le istanze dell'app Web sono senza stato, quindi ogni nuova istanza avviata deve essere configurata all'avvio per supportare la configurazione Wildfly richiesta dall'applicazione.
È possibile scrivere uno script Bash di avvio per chiamare l'interfaccia della riga di comando di WildFly per:

- Configurare le origini dati
- Configurare i provider di messaggistica
- Aggiungere altri moduli e altre dipendenze alla configurazione del server Wildfly.

 Lo script viene eseguito quando Wildfly è in esecuzione, ma prima dell'avvio dell'applicazione. È consigliabile che lo script usi l'[interfaccia della riga di comando di JBOSS](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) chiamata da `/opt/jboss/wildfly/bin/jboss-cli.sh` per configurare il server applicazioni con qualsiasi configurazione o modifica necessaria dopo l'avvio del server.

Non usare la modalità interattiva dell'interfaccia della riga di comando per configurare Wildfly. Si può invece specificare uno script di comandi all'interfaccia della riga di comando di JBoss usando il comando `--file`, ad esempio:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Caricare lo script di avvio in `/home/site/deployments/tools` nell'istanza del Servizio app di Azure. Per le istruzioni su come ottenere le credenziali FTP, vedere [questo documento](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#userscope).

Impostare il campo **Script di avvio** nel portale di Azure sul percorso dello script della shell di avvio, ad esempio `/home/site/deployments/tools/your-startup-script.sh`.

Fornire [le impostazioni dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) nella configurazione dell'applicazione per passare le variabili di ambiente da usare nello script. Le impostazioni dell'applicazione mantengono le stringhe di connessione e altri segreti necessari per configurare il controllo delle versioni dell'applicazione.

### <a name="modules-and-dependencies"></a>Moduli e dipendenze

Per installare i moduli e le relative dipendenze nella classpath di Wildfly tramite l'interfaccia della riga di comando di JBoss, sarà necessario creare i file seguenti nella directory specifica. Per alcuni moduli e dipendenze può essere necessario specificare una configurazione aggiuntiva, ad esempio la denominazione JNDI o un'altra configurazione specifica dell'API. L'elenco che segue include gli elementi minimi necessari per configurare una dipendenza nella maggior parte dei casi.

- Un [descrittore di modulo XML](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Questo file XML definisce il nome, gli attributi e le dipendenze del modulo. Questo [file module.xml di esempio](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) definisce un modulo Postgres, la relativa dipendenza JDBC del file JAR e altre dipendenze del modulo richieste.
- Le dipendenze del file con estensione JAR necessarie per il modulo.
- Uno script con i comandi dell'interfaccia della riga di comando di JBoss per configurare il nuovo modulo. Questo file conterrà i comandi che si indica all'interfaccia della riga di comando di JBoss di eseguire per configurare il server per usare la dipendenza. Per la documentazione sui comandi per aggiungere moduli, origini dati e provider di messaggistica, fare riferimento a [questo documento](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- Uno script Bash di avvio per chiamare l'interfaccia della riga di comando di JBoss ed eseguire lo script del passaggio precedente. Questo file viene eseguito quando l'istanza del Servizio app di Azure viene riavviata o quando viene effettuato il provisioning di nuove istanze durante un'operazione di scale-out. Questo script di avvio è usato per eseguire qualsiasi altra configurazione per l'applicazione man mano che i comandi di JBoss vengono passati all'interfaccia della riga di comando di JBoss. Questo file può essere come minimo un singolo comando per passare lo script di comandi della CLI di JBoss all'interfaccia della riga di comando di JBoss:

```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli`
```

Dopo avere creato il file e il contenuto per il modulo, seguire la procedura sottostante per aggiungere il modulo al server applicazioni Wildfly.

1. Trasferire tramite FTP i file in `/home/site/deployments/tools` nell'istanza del Servizio app di Azure. Per le istruzioni su come ottenere le credenziali FTP, vedere questo documento.
2. Nel **Configuration** > **impostazioni generali** campo pagina di Azure del portale, impostare "Script di avvio" nel percorso di script della shell di avvio, ad esempio `/home/site/deployments/tools/your-startup-script.sh` .
3. Riavviare l'istanza del servizio App, premere il **riavviare** pulsante il **Panoramica** sezione del portale o tramite la CLI di Azure.

### <a name="configure-data-source-connections"></a>Configurare connessioni a origini dati

Per configurare in Wildfly una connessione all'origine dati, seguire lo stesso processo descritto nella sezione precedente "Moduli e dipendenze". È possibile seguire gli stessi passaggi per qualsiasi servizio di database di Azure.

1. Scaricare il driver JDBC per la versione del database. Per praticità, ecco i driver per [Postgres](https://jdbc.postgresql.org/download.html) e [MySQL](https://dev.mysql.com/downloads/connector/j/). Decomprimere il pacchetto scaricato per ottenere il file con estensione jar.
2. Seguire i passaggi descritti in "Moduli e dipendenze" per creare e caricare il descrittore di modulo XML, lo script dell'interfaccia della riga di comando di JBoss, lo script di avvio e il file della dipendenza con estensione jar di JDBC.

Sono disponibili altre informazioni sulla configurazione di Wildfly con [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource) e [SQL Database](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898). È possibile usare queste istruzioni personalizzate con l'approccio generalizzato sopra indicato per aggiungere definizioni di origini dati al server.

### <a name="enable-messaging-providers"></a>Consentire ai provider di messaggistica

Per abilitare Beans basato su messaggi usando il bus di servizio come meccanismo di messaggistica:

1. Usare la [libreria di messaggistica di Apache QPId JMS](https://qpid.apache.org/proton/index.html). Includere questa dipendenza nel file pom.xml (o in un altro file di compilazione) per l'applicazione.

2. Creare le [risorse del bus di servizio](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Creare uno spazio dei nomi del bus di servizio di Azure, una coda all'interno di tale spazio dei nomi e criteri di accesso condiviso con capacità di invio e di ricezione.

3. Passare la chiave dei criteri di accesso condiviso al codice tramite la codifica URL della chiave primaria dei criteri o [usare il Service Bus SDK](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Seguire i passaggi descritti nella sezione "Moduli e dipendenze" con il descrittore del modulo XML, le dipendenze con estensione jar, i comandi dell'interfaccia della riga di comando di JBoss e lo script di avvio per il provider JMS. Oltre ai quattro file, sarà necessario creare un file XML che definisce il nome JNDI per l'argomento e la coda JMS. Vedere [questo repository](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig) per i file di configurazione di riferimento.

### <a name="configure-session-management-caching"></a>Configurare la cache per la gestione delle sessioni

Per impostazione predefinita, il Servizio app di Azure per Linux userà i cookie di affinità di sessione per garantire che le richieste dei client con sessioni esistenti vengano instradate alla stessa istanza dell'applicazione. Questo comportamento predefinito non richiede alcuna configurazione, ma presenta alcune limitazioni:

- Se un'istanza dell'applicazione viene riavviata o ridotta, lo stato della sessione utente nel server di applicazioni verrà perso.
- Se le applicazioni sono impostate su un timeout della sessione prolungato o un numero fisso di utenti, la ricezione del carico da parte delle nuove istanze scalate automaticamente può richiedere tempo poiché solo le nuove sessioni verranno instradate verso le istanze appena avviate.

È possibile configurare Wildfly in modo che usi un archivio delle sessioni esterne, ad esempio la [Cache Redis di Azure](/azure/azure-cache-for-redis/). Sarà necessario [disabilitare la configurazione di affinità di istanza ARR esistente](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) per disattivare l'instradamento basato sui cookie della sessione e consentire a un archivio delle sessioni di Wildfly di operare senza interferenze.

## <a name="docker-containers"></a>Contenitori Docker

Per usare nei contenitori il pacchetto JDK Zulu supportato da Azure, assicurarsi di eseguire il pull e di usare le immagini precompilate, come documentato nella [pagina di download di Azul Zulu Enterprise per Azure supportata](https://www.azul.com/downloads/azure-only/zulu/) o di usare gli esempi `Dockerfile` dal [repository GitHub Java di Microsoft](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Dichiarazione di supporto

### <a name="runtime-availability"></a>Disponibilità del runtime

Il servizio app per Linux supporta due runtime per l'hosting gestito delle applicazioni Web Java:

- Il [contenitore servlet Tomcat](https://tomcat.apache.org/) per l'esecuzione di applicazioni in pacchetti come file di archivio Web (WAR). Sono supportate le versioni 8.5 e 9.0.
- Ambiente di runtime Java SE per l'esecuzione di applicazioni in pacchetti come file di archivio Java (JAR). Versioni supportate sono Java 8 e 11.

### <a name="jdk-versions-and-maintenance"></a>Versioni di JDK e manutenzione

Le build Azul Zulu Enterprise di OpenJDK sono distribuzioni di OpenJDK gratuite, multipiattaforma e pronte per la produzione per Azure e Azure Stack, supportate da Microsoft e Azul Systems. Contengono tutti i componenti necessari per compilare ed eseguire applicazioni Java SE. È possibile installare il pacchetto JDK da [installazione di JDK di Java](https://aka.ms/azure-jdks).

Ai pacchetti JDK supportati vengono automaticamente applicate patch con cadenza trimestrale, a gennaio, aprile, luglio e ottobre di ogni anno.

### <a name="security-updates"></a>Aggiornamenti della sicurezza

Le patch e le correzioni per le principali vulnerabilità della sicurezza verranno rilasciate da Azul Systems non appena disponibili. Una vulnerabilità "principale" viene definita da un punteggio di base pari o superiore a 9,0 in [NIST Common Vulnerability Scoring System, versione 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Deprecazione e ritiro

Se un runtime Java supportato sarà ritirato, gli sviluppatori di Azure che usano il runtime interessato riceveranno un avviso di funzionalità deprecata almeno sei mesi prima che il runtime venga ritirato.

## <a name="next-steps"></a>Passaggi successivi

Per trovare guide introduttive di Azure, esercitazioni e documentazione di riferimento su Java, visitare la pagina [Azure per sviluppatori Java](/java/azure/).

Per domande generali sull'uso del servizio app per Linux, non specifiche dello sviluppo Java, vedere le [domande frequenti sul servizio app in Linux](app-service-linux-faq.md).


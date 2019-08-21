---
title: Configurare app java per Linux-servizio app Azure | Microsoft Docs
description: Informazioni su come configurare app Java in esecuzione nel Servizio app di Azure in Linux.
keywords: servizio app di Azure, app Web, Linux, OSS, Java, Java EE, JEE, JavaEE
services: app-service
author: bmitchell287
manager: douge
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 06/26/2019
ms.author: brendm
ms.custom: seodec18
ms.openlocfilehash: 07d44bb54c288202d571f8e664822ecf9b4998be
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639768"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Configurare un'app Java Linux per il servizio app Azure

Il Servizio app di Azure in Linux consente agli sviluppatori Java di compilare, distribuire e ridimensionare rapidamente le applicazioni Web in pacchetto Tomcat o Java Standard Edition (SE) in un servizio basato su Linux completamente gestito. Distribuire le applicazioni con i plug-in Maven dalla riga di comando o in editor come IntelliJ, Eclipse o Visual Studio Code.

Questa guida fornisce i concetti chiave e le istruzioni per gli sviluppatori Java che usano un contenitore Linux incorporato nel servizio app. Se non si è mai usato app Azure servizio, seguire prima l'[esercitazione introduttiva su Java](quickstart-java.md) e [Java con PostgreSQL](tutorial-java-enterprise-postgresql-app.md).

## <a name="deploying-your-app"></a>Distribuzione dell'app

È possibile usare il plug-in [Maven per il servizio app Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) per distribuire file con estensione jar e War. La distribuzione con IDE comuni è supportata anche con [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) o [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

In caso contrario, il metodo di distribuzione dipenderà dal tipo di archivio:

- Per distribuire file con estensione war in Tomcat, usare l'endpoint `/api/wardeploy/` per eseguire il comando POST per il file di archivio. Per altre informazioni su questa API, vedere [questa documentazione](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Per distribuire file con estensione jar nelle immagini di Java SE, usare l'endpoint `/api/zipdeploy/` del sito Kudu. Per altre informazioni su questa API, vedere [questa documentazione](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Non distribuire file con estensione war o jar tramite FTP. Lo strumento FTP è stato progettato per caricare script di avvio, dipendenze o altri file di runtime. Non è la scelta ottimale per la distribuzione di app Web.

## <a name="logging-and-debugging-apps"></a>Registrazione e debug delle app

Nel portale di Azure sono disponibili report sulle prestazioni, visualizzazioni del traffico e controlli dell'integrità per ogni app. Per ulteriori informazioni, vedere [Panoramica di diagnostica del servizio app Azure](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Accesso alla console SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Eseguire lo streaming dei log di diagnostica

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Per altre informazioni, vedere [Streaming dei log con l'interfaccia della riga di comando di Azure](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Registrazione dell'app

Abilitare la [registrazione dell'applicazione](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag) tramite il portale di Azure o l'[interfaccia della riga di comando di Azure](/cli/azure/webapp/log#az-webapp-log-config) per configurare il servizio app per scrivere l'output della console standard dell'applicazione e i flussi di errori della console standard nel file system locale o in Archiviazione BLOB di Azure. La registrazione nell'istanza del file system del servizio app locale viene disabilitata 12 ore dopo la configurazione. Se è necessario un periodo di conservazione più lungo, configurare l'applicazione per scrivere l'output in un contenitore di archiviazione BLOB. I log delle app Java e Tomcat sono reperibili nella directory */Home/LogFiles/Application/*

Se l'applicazione usa [Logback](https://logback.qos.ch/) o [Log4j](https://logging.apache.org/log4j) per la traccia, è possibile inoltrare queste tracce per la revisione ad Azure Application Insights usando le istruzioni di configurazione del framework di registrazione illustrate in [Esplorare i log di traccia Java in Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Strumenti per la risoluzione dei problemi

Le immagini Java predefinite sono basate sul sistema operativo [alpino Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) . Usare gestione `apk` pacchetti per installare gli strumenti o i comandi per la risoluzione dei problemi.

### <a name="flight-recorder"></a>Utilità traccia eventi

Per tutte le immagini Java Linux nel servizio app è installato Zulu Flight Recorder, in modo che sia possibile connettersi facilmente a JVM e avviare una registrazione del profiler o generare un dump dell'heap.

#### <a name="timed-recording"></a>Registrazione temporizzata

Per iniziare, connettersi tramite SSH al servizio app ed eseguire il `jcmd` comando per visualizzare un elenco di tutti i processi Java in esecuzione. Oltre a jcmd, l'applicazione Java dovrebbe essere in esecuzione con un numero ID processo (PID).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Eseguire il comando seguente per avviare una registrazione di 30 secondi della JVM. Questa operazione produrrà la JVM e creerà un file JFR denominato *jfr_example. JFR* nella Home Directory. Sostituire 116 con il PID dell'app java.

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Durante l'intervallo di `jcmd 116 JFR.check`30 secondi, è possibile verificare che la registrazione avvenga eseguendo. Vengono visualizzate tutte le registrazioni per il processo Java specificato.

#### <a name="continuous-recording"></a>Registrazione continua

È possibile usare lo strumento di registrazione dei voli Zulu per profilare costantemente l'applicazione Java con un effetto minimo sulle prestazioni di runtime ([origine](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). A tale scopo, eseguire il comando dell'interfaccia della riga di comando di Azure seguente per creare un'impostazione dell'app denominata JAVA_OPTS con la configurazione necessaria. Il contenuto dell'impostazione dell'app JAVA_OPTS viene passato al `java` comando quando l'app viene avviata.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Una volta avviata la registrazione, è possibile scaricare i dati di registrazione correnti in qualsiasi momento `JFR.dump` utilizzando il comando.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Per ulteriori informazioni, vedere la Guida di [riferimento ai comandi di Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analisi delle registrazioni

Usare [FTPS](../deploy-ftp.md) per scaricare il file JFR nel computer locale. Per analizzare il file JFR, scaricare e installare [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Per istruzioni su Zulu Mission Control, vedere la [documentazione di Azul](https://docs.azul.com/zmc/) e le [istruzioni per l'installazione](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Personalizzazione e ottimizzazione

Il servizio app Azure per Linux supporta l'ottimizzazione e la personalizzazione predefinite tramite l'portale di Azure e l'interfaccia della riga di comando. Vedere gli articoli seguenti per la configurazione di app Web non specifiche per Java:

- [Configurare le impostazioni dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [Configurare un nome di dominio](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Abilitare SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Aggiungere una rete CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Configurare il sito Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Impostare le opzioni di runtime Java

Per impostare la memoria allocata o altre opzioni di runtime JVM negli ambienti Tomcat e Java se, creare un'impostazione dell' `JAVA_OPTS` [app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) denominata con le opzioni. Il servizio app in Linux passa questa impostazione come variabile di ambiente al runtime Java quando viene avviato.

Nel portale di Azure, sotto **Impostazioni applicazione** per l'app Web creare una nuova impostazione dell'app denominata `JAVA_OPTS` che includa le impostazioni aggiuntive, ad esempio `-Xms512m -Xmx1204m`.

Per configurare l'impostazione dell'app dal plug-in Maven, aggiungere i tag setting/value nella sezione plug-in di Azure. Nell'esempio seguente vengono impostate le dimensioni minime e massime heap Java specifiche:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Gli sviluppatori che eseguono una singola applicazione con uno slot di distribuzione nel piano di servizio app possono usare le opzioni seguenti:

- Istanze B1 e S1:`-Xms1024m -Xmx1024m`
- Istanze B2 e S2:`-Xms3072m -Xmx3072m`
- Istanze B3 e S3:`-Xms6144m -Xmx6144m`

Quando si ottimizzano le impostazioni heap delle applicazioni, esaminare i dettagli del piano di servizio app e tenere in considerazione le esigenze di più applicazioni e slot di distribuzione per trovare l'allocazione ottimale della memoria.

Se si distribuisce un'applicazione JAR, questa deve essere denominata *app. jar* , in modo che l'immagine incorporata possa identificare correttamente l'app. Il plug-in Maven viene rinominato automaticamente. Se non si vuole rinominare il file JAR in *app. jar*, è possibile caricare uno script della shell con il comando per eseguire il file jar. Incollare quindi il percorso completo di questo script nella casella di testo [file di avvio](app-service-linux-faq.md#built-in-images) nella sezione configurazione del portale. Lo script di avvio non viene eseguito dalla directory in cui si trova. Pertanto, utilizzare sempre i percorsi assoluti per fare riferimento ai file nello script di avvio ( `java -jar /home/myapp/myapp.jar`ad esempio:).

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

### <a name="adjust-startup-timeout"></a>Regola Timeout avvio

Se l'applicazione Java è particolarmente grande, è necessario aumentare il limite di tempo di avvio. A tale scopo, creare un'impostazione `WEBSITES_CONTAINER_START_TIME_LIMIT` dell'applicazione e impostarla sul numero di secondi di attesa del servizio app prima del timeout. Il valore massimo è `1800` secondi.

### <a name="pre-compile-jsp-files"></a>Pre-compila file JSP

Per migliorare le prestazioni delle applicazioni Tomcat, è possibile compilare i file JSP prima della distribuzione nel servizio app. È possibile usare il [plug](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) -in Maven fornito da Apache Sling o usando questo [file di compilazione Ant](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Proteggere le applicazioni

Le applicazioni Java in esecuzione nel servizio app per Linux hanno lo stesso set di [procedure consigliate per la sicurezza](/azure/security/security-paas-applications-using-app-services) delle altre applicazioni.

### <a name="authenticate-users-easy-auth"></a>Autenticare gli utenti (Easy auth)

Configurare l'autenticazione delle app nel portale di Azure con l'opzione di **autenticazione e autorizzazione** . che consente di abilitare l'autenticazione usando Azure Active Directory o gli account di accesso ai social network, ad esempio Facebook, Google o GitHub. La configurazione nel portale di Azure funziona solo quando si configura un singolo provider di autenticazione. Per altre informazioni, vedere [Configurare un'applicazione dei servizi app per usare l'account di accesso di Azure Active Directory](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) e gli articoli correlati per gli altri provider di identità. Se è necessario abilitare più provider di accesso, seguire le istruzioni dell'articolo [Personalizzare l'autenticazione nel servizio app](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

#### <a name="tomcat-and-wildfly"></a>Tomcat e Wildfly

L'applicazione Tomcat o Wildfly può accedere alle attestazioni dell'utente direttamente dalla servlet eseguendo il cast dell'oggetto Principal a un oggetto map. L'oggetto map eseguirà il mapping di ogni tipo di attestazione a una raccolta di attestazioni per quel tipo. Nel codice riportato di seguito `request` , è un'istanza `HttpServletRequest`di.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

A questo punto è possibile `Map` esaminare l'oggetto per qualsiasi attestazione specifica. Il frammento di codice seguente, ad esempio, scorre tutti i tipi di attestazione e stampa il contenuto di ogni raccolta.

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

Per disconnettersi gli utenti, usare `/.auth/ext/logout` il percorso. Per eseguire altre azioni, vedere la documentazione sull' [utilizzo dell'autenticazione e dell'autorizzazione del servizio app](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to). È disponibile anche la documentazione ufficiale sull' [interfaccia HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) di Tomcat e sui relativi metodi. Anche i seguenti metodi servlet vengono idratati in base alla configurazione del servizio app:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Per disabilitare questa funzionalità, creare un'impostazione dell'applicazione `WEBSITE_AUTH_SKIP_PRINCIPAL` denominata con un valore `1`di. Per disabilitare tutti i filtri servlet aggiunti dal servizio app, creare un'impostazione `WEBSITE_SKIP_FILTERS` denominata con un `1`valore.

#### <a name="spring-boot"></a>Spring Boot

Gli sviluppatori Spring Boot possono usare l'[utilità di avvio Spring Boot per Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) per proteggere le applicazioni usando le familiari annotazioni e API di Spring Security. Assicurarsi di aumentare le dimensioni massime dell'intestazione nel file *Application. Properties* . È consigliabile il valore `16384`.

### <a name="configure-tlsssl"></a>Configurare TLS/SSL

Seguire le istruzioni illustrate in [Associare un certificato SSL personalizzato esistente](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) per caricare un certificato SSL esistente e associarlo al nome di dominio dell'applicazione. Per impostazione predefinita, l'applicazione consentirà ancora le connessioni HTTP. Seguire i passaggi specifici dell'esercitazione per applicare SSL e TLS.

### <a name="use-keyvault-references"></a>Usare i riferimenti all'insieme di credenziali delle credenziali

[Azure chiave Vault](../../key-vault/key-vault-overview.md) fornisce una gestione centralizzata dei segreti con i criteri di accesso e la cronologia di controllo. È possibile archiviare segreti (ad esempio password o stringhe di connessione) nell'insieme di credenziali delle chiavi e accedere a questi segreti nell'applicazione tramite le variabili di ambiente.

Per prima cosa, seguire le istruzioni per [concedere all'app l'accesso a Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e [creare un riferimento a un insieme di credenziali delle credenziali al segreto in un'impostazione dell'applicazione](../app-service-key-vault-references.md#reference-syntax). È possibile verificare che il riferimento venga risolto nel segreto stampando la variabile di ambiente durante l'accesso remoto al terminale del servizio app.

Per inserire questi segreti nel file di configurazione di Spring o Tomcat, usare la sintassi di inserimento`${MY_ENV_VAR}`delle variabili di ambiente (). Per i file di configurazione della primavera, vedere la documentazione sulle [configurazioni esternalizzate](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

## <a name="configure-apm-platforms"></a>Configurare le piattaforme APM

Questa sezione illustra come connettere le applicazioni Java distribuite nel servizio app Azure in Linux con le piattaforme APM (Application Performance Monitoring) NewRelic e AppDynamics.

### <a name="configure-new-relic"></a>Configurare New Relic

1. Creare un account NewRelic in [NewRelic.com](https://newrelic.com/signup)
2. Scaricare l'agente Java da NewRelic. il nome del file sarà simile a quello di *newrelic-Java-x. x.x. x. zip*.
3. Copiare il codice di licenza, che sarà necessario per configurare l'agente in un secondo momento.
4. Usare [SSH nell'istanza del servizio app](app-service-linux-ssh-support.md) e creare una nuova directory */Home/site/wwwroot/APM*.
5. Caricare i file dell'agente Java NewRelic decompressi in una directory in */Home/site/wwwroot/APM*. I file per l'agente devono trovarsi in */Home/site/wwwroot/APM/newrelic*.
6. Modificare il file YAML in */Home/site/wwwroot/APM/newrelic/newrelic.yml* e sostituire il valore di licenza segnaposto con il proprio codice di licenza.
7. Nel portale di Azure passare all'applicazione nel servizio app e creare una nuova impostazione dell'applicazione.
    - Se l'app usa **Java SE**, creare una variabile di ambiente denominata `JAVA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se si usa **Tomcat**, creare una variabile di ambiente denominata `CATALINA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se si usa **WildFly**, vedere la documentazione di New Relic [qui](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) per istruzioni sull'installazione dell'agente Java e della configurazione di JBoss.

### <a name="configure-appdynamics"></a>Configurare AppDynamics

1. Creare un account AppDynamics in [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Scaricare l'agente Java dal sito Web AppDynamics. il nome del file sarà simile a *AppServerAgent-x. x.x. x. xxxxx. zip*
3. Usare [SSH nell'istanza del servizio app](app-service-linux-ssh-support.md) e creare una nuova directory */Home/site/wwwroot/APM*.
4. Caricare i file dell'agente Java in una directory in */Home/site/wwwroot/APM*. I file per l'agente devono trovarsi in */Home/site/wwwroot/APM/AppDynamics*.
5. Nel portale di Azure passare all'applicazione nel servizio app e creare una nuova impostazione dell'applicazione.
    - Se si usa **Java SE**, creare una variabile di ambiente denominata `JAVA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` in cui `<app-name>` è il nome del servizio app.
    - Se si usa **Tomcat**, creare una variabile di ambiente denominata `CATALINA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` in cui `<app-name>` è il nome del servizio app.
    - Se si usa **WildFly**, vedere la documentazione di AppDynamics [qui](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) per istruzioni sull'installazione dell'agente Java e della configurazione di JBoss.

>  Se si dispone già di una variabile di ambiente per `JAVA_OPTS` oppure `CATALINA_OPTS`, aggiungere l'opzione `-javaagent:/...` alla fine del valore corrente.

## <a name="configure-jar-applications"></a>Configurare le applicazioni JAR

### <a name="starting-jar-apps"></a>Avvio di app JAR

Per impostazione predefinita, il servizio app prevede che l'applicazione JAR sia denominata *app. jar*. Se il nome è presente, verrà eseguito automaticamente. Per gli utenti di Maven è possibile impostare il nome del file `<finalName>app</finalName>` jar includendo `<build>` nella sezione del file *POM. XML*. [È possibile eseguire la stessa operazione in Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) impostando `archiveFileName` la proprietà.

Se si vuole usare un nome diverso per il file JAR, è necessario specificare anche il [comando di avvio](app-service-linux-faq.md#built-in-images) che esegue il file jar. Ad esempio `java -jar my-jar-app.jar`. È possibile impostare il valore per il comando di avvio nel portale, in configurazione > Impostazioni generali o con un'impostazione dell'applicazione denominata `STARTUP_COMMAND`.

### <a name="server-port"></a>Porta server

Il servizio app Linux instrada le richieste in ingresso alla porta 80, in modo che l'applicazione sia in ascolto anche sulla porta 80. Questa operazione può essere eseguita nella configurazione dell'applicazione, ad esempio il file *Application. Properties* di Spring, oppure nel comando di avvio (ad esempio, `java -jar spring-app.jar --server.port=80`). Vedere la documentazione seguente per i framework Java comuni:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Esegui Framework](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
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

Per configurare Tomcat per l'uso di Java Database Connectivity (JDBC) o Java Persistence API (app), è `CATALINA_OPTS` innanzitutto necessario personalizzare la variabile di ambiente letta da Tomcat all'avvio. Impostare questi valori tramite un'impostazione app nel [plug-in Maven del servizio app](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

In alternativa, impostare le variabili di ambiente nella pagina**Impostazioni applicazione** di **configurazione** > nel portale di Azure.

Determinare quindi se l'origine dati deve essere disponibile per un'applicazione o per tutte le applicazioni in esecuzione nel servlet Tomcat.

#### <a name="application-level-data-sources"></a>Origini dati a livello di applicazione

1. Creare un file *context. XML* nel file *meta-inf/* directory del progetto. Creare il *meta-inf/* directory se non esiste.

2. In *context. XML*aggiungere un `Context` elemento per collegare l'origine dati a un indirizzo JNDI. Sostituire il segnaposto `driverClassName` con il nome della classe del driver indicato nella tabella precedente.

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

3. Aggiornare il file *Web. XML* dell'applicazione per usare l'origine dati nell'applicazione.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Risorse a livello di server condivise

1. Copiare il contenuto di */usr/local/Tomcat/conf* in */Home/Tomcat/conf* nell'istanza di Linux del servizio app usando SSH se non si ha già una configurazione.

    ```bash
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. Aggiungere un elemento context nel file *Server. XML* all'interno `<Server>` dell'elemento.

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

3. Aggiornare il file *Web. XML* dell'applicazione per usare l'origine dati nell'applicazione.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Completare la configurazione

Infine, inserire i file jar del driver nel classpath di Tomcat e riavviare il servizio app.

1. Assicurarsi che i file del driver JDBC siano disponibili per il classloader di Tomcat inserendoli nella directory */Home/Tomcat/lib* Creare questa directory se non esiste già. Per caricare questi file nell'istanza del servizio app, seguire questa procedura:

    1. Nel [cloud Shell](https://shell.azure.com)installare l'estensione WEBAPP:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Eseguire il comando dell'interfaccia della riga di comando seguente per creare un tunnel SSH dal sistema locale al servizio app:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Connettersi alla porta di tunneling locale con il client SFTP e caricare i file nella cartella */Home/Tomcat/lib* .

    In alternativa, è possibile usare un client FTP per caricare il driver JDBC. Seguire queste [istruzioni per ottenere le credenziali FTP](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

2. Se è stata creata un'origine dati a livello di server, riavviare l'applicazione Linux del Servizio app. Tomcat reimposterà `CATALINA_HOME` su `/home/tomcat/conf` e userà la configurazione aggiornata.

### <a name="spring-boot"></a>Spring Boot

Per connettersi alle origini dati nelle applicazioni Spring boot, è consigliabile creare stringhe di connessione e inserirle nel file *Application. Properties* .

1. Nella sezione "configurazione" della pagina del servizio app, impostare un nome per la stringa, incollare la stringa di connessione JDBC nel campo valore e impostare il tipo su "Custom". Facoltativamente, è possibile impostare questa stringa di connessione come impostazione dello slot.

    Questa stringa di connessione è accessibile all'applicazione come variabile di ambiente denominata `CUSTOMCONNSTR_<your-string-name>`. Ad esempio, la stringa di connessione creata in precedenza verrà denominata `CUSTOMCONNSTR_exampledb`.

2. Nel file *Application. Properties* , fare riferimento a questa stringa di connessione con il nome della variabile di ambiente. Per questo esempio, si utilizzerà il codice seguente.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Per ulteriori informazioni su questo argomento, vedere la [documentazione di Spring boot relativa all'accesso ai dati](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) e alle [configurazioni esternalizzate](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) .

## <a name="configure-java-ee-wildfly"></a>Configurare Java EE (WildFly)

> [!NOTE]
> Java Enterprise Edition nel servizio app Linux è attualmente in fase di anteprima. Questo stack **non** è consigliato per le attività di produzione. informazioni sugli stack Java SE e Tomcat.

App Azure servizio in Linux consente agli sviluppatori Java di creare, distribuire e ridimensionare applicazioni Java Enterprise (Java EE) in un servizio completamente gestito basato su Linux.  L'ambiente di runtime Java Enterprise sottostante è il server applicazioni open source [WildFly](https://wildfly.org/) .

In questa sezione sono disponibili le procedure seguenti:

- [Scalabilità con il servizio app](#scale-with-app-service)
- [Personalizzare la configurazione del server applicazioni](#customize-application-server-configuration)
- [Installare moduli e dipendenze](#install-modules-and-dependencies)
- [Configurare le origini dati](#configure-data-sources)
- [Abilita provider di messaggistica](#enable-messaging-providers)
- [Configurare la memorizzazione nella cache di gestione delle sessioni](#configure-session-management-caching)

### <a name="scale-with-app-service"></a>Ridimensionare con il Servizio app di Azure

Il server applicazioni WildFly del Servizio app di Azure per Linux viene eseguito in modalità autonoma, non in una configurazione di dominio. Quando si amplia il piano di Servizio app, ogni istanza di WildFly viene configurata come server autonomo.

Per scalare l'applicazione orizzontalmente o verticalmente, si usano le [regole di scalabilità](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) e si [aumenta il numero di istanze](../manage-scale-up.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="customize-application-server-configuration"></a>Personalizzare la configurazione del server applicazioni

Le istanze dell'app Web sono senza stato, quindi ogni nuova istanza avviata deve essere configurata all'avvio per supportare la configurazione WildFly necessaria per l'applicazione.
È possibile scrivere uno script Bash di avvio per chiamare l'interfaccia della riga di comando di WildFly per:

- Configurare le origini dati
- Configurare i provider di messaggistica
- Aggiungere altri moduli e dipendenze alla configurazione del server WildFly.

Lo script viene eseguito quando WildFly è attivo e in esecuzione, ma prima dell'avvio dell'applicazione. Lo script deve usare l' [interfaccia](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) della riga di comando di JBoss denominata da */opt/JBoss/wildfly/bin/JBoss-cli.sh* per configurare il server applicazioni con qualsiasi configurazione o modifica necessaria dopo l'avvio del server.

Non usare la modalità interattiva dell'interfaccia della riga di comando per configurare WildFly. Si può invece specificare uno script di comandi all'interfaccia della riga di comando di JBoss usando il comando `--file`, ad esempio:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Usare FTP per caricare lo script di avvio in un percorso dell'istanza del servizio app nella directory */Home* , ad esempio */Home/site/Deployments/Tools*. Per altre informazioni, vedere [distribuire l'app nel servizio app Azure tramite FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

Impostare il campo **script di avvio** nella portale di Azure sul percorso dello script della shell di avvio, ad esempio */Home/site/Deployments/Tools/your-startup-script.sh*.

Specificare le [impostazioni dell'app](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) nella configurazione dell'applicazione per passare le variabili di ambiente da usare nello script. Le impostazioni dell'applicazione mantengono le stringhe di connessione e altri segreti necessari per configurare il controllo delle versioni dell'applicazione.

### <a name="install-modules-and-dependencies"></a>Installare moduli e dipendenze

Per installare i moduli e le relative dipendenze nel classpath WildFly tramite l'interfaccia della riga di comando di JBoss, è necessario creare i file seguenti nella propria directory. Per alcuni moduli e dipendenze può essere necessario specificare una configurazione aggiuntiva, ad esempio la denominazione JNDI o un'altra configurazione specifica dell'API. L'elenco che segue include gli elementi minimi necessari per configurare una dipendenza nella maggior parte dei casi.

- Un [descrittore di modulo XML](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Questo file XML definisce il nome, gli attributi e le dipendenze del modulo. Questo [file module.xml di esempio](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) definisce un modulo Postgres, la relativa dipendenza JDBC del file JAR e altre dipendenze del modulo richieste.
- Le dipendenze del file con estensione JAR necessarie per il modulo.
- Uno script con i comandi dell'interfaccia della riga di comando di JBoss per configurare il nuovo modulo. Questo file conterrà i comandi che si indica all'interfaccia della riga di comando di JBoss di eseguire per configurare il server per usare la dipendenza. Per la documentazione sui comandi per aggiungere moduli, origini dati e provider di messaggistica, fare riferimento a [questo documento](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- Uno script Bash di avvio per chiamare l'interfaccia della riga di comando di JBoss ed eseguire lo script del passaggio precedente. Questo file viene eseguito quando l'istanza del Servizio app di Azure viene riavviata o quando viene effettuato il provisioning di nuove istanze durante un'operazione di scale-out. Questo script di avvio è usato per eseguire qualsiasi altra configurazione per l'applicazione man mano che i comandi di JBoss vengono passati all'interfaccia della riga di comando di JBoss. Questo file può essere come minimo un singolo comando per passare lo script di comandi della CLI di JBoss all'interfaccia della riga di comando di JBoss:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Dopo aver creato i file e il contenuto per il modulo, attenersi alla procedura seguente per aggiungere il modulo al server applicazioni WildFly.

1. Usare FTP per caricare i file in un percorso dell'istanza del servizio app nella directory */Home* , ad esempio */Home/site/Deployments/Tools*. Per altre informazioni, vedere [distribuire l'app nel servizio app Azure tramite FTP/S](../deploy-ftp.md).
2. Nella pagina**Impostazioni generali** **configurazione** > della portale di Azure impostare il campo script di **avvio** sul percorso dello script della shell di avvio, ad esempio */Home/site/Deployments/Tools/Startup.sh*.
3. Riavviare l'istanza del servizio app premendo il pulsante **Riavvia** nella sezione **Panoramica** del portale o usando l'interfaccia della riga di comando di Azure.

### <a name="configure-data-sources"></a>Configurare le origini dati

Per configurare WildFly/JBoss per accedere a un'origine dati, usare il processo generale descritto in precedenza nella sezione "installare moduli e dipendenze". La sezione seguente fornisce dettagli specifici su questo processo per le origini dati PostgreSQL, MySQL e SQL Server.

Questa sezione presuppone che sia già presente un'app, un'istanza del servizio app e un'istanza del servizio di database di Azure. Le istruzioni riportate di seguito fanno riferimento al nome del servizio app, al relativo gruppo di risorse e alle informazioni di connessione al database. È possibile trovare queste informazioni nella portale di Azure.

Se si preferisce eseguire l'intero processo dall'inizio usando un'app di esempio, vedere [esercitazione: Compilare un'app Web Java EE e Postgres in Azure](tutorial-java-enterprise-postgresql-app.md).

I passaggi seguenti illustrano i requisiti per la connessione del servizio app e del database esistenti.

1. Scaricare il driver JDBC per [PostgreSQL](https://jdbc.postgresql.org/download.html), [MySQL](https://dev.mysql.com/downloads/connector/j/)o [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server). Decomprimere l'archivio scaricato per ottenere il file con estensione jar del driver.

2. Creare un file con un nome come *Module. XML* e aggiungere il markup seguente. Sostituire il `<module name>` segnaposto (incluse le parentesi angolari `org.postgres` ) con per `com.mysql` PostgreSQL, per MySQL `com.microsoft` o per SQL Server. Sostituire `<JDBC .jar file path>` con il nome del file con estensione jar del passaggio precedente, incluso il percorso completo della posizione in cui si vuole inserire il file nell'istanza del servizio app. Può trattarsi di qualsiasi percorso nella directory */Home* .

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="<module name>">
        <resources>
           <resource-root path="<JDBC .jar file path>" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

3. Creare un file con un nome come *DataSource-Commands. CLI* e aggiungere il codice seguente. Sostituire `<JDBC .jar file path>` con il valore usato nel passaggio precedente. Sostituire `<module file path>` con il nome file e il percorso del servizio app del passaggio precedente, ad esempio */Home/Module.XML*.

    **PostgreSQL**

    ```console
    module add --name=org.postgres --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name=postgres,driver-module-name=org.postgres,driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=$DATABASE_CONNECTION_URL --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker

    reload --use-current-server-config=true
    ```

    **MySQL**

    ```console
    module add --name=com.mysql --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.cj.jdbc.Driver)

    data-source add --name=mysqlDS --jndi-name=java:jboss/datasources/mysqlDS --connection-url=$DATABASE_CONNECTION_URL --driver-name=mysql --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=com.mysql.cj.jdbc.Driver --jta=true --use-java-context=true --exception-sorter-class-name=com.mysql.cj.jdbc.integration.jboss.ExtendedMysqlExceptionSorter

    reload --use-current-server-config=true
    ```

    **SQL Server**

    ```console
    module add --name=com.microsoft --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=sqlserver:add(driver-name=sqlserver,driver-module-name=com.microsoft,driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver,driver-datasource-class-name=com.microsoft.sqlserver.jdbc.SQLServerDataSource)

    data-source add --name=sqlDS --jndi-name=java:jboss/datasources/sqlDS --driver-name=sqlserver --connection-url=$DATABASE_CONNECTION_URL --validate-on-match=true --background-validation=false --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLExceptionSorter

    reload --use-current-server-config=true
    ```

    Questo file viene eseguito dallo script di avvio descritto nel passaggio successivo. Viene installato il driver JDBC come modulo WildFly, viene creata l'origine dati WildFly corrispondente e viene ricaricato il server per assicurarsi che le modifiche abbiano effetto.

4. Creare un file con un nome come *Startup.sh* e aggiungere il codice seguente. Sostituire `<JBoss CLI script>` con il nome del file creato nel passaggio precedente. Assicurarsi di includere il percorso completo della posizione in cui verrà inserito il file nell'istanza del servizio app, ad esempio */Home/DataSource-Commands.CLI*.

    ```bash
    #!/usr/bin/env bash
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=<JBoss CLI script>
    ```

5. Usare FTP per caricare il file JDBC. jar, il file XML del modulo, lo script dell'interfaccia della riga di comando di JBoss e lo script di avvio nell'istanza del servizio app. Inserire i file nel percorso specificato nei passaggi precedenti, ad esempio */Home*. Per altre informazioni su FTP, vedere [distribuire l'app nel servizio app Azure tramite FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

6. Usare l'interfaccia della riga di comando di Azure per aggiungere impostazioni al servizio app che contengono le informazioni di connessione al database. Sostituire `<resource group>` e`<webapp name>` con i valori usati dal servizio app. Sostituire `<database server name>` ,`<database name>`, e`<admin password>` con le informazioni di connessione al database. `<admin name>` È possibile ottenere il servizio app e le informazioni sul database dal portale di Azure.

    **PostgreSQL**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:postgresql://<database server name>:5432/<database name>?ssl=true \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **MySQL:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **SQL Server:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
    ```

    I valori di DATABASE_CONNECTION_URL sono diversi per ogni server di database e diversi dai valori del portale di Azure. I formati URL indicati qui (e nei frammenti di codice precedenti) sono obbligatori per l'uso da parte di WildFly:

    * **PostgreSQL:** `jdbc:postgresql://<database server name>:5432/<database name>?ssl=true`
    * **MySQL:** `jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT`
    * **SQL Server:** `jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;`

7. Nella portale di Azure passare al servizio app e trovare la pagina**Impostazioni generali** **configurazione** > . Impostare il campo **script di avvio** sul nome e il percorso dello script di avvio, ad esempio */Home/Startup.sh*.

Alla successiva riavvio del servizio app, verrà eseguito lo script di avvio ed eseguire i passaggi di configurazione necessari. Per verificare che la configurazione venga eseguita correttamente, è possibile accedere al servizio app usando SSH e quindi eseguire lo script di avvio dal prompt di bash. È anche possibile esaminare i log del servizio app. Per altre informazioni su queste opzioni, vedere [registrazione e debug di app](#logging-and-debugging-apps).

Successivamente, sarà necessario aggiornare la configurazione di WildFly per l'app e ridistribuirla. Seguire questa procedura:

1. Aprire il file *src/main/resources/META-INF/persistence. XML* per l'app `<jta-data-source>` e trovare l'elemento. Sostituire il relativo contenuto come illustrato di seguito:

    **PostgreSQL**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

    **MySQL**

    ```xml
    <jta-data-source>java:jboss/datasources/mysqlDS</jta-data-source>
    ```

    **SQL Server**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

2. Ricompilare e ridistribuire l'app usando il comando seguente al prompt di bash:

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

3. Riavviare l'istanza del servizio app premendo il pulsante **Riavvia** nella sezione **Panoramica** del portale di Azure o usando l'interfaccia della riga di comando di Azure.

L'istanza del servizio app è ora configurata per l'accesso al database.

Per altre informazioni sulla configurazione della connettività del database con WildFly, vedere [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource)o [SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898).

### <a name="enable-messaging-providers"></a>Abilita provider di messaggistica

Per abilitare Beans basato su messaggi usando il bus di servizio come meccanismo di messaggistica:

1. Usare la [libreria di messaggistica di Apache QPId JMS](https://qpid.apache.org/proton/index.html). Includere questa dipendenza nel file pom.xml (o in un altro file di compilazione) per l'applicazione.

2. Creare le [risorse del bus di servizio](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Creare uno spazio dei nomi del bus di servizio di Azure, una coda all'interno di tale spazio dei nomi e criteri di accesso condiviso con capacità di invio e di ricezione.

3. Passare la chiave dei criteri di accesso condiviso al codice tramite la codifica URL della chiave primaria dei criteri o [usare il Service Bus SDK](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Seguire i passaggi descritti nella sezione "Moduli e dipendenze" con il descrittore del modulo XML, le dipendenze con estensione jar, i comandi dell'interfaccia della riga di comando di JBoss e lo script di avvio per il provider JMS. Oltre ai quattro file, sarà necessario creare un file XML che definisce il nome JNDI per l'argomento e la coda JMS. Vedere [questo repository](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig) per i file di configurazione di riferimento.

### <a name="configure-session-management-caching"></a>Configurare la cache per la gestione delle sessioni

Per impostazione predefinita, il Servizio app di Azure per Linux userà i cookie di affinità di sessione per garantire che le richieste dei client con sessioni esistenti vengano instradate alla stessa istanza dell'applicazione. Questo comportamento predefinito non richiede alcuna configurazione, ma presenta alcune limitazioni:

- Se un'istanza dell'applicazione viene riavviata o ridotta, lo stato della sessione utente nel server di applicazioni verrà perso.
- Se le applicazioni sono impostate su un timeout della sessione prolungato o un numero fisso di utenti, la ricezione del carico da parte delle nuove istanze scalate automaticamente può richiedere tempo poiché solo le nuove sessioni verranno instradate verso le istanze appena avviate.

È possibile configurare WildFly per l'uso di un archivio di sessione esterno, ad esempio [cache di Azure per Redis](/azure/azure-cache-for-redis/). È necessario [disabilitare la configurazione di affinità dell'istanza di Arr esistente](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) per disattivare il routing basato su cookie di sessione e consentire il funzionamento dell'archivio sessioni WildFly configurato senza interferenze.

## <a name="docker-containers"></a>Contenitori Docker

Per usare nei contenitori il pacchetto JDK Zulu supportato da Azure, assicurarsi di eseguire il pull e di usare le immagini precompilate, come documentato nella [pagina di download di Azul Zulu Enterprise per Azure supportata](https://www.azul.com/downloads/azure-only/zulu/) o di usare gli esempi `Dockerfile` dal [repository GitHub Java di Microsoft](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Rapporto di supporto

### <a name="runtime-availability"></a>Disponibilità Runtime

Il servizio app per Linux supporta due runtime per l'hosting gestito delle applicazioni Web Java:

- Il [contenitore servlet Tomcat](https://tomcat.apache.org/) per l'esecuzione di applicazioni in pacchetti come file di archivio Web (WAR). Sono supportate le versioni 8.5 e 9.0.
- Ambiente di runtime Java SE per l'esecuzione di applicazioni in pacchetti come file di archivio Java (JAR). Le versioni supportate sono Java 8 e 11.

### <a name="jdk-versions-and-maintenance"></a>Versioni di JDK e manutenzione

Le build Azul Zulu Enterprise di OpenJDK sono distribuzioni di OpenJDK gratuite, multipiattaforma e pronte per la produzione per Azure e Azure Stack, supportate da Microsoft e Azul Systems. Contengono tutti i componenti necessari per compilare ed eseguire applicazioni Java SE. È possibile installare JDK dall' [installazione di Java JDK](https://aka.ms/azure-jdks).

Ai pacchetti JDK supportati vengono automaticamente applicate patch con cadenza trimestrale, a gennaio, aprile, luglio e ottobre di ogni anno.

### <a name="security-updates"></a>Aggiornamenti della sicurezza

Le patch e le correzioni per le principali vulnerabilità della sicurezza verranno rilasciate da Azul Systems non appena disponibili. Una vulnerabilità "principale" viene definita da un punteggio di base pari o superiore a 9,0 in [NIST Common Vulnerability Scoring System, versione 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Deprecazione e ritiro

Se un runtime Java supportato sarà ritirato, gli sviluppatori di Azure che usano il runtime interessato riceveranno un avviso di funzionalità deprecata almeno sei mesi prima che il runtime venga ritirato.

## <a name="next-steps"></a>Passaggi successivi

Per trovare guide introduttive di Azure, esercitazioni e documentazione di riferimento su Java, visitare la pagina [Azure per sviluppatori Java](/java/azure/).

Per domande generali sull'uso del servizio app per Linux, non specifiche dello sviluppo Java, vedere le [domande frequenti sul servizio app in Linux](app-service-linux-faq.md).

---
title: Configurare app java per Linux
description: Informazioni su come configurare un contenitore Java predefinito per l'app. Questo articolo illustra le attività di configurazione più comuni.
keywords: servizio app di Azure, app Web, Linux, OSS, Java, Java EE, JEE, JavaEE
author: bmitchell287
manager: barbkess
ms.devlang: java
ms.topic: article
ms.date: 11/22/2019
ms.author: brendm
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: edb8f25ff1e4fa01e905c3ae5c7d0ec7ab58f8bb
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705944"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Configurare un'app Java Linux per il servizio app Azure

Il servizio app Azure in Linux consente agli sviluppatori Java di creare, distribuire e ridimensionare rapidamente le applicazioni Web in pacchetto Tomcat, WildFly o Java Standard Edition (SE) in un servizio basato su Linux completamente gestito. Distribuire le applicazioni con i plug-in Maven dalla riga di comando o in editor come IntelliJ, Eclipse o Visual Studio Code.

Questa guida fornisce i concetti chiave e le istruzioni per gli sviluppatori Java che usano un contenitore Linux incorporato nel servizio app. Se non si è mai usato app Azure servizio, seguire prima l'esercitazione [introduttiva](quickstart-java.md) su Java e [Java con PostgreSQL](tutorial-java-enterprise-postgresql-app.md) .

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

Per ulteriori informazioni, vedere la pagina relativa ai [log di flusso in cloud Shell](../troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Registrazione dell'app

Abilitare la [registrazione dell'applicazione](../troubleshoot-diagnostic-logs.md?toc=/azure/app-service/containers/toc.json#enable-application-logging-windows) tramite il portale di Azure o l'[interfaccia della riga di comando di Azure](/cli/azure/webapp/log#az-webapp-log-config) per configurare il servizio app per scrivere l'output della console standard dell'applicazione e i flussi di errori della console standard nel file system locale o in Archiviazione BLOB di Azure. La registrazione nell'istanza del file system del servizio app locale viene disabilitata 12 ore dopo la configurazione. Se è necessario un periodo di conservazione più lungo, configurare l'applicazione per scrivere l'output in un contenitore di archiviazione BLOB. I log delle app Java e Tomcat sono reperibili nella directory */Home/LogFiles/Application/*

Se l'applicazione usa [Logback](https://logback.qos.ch/) o [Log4j](https://logging.apache.org/log4j) per la traccia, è possibile inoltrare queste tracce per la revisione ad Azure Application Insights usando le istruzioni di configurazione del framework di registrazione illustrate in [Esplorare i log di traccia Java in Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Strumenti per la risoluzione dei problemi

Le immagini Java predefinite sono basate sul sistema operativo [alpino Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) . Usare Gestione pacchetti `apk` per installare gli strumenti o i comandi per la risoluzione dei problemi.

### <a name="flight-recorder"></a>Utilità traccia eventi

Per tutte le immagini Java Linux nel servizio app è installato Zulu Flight Recorder, in modo che sia possibile connettersi facilmente a JVM e avviare una registrazione del profiler o generare un dump dell'heap.

#### <a name="timed-recording"></a>Registrazione temporizzata

Per iniziare, connettersi tramite SSH al servizio app ed eseguire il comando `jcmd` per visualizzare un elenco di tutti i processi Java in esecuzione. Oltre a jcmd, l'applicazione Java dovrebbe essere in esecuzione con un numero ID processo (PID).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Eseguire il comando seguente per avviare una registrazione di 30 secondi della JVM. Verrà profilata la JVM e verrà creato un file JFR denominato *jfr_example. JFR* nella Home Directory. Sostituire 116 con il PID dell'app java.

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Durante l'intervallo di 30 secondi, è possibile verificare che la registrazione avvenga eseguendo `jcmd 116 JFR.check`. Vengono visualizzate tutte le registrazioni per il processo Java specificato.

#### <a name="continuous-recording"></a>Registrazione continua

È possibile usare lo strumento di registrazione dei voli Zulu per profilare costantemente l'applicazione Java con un effetto minimo sulle prestazioni di runtime ([origine](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). A tale scopo, eseguire il comando dell'interfaccia della riga di comando di Azure seguente per creare un'impostazione dell'app denominata JAVA_OPTS con la configurazione necessaria. Il contenuto dell'impostazione dell'app JAVA_OPTS viene passato al comando `java` quando l'app viene avviata.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Una volta avviata la registrazione, è possibile scaricare i dati di registrazione correnti in qualsiasi momento usando il comando `JFR.dump`.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Per ulteriori informazioni, vedere la Guida di [riferimento ai comandi di Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analisi delle registrazioni

Usare [FTPS](../deploy-ftp.md) per scaricare il file JFR nel computer locale. Per analizzare il file JFR, scaricare e installare [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Per istruzioni su Zulu Mission Control, vedere la [documentazione di Azul](https://docs.azul.com/zmc/) e le [istruzioni per l'installazione](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Personalizzazione e ottimizzazione

Il servizio app Azure per Linux supporta l'ottimizzazione e la personalizzazione predefinite tramite l'portale di Azure e l'interfaccia della riga di comando. Vedere gli articoli seguenti per la configurazione di app Web non specifiche per Java:

- [Configurare le impostazioni dell'app](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)
- [Configurare un nome di dominio](../app-service-web-tutorial-custom-domain.md?toc=/azure/app-service/containers/toc.json)
- [Configurare associazioni SSL](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)
- [Aggiungere una rete CDN](../../cdn/cdn-add-to-web-app.md?toc=/azure/app-service/containers/toc.json)
- [Configurare il sito Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Impostare le opzioni di runtime Java

Per impostare la memoria allocata o altre opzioni di runtime JVM negli ambienti Tomcat e Java SE, creare un' [impostazione dell'app](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) denominata `JAVA_OPTS` con le opzioni. Il servizio app in Linux passa questa impostazione come variabile di ambiente al runtime Java quando viene avviato.

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

- Istanze B1 e S1: `-Xms1024m -Xmx1024m`
- Istanze B2 e S2: `-Xms3072m -Xmx3072m`
- Istanze B3 e S3: `-Xms6144m -Xmx6144m`

Quando si ottimizzano le impostazioni heap delle applicazioni, esaminare i dettagli del piano di servizio app e tenere in considerazione le esigenze di più applicazioni e slot di distribuzione per trovare l'allocazione ottimale della memoria.

Se si distribuisce un'applicazione JAR, questa deve essere denominata *app. jar* , in modo che l'immagine incorporata possa identificare correttamente l'app. Il plug-in Maven viene rinominato automaticamente. Se non si vuole rinominare il file JAR in *app. jar*, è possibile caricare uno script della shell con il comando per eseguire il file jar. Incollare quindi il percorso completo di questo script nella casella di testo [file di avvio](app-service-linux-faq.md#built-in-images) nella sezione configurazione del portale. Lo script di avvio non viene eseguito dalla directory in cui si trova. Pertanto, utilizzare sempre i percorsi assoluti per fare riferimento ai file nello script di avvio (ad esempio: `java -jar /home/myapp/myapp.jar`).

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

Se l'applicazione Java è particolarmente grande, è necessario aumentare il limite di tempo di avvio. A tale scopo, creare un'impostazione dell'applicazione `WEBSITES_CONTAINER_START_TIME_LIMIT` e impostarla sul numero di secondi di attesa del servizio app prima del timeout. Il valore massimo è `1800` secondi.

### <a name="pre-compile-jsp-files"></a>Pre-compila file JSP

Per migliorare le prestazioni delle applicazioni Tomcat, è possibile compilare i file JSP prima della distribuzione nel servizio app. È possibile usare il [plug](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) -in Maven fornito da Apache Sling o usando questo [file di compilazione Ant](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Proteggere le applicazioni

Le applicazioni Java in esecuzione nel servizio app per Linux hanno lo stesso set di [procedure consigliate per la sicurezza](/azure/security/security-paas-applications-using-app-services) delle altre applicazioni.

### <a name="authenticate-users-easy-auth"></a>Autenticare gli utenti (Easy auth)

Configurare l'autenticazione delle app nel portale di Azure con l'opzione di **autenticazione e autorizzazione** . che consente di abilitare l'autenticazione usando Azure Active Directory o gli account di accesso ai social network, ad esempio Facebook, Google o GitHub. La configurazione nel portale di Azure funziona solo quando si configura un singolo provider di autenticazione. Per altre informazioni, vedere [Configurare un'applicazione dei servizi app per usare l'account di accesso di Azure Active Directory](../configure-authentication-provider-aad.md?toc=/azure/app-service/containers/toc.json) e gli articoli correlati per gli altri provider di identità. Se è necessario abilitare più provider di accesso, seguire le istruzioni dell'articolo [Personalizzare l'autenticazione nel servizio app](../app-service-authentication-how-to.md?toc=/azure/app-service/containers/toc.json).

#### <a name="tomcat-and-wildfly"></a>Tomcat e WildFly

L'applicazione Tomcat o WildFly può accedere alle attestazioni dell'utente direttamente dalla servlet eseguendo il cast dell'oggetto Principal a un oggetto map. L'oggetto map eseguirà il mapping di ogni tipo di attestazione a una raccolta di attestazioni per quel tipo. Nel codice riportato di seguito `request` è un'istanza di `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

A questo punto è possibile esaminare l'oggetto `Map` per qualsiasi attestazione specifica. Il frammento di codice seguente, ad esempio, scorre tutti i tipi di attestazione e stampa il contenuto di ogni raccolta.

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

Per disconnettersi gli utenti, usare il percorso `/.auth/ext/logout`. Per eseguire altre azioni, vedere la documentazione sull' [utilizzo dell'autenticazione e dell'autorizzazione del servizio app](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to). È disponibile anche la documentazione ufficiale sull' [interfaccia HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) di Tomcat e sui relativi metodi. Anche i seguenti metodi servlet vengono idratati in base alla configurazione del servizio app:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Per disabilitare questa funzionalità, creare un'impostazione dell'applicazione denominata `WEBSITE_AUTH_SKIP_PRINCIPAL` con un valore di `1`. Per disabilitare tutti i filtri servlet aggiunti dal servizio app, creare un'impostazione denominata `WEBSITE_SKIP_FILTERS` con un valore di `1`.

#### <a name="spring-boot"></a>Spring Boot

Gli sviluppatori Spring Boot possono usare l'[utilità di avvio Spring Boot per Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) per proteggere le applicazioni usando le familiari annotazioni e API di Spring Security. Assicurarsi di aumentare le dimensioni massime dell'intestazione nel file *Application. Properties* . È consigliabile il valore `16384`.

### <a name="configure-tlsssl"></a>Configurare TLS/SSL

Per caricare un certificato SSL esistente e associarlo al nome di dominio dell'applicazione, seguire le istruzioni riportate nella pagina [proteggere un nome DNS personalizzato con binding SSL nel servizio app Azure](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json) . Per impostazione predefinita, l'applicazione consentirà ancora le connessioni HTTP. Seguire i passaggi specifici dell'esercitazione per applicare SSL e TLS.

### <a name="use-keyvault-references"></a>Usare i riferimenti all'insieme di credenziali delle credenziali

[Azure chiave Vault](../../key-vault/key-vault-overview.md) fornisce una gestione centralizzata dei segreti con i criteri di accesso e la cronologia di controllo. È possibile archiviare segreti (ad esempio password o stringhe di connessione) nell'insieme di credenziali delle chiavi e accedere a questi segreti nell'applicazione tramite le variabili di ambiente.

Per prima cosa, seguire le istruzioni per [concedere all'app l'accesso a Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e [creare un riferimento a un insieme di credenziali delle credenziali al segreto in un'impostazione dell'applicazione](../app-service-key-vault-references.md#reference-syntax). È possibile verificare che il riferimento venga risolto nel segreto stampando la variabile di ambiente durante l'accesso remoto al terminale del servizio app.

Per inserire questi segreti nel file di configurazione di Spring o Tomcat, usare la sintassi di inserimento delle variabili di ambiente (`${MY_ENV_VAR}`). Per i file di configurazione della primavera, vedere la documentazione sulle [configurazioni esternalizzate](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

### <a name="using-the-java-key-store"></a>Uso dell'archivio chiavi Java

Per impostazione predefinita, tutti i certificati pubblici o privati [caricati nel servizio app Linux](../configure-ssl-certificate.md) verranno caricati nell'archivio chiavi Java quando il contenitore viene avviato. Ciò significa che i certificati caricati saranno disponibili nel contesto di connessione quando si effettuano connessioni TLS in uscita. Dopo aver caricato il certificato, sarà necessario riavviare il servizio app affinché venga caricato nell'archivio chiavi Java.

È possibile interagire o eseguire il debug dello strumento chiave Java [aprendo una connessione SSH](app-service-linux-ssh-support.md) al servizio app ed eseguendo il comando `keytool`. Vedere la [documentazione dello strumento chiave](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) per un elenco di comandi. I certificati vengono archiviati nel percorso del file dell'archivio chiavi predefinito di Java, `$JAVA_HOME/jre/lib/security/cacerts`.

Potrebbe essere necessaria una configurazione aggiuntiva per la crittografia della connessione JDBC. Per informazioni sul driver JDBC scelto, vedere la documentazione.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)

#### <a name="manually-initialize-and-load-the-key-store"></a>Inizializzare e caricare manualmente l'archivio chiavi

È possibile inizializzare l'archivio chiavi e aggiungere i certificati manualmente. Creare un'impostazione dell'app, `SKIP_JAVA_KEYSTORE_LOAD`con un valore `1` per disabilitare il caricamento automatico dei certificati nell'archivio chiavi da parte del servizio app. Tutti i certificati pubblici caricati nel servizio app tramite il portale di Azure vengono archiviati in `/var/ssl/certs/`. I certificati privati vengono archiviati in `/var/ssl/private/`.

Per ulteriori informazioni sull'API dell'archivio chiavi, consultare [la documentazione ufficiale](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

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

> [!NOTE]
> Se si dispone già di una variabile di ambiente per `JAVA_OPTS` oppure `CATALINA_OPTS`, aggiungere l'opzione `-javaagent:/...` alla fine del valore corrente.

## <a name="configure-jar-applications"></a>Configurare le applicazioni JAR

### <a name="starting-jar-apps"></a>Avvio di app JAR

Per impostazione predefinita, il servizio app prevede che l'applicazione JAR sia denominata *app. jar*. Se il nome è presente, verrà eseguito automaticamente. Per gli utenti di Maven è possibile impostare il nome del file JAR includendo `<finalName>app</finalName>` nella sezione `<build>` del file *POM. XML*. [È possibile eseguire la stessa operazione in Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) impostando la proprietà `archiveFileName`.

Se si vuole usare un nome diverso per il file JAR, è necessario specificare anche il [comando di avvio](app-service-linux-faq.md#built-in-images) che esegue il file jar. Ad esempio `java -jar my-jar-app.jar`. È possibile impostare il valore per il comando di avvio nel portale, in configurazione > Impostazioni generali o con un'impostazione dell'applicazione denominata `STARTUP_COMMAND`.

### <a name="server-port"></a>Porta server

Il servizio app Linux instrada le richieste in ingresso alla porta 80, in modo che l'applicazione sia in ascolto anche sulla porta 80. Questa operazione può essere eseguita nella configurazione dell'applicazione, ad esempio il file *Application. Properties* di Spring, oppure nel comando di avvio (ad esempio, `java -jar spring-app.jar --server.port=80`). Vedere la documentazione seguente per i framework Java comuni:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Esegui Framework](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [VertX](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quark](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Origini dati

### <a name="tomcat"></a>Tomcat

Queste istruzioni si applicano a tutte le connessioni di database. È necessario sostituire i segnaposto con il nome della classe del driver del database scelto e il file JAR. Viene fornita una tabella con i nomi delle classi e i download dei driver per i database più comuni.

| Database   | Nome della classe del driver                             | Driver JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Scaricare](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Scaricare](https://dev.mysql.com/downloads/connector/j/) (selezionare "Indipendente dalla piattaforma") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Scaricare](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Per configurare Tomcat per l'uso di Java Database Connectivity (JDBC) o Java Persistence API (app), è innanzitutto necessario personalizzare la variabile di ambiente `CATALINA_OPTS` letta da Tomcat all'avvio. Impostare questi valori tramite un'impostazione app nel [plug-in Maven del servizio app](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

In alternativa, impostare le variabili di ambiente nella pagina **configurazione** > **impostazioni applicazione** della portale di Azure.

Determinare quindi se l'origine dati deve essere disponibile per un'applicazione o per tutte le applicazioni in esecuzione nel servlet Tomcat.

#### <a name="application-level-data-sources"></a>Origini dati a livello di applicazione

1. Creare un file *context. XML* nel file *meta-inf/* directory del progetto. Creare il *meta-inf/* directory se non esiste.

2. In *context. XML*aggiungere un elemento `Context` per collegare l'origine dati a un indirizzo JNDI. Sostituire il segnaposto `driverClassName` con il nome della classe del driver indicato nella tabella precedente.

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

Se si aggiunge un'origine dati condivisa a livello di server, sarà necessario modificare il file server. XML di Tomcat. Innanzitutto, caricare uno [script di avvio](app-service-linux-faq.md#built-in-images) e impostare il percorso dello script nel comando **Configuration** > **Startup**. È possibile caricare lo script di avvio mediante [FTP](../deploy-ftp.md).

Lo script di avvio effettuerà una [trasformazione XSL](https://www.w3schools.com/xml/xsl_intro.asp) nel file server. XML e restituirà il file XML risultante a `/usr/local/tomcat/conf/server.xml`. Lo script di avvio deve installare libxslt tramite apk. Il file XSL e lo script di avvio possono essere caricati tramite FTP. Di seguito è riportato uno script di avvio di esempio.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /usr/local/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /home/tomcat/conf/server.xml
```

Di seguito è riportato un esempio di file XSL. Il file XSL di esempio aggiunge un nuovo nodo connettore al file server. XML di Tomcat.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>Completare la configurazione

Infine, inserire i file jar del driver nel classpath di Tomcat e riavviare il servizio app.

1. Assicurarsi che i file del driver JDBC siano disponibili per il classloader di Tomcat inserendoli nella directory */Home/Tomcat/lib* (Creare questa directory se non esiste già.) Per caricare i file nell'istanza del servizio app, seguire questa procedura:

    1. Nel [cloud Shell](https://shell.azure.com)installare l'estensione WEBAPP:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Eseguire il comando dell'interfaccia della riga di comando seguente per creare un tunnel SSH dal sistema locale al servizio app:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Connettersi alla porta di tunneling locale con il client SFTP e caricare i file nella cartella */Home/Tomcat/lib* .

    In alternativa, è possibile usare un client FTP per caricare il driver JDBC. Seguire queste [istruzioni per ottenere le credenziali FTP](../deploy-configure-credentials.md?toc=/azure/app-service/containers/toc.json).

2. Se è stata creata un'origine dati a livello di server, riavviare l'applicazione Linux del Servizio app. Tomcat reimposterà `CATALINA_BASE` su `/home/tomcat` e userà la configurazione aggiornata.

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
> Java Enterprise Edition nel servizio app Linux è attualmente in fase di anteprima. Questo stack **non** è consigliato per le attività di produzione.

App Azure servizio in Linux consente agli sviluppatori Java di creare, distribuire e ridimensionare applicazioni Java Enterprise (Java EE) in un servizio completamente gestito basato su Linux.  L'ambiente di runtime Java Enterprise sottostante è il server applicazioni open source [WildFly](https://wildfly.org/) .

In questa sezione sono disponibili le procedure seguenti:

- [Scalabilità con il servizio app](#scale-with-app-service)
- [Personalizzare la configurazione del server applicazioni](#customize-application-server-configuration)
- [Installare moduli e dipendenze](#install-modules-and-dependencies)
- [Configurare le origini dati](#configure-data-sources)
- [Usare il bus di servizio come broker di messaggi](#use-service-bus-as-a-message-broker)

### <a name="scale-with-app-service"></a>Ridimensionare con il Servizio app di Azure

Il server applicazioni WildFly del Servizio app di Azure per Linux viene eseguito in modalità autonoma, non in una configurazione di dominio. Quando si amplia il piano di Servizio app, ogni istanza di WildFly viene configurata come server autonomo.

Per scalare l'applicazione orizzontalmente o verticalmente, si usano le [regole di scalabilità](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) e si [aumenta il numero di istanze](../manage-scale-up.md?toc=/azure/app-service/containers/toc.json).

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

Specificare le [impostazioni dell'app](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) nella configurazione dell'applicazione per passare le variabili di ambiente da usare nello script. Le impostazioni dell'applicazione mantengono le stringhe di connessione e altri segreti necessari per configurare il controllo delle versioni dell'applicazione.

### <a name="install-modules-and-dependencies"></a>Installare moduli e dipendenze

Per installare i moduli e le relative dipendenze nel classpath WildFly tramite l'interfaccia della riga di comando di JBoss, è necessario creare i file seguenti nella propria directory. Per alcuni moduli e dipendenze può essere necessario specificare una configurazione aggiuntiva, ad esempio la denominazione JNDI o un'altra configurazione specifica dell'API. L'elenco che segue include gli elementi minimi necessari per configurare una dipendenza nella maggior parte dei casi.

- Un [descrittore di modulo XML](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Questo file XML definisce il nome, gli attributi e le dipendenze del modulo. Questo [file module.xml di esempio](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) definisce un modulo Postgres, la relativa dipendenza JDBC del file JAR e altre dipendenze del modulo richieste.
- Le dipendenze del file con estensione JAR necessarie per il modulo.
- Uno script con i comandi dell'interfaccia della riga di comando di JBoss per configurare il nuovo modulo. Questo file conterrà i comandi che si indica all'interfaccia della riga di comando di JBoss di eseguire per configurare il server per usare la dipendenza. Per la documentazione sui comandi per aggiungere moduli, origini dati e provider di messaggistica, fare riferimento a [questo documento](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- Uno script Bash di avvio per chiamare l'interfaccia della riga di comando di JBoss ed eseguire lo script del passaggio precedente. Questo file verrà eseguito quando l'istanza del servizio app viene riavviata o quando viene effettuato il provisioning di nuove istanze durante un'operazione di scalabilità orizzontale. Questo script di avvio è il punto in cui è possibile eseguire qualsiasi altra configurazione per l'applicazione, perché i comandi JBoss vengono passati all'interfaccia della riga di comando di JBoss. Questo file può essere come minimo un singolo comando per passare lo script di comandi della CLI di JBoss all'interfaccia della riga di comando di JBoss:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Dopo aver creato i file e il contenuto per il modulo, attenersi alla procedura seguente per aggiungere il modulo al server applicazioni WildFly.

1. Usare FTP per caricare i file in un percorso dell'istanza del servizio app nella directory */Home* , ad esempio */Home/site/Deployments/Tools*. Per altre informazioni, vedere [distribuire l'app nel servizio app Azure tramite FTP/S](../deploy-ftp.md).
2. Nella pagina **configurazione** > **impostazioni generali** della portale di Azure impostare il campo script di **avvio** sul percorso dello script della shell di avvio, ad esempio */Home/site/Deployments/Tools/Startup.sh*.
3. Riavviare l'istanza del servizio app premendo il pulsante **Riavvia** nella sezione **Panoramica** del portale o usando l'interfaccia della riga di comando di Azure.

### <a name="configure-data-sources"></a>Configurare le origini dati

Per configurare WildFly/JBoss per accedere a un'origine dati, usare il processo generale descritto in precedenza nella sezione "installare moduli e dipendenze". La sezione seguente fornisce dettagli specifici su questo processo per le origini dati PostgreSQL, MySQL e SQL Server.

Questa sezione presuppone che sia già presente un'app, un'istanza del servizio app e un'istanza del servizio di database di Azure. Le istruzioni riportate di seguito fanno riferimento al nome del servizio app, al relativo gruppo di risorse e alle informazioni di connessione al database. È possibile trovare queste informazioni nella portale di Azure.

Se si preferisce eseguire l'intero processo dall'inizio usando un'app di esempio, vedere [esercitazione: creare un'app Web Java EE e Postgres in Azure](tutorial-java-enterprise-postgresql-app.md).

I passaggi seguenti illustrano i requisiti per la connessione del servizio app e del database esistenti.

1. Scaricare il driver JDBC per [PostgreSQL](https://jdbc.postgresql.org/download.html), [MySQL](https://dev.mysql.com/downloads/connector/j/)o [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server). Decomprimere l'archivio scaricato per ottenere il file con estensione jar del driver.

2. Creare un file con un nome come *Module. XML* e aggiungere il markup seguente. Sostituire il segnaposto `<module name>` (incluse le parentesi angolari) con `org.postgres` per PostgreSQL, `com.mysql` per MySQL o `com.microsoft` per SQL Server. Sostituire `<JDBC .jar file path>` con il nome del file con estensione jar del passaggio precedente, incluso il percorso completo della posizione in cui si vuole inserire il file nell'istanza del servizio app. Può trattarsi di qualsiasi percorso nella directory */Home* .

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

6. Usare l'interfaccia della riga di comando di Azure per aggiungere impostazioni al servizio app che contengono le informazioni di connessione al database. Sostituire `<resource group>` e `<webapp name>` con i valori usati dal servizio app. Sostituire `<database server name>`, `<database name>`, `<admin name>`e `<admin password>` con le informazioni di connessione al database. È possibile ottenere il servizio app e le informazioni sul database dal portale di Azure.

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

    **MySQL**

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

    I valori di DATABASE_CONNECTION_URL sono diversi per ogni server di database e diversi dai valori della portale di Azure. I formati URL indicati qui (e nei frammenti di codice precedenti) sono obbligatori per l'uso da parte di WildFly:

    * **PostgreSQL:** `jdbc:postgresql://<database server name>:5432/<database name>?ssl=true`
    * **MySQL:** `jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT`
    * **SQL Server:** `jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;`

7. Nella portale di Azure passare al servizio app e trovare la pagina **configurazione** > **Impostazioni generali** . Impostare il campo **script di avvio** sul nome e il percorso dello script di avvio, ad esempio */Home/Startup.sh*.

Alla successiva riavvio del servizio app, verrà eseguito lo script di avvio ed eseguire i passaggi di configurazione necessari. Per verificare che la configurazione venga eseguita correttamente, è possibile accedere al servizio app usando SSH e quindi eseguire lo script di avvio dal prompt di bash. È anche possibile esaminare i log del servizio app. Per altre informazioni su queste opzioni, vedere [registrazione e debug di app](#logging-and-debugging-apps).

Successivamente, sarà necessario aggiornare la configurazione di WildFly per l'app e ridistribuirla. Seguire questa procedura:

1. Aprire il file *src/main/resources/META-INF/persistence. XML* per l'app e trovare l'elemento `<jta-data-source>`. Sostituire il relativo contenuto come illustrato di seguito:

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

### <a name="use-service-bus-as-a-message-broker"></a>Usare il bus di servizio come broker di messaggi

È possibile configurare WildFly e i fagioli basati su messaggi per usare il [bus di servizio di Azure](/azure/service-bus-messaging) come broker di messaggi. Dopo la configurazione, è possibile inviare e ricevere messaggi usando [Apache Qpid](https://qpid.apache.org) come client di Java Message Service (JMS). Sono necessari alcuni passaggi per configurare un adattatore di risorse JMS (JMS RA) che consentirà a Enterprise Java Beans (EJB) di configurare una factory di connessione JMS remota e una coda. Questa configurazione remota punterà al bus di servizio di Azure e userà il provider di Apache Qpid JMS per il protocollo AMQP.

I passaggi seguenti descrivono la configurazione e il codice necessari. Questi passaggi presuppongono che sia stata creata un'istanza del servizio app per ospitare Bean, uno spazio dei nomi del bus di servizio, una coda e un argomento con una sottoscrizione. Per informazioni sulla creazione di queste risorse, vedere:

- [Guida introduttiva: creare un'app Java nel servizio app Azure in Linux](/azure/app-service/containers/quickstart-java)
- [Guida introduttiva: usare l'interfaccia della riga di comando di Azure per creare una coda Service Bus](/azure/service-bus-messaging/service-bus-quickstart-cli)
- [Guida introduttiva: usare la portale di Azure per creare un argomento e sottoscrizioni del bus di servizio per l'argomento](/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal)

1. Aprire un terminale bash e usare i comandi seguenti per salvare le informazioni sulle risorse di Azure in variabili di ambiente. Sostituire i segnaposto (incluse le parentesi angolari) con i valori indicati.

    | Variabile            | Value                                                                      |
    |---------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME  | Nome del gruppo di risorse contenente l'istanza del servizio app.       |
    | WEBAPP_NAME         | Nome dell'istanza del servizio app.                                     |
    | AREA              | Nome dell'area in cui è ospitata l'app.                           |
    | DEFAULT_SBNAMESPACE | Nome dello spazio dei nomi del bus di servizio.                                    |
    | SB_SAS_POLICY       | Nome dei criteri di firma di accesso condiviso (SAS) per lo spazio dei nomi.   |
    | SB_SAS_KEY          | Chiave primaria o secondaria per i criteri SAS della coda.                  |
    | SB_QUEUE            | Nome della coda del bus di servizio.                                        |
    | SB_TOPIC            | Nome dell'argomento del bus di servizio.                                        |
    | SB_SUBSCRIPTION     | Nome della sottoscrizione dell'argomento.                                |

    ```bash
    RESOURCEGROUP_NAME=<resource group>
    WEBAPP_NAME=<web app>
    WEBAPP_PLAN_NAME=${WEBAPP_NAME}-appservice-plan
    REGION=<region>
    DEFAULT_SBNAMESPACE=<namespace>
    SB_SAS_POLICY=<SAS policy>
    SB_SAS_KEY=<SAS key>
    SB_QUEUE=<queue>
    SB_TOPIC=<topic>
    SB_SUBSCRIPTION=<subscription>
    PROVIDER_URL=amqps://${DEFAULT_SBNAMESPACE}.servicebus.windows.net?amqp.idleTimeout=120000
    ```

    Queste informazioni sono disponibili nel portale di Azure. Per i criteri e la chiave SAS, assicurarsi di usare i valori per lo spazio dei nomi in modo che l'app possa accedere sia alla propria coda che alla sottoscrizione dell'argomento. Per trovare questi valori nel portale di Azure, passare alla risorsa dello spazio dei nomi, selezionare **criteri di accesso condiviso**e quindi selezionare il criterio **RootManageSharedAccessKey** .

2. Scaricare il [provider JMS di Apache Qpid](https://qpid.apache.org/components/jms/index.html). Individuare i file con estensione jar nelle directory *lib* e *lib/optional* .

3. Creare un file denominato *Module. XML* e aggiungere il markup seguente. Sostituire ogni istanza del segnaposto `<version>` (incluse le parentesi angolari) con la versione corretta per ogni file con estensione jar, in modo che i nomi dei file corrispondano ai file estratti nel passaggio 1.

    ```xml
    <module xmlns="urn:jboss:module:1.1" name="org.jboss.genericjms.provider">
        <resources>
            <resource-root path="proton-j-<version>.jar"/>
            <resource-root path="qpid-jms-client-<version>.jar"/>
            <resource-root path="slf4j-log4j12-<version>.jar"/>
            <resource-root path="slf4j-api-<version>.jar"/>
            <resource-root path="log4j-<version>.jar"/>
            <resource-root path="netty-buffer-<version>.jar" />
            <resource-root path="netty-codec-<version>.jar" />
            <resource-root path="netty-codec-http-<version>.jar" />
            <resource-root path="netty-common-<version>.jar" />
            <resource-root path="netty-handler-<version>.jar" />
            <resource-root path="netty-resolver-<version>.jar" />
            <resource-root path="netty-transport-<version>.jar" />
            <resource-root path="netty-transport-native-epoll-<version>-linux-x86_64.jar" />
            <resource-root path="netty-transport-native-kqueue-<version>-osx-x86_64.jar" />
            <resource-root path="netty-transport-native-unix-common-<version>.jar" />
            <resource-root path="qpid-jms-discovery-<version>jar" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.jms.api"/>
        </dependencies>
    </module>
    ```

4. Creare un file denominato *Startup.sh* e aggiungere il codice seguente.

    ```bash
    echo "Generating jndi.properties file in /home/site/deployments/tools directory"
    echo "connectionfactory.mymdbconnection=amqps://${DEFAULT_SBNAMESPACE}.servicebus.windows.net?amqp.idleTimeout=120000&jms.username=${SB_SAS_POLICY}&jms.password=${SB_SAS_KEY}" > /home/site/deployments/tools/jndi.properties
    echo "queue.mymdbqueue=${SB_QUEUE}" >> /home/site/deployments/tools/jndi.properties
    echo "topic.mymdbtopic=${SB_TOPIC}" >> /home/site/deployments/tools/jndi.properties
    echo "queue.mymdbsubscription=${SB_TOPIC}/Subscriptions/${SB_SUBSCRIPTION}" >> /home/site/deployments/tools/jndi.properties
    echo "====== contents of /home/site/deployments/tools/jndi.properties ======"
    cat /home/site/deployments/tools/jndi.properties
    echo "====== EOF /home/site/deployments/tools/jndi.properties ======"
    echo "Generating commands.cli file for /home/site/deployments/tools directory"
    echo "# Start batching commands" > /home/site/deployments/tools/commands.cli
    echo "batch" >> /home/site/deployments/tools/commands.cli
    echo "# Configure the ee subsystem to enable MDB annotation property substitution" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=ee:write-attribute(name=annotation-property-replacement,value=true)" >> /home/site/deployments/tools/commands.cli
    echo "# Define system properties to be used in the substititution" >> /home/site/deployments/tools/commands.cli
    echo "/system-property=property.mymdb.queue:add(value=java:global/remoteJMS/mymdbqueue})" >> /home/site/deployments/tools/commands.cli
    echo "/system-property=property.mymdb.topic:add(value=java:global/remoteJMS/mymdbsubscription)" >> /home/site/deployments/tools/commands.cli
    echo "/system-property=property.connection.factory:add(value=java:global/remoteJMS/mymdbconnection)" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=ee:list-add(name=global-modules, value={\"name\" => \"org.jboss.genericjms.provider\", \"slot\" =>\"main\"}" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=naming/binding=\"java:global/remoteJMS\":add(binding-type=external-context,module=org.jboss.genericjms.provider,class=javax.naming.InitialContext,environment=[java.naming.factory.initial=org.apache.qpid.jms.jndi.JmsInitialContextFactory,org.jboss.as.naming.lookup.by.string=true,java.naming.provider.url=/home/site/deployments/tools/jndi.properties])" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=resource-adapters/resource-adapter=generic-ra:add(module=org.jboss.genericjms,transaction-support=XATransaction)" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd:add(class-name=org.jboss.resource.adapter.jms.JmsManagedConnectionFactory, jndi-name=java:/jms/mymdbconnection)" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd/config-properties=ConnectionFactory:add(value=mymdbconnection)" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd/config-properties=JndiParameters:add(value=\"java.naming.factory.initial=org.apache.qpid.jms.jndi.JmsInitialContextFactory;java.naming.provider.url=/home/site/deployments/tools/jndi.properties\")" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd:write-attribute(name=security-application,value=true)" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=ejb3:write-attribute(name=default-resource-adapter-name, value=generic-ra)" >> /home/site/deployments/tools/commands.cli
    echo "# Run the batch commands" >> /home/site/deployments/tools/commands.cli
    echo "run-batch" >> /home/site/deployments/tools/commands.cli
    echo "reload" >> /home/site/deployments/tools/commands.cli
    echo "====== contents of /home/site/deployments/tools/commands.cli ======"
    cat /home/site/deployments/tools/commands.cli
    echo "======= EOF /home/site/deployments/tools/commands.cli ========"
    mkdir /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider
    mkdir /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider/main
    cp  /home/site/deployments/tools/*.jar /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider/main/
    cp /home/site/deployments/tools/module.xml /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider/main/
    cp /home/site/deployments/tools/jndi.properties /opt/jboss/wildfly/standalone/configuration/
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/home/site/deployments/tools/commands.cli
    echo "Startup Run done"
    ```

    L'istanza del servizio app eseguirà lo script ogni volta che viene avviato, fornendo la configurazione aggiuntiva richiesta da WildFly. Questo script copia le dipendenze dell'app nei percorsi richiesti. Genera anche i file *JNDI. Properties* e *Commands. CLI* , che usano le variabili di ambiente illustrate nel passaggio 1. Questi valori vengono anche passati all'istanza del servizio app in un passaggio successivo.

    Il file *Commands. CLI* è uno script dell'interfaccia della riga di comando di [Wildfly](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) avviato dallo script di avvio. I comandi in questo file configurano JMS e JNDI, facendo uso del file *JNDI. Properties* . Questi comandi creano una connessione tra l'app e la coda o l'argomento del bus di servizio.

5. Usare FTP per caricare i file con estensione jar, il file *Module. XML* e il file *Startup.sh* nell'istanza del servizio app. Inserire *Startup.sh* nella directory */Home* e inserire gli altri file nella directory */Home/site/Deployments/Tools* Assicurarsi di caricare tutti i file con estensione jar elencati nel file *Module. XML* per ottenere la chiusura transitiva delle dipendenze. Per altre informazioni su FTP, vedere [distribuire l'app nel servizio app Azure tramite FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

6. Aggiornare l'implementazione di MessageListener per aggiungere le istruzioni `import` seguenti:

    ```java
    import javax.ejb.ActivationConfigProperty;
    import javax.ejb.MessageDriven;
    import javax.ejb.TransactionAttribute;
    import javax.ejb.TransactionAttributeType;
    import javax.ejb.TransactionManagement;
    import javax.ejb.TransactionManagementType;
    import javax.jms.JMSException;
    import javax.jms.Message;
    import javax.jms.MessageListener;
    import javax.jms.TextMessage;
    ```

7. Aggiornare quindi le annotazioni della classe listener in modo che corrispondano all'esempio seguente. Questa classe fornisce un'implementazione di esempio che registra la ricezione dei messaggi.

    ```java
    @TransactionManagement(TransactionManagementType.BEAN)
    @TransactionAttribute(TransactionAttributeType.NOT_SUPPORTED)
    @MessageDriven(name = "MyQueueListener", activationConfig = {
            @ActivationConfigProperty(propertyName = "connectionFactory", propertyValue = "${property.connection.factory}"),
            @ActivationConfigProperty(propertyName = "destinationLookup", propertyValue = "${property.mymdb.queue}"),
            @ActivationConfigProperty(propertyName = "destinationType", propertyValue = "javax.jms.Queue"),
            @ActivationConfigProperty(propertyName = "acknowledgeMode", propertyValue = "Auto-acknowledge") })
    public class MyQueueListener implements MessageListener {

        private static final Logger LOGGER = Logger.getLogger(TopicListener.class.toString());

        public void onMessage(Message rcvMessage) {
            TextMessage msg = null;
            try {
                if (rcvMessage instanceof TextMessage) {
                    msg = (TextMessage) rcvMessage;
                    LOGGER.info("Received Message from topic: " + msg.getText());
                } else {
                    LOGGER.warning("Message of wrong type: " + rcvMessage.getClass().getName());
                }
            } catch (JMSException e) {
                LOGGER.warning("Exception on message : " + e.getMessage());
                throw new RuntimeException(e);
            }
        }
    }
    ```

    I valori `connectionFactory` e `destinationLookup` fanno riferimento ai valori della proprietà di sistema WildFly configurati dallo script *Startup.sh* . Il valore `destinationType` è `javax.jms.Queue`, a indicare che si sta effettuando la connessione a un'istanza di coda del bus di servizio. Questo valore deve essere `javax.jms.Topic` quando ci si connette a un argomento del bus di servizio, come illustrato di seguito:

    ```java
    @TransactionManagement(TransactionManagementType.BEAN)
    @TransactionAttribute(TransactionAttributeType.NOT_SUPPORTED)
    @MessageDriven(name = "MyTopicListener", activationConfig = {
            @ActivationConfigProperty(propertyName = "connectionFactory", propertyValue = "${property.connection.factory}"),
            @ActivationConfigProperty(propertyName = "destinationLookup", propertyValue = "${property.mymdb.topic}"),
            @ActivationConfigProperty(propertyName = "destinationType", propertyValue = "javax.jms.Topic"),
            @ActivationConfigProperty(propertyName = "acknowledgeMode", propertyValue = "Auto-acknowledge") })
        public class MyTopicListener implements MessageListener {
        // ...
    }
    ```

8. Aggiornare la sezione `dependencies` del file *POM. XML* per aggiungere le dipendenze seguenti:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.qpid</groupId>
            <artifactId>qpid-jms-client</artifactId>
            <version>0.40.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.qpid</groupId>
            <artifactId>proton-j</artifactId>
            <version>0.31.0</version>
        </dependency>
        <dependency>
            <groupId>javax.enterprise</groupId>
            <artifactId>cdi-api</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.jboss.spec.javax.ejb</groupId>
            <artifactId>jboss-ejb-api_3.2_spec</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.jboss.spec.javax.jms</groupId>
            <artifactId>jboss-jms-api_2.0_spec</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.jboss.spec.javax.servlet</groupId>
            <artifactId>jboss-servlet-api_4.0_spec</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.jboss.spec.javax.annotation</groupId>
            <artifactId>jboss-annotations-api_1.3_spec</artifactId>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

9. Aggiornare la configurazione del `azure-webapp-maven-plugin` nel file *POM. XML* per fare riferimento alle informazioni sull'account del bus di servizio. Se necessario, sostituire `1.7.0` con la versione corrente del [plug-in Maven per il servizio app di Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>

            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>

            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>wildfly 14-jre8</linuxRuntime>

            <appSettings>
                <property>
                    <name>DEFAULT_SBNAMESPACE</name>
                    <value>${DEFAULT_SBNAMESPACE}</value>
                </property>
                <property>
                    <name>SB_SAS_POLICY</name>
                    <value>${SB_SAS_POLICY}</value>
                </property>
                <property>
                    <name>SB_SAS_KEY</name>
                    <value>${SB_SAS_KEY}</value>
                </property>
                <property>
                    <name>PROVIDER_URL</name>
                    <value>${PROVIDER_URL}</value>
                </property>
                <property>
                    <name>SB_QUEUE</name>
                    <value>${SB_QUEUE}</value>
                </property>
                <property>
                    <name>SB_TOPIC</name>
                    <value>${SB_TOPIC}</value>
                </property>
                <property>
                    <name>SB_SUBSCRIPTION</name>
                    <value>${SB_SUBSCRIPTION}</value>
                </property>
            </appSettings>
        </configuration>
    </plugin>
    ```

    Queste impostazioni consentono di configurare l'istanza del servizio app in modo che abbia le stesse variabili di ambiente impostate localmente. Usa le variabili di ambiente per tenere le informazioni sull'account dai file di origine.

10. Ricompilare e ridistribuire l'app.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

Il fagiolo basato su messaggi è ora configurato per l'uso del bus di servizio come meccanismo di messaggistica.

Alla successiva riavvio del servizio app, verrà eseguito lo script di avvio ed eseguire i passaggi di configurazione necessari. Per verificare che la configurazione venga eseguita correttamente, è possibile accedere al servizio app usando SSH e quindi eseguire lo script di avvio dal prompt di bash. È anche possibile esaminare i log del servizio app. Per altre informazioni su queste opzioni, vedere [registrazione e debug di app](#logging-and-debugging-apps).

Per un esempio che è possibile usare per testare queste istruzioni, vedere il repository [migrate-Java-EE-app-to-Azure-2](https://github.com/Azure-Samples/migrate-java-ee-app-to-azure-2) su GitHub e cercare l'esempio `helloworld-mdb-propertysubstitution`.

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Usare Redis come cache di sessione con Tomcat

È possibile configurare Tomcat per l'uso di un archivio di sessione esterno, ad esempio [cache di Azure per Redis](/azure/azure-cache-for-redis/). Questo consente di mantenere lo stato della sessione utente, ad esempio i dati del carrello acquisti, quando un utente viene trasferito a un'altra istanza dell'app, ad esempio quando si verifica la scalabilità automatica, il riavvio o il failover.

Per usare Tomcat con Redis, è necessario configurare l'app per l'uso di un'implementazione di [PersistentManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) . I passaggi seguenti illustrano questo processo usando [pivotal Session Manager: Redis-Store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) come esempio.

1. Aprire un terminale bash e usare `<variable>=<value>` per impostare ognuna delle variabili di ambiente seguenti.

    | Variabile                 | Value                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | Nome del gruppo di risorse contenente l'istanza del servizio app.       |
    | WEBAPP_NAME              | Nome dell'istanza del servizio app.                                     |
    | WEBAPP_PLAN_NAME         | Nome del piano di servizio app.                                         |
    | AREA                   | Nome dell'area in cui è ospitata l'app.                           |
    | REDIS_CACHE_NAME         | Nome della cache di Azure per l'istanza di Redis.                           |
    | REDIS_PORT               | Porta SSL su cui è in ascolto la cache Redis.                             |
    | REDIS_PASSWORD           | Chiave di accesso primaria per l'istanza di.                                  |
    | REDIS_SESSION_KEY_PREFIX | Valore specificato per identificare le chiavi della sessione provenienti dall'app. |

    ```bash
    RESOURCEGROUP_NAME=<resource group>
    WEBAPP_NAME=<web app>
    WEBAPP_PLAN_NAME=<App Service plan>
    REGION=<region>
    REDIS_CACHE_NAME=<cache>
    REDIS_PORT=<port>
    REDIS_PASSWORD=<access key>
    REDIS_SESSION_KEY_PREFIX=<prefix>
    ```

    È possibile trovare il nome, la porta e le informazioni sulla chiave di accesso nel portale di Azure cercando nelle sezioni **Proprietà** o **chiavi di accesso** dell'istanza del servizio.

2. Creare o aggiornare il file *src/main/webapp/META-INF/context. XML* dell'app con il contenuto seguente:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Context path="">
        <!-- Specify Redis Store -->
        <Valve className="com.gopivotal.manager.SessionFlushValve" />
        <Manager className="org.apache.catalina.session.PersistentManager">
            <Store className="com.gopivotal.manager.redis.RedisStore"
                   connectionPoolSize="20"
                   host="${REDIS_CACHE_NAME}.redis.cache.windows.net"
                   port="${REDIS_PORT}"
                   password="${REDIS_PASSWORD}"
                   sessionKeyPrefix="${REDIS_SESSION_KEY_PREFIX}"
                   timeout="2000"
            />
        </Manager>
    </Context>
    ```

    Questo file specifica e configura l'implementazione di gestione sessioni per l'app. Usa le variabili di ambiente impostate nel passaggio precedente per evitare che le informazioni dell'account siano presenti nei file di origine.

3. Usare FTP per caricare il file JAR del gestore sessioni nell'istanza del servizio app, inserendolo nella directory */Home/Tomcat/lib* Per altre informazioni, vedere [distribuire l'app nel servizio app Azure tramite FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

4. Disabilitare il [cookie di affinità di sessione](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) per l'istanza del servizio app. È possibile eseguire questa operazione dal portale di Azure passando all'app e quindi impostando **configurazione > Impostazioni generali > affinità arr** su **disattivato**. In alternativa, è possibile usare il comando seguente:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    Per impostazione predefinita, il servizio App utilizzerà i cookie di affinità di sessione per garantire che le richieste client con sessioni esistenti vengano indirizzate alla stessa istanza dell'applicazione. Questo comportamento predefinito non richiede alcuna configurazione ma non può mantenere lo stato della sessione utente quando l'istanza dell'app viene riavviata o quando il traffico viene reindirizzato a un'altra istanza. Quando si [Disabilita la configurazione di affinità dell'istanza di Arr esistente](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) per disattivare il routing basato su cookie di sessione, l'archivio di sessioni configurato funziona senza interferenze.

5. Passare alla sezione delle **Proprietà** dell'istanza del servizio app e trovare **altri indirizzi IP in uscita**. Rappresentano tutti i possibili indirizzi IP in uscita per l'app. Copiarli per l'uso nel passaggio successivo.

6. Per ogni indirizzo IP, creare una regola del firewall nella cache di Azure per l'istanza di Redis. Questa operazione può essere eseguita nel portale di Azure dalla sezione **Firewall** dell'istanza di Redis. Specificare un nome univoco per ogni regola e impostare i valori indirizzo IP **iniziale** e **indirizzo IP finale** sullo stesso indirizzo IP.

7. Passare alla sezione **Impostazioni avanzate** dell'istanza di redis e impostare **Consenti accesso solo tramite SSL** su **No**. Ciò consente all'istanza del servizio app di comunicare con la cache Redis tramite l'infrastruttura di Azure.

8. Aggiornare la configurazione del `azure-webapp-maven-plugin` nel file *POM. XML* dell'app per fare riferimento alle informazioni dell'account Redis. Questo file usa le variabili di ambiente impostate in precedenza per evitare che le informazioni dell'account siano presenti nei file di origine.

    Se necessario, sostituire `1.7.0` con la versione corrente del [plug-in Maven per il servizio app di Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>
            <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>

            <appSettings>
                <property>
                    <name>REDIS_CACHE_NAME</name>
                    <value>${REDIS_CACHE_NAME}</value>
                </property>
                <property>
                    <name>REDIS_PORT</name>
                    <value>${REDIS_PORT}</value>
                </property>
                <property>
                    <name>REDIS_PASSWORD</name>
                    <value>${REDIS_PASSWORD}</value>
                </property>
                <property>
                    <name>REDIS_SESSION_KEY_PREFIX</name>
                    <value>${REDIS_SESSION_KEY_PREFIX}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Xms2048m -Xmx2048m -DREDIS_CACHE_NAME=${REDIS_CACHE_NAME} -DREDIS_PORT=${REDIS_PORT} -DREDIS_PASSWORD=${REDIS_PASSWORD} IS_SESSION_KEY_PREFIX=${REDIS_SESSION_KEY_PREFIX}</value>
                </property>

            </appSettings>

        </configuration>
    </plugin>
    ```

9. Ricompilare e ridistribuire l'app.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

L'app userà ora la cache Redis per la gestione delle sessioni.

Per un esempio che è possibile usare per testare queste istruzioni, vedere il repository [scaling-state-Java-Web-App-on-Azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) su GitHub.

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

---
title: Configurare le app Java Linux
description: Scopri come configurare un contenitore Java predefinito per la tua app. Questo articolo illustra le attività di configurazione più comuni.
keywords: servizio app azure, app web, linux, oss, java, java ee, jee, javaee
author: bmitchell287
manager: barbkess
ms.devlang: java
ms.topic: article
ms.date: 11/22/2019
ms.author: brendm
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: f4f6de807628704051cdddf74bcefbed678f8fcd
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457893"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Configurare un'app Java in Linux per il servizio app di Azure

Il servizio app di Azure su Linux consente agli sviluppatori Java di compilare, distribuire e ridimensionare rapidamente le applicazioni Web in pacchetto Tomcat o Java Standard Edition (SE) in un servizio basato su Linux completamente gestito. Distribuire le applicazioni con i plug-in Maven dalla riga di comando o in editor come IntelliJ, Eclipse o Visual Studio Code.

Questa guida fornisce concetti chiave e istruzioni per gli sviluppatori Java che usano un contenitore Linux incorporato nel servizio app. Se non è mai stato usato il servizio app di Azure, seguire la [guida introduttiva](quickstart-java.md)di Java .

## <a name="deploying-your-app"></a>Distribuzione dell'app

È possibile usare [Maven Plugin per il servizio app di Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) per distribuire file CON estensione jar e war. La distribuzione con IDE più diffusi è supportata anche con [Azure Toolkit per IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) o [Azure Toolkit per Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

In caso contrario, il metodo di distribuzione dipenderà dal tipo di archivio:Otherwise, your deployment method will depend on your archive type:

- Per distribuire file con estensione war in Tomcat, usare l'endpoint `/api/wardeploy/` per eseguire il comando POST per il file di archivio. Per altre informazioni su questa API, vedere [questa documentazione](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Per distribuire file con estensione jar nelle immagini di Java SE, usare l'endpoint `/api/zipdeploy/` del sito Kudu. Per altre informazioni su questa API, vedere [questa documentazione](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Non distribuire file con estensione war o jar tramite FTP. Lo strumento FTP è stato progettato per caricare script di avvio, dipendenze o altri file di runtime. Non è la scelta ottimale per la distribuzione di app Web.

## <a name="logging-and-debugging-apps"></a>Registrazione e debug delle app

Nel portale di Azure sono disponibili report sulle prestazioni, visualizzazioni del traffico e controlli dell'integrità per ogni app. Per altre informazioni, vedere [Panoramica](../overview-diagnostics.md)della diagnostica del servizio app di Azure.For more information, see Azure App Service diagnostics overview .

### <a name="ssh-console-access"></a>Accesso alla console SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Eseguire lo streaming dei log di diagnostica

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Per ulteriori informazioni, consultate [Eseguire lo streaming dei log in Cloud Shell.](../troubleshoot-diagnostic-logs.md#in-cloud-shell)

### <a name="app-logging"></a>Registrazione dell'app

Abilitare la [registrazione dell'applicazione](../troubleshoot-diagnostic-logs.md?toc=/azure/app-service/containers/toc.json#enable-application-logging-windows) tramite il portale di Azure o l'[interfaccia della riga di comando di Azure](/cli/azure/webapp/log#az-webapp-log-config) per configurare il servizio app per scrivere l'output della console standard dell'applicazione e i flussi di errori della console standard nel file system locale o in Archiviazione BLOB di Azure. La registrazione nell'istanza del file system del servizio app locale viene disabilitata 12 ore dopo la configurazione. Se è necessario un periodo di conservazione più lungo, configurare l'applicazione per scrivere l'output in un contenitore di archiviazione BLOB. I log dell'app Java e Tomcat sono disponibili nella directory */home/LogFiles/Application/.*

Se l'applicazione usa [Logback](https://logback.qos.ch/) o [Log4j](https://logging.apache.org/log4j) per la traccia, è possibile inoltrare queste tracce per la revisione ad Azure Application Insights usando le istruzioni di configurazione del framework di registrazione illustrate in [Esplorare i log di traccia Java in Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Strumenti per la risoluzione dei problemi

Le immagini Java integrate sono basate sul sistema operativo [Alpine Linux.](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) Utilizzare `apk` Gestione pacchetti per installare eventuali strumenti o comandi per la risoluzione dei problemi.

### <a name="flight-recorder"></a>Flight Recorder

Tutte le immagini Java di Linux nel servizio App hanno installato il registratore di volo di .usull in modo da potersi facilmente connettere alla JVM e avviare una registrazione del profiler o generare un dump dell'heap.

#### <a name="timed-recording"></a>Registrazione a tempo

Per iniziare, SSH nel servizio app `jcmd` ed eseguire il comando per visualizzare un elenco di tutti i processi Java in esecuzione. Oltre a jcmd stesso, si dovrebbe vedere l'applicazione Java in esecuzione con un numero ID processo (pid).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Eseguire il comando seguente per avviare una registrazione di 30 secondi della JVM. In questo modo verrà profilata la JVM e verrà creato un file JFR denominato *jfr_example.jfr* nella home directory. (Sostituire 116 con il pid dell'app Java.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Durante l'intervallo di 30 secondi, è `jcmd 116 JFR.check`possibile convalidare che la registrazione è in corso eseguendo . Questo mostrerà tutte le registrazioni per il processo Java specificato.

#### <a name="continuous-recording"></a>Registrazione continua

È possibile utilizzare il registratore di volo di .unell per profilare continuamente l'applicazione Java con un impatto minimo sulle prestazioni di runtime ([source](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). A tale scopo, eseguire il comando dell'interfaccia della riga di comando di Azure seguente per creare un'impostazione dell'app denominata JAVA_OPTS con la configurazione necessaria. Il contenuto dell'impostazione dell'app JAVA_OPTS viene passato al comando all'avvio `java` dell'app.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Una volta avviata la registrazione, è possibile eseguire `JFR.dump` il dump dei dati di registrazione correnti in qualsiasi momento utilizzando il comando.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Per ulteriori informazioni, vedere la Guida di riferimento ai [comandi Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analisi delle registrazioni

Utilizzare [FTPS](../deploy-ftp.md) per scaricare il file JFR nel computer locale. Per analizzare il file JFR, scaricare e installare [il Mission Control di zulu](https://www.azul.com/products/zulu-mission-control/). Per istruzioni sul controllo missione zulu, vedere la documentazione di [Azul](https://docs.azul.com/zmc/) e le istruzioni di [installazione.](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)

## <a name="customization-and-tuning"></a>Personalizzazione e ottimizzazione

Il servizio app di Azure per Linux supporta l'ottimizzazione e la personalizzazione all'esterno della scatola tramite il portale di Azure e l'interfaccia della riga di comando. Esaminare gli articoli seguenti per la configurazione di app Web non specifiche di Java:

- [Configurare le impostazioni dell'app](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)
- [Configurare un nome di dominio](../app-service-web-tutorial-custom-domain.md?toc=/azure/app-service/containers/toc.json)
- [Configurare i binding SSL](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)
- [Aggiungere una rete CDN](../../cdn/cdn-add-to-web-app.md?toc=/azure/app-service/containers/toc.json)
- [Configurare il sito Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Impostare le opzioni di runtime Java

Per impostare la memoria allocata o altre opzioni di runtime JVM `JAVA_OPTS` in entrambi gli ambienti Tomcat e Java SE, creare [un'impostazione dell'app](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) denominata con le opzioni. Il servizio app in Linux passa questa impostazione come variabile di ambiente al runtime Java quando viene avviato.

Nel portale di Azure, sotto **Impostazioni applicazione** per l'app Web creare una nuova impostazione dell'app denominata `JAVA_OPTS` che includa le impostazioni aggiuntive, ad esempio `-Xms512m -Xmx1204m`.

Per configurare l'impostazione dell'app dal plug-in Maven, aggiungere tag di impostazione/valore nella sezione del plug-in di Azure.To configure the app setting from the Maven plugin, add setting/value tags in the Azure plugin section. L'esempio seguente imposta una dimensione minima e massima dell'heap Java specifica:

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

Se si distribuisce un'applicazione JAR, deve essere denominata *app.jar* in modo che l'immagine incorporata possa identificare correttamente l'app. (Il plugin Maven fa questa ridenominazione automaticamente.) Se non si desidera rinominare il file JAR in *app.jar*, è possibile caricare uno script della shell con il comando per eseguire il file JAR. Incollare quindi il percorso completo di questo script nella casella di testo [File di avvio](app-service-linux-faq.md#built-in-images) nella sezione Configurazione del portale. Lo script di avvio non viene eseguito dalla directory in cui si trova. Pertanto, usare sempre percorsi assoluti per fare riferimento ai file dello script di avvio, ad esempio `java -jar /home/myapp/myapp.jar`.

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

### <a name="adjust-startup-timeout"></a>Regolare il timeout di avvio

Se l'applicazione Java è particolarmente grande, è necessario aumentare il limite di tempo di avvio. A tale scopo, creare `WEBSITES_CONTAINER_START_TIME_LIMIT` un'impostazione dell'applicazione e impostarla sul numero di secondi che il servizio app deve attendere prima del timeout. Il valore `1800` massimo è secondi.

### <a name="pre-compile-jsp-files"></a>Precompilazione di file JSP

Per migliorare le prestazioni delle applicazioni Tomcat, è possibile compilare i file JSP prima della distribuzione nel servizio app. È possibile utilizzare il [plugin Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) fornito da Apache Sling, o utilizzando questo file di [compilazione Ant](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Proteggere le applicazioni

Le applicazioni Java in esecuzione nel servizio app per Linux hanno lo stesso set di [procedure consigliate per la sicurezza](/azure/security/security-paas-applications-using-app-services) delle altre applicazioni.

### <a name="authenticate-users-easy-auth"></a>Autenticare gli utenti (Easy Auth)

Configurare l'autenticazione dell'app nel portale di Azure con l'opzione **Autenticazione e autorizzazione.** che consente di abilitare l'autenticazione usando Azure Active Directory o gli account di accesso ai social network, ad esempio Facebook, Google o GitHub. La configurazione nel portale di Azure funziona solo quando si configura un singolo provider di autenticazione. Per altre informazioni, vedere [Configurare un'applicazione dei servizi app per usare l'account di accesso di Azure Active Directory](../configure-authentication-provider-aad.md?toc=/azure/app-service/containers/toc.json) e gli articoli correlati per gli altri provider di identità. Se è necessario abilitare più provider di accesso, seguire le istruzioni dell'articolo [Personalizzare l'autenticazione nel servizio app](../app-service-authentication-how-to.md?toc=/azure/app-service/containers/toc.json).

#### <a name="tomcat"></a>Tomcat

L'applicazione Tomcat può accedere alle attestazioni dell'utente direttamente dal servlet eseguendo il cast dell'oggetto Principal a un oggetto Map. L'oggetto Map eserciterà ogni tipo di attestazione a una raccolta di attestazioni per quel tipo. Nel codice riportato di seguito è `request` un'istanza di `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

A questo punto `Map` è possibile esaminare l'oggetto per ottenere qualsiasi attestazione specifica. Ad esempio, il frammento di codice seguente scorre tutti i tipi di attestazione e stampa il contenuto di ogni raccolta.

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

Per disconnettere gli `/.auth/ext/logout` utenti, utilizzare il percorso. Per eseguire altre azioni, vedere la documentazione [sull'utilizzo dell'autorizzazione e dell'autenticazione](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)del servizio app . C'è anche la documentazione ufficiale sull'interfaccia Tomcat [HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) e sui relativi metodi. Anche i seguenti metodi servlet vengono idratati in base alla configurazione del servizio app:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Per disattivare questa funzionalità, `WEBSITE_AUTH_SKIP_PRINCIPAL` creare un'impostazione dell'applicazione denominata con un valore di `1`. Per disabilitare tutti i filtri servlet aggiunti `WEBSITE_SKIP_FILTERS` dal servizio `1`app, creare un'impostazione denominata con un valore di .

#### <a name="spring-boot"></a>Spring Boot

Gli sviluppatori Spring Boot possono usare l'[utilità di avvio Spring Boot per Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) per proteggere le applicazioni usando le familiari annotazioni e API di Spring Security. Assicurarsi di aumentare la dimensione massima dell'intestazione nel file *application.properties.* È consigliabile il valore `16384`.

### <a name="configure-tlsssl"></a>Configurare TLS/SSL

Seguire le istruzioni in [Proteggere un nome DNS personalizzato con un'associazione SSL nel servizio app](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json) di Azure per caricare un certificato SSL esistente e associarlo al nome di dominio dell'applicazione. Per impostazione predefinita, l'applicazione consentirà ancora le connessioni HTTP. Seguire i passaggi specifici dell'esercitazione per applicare SSL e TLS.

### <a name="use-keyvault-references"></a>Usare i riferimenti KeyVaultUse KeyVault References

[Azure KeyVault](../../key-vault/general/overview.md) offre una gestione segreta centralizzata con criteri di accesso e cronologia di controllo. È possibile archiviare segreti (ad esempio password o stringhe di connessione) in KeyVault e accedere a questi segreti nell'applicazione tramite variabili di ambiente.

In primo luogo, seguire le istruzioni per [concedere all'app l'accesso a Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e creare un riferimento [KeyVault al segreto in un'impostazione dell'applicazione](../app-service-key-vault-references.md#reference-syntax). È possibile convalidare che il riferimento viene risolto nel segreto stampando la variabile di ambiente mentre si accede in remoto al terminale del servizio app.

Per inserire questi segreti nel file di configurazione Spring o`${MY_ENV_VAR}`Tomcat, utilizzare la sintassi di inserimento delle variabili di ambiente ( ). Per i file di configurazione di Spring, consultare questa documentazione sulle [configurazioni esternalizzate](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

### <a name="using-the-java-key-store"></a>Utilizzo dell'archivio chiavi Java

Per impostazione predefinita, tutti i certificati pubblici o privati [caricati](../configure-ssl-certificate.md) in App Service Linux verranno caricati nei rispettivi archivi di chiavi Java all'avvio del contenitore. Dopo aver caricato il certificato, è necessario riavviare il servizio app per caricarlo nell'archivio chiavi Java. I certificati pubblici vengono caricati nell'archivio chiavi in `$JAVA_HOME/jre/lib/security/cacerts`e i certificati privati vengono archiviati in `$JAVA_HOME/lib/security/client.jks`.

Potrebbe essere necessaria una configurazione aggiuntiva per crittografare la connessione JDBC con i certificati nell'archivio delle chiavi Java. Consultare la documentazione del driver JDBC scelto.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [Mongodb](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>Inizializzazione dell'archivio chiavi Java

Per inizializzare l'oggetto, `import java.security.KeyStore` caricare il file keystore con la password. La password predefinita per entrambi gli archivi chiavi è "changeit".

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>Caricare manualmente l'archivio chiavi

È possibile caricare manualmente i certificati nell'archivio chiavi. Creare un'impostazione dell'app, `SKIP_JAVA_KEYSTORE_LOAD`, con il valore di `1` per disabilitare il caricamento automatico dei certificati nell'archivio chiavi da parte del servizio app. Tutti i certificati pubblici caricati nel servizio `/var/ssl/certs/`app tramite il portale di Azure vengono archiviati in . I certificati privati `/var/ssl/private/`vengono archiviati in .

È possibile interagire o eseguire il debug dello strumento chiave Java aprendo una [connessione SSH](app-service-linux-ssh-support.md) al servizio app ed eseguendo il comando `keytool`. Per un elenco dei comandi, vedere la [documentazione relativa allo strumento chiave.](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) Per ulteriori informazioni sull'API KeyStore, fare riferimento [alla documentazione ufficiale](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

## <a name="configure-apm-platforms"></a>Configurare le piattaforme APM

Questa sezione illustra come connettere le applicazioni Java distribuite nel servizio app di Azure in Linux con le piattaforme NewRelic e AppDynamics per il monitoraggio delle prestazioni delle applicazioni (APM).

### <a name="configure-new-relic"></a>Configurare New Relic

1. Creare un account NewRelic in [NewRelic.com](https://newrelic.com/signup)
2. Scaricare l'agente Java da NewRelic, avrà un nome di file simile a *newrelic-java-x.x.x.zip*.
3. Copiare il codice di licenza, che sarà necessario per configurare l'agente in un secondo momento.
4. [SSH nell'istanza del servizio app](app-service-linux-ssh-support.md) e creare una nuova directory */home/site/wwwroot/apm*.
5. Caricare i file dell'agente Java NewRelic non compressi in una directory in */home/site/wwwroot/apm*. I file per l'agente devono essere in */home/site/wwwroot/apm/newrelic*.
6. Modificare il file YAML in */home/site/wwwroot/apm/newrelic/newrelic.yml* e sostituire il valore della licenza segnaposto con il proprio codice di licenza.
7. Nel portale di Azure passare all'applicazione nel servizio app e creare una nuova impostazione dell'applicazione.
    - Se l'app usa **Java SE**, creare una variabile di ambiente denominata `JAVA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se si usa **Tomcat**, creare una variabile di ambiente denominata `CATALINA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Configurare AppDynamics

1. Creare un account AppDynamics in [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Scaricare l'agente Java dal sito Web AppDynamics, il nome del file sarà simile a *AppServerAgent-x.x.x.xxxxx.zip*
3. [SSH nell'istanza del servizio app](app-service-linux-ssh-support.md) e creare una nuova directory */home/site/wwwroot/apm*.
4. Caricare i file dell'agente Java in una directory in */home/site/wwwroot/apm*. I file per l'agente devono essere in */home/site/wwwroot/apm/appdynamics*.
5. Nel portale di Azure passare all'applicazione nel servizio app e creare una nuova impostazione dell'applicazione.
    - Se si usa **Java SE**, creare una variabile di ambiente denominata `JAVA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` in cui `<app-name>` è il nome del servizio app.
    - Se si usa **Tomcat**, creare una variabile di ambiente denominata `CATALINA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` in cui `<app-name>` è il nome del servizio app.

> [!NOTE]
> Se si dispone già di una variabile di ambiente per `JAVA_OPTS` oppure `CATALINA_OPTS`, aggiungere l'opzione `-javaagent:/...` alla fine del valore corrente.

## <a name="configure-jar-applications"></a>Configurare le applicazioni JAR

### <a name="starting-jar-apps"></a>Avvio delle app JAR

Per impostazione predefinita, il servizio app prevede che l'applicazione JAR sia denominata *app.jar*. Se ha questo nome, verrà eseguito automaticamente. Per gli utenti Maven, è possibile `<finalName>app</finalName>` impostare il nome JAR includendo nella `<build>` sezione del *file pom.xml*. [È possibile eseguire la stessa operazione in Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) impostando la `archiveFileName` proprietà.

Se si desidera utilizzare un nome diverso per il file JAR, è necessario fornire anche il comando di [avvio](app-service-linux-faq.md#built-in-images) che esegue il file JAR. Ad esempio: `java -jar my-jar-app.jar`. È possibile impostare il valore del comando di avvio nel portale, `STARTUP_COMMAND`in Configurazione > Impostazioni generali o con un'impostazione dell'applicazione denominata .

### <a name="server-port"></a>Porta server

Servizio app Linux instrada le richieste in ingresso alla porta 80, in modo che l'applicazione deve rimanere in ascolto anche sulla porta 80. È possibile eseguire questa operazione nella configurazione dell'applicazione (ad esempio il file *application.properties* di Spring) o nel comando di avvio (ad esempio, `java -jar spring-app.jar --server.port=80`). Vedere la documentazione seguente per i framework Java comuni:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronauta](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Play Framework](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Origini dati

### <a name="tomcat"></a>Tomcat

Queste istruzioni si applicano a tutte le connessioni di database. È necessario sostituire i segnaposto con il nome della classe del driver del database scelto e il file JAR. Viene fornita una tabella con i nomi delle classi e i download dei driver per i database più comuni.

| Database   | Nome della classe del driver                             | Driver JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Scarica](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Scaricare](https://dev.mysql.com/downloads/connector/j/) (selezionare "Indipendente dalla piattaforma") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Scarica](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Per configurare Tomcat per l'utilizzo di Java Database Connectivity (JDBC) o JPEG (Java Persistence API), personalizzare innanzitutto la `CATALINA_OPTS` variabile di ambiente letta da Tomcat all'avvio. Impostare questi valori tramite un'impostazione app nel [plug-in Maven del servizio app](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

In alternativa, impostare le variabili di ambiente nella pagina Impostazioni applicazione di configurazione nel portale di Azure.Or set the environment variables in the **Configuration** > **Application Settings** page in the Azure portal.

Determinare quindi se l'origine dati deve essere disponibile per un'applicazione o per tutte le applicazioni in esecuzione nel servlet Tomcat.

#### <a name="application-level-data-sources"></a>Origini dati a livello di applicazioneApplication-level data sources

1. Creare un file *context.xml* nella directory *META-INF/* del progetto. Creare la directory *META-INF/* se non esiste.

2. In *context.xml*aggiungere `Context` un elemento per collegare l'origine dati a un indirizzo JNDI. Sostituire il segnaposto `driverClassName` con il nome della classe del driver indicato nella tabella precedente.

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

3. Aggiornare il *file web.xml* dell'applicazione per utilizzare l'origine dati nell'applicazione.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Risorse condivise a livello di server

L'aggiunta di un'origine dati condivisa a livello di server richiederà la modifica del file server.xml di Tomcat. Innanzitutto, caricare uno script di [avvio](app-service-linux-faq.md#built-in-images) e impostare il percorso dello script in **Configuration** > **Startup Startup Command**. È possibile caricare lo script di avvio utilizzando [FTP](../deploy-ftp.md).

Lo script di avvio eseguirà una [trasformazione xsl](https://www.w3schools.com/xml/xsl_intro.asp) nel `/usr/local/tomcat/conf/server.xml`file server.xml e restituisce il file xml risultante in . Lo script di avvio deve installare libxslt tramite apk. Il file xsl e lo script di avvio possono essere caricati tramite FTP. Di seguito è riportato uno script di avvio di esempio.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Di seguito viene fornito un esempio di file xsl. Il file xsl di esempio aggiunge un nuovo nodo del connettore al file Tomcat server.xml.

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

Infine, inserisci i jar del driver nel percorso di classe Tomcat e riavvia il servizio app.

1. Assicurarsi che i file del driver JDBC siano disponibili per il classloader Tomcat inserendoli nella directory */home/tomcat/lib.* (Creare questa directory se non esiste già.) Per caricare questi file nell'istanza del servizio app, eseguire la procedura seguente:To upload these files to your App Service instance, perform the following steps:

    1. In [Cloud Shell](https://shell.azure.com), installare l'estensione webapp:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Eseguire il comando CLI seguente per creare un tunnel SSH dal sistema locale al servizio app:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Connettersi alla porta di tunneling locale con il client SFTP e caricare i file nella cartella */home/tomcat/lib.*

    In alternativa, è possibile usare un client FTP per caricare il driver JDBC. Seguire queste [istruzioni per ottenere le credenziali FTP](../deploy-configure-credentials.md?toc=/azure/app-service/containers/toc.json).

2. Se è stata creata un'origine dati a livello di server, riavviare l'applicazione Linux del Servizio app. Tomcat reimposterà `CATALINA_BASE` su `/home/tomcat` e userà la configurazione aggiornata.

### <a name="spring-boot"></a>Spring Boot

Per connettersi alle origini dati nelle applicazioni di avvio di Spring, è consigliabile creare stringhe di connessione e inserirle nel file *application.properties.*

1. Nella sezione "Configurazione" della pagina Servizio app impostare un nome per la stringa, incollare la stringa di connessione JDBC nel campo del valore e impostare il tipo su "Personalizzato". Facoltativamente, è possibile impostare questa stringa di connessione come impostazione dello slot.

    Questa stringa di connessione è accessibile `CUSTOMCONNSTR_<your-string-name>`all'applicazione come variabile di ambiente denominata . Ad esempio, la stringa di connessione `CUSTOMCONNSTR_exampledb`creata in precedenza sarà denominata .

2. Nel file *application.properties* fare riferimento a questa stringa di connessione con il nome della variabile di ambiente. Per il nostro esempio, useremmo quanto segue.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Per ulteriori informazioni su questo argomento, consultare la documentazione relativa [all'avvio di primavera sull'accesso ai dati](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) e le [configurazioni esternalizzate.](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Usare Redis come cache di sessione con Tomcat

È possibile configurare Tomcat per l'utilizzo di un archivio di sessione esterno, ad esempio Cache di [Azure per Redis](/azure/azure-cache-for-redis/). In questo modo è possibile mantenere lo stato della sessione utente (ad esempio i dati del carrello acquisti) quando un utente viene trasferito a un'altra istanza dell'app, ad esempio quando si verifica la scalabilità automatica, il riavvio o il failover.

Per usare Tomcat con Redis, devi configurare l'app per l'uso di un'implementazione [di PersistentManager.To](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) use Tomcat with Redis, you must configure your app to use a PersistentManager implementation. I passaggi seguenti illustrano questo processo usando [Gestione sessioni Pivotal: redis-store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) come esempio.

1. Aprire un terminale `<variable>=<value>` Bash e utilizzare per impostare ciascuna delle seguenti variabili di ambiente.

    | Variabile                 | valore                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | Nome del gruppo di risorse contenente l'istanza del servizio app.       |
    | WEBAPP_NAME              | Nome dell'istanza del servizio app.                                     |
    | WEBAPP_PLAN_NAME         | Nome del piano di servizio app.                                         |
    | AREA                   | Nome dell'area in cui è ospitata l'app.                           |
    | REDIS_CACHE_NAME         | Nome dell'istanza della cache di Azure per Redis.                           |
    | REDIS_PORT               | Porta SSL su cui è in ascolto la cache Redis.                             |
    | REDIS_PASSWORD           | Chiave di accesso primaria per l'istanza.                                  |
    | REDIS_SESSION_KEY_PREFIX | Valore specificato per identificare le chiavi di sessione provenienti dall'app. |

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

    È possibile trovare il nome, la porta e le informazioni sulla chiave di accesso nel portale di Azure cercando nelle sezioni **Proprietà** o Chiavi di **accesso** dell'istanza del servizio.

2. Crea o aggiorna il file *src/main/webapp/META-INF/context.xml* dell'app con il contenuto seguente:

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

    Questo file specifica e configura l'implementazione del gestore di sessione per l'app. Utilizza le variabili di ambiente impostate nel passaggio precedente per mantenere le informazioni dell'account fuori dai file di origine.

3. Utilizzare FTP per caricare il file JAR del gestore di sessione nell'istanza del servizio app, inserendolo nella directory */home/tomcat/lib.* Per altre info, vedi [Distribuire l'app nel servizio app di Azure usando FTP/S.](https://docs.microsoft.com/azure/app-service/deploy-ftp)

4. Disabilitare il cookie di [affinità](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) di sessione per l'istanza del servizio app. È possibile eseguire questa operazione dal portale di Azure passando all'app e quindi impostando **Configurazione > Impostazioni generali > affinità ARR** su **Disattivato**. In alternativa, è possibile utilizzare il comando seguente:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    Per impostazione predefinita, il servizio app utilizzerà i cookie di affinità di sessione per garantire che le richieste client con sessioni esistenti vengano instradate alla stessa istanza dell'applicazione. Questo comportamento predefinito non richiede alcuna configurazione, ma non può mantenere lo stato della sessione utente quando l'istanza dell'app viene riavviata o quando il traffico viene reindirizzato a un'altra istanza. Quando si disabilita la configurazione di [affinità dell'istanza ARR esistente](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) per disattivare il routing basato su cookie di sessione, si consente all'archivio di sessione configurato di funzionare senza interferenze.

5. Passare alla sezione **Proprietà** dell'istanza del servizio app e individuare **Indirizzi IP in uscita aggiuntivi**. Rappresentano tutti i possibili indirizzi IP in uscita per l'app. Copiarli per utilizzarli nel passaggio successivo.

6. Per ogni indirizzo IP, creare una regola del firewall nella cache di Azure per l'istanza Redis.For each IP address, create a firewall rule in your Azure Cache for Redis instance. È possibile eseguire questa operazione nel portale di Azure dalla sezione Firewall dell'istanza Redis.You can do this on the Azure portal from the **Firewall** section of your Redis instance. Specificare un nome univoco per ogni regola e impostare i valori **Indirizzo IP** iniziale e **Indirizzo IP** finale sullo stesso indirizzo IP.

7. Passare alla sezione **Impostazioni avanzate** dell'istanza Redis e impostare Consenti accesso **solo tramite SSL** su **No**. Ciò consente all'istanza del servizio app di comunicare con la cache Redis tramite l'infrastruttura di Azure.This enables your App Service instance to communicate with your Redis cache via the Azure infrastructure.

8. Aggiorna `azure-webapp-maven-plugin` la configurazione nel file *pom.xml* dell'app in modo che faccia riferimento alle informazioni del tuo account Redis. Questo file utilizza le variabili di ambiente impostate in precedenza per mantenere le informazioni sull'account all'esterno dei file di origine.

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

L'app ora utilizzerà la cache Redis per la gestione delle sessioni.

Per un esempio che è possibile usare per testare queste istruzioni, vedere il repository [scaling-stateful-java-web-app-on-azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) in GitHub.For a sample that you can use to test these instructions, see the scaling-stateful-java-web-app-on-azure repo on GitHub.

## <a name="docker-containers"></a>Contenitori Docker

Per usare nei contenitori il pacchetto JDK Zulu supportato da Azure, assicurarsi di eseguire il pull e di usare le immagini precompilate, come documentato nella [pagina di download di Azul Zulu Enterprise per Azure supportata](https://www.azul.com/downloads/azure-only/zulu/) o di usare gli esempi `Dockerfile` dal [repository GitHub Java di Microsoft](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Dichiarazione di sostegno

### <a name="runtime-availability"></a>Disponibilità di runtime

Il servizio app per Linux supporta due runtime per l'hosting gestito delle applicazioni Web Java:

- Il [contenitore servlet Tomcat](https://tomcat.apache.org/) per l'esecuzione di applicazioni in pacchetti come file di archivio Web (WAR). Sono supportate le versioni 8.5 e 9.0.
- Ambiente di runtime Java SE per l'esecuzione di applicazioni in pacchetti come file di archivio Java (JAR). Le versioni supportate sono Java 8 e 11.

### <a name="jdk-versions-and-maintenance"></a>Versioni di JDK e manutenzione

Le build Azul Zulu Enterprise di OpenJDK sono distribuzioni di OpenJDK gratuite, multipiattaforma e pronte per la produzione per Azure e Azure Stack, supportate da Microsoft e Azul Systems. Contengono tutti i componenti necessari per compilare ed eseguire applicazioni Java SE. È possibile installare JDK da [Java JDK Installation](https://aka.ms/azure-jdks).

Ai pacchetti JDK supportati vengono automaticamente applicate patch con cadenza trimestrale, a gennaio, aprile, luglio e ottobre di ogni anno.

### <a name="security-updates"></a>Aggiornamenti per la sicurezza

Le patch e le correzioni per le principali vulnerabilità della sicurezza verranno rilasciate da Azul Systems non appena disponibili. Una vulnerabilità "principale" viene definita da un punteggio di base pari o superiore a 9,0 in [NIST Common Vulnerability Scoring System, versione 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Deprecazione e ritiro

Se un runtime Java supportato sarà ritirato, gli sviluppatori di Azure che usano il runtime interessato riceveranno un avviso di funzionalità deprecata almeno sei mesi prima che il runtime venga ritirato.

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Passaggi successivi

Per trovare guide introduttive di Azure, esercitazioni e documentazione di riferimento su Java, visitare la pagina [Azure per sviluppatori Java](/java/azure/).

Per domande generali sull'uso del servizio app per Linux, non specifiche dello sviluppo Java, vedere le [domande frequenti sul servizio app in Linux](app-service-linux-faq.md).

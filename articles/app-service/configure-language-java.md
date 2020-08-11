---
title: Configurare app Java di Windows
description: Informazioni su come configurare le app Java da eseguire nelle istanze di macchine virtuali Windows nel servizio app Azure. Questo articolo illustra le attività di configurazione più comuni.
keywords: servizio app di Azure, app Web, Windows, OSS, Java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 30d5fa329131cdfd380a84843b3ba202b2e22e39
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080131"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Configurare un'app java per il servizio app Azure

::: zone pivot="platform-windows"  

App Azure servizio consente agli sviluppatori Java di creare, distribuire e ridimensionare rapidamente le applicazioni Web Tomcat in un servizio basato su Windows completamente gestito. Distribuire le applicazioni con i plug-in Maven dalla riga di comando o in editor come IntelliJ, Eclipse o Visual Studio Code.

Questa guida fornisce i concetti chiave e le istruzioni per gli sviluppatori Java che usano nel servizio app. Se non si è mai usato app Azure servizio, è consigliabile leggere prima di tutto la [Guida introduttiva per Java](quickstart-java.md) . Domande generali sull'uso del servizio app non specifiche per lo sviluppo Java sono disponibili nelle [domande frequenti su Windows del servizio app](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Distribuzione dell'app

È possibile usare il plug-in [app Web di Azure per Maven](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) per distribuire i file con estensione War. La distribuzione con IDE comuni è supportata anche con [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) o [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse).

Altrimenti, il metodo di distribuzione dipenderà dal tipo di archivio:

- Per distribuire file con estensione war in Tomcat, usare l'endpoint `/api/wardeploy/` per eseguire il comando POST per il file di archivio. Per altre informazioni su questa API, vedere [questa documentazione](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Per distribuire i file con estensione jar in Java SE, usare l' `/api/zipdeploy/` endpoint del sito Kudu. Per altre informazioni su questa API, vedere [questa documentazione](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Non distribuire file con estensione war o jar tramite FTP. Lo strumento FTP è stato progettato per caricare script di avvio, dipendenze o altri file di runtime. Non è la scelta ottimale per la distribuzione di app Web.

## <a name="logging-and-debugging-apps"></a>Registrazione e debug delle app

Nel portale di Azure sono disponibili report sulle prestazioni, visualizzazioni del traffico e controlli dell'integrità per ogni app. Per altre informazioni, vedere [Panoramica approfondita della diagnostica del Servizio app di Azure](overview-diagnostics.md).

### <a name="use-flight-recorder"></a>Usare l'utilità traccia eventi

Tutti i runtime Java nel servizio app con il JVM Azul sono disponibili con il registratore di volo Zulu. È possibile usarlo per registrare gli eventi a livello di JVM, di sistema e Java per monitorare il comportamento e risolvere i problemi nelle applicazioni Java.

Per eseguire una registrazione temporizzata, sarà necessario il PID (ID processo) dell'applicazione Java. Per trovare il PID, aprire un browser al sito SCM dell'app Web in https://<nome-sito>. scm.azurewebsites.net/ProcessExplorer/. Questa pagina mostra i processi in esecuzione nell'app Web. Individuare il processo denominato "Java" nella tabella e copiare il PID corrispondente (ID processo).

Aprire quindi il **console di debug** sulla barra degli strumenti superiore del sito SCM ed eseguire il comando seguente. Sostituire `<pid>` con l'ID processo copiato in precedenza. Questo comando avvierà una registrazione di 30 secondi del profiler dell'applicazione Java e genererà un file denominato `timed_recording_example.jfr` nella `D:\home` Directory.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

Per altre informazioni, vedere la [documentazione di riferimento sul comando Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

#### <a name="analyze-jfr-files"></a>Analizza `.jfr` file

Usare [FTPS](deploy-ftp.md) per scaricare il file JFR nel computer locale. Per analizzare il file JFR, scaricare e installare [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Per istruzioni su Zulu Mission Control, vedere la [documentazione di Azul](https://docs.azul.com/zmc/) e le [istruzioni di installazione](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

### <a name="stream-diagnostic-logs"></a>Eseguire lo streaming dei log di diagnostica

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Per altre informazioni, vedere [Eseguire lo streaming dei log in Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Registrazione dell'app

Abilitare la [registrazione dell'applicazione](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) tramite il portale di Azure o l'[interfaccia della riga di comando di Azure](/cli/azure/webapp/log#az-webapp-log-config) per configurare il servizio app per scrivere l'output della console standard dell'applicazione e i flussi di errori della console standard nel file system locale o in Archiviazione BLOB di Azure. La registrazione nell'istanza del file system del servizio app locale viene disabilitata 12 ore dopo la configurazione. Se è necessario un periodo di conservazione più lungo, configurare l'applicazione per scrivere l'output in un contenitore di archiviazione BLOB. I log delle app Java e Tomcat sono reperibili nella directory */LogFiles/Application/*

Se l'applicazione usa [Logback](https://logback.qos.ch/) o [Log4j](https://logging.apache.org/log4j) per la traccia, è possibile inoltrare queste tracce per la revisione ad Azure Application Insights usando le istruzioni di configurazione del framework di registrazione illustrate in [Esplorare i log di traccia Java in Application Insights](/azure/application-insights/app-insights-java-trace-logs).


## <a name="customization-and-tuning"></a>Personalizzazione e ottimizzazione

Il servizio app Azure supporta l'ottimizzazione e la personalizzazione predefinite tramite l'portale di Azure e l'interfaccia della riga di comando. Per la configurazione delle app Web non specifiche di Java, vedere gli articoli seguenti:

- [Configurare le impostazioni dell'app](configure-common.md#configure-app-settings)
- [Configurare un nome di dominio](app-service-web-tutorial-custom-domain.md)
- [Configurare binding TLS](configure-ssl-bindings.md)
- [Aggiungere una rete CDN](../cdn/cdn-add-to-web-app.md)
- [Configurare il sito di Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Impostare le opzioni di runtime Java

Per impostare la memoria allocata o altre opzioni di runtime JVM, creare un' [impostazione dell'app](configure-common.md#configure-app-settings) denominata `JAVA_OPTS` con le opzioni. Il servizio app passa questa impostazione come variabile di ambiente al runtime Java all'avvio.

Nel portale di Azure, sotto **Impostazioni applicazione** per l'app Web creare una nuova impostazione dell'app denominata `JAVA_OPTS` che includa le impostazioni aggiuntive, ad esempio `-Xms512m -Xmx1204m`.

Per configurare l'impostazione dell'app dal plug-in Maven, aggiungere i tag setting/value nella sezione dei plug-in di Azure. L'esempio seguente imposta le dimensioni heap Java specifiche minime e massime:

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

### <a name="pre-compile-jsp-files"></a>Pre-compilare i file JSP

Per migliorare le prestazioni delle applicazioni Tomcat, è possibile compilare i file JSP prima della distribuzione nel Servizio app di Azure. È possibile usare il [plug-in Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) fornito da Apache Sling o questo [file di compilazione Ant](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Proteggere le applicazioni

Le applicazioni Java in esecuzione nel servizio app hanno lo stesso set di procedure consigliate per la [sicurezza](/azure/security/security-paas-applications-using-app-services) di altre applicazioni.

### <a name="authenticate-users-easy-auth"></a>Autenticare gli utenti (Easy Auth)

Configurare l'autenticazione dell'app nel portale di Azure con l'opzione **Autenticazione e autorizzazione**, che consente di abilitare l'autenticazione usando Azure Active Directory o gli account di accesso ai social network, ad esempio Facebook, Google o GitHub. La configurazione nel portale di Azure funziona solo quando si configura un singolo provider di autenticazione. Per altre informazioni, vedere [Configurare un'applicazione dei servizi app per usare l'account di accesso di Azure Active Directory](configure-authentication-provider-aad.md) e gli articoli correlati per gli altri provider di identità. Se è necessario abilitare più provider di accesso, seguire le istruzioni dell'articolo [Personalizzare l'autenticazione nel servizio app](app-service-authentication-how-to.md).

#### <a name="tomcat"></a>Tomcat

L'applicazione Tomcat può accedere alle attestazioni dell'utente direttamente dalla servlet eseguendo il cast dell'oggetto Principal a un oggetto Map. L'oggetto Map eseguirà il mapping di ogni tipo di attestazione a una raccolta di attestazioni per quel tipo. Nel codice riportato di seguito `request` è un'istanza di `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

A questo punto è possibile esaminare l'oggetto `Map` per qualsiasi attestazione specifica. Il frammento di codice seguente, ad esempio, esegue l'iterazione di tutti i tipi di attestazione e stampa il contenuto di ogni raccolta.

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

Per disconnettere gli utenti, usare il percorso `/.auth/ext/logout`. Per eseguire altre azioni, vedere la documentazione relativa all'[uso di Autenticazione e autorizzazione del Servizio app di Azure](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to). Sono inoltre disponibili documenti ufficiali sull'[interfaccia HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) di Tomcat e sui relativi metodi. Anche i seguenti metodi servlet vengono idratati in base alla configurazione del Servizio app di Azure:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Per disabilitare questa funzionalità, creare un'impostazione dell'applicazione denominata `WEBSITE_AUTH_SKIP_PRINCIPAL` con un valore di `1`. Per disabilitare tutti i filtri servlet aggiunti dal Servizio app di Azure, creare un'impostazione denominata `WEBSITE_SKIP_FILTERS` con un valore di `1`.

### <a name="configure-tlsssl"></a>Configurare TLS/SSL

Per caricare un certificato TLS/SSL esistente e associarlo al nome di dominio dell'applicazione, seguire le istruzioni riportate nella pagina [proteggere un nome DNS personalizzato con binding TLS nel servizio app Azure](configure-ssl-bindings.md) . Per impostazione predefinita, l'applicazione consentirà ancora le connessioni HTTP. Seguire i passaggi specifici dell'esercitazione per applicare SSL e TLS.

### <a name="use-keyvault-references"></a>Usare i riferimenti di KeyVault

[Azure KeyVault](../key-vault/general/overview.md) fornisce la gestione centralizzata dei segreti con i criteri di accesso e la cronologia controlli. È possibile archiviare segreti (ad esempio password o stringhe di connessione) in KeyVault e accedere a questi segreti nell'applicazione tramite le variabili di ambiente.

Per prima cosa, seguire le istruzioni per [concedere all'app l'accesso a Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e [creare un riferimento KeyVault al segreto in un'impostazione dell'applicazione](app-service-key-vault-references.md#reference-syntax). È possibile verificare che il riferimento venga risolto nel segreto stampando la variabile di ambiente durante l'accesso remoto al terminale del Servizio app di Azure.

Per inserire questi segreti nel file di configurazione di Spring o Tomcat, usare la sintassi di inserimento delle variabili di ambiente (`${MY_ENV_VAR}`). Per i file di configurazione di Spring, vedere la documentazione relativa alle [configurazioni esternalizzate](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).


## <a name="configure-apm-platforms"></a>Configurare le piattaforme APM

Questa sezione illustra come connettere le applicazioni Java distribuite nel Servizio app di Azure in Linux con le piattaforme di monitoraggio delle prestazioni applicative NewRelic e AppDynamics.

### <a name="configure-new-relic"></a>Configurare New Relic

1. Crea un nuovo account Relic in [NewRelic.com](https://newrelic.com/signup)
2. Scaricare l'agente Java da NewRelic; il nome file sarà simile a *newrelic-java-x.x.x.zip*.
3. Copiare il codice di licenza, che sarà necessario per configurare l'agente in un secondo momento.
4. Usare la [console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) per creare una nuova directory */Home/site/wwwroot/APM*.
5. Caricare i file dell'agente Java di New Relic decompressi in una directory in */Home/site/wwwroot/APM*. I file per l'agente devono trovarsi in */home/site/wwwroot/apm/newrelic*.
6. Modificare il file YAML in */home/site/wwwroot/apm/newrelic/newrelic.yml* e sostituire il valore della licenza segnaposto con il proprio codice di licenza.
7. Nel portale di Azure passare all'applicazione nel servizio app e creare una nuova impostazione dell'applicazione.
    - Se l'app usa **Java SE**, creare una variabile di ambiente denominata `JAVA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se si usa **Tomcat**, creare una variabile di ambiente denominata `CATALINA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Configurare AppDynamics

1. Creare un account AppDynamics in [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Scaricare l'agente Java dal sito Web di AppDynamics, il nome file sarà simile a *AppServerAgent-x.x.x.xxxxx.zip*
3. Usare la [console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) per creare una nuova directory */Home/site/wwwroot/APM*.
4. Caricare i file dell'agente Java decompressi in una directory in */home/site/wwwroot/apm*. I file per l'agente devono trovarsi in */home/site/wwwroot/apm/appdynamics*.
5. Nel portale di Azure passare all'applicazione nel servizio app e creare una nuova impostazione dell'applicazione.
    - Se si usa **Java SE**, creare una variabile di ambiente denominata `JAVA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` in cui `<app-name>` è il nome del servizio app.
    - Se si usa **Tomcat**, creare una variabile di ambiente denominata `CATALINA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` in cui `<app-name>` è il nome del servizio app.

>  Se si dispone già di una variabile di ambiente per `JAVA_OPTS` oppure `CATALINA_OPTS`, aggiungere l'opzione `-javaagent:/...` alla fine del valore corrente.

## <a name="data-sources"></a>Origini dati

### <a name="tomcat"></a>Tomcat

Queste istruzioni si applicano a tutte le connessioni di database. È necessario sostituire i segnaposto con il nome della classe del driver del database scelto e il file JAR. Viene fornita una tabella con i nomi delle classi e i download dei driver per i database più comuni.

| Database   | Nome della classe del driver                             | Driver JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Scaricare](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Scaricare](https://dev.mysql.com/downloads/connector/j/) (selezionare "Indipendente dalla piattaforma") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Scaricare](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Per configurare Tomcat per l'uso di Java Database Connectivity (JDBC) o Java Persistence API (JPA), personalizzare prima la variabile di ambiente `CATALINA_OPTS` letta da Tomcat all'avvio. Impostare questi valori tramite un'impostazione app nel [plug-in Maven del servizio app](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

In alternativa, impostare le variabili di ambiente nella pagina **Configurazione** > **Impostazioni dell'applicazione** nel portale di Azure.

Determinare quindi se l'origine dati deve essere disponibile per un'applicazione o per tutte le applicazioni in esecuzione nel servlet Tomcat.

#### <a name="application-level-data-sources"></a>Origini dati a livello di applicazione

1. Creare un file *context.xml* nella directory *META-INF/* del progetto. Creare la directory *META-INF/* se non esiste.

2. In *context-xml* aggiungere un elemento `Context` per collegare l'origine dati a un indirizzo JNDI. Sostituire il segnaposto `driverClassName` con il nome della classe del driver indicato nella tabella precedente.

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

3. Aggiornare il file *web.xml* dell'applicazione per usare l'origine dati nell'applicazione.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Completare la configurazione

Infine, i file jar del driver vengono posizionati nel classpath di Tomcat e il servizio app viene riavviato. Assicurarsi che i file driver JDBC siano disponibili per il caricatore di classe Tomcat inserendoli nella directory */home/tomcat/lib*. Creare questa directory se non esiste già. Per caricare questi file nell'istanza del servizio app, seguire questa procedura:

1. Nel [Cloud Shell](https://shell.azure.com) installare l'estensione webapp:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Eseguire il comando dell'interfaccia della riga di comando seguente per creare un tunnel SSH dal sistema locale al Servizio app di Azure:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Connettersi alla porta di tunneling locale con il client SFTP e caricare i file nella cartella */home/tomcat/lib*.

In alternativa, è possibile usare un client FTP per caricare il driver JDBC. Seguire queste [istruzioni per ottenere le credenziali FTP](deploy-configure-credentials.md).

## <a name="configuring-tomcat"></a>Configurazione di Tomcat

Per modificare Tomcat `server.xml` o altri file di configurazione, prendere nota della versione principale di Tomcat nel portale.

1. Trovare la home directory di Tomcat per la versione eseguendo il `env` comando. Cercare la variabile di ambiente che inizia con `AZURE_TOMCAT` e corrisponde alla versione principale. Ad esempio, `AZURE_TOMCAT85_HOME` punta alla directory Tomcat per tomcat 8,5.
1. Dopo aver identificato la home directory di Tomcat per la versione in uso, copiare la directory di configurazione in `D:\home` . Se, ad esempio, `AZURE_TOMCAT85_HOME` il valore di `D:\Program Files (x86)\apache-tomcat-8.5.37` è, il nuovo percorso della directory copiata sarà `D:\home\apache-tomcat-8.5.37` .

Riavviare infine il servizio app. Le distribuzioni devono essere `D:\home\site\wwwroot\webapps` inserite come prima.

## <a name="configure-java-se"></a>Configurare Java SE

Quando si esegue un. L'applicazione JAR in Java SE in Windows `server.port` viene passata come opzione della riga di comando all'avvio dell'applicazione. È possibile risolvere manualmente la porta HTTP dalla variabile di ambiente `HTTP_PLATFORM_PORT` . Il valore di questa variabile di ambiente sarà la porta HTTP su cui l'applicazione deve essere in ascolto. 

## <a name="java-runtime-statement-of-support"></a>Istruzione di supporto del runtime Java

### <a name="jdk-versions-and-maintenance"></a>Versioni di JDK e manutenzione

Il pacchetto Java Development Kit (JDK) supportato di Azure è [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fornito da [Azul Systems](https://www.azul.com/).

Gli aggiornamenti delle versioni principali verranno forniti tramite nuove opzioni di runtime nel servizio app Azure per Windows. I clienti eseguono l'aggiornamento a queste versioni più recenti di Java configurando la distribuzione del servizio app e sono responsabili dei test, oltre che di assicurare che l'aggiornamento principale risponda alle esigenze.

Ai pacchetti JDK supportati vengono automaticamente applicate patch con cadenza trimestrale, a gennaio, aprile, luglio e ottobre di ogni anno. Per altre informazioni su Java in Azure, vedere [questo documento di supporto](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support).

### <a name="security-updates"></a>Aggiornamenti per la sicurezza

Le patch e le correzioni per le principali vulnerabilità della sicurezza verranno rilasciate da Azul Systems non appena disponibili. Una vulnerabilità "principale" viene definita da un punteggio di base pari o superiore a 9,0 in [NIST Common Vulnerability Scoring System, versione 2](https://nvd.nist.gov/vuln-metrics/cvss).

Tomcat 8,0 ha raggiunto la [fine del ciclo di vita (EOL) a partire dal 30 settembre 2018](https://tomcat.apache.org/tomcat-80-eol.html). Mentre il runtime è ancora disponibile nel servizio app Azure, Azure non applica gli aggiornamenti della sicurezza per Tomcat 8,0. Se possibile, eseguire la migrazione delle applicazioni a Tomcat 8,5 o 9,0. Sia Tomcat 8,5 che 9,0 sono disponibili nel servizio app Azure. Per ulteriori informazioni, vedere il [sito Tomcat ufficiale](https://tomcat.apache.org/whichversion.html) . 

### <a name="deprecation-and-retirement"></a>Deprecazione e ritiro

Se un runtime Java supportato sarà ritirato, gli sviluppatori di Azure che usano il runtime interessato riceveranno un avviso di funzionalità deprecata almeno sei mesi prima che il runtime venga ritirato.

### <a name="local-development"></a>Sviluppo locale

Gli sviluppatori possono scaricare l'edizione Production di Azul Zulu Enterprise JDK per lo sviluppo locale dal [sito di download di Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Supporto per lo sviluppo

Il supporto del prodotto per l' [Azul Zulu del JDK supportato da Azure](https://www.azul.com/downloads/azure-only/zulu/) è disponibile tramite Microsoft quando si sviluppa per azure o [Azure stack](https://azure.microsoft.com/overview/azure-stack/) con un [piano di supporto di Azure completo](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Supporto di runtime

Gli sviluppatori possono [aprire un problema](/azure/azure-portal/supportability/how-to-create-azure-support-request) relativo ai pacchetti JDK Azul Zulu tramite il supporto di Azure se hanno un [piano di supporto qualificato](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Passaggi successivi

Questo argomento descrive il supporto di Java Runtime per app Azure servizio in Windows.

- Per altre informazioni sull'hosting di applicazioni Web con app Azure servizio, vedere [Panoramica del servizio app](overview.md).
- Per informazioni sullo sviluppo di Java in Azure, vedere [Azure per Java Dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).

::: zone-end

::: zone pivot="platform-linux"

Il Servizio app di Azure in Linux consente agli sviluppatori Java di compilare, distribuire e ridimensionare rapidamente le applicazioni Web in pacchetto Tomcat o Java Standard Edition (SE) in un servizio basato su Linux completamente gestito. Distribuire le applicazioni con i plug-in Maven dalla riga di comando o in editor come IntelliJ, Eclipse o Visual Studio Code.

Questa guida illustra i concetti chiave e le istruzioni per gli sviluppatori Java che usano un contenitore Linux predefinito nel Servizio app di Azure. Se non si è mai usato il Servizio app di Azure, seguire l'[avvio rapido di Java](quickstart-java.md).

## <a name="deploying-your-app"></a>Distribuzione dell'app

È possibile usare il [plug-in Maven per il Servizio app di Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) per distribuire entrambi i tipi di file con estensione jar e war. La distribuzione con IDE comuni è supportata anche con [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) o [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse).

Altrimenti, il metodo di distribuzione dipenderà dal tipo di archivio:

- Per distribuire file con estensione war in Tomcat, usare l'endpoint `/api/wardeploy/` per eseguire il comando POST per il file di archivio. Per altre informazioni su questa API, vedere [questa documentazione](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Per distribuire file con estensione jar nelle immagini di Java SE, usare l'endpoint `/api/zipdeploy/` del sito Kudu. Per altre informazioni su questa API, vedere [questa documentazione](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Non distribuire file con estensione war o jar tramite FTP. Lo strumento FTP è stato progettato per caricare script di avvio, dipendenze o altri file di runtime. Non è la scelta ottimale per la distribuzione di app Web.

## <a name="logging-and-debugging-apps"></a>Registrazione e debug delle app

Nel portale di Azure sono disponibili report sulle prestazioni, visualizzazioni del traffico e controlli dell'integrità per ogni app. Per altre informazioni, vedere [Panoramica approfondita della diagnostica del Servizio app di Azure](overview-diagnostics.md).

### <a name="ssh-console-access"></a>Accesso alla console SSH

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Eseguire lo streaming dei log di diagnostica

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

Per altre informazioni, vedere [Eseguire lo streaming dei log in Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Registrazione dell'app

Abilitare la [registrazione dell'applicazione](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) tramite il portale di Azure o l'[interfaccia della riga di comando di Azure](/cli/azure/webapp/log#az-webapp-log-config) per configurare il servizio app per scrivere l'output della console standard dell'applicazione e i flussi di errori della console standard nel file system locale o in Archiviazione BLOB di Azure. La registrazione nell'istanza del file system del servizio app locale viene disabilitata 12 ore dopo la configurazione. Se è necessario un periodo di conservazione più lungo, configurare l'applicazione per scrivere l'output in un contenitore di archiviazione BLOB. I log delle app Java e Tomcat sono reperibili nella directory */home/LogFiles/Application/* .

>[!NOTE]
>La registrazione al file system del Servizio app di Azure locale viene disabilitata dopo 12 ore solo per i Servizi app basati su Windows. La registrazione dell'archiviazione BLOB di Azure per i Servizi app basati su Linux può essere configurata solo con [Monitoraggio di Azure (anteprima)](/azure/app-service/troubleshoot-diagnostic-logs#send-logs-to-azure-monitor-preview) 

Se l'applicazione usa [Logback](https://logback.qos.ch/) o [Log4j](https://logging.apache.org/log4j) per la traccia, è possibile inoltrare queste tracce per la revisione ad Azure Application Insights usando le istruzioni di configurazione del framework di registrazione illustrate in [Esplorare i log di traccia Java in Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Strumenti per la risoluzione dei problemi

Le immagini Java integrate sono basate sul sistema operativo [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html). Usare la gestione pacchetti `apk` per installare gli strumenti o i comandi per la risoluzione dei problemi.

### <a name="flight-recorder"></a>Flight Recorder

Per tutte le immagini Java Linux nel Servizio app di Azure è installato Zulu Flight Recorder, in modo che sia possibile connettersi facilmente a JVM e avviare una registrazione del profiler o generare un dump dell'heap.

#### <a name="timed-recording"></a>Registrazione temporizzata

Per iniziare, connettersi tramite SSH al Servizio app di Azure ed eseguire il comando `jcmd` per visualizzare un elenco di tutti i processi Java in esecuzione. Oltre a jcmd, l'applicazione Java dovrebbe essere in esecuzione con un numero ID processo (PID).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Eseguire il comando seguente per avviare una registrazione di 30 secondi della JVM. Verrà profilata la JVM e verrà creato un file JFR denominato *jfr_example.jfr* nella home directory. (Sostituire 116 con il PID dell'app Java.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Durante l'intervallo di 30 secondi, è possibile verificare che la registrazione avvenga eseguendo `jcmd 116 JFR.check`. Vengono visualizzate tutte le registrazioni per il processo Java specificato.

#### <a name="continuous-recording"></a>Registrazione continua

È possibile usare Zulu Flight Recorder per profilare costantemente l'applicazione Java con un effetto minimo sulle prestazioni di runtime ([origine](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). A tale scopo, eseguire il comando dell'interfaccia della riga di comando di Azure seguente per creare un'impostazione dell'app denominata JAVA_OPTS con la configurazione necessaria. Il contenuto dell'impostazione dell'app JAVA_OPTS viene trasmesso al comando `java` quando l'app viene avviata.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Dopo aver avviato la registrazione, è possibile scaricare i dati di registrazione correnti in qualsiasi momento usando il comando `JFR.dump`.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Per altre informazioni, vedere la [documentazione di riferimento sul comando Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analisi delle registrazioni

Usare [FTPS](deploy-ftp.md) per scaricare il file JFR nel computer locale. Per analizzare il file JFR, scaricare e installare [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Per istruzioni su Zulu Mission Control, vedere la [documentazione di Azul](https://docs.azul.com/zmc/) e le [istruzioni di installazione](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Personalizzazione e ottimizzazione

Il Servizio app di Azure per Linux supporta l'ottimizzazione e la personalizzazione predefinite tramite l'interfaccia della riga di comando e il portale di Azure. Per la configurazione delle app Web non specifiche di Java, vedere gli articoli seguenti:

- [Configurare le impostazioni dell'app](configure-common.md#configure-app-settings)
- [Configurare un nome di dominio](app-service-web-tutorial-custom-domain.md)
- [Configurare le associazioni SSL](configure-ssl-bindings.md)
- [Aggiungere una rete CDN](../cdn/cdn-add-to-web-app.md)
- [Configurare il sito di Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Impostare le opzioni di runtime Java

Per impostare la memoria allocata o altre opzioni di runtime JVM in entrambi gli ambienti Tomcat e Java SE, creare un'[impostazione dell'app](configure-common.md#configure-app-settings) denominata `JAVA_OPTS` usando le opzioni. Il servizio app in Linux passa questa impostazione come variabile di ambiente al runtime Java quando viene avviato.

Nel portale di Azure, sotto **Impostazioni applicazione** per l'app Web creare una nuova impostazione dell'app denominata `JAVA_OPTS` che includa le impostazioni aggiuntive, ad esempio `-Xms512m -Xmx1204m`.

Per configurare l'impostazione dell'app dal plug-in Maven, aggiungere i tag setting/value nella sezione dei plug-in di Azure. L'esempio seguente imposta le dimensioni heap Java specifiche minime e massime:

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

Se si distribuisce un'applicazione JAR, questa deve essere denominata *app. jar* in modo che l'immagine incorporata possa identificare correttamente l'app. (Il plug-in Maven effettua automaticamente la ridenominazione). Se non si vuole rinominare il file JAR in *app.jar*, è possibile caricare uno script della shell con il comando per eseguire il file JAR. Incollare quindi il percorso completo di questo script nella casella di testo [File di avvio](faq-app-service-linux.md#built-in-images) nella sezione Configurazione del portale. Lo script di avvio non viene eseguito dalla directory in cui si trova. Pertanto, usare sempre percorsi assoluti per fare riferimento ai file dello script di avvio, ad esempio `java -jar /home/myapp/myapp.jar`.

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

### <a name="adjust-startup-timeout"></a>Regolare il timeout avvio

Se l'applicazione Java è particolarmente grande, è necessario aumentare il limite del tempo di avvio. A tale scopo, creare un'impostazione dell'applicazione `WEBSITES_CONTAINER_START_TIME_LIMIT` e impostarla sul numero di secondi che il Servizio app di Azure deve attendere prima del timeout. Il valore massimo è `1800` secondi.

### <a name="pre-compile-jsp-files"></a>Pre-compilare i file JSP

Per migliorare le prestazioni delle applicazioni Tomcat, è possibile compilare i file JSP prima della distribuzione nel Servizio app di Azure. È possibile usare il [plug-in Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) fornito da Apache Sling o questo [file di compilazione Ant](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Proteggere le applicazioni

Le applicazioni Java in esecuzione nel servizio app per Linux hanno lo stesso set di [procedure consigliate per la sicurezza](/azure/security/security-paas-applications-using-app-services) delle altre applicazioni.

### <a name="authenticate-users-easy-auth"></a>Autenticare gli utenti (Easy Auth)

Configurare l'autenticazione dell'app nel portale di Azure con l'opzione **Autenticazione e autorizzazione**, che consente di abilitare l'autenticazione usando Azure Active Directory o gli account di accesso ai social network, ad esempio Facebook, Google o GitHub. La configurazione nel portale di Azure funziona solo quando si configura un singolo provider di autenticazione. Per altre informazioni, vedere [Configurare un'applicazione dei servizi app per usare l'account di accesso di Azure Active Directory](configure-authentication-provider-aad.md) e gli articoli correlati per gli altri provider di identità. Se è necessario abilitare più provider di accesso, seguire le istruzioni dell'articolo [Personalizzare l'autenticazione nel servizio app](app-service-authentication-how-to.md).

#### <a name="tomcat"></a>Tomcat

L'applicazione Tomcat può accedere alle attestazioni dell'utente direttamente dalla servlet eseguendo il cast dell'oggetto Principal a un oggetto Map. L'oggetto Map eseguirà il mapping di ogni tipo di attestazione a una raccolta di attestazioni per quel tipo. Nel codice riportato di seguito `request` è un'istanza di `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

A questo punto è possibile esaminare l'oggetto `Map` per qualsiasi attestazione specifica. Il frammento di codice seguente, ad esempio, esegue l'iterazione di tutti i tipi di attestazione e stampa il contenuto di ogni raccolta.

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

Per disconnettere gli utenti, usare il percorso `/.auth/ext/logout`. Per eseguire altre azioni, vedere la documentazione relativa all'[uso di Autenticazione e autorizzazione del Servizio app di Azure](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to). Sono inoltre disponibili documenti ufficiali sull'[interfaccia HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) di Tomcat e sui relativi metodi. Anche i seguenti metodi servlet vengono idratati in base alla configurazione del Servizio app di Azure:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Per disabilitare questa funzionalità, creare un'impostazione dell'applicazione denominata `WEBSITE_AUTH_SKIP_PRINCIPAL` con un valore di `1`. Per disabilitare tutti i filtri servlet aggiunti dal Servizio app di Azure, creare un'impostazione denominata `WEBSITE_SKIP_FILTERS` con un valore di `1`.

#### <a name="spring-boot"></a>Spring Boot

Gli sviluppatori Spring Boot possono usare l'[utilità di avvio Spring Boot per Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) per proteggere le applicazioni usando le familiari annotazioni e API di Spring Security. Assicurarsi di aumentare le dimensioni massime dell'intestazione nel file *application.properties*. È consigliabile il valore `16384`.

### <a name="configure-tlsssl"></a>Configurare TLS/SSL

Seguire le istruzioni illustrate in [Proteggere un nome DNS personalizzato con un'associazione SSL nel Servizio app di Azure](configure-ssl-bindings.md) per caricare un certificato SSL esistente e associarlo al nome di dominio dell'applicazione. Per impostazione predefinita, l'applicazione consentirà ancora le connessioni HTTP. Seguire i passaggi specifici dell'esercitazione per applicare SSL e TLS.

### <a name="use-keyvault-references"></a>Usare i riferimenti di KeyVault

[Azure KeyVault](../key-vault/general/overview.md) fornisce la gestione centralizzata dei segreti con i criteri di accesso e la cronologia controlli. È possibile archiviare segreti (ad esempio password o stringhe di connessione) in KeyVault e accedere a questi segreti nell'applicazione tramite le variabili di ambiente.

Per prima cosa, seguire le istruzioni per [concedere all'app l'accesso a Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e [creare un riferimento KeyVault al segreto in un'impostazione dell'applicazione](app-service-key-vault-references.md#reference-syntax). È possibile verificare che il riferimento venga risolto nel segreto stampando la variabile di ambiente durante l'accesso remoto al terminale del Servizio app di Azure.

Per inserire questi segreti nel file di configurazione di Spring o Tomcat, usare la sintassi di inserimento delle variabili di ambiente (`${MY_ENV_VAR}`). Per i file di configurazione di Spring, vedere la documentazione relativa alle [configurazioni esternalizzate](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

### <a name="using-the-java-key-store"></a>Usare l'archivio chiavi Java

Per impostazione predefinita, tutti i certificati pubblici o privati [caricati nel Servizio app di Azure in Linux](configure-ssl-certificate.md) verranno caricati nei rispettivi archivi chiavi Java quando il contenitore viene avviato. Dopo aver caricato il certificato, sarà necessario riavviare il Servizio app di Azure affinché venga caricato nell'archivio chiavi Java. I certificati pubblici vengono caricati nell'archivio chiavi in `$JAVA_HOME/jre/lib/security/cacerts`, mentre i certificati privati vengono archiviati in `$JAVA_HOME/lib/security/client.jks`.

Potrebbe essere necessaria una configurazione aggiuntiva per la crittografia della connessione JDBC con i certificati nell'archivio chiavi Java. Fare riferimento alla documentazione per il driver JDBC selezionato.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>Inizializzazione dell'archivio chiavi Java

Per inizializzare l'oggetto `import java.security.KeyStore`, caricare il file dell'archivio chiavi con la password. La password predefinita per entrambi gli archivi chiavi è "changeit".

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

È possibile caricare manualmente i certificati nell'archivio chiavi. Creare un'impostazione dell'app, `SKIP_JAVA_KEYSTORE_LOAD`, con un valore `1` per disabilitare il caricamento automatico dei certificati nell'archivio chiavi da parte del Servizio app di Azure. Tutti i certificati pubblici caricati nel Servizio app di Azure tramite il portale di Azure vengono archiviati in `/var/ssl/certs/`. Tutti i certificati privati sono archiviati in `/var/ssl/private/`.

È possibile interagire o eseguire il debug dello strumento chiave Java [aprendo una connessione SSH](configure-linux-open-ssh-session.md) al Servizio app di Azure ed eseguendo il comando `keytool`. Per un elenco di comandi, vedere la documentazione dello [strumento chiave](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html). Per altre informazioni sull'API dell'archivio chiavi, vedere la [documentazione ufficiale](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

## <a name="configure-apm-platforms"></a>Configurare le piattaforme APM

Questa sezione illustra come connettere le applicazioni Java distribuite nel Servizio app di Azure in Linux con le piattaforme di monitoraggio delle prestazioni applicative NewRelic e AppDynamics.

### <a name="configure-new-relic"></a>Configurare New Relic

1. Creare un account NewRelic in [NewRelic.com](https://newrelic.com/signup)
2. Scaricare l'agente Java da NewRelic; il nome file sarà simile a *newrelic-java-x.x.x.zip*.
3. Copiare il codice di licenza, che sarà necessario per configurare l'agente in un secondo momento.
4. [Usare SSH per connettersi all'istanza del Servizio app di Azure](configure-linux-open-ssh-session.md) e creare una nuova directory */home/site/wwwroot/apm*.
5. Caricare i file dell'agente Java NewRelic decompressi in una directory in */home/site/wwwroot/apm*. I file per l'agente devono trovarsi in */home/site/wwwroot/apm/newrelic*.
6. Modificare il file YAML in */home/site/wwwroot/apm/newrelic/newrelic.yml* e sostituire il valore della licenza segnaposto con il proprio codice di licenza.
7. Nel portale di Azure passare all'applicazione nel servizio app e creare una nuova impostazione dell'applicazione.
    - Se l'app usa **Java SE**, creare una variabile di ambiente denominata `JAVA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se si usa **Tomcat**, creare una variabile di ambiente denominata `CATALINA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Configurare AppDynamics

1. Creare un account AppDynamics in [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Scaricare l'agente Java dal sito Web di AppDynamics, il nome file sarà simile a *AppServerAgent-x.x.x.xxxxx.zip*
3. [Usare SSH per connettersi all'istanza del Servizio app di Azure](configure-linux-open-ssh-session.md) e creare una nuova directory */home/site/wwwroot/apm*.
4. Caricare i file dell'agente Java decompressi in una directory in */home/site/wwwroot/apm*. I file per l'agente devono trovarsi in */home/site/wwwroot/apm/appdynamics*.
5. Nel portale di Azure passare all'applicazione nel servizio app e creare una nuova impostazione dell'applicazione.
    - Se si usa **Java SE**, creare una variabile di ambiente denominata `JAVA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` in cui `<app-name>` è il nome del servizio app.
    - Se si usa **Tomcat**, creare una variabile di ambiente denominata `CATALINA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` in cui `<app-name>` è il nome del servizio app.

    > [!NOTE]
    > Se si dispone già di una variabile di ambiente per `JAVA_OPTS` oppure `CATALINA_OPTS`, aggiungere l'opzione `-javaagent:/...` alla fine del valore corrente.
    
## <a name="configure-jar-applications"></a>Configurare le applicazioni JAR

### <a name="starting-jar-apps"></a>Avvio delle applicazioni JAR

Per impostazione predefinita, il Servizio app di Azure prevede che l'applicazione JAR venga denominata *app.jar*. Se il nome è presente, verrà eseguito automaticamente. Per gli utenti di Maven è possibile impostare il nome JAR includendo `<finalName>app</finalName>` nella sezione `<build>` di *pom.xml*. [È possibile eseguire la stessa operazione in Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) impostando la proprietà `archiveFileName`.

Se si vuole usare un nome diverso per il file JAR, è necessario fornire anche il [comando di avvio](faq-app-service-linux.md#built-in-images) che esegue il file JAR. Ad esempio: `java -jar my-jar-app.jar`. È possibile impostare il valore per il comando di avvio nel portale, in Configurazione > Impostazioni generali o con un'impostazione dell'applicazione denominata `STARTUP_COMMAND`.

### <a name="server-port"></a>Porta server

Il Servizio app di Azure in Linux instrada le richieste in ingresso alla porta 80, in modo che l'applicazione sia in ascolto anche sulla porta 80. Questa operazione può essere eseguita nella configurazione dell'applicazione (ad esempio il file *application.properties* di Spring) oppure nel comando di avvio (ad esempio `java -jar spring-app.jar --server.port=80`). Vedere la documentazione seguente per i framework Java comuni:

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
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Scaricare](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Per configurare Tomcat per l'uso di Java Database Connectivity (JDBC) o Java Persistence API (JPA), personalizzare prima la variabile di ambiente `CATALINA_OPTS` letta da Tomcat all'avvio. Impostare questi valori tramite un'impostazione app nel [plug-in Maven del servizio app](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

In alternativa, impostare le variabili di ambiente nella pagina **Configurazione** > **Impostazioni dell'applicazione** nel portale di Azure.

Determinare quindi se l'origine dati deve essere disponibile per un'applicazione o per tutte le applicazioni in esecuzione nel servlet Tomcat.

#### <a name="application-level-data-sources"></a>Origini dati a livello di applicazione

1. Creare un file *context.xml* nella directory *META-INF/* del progetto. Creare la directory *META-INF/* se non esiste.

2. In *context-xml* aggiungere un elemento `Context` per collegare l'origine dati a un indirizzo JNDI. Sostituire il segnaposto `driverClassName` con il nome della classe del driver indicato nella tabella precedente.

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

3. Aggiornare il file *web.xml* dell'applicazione per usare l'origine dati nell'applicazione.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Risorse a livello di server condiviso

Se si aggiunge un'origine dati condivisa a livello di server, sarà necessario modificare il file server.xml di Tomcat. Per prima cosa, caricare uno [script di avvio](faq-app-service-linux.md#built-in-images) e impostare il percorso dello script in **Configuration** > **Comando di avvio**. È possibile caricare lo script di avvio usando [FTP](deploy-ftp.md).

Lo script di avvio effettuerà una [trasformazione xsl](https://www.w3schools.com/xml/xsl_intro.asp) sul file server.xml e restituirà il file xml risultante a `/usr/local/tomcat/conf/server.xml`. Lo script di avvio deve installare libxslt tramite apk. Il file xsl e lo script di avvio possono essere caricati tramite FTP. Di seguito è riportato uno script di avvio di esempio.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Di seguito è disponibile un esempio di file xsl. Il file xsl di esempio aggiunge un nuovo nodo connettore al file server.xml di Tomcat.

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

Infine, inserire i file driver con estensione jar nel classpath Tomcat e riavviare il Servizio app di Azure.

1. Assicurarsi che i file driver JDBC siano disponibili per il caricatore di classe Tomcat inserendoli nella directory */home/tomcat/lib*. Creare questa directory se non esiste già. Per caricare questi file nell'istanza del servizio app, seguire questa procedura:

    1. Nel [Cloud Shell](https://shell.azure.com) installare l'estensione webapp:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Eseguire il comando dell'interfaccia della riga di comando seguente per creare un tunnel SSH dal sistema locale al Servizio app di Azure:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Connettersi alla porta di tunneling locale con il client SFTP e caricare i file nella cartella */home/tomcat/lib*.

    In alternativa, è possibile usare un client FTP per caricare il driver JDBC. Seguire queste [istruzioni per ottenere le credenziali FTP](deploy-configure-credentials.md).

2. Se è stata creata un'origine dati a livello di server, riavviare l'applicazione Linux del Servizio app. Tomcat reimposterà `CATALINA_BASE` su `/home/tomcat` e userà la configurazione aggiornata.

### <a name="spring-boot"></a>Spring Boot

Per connettersi alle origini dati nelle applicazioni Spring Boot, è consigliabile creare stringhe di connessione e inserirle nel file *application.properties*.

1. Nella sezione "Configurazione" della pagina del Servizio app di Azure, impostare un nome per la stringa, incollare la stringa di connessione JDBC nel campo valore e impostare il tipo su "Personalizzato". Facoltativamente, è possibile impostare questa stringa di connessione come impostazione dello slot.

    Questa stringa di connessione è accessibile all'applicazione come variabile di ambiente denominata `CUSTOMCONNSTR_<your-string-name>`. Ad esempio, la stringa di connessione creata in precedenza verrà denominata `CUSTOMCONNSTR_exampledb`.

2. Nel file *application.properties*, fare riferimento a questa stringa di connessione con il nome della variabile di ambiente. Per questo esempio, si userà il codice seguente.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Per altre informazioni su questo argomento, vedere la [documentazione di Spring Boot relativa all'accesso ai dati](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) e alle [configurazioni esternalizzate](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Usare Redis come cache di sessione con Tomcat

È possibile configurare Tomcat in modo che usi un archivio delle sessioni esterne, ad esempio la [Cache Redis di Azure](/azure/azure-cache-for-redis/). Questo consente di mantenere lo stato della sessione utente, ad esempio i dati del carrello acquisti, quando un utente viene trasferito a un'altra istanza dell'app, ad esempio quando si verifica la scalabilità automatica, il riavvio o il failover.

Per usare Tomcat con Redis, è necessario configurare l'app per l'uso di un'implementazione di [PersistentManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html). I passaggi seguenti illustrano questo processo usando lo [strumento di gestione delle sessioni pivot: redis-store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) come esempio.

1. Aprire un terminale bash e usare `<variable>=<value>` per impostare ognuna delle variabili di ambiente seguenti.

    | Variabile                 | valore                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | Il nome del gruppo di risorse contenente l'istanza del Servizio app di Azure.       |
    | WEBAPP_NAME              | Il nome dell'istanza del Servizio app di Azure.                                     |
    | WEBAPP_PLAN_NAME         | Il nome del piano del Servizio app di Azure.                                         |
    | REGION                   | Il nome dell'area in cui è ospitata l'app.                           |
    | REDIS_CACHE_NAME         | Il nome dell'istanza di cache di Azure per Redis.                           |
    | REDIS_PORT               | La porta SSL su cui è in ascolto la cache Redis.                             |
    | REDIS_PASSWORD           | La chiave di accesso primaria per l'istanza.                                  |
    | REDIS_SESSION_KEY_PREFIX | Un valore specificato per identificare le chiavi della sessione provenienti dall'app. |

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

    È possibile trovare il nome, la porta e le informazioni sulla chiave di accesso nel portale di Azure cercando nelle sezioni **Proprietà** o **Chiavi di accesso** dell'istanza del servizio.

2. Creare o aggiornare il file *src/main/webapp/META-INF/context.xml* dell'app con il contenuto seguente:

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

    Questo file specifica e configura l'implementazione dello strumento di gestione sessioni per l'app. Usa le variabili di ambiente impostate nel passaggio precedente per evitare che le informazioni dell'account siano presenti nei file di origine.

3. Usare FTP per caricare il file JAR dello strumento di gestione sessioni nell'istanza del Servizio app di Azure, inserendolo nella directory */home/tomcat/lib*. Per altre informazioni, vedere [Distribuire l'app nel Servizio app di Azure usando FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

4. Disabilitare il [cookie di affinità sessione](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) per l'istanza del Servizio app di Azure. È possibile eseguire questa operazione dal portale di Azure andando all'app e quindi impostando **Configurazione > Impostazioni generali > Affinità ARR** su **Off**. In alternativa, è possibile usare il comando seguente:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    Per impostazione predefinita, il Servizio app di Azure userà i cookie di affinità di sessione per garantire che le richieste dei client con sessioni esistenti vengano instradate alla stessa istanza dell'applicazione. Questo comportamento predefinito non richiede alcuna configurazione ma non può mantenere lo stato della sessione utente quando l'istanza dell'app viene riavviata o quando il traffico viene reindirizzato a un'altra istanza. [Disabilitando la configurazione di affinità di istanza ARR esistente](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) per disattivare l'instradamento basato sui cookie della sessione, si consente all'archivio delle sessioni configurato di operare senza interferenze.

5. Andare alla sezione **Proprietà** dell'istanza del Servizio app di Azure e trovare **Indirizzi IP in uscita aggiuntivi**. Rappresentano tutti i possibili indirizzi IP in uscita per l'app. Copiarli per usarli nel passaggio successivo.

6. Per ogni indirizzo IP, creare una regola del firewall nella cache di Azure per l'istanza di Redis. Questa operazione può essere eseguita nel portale di Azure dalla sezione **Firewall** dell'istanza di Redis. Specificare un nome univoco per ogni regola e impostare i valori relativi all'**Indirizzo IP iniziale** e all'**Indirizzo IP finale** allo stesso indirizzo IP.

7. Andare alla sezione **Impostazioni avanzate** dell'istanza di Redis e impostare **Consenti l'accesso solo tramite SSL** su **No**. Ciò consente all'istanza del Servizio app di comunicare con la cache Redis tramite l'infrastruttura di Azure.

8. Aggiornare la configurazione `azure-webapp-maven-plugin` nel file *pom.xml* dell'app per fare riferimento alle informazioni sull'account Redis. Questo file usa le variabili di ambiente impostate nel passaggio precedente per evitare che le informazioni dell'account siano presenti nei file di origine.

    Se necessario, sostituire `1.9.1` con la versione corrente del [plug-in Maven per il servizio app di Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.9.1</version>
        <configuration>            
            <!-- Web App information -->
            <schemaVersion>v2</schemaVersion>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>            
            <runtime>
                <os>linux</os>
                <javaVersion>jre8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>

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

Per un esempio che è possibile usare per testare queste istruzioni, vedere il repository [scaling-stateful-java-web-app-on-azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) su GitHub.

## <a name="docker-containers"></a>Contenitori Docker

Per usare nei contenitori il pacchetto JDK Zulu supportato da Azure, assicurarsi di eseguire il pull e di usare le immagini precompilate, come documentato nella [pagina di download di Azul Zulu Enterprise per Azure supportata](https://www.azul.com/downloads/azure-only/zulu/) o di usare gli esempi `Dockerfile` dal [repository GitHub Java di Microsoft](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Istruzione di supporto

### <a name="runtime-availability"></a>Disponibilità runtime

Il servizio app per Linux supporta due runtime per l'hosting gestito delle applicazioni Web Java:

- Il [contenitore servlet Tomcat](https://tomcat.apache.org/) per l'esecuzione di applicazioni in pacchetti come file di archivio Web (WAR). Sono supportate le versioni 8.5 e 9.0.
- Ambiente di runtime Java SE per l'esecuzione di applicazioni in pacchetti come file di archivio Java (JAR). Le versioni supportate sono Java 8 e 11.

### <a name="jdk-versions-and-maintenance"></a>Versioni di JDK e manutenzione

Le build Azul Zulu Enterprise di OpenJDK sono distribuzioni di OpenJDK gratuite, multipiattaforma e pronte per la produzione per Azure e Azure Stack, supportate da Microsoft e Azul Systems. Contengono tutti i componenti necessari per compilare ed eseguire applicazioni Java SE. È possibile installare JDK da [Installazione di Java JDK](https://aka.ms/azure-jdks).

Ai pacchetti JDK supportati vengono automaticamente applicate patch con cadenza trimestrale, a gennaio, aprile, luglio e ottobre di ogni anno.

### <a name="security-updates"></a>Aggiornamenti per la sicurezza

Le patch e le correzioni per le principali vulnerabilità della sicurezza verranno rilasciate da Azul Systems non appena disponibili. Una vulnerabilità "principale" viene definita da un punteggio di base pari o superiore a 9,0 in [NIST Common Vulnerability Scoring System, versione 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Deprecazione e ritiro

Se un runtime Java supportato sarà ritirato, gli sviluppatori di Azure che usano il runtime interessato riceveranno un avviso di funzionalità deprecata almeno sei mesi prima che il runtime venga ritirato.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Passaggi successivi

Per trovare guide introduttive di Azure, esercitazioni e documentazione di riferimento su Java, visitare la pagina [Azure per sviluppatori Java](/java/azure/).

Per domande generali sull'uso del servizio app per Linux, non specifiche dello sviluppo Java, vedere le [domande frequenti sul servizio app in Linux](faq-app-service-linux.md).

::: zone-end

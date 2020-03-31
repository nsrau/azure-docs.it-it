---
title: Configurare le app Java di Windows
description: Informazioni su come configurare le app Java per l'esecuzione nelle istanze di macchine virtuali Windows nel servizio app di Azure.Learn how to configure Java apps to run on the Windows VM instances in Azure App Service. Questo articolo illustra le attività di configurazione più comuni.
keywords: servizio app azure, app Web, windows, oss, java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 2b21061e8a939b91c637ef05bbe6375c0b3f82e8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383974"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Configurare un'app Java di Windows per il servizio app di AzureConfigure a Windows Java app for Azure App Service

Il servizio app di Azure consente agli sviluppatori Java di creare, distribuire e ridimensionare rapidamente le applicazioni Web Tomcat in un servizio completamente gestito basato su Windows.Azure App Service lets Java developers to quickly build, deploy, and scale their Tomcat web applications on a fully managed Windows-based service. Distribuire le applicazioni con i plug-in Maven dalla riga di comando o in editor come IntelliJ, Eclipse o Visual Studio Code.

Questa guida fornisce concetti chiave e istruzioni per gli sviluppatori Java che usano nel servizio app. Se non è mai stato usato il servizio app di Azure, è consigliabile leggere prima la guida introduttiva di Java.If you've never used Azure App Service, you should read through the [Java quickstart](app-service-web-get-started-java.md) first. Le domande generali sull'utilizzo del servizio app che non sono specifiche dello sviluppo Java sono alle risposte nelle [domande frequenti su Windows del servizio app.](faq-configuration-and-management.md)

## <a name="deploying-your-app"></a>Distribuzione dell'app

È possibile usare [il plug-in di App Web di Azure per Maven](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) per distribuire i file con estensione war. La distribuzione con IDE più diffusi è supportata anche con [Azure Toolkit per IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) o [Azure Toolkit per Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

In caso contrario, il metodo di distribuzione dipenderà dal tipo di archivio:Otherwise, your deployment method will depend on your archive type:

- Per distribuire file con estensione war in Tomcat, usare l'endpoint `/api/wardeploy/` per eseguire il comando POST per il file di archivio. Per altre informazioni su questa API, vedere [questa documentazione](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Per distribuire i file JAR in `/api/zipdeploy/` Java SE, utilizzare l'endpoint del sito Kudu. Per altre informazioni su questa API, vedere [questa documentazione](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Non distribuire la tua guerra tramite FTP. Lo strumento FTP è stato progettato per caricare script di avvio, dipendenze o altri file di runtime. Non è la scelta ottimale per la distribuzione di app Web.

## <a name="logging-and-debugging-apps"></a>Registrazione e debug delle app

Nel portale di Azure sono disponibili report sulle prestazioni, visualizzazioni del traffico e controlli dell'integrità per ogni app. Per altre informazioni, vedere [Panoramica](overview-diagnostics.md)della diagnostica del servizio app di Azure.For more information, see Azure App Service diagnostics overview .

### <a name="use-flight-recorder"></a>Utilizzare Flight Recorder

Tutti i runtime Java in App Service che utilizzano i JVM di Azul vengono forniti con il registratore di volo di zulu. È possibile utilizzarlo per registrare gli eventi a livello JVM, system e Java per monitorare il comportamento e risolvere i problemi nelle applicazioni Java.

Per eseguire una registrazione a tempo è necessario il PID (ID processo) dell'applicazione Java. Per trovare il PID, aprire un browser per il sito SCM dell'app Web all'indirizzo https://<nome del sito>.scm.azurewebsites.net/ProcessExplorer/. Questa pagina mostra i processi in esecuzione nell'app Web. Individuare il processo denominato "java" nella tabella e copiare il PID corrispondente (ID processo).

Aprire quindi la Console di **debug** nella barra degli strumenti superiore del sito SCM ed eseguire il comando seguente. Sostituire `<pid>` con l'ID di processo copiato in precedenza. Questo comando avvierà una registrazione del profiler di 30 `timed_recording_example.jfr` secondi `D:\home` dell'applicazione Java e genererà un file denominato nella directory.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

Per ulteriori informazioni, vedere la Guida di riferimento ai [comandi Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

#### <a name="analyze-jfr-files"></a>Analizzare i `.jfr` file

Utilizzare [FTPS](deploy-ftp.md) per scaricare il file JFR nel computer locale. Per analizzare il file JFR, scaricare e installare [il Mission Control di zulu](https://www.azul.com/products/zulu-mission-control/). Per istruzioni sul controllo missione zulu, vedere la documentazione di [Azul](https://docs.azul.com/zmc/) e le istruzioni di [installazione.](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)

### <a name="stream-diagnostic-logs"></a>Eseguire lo streaming dei log di diagnostica

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Per ulteriori informazioni, consultate [Eseguire lo streaming dei log in Cloud Shell.](troubleshoot-diagnostic-logs.md#in-cloud-shell)

### <a name="app-logging"></a>Registrazione dell'app

Abilitare la [registrazione dell'applicazione](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) tramite il portale di Azure o l'[interfaccia della riga di comando di Azure](/cli/azure/webapp/log#az-webapp-log-config) per configurare il servizio app per scrivere l'output della console standard dell'applicazione e i flussi di errori della console standard nel file system locale o in Archiviazione BLOB di Azure. La registrazione nell'istanza del file system del servizio app locale viene disabilitata 12 ore dopo la configurazione. Se è necessario un periodo di conservazione più lungo, configurare l'applicazione per scrivere l'output in un contenitore di archiviazione BLOB. I log dell'app Java e Tomcat sono disponibili nella directory */LogFiles/Application/.*

Se l'applicazione usa [Logback](https://logback.qos.ch/) o [Log4j](https://logging.apache.org/log4j) per la traccia, è possibile inoltrare queste tracce per la revisione ad Azure Application Insights usando le istruzioni di configurazione del framework di registrazione illustrate in [Esplorare i log di traccia Java in Application Insights](/azure/application-insights/app-insights-java-trace-logs).


## <a name="customization-and-tuning"></a>Personalizzazione e ottimizzazione

Il servizio app di Azure supporta l'ottimizzazione e la personalizzazione all'esterno della scatola tramite il portale di Azure e l'interfaccia della riga di comando. Esaminare gli articoli seguenti per la configurazione di app Web non specifiche di Java:

- [Configurare le impostazioni dell'app](configure-common.md#configure-app-settings)
- [Configurare un nome di dominio](app-service-web-tutorial-custom-domain.md)
- [Configurare i binding SSL](configure-ssl-bindings.md)
- [Aggiungere una rete CDN](../cdn/cdn-add-to-web-app.md)
- [Configurare il sito Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Impostare le opzioni di runtime Java

Per impostare la memoria allocata o altre `JAVA_OPTS` opzioni di runtime JVM, creare [un'impostazione dell'app](configure-common.md#configure-app-settings) denominata con le opzioni. Il servizio app passa questa impostazione come variabile di ambiente al runtime Java all'avvio.

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

### <a name="pre-compile-jsp-files"></a>Precompilazione di file JSP

Per migliorare le prestazioni delle applicazioni Tomcat, è possibile compilare i file JSP prima della distribuzione nel servizio app. È possibile utilizzare il [plugin Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) fornito da Apache Sling, o utilizzando questo file di [compilazione Ant](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Proteggere le applicazioni

Le applicazioni Java in esecuzione nel servizio app hanno lo stesso set di [procedure consigliate](/azure/security/security-paas-applications-using-app-services) per la sicurezza di altre applicazioni.

### <a name="authenticate-users-easy-auth"></a>Autenticare gli utenti (Easy Auth)

Configurare l'autenticazione dell'app nel portale di Azure con l'opzione **Autenticazione e autorizzazione.** che consente di abilitare l'autenticazione usando Azure Active Directory o gli account di accesso ai social network, ad esempio Facebook, Google o GitHub. La configurazione nel portale di Azure funziona solo quando si configura un singolo provider di autenticazione. Per altre informazioni, vedere [Configurare un'applicazione dei servizi app per usare l'account di accesso di Azure Active Directory](configure-authentication-provider-aad.md) e gli articoli correlati per gli altri provider di identità. Se è necessario abilitare più provider di accesso, seguire le istruzioni dell'articolo [Personalizzare l'autenticazione nel servizio app](app-service-authentication-how-to.md).

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

### <a name="configure-tlsssl"></a>Configurare TLS/SSL

Seguire le istruzioni in [Proteggere un nome DNS personalizzato con un'associazione SSL nel servizio app](configure-ssl-bindings.md) di Azure per caricare un certificato SSL esistente e associarlo al nome di dominio dell'applicazione. Per impostazione predefinita, l'applicazione consentirà ancora le connessioni HTTP. Seguire i passaggi specifici dell'esercitazione per applicare SSL e TLS.

### <a name="use-keyvault-references"></a>Usare i riferimenti KeyVaultUse KeyVault References

[Azure KeyVault](../key-vault/key-vault-overview.md) offre una gestione segreta centralizzata con criteri di accesso e cronologia di controllo. È possibile archiviare segreti (ad esempio password o stringhe di connessione) in KeyVault e accedere a questi segreti nell'applicazione tramite variabili di ambiente.

In primo luogo, seguire le istruzioni per [concedere all'app l'accesso a Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e creare un riferimento [KeyVault al segreto in un'impostazione dell'applicazione](app-service-key-vault-references.md#reference-syntax). È possibile convalidare che il riferimento viene risolto nel segreto stampando la variabile di ambiente mentre si accede in remoto al terminale del servizio app.

Per inserire questi segreti nel file di configurazione Spring o`${MY_ENV_VAR}`Tomcat, utilizzare la sintassi di inserimento delle variabili di ambiente ( ). Per i file di configurazione di Spring, consultare questa documentazione sulle [configurazioni esternalizzate](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).


## <a name="configure-apm-platforms"></a>Configurare le piattaforme APM

Questa sezione illustra come connettere le applicazioni Java distribuite nel servizio app di Azure in Linux con le piattaforme NewRelic e AppDynamics per il monitoraggio delle prestazioni delle applicazioni (APM).

### <a name="configure-new-relic"></a>Configurare New Relic

1. Creare un account New Relic in [NewRelic.com](https://newrelic.com/signup)
2. Scaricare l'agente Java da NewRelic, avrà un nome di file simile a *newrelic-java-x.x.x.zip*.
3. Copiare il codice di licenza, che sarà necessario per configurare l'agente in un secondo momento.
4. Utilizzare la [console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) per creare una nuova directory */home/site/wwwroot/apm*.
5. Caricare i file dell'agente New Relic Java non compressi in una directory in */home/site/wwwroot/apm*. I file per l'agente devono essere in */home/site/wwwroot/apm/newrelic*.
6. Modificare il file YAML in */home/site/wwwroot/apm/newrelic/newrelic.yml* e sostituire il valore della licenza segnaposto con il proprio codice di licenza.
7. Nel portale di Azure passare all'applicazione nel servizio app e creare una nuova impostazione dell'applicazione.
    - Se l'app usa **Java SE**, creare una variabile di ambiente denominata `JAVA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se si usa **Tomcat**, creare una variabile di ambiente denominata `CATALINA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Configurare AppDynamics

1. Creare un account AppDynamics in [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Scaricare l'agente Java dal sito Web AppDynamics, il nome del file sarà simile a *AppServerAgent-x.x.x.xxxxx.zip*
3. Utilizzare la [console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) per creare una nuova directory */home/site/wwwroot/apm*.
4. Caricare i file dell'agente Java in una directory in */home/site/wwwroot/apm*. I file per l'agente devono essere in */home/site/wwwroot/apm/appdynamics*.
5. Nel portale di Azure passare all'applicazione nel servizio app e creare una nuova impostazione dell'applicazione.
    - Se si usa **Java SE**, creare una variabile di ambiente denominata `JAVA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` in cui `<app-name>` è il nome del servizio app.
    - Se si usa **Tomcat**, creare una variabile di ambiente denominata `CATALINA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` in cui `<app-name>` è il nome del servizio app.

>  Se si dispone già di una variabile di ambiente per `JAVA_OPTS` oppure `CATALINA_OPTS`, aggiungere l'opzione `-javaagent:/...` alla fine del valore corrente.

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

#### <a name="finalize-configuration"></a>Completare la configurazione

Infine, inseriremo i JAR del driver nel percorso di classe Tomcat e riavvieremo il servizio app. Assicurarsi che i file del driver JDBC siano disponibili per il classloader Tomcat inserendoli nella directory */home/tomcat/lib.* (Creare questa directory se non esiste già.) Per caricare questi file nell'istanza del servizio app, eseguire la procedura seguente:To upload these files to your App Service instance, perform the following steps:

1. In [Cloud Shell](https://shell.azure.com), installare l'estensione webapp:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Eseguire il comando CLI seguente per creare un tunnel SSH dal sistema locale al servizio app:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Connettersi alla porta di tunneling locale con il client SFTP e caricare i file nella cartella */home/tomcat/lib.*

In alternativa, è possibile usare un client FTP per caricare il driver JDBC. Seguire queste [istruzioni per ottenere le credenziali FTP](deploy-configure-credentials.md).

## <a name="configuring-tomcat"></a>Configurazione di Tomcat

Per modificare i file `server.xml` di configurazione tomcat o di altro tipo, prendere nota della versione principale di Tomcat nel portale.

1. Trovare la home directory Tomcat per `env` la versione in uso eseguendo il comando. Cercare la variabile di `AZURE_TOMCAT`ambiente che inizia con e corrisponde alla versione principale. Ad esempio, `AZURE_TOMCAT85_HOME` punta alla directory Tomcat di Tomcat 8.5.
1. Una volta identificata la home directory Tomcat per `D:\home`la versione in uso, copiare la directory di configurazione in . Ad esempio, `AZURE_TOMCAT85_HOME` se il `D:\Program Files (x86)\apache-tomcat-8.5.37`valore di , il nuovo `D:\home\apache-tomcat-8.5.37`percorso della directory copiata sarà .

Riavviare infine il servizio app. Le distribuzioni devono `D:\home\site\wwwroot\webapps` essere considerate come prima.

## <a name="configure-java-se"></a>Configurare Java SE

Quando si esegue un file . JAR su Java SE `server.port` in Windows, viene passato come opzione della riga di comando all'avvio dell'applicazione. È possibile risolvere manualmente la porta `HTTP_PLATFORM_PORT`HTTP dalla variabile di ambiente , . Il valore di questa variabile di ambiente sarà la porta HTTP su cui l'applicazione deve restare in ascolto. 

## <a name="java-runtime-statement-of-support"></a>Istruzione di supporto del runtime Java

### <a name="jdk-versions-and-maintenance"></a>Versioni di JDK e manutenzione

Il pacchetto Java Development Kit (JDK) supportato di Azure è [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fornito da [Azul Systems](https://www.azul.com/).

Gli aggiornamenti delle versioni principali verranno forniti tramite le nuove opzioni di runtime nel servizio app di Azure per Windows.Major version updates will be provided through new runtime options in Azure App Service for Windows. I clienti eseguono l'aggiornamento a queste versioni più recenti di Java configurando la distribuzione del servizio app e sono responsabili dei test, oltre che di assicurare che l'aggiornamento principale risponda alle esigenze.

Ai pacchetti JDK supportati vengono automaticamente applicate patch con cadenza trimestrale, a gennaio, aprile, luglio e ottobre di ogni anno. Per altre informazioni su Java in Azure, vedere [questo documento](https://docs.microsoft.com/azure/java/jdk/)di supporto .

### <a name="security-updates"></a>Aggiornamenti per la sicurezza

Le patch e le correzioni per le principali vulnerabilità della sicurezza verranno rilasciate da Azul Systems non appena disponibili. Una vulnerabilità "principale" viene definita da un punteggio di base pari o superiore a 9,0 in [NIST Common Vulnerability Scoring System, versione 2](https://nvd.nist.gov/cvss.cfm).

Tomcat 8.0 ha raggiunto [la fine del ciclo di vita (EOL) al 30 settembre 2018](https://tomcat.apache.org/tomcat-80-eol.html). Mentre il runtime è ancora avialable nel servizio app di Azure, Azure non applicherà gli aggiornamenti della sicurezza a Tomcat 8.0. Se possibile, eseguire la migrazione delle applicazioni a Tomcat 8.5 o 9.0. Tomcat 8.5 e 9.0 sono disponibili nel servizio app di Azure.Both Tomcat 8.5 and 9.0 are available on Azure App Service. Per ulteriori informazioni, consultare il [sito ufficiale di Tomcat.](https://tomcat.apache.org/whichversion.html) 

### <a name="deprecation-and-retirement"></a>Deprecazione e ritiro

Se un runtime Java supportato sarà ritirato, gli sviluppatori di Azure che usano il runtime interessato riceveranno un avviso di funzionalità deprecata almeno sei mesi prima che il runtime venga ritirato.

### <a name="local-development"></a>Sviluppo locale

Gli sviluppatori possono scaricare l'edizione Production di Azul Zulu Enterprise JDK per lo sviluppo locale dal [sito di download di Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Supporto per lo sviluppo

Il supporto del prodotto per il [JDK di Azure supportato da Azul zoul](https://www.azul.com/downloads/azure-only/zulu/) è disponibile tramite Microsoft durante lo sviluppo per Azure o [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) con un piano di [supporto di Azure qualificato.](https://azure.microsoft.com/support/plans/)

### <a name="runtime-support"></a>Supporto di runtime

Gli sviluppatori possono [aprire un problema](/azure/azure-portal/supportability/how-to-create-azure-support-request) relativo ai pacchetti JDK Azul Zulu tramite il supporto di Azure se hanno un [piano di supporto qualificato](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Passaggi successivi

Questo argomento fornisce l'istruzione Java Runtime di supporto per il servizio app di Azure in Windows.This topic provides the Java Runtime statement of support for Azure App Service on Windows.

- Per altre informazioni sull'hosting di applicazioni Web con il servizio app di Azure, vedere Panoramica del [servizio app.](overview.md)
- Per informazioni sullo sviluppo di Java in Azure, vedere [Azure per Java Dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).

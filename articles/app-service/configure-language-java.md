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
ms.custom: seodec18
ms.openlocfilehash: dec5d1c7c7664bf72d92e5aca4333ba64db26d02
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671901"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Configurare un'app Java Windows per il servizio app Azure

App Azure servizio consente agli sviluppatori Java di creare, distribuire e ridimensionare rapidamente le applicazioni Web Tomcat in un servizio basato su Windows completamente gestito. Distribuire le applicazioni con i plug-in Maven dalla riga di comando o in editor come IntelliJ, Eclipse o Visual Studio Code.

Questa guida fornisce i concetti chiave e le istruzioni per gli sviluppatori Java che usano nel servizio app. Se non si è mai usato app Azure servizio, è consigliabile leggere prima di tutto la [Guida introduttiva per Java](app-service-web-get-started-java.md) . Domande generali sull'uso del servizio app non specifiche per lo sviluppo Java sono disponibili nelle [domande frequenti su Windows del servizio app](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Distribuzione dell'app

È possibile usare il plug-in [Maven per il servizio app Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) per distribuire i file con estensione War. La distribuzione con IDE comuni è supportata anche con [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) o [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

In caso contrario, il metodo di distribuzione dipenderà dal tipo di archivio:

- Per distribuire file con estensione war in Tomcat, usare l'endpoint `/api/wardeploy/` per eseguire il comando POST per il file di archivio. Per altre informazioni su questa API, vedere [questa documentazione](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).

Non distribuire il. War mediante FTP. Lo strumento FTP è stato progettato per caricare script di avvio, dipendenze o altri file di runtime. Non è la scelta ottimale per la distribuzione di app Web.

## <a name="logging-and-debugging-apps"></a>Registrazione e debug delle app

Nel portale di Azure sono disponibili report sulle prestazioni, visualizzazioni del traffico e controlli dell'integrità per ogni app. Per ulteriori informazioni, vedere [Panoramica di diagnostica del servizio app Azure](overview-diagnostics.md).

### <a name="stream-diagnostic-logs"></a>Eseguire lo streaming dei log di diagnostica

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Per ulteriori informazioni, vedere la pagina relativa ai [log di flusso in cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Registrazione dell'app

Abilitare la [registrazione dell'applicazione](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) tramite il portale di Azure o l'[interfaccia della riga di comando di Azure](/cli/azure/webapp/log#az-webapp-log-config) per configurare il servizio app per scrivere l'output della console standard dell'applicazione e i flussi di errori della console standard nel file system locale o in Archiviazione BLOB di Azure. La registrazione nell'istanza del file system del servizio app locale viene disabilitata 12 ore dopo la configurazione. Se è necessario un periodo di conservazione più lungo, configurare l'applicazione per scrivere l'output in un contenitore di archiviazione BLOB. I log delle app Java e Tomcat sono reperibili nella directory */LogFiles/Application/*

Se l'applicazione usa [Logback](https://logback.qos.ch/) o [Log4j](https://logging.apache.org/log4j) per la traccia, è possibile inoltrare queste tracce per la revisione ad Azure Application Insights usando le istruzioni di configurazione del framework di registrazione illustrate in [Esplorare i log di traccia Java in Application Insights](/azure/application-insights/app-insights-java-trace-logs).


## <a name="customization-and-tuning"></a>Personalizzazione e ottimizzazione

Il servizio app Azure supporta l'ottimizzazione e la personalizzazione predefinite tramite l'portale di Azure e l'interfaccia della riga di comando. Vedere gli articoli seguenti per la configurazione di app Web non specifiche per Java:

- [Configurare le impostazioni dell'app](configure-common.md#configure-app-settings)
- [Configurare un nome di dominio](app-service-web-tutorial-custom-domain.md)
- [Configurare associazioni SSL](configure-ssl-bindings.md)
- [Aggiungere una rete CDN](../cdn/cdn-add-to-web-app.md)
- [Configurare il sito Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Impostare le opzioni di runtime Java

Per impostare la memoria allocata o altre opzioni di runtime JVM, creare un' [impostazione dell'app](configure-common.md#configure-app-settings) denominata `JAVA_OPTS` con le opzioni. Il servizio app passa questa impostazione come variabile di ambiente al runtime Java all'avvio.

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

### <a name="pre-compile-jsp-files"></a>Pre-compila file JSP

Per migliorare le prestazioni delle applicazioni Tomcat, è possibile compilare i file JSP prima della distribuzione nel servizio app. È possibile usare il [plug](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) -in Maven fornito da Apache Sling o usando questo [file di compilazione Ant](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Proteggere le applicazioni

Le applicazioni Java in esecuzione nel servizio app hanno lo stesso set di procedure consigliate per la [sicurezza](/azure/security/security-paas-applications-using-app-services) di altre applicazioni.

### <a name="authenticate-users-easy-auth"></a>Autenticare gli utenti (Easy auth)

Configurare l'autenticazione delle app nel portale di Azure con l'opzione di **autenticazione e autorizzazione** . che consente di abilitare l'autenticazione usando Azure Active Directory o gli account di accesso ai social network, ad esempio Facebook, Google o GitHub. La configurazione nel portale di Azure funziona solo quando si configura un singolo provider di autenticazione. Per altre informazioni, vedere [Configurare un'applicazione dei servizi app per usare l'account di accesso di Azure Active Directory](configure-authentication-provider-aad.md) e gli articoli correlati per gli altri provider di identità. Se è necessario abilitare più provider di accesso, seguire le istruzioni dell'articolo [Personalizzare l'autenticazione nel servizio app](app-service-authentication-how-to.md).

#### <a name="tomcat-and-wildfly"></a>Tomcat e Wildfly

L'applicazione Tomcat o Wildfly può accedere alle attestazioni dell'utente direttamente dalla servlet eseguendo il cast dell'oggetto Principal a un oggetto map. L'oggetto map eseguirà il mapping di ogni tipo di attestazione a una raccolta di attestazioni per quel tipo. Nel codice riportato di seguito `request` è un'istanza di `HttpServletRequest`.

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

### <a name="configure-tlsssl"></a>Configurare TLS/SSL

Per caricare un certificato SSL esistente e associarlo al nome di dominio dell'applicazione, seguire le istruzioni riportate nella pagina [proteggere un nome DNS personalizzato con binding SSL nel servizio app Azure](configure-ssl-bindings.md) . Per impostazione predefinita, l'applicazione consentirà ancora le connessioni HTTP. Seguire i passaggi specifici dell'esercitazione per applicare SSL e TLS.

### <a name="use-keyvault-references"></a>Usare i riferimenti all'insieme di credenziali delle credenziali

[Azure chiave Vault](../key-vault/key-vault-overview.md) fornisce una gestione centralizzata dei segreti con i criteri di accesso e la cronologia di controllo. È possibile archiviare segreti (ad esempio password o stringhe di connessione) nell'insieme di credenziali delle chiavi e accedere a questi segreti nell'applicazione tramite le variabili di ambiente.

Per prima cosa, seguire le istruzioni per [concedere all'app l'accesso a Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e [creare un riferimento a un insieme di credenziali delle credenziali al segreto in un'impostazione dell'applicazione](app-service-key-vault-references.md#reference-syntax). È possibile verificare che il riferimento venga risolto nel segreto stampando la variabile di ambiente durante l'accesso remoto al terminale del servizio app.

Per inserire questi segreti nel file di configurazione di Spring o Tomcat, usare la sintassi di inserimento delle variabili di ambiente (`${MY_ENV_VAR}`). Per i file di configurazione della primavera, vedere la documentazione sulle [configurazioni esternalizzate](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).


## <a name="configure-apm-platforms"></a>Configurare le piattaforme APM

Questa sezione illustra come connettere le applicazioni Java distribuite nel servizio app Azure in Linux con le piattaforme APM (Application Performance Monitoring) NewRelic e AppDynamics.

### <a name="configure-new-relic"></a>Configurare New Relic

1. Crea un nuovo account Relic in [NewRelic.com](https://newrelic.com/signup)
2. Scaricare l'agente Java da NewRelic. il nome del file sarà simile a quello di *newrelic-Java-x. x.x. x. zip*.
3. Copiare il codice di licenza, che sarà necessario per configurare l'agente in un secondo momento.
4. Usare la [console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) per creare una nuova directory */Home/site/wwwroot/APM*.
5. Caricare i file dell'agente Java di New Relic decompressi in una directory in */Home/site/wwwroot/APM*. I file per l'agente devono trovarsi in */Home/site/wwwroot/APM/newrelic*.
6. Modificare il file YAML in */Home/site/wwwroot/APM/newrelic/newrelic.yml* e sostituire il valore di licenza segnaposto con il proprio codice di licenza.
7. Nel portale di Azure passare all'applicazione nel servizio app e creare una nuova impostazione dell'applicazione.
    - Se l'app usa **Java SE**, creare una variabile di ambiente denominata `JAVA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se si usa **Tomcat**, creare una variabile di ambiente denominata `CATALINA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Configurare AppDynamics

1. Creare un account AppDynamics in [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Scaricare l'agente Java dal sito Web AppDynamics. il nome del file sarà simile a *AppServerAgent-x. x.x. x. xxxxx. zip*
3. Usare la [console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) per creare una nuova directory */Home/site/wwwroot/APM*.
4. Caricare i file dell'agente Java in una directory in */Home/site/wwwroot/APM*. I file per l'agente devono trovarsi in */Home/site/wwwroot/APM/AppDynamics*.
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

#### <a name="finalize-configuration"></a>Completare la configurazione

Infine, i file jar del driver vengono posizionati nel classpath di Tomcat e il servizio app viene riavviato. Assicurarsi che i file del driver JDBC siano disponibili per il classloader di Tomcat inserendoli nella directory */Home/Tomcat/lib* (Creare questa directory se non esiste già.) Per caricare i file nell'istanza del servizio app, seguire questa procedura:

1. Nel [cloud Shell](https://shell.azure.com)installare l'estensione WEBAPP:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Eseguire il comando dell'interfaccia della riga di comando seguente per creare un tunnel SSH dal sistema locale al servizio app:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Connettersi alla porta di tunneling locale con il client SFTP e caricare i file nella cartella */Home/Tomcat/lib* .

In alternativa, è possibile usare un client FTP per caricare il driver JDBC. Seguire queste [istruzioni per ottenere le credenziali FTP](deploy-configure-credentials.md).

## <a name="configuring-tomcat"></a>Configurazione di Tomcat

Per modificare la `server.xml` di Tomcat o altri file di configurazione, prendere nota della versione principale di Tomcat nel portale.

1. Trovare la home directory di Tomcat per la versione in uso eseguendo il comando `env`. Cercare la variabile di ambiente che inizia con `AZURE_TOMCAT`e corrisponda alla versione principale. Ad esempio, `AZURE_TOMCAT85_HOME` punta alla directory Tomcat per Tomcat 8,5.
1. Dopo aver identificato la home directory di Tomcat per la versione, copiare la directory di configurazione in `D:\home`. Se ad esempio `AZURE_TOMCAT85_HOME` il valore `D:\Program Files (x86)\apache-tomcat-8.5.37`, il nuovo percorso della directory copiata verrà `D:\home\apache-tomcat-8.5.37`.

Riavviare infine il servizio app. Le distribuzioni devono passare a `D:\home\site\wwwroot\webapps` come prima.

## <a name="java-runtime-statement-of-support"></a>Istruzione di supporto del runtime Java

### <a name="jdk-versions-and-maintenance"></a>Versioni di JDK e manutenzione

Il pacchetto Java Development Kit (JDK) supportato di Azure è [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fornito da [Azul Systems](https://www.azul.com/).

Gli aggiornamenti delle versioni principali verranno forniti tramite nuove opzioni di runtime nel servizio app Azure per Windows. I clienti eseguono l'aggiornamento a queste versioni più recenti di Java configurando la distribuzione del servizio app e sono responsabili dei test, oltre che di assicurare che l'aggiornamento principale risponda alle esigenze.

Ai pacchetti JDK supportati vengono automaticamente applicate patch con cadenza trimestrale, a gennaio, aprile, luglio e ottobre di ogni anno. Per altre informazioni su Java in Azure, vedere [questo documento di supporto](https://docs.microsoft.com/azure/java/jdk/).

### <a name="security-updates"></a>Aggiornamenti della sicurezza

Le patch e le correzioni per le principali vulnerabilità della sicurezza verranno rilasciate da Azul Systems non appena disponibili. Una vulnerabilità "principale" viene definita da un punteggio di base pari o superiore a 9,0 in [NIST Common Vulnerability Scoring System, versione 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Deprecazione e ritiro

Se un runtime Java supportato sarà ritirato, gli sviluppatori di Azure che usano il runtime interessato riceveranno un avviso di funzionalità deprecata almeno sei mesi prima che il runtime venga ritirato.

### <a name="local-development"></a>Sviluppo locale

Gli sviluppatori possono scaricare l'edizione Production di Azul Zulu Enterprise JDK per lo sviluppo locale dal [sito di download di Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Supporto per lo sviluppo

Il supporto del prodotto per l' [Azul Zulu del JDK supportato da Azure](https://www.azul.com/downloads/azure-only/zulu/) è disponibile tramite Microsoft quando si sviluppa per azure o [Azure stack](https://azure.microsoft.com/overview/azure-stack/) con un [piano di supporto di Azure completo](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Supporto di runtime

Gli sviluppatori possono [aprire un problema](/azure/azure-supportability/how-to-create-azure-support-request) relativo ai pacchetti JDK Azul Zulu tramite il supporto di Azure se hanno un [piano di supporto qualificato](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Passaggi successivi

Questo argomento descrive il supporto di Java Runtime per app Azure servizio in Windows.

- Per altre informazioni sull'hosting di applicazioni Web con app Azure servizio, vedere [Panoramica del servizio app](overview.md).
- Per informazioni sullo sviluppo di Java in Azure, vedere [Azure per Java Dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).

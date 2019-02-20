---
title: Guida dello sviluppatore Java per il servizio app in Linux - Azure | Microsoft Docs
description: Informazioni su come configurare app Java in esecuzione nel Servizio app di Azure in Linux.
keywords: Servizio app di Azure, app Web, Linux, OSS, Java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 12/10/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: 0d24fbe075316e492b638a2877439af270250d70
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234632"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>Guida dello sviluppatore Java per il servizio app in Linux

Il Servizio app di Azure in Linux consente agli sviluppatori Java di compilare, distribuire e ridimensionare rapidamente le applicazioni Web in pacchetto Tomcat o Java Standard Edition (SE) in un servizio basato su Linux completamente gestito. Distribuire le applicazioni con i plug-in Maven dalla riga di comando o in editor come IntelliJ, Eclipse o Visual Studio Code.

Questa guida contiene i concetti chiave e le istruzioni per gli sviluppatori Java che usano il servizio app per Linux. Se è la prima volta che si usa il Servizio app di Azure per Linux, è consigliabile leggere prima la [guida introduttiva a Java](quickstart-java.md). Per domande generali sull'uso del servizio app per Linux, non specifiche dello sviluppo Java, vedere le [domande frequenti sul servizio app in Linux](app-service-linux-faq.md).

## <a name="deploying-your-app"></a>Distribuzione dell'app

È possibile usare il plug-in Maven per distribuire entrambi i tipi di file con estensione jar e war. Per altre informazioni sul plug-in Maven, vedere [questa documentazione](https://docs.microsoft.com/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme?view=azure-java-stable). 

Se non si usa Maven, il metodo di distribuzione dipenderà dal tipo di archivio:

- Per distribuire file con estensione war in Tomcat, usare l'endpoint `/api/wardeploy/` per eseguire il comando POST per il file di archivio. Per altre informazioni su questa API, vedere [questa documentazione](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Per distribuire file con estensione jar nelle immagini di Java SE, usare l'endpoint `/api/zipdeploy/` del sito Kudu. Per altre informazioni su questa API, vedere [questa documentazione](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Non distribuire file con estensione war o jar tramite FTP. Lo strumento FTP è stato progettato per caricare script di avvio, dipendenze o altri file di runtime. Non è la scelta ottimale per la distribuzione di app Web.

## <a name="logging-and-debugging-apps"></a>Registrazione e debug delle app

Nel portale di Azure sono disponibili report sulle prestazioni, visualizzazioni del traffico e controlli dell'integrità per ogni app. Per altre informazioni su come accedere a questi strumenti di diagnostica e su come usarli, vedere la [panoramica della diagnostica del Servizio app di Azure](/azure/app-service/overview-diagnostics).

## <a name="application-performance-monitoring"></a>Monitoraggio delle prestazioni delle applicazioni

Vedere [Application performance monitoring tools with Java apps on Azure App Service on Linux](how-to-java-apm-monitoring.md) (Strumenti di monitoraggio delle prestazioni delle applicazioni con app Java in Servizio app di Azure in Linux) per istruzioni sulle procedure per la configurazione di New Relic e AppDynamics con le app Java in esecuzione in Servizio app in Linux.

### <a name="ssh-console-access"></a>Accesso alla console SSH 

È disponibile la connettività SSH all'ambiente Linux che esegue l'app. Per istruzioni complete per connettersi al sistema Linux tramite il Web browser o il terminale locale, vedere [Supporto SSH per il Servizio app di Azure in Linux](/azure/app-service/containers/app-service-linux-ssh-support).

### <a name="streaming-logs"></a>Streaming dei log

Per velocizzare il debug e la risoluzione dei problemi, è possibile trasmettere i log alla console usando l'interfaccia della riga di comando di Azure. Configurare l'interfaccia della riga di comando con `az webapp log config` per abilitare la registrazione:

```azurecli-interactive
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
 --web-server-logging filesystem
```

Trasmettere quindi i log alla console usando `az webapp log tail`:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

Per altre informazioni, vedere [Streaming dei log con l'interfaccia della riga di comando di Azure](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Registrazione dell'app

Abilitare la [registrazione dell'applicazione](/azure/app-service/troubleshoot-diagnostic-logs#enablediag) tramite il portale di Azure o l'[interfaccia della riga di comando di Azure](/cli/azure/webapp/log#az-webapp-log-config) per configurare il servizio app per scrivere l'output della console standard dell'applicazione e i flussi di errori della console standard nel file system locale o in Archiviazione BLOB di Azure. La registrazione nell'istanza del file system del servizio app locale viene disabilitata 12 ore dopo la configurazione. Se è necessario un periodo di conservazione più lungo, configurare l'applicazione per scrivere l'output in un contenitore di archiviazione BLOB.

Se l'applicazione usa [Logback](https://logback.qos.ch/) o [Log4j](https://logging.apache.org/log4j) per la traccia, è possibile inoltrare queste tracce per la revisione ad Azure Application Insights usando le istruzioni di configurazione del framework di registrazione illustrate in [Esplorare i log di traccia Java in Application Insights](/azure/application-insights/app-insights-java-trace-logs). 

## <a name="customization-and-tuning"></a>Personalizzazione e ottimizzazione

Il Servizio app di Azure per Linux supporta l'ottimizzazione e la personalizzazione predefinite tramite l'interfaccia della riga di comando e il portale di Azure. Per la configurazione delle app Web non specifiche di Java, vedere gli articoli seguenti:

- [Configurare le impostazioni del servizio app](/azure/app-service/web-sites-configure?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Configurare un nome di dominio](/azure/app-service/app-service-web-tutorial-custom-domain?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Abilitare SSL](/azure/app-service/app-service-web-tutorial-custom-ssl?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Aggiungere una rete CDN](/azure/cdn/cdn-add-to-web-app?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)

### <a name="set-java-runtime-options"></a>Impostare le opzioni di runtime Java

Per impostare la memoria allocata o altre opzioni di runtime JVM in entrambi gli ambienti Tomcat e Java SE, impostare JAVA_OPTS come [impostazione applicazione](/azure/app-service/web-sites-configure#app-settings), come illustrato sotto. Il servizio app in Linux passa questa impostazione come variabile di ambiente al runtime Java quando viene avviato.

Nel portale di Azure, sotto **Impostazioni applicazione** per l'app Web creare una nuova impostazione dell'app denominata `JAVA_OPTS` che includa le impostazioni aggiuntive, ad esempio `$JAVA_OPTS -Xms512m -Xmx1204m`.

Per configurare l'impostazione dell'app dal plug-in Maven del Servizio app di Azure in Linux, aggiungere i tag setting/value nella sezione dei plug-in di Azure. L'esempio seguente imposta le dimensioni heap Java specifiche minime e massime:

```xml
<appSettings> 
    <property> 
        <name>JAVA_OPTS</name> 
        <value>$JAVA_OPTS -Xms512m -Xmx1204m</value> 
    </property> 
</appSettings> 
```

Gli sviluppatori che eseguono una singola applicazione con uno slot di distribuzione nel piano di servizio app possono usare le opzioni seguenti:

- Istanze B1 e S1: -Xms1024m -Xmx1024m
- Istanze B2 e S2: -Xms3072m -Xmx3072m
- Istanze B3 e S3: -Xms6144m -Xmx6144m


Quando si ottimizzano le impostazioni heap delle applicazioni, esaminare i dettagli del piano di servizio app e tenere in considerazione le esigenze di più applicazioni e slot di distribuzione per trovare l'allocazione ottimale della memoria.

### <a name="turn-on-web-sockets"></a>Attivare i Web Socket

Attivare il supporto per i Web Socket nel portale di Azure in **Impostazioni applicazione** per l'applicazione. Sarà necessario riavviare l'applicazione per rendere effettiva l'impostazione.

Attivare il supporto per i Web Socket usando l'interfaccia della riga di comando di Azure con il comando seguente:

```azurecli-interactive
az webapp config set -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} --web-sockets-enabled true 
```

Riavviare quindi l'applicazione:

```azurecli-interactive
az webapp stop -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} 
az webapp start -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}  
```

### <a name="set-default-character-encoding"></a>Impostare la codifica dei caratteri predefinita 

Nel portale di Azure, sotto **Impostazioni applicazione** per l'app Web creare una nuova impostazione dell'app denominata `JAVA_OPTS` con il valore `$JAVA_OPTS -Dfile.encoding=UTF-8`.

In alternativa, è possibile configurare l'impostazione dell'app usando il plug-in Maven del servizio app. Aggiungere i tag name e value dell'impostazione nella configurazione del plug-in: 

```xml
<appSettings> 
    <property> 
        <name>JAVA_OPTS</name> 
        <value>$JAVA_OPTS -Dfile.encoding=UTF-8</value> 
    </property> 
</appSettings> 
```

## <a name="secure-applications"></a>Proteggere le applicazioni

Le applicazioni Java in esecuzione nel servizio app per Linux hanno lo stesso set di [procedure consigliate per la sicurezza](/azure/security/security-paas-applications-using-app-services) delle altre applicazioni. 

### <a name="authenticate-users"></a>Autenticare gli utenti

Configurare l'autenticazione dell'app nel portale di Azure con l'opzione **Authentication and Authorization** (Autenticazione e autorizzazione), che consente di abilitare l'autenticazione usando Azure Active Directory o gli account di accesso ai social network, ad esempio Facebook, Google o GitHub. La configurazione nel portale di Azure funziona solo quando si configura un singolo provider di autenticazione.  Per altre informazioni, vedere [Configurare un'applicazione dei servizi app per usare l'account di accesso di Azure Active Directory](/azure/app-service/configure-authentication-provider-aad) e gli articoli correlati per gli altri provider di identità.

Se è necessario abilitare più provider di accesso, seguire le istruzioni dell'articolo [Personalizzare l'autenticazione nel servizio app](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to).

Gli sviluppatori Spring Boot possono usare l'[utilità di avvio Spring Boot per Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) per proteggere le applicazioni usando le familiari annotazioni e API di Spring Security. Assicurarsi di aumentare le dimensioni massime dell'intestazione nel file `application.properties`. È consigliabile il valore `16384`. 

### <a name="configure-tlsssl"></a>Configurare TLS/SSL

Seguire le istruzioni illustrate in [Associare un certificato SSL personalizzato esistente](/azure/app-service/app-service-web-tutorial-custom-ssl) per caricare un certificato SSL esistente e associarlo al nome di dominio dell'applicazione. Per impostazione predefinita, l'applicazione consentirà ancora le connessioni HTTP. Seguire i passaggi specifici dell'esercitazione per applicare SSL e TLS.

## <a name="tomcat"></a>Tomcat 

### <a name="connecting-to-data-sources"></a>Connessione alle origini dati

>[!NOTE]
> Se l'applicazione usa Spring Framework o Spring Boot, è possibile impostare le informazioni di connessione di database per Spring Data JPA come variabili di ambiente [nel file delle proprietà dell'applicazione]. Usare quindi le [impostazioni app](/azure/app-service/web-sites-configure#app-settings) per definire questi valori per l'applicazione nell'interfaccia della riga di comando o nel portale di Azure.

Queste istruzioni si applicano a tutte le connessioni di database. È necessario sostituire i segnaposto con il nome della classe del driver del database scelto e il file JAR. Viene fornita una tabella con i nomi delle classi e i download dei driver per i database più comuni.

| Database   | Nome della classe del driver                             | Driver JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Drvier`                        | [Scaricare](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Scaricare](https://dev.mysql.com/downloads/connector/j/) (selezionare "Indipendente dalla piattaforma") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Scaricare](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Per configurare Tomcat per l'uso di Java Database Connectivity (JDBC) o Java Persistence API (JPA), personalizzare prima la variabile di ambiente `CATALINA_OPTS` letta da Tomcat all'avvio. Impostare questi valori tramite un'impostazione app nel [plug-in Maven del servizio app](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings> 
    <property> 
        <name>CATALINA_OPTS</name> 
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value> 
    </property> 
</appSettings> 
```

In alternativa, impostare le variabili di ambiente nel pannello "Impostazioni applicazione" nel portale di Azure.

Determinare quindi se l'origine dati deve essere disponibile per un'applicazione o per tutte le applicazioni in esecuzione nel servlet Tomcat.

#### <a name="for-application-level-data-sources"></a>Per le origini dati a livello di applicazione: 

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

#### <a name="for-shared-server-level-resources"></a>Per le risorse a livello di server condiviso:

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

#### <a name="finally-place-the-driver-jars-in-the-tomcat-classpath-and-restart-your-app-service"></a>Infine, inserire i file di driver con estensione jar nel classpath Tomcat e riavviare il Servizio app: 

1. Assicurarsi che i file driver JDBC siano disponibili per il caricatore di classe Tomcat inserendoli nella directory `/home/tomcat/lib`. Creare questa directory se non esiste già. Per caricare questi file nell'istanza del servizio app, seguire questa procedura:  
    1. Installare l'estensione app Web del Servizio app di Azure:

      ```azurecli-interactive
      az extension add –name webapp
      ```

    2. Eseguire il comando dell'interfaccia della riga di comando seguente per creare un tunnel SSH dal sistema locale al servizio app:

      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```

    3. Connettersi alla porta di tunneling locale con il client SFTP e caricare i file nella cartella `/home/tomcat/lib`.

    In alternativa, è possibile usare un client FTP per caricare il driver JDBC. Seguire queste [istruzioni per ottenere le credenziali FTP](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

2. Se è stata creata un'origine dati a livello di server, riavviare l'applicazione Linux del Servizio app. Tomcat reimposterà `CATALINA_HOME` su `/home/tomcat/conf` e userà la configurazione aggiornata.

## <a name="docker-containers"></a>Contenitori Docker

Per usare nei contenitori il pacchetto JDK Zulu supportato da Azure, assicurarsi di eseguire il pull e di usare le immagini precompilate, come documentato nella [pagina di download di Azul Zulu Enterprise per Azure supportata](https://www.azul.com/downloads/azure-only/zulu/) o di usare gli esempi `Dockerfile` dal [repository GitHub Java di Microsoft](https://github.com/Microsoft/java/tree/master/docker).

## <a name="runtime-availability-and-statement-of-support"></a>Disponibilità del runtime e istruzione di supporto

Il servizio app per Linux supporta due runtime per l'hosting gestito delle applicazioni Web Java:

- Il [contenitore servlet Tomcat](https://tomcat.apache.org/) per l'esecuzione di applicazioni in pacchetti come file di archivio Web (WAR). Sono supportate le versioni 8.5 e 9.0.
- Ambiente di runtime Java SE per l'esecuzione di applicazioni in pacchetti come file di archivio Java (JAR). L'unica versione principale supportata è Java 8.

## <a name="java-runtime-statement-of-support"></a>Istruzione di supporto del runtime Java 

### <a name="jdk-versions-and-maintenance"></a>Versioni di JDK e manutenzione

Il pacchetto Java Development Kit (JDK) supportato di Azure è [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fornito da [Azul Systems](https://www.azul.com/).

Gli aggiornamenti delle versioni principali verranno forniti tramite nuove opzioni di runtime nel Servizio app di Azure per Linux. I clienti eseguono l'aggiornamento a queste versioni più recenti di Java configurando la distribuzione del servizio app e sono responsabili dei test, oltre che di assicurare che l'aggiornamento principale risponda alle esigenze.

Ai pacchetti JDK supportati vengono automaticamente applicate patch con cadenza trimestrale, a gennaio, aprile, luglio e ottobre di ogni anno.

### <a name="security-updates"></a>Aggiornamenti della sicurezza

Le patch e le correzioni per le principali vulnerabilità della sicurezza verranno rilasciate da Azul Systems non appena disponibili. Una vulnerabilità "principale" viene definita da un punteggio di base pari o superiore a 9,0 in [NIST Common Vulnerability Scoring System, versione 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Deprecazione e ritiro

Se un runtime Java supportato sarà ritirato, gli sviluppatori di Azure che usano il runtime interessato riceveranno un avviso di funzionalità deprecata almeno sei mesi prima che il runtime venga ritirato.

### <a name="local-development"></a>Sviluppo locale

Gli sviluppatori possono scaricare l'edizione Production di Azul Zulu Enterprise JDK per lo sviluppo locale dal [sito di download di Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Supporto per lo sviluppo

Il supporto per il prodotto [Azul Zulu Enterprise JDK](https://www.azul.com/downloads/azure-only/zulu/) è disponibile quando si sviluppa per Azure o [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) con un [piano di supporto per Azure qualificato](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Supporto di runtime

Gli sviluppatori possono [aprire un problema](/azure/azure-supportability/how-to-create-azure-support-request) relativo ai pacchetti JDK Azul Zulu tramite il supporto di Azure se hanno un [piano di supporto qualificato](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Passaggi successivi

Per trovare guide introduttive di Azure, esercitazioni e documentazione di riferimento su Java, visitare la pagina [Azure per sviluppatori Java](/java/azure/).

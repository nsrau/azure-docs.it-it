---
title: Supporto del linguaggio Java per il Servizio app di Azure in Linux | Microsoft Docs
description: Guida dello sviluppatore alla distribuzione di app Java con il Servizio app di Azure in Linux.
keywords: Servizio app di Azure, app Web, Linux, OSS, Java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/29/2018
ms.author: routlaw
ms.openlocfilehash: e11b115d7a6421c34e7f1371ad8931b6affa0436
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815172"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>Guida dello sviluppatore Java per il servizio app in Linux

Il Servizio app di Azure in Linux consente agli sviluppatori Java di compilare, distribuire e ridimensionare rapidamente le applicazioni Web in pacchetto Tomcat o Java Standard Edition (SE) in un servizio basato su Linux completamente gestito. Distribuire le applicazioni con i plug-in Maven dalla riga di comando o in editor come IntelliJ, Eclipse o Visual Studio Code.

Questa guida contiene i concetti chiave e le istruzioni per gli sviluppatori Java che usano il servizio app per Linux. Se è la prima volta che si usa il Servizio app di Azure per Linux, è consigliabile leggere prima la [guida introduttiva a Java](quickstart-java.md). Per domande generali sull'uso del servizio app per Linux, non specifiche dello sviluppo Java, vedere le [domande frequenti sul servizio app in Linux](app-service-linux-faq.md).

## <a name="logging-and-debugging-apps"></a>Registrazione e debug delle app

Nel portale di Azure sono disponibili report sulle prestazioni, visualizzazioni del traffico e controlli dell'integrità per ogni app. Per altre informazioni su come accedere a questi strumenti di diagnostica e su come usarli, vedere la [panoramica della diagnostica del Servizio app di Azure](/azure/app-service/app-service-diagnostics).

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

Per altre informazioni, vedere [Streaming dei log con l'interfaccia della riga di comando di Azure](/azure/app-service/web-sites-enable-diagnostic-log#streaming-with-azure-command-line-interface).

### <a name="app-logging"></a>Registrazione dell'app

Abilitare la [registrazione dell'applicazione](/azure/app-service/web-sites-enable-diagnostic-log#enablediag) tramite il portale di Azure o l'[interfaccia della riga di comando di Azure](/cli/azure/webapp/log#az-webapp-log-config) per configurare il servizio app per scrivere l'output della console standard dell'applicazione e i flussi di errori della console standard nel file system locale o in Archiviazione BLOB di Azure. La registrazione nell'istanza del file system del servizio app locale viene disabilitata 12 ore dopo la configurazione. Se è necessario un periodo di conservazione più lungo, configurare l'applicazione per scrivere l'output in un contenitore di archiviazione BLOB.

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

## <a name="secure-application"></a>Proteggere l'applicazione

Le applicazioni Java in esecuzione nel servizio app per Linux hanno lo stesso set di [procedure consigliate per la sicurezza](/azure/security/security-paas-applications-using-app-services) delle altre applicazioni. 

### <a name="authenticate-users"></a>Autenticare gli utenti

Configurare l'autenticazione dell'app nel portale di Azure con l'opzione **Authentication and Authorization** (Autenticazione e autorizzazione), che consente di abilitare l'autenticazione usando Azure Active Directory o gli account di accesso ai social network, ad esempio Facebook, Google o GitHub. La configurazione nel portale di Azure funziona solo quando si configura un singolo provider di autenticazione.  Per altre informazioni, vedere [Configurare un'applicazione dei servizi app per usare l'account di accesso di Azure Active Directory](/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication) e gli articoli correlati per gli altri provider di identità.

Se è necessario abilitare più provider di accesso, seguire le istruzioni dell'articolo [Personalizzare l'autenticazione nel servizio app](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to).

 Gli sviluppatori Spring Boot possono usare l'[utilità di avvio Spring Boot per Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) per proteggere le applicazioni usando le familiari annotazioni e API di Spring Security.

### <a name="configure-tlsssl"></a>Configurare TLS/SSL

Seguire le istruzioni illustrate in [Associare un certificato SSL personalizzato esistente](/azure/app-service/app-service-web-tutorial-custom-ssl) per caricare un certificato SSL esistente e associarlo al nome di dominio dell'applicazione. Per impostazione predefinita, l'applicazione consentirà ancora le connessioni HTTP. Seguire i passaggi specifici dell'esercitazione per applicare SSL e TLS.

## <a name="tomcat"></a>Tomcat 

### <a name="connecting-to-data-sources"></a>Connessione alle origini dati

>[!NOTE]
> Se l'applicazione usa Spring Framework o Spring Boot, è possibile impostare le informazioni di connessione di database per Spring Data JPA come variabili di ambiente [nel file delle proprietà dell'applicazione]. Usare quindi le [impostazioni app](/azure/app-service/web-sites-configure#app-settings) per definire questi valori per l'applicazione nell'interfaccia della riga di comando o nel portale di Azure.

Per configurare Tomcat per usare le connessioni gestite ai database usando Java Database Connectivity (JDBC) o Java Persistence API (JPA), personalizzare prima la variabile di ambiente CATALINA_OPTS letta da Tomcat all'avvio. Impostare questi valori tramite un'impostazione app nel plug-in Maven del servizio app:

```xml
<appSettings> 
    <property> 
        <name>CATALINA_OPTS</name> 
        <value>"$CATALINA_OPTS -Dmysqluser=${mysqluser} -Dmysqlpass=${mysqlpass} -DmysqlURL=${mysqlURL}"</value> 
    </property> 
</appSettings> 
```

Oppure un'impostazione del servizio app equivalente dal portale di Azure.

Determinare quindi se le origini dati devono essere disponibili solo per un'applicazione o per tutte le applicazioni in esecuzione nel piano di servizio app.

Per le origini dati a livello di applicazione: 

1. Aggiungere un file `context.xml`, se non esiste, all'applicazione Web e aggiungerlo alla directory `META-INF` del file WAR quando viene creato il progetto.

2. In questo file aggiungere una voce di percorso `Context` per collegare l'origine dati all'indirizzo JNDI. Il valore di 

    ```xml
    <Context>
        <Resource
            name="jdbc/mysqldb" type="javax.sql.DataSource"
            url="${mysqlURL}"
            driverClassName="com.mysql.jdbc.Driver"
            username="${mysqluser}" password="${mysqlpass}"
        />
    </Context>
    ```

3. Aggiornare il file `web.xml` dell'applicazione per usare l'origine dati nell'applicazione.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/mysqldb</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

Per le risorse a livello di server condiviso:

1. Copiare il contenuto di `/usr/local/tomcat/conf` in `/home/tomcat` nell'istanza del servizio app in Linux usando SSH se non è già disponibile una configurazione.

2. Aggiungere il contesto a `server.xml`

    ```xml
    <Context>
        <Resource
            name="jdbc/mysqldb" type="javax.sql.DataSource"
            url="${mysqlURL}"
            driverClassName="com.mysql.jdbc.Driver"
            username="${mysqluser}" password="${mysqlpass}"
        />
    </Context>
    ```

3. Aggiornare il file `web.xml` dell'applicazione per usare l'origine dati nell'applicazione.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/mysqldb</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

4. Assicurarsi che i file driver JDBC siano disponibili per il caricatore di classe Tomcat inserendoli nella directory `/home/tomcat/lib`. Per caricare questi file nell'istanza del servizio app, seguire questa procedura:  
    1. Installare l'estensione app Web del Servizio app di Azure:

      ```azurecli-interactive
      az extension add –name webapp
      ```

    2. Eseguire il comando dell'interfaccia della riga di comando seguente per creare un tunnel SSH dal sistema locale al servizio app:

      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```

    3. Connettersi alla porta di tunneling locale con il client SFTP e caricare i file nella cartella `/home/tomcat/lib`.

5. Riavviare l'applicazione del servizio app in Linux. Tomcat reimposterà `CATALINA_HOME` su `/home/tomcat` e userà la configurazione e le classi aggiornate.

## <a name="docker-containers"></a>Contenitori Docker

Per usare nei contenitori il pacchetto JDK Zulu supportato in Azure, assicurarsi di eseguire il pull delle immagini esistenti elencate nella [pagina di download di Azul](https://www.azul.com/downloads/azure-only/zulu/#docker) e di usare tale immagini oppure usare gli esempi `Dockerfile` nel [repository GitHub Java di Microsoft](https://github.com/Microsoft/java/tree/master/docker).

## <a name="runtime-availability-and-statement-of-support"></a>Disponibilità del runtime e istruzione di supporto

Il servizio app per Linux supporta due runtime per l'hosting gestito delle applicazioni Web Java:

- Il [contenitore servlet Tomcat](http://tomcat.apache.org/) per l'esecuzione di applicazioni in pacchetti come file di archivio Web (WAR). Sono supportate le versioni 8.5 e 9.0.
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

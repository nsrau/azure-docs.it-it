---
title: 'Monitorare le applicazioni Java in esecuzione in qualsiasi ambiente: monitoraggio di Azure Application Insights'
description: Application Performance Monitoring per le applicazioni Java in esecuzione in qualsiasi ambiente con agente autonomo Java senza strumentazione dell'app. Traccia distribuita e mappa delle applicazioni.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 527f1eaf04be7b5e8c89c12912a06d2f5d50321f
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508038"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>Configurazione degli argomenti JVM Java autonomo Agent per monitoraggio di Azure Application Insights



## <a name="azure-environments"></a>Ambienti Azure

Configurare i [Servizi app](https://docs.microsoft.com/azure/app-service/configure-language-java#set-java-runtime-options).

## <a name="spring-boot"></a>Spring Boot

Aggiungere la JVM ARG `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` in un `-jar`punto qualsiasi prima, ad esempio:

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

## <a name="spring-boot-via-docker-entry-point"></a>Spring boot tramite il punto di ingresso Docker

Se si usa il modulo *Exec* , aggiungere il parametro `"-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"` all'elenco di parametri in un punto precedente `"-jar"` al parametro, ad esempio:

```
ENTRYPOINT ["java", "-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar", "-jar", "<myapp.jar>"]
```

Se si usa il modulo della *Shell* , aggiungere la JVM ARG `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` in un `-jar`punto precedente, ad esempio:

```
ENTRYPOINT java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat installato tramite `apt-get` o`yum`

Se Tomcat è stato installato `apt-get` tramite `yum`o, è necessario disporre di un `/etc/tomcat8/tomcat8.conf`file.  Aggiungere questa riga alla fine del file:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat installato tramite download e decompressione

Se Tomcat è stato installato tramite download e unzip [https://tomcat.apache.org](https://tomcat.apache.org)da, è necessario disporre di un `<tomcat>/bin/catalina.sh`file.  Creare un nuovo file nella stessa directory denominata `<tomcat>/bin/setenv.sh` con il contenuto seguente:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Se il file `<tomcat>/bin/setenv.sh` esiste già, modificare il file e aggiungere `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` a. `CATALINA_OPTS`


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Esecuzione di Tomcat dalla riga di comando

Individuare il file `<tomcat>/bin/catalina.bat`.  Creare un nuovo file nella stessa directory denominata `<tomcat>/bin/setenv.bat` con il contenuto seguente:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

Le virgolette non sono necessarie, ma se si desidera includerle, il posizionamento appropriato è:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Se il file `<tomcat>/bin/setenv.bat` esiste già, è sufficiente modificare il file e `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` aggiungere `CATALINA_OPTS`a.

### <a name="running-tomcat-as-a-windows-service"></a>Esecuzione di Tomcat come servizio Windows

Individuare il file `<tomcat>/bin/tomcat8w.exe`.  Eseguire il file eseguibile `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` e aggiungere `Java Options` a sotto `Java` la scheda.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Server autonomo

Aggiungere `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` alla variabile di `JAVA_OPTS` ambiente esistente nel file `JBOSS_HOME/bin/standalone.conf` (Linux) o `JBOSS_HOME/bin/standalone.conf.bat` (Windows):

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Server di dominio

Aggiungi `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` alla esistente `jvm-options` in `JBOSS_HOME/domain/configuration/host.xml`:

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

Se si eseguono più server gestiti in un singolo host, sarà necessario aggiungere `applicationinsights.agent.id` alla `system-properties` per ogni: `server`

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

Il valore `applicationinsights.agent.id` specificato deve essere univoco. Viene usato per creare una sottodirectory nella directory applicationinsights, perché ogni processo JVM necessita di una configurazione applicationinsights locale e di un file di log applicationinsights locale. Inoltre, se si inviano report all'agente di `applicationinsights.properties` raccolta centrale, il file viene condiviso da più server gestiti, pertanto `applicationinsights.agent.id` l'oggetto specificato è necessario `agent.id` per eseguire l'override dell'impostazione nel file condiviso. `applicationinsights.agent.rollup.id`può essere specificato in modo analogo nel `system-properties` server se è necessario eseguire l' `agent.rollup.id` override dell'impostazione per ogni server gestito.


## <a name="jetty-9"></a>Jetty 9

Aggiungi le righe seguenti a`start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>Payara 5

Aggiungi `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` alla esistente `jvm-options` in `glassfish/domains/domain1/config/domain.xml`:

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSphere 8

Aprire Management console passare a **server > WebSphere Application servers > server**applicazioni, scegliere i server applicazioni appropriati e fare clic su: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
In "argomenti JVM generici" aggiungere quanto segue:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
Successivamente, salvare e riavviare il server applicazioni.


## <a name="openliberty-18"></a>OpenLiberty 18

Creare un nuovo file `jvm.options` nella directory del server (ad esempio `<openliberty>/usr/servers/defaultServer`) e aggiungere la riga seguente:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

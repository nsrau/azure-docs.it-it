---
title: Monitor Java applications running in any environment - Azure Monitor Application Insights
description: Monitoraggio delle prestazioni delle applicazioni per le applicazioni Java in esecuzione in qualsiasi ambiente con agente autonomo Java senza strumentazione dell'app. Traccia distribuita e mappa dell'applicazione.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 08a83fbc05276808b62a0391a5c4217cc09f6d00
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641875"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>Configurazione degli argomenti JVM Agente autonomo Java per Azure Monitor Application InsightsConfiguring JVM args Java standalone agent for Azure Monitor Application Insights



## <a name="azure-environments"></a>Ambienti Azure

Configurare [i servizi dell'app](https://docs.microsoft.com/azure/app-service/configure-language-java#set-java-runtime-options).

## <a name="spring-boot"></a>Spring Boot

Aggiungere l'arg `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` JVM da qualche parte prima `-jar <myapp.jar>`, ad esempio:

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

> [!NOTE]
> Gli argomenti `-jar <myapp.jar>` posizionati dopo vengono passati all'app come argomenti del programma.


## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat installato `apt-get` tramite o`yum`

Se è stato installato `apt-get` `yum`Tomcat via o `/etc/tomcat8/tomcat8.conf`, allora si dovrebbe avere un file .  Aggiungere questa riga alla fine del file:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat installato tramite download e decomprimi

Se è stato installato Tomcat [https://tomcat.apache.org](https://tomcat.apache.org)tramite download e `<tomcat>/bin/catalina.sh`decomprimere da , allora si dovrebbe avere un file .  Creare un nuovo file nella `<tomcat>/bin/setenv.sh` stessa directory denominata con il contenuto seguente:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Se il `<tomcat>/bin/setenv.sh` file esiste già, modificare `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `CATALINA_OPTS`il file e aggiungerlo a .


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Esecuzione di Tomcat dalla riga di comando

Individuare il `<tomcat>/bin/catalina.bat`file .  Creare un nuovo file nella `<tomcat>/bin/setenv.bat` stessa directory denominata con il contenuto seguente:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

Le citazioni non sono necessarie, ma se si desidera includerli, il posizionamento corretto è:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Se il `<tomcat>/bin/setenv.bat` file esiste già, è `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `CATALINA_OPTS`sufficiente modificare tale file e aggiungerlo a .

### <a name="running-tomcat-as-a-windows-service"></a>Esecuzione di Tomcat come servizio WindowsRunning Tomcat as a Windows service

Individuare il `<tomcat>/bin/tomcat8w.exe`file .  Eseguire l'eseguibile `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `Java Options` e `Java` aggiungerlo alla sottolato la scheda.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Server autonomo

Aggiungere `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` alla `JAVA_OPTS` variabile di ambiente `JBOSS_HOME/bin/standalone.conf` esistente nel `JBOSS_HOME/bin/standalone.conf.bat` file (Linux) o (Windows):Add to the existing environment variable in the file (Linux) or (Windows):

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Server di dominio

Aggiungi `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `jvm-options` all'esistente `JBOSS_HOME/domain/configuration/host.xml`in :

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

Se si eseguono più server gestiti in un `applicationinsights.agent.id` singolo `system-properties` host, sarà necessario aggiungere al per ogni: `server`

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

Il `applicationinsights.agent.id` valore specificato deve essere univoco. Viene utilizzato per creare una sottodirectory nella directory applicationinsights, poiché ogni processo JVM necessita di una propria configurazione applicationinsights locale e di un file di log applicationinsights locale. Inoltre, se si segnala all'agente di raccolta centrale, il `applicationinsights.properties` file `applicationinsights.agent.id` viene condiviso `agent.id` da più server gestiti e pertanto è necessario specificare per sostituire l'impostazione in tale file condiviso. `applicationinsights.agent.rollup.id`può essere specificato in modo `system-properties` analogo nel server `agent.rollup.id` se è necessario eseguire l'override dell'impostazione per ogni server gestito.


## <a name="jetty-9"></a>Jetty 9

Aggiungi queste righe a`start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>Payara 5

Aggiungi `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `jvm-options` all'esistente `glassfish/domains/domain1/config/domain.xml`in :

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

## <a name="websphere-8"></a>Sfera Web 8

Open Management Console vai **ai server > server applicazioni WebSphere > server applicazioni**, scegliere i server applicazioni appropriati e fare clic su: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
In "Argomenti JVM generici" aggiungere quanto segue:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
Dopo di che, salvare e riavviare il server applicazioni.


## <a name="openliberty-18"></a>OpenLiberty 18

Creare un `jvm.options` nuovo file nella directory `<openliberty>/usr/servers/defaultServer`del server (ad esempio ) e aggiungere questa riga:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

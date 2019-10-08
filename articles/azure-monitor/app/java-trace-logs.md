---
title: Esaminare i log di traccia Java in Azure Application Insights | Documentazione Microsoft
description: Eseguire la ricerca di tracce Log4J o Logback in Application Insights
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/18/2019
ms.author: mbullwin
ms.openlocfilehash: 99a18125fa5ce07a46fcc1af554fd03746ebf110
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038144"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Esplorare i log di traccia Java in Application Insights
Se si usa Logback o Log4J (v1.2 o v2.0) per la traccia, è possibile inviare automaticamente i log di traccia ad Application Insights dove è possibile esplorarli e eseguirvi ricerche.

> [!TIP]
> È necessario impostare la chiave di strumentazione Application Insights una sola volta per l'applicazione. Se si usa un Framework come Java Spring, è possibile che la chiave sia già stata registrata in un'altra posizione nella configurazione dell'app.

## <a name="using-the-application-insights-java-agent"></a>Uso dell'agente Java Application Insights

È possibile configurare l'agente Java Application Insights per acquisire automaticamente i log, abilitando la funzionalità nel file `AI-Agent.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">
         <Logging enabled="true" />
      </BuiltIn>
   </Instrumentation>
   <AgentLogger />
</ApplicationInsightsAgent>
```

In alternativa, è possibile seguire le istruzioni riportate di seguito.

## <a name="install-the-java-sdk"></a>Installare Java SDK

Seguire le istruzioni per installare [Application Insights SDK per Java][java], se non è già stato fatto.

## <a name="add-logging-libraries-to-your-project"></a>Aggiungere le librerie di registrazione al progetto
*Scegliere il modo più appropriato per il progetto.*

#### <a name="if-youre-using-maven"></a>Se si usa Maven...
Se il progetto è già stato configurato per usare Maven per la compilazione, aggiungere uno dei frammenti di codice seguenti nel file pom.xml.

Aggiornare quindi le dipendenze progetto per fare in modo che i file binari vengano scaricati.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Se si usa Gradle...
Se il progetto è già configurato per usare Gradle per la compilazione, aggiungere una delle righe seguenti al gruppo `dependencies` nel file build.gradle:

Aggiornare quindi le dipendenze progetto per fare in modo che i file binari vengano scaricati.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>In caso contrario...
Seguire le linee guida per installare manualmente Application Insights SDK per Java, scaricare il file jar (dopo aver raggiunto la pagina del repository centrale Maven, fare clic sul collegamento "jar" nella sezione di download) per l'appender appropriato e aggiungere il file jar dell'appender scaricato al progetto.

| Logger | Scarica | Libreria |
| --- | --- | --- |
| Logback |[Jar dell'appender logback](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v2.0 |[Jar dell'appender Log4J versione 2](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4J v1.2 |[Jar dell'appender Log4J versione 1.2](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


## <a name="add-the-appender-to-your-logging-framework"></a>Aggiungere l'appender per il framework di registrazione
Per iniziare la raccolta di tracce, unire il frammento di codice rilevante al file di configurazione Log4J o Logback: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

È possibile fare riferimento agli appender di Application Insights da qualsiasi logger configurato e non necessariamente dal logger principale (come illustrato negli esempi di codice riportati sopra).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Esplorare le tracce nel portale Application Insights.
Ora che il progetto è stato configurato per l'invio di tracce a Application Insights, è possibile visualizzare e cercare queste tracce nel portale di Application Insights, nel pannello [ricerca][diagnostic] .

Le eccezioni inviate tramite logger vengono visualizzate nel portale come telemetria delle eccezioni.

![Nel portale di Application Insights, aprire Ricerca diagnostica](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Passaggi successivi
[Ricerca diagnostica][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md



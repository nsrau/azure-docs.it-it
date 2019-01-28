---
title: Monitoraggio delle prestazioni per le app Web Java in Azure Application Insights | Documentazione Microsoft
description: Estendere il monitoraggio di prestazioni e utilizzo del sito Web Java con Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: mbullwin
ms.openlocfilehash: b7710b081668bf07d40718baf1d84314246861f5
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2019
ms.locfileid: "54412403"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Monitorare dipendenze e rilevare eccezioni e tempi di esecuzione del metodo nelle app Web Java


Se l'[app Web Java è stata instrumentata con Application Insights][java], sarà possibile usare l'agente Java per ottenere informazioni più dettagliate, senza modificare il codice:

* **Dipendenze:** dati sulle chiamate effettuate dall'applicazione ad altri componenti, tra cui:
  * **Chiamate REST** eseguite tramite HttpClient, OkHttp e RestTemplate (Spring).
  * **Chiamate Redis** effettuate tramite il client Jedis.
  * **[Chiamate JDBC](https://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)**: vengono acquisiti automaticamente i comandi MySQL, SQL Server e Oracle DB. Per MySQL, se la chiamata dura più di 10s, l'agente segnala il piano di query.
* **Eccezioni rilevate:** informazioni sulle eccezioni gestite dal codice.
* **Tempo di esecuzione del metodo:** informazioni sul tempo necessario per eseguire metodi specifici.

Per usare l'agente Java, installarlo nel server. Le app Web devono essere instrumentate con [Application Insights Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Installare l'agente di Application Insights per Java
1. [Scaricare l'agente](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest)sul computer che esegue il server Java. Assicurarsi di scaricare la stessa versione dell'agente Java e dei pacchetti core e Web dell'SDK per Java di Application Insights.
2. Modificare lo script di avvio del server applicazioni e aggiungere il codice JVM seguente:
   
    `javaagent:`*percorso completo del file JAR dell'agente*
   
    Ad esempio, in Tomcat su un computer Linux:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Riavviare il server applicazioni.

## <a name="configure-the-agent"></a>Configurare l'agente
Creare un file detto `AI-Agent.xml` e inserirlo nella stessa cartella che include il file JAR dell'agente.

Configurare il contenuto del file XML. Modificare l'esempio seguente in modo da includere o escludere le funzionalità desiderate.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

È necessario abilitare le eccezioni dei report e la durata del metodo per i singoli metodi.

Per impostazione predefinita, `reportExecutionTime` è true e `reportCaughtExceptions` è false.

### <a name="spring-boot-agent-additional-config"></a>Configurazioni aggiuntive dell'agente Spring Boot

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

> [!NOTE]
> AI-Agent.xml e il file agente JAR devono essere nella stessa cartella. Vengono spesso inseriti insieme nella cartella `/resources` del progetto. 

### <a name="spring-rest-template"></a>Modello Rest Spring

Affinché Application Insights possa instrumentare correttamente le chiamate HTTP eseguite con il modello Rest di Spring, è necessario usare il client HTTP Apache. Per impostazione predefinita il modello Rest di Spring non è configurato per usare il client HTTP Apache. Specificando [HttpComponentsClientHttpRequestfactory](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/client/HttpComponentsClientHttpRequestFactory.html) nel costruttore di un modello Rest Spring, verrà utilizzato HTTP Apache.

Ecco un esempio di come eseguire questa operazione con Spring Beans. Questo è un esempio molto semplice che usa le impostazioni predefinite della classe factory.

```java
@bean
public ClientHttpRequestFactory httpRequestFactory() {
return new HttpComponentsClientHttpRequestFactory()
}
@Bean(name = 'myRestTemplate')
public RestTemplate dcrAccessRestTemplate() {
    return new RestTemplate(httpRequestFactory())
}
```

#### <a name="enable-w3c-distributed-tracing"></a>Abilitare l'analisi distribuita W3C

Aggiungere il codice seguente al file AI-Agent.xml:

```xml
<Instrumentation>
        <BuiltIn enabled="true">
            <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
        </BuiltIn>
    </Instrumentation>
```

> [!NOTE]
> La modalità di compatibilità con le versioni precedenti è abilitata per impostazione predefinita. Il parametro enableW3CBackCompat è facoltativo e deve essere utilizzato solo quando si desidera disattivare tale funzionalità. 

Ad esempio, quando tutti i servizi sono stati aggiornati alla versione più recente degli SDK che supportano il protocollo W3C. Si raccomanda di passare alla versione più recente degli SDK con supporto W3C il prima possibile.

Assicurarsi che **entrambe le configurazioni [in ingresso](correlation.md#w3c-distributed-tracing) e in uscita (agente)** siano identiche.

## <a name="view-the-data"></a>Visualizzare i dati
Nella risorsa Application Insights vengono visualizzate le dipendenze remote aggregate e i tempi di esecuzione dei metodi [nel riquadro Prestazioni][metrics].

Per cercare singole istanze di dipendenze, eccezioni e report sui metodi, aprire [Ricerca][diagnostic].

[Diagnosi dei problemi di dipendenza - ulteriori informazioni](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Domande? Problemi?
* Dati non visualizzati [Impostare le eccezioni del firewall](../../azure-monitor/app/ip-addresses.md)
* [Risoluzione dei problemi Java](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md

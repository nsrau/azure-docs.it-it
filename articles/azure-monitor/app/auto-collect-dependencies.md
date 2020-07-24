---
title: Azure Application Insights - raccolta automatica delle dipendenze | Microsoft Docs
description: Application Insights raccoglie e visualizza automaticamente le dipendenze
ms.topic: reference
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/06/2020
ms.openlocfilehash: ba7830cc1c0a3e6a563b4343b37ec07f7d508af4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087986"
---
# <a name="dependency-auto-collection"></a>Raccolta automatica delle dipendenze

Di seguito è riportato l'elenco delle chiamate di dipendenza attualmente supportate che vengono rilevate automaticamente come dipendenze senza richiedere alcun'altra modifica al codice dell'applicazione. Queste dipendenze sono visualizzate nelle viste [Mappa delle applicazioni](./app-map.md) e [Diagnostica delle transazioni](./transaction-diagnostics.md) di Application Insights. Se la dipendenza non è presente nell'elenco seguente, è possibile comunque tenerne traccia manualmente con una [chiamata di dipendenza per il tracciamento](./api-custom-events-metrics.md#trackdependency).

## <a name="net"></a>.NET

| Framework per app| Versioni |
| ------------------------|----------|
| Web Form ASP.NET | 4.5+ |
| ASP.NET MVC | 4+ |
| WebAPI ASP.NET | 4.5+ |
| Risultato dell'azione di | 1.1+ |
| <b>Librerie di comunicazione</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5+, .NET Core 1.1+ |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0+, NuGet 4.3.0 |
| [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/1.1.2)| 1.1.0-versione stabile più recente. (Vedere la nota di seguito).
| [SDK Client di hub eventi](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [SDK Client di ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Client di archiviazione</b>|  |
| ADO.NET | 4.5+ |

> [!NOTE]
> Si è verificato un [problema noto](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1347) con le versioni precedenti di Microsoft. Data. SqlClient. Per attenuare questo problema, è consigliabile usare 1.1.0 o versione successiva. Entity Framework Core non è necessariamente fornito con la versione stabile più recente di Microsoft. Data. SqlClient, quindi è consigliabile confermare di avere almeno 1.1.0 per evitare questo problema.   


## <a name="java"></a>Java
| Server app | Versioni |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>Framework per app</b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9 +<sup>*</sup> |
| Servlet Java | 3.1+ |
| <b>Librerie di comunicazione</b> |  |
| [Client HTTP Apache](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3 +<sup>†</sup> |
| <b>Client di archiviazione</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1 +<sup>†</sup> |
| [PostgreSQL (supporto beta)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1 +<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1 +<sup>†</sup> |
| <b>Librerie di registrazione</b> | |
| [Logback](https://logback.qos.ch/) | 1+ |
| [Log4j](https://logging.apache.org/log4j/) | 1.2+ |
| <b>Librerie di metriche</b> |  |
| JMX | 1.0+ |

> [!NOTE]
> *Ad eccezione del supporto di programmazione reattiva.
> <br>†Richiede l'installazione dell'[agente JVM](./java-agent.md#install-the-application-insights-agent-for-java).

## <a name="nodejs"></a>Node.js

| Librerie di comunicazione | Versioni |
| ------------------------|----------|
| [http](https://nodejs.org/api/http.html), [https](https://nodejs.org/api/https.html) | 0.10+ |
| <b>Client di archiviazione</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb); [MongoDb Core](https://www.npmjs.com/package/mongodb-core) | 2. x-3. x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0-2.16. x |
| [PostgreSQL](https://www.npmjs.com/package/pg); | 6. x-7. x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1. x-2. x |
| <b>Librerie di registrazione</b> | |
| [Console](https://nodejs.org/api/console.html) | 0.10+ |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2. x-3. x |

## <a name="javascript"></a>JavaScript

| Librerie di comunicazione | Versioni |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Tutti |

## <a name="next-steps"></a>Passaggi successivi

- Impostare il rilevamento personalizzato delle dipendenze per [.NET](../../azure-monitor/app/asp-net-dependencies.md).
- Impostare il rilevamento personalizzato delle dipendenze per [Java](../../azure-monitor/app/java-agent.md).
- Configurare il rilevamento delle dipendenze personalizzato per [OpenCensus Python](../../azure-monitor/app/opencensus-python-dependency.md).
- [Scrivere dati di telemetria delle dipendenze personalizzate](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Per informazioni sul modello di dati e sui tipi di Application Insights, vedere il [modello di dati](../../azure-monitor/app/data-model.md).
- Verificare quali [piattaforme](../../azure-monitor/app/platforms.md) supportano Application Insights.

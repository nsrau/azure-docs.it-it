---
title: Configurare il monitoraggio delle prestazioni applicative per Java e gli strumenti di monitoraggio con il Servizio app di Azure in Linux
description: Informazioni su come inviare i log e le metriche per le applicazioni Java in esecuzione nel Servizio app di Azure in Linux nei provider del monitoraggio delle prestazioni applicative NewRelic e App Dynamics
services: app-service\web
author: rloutlaw
manager: angerobe
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 11/27/2018
ms.author: astay;routlaw
ms.openlocfilehash: 06ae71fea1b85a74d87588d2635038c64c8540cc
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52577104"
---
# <a name="how-to-application-performance-monitoring-tools-with-java-apps-on-azure-app-service-on-linux"></a>Procedura: Strumenti di monitoraggio delle prestazioni applicative con le app Java nel Servizio app di Azure in Linux

Questo articolo illustra come connettere le applicazioni Java distribuite nel Servizio app di Azure in Linux con le piattaforme di monitoraggio delle prestazioni applicative NewRelic e AppDynamics.

## <a name="configure-new-relic"></a>Configurare New Relic
1. Creare un account NewRelic in [NewRelic.com](https://newrelic.com/signup)
1. Scaricare l'agente Java da NewRelic; il nome sarà simile a `newrelic-java-x.x.x.zip`.
1. Copiare il codice di licenza, che sarà necessario per configurare l'agente in un secondo momento.
1. [Usare SSH per connettersi all'istanza del servizio app](/azure/app-service/containers/app-service-linux-ssh-support) e creare una nuova directory `/home/site/wwwroot/apm`. 
1. Caricare i file dell'agente Java NewRelic decompressi in una directory in `/home/site/wwwroot/apm`. I file per l'agente devono essere presenti in `/home/site/wwwroot/apm/newrelic`.
1. Modificare il file YAML in `/home/site/wwwroot/apm/newrelic/newrelic.yml` e sostituire il valore della licenza segnaposto con il proprio codice di licenza.
1. Nel portale di Azure passare all'applicazione nel servizio app e creare una nuova impostazione dell'applicazione.
    - Se l'app usa **Java SE**, creare una variabile di ambiente denominata `JAVA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se si usa **Tomcat**, creare una variabile di ambiente denominata `CATALINA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se si dispone già di una variabile di ambiente per `JAVA_OPTS` oppure `CATALINA_OPTS`, aggiungere l'opzione `javaagent` alla fine del valore corrente.

## <a name="configure-appdynamics"></a>Configurare AppDynamics
1. Creare un account AppDynamics in [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Scaricare l'agente Java dal sito Web di AppDynamics, il nome file sarà simile a `AppServerAgent-x.x.x.xxxxx.zip`
1. [Usare SSH per connettersi all'istanza del servizio app](/azure/app-service/containers/app-service-linux-ssh-support) e creare una nuova directory `/home/site/wwwroot/apm`. 
1. Caricare i file dell'agente Java in una directory in `/home/site/wwwroot/apm`. I file per l'agente devono essere presenti in `/home/site/wwwroot/apm/appdynamics`.
1. Nel portale di Azure passare all'applicazione nel servizio app e creare una nuova impostazione dell'applicazione.
    - Se si usa **Java SE**, creare una variabile di ambiente denominata `JAVA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` in cui `<YOUR_SITE_NAME>` è il nome del servizio app.
    - Se si usa **Tomcat**, creare una variabile di ambiente denominata `CATALINA_OPTS` con il valore `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` in cui `<YOUR_SITE_NAME>` è il nome del servizio app.

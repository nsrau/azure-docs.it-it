---
title: Trasmettere i log dell'app Azure Spring Cloud in tempo reale
description: Come utilizzare lo streaming di log per visualizzare istantaneamente i log delle applicazioni
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: fc208a3542528fb4554a365a02e13c2da3055cf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192201"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Trasmettere i log dell'app Azure Spring Cloud in tempo reale
Azure Spring Cloud consente lo streaming dei log nell'interfaccia della riga di comando di Azure per ottenere i log della console dell'applicazione in tempo reale per la risoluzione dei problemi. È inoltre possibile [analizzare i log e le metriche con le impostazioni](./diagnostic-services.md)di diagnostica .

## <a name="prerequisites"></a>Prerequisiti

* Installare [l'estensione CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) di Azure per Spring Cloud, versione minima 0.2.0.
* Un'istanza di **Azure Spring Cloud** con un'applicazione in esecuzione, ad esempio [l'app Spring Cloud.](./spring-cloud-quickstart-launch-app-cli.md)

> [!NOTE]
>  L'estensione DELL'interfaccia della riga di comando ASC viene aggiornata dalla versione 0.2.0 a 0.2.1.The ASC CLI extension is updated from version 0.2.0 to 0.2.1. Questa modifica influisce sulla sintassi del `az spring-cloud app log tail`comando per lo `az spring-cloud app logs`streaming dei log: , che viene sostituito da: . Il comando: `az spring-cloud app log tail` sarà deprecato in una versione futura. Se è stata usata la versione 0.2.0, è possibile eseguire l'aggiornamento a 0.2.1. In primo luogo, rimuovere la `az extension remove -n spring-cloud`versione precedente con il comando: .  Quindi, installare 0.2.1 con `az extension add -n spring-cloud`il comando: .

## <a name="use-cli-to-tail-logs"></a>Usare l'interfaccia della riga di comando per eseguire la coda dei logUse CLI to

Per evitare di specificare ripetutamente il nome del gruppo di risorse e dell'istanza del servizio, impostare il nome del gruppo di risorse predefinito e il nome del cluster.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
Negli esempi seguenti, il gruppo di risorse e il nome del servizio verranno omessi nei comandi.

### <a name="tail-log-for-app-with-single-instance"></a>Tail log per app con istanza singola
Se un'app denominata auth-service ha una sola istanza, è possibile visualizzare il log dell'istanza dell'app con il comando seguente:
```
az spring-cloud app logs -n auth-service
```
In questo modo verranno restituiti i registri:
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Tail log per app con più istanze
Se esistono più istanze `auth-service`per l'app denominata `-i/--instance` , è possibile visualizzare il log dell'istanza utilizzando l'opzione . 

In primo luogo, è possibile ottenere i nomi delle istanze dell'app con il comando seguente.

```
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
Con i risultati:

```
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
Quindi, puoi trasmettere i log di `-i/--instance` un'istanza dell'app con l'opzione:

```
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

È anche possibile ottenere informazioni dettagliate sulle istanze dell'app dal portale di Azure.You can also get details of app instances from the Azure portal.  Dopo aver selezionato **App** nel riquadro di spostamento sinistro del servizio Azure Spring Cloud, selezionare **Istanze dell'app.**

### <a name="continuously-stream-new-logs"></a>Flusso continuo di nuovi log
Per impostazione predefinita, `az spring-cloud ap log tail` stampa solo i log esistenti trasmessi alla console dell'app e quindi vengono chiusi. Se si desidera trasmettere nuovi log, aggiungere -f (--seguire):  

```
az spring-cloud app logs -n auth-service -f
``` 
Per controllare tutte le opzioni di registrazione supportate:
``` 
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>Passaggi successivi

* [Analizzare i log e le metriche con le impostazioni di diagnosticaAnalyze logs and metrics with diagnostics settings](./diagnostic-services.md)

 






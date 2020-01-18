---
title: Trasmetti i log delle app di Azure Spring cloud in tempo reale
description: Come usare lo streaming dei log per visualizzare immediatamente i registri applicazioni
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: 27978d367ded7a31d73949cd675ae9e6f8cb887c
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264000"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Trasmetti i log delle app di Azure Spring cloud in tempo reale
Azure Spring cloud consente lo streaming di log nell'interfaccia della riga di comando di Azure per ottenere i log della console applicazione in tempo reale per la risoluzione dei problemi È anche possibile [analizzare i log e le metriche con le impostazioni di diagnostica](./diagnostic-services.md).

## <a name="prerequisites"></a>Prerequisiti

* Installare l' [estensione dell'interfaccia](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) della riga di comando di Azure per Spring cloud, versione minima 0.2.0.
* Un'istanza del **cloud Spring di Azure** con un'applicazione in esecuzione, ad esempio [Spring cloud app](./spring-cloud-quickstart-launch-app-cli.md).

## <a name="use-cli-to-tail-logs"></a>Usare l'interfaccia della riga di comando per la coda

Per evitare di specificare ripetutamente il nome del gruppo di risorse e dell'istanza del servizio, impostare il nome del gruppo di risorse e il nome del cluster predefiniti.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
Negli esempi seguenti, il gruppo di risorse e il nome del servizio verranno omessi nei comandi.

### <a name="tail-log-for-app-with-single-instance"></a>Log della parte finale per l'app con una singola istanza
Se un'app denominata auth-Service ha solo un'istanza, è possibile visualizzare il log dell'istanza dell'app con il comando seguente:
```
az spring-cloud app log tail -n auth-service
```
Questo restituirà i log:
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Log della parte finale per l'app con più istanze
Se esistono più istanze per l'app denominata `auth-service`, è possibile visualizzare il log dell'istanza usando l'opzione `-i/--instance`. 

Per prima cosa, è possibile ottenere i nomi delle istanze dell'app con il comando seguente.

```
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
Con risultati:

```
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
Quindi, è possibile eseguire lo streaming dei log di un'istanza dell'app con l'opzione `-i/--instance` opzione:

```
az spring-cloud app log tail -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

È anche possibile ottenere i dettagli delle istanze dell'app dal portale di Azure.  Dopo aver selezionato le **app** nel riquadro di spostamento a sinistra del servizio cloud di Azure Spring, selezionare **istanze dell'app**.

### <a name="continuously-stream-new-logs"></a>Flusso continuo di nuovi log
Per impostazione predefinita, `az spring-cloud ap log tail` stampa solo i log esistenti trasmessi alla console dell'app e quindi viene chiuso. Se si vuole eseguire lo streaming di nuovi log, aggiungere-f (--follow):  

```
az spring-cloud app log tail -n auth-service -f
``` 
Per controllare tutte le opzioni di registrazione supportate:
``` 
az spring-cloud app log tail -h 
```

## <a name="next-steps"></a>Passaggi successivi

* [Analizzare i log e le metriche con le impostazioni di diagnostica](./diagnostic-services.md)

 






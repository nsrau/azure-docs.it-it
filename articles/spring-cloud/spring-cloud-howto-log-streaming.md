---
title: Trasmetti i log delle app di Azure Spring cloud in tempo reale
description: Come usare lo streaming dei log per visualizzare immediatamente i registri applicazioni
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: fa2e7af51ff681da0bfdac928cc08bf75126a3b8
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156421"
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
Se esistono più istanze per l'app denominata `auth-service`, è possibile visualizzare il log dell'istanza usando l'opzione `-i/--instance`. È ad esempio possibile trasmettere in streaming il log di un'istanza di un'app specificando il nome dell'app e il nome dell'istanza:

```
az spring-cloud app log tail -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```
È anche possibile ottenere le istanze dell'app dal portale di Azure. 
1. Passare al gruppo di risorse e selezionare l'istanza di Azure Spring cloud.
1. Nella panoramica dell'istanza di Azure Spring cloud selezionare **app** nel riquadro di spostamento a sinistra.
1. Selezionare l'app e quindi fare clic su **istanze app** nel riquadro di spostamento a sinistra. 
1. Verranno visualizzate le istanze dell'app.

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

 






---
title: Usare la traccia distribuita con Azure Spring Cloud
description: Informazioni su come usare la traccia distribuita di Spring Cloud tramite Azure Application Insights
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: ccaf58465c1ade0228daea2b535d06fb6168d64f
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142123"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Usare la traccia distribuita con Azure Spring Cloud

Gli strumenti di traccia distribuita in Azure Spring Cloud consentono di eseguire facilmente il debug e il monitoraggio di problemi complessi. Azure Spring Cloud integra [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) con [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) di Azure. Questa integrazione fornisce funzionalità di traccia distribuita avanzate dal portale di Azure.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Abilitare Traccia distribuita nel portale di Azure.
> * Aggiungere Spring Cloud Sleuth all'applicazione.
> * Visualizzare le mappe di dipendenze per le applicazioni di microservizi.
> * Eseguire ricerche nei dati di traccia con filtri diversi.

## <a name="prerequisites"></a>Prerequisiti

Per seguire queste procedure, è necessario un servizio Azure Spring Cloud di cui sia già stato effettuato il provisioning e che sia in esecuzione. Completare la guida di [avvio rapido sulla distribuzione di un'app tramite l'interfaccia della riga di comando di Azure](spring-cloud-quickstart-launch-app-cli.md) per effettuare il provisioning ed eseguire un servizio Azure Spring Cloud.
    
## <a name="add-dependencies"></a>Aggiungere le dipendenze

1. Aggiungere la riga seguente nel file application.properties:

   ```xml
   spring.zipkin.sender.type = web
   ```

   Dopo questa modifica, il mittente Zipkin può inviare al Web.

1. Se è stata seguita la [guida alla preparazione dell'applicazione Azure Spring Cloud](spring-cloud-tutorial-prepare-app-deployment.md), è possibile ignorare questo passaggio. In caso contrario, passare all'ambiente di sviluppo locale e modificare il file pom.xml per includere la dipendenza Spring Cloud Sleuth seguente:

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-sleuth</artifactId>
                <version>${spring-cloud-sleuth.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-sleuth</artifactId>
        </dependency>
    </dependencies>
    ```

1. Ripetere la compilazione e la distribuzione per il servizio Azure Spring Cloud in modo da riflettere queste modifiche.

## <a name="modify-the-sample-rate"></a>Modificare la frequenza di campionamento

È possibile cambiare la frequenza con cui vengono raccolti i dati di telemetria modificando la frequenza di campionamento. Se ad esempio si vuole dimezzare la frequenza di campionamento, aprire il file application.properties e modificare la riga seguente:

```xml
spring.sleuth.sampler.probability=0.5
```

Se è già stata compilata e distribuita un'applicazione, è possibile modificare la frequenza di campionamento. A tale scopo, aggiungere la riga precedente come variabile di ambiente nell'interfaccia della riga di comando di Azure o nel portale di Azure.

## <a name="enable-application-insights"></a>Abilitare Application Insights

1. Passare alla pagina del servizio Azure Spring Cloud nel portale di Azure.
1. Nella pagina **Monitoraggio** selezionare **Traccia distribuita**.
1. Selezionare **Modifica l'impostazione** per modificare o aggiungere una nuova impostazione.
1. Creare una nuova query di Application Insights o selezionarne una esistente.
1. Scegliere quale categoria di registrazione si vuole monitorare e specificare il periodo di conservazione in giorni.
1. Selezionare **Applica** per applicare la nuova traccia.

## <a name="view-the-application-map"></a>Visualizzare la mappa delle applicazioni

Tornare nella pagina **Traccia distribuita** e selezionare **View application map** (Visualizza mappa delle applicazioni). Esaminare la rappresentazione visiva dell'applicazione e le impostazioni di monitoraggio. Per informazioni su come usare la mappa delle applicazioni, vedere [Mappa delle applicazioni: Valutazione delle applicazioni distribuite](https://docs.microsoft.com/azure/azure-monitor/app/app-map).

## <a name="use-search"></a>Usare la ricerca

Usare la funzione di ricerca per eseguire query su altri dati di telemetria specifici. Nella pagina **Traccia distribuita** selezionare **Cerca**. Per altre informazioni su come usare la funzione di ricerca, vedere [Utilizzo della funzionalità Ricerca in Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search).

## <a name="use-application-insights"></a>Usare Application Insights

Application Insights fornisce funzionalità di monitoraggio oltre alla mappa delle applicazioni e alla funzione di ricerca. Cercare il nome dell'applicazione nel portale di Azure e quindi aprire una pagina di Application Insights per trovare le informazioni di monitoraggio. Per altre indicazioni su come usare questi strumenti, vedere [Query su log di Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

## <a name="disable-application-insights"></a>Disabilitare Application Insights

1. Passare alla pagina del servizio Azure Spring Cloud nel portale di Azure.
1. In **Monitoraggio** selezionare **Traccia distribuita**.
1. Selezionare **Disabilita** per disabilitare Application Insights.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come abilitare e interpretare la traccia distribuita in Azure Spring Cloud. Per informazioni sul binding di servizi a un'applicazione, vedere [Associare un database Azure Cosmos DB a un'applicazione Azure Spring Cloud](spring-cloud-tutorial-bind-cosmos.md).

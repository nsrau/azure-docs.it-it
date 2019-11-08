---
title: 'Esercitazione: Usare Traccia distribuita con Azure Spring Cloud | Microsoft Docs'
description: Informazioni su come usare Traccia distribuita di Spring Cloud tramite Azure Application Insights
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: a9d2100103cdd5858d0d58cf6ef77a6ccac3745f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607557"
---
# <a name="tutorial-using-distributed-tracing-with-azure-spring-cloud"></a>Esercitazione: Uso di Traccia distribuita con Azure Spring Cloud

Gli strumenti Traccia distribuita di Spring Cloud facilitano il debug e il monitoraggio di problemi complessi. Azure Spring Cloud integra [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) con Azure [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) per offrire potenti funzionalità di traccia distribuita dal portale di Azure.

In questo articolo verrà spiegato come:

> [!div class="checklist"]
> * Abilitare Traccia distribuita nel portale di Azure
> * Aggiungere Spring Cloud Sleuth all'applicazione
> * Visualizzare le mappe di dipendenze per le applicazioni di microservizi
> * Avviare la traccia dei dati con filtri diversi

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

* Un servizio Azure Spring Cloud in esecuzione e di cui è già stato effettuato il provisioning.  Per effettuare il provisioning e avviare il servizio Azure Spring Cloud, completare questo [argomento di avvio rapido](spring-cloud-quickstart-launch-app-cli.md).
    
## <a name="add-dependencies"></a>Aggiungere le dipendenze

Abilitare il mittente zipkin per l'invio nel Web aggiungendo la riga seguente nel file application.properties:

```xml
spring.zipkin.sender.type = web
```

Se è stata seguita la [guida alla preparazione dell'applicazione Azure Spring Cloud](spring-cloud-tutorial-prepare-app-deployment.md), è possibile ignorare il passaggio seguente. In caso contrario, passare all'ambiente di sviluppo locale e modificare il file `pom.xml` per includere la dipendenza Spring Cloud Sleuth:

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

* Ripetere la compilazione e la distribuzione per il servizio Azure Spring Cloud in modo da riflettere queste modifiche. 

## <a name="modify-the-sample-rate"></a>Modificare la frequenza di campionamento
È possibile cambiare la frequenza con cui vengono raccolti i dati di telemetria modificando la frequenza di campionamento. Se ad esempio si vuole dimezzare la frequenza di campionamento, aprire il file `application.properties` e cambiare la riga seguente:

```xml
spring.sleuth.sampler.probability=0.5
```

Se è già stata compilata e distribuita un'applicazione, è possibile modificare la frequenza di campionamento aggiungendo la riga precedente come variabile di ambiente nell'interfaccia della riga di comando o nel portale di Azure. 

## <a name="enable-application-insights"></a>Abilitare Application Insights

1. Passare alla pagina del servizio Azure Spring Cloud nel portale di Azure.
1. Nella sezione Monitoraggio selezionare **Traccia distribuita**.
1. Selezionare **Modifica l'impostazione** per modificare o aggiungere una nuova impostazione.
1. Creare una nuova query di Application Insights o selezionarne una esistente.
1. Scegliere quale categoria di registrazione si vuole monitorare e specificare il periodo di conservazione (in giorni).
1. Selezionare **Applica** per applicare la nuova traccia.

## <a name="view-application-map"></a>Visualizzare la mappa delle applicazioni

Tornare nella pagina Traccia distribuita e selezionare **View application map** (Visualizza mappa delle applicazioni). Esaminare la rappresentazione visiva dell'applicazione e le impostazioni di monitoraggio. Per informazioni su come usare la mappa delle applicazioni, vedere [questo articolo](https://docs.microsoft.com/azure/azure-monitor/app/app-map).

## <a name="search"></a>Ricerca

Usare la funzione di ricerca per eseguire query su altri dati di telemetria specifici. Nella pagina **Traccia distribuita** selezionare **Cerca**. Per altre informazioni su come usare la funzionalità di ricerca, vedere [questo articolo](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search).

## <a name="application-insights-page"></a>Pagina Application Insights

Application Insights fornisce funzionalità di monitoraggio oltre alla mappa delle applicazioni e alla ricerca. Cercare il nome dell'applicazione nel portale di Azure e quindi avviare una pagina di Application Insights per altre informazioni. Per altre indicazioni su come usare questi strumenti, [vedere la documentazione](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).


## <a name="disable-application-insights"></a>Disabilitare Application Insights

1. Passare alla pagina del servizio Azure Spring Cloud nel portale di Azure.
1. Nella sezione Monitoraggio fare clic su **Traccia distribuita**.
1. Fare clic su **Disabilita** per disabilitare Application Insights

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come abilitare e interpretare la traccia distribuita in Azure Spring Cloud. Per informazioni su come associare l'applicazione a un'istanza di Azure CosmosDB, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Informazioni su come associare l'applicazione a un'istanza di Azure CosmosDB](spring-cloud-tutorial-bind-cosmos.md).

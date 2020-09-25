---
title: 'Esercitazione: Usare Circuit Breaker Dashboard con Azure Spring Cloud'
description: Informazioni su come usare Circuit Breaker Dashboard con Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: devx-track-java
ms.openlocfilehash: fa66f17c6f96ac7f70188c5a28c0b180ed2f03e0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906889"
---
# <a name="use-circuit-breaker-dashboard-with-azure-spring-cloud"></a>Usare Circuit Breaker Dashboard con Azure Spring Cloud

**Questo articolo si applica a:** ✔️ Java

::: zone pivot="programming-language-java"
[Spring Cloud Netflix Turbine](https://github.com/Netflix/Turbine) è ampiamente usato per aggregare più flussi di metriche [Hystrix](https://github.com/Netflix/Hystrix) in modo da poter monitorare i flussi in un'unica vista usando il dashboard di Hystrix. Questa esercitazione illustra come usarli in Azure Spring Cloud.
> [!NOTE]
> Netflix Hystrix è ampiamente usato in molte app Spring Cloud esistenti, ma non è più in fase di sviluppo attivo. Se si sta sviluppando un nuovo progetto, usare al suo posto implementazioni di Spring Cloud Circuit Breaker come [resilience4j](https://github.com/resilience4j/resilience4j). A differenza del framework Turbine illustrato in questa esercitazione, il nuovo framework Spring Cloud Circuit Breaker unifica tutte le implementazioni della pipeline di dati di metriche in Micrometer. Il supporto di Micrometer in Azure Spring Cloud è ancora in fase di elaborazione, quindi non verrà trattato in questa esercitazione.

## <a name="prepare-your-sample-applications"></a>Preparare le applicazioni di esempio
L'esempio è basato su una copia tramite fork da questo [repository](https://github.com/StackAbuse/spring-cloud/tree/master/spring-turbine).

Clonare il repository di esempio nell'ambiente di sviluppo:
```
git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
cd Azure-Spring-Cloud-Samples/hystrix-turbine-sample
```

Compilare le tre applicazioni che verranno usate in questa esercitazione:
* user-service: un servizio REST semplice con un singolo endpoint di /personalized/{id}
* recommendation-service: un servizio REST semplice con un singolo endpoint di /recommendations, che verrà chiamato da user-service.
* hystrix-turbine: un servizio dashboard di Hystrix per visualizzare i flussi Hystrix e un servizio Turbine che aggrega il flusso di metriche Hystrix da altri servizi.
```
mvn clean package -D skipTests -f user-service/pom.xml
mvn clean package -D skipTests -f recommendation-service/pom.xml
mvn clean package -D skipTests -f hystrix-turbine/pom.xml
```
## <a name="provision-your-azure-spring-cloud-instance"></a>Effettuare il provisioning dell'istanza di Azure Spring Cloud
Seguire la procedura [Effettuare il provisioning di un'istanza del servizio nell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#provision-a-service-instance-on-the-azure-cli).

## <a name="deploy-your-applications-to-azure-spring-cloud"></a>Distribuire le applicazioni in Azure Spring Cloud
Queste app non usano il **server di configurazione**, pertanto non è necessario configurare il **server di configurazione** per Azure Spring Cloud.  Creare e distribuire le applicazioni nel modo seguente:
```azurecli
az spring-cloud app create -n user-service --is-public
az spring-cloud app create -n recommendation-service
az spring-cloud app create -n hystrix-turbine --is-public

az spring-cloud app deploy -n user-service --jar-path user-service/target/user-service.jar
az spring-cloud app deploy -n recommendation-service --jar-path recommendation-service/target/recommendation-service.jar
az spring-cloud app deploy -n hystrix-turbine --jar-path hystrix-turbine/target/hystrix-turbine.jar
```
## <a name="verify-your-apps"></a>Verificare le app
Una volta che tutte le app sono in esecuzione e individuabili, accedere a `user-service` con il percorso https://<username>-user-service.azuremicroservices.io/personalized/1 dal browser. Se user-service è in grado di accedere a `recommendation-service`, dovrebbe essere visualizzato l'output seguente. Se non funziona, aggiornare la pagina Web alcune volte.
```json
[{"name":"Product1","description":"Description1","detailsLink":"link1"},{"name":"Product2","description":"Description2","detailsLink":"link3"},{"name":"Product3","description":"Description3","detailsLink":"link3"}]
```
## <a name="access-your-hystrix-dashboard-and-metrics-stream"></a>Accedere al dashboard di Hystrix e al flusso di metriche
Eseguire la verifica usando endpoint pubblici o endpoint di test privati.

### <a name="using-public-endpoints"></a>Uso di endpoint pubblici
Accedere a hystrix-turbine con il percorso `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/hystrix` dal browser.  La figura seguente mostra il dashboard di Hystrix in esecuzione nell'app.

![Dashboard di Hystrix](media/spring-cloud-circuit-breaker/hystrix-dashboard.png)

Copiare l'URL del flusso di Turbine `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/turbine.stream?cluster=default` nella casella di testo e fare clic su **Monitor Stream** (Esegui monitoraggio del flusso).  Verrà visualizzato il dashboard. Se nel visualizzatore non viene visualizzato nulla, fare clic sugli endpoint di `user-service` per generare i flussi.

![Flusso Hystrix](media/spring-cloud-circuit-breaker/hystrix-stream.png) A questo punto è possibile sperimentare Circuit Breaker Dashboard.
> [!NOTE] 
> In ambiente di produzione, il dashboard di Hystrix e il flusso di metriche non devono essere esposti a Internet.

### <a name="using-private-test-endpoints"></a>Uso di endpoint di test privati
I flussi di metriche Hystrix sono accessibili anche da `test-endpoint`. Come servizio back-end, non è stato assegnato un endpoint pubblico per `recommendation-service`, ma è possibile visualizzarne le metriche con test-endpoint all'indirizzo `https://primary:<KEY>@<SERVICE-NAME>.test.azuremicroservices.io/recommendation-service/default/actuator/hystrix.stream`.

![Flusso di endpoint di test Hystrix](media/spring-cloud-circuit-breaker/hystrix-test-endpoint-stream.png)

Come app Web, il dashboard di Hystrix dovrebbe funzionare su `test-endpoint`. Se non funziona correttamente, i motivi possono essere due: l'uso di `test-endpoint` può aver cambiato l'URL di base da `/ to /<APP-NAME>/<DEPLOYMENT-NAME>` oppure l'app Web potrebbe usare un percorso assoluto per una risorsa statica. Per consentire il funzionamento su `test-endpoint`, potrebbe essere necessario modificare manualmente <base> nei file front-end.

## <a name="next-steps"></a>Passaggi successivi
* [Effettuare il provisioning di un'istanza del servizio nell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#provision-a-service-instance-on-the-azure-cli)
* [Preparare un'applicazione Java Spring per la distribuzione in Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment)
::: zone-end

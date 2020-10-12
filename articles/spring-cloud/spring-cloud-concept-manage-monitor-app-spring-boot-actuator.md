---
title: Gestire e monitorare l'app con Azure Spring Boot Actuator
description: Informazioni su come gestire e monitorare l'app con l'attuatore Spring boot.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 93fd286aa76a0409a515abbf8c9dabd88a9a65c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904278"
---
# <a name="manage-and-monitor-app-with-azure-spring-boot-actuator"></a>Gestire e monitorare l'app con Azure Spring Boot Actuator

**Questo articolo si applica a:** ✔️ Java

Dopo la distribuzione di un nuovo file binario nell'app, è possibile controllare la funzionalità e visualizzare le informazioni sull'applicazione in esecuzione. Questo articolo illustra come accedere all'API da un endpoint di test fornito da Azure Spring cloud ed esporre le funzionalità pronte per la produzione per l'app.

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che si disponga di un'applicazione Spring Boot 2. x che può essere distribuita e avviata correttamente nel servizio cloud Spring di Azure.  Vedere [Guida introduttiva: avviare un'applicazione Azure Spring cloud esistente usando il portale di Azure](spring-cloud-quickstart.md)

## <a name="verify-app-through-test-endpoint"></a>Verifica dell'app tramite endpoint di test
1. Passare al **Dashboard dell'applicazione** e fare clic sull'app per accedere alla pagina Panoramica dell'app.

1. Nel riquadro **Panoramica** verrà visualizzato **endpoint di test**.  Accedere a questo endpoint dalla riga di comando o dal browser e osservare la risposta dell'API.

1. Si noti l'URI dell' **endpoint di test** che verrà usato nella sezione in arrivo.

>[!TIP]
> * Se l'app restituisce una pagina front-end e fa riferimento ad altri file tramite il percorso relativo, verificare che l'endpoint di test termini con una barra (/). In questo modo il file CSS verrà caricato correttamente.
> * Se si visualizza l'API da un sopracciglio e il browser richiede l'immissione delle credenziali di accesso per visualizzare la pagina, usare la [decodifica URL](https://www.urldecoder.org/) per decodificare l'endpoint di test. La decodifica URL restituisce un URL nel formato "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/ \<app-name> / \<deployment-name> ".  Usare questo modulo per accedere all'endpoint.

## <a name="add-actuator-dependency"></a>Aggiungi dipendenza attuatore

Per aggiungere l'attuatore a un progetto basato su Maven, aggiungere la dipendenza ' Starter ':

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

Compilare il nuovo file binario e distribuirlo nell'app.

## <a name="enable-production-ready-features"></a>Abilita le funzionalità pronte per la produzione
Gli endpoint dell'attuatore consentono di monitorare e interagire con l'applicazione. Per impostazione predefinita, l'applicazione Spring boot espone gli `health` `info` endpoint e per visualizzare informazioni sull'applicazione e informazioni di integrità arbitrarie.

Per osservare la configurazione e l'ambiente configurabile, è necessario `env` abilitare `configgrops` anche gli endpoint e.

1. Passare al riquadro **Panoramica** app, fare clic su **configurazione** nel menu impostazione, passare alla pagina di configurazione delle **variabili di ambiente** .
1. Aggiungere le proprietà seguenti come nel form "chiave: valore". In questo ambiente verrà aperto l'endpoint dell'attuatore della molla "ENV", "Health", "info".

   ```
   management.endpoints.web.exposure.include: env,health,info
   ```
1. Fare clic sul pulsante **Salva** . l'applicazione verrà riavviata automaticamente e caricherà le nuove variabili di ambiente.

È ora possibile tornare al riquadro Panoramica app e attendere che lo stato del provisioning venga modificato in "succeeded".  Ci sarà più di un'istanza in esecuzione.

> [!Note] 
> Quando si espone l'app a Public, questi endpoint dell'attuatore sono esposti anche a public. È possibile nascondere tutti gli endpoint eliminando le variabili di ambiente `management.endpoints.web.exposure.include` e impostando `management.endpoints.web.exposure.exclude=*`

## <a name="view-the-actuator-endpoint-to-view-application-information"></a>Visualizzare l'endpoint dell'attuatore per visualizzare le informazioni sull'applicazione
1. È ora possibile accedere all'URL `"<test-endpoint>/actuator/"` per visualizzare tutti gli endpoint esposti dall'attuatore Spring boot.
1. URL `"<test-endpoint>/actuator/env"` di accesso, è possibile visualizzare i profili attivi usati dall'app e tutte le variabili di ambiente caricate.
1. Se si desidera eseguire la ricerca in un ambiente specifico, è possibile accedere  `"<test-endpoint>/actuator/env/{toMatch}"` all'URL per visualizzarlo.

Per visualizzare tutti gli endpoint predefiniti, vedere [esposizione degli endpoint](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-endpoints-exposing-endpoints)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulle metriche per il cloud Spring di Azure](spring-cloud-concept-metrics.md)
* [Informazioni sullo stato delle app in Azure Spring Cloud](spring-cloud-concept-app-status.md)


---
title: Automatizzare il registro del servizio e l'individuazione
description: Informazioni su come automatizzare l'individuazione e la registrazione del servizio usando il registro di sistema del servizio cloud Spring
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 1e60799878cc30b729344c03df36a4c5e4f4a199
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904207"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Individuare e registrare i servizi cloud Spring

L'individuazione del servizio è un requisito fondamentale per un'architettura basata su microservizi.  La configurazione manuale di ogni client richiede tempo e introduce la possibilità di errore umano.  Il registro del servizio cloud Spring di Azure risolve questo problema.  Una volta configurata, un server del registro dei Servizi controllerà la registrazione e l'individuazione del servizio per i microservizi dell'applicazione. Il server del registro di sistema del servizio gestisce un registro dei microservizi distribuiti, Abilita il bilanciamento del carico lato client e separa i provider di servizi dai client senza basarsi su DNS.

::: zone pivot="programming-language-csharp"
Per informazioni su come configurare la registrazione del servizio per un'app Steeltoe, vedere [preparare un'applicazione Java Spring per la distribuzione nel cloud Spring di Azure](spring-cloud-tutorial-prepare-app-deployment.md).
::: zone-end
::: zone pivot="programming-language-java"

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Registrare l'applicazione usando il registro di sistema del servizio cloud Spring

Prima che l'applicazione possa gestire la registrazione e l'individuazione dei servizi usando il registro di sistema del servizio cloud Spring, è necessario includere diverse dipendenze nel file di *pom.xml* dell'applicazione.
Includere le dipendenze per *Spring-cloud-Starter-Netflix-Eureka-client* e *Spring-cloud-Starter-Azure-Spring-cloud-client* per la *pom.xml*

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>Aggiornare la classe di primo livello

Infine, si aggiunge un'annotazione alla classe di primo livello dell'applicazione

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

L'endpoint del server del registro di sistema Spring Cloud Service verrà inserito come variabile di ambiente nell'applicazione.  I microservizi saranno ora in grado di registrarsi con il server del registro di sistema del servizio e di individuare altri microservizi dipendenti.

Si noti che potrebbero essere necessari alcuni minuti prima che le modifiche vengano propagate dal server a tutti i microservizi.
::: zone-end

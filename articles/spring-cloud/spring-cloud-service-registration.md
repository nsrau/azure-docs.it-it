---
title: Automatizzare il registro del servizio e l'individuazione
description: Informazioni su come automatizzare l'individuazione e la registrazione del servizio usando il registro di sistema del servizio cloud Spring
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: c3e26b157630df6004292c93a0a0a47307d5949a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87071011"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Individuare e registrare i servizi cloud Spring

L'individuazione del servizio è un requisito fondamentale per un'architettura basata su microservizi.  La configurazione manuale di ogni client richiede tempo e introduce la possibilità di errore umano.  Il registro del servizio cloud Spring di Azure risolve questo problema.  Una volta configurata, un server del registro dei Servizi controllerà la registrazione e l'individuazione del servizio per i microservizi dell'applicazione. Il server del registro di sistema del servizio gestisce un registro dei microservizi distribuiti, Abilita il bilanciamento del carico lato client e separa i provider di servizi dai client senza basarsi su DNS.

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

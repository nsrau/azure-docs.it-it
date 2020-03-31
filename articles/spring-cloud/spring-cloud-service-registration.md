---
title: Automatizzare il Registro di sistema e l'individuazione del servizioAutomate service registry and
description: Scopri come automatizzare l'individuazione e la registrazione dei servizi utilizzando Spring Cloud Service Registry
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: brendm
ms.openlocfilehash: 6c217096f0ba4200f49bd1fd8056768a6f6f6dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278849"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Scopri e registra i tuoi servizi Spring Cloud

L'individuazione dei servizi è un requisito fondamentale per un'architettura basata su microservizi.  La configurazione manuale di ogni client richiede tempo e introduce la possibilità di errore umano.  Azure Spring Cloud Service Registry risolve questo problema.  Una volta configurato, un server del Registro di sistema del servizio controllerà la registrazione e l'individuazione dei servizi per i microservizi dell'applicazione. Il server del Registro di sistema del servizio gestisce un registro dei microservizi distribuiti, abilita il bilanciamento del carico sul lato client e separa i provider di servizi dai client senza fare affidamento sul DNS.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Registrare l'applicazione utilizzando Spring Cloud Service Registry

Prima che l'applicazione possa gestire la registrazione e l'individuazione del servizio tramite Spring Cloud Service Registry, diverse dipendenze devono essere incluse nel file *pom.xml* dell'applicazione.
Includi le dipendenze per *spring-cloud-starter-netflix-eureka-client* e *spring-cloud-starter-azure-spring-cloud-client* al tuo *pom.xml*

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

Infine, aggiungiamo un'annotazione alla classe di primo livello dell'applicazione

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

L'endpoint del server Spring Cloud Service Registry verrà inserito come variabile di ambiente nell'applicazione.  I microservizi saranno ora in grado di registrarsi con il server del Registro di sistema del servizio e di individuare altri microservizi dipendenti.

Si noti che la propagazione delle modifiche dal server a tutti i microservizi può richiedere alcuni minuti.

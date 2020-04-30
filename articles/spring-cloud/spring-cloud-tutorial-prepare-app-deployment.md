---
title: "Procedura: preparare un'applicazione Java Spring per la distribuzione nel cloud Spring di Azure"
description: In questo argomento si prepara un'applicazione Java Spring per la distribuzione nel cloud Spring di Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 16cee333d52765755b732c4de4dd8a6e092a130d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81731171"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Preparare un'applicazione Java Spring per la distribuzione in Azure Spring Cloud

Questo argomento illustra come preparare un'applicazione Java Spring esistente per la distribuzione nel cloud Spring di Azure. Se correttamente configurato, Azure Spring Cloud offre servizi affidabili per il monitoraggio, il ridimensionamento e l'aggiornamento dell'applicazione Java Spring Cloud.

Altri esempi illustrano come distribuire un'applicazione in Azure Spring Cloud quando viene configurato il file POM. 
* [Avviare l'app usando il portale di Azure](spring-cloud-quickstart-launch-app-portal.md)
* [Avviare l'app usando l'interfaccia della riga di comando di Azure](spring-cloud-quickstart-launch-app-cli.md)

Questo articolo descrive le dipendenze necessarie e spiega come aggiungerle al file POM.

## <a name="java-runtime-version"></a>Versione del runtime Java

In Azure Spring Cloud è possibile eseguire solo applicazioni Spring/Java.

Azure Spring Cloud supporta Java 8 e 11. L'ambiente di hosting contiene almeno la versione più recente di Azul Zulu OpenJDK per Azure. Per altre informazioni su Azul Zulu OpenJDK per Azure, vedere [Installare JDK](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Versioni di Spring Boot e Spring Cloud

Per preparare un'applicazione Spring Boot esistente per la distribuzione in Azure Spring Cloud, includere le dipendenze di Spring Boot e Spring Cloud nel file POM dell'applicazione come illustrato nelle sezioni seguenti.

Azure Spring Cloud supporta solo le app Spring Boot versione 2.1 o 2.2. Le combinazioni supportate di Spring Boot e Spring Cloud sono indicate nella tabella seguente:

Versione di Spring Boot | Versione di Spring Cloud
---|---
2.1 | Greenwich.RELEASE
2.2 | Hoxton.RELEASE

### <a name="dependencies-for-spring-boot-version-21"></a>Dipendenze per Spring Boot versione 2.1

Per Spring Boot versione 2.1 aggiungere le dipendenze seguenti al file POM dell'applicazione.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.12.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>Dipendenze per Spring Boot versione 2.2

Per Spring Boot versione 2.2 aggiungere le dipendenze seguenti al file POM dell'applicazione.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Dipendenza client di Azure Spring Cloud

Azure Spring Cloud ospita e gestisce i componenti di Spring Cloud. Tali componenti includono Spring Cloud Service Registry e Spring Cloud Config Server. Includere la libreria client di Azure Spring Cloud nelle dipendenze per consentire la comunicazione con l'istanza del servizio Azure Spring Cloud.

La tabella seguente elenca le versioni di Azure Spring Cloud corrette per l'app che usa Spring Boot e Spring Cloud.

Versione di Spring Boot | Versione di Spring Cloud | Versione di Azure Spring Cloud
---|---|---
2.1 | Greenwich.RELEASE | 2.1
2.2 | Hoxton.RELEASE | 2.2

Includere una delle dipendenze seguenti nel file pom.xml. Selezionare la dipendenza con la versione di Azure Spring Cloud corrispondente a quella in uso.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Dipendenza per Azure Spring Cloud versione 2.1

Per Spring Boot versione 2.1 aggiungere la dipendenza seguente al file POM dell'applicazione.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.1</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Dipendenza per Azure Spring Cloud versione 2.2

Per Spring Boot versione 2.2 aggiungere la dipendenza seguente al file POM dell'applicazione.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Altre dipendenze obbligatorie

Per abilitare le funzionalità predefinite di Azure Spring Cloud, l'applicazione deve includere le dipendenze seguenti. In questo modo l'applicazione si configura correttamente con ogni componente.

### <a name="enablediscoveryclient-annotation"></a>Annotazione EnableDiscoveryClient

Aggiungere l'annotazione seguente al codice sorgente dell'applicazione.
```java
@EnableDiscoveryClient
```
Vedere ad esempio l'applicazione piggymetrics negli esempi precedenti:
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="service-registry-dependency"></a>Dipendenza Registro servizi

Per usare il servizio Registro servizi di Azure gestito, includere la dipendenza `spring-cloud-starter-netflix-eureka-client` nel file pom.xml, come illustrato di seguito:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

L'endpoint del server Service Registry verrà inserito automaticamente come variabile di ambiente nell'app. Le applicazioni potranno quindi registrarsi con il server Service Registry e individuare altri microservizi dipendenti.

### <a name="distributed-configuration-dependency"></a>Dipendenza Configurazione distribuita

Per abilitare la configurazione distribuita, includere la dipendenza `spring-cloud-config-client` seguente nella sezione delle dipendenze del file pom.xml:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Non specificare `spring.cloud.config.enabled=false` nella configurazione bootstrap. In caso contrario, l'applicazione non funzionerà più con Config Server.

### <a name="metrics-dependency"></a>Dipendenza Metriche

Includere la dipendenza `spring-boot-starter-actuator` nella sezione delle dipendenze del file pom.xml, come illustrato di seguito:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Viene periodicamente eseguito il pull delle metriche dagli endpoint JMX. È possibile visualizzare le metriche usando il portale di Azure.

### <a name="distributed-tracing-dependency"></a>Dipendenza Traccia distribuita

Includere le dipendenze `spring-cloud-starter-sleuth` e `spring-cloud-starter-zipkin` nella sezione delle dipendenze del file pom.xml:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

 È anche necessario abilitare un'istanza di Azure Application Insights da usare con l'istanza del servizio Azure Spring Cloud. Per informazioni su come usare Application Insights con Azure Spring Cloud, vedere l'[esercitazione sulla traccia distribuita](spring-cloud-tutorial-distributed-tracing.md).

## <a name="see-also"></a>Vedere anche
* [Analizzare i log e le metriche dell'applicazione](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [Configurare Config Server](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [Usare la traccia distribuita con Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Guida di avvio rapido per Spring](https://spring.io/quickstart)
* [Documentazione di Spring Boot](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Passaggi successivi

In questo argomento si è appreso come configurare un'applicazione Java Spring per la distribuzione nel cloud Spring di Azure. Per informazioni su come configurare un'istanza del server di configurazione, vedere l'articolo seguente.

> [!div class="nextstepaction"]
> [Come configurare un'istanza di Config Server](spring-cloud-tutorial-config-server.md)

Altri esempi sono disponibili in GitHub: [Esempi di Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).

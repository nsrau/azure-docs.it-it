---
title: "Esercitazione: Preparare un'applicazione Spring per la distribuzione in Azure Spring Cloud"
description: Questa esercitazione mostra come preparare un'applicazione Java Spring per la distribuzione.
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 9918c7866b21cd2a9e021a355fb43977c91a89cf
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277440"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Preparare un'applicazione Java Spring per la distribuzione in Azure Spring Cloud

Questo argomento di avvio rapido illustra come preparare un'applicazione Java Spring Cloud esistente per la distribuzione in Azure Spring Cloud. Se correttamente configurato, Azure Spring Cloud offre servizi affidabili per il monitoraggio, il ridimensionamento e l'aggiornamento dell'applicazione Java Spring Cloud.

## <a name="java-runtime-version"></a>Versione del runtime Java

In Azure Spring Cloud è possibile eseguire solo applicazioni Spring/Java.

Azure Spring Cloud supporta Java 8 e 11. L'ambiente di hosting contiene almeno la versione più recente di Azul Zulu OpenJDK per Azure. Per altre informazioni su Azul Zulu OpenJDK per Azure, vedere [Installare JDK](https://docs.microsoft.com/azure/java/jdk/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Versioni di Spring Boot e Spring Cloud

Azure Spring Cloud supporta solo app Spring Boot. Supporta entrambe le versioni Spring Boot 2.0 e 2.1. Le combinazioni supportate di Spring Boot e Spring Cloud sono indicate nella tabella seguente:

Versione di Spring Boot | Versione di Spring Cloud
---|---
2.0 | Finchley.RELEASE
2.1 | Greenwich.RELEASE

Verificare che il file pom.xml includa le dipendenze di Spring Boot e Spring Cloud corrette in base alla versione di Spring Boot in uso.

### <a name="dependencies-for-spring-boot-version-20"></a>Dipendenze per Spring Boot versione 2.0

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.9.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-21"></a>Dipendenze per Spring Boot versione 2.1

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR3</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Dipendenza client di Azure Spring Cloud

Azure Spring Cloud ospita e gestisce automaticamente i componenti di Spring Cloud. Tali componenti includono Spring Cloud Service Registry e Spring Cloud Config Server. Includere la libreria client di Azure Spring Cloud nelle dipendenze per consentire la comunicazione con l'istanza del servizio Azure Spring Cloud.

La tabella seguente elenca le versioni di Azure Spring Cloud corrette per l'app che usa Spring Boot e Spring Cloud.

Versione di Spring Boot | Versione di Spring Cloud | Versione di Azure Spring Cloud
---|---|---
2.0 | Finchley.RELEASE | 2.0
2.1 | Greenwich.RELEASE | 2.1

Includere una delle dipendenze seguenti nel file pom.xml. Selezionare la dipendenza con la versione di Azure Spring Cloud corrispondente a quella in uso.

### <a name="dependency-for-azure-spring-cloud-version-20"></a>Dipendenza per Azure Spring Cloud versione 2.0

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.0.0</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Dipendenza per Azure Spring Cloud versione 2.1

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Altre dipendenze obbligatorie

Per abilitare le funzionalità predefinite di Azure Spring Cloud, l'applicazione deve includere le dipendenze seguenti. In questo modo l'applicazione si configura correttamente con ogni componente.  

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

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come configurare l'applicazione Java Spring per la distribuzione in Azure Spring Cloud. Per informazioni su come configurare un'istanza di Config Server, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Come configurare un'istanza di Config Server](spring-cloud-tutorial-config-server.md)

Altri esempi sono disponibili in GitHub: [Esempi di Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

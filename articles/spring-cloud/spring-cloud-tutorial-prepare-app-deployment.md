---
title: "Esercitazione: Preparare un'applicazione Spring per la distribuzione in Azure Spring Cloud"
description: Questa esercitazione mostra come preparare un'applicazione Java Spring per la distribuzione.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 3c567c54f887afe9ac112970a682b9466515d949
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708739"
---
# <a name="tutorial-prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Esercitazione: Preparare un'applicazione Java Spring per la distribuzione in Azure Spring Cloud

Questo argomento di avvio rapido illustra come preparare un'applicazione Java Spring Cloud esistente per la distribuzione in Azure Spring Cloud.  Se correttamente configurato, Azure Spring Cloud offre servizi affidabili per il monitoraggio, il ridimensionamento e l'aggiornamento dell'applicazione Spring Cloud. 

## <a name="java-runtime-version"></a>Versione del runtime Java

In Azure Spring Cloud è possibile eseguire solo applicazioni Spring/Java.

Sono supportate le versioni Java 8 e Java 11. L'ambiente di hosting contiene almeno Azul Zulu OpenJDK per Azure. Per altre informazioni su Azul Zulu OpenJDK per Azure, vedere [questo articolo](https://docs.microsoft.com/azure/java/jdk/java-jdk-install). 

## <a name="spring-boot-and-spring-cloud-versions"></a>Versioni di Spring Boot e Spring Cloud

In Azure Spring Cloud sono supportate solo app Spring Boot. Sono supportate le versioni 2.0 e 2.1 di Spring Boot. Le combinazioni supportate di Spring Boot e Spring Cloud sono indicate nella tabella seguente.

Versione di Spring Boot | Versione di Spring Cloud
---|---
2.0.x | Finchley.RELEASE
2.1.x | Greenwich.RELEASE

Verificare che il file `pom.xml` includa le dipendenze di Spring Boot e Spring Cloud in base alla versione in uso.

### <a name="version-20"></a>Versione 2.0:

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

### <a name="version-21"></a>Versione 2.1:

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

Azure Spring Cloud ospita e gestisce automaticamente i componenti di Spring Cloud, ad esempio Spring Cloud Service Registry e Spring Cloud Config Server. Includere la libreria client di Azure Spring Cloud nelle dipendenze per consentire la comunicazione con l'istanza del servizio Azure Spring Cloud.

La tabella seguente include le versioni correnti dell'app Spring Boot/Spring Cloud.

Versione di Spring Boot | Versione di Spring Cloud | Versione di Azure Spring Cloud
---|---|---
2.0.x | Finchley.RELEASE | 2.0.x
2.1.x | Greenwich.RELEASE | 2.1.x

Includere uno dei frammenti di codice seguenti  nel file `pom.xml`.  Selezionare il frammento con la versione corrispondente a quella in uso.

### <a name="version-20x"></a>Versione 2.0.x:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.0.0</version>
</dependency>
```

### <a name="version-21x"></a>Versione 2.1.x:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Altre dipendenze obbligatorie

Per abilitare le funzionalità predefinite di Azure Spring Cloud, l'applicazione deve includere le dipendenze seguenti. In questo modo l'applicazione si configura correttamente con ogni componente.  

### <a name="service-registry"></a>Service Registry

Per usare il servizio Azure Service Registry, includere `spring-cloud-starter-netflix-eureka-client` nel file `POM.xml` come illustrato di seguito.

L'endpoint del server Service Registry verrà inserito automaticamente come variabile di ambiente nell'app. Le applicazioni potranno registrarsi con il server Service Registry e individuare altri microservizi dipendenti.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

### <a name="distributed-configuration"></a>Configurazione distribuita

Per abilitare la configurazione distribuita, includere `spring-cloud-config-client` nella sezione di dipendenze del file `pom.xml`.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Non specificare `spring.cloud.config.enabled=false` nella configurazione di bootstrap, altrimenti l'applicazione non funzionerà più con Config Server.

### <a name="metrics"></a>Metriche

Includere `spring-boot-starter-actuator` nella sezione di dipendenze del file pom.xml. Verrà eseguito periodicamente il pull delle metriche dagli endpoint JMX e sarà possibile visualizzarle tramite il portale di Azure.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### <a name="distributed-tracing"></a>Traccia distribuita

Includere `spring-cloud-starter-sleuth` e `spring-cloud-starter-zipkin` nella sezione di dipendenze del file pom.xml, come indicato di seguito. Inoltre, è necessario abilitare un'istanza di Azure Application Insights per l'uso con l'istanza del servizio Azure Spring Cloud. Per altre informazioni su come abilitare Application Insights con Azure Spring Cloud, vedere [qui](spring-cloud-tutorial-distributed-tracing.md)

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

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come configurare l'applicazione Java Spring per la distribuzione in Azure Spring Cloud.  Per informazioni su come abilitare Config Server, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Informazioni su come configurare Config Server](spring-cloud-tutorial-config-server.md).

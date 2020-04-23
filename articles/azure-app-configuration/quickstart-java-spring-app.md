---
title: Avvio rapido per imparare a usare Configurazione app di Azure
description: Guida di avvio rapido per l'uso di Configurazione app di Azure con le app Java Spring.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 04/18/2020
ms.author: lcozzens
ms.openlocfilehash: 69b9733f4d79f5fc42fe86ca0f8a97305205aeef
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085060"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Guida di avvio rapido: Creare un'app Java Spring con Configurazione app di Azure

In questa guida di avvio rapido si incorpora Configurazione app di Azure in un'app Java Spring per centralizzare l'archiviazione e la gestione delle impostazioni dell'applicazione separatamente dal codice.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk) versione 8 supportato.
- [Apache Maven](https://maven.apache.org/download.cgi) versione 3.0 o successive.

## <a name="create-an-app-configuration-store"></a>Creare un archivio di Configurazione app

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selezionare **Esplora configurazioni** >  **+ Crea** > **Coppia chiave-valore** per aggiungere le chiavi chiave-valore seguenti:

    | Chiave | Valore |
    |---|---|
    | /application/config.message | Ciao |

    Lasciare vuoti i campi **Etichetta** e **Tipo di contenuto** per il momento.

7. Selezionare **Applica**.

## <a name="create-a-spring-boot-app"></a>Compilare l'app Spring Boot

Usare [Spring Initializr](https://start.spring.io/) per creare un nuovo progetto Spring Boot.

1. Passare a <https://start.spring.io/>.

1. Specificare le opzioni seguenti:

   - Generare un progetto **Maven** con **Java**.
   - Specificare **Spring Boot** versione 2.0 o successiva.
   - Specificare i nomi di **Group** (Gruppo) e **Artifact** (Artefatto) per l'applicazione.
   - Aggiungere la dipendenza **Spring Web**.

1. Dopo aver specificato le opzioni precedenti, selezionare **Genera progetto**. Quando richiesto, scaricare il progetto in un percorso nel computer locale.

## <a name="connect-to-an-app-configuration-store"></a>Connettersi a un archivio di Configurazione app

1. Dopo l'estrazione dei file nel sistema locale, la semplice applicazione Spring Boot è pronta per la modifica. Individuare il file *pom.xml* nella directory radice dell'app.

1. Aprire il file *pom.xml* in un editor di testo e aggiungere l'utilità di avvio Spring Cloud per la configurazione di Azure all'elenco di `<dependencies>`:

    **Spring Cloud 1.1.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.2</version>
    </dependency>
    ```

    **Spring Cloud 1.2.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.2</version>
    </dependency>
    ```

1. Creare un nuovo file Java denominato *MessageProperties.java* nella directory del pacchetto dell'app. Aggiungere le righe seguenti:

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;

    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

1. Creare un nuovo file Java denominato *HelloController.java* nella directory del pacchetto dell'app. Aggiungere le righe seguenti:

    ```java
    package com.example.demo;

    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

1. Aprire il file Java dell'applicazione principale e aggiungere `@EnableConfigurationProperties` per abilitare questa funzionalità.

    ```java
    import org.springframework.boot.context.properties.EnableConfigurationProperties;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Creare un nuovo file denominato `bootstrap.properties` nella directory di risorse dell'app e aggiungere le righe seguenti al file. Sostituire i valori di esempio con le proprietà appropriate per l'archivio di Configurazione app.

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].connection-string= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Impostare una variabile di ambiente denominata **APP_CONFIGURATION_CONNECTION_STRING** sulla chiave di accesso all'archivio di Configurazione app. Al prompt dei comandi eseguire questo comando e riavviare il prompt per rendere effettiva la modifica:

    ```cmd
    setx APP_CONFIGURATION_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Se si usa Windows PowerShell, eseguire il comando seguente:

    ```azurepowershell
    $Env:APP_CONFIGURATION_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Se si usa macOS o Linux, eseguire il comando seguente:

    ```cmd
    export APP_CONFIGURATION_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

## <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Compilare l'applicazione Spring Boot con Maven ed eseguirla, ad esempio:

    ```cmd
    mvn clean package
    mvn spring-boot:run
    ```

2. Quando l'applicazione è in esecuzione, è possibile testarla usando *curl*, ad esempio:

      ```cmd
      curl -X GET http://localhost:8080/
      ```

    Dovrebbe essere visualizzato il messaggio indicante che è stato eseguito l'accesso nell'archivio di Configurazione app.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato creato un nuovo archivio di Configurazione app, che è stato usato con un'app Java Spring. Per altre informazioni, vedere [Spring in Azure](https://docs.microsoft.com/java/azure/spring-framework/). Per informazioni su come abilitare l'app Java Spring per aggiornare in modo dinamico le impostazioni di configurazione, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Abilitare la configurazione dinamica](./enable-dynamic-configuration-java-spring-app.md)

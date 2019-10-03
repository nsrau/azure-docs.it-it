---
title: Guida introduttiva con informazioni su come usare Configurazione app di Azure | Microsoft Docs
description: Guida introduttiva per l'uso di Configurazione app di Azure con le app Java Spring.
services: azure-app-configuration
documentationcenter: ''
author: yidon
manager: jeffya
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: Spring
ms.workload: tbd
ms.date: 01/08/2019
ms.author: yidon
ms.openlocfilehash: f4ebbd4f37422c5aa2fea07a243eb624ec9e2961
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687031"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Guida introduttiva: Creare un'app Java Spring con Configurazione app di Azure

In questa guida di avvio rapido si incorpora Configurazione app di Azure in un'app Java Spring per centralizzare l'archiviazione e la gestione delle impostazioni dell'applicazione separatamente dal codice.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk) versione 8 supportato.
- [Apache Maven](https://maven.apache.org/download.cgi) versione 3.0 o successive.

## <a name="create-an-app-configuration-store"></a>Creare un archivio di configurazione app

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selezionare **Configuration Explorer** >  **+ Crea** per aggiungere le coppie chiave-valore seguenti:

    | Chiave | Valore |
    |---|---|
    | /application/config.message | Ciao |

    Lasciare vuoti i campi **Etichetta** e **Tipo di contenuto** per il momento.

## <a name="create-a-spring-boot-app"></a>Compilare l'app Spring Boot

Per creare un nuovo progetto Spring Boot, usare [Spring Initializr](https://start.spring.io/).

1. Passare a <https://start.spring.io/>.

2. Specificare le opzioni seguenti:

   * Generare un progetto **Maven** con **Java**.
   * Specificare **Spring Boot** versione 2.0 o successiva.
   * Specificare i nomi di **Group** (Gruppo) e **Artifact** (Artefatto) per l'applicazione.
   * Aggiungere la dipendenza **Web**.

3. Dopo aver specificato le opzioni precedenti, selezionare **Genera progetto**. Quando richiesto, scaricare il progetto in un percorso nel computer locale.

## <a name="connect-to-an-app-configuration-store"></a>Connettersi a un archivio di configurazione app

1. Dopo l'estrazione dei file nel sistema locale, la semplice applicazione Spring Boot è pronta per la modifica. Individuare il file *pom.xml* nella directory radice dell'app.

2. Aprire il file *pom.xml* in un editor di testo e aggiungere l'utilità di avvio Spring Cloud per la configurazione di Azure all'elenco di `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M4</version>
    </dependency>
    ```

3. Creare un nuovo file Java denominato *MessageProperties.java* nella directory del pacchetto dell'app. Aggiungere le righe seguenti:

    ```java
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

4. Creare un nuovo file Java denominato *HelloController.java* nella directory del pacchetto dell'app. Aggiungere le righe seguenti:

    ```java
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

5. Aprire il file Java dell'applicazione principale e aggiungere `@EnableConfigurationProperties` per abilitare questa funzionalità.

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

6. Creare un nuovo file denominato `bootstrap.properties` nella directory di risorse dell'app e aggiungere le righe seguenti al file. Sostituire i valori di esempio con le proprietà appropriate per l'archivio di configurazione app.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Compilare l'applicazione Spring Boot con Maven ed eseguirla, ad esempio:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. Quando l'applicazione è in esecuzione, è possibile testarla usando *curl*, ad esempio:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Dovrebbe essere visualizzato il messaggio indicante che è stato eseguito l'accesso nell'archivio configurazione app.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato creato un nuovo archivio di configurazione app, che è stato usato con un'app Java Spring. Per altre informazioni, vedere [Spring in Azure](https://docs.microsoft.com/java/azure/spring-framework/).

Per altre informazioni sull'uso di Configurazione app, continuare con l'esercitazione successiva sull'autenticazione.

> [!div class="nextstepaction"]
> [Integrazione dell'identità gestita](./howto-integrate-azure-managed-service-identity.md)

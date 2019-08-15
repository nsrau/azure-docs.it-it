---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 64bedef3cf52451d145a97385937ae2adc9b2b0c
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968064"
---
## <a name="prerequisites"></a>Prerequisiti

* [JDK 7 o versione successiva](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* Una chiave di sottoscrizione di Azure per Traduzione testuale

## <a name="initialize-a-project-with-gradle"></a>Inizializzare un progetto con Gradle

Per iniziare, si crea una directory di lavoro per questo progetto. Dalla riga di comando (o terminale) eseguire questo comando:

```console
mkdir alt-translation-sample
cd alt-translation-sample
```

Successivamente, si inizializzerà un progetto Gradle. Questo comando creerà i file di compilazione essenziali per Gradle, in particolare `build.gradle.kts`, che viene usato in fase di esecuzione per creare e configurare l'applicazione. Eseguire questo comando dalla directory di lavoro:

```console
gradle init --type basic
```

Quando viene chiesto di scegliere un linguaggio **DSL**, selezionare **Kotlin**.

## <a name="configure-the-build-file"></a>Configurare il file di compilazione

Individuare `build.gradle.kts` e aprirlo con l'ambiente di sviluppo integrato o l'editor di testo preferito, quindi copiare questa configurazione della build:

```
plugins {
    java
    application
}
application {
    mainClassName = "AltTranslation"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Tenere presente che questo esempio ha dipendenze da OkHttp per le richieste HTTP e da Gson gestire e analizzare JSON. Per altre informazioni sulle configurazioni della build, vedere [Creating New Gradle Builds](https://guides.gradle.org/creating-new-gradle-builds/) (Creazione di nuove compilazioni Gradle).

## <a name="create-a-java-file"></a>Creare un file Java

È possibile creare una cartella per l'app di esempio. Dalla directory di lavoro eseguire:

```console
mkdir -p src\main\java
```

Successivamente, in questa cartella creare un file denominato `AltTranslation.java`.

## <a name="import-required-libraries"></a>Importare le librerie obbligatorie

Aprire `AltTranslation.java` e aggiungere queste istruzioni import:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>Definire le variabili

In primo luogo, è necessario creare una classe pubblica per il progetto:

```java
public class AltTranslation {
  // All project code goes here...
}
```

Aggiungere le righe seguenti alla classe `AltTranslation`. Si noterà che oltre a `api-version`, sono stati aggiunti altri due parametri a `url`. Questi parametri vengono usati per impostare la lingua di input e output della traduzione. In questo esempio vengono usate le lingue inglese (`en`) e spagnolo (`es`).

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es";
```

Se si usa una sottoscrizione multiservizio di Servizi cognitivi, è necessario includere anche `Ocp-Apim-Subscription-Region` nei parametri della richiesta. [Informazioni sull'autenticazione con la sottoscrizione multiservizio](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Creare un client e compilare una richiesta

Aggiungere questa riga alla classe `AltTranslation` per creare un'istanza di `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Viene quindi compilata la richiesta POST. È possibile modificare il testo per la traduzione.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"Pineapples\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Creare una funzione per analizzare la risposta

Questa semplice funzione analizza e migliora la risposta JSON dal servizio Traduzione testuale.

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>Combinare tutti gli elementi

L'ultimo passaggio consiste nell'effettuare una richiesta e ottenere una risposta. Aggiungere le righe seguenti al progetto:

```java
public static void main(String[] args) {
    try {
        AltTranslation altTranslationRequest = new AltTranslation();
        String response = altTranslationRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio

A questo punto è possibile eseguire l'app di esempio. Dalla riga di comando, o dalla sessione di terminale, passare alla radice della directory di lavoro ed eseguire:

```console
gradle build
```

Al termine della compilazione, eseguire:

```console
gradle run
```

## <a name="sample-response"></a>Risposta di esempio

```json
[
  {
    "normalizedSource": "pineapples",
    "displaySource": "pineapples",
    "translations": [
      {
        "normalizedTarget": "piñas",
        "displayTarget": "piñas",
        "posTag": "NOUN",
        "confidence": 0.7016,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "pineapples",
            "displayText": "pineapples",
            "numExamples": 5,
            "frequencyCount": 158
          },
          {
            "normalizedText": "cones",
            "displayText": "cones",
            "numExamples": 5,
            "frequencyCount": 13
          },
          {
            "normalizedText": "piña",
            "displayText": "piña",
            "numExamples": 3,
            "frequencyCount": 5
          },
          {
            "normalizedText": "ganks",
            "displayText": "ganks",
            "numExamples": 2,
            "frequencyCount": 3
          }
        ]
      },
      {
        "normalizedTarget": "ananás",
        "displayTarget": "ananás",
        "posTag": "NOUN",
        "confidence": 0.2984,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "pineapples",
            "displayText": "pineapples",
            "numExamples": 2,
            "frequencyCount": 16
          }
        ]
      }
    ]
  }
]
```

## <a name="next-steps"></a>Passaggi successivi

Esaminare le informazioni di riferimento sulle API per conoscere quali operazioni è possibile eseguire con l'API Traduzione testuale.

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

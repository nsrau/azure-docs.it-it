---
title: 'Avvio rapido: Rilevare la lingua del testo, Java - API Traduzione testuale'
titleSuffix: Azure Cognitive Services
description: In questo argomento di avvio rapido si apprenderà come rilevare la lingua del testo fornito con Java e l'API REST Traduzione testuale.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 12/03/2018
ms.author: erhopf
ms.openlocfilehash: d810b282936db1a31cdeb0133ce3c5bf0059850b
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52890782"
---
# <a name="quickstart-use-the-translator-text-api-to-detect-text-language-using-java"></a>Avvio rapido: Usare l'API Traduzione testuale per rilevare la lingua del testo con Java

In questo argomento di avvio rapido si apprenderà come rilevare la lingua del testo fornito con Java e l'API REST Traduzione testuale.

Per questa guida introduttiva è necessario avere un [account di Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con una risorsa Traduzione testuale. Se non si dispone di un account, è possibile usare la [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/) per ottenere una chiave di sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

* [JDK 7 o versione successiva](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* Una chiave di sottoscrizione di Azure per Traduzione testuale

## <a name="initialize-a-project-with-gradle"></a>Inizializzare un progetto con Gradle

Per iniziare, si crea una directory di lavoro per questo progetto. Dalla riga di comando (o terminale) eseguire questo comando:

```console
mkdir detect-sample
cd detect-sample
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
    mainClassName = "Detect"
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
mkdir -p src/main/java
```

Successivamente, in questa cartella creare un file denominato `Detect.java`.

## <a name="import-required-libraries"></a>Importare le librerie obbligatorie

Aprire `Detect.java` e aggiungere queste istruzioni import:

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
public class Detect {
  // All project code goes here...
}
```

Aggiungere le righe seguenti alla classe `Detect`:

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0";
```

## <a name="create-a-client-and-build-a-request"></a>Creare un client e compilare una richiesta

Aggiungere questa riga alla classe `Detect` per creare un'istanza di `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Viene quindi compilata la richiesta POST. È possibile modificare il testo per il rilevamento della lingua.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"Salve mondo!\"\n}]");
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
        Detect detectRequest = new Detect();
        String response = detectRequest.Post();
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

## <a name="sample-response"></a>Risposta di esempio

```json
[
  {
    "language": "it",
    "score": 1.0,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "en",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="next-steps"></a>Passaggi successivi

Esaminare il codice di esempio per questa guida introduttiva e per altre, incluse quelle relative alla traduzione e alla traslitterazione, e anche altri progetti di esempio di Traduzione testuale su GitHub.

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Java su GitHub](https://aka.ms/TranslatorGitHub?type=&language=java)

## <a name="see-also"></a>Vedere anche 

* [Tradurre un testo](quickstart-java-translate.md)
* [Traslitterare testo](quickstart-java-transliterate.md)
* [Identificare la lingua da un input](quickstart-java-detect.md)
* [Ottenere traduzioni alternative](quickstart-java-dictionary.md)
* [Ottenere un elenco di lingue supportate](quickstart-java-languages.md)
* [Determinare la lunghezza delle frasi da un input](quickstart-java-sentences.md)

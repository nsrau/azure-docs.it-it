---
title: 'Guida introduttiva: API Controllo ortografico Bing, Java'
titlesuffix: Azure Cognitive Services
description: Ottenere informazioni ed esempi di codice per iniziare a usare rapidamente l'API Controllo ortografico Bing.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: quickstart
ms.date: 09/14/2017
ms.author: v-jaswel
ms.openlocfilehash: 00e0b7db5bfc8b763d9b16524bd783601d1ec4d8
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48800996"
---
# <a name="quickstart-for-bing-spell-check-api-with-java"></a>Guida introduttiva all'API Controllo ortografico Bing con Java 

Questo articolo spiega come usare l'[API Controllo ortografico Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) con Java. L'API Controllo ortografico restituisce un elenco di parole che non riconosce insieme a suggerimenti di parole sostitutive. In genere si invia il testo a questa API e quindi si effettuano le sostituzioni suggerite nel testo oppure si mostrano i suggerimenti all'utente dell'applicazione in modo che possa decidere se effettuare le sostituzioni. Questo articolo illustra come inviare una richiesta che contiene il testo "Hollo, wrld!". Le parole sostitutive suggerite sono "Hello" e "world".

## <a name="prerequisites"></a>Prerequisiti

Per compilare ed eseguire questo codice è necessario [JDK 7 o 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Se si preferisce, è possibile usare un ambiente IDE Java, ma è sufficiente un editor di testo.

È necessario avere un [account API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con l'**API Controllo ortografico Bing versione 7**. Per questa guida introduttiva è sufficiente la [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/#lang). È necessaria la chiave di accesso fornita all'attivazione della versione di valutazione gratuita oppure è possibile usare una chiave di sottoscrizione a pagamento dal dashboard di Azure.

## <a name="get-spell-check-results"></a>Ottenere i risultati del controllo ortografico

1. Creare un nuovo progetto Java nell'IDE preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `subscriptionKey` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```java
import java.io.*;
import java.net.*;
import javax.net.ssl.HttpsURLConnection;

public class HelloWorld {

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    // NOTE: Replace this example key with a valid subscription key.
    static String key = "ENTER KEY HERE";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";

    public static void check () throws Exception {
        String params = "?mkt=" + mkt + "&mode=" + mode;
        URL url = new URL(host + path + params);
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
        connection.setRequestProperty("Content-Length", "" + text.length() + 5);
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();

        BufferedReader in = new BufferedReader(
        new InputStreamReader(connection.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
            System.out.println(line);
        }
        in.close();
    }

    public static void main(String[] args) {
        try {
            check ();
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

**Risposta**

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione sul Controllo ortografico Bing](../tutorials/spellcheck.md)

## <a name="see-also"></a>Vedere anche 

- [Panoramica del Controllo ortografico Bing](../proof-text.md)
- [Informazioni di riferimento per l'API Controllo ortografico Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)

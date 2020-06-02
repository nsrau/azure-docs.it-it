---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 61c5370bc8dd77babaaa79c814c0d7a9c8049d73
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82978834"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Creare una risorsa Voce di Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Configurare l'ambiente di sviluppo e creare un progetto vuoto](../../../../quickstarts/setup-platform.md)

## <a name="create-a-new-website-folder"></a>Creare una nuova cartella per il sito Web

Creare una nuova cartella vuota. Se si vuole ospitare l'esempio in un server Web, assicurarsi che il server Web possa accedere alla cartella.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Decomprimere Speech SDK per JavaScript nella cartella

Scaricare Speech SDK come [pacchetto con estensione zip](https://aka.ms/csspeech/jsbrowserpackage) e decomprimerlo nella nuova cartella. Vengono decompressi due file, `microsoft.cognitiveservices.speech.sdk.bundle.js` e `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
Quest'ultimo file è facoltativo ed è utile per eseguire il debug nel codice dell'SDK.

## <a name="create-an-indexhtml-page"></a>Creare una pagina index.html

Creare un nuovo file nella cartella denominato `index.html` e aprirlo con un editor di testo.

1. Creare la struttura HTML seguente:

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/index-translate-stt.html)]

## <a name="create-the-token-source-optional"></a>Creare l'origine del token (facoltativo)

Se si vuole ospitare la pagina Web in un server Web, facoltativamente è possibile specificare un'origine del token per l'applicazione demo.
In questo modo la chiave di sottoscrizione non lascerà mai il server, consentendo agli utenti di usare le funzionalità di riconoscimento vocale senza dover immettere codici di autorizzazione.

Creare un file denominato `token.php`. In questo esempio si presuppone che il server Web supporti il linguaggio di scripting PHP. Immettere il codice seguente:

```php
<?php
header('Access-Control-Allow-Origin: ' . $_SERVER['SERVER_NAME']);

// Replace with your own subscription key and service region (e.g., "westus").
$subscriptionKey = 'YourSubscriptionKey';
$region = 'YourServiceRegion';

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'https://' . $region . '.api.cognitive.microsoft.com/sts/v1.0/issueToken');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, '{}');
curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json', 'Ocp-Apim-Subscription-Key: ' . $subscriptionKey));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
echo curl_exec($ch);
?>
```

> [!NOTE]
> I token di autorizzazione hanno una durata limitata.
> Questo esempio semplificato non illustra come aggiornare automaticamente i token di autorizzazione. Come utente, è possibile ricaricare la pagina manualmente o premere F5 per aggiornare.

## <a name="build-and-run-the-sample-locally"></a>Compilare ed eseguire l'esempio in locale

Per avviare l'app, fare doppio clic sul file index.html o aprirlo con il Web browser preferito. Presenterà una semplice interfaccia utente grafica, che consente di immettere la chiave di sottoscrizione e l'[area](../../../../regions.md) e di attivare la sintesi del testo di input.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Compilare ed eseguire l'esempio tramite un server Web

Per avviare l'app, aprire il Web browser preferito e passare all'URL pubblico in cui è ospitata la cartella, immettere l'[area](../../../../regions.md) e attivare la sintesi del testo di input. Se il comportamento è configurato, verrà acquisito un token dall'origine del token.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]
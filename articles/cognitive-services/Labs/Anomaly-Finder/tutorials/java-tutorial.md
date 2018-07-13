---
title: Rilevamento anomalie in un'app Java - Servizi cognitivi Microsoft | Microsoft Docs
description: Esplorare un'app Java che usa l'API Rilevamento anomalie in Servizi cognitivi Microsoft. Inviare i punti dati originali all'API e ottenere i punti dei valori previsti e delle anomalie.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 228d440da358eba1322e2228c54f21e925e36ecd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35375324"
---
# <a name="anomaly-detection-java-application"></a>Rilevamento anomalie in un'app Java

Questo articolo illustra l'uso di una semplice applicazione Java per richiamare l'API Rilevamento anomalie.  
Nell'esempio vengono inviati i dati di una serie temporale all'API Rilevamento anomalie con la chiave di sottoscrizione dell'utente e si ottengono quindi dall'API tutti i punti di anomalie e il valore previsto per ogni punto dati.

## <a name="prerequisites"></a>prerequisiti

### <a name="platform-requirements"></a>Requisiti di piattaforma

Questa esercitazione è stata sviluppata usando [IntelliJ IDEA](https://www.jetbrains.com/idea). È necessario installare anche [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/index.html) versione 1.8 + e uno strumento di compilazione aggiornato [Apache's Maven](http://maven.apache.org/).

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Sottoscrivere Rilevamento anomalie e ottenere una chiave di sottoscrizione 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]
 

## <a name="download-the-tutorial-project"></a>Scaricare il progetto dell'esercitazione

1. Passare al [repository Java](https://github.com/MicrosoftAnomalyDetection/java-sample) MicrosoftAnomalyDetection.
2. Fare clic sul pulsante Clone or download (Clona o scarica).
3. Fare clic su Download ZIP (Scarica file zip) per scaricare un file zip del progetto dell'esercitazione.

<a name="Step1"></a>
### <a name="open-the-tutorial-project"></a>Aprire il progetto dell'esercitazione

1. Estrarre il file zip del progetto dell'esercitazione.
2. In IntelliJ IDEA fare clic su **File > Open** (File > Apri). Verrà visualizzata la finestra di dialogo Open File or Project (Apri file o progetto).
3. Selezionare il percorso radice del progetto estratto e quindi fare clic su OK.
4. Nel pannello Projects (Progetti) espandere **src > main > java**.
5. Fare doppio clic su com.microsoft.cognitiveservice.anomalydetection.Main.java per caricare il file nell'editor.

<a name="Step2"></a>
### <a name="replace-subscriptionkey-and-uri-region"></a>Sostituire subscriptionKey e l'area URI

```
// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
public static final String subscriptionKey = "<Subscription Key>";

public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

```

<a name="Step3"></a>
### <a name="build-and-run-the-tutorial-project"></a>Compilare ed eseguire il progetto dell'esercitazione

1. Visualizzare il menu di scelta rapida facendo clic con il pulsante destro del mouse in un punto qualsiasi della scheda del codice sorgente com.microsoft.cognitiveservice.anomalydetection.Main.java. 
2. Selezionare Run 'Main.main()' (Esegui 'Main.main()')
3. Il risultato della richiesta di esempio verrà restituito e visualizzato nel terminale.

### <a name="result-of-the-tutorial-project"></a>Risultato del progetto dell'esercitazione

[!INCLUDE [diagrams](../includes/diagrams.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)

---
title: 'Guida introduttiva: Riconoscimento vocale in Java (Windows o Linux)'
titleSuffix: Azure Cognitive Services
description: Informazioni sul riconoscimento vocale in Java (Windows o Linux)
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: fmegen
ms.openlocfilehash: 80ddef79392acb677555ed795bf429f5ec0266a0
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49467260"
---
# <a name="quickstart-recognize-speech-in-java-on-windows-or-linux-by-using-the-speech-service-sdk"></a>Guida introduttiva: Riconoscimento vocale in Java su Windows o Linux con l'SDK del servizio Voce

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In questo articolo viene creata un'applicazione console Java con l'[SDK del servizio Voce](speech-sdk.md). Avviene la trascrizione del riconoscimento vocale in tempo reale dal microfono del PC. L'applicazione è compilata con il pacchetto Speech SDK Maven ed Eclipse Java IDE (v4.8) su Windows a 64 bit o Ubuntu Linux 16.04. Viene eseguito su un ambiente Java 8 runtime a 64 bit (JRE).

> [!NOTE]
> Per Speech Device SDK e il dispositivo Roobo, vedere [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Prerequisiti

È necessaria una chiave di sottoscrizione del servizio Voce per completare la guida introduttiva. È possibile ottenerne una gratuitamente. Per informazioni dettagliate, vedere [Provare gratuitamente il servizio Voce](get-started.md).


## <a name="create-and-configure-project"></a>Creare e configurare un progetto

Se si utilizza Ubuntu 16.04, prima di avviare Eclipse eseguire i seguenti comandi per assicurarsi che i pacchetti richiesti siano installati.

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

1. Avviare Eclipse.

1. Nell'utilità di avvio di Eclipse, nel campo **Area di lavoro**, inserire il nome di una nuova directory dell'area di lavoro. Selezionare quindi **Avvio**.

   ![Screenshot dell'utilità di avvio di Eclipse](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Dopo qualche istante verrà visualizzata la finestra principale dell'IDE di Eclipse. Se è presente, chiudere la schermata iniziale.

1. Dalla barra dei menu di Eclipse, creare un nuovo progetto selezionando **File** > **Nuovo** > **Progetto**.

1. Verrà visualizzata la finestra di dialogo **Nuovo progetto** . Selezionare **Progetto Java**e selezionare **Avanti**.

   ![Screenshot della finestra di dialogo Nuovo progetto, con il progetto Java evidenziato](media/sdk/qs-java-jre-02-select-wizard.png)

1. Viene avviata la procedura guidata Nuovo progetto Java. Nel campo **Nome progetto**, inserire **quickstart** e selezionare **JavaSE-1.8** come ambiente di esecuzione. Selezionare **Fine**.

   ![Screenshot della procedura guidata Nuovo progetto Java](media/sdk/qs-java-jre-03-create-java-project.png)

1. Se viene visualizzata la finestra **Aprire prospettiva associata?**, selezionare **Apri prospettiva**.

1. In **Esplora pacchetti**, fare doppio clic sul progetto **quickstart**. Selezionare **Configura** > **Converti in progetto Maven** dal menu di scelta rapida.

   ![Screenshot di Esplora pacchetti](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Viene visualizzata la finestra **Crea nuovo POM**. Nel campo **Id gruppo**, inserire **com.microsoft.cognitiveservices.speech.samples**, e nel campo **Id artefatto**, inserire **quickstart**. Selezionare quindi **Fine**.

   ![Screenshot di Crea nuova finestra POM](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Aprire il file **pom.xml** e modificarlo.

   * Alla fine del file, prima del tag di chiusura `</project>`, creare un elemento `repositories` con un riferimento al repository Maven per Speech SDK, come mostrato di seguito:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * Aggiungere anche un elemento `dependencies` specificando come dipendenza la versione 1.0.1 di Speech SDK:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

   * Salvare le modifiche.

## <a name="add-sample-code"></a>Aggiungere codice di esempio

1. Per aggiungere una nuova classe vuota al progetto Java, selezionare **File** > **Nuovo** > **Classe**.

1. Nella finestra **Nuova classe Java** immettere **speechsdk.quickstart** nel campo **Pacchetto** e **Main** nel campo **Nome** .

   ![Screenshot della procedura guidata Nuova classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Sostituire tutto il codice in `Main.java` con il frammento di codice seguente:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Sostituire la stringa `YourServiceRegion` con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare le modifiche apportate al progetto.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

Premere F11 o selezionare **Esegui** > **Debug**.
I successivi 15 secondi di input vocale dal microfono verranno riconosciuti e registrati nella finestra della console.

![Screenshot della console dopo il riconoscimento corretto](media/sdk/qs-java-jre-07-console-output.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Ricercare questo esempio nella cartella `quickstart/java-jre`.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Riconoscere le finalità dai contenuti vocali con Speech SDK per Java](how-to-recognize-intents-from-speech-java.md)

## <a name="see-also"></a>Vedere anche 

- [Traduzione vocale](how-to-translate-speech-csharp.md)
- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)

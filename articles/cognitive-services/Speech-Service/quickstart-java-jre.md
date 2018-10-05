---
title: 'Guida introduttiva: Riconoscimento vocale in Java (Windows o Linux)'
titleSuffix: Microsoft Cognitive Services
description: Informazioni sul riconoscimento vocale in Java (Windows o Linux)
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: fmegen
ms.openlocfilehash: 00603c467ec96e52fc2b7745263153a68d20f584
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47053963"
---
# <a name="quickstart-recognize-speech-in-java-on-windows-or-linux-by-using-the-speech-sdk"></a>Avvio rapido: Riconoscimento vocale in Java su Windows o Linux con Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In questo articolo, si crea un'applicazione console Java usando [Speech SDK](speech-sdk.md). Trascrizione riconoscimento vocale in tempo reale dal microfono del PC. L'applicazione è compilata con il pacchetto Speech SDK Maven ed Eclipse Java IDE (v4.8) su Windows a 64 bit o Ubuntu Linux 16.04. Viene eseguito su un ambiente Java 8 runtime a 64 bit (JRE).

> [!NOTE]
> Per Speech Device SDK e il dispositivo Roobo, vedere [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Prerequisiti

È necessaria una chiave di sottoscrizione del Servizio di riconoscimento vocale per completare la guida introduttiva. È possibile ottenerne una gratuitamente. Vedere [Prova il servizio di riconoscimento vocale gratuitamente](get-started.md) per i dettagli.


## <a name="create-and-configure-project"></a>Creare e configurare un progetto

Se si utilizza Ubuntu 16.04, prima di avviare Eclipse eseguire i seguenti comandi per assicurarsi che i pacchetti richiesti siano installati.

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

1. Avviare Eclipse.

1. Nell’utilità di avvio di Eclipse, nel campo **Area di lavoro**, inserire il nome di una nuova directory dell’area di lavoro. Selezionare quindi **Avvio**.

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

   * Alla fine del file, prima del tag di chiusura `</project>`, creare una sezione repositories con un riferimento al repository Maven per Speech SDK, come mostrato di seguito:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * Aggiungere anche una sezione dependencies con la versione 1.0.0 di Speech SDK come dipendenza:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

   * Salvare le modifiche.

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

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

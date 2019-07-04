---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 5/23/2019
ms.author: travisw
ms.openlocfilehash: a8118d80e85d562fa4137ed1f1844e6bf9f1793e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485040"
---
1. Avviare Android Studio e selezionare **Start a new Android Studio project** (Avvia un nuovo progetto di Android Studio) nella schermata iniziale.

    ![Screenshot della finestra iniziale di Android Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Nella procedura guidata **Choose your project** (Scegliere il progetto) visualizzata, selezionare **Phone and Tablet** (Telefono e tablet) e quindi **Empty Activity** (Attività vuota) nella casella di selezione dell'attività. Selezionare **Avanti**.

   ![Screenshot della procedura guidata Choose your project](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. Nella schermata **Configure your project** (Configurare il progetto) immettere **Quickstart** (Avvio rapido) come **nome**, **samples.speech.cognitiveservices.microsoft.com** come **Package name** (Nome pacchetto) e scegliere una directory di progetto. Per **Minimum API level** (Livello API minimo) scegliere **API 23: Android 6.0 (Marshmallow)** . Lasciare deselezionate tutte le altre caselle di controllo e scegliere **Finish** (Fine).

   ![Screenshot della procedura guidata Configure your project](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Android Studio richiede qualche momento per preparare il nuovo progetto Android. Successivamente, configurare il progetto per conoscere Speech SDK e usare Java 8.

[!INCLUDE [License Notice](cognitive-services-speech-service-license-notice.md)]

La versione corrente di Speech SDK di Servizi cognitivi è `1.6.0`.

Speech SDK per Android viene inserito in un pacchetto come una [AAR (libreria Android)](https://developer.android.com/studio/projects/android-library), che include le librerie necessarie, nonché le autorizzazioni Android richieste.
È ospitato in un repository Maven in https:\//csspeechstorage.blob.core.windows.net/maven/.

Di seguito viene descritto come configurare il progetto per usare Speech SDK. Aprire la finestra Struttura del progetto, scegliendo **File** > **Struttura del progetto** dalla barra del menu Android Studio. Nella finestra Struttura del progetto, apportare le modifiche seguenti:

1. Nell'elenco sul lato sinistro della finestra, selezionare **Progetto**. Modificare le impostazioni del **Repository predefinito della libreria** aggiungendo una virgola e l'URL del repository Maven racchiuso tra virgolette singole. 'https:\//csspeechstorage.blob.core.windows.net/maven/'

   ![Screenshot della finestra della struttura del progetto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Nella stessa schermata, sul lato sinistro, selezionare **app**. Quindi selezionare la scheda **Dipendenze** nella parte superiore della finestra. Selezionare il segno verde più (+) e scegliere **Dipendenza della libreria** dal menu a discesa.

   ![Screenshot della finestra della struttura del progetto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. Nella finestra visualizzata immettere il nome e la versione di Speech SDK per Android, `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0`. Selezionare **OK**.
   A questo punto Speech SDK viene aggiunto all'elenco delle dipendenze come illustrato di seguito:

   ![Screenshot della finestra della struttura del progetto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Fare clic sulla scheda **Proprietà**. Selezionare **1.8** sia per **Source Compability** (Compatibilità di origine) che per **Target Compatibility** (Compatibilità di destinazione).

   ![](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Selezionare **OK** per chiudere la finestra Struttura del progetto e applicare le modifiche apportate al progetto.

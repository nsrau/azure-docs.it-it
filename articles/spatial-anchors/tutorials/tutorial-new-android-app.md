---
title: Esercitazione - Istruzioni dettagliate per la creazione di una nuova app Android usando Ancoraggi nello spazio di Azure | Microsoft Docs
description: Questa esercitazione illustra come creare una nuova app Android usando Ancoraggi nello spazio di Azure.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 04/03/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 64f78b04d433c81302499addf15c3d19621bbf9f
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2019
ms.locfileid: "58919874"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Esercitazione: Istruzioni dettagliate per la creazione di una nuova app Android usando Ancoraggi nello spazio di Azure

Questa esercitazione illustra come creare una nuova app Android che integra funzionalità ARCore con Ancoraggi nello spazio di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, accertarsi di avere:

- Un computer Windows o macOS con <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3+</a> installato.
- Un dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">abilitato per lo sviluppo</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">idoneo per ARCore</a>.

## <a name="getting-started"></a>Introduzione

Avviare Android Studio. **Nella finestra iniziale di Android Studio** selezionare **Start a new Android Studio project** (Avvia un nuovo progetto di Android Studio). Oppure, se è già aperto un progetto, selezionare **File**->**New Project** (Nuovo progetto).

Nella finestra **Phone and Tablet** (Telefono e tablet) della finestra **Create New Project** (Crea nuovo progetto) scegliere **Empty Activity** (Attività vuota) e fare clic su **Avanti**. Quindi, in **Minimum API level** (Livello minimo API), scegliere `API 26: Android 8.0 (Oreo)` e assicurarsi che l'opzione **Language** (Linguaggio) sia impostata su `Java`. Si può scegliere di cambiare il nome e la posizione del progetto, oltre al nome del pacchetto. Lasciare inalterate le altre opzioni. Fare clic su **Fine**. Verrà eseguito il **programma di installazione del componente**. Al termine, fare clic su **Fine**. Dopo un certo tempo di elaborazione, Android Studio aprirà l'IDE.

## <a name="trying-it-out"></a>Prova pratica

Per testare la nuova app, connettere il dispositivo abilitato per lo sviluppo al computer di sviluppo con un cavo USB. Fare clic su **Run (Esegui)**->**Run 'app' (Esegui 'app')**. Nella finestra **Select Deployment Target** (Selezionare la destinazione della distribuzione) selezionare il dispositivo e fare clic su **OK**. Android Studio installa l'app nel dispositivo connesso e la avvia. Dovrebbe essere visualizzato "Hello World!" nell'app in esecuzione nel dispositivo. Fare clic su **Run (Esegui)**->**Stop 'app' (Arresta 'app')**.

## <a name="integrating-arcore"></a>Integrazione di _ARCore_

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> è la piattaforma di Google per lo sviluppo di esperienze di realtà aumentata, che consente al dispositivo di tenere traccia della propria posizione mentre si muove e inizia a riconoscere il mondo reale.

Modificare `app\manifests\AndroidManifest.xml` per includere le voci seguenti nel nodo `<manifest>` radice. Questo frammento di codice esegue alcune operazioni:

- Consente all'app di accedere alla fotocamera del dispositivo.
- Assicura inoltre che l'app sia visibile in Google Play Store solo per i dispositivi che supportano ARCore.
- Configura Google Play Store per scaricare e installare ARCore, se non è già installato, quando viene installata l'app.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Modificare `Gradle Scripts\build.gradle (Module: app)` per includere la voce seguente. Questo codice assicura che l'app sia destinata alla versione 1.5 di ARCore (il supporto per la versione 1.6 e successive in Ancoraggi nello spazio di Azure verrà aggiunto prossimamente). Dopo aver apportato questa modifica, è possibile che venga visualizzata una notifica di Gradle che chiede di eseguire la sincronizzazione. Fare clic su **Sync now** (Sincronizza ora).

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.5.0'
    ...
}
```

## <a name="integrating-sceneform"></a>Integrazione di _Sceneform_

<a href="https://developers.google.com/ar/develop/java/sceneform/" target="_blank">_Sceneform_</a> semplifica il rendering di scene 3D realistiche nelle app di realtà aumentata, senza la necessità di competenze in OpenGL.

Modificare `Gradle Scripts\build.gradle (Module: app)` per includere le voci seguenti. Questo codice consente all'app di usare i costrutti del linguaggio Java 8, richiesti da `Sceneform`. Assicura inoltre che l'app sia destinata alla versione 1.5 di `Sceneform`, perché deve corrispondere alla versione di ARCore usata dall'app. Dopo aver apportato questa modifica, è possibile che venga visualizzata una notifica di Gradle che chiede di eseguire la sincronizzazione. Fare clic su **Sync now** (Sincronizza ora).

```
android {
    ...

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.5.0'
    ...
}
```

Aprire `app\res\layout\activity_main.xml` e sostituire l'elemento `<TextView>` Hello Word con l'elemento ArFragment seguente. Questo codice causa la visualizzazione del feed della fotocamera sullo schermo, consentendo ad ARCore di tenere traccia della posizione del dispositivo mentre si muove.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Ridistribuire](#trying-it-out) l'app nel dispositivo per verificarla ancora una volta. Questa volta, dovrebbero essere chieste le autorizzazioni per la fotocamera. Dopo l'approvazione, sullo schermo dovrebbe essere visualizzato il feed della fotocamera.

## <a name="place-an-object-in-the-real-world"></a>Posizionare un oggetto nel mondo reale

Creare e posizionare un oggetto con l'app. Prima di tutto, aggiungere le istruzioni import seguenti in `app\java\<PackageName>\MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Quindi aggiungere le variabili membro seguenti nella classe `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Aggiungere poi il codice seguente nel metodo `onCreate()` di `app\java\<PackageName>\MainActivity`. Questo codice assocerà un listener, denominato `handleTap()`, che rileverà il tocco dell'utente sullo schermo del dispositivo. Se il tocco avviene su una superficie del mondo reale che è già stata riconosciuta dal rilevamento di ARCore, il listener verrà eseguito.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Infine, aggiungere il metodo `handleTap()` seguente, che collegherà tutti gli elementi tra loro. Viene creata una sfera, che viene posizionata nel punto toccato. Inizialmente la sfera sarà nera, perché `this.recommendedSessionProgress` è impostato su zero per il momento. Questo valore verrà modificato in seguito.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-171,174-182,198-199)]

[Ridistribuire](#trying-it-out) l'app nel dispositivo per verificarla ancora una volta. Questa volta, è possibile muovere il dispositivo per fare in modo che ARCore inizi a riconoscere l'ambiente. Quindi, toccare lo schermo per creare e posizionare la sfera nera sopra la superficie scelta.

## <a name="attach-a-local-azure-spatial-anchor"></a>Collegare un ancoraggio nello spazio di Azure locale

Modificare `Gradle Scripts\build.gradle (Module: app)` per includere la voce seguente. Questo codice assicura che l'app sia destinata ad Ancoraggi nello spazio di Azure versione 1.0.2. In ogni caso, il riferimento a qualsiasi versione recente di Ancoraggi nello spazio di Azure dovrebbe funzionare.

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[1.0.2]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[1.0.2]"
    ...
}
```

Fare clic con il pulsante destro del mouse su `app\java\<PackageName>`->**New (Nuovo)**->**Java Class (Classe Java)**. Impostare **Name** (Nome) su _MyFirstApp_ e **Superclass** (Superclasse) su _android.app.Application_. Lasciare inalterate le altre opzioni. Fare clic su **OK**. Verrà creato un file denominato `MyFirstApp.java`. Aggiungere l'istruzione import seguente:

```java
import com.microsoft.CloudServices;
```

Aggiungere quindi il codice seguente all'interno della nuova classe `MyFirstApp`, che assicura l'inizializzazione di Ancoraggi nello spazio di Azure con il contesto dell'applicazione.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Modificare ora `app\manifests\AndroidManifest.xml` per includere la voce seguente nel nodo `<application>` radice. Questo codice associa la classe Application creata nell'app.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

Tornare in `app\java\<PackageName>\MainActivity` e aggiungere le istruzioni import seguenti:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Quindi aggiungere le variabili membro seguenti nella classe `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Aggiungere poi il metodo `initializeSession()` seguente nella classe `mainActivity`. Quando viene chiamato, questo metodo assicura che durante l'avvio dell'app venga creata e inizializzata correttamente una sessione di Ancoraggi nello spazio di Azure.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,146)]

Associare quindi il metodo `initializeSession()` al metodo `onCreate()`. Assicurarsi inoltre che i fotogrammi provenienti dal feed della fotocamera vengano inviati all'SDK di Ancoraggi nello spazio di Azure per l'elaborazione.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Infine, aggiungere il codice seguente nel metodo `handleTap()`. Il codice collega un ancoraggio nello spazio di Azure locale alla sfera nera posizionata nel mondo reale.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-182,198-199&highlight=12-13)]

[Ridistribuire](#trying-it-out) ancora una volta l'app. Muovere il dispositivo, toccare lo schermo e posizionare una sfera nera. Questa volta, però, il codice crea e collega un ancoraggio nello spazio di Azure locale alla sfera.

Prima di procedere, sarà necessario creare un identificatore e una chiave dell'account di Ancoraggi nello spazio di Azure, se non è già stato fatto. Per ottenerli, seguire la sezione seguente.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Caricare l'ancoraggio locale nel cloud

Dopo aver ottenuto l'identificatore e la chiave dell'account di Ancoraggi nello spazio di Azure, è possibile tornare in `app\java\<PackageName>\MainActivity` e aggiungervi le istruzioni import seguenti:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Quindi aggiungere le variabili membro seguenti nella classe `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Aggiungere poi il codice seguente nel metodo `initializeSession()`. Questo codice consente prima di tutto all'app di monitorare lo stato di avanzamento dell'SDK Ancoraggi nello spazio di Azure mentre raccoglie i fotogrammi dal feed della fotocamera. Durante questo processo, il colore della sfera inizia a cambiare dal nero originale al grigio. Quindi diventerà bianca quando sarà stato raccolto un numero di fotogrammi sufficiente per inviare l'ancoraggio nel cloud. Il codice fornirà poi le credenziali necessarie per comunicare con il back-end del cloud. Ecco dove configurare l'app per usare l'identificatore e la chiave dell'account. Questi dati sono stati copiati in un editor di testo durante la [configurazione della risorsa Ancoraggi nello spazio](#create-a-spatial-anchors-resource).

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-146&highlight=11-36)]

Aggiungere poi il metodo `uploadCloudAnchorAsync()` seguente nella classe `mainActivity`. Quando viene chiamato, questo metodo aspetta in modo asincrono finché non viene raccolto un numero di fotogrammi sufficiente dal dispositivo. Non appena ciò avviene, trasforma il colore della sfera in giallo e quindi avvia il caricamento dell'ancoraggio nello spazio di Azure locale nel cloud. Al termine del caricamento, il codice restituisce un identificatore di ancoraggio.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Infine, associare tutti gli elementi tra loro. Aggiungere il codice seguente nel metodo `handleTap()`. Il codice richiama il metodo `uploadCloudAnchorAsync()` non appena verrà creata la sfera. Quando il metodo restituisce il risultato, il codice seguente esegue l'aggiornamento finale della sfera, cambiandone il colore in blu.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=150-158,170-199&highlight=24-37)]

[Ridistribuire](#trying-it-out) ancora una volta l'app. Muovere il dispositivo, toccare lo schermo e posizionare la sfera. Questa volta, però, il colore della sfera cambia dal nero verso il bianco, mentre vengono raccolti i fotogrammi dalla fotocamera. Quando il numero di fotogrammi è sufficiente, la sfera diventerà gialla e verrà avviato il caricamento nel cloud. Al termine del caricamento, la sfera diventerà blu. Facoltativamente, è anche possibile usare la finestra `Logcat` all'interno di Android Studio per monitorare i messaggi di log inviati dall'app, ad esempio lo stato di avanzamento della sessione durante l'acquisizione dei fotogrammi e l'identificatore dell'ancoraggio restituito dal cloud al termine del caricamento.

## <a name="locate-your-cloud-spatial-anchor"></a>Individuare l'ancoraggio nello spazio nel cloud

Dopo aver caricato l'ancoraggio nel cloud, è possibile provare a individuarlo di nuovo. Prima di tutto, aggiungere le istruzioni import seguenti nel codice.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Quindi, aggiungere il codice seguente nel metodo `handleTap()`. Il codice consentirà di:

- Rimuovere l'attuale sfera blu dallo schermo.
- Inizializzare di nuovo la sessione di Ancoraggi nello spazio di Azure. Questa operazione assicura che l'ancoraggio da individuare proviene dal cloud e non è l'ancoraggio locale creato.
- Eseguire una query per trovare l'ancoraggio caricato nel cloud.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Associare ora il codice che verrà richiamato quando verrà individuato l'ancoraggio tramite la query. Aggiungere il codice seguente nel metodo `initializeSession()`. Questo frammento di codice crea e posiziona una sfera verde quando verrà individuato l'ancoraggio nello spazio nel cloud. Consente inoltre di toccare di nuovo lo schermo, quindi sarà possibile ripetere ancora una volta l'intero scenario, ossia creare un altro ancoraggio locale, caricarlo e individuarlo.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

L'operazione è terminata. [Ridistribuire](#trying-it-out) l'app un'ultima volta per provare l'intero scenario completato. Muovere il dispositivo e posizionare la sfera nera. Quindi continuare a muovere il dispositivo per acquisire i fotogrammi della fotocamera finché la sfera non diventa gialla. L'ancoraggio locale verrà caricato e la sfera diventerà blu. Infine, toccare ancora una volta lo schermo in modo da rimuovere l'ancoraggio locale ed eseguire una query per trovare la controparte nel cloud. Continuare a muovere il dispositivo finché non viene individuato l'ancoraggio nello spazio nel cloud. Dovrebbe comparire una sfera verde nella posizione corretta ed è possibile pulire e ripetere di nuovo l'intero scenario.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]

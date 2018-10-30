---
title: "Esercitazione: Rilevare e incorniciare i visi in un'immagine con Android SDK"
titleSuffix: Azure Cognitive Services
description: In questa esercitazione viene creata una semplice app Android che usa l'API Viso per rilevare e incorniciare i visi in un'immagine.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 07/12/2018
ms.author: pafarley
ms.openlocfilehash: 99b2734745df722f45443b5347ae6dd054c8aa31
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957038"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Esercitazione: Creare un'app Android per rilevare e incorniciare i visi in un'immagine

In questa esercitazione viene creata una semplice applicazione Android che usa la libreria di classi Java del servizio Viso per rilevare i visi umani in un'immagine. L'applicazione mostra un'immagine selezionata con ogni viso rilevato incorniciato da un rettangolo. Il codice di esempio completo è disponibile su GitHub in [Detect and frame faces in an image on Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample) (Rilevare e incorniciare i visi in un'immagine in Android).

![Screenshot di Android di una foto con visi incorniciati da un rettangolo rosso](../Images/android_getstarted2.1.PNG)

Questa esercitazione illustra come:

> [!div class="checklist"]
> - Creare un'applicazione Android
> - Installare la libreria client del servizio Viso
> - Usare la libreria client per rilevare i visi in un'immagine
> - Tracciare una cornice intorno a ogni viso rilevato

## <a name="prerequisites"></a>Prerequisiti

- Per eseguire l'esempio è necessaria una sottoscrizione. È possibile ottenere le chiavi di sottoscrizione della versione di valutazione gratuita da [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
- [Android Studio](https://developer.android.com/studio/) con almeno SDK 22 (richiesto dalla libreria client del servizio Viso).
- La libreria client [com.microsoft.projectoxford:face:1.4.3](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.microsoft.projectoxford%22) del servizio Viso disponibile su Maven. Non è necessario scaricare il pacchetto. Le istruzioni di installazione sono disponibili più avanti.

## <a name="create-the-project"></a>Creare il progetto

Creare il progetto di applicazione Android seguendo questa procedura:

1. Aprire Android Studio. Questa esercitazione usa Android Studio 3.1.
1. Selezionare **Start a new Android Studio project** (Avvia un nuovo progetto Android Studio).
1. Nella schermata **Create Android Project** (Crea progetto Android) modificare i campi predefiniti, se necessario, quindi fare clic su **Next** (Avanti).
1. Nella schermata **Target Android Devices** (Dispositivi Android di destinazione) usare il selettore a discesa per scegliere **API 22** o superiore, quindi fare clic su **Next** (Avanti).
1. Selezionare **Empty Activity** (Attività vuota) e quindi fare clic su **Next** (Avanti).
1. Deselezionare l'opzione **Backwards Compatibility** (Compatibilità con le versioni precedenti) e quindi fare clic su **Finish** (Fine).

## <a name="create-the-ui-for-selecting-and-displaying-the-image"></a>Creare l'interfaccia utente per la selezione e la visualizzazione dell'immagine

Aprire *activity_main.xml*. Verrà visualizzato l'editor di layout. Selezionare la scheda **Text** (Testo) e quindi sostituire i contenuti con il codice seguente.

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <ImageView
        android:layout_width="match_parent"
        android:layout_height="fill_parent"
        android:id="@+id/imageView1"
        android:layout_above="@+id/button1"
        android:contentDescription="Image with faces to analyze"/>

    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Browse for face image"
        android:id="@+id/button1"
        android:layout_alignParentBottom="true"/>
</RelativeLayout>
```

Aprire *MainActivity.java* e quindi sostituire tutti gli elementi tranne la prima istruzione `package` con il codice seguente.

Il codice imposta un gestore dell'evento su `Button` che avvia una nuova attività per consentire all'utente di selezionare un'immagine. Dopo la selezione, l'immagine viene visualizzata in `ImageView`.

```java
import java.io.*;
import android.app.*;
import android.content.*;
import android.net.*;
import android.os.*;
import android.view.*;
import android.graphics.*;
import android.widget.*;
import android.provider.*;

public class MainActivity extends Activity {
    private final int PICK_IMAGE = 1;
    private ProgressDialog detectionProgressDialog;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            Button button1 = (Button)findViewById(R.id.button1);
            button1.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
                intent.setType("image/*");
                startActivityForResult(Intent.createChooser(
                        intent, "Select Picture"), PICK_IMAGE);
            }
        });

        detectionProgressDialog = new ProgressDialog(this);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                        getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);

                // Uncomment
                //detectAndFrame(bitmap);
                } catch (IOException e) {
                    e.printStackTrace();
                }
        }
    }
}
```

Ora l'app può cercare una foto e visualizzarla in una finestra simile all'immagine riportata di seguito.

![Schermata di Android di una foto con visi](../Images/android_getstarted1.1.PNG)

## <a name="configure-the-face-client-library"></a>Configurare la libreria client per il servizio Viso

L'API Viso è un'API cloud che è possibile chiamare tramite richieste HTTPS. Questa esercitazione usa la libreria client del servizio Viso, che incapsula queste richieste Web per semplificare le attività dell'utente.

Nel riquadro **Project** (Progetto) usare il selettore a discesa per selezionare **Android**. Espandere **Gradle Scripts** (Script Gradle) e quindi aprire *build.gradle (Module: app)* (build.gradle - Modulo: app).

Aggiungere una dipendenza per la libreria client del servizio Viso, `com.microsoft.projectoxford:face:1.4.3`, come mostrato nello screenshot più avanti, quindi fare clic su **Sync Now** (Sincronizza adesso).

![Screenshot di Android Studio per il file build.gradle dell'app](../Images/face-tut-java-gradle.png)

Aprire **MainActivity.java** e aggiungere le direttive import seguenti alla fine del file:

```java
import com.microsoft.projectoxford.face.*;
import com.microsoft.projectoxford.face.contract.*;
```

## <a name="add-the-face-client-library-code"></a>Aggiungere il codice della libreria client del servizio Viso

Inserire il codice seguente nella classe `MainActivity`, sopra il metodo `onCreate`:

```java
private final String apiEndpoint = "<API endpoint>";
private final String subscriptionKey = "<Subscription Key>";

private final FaceServiceClient faceServiceClient =
        new FaceServiceRestClient(apiEndpoint, subscriptionKey);
```

Sostituire `<API endpoint>` con l'endpoint API assegnato alla chiave. Le chiavi di sottoscrizione per la versione di valutazione gratuita vengono generate nell'area **westcentralus**. Se si usa una chiave di sottoscrizione per la versione di valutazione gratuita, l'istruzione sarà quindi simile alla seguente:

```java
apiEndpoint = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";
```

Sostituire `<Subscription Key>` con la chiave di sottoscrizione. Ad esempio:

```java
subscriptionKey = "0123456789abcdef0123456789ABCDEF"
```

Nel riquadro **Project** (Progetto) espandere **app**, quindi **manifests** (manifesti) e infine aprire *AndroidManifest.xml*.

Inserire l'elemento seguente come figlio diretto dell'elemento `manifest`:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Compilare il progetto e verificare se sono presenti errori. Ora è possibile chiamare il servizio Viso.

## <a name="upload-an-image-to-detect-faces"></a>Caricare un'immagine per rilevare i visi

Il modo più semplice per rilevare i visi consiste nel chiamare il metodo `FaceServiceClient.detect`. Questo metodo esegue il wrapping del metodo dell'API [Rileva](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) e restituisce una matrice di `Face`.

Ogni elemento `Face` restituito è racchiuso in un rettangolo che ne indica la posizione, combinata con una serie di attributi del viso facoltativi. In questo esempio sono necessarie solo le posizioni dei visi.

Se si verifica un errore, un elemento `AlertDialog` mostra la causa sottostante.

Inserire i metodi seguenti nella classe `MainActivity`.

```java
// Detect faces by uploading a face image.
// Frame faces after detection.
private void detectAndFrame(final Bitmap imageBitmap) {
    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
    ByteArrayInputStream inputStream =
            new ByteArrayInputStream(outputStream.toByteArray());

    AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                String exceptionMessage = "";

                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0],
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null          // returnFaceAttributes:
                                /* new FaceServiceClient.FaceAttributeType[] {
                                    FaceServiceClient.FaceAttributeType.Age,
                                    FaceServiceClient.FaceAttributeType.Gender }
                                */
                        );
                        if (result == null){
                            publishProgress(
                                    "Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(String.format(
                                "Detection Finished. %d face(s) detected",
                                result.length));
                        return result;
                    } catch (Exception e) {
                        exceptionMessage = String.format(
                                "Detection failed: %s", e.getMessage());
                        return null;
                    }
                }

                @Override
                protected void onPreExecute() {
                    //TODO: show progress dialog
                }
                @Override
                protected void onProgressUpdate(String... progress) {
                    //TODO: update progress
                }
                @Override
                protected void onPostExecute(Face[] result) {
                    //TODO: update face frames
                }
            };

    detectTask.execute(inputStream);
}

private void showError(String message) {
    new AlertDialog.Builder(this)
    .setTitle("Error")
    .setMessage(message)
    .setPositiveButton("OK", new DialogInterface.OnClickListener() {
            public void onClick(DialogInterface dialog, int id) {
        }})
    .create().show();
}
```

## <a name="frame-faces-in-the-image"></a>Incorniciare i visi nell'immagine

Inserire il metodo dell'helper seguente nella classe `MainActivity`. Questo metodo traccia un rettangolo intorno a ogni viso rilevato.

```java
private static Bitmap drawFaceRectanglesOnBitmap(
        Bitmap originalBitmap, Face[] faces) {
    Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
    Canvas canvas = new Canvas(bitmap);
    Paint paint = new Paint();
    paint.setAntiAlias(true);
    paint.setStyle(Paint.Style.STROKE);
    paint.setColor(Color.RED);
    paint.setStrokeWidth(10);
    if (faces != null) {
        for (Face face : faces) {
            FaceRectangle faceRectangle = face.faceRectangle;
            canvas.drawRect(
                    faceRectangle.left,
                    faceRectangle.top,
                    faceRectangle.left + faceRectangle.width,
                    faceRectangle.top + faceRectangle.height,
                    paint);
        }
    }
    return bitmap;
}
```

Completare i metodi `AsyncTask`, indicati dai commenti `TODO`, nel metodo `detectAndFrame`. Se l'operazione ha esito positivo, l'immagine selezionata viene visualizzata con i visi incorniciati in `ImageView`.

```java
@Override
protected void onPreExecute() {
    detectionProgressDialog.show();
}
@Override
protected void onProgressUpdate(String... progress) {
    detectionProgressDialog.setMessage(progress[0]);
}
@Override
protected void onPostExecute(Face[] result) {
    detectionProgressDialog.dismiss();
    if(!exceptionMessage.equals("")){
        showError(exceptionMessage);
    }
    if (result == null) return;
    ImageView imageView = findViewById(R.id.imageView1);
    imageView.setImageBitmap(
            drawFaceRectanglesOnBitmap(imageBitmap, result));
    imageBitmap.recycle();
}
```

Nel metodo `onActivityResult` rimuovere infine il commento dalla chiamata al metodo `detectAndFrame`, come mostrato di seguito.

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
        Uri uri = data.getData();
        try {
            Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                    getContentResolver(), uri);
            ImageView imageView = findViewById(R.id.imageView1);
            imageView.setImageBitmap(bitmap);

            // Uncomment
            detectAndFrame(bitmap);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## <a name="run-the-app"></a>Esecuzione dell'app

Eseguire l'applicazione e quindi cercare un'immagine con un viso. Attendere qualche secondo per consentire al servizio Viso di rispondere. Successivamente, si otterrà un risultato simile all'immagine riportata di seguito:

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a>Summary

In questa esercitazione si è appreso il processo di base per l'uso del servizio Viso ed è stata creata un'applicazione per visualizzare visi incorniciati in un'immagine.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul rilevamento e sull'uso dei punti di riferimento del viso.

> [!div class="nextstepaction"]
> [Come rilevare visi nelle immagini](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)

Esplorare le API Viso usate per rilevare i visi e i rispettivi attributi, tra cui posa, sesso, età, posizione della testa, presenza di barba e baffi e occhiali.

> [!div class="nextstepaction"]
> [Informazioni di riferimento per l'API Viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
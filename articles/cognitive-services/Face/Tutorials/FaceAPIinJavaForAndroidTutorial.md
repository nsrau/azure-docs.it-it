---
title: Esercitazione per l'API Viso in Java per Android | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Creare una semplice app di Android che usa l'API Viso di Servizi cognitivi per rilevare e incorniciare i visi umani in un'immagine.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 5164a261d482d0cca3842a973d2109b17999bd25
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377812"
---
# <a name="getting-started-with-face-api-in-java-for-android-tutorial"></a>Esercitazione introduttiva per l'API Viso in Java per Android

In questa esercitazione verrà illustrato come creare e sviluppare una semplice applicazione Android che richiama l'API Viso per rilevare i visi umani in un'immagine. L'applicazione mostra il risultato incorniciando i visi rilevati.

![GettingStartedAndroid](../Images/android_getstarted2.1.PNG)

## <a name="preparation"></a> Preparativi

Per usare questa esercitazione, è necessario soddisfare i prerequisiti seguenti:

- Installazione di Android Studio e SDK
- Dispositivo Android (facoltativo per i test).

## <a name="step1"></a>Passaggio 1: Sottoscrivere l'API Viso e ottenere la chiave di sottoscrizione

Prima di usare l'API Viso, è necessario registrarsi per sottoscrivere l'API Viso nel portale di Servizi cognitivi Microsoft. Vedere [Sottoscrizioni](https://azure.microsoft.com/try/cognitive-services/). In questa esercitazione è possibile usare entrambe le chiavi primaria e secondaria.

## <a name="step2"></a>Passaggio 2: Creare il framework dell'applicazione

In questo passaggio si creerà un progetto di applicazione Android per implementare l'interfaccia utente di base per prelevare e visualizzare un'immagine. Seguire semplicemente le istruzioni riportate di seguito: 

1. Aprire Android Studio.
2. Scegliere **New Project (Nuovo progetto)** dal menu File.
3. Assegnare all'applicazione il nome **MyFirstApp** e quindi fare clic su Next (Avanti). 

    ![GettingStartAndroidNewProject](../Images/AndroidNewProject.png)

4. Scegliere la piattaforma di destinazione come richiesto e quindi fare clic su Next (Avanti). 

    ![GettingStartAndroidNewProject2](../Images/AndroidNewProject2.png)

5. Selezionare **Basic Activity** (Attività di base) e quindi fare clic su Next (Avanti).
6. Assegnare il nome indicato di seguito all'attività e quindi fare clic su Finish (Fine). 

    ![GettingStartAndroidNewProject4](../Images/AndroidNewProject4.png)

7. Aprire **activity_main.xml**. Verrà visualizzato l'editor di layout di questa attività.
8. Visualizzare il file di origine di testo e quindi modificare il layout dell'attività come indicato di seguito:

    ```xml
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
        android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
        android:paddingRight="@dimen/activity_horizontal_margin"
        android:paddingTop="@dimen/activity_vertical_margin"
        android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">
     
        <ImageView
            android:layout_width="match_parent"
            android:layout_height="fill_parent"
            android:id="@+id/imageView1"
            android:layout_above="@+id/button1" />
    
        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Browse"
            android:id="@+id/button1"
            android:layout_alignParentBottom="true" />
    </RelativeLayout>
    ```  

9. Aprire **MainActivity.java** e inserire le direttive import seguenti all'inizio del file:

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
    ```
      
    Modificare poi la classe come indicato di seguito:  
    
    ```java
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
                Intent gallIntent = new Intent(Intent.ACTION_GET_CONTENT);
                gallIntent.setType("image/*");
                startActivityForResult(Intent.createChooser(gallIntent, "Select Picture"), PICK_IMAGE);
            }
        });
         
        detectionProgressDialog = new ProgressDialog(this);
    }
    
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
                } catch (IOException e) {
                e.printStackTrace();
                }
        }
    }
    ```

Ora l'app può cercare una foto dalla raccolta e visualizzarla in una finestra simile all'immagine riportata di seguito:

![GettingStartAndroidUI](../Images/android_getstarted1.1.PNG)

## <a name="step3"></a>Passaggio 3: Configurare la libreria client dell'API Viso

L'API Viso è un'API cloud che è possibile richiamare tramite richieste HTTPS. Per offrire un modo più efficace di usare l'API Viso nelle applicazioni della piattaforma .NET, è disponibile anche una libreria client per incapsulare le richieste Web. In questo esempio si usa la libreria client per semplificare il lavoro. 

Per configurare la libreria client, seguire queste istruzioni: 

1. Individuare il file **build.gradle** di primo livello del progetto dal pannello del progetto illustrato nell'esempio. Si noti che esistono vari altri file **build.gradle** nella struttura del progetto e che è necessario aprire il file **build.gradle** di primo livello inizialmente.
2. Aggiungere **mavenCentral()** ai repository dei progetti. È anche possibile usare jcenter(), ovvero il repository predefinito di Android Studio, dato che jcenter() è un superset di mavenCentral().  

```
    allprojects {
        repositories {
            ...
            mavenCentral()
        }
    }
```

3. Aprire il file **build.gradle** nel progetto 'app'.
4. Aggiungere una dipendenza per la libreria client archiviata nel repository centrale Maven:

```
    dependencies {  
        ...  
        implementation 'com.microsoft.projectoxford:face:1.4.3'  
    }
```

5. Aprire **MainActivity.java** nel progetto 'app' e inserire le direttive import seguenti: 
    
    ```java
    import com.microsoft.projectoxford.face.*;  
    import com.microsoft.projectoxford.face.contract.*;  
    ```
    
   Inserire quindi il codice seguente nella classe:

    ```java
    private FaceServiceClient faceServiceClient = new FaceServiceRestClient("your API endpoint", "<Subscription Key>");
    ```

   Sostituire il primo parametro sopra con l'endpoint dell'API assegnato alla chiave nel passaggio 1. Ad esempio: 
   
        https://eastus2.api.cognitive.microsoft.com/face/v1.0
   
   Sostituire il secondo parametro con la chiave di sottoscrizione ottenuta nel passaggio 1.
   
6. Aprire il file **AndroidManifest.xml** nel progetto 'app'. Inserire l'elemento seguente come figlio dell'elemento **manifest**:  

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />  
    ```

7. A questo punto si è pronti per chiamare l'API Viso dall'applicazione. 

## <a name="step4"></a>Passaggio 4: Caricare immagini per rilevare i visi

Il modo più diretto di rilevare i visi consiste nel richiamare l'API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Viso - Rilevamento) caricando direttamente il file immagine. Quando si usa la libreria client, questa operazione può essere eseguita usando il metodo asincrono **DetectAsync** della classe **FaceServiceClient**. Ogni viso restituito è racchiuso in un rettangolo che ne indica la posizione, combinata con una serie di attributi del viso facoltativi. In questo esempio, è solo necessario recuperare la posizione del viso. È necessario inserire un metodo nella classe **MainActivity** per il rilevamento dei visi: 

```java

    // Detect faces by uploading face images
    // Frame faces after detection
    
    private void detectAndFrame(final Bitmap imageBitmap)
    {
        ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
        imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
        ByteArrayInputStream inputStream = 
            new ByteArrayInputStream(outputStream.toByteArray());
        AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0], 
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null           // returnFaceAttributes: a string like "age, gender"
                /* If you want value of FaceAttributes, try adding 4th argument like below.
                            new FaceServiceClient.FaceAttributeType[] {
                    FaceServiceClient.FaceAttributeType.Age,
                    FaceServiceClient.FaceAttributeType.Gender }
                */              
                        );
                        if (result == null)
                        {
                            publishProgress("Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(
                                String.format("Detection Finished. %d face(s) detected",
                                        result.length));
                        return result;
                    } catch (Exception e) {
                        publishProgress("Detection failed");
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
```

## <a name="step5"></a>Passaggio 5: Contrassegnare i visi nell'immagine

In questo ultimo passaggio si combinano tutti i passaggi precedenti e i visi rilevati vengono contrassegnati con una cornice nell'immagine. Prima di tutto, aprire **MainActivity.java** e inserire un metodo helper per disegnare rettangoli: 

```java
    private static Bitmap drawFaceRectanglesOnBitmap(Bitmap originalBitmap, Face[] faces) {
        Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
        Canvas canvas = new Canvas(bitmap);
        Paint paint = new Paint();
        paint.setAntiAlias(true);
        paint.setStyle(Paint.Style.STROKE);
        paint.setColor(Color.RED);
        int stokeWidth = 2;
        paint.setStrokeWidth(stokeWidth);
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

Completare ora le parti TODO nel metodo **detectAndFrame** per incorniciare i visi e segnalare lo stato.

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
        if (result == null) return;
        ImageView imageView = (ImageView)findViewById(R.id.imageView1);
        imageView.setImageBitmap(drawFaceRectanglesOnBitmap(imageBitmap, result));
        imageBitmap.recycle();
    }
```
 
Aggiungere infine una chiamata al metodo **detectAndFrame** dal metodo **onActivityResult**, come illustrato di seguito. 

```java
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
     
                // This is the new addition.
                // detectAndFrame(bitmap);
     
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

Eseguire questa applicazione e quindi cercare un'immagine che contenga un viso. Attendere alcuni secondi per consentire all'API cloud di rispondere. Successivamente, si otterrà un risultato simile all'immagine riportata di seguito: 

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a> Riepilogo

In questa esercitazione si è appreso il processo di base per l'uso dell'API Viso ed è stata creata un'applicazione per visualizzare visi incorniciati nelle immagini. Per altre informazioni sull'API Viso, vedere le guide pratiche e le [informazioni di riferimento sull'API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236). 

## <a name="related"></a> Esercitazioni correlate

- [Esercitazione introduttiva per l'API Viso in CSharp](FaceAPIinCSharpTutorial.md)
- [Esercitazione introduttiva per l'API Viso in Python](FaceAPIinPythonTutorial.md)

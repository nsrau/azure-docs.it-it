---
title: 'Esercitazione: API Visione artificiale in Java'
titlesuffix: Azure Cognitive Services
description: In questa esercitazione si esamina un'app Swing Java di base che usa l'API Visione artificiale in Servizi cognitivi di Azure. Si effettua il riconoscimento ottico dei caratteri (OCR), si creano anteprime e si usano le caratteristiche visive in un'immagine.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: tutorial
ms.author: kefre
ms.date: 09/21/2017
ms.openlocfilehash: cca35d031e860e014c8fd84b0daf6b4d60d18046
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985848"
---
# <a name="tutorial-computer-vision-api-java"></a>Esercitazione: API Visione artificiale in Java

Questa esercitazione illustra le funzionalità dell'API REST Visione artificiale di Servizi cognitivi di Azure.

Si esamina un'applicazione Swing Java che usa l'API REST Visione artificiale per effettuare il riconoscimento ottico dei caratteri (OCR), creare immagini di anteprima con ritaglio intelligente, nonché rilevare, classificare, contrassegnare con tag e descrivere le caratteristiche visive, inclusi i visi, in un'immagine. L'esempio consente di inviare un URL di un'immagine per l'analisi o l'elaborazione. È possibile usare questo esempio open source come modello per la creazione di un'app personalizzata in Java per l'uso dell'API REST Visione artificiale.

Questa esercitazione illustra come usare Visione artificiale per:

> [!div class="checklist"]
> * Analizzare un'immagine
> * Identificare un luogo di interesse naturale o artificiale in un'immagine
> * Identificare una celebrità in un'immagine
> * Creare un'anteprima di qualità da un'immagine
> * Leggere testo stampato in un'immagine
> * Leggere testo scritto a mano in un'immagine

L'applicazione Swing Java è già stata scritta, ma non include alcuna funzionalità. In questa esercitazione si aggiunge codice specifico dell'API REST Visione artificiale per completare le funzionalità dell'applicazione.

## <a name="prerequisites"></a>Prerequisiti

### <a name="platform-requirements"></a>Requisiti di piattaforma

Questa esercitazione è stata sviluppata usando l'ambiente di sviluppo integrato NetBeans, nello specifico la versione **Java SE** di NetBeans, che può essere [scaricata qui](https://netbeans.org/downloads/index.html).

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Effettuare la sottoscrizione dell'API Visione artificiale e ottenere una chiave di sottoscrizione 

Prima di creare l'esempio, è necessario effettuare la sottoscrizione dell'API Visione artificiale, che fa parte di Servizi cognitivi di Azure. Per informazioni dettagliate sulla sottoscrizione e sulla gestione delle chiavi, vedere [Sottoscrizioni](https://azure.microsoft.com/try/cognitive-services/). In questa esercitazione è possibile usare sia la chiave primaria sia quella secondaria. 

## <a name="download-the-tutorial-project"></a>Scaricare il progetto dell'esercitazione

1. Passare al repository [Cognitive Services Java Computer Vision Tutorial](https://github.com/Azure-Samples/cognitive-services-java-computer-vision-tutorial) (Esercitazione in Java su Visione artificiale di Servizi cognitivi).
1. Fare clic sul pulsante **Clone or download** (Clona o scarica).
1. Fare clic su **Download ZIP** (Scarica file ZIP) per scaricare un file ZIP del progetto dell'esercitazione.

Non è necessario estrarre il contenuto del file ZIP, in quanto NetBeans importa il progetto da questo file.

## <a name="import-the-tutorial-project"></a>Importare il progetto dell'esercitazione

Importare il file **cognitive-services-java-computer-vision-tutorial-master.zip** in NetBeans.

1. In NetBeans fare clic su **File** > **Import Project**(Importa progetto)  > **From ZIP...** (Da file ZIP). Viene visualizzata la finestra di dialogo **Import Project(s) from ZIP** (Importa progetti da file ZIP).
1. Nel campo **ZIP File** (File ZIP) fare clic sul pulsante **Browse** (Sfoglia) per trovare il file **cognitive-services-java-computer-vision-tutorial-master.zip**, quindi fare clic su **Open** (Apri).
1. Nella finestra di dialogo **Import Project(s) from ZIP** (Importa progetti da file ZIP) fare clic su **Import** (Importa).
1. Nel riquadro **Projects** (Progetti) espandere **ComputerVision** > **Source Packages** (Pacchetti di origine)  > **&lt;default package&gt;** (pacchetto predefinito). 
   Alcune versioni di NetBeans usano **src** al posto di **Source Packages** > **&lt;default package&gt;**. In questo caso espandere **src**.
1. Fare doppio clic su **MainFrame.java** per caricare il file nell'editor di NetBeans. Viene visualizzata la scheda **Design** (Progettazione) del file **MainFrame.java**.
1. Fare clic sulla scheda **Source** (Origine) per visualizzare il codice sorgente Java.

## <a name="build-and-run-the-tutorial-project"></a>Compilare ed eseguire il progetto dell'esercitazione

1. Premere **F6** per compilare ed eseguire l'applicazione dell'esercitazione.

    Nell'applicazione stessa fare clic su una scheda per visualizzare il riquadro della funzionalità corrispondente. I pulsanti hanno metodi vuoti, quindi non eseguono alcuna azione.

    Nella parte inferiore della finestra sono disponibili i campi **Subscription Key** (Chiave di sottoscrizione) e **Subscription Region** (Area sottoscrizione). Questi campi devono essere compilati con una chiave di sottoscrizione valida e con l'area corretta per tale chiave. Per ottenere una chiave di sottoscrizione, vedere [Sottoscrizioni](https://azure.microsoft.com/try/cognitive-services/). Se la chiave di sottoscrizione è stata ottenuta dalla versione di valutazione gratuita disponibile a quel collegamento, l'area predefinita **westcentralus** è l'area corretta.

1. Chiudere l'applicazione dell'esercitazione.

## <a name="add-the-tutorial-code"></a>Aggiungere il codice dell'esercitazione

L'applicazione Swing Java è configurata con sei schede. Ogni scheda illustra una funzione diversa di Visione artificiale (analisi, OCR e così via). Le sei sezioni dell'esercitazione non sono interdipendenti, quindi è possibile aggiungere un'unica sezione, tutte e sei le sezioni oppure solo un paio. Le sezioni possono inoltre essere aggiunte in qualsiasi ordine.

Di seguito sono riportati i requisiti iniziali.

## <a name="analyze-an-image"></a>Analizzare un'immagine

La funzionalità di analisi di Visione artificiale analizza un'immagine alla ricerca di più di 2.000 oggetti riconoscibili, esseri viventi, panorami e azioni. Una volta completata l'analisi, la funzionalità restituisce un oggetto JSON che descrive l'immagine con tag descrittivi, analisi del colore, didascalie e molto altro.

Per completare la funzionalità di analisi dell'applicazione dell'esercitazione, procedere come segue:

### <a name="analyze-step-1-add-the-event-handler-code-for-the-form-button"></a>Analisi, passaggio 1: aggiungere il codice del gestore dell'evento per il pulsante del modulo

Il metodo del gestore dell'evento **analyzeImageButtonActionPerformed** cancella il modulo, visualizza l'immagine specificata nell'URL e quindi chiama il metodo **AnalyzeImage** per analizzare l'immagine. Quando viene restituito il risultato di **AnalyzeImage**, il metodo visualizza la risposta in formato JSON nell'area di testo **Response** (Risposta), estrae la prima didascalia da **JSONObject** e visualizza la didascalia e il livello di attendibilità relativo alla correttezza della didascalia.

Copiare e incollare il codice seguente nel metodo **analyzeImageButtonActionPerformed**.

> [!NOTE]
> NetBeans non consente di incollare nella riga di definizione del metodo (```private void```) o nella parentesi graffa chiusa del metodo. Per copiare il codice, copiare le righe comprese tra la definizione del metodo e la parentesi graffa chiusa, quindi incollarle sul contenuto del metodo.

```java
    private void analyzeImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL analyzeImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        analyzeImage.setIcon(new ImageIcon());
        analyzeCaptionLabel.setText("");
        analyzeResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            analyzeImageUrl = new URL(analyzeImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(analyzeImageUrl);
            scaleAndShowImage(bImage, analyzeImage);
        } catch(IOException e) {
            analyzeResponseTextArea.setText("Error loading Analyze image: " + e.getMessage());
            return;
        }
        
        // Analyze the image.
        JSONObject jsonObj = AnalyzeImage(analyzeImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        analyzeResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("description") && jsonObj.getJSONObject("description").has("captions")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("description").getJSONArray("captions").getJSONObject(0);
            
            if (jsonCaption.has("text") && jsonCaption.has("confidence")) {
                
                analyzeCaptionLabel.setText("Caption: " + jsonCaption.getString("text") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

### <a name="analyze-step-2-add-the-wrapper-for-the-rest-api-call"></a>Analisi, passaggio 2: aggiungere il wrapper per la chiamata API REST

Il metodo **AnalyzeImage** esegue il wrapping della chiamata API REST per analizzare un'immagine. Il metodo restituisce un **JSONObject** che descrive l'immagine oppure **null** in caso di errore.

Copiare e incollare il metodo**AnalyzeImage** subito sotto il metodo **analyzeImageButtonActionPerformed**.

```java
    /**
     * Encapsulates the Microsoft Cognitive Services REST API call to analyze an image.
     * @param imageUrl: The string URL of the image to analyze.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject AnalyzeImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call for Analyze Image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseAnalyze;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color,Adult");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
 ```

### <a name="analyze-step-3-run-the-application"></a>Analisi, passaggio 3: eseguire l'applicazione

Premere **F6** per eseguire l'applicazione. Inserire la chiave di sottoscrizione nel campo **Chiave sottoscrizione** e verificare di usare l'area corretta in **Subscription Region** (Area sottoscrizione). Immettere un URL di un'immagine da analizzare, quindi fare clic sul pulsante **Analyze Image** (Analizza immagine) per analizzare un'immagine e visualizzare il risultato.

## <a name="recognize-a-landmark"></a>Riconoscere un punto di riferimento

La funzionalità relativa ai punti di riferimento di Visione artificiale analizza un'immagine per individuare punti di riferimento naturali e artificiali, ad esempio montagne o edifici famosi. Una volta completata l'analisi, questa funzionalità restituisce un oggetto JSON che identifica i punti di riferimento trovati nell'immagine.

Per completare la funzionalità relativa ai punti di riferimento dell'applicazione dell'esercitazione, procedere come segue:

### <a name="landmark-step-1-add-the-event-handler-code-for-the-form-button"></a>Punto di riferimento, passaggio 1: aggiungere il codice del gestore dell'evento per il pulsante del modulo

Il metodo del gestore dell'evento **landmarkImageButtonActionPerformed** cancella il modulo, visualizza l'immagine specificata nell'URL e quindi chiama il metodo **LandmarkImage** per analizzare l'immagine. Quando viene restituito il risultato di **LandmarkImage**, il metodo visualizza la risposta in formato JSON nell'area di testo **Response** (Risposta), quindi estrae il nome del primo luogo di interesse da **JSONObject** e lo visualizza nella finestra insieme al livello di attendibilità relativo alla corretta identificazione del luogo di interesse.

Copiare e incollare il codice seguente nel metodo **landmarkImageButtonActionPerformed**.

> [!NOTE]
> NetBeans non consente di incollare nella riga di definizione del metodo (```private void```) o nella parentesi graffa chiusa del metodo. Per copiare il codice, copiare le righe comprese tra la definizione del metodo e la parentesi graffa chiusa, quindi incollarle sul contenuto del metodo.

```java
    private void landmarkImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL landmarkImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        landmarkImage.setIcon(new ImageIcon());
        landmarkCaptionLabel.setText("");
        landmarkResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            landmarkImageUrl = new URL(landmarkImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(landmarkImageUrl);
            scaleAndShowImage(bImage, landmarkImage);
        } catch(IOException e) {
            landmarkResponseTextArea.setText("Error loading Landmark image: " + e.getMessage());
            return;
        }
        
        // Identify the landmark in the image.
        JSONObject jsonObj = LandmarkImage(landmarkImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        landmarkResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("landmarks")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("landmarks").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                landmarkCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

### <a name="landmark-step-2-add-the-wrapper-for-the-rest-api-call"></a>Punto di riferimento, passaggio 2: aggiungere il wrapper per la chiamata API REST

Il metodo **LandmarkImage** esegue il wrapping della chiamata API REST per analizzare un'immagine. Il metodo restituisce un **JSONObject** che descrive i luoghi di interesse trovati nell'immagine oppure **null** in caso di errore.

Copiare e incollare il metodo**LandmarkImage** subito sotto il metodo **landmarkImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify a landmark in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject LandmarkImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify a Landmark in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseLandmark;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

### <a name="landmark-step-3-run-the-application"></a>Punto di riferimento, passaggio 3: eseguire l'applicazione

Premere **F6** per eseguire l'applicazione. Inserire la chiave di sottoscrizione nel campo **Chiave sottoscrizione** e verificare di usare l'area corretta in **Subscription Region** (Area sottoscrizione). Fare clic sulla scheda **Landmark** (Luogo di interesse), immettere l'URL di un'immagine di un luogo di interesse, quindi fare clic sul pulsante **Analyze Image** (Analizza immagine) per analizzare l'immagine e visualizzare il risultato.

## <a name="recognize-celebrities"></a>Riconoscere le celebrità

La funzionalità relativa alle celebrità di Visione artificiale analizza un'immagine per individuare personaggi famosi. Una volta completata l'analisi, questa funzionalità restituisce un oggetto JSON che identifica le celebrità trovate nell'immagine.

Per completare la funzionalità relativa alle celebrità dell'applicazione dell'esercitazione, procedere come segue:

### <a name="celebrities-step-1-add-the-event-handler-code-for-the-form-button"></a>Celebrità, passaggio 1: aggiungere il codice del gestore dell'evento per il pulsante del modulo

Il metodo del gestore dell'evento **celebritiesImageButtonActionPerformed** cancella il modulo, visualizza l'immagine specificata nell'URL e quindi chiama il metodo **CelebritiesImage** per analizzare l'immagine. Quando viene restituito il risultato di **CelebritiesImage**, il metodo visualizza la risposta in formato JSON nell'area di testo **Response** (Risposta), quindi estrae il nome della prima celebrità da **JSONObject** e lo visualizza nella finestra insieme al livello di attendibilità relativo alla corretta identificazione della celebrità.

Copiare e incollare il codice seguente nel metodo **celebritiesImageButtonActionPerformed**.

> [!NOTE]
> NetBeans non consente di incollare nella riga di definizione del metodo (```private void```) o nella parentesi graffa chiusa del metodo. Per copiare il codice, copiare le righe comprese tra la definizione del metodo e la parentesi graffa chiusa, quindi incollarle sul contenuto del metodo.

```java
    private void celebritiesImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL celebritiesImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        celebritiesImage.setIcon(new ImageIcon());
        celebritiesCaptionLabel.setText("");
        celebritiesResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            celebritiesImageUrl = new URL(celebritiesImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(celebritiesImageUrl);
            scaleAndShowImage(bImage, celebritiesImage);
        } catch(IOException e) {
            celebritiesResponseTextArea.setText("Error loading Celebrity image: " + e.getMessage());
            return;
        }
        
        // Identify the celebrities in the image.
        JSONObject jsonObj = CelebritiesImage(celebritiesImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        celebritiesResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("celebrities")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("celebrities").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                celebritiesCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

### <a name="celebrities-step-2-add-the-wrapper-for-the-rest-api-call"></a>Celebrità, passaggio 2: aggiungere il wrapper per la chiamata API REST

Il metodo **CelebritiesImage** esegue il wrapping della chiamata API REST per analizzare un'immagine. Il metodo restituisce un **JSONObject** che descrive le celebrità trovate nell'immagine oppure **null** in caso di errore.

Copiare e incollare il metodo**CelebritiesImage** subito sotto il metodo **celebritiesImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify celebrities in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject CelebritiesImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseCelebrities;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

### <a name="celebrities-step-3-run-the-application"></a>Celebrità, passaggio 3: eseguire l'applicazione

Premere **F6** per eseguire l'applicazione. Inserire la chiave di sottoscrizione nel campo **Chiave sottoscrizione** e verificare di usare l'area corretta in **Subscription Region** (Area sottoscrizione). Fare clic sulla scheda **Celebrities** (Celebrità), immettere l'URL di un'immagine di una celebrità, quindi fare clic sul pulsante **Analyze Image** (Analizza immagine) per analizzare l'immagine e visualizzare il risultato.

## <a name="intelligently-generate-a-thumbnail"></a>Generare un'anteprima intelligente

La funzionalità di anteprima di Visione artificiale genera un'anteprima da un'immagine. Usando la funzionalità **Smart Crop** (Ritaglio intelligente), la funzionalità di anteprima identifica l'area di interesse in un'immagine e focalizza l'anteprima su quest'area, per generare immagini di anteprima esteticamente più gradevoli.

Per completare la funzionalità di anteprima dell'applicazione dell'esercitazione, procedere come segue:

### <a name="thumbnail-step-1-add-the-event-handler-code-for-the-form-button"></a>Anteprima, passaggio 1: aggiungere il codice del gestore dell'evento per il pulsante del modulo

Il metodo del gestore dell'evento **thumbnailImageButtonActionPerformed** cancella il modulo, visualizza l'immagine specificata nell'URL e quindi chiama il metodo **getThumbnailImage** per creare l'anteprima. Quando viene restituito il risultato di **getThumbnailImage**, il metodo visualizza l'anteprima generata.

Copiare e incollare il codice seguente nel metodo **thumbnailImageButtonActionPerformed**.

> [!NOTE]
> NetBeans non consente di incollare nella riga di definizione del metodo (```private void```) o nella parentesi graffa chiusa del metodo. Per copiare il codice, copiare le righe comprese tra la definizione del metodo e la parentesi graffa chiusa, quindi incollarle sul contenuto del metodo.

```java
    private void thumbnailImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL thumbnailImageUrl;
        JSONObject jsonError[] = new JSONObject[1];
        
        // Clear out the previous image, response, and thumbnail, if any.
        thumbnailSourceImage.setIcon(new ImageIcon());
        thumbnailResponseTextArea.setText("");
        thumbnailImage.setIcon(new ImageIcon());

        // Display the image specified in the text box.
        try {
            thumbnailImageUrl = new URL(thumbnailImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(thumbnailImageUrl);
            scaleAndShowImage(bImage, thumbnailSourceImage);
        } catch(IOException e) {
            thumbnailResponseTextArea.setText("Error loading image to thumbnail: " + e.getMessage());
            return;
        }
        
        // Get the thumbnail for the image.
        BufferedImage thumbnail = getThumbnailImage(thumbnailImageUrl.toString(), jsonError);
        
        // A non-null value indicates error.
        if (jsonError[0] != null) {
            // Format and display the JSON error.
            thumbnailResponseTextArea.setText(jsonError[0].toString(2));
            return;
        }
        
        // Display the thumbnail.
        if (thumbnail != null) {
            scaleAndShowImage(thumbnail, thumbnailImage);
        }
    }
```

### <a name="thumbnail-step-2-add-the-wrapper-for-the-rest-api-call"></a>Anteprima, passaggio 2: aggiungere il wrapper per la chiamata API REST

Il metodo **getThumbnailImage** esegue il wrapping della chiamata API REST per analizzare un'immagine. Il metodo restituisce un oggetto **BufferedImage** che contiene l'anteprima oppure **null** in caso di errore. Il messaggio di errore viene restituito nel primo elemento della matrice di stringhe **jsonError**.

Copiare e incollare il metodo **getThumbnailImage** seguente subito sotto il metodo **thumbnailImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to create a thumbnail for an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A BufferedImage containing the thumbnail, or null if a runtime error occurs. In the case
     * of an error, the error message will be returned in the first element of the jsonError string array.
     */
    private BufferedImage getThumbnailImage(String imageUrl, JSONObject[] jsonError) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseThumbnail;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity requestEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(requestEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200)
            {
                // Return the thumbnail.
                return ImageIO.read(entity.getContent());
            }
            else
            {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                jsonError[0] = new JSONObject(jsonString);
                return null;
            }
        }
        catch (Exception e)
        {
            String errorMessage = e.getMessage();
            System.out.println(errorMessage);
            jsonError[0] = new JSONObject(errorMessage);
            return null;
        }
    }
```

### <a name="thumbnail-step-3-run-the-application"></a>Anteprima, passaggio 3: eseguire l'applicazione

Premere **F6** per eseguire l'applicazione. Inserire la chiave di sottoscrizione nel campo **Chiave sottoscrizione** e verificare di usare l'area corretta in **Subscription Region** (Area sottoscrizione). Fare clic sulla scheda **Thumbnail** (Anteprima), immettere un URL di un'immagine, quindi fare clic sul pulsante **Generate Thumbnails** (Genera anteprime) per analizzare l'immagine e visualizzare il risultato.

## <a name="read-printed-text-ocr"></a>Leggere testo stampato (OCR)

La funzionalità di riconoscimento ottico dei caratteri (OCR) di Visione artificiale analizza un'immagine di testo stampato. Dopo il completamento dell'analisi, la funzionalità OCR restituisce un oggetto JSON che contiene il testo e la posizione del testo nell'immagine.

Per completare la funzionalità OCR dell'applicazione dell'esercitazione, procedere come segue:

### <a name="ocr-step-1-add-the-event-handler-code-for-the-form-button"></a>OCR, passaggio 1: aggiungere il codice del gestore dell'evento per il pulsante del modulo

Il metodo del gestore dell'evento **ocrImageButtonActionPerformed** cancella il modulo, visualizza l'immagine specificata nell'URL e quindi chiama il metodo **OcrImage** per analizzare l'immagine. Quando viene restituito il risultato di **OcrImage**, il metodo visualizza il testo rilevato in formato JSON nell'area di testo **Response** (Risposta).

Copiare e incollare il codice seguente nel metodo **ocrImageButtonActionPerformed**.

> [!NOTE]
> NetBeans non consente di incollare nella riga di definizione del metodo (```private void```) o nella parentesi graffa chiusa del metodo. Per copiare il codice, copiare le righe comprese tra la definizione del metodo e la parentesi graffa chiusa, quindi incollarle sul contenuto del metodo.

```java
    private void ocrImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL ocrImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        ocrImage.setIcon(new ImageIcon());
        ocrResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            ocrImageUrl = new URL(ocrImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(ocrImageUrl);
            scaleAndShowImage(bImage, ocrImage);
        } catch(IOException e) {
            ocrResponseTextArea.setText("Error loading OCR image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = OcrImage(ocrImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        ocrResponseTextArea.setText(jsonObj.toString(2));
    }
```

### <a name="ocr-step-2-add-the-wrapper-for-the-rest-api-call"></a>OCR, passaggio 2: aggiungere il wrapper per la chiamata API REST

Il metodo **OcrImage** esegue il wrapping della chiamata API REST per analizzare un'immagine. Il metodo restituisce un **JSONObject** dei dati JSON restituiti dalla chiamata oppure **null** in caso di errore.

Copiare e incollare il metodo**OcrImage** subito sotto il metodo **ocrImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject OcrImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseOcr;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("language", "unk");
            uriBuilder.setParameter("detectOrientation ", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

### <a name="ocr-step-3-run-the-application"></a>OCR, passaggio 3: eseguire l'applicazione

Premere **F6** per eseguire l'applicazione. Inserire la chiave di sottoscrizione nel campo **Chiave sottoscrizione** e verificare di usare l'area corretta in **Subscription Region** (Area sottoscrizione). Fare clic sulla scheda **OCR**, immettere l'URL di un'immagine di testo stampato, quindi fare clic sul pulsante **Read Image** (Leggi immagine) per analizzare l'immagine e visualizzare il risultato.

## <a name="read-handwritten-text-handwriting-recognition"></a>Leggere testo scritto a mano (riconoscimento della grafia)

La funzionalità di riconoscimento della grafia di Visione artificiale analizza un'immagine di testo scritto a mano. Dopo il completamento dell'analisi, la funzionalità di riconoscimento della grafia restituisce un oggetto JSON che contiene il testo e la posizione del testo nell'immagine.

Per completare la funzionalità di riconoscimento della grafia dell'applicazione dell'esercitazione, procedere come segue:

### <a name="handwriting-recognition-step-1-add-the-event-handler-code-for-the-form-button"></a>Riconoscimento della grafia, passaggio 1: aggiungere il codice del gestore dell'evento per il pulsante del modulo

Il metodo del gestore dell'evento **handwritingImageButtonActionPerformed** cancella il modulo, visualizza l'immagine specificata nell'URL e quindi chiama il metodo **HandwritingImage** per analizzare l'immagine. Quando viene restituito il risultato di **HandwritingImage**, il metodo visualizza il testo rilevato in formato JSON nell'area di testo **Response** (Risposta).

Copiare e incollare il codice seguente nel metodo **handwritingImageButtonActionPerformed**.

> [!NOTE]
> NetBeans non consente di incollare nella riga di definizione del metodo (```private void```) o nella parentesi graffa chiusa del metodo. Per copiare il codice, copiare le righe comprese tra la definizione del metodo e la parentesi graffa chiusa, quindi incollarle sul contenuto del metodo.

```java
    private void handwritingImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL handwritingImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        handwritingImage.setIcon(new ImageIcon());
        handwritingResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            handwritingImageUrl = new URL(handwritingImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(handwritingImageUrl);
            scaleAndShowImage(bImage, handwritingImage);
        } catch(IOException e) {
            handwritingResponseTextArea.setText("Error loading Handwriting image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = HandwritingImage(handwritingImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        handwritingResponseTextArea.setText(jsonObj.toString(2));
    }
```

### <a name="handwriting-recognition-step-2-add-the-wrapper-for-the-rest-api-call"></a>Riconoscimento della grafia, passaggio 2: aggiungere il wrapper per la chiamata API REST

Il metodo **HandwritingImage** esegue il wrapping delle due chiamate API REST necessarie per analizzare un'immagine. Poiché il riconoscimento della grafia richiede tempo, viene usato un processo in due fasi. La prima chiamata invia l'immagine per l'elaborazione, mentre la seconda recupera il testo rilevato al termine dell'elaborazione.

Una volta recuperato il testo, il metodo **HandwritingImage** restituisce un **JSONObject** che descrive il testo e le relative posizioni oppure **null** in caso di errore.

Copiare e incollare il metodo**HandwritingImage** subito sotto il metodo **handwritingImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read handwritten text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject HandwritingImage(String imageUrl) {
        try (CloseableHttpClient textClient = HttpClientBuilder.create().build();
             CloseableHttpClient resultClient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseHandwriting;
            URIBuilder uriBuilder = new URIBuilder(uriString);
            
            // Request parameters.
            uriBuilder.setParameter("handwriting", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response.
            HttpResponse textResponse = textClient.execute(request);
            
            // Check for success.
            if (textResponse.getStatusLine().getStatusCode() != 202) {
                // An error occured. Return the JSON error message.
                HttpEntity entity = textResponse.getEntity();
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            }
            
            String operationLocation = null;

            // The 'Operation-Location' in the response contains the URI to retrieve the recognized text.
            Header[] responseHeaders = textResponse.getAllHeaders();
            for(Header header : responseHeaders) {
                if(header.getName().equals("Operation-Location"))
                {
                    // This string is the URI where you can get the text recognition operation result.
                    operationLocation = header.getValue();
                    break;
                }
            }
            
            // NOTE: The response may not be immediately available. Handwriting recognition is an
            // async operation that can take a variable amount of time depending on the length
            // of the text you want to recognize. You may need to wait or retry this operation.
            //
            // This example checks once per second for ten seconds.
            
            JSONObject responseObj = null;
            int i = 0;
            do {
                // Wait one second.
                Thread.sleep(1000);
                
                // Check to see if the operation completed.
                HttpGet resultRequest = new HttpGet(operationLocation);
                resultRequest.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());
                HttpResponse resultResponse = resultClient.execute(resultRequest);
                HttpEntity responseEntity = resultResponse.getEntity();
                if (responseEntity != null)
                {
                    // Get the JSON response.
                    String jsonString = EntityUtils.toString(responseEntity);
                    responseObj = new JSONObject(jsonString);
                }
            }
            while (i < 10 && responseObj != null &&
                    !responseObj.getString("status").equalsIgnoreCase("Succeeded"));
            
            // If the operation completed, return the JSON object.
            if (responseObj != null) {
                return responseObj;
            } else {
                // Return null for timeout error.
                System.out.println("Timeout error.");
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

### <a name="handwriting-recognition-step-3-run-the-application"></a>Riconoscimento della grafia, passaggio 3: eseguire l'applicazione

Per eseguire l'applicazione, premere **F6**. Inserire la chiave di sottoscrizione nel campo **Chiave sottoscrizione** e verificare di usare l'area corretta in **Subscription Region** (Area sottoscrizione). Fare clic sulla scheda **Read Handwritten Text** (Leggi testo scritto a mano), immettere l'URL di un'immagine di testo scritto a mano, quindi fare clic sul pulsante **Read Image** (Leggi immagine) per analizzare l'immagine e visualizzare il risultato.

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione in C&#35; dell'API Visione artificiale](CSharpTutorial.md)
- [Esercitazione per Python dell'API Visione artificiale](PythonTutorial.md)

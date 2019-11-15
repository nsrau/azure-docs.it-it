---
title: "Esercitazione: Creare un'app Flask per tradurre, sintetizzare e analizzare il testo - API Traduzione testuale"
titleSuffix: Azure Cognitive Services
description: In questa esercitazione viene creata un'app Web basata su Flask per tradurre testo, analizzare il sentiment e sintetizzare il testo tradotto in voce.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 7b9f1cfeb6ebcbc693135d83ad167092e16f478d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647339"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Esercitazione: Creare un'app Flask con Servizi cognitivi di Azure

Questa esercitazione descrive come creare un'app Web Flask che usa Servizi cognitivi di Azure per tradurre testo, analizzare sentiment e sintetizzare il testo tradotto in voce. Il contenuto riguarda principalmente il codice Python e le route Flask che rendono possibile l'applicazione, ma verranno fornite indicazioni anche sul codice HTML e Javascript per l'assemblaggio dell'app. Se si verificano problemi, segnalarlo tramite il pulsante del feedback qui sotto.

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Ottenere le chiavi di sottoscrizione di Azure
> * Configurare l'ambiente di sviluppo e installare le dipendenze
> * Creare un'app Flask
> * Usare l'API Traduzione testuale per tradurre il testo
> * Usare Analisi del testo per analizzare sentiment positivi/negativi su testo di input e traduzioni
> * Usare i servizi Voce per convertire il testo tradotto in voce sintetizzata
> * Eseguire l'app Flask in locale

> [!TIP]
> Se si preferisce andare avanti e visualizzare tutto il codice contemporaneamente, l'intero esempio insieme alle istruzioni per la compilazione sono disponibili su [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="what-is-flask"></a>Cos'è Flask?

Flask è un microframework per la creazione di applicazioni Web. Offre quindi gli strumenti, le librerie e le tecnologie che consentono di creare un'applicazione Web. L'applicazione Web può essere costituita da alcune pagine Web, un blog, un wiki oppure può essere più impegnativa, ad esempio un calendario Web o un sito Web commerciale.

Per chi vuole approfondire l'argomento dopo questa esercitazione, ecco alcuni collegamenti utili:

* [Documentazione di Flask](http://flask.pocoo.org/)
* [Flask for Dummies - A Beginner's Guide to Flask](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1) (Guida per principianti di Flask)

## <a name="prerequisites"></a>Prerequisiti

Ecco il software e le chiavi di sottoscrizione che è necessario avere per questa esercitazione.

* [Python 3.5.2 o versione successiva](https://www.python.org/downloads/)
* [Strumenti di Git](https://git-scm.com/downloads)
* Un IDE o un editor di testo, come [Visual Studio Code](https://code.visualstudio.com/) o [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) o [Firefox](https://www.mozilla.org/firefox)
* Una chiave di sottoscrizione di **Traduzione testuale** (non è necessario selezionare un'area)
* Una chiave di sottoscrizione di **Analisi del testo** nell'area **Stati Uniti occidentali**.
* Una chiave di sottoscrizione di **servizi Voce** nell'area **Stati Uniti occidentali**.

## <a name="create-an-account-and-subscribe-to-resources"></a>Creare un account e sottoscrivere le risorse

Come accennato in precedenza, è necessario avere tre chiavi di sottoscrizione per questa esercitazione. Quindi, è necessario creare una risorsa all'interno dell'account di Azure per:
* Traduzione testuale
* Text Analytics
* Servizi Voce

Per le istruzioni dettagliate su come creare le risorse, vedere [Creare un account di Servizi cognitivi nel portale di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

> [!IMPORTANT]
> Per questa esercitazione, creare le risorse nell'area Stati Uniti occidentali. Se si usa un'area diversa, sarà necessario modificare l'URL di base per ogni file Python.

## <a name="set-up-your-dev-environment"></a>Configurare l'ambiente di sviluppo

Prima di compilare l'app Web Flask, è necessario creare una directory di lavoro per il progetto e installare alcuni pacchetti Python.

### <a name="create-a-working-directory"></a>Creare una directory di lavoro

1. Aprire la riga di comando (Windows) o il terminale (macOS/Linux). Quindi, creare una directory di lavoro e le sottodirectory per il progetto:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Passare alla directory di lavoro del progetto:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Creare e attivare un ambiente virtuale con `virtualenv`

Creare un ambiente virtuale per l'app Flask usando `virtualenv`. L'uso di un ambiente virtuale assicura la disponibilità di un ambiente pulito in cui lavorare.

1. Nella directory di lavoro eseguire questo comando per creare un ambiente virtuale: **macOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   È stato dichiarato in modo esplicito che l'ambiente virtuale dovrà usare Python 3. In questo modo ci si assicura che gli utenti con più installazioni di Python usino la versione corretta.

   **CMD Windows/Bash Windows:**
   ```
   virtualenv venv
   ```
   Per semplicità, all'ambiente virtuale viene assegnato il nome venv.

2. I comandi per attivare l'ambiente virtuale variano in base alla piattaforma/shell:   

   | Piattaforma | Shell | Comando |
   |----------|-------|---------|
   | macOS/Linux | bash/zsh | `source venv/bin/activate` |
   | Windows | bash | `source venv/Scripts/activate` |
   | | Riga di comando | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   Dopo aver eseguito questo comando, la riga di comando o la sessione del terminale sarà preceduta da `venv`.

3. È possibile disattivare la sessione in qualsiasi momento digitando questo comando nella riga di comando o nel terminale: `deactivate`.

> [!NOTE]
> Python include una documentazione completa per la creazione e la gestione di ambienti virtuali. Vedere [virtualenv](https://virtualenv.pypa.io/en/latest/).

### <a name="install-requests"></a>Installare Requests

Requests è un modulo ampiamente diffuso usato per inviare richieste HTTP 1.1. Non è necessario aggiungere manualmente stringhe di query agli URL o applicare la codifica per modulo ai dati POST.

1. Per installare Requests, eseguire:

   ```
   pip install requests
   ```

> [!NOTE]
> Per altre informazioni su Requests, vedere [Requests: HTTP for Humans](http://docs.python-requests.org/en/master/).

### <a name="install-and-configure-flask"></a>Installare e configurare Flask

A questo punto è necessario installare Flask. Flask gestisce il routing per l'app Web e consente di eseguire chiamate da server a server che nascondono la chiavi di sottoscrizione all'utente finale.

1. Per installare Flask, eseguire:
   ```
   pip install Flask
   ```
   Verificare se Flask è stato installato. Eseguire:
   ```
   flask --version
   ```
   La versione dovrebbe essere stampata nel terminale. Qualsiasi altro risultato implica che si è verificato un errore.

2. Per eseguire l'app Flask, è possibile usare il comando flask o l'opzione -m di Python con Flask. Ma prima è necessario indicare al terminale quale app usare esportando la variabile di ambiente `FLASK_APP`:

   **macOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>Creare l'app Flask

In questa sezione verrà creata un'app Flask barebone che restituisce un file HTML quando l'utente seleziona la radice dell'app. Non è necessario esaminare adesso tutto il codice; il file verrà aggiornato in seguito.

### <a name="what-is-a-flask-route"></a>Cos'è una route di Flask?

Vediamo che cosa sono le "[route](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)". Il routing viene usato per associare un URL a una specifica funzione. Flask usa gli elementi Decorator delle route per registrare le funzioni in specifici URL. Ad esempio, quando un utente passa alla radice (`/`) dell'app Web, viene visualizzato `index.html`.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Ecco un altro esempio per spiegare questo concetto.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Questo codice assicura che quando un utente passa a `http://your-web-app.com/about`, viene visualizzato il file `about.html`.

Questi esempi illustrano come visualizzare le pagine HTML per un utente, ma le route possono essere usate anche per chiamare le API quando viene premuto un pulsante oppure per eseguire un numero qualsiasi di azioni senza la necessità di uscire dalla home page. Questo comportamento verrà visto quando si creeranno route per la traduzione, il sentiment e la sintesi vocale.

### <a name="get-started"></a>Attività iniziali

1. Aprire il progetto nell'IDE, quindi creare un file denominato `app.py` nella radice della directory di lavoro. Copiare quindi questo codice in `app.py` e salvare:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Questo blocco di codice indica all'app di visualizzare `index.html` ogni volta che un utente passa alla radice dell'app Web (`/`).

2. Creare quindi il front-end per l'app Web. Creare un file denominato `index.html` nella directory `templates`. Copiare questo codice in `templates/index.html`.

   ```html
   <!doctype html>
   <html lang="en">
     <head>
       <!-- Required metadata tags -->
       <meta charset="utf-8">
       <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
       <meta name="description" content="Translate and analyze text with Azure Cognitive Services.">
       <!-- Bootstrap CSS -->
       <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
       <title>Translate and analyze text with Azure Cognitive Services</title>
     </head>
     <body>
       <div class="container">
         <h1>Translate, synthesize, and analyze text with Azure</h1>
         <p>This simple web app uses Azure for text translation, text-to-speech conversion, and sentiment analysis of input text and translations. Learn more about <a href="https://docs.microsoft.com/azure/cognitive-services/">Azure Cognitive Services</a>.
        </p>
        <!-- HTML provided in the following sections goes here. -->

        <!-- End -->
       </div>

       <!-- Required Javascript for this tutorial -->
       <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
       <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
       <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
       <script type = "text/javascript" src ="static/scripts/main.js"></script>
     </body>
   </html>
   ```

3. Testare l'app Flask. Nel terminale eseguire:

   ```
   flask run
   ```

4. Aprire un browser e passare all'URL specificato. Dovrebbe essere visualizzata l'app a pagina singola. Premere **CTRL+C** per terminare l'app.

## <a name="translate-text"></a>Traduci testo

Dopo aver visto come funziona una semplice app Flask, procedere come segue:

* Scrivere codice Python per chiamare l'API Traduzione testuale e restituire una risposta
* Creare una route di Flask per chiamare il codice Python
* Aggiornare il codice HTML con un'area per l'input e la traduzione del testo, un selettore della lingua e un pulsante per la traduzione
* Scrivere codice Javascript che consente agli utenti di interagire con l'app Flask dal codice HTML

### <a name="call-the-translator-text-api"></a>Chiamare l'API Traduzione testuale

Prima di tutto, è necessario scrivere una funzione per chiamare l'API Traduzione testuale. Questa funzione accetta due argomenti: `text_input` e `language_output`. Viene chiamata ogni volta che un utente preme il pulsante per la traduzione nell'app. L'area di testo nell'HTML viene inviata come `text_input` e il valore per la selezione della lingua viene inviato come `language_output`.

1. Per iniziare, creare un file denominato `translate.py` nella radice della directory di lavoro.
2. Aggiungere quindi questo codice a `translate.py`. Questa funzione accetta due argomenti: `text_input` e `language_output`.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
   
   # Don't forget to replace with your Cog Services location!
   # Our Flask route will supply two arguments: text_input and language_output.
   # When the translate text button is pressed in our Flask app, the Ajax request
   # will grab these values from our web app, and use them in the request.
   # See main.js for Ajax calls.
   def get_translation(text_input, language_output):
       base_url = 'https://api.cognitive.microsofttranslator.com'
       path = '/translate?api-version=3.0'
       params = '&to=' + language_output
       constructed_url = base_url + path + params

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Ocp-Apim-Subscription-Region': 'location',
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = [{
           'text' : text_input
       }]
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Aggiungere la chiave di sottoscrizione di Traduzione testuale e salvare.

### <a name="add-a-route-to-apppy"></a>Aggiungere una route a `app.py`

Ora è necessario creare una route nell'app Flask che chiama `translate.py`. Questa route verrà chiamata ogni volta che un utente preme il pulsante per la traduzione nell'app.

Per questa app, la route accetterà richieste `POST`. Il motivo è che la funzione si aspetta il testo da tradurre e una lingua di output per la traduzione.

Flask prevede funzioni helper che consentono di analizzare e gestire ogni richiesta. Nel codice fornito `get_json()` restituisce i dati delle richieste `POST` in formato JSON. Quindi usando `data['text']` e `data['to']`, i valori del testo e della lingua di output vengono passati alla funzione `get_translation()` disponibile in `translate.py`. L'ultimo passaggio consiste nel restituire la risposta in formato JSON, perché sarà necessario visualizzare questi dati nell'app Web.

Nelle sezioni seguenti questa procedura verrà ripetuta per creare le route per l'analisi del sentiment e la sintesi vocale.

1. Aprire `app.py` e individuare l'istruzione import nella parte superiore di `app.py`, quindi aggiungere la riga seguente:

   ```python
   import translate
   ```
   Ora l'app Flask può usare il metodo disponibile tramite `translate.py`.

2. Copiare questo codice alla fine di `app.py` e salvare:

   ```python
   @app.route('/translate-text', methods=['POST'])
   def translate_text():
       data = request.get_json()
       text_input = data['text']
       translation_output = data['to']
       response = translate.get_translation(text_input, translation_output)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Aggiornare `index.html`

Dopo aver aggiunto una funzione per tradurre il testo e una route nell'app Flask per chiamarla, il passaggio successivo consiste nell'iniziare a compilare il codice HTML per l'app. Il codice HTML seguente esegue queste operazioni:

* Fornisce un'area di testo in cui gli utenti possono immettere il testo da tradurre.
* Include un selettore della lingua.
* Include gli elementi HTML per il rendering della lingua rilevata e dei punteggi di attendibilità restituiti durante la traduzione.
* Fornisce un'area di testo di sola lettura in cui viene visualizzato l'output della traduzione.
* Include i segnaposto per il codice dell'analisi del sentiment e della sintesi vocale che verrà aggiunto al file più avanti nell'esercitazione.

Aggiornare `index.html`.

1. Aprire `index.html` e individuare questi commenti del codice:
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. Sostituire i commenti del codice con questo blocco HTML:
   ```html
   <div class="row">
     <div class="col">
       <form>
         <!-- Enter text to translate. -->
         <div class="form-group">
           <label for="text-to-translate"><strong>Enter the text you'd like to translate:</strong></label>
           <textarea class="form-control" id="text-to-translate" rows="5"></textarea>
         </div>
         <!-- Select output language. -->
         <div class="form-group">
           <label for="select-language"><strong>Translate to:</strong></label>
           <select class="form-control" id="select-language">
             <option value="ar">Arabic</option>
             <option value="ca">Catalan</option>
             <option value="zh-Hans">Chinese (Simplified)</option>
             <option value="zh-Hant">Chinese (Traditional)</option>
             <option value="hr">Croatian</option>
             <option value="en">English</option>
             <option value="fr">French</option>
             <option value="de">German</option>
             <option value="el">Greek</option>
             <option value="he">Hebrew</option>
             <option value="hi">Hindi</option>
             <option value="it">Italian</option>
             <option value="ja">Japanese</option>
             <option value="ko">Korean</option>
             <option value="pt">Portuguese</option>
             <option value="ru">Russian</option>
             <option value="es">Spanish</option>
             <option value="th">Thai</option>
             <option value="tr">Turkish</option>
             <option value="vi">Vietnamese</option>
           </select>
         </div>
         <button type="submit" class="btn btn-primary mb-2" id="translate">Translate text</button></br>
         <div id="detected-language" style="display: none">
           <strong>Detected language:</strong> <span id="detected-language-result"></span><br />
           <strong>Detection confidence:</strong> <span id="confidence"></span><br /><br />
         </div>

         <!-- Start sentiment code-->

         <!-- End sentiment code -->

       </form>
     </div>
     <div class="col">
       <!-- Translated text returned by the Translate API is rendered here. -->
       <form>
         <div class="form-group" id="translator-text-response">
           <label for="translation-result"><strong>Translated text:</strong></label>
           <textarea readonly class="form-control" id="translation-result" rows="5"></textarea>
         </div>

         <!-- Start voice font selection code -->

         <!-- End voice font selection code -->

       </form>

       <!-- Add Speech Synthesis button and audio element -->

       <!-- End Speech Synthesis button -->

     </div>
   </div>
   ```

Il passaggio successivo consiste nello scrivere codice Javascript. Si tratta del bridge tra l'HTML e la route di Flask.

### <a name="create-mainjs"></a>Creare `main.js`.  

Il file `main.js` rappresenta il bridge tra l'HTML e la route di Flask. L'app userà una combinazione di jQuery, Ajax e XMLHttpRequest per eseguire il rendering del contenuto ed effettuerà richieste `POST` alle route di Flask.

Nel codice seguente il contenuto dell'HTML viene usato per costruire una richiesta per la route di Flask. In particolare, i contenuti dell'area di testo e del selettore della lingua vengono assegnati a variabili e quindi passati nella richiesta a `translate-text`.

Il codice esegue quindi l'iterazione attraverso la risposta e aggiorna il blocco HTML con la traduzione, la lingua rilevata e il punteggio di attendibilità.

1. Nell'IDE creare un file denominato `main.js` nella directory `static/scripts`.
2. Copiare questo codice in `static/scripts/main.js`:
   ```javascript
   //Initiate jQuery on load.
   $(function() {
     //Translate text with flask route
     $("#translate").on("click", function(e) {
       e.preventDefault();
       var translateVal = document.getElementById("text-to-translate").value;
       var languageVal = document.getElementById("select-language").value;
       var translateRequest = { 'text': translateVal, 'to': languageVal }

       if (translateVal !== "") {
         $.ajax({
           url: '/translate-text',
           method: 'POST',
           headers: {
               'Content-Type':'application/json'
           },
           dataType: 'json',
           data: JSON.stringify(translateRequest),
           success: function(data) {
             for (var i = 0; i < data.length; i++) {
               document.getElementById("translation-result").textContent = data[i].translations[0].text;
               document.getElementById("detected-language-result").textContent = data[i].detectedLanguage.language;
               if (document.getElementById("detected-language-result").textContent !== ""){
                 document.getElementById("detected-language").style.display = "block";
               }
               document.getElementById("confidence").textContent = data[i].detectedLanguage.score;
             }
           }
         });
       };
     });
     // In the following sections, you'll add code for sentiment analysis and
     // speech synthesis here.
   })
   ```

### <a name="test-translation"></a>Testare la traduzione

Testare la traduzione nell'app.

```
flask run
```

Passare all'indirizzo del server fornito. Digitare un testo nell'area di input, selezionare una lingua e premere il pulsante per la traduzione. Si dovrebbe ottenere una traduzione. Se l'app non funziona, verificare di aver aggiunto la propria chiave di sottoscrizione.

> [!TIP]
> Se le modifiche apportate non vengono visualizzate oppure se l'app non funziona come previsto, provare a cancellare la cache o ad aprire una finestra privata/in incognito.

Premere **CTRL +C** per terminare l'app e passare alla sezione successiva.

## <a name="analyze-sentiment"></a>Analizzare la valutazione

L'[API Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) consente di eseguire l'analisi del sentiment, estrarre frasi chiave dal testo o rilevare la lingua di origine. In questa app verrà usata l'analisi del sentiment per determinare se il giudizio sul testo fornito è positivo, neutro o negativo. L'API restituisce un punteggio numerico compreso tra 0 e 1. I punteggi vicini all'1 indicano una valutazione positiva e i punteggi vicini allo 0 indicano una valutazione negativa.

In questa sezione verranno eseguite queste operazioni:

* Scrivere codice Python per chiamare l'API Analisi del testo, che eseguirà l'analisi del sentiment e restituirà una risposta
* Creare una route di Flask per chiamare il codice Python
* Aggiornare l'HTML con un'area per i punteggi del sentiment e un pulsante per eseguire l'analisi
* Scrivere codice Javascript che consente agli utenti di interagire con l'app Flask dal codice HTML

### <a name="call-the-text-analytics-api"></a>Chiamare l'API Analisi del testo

Scrivere una funzione per chiamare l'API Analisi del testo. Questa funzione accetta quattro argomenti: `input_text`, `input_language`, `output_text` e `output_language`. Viene chiamata ogni volta che un utente preme il pulsante per l'analisi del sentiment nell'app. Con ogni richiesta vengono forniti i dati specificati dall'utente nell'area di testo e con il selettore della lingua, oltre alla lingua rilevata e all'output della traduzione. L'oggetto risposta include i punteggi del sentiment per l'origine e per la traduzione. Nelle sezioni seguenti verrà scritto il codice Javascript per analizzare la risposta e usarla nell'app. Per il momento, chiamare l'API Analisi del testo.

1. Creare un file denominato `sentiment.py` nella radice della directory di lavoro.
2. Aggiungere quindi questo codice a `sentiment.py`.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   subscription_key = 'YOUR_TEXT_ANALYTICS_SUBSCRIPTION_KEY'

   # Our Flask route will supply four arguments: input_text, input_language,
   # output_text, output_language.
   # When the run sentiment analysis button is pressed in our Flask app,
   # the Ajax request will grab these values from our web app, and use them
   # in the request. See main.js for Ajax calls.

   def get_sentiment(input_text, input_language, output_text, output_language):
       base_url = 'https://westus.api.cognitive.microsoft.com/text/analytics'
       path = '/v2.0/sentiment'
       constructed_url = base_url + path

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = {
           'documents': [
               {
                   'language': input_language,
                   'id': '1',
                   'text': input_text
               },
               {
                   'language': output_language,
                   'id': '2',
                   'text': output_text
               }
           ]
       }
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Aggiungere la chiave di sottoscrizione di Analisi del testo e salvare.

### <a name="add-a-route-to-apppy"></a>Aggiungere una route a `app.py`

Creare una route nell'app Flask che chiama `sentiment.py`. Questa route verrà chiamata ogni volta che un utente preme il pulsante per l'analisi del sentiment nell'app. Analogamente alla route per la traduzione, questa route accetterà richieste `POST`, perché la funzione prevede argomenti.

1. Aprire `app.py` e individuare l'istruzione import nella parte superiore di `app.py`, quindi aggiornarla:

   ```python
   import translate, sentiment
   ```
   Ora l'app Flask può usare il metodo disponibile tramite `sentiment.py`.

2. Copiare questo codice alla fine di `app.py` e salvare:
   ```python
   @app.route('/sentiment-analysis', methods=['POST'])
   def sentiment_analysis():
       data = request.get_json()
       input_text = data['inputText']
       input_lang = data['inputLanguage']
       output_text = data['outputText']
       output_lang =  data['outputLanguage']
       response = sentiment.get_sentiment(input_text, input_lang, output_text, output_lang)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Aggiornare `index.html`

Dopo aver aggiunto una funzione per eseguire l'analisi del sentiment e una route nell'app Flask per chiamarla, il passaggio successivo consiste nell'iniziare a scrivere il codice HTML per l'app. Il codice HTML seguente esegue queste operazioni:

* Aggiunge un pulsante nell'app per eseguire l'analisi del sentiment
* Aggiunge un elemento che spiega i punteggi del sentiment
* Aggiunge un elemento per visualizzare i punteggi del sentiment

1. Aprire `index.html` e individuare questi commenti del codice:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. Sostituire i commenti del codice con questo blocco HTML:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>Aggiornare `main.js`

Nel codice seguente il contenuto dell'HTML viene usato per costruire una richiesta per la route di Flask. In particolare, i contenuti dell'area di testo e del selettore della lingua vengono assegnati a variabili e quindi passati nella richiesta alla route `sentiment-analysis`.

Il codice esegue quindi l'iterazione attraverso la risposta e aggiorna l'HTML con i punteggi del sentiment.

1. Nell'IDE creare un file denominato `main.js` nella directory `static`.

2. Copiare questo codice in `static/scripts/main.js`:
   ```javascript
   //Run sentinment analysis on input and translation.
   $("#sentiment-analysis").on("click", function(e) {
     e.preventDefault();
     var inputText = document.getElementById("text-to-translate").value;
     var inputLanguage = document.getElementById("detected-language-result").innerHTML;
     var outputText = document.getElementById("translation-result").value;
     var outputLanguage = document.getElementById("select-language").value;

     var sentimentRequest = { "inputText": inputText, "inputLanguage": inputLanguage, "outputText": outputText,  "outputLanguage": outputLanguage };

     if (inputText !== "") {
       $.ajax({
         url: "/sentiment-analysis",
         method: "POST",
         headers: {
             "Content-Type":"application/json"
         },
         dataType: "json",
         data: JSON.stringify(sentimentRequest),
         success: function(data) {
           for (var i = 0; i < data.documents.length; i++) {
             if (typeof data.documents[i] !== "undefined"){
               if (data.documents[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.documents[i].score;
               }
               if (data.documents[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.documents[i].score;
               }
             }
           }
           for (var i = 0; i < data.errors.length; i++) {
             if (typeof data.errors[i] !== "undefined"){
               if (data.errors[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.errors[i].message;
               }
               if (data.errors[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.errors[i].message;
               }
             }
           }
           if (document.getElementById("input-sentiment").textContent !== '' && document.getElementById("translation-sentiment").textContent !== ""){
             document.getElementById("sentiment").style.display = "block";
           }
         }
       });
     }
   });
   // In the next section, you'll add code for speech synthesis here.
   ```

### <a name="test-sentiment-analysis"></a>Testare l'analisi del sentiment

Testare l'analisi del sentiment nell'app.

```
flask run
```

Passare all'indirizzo del server fornito. Digitare un testo nell'area di input, selezionare una lingua e premere il pulsante per la traduzione. Si dovrebbe ottenere una traduzione. Premere quindi il pulsante per eseguire l'analisi del sentiment. Dovrebbero essere visualizzati due punteggi. Se l'app non funziona, verificare di aver aggiunto la propria chiave di sottoscrizione.

> [!TIP]
> Se le modifiche apportate non vengono visualizzate oppure se l'app non funziona come previsto, provare a cancellare la cache o ad aprire una finestra privata/in incognito.

Premere **CTRL +C** per terminare l'app e passare alla sezione successiva.

## <a name="convert-text-to-speech"></a>Conversione sintesi vocale

L'[API Sintesi vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) consente all'app di convertire il testo in una voce sintetizzata naturale simile a quella umana. Il servizio supporta voci standard, neurali e personalizzate. Nell'app di esempio vengono usate alcune voci disponibili. Per l'elenco completo vedere le [lingue supportate](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech).

In questa sezione verranno eseguite queste operazioni:

* Scrivere codice Python per convertire il testo in voce con l'API Sintesi vocale
* Creare una route di Flask per chiamare il codice Python
* Aggiornare l'HTML con un pulsante per convertire il testo in voce e un elemento per la riproduzione dell'audio
* Scrivere codice Javascript che consente agli utenti di interagire con l'app Flask

### <a name="call-the-text-to-speech-api"></a>Chiamare l'API Sintesi vocale

Scrivere una funzione per convertire il testo in voce. Questa funzione accetta due argomenti: `input_text` e `voice_font`. Viene chiamata ogni volta che un utente preme il pulsante per la conversione del testo in voce nell'app. `input_text` è l'output della traduzione restituito dalla chiamata per tradurre testo, `voice_font` è il valore del selettore del carattere voce nell'HTML.

1. Creare un file denominato `synthesize.py` nella radice della directory di lavoro.

2. Aggiungere quindi questo codice a `synthesize.py`.
   ```Python
   import os, requests, time
   from xml.etree import ElementTree

   class TextToSpeech(object):
       def __init__(self, input_text, voice_font):
           subscription_key = 'YOUR_SPEECH_SERVICES_SUBSCRIPTION_KEY'
           self.subscription_key = subscription_key
           self.input_text = input_text
           self.voice_font = voice_font
           self.timestr = time.strftime('%Y%m%d-%H%M')
           self.access_token = None

       # This function performs the token exchange.
       def get_token(self):
           fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
           headers = {
               'Ocp-Apim-Subscription-Key': self.subscription_key
           }
           response = requests.post(fetch_token_url, headers=headers)
           self.access_token = str(response.text)

       # This function calls the TTS endpoint with the access token.
       def save_audio(self):
           base_url = 'https://westus.tts.speech.microsoft.com/'
           path = 'cognitiveservices/v1'
           constructed_url = base_url + path
           headers = {
               'Authorization': 'Bearer ' + self.access_token,
               'Content-Type': 'application/ssml+xml',
               'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
               'User-Agent': 'YOUR_RESOURCE_NAME',
           }
           # Build the SSML request with ElementTree
           xml_body = ElementTree.Element('speak', version='1.0')
           xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
           voice = ElementTree.SubElement(xml_body, 'voice')
           voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
           voice.set('name', 'Microsoft Server Speech Text to Speech Voice {}'.format(self.voice_font))
           voice.text = self.input_text
           # The body must be encoded as UTF-8 to handle non-ascii characters.
           body = ElementTree.tostring(xml_body, encoding="utf-8")

           #Send the request
           response = requests.post(constructed_url, headers=headers, data=body)

           # Write the response as a wav file for playback. The file is located
           # in the same directory where this sample is run.
           return response.content
   ```
3. Aggiungere la chiave di sottoscrizione di servizi Voce e salvare.

### <a name="add-a-route-to-apppy"></a>Aggiungere una route a `app.py`

Creare una route nell'app Flask che chiama `synthesize.py`. Questa route verrà chiamata ogni volta che un utente preme il pulsante per la conversione di testo in voce nell'app. Analogamente alle route per la traduzione e l'analisi del sentiment, questa route accetterà richieste `POST`, perché la funzione prevede due argomenti, ossia il testo da sintetizzare e il carattere voce per la riproduzione.

1. Aprire `app.py` e individuare l'istruzione import nella parte superiore di `app.py`, quindi aggiornarla:

   ```python
   import translate, sentiment, synthesize
   ```
   Ora l'app Flask può usare il metodo disponibile tramite `synthesize.py`.

2. Copiare questo codice alla fine di `app.py` e salvare:

   ```Python
   @app.route('/text-to-speech', methods=['POST'])
   def text_to_speech():
       data = request.get_json()
       text_input = data['text']
       voice_font = data['voice']
       tts = synthesize.TextToSpeech(text_input, voice_font)
       tts.get_token()
       audio_response = tts.save_audio()
       return audio_response
   ```

### <a name="update-indexhtml"></a>Aggiornare `index.html`

Dopo aver aggiunto una funzione per convertire il testo in voce e una route nell'app Flask per chiamarla, il passaggio successivo consiste nell'iniziare a scrivere il codice HTML per l'app. Il codice HTML seguente esegue queste operazioni:

* Fornisce un menu a discesa per la selezione della voce
* Aggiunge un pulsante per convertire il testo in voce
* Aggiunge un elemento audio, usato per riprodurre la voce sintetizzata

1. Aprire `index.html` e individuare questi commenti del codice:
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. Sostituire i commenti del codice con questo blocco HTML:
   ```html
   <div class="form-group">
     <label for="select-voice"><strong>Select voice font:</strong></label>
     <select class="form-control" id="select-voice">
       <option value="(ar-SA, Naayf)">Arabic | Male | Naayf</option>
       <option value="(ca-ES, HerenaRUS)">Catalan | Female | HerenaRUS</option>
       <option value="(zh-CN, HuihuiRUS)">Chinese (Mainland) | Female | HuihuiRUS</option>
       <option value="(zh-CN, Kangkang, Apollo)">Chinese (Mainland) | Male | Kangkang, Apollo</option>
       <option value="(zh-HK, Tracy, Apollo)">Chinese (Hong Kong)| Female | Tracy, Apollo</option>
       <option value="(zh-HK, Danny, Apollo)">Chinese (Hong Kong) | Male | Danny, Apollo</option>
       <option value="(zh-TW, Yating, Apollo)">Chinese (Taiwan)| Female | Yaiting, Apollo</option>
       <option value="(zh-TW, Zhiwei, Apollo)">Chinese (Taiwan) | Male | Zhiwei, Apollo</option>
       <option value="(hr-HR, Matej)">Croatian | Male | Matej</option>
       <option value="(en-US, Jessa24kRUS)">English (US) | Female | Jessa24kRUS</option>
       <option value="(en-US, Guy24kRUS)">English (US) | Male | Guy24kRUS</option>
       <option value="(en-IE, Sean)">English (IE) | Male | Sean</option>
       <option value="(fr-FR, Julie, Apollo)">French | Female | Julie, Apollo</option>
       <option value="(fr-FR, HortenseRUS)">French | Female | Julie, HortenseRUS</option>
       <option value="(fr-FR, Paul, Apollo)">French | Male | Paul, Apollo</option>
       <option value="(de-DE, Hedda)">German | Female | Hedda</option>
       <option value="(de-DE, HeddaRUS)">German | Female | HeddaRUS</option>
       <option value="(de-DE, Stefan, Apollo)">German | Male | Apollo</option>
       <option value="(el-GR, Stefanos)">Greek | Male | Stefanos</option>
       <option value="(he-IL, Asaf)">Hebrew (Isreal) | Male | Asaf</option>
       <option value="(hi-IN, Kalpana, Apollo)">Hindi | Female | Kalpana, Apollo</option>
       <option value="(hi-IN, Hemant)">Hindi | Male | Hemant</option>
       <option value="(it-IT, LuciaRUS)">Italian | Female | LuciaRUS</option>
       <option value="(it-IT, Cosimo, Apollo)">Italian | Male | Cosimo, Apollo</option>
       <option value="(ja-JP, Ichiro, Apollo)">Japanese | Male | Ichiro</option>
       <option value="(ja-JP, HarukaRUS)">Japanese | Female | HarukaRUS</option>
       <option value="(ko-KR, HeamiRUS)">Korean | Female | Haemi</option>
       <option value="(pt-BR, HeloisaRUS)">Portuguese (Brazil) | Female | HeloisaRUS</option>
       <option value="(pt-BR, Daniel, Apollo)">Portuguese (Brazil) | Male | Daniel, Apollo</option>
       <option value="(pt-PT, HeliaRUS)">Portuguese (Portugal) | Female | HeliaRUS</option>
       <option value="(ru-RU, Irina, Apollo)">Russian | Female | Irina, Apollo</option>
       <option value="(ru-RU, Pavel, Apollo)">Russian | Male | Pavel, Apollo</option>
       <option value="(ru-RU, EkaterinaRUS)">Russian | Female | EkaterinaRUS</option>
       <option value="(es-ES, Laura, Apollo)">Spanish | Female | Laura, Apollo</option>
       <option value="(es-ES, HelenaRUS)">Spanish | Female | HelenaRUS</option>
       <option value="(es-ES, Pablo, Apollo)">Spanish | Male | Pablo, Apollo</option>
       <option value="(th-TH, Pattara)">Thai | Male | Pattara</option>
       <option value="(tr-TR, SedaRUS)">Turkish | Female | SedaRUS</option>
       <option value="(vi-VN, An)">Vietnamese | Male | An</option>
     </select>
   </div>
   ```

3. Individuare quindi questi commenti del codice:
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. Sostituire i commenti del codice con questo blocco HTML:

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. Assicurarsi di salvare le modifiche.

### <a name="update-mainjs"></a>Aggiornare `main.js`

Nel codice seguente il contenuto dell'HTML viene usato per costruire una richiesta per la route di Flask. In particolare, la traduzione e il carattere voce vengono assegnati a variabili e quindi passati nella richiesta alla route `text-to-speech`.

Il codice esegue quindi l'iterazione attraverso la risposta e aggiorna l'HTML con i punteggi del sentiment.

1. Nell'IDE creare un file denominato `main.js` nella directory `static`.
2. Copiare questo codice in `static/scripts/main.js`:
   ```javascript
   // Convert text-to-speech
   $("#text-to-speech").on("click", function(e) {
     e.preventDefault();
     var ttsInput = document.getElementById("translation-result").value;
     var ttsVoice = document.getElementById("select-voice").value;
     var ttsRequest = { 'text': ttsInput, 'voice': ttsVoice }

     var xhr = new XMLHttpRequest();
     xhr.open("post", "/text-to-speech", true);
     xhr.setRequestHeader("Content-Type", "application/json");
     xhr.responseType = "blob";
     xhr.onload = function(evt){
       if (xhr.status === 200) {
         audioBlob = new Blob([xhr.response], {type: "audio/mpeg"});
         audioURL = URL.createObjectURL(audioBlob);
         if (audioURL.length > 5){
           var audio = document.getElementById("audio");
           var source = document.getElementById("audio-source");
           source.src = audioURL;
           audio.load();
           audio.play();
         }else{
           console.log("An error occurred getting and playing the audio.")
         }
       }
     }
     xhr.send(JSON.stringify(ttsRequest));
   });
   // Code for automatic language selection goes here.
   ```
3. La procedura è quasi terminata. L'ultima operazione da eseguire è l'aggiunta di codice in `main.js` per selezionare automaticamente un carattere voce in base alla lingua selezionata per la traduzione. Aggiungere questo blocco di codice a `main.js`:
   ```javascript
   // Automatic voice font selection based on translation output.
   $('select[id="select-language"]').change(function(e) {
     if ($(this).val() == "ar"){
       document.getElementById("select-voice").value = "(ar-SA, Naayf)";
     }
     if ($(this).val() == "ca"){
       document.getElementById("select-voice").value = "(ca-ES, HerenaRUS)";
     }
     if ($(this).val() == "zh-Hans"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "zh-Hant"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "hr"){
       document.getElementById("select-voice").value = "(hr-HR, Matej)";
     }
     if ($(this).val() == "en"){
       document.getElementById("select-voice").value = "(en-US, Jessa24kRUS)";
     }
     if ($(this).val() == "fr"){
       document.getElementById("select-voice").value = "(fr-FR, HortenseRUS)";
     }
     if ($(this).val() == "de"){
       document.getElementById("select-voice").value = "(de-DE, HeddaRUS)";
     }
     if ($(this).val() == "el"){
       document.getElementById("select-voice").value = "(el-GR, Stefanos)";
     }
     if ($(this).val() == "he"){
       document.getElementById("select-voice").value = "(he-IL, Asaf)";
     }
     if ($(this).val() == "hi"){
       document.getElementById("select-voice").value = "(hi-IN, Kalpana, Apollo)";
     }
     if ($(this).val() == "it"){
       document.getElementById("select-voice").value = "(it-IT, LuciaRUS)";
     }
     if ($(this).val() == "ja"){
       document.getElementById("select-voice").value = "(ja-JP, HarukaRUS)";
     }
     if ($(this).val() == "ko"){
       document.getElementById("select-voice").value = "(ko-KR, HeamiRUS)";
     }
     if ($(this).val() == "pt"){
       document.getElementById("select-voice").value = "(pt-BR, HeloisaRUS)";
     }
     if ($(this).val() == "ru"){
       document.getElementById("select-voice").value = "(ru-RU, EkaterinaRUS)";
     }
     if ($(this).val() == "es"){
       document.getElementById("select-voice").value = "(es-ES, HelenaRUS)";
     }
     if ($(this).val() == "th"){
       document.getElementById("select-voice").value = "(th-TH, Pattara)";
     }
     if ($(this).val() == "tr"){
       document.getElementById("select-voice").value = "(tr-TR, SedaRUS)";
     }
     if ($(this).val() == "vi"){
       document.getElementById("select-voice").value = "(vi-VN, An)";
     }
   });
   ```

### <a name="test-your-app"></a>Test dell'app

Testare la sintesi vocale nell'app.

```
flask run
```

Passare all'indirizzo del server fornito. Digitare un testo nell'area di input, selezionare una lingua e premere il pulsante per la traduzione. Si dovrebbe ottenere una traduzione. Selezionare quindi una voce e premere il pulsante per la conversione di testo in voce. La traduzione dovrebbe essere riprodotta come voce sintetizzata. Se l'app non funziona, verificare di aver aggiunto la propria chiave di sottoscrizione.

> [!TIP]
> Se le modifiche apportate non vengono visualizzate oppure se l'app non funziona come previsto, provare a cancellare la cache o ad aprire una finestra privata/in incognito.

A questo punto è disponibile un'app funzionante che esegue traduzioni, analizza il sentiment e sintetizza la voce. Premere **CTRL+C** per terminare l'app. Assicurarsi di esaminare gli altri [Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/).

## <a name="get-the-source-code"></a>Ottenere il codice sorgente

Il codice sorgente di questo progetto è disponibile in [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni di riferimento sull'API Traduzione testuale](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
* [Informazioni di riferimento sull'API Analisi del testo](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [Informazioni di riferimento sull'API Sintesi vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)

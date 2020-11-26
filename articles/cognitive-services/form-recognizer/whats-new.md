---
title: Novità di Riconoscimento modulo
titleSuffix: Azure Cognitive Services
description: Comprendere le ultime modifiche apportate all'API di riconoscimento del modulo.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: pafarley
ms.openlocfilehash: 929aed28df2725ccde62c348a18ef7b41334e233
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172130"
---
# <a name="whats-new-in-form-recognizer"></a>Novità di Riconoscimento modulo

Il servizio di riconoscimento form viene aggiornato in modo continuo. Usare questo articolo per rimanere sempre aggiornati sui miglioramenti delle funzionalità, sulle correzioni e sugli aggiornamenti della documentazione.

## <a name="november-2020"></a>Novembre 2020

### <a name="new-features"></a>Nuove funzionalità

- **Nuovo modello di fatturazione predefinito** : il nuovo modello di fatturazione predefinito consente ai clienti di ottenere fatture in diversi formati e restituire dati strutturati per automatizzare l'elaborazione della fattura. Combina le potenti funzionalità di riconoscimento ottico dei caratteri (OCR) con la comprensione dei modelli di apprendimento avanzato per estrarre le informazioni chiave dalle fatture in inglese. Estrae il testo, le tabelle e le informazioni, ad esempio cliente, fornitore, ID fattura, data di scadenza della fattura, totale, importo dovuto, importo imposta, spedizione, fatturazione e altro ancora.

  > [Altre informazioni sul modello di fattura predefinito](concept-invoices.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="esempio di fattura" lightbox="./media/invoice-example.jpg":::

- **Enhanced Table Extraction** : il riconoscitore di form offre ora un'estrazione avanzata delle tabelle, che combina le potenti funzionalità di riconoscimento ottico dei caratteri con un modello di estrazione di tabelle di apprendimento avanzato. Il riconoscitore del modulo può estrarre i dati dalle tabelle, incluse le tabelle complesse con colonne unite, righe, nessun bordo e altro ancora. 
 
  :::image type="content" source="./media/tables-example.jpg" alt-text="esempio di tabelle" lightbox="./media/tables-example.jpg":::

 
  > [Altre informazioni sull'estrazione del layout](concept-layout.md)

- **Aggiornamento della libreria client** : le [librerie client](quickstarts/client-library.md) per .NET, Python, Java e JavaScript hanno immesso la disponibilità a livello generale.
- **Nuovo linguaggio supportato: giapponese** : sono ora supportate le nuove lingue seguenti: per `AnalyzeLayout` e `AnalyzeCustomForm` : Japanese ( `ja` ). [Lingue supportate](language-support.md)
- **Indicazione dello stile della linea di testo (manoscritta/altro) (solo lingue latine)** : il riconoscitore di form ora restituisce un `appearance` oggetto che classifica se ogni riga di testo è di tipo manoscritto o meno, insieme a un punteggio di confidenza. Questa funzionalità è supportata solo per le lingue latine.
- **Miglioramenti della qualità** : miglioramenti dell'estrazione, inclusi i miglioramenti dell'estrazione a una sola cifra.
- **Nuova funzionalità try-it-out nell'esempio form Recognizer e strumento** per l'assegnazione di etichette: possibilità di provare i modelli predefiniti di fattura, ricezione e business card e l'API di layout usando lo strumento per l'assegnazione di etichette di esempio form Recognizer. Scopri in che modo i tuoi dati verranno estratti senza scrivere codice.

  > [Provare lo strumento di esempio del modulo Recognizer](https://fott-preview.azurewebsites.net/)

  ![Esempio di FOTT](./media/ui-preview.jpg)
  
- **Loop di feedback** : quando si analizzano i file tramite lo strumento di assegnazione di etichette di esempio, è ora possibile aggiungerlo anche al set di training e modificare le etichette, se necessario, e eseguire il training per migliorare il modello.
- **Auto label Documents** : etichetta automaticamente i documenti aggiuntivi in base ai precedenti documenti con etichetta nel progetto.

## <a name="august-2020"></a>Agosto 2020

### <a name="new-features"></a>Nuove funzionalità

**È ora disponibile l'anteprima pubblica form Recognizer v 2.1.** V 2.1-Preview. 1 è stato rilasciato, incluse le funzionalità seguenti: 


- Per **informazioni di riferimento sull'API REST** , vedere il [riferimento v 2.1-Preview. 1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync) 
- Sono ora supportate le **nuove lingue supportate oltre all'inglese**, le seguenti [lingue](language-support.md) : per `Layout` e `Train Custom Model` : inglese ( `en` ), cinese (semplificato) ( `zh-Hans` ), olandese () `nl` , francese ( `fr` ), tedesco ( `de` ), Italiano (), `it` portoghese () `pt` e spagnolo ( `es` ).
- **Rilevamento del contrassegno della casella** di controllo: il riconoscimento del modulo supporta il rilevamento e l'estrazione dei contrassegni di selezione, ad esempio caselle di controllo e pulsanti di opzione. I contrassegni di selezione vengono estratti in `Layout` ed è ora possibile anche etichettare e eseguire il training in `Train Custom Model`  -  _Train con etichette_ per estrarre le coppie chiave-valore per i contrassegni di selezione. 
- **Modello compose** : consente la composizione e la chiamata di più modelli con un unico ID modello. Quando un documento viene inviato per essere analizzato con un ID di modello composto, viene innanzitutto eseguito un passaggio di classificazione per indirizzarlo al modello personalizzato corretto. La composizione del modello è disponibile per il `Train Custom Model`  -  _training con etichette_.
- **Nome modello** : aggiungere un nome descrittivo ai modelli personalizzati per semplificare la gestione e il rilevamento.
- **[Nuovo modello predefinito per i biglietti](concept-business-cards.md)** da visita per l'estrazione di campi comuni in lingua inglese, business card.
- **[Nuove impostazioni locali per le ricevute](concept-receipts.md)** predefinite, oltre a en-US, è ora disponibile il supporto per en-au, en-CA, en-GB, en-in
- **Miglioramenti qualitativi** per `Layout` , il `Train Custom Model`  -  _training senza etichette_ e il _training con etichette_.


**v 2.0** include l'aggiornamento seguente:

- Le [librerie client](quickstarts/client-library.md) per NET, Python, Java e JavaScript hanno immesso la disponibilità a livello generale. 


I **nuovi esempi** sono disponibili in GitHub. 
- Le [ricette di estrazione delle informazioni](https://github.com/microsoft/knowledge-extraction-recipes-forms) , ovvero i moduli PlayBook, raccolgono le procedure consigliate dagli impegni dei clienti riconoscibili in forma reale e forniscono esempi di codice, elenchi di controllo e pipeline di esempio utilizzabili per lo sviluppo di questi progetti. 
- Lo [strumento di assegnazione di etichette di esempio](https://github.com/microsoft/OCR-Form-Tools) è stato aggiornato per supportare la nuova funzionalità v 2.1. Per iniziare a usare lo strumento, vedere questa [Guida introduttiva](quickstarts/label-tool.md) . 
- L'esempio di riconoscimento in modalità [chiosco intelligente](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) Mostra come eseguire l'integrazione `Analyze Receipt` e il `Train Custom Model`  -  _training senza etichette_.



## <a name="july-2020"></a>Luglio 2020

### <a name="new-features"></a>Nuove funzionalità

* **informazioni di riferimento sulla versione 2.0 disponibili** : visualizzare le informazioni di [riferimento sulle API v 2.0](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) e gli SDK aggiornati per [.NET](/dotnet/api/overview/azure/ai.formrecognizer-readme?view=azure-dotnet), [Python](/python/api/overview/azure/?view=azure-python), [Java](/java/api/overview/azure/ai-formrecognizer-readme?view=azure-java-preview)e [JavaScript](/javascript/api/overview/azure/?view=azure-node-latest).
* Miglioramenti apportati alla tabella e miglioramenti dell' **estrazione** : include miglioramenti dell'accuratezza ed estrazioni di tabelle, in particolare la funzionalità per apprendere le intestazioni e le strutture delle tabelle in un _training personalizzato senza etichette_. 

* **Supporto della valuta** : rilevamento ed estrazione dei simboli di valuta globali.
* Il sistema di riconoscimento del modulo **gov di Azure** è ora disponibile anche in Azure gov.
* **Funzionalità di sicurezza avanzate**: 
   * Il riconoscitore di form **chiave di Bring** consente di crittografare automaticamente i dati quando vengono salvati in modo permanente nel cloud per proteggerli e aiutarti a soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Per impostazione predefinita, la sottoscrizione usa chiavi di crittografia gestite da Microsoft. È ora possibile gestire la sottoscrizione anche con le proprie chiavi di crittografia. Le [chiavi gestite dal cliente, note anche come Bring your own key (BYOK)](./form-recognizer-encryption-of-data-at-rest.md), offrono una maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È anche possibile controllare le chiavi di crittografia usate per proteggere i dati.  
   * **Endpoint privati** : consente di accedere in modo [sicuro ai dati tramite un collegamento privato](../../private-link/private-link-overview.md) in una rete virtuale (VNet).


## <a name="june-2020"></a>Giugno 2020

### <a name="new-features"></a>Nuove funzionalità
* **API CopyModel aggiunta agli SDK client** : è ora possibile usare gli SDK client per copiare i modelli da una sottoscrizione a un'altra. Per informazioni generali su questa funzionalità, vedere [eseguire il backup e il ripristino dei modelli](./disaster-recovery.md) .
* **Integrazione Azure Active Directory** : è ora possibile usare le credenziali Azure ad per autenticare gli oggetti client del riconoscimento del modulo negli SDK.
* **Modifiche specifiche dell'SDK** : sono incluse le aggiunte di funzionalità secondarie e le modifiche di rilievo. Per ulteriori informazioni, vedere i log delle modifiche dell'SDK.
  * [Log delle modifiche anteprima 3 di C# SDK](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Log delle modifiche di Python SDK Preview 3](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Changelog di Java SDK Preview 3](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Log delle modifiche anteprima 3 di JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>Aprile 2020

### <a name="new-features"></a>Nuove funzionalità
* **Supporto dell'SDK per l'API di riconoscimento moduli v 2.0 anteprima pubblica** : questo mese è stato ampliato il supporto tecnico per includere una versione di anteprima dell'SDK per il modulo di riconoscimento v 2.0 (anteprima). Usare i collegamenti seguenti per iniziare a usare la lingua preferita: 
   * [.NET SDK](/dotnet/api/overview/azure/ai.formrecognizer-readme?view=azure-dotnet)
   * [SDK per Java](/java/api/overview/azure/ai-formrecognizer-readme?view=azure-java-preview)
   * [Python SDK](/python/api/overview/azure/ai-formrecognizer-readme?view=azure-python-preview)
   * [JavaScript SDK](/javascript/api/overview/azure/ai-form-recognizer-readme?view=azure-node-preview)

  Il nuovo SDK supporta tutte le funzionalità dell'API REST v 2.0 per il riconoscimento del modulo. Ad esempio, è possibile eseguire il training di un modello con o senza etichette ed estrarre il testo, le coppie chiave-valore e le tabelle dai moduli, estrarre i dati dalle ricevute con il servizio ricevute predefinite ed estrarre il testo e le tabelle con il servizio di layout dai documenti. È possibile condividere commenti e suggerimenti sugli SDK tramite il [modulo feedback SDK](https://aka.ms/FR_SDK_v1_feedback).
 
* **Copia modello personalizzato** È ora possibile copiare i modelli tra aree e sottoscrizioni usando la nuova funzionalità Copia modello personalizzato. Prima di richiamare l'API del modello di copia personalizzata, è necessario ottenere prima di tutto l'autorizzazione per la copia nella risorsa di destinazione chiamando l'operazione di autorizzazione copia sull'endpoint della risorsa di destinazione.
   * [Genera un'autorizzazione di copia](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) API REST
   * [Copiare un modello personalizzato](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) API REST 

### <a name="security-improvements"></a>Miglioramenti della sicurezza

* Customer-Managed chiavi sono ora disponibili per FormRecognizer. Per altre informazioni, vedere [crittografia dei dati inattivi per il riconoscimento del modulo](./form-recognizer-encryption-of-data-at-rest.md).
* Usare le identità gestite per accedere alle risorse di Azure con Azure Active Directory. Per altre informazioni, vedere [autorizzare l'accesso alle identità gestite](../authentication.md#authorize-access-to-managed-identities).

## <a name="march-2020"></a>Marzo 2020 

### <a name="new-features"></a>Nuove funzionalità

* **Tipi di valore per l'assegnazione di etichette** È ora possibile specificare i tipi di valori di cui si sta eseguendo l'assegnazione di etichette con lo strumento di assegnazione di etichette di esempio di form Recognizer. Sono attualmente supportati i tipi di valore e le varianti seguenti:
  * `string`
    * predefinito, `no-whitespaces`, `alphanumeric`
  * `number`
    * predefinito, `currency`
  * `date` 
    * predefinito, `dmy`, `mdy`, `ymd`
  * `time`
  * `integer`

  Per informazioni sull'uso di questa funzionalità, vedere la guida agli strumenti per l' [assegnazione di etichette di esempio](./quickstarts/label-tool.md#specify-tag-value-types) .


* **Visualizzazione tabella** Lo strumento di assegnazione di etichette di esempio ora Visualizza le tabelle riconosciute nel documento. Questa funzionalità consente di visualizzare le tabelle riconosciute ed estratte dal documento, prima dell'assegnazione di etichette e dell'analisi. Questa funzionalità può essere attivata/disattivata tramite l'opzione livelli.

  Nell'immagine seguente è riportato un esempio di come vengono riconosciute ed estratte le tabelle:

  > [!div class="mx-imgBorder"]
  > ![Visualizzazione delle tabelle mediante lo strumento di assegnazione di etichette di esempio](./media/whats-new/formre-table-viz.png)

    Le tabelle estratte sono disponibili nell'output JSON sotto `"pageResults"` .

  > [!IMPORTANT]
  > L'assegnazione di etichette alle tabelle non è supportata. Se le tabelle non vengono riconosciute e extrated automaticamente, è possibile contrassegnarle solo come coppie chiave/valore. Quando si etichettano le tabelle come coppie chiave/valore, etichettare ogni cella come valore univoco.

### <a name="extraction-enhancements"></a>Miglioramenti dell'estrazione

Questa versione include miglioramenti dell'estrazione e miglioramenti dell'accuratezza, in particolare la funzionalità per etichettare ed estrarre più coppie chiave/valore nella stessa riga di testo. 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>Lo strumento di assegnazione di etichette di esempio è ora Open Source

Lo strumento per l'assegnazione di etichette di esempio di form Recognizer è ora disponibile come progetto open source. Puoi integrarlo nelle tue soluzioni e apportare modifiche specifiche per i clienti in base alle tue esigenze.

Per altre informazioni sullo strumento per l'assegnazione di etichette di esempio di form Recognizer, vedere la documentazione disponibile su [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="tls-12-enforcement"></a>Imposizione di TLS 1.2

TLS 1.2 viene ora applicato per tutte le richieste HTTP a questo servizio. Per altre informazioni, vedere [Sicurezza di Servizi cognitivi di Azure](../cognitive-services-security.md).

## <a name="january-2020"></a>Gennaio 2020

Questa versione introduce il modulo Recognizer 2,0 (Preview). Nelle sezioni seguenti sono disponibili altre informazioni su nuove funzionalità, miglioramenti e modifiche. 

### <a name="new-features"></a>Nuove funzionalità

* **Modello personalizzato**
  * Eseguire il **training con etichette** È ora possibile eseguire il training di un modello personalizzato con dati con etichetta manuale. Questo metodo genera modelli con prestazioni migliori e può produrre modelli che funzionano con moduli o moduli complessi contenenti valori senza chiavi.
  * **API asincrona** È possibile usare le chiamate API asincrone per eseguire il training e analizzare i set di dati e i file di grandi dimensioni.
  * **Supporto file TIFF** È ora possibile eseguire il training con ed estrarre i dati dai documenti TIFF.
  * **Miglioramenti dell'accuratezza dell'estrazione**

* **Modello di ricezione predefinito**
  * **Importi delle mance** È ora possibile estrarre gli importi delle mance e altri valori scritti a mano.
  * **Estrazione di elementi linea** È possibile estrarre i valori degli elementi linea dalle ricevute.
  * **Valori di confidenza** È possibile visualizzare la confidenza del modello per ogni valore estratto.
  * **Miglioramenti dell'accuratezza dell'estrazione**

* **Estrazione del layout** È ora possibile usare l'API layout per estrarre dati di testo e dati della tabella dai form.

### <a name="custom-model-api-changes"></a>Modifiche all'API del modello personalizzato

Tutte le API per il training e l'utilizzo di modelli personalizzati sono state rinominate e alcuni metodi sincroni sono ora asincroni. Di seguito sono riportate le modifiche principali:

* Il processo di training di un modello è ora asincrono. Si avvia il training tramite la chiamata API **/Custom/Models** . Questa chiamata restituisce un ID operazione, che può essere passato in **Custom/Models/{ModelId}** per restituire i risultati di training.
* L'estrazione chiave/valore è ora iniziata dalla chiamata API **/Custom/Models/{ModelId}/Analyze** . Questa chiamata restituisce un ID operazione, che può essere passato in **Custom/Models/{ModelId}/analyzeResults/{resultID}** per restituire i risultati dell'estrazione.
* Gli ID operazione per l'operazione di training sono ora disponibili nell'intestazione **location** delle risposte http, non nell'intestazione **Operation-location** .

### <a name="receipt-api-changes"></a>Modifiche all'API di ricezione

Le API per la lettura delle ricevute di vendita sono state rinominate.

* L'estrazione dei dati della ricevuta è stata avviata dalla chiamata dell'API **/prebuilt/Receipt/Analyze** . Questa chiamata restituisce un ID operazione, che può essere passato in **/prebuilt/Receipt/analyzeResults/{resultID}** per restituire i risultati dell'estrazione.

### <a name="output-format-changes"></a>Modifiche al formato di output

Le risposte JSON per tutte le chiamate API hanno nuovi formati. Alcune chiavi e valori sono stati aggiunti, rimossi o rinominati. Vedere le guide introduttive per esempi dei formati JSON correnti.

## <a name="next-steps"></a>Passaggi successivi

Completare una [guida di avvio rapido sulla libreria client](quickstarts/client-library.md) per apprendere come scrivere un'app di elaborazione moduli con Riconoscimento modulo nel linguaggio preferito.

## <a name="see-also"></a>Vedere anche

* [Informazioni su Riconoscimento modulo](./overview.md)

---
title: Che cos'è l'API Visione artificiale?
titlesuffix: Azure Cognitive Services
description: L'API Visione artificiale consente agli sviluppatori di accedere ad algoritmi avanzati per l'elaborazione delle immagini e la restituzione delle informazioni.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: overview
ms.date: 08/10/2017
ms.author: kefre
ms.openlocfilehash: 09a7f02df1102e42bc45b4615a2af4235372bcdc
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957028"
---
# <a name="what-is-computer-vision-api-version-10"></a>Informazioni sull'API Visione artificiale versione 1.0?

> [!IMPORTANT]
> Per informazioni sulla nuova versione dell'API Visione artificiale, vedere:
>- [Overview](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
>- [API Visione artificiale versione 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

L'API Visione artificiale basata sul cloud consente agli sviluppatori di accedere ad algoritmi avanzati per l'elaborazione delle immagini e la restituzione delle informazioni. Caricando un'immagine o specificando l'URL di un'immagine, gli algoritmi di Visione artificiale di Microsoft possono analizzare i contenuti visivi in diversi modi in base agli input e alle scelte dell'utente. L'API Visione artificiale consente agli utenti di eseguire queste operazioni:
* [Contrassegnare le immagini in base al contenuto.](#Tagging)
* [Classificare le immagini.](#Categorizing)
* [Identificare il tipo e la qualità delle immagini.](#Identifying)
* [Rilevare visi umani e restituirne le coordinate. ](#Faces)
* [Riconoscere contenuto specifico di dominio.](#Domain-Specific)
* [Generare le descrizioni del contenuto.](#Descriptions)
* [Usare il riconoscimento ottico dei caratteri per identificare testo stampato trovato nelle immagini.](#OCR)
* [Riconoscere testo scritto a mano.](#RecognizeText)
* [Distinguere le combinazioni di colori.](#Color)
* [Contrassegnare il contenuto per adulti.](#Adult)
* [Tagliare le foto da usare come anteprime.](#Thumbnails)

## <a name="requirements"></a>Requisiti
* Metodi di input supportati: file binario di immagine non elaborato di tipo application/octet-stream o URL di immagine.
* Formati di immagine supportati: JPEG, PNG, GIF, BMP.
* Dimensioni file di immagine: minori di 4 MB.
* Dimensioni immagine: maggiori di 50 x 50 pixel.

## <a name="tagging-images"></a>Assegnazione di tag alle immagini
L'API Visione artificiale restituisce tag basati su migliaia di oggetti riconoscibili, esseri viventi, panorami e azioni. Quando i tag sono ambigui o di conoscenza non comune, la risposta dell'API indica suggerimenti per chiarire il significato del tag nel contesto di un'impostazione nota. I tag non sono organizzati in una tassonomia e non esiste alcuna gerarchia di ereditarietà. Una raccolta di tag di contenuto costituisce la base per la descrizione di un'immagine visualizzata come linguaggio leggibile dall'utente e formattata in frasi complete. Si noti che a questo punto l'inglese è l'unica lingua supportata per la descrizione dell'immagine.

Dopo il caricamento di un'immagine o la specifica di un URL dell'immagine, gli algoritmi dell'API Visione artificiale assegnano i tag in base a oggetti, essere umani e azioni identificati nell'immagine. L'assegnazione di tag non è limitata al soggetto principale, ad esempio una persona in primo piano, ma include anche scenari (interni o esterni), arredamenti, strumenti, piante, animali, accessori, gadget e così via.

### <a name="example"></a>Esempio
![House_Yard](./Images/house_yard.png) '

```json
Returned Json
{
   'tags':[
      {
         "name":"grass",
         "confidence":0.999999761581421
      },
      {
         "name":"outdoor",
         "confidence":0.999970674514771
      },
      {
         "name":"sky",
         "confidence":999289751052856
      },
      {
         "name":"building",
         "confidence":0.996463239192963
      },
      {
         "name":"house",
         "confidence":0.992798030376434
      },
      {
         "name":"lawn",
         "confidence":0.822680294513702
      },
      {
         "name":"green",
         "confidence":0.641222536563873
      },
      {
         "name":"residential",
         "confidence":0.314032256603241
      },
   ],
}
```
## <a name="categorizing-images"></a>Classificazione delle immagini
Oltre ai tag e alla descrizione, l'API Visione artificiale restituisce le categorie in base tassonomia definite nelle versioni precedenti. Tali categorie sono organizzate in una tassonomia con gerarchie ereditarie padre-figlio. Tutte le categorie sono in inglese e possono essere usate singolarmente o con i nuovi modelli.

### <a name="the-86-category-concept"></a>Categoria basata su 86 concetti
In base a un elenco di 86 concetti illustrati nel diagramma seguente, le caratteristiche visivi presenti in un'immagine possono essere classificate a partire da un concetto ampio fino ad arrivare a uno più specifico. Per la classificazione completa in formato testo, vedere [Category Taxonomy](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy) (Tassonomia delle categorie).

![Analisi delle categorie](./Images/analyze_categories.png)

Image                                                  | Risposta
------------------------------------------------------ | ----------------
![Donna su un tetto](./Images/woman_roof.png)                 | people
![Foto di famiglia](./Images/family_photo.png)             | people_crowd
![Cucciolo di cane](./Images/cute_dog.png)                     | animal_dog
![Panorama montano](./Images/mountain_vista.png)       | outdoor_mountain
![Pane - Analisi visione](./Images/bread.png)       | food_bread

## <a name="identifying-image-types"></a>Identificazione dei tipi di immagine
Le immagini possono essere classificate in diversi modi. API Visione artificiale consente di impostare un flag booleano per indicare se un'immagine è in bianco e nero oppure a colori. L'API può anche impostare un flag per indicare se un'immagine è un disegno di linee o meno o se si tratta di un'immagine ClipArt e per indicarne la qualità, ad esempio in una scala compresa tra 0 e 3.

### <a name="clip-art-type"></a>Tipo ClipArt
Rileva se un'immagine è ClipArt o meno.  

Valore | Significato
----- | --------------
0     | Non-clip-art
1     | ambiguous
2     | normal-clip-art
3     | good-clip-art

Image|Risposta
----|----
![ClipArt formaggio - Analisi visione](./Images/cheese_clipart.png)|3 good-clip-art
![Casa con giardino - Analisi visione](./Images/house_yard.png)|0 Non-clip-art

### <a name="line-drawing-type"></a>Tipo disegno di linee
Rileva se un'immagine è un disegno di linee o meno.

Image|Risposta
----|----
![Disegno di leone - Analisi visione](./Images/lion_drawing.png)|True 
![Fiore - Analisi visione](./Images/flower.png)|False

### <a name="faces"></a>Visi
Rileva i visi in un'immagine e genera le coordinate del viso e il rettangolo per viso, genere ed età. Queste funzionalità visive sono un subset di metadati generati per il viso. Per altre informazioni sui metadati generati per i visi (identificazione del viso, rilevamento della posa e altro ancora), usare l'API Viso.  

Image|Risposta
----|----
![Viso di donna su tetto - Analisi visione](./Images/woman_roof_face.png) | [ { "age": 23, "gender": "Female", "faceRectangle": { "left": 1379, "top": 320, "width": 310, "height": 310 } } ]
![Viso di mamma e figlia - Analisi visione](./Images/mom_daughter_face.png) | [ { "age": 28, "gender": "Female", "faceRectangle": { "left": 447, "top": 195, "width": 162, "height": 162 } }, { "age": 10, "gender": "Male", "faceRectangle": { "left": 355, "top": 87, "width": 143, "height": 143 } } ]
![Viso foto di famiglia - Analisi visione](./Images/family_photo_face.png) | [ { "age": 11, "gender": "Male", "faceRectangle": { "left": 113, "top": 314, "width": 222, "height": 222 } }, { "age": 11, "gender": "Female", "faceRectangle": { "left": 1200, "top": 632, "width": 215, "height": 215 } }, { "age": 41, "gender": "Male", "faceRectangle": { "left": 514, "top": 223, "width": 205, "height": 205 } }, { "age": 37, "gender": "Female", "faceRectangle": { "left": 1008, "top": 277, "width": 201, "height": 201 } } ]


## <a name="domain-specific-content"></a>Contenuto specifico di dominio

Oltre all'assegnazione di tag e alla classificazione di primo livello, l'API Visione artificiale supporta anche informazioni specializzate (o specifiche di dominio). Le informazioni specializzate possono essere implementate come metodo autonomo o con la classificazione di alto livello. In tal modo è possibile perfezionare ulteriormente la tassonomia basata sulle 86 categorie con l'aggiunta di modelli specifici di dominio.

Le uniche informazioni specializzate supportate attualmente sono il riconoscimento di personaggi famosi e di monumenti. Si tratta di perfezionamenti specifici di dominio per le categorie persone e gruppo di persone e per la categoria monumenti nel mondo.

Per usare i modelli specifici di dominio, sono disponibili due opzioni.

### <a name="option-one---scoped-analysis"></a>Opzione 1 - Analisi con ambito
Analizza solo un modello scelto, con una chiamata a HTTP POST. Per questa opzione, se si conosce il modello da usare specificarne il nome per ottenere informazioni rilevanti solo per tale modello. È possibile ad esempio usare questa opzione per eseguire il riconoscimento solo di persone famose. La risposta contiene un elenco di personaggi potenzialmente corrispondenti, associati ai punteggi di attendibilità.

### <a name="option-two---enhanced-analysis"></a>Opzione 2 - Analisi avanzata
Esegue l'analisi per indicare dettagli aggiuntivi relativi alle categorie della tassonomia 86. Questa opzione è disponibile per essere usata in applicazioni in cui gli utenti desiderano ottenere l'analisi di immagini generiche oltre ai dettagli di uno o più modelli specifici di dominio. Quando questo metodo viene richiamato, lo strumento di classificazione della tassonomia 86 viene chiamato per primo. Se una delle categorie corrisponde a uno dei modelli noti/corrispondenti, viene eseguito un secondo passaggio della chiamata allo strumento di classificazione. Se ad esempio 'details=all' o "details" include 'celebrities', il metodo chiama lo strumento di classificazione dei personaggi famosi dopo la chiamata allo strumento di classificazione della tassonomia 86. Il risultato include tag che iniziano con 'people_'.

## <a name="generating-descriptions"></a>Generazione di descrizioni 
Gli algoritmi dell'API Visione artificiale analizzano il contenuto in un'immagine. Tale analisi costituisce la base per una descrizione visualizzata come linguaggio leggibile dall'utente in frasi complete. La descrizione riepiloga gli elementi trovati nell'immagine. Gli algoritmi dell'API Visione artificiale generano descrizioni diverse in base agli oggetti identificati nell'immagine. Tutte le descrizioni vengono valutate e per ognuna viene generato un punteggio di attendibilità. Viene quindi restituito un elenco dei punteggi di attendibilità in ordine decrescente. Un esempio di un bot che usa questa tecnologia per generare didascalie di immagine è disponibile [qui](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).  

### <a name="example-description-generation"></a>Generazione di descrizioni di esempio
![Edifici in bianco e nero](./Images/bw_buildings.png) '
```json
 Returned Json

'description':{
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a large city',
         'confidence':0.607638706850331
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a photo of a large city',
         'confidence':0.577256764264197
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a city',
         'confidence':0.538493271791207
      }
   ]   
   'description':[
      "tags":{
         "outdoor",
         "city",
         "building",
         "photo",
         "large",
      }
   ]
}
```

## <a name="perceiving-color-schemes"></a>Percezione delle combinazioni di colori
L'algoritmo di Visione artificiale estrae colori da un'immagine. I colori vengono analizzati in tre contesti diversi, ovvero primo piano, sfondo e complessivo. I colori vengono raggruppati in dodici colori principali dominanti, ovvero nero, blu, marrone, grigio, verde, arancione, rosa, viola, rosso, verde acqua, bianco e giallo. A seconda dei colori in un'immagine, il semplice bianco e nero oppure i colori principali possono essere restituiti in codici esadecimali.

Image                                                       | Primo piano |Background| Colori
----------------------------------------------------------- | --------- | ------- | ------
![Panorama montano](./Images/mountain_vista.png)            | Nero     | Nero   | Bianco
![Fiore - Analisi visione](./Images/flower.png)               | Nero     | Bianco   | Bianco, nero, verde
![Stazione ferroviaria - Analisi visione](./Images/train_station.png) | Nero     | Nero   | Nero

### <a name="accent-color"></a>Colore principale
Colore estratto da un'immagine pensato per rappresentare il colore più acceso tramite una combinazione di colori dominanti e di saturazione

Image                                                       | Risposta
----------------------------------------------------------- | ----
![Panorama montano](./Images/mountain_vista.png)            | #BC6F0F
![Fiore - Analisi visione](./Images/flower.png)               | #CAA501
![Stazione ferroviaria - Analisi visione](./Images/train_station.png) | #484B83


### <a name="black--white"></a>Bianco e nero
Flag booleano che indica se un'immagine è in bianco e nero o meno.

Image                                                      | Risposta
---------------------------------------------------------- | ----
![Edificio - Analisi visione](./Images/bw_buildings.png)      | True 
![Casa con giardino - Analisi visione](./Images/house_yard.png)      | False

## <a name="flagging-adult-content"></a>Contrassegno di contenuti per adulti
Le diverse categorie visive includono anche il gruppo di contenuto spinti e per adulti, che consente d rilevare materiali per adulti e di limitare la visualizzazione delle immagini con contenuto sessuale. Il filtro per il rilevamento del contenuto spinto e per adulti può essere impostato tramite un indicatore di scorrimento in base alle preferenze dell'utente.

## <a name="optical-character-recognition-ocr"></a>Riconoscimento ottico dei caratteri (OCR)
La tecnologia OCR rileva contenuto di testo in un'immagine ed estrae il testo identificato in un flusso di caratteri leggibile dal computer. È possibile usare il risultato per eseguire ricerche e per numerosi altri scopi, ad esempio compilazione di cartelle cliniche, sicurezza e servizi bancari. La lingua viene rilevata automaticamente. La tecnologia OCR consente di risparmiare tempo ed è molto pratica perché permette agli utenti di scattare foto del testo invece di trascriverlo.

OCR supporta 25 lingue, Le lingue supportate sono: arabo, cinese semplificato, cinese tradizionale, ceco, coreano, danese, finlandese, francese, giapponese, greco, inglese, italiano, norvegese, olandese, polacco, portoghese, rumeno, russo, serbo (cirillico e latino), slovacco, spagnolo, svedese, tedesco, turco e ungherese.

Se necessario, la funzione OCR corregge la rotazione del testo riconosciuto, espresso in gradi, intorno all'asse orizzontale dell'immagine. La funzione OCR indica le coordinate del riquadro per ogni parola, come illustrato nell'immagine seguente.

![Panoramica di OCR](./Images/vision-overview-ocr.png) Requisiti per OCR:
- Le dimensioni dell'immagine di input devono essere comprese tra 40 x 40 e 3200 x 3200 pixel.
- L'immagine non può superare i 10 megapixel.

L'immagine di input può essere ruotata di qualsiasi multiplo di 90 gradi oltre a un piccolo angolo di '40 gradi (massimo).

La precisione del riconoscimento del testo dipende dalla qualità dell'immagine. Una lettura non corretta potrebbe dipendere dalle situazioni seguenti:
- Immagini sfocate.
- Testo in corsivo o scritto a mano.
- Stili di caratteri artistici.
- Testo di piccole dimensioni.
- Sfondi complessi, ombre o riflessi sul testo o distorsione prospettica.
- Lettere maiuscole mancanti o di dimensioni eccessive all'inizio delle parole
- Testo barrato, pedici o apici.

 Limitazioni: Nelle fotografie dove il testo è predominante, si possono ottenere risultati falsi positivi da parole riconosciute parzialmente. In alcune foto, in particolare quelle in cui non è presente alcun testo, la precisione può variare notevolmente a seconda del tipo di immagine.

## <a name="recognize-handwritten-text"></a>Riconoscere testo scritto a mano
Questa tecnologia consente di rilevare ed estrarre testo scritto a mano da note, lettere, saggi, lavagne, moduli e così via. La tecnologia funziona per superfici e sfondi diversi, ad esempio carta bianca, memo gialli e lavagne.

Il riconoscimento del testo scritto a mano permette di risparmiare tempo e impegno e aiuta ad aumentare la produttività, consentendo di acquisire immagini del testo invece di doverlo trascrivere. Rende anche possibile la digitalizzazione delle note e di conseguenza l'implementazione di una ricerca semplice e rapida e la riduzione della quantità di carta usata.

Requisiti di input:
- Formati di immagine supportati: JPEG, PNG e BMP.
- Le dimensioni del file di immagine devono essere minori di 4 MB.
- Dimensioni dell'immagine devono essere almeno 40 x 40 pixel e al massimo 3200 x 3200 pixel.

Nota: questa tecnologia è attualmente disponibile in anteprima e solo per testo in lingua inglese.

## <a name="generating-thumbnails"></a>Generazione di anteprime
Un'anteprima è una rappresentazione di piccole dimensioni di un'immagine con le dimensioni originali. Diversi dispositivi, ad esempio telefoni, tablet e PC, comportano la necessità di layout e di dimensioni delle anteprime diversi per l'esperienza utente. Grazie a una funzione di ritaglio intelligente, l'API Visione artificiale consente di risolvere il problema.

Dopo il caricamento di un'immagine, viene generata un'anteprima di alta qualità e l'algoritmo dell'API Visione artificiale consente di analizzare gli oggetti all'interno dell'immagine. L'immagine viene quindi ritagliata per soddisfare i requisiti dell'area di interesse. L'output viene visualizzato all'interno di un framework speciale come illustrato nella figura seguente. L'anteprima generata può essere visualizzata con proporzioni diverse da quelle dell'immagine originale per rispondere alle esigenze dell'utente.

L'algoritmo per la generazione delle anteprime algoritmo funziona nel modo seguente:

1. Rimuove gli elementi di disturbo dall'immagine e riconosce l'oggetto principale, ovvero l'area di interesse.
2. Ritaglia l'immagine in base all'area di interesse identificata.
3. Modifica le proporzioni per adattare le dimensioni all'anteprima di destinazione.

![Anteprime](./Images/thumbnail-demo.png)

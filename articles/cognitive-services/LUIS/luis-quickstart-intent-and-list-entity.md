---
title: 'Esercitazione 4: estrarre corrispondenze del testo - entità elenco LUIS'
titleSuffix: Azure Cognitive Services
description: Ottenere i dati che corrisponde a un elenco predefinito di elementi. Ogni elemento nell'elenco può avere anche sinonimi che corrispondono esattamente
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 2ce6e7c796faf0c7377a33dabe1e8c05e81fde2f
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51280708"
---
# <a name="tutorial-4-extract-exact-text-matches"></a>Esercitazione 4: estrarre corrispondenze esatte del testo
Questa esercitazione spiega come ottenere dati corrispondenti a un elenco predefinito di elementi. Ogni elemento nell'elenco può includere un elenco di sinonimi. Per l'app HumanResources, un dipendente può essere identificato tramite diverse informazioni essenziali, ad esempio nome, indirizzo di posta elettronica, numero di telefono e codice fiscale federale degli Stati Uniti. 

L'app HumanResources deve stabilire quali sono i dipendenti che si spostano verso un edificio diverso. Per un'espressione relativa allo spostamento di un dipendente, LUIS stabilisce la finalità ed estrae il dipendente in modo che l'applicazione client crei un ordine standard per spostare il dipendente.

Quest'app usa un'entità elenco per estrarre il dipendente. Il dipendente può essere designato usando il nome, l'estensione del numero di telefono aziendale, il numero di telefono cellulare, l'indirizzo di posta elettronica o il codice fiscale federale degli Stati Uniti. 

Un'entità elenco è una scelta appropriata per questo tipo di dati quando:

* I valori dei dati sono un set noto.
* Il set non supera i [limiti](luis-boundaries.md) massimi di LUIS per questo tipo di entità.
* Il testo nell'espressione è una corrispondenza esatta con un sinonimo o il nome canonico. 

**In questa esercitazione si apprenderà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Usare l'app di esercitazione esistente
> * Aggiungere la finalità MoveEmployee
> * Aggiungere un'entità elenco 
> * Eseguire il training 
> * Pubblica
> * Ottenere finalità ed entità dall'endpoint

[!INCLUDE[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Usare l'app esistente
Continuare con l'app creata nell'ultima esercitazione denominata **HumanResources**. 

Se non si dispone dell'app HumanResources dell'esercitazione precedente, usare la procedura seguente:

1.  Scaricare e salvare il [file JSON dell'app](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-regex-HumanResources.json).

2. Importare il file JSON in una nuova app.

3. Nella scheda **Versioni** della sezione **Gestisci**, clonare la versione e denominarla `list`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. Poiché viene usato come parte della route dell'URL, il nome della versione non può contenere caratteri non validi per un URL. 


## <a name="moveemployee-intent"></a>Finalità MoveEmployee

1. [!INCLUDE[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selezionare **Create new intent** (Crea nuova finalità). 

3. Immettere `MoveEmployee` nella finestra di dialogo popup, quindi selezionare **Done** (Fine). 

    ![Screenshot della finestra di dialogo di creazione di una nuova finalità](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Aggiungere espressioni di esempio alla finalità.

    |Espressioni di esempio|
    |--|
    |move John W. Smith from B-1234 to H-4452|
    |mv john.w.smith@mycompany.com from office b-1234 to office h-4452|
    |shift x12345 to h-1234 tomorrow|
    |place 425-555-1212 in HH-2345|
    |move 123-45-6789 from A-4321 to J-23456|
    |mv Jill Jones from D-2345 to J-23456|
    |shift jill-jones@mycompany.com to M-12345|
    |x23456 to M-12345|
    |425-555-0000 to h-4452|
    |234-56-7891 to hh-2345|

    [ ![Schermata della pagina della finalità con le nuove espressioni evidenziate](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png) ](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png#lightbox)

    Tenere presente che numero e datetimeV2 sono stati aggiunti in un'esercitazione precedente e saranno etichettati automaticamente quando si trovano in ogni espressione di esempio.

    [!INCLUDE[Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="employee-list-entity"></a>Entità elenco dipendenti
Ora che la finalità **MoveEmployee** ha espressioni di esempio, LUIS deve capire cos'è un dipendente. 

Il nome _canonico_ per ogni elemento è il numero dipendente. Ecco alcuni sinonimi di esempio di ogni nome canonico per questo dominio: 

|Scopo del sinonimo|Valore del sinonimo|
|--|--|
|NOME|John W. Smith|
|Indirizzo di posta elettronica|john.w.smith@mycompany.com|
|Numero interno|x12345|
|Numero di telefono cellulare personale|425-555-1212|
|Codice fiscale federale degli Stati Uniti|123-45-6789|


1. Selezionare **Entities** (Entità) nel pannello di sinistra.

2. Selezionare **Create new entity** (Crea nuova entità).

3. Nella finestra popup relativa all'entità immettere `Employee` come nome dell'entità e **List** come tipo di entità. Selezionare **Operazione completata**.  

    [![](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png "Screenshot della finestra popup di creazione di una nuova entità")](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png#lightbox)

4. Nella pagina relativa all'entità dipendente immettere `Employee-24612` come nuovo valore.

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "Screenshot di immissione di un valore")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

5. Per i sinonimi, aggiungere i valori seguenti:

    |Scopo del sinonimo|Valore del sinonimo|
    |--|--|
    |NOME|John W. Smith|
    |Indirizzo di posta elettronica|john.w.smith@mycompany.com|
    |Numero interno|x12345|
    |Numero di telefono cellulare personale|425-555-1212|
    |Codice fiscale federale degli Stati Uniti|123-45-6789|

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "Screenshot dell'immissione di sinonimi")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

6. Immettere `Employee-45612` come nuovo valore.

7. Per i sinonimi, aggiungere i valori seguenti:

    |Scopo del sinonimo|Valore del sinonimo|
    |--|--|
    |NOME|Jill Jones|
    |Indirizzo di posta elettronica|jill-jones@mycompany.com|
    |Numero interno|x23456|
    |Numero di telefono cellulare personale|425-555-0000|
    |Codice fiscale federale degli Stati Uniti|234-56-7891|

## <a name="train"></a>Eseguire il training

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Pubblica

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Ottenere finalità ed entità dall'endpoint

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Andare alla fine dell'URL nell'indirizzo e immettere `shift 123-45-6789 from Z-1242 to T-54672`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni etichettate, per cui rappresenta un buon test e deve restituire la finalità `MoveEmployee` con l'oggetto `Employee` estratto.

  ```JSON
  {
    "query": "shift 123-45-6789 from Z-1242 to T-54672",
    "topScoringIntent": {
      "intent": "MoveEmployee",
      "score": 0.9882801
    },
    "intents": [
      {
        "intent": "MoveEmployee",
        "score": 0.9882801
      },
      {
        "intent": "FindForm",
        "score": 0.016044287
      },
      {
        "intent": "GetJobInformation",
        "score": 0.007611245
      },
      {
        "intent": "ApplyForJob",
        "score": 0.007063288
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00684710965
      },
      {
        "intent": "None",
        "score": 0.00304174074
      },
      {
        "intent": "Utilities.Help",
        "score": 0.002981
      },
      {
        "intent": "Utilities.Confirm",
        "score": 0.00212222221
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.00191026414
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0007461446
      }
    ],
    "entities": [
      {
        "entity": "123 - 45 - 6789",
        "type": "Employee",
        "startIndex": 6,
        "endIndex": 16,
        "resolution": {
          "values": [
            "Employee-24612"
          ]
        }
      },
      {
        "entity": "123",
        "type": "builtin.number",
        "startIndex": 6,
        "endIndex": 8,
        "resolution": {
          "value": "123"
        }
      },
      {
        "entity": "45",
        "type": "builtin.number",
        "startIndex": 10,
        "endIndex": 11,
        "resolution": {
          "value": "45"
        }
      },
      {
        "entity": "6789",
        "type": "builtin.number",
        "startIndex": 13,
        "endIndex": 16,
        "resolution": {
          "value": "6789"
        }
      },
      {
        "entity": "-1242",
        "type": "builtin.number",
        "startIndex": 24,
        "endIndex": 28,
        "resolution": {
          "value": "-1242"
        }
      },
      {
        "entity": "-54672",
        "type": "builtin.number",
        "startIndex": 34,
        "endIndex": 39,
        "resolution": {
          "value": "-54672"
        }
      }
    ]
  }
  ```

  Il dipendente è stato trovato e restituito come tipo `Employee` con un valore di risoluzione di `Employee-24612`.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stata creata una nuova finalità, sono state aggiunte espressioni di esempio e quindi è stato creato un elenco di entità per estrarre corrispondenze esatte del testo dalle espressioni. Dopo il training e la pubblicazione dell'app, una query per l'endpoint ha identificato la finalità e restituito i dati estratti.

> [!div class="nextstepaction"]
> [Aggiungere un'entità gerarchica all'app](luis-quickstart-intent-and-hier-entity.md)


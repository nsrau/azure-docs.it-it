---
title: Informazioni sul servizio Language Understanding (LUIS) in Azure | Microsoft Docs
description: Imparare come usare il servizio di Language Understanding (LUIS) per aggiungere la capacità di apprendimento automatico alle proprie applicazioni.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/22/2017
ms.author: v-geberr
ms.openlocfilehash: 1c68c586fb799a540f70804d181aa66b2bda9e97
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952539"
---
# <a name="what-is-language-understanding-luis"></a>Che cos'è il servizio Language Understanding (LUIS)?
Il servizio Language Understanding (LUIS) è un servizio basato su cloud che applica l'apprendimento automatico personalizzato al testo in linguaggio naturale parlato di un utente per prevedere il significato generale ed estrarre informazioni rilevanti e dettagliate. 

Un'applicazione client per LUIS può essere una qualsiasi applicazione di linguaggio parlato che comunica con un utente in linguaggio naturale per completare un compito. Esempi di applicazioni client includono app di social media, chatbot e applicazioni per desktop con funzionalità vocali.  

![Immagine concettuale di 3 applicazioni che inviano informazioni al servizio LUIS](./media/luis-overview/luis-entry-point.png)

## <a name="what-is-a-luis-app"></a>Che cos'è un'app LUIS?
Un'app LUIS contiene un modello progettato dall'utente di linguaggio naturale specifico per il dominio. È possibile avviare l'app LUIS con un modello di dominio precompilato, creare il proprio o unire parti di un dominio precompilato con le proprie informazioni personalizzate.

I [modelli di dominio precompilati](luis-how-to-use-prebuilt-domains.md) includono tutti questi elementi e sono un ottimo modo per iniziare a usare rapidamente il servizio LUIS.

L'app LUIS contiene anche le impostazioni di integrazione, i [collaboratori](luis-concept-collaborator.md) e le [versioni](luis-concept-version.md).

## <a name="using-a-luis-app"></a>Utilizzo di un'app LUIS
<a name="Accessing-LUIS"></a> Una volta pubblicata l'app LUIS, l'applicazione client invia le espressioni all'[endpoint API][endpoint-apis] LUIS e riceve i risultati di stima come risposte JSON.

Nel diagramma seguente, in primo luogo la chatbot client invia il testo utente di ciò che una persona intende esprimere con le proprie parole al servizio LUIS in una richiesta HTTP. In secondo luogo, LUIS applica il modello appreso al linguaggio naturale per interpretare l'input dell'utente e restituisce una risposta in formato JavaScript Object Notation (JSON). In terzo luogo, la chatbot client utilizza la risposta JSON per soddisfare le richieste dell'utente. 

![Immagini concettuali dell'attività combinata di LUIS con Chatbot](./media/luis-overview/luis-overview-process-2.png)

### <a name="example-of-json-endpoint-response"></a>Esempio di risposta dell'endpoint JSON

La risposta dell'endpoint JSON contiene almeno l'espressione della query e la finalità con il punteggio più alto. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>
## <a name="what-is-a-natural-language-model"></a>Che cos'è un modello di linguaggio naturale?
Un modello inizia con un elenco di intenzioni generali dell'utente, dette _finalità_, come "Prenota un volo" o "Contatta l'help desk." Fornire un testo di esempio dell'utente, denominato _espressioni di esempio_ per le finalità. Quindi, contrassegnare le parole o frasi significative nell'espressione, denominate _entità_.


Un modello comprende:

* **[finalità](#intents)**: categorie di finalità dell'utente (azione o risultato previsti)
* **[entità](#entities)**: determinati tipi di dati nelle espressioni, quali numero, posta elettronica o nome
* **[espressioni di esempio](#example-utterances)**: testo di esempio che un utente inserisce nell'applicazione client

### <a name="intents"></a>Finalità 
Una [finalità](luis-how-to-add-intents.md) o _intenzione_, è uno scopo o un obiettivo manifestato nell'espressione di un utente, come prenotare un volo, pagare una bolletta o trovare un articolo di giornale. Viene creata una finalità per ogni azione. Un'app di viaggi LUIS può definire una finalità denominata "PrenotaVolo". L'applicazione client può usare la finalità con il punteggio più alto per attivare un'azione. Ad esempio, quando LUIS restituisce la finalità "PrenotaVolo", l'applicazione client potrebbe attivare una chiamata API a un servizio esterno per la prenotazione di un biglietto aereo.

### <a name="entities"></a>Entità
Un'[entità](luis-how-to-add-entities.md) rappresenta informazioni dettagliate reperite nell'espressione pertinente alla richiesta dell'utente. Ad esempio, nell'espressione "Prenota un biglietto per Parigi", è richiesto un biglietto singolo e "Parigi" è una località. Vengono rilevate due entità: "un biglietto" che indica un biglietto singolo e "Parigi", che indica la destinazione. 

Dopo che LUIS restituisce le entità trovate nell'espressione dell'utente, l'applicazione client può usare l'elenco delle entità come parametri per attivare un'azione. Ad esempio, la prenotazione di un volo richiede entità come la destinazione del viaggio, la data e la compagnia aerea.

LUIS fornisce diversi metodi per identificare e classificare le entità.

* **Entità predefinite** Il servizio LUIS dispone di molti modelli di dominio predefiniti che includono finalità, espressioni ed [entità predefinite](luis-prebuilt-entities.md). È possibile usare le entità predefinite senza dover usare le finalità e le espressioni del modello predefinito. Le entità predefinite consentono di risparmiare tempo.

* **Entità personalizzate** LUIS offre diversi modi per identificare le proprie [entità](luis-concept-entity-types.md) personalizzate, comprese le entità apprese automaticamente, le entità specifiche o con valore letterale e una combinazione di entità apprese automaticamente e con valore letterale.

### <a name="example-utterances"></a>Espressioni di esempio
Un esempio di [espressione](luis-how-to-add-example-utterances.md) è un input di testo proveniente dall'utente necessario all'applicazione client per la comprensione. Potrebbe essere una frase, come "Prenota un biglietto per Parigi" o un frammento di frase, come "Prenotazione" o "Volo Parigi". Le espressioni non sono sempre ben formate e possono esserci molte variazioni di espressione per una particolare finalità. Aggiungere da 10 a 20 esempi di espressione ad ogni finalità e contrassegnare le entità in ogni espressione.

|Espressione utente di esempio|Finalità|Entità|
|-----------|-----------|-----------|
|"Prenota un volo per __Seattle__?"|PrenotaVolo|Seattle|
|"Quando __apre__ il negozio?"|OrariePosizioneNegozio|apre|
|"Programma una riunione alle ore __13:00__ con __Bob__ presso la distribuzione"|ProgrammaRiunione|13:00, Bob|

## <a name="improve-prediction-accuracy"></a>Migliorare l'accuratezza della previsione
Dopo che l'app LUIS è stata pubblicata e ha ricevuto espressioni utente reali, LUIS fornisce diversi metodi per migliorare l'accuratezza della previsione: [apprendimento attivo](#active-learning) di espressioni endpoint [elenchi di frasi](#phrase-lists) per l'inclusione delle parole nel dominio e [modelli](#patterns) per ridurre il numero di espressioni necessarie.

### <a name="active-learning"></a>Apprendimento attivo
Nel processo di [apprendimento attivo](luis-how-to-review-endoint-utt.md), LUIS consente all'utente di adattare l'app LUIS alle espressioni reali selezionando le espressioni ricevute in corrispondenza dell'endpoint per la revisione da parte dell'utente. È possibile accettare o correggere la previsione, l'impostazione e la ripubblicazione degli endpoint. LUIS apprende rapidamente con questo processo iterativo e richiede una quantità minima di tempo e impegno. 

### <a name="phrase-lists"></a>Elenchi di frasi 
LUIS offre [elenchi di frasi](luis-concept-feature.md) pertanto è possibile indicare parole o frasi importanti al dominio modello. LUIS usa questi elenchi per aggiungere ulteriore significato a tali parole e frasi che altrimenti non verrebbero trovate nel modello.

### <a name="patterns"></a>Modelli 
I modelli consentono di semplificare la raccolta di espressioni di una finalità in [modelli](luis-concept-patterns.md) comuni di scelta e ordine delle parole. Ciò permette a LUIS di imparare più rapidamente, avendo bisogno di meno espressioni di esempio per le finalità. I modelli sono un sistema ibrido di espressioni regolari e di espressioni apprese automaticamente. 

<a name="using-luis"></a>

## <a name="authoring-and-accessing-luis"></a>Creazione e accesso a LUIS
Compilare l'app LUIS dal sito web LUIS o programmaticamente con le API di [creazione](https://aka.ms/luis-authoring-apis), oppure usare entrambe le modalità a seconda dell'esigenza di creazione. Accedere all'app LUIS pubblicata dall'[endpoint](https://aka.ms/luis-endpoint-apis) della query. 

LUIS offre tre siti Web in tutto il mondo, a seconda della propria regione di creazione. La regione di creazione determina la regione di Azure in cui è possibile pubblicare l'app LUIS.
<!--
|Authoring region|Publishing region(s)|
|--|--|
|[www.luis.ai](https://www.luis.ai)|**U.S.**<br>West US<br>West US 2<br>East US<br>East US 2<br>South Central US<br>West Central US<br><br>**Asia**<br>Southeast Asia<br>East Asia<br><br>**South America**<br>Brazil South |
|[au.luis.ai](https://au.luis.ai)|Australia East|
|[eu.luis.ai](https://eu.luis.ai)|West Europe<br>North Europe|
-->

Altre [informazioni](luis-reference-regions.md) sulle regioni di creazione e pubblicazione.

## <a name="what-technologies-work-with-luis"></a>Quali sono le tecnologie supportate da LUIS?
LUIS supporta diverse tecnologie di Microsoft:

* [API Controllo ortografico Bing](../bing-spell-check/proof-text.md) fornisce la correzione del testo prima della previsione. 
* [Bot Framework] [ bot-framework] consente a un chatbot di comunicare con un utente tramite input di testo. Selezionare [3.x](https://github.com/Microsoft/BotBuilder) o [4.x](https://github.com/Microsoft/botbuilder-dotnet) SDK per un'esperienza bot completa.
* [QnA Maker][qnamaker] consente di combinare diversi tipi di testo in una base di conoscenze con domande e risposte.
* [Riconoscimento vocale](../Speech/home.md) converte le richieste di linguaggio parlato in testo. Dopo averle convertite in testo, LUIS elabora le richieste. Vedere [Speech SDK](https://aka.ms/csspeech) per altre informazioni.
* [Analisi del testo](../text-analytics/overview.md) offre l'analisi delle emozioni e l'estrazione dei dati delle frasi principali.

## <a name="next-steps"></a>Passaggi successivi
Creare una nuova app LUIS con un dominio [precompilato](luis-get-started-create-app.md) o [personalizzato](luis-quickstart-intents-only.md).

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/
---
title: Cos'è training e modello? - Custom Translator
titleSuffix: Azure Cognitive Services
description: 'Un modello è il sistema, che fornisce una traduzione per una coppia di lingue specifiche. Il risultato di un training riuscito è un modello. Durante il training di un modello, sono necessari tre set di dati che si escludono a vicenda: set di dati di training, set di dati di ottimizzazione e set di dati di test.'
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: v-rada
ms.openlocfilehash: e681d40a56bc481fce5a114513a579465ba07c85
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626908"
---
# <a name="what-are-trainings-and-models"></a>Cosa sono i training e i modelli?

Un modello è il sistema, che fornisce una traduzione per una coppia di lingue specifiche.
Il risultato di un training riuscito è un modello. Durante il training di un modello, sono necessari tre set di dati che si escludono a vicenda: set di dati di training, set di dati di ottimizzazione e set di dati di test. Possono anche essere specificati i dati del dizionario.

Se vengono specificati solo i dati di training durante l'accodamento di un training, Custom Translator assemblerà automaticamente i set di dati di test e di ottimizzazione. Verranno escluse 5000 frasi dai dati di training e utilizzate 2500 frasi per assemblare un set di ottimizzazione e di test.

## <a name="training-dataset-for-custom-translator"></a>Set di dati di training per Custom Translator

I documenti inclusi nel set di training vengono utilizzati da Custom Translator come base per la compilazione del modello. Durante l'esecuzione del training, le frasi presenti in questi documenti vengono allineate (o abbinate). È possibile comporre liberamente i set di documenti di training. È possibile includere in un unico modello i documenti che si ritiene siano di rilevanza tangenziale. Anche in questo caso escluderli in un altro per visualizzare l'impatto in [punteggio BLEU (Bilingual Evaluation Understudy)](what-is-bleu-score.md). Purché si mantengono costanti il set di ottimizzazione e di test, è possibile sperimentare la composizione del set di training. Questo approccio è un modo efficace per modificare la qualità del sistema di traduzione.

È possibile eseguire più training all'interno di un progetto e confrontare i [punteggi BLEU](what-is-bleu-score.md) in tutte le esecuzioni di training. Quando si eseguono più training per il confronto, assicurarsi che siano specificati gli stessi set di dati di ottimizzazione / test ogni volta. Assicurarsi anche di ispezionare manualmente anche i risultati nella scheda ["Test"](how-to-view-system-test-results.md).

## <a name="tuning-dataset-for-custom-translator"></a>Set di dati di ottimizzazione per Custom Translator

I documenti paralleli inclusi in questo set sono utilizzati da Custom Traslator per regolare il sistema di traduzione per ottenere risultati ottimali.

Il set di ottimizzazione viene usato durante il training per regolare tutti i parametri e i pesi del sistema di traduzione ai valori ottimali. Scegliere il set di ottimizzazione con attenzione: il set di ottimizzazione deve essere rappresentativo del contenuto dei documenti che si desidera tradurre in futuro. Il set di ottimizzazione ha un forte impatto sulla qualità delle traduzioni generate. L'ottimizzazione consente al sistema di traduzione di fornire traduzioni più vicine agli esempi forniti nel set di dati di ottimizzazione. Non sono necessarie più di 2500 frasi per il set di ottimizzazione. Per una qualità di traduzione ottimale, si consiglia di selezionare manualmente il set di ottimizzazione scegliendo la selezione di frasi più rappresentativa.

Durante la creazione del set di ottimizzazione, scegliere frasi che hanno una lunghezza significativa e rappresentativa delle frasi che si prevede di tradurre in futuro. Si dovrebbero anche scegliere frasi che hanno parole ed espressioni che si intende tradurre nella distribuzione approssimativa delle traduzioni future. In pratica, una frase lunga da 8 a 18 parole produrrà i risultati migliori, perché queste frasi contengono un contesto sufficiente per mostrare l'inflessione e offrono una lunghezza significativa, senza essere eccessivamente complesse.

Una buona descrizione del tipo di frasi da utilizzare nel set di ottimizzazione è la prosa: vere e proprie frasi scorrevoli. Non celle di tabella, poesie, liste di cose, non solo punteggiatura o numeri in una frase, ma linguaggio abituale.

Se si seleziona manualmente il set di dati di ottimizzazione, non dovrebbe avere le stesse frasi dei dati di training e di test. Il set di ottimizzazione ha un impatto significativo sulla qualità delle traduzioni: scegliere con attenzione le frasi.

Se non si è certi su cosa scegliere per il set di ottimizzazione, è sufficiente selezionare il set di training e lasciare che Custom Translator selezioni il set di ottimizzazione per l'utente. Se si consente che Custom Translator scelga automaticamente il set di ottimizzazione, verrà utilizzato un subset casuale di frasi dai tuoi documenti di training bilingue escludendo quelle stesse frasi dal materiale di training.

## <a name="testing-dataset-for-custom-translator"></a>Set di dati di test per Custom Translator

I documenti paralleli inclusi nel set di test sono utilizzati per calcolare il punteggio BLEU (Bilingual Evaluation Understudy). Questo punteggio indica la qualità del sistema di traduzione. Questo punteggio indica in realtà quanto le traduzioni effettuate dal sistema di traduzione risultante da questo training corrispondono alle frasi di riferimento del set di dati del test.

Il punteggio BLEU è una misura del delta tra la traduzione automatica e la traduzione di riferimento. Il suo valore va da 0 a 100. Un punteggio pari a 0 indica che nemmeno una singola parola di riferimento viene visualizzata nella traduzione. Un punteggio pari a 100 indica che la traduzione automatica corrisponde esattamente al riferimento: la stessa parola si trova nella stessa esatta posizione. Il punteggio che viene visualizzato è la media del punteggio BLEU per tutte le frasi del set di test.

Il set di test deve includere documenti paralleli in cui le frasi della lingua di destinazione sono le traduzioni più opportune delle corrispondenti frasi della lingua di origine. È possibile usare gli stessi criteri usati per comporre il set di ottimizzazione. Tuttavia, il set di test non ha alcun effetto sulla qualità del sistema di traduzione. Viene utilizzato esclusivamente per generare il punteggio BLEU per l'utente.

Non sono necessarie più di 2500 frasi, come per il set di test. Se si consente al sistema di scegliere automaticamente il set di test, verrà utilizzato un subset casuale di frasi dai tuoi documenti di training bilingue escludendo quelle stesse frasi dal materiale di training.

È possibile visualizzare le traduzioni personalizzate del set di test e confrontarle con le traduzioni fornite nel set di test, passando alla scheda di test all'interno di un modello.
---
title: Creare modelli di analisi del testo in Azure Machine Learning Studio | Microsoft Docs
description: Come creare modelli di analisi del testo in Azure Machine Learning Studio usando moduli di pre-elaborazione del testo, estrazione degli n-grammi o hashing delle caratteristiche
services: machine-learning
documentationcenter: ''
author: rastala
manager: jhubbard
editor: ''

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: roastala

---
# Creare modelli di analisi del testo in Azure Machine Learning Studio
È possibile usare Azure Machine Learning per creare modelli di analisi del testo e renderli operativi. Questi modelli consentono di risolvere, ad esempio, problemi di classificazione dei documenti o analisi di valutazione.

In un esperimento di analisi del testo è necessario in genere:

1. Pulire e pre-elaborare i set di dati di testo
2. Estrarre i vettori di caratteristiche numeriche dal testo pre-elaborato
3. Addestrare il modello di classificazione o regressione
4. Assegnare un punteggio e convalidare il modello
5. Distribuire il modello in produzione

In questa esercitazione si apprenderanno questi passaggi eseguendo un modello di analisi di valutazione mediante il set di dati di Amazon Book Reviews (vedere il documento di ricerca “Biographies, Bollywood, Boom-boxes and Blenders: Domain Adaptation for Sentiment Classification” di John Blitzer, Mark Dredze e Fernando Pereira; Association of Computational Linguistics (ACL), 2007). Questo set di dati è costituito da punteggi di recensione (1-2 o 4-5) e testo in formato libero. L'obiettivo consiste nella stima del punteggio di recensione: basso (1-2) o alto (4-5).

È possibile trovare gli esperimenti trattati in questa esercitazione nella raccolta Cortana Intelligence:

[Stimare le recensioni dei libri](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-1)

[Stimare le recensioni dei libri - Esperimento predittivo](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## Passaggio 1: Pulire e pre-elaborare i set di dati di testo
Iniziamo l'esperimento dividendo i punteggi di recensione nei bucket di categoria basso e alto per formulare il problema come classificazione a due classi. Si useranno i moduli [Edit Metadata](https://msdn.microsoft.com/library/azure/dn905986.aspx) e [Group Categorical Values](https://msdn.microsoft.com/library/azure/dn906014.aspx).

![Creazione dell'etichetta](./media/machine-learning-text-analytics-module-tutorial/create-label.png)

Quindi si pulirà il testo tramite il modulo [Preprocess Text](https://msdn.microsoft.com/library/azure/mt762915.aspx). La pulizia riduce il rumore nel set di dati, aiuta a trovare le funzioni più importanti e a migliorare l'accuratezza del modello finale. Vengono rimosse le parole non significative: parole comuni, come articoli e preposizioni, nonché numeri, caratteri speciali, caratteri duplicati, indirizzi di posta elettronica e URL. Si converte inoltre il testo in minuscolo, si lemmatizzano le parole e si individuano i delimitatori delle frasi che vengono poi indicati dal simbolo "|||" nel testo pre-elaborato.

![Pre-elaborazione del testo](./media/machine-learning-text-analytics-module-tutorial/preprocess-text.png)

Se si desidera usare un elenco di parole non significative personalizzato? È possibile passarlo come input facoltativo. È inoltre possibile usare un'espressione regolare con sintassi C# personalizzata per sostituire le sottostringhe e rimuovere parole da parte del discorso: nomi, verbi o aggettivi.

Dopo aver completato la pre-elaborazione, si suddividono i dati in set di addestramento e set di test.

## Passaggio 2: Estrarre vettori di caratteristiche numeriche dal testo pre-elaborato
Per compilare un modello per i dati di testo è necessario in genere convertire il testo in formato libero in vettori di caratteristiche numeriche. In questo esempio si usa il modulo [Extract N-Gram Features from Text](https://msdn.microsoft.com/library/azure/mt762916.aspx) per trasformare i dati di testo in tale formato. Il modulo accetta una colonna di parole separate da spazi e calcola un dizionario di parole, o n-grammi di parole, che vengono visualizzate nel set di dati. Quindi conta il numero di volte in cui ogni parola, o n-gramma, compare in ogni record e crea vettori di caratteristiche da questi conteggi. In questa esercitazione impostiamo la dimensione dell'n-gramma su 2 quindi i nostri vettori di caratteristiche includono singole parole e combinazioni di due parole consecutive.

![Estrazione degli n-grammi](./media/machine-learning-text-analytics-module-tutorial/extract-ngrams.png)

Ai conteggi di n-grammi si applica la ponderazione TF*IDF (frequenza del termine, frequenza inversa del documento). Questo approccio aggiunge il peso delle parole che compaiono frequentemente in un singolo record ma sono rare nell'intero set di dati. Altre opzioni sono la ponderazione binaria, TF e grafica.

Funzioni di testo come queste sono spesso caratterizzate da alta dimensionalità. Ad esempio, se il corpo ha 100.000 parole univoche, lo spazio di funzioni avrà 100.000 dimensioni o più se vengono usati gli n-grammi. Il modulo Extract N-Gram Features offre un gruppo di opzioni per ridurre la dimensionalità. È possibile scegliere di escludere le parole che sono brevi o lunghe o troppo insolite o frequenti per avere un valore predittivo significativo. In questa esercitazione si escludono gli n-grammi che vengono visualizzati in meno di 5 record o in più dell'80% dei record.

Inoltre è possibile usare la selezione delle funzioni per selezionare solo le funzioni che sono maggiormente correlate con il target di stima. Si usa la selezione di funzioni chi quadro per selezionare 1000 funzioni. È possibile visualizzare il vocabolario di parole o n-grammi selezionati facendo clic sull'output giusto del modulo Extract N-Gram Features.

In alternativa all'uso al modulo Extract N-Gram Features è possibile usare il modulo Feature Hashing. Si noti tuttavia che [Feature Hashing](https://msdn.microsoft.com/library/azure/dn906018.aspx) non dispone di capacità integrate di selezione delle funzioni o di ponderazione TF*IDF.

## Passaggio 3: Addestrare il modello di classificazione o regressione
Il testo è stato ora trasformato in colonne di caratteristiche numeriche. Il set di dati contiene ancora le colonne di stringhe dalle fasi precedenti, perciò usiamo Select Columns in Dataset per escluderle.

Usiamo poi [Two-Class Logistic Regression](https://msdn.microsoft.com/library/azure/dn905994.aspx) per stimare il target: punteggio di recensione alto o basso. A questo punto il problema di analisi del testo è stato trasformato in un normale problema di classificazione. È possibile usare gli strumenti disponibili in Azure Machine Learning per migliorare il modello. Ad esempio è possibile sperimentare diversi classificatori per scoprire l'accuratezza dei loro risultati o usare l'ottimizzazione con iperparametri per migliorare l'accuratezza.

![Addestramento e assegnazione dei punteggi](./media/machine-learning-text-analytics-module-tutorial/scoring-text.png)

## Passaggio 4: Assegnare un punteggio e convalidare il modello
Come si convalida il modello addestrato? Si assegna un punteggio rispetto al set di dati di test e si valuta l'accuratezza. Tuttavia, il modello ha appreso il vocabolario degli n-grammi e i loro pesi del set di dati di addestramento. Pertanto, sarà necessario usare quel vocabolario e quei pesi per l'estrazione delle funzioni dai dati di test, invece di creare il vocabolario di nuovo. Si aggiunge perciò il modulo Extract N-Gram Features al ramo di assegnazione del punteggio dell'esperimento, si connette il vocabolario di output dal ramo di addestramento e si imposta la modalità di vocabolario in sola lettura. Si disattiva inoltre il filtro di n-grammi per frequenza impostando il minimo su 1 istanza e il massimo su 100% e si disattiva la selezione delle funzioni.

Dopo che la colonna di testo nei dati di test è stata trasformata in colonne di caratteristiche numeriche, si escludono le colonne stringa delle fasi precedenti come nel ramo di addestramento. Si usa poi il modulo Score Model per eseguire stime e il modulo Evaluate Model per valutare l'accuratezza.

## Passaggio 5: Distribuire il modello in produzione
Il modello è quasi pronto per essere distribuito nell'ambiente di produzione. Se viene distribuito come servizio Web, accetta una stringa di testo in formato libero come input e restituisce una stima "alta" o "bassa". Usa il vocabolario di n-grammi appreso per trasformare il testo in funzioni e il modello di regressione logistica addestrato per effettuare una previsione da queste funzioni.

Per configurare l'esperimento predittivo è innanzitutto necessario salvare il vocabolario di n-grammi come set di dati e il modello di regressione logistica addestrato del ramo di addestramento dell'esperimento. Quindi si salva l'esperimento usando "Salva con nome" per creare un grafico per l'esperimento predittivo. Si rimuove il modulo Split Data e il ramo di addestramento dall'esperimento. Quindi si collega il vocabolario di n-grammi e il modello salvati in precedenza rispettivamente ai moduli Extract N-Gram Features e Score Model. Si rimuove anche il modulo Evaluate Model.

Si inseriscono le colonne selezionate nel modulo Select Columns in Dataset prima del modulo Preprocess Text per rimuovere la colonna di etichette e si deseleziona l'opzione "Aggiungi colonna punteggio al set di dati" nel modulo Score Model. In questo modo il servizio Web non richiede l'etichetta che tenta di prevedere e non riproduce le funzioni di input come risposta.

![Esperimento predittivo](./media/machine-learning-text-analytics-module-tutorial/predictive-text.png)

Ora abbiamo un esperimento che può essere pubblicato come servizio Web e chiamato mediante le API di richiesta-risposta o di esecuzione in batch.

## Passaggi successivi
Per informazioni sui moduli di analisi del testo, vedere la [documentazione su MSDN](https://msdn.microsoft.com/library/azure/dn905886.aspx).

<!---HONumber=AcomDC_0914_2016-->
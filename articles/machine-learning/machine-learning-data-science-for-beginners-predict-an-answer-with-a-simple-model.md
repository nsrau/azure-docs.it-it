<properties
   pageTitle="Prevedere una risposta con un modello semplice - modello di regressione | Microsoft Azure"
   description="È possibile scoprire come creare un modello semplice per prevedere il prezzo di un diamante guardando il quarto video di Analisi scientifica dei dati per principianti. Include una regressione lineare di base con i dati di destinazione."                                  
   keywords="creare un modello, modello semplice, semplice modello di dati, previsione prezzi, modello di regressione semplice"
   services="machine-learning"
   documentationCenter="na"
   authors="brohrer-ms"
   manager="jhubbard"
   editor="cjgronlund"/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2016"
   ms.author="cgronlun;brohrer;garye"/>

# Prevedere una risposta con un modello semplice

## Video 4: Analisi scientifica dei dati per principianti

È possibile scoprire come creare un modello semplice per prevedere il prezzo di un diamante guardando il quarto video della serie Analisi scientifica dei dati per principianti. Verrà rappresentato un modello di regressione con i dati di destinazione.

Per trarre il meglio dalla serie è consigliabile guardare i video in ordine. [L'elenco dei video è disponibile qui](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-series-predict-an-answer-with-a-simple-model]

## Altri video della serie

*Analisi scientifica dei dati per principianti* è una rapida introduzione all'analisi scientifica dei dati in cinque brevi video.

  * Video 1: [5 domande a cui può rispondere l'analisi scientifica dei dati](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min e 14 sec)*
  * Video 2: [Verifica della preparazione dei dati per l'analisi scientifica dei dati](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min e 56 sec)*
  * Video 3: [Porre una domanda a cui è possibile rispondere con i dati](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min e 17 sec)*
  * Video 4: Prevedere una risposta con un modello semplice
  * Video 5: [Copiare il lavoro di altre persone per l'analisi scientifica dei dati](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min e 18 sec)*

## Trascrizione: Prevedere una risposta con un modello semplice

Benvenuti al quarto video della serie "Analisi scientifica dei dati per principianti". In questa occasione verrà creato un modello semplice e realizzata una previsione.

Un *modello* non è altro che una storia semplificata dei dati. Adesso spiegherò cosa intendo.

## Raccolta di dati rilevanti, accurati, connessi, in quantità sufficiente

Si pensi all'acquisto di un diamante. Possiedo un anello che apparteneva a mia nonna con un'incastonatura da 1,35 carati e vorrei farmi un'idea sul suo prezzo. Prendo un blocco note e una penna nella gioielleria e scrivo il prezzo di tutti i diamanti e il relativo peso in carati. Iniziando dal primo diamante, pesa 1,01 carati e costa $ 7.366.

Adesso faccio la stessa cosa con tutti gli altri diamanti presenti in negozio.

![Colonne di dati relativi ai diamanti](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

L'elenco presenta quindi due colonne. Ciascuna colonna contiene un attributo diverso - peso in carati e prezzo - e ciascuna riga è un unico punto di dati che rappresenta un unico diamante.

In questo modo è stata creato un piccolo set di dati, ovvero una tabella. Si noti che questa tabella soddisfa i criteri di qualità:

* I dati sono **rilevanti**: il peso è senza dubbio correlato al prezzo
* Sono **accurati**: i prezzi appuntati sono stati verificati due volte
* Sono **connessi**: nessuna delle colonne contiene spazi vuoti
* E, come illustrato, la quantità dei dati è **sufficiente** per rispondere alla domanda

## Porre una domanda ben strutturata

La domanda verrà adesso posta in modo ben strutturato: "Quale sarà il costo di acquisto di un diamante da 1,35 carati?"

Nell'elenco non c'è un diamante da 1,35 carati, quindi sarà necessario usare gli altri dati per ottenere una risposta a questa domanda.

## Fare un grafico dei dati esistenti

Per prima cosa verrà disegnata una linea numerica orizzontale, detta asse, su cui saranno indicati i pesi. L'intervallo dei pesi va da 0 a 2, quindi verranno disegnati una linea che copre questo intervallo e dei trattini per ciascun mezzo carato.

Adesso, per registrare il prezzo verrà tracciato un asse verticale che sarà connesso all'asse orizzontale del peso. L'asse verticale sarà suddiviso in unità di dollari. A questo punto viene fuori un set di assi coordinati.

![Assi del peso e del prezzo](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Questi dati verranno adesso convertiti in un *grafico di dispersione*, un'ottima soluzione per visualizzare i set di dati numerici.

Per il primo punto di dati verrà tracciata una linea verticale fino a 1,01 carati. Poi, una linea orizzontale fino a $ 7.366. Laddove si intersecano, verrà disegnato un punto. Questo rappresenta il primo diamante.

Adesso farò la stessa cosa per ogni diamante presente nell'elenco. Una volta terminato, questo è quello che si ottiene: un mucchio di punti, uno per ogni diamante.

![Grafico a dispersione](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## Disegnare il modello tramite i punti dei dati

Dando un'occhiata veloce ai punti, la raccolta sembra una linea confusa e grossa. Usando il pennarello è possibile tracciare una linea retta che la attraversi.

In questo modo si crea un *modello*. È come se si prendesse il modo reale e si realizzasse una versione cartacea semplicistica dello stesso. Adesso la versione cartacea è sbagliata, la linea non passa per tutti i punti di dati, ma è una semplificazione utile.

![Linea di regressione lineare](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Il fatto che non tutti i punti si trovino esattamente sulla linea va bene. Gli esperti di gestione dati spiegano tutto questo affermando che esiste il modello, ovvero la linea, e che a ogni punto vengono associati alcuni *disturbi* o *varianze*. Vi è quindi la perfetta relazione sottostante e vi è il mondo reale dinamico che aggiunge disturbo e incertezza.

Dal momento che si cerca di rispondere alla domanda *Quanto costa?*, è possibile parlare di *regressione*. E visto che viene usata una linea dritta, si tratta di una *regressione lineare*.

## Usare il modello per trovare la risposta

Una volta pronto il modello, è possibile porgli la domanda: Quando costerà un diamante da 1,35 carati?

Per rispondere a questa domanda, occorre partire da 1,35 carati e disegnare una linea verticale. Esattamente nel punto in cui incrocia la linea del modello, verrà tracciata una linea orizzontale verso l'asse dei dollari. Va a colpire proprio i 10.000 dollari. Boom! Quella è la risposta: un diamante da 1,35 carati costa circa $ 10.000.

![Individuare la risposta a partire dal modello](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## Creare un intervallo di confidenza

È naturale chiedersi quanto precisa sia questa previsione. È utile sapere se il diamante da 1,35 carati sarà più vicino ai $ 10.000 oppure se costerà molto più o molto meno. Per capirlo, sarà sufficiente disegnare un inviluppo attorno alla linea di regressione che include la maggior parte dei punti. Questo inviluppo viene detto *intervallo di confidenza*: si è abbastanza sicuri che i prezzi rientrino in questo inviluppo, poiché è stato così per la maggior parte degli stessi prezzi in precedenza. È possibile disegnare altre due linee orizzontali da cui la linea 1,35 carati incrocia la parte superiore e inferiore di quell'inviluppo.

![Creare un intervallo di confidenza](./media/machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

A proposito dell'intervallo di confidenza è possibile affermare con sicurezza che il prezzo di un diamante da 1,35 carati costa circa $ 10.000, ma potrebbe scendere fino a $ 8.000 e salire fino a $ 12.000.

## Ecco fatto, senza matematica, né computer

Abbiamo svolto un lavoro scientifico semplicemente disegnando:

* Abbiamo posto una domanda a cui è stato possibile rispondere con i dati
* È stato creato un *modello* usando una *regressione lineare*
* È stata realizzata una *previsione*, completa di *intervallo di confidenza*

senza matematica, né computer.

Se fossero state necessarie ulteriori informazioni, ad esempio...

* il taglio del diamante
* le variazioni di colore (quanto si avvicina al bianco il diamante)
* il numero di inclusioni nel diamante

...avremmo avuto più colonne. In quel caso, la matematica diventa utile. Se le colonne sono più di due, è difficile disegnare punti su carta. La matematica consente di adattare una determinata linea o un determinato piano ai dati in modo molto preciso.

E se al posto di una semplice manciata diamanti, si trattasse di duemila o due milioni, allora un lavoro al computer sarebbe molto più veloce.

Oggi l'argomento del video è stata la creazione di una regressione lineare e la realizzazione di una previsione usando i dati.

Anche gli altri video della serie "Analisi scientifica dei dati per principianti" di Microsoft Azure Machine Learning meritano di essere visti.



## Passaggi successivi

  * [È possibile effettuare il primo esperimento di analisi scientifica dei dati con Azure Machine Learning](machine-learning-create-experiment.md)
  * [È possibile ottenere un'introduzione a Machine Learning in Microsoft Azure](machine-learning-what-is-machine-learning.md)

<!---HONumber=AcomDC_0914_2016-->
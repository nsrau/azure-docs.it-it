<properties
	pageTitle="Progettazione di funzioni in Cortana Analytics Process | Microsoft Azure" 
	description="Illustra le finalità della progettazione di funzioni e offre esempi del relativo ruolo nel processo di miglioramento dei dati di Machine Learning."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="zhangya;bradsev" />


# Progettazione di funzionalità in Cortana Analytics Process 

Questo argomento illustra le finalità della progettazione di funzioni e offre esempi del relativo ruolo nel processo di miglioramento dei dati di Machine Learning. Gli esempi utilizzati per illustrare questo processo sono tratti da Azure Machine Learning Studio.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]

Questo **menu** fornisce collegamenti ad argomenti che descrivono come creare funzionalità per dati in diversi ambienti. Questa attività è un passaggio del [Processo di analisi scientifica dei dati per i team (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

La progettazione di funzionalità tenta di aumentare la capacità predittiva degli algoritmi di apprendimento tramite la creazione di funzionalità da dati non elaborati che contribuiscono a semplificare il processo di apprendimento. La progettazione e la selezione delle funzionalità sono parti del processo TDSP descritto in [Informazioni sul Processo di analisi scientifica dei dati per i team](data-science-process-overview.md) La progettazione e la selezione delle funzionalità sono parti del passaggio **Sviluppare funzionalità** del processo CAP.
* **Progettazione di funzionalità**: questo processo prova a creare altre funzioni rilevanti dalle funzioni non elaborate esistenti nei dati e ad aumentare le potenzialità predittive dell'algoritmo di apprendimento.
* **Selezione di funzionalità**: questo processo seleziona il subset principale delle funzionalità dei dati originali nel tentativo di ridurre la dimensionalità del problema di training.

In genere **la progettazione di funzioni** viene applicata innanzitutto per generare altre funzioni e quindi viene eseguito il passaggio di **selezione delle funzioni** per eliminare quelle irrilevanti, ridondanti o altamente correlate.

I dati di training usati in Machine Learning spesso possono essere migliorati con l'estrazione di funzioni dai dati non elaborati raccolti. Un esempio di funzione progettata nel contesto dell'apprendimento della modalità di classificazione delle immagini dei caratteri scritti a mano è la creazione di una mappa della densità di bit costruita dai dati di distribuzione dei bit non elaborati. La mappa può essere utile per individuare in modo più efficiente i bordi dei caratteri rispetto all'uso della distribuzione non elaborata.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## Creazione di funzioni dai dati: progettazione di funzioni

I dati di training sono costituiti da una matrice composta da esempi (record o osservazioni archiviate in righe), ognuno dei quali presenta un set di funzioni (variabili o campi archiviati in colonne). Le funzioni specificate nella progettazione sperimentale caratterizzeranno i modelli nei dati. Anche se molti dei campi dati non elaborati possono essere inclusi direttamente nel set di funzioni selezionate usato per il training di un modello, spesso è necessario costruire altre funzioni (progettate) dalle funzioni presenti nei dati non elaborati per generare un set di dati di training avanzato.

Il tipo di funzioni da creare per migliorare il set di dati durante il training di un modello sono le funzioni progettate che migliorano il training e forniscono informazioni che consentono di differenziare meglio i modello nei dati Le nuove funzioni dovrebbero fornire informazioni aggiuntive che non vengono chiaramente acquisite o non risultano facilmente apparenti nel set di funzioni originali o esistenti. Questo processo è tuttavia una sorta di artificio. Le decisioni valide e produttive richiedono spesso una certa esperienza specifica.

Quando si inizia a usare Azure Machine Learning, è più facile afferrare questo processo in modo concreto tramite gli esempi disponibili in Studio. Di seguito ne vengono presentati due:

* Un esempio di regressione basato sulla [stima del numero di noleggi di biciclette](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) in un esperimento controllato in cui sono noti i valori di destinazione.
* Un esempio di classificazione di data mining del testo tramite [Feature Hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/).

### Esempio 1: Aggiunta di funzioni temporali per il modello di regressione ###

Per dimostrare come progettare le funzioni per un'attività di regressione, si userà l'esperimento "Demand forecasting of bikes" in Azure Machine Learning Studio. L'obiettivo di questo esperimento è stimare la domanda di biciclette, ovvero il numero di noleggi di biciclette nell'arco di un mese, un giorno o un'ora specifica. Come dati di input non elaborati si usa il set di dati "Bike Rental UCI dataset". Questo set di dati si basa su dati reali della società Capital Bikeshare che gestisce una rete di noleggio di biciclette a Washington DC, negli Stati Uniti. Il set di dati rappresenta il numero di noleggi di biciclette in un'ora specifica del giorno negli anni 2011 e 2012 r include 17379 righe e 17 colonne. Il set di funzioni non elaborate include le condizioni meteorologiche (temperatura/umidità/velocità del vento) e il tipo di giorno (festività/giorno feriale). Il campo per la stima è "cnt", un conteggio che rappresenta i noleggi di biciclette in un'ora specifica e compreso nell'intervallo da 1 a 977.

Allo scopo di costruire funzioni efficaci nei dati di training, vengono compilati quattro modelli di regressione con lo stesso algoritmo, ma con quattro diversi set di dati di training. I quattro set di dati rappresentano gli stessi dati di input non elaborati, ma con un numero crescente di set di funzioni. Queste funzioni sono raggruppate in quattro categorie:

1. A = funzioni meteo + festività + giorno feriale + fine settimana per la giornata prevista.
2. B = numero di biciclette noleggiate in ognuna delle 12 ore precedenti.
3. C = numero di biciclette noleggiate in ognuno dei 12 giorni precedenti alla stessa ora.
4. D = numero di biciclette noleggiate in ognuna delle 12 settimane precedenti nello stesso giorno e alla stessa ora.

Oltre al set di funzioni A, che esiste già nei dati non elaborati originali, gli altri tre set di funzioni vengono creati tramite il processo di progettazione delle funzioni. Il set di funzioni B acquisisce la domanda di biciclette molto recente. Il set di funzioni C acquisisce la domanda di biciclette in un particolare orario. Il set di funzioni D acquisisce la domanda di biciclette in un particolare orario e giorno della settimana. Ognuno dei quattro set di dati di training include rispettivamente il set di funzioni A, A+B, A+B+C e A+B+C+D.

Nell'esperimento di Azure Machine Learning questi quattro set di dati vengono creati dai quattro rami del set di dati di input pre-elaborati. A eccezione del ramo all'estrema sinistra, ognuno di questi rami contiene un modulo [Execute R Script](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) in cui un set di funzioni derivate (set B, C e D) viene rispettivamente costruito e aggiunto al set di dati importato. La figura seguente illustra lo script R usato per creare il set di funzioni B nel secondo ramo a sinistra.

![creare funzioni](./media/machine-learning-data-science-create-features/addFeature-Rscripts.png)

Il confronto dei risultati delle prestazioni dei quattro modelli è riepilogato nella tabella seguente. I risultati migliori vengono forniti dalle funzioni A+B+C. Si noti che il tasso di errore diminuisce quando si include un set di funzioni aggiuntivo nei dati di training. Verifica la stima secondo cui il set di funzioni B, C fornisce altre informazioni rilevanti per l'attività di regressione. L'aggiunta della funzioni D non sembra tuttavia fornire un'altra riduzione del tasso di errore.

![confronto dei risultati](./media/machine-learning-data-science-create-features/result1.png)

### <a name="example2"></a> Esempio 2: Creazione di funzionalità per il data mining del testo  

La progettazione di funzioni viene ampiamente applicata nelle attività correlate al data mining del testo, ad esempio la classificazione di documenti e l'analisi del sentiment. Ad esempio, quando si vogliono classificare documenti in diverse categorie, un tipico presupposto è il fatto che le parole o le frasi incluse in una categoria di documenti sono presenti con minore probabilità in un'altra categoria di documenti. In altre parole, la frequenza di distribuzione di parole/frasi è in grado di caratterizzare diverse categorie di documenti. Poiché in genere nelle applicazioni di data mining del testo singole parti del contenuto di testo vengono usate come dati di input, per creare le funzioni che comportano frequenze di parole/frasi è necessario usare il processo di progettazione delle funzioni.

Per completare questa attività, si applica una tecnica definita **hashing di funzioni** per trasformare in modo efficiente le funzioni di testo arbitrarie in indici. Anziché associare ogni funzioni di testo (parole/frasi) a un indice particolare, questo metodo applica una funzione hash alle funzioni e usa direttamente i relativi valori hash come indici.

In Azure Machine Learning è disponibile un modulo [Feature Hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) che crea in modo pratico queste funzioni basate su parole/frasi. La figura seguente mostra un esempio dell'uso di questo modulo. Il set di dati di input contiene due colonne: la classificazione dei libri da 1 a 5 e il contenuto effettivo della recensione. L'obiettivo del modulo [Feature Hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) è recuperare una serie di nuove funzioni che mostrino la frequenza di occorrenza delle parole/frasi corrispondenti nella recensione di un libro particolare. Per usare questo modulo, è necessario completare i passaggi seguenti:

* Selezionare innanzitutto la colonna che contiene il testo di input ("Col2" in questo esempio).
* Impostare quindi "Hashing bitsize" su 8, che equivale alla creazione di 2^8=256 funzioni. Per le parole/frasi in tutto il testo verrà generato un hash per 256 indici. Il parametro "Hashing bitsize" è compreso nell'intervallo da 1 a 31. Con l'impostazione di un numero maggiore, è meno probabile che per le parole/frasi venga generato un hash nello stesso indice.
* Infine, impostare il parametro "N-grams" su 2. In questo modo si ottiene la frequenza di occorrenze di unigrammi (una funzione per ogni singola parola) e di digrammi (una funzione per ogni coppia di valori adiacenti) dal testo di input. L'intervallo del parametro "N-grams" è compreso tra 0 e 10 e indica il numero massimo di parole sequenziali da includere in una funzione.  

![Modulo "Feature Hashing"](./media/machine-learning-data-science-create-features/feature-Hashing1.png)

La figura seguente mostra l'aspetto delle nuove funzioni.

![Esempio di "Feature Hashing"](./media/machine-learning-data-science-create-features/feature-Hashing2.png)


## Conclusioni

Le funzioni progettate e selezionate migliorano l'efficienza del processo di training che tenta di estrarre le informazioni essenziali contenute nei dati. Migliorano anche le potenzialità di questi modelli per la classificazione accurata dei dati di input e per la stima più affidabile dei risultati di interesse. Progettazione e selezione delle funzioni possono anche combinarsi per rendere l'apprendimento più computazionalmente trattabile. Questa operazione viene eseguita tramite il miglioramento e la successiva riduzione del numero di funzioni richieste per calibrare o eseguire il training di un modello. Da un punto di vista matematico, le funzioni selezionate per eseguire il training di un modello sono costituite da un set minimo di variabili indipendenti che spiegano i modelli nei dati e quindi stimano correttamente i risultati.

Si noti che non sempre è necessario eseguire la progettazione o la selezione delle funzioni. La necessità o meno di questi passaggi dipende dai dati da raccogliere, dagli algoritmi scelti e dall'obiettivo dell'esperimento.
 

<!---HONumber=AcomDC_0622_2016-->
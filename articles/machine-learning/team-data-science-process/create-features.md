---
title: Progettazione di caratteristiche in data science - Processo di data science per i team
description: Illustra le finalità della progettazione di caratteristiche e offre esempi del relativo ruolo nel processo di miglioramento dei dati di Machine Learning.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 5767399189e4ed5168fbcc083ef9b4830cac1421
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138977"
---
# <a name="feature-engineering-in-data-science"></a>Progettazione di caratteristiche in data science
Questo articolo illustra le finalità della progettazione di caratteristiche e offre esempi del relativo ruolo nel processo di miglioramento dei dati di Machine Learning. Gli esempi utilizzati per illustrare questo processo sono tratti da Azure Machine Learning Studio. 

Questa attività è un passaggio del [processo di data science per i team (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

La progettazione di caratteristiche ha l'obiettivo di aumentare la capacità predittiva degli algoritmi di apprendimento creando caratteristiche da dati non elaborati in modo da semplificare il processo di apprendimento. La progettazione e la selezione di caratteristiche sono parti del processo di data science per i team descritto in [Informazioni sul processo di data science per i team](overview.md) La progettazione e la selezione di caratteristiche sono parti del passaggio di **sviluppo delle caratteristiche** del processo di data science per i team. 

* **progettazione di caratteristiche**: questo processo tenta di creare altre caratteristiche rilevanti a partire dalle caratteristiche non elaborate esistenti nei dati e di aumentare la capacità predittiva dell'algoritmo di apprendimento.
* **selezione di caratteristiche**: questo processo seleziona il subset principale delle caratteristiche dei dati originali nel tentativo di ridurre la dimensionalità del problema di training.

In genere, viene prima applicata la **progettazione di caratteristiche** per generare altre caratteristiche e quindi viene eseguito il passaggio di **selezione delle caratteristiche** per eliminare quelle irrilevanti, ridondanti o altamente correlate.

I dati di training usati in Machine Learning spesso possono essere migliorati con l'estrazione di caratteristiche dai dati non elaborati raccolti. Un esempio di caratteristica progettata nel contesto dell'apprendimento della modalità di classificazione delle immagini dei caratteri scritti a mano è dato da una mappa della densità di bit costruita a partire dai dati di distribuzione dei bit non elaborati. La mappa può essere utile per individuare in modo più efficiente i bordi dei caratteri rispetto all'uso della distribuzione non elaborata.

Per creare funzionalità per dati in ambienti specifici, vedere gli articoli seguenti:

* [Creare caratteristiche per i dati in SQL Server](create-features-sql-server.md)
* [Creare caratteristiche per i dati in un cluster Hadoop con query Hive](create-features-hive.md)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="create-features-from-your-data---feature-engineering"></a>Creazione di caratteristiche dai dati: progettazione di caratteristiche
I dati di training sono costituiti da una matrice composta da esempi (record o osservazioni archiviate in righe), ognuno dei quali presenta un set di caratteristiche (variabili o campi archiviati in colonne). Le caratteristiche specificate nella progettazione sperimentale identificheranno i modelli presenti nei dati. Anche se molti dei campi dati non elaborati possono essere inclusi direttamente nel set di caratteristiche selezionate usato per il training di un modello, spesso è necessario creare altre caratteristiche (progettate) da quelle presenti nei dati non elaborati per generare un set di dati di training avanzato.

Quali caratteristiche è necessario creare per migliorare il set di dati durante il training di un modello? Le caratteristiche progettate che migliorano il training forniscono informazioni che consentono di differenziare meglio i modelli presenti nei dati. Le nuove caratteristiche dovrebbero fornire informazioni aggiuntive che non vengono chiaramente acquisite o non sono immediatamente identificabili nel set di caratteristiche originali o esistenti. Questo processo è tuttavia una sorta di artificio. Le decisioni valide e produttive richiedono spesso una certa esperienza specifica.

Quando si inizia a usare Azure Machine Learning, è più facile afferrare questo processo in modo concreto tramite gli esempi disponibili in Studio. Di seguito ne vengono presentati due:

* Un esempio di regressione basato sulla [stima del numero di noleggi di biciclette](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) in un esperimento controllato in cui sono noti i valori di destinazione.
* Un esempio di classificazione di data mining del testo tramite [Feature Hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Esempio 1: Aggiungere caratteristiche temporali per un modello di regressione
Per dimostrare come progettare le caratteristiche per un'attività di regressione, si userà l'esperimento "Demand forecasting of bikes" in Azure Machine Learning Studio. L'obiettivo di questo esperimento è stimare la domanda di biciclette, ovvero il numero di noleggi di biciclette nell'arco di un mese, un giorno o un'ora specifica. Come dati di input non elaborati si usa il set di dati "Bike Rental UCI dataset". Questo set di dati si basa su dati reali della società Capital Bikeshare che gestisce una rete di noleggio di biciclette a Washington DC, negli Stati Uniti. Il set di dati rappresenta il numero di noleggi di biciclette in un'ora specifica del giorno negli anni 2011 e 2012 r include 17379 righe e 17 colonne. Il set di caratteristiche non elaborate include le condizioni meteorologiche (temperatura/umidità/velocità del vento) e il tipo di giorno (festività/giorno feriale). Il campo per la stima è "cnt", un conteggio che rappresenta i noleggi di biciclette in un'ora specifica e compreso nell'intervallo da 1 a 977.

Allo scopo di creare caratteristiche efficaci nei dati di training, vengono compilati quattro modelli di regressione con lo stesso algoritmo, ma con quattro diversi set di dati di training. I quattro set di dati rappresentano gli stessi dati di input non elaborati, ma con un numero crescente di set di caratteristiche. Queste caratteristiche sono raggruppate in quattro categorie:

1. A = caratteristiche meteo + festività + giorno feriale + fine settimana per la giornata prevista.
2. B = numero di biciclette noleggiate in ognuna delle 12 ore precedenti.
3. C = numero di biciclette noleggiate in ognuno dei 12 giorni precedenti alla stessa ora.
4. D = numero di biciclette noleggiate in ognuna delle 12 settimane precedenti nello stesso giorno e alla stessa ora.

Oltre al set di caratteristiche A, che esiste già nei dati non elaborati originali, gli altri tre set di caratteristiche vengono creati tramite il processo di progettazione. Il set di caratteristiche B acquisisce la domanda di biciclette molto recente. Il set di caratteristiche C acquisisce la domanda di biciclette in un particolare orario. Il set di caratteristiche D acquisisce la domanda di biciclette in un particolare orario e giorno della settimana. Ognuno dei quattro set di dati di training include rispettivamente il set di caratteristiche A, A+B, A+B+C e A+B+C+D.

Nell'esperimento di Azure Machine Learning questi quattro set di dati vengono creati dai quattro rami del set di dati di input pre-elaborati. A eccezione del ramo all'estrema sinistra, ognuno di questi rami contiene un modulo [Execute R Script](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) in cui un set di caratteristiche derivate (set B, C e D) viene rispettivamente costruito e aggiunto al set di dati importato. La figura seguente illustra lo script R usato per creare il set di caratteristiche B nel secondo ramo a sinistra.

![creare caratteristiche](./media/create-features/addFeature-Rscripts.png)

Il confronto dei risultati delle prestazioni dei quattro modelli è riepilogato nella tabella seguente: 

![confronto dei risultati](./media/create-features/result1.png)

I risultati migliori vengono forniti dalle caratteristiche A+B+C. Si noti che il tasso di errore diminuisce quando si include un set di caratteristiche aggiuntivo nei dati di training. Verifica la stima secondo cui il set di caratteristiche B, C fornisce altre informazioni rilevanti per l'attività di regressione. L'aggiunta della caratteristica D non sembra tuttavia fornire un'altra riduzione del tasso di errore.

## <a name="example2"></a> Esempio 2: Creazione di caratteristiche per il data mining del testo
La progettazione di caratteristiche viene ampiamente applicata nelle attività correlate al data mining del testo, ad esempio la classificazione di documenti e l'analisi del sentiment. Ad esempio, quando si vogliono classificare documenti in diverse categorie, un tipico presupposto è il fatto che le parole o le frasi incluse in una categoria di documenti sono presenti con minore probabilità in un'altra categoria di documenti. In altre parole, la frequenza di distribuzione di parole/frasi è in grado di caratterizzare diverse categorie di documenti. Poiché in genere nelle applicazioni di data mining del testo singole parti del contenuto di testo vengono usate come dati di input, per creare le caratteristiche che comportano frequenze di parole/frasi è necessario usare il processo di progettazione delle caratteristiche.

Per completare questa attività, si applica una tecnica definita **hashing di caratteristiche** per trasformare in modo efficiente le caratteristiche di testo arbitrarie in indici. Anziché associare ogni caratteristica di testo (parole/frasi) a un indice particolare, questo metodo applica una funzione hash alle caratteristiche e usa direttamente i relativi valori hash come indici.

In Azure Machine Learning è disponibile un modulo [Feature Hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) che crea in modo pratico queste caratteristiche basate su parole/frasi. La figura seguente mostra un esempio dell'uso di questo modulo. Il set di dati di input contiene due colonne: la classificazione dei libri da 1 a 5 e il contenuto effettivo della recensione. L'obiettivo del modulo [Feature Hashing](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) è recuperare una serie di nuove caratteristiche che mostrino la frequenza di occorrenza delle parole/frasi corrispondenti nella recensione di un libro particolare. Per usare questo modulo, completare i passaggi seguenti:

* Selezionare innanzitutto la colonna che contiene il testo di input ("Col2" in questo esempio).
* Impostare quindi "Hashing bitsize" su 8, che equivale alla creazione di 2^8=256 caratteristiche. Per le parole/frasi in tutto il testo verrà generato un hash per 256 indici. Il parametro "Hashing bitsize" è compreso nell'intervallo da 1 a 31. Con l'impostazione di un numero maggiore, è meno probabile che per le parole/frasi venga generato un hash nello stesso indice.
* Infine, impostare il parametro "N-grams" su 2. In questo modo si ottiene la frequenza di occorrenze di unigrammi (una caratteristica per ogni singola parola) e di digrammi (una caratteristica per ogni coppia di valori adiacenti) dal testo di input. L'intervallo del parametro "N-grams" è compreso tra 0 e 10 e indica il numero massimo di parole sequenziali da includere in una funzione.  

![Modulo "Feature Hashing"](./media/create-features/feature-Hashing1.png)

La figura seguente mostra l'aspetto delle nuove caratteristiche.

![Esempio di "Feature Hashing"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Conclusioni
Le caratteristiche progettate e selezionate migliorano l'efficienza del processo di training che tenta di estrarre le informazioni essenziali contenute nei dati. Migliorano anche le potenzialità di questi modelli per la classificazione accurata dei dati di input e per la stima più affidabile dei risultati di interesse. La progettazione e la selezione delle caratteristiche possono anche combinarsi per rendere l'apprendimento più trattabile a livello computazionale. Questa operazione viene eseguita tramite il miglioramento e la successiva riduzione del numero di caratteristiche richieste per calibrare o eseguire il training di un modello. Da un punto di vista matematico, le caratteristiche selezionate per eseguire il training di un modello sono costituite da un set minimo di variabili indipendenti che spiegano i modelli nei dati e quindi stimano correttamente i risultati.

Non sempre è necessario eseguire la progettazione o la selezione delle caratteristiche. La necessità o meno di questi passaggi dipende dai dati raccolti o da gestire, dagli algoritmi selezionati e dall'obiettivo dell'esperimento.


---
title: Progettazione di caratteristiche in data science - Processo di data science per i team
description: Informazioni sulla progettazione di funzionalità e sul suo ruolo nel processo di miglioramento dei dati di apprendimento automatico.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: conceptual
ms.date: 05/14/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperfq4
ms.openlocfilehash: 44b5baa074b62a072873d8097de184a2813b54ec
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322015"
---
# <a name="feature-engineering-in-data-science"></a>Progettazione di funzionalità nell'analisi scientifica dei dati

Questo articolo descrive la progettazione di funzionalità e il suo ruolo nel miglioramento dei dati di apprendimento automatico. A tale scopo, sono riportate illustrazioni dagli esperimenti di [Azure Machine Learning Studio (versione classica)](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio). 

* **Progettazione di caratteristiche** : processo di creazione di nuove funzionalità da dati non elaborati per aumentare la potenza predittiva dell'algoritmo di apprendimento. Le funzionalità progettate devono acquisire informazioni aggiuntive che non sono facilmente evidenti nel set di funzionalità originale.
* **Selezione delle funzionalità** : processo di selezione del subset principale delle funzionalità per ridurre la dimensionalità del problema di training.

In genere la prima fase è la **progettazione di funzionalità** per generare altre funzioni, quindi si passa alla **selezione delle funzionalità** per eliminare quelle irrilevanti, ridondanti o altamente correlate.

Progettazione e selezione di funzionalità fanno parte della [fase di modellazione](lifecycle-modeling.md) del Team Data Science Process (TDSP). Per altre informazioni sul TDSP e sul ciclo di vita del data science vedere [Introduzione a Team Data Science Process](overview.md)

## <a name="what-is-feature-engineering"></a>Cos'è la progettazione delle funzionalità?

I dati di training sono costituiti da una matrice composta da righe e colonne. Ogni riga della matrice corrisponde a un'osservazione o a un record. Le colonne di ogni riga corrispondono alle funzionalità che descrivono ciascun record. Le funzionalità specificate nella progettazione sperimentale devono caratterizzare i modelli nei dati.

Anche se diversi campi dei dati non elaborati possono essere usati direttamente per eseguire il training di un modello, spesso è necessario creare funzionalità aggiuntive (progettate) per migliorare il set di dati del training.

Le funzionalità progettate che migliorano il training forniscono informazioni che consentono di differenziare meglio i modelli nei dati. Questo processo è tuttavia una sorta di artificio. Per le decisioni relative a suono e produzione spesso è necessaria esperienza sui domini.

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>Esempio 1: Aggiungere caratteristiche temporali per un modello di regressione

Per dimostrare come progettare le funzionalità per un'attività di regressione, si userà l'esperimento [Demand forecasting of bikes rentals](https://gallery.azure.ai/Experiment/Regression-Demand-estimation-4) in Azure Machine Learning Studio (versione classica). L'obiettivo di questo esperimento è stimare la domanda di noleggi di biciclette nell'arco di un mese, un giorno o un'ora specifica.

### <a name="bike-rental-dataset"></a>Set di dati Bike rental

Il set di dati [Bike Rental UCI](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset/) si basa su dati reali di un'azienda statunitense di bike sharing. Rappresenta il numero di noleggi di biciclette in un'ora specifica del giorno negli anni 2011 e 2012. Contiene 17.379 righe e 17 colonne.

Il set di funzioni non elaborate include le condizioni meteorologiche (temperatura/umidità/velocità del vento) e il tipo di giorno (festività/giorno feriale). Il campo da stimare è il conteggio che rappresenta i noleggi di biciclette in un'ora specifica e che è compreso tra 1 e 977.

### <a name="create-a-feature-engineering-experiment"></a>Creazione di un esperimento di progettazione delle funzionalità

Allo scopo di costruire funzioni efficaci nei dati di training, vengono compilati quattro modelli di regressione con lo stesso algoritmo, ma con quattro diversi set di dati di training. I quattro set di dati rappresentano gli stessi dati di input non elaborati, ma con un numero crescente di set di funzioni. Queste funzioni sono raggruppate in quattro categorie:

1. A = funzioni meteo + festività + giorno feriale + fine settimana per la giornata prevista.
2. B = numero di biciclette noleggiate in ognuna delle 12 ore precedenti.
3. C = numero di biciclette noleggiate in ognuno dei 12 giorni precedenti alla stessa ora.
4. D = numero di biciclette noleggiate in ognuna delle 12 settimane precedenti nello stesso giorno e alla stessa ora.

Oltre al set di funzioni A, che esiste già nei dati non elaborati originali, gli altri tre set di funzioni vengono creati tramite il processo di progettazione delle funzioni. Il set di funzionalità B acquisisce la domanda di biciclette recente. Il set di funzioni C acquisisce la domanda di biciclette in un particolare orario. Il set di funzioni D acquisisce la domanda di biciclette in un particolare orario e giorno della settimana. Ognuno dei quattro set di dati di training include rispettivamente il set di funzioni A, A+B, A+B+C e A+B+C+D.

### <a name="feature-engineering-using-studio-classic"></a>Progettazione di funzionalità con Studio (versione classica)

Nell'esperimento su Studio (versione classica) questi quattro set di dati del training vengono creati dai quattro rami del set di dati di input pre-elaborati. Ad eccezione del ramo all'estrema sinistra, ognuno di questi rami contiene un modulo [Execute R Script](/azure/machine-learning/studio-module-reference/execute-r-script) in cui le funzionalità derivate (set B, C e D) vengono costruite e aggiunte al set di dati importato.

La figura seguente illustra lo script R usato per creare il set di funzioni B nel secondo ramo a sinistra.

![creare funzioni](./media/create-features/addFeature-Rscripts.png)

### <a name="results"></a>Risultati

Il confronto dei risultati delle prestazioni dei quattro modelli è riepilogato nella tabella seguente: 

![confronto dei risultati](./media/create-features/result1.png)

I risultati migliori vengono forniti dalle funzioni A+B+C. Il tasso di errore diminuisce quando si includono set di funzionalità aggiuntivi nei dati di training. Verifica la stima secondo cui il set di funzioni B, C fornisce altre informazioni rilevanti per l'attività di regressione. L'aggiunta della funzioni D non sembra tuttavia fornire un'altra riduzione del tasso di errore.

## <a name="example-2-create-features-for-text-mining"></a><a name="example2"></a> Esempio 2: Creare funzionalità nel data mining del testo

La progettazione di funzionalità viene ampiamente applicata nelle attività correlate al data mining del testo, ad esempio la classificazione di documenti e l'analisi delle valutazioni. Poiché in genere singole parti del contenuto di testo non elaborato vengono usate come dati di input, per creare le funzionalità che comportano frequenze di parole/frasi è necessario usare il processo di progettazione delle funzionalità.

### <a name="feature-hashing"></a>Hashing di funzioni

Per completare questa attività, si applica una tecnica definita [hashing di funzioni](/azure/machine-learning/studio-module-reference/feature-hashing) per trasformare in modo efficiente le funzioni di testo arbitrarie in indici. Anziché associare ogni funzione di testo (parole/frasi) a un indice particolare, questo metodo applica una funzione hash alle funzioni e usa direttamente i relativi valori hash come indici.

In Studio (versione classica) è disponibile il modulo [Feature Hashing](/azure/machine-learning/studio-module-reference/feature-hashing) che crea in modo pratico funzioni basate su parole/frasi. La figura seguente mostra un esempio dell'uso di questo modulo. Il set di dati di input contiene due colonne: la classificazione dei libri da 1 a 5 e il contenuto effettivo della recensione. L'obiettivo di questo modulo è recuperare una serie di nuove funzionalità che mostrino la frequenza di occorrenza delle parole/frasi corrispondenti nella recensione di un libro specifico. Per usare questo modulo, completare i passaggi seguenti:

* Selezionare innanzitutto la colonna che contiene il testo di input ("Col2" in questo esempio).
* Impostare quindi "Hashing bitsize" su 8, che equivale alla creazione di 2^8=256 funzioni. Per le parole/frasi in tutto il testo verrà generato un hash per 256 indici. Il parametro "Hashing bitsize" è compreso nell'intervallo da 1 a 31. Con l'impostazione di un numero maggiore, è meno probabile che per le parole/frasi venga generato un hash nello stesso indice.
* Infine, impostare il parametro "N-grams" su 2. In questo modo si ottiene la frequenza di occorrenze di unigrammi (una funzione per ogni singola parola) e di digrammi (una funzione per ogni coppia di valori adiacenti) dal testo di input. L'intervallo del parametro "N-grams" è compreso tra 0 e 10 e indica il numero massimo di parole sequenziali da includere in una funzione.  

![Modulo "Feature Hashing"](./media/create-features/feature-Hashing1.png)

La figura seguente mostra l'aspetto delle nuove funzioni.

![Esempio di "Feature Hashing"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Conclusioni
Le funzioni progettate e selezionate migliorano l'efficienza del processo di training che tenta di estrarre le informazioni essenziali contenute nei dati. Migliorano anche le potenzialità di questi modelli per la classificazione accurata dei dati di input e per la stima più affidabile dei risultati di interesse.

Progettazione e selezione delle funzioni possono anche combinarsi per rendere l'apprendimento più computazionalmente trattabile. Questa operazione viene eseguita tramite il miglioramento e la successiva riduzione del numero di funzioni richieste per calibrare o eseguire il training di un modello. Da un punto di vista matematico, le funzioni selezionate sono un set minimo di variabili indipendenti che spiegano i modelli nei dati e stimano correttamente i risultati.

Non sempre è necessario eseguire la progettazione o la selezione delle funzionalità. Dipende dai dati, dall'algoritmo selezionato e dall'obiettivo dell'esperimento.

## <a name="next-steps"></a>Passaggi successivi

Per creare funzionalità per dati in ambienti specifici, vedere gli articoli seguenti:

* [Creare funzionalità per i dati in SQL Server](create-features-sql-server.md)
* [Creare funzionalità per i dati in un cluster Hadoop con query Hive](create-features-hive.md)
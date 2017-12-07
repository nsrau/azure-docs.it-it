---
title: Eseguire gli script di apprendimento automatico di Python | Documentazione Microsoft
description: "Descrive i principi di progettazione di base per gli script Python in Azure Machine Learning nonché gli scenari di utilizzo di base, le funzionalità e le limitazioni."
keywords: apprendimento automatico di python, pandas, pandas di python, script di python, eseguire gli script di python
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ee9eb764-0d3e-4104-a797-19fc29345d39
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: bradsev;garye
ms.openlocfilehash: c25f31ca72417672298657c4585184ad72db6c99
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Eseguire gli script di apprendimento automatico di Python in Azure Machine Learning Studio

Questo argomento descrive i principi di progettazione alla base dell'attuale supporto per gli script Python in Azure Machine Learning. Vengono illustrate anche le funzionalità principali disponibili, ad esempio:

- Esecuzione di scenari di utilizzo di base
- Assegnazione di punteggi a un esperimento in un servizio Web
- Supporto per l'importazione di codice esistente
- Esportazione delle visualizzazioni
- Selezione delle funzionalità con supervisione
- Informazioni su alcune limitazioni

[Python](https://www.python.org/) è uno strumento indispensabile nella dotazione di strumenti di molti data scientist. Le sue caratteristiche sono:

* una sintassi elegante e concisa; 
* il supporto multipiattaforma; 
* un'ampia raccolta di librerie complete; 
* strumenti di sviluppo avanzati. 

Python viene usato in tutte le fasi di un flusso di lavoro usato in genere nella modellazione di Machine Learning:

- Inserimento ed elaborazione dei dati 
- Costruzione delle funzionalità
- Training dei modelli 
- Convalida dei modelli
- Distribuzione dei modelli

Azure Machine Learning Studio supporta l'incorporamento di script Python in varie parti di un esperimento di apprendimento automatico, nonché la relativa pubblicazione diretta come servizi Web in Microsoft Azure.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="design-principles-of-python-scripts-in-machine-learning"></a>Principi di progettazione degli script Python in Machine Learning

L'interfaccia primaria di Python in Azure Machine Learning Studio è il modulo [Execute Python Script][execute-python-script] (Esegui script Python) illustrato nella figura 1.

![Immagine1](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Immagine2](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

Figura 1. Modulo **Execute Python Script** .

Il modulo [Execute Python Script][execute-python-script] in Azure ML Studio accetta un massimo di tre input e produce un massimo di due output, come illustrato nella sezione seguente, come l'analogo modulo R, ovvero [Esegui script R][execute-r-script]. Il codice Python da eseguire viene immesso nella casella dei parametri come una funzione del punto di ingresso appositamente denominata, detta `azureml_main`. Di seguito sono descritti i principi di progettazione principali usati per implementare questo modulo:

1. *Deve essere idiomatico per gli utenti di Python.* La maggior parte degli utenti di Python struttura il codice come funzioni nei moduli. L'inserimento di un numero elevato di istruzioni eseguibili in un modo di primo livello è quindi relativamente raro. Di conseguenza, anche la casella di script accetta una funzione Python appositamente denominata invece di una semplice sequenza di istruzioni. Gli oggetti esposti nella funzione sono tipi di librerie Python standard, ad esempio frame di dati [Pandas](http://pandas.pydata.org/) e matrici [NumPy](http://www.numpy.org/).
2. *Deve avere un alto livello di fedeltà tra le esecuzioni locali e nel cloud.* Il back-end usato per eseguire il codice Python si basa su [Anaconda](https://store.continuum.io/cshop/anaconda/), una distribuzione Python scientifica multipiattaforma ampiamente diffusa. Include circa 200 dei più comuni pacchetti Python. Di conseguenza, i professionisti possono eseguire il debug e qualificare il codice nel proprio ambiente Anaconda locale compatibile con Machine Learning di Azure. Possono quindi usare un ambiente di sviluppo esistente, ad esempio notebook [IPython](http://ipython.org/) o [Python Tools for Visual Studio](http://aka.ms/ptvs), per eseguire il codice come parte di un esperimento di Azure Machine Learning. Il punto di ingresso `azureml_main` è una funzione Python convenzionale che è possibile modificare senza il codice specifico di Azure Machine Learning o l'SDK installato.
3. *Deve essere facilmente componibile con altri moduli di Azure Machine Learning.* Il modulo [Execute Python Script][execute-python-script] (Esegui script Python) accetta come input e output i set di dati standard di Azure Machine Learning. Il framework sottostante collega in modo trasparente ed efficiente i runtime di Azure Machine Learning e Python. È quindi possibile usare Python in combinazione ai flussi di lavoro di Azure Machine Learning esistenti, inclusi quelli che effettuano chiamate in R e SQLite. Un data scientist può quindi creare flussi di lavoro che consentono di eseguire le operazioni seguenti:
   * Uso di Python e Pandas per la pre-elaborazione e la pulizia dei dati.
   * Feed dei dati a una trasformazione SQL, unendo più set di dati per creare funzioni.
   * Training di modelli tramite algoritmi in Azure Machine Learning. 
   * Valutazione e post-elaborazione dei risultati tramite R.


## <a name="basic-usage-scenarios-in-ml-for-python-scripts"></a>Scenari di utilizzo di base in Machine Learning per gli script Python

In questa sezione si osserveranno alcuni usi di base del modulo [Execute Python Script][execute-python-script] (Esegui script Python). Qualsiasi input nel modulo Python viene esposto come frame di dati Pandas. La funzione deve restituire un pacchetto con un singolo frame di dati Pandas all'interno di una [sequenza](https://docs.python.org/2/c-api/sequence.html) Python, ad esempio, una tupla, un elenco o una matrice NumPy. Il primo elemento di questa sequenza viene quindi restituito nella prima porta di output del modulo. Questo schema è illustrato nella figura 2.

![Immagine3](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

Figura 2. Mapping di porte di input ai parametri e valore restituito alla porta di output.

Una semantica più dettagliata della modalità di mapping delle porte di input ai parametri della funzione `azureml_main` è descritta nella tabella 1:

![Immagine1T](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

Tabella 1. Mapping delle porte di input ai parametri della funzione.

Il mapping delle porte di input ai parametri della funzione è di tipo posizionale:

- La prima porta di input connessa è mappata al primo parametro della funzione. 
- Il secondo input, se connesso, è mappato al secondo parametro della funzione.

Per altre informazioni su Python Pandas e su come è possibile usarlo per modificare i dati in modo efficace ed efficiente, vedere *Python for Data Analysis* (Python per l'analisi dei dati - O'Reilly, 2012) di W. McKinney. 


## <a name="translation-of-input-and-output-types"></a>Conversione dei tipi di input e di output 
I set di dati di input in Azure Machine Learning vengono convertiti in frame di dati in Pandas. I frame di dati di output vengono riconvertiti in set di dati di Azure Machine Learning. Vengono eseguite le conversioni seguenti:

1. Le colonne stringa e numeriche vengono convertite come sono e i valori mancanti in un set di dati vengono convertiti in valori "ND" in Pandas. La stessa conversione viene eseguita in senso inverso, ovvero i valori ND in Pandas vengono convertiti in valori mancanti in Azure Machine Learning.
2. I vettori indice di Pandas non sono supportati in Azure Machine Learning. Tutti i frame di dati di input della funzione Python hanno sempre un indice numerico a 64 bit compreso tra 0 e il numero di righe meno 1. 
3. I set di dati di Azure Machine Learning non possono avere nomi di colonna duplicati e diversi da stringhe. Se un frame di dati di output contiene colonne non numeriche, il framework chiama `str` sui nomi di colonna. In modo analogo, tutti i nomi di colonna duplicati vengono modificati automaticamente per assicurare che siano univoci. Al primo duplicato viene aggiunto il suffisso (2), al secondo duplicato il suffisso (3) e così via.


## <a name="operationalizing-python-scripts"></a>Rendere operativi gli script Python

Tutti i moduli [Execute Python Script][execute-python-script] (Esegui script Python) usati in un esperimento di assegnazione dei punteggi vengono chiamati al momento della pubblicazione come servizio Web. Ad esempio, la figura 3 mostra un esperimento di assegnazione dei punteggi contenente il codice per valutare una singola espressione Python. 

![Immagine4](./media/execute-python-scripts/figure3a.png)

![Immagine5](./media/execute-python-scripts/python-script-with-python-pandas.png)

Figura 3. Servizio Web per la valutazione di un'espressione Python.

Un servizio Web creato da questo esperimento:

- Accetta come input un'espressione Python, come stringa.
- La invia all'interprete Python. 
- Restituisce una tabella contenente l'espressione e il risultato valutato.


## <a name="importing-existing-python-script-modules"></a>Importazione di moduli di script Python esistenti

Un caso di utilizzo comune per molti data scientist consiste nell'incorporare gli script Python esistenti negli esperimenti di Azure Machine Learning. Invece di richiedere che tutto il codice sia concatenato e incollato in una singola casella di script, il modulo [Execute Python Script][execute-python-script] (Esegui script Python) accetta un file ZIP che include moduli Python in corrispondenza della terza porta di input. Il file viene decompresso in fase di esecuzione dal framework di esecuzione e il contenuto viene aggiunto al percorso della libreria dell'interprete Python. La funzione del punto di ingresso `azureml_main` può quindi importare questi moduli direttamente.

Considerare, ad esempio, il file Hello.py che contiene una semplice funzione "Hello, World".

![Immagine6](./media/execute-python-scripts/figure4.png)

Figura 4. Funzione definita dall'utente nel file Hello.py.

Viene quindi creato un file Hello.zip contenente Hello.py:

![Immagine7](./media/execute-python-scripts/figure5.png)

Figura 5. File ZIP contenente codice Python definito dall'utente.

Caricare il file ZIP come set di dati in Azure Machine Learning Studio. Creare quindi ed eseguire un esperimento che usa il codice Python nel file Hello.zip associandolo alla terza porta di input del modulo **Execute Python Script** (Esegui script Python), come mostrato in questa figura.

![Immagine8](./media/execute-python-scripts/figure6a.png)

![Immagine9](./media/execute-python-scripts/figure6b.png)

Figura 6. Esperimento di esempio con codice Python definito dall'utente caricato come file ZIP.

L'output del modulo indica che il file ZIP è stato estratto dal pacchetto e che la funzione `print_hello` è stata eseguita.
 
![image10](./media/execute-python-scripts/figure7.png)

Figura 7. Funzione definita dall'utente in uso all'interno del modulo [Execute Python Script][execute-python-script] (Esegui script Python).


## <a name="working-with-visualizations"></a>Utilizzo di visualizzazioni

I tracciati creati con MatplotLib che possono essere visualizzati nel browser possono essere restituiti da [Execute Python Script][execute-python-script] (Esegui script Python). I tracciati non vengono tuttavia reindirizzati automaticamente in immagini così come sono quando si usa R. Di conseguenza, se devono essere restituiti ad Azure Machine Learning, l'utente dovrà salvare espressamente i tracciati in file PNG. 

Per generare immagini da MatplotLib, è necessario completare la procedura seguente:

* Cambiare il back-end impostandolo su "AGG" dal renderer predefinito basato su Qt. 
* Creare un nuovo oggetto figura. 
* Ottenere l'asse e generare tutti i tracciati al suo interno. 
* Salvare la figura in un file PNG. 

Questo processo, illustrato nella Figura 8 seguente, crea una matrice di grafici a dispersione tramite la funzione scatter_matrix in Pandas.

![Immagine1v](./media/execute-python-scripts/figure-v1-8.png)

Figura 8. Codice per il salvataggio di figure di MatplotLib in immagini.

La figura 9 mostra un esperimento che usa lo script precedentemente illustrato per restituire tracciati tramite una seconda porta di output.

![Immagine2v](./media/execute-python-scripts/figure-v2-9a.png) 

![Immagine2v](./media/execute-python-scripts/figure-v2-9b.png) 

Figura 9. Visualizzazione di tracciati generati da codice Python.

È possibile restituire più figure salvandole in immagini diverse. Il runtime di Azure Machine Learning recupera tutte le immagini e le concatena per la visualizzazione.


## <a name="advanced-examples"></a>Esempi avanzati

L'ambiente Anaconda installato in Azure Machine Learning contiene pacchetti comuni come NumPy, SciPy e Scikits-Learn. Questi pacchetti possono essere usati in modo efficace per diverse attività di elaborazione dei dati in una pipeline di Machine Learning. L'esperimento e lo script seguenti illustrano, ad esempio, come usare gli strumenti di apprendimento d'insieme di Scikits-Learn per calcolare i punteggi di rilevanza della funzione per un set di dati. I punteggi possono essere usati per eseguire una selezione della funzione supervisionata prima di inserirla in un altro modello di Machine Learning.

Di seguito è illustrata la funzione Python usata per calcolare i punteggi di rilevanza e ordinare le funzioni in base ai punteggi:

![Immagine11](./media/execute-python-scripts/figure8.png)

Figura 10. Funzione per classificare le funzioni in base ai punteggi.
  L'esperimento seguente calcola quindi e restituisce i punteggi di rilevanza delle funzioni nel set di dati "Pima Indian Diabetes" in Azure Machine Learning:

![image12](./media/execute-python-scripts/figure9a.png)
![image13](./media/execute-python-scripts/figure9b.png)    

Figura 11. Esperimento per classificare le funzioni nel set di dati Pima Indian Diabetes.

## <a name="limitations"></a>Limitazioni
Attualmente [Execute Python Script][execute-python-script] (Esegui script Python) presenta le limitazioni seguenti:

1. *Esecuzione in modalità sandbox.* Il runtime Python è attualmente in modalità sandbox e di conseguenza non consente l'accesso alla rete o al file system locale in modo permanente. Tutti i file salvati localmente sono isolati ed eliminati al termine del modulo. Il codice Python non è in grado di accedere alla maggior parte delle directory nel computer in cui è in esecuzione, ad eccezione della directory corrente e delle relative sottodirectory.
2. *Mancanza di supporto avanzato per sviluppo e debug.* Il modulo Python non supporta attualmente le funzionalità dell'IDE, ad esempio IntelliSense e debug. In caso di errore del modulo durante il runtime, è disponibile l'analisi completa dello stack Python. È tuttavia necessario visualizzarla nel log di output per il modulo. Al momento si consiglia di sviluppare ed eseguire il debug degli script Python in un ambiente come IPython e quindi importare il codice nel modulo.
3. *Output di un singolo frame di dati.* Al punto di ingresso Python è consentito restituire un singolo frame di dati come output. Non è attualmente possibile restituire oggetti Python arbitrari, ad esempio modelli con training, direttamente al runtime di Azure Machine Learning. Come per [Execute R Script][execute-r-script] (Esegui script R), che presenta le stesse limitazioni, in molti casi è possibile inserire oggetti in una matrice di byte e quindi restituirla all'interno di un frame di dati.
4. *Impossibilità di personalizzare un'installazione di Python*. Attualmente, è possibile aggiungere moduli Python personalizzati solo tramite il meccanismo con file ZIP descritto in precedenza. Anche se ciò è fattibile per i moduli piccoli, risulta complesso per quelli grandi, specialmente quelli con DLL native o un numero elevato di moduli. 

## <a name="conclusions"></a>Conclusioni
Il modulo [Execute Python Script][execute-python-script] (Esegui script Python) consente ai data scientist di incorporare codice Python esistente nei flussi di lavoro di apprendimento automatico ospitati nel cloud in Azure Machine Learning e di renderli facilmente operativi come parte di un servizio Web. Il modulo di script Python interagisce naturalmente con altri moduli in Azure Machine Learning. Il modulo può essere usato per diverse attività, dall'esplorazione dei dati alla pre-elaborazione e all'estrazione delle funzionalità, fino alla valutazione e alla post-elaborazione dei risultati. Il runtime di back-end usato per l'esecuzione è basato su Anaconda, una distribuzione di Python testata e ampiamente usata. Questo back-end semplifica l'onboarding degli asset di codice esistenti sul cloud.

È prevista l'introduzione di altre funzionalità nel modulo [Execute Python Script][execute-python-script] (Esegui script Python), ad esempio la possibilità di eseguire il training di modelli e renderli operativi in Python, e il miglioramento del supporto per lo sviluppo e il debug del codice in Azure Machine Learning Studio.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni, vedere il [Centro per sviluppatori di Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

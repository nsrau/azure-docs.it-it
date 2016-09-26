<properties 
	pageTitle="Come scegliere i parametri per ottimizzare gli algoritmi in Azure Machine Learning | Microsoft Azure" 
	description="Viene illustrato come scegliere il set di parametri ottimale per un algoritmo in Azure Machine Learning." 
	services="machine-learning"
	documentationCenter="" 
	authors="bradsev" 
	manager="jhubbard" 
	editor="cgronlun"/>  

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="bradsev" />  


# Come scegliere i parametri per ottimizzare gli algoritmi in Azure Machine Learning

In questo argomento viene descritto come scegliere il set di iperparametri corretto per un algoritmo in Azure Machine Learning. Per la maggior parte degli algoritmi di Machine Learning è necessario impostare i parametri. Quando si esegue il training di un modello, è necessario fornire valori per tali parametri. L'efficacia del modello sottoposto a training dipende dai parametri del modello scelto. Il processo di individuazione dell'impostazione ottimale dei parametri è noto come selezione del modello.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Esistono vari modi per effettuare la selezione del modello. Nell'apprendimento automatico, la convalida incrociata è uno dei metodi più diffusi per la selezione del modello ed è il meccanismo di selezione del modello predefinito in Azure Machine Learning. Poiché R e Python sono supportati da Azure Machine Learning, è possibile sempre implementare il proprio meccanismo di selezione del modello tramite R o Python.

Il processo di ricerca della migliore impostazione del parametro è formato da quattro passaggi.

1.	**Definire lo spazio del parametro**: per l'algoritmo, è innanzitutto necessario decidere i valori dei parametri esatti che si desiderano prendere in considerazione.
2.	**Definire le impostazioni convalida incrociate**: per il set di dati, è necessario decidere come scegliere le riduzioni di convalida incrociata.
3.	**Definire la metrica**: è necessario decidere la metrica da usare per determinare la migliore impostazione dei parametri, ad esempio, l'accuratezza, la radice dell'errore quadratico medio, la precisione o il valore f.
4.	**Formarsi, valutare e confrontare**: per ogni combinazione univoca dei valori del parametro, la convalida incrociata viene eseguita e basata sulla metrica di errore definita dall'utente. È possibile scegliere il modello di elaborazione migliore.

L'esperimento riportato di seguito illustra come poter raggiungere l'obiettivo in Azure Machine Learning.

![Immagine1](./media/machine-learning-algorithm-parameters-optimize/fig1.png)
 
## Definizione dello spazio del parametro
Il set di parametri può essere definito nel passaggio di inizializzazione del modello. Il riquadro del parametro di tutti gli algoritmi di apprendimento automatico presenta due modalità di formazione **parametro singolo** e **intervallo parametro**. È necessario scegliere la modalità **Intervallo parametro** (figura 1). Questa modalità consente l'immissione di più valori per ciascun parametro: i valori delimitati da virgole possono essere immessi nella casella di testo. In alternativa è possibile usare **Utilizzo del generatore di intervalli** per definire il punto massimo e minimo della griglia e il numero totale dei punti da generare. Per impostazione predefinita i valori dei parametri vengono generati su una scala lineare. Se però è selezionata la casella **Scala logaritmica**, i valori vengono generati nella scala logaritmica (ovvero, il rapporto dei punti adiacenti è costante invece della loro differenza). Per i parametri di tipo integer è possibile definire un intervallo con un trattino "-", ad esempio, "1-10" che significa che tutti i numeri interi compresi tra 1 e 10 (entrambi compresi) costituiscono il set di parametri. È supportata anche una modalità mista, ad esempio, "1-10, 20, 50". In questo caso, oltre ai numeri interi da 1 a 10, anche 20 e 50 vengono aggiunti al set di parametri.
  
![Immagine2](./media/machine-learning-algorithm-parameters-optimize/fig2.png) ![Immagine3](./media/machine-learning-algorithm-parameters-optimize/fig3.png)

## Definizione della riduzione di convalida incrociata
Il modulo [Partizione ed esempio][partition-and-sample] può essere utilizzato per assegnare riduzioni ai dati in modo casuale. Nella figura seguente è possibile visualizzare un esempio di configurazione per il modulo in cui si definiscono cinque riduzioni e si assegna in modo casuale un numero di riduzione per le istanze di esempio.

![Immagine4](./media/machine-learning-algorithm-parameters-optimize/fig4.png)  


## Definizione della metrica
Il modulo [Ottimizzazione degli iperparametri del modello][tune-model-hyperparameters] fornisce supporto per scegliere in modo empirico il miglior set di parametri per un algoritmo e un set di dati determinati. Il riquadro delle proprietà di questo modulo include, oltre alle informazioni relative al training del modello, la metrica da utilizzare per determinare il miglior set di parametri. Dispone di due diversi elenchi a discesa per gli algoritmi di classificazione e regressione, rispettivamente. Se l'algoritmo in esame è un algoritmo di classificazione, la metrica di regressione viene ignorata e viceversa. In questo esempio specifico, è stato scelto **Accuratezza** come metrica.
 
![Immagine5](./media/machine-learning-algorithm-parameters-optimize/fig5.png)  

## Eseguire il training, valutare e confrontare  
Lo stesso modulo [Ottimizzazione degli iperparametri del modello][tune-model-hyperparameters] esegue il training di tutti i modelli corrispondenti al set di parametri, valuta differenti metriche e poi restituisce il miglior modello di training in base alla metrica scelta. Tale modulo dispone di due input obbligatori:

* l'allievo non formato
* il set di dati

Il modulo contiene anche un set di dati di input facoltativo. Si effettua la connessione al set di dati con le informazioni di riduzione per l'input del set di dati obbligatorio. Se il set di dati non viene assegnato a delle informazione di riduzione, per impostazione predefinita viene eseguita automaticamente una convalida incrociata a 10 riduzioni. Se non viene eseguita l'assegnazione di riduzione e la convalida del set di dati viene fornita alla porta del set di dati facoltativo, vengono usati la modalità di test di esecuzione del training scelta e il primo set di dati per il training del modello per ogni combinazione di parametri. Il modello viene poi valutato sul set di dati di convalida. La porta di output sinistra del modulo mostra diverse metriche in funzione dei valori di parametro. La porta di output destra fornisce il corrispondente modello sottoposto a training per il miglior modello di prestazioni in base alle metriche scelte (accuratezza in questo caso).

![Immagine6](./media/machine-learning-algorithm-parameters-optimize/fig6a.png) ![Immagine7](./media/machine-learning-algorithm-parameters-optimize/fig6b.png)
 
È possibile vedere esattamente i parametri scelti visualizzando la porta di output destra. Questo modello può essere utilizzato per la valutazione in un set di test o in un servizio web operativo dopo il salvataggio come modello per il training.


<!-- Module References -->  
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
 

<!---HONumber=AcomDC_0914_2016-->
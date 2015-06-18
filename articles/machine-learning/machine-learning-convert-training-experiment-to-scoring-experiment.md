<properties 
	pageTitle="Conversione di un esperimento di training Machine Learning in un test di valutazione | Azure" 
	description="Come convertire un esperimento di training Machine Learning, utilizzato per il training del modello di analisi predittiva, in un test di valutazione che può essere pubblicato come servizio Web." 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="garye"/>

#Conversione di un esperimento di training Machine Learning in un esperimento di valutazione

Azure Machine Learning consente di compilare, testare e distribuire soluzioni di analisi predittiva. 

Dopo avere creato e iterato un *training experiment* per eseguire il training del modello di analisi predittiva e nel momento in cui si è pronti a utilizzarlo per valutare nuovi dati, è necessario preparare e semplificare l'esperimento di valutazione. È quindi possibile pubblicare *scoring experiment* come servizio Web di Azure in modo che gli utenti possano inviare dati al modello e ricevere le relative stime.

Effettuando la conversione a un esperimento di classificazione, si prepara il modello sottoposto a training per essere pubblicato come servizio Web di classificazione. Gli utenti del servizio Web possono inviare dati di input al modello che poi restituirà i risultati della stima. Pertanto, effettuando la conversione a un esperimento di valutazione, è possibile valutare il modo in cui il modello verrà utilizzato da altri utenti.

Il processo di conversione di un esperimento di training in un esperimento di valutazione prevede tre passaggi:

1.	Salvataggio del modello di Machine Learning sottoposto a training e quindi sostituzione dell'algoritmo di Machine Learning e i moduli **Modello di training** con il proprio modello sottoposto a training salvato.
2.	Organizzare l'esperimento solo per i moduli necessari alla valutazione. Una prova di training include un numero di moduli necessario al training. Tali moduli non sono più necessari quando il modulo è stato sottoposto a training ed è pronto per la valutazione.
3.	Definire il punto dell'esperimento in cui accettare i dati dall'utente servizio Web e i dati da restituire.

##Pulsante Crea esperimento di valutazione

Il pulsante **Crea esperimento di valutazione**, nella parte inferiore dell'area di disegno del test, esegue automaticamente i tre passaggi di conversione del test di training a un esperimento di valutazione:

1.	Consente di salvare il modello sottoposto a training come modulo nella sezione **Modelli sottoposti a training** della tavolozza del modulo (a sinistra dell'area di disegno dell'esperimento), quindi sostituisce l'algoritmo di Machine Learning e i moduli **Training modello** con il modello sottoposto a training salvato. 
2.	Rimuove i moduli chiaramente non necessari. In questo esempio sono inclusi i moduli **Suddivisione**, secondo **Classifica modello** e **Valuta modello**.
3.	Crea i moduli di input e output del servizio Web e li aggiunge alle posizioni predefinite nell'esperimento.

Ad esempio, il seguente esperimento esegue il training di un modello di albero delle decisioni incrementato a due classi utilizzando dati di classificazione di esempio:

![Training experiment][figure1]

I moduli nell'esperimento eseguono fondamentalmente quattro diverse funzioni:

![Module functions][figure2]

Quando si converte l'esperimento di training in un esperimento di classificazione, alcuni di questi moduli non sono più necessari o hanno uno scopo diverso:

- **Dati** - I dati in questo set di dati di esempio non vengono utilizzati durante la classificazione. L'utente del servizio Web fornisce i dati da classificare. Tuttavia, i metadati di questo set di dati, ad esempio i tipi di dati, vengono utilizzati dal modello sottoposto a training. Pertanto, è necessario mantenere il set di dati nell'esperimento di classificazione in modo da poter fornire questi metadati.

- **Preparazione** - In base ai dati che verranno inviati per la classificazione, tali moduli potrebbero non essere necessari all'elaborazione dei dati in ingresso. 

	In questo esempio, nel set di dati di esempio potrebbero mancare dei valori e sono incluse delle colonne non necessarie per il eseguire il training del modello. Pertanto, sono stati inclusi un modulo **Pulizia dati mancanti** per gestire i valori mancanti e un modulo **Colonne progetto** per escludere le colonne aggiuntive dal flusso di dati. Se si è certi che i dati inviati per la classificazione tramite il servizio Web non presentino valori mancanti, è possibile rimuovere il modulo **Pulizia dati mancanti**. Tuttavia, poiché il modulo **Colonne progetto** consente di definire il set di funzionalità in corso di classificazione, il modulo deve essere mantenuto.

- **Training** - Dopo aver correttamente eseguito il training del modello, è necessario salvarlo come modulo del modello sottoposto a training singolo. Sarà poi possibile sostituire i singoli moduli con il modello sottoposto a training salvato.

- **Classificazione** - Nell'esempio, il modulo Suddivisione viene utilizzato per suddividere il flusso di dati in un set di dati del test e dati di training. Questa operazione non è necessaria nell'esperimento di classificazione, pertanto può essere rimosso. Analogamente, il secondo modulo **Modello di classificazione** e il modulo **Valuta modello** sono utilizzati per confrontare i risultati dei dati del test, quindi i moduli non sono necessari nell'esperimento di classificazione. Il modulo rimanente **Modello di classificazione** è però necessario per restituire un risultato di classificazione tramite il servizio Web.

Di seguito è possibile visualizzare l'aspetto dell'esempio dopo aver selezionato **Crea test di classificazione**:	

![Converted scoring experiment][figure3]

Tutto ciò potrebbe essere sufficiente per preparare l'esperimento alla pubblicazione come servizio Web. Tuttavia, potrebbe essere necessario eseguire ulteriori operazioni specifiche per l'esperimento.

###Regolazione dei moduli di input e output

Nell'esperimento di training è stato utilizzato un set di dati di training e quindi sono state eseguite delle operazioni di elaborazione per ottenere i dati in un formato utile all'algoritmo Machine Learning necessario. Se i dati che si prevede di ricevere tramite il servizio Web non necessitano di tale elaborazione, è possibile spostare il **modulo di input del servizio Web** in un nodo differente dell'esperimento.

Ad esempio, per impostazione predefinita **Crea esperimento di classificazione** inserisce il modulo **Input servizio Web** all'inizio del flusso di dati, come illustrato nella figura precedente. Tuttavia, se i dati di input non necessitano di tale elaborazione, è possibile posizionare manualmente l'**input del servizio Web** oltre i moduli di elaborazione dei dati:

![Moving the web service input][figure4]

I dati di input forniti tramite il servizio Web ora passano direttamente al modulo Modello di classificazione senza pre-elaborazione.

Analogamente, per impostazione predefinita, **Crea esperimento di classificazione** inserisce il modulo di output del servizio Web nella parte inferiore del flusso di dati. In questo esempio, il servizio Web restituisce all'utente l'output del modulo **Modello di classficazione** che include il vettore dei dati di input completo oltre ai risultati della valutazione.

Tuttavia, se si preferisce restituire un valore diverso - ad esempio, solo i risultati della classificazione e non l'intero vettore di dati di input, è possibile inserire un modulo **Colonne progetto** per escludere tutte le colonne tranne i risultati della classificazione. Spostare poi il modulo **Output servizio Web** sull'output del modulo **Colonne progetto**:

![Moving the web service output][figure5]

###Aggiungere o rimuovere i moduli di elaborazione dati aggiuntivi

Se presenti nell'esperimento, è possibile rimuove i moduli di cui si è certi che non saranno utili per la classificazione. Ad esempio, poiché è stato spostato il modulo **Input servizio Web** in un punto successivo ai moduli di elaborazione dei dati, è possibile rimuovere il modulo **Pulizia dati mancanti** dall'esperimento di classificazione. 

L'esperimento di valutazione ora appare come segue:

![Removing additional module][figure6]

###Aggiungere parametri facoltativi al servizio Web 

In alcuni casi, è possibile consentire all'utente del servizio Web di modificare il comportamento dei moduli quando si accede al servizio. *Web Service Parameters* consente di eseguire tale operazione.

Un esempio comune è la configurazione del modulo **Lettore** in modo che l'utente del servizio Web pubblicato possa specificare un'origine dati differente quando si accede al servizio Web. Oppure configurando il modulo **Scrittore** in modo che sia possibile specificare una destinazione differente. 

È possibile definire i parametri del servizio Web e associarli a uno o più parametri di modulo e specificare se sono obbligatori o facoltativi. Effettuando l'accesso al servizio e modificando adeguatamente le azioni del modulo, l'utente del servizio Web può fornire valori per tali parametri.

Per ulteriori informazioni sui parametri del servizio Web, vedere [Utilizzo dei parametri del servizio Web Azure Machine Learning
][webserviceparameters].

[webserviceparameters]: machine-learning-web-service-parameters.md


##Pubblicazione dell'esperimento di classificazione come servizio Web

Ora che l'esperimento di classificazione è stato sufficientemente preparato, è possibile pubblicarlo come servizio Web di Azure. Utilizzando il servizio Web, gli utenti possono inviare dati al modello che poi restituirà le previsioni.

Per altre informazioni sul processo di pubblicazione completo, vedere [Pubblicazione di un servizio Web Azure Machine Learning][pubblicazione]

[pubblicazione]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!--HONumber=49--> 
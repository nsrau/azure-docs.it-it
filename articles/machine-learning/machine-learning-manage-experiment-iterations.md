<properties 
	pageTitle="Gestire iterazioni dell'esperimento in Azure Machine Learning Studio | Azure" 
	description="Come gestire iterazioni dell'esperimento in Azure Machine Learning Studio" 
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
	ms.date="04/21/2015" 
	ms.author="garye"/>

#Gestire iterazioni dell'esperimento in Azure Machine Learning Studio 

Lo sviluppo di un modello di analisi predittive è un processo iterativo: man mano che si modificano le varie funzioni e i parametri dell'esperimento, i risultati convergono fino a quando l'utente non è soddisfatto del modello sottoposto a training. Per questo processo è fondamentale tenere traccia delle varie iterazioni dei parametri e delle configurazioni dell'esperimento.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

È possibile esaminare le esecuzioni precedenti dei propri esperimenti in qualsiasi momento per verificare, rivedere e infine confermare o ridefinire i presupposti precedenti. Quando si esegue un esperimento, ML Studio mantiene una cronologia dell'esecuzione, che include il set di dati, il modulo e le connessioni e i parametri di porta. Questa cronologia acquisisce anche i risultati, le informazioni di runtime come l'ora di inizio e di fine, i messaggi di log e lo stato dell'esecuzione. È possibile riesaminare tali esecuzioni in qualsiasi momento per rivedere la cronologia dell'esperimento e i risultati intermedi. È anche possibile usare un'esecuzione precedente dell'esperimento per avviare una nuova fase di richiesta e individuazione di informazioni nel percorso desiderato per la creazione di soluzioni di modellazione semplici, complesse o di insieme.

> [AZURE.NOTE]Quando si visualizza un'esecuzione precedente di un esperimento, tale versione dell'esperimento è bloccata e non può essere modificata. È tuttavia possibile salvarne una copia facendo clic su **SAVE AS** e specificando un nuovo nome per la copia. La nuova copia verrà visualizzata in ML Studio e sarà quindi possibile modificarla ed eseguirla. Questa copia dell'esperimento è disponibile nell'elenco **EXPERIMENTS** insieme a tutti gli altri esperimenti.

##Visualizzazione dell'esecuzione precedente

Dopo aver aperto un esperimento che è stato eseguito almeno una volta, è possibile visualizzarne l'esecuzione precedente facendo clic su **Prior Run** nel riquadro delle proprietà.

Si supponga ad esempio di creare un esperimento e di eseguirne tre versioni, alle 11:23, alle 11:42 e alle 11:55. Se si apre l'ultima esecuzione dell'esperimento (11:55) e si fa clic su **Prior Run**, verrà aperta la versione eseguita alle 11:42.

##Visualizzazione della cronologia delle esecuzioni

È possibile visualizzare tutte le esecuzioni precedenti di un esperimento facendo clic su **View Run History** in un esperimento aperto.

Si supponga ad esempio di creare un esperimento con il modulo [Linear Regression][linear-regression] e di voler osservare l'effetto della modifica del valore di **Learning rate** sui risultati dell'esperimento. Eseguire l'esperimento più volte con valori diversi per questo parametro, come indicato di seguito:

| Valore di Learning rate | Ora di inizio dell'esecuzione |
| ------------------- | -------------- |
| 0,1 | 11/9/2014 16:18:58
| 0,2 | 11/9/2014 16:24:33
| 0,4 | 11/9/2014 16:28:36
| 0,5 | 11/9/2014 16:33:31

Se si fa clic su **VIEW RUN HISTORY**, verrà visualizzato un elenco di tutte le esecuzioni:

![Esempio di cronologia delle esecuzioni][runhistory]

Fare clic su una di queste esecuzioni per visualizzare uno snapshot dell'esperimento al momento dell'esecuzione. La configurazione, i valori dei parametri, i commenti e i risultati vengono conservati per fornire un record completo dell'esecuzione dell'esperimento.

> [AZURE.TIP]Per documentarne le iterazioni dell'esperimento, è possibile modificarne il titolo ogni volta che viene eseguito, aggiornare il **Summary** nel riquadro delle proprietà e aggiungere o aggiornare i commenti relativi ai singoli moduli per registrare le modifiche. Il titolo, il riepilogo e i commenti relativi al modulo vengono salvati a ogni esecuzione dell'esperimento.

Nell'elenco degli esperimenti nella scheda **EXPERIMENTS** in ML Studio viene visualizzata sempre la versione più recente di un esperimento. Se si apre un'esecuzione precedente dell'esperimento (usando **Prior Run** o **VIEW RUN HISTORY**), è possibile tornare alla versione bozza facendo clic su **VIEW RUN HISTORY** e selezionando l'iterazione il cui **STATE** è **Editable**.

##Iterazione dell'esecuzione precedente 

Se si fa clic su **Prior Run** o su **VIEW RUN HISTORY** e si apre un'esecuzione precedente, è possibile visualizzare un esperimento completato in modalità di sola lettura.

Se si desidera avviare un'iterazione dell'esperimento nella modalità usata per configurare un'esecuzione precedente, è possibile aprire l'esecuzione e fare clic su **SAVE AS**. In questo modo verrà creato un nuovo esperimento con un nuovo titolo, una cronologia delle esecuzioni vuota e tutti i componenti e i valori dei parametri dell'esecuzione precedente. Questo nuovo esperimento è elencato nella scheda **EXPERIMENTS** nella home page di ML Studio ed è possibile modificarlo ed eseguirlo avviando una nuova cronologia delle esecuzioni per questa iterazione dell'esperimento.

Si supponga ad esempio di disporre della cronologia delle esecuzioni dell'esperimento illustrata nella sezione precedente e di voler osservare cosa accade quando si imposta il parametro **Learning rate** su 0,4 e si provano valori diversi per il parametro **Number of training epochs**.


1. Fare clic su **VIEW RUN HISTORY** e aprire l'iterazione dell'esperimento eseguita alle 16:28:36 in cui il valore del parametro è stato impostato su 0,4. 

2. Fare clic su **SAVE AS**.

3. Digitare un nuovo titolo e fare clic sul segno di spunta **OK**. Verrà creata una nuova copia dell'esperimento.

4. Modificare il parametro **Number of training epochs**.

5. Fare clic su **RUN**.

È ora possibile continuare a modificare ed eseguire questa versione dell'esperimento, creando una nuova cronologia delle esecuzioni per registrare il proprio lavoro.


<!-- Images -->
[runhistory]: ./media/machine-learning-manage-experiment-iterations/viewrunhistory.jpg


<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/

<!--HONumber=54--> 
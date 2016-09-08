<properties
	pageTitle="Estendere l'esperimento con R | Microsoft Azure"
	description="Come estendere le funzionalità di Azure Machine Learning Studio tramite il linguaggio R usando il modulo Execute R Script."
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
	ms.date="08/19/2016"
	ms.author="garye" />


#Estendere l'esperimento con R

È possibile estendere le funzionalità di ML Studio tramite il linguaggio R usando il modulo [Execute R Script][execute-r-script].

Questo modulo accetta più set di dati di input e produce un singolo set di dati come output. È possibile digitare uno script R nel parametro **R Script** del modulo [Execute R Script][execute-r-script].

Per accedere a ogni porta di input del modulo, usare codice simile al seguente:

    dataset1 <- maml.mapInputPort(1)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Elenco di tutti i pacchetti installati attualmente

L'elenco dei pacchetti installati può cambiare. Per ottenere l'elenco completo e corrente dei pacchetti installati, compresa la descrizione di ogni pacchetto, immettere il codice seguente nel modulo [Execute R Script][execute-r-script]\:

    out <- data.frame(installed.packages(,,,fields="Description"))
	maml.mapOutputPort("out")

In questo modo l'elenco dei pacchetti viene inviato alla porta di output del modulo [Execute R Script][execute-r-script]. Per visualizzare l'elenco di pacchetti, connettere un modulo di conversione, ad esempio [Convert to CSV][convert-to-csv] all'output di sinistra del modulo [Execute R Script][execute-r-script], eseguire l'esperimento, quindi fare clic sull'output del modulo di conversione e selezionare **Download**.

![](./media/machine-learning-extend-your-experiment-with-r/download-package-list.png)


##Importazione di pacchetti

È anche possibile importare pacchetti non ancora installati da un repository di gestione temporanea di ML Studio usando i comandi seguenti nel modulo [Execute R Script][execute-r-script] e l'archivio di pacchetti compresso:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

dove `my_favorite_package.zip` contiene il file ZIP del pacchetto.


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/

<!---HONumber=AcomDC_0824_2016-->
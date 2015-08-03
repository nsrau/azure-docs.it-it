<properties 
	pageTitle="App di esempio di Machine Learning: Frequently Bought Together | Microsoft Azure" 
	description="Un servizio Web di Machine Learning che esegue l'analisi dei carrelli degli acquisti online per offrire consigli sugli articoli spesso acquistati insieme ad altri in base alle transazioni cronologiche fornite dall'utente." 
	services="machine-learning" 
	documentationCenter="" 
	authors="CoromT" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/12/2015" 
	ms.author="luisca"/>

# App di esempio di Machine Learning: Frequently Bought Together


## IMPORTANTE: QUESTO SERVIZIO È STATO DEPRECATO

La funzionalità Frequently Bought Together fornita da questo servizio ora è stata integrata nella più estesa [API Recommendations](http://gallery.azureml.net/MachineLearningAPI/3574432384684cac9cc766e57729ea4c). È consigliabile usare Recommendations invece di questo servizio.

##Panoramica

Il [servizio Web Frequently Bought Together](https://datamarket.azure.com/dataset/amla/mba) in Machine Learning esegue l'analisi del carrello degli acquisti online per produrre, dalla cronologia delle transazioni, consigli sui prodotti di articoli frequentemente acquistati insieme. I consigli di Spesso acquistati insieme aiutano gli acquirenti a identificare i prodotti più rilevanti di un catalogo quando si acquista un articolo specifico. È stato dimostrato che la visualizzazione dei consigli, da parte del cliente, provoca una crescita reale delle vendite per i rivenditori online.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
  
##Introduzione 

Dopo aver effettuato la sottoscrizione al servizio Web Frequently Bought Together, è possibile usare l'[applicazione Web di esempio Market basket analysis](https://marketbasket.cloudapp.net/) per caricare facilmente i dati in un modello e scoprire i set di prodotti acquistati di frequente. Prima di usare l'applicazione o l'API, è necessaria la chiave API, che è possibile ottenere dalla [pagina dell'account di Marketplace dati di Azure](https://datamarket.azure.com/account).

##Uso del servizio Web 

Questo servizio contiene le API per creare modelli di Spesso acquistati insieme, caricare transazioni cronologiche e recuperare il set di prodotti più spesso acquistati insieme per un determinato prodotto. Esempi che illustrano come usare queste API sono disponibili nel repository [Azure-MachineLearning-DataScience](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Apps/FrequentlyBoughtTogether) su GitHub.

 

<!---HONumber=July15_HO4-->
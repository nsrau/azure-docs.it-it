<properties 
	pageTitle="App di esempio di Machine Learning: Spesso acquistati insieme | Azure" 
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
	ms.date="10/17/2014" 
	ms.author="coromt"/> 

# App di esempio di Machine Learning: Spesso acquistati insieme

##Informazioni generali
 
Il [Servizio Web Spesso acquistati insieme]( https://datamarket.azure.com/dataset/amla/mba) in Machine Learning consente di eseguire l'analisi del carrello degli acquisti online per produrre, dalla cronologia delle transazioni, consigli sui prodotti di articoli frequentemente acquistati insieme. I consigli di Spesso acquistati insieme aiutano gli acquirenti a identificare i prodotti più rilevanti di un catalogo quando si acquista un articolo specifico. È stato dimostrato che la visualizzazione dei consigli, da parte del cliente, provoca una crescita reale delle vendite per i rivenditori online. 

[AZURE.INCLUDE [machine-learning-free-trial](../includes/machine-learning-free-trial.md)] 
  
##Guida introduttiva 

Dopo aver effettuato la sottoscrizione al servizio Web Spesso acquistati insieme, è possibile utilizzare l'[applicazione Web di esempio Servizio di analisi del carrello degli acquisti](https://marketbasket.cloudapp.net/) per caricare facilmente i dati in un modello e scoprire i set di prodotti frequentemente acquistati. Prima di utilizzare l'applicazione o l'API, è necessaria la chiave API, che può essere ottenuta dalla [pagina dell'account di Azure DataMarket](https://datamarket.azure.com/account).

##Uso del servizio Web 

Questo servizio contiene le API per creare modelli di Spesso acquistati insieme, caricare transazioni cronologiche e recuperare il set di prodotti più spesso acquistati insieme per un determinato prodotto. Esempi che illustrano come utilizzare queste API sono disponibili sul repository [Azure-MachineLearning-DataScience](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Apps/FrequentlyBoughtTogether) su GitHub.


<!--HONumber=49-->
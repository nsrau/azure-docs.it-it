<properties 
	pageTitle="Analisi dei sentimenti basata sul lessico | Microsoft Azure" 
	description="Analisi dei sentimenti basata sul lessico" 
	services="machine-learning" 
	documentationCenter="" 
	authors="pengxia" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/02/2015" 
	ms.author="pengxia"/>



#Analisi dei sentimenti basata sul lessico 

Come si misurano le opinioni degli utenti e gli atteggiamenti verso i marchi o gli argomenti dei social network online, ad esempio post di Facebook, tweet, revisioni, ecc.? Le analisi dei sentimenti forniscono un metodo per l'analisi di queste domande.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

In genere esistono due metodi per l'analisi dei sentimenti. Uno consiste nell'utilizzare un algoritmo di apprendimento supervisionato e l'altro può essere considerato come un apprendimento senza supervisione. Un algoritmo di apprendimento supervisionato crea in genere un modello di classificazione in base a una raccolta con annotazioni di grandi dimensioni. La precisione si basa principalmente sulla qualità dell'annotazione e in genere il processo di training richiede molto tempo. Quando si applica l'algoritmo a un altro dominio, inoltre, i risultati non sono in genere validi. Rispetto all'apprendimento supervisionato, l'apprendimento non supervisionato basato sul lessico usa un dizionario di sentimenti, che non richiede l'archiviazione di un corpus di dati di grandi dimensioni e non necessita di training. Il processo risulta quindi molto più veloce.

Questo [servizio](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis) è basato sul lessico di soggettività MPQA , http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/) che è uno dei lessici di soggettività più usati. MPQA include 5097 parole negative e 2533 parole positive. Tutte queste parole sono annotate con polarità forte o debole. L'intero corpus è disponibile con la licenza pubblica generale GNU. Il servizio Web può essere applicato a qualsiasi frase breve, ad esempio tweet, post di Facebook e così via.

>Questo servizio Web può essere utilizzato dagli utenti: potenzialmente tramite un'app mobile, un sito Web o anche in un computer locale, ad esempio. Ma lo scopo del servizio Web è anche fornire un esempio di come è possibile utilizzare Azure Machine Learning per creare servizi Web in codice R. Con poche righe di codice R e la selezione di alcuni pulsanti in Azure Machine Learning Studio è possibile creare un esperimento con codice R e pubblicarlo come servizio Web. Il servizio Web può essere quindi pubblicato in Azure Marketplace e può essere usato da utenti e dispositivi in tutto il mondo, senza che l'autore del servizio Web debba configurare alcuna infrastruttura.

##Uso del servizio Web

Come dati di input è possibile usare qualsiasi testo, ma il servizio funziona in modo ottimale con frasi brevi. L'output è un valore numerico compreso tra -1 e 1. Qualsiasi valore inferiore a 0 indica che il sentimento del testo è negativo; mentre un valore maggiore di 0 indica che è positivo. Il valore assoluto del risultato indica il livello di attendibilità del sentimento associato.

>Questo servizio come ospitato in Azure Marketplace, è un servizio OData ed è possibile utilizzare i metodi POST o GET per effettuare le chiamate.

Sono disponibili molte opzioni per l'uso del servizio in modalità automatica. Per un'app di esempio, vedere [qui](http://microsoftazuremachinelearning.azurewebsites.net/).

###Codice C# iniziale per l'uso del servizio Web:

	public class ScoreResult
	{
	        [DataMember]
	        public double result
	        {
	            get;
	            set;
	        }
	}

	void main()
	{
	        using (var wb = new WebClient())
	        {
	            var acitionUri = new Uri("PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score");
	            DataServiceContext ctx = new DataServiceContext(acitionUri);
	            var cred = new NetworkCredential("PutEmailAddressHere", "ChangeToAPIKey");
	            var cache = new CredentialCache();
	
	            cache.Add(acitionUri, "Basic", cred);
	            ctx.Credentials = cache;
	            var query = ctx.Execute<ScoreResult>(acitionUri, "POST", true, new BodyOperationParameter("Text", TextBox1.Text));
	            ScoreResult scoreResult = query.ElementAt(0);
	            double result = scoreResult.result;
	    	}
	}



L'input è "Oggi è un buon giorno". L'output è "1", che indica un sentimento positivo associato alla frase di input.

##Creazione del servizio Web
>Questo servizio Web è stato creato tramite Azure Machine Learning. Per una versione di valutazione gratuita e per video introduttivi sulla creazione di esperimenti e sulla [pubblicazione di servizi Web](machine-learning-publish-a-machine-learning-web-service.md), vedere [azure.com/ml.](http://azure.com/ml) La schermata seguente mostra un esperimento per la creazione del servizio Web e codice di esempio per ogni modulo incluso nell'esperimento.


In Azure Machine Learning è stato creato un nuovo esperimento vuoto. La figura seguente mostra il flusso dell'esperimento di un'analisi del sentimento basata sul lessico. Il file "sent\_dict.csv" è il lessico di soggettività MPQA ed è configurato come uno degli input di ["Execute R Script"][execute-r-script]. Un altro input è costituito da una revisione campionata dal set di dati di revisioni Amazon per la verifica, in cui sono state eseguite operazioni di selezione, modifica del nome di colonna e suddivisione. Un pacchetto di hash viene usato per archiviare il lessico di soggettività in memoria e accelerare il processo di calcolo del punteggio. L'intero testo verrà suddiviso in token dal pacchetto "tm" e verrà confrontato con le parole disponibili nel dizionario di sentiment. Verrà infine calcolato un punteggio tramite la somma dei pesi di ogni parola soggettiva nel testo.

###Flusso dell'esperimento:

![Flusso dell'esperimento][2]


####Modulo 1:
	
	# Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame
 
   # Install hash package install.packages("src/hash\_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE) success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE) library(tm) library(stringr)

    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)

    #  Compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
	polarity_ratio <- 0
	polarity_total <- 0
	not <- 0
	sentence <- tolower(dataset2[m,1])
	if (nchar(sentence) > 0){
		token_array <- scan_tokenizer(sentence)
		for (j in 1:length(token_array)){
			word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
		    for (k in 1:length(negation_word)){
		      if (word == negation_word[k]){
		        not <- (not+1) %% 2

			  }
		    }
			if (word != ""){
			    if (!is.null(sent_dict[[word]])){
			      polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
			      polarity_total <- polarity_total + abs(sent_dict[[word]])
			    }
			}
		  
		}
	}
	if (polarity_total > 0){
		result <- c(result, polarity_ratio/polarity_total)
	}else{
		result<- c(result,0)
	}
    }

    # Sample operation
    data.set <- data.frame(result)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")
	


##Limitazioni

Dal punto di vista dell'algoritmo, l'analisi dei sentimenti basata sul lessico è uno strumento generale per l'analisi dei sentimenti ed è possibile che non offra prestazioni migliori rispetto al metodo di classificazione per campi specifici. Il problema relativo alla negazione non viene affrontato. Nel programma sono codificate alcune parole di negazione, ma è consigliabile usare un dizionario di negazione e creare alcune regole. Il servizio Web offre prestazioni migliori per frasi brevi e semplici quali tweet e post di Facebook rispetto a frasi lunghe e complesse come quelle delle revisioni di Amazon.

##Domande frequenti
Per le domande frequenti relative all'uso del servizio Web o alla pubblicazione in Azure Marketplace, vedere [qui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/

 

<!---HONumber=Sept15_HO2-->
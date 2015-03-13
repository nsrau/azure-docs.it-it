<properties 
	pageTitle="Passaggio 1: Analisi dei sentimenti basata sul lessico | Azure" 
	description="Analisi dei sentimenti basata sul lessico" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="jaymathe"/>



#Analisi del sentiment basata sul lessico 

 


In che modo è possibile misurare l'opinione e l'atteggiamento degli utenti rispetto a marchi o argomenti in social network online, quali post di Facebook, tweet, valutazioni e così via. L'analisi del sentiment offre un metodo per rispondere a questa domanda.

Sono in genere disponibili due metodi per l'analisi del sentiment, ovvero un metodo basato sull'uso di un algoritmo di apprendimento supervisionato e uno basato sull'apprendimento non supervisionato. L'algoritmo di apprendimento supervisionato crea in genere un modello di classificazione su un corpus annotato di grandi dimensioni. La precisione dell'algoritmo dipende principalmente dalla qualità dell'annotazione e il processo di training richiede in genere molto tempo. Quando si applica l'algoritmo a un altro dominio, inoltre, i risultati non sono in genere validi. Rispetto all'apprendimento supervisionato, l'apprendimento non supervisionato basato sul lessico usa un dizionario di sentiment, che non richiede l'archiviazione di un corpus di dati di grandi dimensioni e non necessita di training. Il processo risulta quindi molto più veloce. 

Questo [servizio](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis) è basato sul lessico MPQA (http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/), che è uno dei lessici di soggettività più usati. MPQA include 5097 parole negative e 2533 parole positive. Tutte queste parole sono annotate con polarità forte o debole. L'intero corpus è disponibile con la licenza pubblica generale GNU. Il servizio Web può essere applicato a qualsiasi frase breve, ad esempio tweet, post di Facebook e così via. 

>Benché questo servizio Web possa essere usato dagli utenti, potenzialmente tramite un'app mobile, un sito Web o anche un computer locale, ad esempio, la finalità di questo servizio Web consiste anche nel fungere da esempio dell'uso di Azure ML per creare servizi Web basati sul codice R. Con poche righe di codice R e la selezione di alcuni pulsanti in Azure ML Studio è possibile creare un esperimento con codice R e pubblicato come servizio Web. Il servizio Web può essere quindi pubblicato in Azure Marketplace e può essere usato da utenti e dispositivi in tutto il mondo, senza che l'autore del sito Web debba configurare alcuna infrastruttura.

##Uso del servizio Web

Come dati di input è possibile usare qualsiasi testo, ma il servizio funziona in modo ottimale con frasi brevi. L'output è un valore numerico compreso tra -1 e 1. Qualsiasi valore inferiore a 0 indica che il sentiment del testo è negativo e qualsiasi valore superiore a 0 indica che il sentiment è positivo. Il valore assoluto del risultato indica la forza del sentiment associato. 

>Questo servizio è ospitato in Microsoft Azure Marketplace come servizio OData ed è possibile usare i metodi POST o GET per effettuare le chiamate. 

Sono disponibili molte opzioni per l'uso del servizio in modalità automatica. Per un'app di esempio, vedere [qui](http://microsoftazuremachinelearning.azurewebsites.net/).

###Codice C# iniziale per l'uso del servizio Web:

	public class Input{
	public double Recency;
	public double Frequency;
	public double Monetary;
	public double Time;
	public double Class;
	}

	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
        System.Diagnostics.Debug.WriteLine("AuthenticationHeaderValue" + new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray)));
        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
       
	void Main()
	{
  	var input = new Input(){Recency =1, Frequency=0,Monetary=0,Time=1, Class= 0};
	var json = JsonConvert.SerializeObject(input);
	var acitionUri =  "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
       
  	var httpClient = new HttpClient();
   	httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere","ChangeToAPIKey");
   	httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
  	var query = httpClient.PostAsync(acitionUri,new StringContent(json));
  	var result = query.Result.Content;
  	var scoreResult = result.ReadAsStringAsync().Result;
  	scoreResult.Dump();
	}


L'input è "Today is a good day". L'output è "1", che indica un sentiment positivo associato alla frase di input. 

##Creazione del servizio Web
>Questo servizio Web è stato creato tramite Azure ML. Per una versione di valutazione gratuita e per video introduttivi sulla creazione di esperimenti e sulla [pubblicazione di servizi Web](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), vedere [azure.com/ml](http://azure.com/ml). La schermata seguente mostra un esperimento per la creazione del servizio Web e codice di esempio per ogni modulo incluso nell'esperimento.


In Azure ML è stato creato un nuovo esperimento vuoto. La figura seguente mostra il flusso dell'esperimento di un'analisi del sentiment basata sul lessico. "sent_dict.csv" è il lessico di soggettività MPQA ed è configurato come uno degli input di "Execute R Script". Un altro input è costituito da una revisione campionata dal set di dati di revisioni Amazon per la verifica, in cui sono state eseguite operazioni di selezione, modifica del nome di colonna e suddivisione.  Un pacchetto di hash viene usato per archiviare il lessico di soggettività in memoria e accelerare il processo di calcolo del punteggio. L'intero testo verrà suddiviso in token dal pacchetto "tm" e verrà confrontato con le parole disponibili nel dizionario di sentiment. Verrà infine calcolato un punteggio tramite la somma dei pesi di ogni parola soggettiva nel testo. 

###Flusso dell'esperimento:

![experimenmt flow][2]


####Modulo 1:
	
	# Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame
    # install hash package
    install.packages("src/hash_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE)
    library(tm)
    library(stringr)
    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)
    #  compute sentiment score for each document
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

Dal punto di vista dell'algoritmo, l'analisi del sentiment basata sul lessico è uno strumento generale per l'analisi del sentiment ed è possibile che non offra prestazioni migliori rispetto al metodo Classification per campi specifici. Il problema relativo alla negazione non viene affrontato. Nel programma sono codificate alcune parole di negazione, ma è consigliabile usare un dizionario di negazione e creare alcune regole. Il servizio Web offre prestazioni migliori per frasi brevi e semplici quali tweet e post di Facebook rispetto a frasi lunghe e complesse come quelle delle revisioni di Amazon. 

##Domande frequenti
Per le domande frequenti relative all'uso del servizio Web o alla pubblicazione nel Marketplace, vedere [qui](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png









<!--HONumber=46--> 

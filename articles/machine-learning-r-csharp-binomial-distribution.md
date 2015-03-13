<properties 
	pageTitle="Binomial Distribution Suite | Azure" 
	description="Binomial Distribution Suite" 
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


#Binomial Distribution Suite




Binomial Distribution Suite è un insieme di servizi Web di esempio ([Binomial Generator](https://datamarket.azure.com/dataset/aml_labs/bdg5), [Probability Calculator]( https://datamarket.azure.com/dataset/aml_labs/bdp4), [Quantile Calculator]( https://datamarket.azure.com/dataset/aml_labs/bdq5)), che include in particolare tre servizi che semplificano la generazione e la gestione delle distribuzioni binomiali. I servizi permettono la generazione di una sequenza di distribuzioni binomiali di qualsiasi lunghezza, calcolando i quantili rispetto alla probabilità specificata e calcolando la probabilità in base a un quantile specificato.  Ogni servizio emette output diversi, in base al servizio selezionato, come illustrato più avanti. Binomial Distribution Suite è basato sulle funzioni qbinom, rbinom e pbinom del codice R, incluse nel pacchetto statistico R. 

>Benché questi servizi Web possano essere usati dagli utenti, potenzialmente direttamente nel Marketplace, tramite un'app mobile, un sito Web o anche un computer locale, ad esempio, la finalità di questo servizio Web consiste anche nel fungere da esempio dell'uso di Azure ML per creare servizi Web basati sul codice R. Con poche righe di codice R e la selezione di alcuni pulsanti in Azure ML Studio è possibile creare un esperimento con codice R e pubblicato come servizio Web. Il servizio Web può essere quindi pubblicato in Azure Marketplace e può essere usato da utenti e dispositivi in tutto il mondo, senza che l'autore del sito Web debba configurare alcuna infrastruttura.

##Uso del servizio Web
Binomial Distribution Suite include i tre servizi seguenti:

###Binomial Distribution Quantile Calculator
Questo servizio accetta quattro argomenti di una distribuzione normale e calcola il quantile associato.
Gli argomenti di input sono i seguenti:

- p: probabilità aggregata singola di più prove.  
- size: numero di prove.
- prob: probabilità di successo in una prova.
- Side: L per la parte inferiore della distribuzione, U per la parte superiore della distribuzione. 

L'output del servizio corrisponde al quantile calcolato associato alla probabilità specificata.

###Binomial Distribution Probability Calculator
Questo servizio accetta quattro argomenti di una distribuzione binomiale e calcola la probabilità associata.
Gli argomenti di input sono i seguenti:

- q: singolo quantile di un evento con distribuzione binomiale. 
- size: numero di prove.
- prob: probabilità di successo in una prova.
- side: L per la parte inferiore della distribuzione, U per la parte superiore della distribuzione oppure E che equivale a un numero singolo di successi.

L'output del servizio corrisponde alla probabilità calcolata associata al quantile specificato.

###Binomial Distribution Generator
Questo servizio accetta tre argomenti di una distribuzione binomiale e genera una sequenza casuale di numeri distribuiti in modo binomiale. 
È necessario fornire gli argomenti seguenti nella richiesta:

- n: numero di osservazioni. 
- size: numero di prove.
- prob: probabilità di successo.

L'output del servizio corrisponde a una sequenza di lunghezza n con una distribuzione binomiale basata sugli argomenti size e prob.

>Questo servizio è ospitato in Microsoft Azure Marketplace come servizio OData ed è possibile usare i metodi POST o GET per effettuare le chiamate. 

Sono disponibili molte opzioni per l'uso del servizio in modalità automatica. Per app di esempio, vedere qui: [Generator](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionGenerator.aspx),
[Probability Calculator](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionProbabilityCalculator.aspx),
[Quantile Calculator](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionQuantileCalculator). 

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


##Creazione del servizio Web 

>Questo servizio Web è stato creato tramite Azure ML. Per una versione di valutazione gratuita e per video introduttivi sulla creazione di esperimenti e sulla [pubblicazione di servizi Web](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), vedere [azure.com/ml](http://azure.com/ml). La schermata seguente mostra un esperimento per la creazione del servizio Web e codice di esempio per ogni modulo incluso nell'esperimento.

###Binomial Distribution Quantile Calculator

![Create workspace][4]

####Modulo 1:
    #data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
####Modulo 2:

    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    if (param$p < 0 ) {
	print('Bad input: p must be between 0 and 1')
	param$p = 0
    } else if (param$p > 1) {
	print('Bad input: p must be between 0 and 1')
	param$p = 1
    }

    if (param$prob < 0 ) {
	print('Bad input: prob must be between 0 and 1')
	param$prob = 0
    } else if (param$prob > 1) {
	print('Bad input: prob must be between 0 and 1')
	param$prob = 1
    }

    quantile = qbinom(param$p,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    quantile

    if (param$side == 'U'){
	quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = F)
	band=subset(df,x>quantile)
    } else if (param$side =='L') {
	quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = T)
	band=subset(df,x<=quantile)
    } else {
	print("Invalid side choice")
    }

    output = as.data.frame(quantile)
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");


###Binomial Distribution Probability Calculator

![Create workspace][5]

####Modulo 1:

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(q=5,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port


####Modulo 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    prob = pbinom(param$q,size=param$size,prob=param$prob)
    prob.eq = dbinom(param$q,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    prob

    if (param$side == 'U'){
	prob = 1 - prob
	band=subset(df,x>param$q)
    } else if (param$side =='E') {
	prob = prob.eq
	band=subset(df,x==param$q)
    } else if (param$side =='L') {
	prob = prob
	band=subset(df,x<=param$q)
    } else {
	print("Invalid side choice")
    }

    output = as.data.frame(prob)
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

###Binomial Distribution Generator

![Create workspace][6]

####Modulo 1:

    #data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,size=10,p=.5);
    maml.mapOutputPort("data.set"); #send data to output port

####Modulo 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    dist = rbinom(param$n,param$size,param$p)

    output = as.data.frame(t(dist))
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

##Limitazioni 
Questi sono esempi molto semplici relativi alla distribuzione binomiale. Come si può notare dal codice di esempio precedente, è implementata un rilevamento limitato degli errori.

##Domande frequenti
Per le domande frequenti relative all'uso del servizio Web o alla pubblicazione nel Marketplace, vedere [qui](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).


[1]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_1.png

[2]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_2.png

[3]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_3.png

[4]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_4.png

[5]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_5.png

[6]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_6.png

<!--HONumber=46--> 

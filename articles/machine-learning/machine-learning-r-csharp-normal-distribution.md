<properties 
	pageTitle="Normal Distribution Web Service Suite | Azure" 
	description="Normal Distribution Web Service Suite" 
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

#Normal Distribution Web Service Suite



Normal Distribution Suite è un set di servizi Web di esempio ([Generator]( https://datamarket.azure.com/dataset/aml_labs/ndg7), [Quantile Calculator]( https://datamarket.azure.com/dataset/aml_labs/ndq5), [Probability Calculator]( https://datamarket.azure.com/dataset/aml_labs/ndp5)), che include in particolare tre servizi che semplificano la generazione e la gestione delle distribuzioni normali. I servizi permettono la generazione di una sequenza di distribuzioni normali di qualsiasi lunghezza, calcolando i quantili rispetto alla probabilità specificata e calcolando la probabilità in base a un quantile specificato.  Ogni servizio emette output diversi, in base al servizio selezionato, come illustrato più avanti. Normal Distribution Suite è basato sulle funzioni qnorm, rnorm e pnorm del codice R, incluse nel pacchetto statistico R.

>Benché questo servizio Web possa essere usato dagli utenti, potenzialmente tramite un'app mobile, un sito Web o anche un computer locale, ad esempio, la finalità di questo servizio Web consiste anche nel fungere da esempio dell'uso di Azure ML per creare servizi Web basati sul codice R. Con poche righe di codice R e la selezione di alcuni pulsanti in Azure ML Studio è possibile creare un esperimento con codice R e pubblicato come servizio Web. Il servizio Web può essere quindi pubblicato in Azure Marketplace e può essere usato da utenti e dispositivi in tutto il mondo, senza che l'autore del sito Web debba configurare alcuna infrastruttura.  
 

##Uso del servizio Web
Normal Distribution Suite include i tre servizi seguenti:

###Normal Distribution Quantile Calculator:
Questo servizio accetta quattro argomenti di una distribuzione normale e calcola il quantile associato.

Gli argomenti di input sono i seguenti:

* p: probabilità singola di un evento con distribuzione normale. 
* Mean: media della distribuzione normale.
* SD: deviazione standard della distribuzione normale. 
* Side: L per la parte inferiore della distribuzione, U per la parte superiore della distribuzione.

L'output del servizio corrisponde al quantile calcolato associato alla probabilità specificata.

###Normal Distribution Probability Calculator:
Questo servizio accetta quattro argomenti di una distribuzione normale e calcola la probabilità associata.

Gli argomenti di input sono i seguenti:

* q: singolo quantile di un evento con distribuzione normale. 
* Mean: media della distribuzione normale.
* SD: deviazione standard della distribuzione normale. 
* Side: L per la parte inferiore della distribuzione, U per la parte superiore della distribuzione.

L'output del servizio corrisponde alla probabilità calcolata associata al quantile specificato.

###Normal Distribution Generator
Questo servizio accetta tre argomenti di una distribuzione normale e genera una sequenza casuale di numeri distribuiti in modo normale. 
È necessario fornire gli argomenti seguenti nella richiesta:

* n: numero di osservazioni. 
* mean: media della distribuzione normale.
* sd: deviazione standard della distribuzione normale. 

L'output del servizio corrisponde a una sequenza di lunghezza n con una distribuzione normale basata sugli argomenti mean e sd.

>Questo servizio è ospitato in Microsoft Azure Marketplace come servizio OData ed è possibile usare i metodi POST o GET per effettuare le chiamate. 

Sono disponibili molte opzioni per l'uso del servizio in modalità automatica. Per app di esempio, vedere qui: [Generator](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionGenerator.aspx),
[Probability Calculator](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionProbabilityCalculator.aspx),
[Quantile Calculator](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionQuantileCalculator.aspx).

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
>Questo servizio Web è stato creato tramite Azure ML. Per una versione di valutazione gratuita e per video introduttivi sulla creazione di esperimenti e sulla [pubblicazione di servizi Web](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), vedere [azure.com/ml](http://azure.com/ml). 

La schermata seguente mostra un esperimento per la creazione del servizio Web e codice di esempio per ogni modulo incluso nell'esperimento.

###Normal Distribution Quantile Calculator:
Flusso dell'esperimento
![Experiment flow][2]
 
	#data schema with example data (replaced with data from web service)
	data.set=data.frame(p=0.1,mean=0,sd=1,side='L');
	maml.mapOutputPort("data.set"); #send data to output port
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	if (param$p < 0 ) {
	print('Bad input: p must be between 0 and 1')
	param$p = 0
	} else if (param$p > 1) {
	print('Bad input: p must be between 0 and 1')
	param$p = 1
	}
	q = qnorm(param$p,mean=param$mean,sd=param$sd)

	if (param$side == 'U'){
	q = 2* param$mean - q
	} else if (param$side =='L') {
	q = q
	} else {
	print("Invalid side choice")
	}

	output = as.data.frame(q)
	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");
	
###Normal Distribution Probability Calculator:
Flusso dell'esperimento
![Experiment flow][3]
 
 	#data schema with example data (replaced with data from web service)
	data.set=data.frame(q=-1,mean=0,sd=1,side='L');
	maml.mapOutputPort("data.set"); #send data to output port
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	prob = pnorm(param$q,mean=param$mean,sd=param$sd)

	if (param$side == 'U'){
	prob = 1 - prob
	} else if (param$side =='B') {
	prob = ifelse(prob<=0.5,prob * 2, 1)
	} else if (param$side =='L') {
	prob = prob
	} else {
	print("Invalid side choice")
	}

	output = as.data.frame(prob)
	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");
	
###Normal Distribution Generator:
Flusso dell'esperimento
![Experiment flow][4]

	#data schema with example data (replaced with data from web service)
	data.set=data.frame(n=50,mean=0,sd=1);
	maml.mapOutputPort("data.set"); #send data to output port
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	dist = rnorm(param$n,mean=param$mean,sd=param$sd)

	output = as.data.frame(t(dist))

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");

##Limitazioni 

Questi sono esempi molto semplici relativi alla distribuzione normale. Come si può notare dal codice di esempio precedente, è implementata un rilevamento limitato degli errori.

##Domande frequenti
Per le domande frequenti relative all'uso del servizio Web o alla pubblicazione nel Marketplace, vedere [qui](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-normal-distribution/normal-img1.png
[2]: ./media/machine-learning-r-csharp-normal-distribution/normal-img2.png
[3]: ./media/machine-learning-r-csharp-normal-distribution/normal-img3.png
[4]: ./media/machine-learning-r-csharp-normal-distribution/normal-img4.png

<!--HONumber=46--> 

<!--HONumber=46--> 
 
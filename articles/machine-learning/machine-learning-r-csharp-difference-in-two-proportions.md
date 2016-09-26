<properties 
	pageTitle="Difference in Proportions Test | Microsoft Azure" 
	description="Difference in Proportions Test" 
	services="machine-learning" 
	documentationCenter="" 
	authors="aniedea" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/06/2016" 
	ms.author="aniedea"/>


#Difference in Proportions Test


Due proporzioni presentano differenze a livello statistico? Si supponga che un utente voglia confrontare due film per determinare se un film ha ottenuto una proporzione significativamente superiore di "mi piace" rispetto all'altro. Con un campione di grandi dimensioni, potrebbe esservi una differenza statisticamente significativa tra le proporzioni 0,50 e 0,51. Con un campione di piccole dimensioni, potrebbero non essere disponibili dati sufficienti per determinare se queste proporzioni sono effettivamente diverse.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Questo [servizio Web](https://datamarket.azure.com/dataset/aml_labs/prop_test) esegue una verifica dell'ipotesi relativa alla differenza tra le due proporzioni in base all'input utente del numero di successi e del numero totale di prove per i due gruppi di confronto. Questo scenario si può verificare in caso di chiamata del servizio Web dall’interno di un'app di confronto tra film, per comunicare a un utente se uno dei film è effettivamente più apprezzato rispetto all'altro sulla base delle classificazioni dei film.

>Questo servizio Web può essere utilizzato dagli utenti: potenzialmente tramite un'app mobile, un sito Web o anche in un computer locale, ad esempio. Ma lo scopo del servizio Web è anche fornire un esempio di come è possibile utilizzare Azure Machine Learning per creare servizi Web in codice R. Con poche righe di codice R e la selezione di alcuni pulsanti in Azure Machine Learning Studio è possibile creare un esperimento con codice R e pubblicarlo come servizio Web. Il servizio Web può essere quindi pubblicato in Azure Marketplace e può essere usato da utenti e dispositivi in tutto il mondo, senza che l'autore del servizio Web debba configurare alcuna infrastruttura.


##Uso del servizio Web

Questo servizio accetta quattro argomenti ed esegue una verifica dell'ipotesi delle proporzioni.

Gli argomenti di input sono i seguenti:

* Successes1: numero di eventi con esito positivo nel campione 1.
* Successes2: numero di eventi con esito positivo nel campione 2.
* Total1: dimensione del campione 1.
* Total2: dimensione del campione 2.

L'output del servizio è il risultato della verifica dell'ipotesi, insieme alla statistica chi quadrato, ai gradi di libertà, al valore p, la proporzione nel campione 1/2 e ai limiti dell'intervallo di confidenza.

>Questo servizio come ospitato in Azure Marketplace, è un servizio OData ed è possibile utilizzare i metodi POST o GET per effettuare le chiamate.

Sono disponibili molte opzioni per l'uso del servizio in modalità automatica. Per un'app di esempio, vedere [qui](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx).

###Codice C# iniziale per l'uso del servizio Web:

	public class Input
	{
	        public string successes1;
	        public string successes2;
	        public string total1;
	        public string total2;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}

	void Main()
	{
	        var input = new Input() { successes1 = TextBox1.Text, successes2 = TextBox2.Text, total1 = TextBox3.Text, total2 = TextBox4.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
	    	var scoreResult = result.ReadAsStringAsync().Result;
	}


##Creazione del servizio Web

>Questo servizio Web è stato creato tramite Azure Machine Learning. Per una versione di valutazione gratuita e per video introduttivi sulla creazione di esperimenti e sulla [pubblicazione di servizi Web](machine-learning-publish-a-machine-learning-web-service.md), vedere [azure.com/ml.](http://azure.com/ml) La schermata seguente mostra un esperimento per la creazione del servizio Web e codice di esempio per ogni modulo incluso nell'esperimento.

In Azure Machine Learning, è stato creato un nuovo esperimento vuoto con due moduli [Execute R Script][execute-r-script]. Lo schema di dati nel primo modulo è definito, mentre il secondo modulo usa il comando prop.test in R per eseguire la verifica dell'ipotesi per due proporzioni.


###Flusso dell'esperimento

![Flusso dell'esperimento][2]


####Modulo 1:
	####Schema definition  
	data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
	maml.mapOutputPort("data.set"); #send data to output port
	dataset1 = maml.mapInputPort(1) #read data from input port
	

####Modulo 2:

	test=prop.test(c(dataset1$successes1[1],dataset1$successes2[1]),c(dataset1$total1[1],dataset1$total2[1])) #conduct hypothesis test

	result=data.frame(
	result=ifelse(test$p.value<0.05,"The proportions are different!",
	"The proportions aren't different statistically."),
	ChiSquarestatistic=round(test$statistic,2),df=test$parameter,
	pvalue=round(test$p.value,4),
	proportion1=round(test$estimate[1],4),
	proportion2=round(test$estimate[2],4),
	confintlow=round(test$conf.int[1],4),
	confinthigh=round(test$conf.int[2],4)) 

	maml.mapOutputPort("result"); #output port
	

##Limitazioni 

Questo è un esempio molto semplice per la verifica della differenza tra due proporzioni. Come si può notare dall'esempio di codice precedente, il rilevamento degli errori non è implementato e il servizio presuppone che tutte le variabili siano continue.

##Domande frequenti
Per le domande frequenti relative all'uso del servizio Web o alla pubblicazione in Azure Marketplace, vedere [qui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=AcomDC_0914_2016-->
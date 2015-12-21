<properties 
	pageTitle="Forecasting-Exponential Smoothing | Microsoft Azure" 
	description="Servizio Web: Forecasting-Exponential Smoothing" 
	services="machine-learning" 
	documentationCenter="" 
	authors="xueshanz" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2015" 
	ms.author="xueshzha"/>


#Forecasting-Exponential Smoothing 

Questo [servizio Web](https://datamarket.azure.com/dataset/aml_labs/ets) implementa il modello di livellamento esponenziale (ETS, Exponential Smoothing) per produrre previsioni basate sui dati cronologici forniti dall'utente. Si verificherà un incremento nella domanda di un prodotto specifico quest'anno? È possibile prevedere le vendite dei prodotti per la stagione natalizia, per pianificare in modo efficace l'inventario? I modelli di previsione sono progettati per rispondere a queste domande. Partendo dai dati passati, questi modelli esaminano le tendenze nascoste e la stagionalità per prevedere le tendenze future.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
 
>Questo servizio Web può essere utilizzato dagli utenti: potenzialmente tramite un'app mobile, un sito Web o anche in un computer locale, ad esempio. Ma lo scopo del servizio Web è anche fornire un esempio di come è possibile utilizzare Azure Machine Learning per creare servizi Web in codice R. Con poche righe di codice R e la selezione di alcuni pulsanti in Azure Machine Learning Studio è possibile creare un esperimento con codice R e pubblicarlo come servizio Web. Il servizio Web può essere quindi pubblicato in Azure Marketplace e può essere usato da utenti e dispositivi in tutto il mondo, senza che l'autore del servizio Web debba configurare alcuna infrastruttura.
 
##Uso del servizio Web 
 
Questo servizio accetta quattro argomenti e calcola le previsioni ETS.
Gli argomenti di input sono i seguenti:

* Frequency: indica la frequenza dei dati non elaborati (giornaliera/settimanale/mensile/trimestrale/annuale)
* Horizon: intervallo di tempo futuro della previsione
* Date: consente di immettere i nuovi dati relativi alle serie temporali per il tempo
* Value: consente di aggiungere i nuovi valori delle serie di dati.

L'output del servizio è costituito dai valori calcolati della previsione.

L'input può essere ad esempio il seguente:

* Frequency - 12
* Horizon - 12
* Date - 1/15/2012;2/15/2012;3/15/2012;4/15/2012;5/15/2012;6/15/2012;7/15/2012;8/15/2012;9/15/2012;10/15/2012;11/15/2012;12/15/2012;
1/15/2013;2/15/2013;3/15/2013;4/15/2013;5/15/2013;6/15/2013;7/15/2013;8/15/2013;9/15/2013;10/15/2013;11/15/2013;12/15/2013;
1/15/2014;2/15/2014;3/15/2014;4/15/2014;5/15/2014;6/15/2014;7/15/2014;8/15/2014;9/15/2014
* Value - 3.479;3.68;3.832;3.941;3.797;3.586;3.508;3.731;3.915;3.844;3.634;3.549;3.557;3.785;3.782;3.601;3.544;3.556;3.65;3.709;3.682;3.511;
3.429;3.51;3.523;3.525;3.626;3.695;3.711;3.711;3.693;3.571;3.509
 
>Questo servizio come ospitato in Azure Marketplace, è un servizio OData ed è possibile utilizzare i metodi POST o GET per effettuare le chiamate.

Sono disponibili molte opzioni per l'uso del servizio in modalità automatica. Per un'app di esempio, vedere [qui](http://microsoftazuremachinelearning.azurewebsites.net/etsForecasting.aspx).

###Codice C# iniziale per l'uso del servizio Web:
	public class Input
	{
	        public string frequency;
	        public string horizon;
	        public string date;
	        public string value;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}

	void Main()
	{
	        var input = new Input() { frequency = TextBox1.Text, horizon = TextBox2.Text, date = TextBox3.Text, value = TextBox4.Text };
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

In Azure Machine Learning è stato creato un nuovo esperimento vuoto. Sono stati caricati dati di input di esempio con uno schema dati predefinito. Un modulo [Execute R Script][execute-r-script] è collegato allo schema dati e genera il modello di previsione ETS usando le funzioni 'ets' e 'forecast' di R.


![Flusso dell'esperimento][2]

####Modulo 1:
 
	# Add in the CSV file with the data in the format shown below 
![Dati di esempio][3]

####Modulo 2:
	# Data input
	data <- maml.mapInputPort(1) # class: data.frame
	library(forecast)
	
	# Preprocessing
	colnames(data) <- c("frequency", "horizon", "dates", "values")
	dates <- strsplit(data$dates, ";")[[1]]
	values <- strsplit(data$values, ";")[[1]]
	
	dates <- as.Date(dates, format = '%m/%d/%Y')
	values <- as.numeric(values)
	
	# Fit a time-series model
	train_ts<- ts(values, frequency=data$frequency)
	fit1 <- ets(train_ts)
	train_model <- forecast(fit1, h = data$horizon)
	plot(train_model)
	
	# Produce forcasting
	train_pred <- round(train_model$mean,2)
	data.forecast <- as.data.frame(t(train_pred))
	colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
	
	# Data output
	maml.mapOutputPort("data.forecast");

 
##Limitazioni 

Questo è un esempio molto semplice di previsione ETS. Come si può notare nell'esempio di codice precedente, il rilevamento di errori non è implementato, il servizio presuppone che tutte le variabili siano valori continui/positivi e la frequenza deve essere un numero intero maggiore di 1. La lunghezza dei vettori di data e valore deve essere uguale. La lunghezza dei vettori della data e il valore deve essere la stessa. La variabile relativa alla data deve avere il formato 'mm/dd/yyyy'.

##Domande frequenti
Per le domande frequenti relative all'uso del servizio Web o alla pubblicazione in Azure Marketplace, vedere [qui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img1.png
[2]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img2.png
[3]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=AcomDC_1203_2015-->


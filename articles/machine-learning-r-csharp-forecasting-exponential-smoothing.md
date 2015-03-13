<properties 
	pageTitle="Forecasting-Exponential Smoothing | Azure" 
	description="Servizio Web: Forecasting-Exponential Smoothing" 
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


#Forecasting-Exponential Smoothing 
  



Questo [servizio Web]( https://datamarket.azure.com/dataset/aml_labs/ets) implementa il modello di livellamento esponenziale (ETS, Exponential Smoothing) per produrre previsioni basate sui dati cronologici forniti dall'utente. Si verificherà un incremento nella domanda di un prodotto specifico quest'anno? È possibile prevedere le vendite dei prodotti per la stagione natalizia, per pianificare in modo efficace l'inventario? I modelli di previsione sono progettati per rispondere a queste domande. Partendo dai dati passati, questi modelli esaminano le tendenze nascoste e la stagionalità per prevedere le tendenze future.  
 
>Benché questo servizio Web possa essere usato dagli utenti, potenzialmente tramite un'app mobile, un sito Web o anche un computer locale, ad esempio, la finalità di questo servizio Web consiste anche nel fungere da esempio dell'uso di Azure ML per creare servizi Web basati sul codice R. Con poche righe di codice R e la selezione di alcuni pulsanti in Azure ML Studio è possibile creare un esperimento con codice R e pubblicato come servizio Web. Il servizio Web può essere quindi pubblicato in Azure Marketplace e può essere usato da utenti e dispositivi in tutto il mondo, senza che l'autore del sito Web debba configurare alcuna infrastruttura.
 
##Uso del servizio Web 
 
Questo servizio accetta quattro argomenti e calcola le previsioni ETS.
Gli argomenti di input sono i seguenti:

* Frequency: indica la frequenza dei dati non elaborati (giornaliera/settimanale/mensile/trimestrale/annuale)
* Horizon: intervallo di tempo futuro della previsione
* Date: inserire i nuovi dati relativi alle serie temporali per il tempo
* Value: aggiungere i nuovi valori di dati per le serie temporali

L'output del servizio è costituito dai valori calcolati della previsione.

L'input può essere ad esempio il seguente: 

* Frequency:12
* Horizon: 12
* Date:1/15/2012;2/15/2012;3/15/2012;4/15/2012;5/15/2012;6/15/2012;7/15/2012;8/15/2012;9/15/2012;10/15/2012;11/15/2012;12/15/2012;
1/15/2013;2/15/2013;3/15/2013;4/15/2013;5/15/2013;6/15/2013;7/15/2013;8/15/2013;9/15/2013;10/15/2013;11/15/2013;12/15/2013;
1/15/2014;2/15/2014;3/15/2014;4/15/2014;5/15/2014;6/15/2014;7/15/2014;8/15/2014;9/15/2014
* Value: 3.479;3.68;3.832;3.941;3.797;3.586;3.508;3.731;3.915;3.844;3.634;3.549;3.557;3.785;3.782;3.601;3.544;3.556;3.65;3.709;3.682;3.511;
3.429;3.51;3.523;3.525;3.626;3.695;3.711;3.711;3.693;3.571;3.509
 
>Questo servizio è ospitato in Microsoft Azure Marketplace come servizio OData ed è possibile usare i metodi POST o GET per effettuare le chiamate. 

Sono disponibili molte opzioni per l'uso del servizio in modalità automatica. Per un'app di esempio, vedere [qui](http://microsoftazuremachinelearning.azurewebsites.net/etsForecasting.aspx).

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

In Azure ML è stato creato un nuovo esperimento vuoto. Sono stati caricati dati di input di esempio con uno schema dati predefinito. Un modulo "Execute R Script" è collegato allo schema dati e genera il modello di previsione ARIMA usando le funzioni 'ets' e 'forecast' di R. 


![Experiment flow][2]

####Modulo 1:
 
	# Aggiungere il file CSV con i dati nel formato mostrato di seguito 
![Data sample][3]	

####Modulo 2:
	# data input
	data <- maml.mapInputPort(1) # class: data.frame
	library(forecast)
	
	# preprocessing
	colnames(data) <- c("frequency", "horizon", "dates", "values")
	dates <- strsplit(data$dates, ";")[[1]]
	values <- strsplit(data$values, ";")[[1]]
	
	dates <- as.Date(dates, format = '%m/%d/%Y')
	values <- as.numeric(values)
	
	# fit a time-series model
	train_ts<- ts(values, frequency=data$frequency)
	fit1 <- ets(train_ts)
	train_model <- forecast(fit1, h = data$horizon)
	plot(train_model)
	
	# produce forcasting
	train_pred <- round(train_model$mean,2)
	data.forecast <- as.data.frame(t(train_pred))
	colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
	
	# data output
	maml.mapOutputPort("data.forecast");

 
##Limitazioni 

Questo è un esempio molto semplice di previsione ETS. Come si può notare nell'esempio di codice precedente, il rilevamento di errori non è implementato, il servizio presuppone che tutte le variabili siano valori continui/positivi e la frequenza deve essere un numero intero maggiore di 1. La lunghezza dei vettori di data e valore deve essere uguale. La variabile relativa alla data deve avere il formato 'mm/dd/yyyy'.

##Domande frequenti
Per le domande frequenti relative all'uso del servizio Web o alla pubblicazione nel Marketplace, vedere [qui](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img1.png
[2]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img2.png
[3]: ./media/machine-learning-r-csharp-forecasting-exponential-smoothing/ets-img3.png

<!--HONumber=46--> 

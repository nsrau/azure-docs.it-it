<properties 
	pageTitle="Binary Classifier | Azure" 
	description="Binary Classifier" 
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



#Binary Classifier

  


Si supponga che sia disponibile un set di dati e che si voglia prevedere una variabile dipendente binaria sulla base delle variabili indipendenti. La 'regressione logistica' è una tecnica statistica molto usata per queste previsioni. In questo caso la variabile dipendente è binaria o dicotomica e p è la probabilità della presenza della caratteristica di interesse. 

Un semplice scenario è costituito ad esempio da una situazione in cui il ricercatore tenta di prevedere la probabilità di accettazione dell'offerta di ammissione a un'università da parte di un potenziale studente sulla base delle informazioni disponibili (voti ottenuti durante il liceo, reddito familiare, residenza, genere). L'esito previsto corrisponde alla probabilità di accettazione dell'offerta di ammissione da parte di un potenziale studente. Questo [servizio Web]( https://datamarket.azure.com/dataset/aml_labs/log_regression) è adatto al modello di regressione logistica per i dati e fornisce come output il valore di probabilità (y) di ogni osservazione nei dati.  
  
>Benché questo servizio Web possa essere usato dagli utenti, potenzialmente tramite un'app mobile, un sito Web o anche un computer locale, ad esempio, la finalità di questo servizio Web consiste anche nel fungere da esempio dell'uso di Azure ML per creare servizi Web basati sul codice R. Con poche righe di codice R e la selezione di alcuni pulsanti in Azure ML Studio è possibile creare un esperimento con codice R e pubblicato come servizio Web. Il servizio Web può essere quindi pubblicato in Azure Marketplace e può essere usato da utenti e dispositivi in tutto il mondo, senza che l'autore del sito Web debba configurare alcuna infrastruttura.  
  

#Uso del servizio Web  
Questo servizio Web fornisce i valori previsti della variabile dipendente sulla base delle variabili indipendenti per tutte le osservazioni. Il servizio Web richiede che l'utente finale immetta i dati come una stringa in cui le righe sono separate da virgola (,) e le colonne sono separate da punto e virgola (;). Il servizio Web richiede una riga alla volta e che la prima colonna corrisponda alla variabile dipendente. Un set di dati di esempio può avere un aspetto analogo al seguente:

![Sample data][1]

Le osservazioni prive di variabile dipendente devono essere immesse come "NA" per y. L'input di dati per il set di dati precedente corrisponderà alla stringa seguente: "1;5;2,1;1;6,0;5.3;2.1,0;5;5,0;3;4,1;2;1,NA;3;4". L'output è il valore previsto per ogni riga sulla base delle variabili indipendenti. 

>Questo servizio è ospitato in Microsoft Azure Marketplace come servizio OData ed è possibile usare i metodi POST o GET per effettuare le chiamate. 

Sono disponibili molte opzioni per l'uso del servizio in modalità automatica. Per un'app di esempio, vedere [qui](http://microsoftazuremachinelearning.azurewebsites.net/BinaryClassifier.aspx ).

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


#Creazione del servizio Web  
>Questo servizio Web è stato creato tramite Azure ML. Per una versione di valutazione gratuita e per video introduttivi sulla creazione di esperimenti e sulla [pubblicazione di servizi Web](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), vedere [azure.com/ml](http://azure.com/ml). La schermata seguente mostra un esperimento per la creazione del servizio Web e codice di esempio per ogni modulo incluso nell'esperimento.

In Azure ML è stato creato un nuovo esperimento vuoto e due "Execute R Script" sono stati inseriti nell'area di lavoro. Questo servizio Web esegue un esperimento di Machine Learning di Azure con lo script R sottostante.  L'esperimento è costituito dalle due parti seguenti: definizione dello schema, modello di training e punteggio.  Il primo modulo definisce la struttura prevista del set di dati di input, in cui la prima variabile è la variabile dipendente e la variabili rimanenti sono indipendenti. Il secondo modulo è appropriato per un modello di regressione logistica generico per i dati di input.    

![Experiment flow][2]

####Modulo 1:

	#Schema definition  
	data <- data.frame(value = "1;2;3,1;5;6,0;8;9", stringsAsFactors=FALSE) maml.mapOutputPort("data");  

####Module 2:
	#GLM Modeling   
	data <- maml.mapInputPort(1) # class: data.frame  data.split <- strsplit(data[1,1], ",")[[1]] data.split <- sapply(data.split, strsplit, ";", simplify = TRUE) data.split <- sapply(data.split, strsplit, ";", simplify = TRUE) data.split <- as.data.frame(t(data.split)) data.split <- 
	data.matrix(data.split) data.split <- data.frame(data.split) model <- glm(data.split$V1 ~., family='binomial', data=data.split)  out <- 
	data.frame(predict(model,data.split, type="response")) pred1 <- as.data.frame(out) group <- array(1:nrow(pred1)) for (i in 
	1:nrow(pred1))  
	{if(as.numeric(pred1[i,])>0.5) {group[i]=1} else {group[i]=0}} pred2 <- as.data.frame(group) maml.mapOutputPort("pred2");  


#Limitazioni
Questo è un esempio molto semplice del servizio Web per la classificazione binaria. Come si può notare nell'esempio di codice precedente, il rilevamento degli errori non è implementato e il servizio presuppone che qualsiasi elemento sia una variabile binaria/continua (non sono consentite funzionalità categoriche), poiché il servizio immette come input solo valori numerici al momento della creazione del servizio Web. Il servizio, inoltre, gestisce attualmente dimensioni limitate di dati a causa della natura di richiesta/risposta della chiamata del servizio Web e del fatto che il modello viene definito ogni volta che il servizio Web viene chiamato. 

##Domande frequenti
Per le domande frequenti relative all'uso del servizio Web o alla pubblicazione nel Marketplace, vedere [qui](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-binary-classifier/binary1.png
[2]: ./media/machine-learning-r-csharp-binary-classifier/binary2.png

<!--HONumber=46--> 

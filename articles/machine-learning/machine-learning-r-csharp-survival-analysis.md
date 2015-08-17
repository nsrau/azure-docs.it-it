<properties 
	pageTitle="Analisi di sopravvivenza con Azure Machine Learning? | Microsoft Azure" 
	description="Informazioni sulla probabilità che si verifichino eventi di Analisi di sopravvivenza." 
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
	ms.date="06/24/2015" 
	ms.author="jaymathe"/>


#Analisi di sopravvivenza 

In molti scenari l'esito principale valutato riguarda il tempo mancante per un evento rilevante. In altri termini, viene posta la domanda "quando si verificherà questo evento?". Si prendano, ad esempio, in considerazione situazioni in cui i dati illustrano il tempo trascorso (giorni, anni, chilometraggio e così via) prima del verificarsi dell'evento di interesse (ricaduta di una malattia, conseguimento di un titolo di studio, guasto ai freni). Ogni istanza dei dati rappresenta un oggetto specifico (un paziente, una persona, un'automobile e così via).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Questo [servizio Web](https://datamarket.azure.com/dataset/aml_labs/survivalanalysis) risponde alla domanda "qual è la probabilità che l'evento rilevante si verifichi entro n per l'oggetto x?". Questo servizio Web fornisce un modello di analisi di sopravvivenza, in modo da permettere agli utenti di fornire dati al modello per eseguirne il training e testarlo. Il tema principale dell'esperimento consiste nel modellare la quantità di tempo trascorsa fino al verificarsi dell'evento rilevante.

>Questo servizio Web può essere utilizzato dagli utenti: potenzialmente tramite un'app mobile, un sito Web o anche in un computer locale, ad esempio. Ma lo scopo del servizio Web è anche fornire un esempio di come è possibile utilizzare Azure Machine Learning per creare servizi Web in codice R. Con poche righe di codice R e la selezione di alcuni pulsanti in Azure Machine Learning Studio è possibile creare un esperimento con codice R e pubblicarlo come servizio Web. Il servizio Web può essere quindi pubblicato in Azure Marketplace e può essere usato da utenti e dispositivi in tutto il mondo, senza che l'autore del servizio Web debba configurare alcuna infrastruttura.

##Uso del servizio Web

Lo schema di dati di input del servizio Web è mostrato nella tabella seguente. Come input sono necessari sei set di informazioni: dati di training, dati di test, ora di interesse, indice della dimensione "time", indice della dimensione "event" e tipi di variabile (continua o fattore). I dati di training sono rappresentati da una stringa, in cui le righe sono separate da virgola e le colonne sono separate da punto e virgola. Il numero di funzionalità dei dati è flessibile. Tutti gli elementi della stringa di input devono essere numerici. Nei dati di training la dimensione "time" indica il numero di unità di tempo (giorni, anni, chilometraggio e così via) trascorse dopo il punto iniziale dello studio (un paziente che si sottopone a programmi di disintossicazione, uno studente che inizia un corso di studiper PHD, l'inizio dell'uso su strada di un'automobile e così via) fino al verificarsi dell'evento rilevante (un paziente che ricomincia ad assumere droghe, lo studente che consegue il titolo di studio, un guasto ai freni di un'automobile e così via). La dimensione "event" indica se l'evento rilevante si verifica al termine dello studio. "event=1" indica che l'evento rilevante si verifica nel momento indicato dalla dimensione "time", mentre "event=0" indica che l'evento rilevante non si è ancora verificato entro il momento indicato dalla dimensione "time".

- trainingdata: una stringa di caratteri. Le righe sono separate da virgola e le colonne sono separate da punto e virgola. Ogni riga include la dimensione "time", la dimensione "event" e le variabili del predittore.
- testingdata: una riga di dati che include variabili di predittore per un oggetto specifico.
- time\_of\_interest: quantità di tempo di interesse trascorsa.
- index\_time: indice della colonna della dimensione "time" (a partire da 1).
- index\_event - indice della colonna della dimensione "event" (a partire da 1).
- variable\_types: stringa di caratteri con punto e virgola come separatore. 0 rappresenta le variabili continue e 1 variabili di fattore.


L'output corrisponde alla probabilità del verificarsi di un evento entro un tempo specifico.

>Questo servizio, come ospitato in Azure Marketplace, è un servizio OData ed è possibile utilizzare i metodi POST o GET per effettuare le chiamate.

Sono disponibili molte opzioni per l'uso del servizio in modalità automatica. Per un'app di esempio, vedere [qui](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx).

###Codice C# iniziale per l'uso del servizio Web:
	public class Input
	{
	        public string trainingdata;
	        public string testingdata;
	        public string timeofinterest;
	        public string indextime;
	        public string indexevent;
	        public string variabletypes;
	}

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { trainingdata = TextBox1.Text, testingdata = TextBox2.Text, timeofinterest = TextBox3.Text, indextime = TextBox4.Text, indexevent = TextBox5.Text, variabletypes = TextBox6.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}




L'interpretazione di questo test è la seguente. Si presupponga che l'obiettivo dei dati consista nel modellare il tempo trascorso fino a quando i pazienti che hanno seguito uno dei due programmi di disintossicazione ricominciano ad assumere droghe. L’output del servizio Web sarà: per i pazienti di età pari a 35 anni, che hanno in precedenza seguito 2 volte il programma di disintossicazione, che sono stati sottoposti a un programma di disintossicazione in una comunità e che assumevano sia eroina che cocaina, la probabilità di ritorno all'assunzione di droghe è pari al 95,64% entro il giorno 500.

##Creazione del servizio Web

>Questo servizio Web è stato creato tramite Azure Machine Learning. Per una versione di valutazione gratuita e per video introduttivi sulla creazione di esperimenti e sulla [pubblicazione di servizi Web](machine-learning-publish-a-machine-learning-web-service.md), vedere [azure.com/ml](http://azure.com/ml). La schermata seguente mostra un esperimento per la creazione del servizio Web e codice di esempio per ogni modulo incluso nell'esperimento.

In Azure Machine Learning è stato creato un nuovo esperimento vuoto e due moduli [Execute R Script][execute-r-script] sono stati inseriti nell'area di lavoro. Lo schema di dati è stato creato con un semplice [Execute R Script][execute-r-script], che definisce lo schema di dati di input per il servizio Web. Questo modulo viene quindi collegato al secondo modulo [Execute R Script][execute-r-script], che esegue la maggior parte delle operazioni, ovvero la pre-elaborazione dei dati, la compilazione dei modelli e le previsioni. Nel passaggio di pre-elaborazione dei dati, i dati di input rappresentati da una stringa lunga vengono trasformati e convertiti in un intervallo di dati. Nel passaggio di compilazione del modello, viene prima di tutto installato un pacchetto R esterno denominato "survival\_2.37-7.zip" per l'esecuzione dell'analisi di sopravvivenza. La funzione "coxph" viene eseguita dopo una serie di attività di elaborazione dei dati. Informazioni dettagliate sulla funzione "coxph" per l'analisi di sopravvivenza sono disponibili nella documentazione su R. Nel passaggio relativo alla previsione, un'istanza di test viene fornita al modello sottoposto a training con la funzione "surfit" e la curva di sopravvivenza per questa istanza di test viene prodotta come variabile "curve". Viene quindi ricavata la probabilità del tempo di interesse.

###Flusso dell'esperimento:

![flusso dell'esperimento][1]

####Modulo 1:

    #Data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1”
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"
    
    # 0 - continuous; 1 -  factor
    variable_types="0;0;1;1"

    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)

    maml.mapOutputPort("sampleInput"); #send data to output port
	
####Modulo 2:

    #Read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")

    # Preprocessing training data
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)

    # Preprocessing testing data
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))

    # Preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types

    # Necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)

    # Prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # Construct the execution string
    for (i in 1:len){
    if(i==len){
    if(variable_types[i]!=0){ c=paste(c, "factor(",v_predictors[i],")",sep="")}
     else{ c=paste(c, v_predictors[i])}
    }else{
    if(variable_types[i]!=0){c=paste(c, "factor(",v_predictors[i],") + ",sep="")}
    else{c=paste(c, v_predictors[i],"+")}
    }
    }
    f=paste("coxph(Surv(",d_time,",",d_event,") ~")
    f=paste(f,c)
    f=paste(f,", data=mydata )")

    # Fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # Based on user input, find the event occurrence probability
    position_closest=which.min(abs(prob_event$time - time_of_interest))

    if(prob_event[position_closest,"time"]==time_of_interest){# exact match
    output=prob_event[position_closest,"prob"]
    }else{# not exact match
    if(time_of_interest>max(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else if(time_of_interest<min(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else{output=(prob_event[position_closest,"prob"]+prob_event[position_closest+1,"prob"])/2}
    }

    #Pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




##Limitazioni

Questo servizio Web può accettare solo valori numerici come variabili della funzionalità (colonne). La colonna "event" può richiedere solo valore 0 o 1. La colonna “Time”deve essere un intero positivo.

##Domande frequenti
Per le domande frequenti relative all'uso del servizio Web o alla pubblicazione in Azure Marketplace, vedere [qui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=August15_HO6-->
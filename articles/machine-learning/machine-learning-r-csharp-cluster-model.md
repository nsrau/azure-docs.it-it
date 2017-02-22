---
title: (Deprecato) Modello di cluster - Azure | Documentazione Microsoft
description: (Deprecato) Modello di cluster
services: machine-learning
documentationcenter: 
author: FrancescaLazzeri
manager: jhubbard
editor: cgronlun
ms.assetid: 51b8d012-ed44-4312-920c-9c808dfd4ff6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: deprecated
ms.date: 01/06/2017
ms.author: lazzeri
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: f6ad106e769c807d1c281c8d19127eabc2048f30
ms.openlocfilehash: 84c94b83878f851c2ac281fe33afe53e02f8456c


---
# <a name="deprecated-cluster-model"></a>(Deprecato) Modello di cluster

> [!NOTE]
> Microsoft DataMarket è in fase di ritiro e questa API è stata deprecata. 
> 
> Numerose API e molti esperimenti utili di esempio sono disponibili in [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com). Per altre informazioni sulla raccolta, vedere [Condividere e scoprire risorse in Cortana Intelligence Gallery](machine-learning-gallery-how-to-use-contribute-publish.md).

In che modo si può prevedere il comportamento di gruppi di titolari di carte di credito, per ridurre gli addebiti relativi ai rischi per le autorità emittenti di carte di credito? In che modo è possibile definire gruppi di aspetti della personalità dei dipendenti per migliorarne le prestazioni lavorative? In che modo i medici possono classificare i pazienti, suddividendoli in gruppi in base alle caratteristiche delle patologie? Teoricamente, è possibile rispondere a tutte queste domande tramite l'analisi dei cluster.   

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

In pratica, l'analisi dei cluster classifica un set di osservazioni in due o più gruppi sconosciuti che si escludono a vicenda in base a una combinazione di variabili. La finalità dell'analisi dei cluster consiste nell'individuare un sistema appropriato per organizzare le osservazioni, in genere relative a persone o alle rispettive caratteristiche, suddividendole in gruppi, in modo che i membri dei gruppi condividano proprietà comuni. Questo [servizio](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model) usa la metodologia K-Means, una tecnica di clustering comunemente usata per raggruppare dati arbitrari. Questo servizio Web accetta i dati e il numero di cluster k come input e produce previsioni relative all'appartenenza delle osservazioni a uno dei gruppi  k. 

> Questo servizio Web può essere utilizzato dagli utenti: potenzialmente tramite un'app mobile, un sito Web o anche in un computer locale, ad esempio. Ma lo scopo del servizio Web è anche fornire un esempio di come è possibile utilizzare Azure Machine Learning per creare servizi Web in codice R. Con poche righe di codice R e la selezione di alcuni pulsanti in Azure Machine Learning Studio è possibile creare un esperimento con codice R e pubblicarlo come servizio Web. Il servizio Web può essere quindi pubblicato in Azure Marketplace e può essere usato da utenti e dispositivi in tutto il mondo, senza che l'autore del servizio Web debba configurare alcuna infrastruttura.  
> 
> 

## <a name="consumption-of-web-service"></a>Uso del servizio Web
Questo servizio Web raggruppa i dati in un set di k gruppi e fornisce come output l'assegnazione ai gruppi per ogni riga. Il servizio Web richiede che l'utente finale immetta i dati come una stringa in cui le righe sono separate da virgola (,) e le colonne sono separate da punto e virgola (;). Il servizio Web richiede l'immissione di una riga alla volta. Un set di dati di esempio può avere un aspetto analogo al seguente:

![Dati di esempio][1]

Si supponga che l'utente voglia separare questi dati in tre gruppi che si escludono a vicenda. L'input di dati per il set di dati precedente sarebbe analogo al seguente: valore = “10;5;2,18;1;6,7;5;5,22;3;4,12;2;1,10;3;4”; k=”3”. L'output corrisponde all'appartenenza prevista ai gruppi per ogni riga.

> Questo servizio, come ospitato in Azure Marketplace, è un servizio OData ed è possibile utilizzare i metodi POST o GET per effettuare le chiamate. 
> 
> 

Sono disponibili molte opzioni per l'uso del servizio in modalità automatica. Per un'app di esempio, vedere [qui](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx).

### <a name="starting-c-code-for-web-service-consumption"></a>Codice C# iniziale per l'uso del servizio Web:
    public class Input
    {
            public string value;
            public string k;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { value = TextBox1.Text, k = TextBox2.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();

            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }




## <a name="creation-of-web-service"></a>Creazione del servizio Web
> Questo servizio Web è stato creato tramite Azure Machine Learning. Per una versione di prova gratuita e per video introduttivi sulla creazione di esperimenti e sulla [pubblicazione di servizi Web](machine-learning-publish-a-machine-learning-web-service.md), vedere [azure.com/ml](http://azure.com/ml). La schermata seguente mostra un esperimento per la creazione del servizio Web e codice di esempio per ogni modulo incluso nell'esperimento.
> 
> 

In Azure Machine Learning è stato creato un nuovo esperimento vuoto e due moduli [Execute R Script][execute-r-script] sono stati inseriti nell'area di lavoro. Lo schema di dati è stato creato con un semplice modello [Execute R Script][execute-r-script]. In seguito, è stato collegato alla sezione del modello di cluster, creato a sua volta con un modulo [Execute R Script][execute-r-script]. Nel modulo [Execute R Script][execute-r-script] usato per il modello di cluster, il servizio Web usa quindi la funzione "k-means", incorporata in [Execute R Script][execute-r-script] di Azure Machine Learning.    

![Flusso dell'esperimento][3]

#### <a name="module-1"></a>Modulo 1:
    #Enter the input data as a string 
    mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)

    maml.mapOutputPort("mydata");     


#### <a name="module-2"></a>Modulo 2:
    # Map 1-based optional input ports to variables
    mydata <- maml.mapInputPort(1) # class: data.frame

    data.split <- strsplit(mydata[1,1], ",")[[1]]
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
    data.split <- as.data.frame(t(data.split))

    data.split <- data.matrix(data.split)
    data.split <- data.frame(data.split)

    # K-Means cluster analysis
    fit <- kmeans(data.split, mydata$k) # k-cluster solution

    # Get cluster means 
    aggregate(data.split,by=list(fit$cluster),FUN=mean)
    # Append cluster assignment
    mydatafinal <- data.frame(t(fit$cluster))
    n_col=ncol(mydatafinal)
    colnames(mydatafinal) <- paste("V",1:n_col,sep="")

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("mydatafinal");


## <a name="limitations"></a>Limitazioni
Questo è un esempio molto semplice di un servizio Web di clustering. Come si può notare nell'esempio di codice precedente, il rilevamento degli errori non è implementato e il servizio presuppone che qualsiasi elemento sia una variabile continua (non sono consentite funzionalità categoriche), poiché il servizio immette come input solo valori numerici al momento della creazione del servizio Web. Il servizio, inoltre, gestisce attualmente dimensioni limitate di dati a causa della natura di richiesta/risposta della chiamata del servizio Web e del fatto che il modello viene definito ogni volta che il servizio Web viene chiamato. 

## <a name="faq"></a>Domande frequenti
Per le domande frequenti relative all'uso del servizio Web o alla pubblicazione in Azure Marketplace, vedere [qui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/




<!--HONumber=Jan17_HO2-->



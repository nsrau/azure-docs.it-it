<properties 
	pageTitle="Come ottenere supporto tecnico dal team di sviluppo di Application Insights" 
	description="Questo articolo illustra come inviare i dettagli dei casi che richiedono uno speciale supporto da parte del team di sviluppo di Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alexbulankou" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2016" 
	ms.author="albulank"/>
	
# Come ottenere supporto tecnico dal team di sviluppo di Application Insights
	
In caso di problemi tecnici con [Visual Studio Application Insights](app-insights-overview.md), ecco come ottenere assistenza:

## 1\. Controllare i documenti

* In caso di dati mancanti, controllare: [campionamento ](app-insights-sampling.md), [quote e limitazione](app-insights-pricing.md).
* Risoluzione dei problemi: [ASP.NET](app-insights-troubleshoot-faq.md) | [Java](app-insights-java-troubleshoot.md)

## 2\. Cercare nei forum

* [Forum MSDN](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
* [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

## 3\. Piano di supporto di Azure?

In alcune situazioni è preferibile che gli sviluppatori esaminino il caso specifico.

Se si ha un [piano di supporto con Microsoft Azure](https://azure.microsoft.com/support/plans/), è possibile [aprire un ticket di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## 4\. Contattare il team di Application Insights

Se non si ha un piano di supporto, il team di sviluppo può offrire il miglior supporto possibile ai clienti di Application Insights proprio mentre prepara l'attività cardine sulla disponibilità generale. Sta per essere introdotta **una nuova opzione di supporto**: è possibile descrivere il caso inviando un modulo per commenti e suggerimenti dal portale di Azure e farsi contattare da uno sviluppatore del team di Application Insights che aiuterà a risolvere il problema.


1. Nel [portale di Application Insights](https://portal.azure.com) fare clic sullo smile nell'angolo in alto a destra:  

    ![immagine](./media/app-insights-get-dev-support/01.png)

2. Nella casella per i commenti verificare di specificare **AppInsights** come prima riga e quindi di includere le informazioni seguenti:

    ```

    AppInsights   
    ikey: <instrumentation key>   
    sdk: <SDK that you are using, including name and version>  
    email: <email address that we should use to contact you. This is the most important field>  
    issue: <please describe the problem you are having>

    ```   

    ![immagine](./media/app-insights-get-dev-support/02.png)

3. Selezionare "Sì, accetto di ricevere un messaggio di posta elettronica".

    ![immagine](./media/app-insights-get-dev-support/03.png)

A breve si verrà contattati da un tecnico del team di Application Insights. Poiché questo servizio viene fornito con il massimo impegno, per il momento non è disponibile alcun contratto di servizio formale.

<!---HONumber=AcomDC_0601_2016-->
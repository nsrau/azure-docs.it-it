<properties 
	pageTitle="Come usare l'analisi dell'utente finale" 
	description="Analisi dell'utente finale per Siti Web di Microsoft Azure." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-01-09" 
	ms.author="awills"/>

# Analisi per i siti Web di Microsoft Azure

Ci si può domandare quanti utenti abbiano visitato un sito.  E quale sia il tempo di caricamento medio delle pagine o quale browser venga usato.  Mediante l'inserimento di poche righe di script nelle pagine di un sito Web è possibile raccogliere dati sull'utilizzo del sito da parte dei clienti. 

*È possibile eseguire questa operazione anche per i siti Web non di Azure: [Monitorare l'utilizzo di applicazioni Web con Application Insights](../app-insights-web-track-usage.md).*

![End User Analytics](./media/insights-usage-analytics/Insights_ConfiguredExperience.png)

## Come configurare l'analisi dell'utente finale

1. Fare clic sulla parte del pannello **Sito Web** con il testo **Analisi dell'utente finale**
2. Nel pannello **Configurazione** selezionare e copiare l'intero script di strumentazione.  
    ![Configuration](./media/insights-usage-analytics/Insights_CopyCode.png)
3. Incollare lo script in tutte le pagine Web poco prima della chiusura del tag </head>. È consigliabile inserire lo script in tutte le pagine del sito Web. Se si usa ASP.NET, è possibile inserire lo script nella pagina master dell'applicazione.
4. Distribuire e usare l'applicazione Web. Le analisi sull'utilizzo del sito verranno visualizzate dopo circa 5-10 minuti.

## Esame dei dati

La parte Browsers session consente di effettuare analisi relative ai diversi browser e alle loro diverse versioni.

![Browsers](./media/insights-usage-analytics/Insights_Browsers.png)

Nella parte Analytics vengono visualizzate:

- Una suddivisione dei diversi tipi di dispositivi, compresi Desktop e dispositivi mobili.
- Le 5 pagine più visitate e grafici relativi al tempo di caricamento delle pagine nell'ultima settimana.  È disponibile anche il numero di sessioni e di visualizzazioni.  
    ![Top Pages](./media/insights-usage-analytics/Insights_TopPages.png)
- Vengono visualizzate anche le pagine più lente nella settimana precedente, per consentire di porre rimedio e soddisfare gli obiettivi aziendali.


<!--HONumber=46--> 
 
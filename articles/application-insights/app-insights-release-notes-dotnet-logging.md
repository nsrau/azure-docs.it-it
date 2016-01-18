<properties 
	pageTitle="Note sulla versione per gli adattatori di registrazione di Application Insights" 
	description="Gli aggiornamenti più recenti." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/21/2015" 
	ms.author="abaranch"/>
 
# Note sulla versione per gli adattatori di registrazione per .NET

Se si usa già un framework di registrazione come log4net, NLog o System.Diagnostics.Trace, è possibile acquisire tali log e inviarli ad [Application Insights](http://azure.microsoft.com/services/application-insights/), dove è possibile correlare le tracce dei log con azioni dell'utente, eccezioni e altri eventi.


#### Attività iniziali

Vedere [Log, eccezioni e diagnostica personalizzata per ASP.NET in Application Insights](app-insights-search-diagnostic-logs.md).

## Versione 1.2.6

- Correzioni di bug
- log4Net: consente di raccogliere proprietà log4net come proprietà personalizzate. UserName non è più una proprietà personalizzata, viene raccolta come telemetry.Context.User.Id. Timestamp non è più una proprietà personalizzata.
- NLog: consente di raccogliere proprietà NLog come proprietà personalizzate. SequenceID non è più una proprietà personalizzata, viene raccolta come telemetry.Sequence. Timestamp non è più una proprietà personalizzata. 

## Versione 1.2.5
- Aprire prima la versione di origine: riferimenti alle API di Application Insights versione 1.2.3 o successiva.

<!---HONumber=AcomDC_0107_2016-->
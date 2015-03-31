<properties 
	pageTitle="Analisi archiviazione" 
	description="Come gestire la concorrenza per i servizi BLOB, di accodamento, di tabelle e file" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/08/2014" 
	ms.author="tamram"/>

# Analisi archiviazione

Analisi archiviazione di Azure esegue la registrazione e fornisce le metriche dei dati per un account di archiviazione. È possibile utilizzare questi dati per tenere traccia delle richieste, analizzare le tendenze d'uso e diagnosticare i problemi relativi al proprio account di archiviazione.

Per utilizzare Analisi archiviazione, è necessario abilitarla singolarmente per ciascun servizio che si desidera monitorare. Tale operazione può essere eseguita dal [portale di gestione di Azure](https://manage.windowsazure.com/). Per i dettagli, vedere [Come monitorare un account di archiviazione](http://www.azure.com/manage/services/storage/how-to-monitor-a-storage-account/). È inoltre possibile abilitare Analisi archiviazione a livello di codice tramite l'API REST o la libreria client. Utilizzare le operazioni [Get Blob Service Properties](https://msdn.microsoft.com/it-it/library/hh452239.aspx), [Get Queue Service Properties](https://msdn.microsoft.com/it-it/library/hh452243.aspx) e [Get Table Service Properties](https://msdn.microsoft.com/it-it/library/hh452238.aspx) per abilitare Analisi archiviazione per ciascun servizio.

I dati aggregati vengono archiviati in un BLOB noto (per la registrazione) e in tabelle note (per le metriche), a cui è possibile accedere tramite le API del servizio BLOB e del servizio tabelle.

Analisi archiviazione può archiviare un massimo di 20 TB di dati. Tale limite è indipendente dal limite totale dell'account di archiviazione. Per altre informazioni sulla fatturazione e sui criteri di conservazione dei dati, vedere [Analisi archiviazione e fatturazione](https://msdn.microsoft.com/library/hh360997.aspx). Per altre informazioni sui limiti dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](https://msdn.microsoft.com/library/dn249410.aspx).

Per una guida dettagliata sull'utilizzo di Analisi archiviazione e di altri strumenti per identificare, diagnosticare e risolvere i problemi relativi ad Archiviazione di Azure, vedere [Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione di Microsoft Azure](http://azure.microsoft.com/documentation/articles/storage-monitoring-diagnosing-troubleshooting/).



<!--HONumber=47-->

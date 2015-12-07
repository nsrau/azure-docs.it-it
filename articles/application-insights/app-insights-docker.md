<properties 
	pageTitle="Monitorare un host Docker in Application Insights" 
	description="I contatori delle prestazioni, gli eventi e le eccezioni dell'host Docker possono essere visualizzati in Application Insights, insieme alla telemetria delle app nei contenitori." 
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
	ms.date="11/20/2015" 
	ms.author="awills"/>
 
# Monitorare un host Docker in Application Insights

I contatori delle prestazioni e i report eccezioni di un host [Docker](https://www.docker.com/) possono essere disegnati in Application Insights. Installare l'app [Application Insights](app-insights-overview.md) in un contenitore dell'host. Verranno visualizzati i contatori delle prestazioni complessive per l'host, oltre alle altre immagini.

Con Docker si distribuiscono le app in contenitori leggeri completi di tutte le dipendenze. Verranno eseguite su tutti i computer host che eseguono un motore Docker.

Application Insights può monitorare le prestazioni e l'attività dei contenitori e dell'host Docker.

![esempio](./media/app-insights-docker/00.png)

Naturalmente, se si è proprietari dell'origine di qualsiasi app presente nei contenitori, [installare Application Insights SDK](app-insights-java-live) in ognuna per ottenere una registrazione dettagliata delle prestazioni e la verifica dell'utilizzo.

## Creare una risorsa di Application Insights

Una risorsa di Application Insights, dove si visualizzeranno e analizzeranno i dati, è ospitata nel portale di Microsoft Azure.

1.	È necessaria una sottoscrizione a [Microsoft Azure](https://azure.com). Esiste un'opzione con pagamento in base al consumo, in cui non si paga nulla se non si usano i servizi e che permette di usare il livello gratuito di Application Insights.
2.	Accedere al [portale di Azure](https://portal.azure.com) e creare una nuova risorsa di Application Insights. Scegliere Altro come tipo di applicazione. Questa scelta influisce solo sulla selezione iniziale del grafico e non ha particolare importanza.

    ![esempio](./media/app-insights-docker/01-new.png)
3.	È sempre possibile passare alla nuova risorsa per il monitoraggio usando Sfoglia, Tutto, ma è probabile che si sia scelto di inserire anche un riquadro per l'app nel dashboard Home che viene visualizzato quando si accede al portale.

    Aprire la nuova risorsa.
4.	Aggiungere il riquadro Docker: scegliere **Modifica**, quindi **Aggiungi riquadri** e trascinare il riquadro Docker dalla raccolta. All'inizio saranno visualizzati solo grafici vuoti.

    ![esempio](./media/app-insights-docker/03.png)

5. Fare clic sull'elenco a discesa **Informazioni di base** e copiare la chiave di strumentazione, che verrà usata per indicare all'SDK dove inviare la telemetria.

Lasciare aperta la finestra del browser, a cui si tornerà a breve per esaminare la telemetria.


## Installare Application Insights nell'host
 
Ora che è disponibile una posizione in cui visualizzare la telemetria, è possibile configurare l'app che la raccoglierà e invierà. 1. Accedere all'host Docker. 2. Cercare l'immagine di Microsoft Application Insights nell'hub Docker e trascinarla nell'host. 3. Impostare la chiave di strumentazione:

    docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444

4. Eseguire l'immagine.

## Visualizzare i dati di telemetria

Tornare alla risorsa Application Insights nel portale di Azure.

In breve verranno visualizzati i dati in arrivo dal contenitore Docker, soprattutto se nel motore Docker sono in esecuzione altri contenitori.

Fare clic su uno qualsiasi dei grafici per visualizzare altri dettagli.

Di seguito sono elencate alcune delle visualizzazioni disponibili.

### Contatori delle prestazioni per host, attività per immagine


![esempio](./media/app-insights-docker/10.png)

Fare clic su un nome immagine per altri dettagli. Per personalizzare la visualizzazione, fare clic su un grafico o sull'intestazione della griglia oppure usare Aggiungi grafico.

[Altre informazioni su Esplora metriche](app-insights-metrics-explorer.md).

## Singoli eventi


![esempio](./media/app-insights-docker/12.png)

Per analizzare i singoli eventi, fare clic su [Cerca](app-insights-diagnostic-search.md). Eseguire una ricerca e applicare filtri per trovare gli eventi desiderati. Per ottenere altri dettagli, fare clic su qualsiasi evento.
 
## Eccezioni per nome del contenitore
 

![esempio](./media/app-insights-docker/14.png)



## Domande e risposte

*Quali sono i vantaggi di Application Insights rispetto a Docker?*

* Suddivisione dettagliata dei contatori delle prestazioni in base al contenitore e all'immagine.
* Integrazione dei dati dell'app e dei contenitori in un unico dashboard.
* [Esportazione della telemetria](app-insights-export-telemetry.md) in un database, in Power BI o in un altro dashboard per ulteriori analisi.

*Come ottenere la telemetria direttamente dall'app?*

* Installare Application Insights SDK nell'app. Per informazioni, vedere [App Web Java](app-insights-java-get-started.md) e [App Web Windows](app-insights-asp-net.md).

<!---HONumber=AcomDC_1125_2015-->
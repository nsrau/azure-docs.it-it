---
title: Integrazione di SCOM con Application Insights | Documentazione Microsoft
description: Gli utenti di SCOM possono monitorare le prestazioni e diagnosticare i problemi con Application Insights. Dashboard completi, avvisi intelligenti, potenti strumenti di diagnostica e query di analisi.
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 606e9d03-c0e6-4a77-80e8-61b75efacde0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/12/2016
ms.author: mbullwin
ms.openlocfilehash: 35ea37b751909e14e616a965462b832e4e51bae0
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="application-performance-monitoring-using-application-insights-for-scom"></a>Application Performance Monitoring con Application Insights per SCOM
Se si usa System Center Operations Manager (SCOM) per gestire i server, è possibile monitorare le prestazioni e diagnosticare problemi di prestazioni con [Azure Application Insights](app-insights-asp-net.md). Application Insights monitora le richieste in ingresso dell'applicazione Web, le chiamate REST e SQL in uscita, le eccezioni e le tracce dei log. Fornisce i dashboard con grafici delle metriche e avvisi intelligenti, nonché funzionalità di ricerca diagnostica avanzate e query analitiche di questi dati di telemetria. 

È possibile attivare il monitoraggio di Application Insights tramite un Management Pack di SCOM.

## <a name="before-you-start"></a>Prima di iniziare
Si presuppone quanto segue:

* Si ha familiarità con SCOM e si usa SCOM 2012 R2 o 2016 per gestire i server Web IIS.
* È già stata installata nei server un'applicazione Web che si vuole monitorare con Application Insights.
* La versione de framework applicazione è .NET 4.5 o versione successiva.
* Si ha accesso a una sottoscrizione in [Microsoft Azure](https://azure.com) e si può accedere al [portale di Azure](https://portal.azure.com). Se l'organizzazione ha una sottoscrizione, è possibile aggiungervi il proprio account Microsoft.

Il team di sviluppo può incorporare [Application Insights SDK](app-insights-asp-net.md) nell'app Web. La strumentazione in fase di compilazione offre una maggiore flessibilità per la scrittura di dati di telemetria personalizzati. Se tuttavia questo aspetto non è significativo, è possibile seguire i passaggi descritti di seguito con o senza l'SDK incorporato.

## <a name="one-time-install-application-insights-management-pack"></a>(Una sola volta) Installare Management Pack per Application Insights
Nel computer in cui è in esecuzione Operations Manager seguire questa procedura:

1. Disinstallare eventuali versioni precedenti del management pack:
   1. In Operations Manager aprire Amministrazione, Management Pack. 
   2. Eliminare la versione precedente.
2. Scaricare e installare il management pack dal catalogo.
3. Riavviare Operations Manager.

## <a name="create-a-management-pack"></a>Creare un management pack
1. In Operations Manager aprire **Creazione**, **.NET... con Application Insights**, **Aggiunta guidata monitoraggio** e scegliere nuovamente **.NET... con Application Insights**.
   
    ![](./media/app-insights-scom/020.png)
2. Assegnare un nome alla configurazione in base all'app. È necessario instrumentare un'applicazione alla volta.
   
    ![](./media/app-insights-scom/030.png)
3. Nella stessa pagina della procedura guidata, creare un nuovo management pack o selezionarne uno creato in precedenza per Application Insights.
   
     [Management Pack](https://technet.microsoft.com/library/cc974491.aspx) per Application Insights è un modello da cui si crea un'istanza. È possibile riutilizzare la stessa istanza in un secondo momento.

    ![Nella scheda Proprietà generali, digitare il nome dell'app. Fare clic su Nuovo e digitare un nome per un management pack. Fare clic su OK e quindi su Avanti.](./media/app-insights-scom/040.png)

1. Scegliere un'app da monitorare. La funzionalità di ricerca esegue la ricerca nelle app installate nei server.
   
    ![Nella scheda delle app da monitorare fare clic su Aggiungi, digitare parte del nome dell'app, fare clic su Cerca, scegliere l'app e quindi Aggiungi, OK.](./media/app-insights-scom/050.png)
   
    Il campo facoltativo Ambito monitoraggio può essere usato per specificare un subset di server, se non si vuole monitorare l'app in tutti i server.
2. Nella pagina successiva della procedura guidata, è necessario fornire prima di tutto le credenziali per accedere a Microsoft Azure.
   
    In questa pagina scegliere la risorsa di Application Insights in cui analizzare e visualizzare i dati di telemetria. 
   
   * Se l'applicazione è stata configurata per Application Insights durante lo sviluppo, selezionare la relativa risorsa esistente.
   * In caso contrario, creare una nuova risorsa denominata in base all'app. Se sono presenti altre app componenti dello stesso sistema, inserirle nello stesso gruppo di risorse, per gestire più facilmente l'accesso ai dati di telemetria.
     
     Queste impostazioni possono essere modificate in seguito.
     
     ![Nella scheda Impostazioni di Application Insights fare clic su 'accedi' e fornire le credenziali dell'account Microsoft per Azure. Quindi scegliere una sottoscrizione, gruppo di risorse e una risorse.](./media/app-insights-scom/060.png)
3. Completare la procedura guidata.
   
    ![Click Create](./media/app-insights-scom/070.png)

Ripetere questa procedura per ogni app da monitorare.

Se è necessario modificare le impostazioni in un secondo momento, riaprire le proprietà del monitoraggio dalla finestra di creazione.

![Nella finestra di creazione selezionare Monitoraggio delle prestazioni delle applicazioni .NET con Application Insights, selezionare la funzionalità di monitoraggio e fare clic su Proprietà.](./media/app-insights-scom/080.png)

## <a name="verify-monitoring"></a>Verificare il monitoraggio
La funzionalità di monitoraggio installata cerca l'app in ogni server. Nel server in cui trova l'app configura Application Insights Status Monitor per monitorare l'app. Se necessario, prima installa Status Monitor nel server.

È possibile verificare quali istanze dell'app sono state trovate:

![In Monitoraggio aprire Application Insights](./media/app-insights-scom/100.png)

## <a name="view-telemetry-in-application-insights"></a>Visualizzare i dati di telemetria in Application Insights
Nel [portale di Azure](https://portal.azure.com)individuare la risorsa per l'app. Nell'app è possibile [visualizzare grafici che mostrano i dati di telemetria](app-insights-dashboards.md) . Se non vengono visualizzati nella pagina principale, fare clic su Flusso metriche attive.

## <a name="next-steps"></a>Passaggi successivi
* [Configurare un dashboard](app-insights-dashboards.md) per riunire i grafici più importanti relativi al monitoraggio di questa e altre app.
* [Informazioni sulle metriche](app-insights-metrics-explorer.md)
* [Impostare gli avvisi in Application Insights](app-insights-alerts.md)
* [Rilevare, valutare e diagnosticare con Application Insights](app-insights-detect-triage-diagnose.md)
* [Query di Analisi avanzate](app-insights-analytics.md)
* [Test Web di disponibilità](app-insights-monitor-web-app-availability.md)


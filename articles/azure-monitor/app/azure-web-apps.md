---
title: Monitorare le prestazioni dei servizi app di Azure | Microsoft Docs
description: Monitoraggio delle prestazioni applicative per i servizi app di Azure. Tempo di caricamento e risposta del grafico, informazioni sulle dipendenze e impostazione di avvisi sulle prestazioni.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: mbullwin
ms.openlocfilehash: bde73e9ee87ab9165c1d2dd720377d2f9c8771cb
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2019
ms.locfileid: "55565956"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorare le prestazioni di Servizio app di Azure
Nel [portale di Azure](https://portal.azure.com) è possibile configurare il monitoraggio delle prestazioni applicative per le app Web, i back-end per dispositivi mobili e le app per le API in [Servizio app di Azure](../../app-service/overview.md). [Application Insights di Azure](../../azure-monitor/app/app-insights-overview.md) consente di instrumentare l'app per inviare dati di telemetria sulle proprie attività al servizio Application Insights, in cui verranno archiviati e analizzati. Sarà quindi possibile usare grafici delle metriche e strumenti di ricerca per diagnosticare i problemi, migliorare le prestazioni e valutare l'utilizzo.

## <a name="run-time-or-build-time"></a>Fase di esecuzione o fase di compilazione
È possibile configurare il monitoraggio instrumentando l'app in uno dei due modi seguenti:

* **Fase di esecuzione**: è possibile selezionare un'estensione di monitoraggio delle prestazioni quando il servizio app è già attivo. Non è necessario ricompilarla o reinstallarla. Si ottiene un set di pacchetti standard che monitorano i tempi di risposta, le percentuali di riuscita, le eccezioni, le dipendenze e così via. 
* **Fase di compilazione** : è possibile installare un pacchetto nell'app durante lo sviluppo. Questa opzione è più versatile. Oltre agli stessi pacchetti standard, è possibile scrivere codice per personalizzare la telemetria o per inviare dati di telemetria personalizzati. È possibile registrare attività specifiche o registrare eventi in base alla semantica del dominio dell'app. 

## <a name="run-time-instrumentation-with-application-insights"></a>Strumentazione della fase di esecuzione con Application Insights
Se si sta già eseguendo un servizio app in Azure, vengono già visualizzati alcuni dati di monitoraggio: la frequenza di esecuzione con errori e la frequenza delle richieste. Aggiungere Application Insights per usufruire di maggiori funzionalità, come i tempi di risposta, il monitoraggio delle chiamate alle dipendenze, il rilevamento intelligente e l'avanzato linguaggio di query di Log Analytics. 

1. **Selezionare Application Insights** nel pannello di controllo di Azure per il servizio app.

    ![In Impostazioni scegliere Application Insights](./media/azure-web-apps/settings-app-insights.png)

   * Scegliere di creare una nuova risorsa, a meno che non sia già stata impostata una risorsa di Application Insights per l'applicazione. 

    > [!NOTE]
    > Quando si fa clic su **OK** per creare la nuova risorsa, viene visualizzata la richiesta **Applica impostazioni di monitoraggio**. Selezionando **Continua** si collegherà la nuova risorsa di Application Insights al servizio app; ciò consente anche di **attivare un riavvio del servizio app**. 

    ![Instrumentazione dell'App Web](./media/azure-web-apps/create-resource.png)

2. Dopo aver specificato la risorsa da usare, è possibile scegliere il modo in cui Application Insights deve raccogliere i dati per ogni piattaforma per l'applicazione. Il monitoraggio delle app ASP.NET è attivo per impostazione predefinita con due livelli di raccolta diversi.

    ![Scegliere le opzioni per ogni piattaforma](./media/azure-web-apps/choose-options-new.png)

    * Il livello di raccolta **Di base** di .NET offre funzionalità essenziali di monitoraggio delle prestazioni applicative per singole istanze.
    
    * Il livello di raccolta **Consigliato** di .NET:
        * Aggiunge le tendenze di utilizzo della CPU, della memoria e delle operazioni di I/O.
        * Mette in correlazione i microservizi attraverso i limiti di richiesta/dipendenza.
        * Raccoglie le tendenze di utilizzo e consente la correlazione dei risultati di disponibilità con le transazioni.
        * Raccoglie le eccezioni non gestite dal processo host.
        * Migliora la precisione delle metriche di monitoraggio delle prestazioni applicative in condizioni di carico quando viene usato il campionamento.
    
    .NET Core offre un livello di raccolta **Consigliato** o Disabilitato per .NET Core 2.0 e 2.1.

3. **Instrumentare il servizio app** dopo l'installazione di Application Insights.

   **Abilitare il monitoraggio lato client** per la visualizzazione delle pagine e la telemetria utente.

    Questo monitoraggio è abilitato per impostazione predefinita per le app .NET Core con livello di raccolta **Consigliato**, indipendentemente dalla presenza dell'impostazione dell'app APPINSIGHTS_JAVASCRIPT_ENABLED. Il supporto granulare basato su interfaccia utente per la disabilitazione del monitoraggio lato client non è attualmente disponibile per .NET Core.
    
   * Selezionare Impostazioni > Impostazioni applicazione
   * In Impostazioni app aggiungere una nuova coppia chiave-valore:

    Chiave: `APPINSIGHTS_JAVASCRIPT_ENABLED`

    Valore: `true`
   * Salvare le impostazioni scegliendo **Salva** e quindi fare clic su **Riavvia** per riavviare l'app.

4. Esplorare i dati di monitoraggio dell'app selezionando **Impostazioni** > **Application Insights** > **Visualizza altro in Application Insights**.

In seguito, se necessario, sarà possibile creare l'app con Application Insights.

*Come è possibile rimuovere Application Insights o passare all'invio di un'altra risorsa?*

* In Azure aprire il pannello di controllo per l'app Web e in Impostazioni aprire **Application Insights**. È possibile disattivare Application Insights facendo clic su **Disabilita** in alto oppure selezionare una nuova risorsa nella sezione **Change your resource** (Modifica risorsa).

## <a name="build-the-app-with-application-insights"></a>Compilare l'app con Application Insights
Application Insights può fornire ulteriori dati di telemetria installando un SDK nell'applicazione. In particolare, è possibile raccogliere i log di traccia, [scrivere dati di telemetria personalizzati](../../azure-monitor/app/api-custom-events-metrics.md) e ottenere report di eccezione più dettagliati.

1. **In Visual Studio** 2013 Update 2 o versione successiva configurare Application Insights per il progetto.

    Fare clic con il pulsante destro del mouse sul progetto Web e scegliere **Aggiungi > Application Insights** o **Progetto** > **Application Insights** > **Configura Application Insights**.

    ![Fare clic con il pulsante destro del mouse sul progetto Web e scegliere Aggiungi o Configura Application Insights](./media/azure-web-apps/03-add.png)

    Se viene chiesto di effettuare l'accesso, usare le credenziali dell'account Azure.

    L'operazione ha due effetti:

   1. Crea una risorsa di Application Insights in Azure, in cui vengono archiviati, analizzati e visualizzati i dati di telemetria.
   2. Se non è già presente, aggiunge il pacchetto NuGet di Application Insights al codice e lo configura per l'invio della telemetria alla risorsa di Azure.
2. **Testare i dati di telemetria** eseguendo l'app nel computer di sviluppo (F5).
3. **Pubblicare l'app** in Azure nel modo consueto. 

*Come è possibile passare all'invio a un'altra risorsa di Application Insights?*

* In Visual Studio fare clic con il pulsante destro del mouse sul progetto, scegliere **Configura Application Insights** e scegliere la risorsa desiderata. Sarà possibile creare una nuova risorsa. Ricompilare e ridistribuire.

## <a name="more-telemetry"></a>Altri dati di telemetria

* [Dati di caricamento della pagina Web](../../azure-monitor/app/javascript.md)
* [Telemetria personalizzata](../../azure-monitor/app/api-custom-events-metrics.md)

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="appinsightsjavascriptenabled-causes-incomplete-html-response-in-net-core-web-applications"></a>APPINSIGHTS_JAVASCRIPT_ENABLED causa risposte HTML incomplete in applicazioni Web NET CORE.

L'abilitazione di Javascript tramite i Servizi app può causare il troncamento delle risposte HTML.

* Soluzione alternativa n. 1: impostare l'impostazione applicazione APPINSIGHTS_JAVASCRIPT_ENABLED su false o rimuoverla completamente e riavviare
* Soluzione alternativa n. 2: aggiungere l'SDK tramite il codice e rimuovere l'estensione (Profiler e Snapshot Debugger non con questa configurazione)

Microsoft sta verificando questo problema [qui](https://github.com/Microsoft/ApplicationInsights-Home/issues/277)

Attualmente per .NET Core **non è supportato** quanto segue:

* Distribuzione autonoma.
* App destinate a .NET Framework.
* Applicazioni .NET Core 2.2.

> [!NOTE]
> È previsto il supporto solo per .NET Core 2.0 e .NET Core 2.1. Quando verrà aggiunto il supporto per .NET Core 2.2, in questo articolo verranno introdotti gli opportuni aggiornamenti.

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire il profiler sull'app live](../../azure-monitor/app/profiler.md).
* [Funzioni di Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample): monitorare Funzioni di Azure con Application Insights
* [Abilitare l'invio dei dati di diagnostica di Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) ad Application Insights.
* [Monitorare le metriche di integrità del servizio](../../azure-monitor/platform/data-collection.md) per assicurarsi che il servizio sia disponibile e reattivo.
* [Ricevere notifiche di avviso](../../azure-monitor/platform/alerts-overview.md) ogni volta che si verificano eventi operativi o le metriche superano una soglia.
* Usare [Analisi dell'utilizzo per applicazioni Web con Application Insights](../../azure-monitor/app/javascript.md) per ottenere i dati di telemetria dei client dai browser che visitano una pagina Web.
* [Monitorare la disponibilità e la velocità di risposta dei siti Web](../../azure-monitor/app/monitor-web-app-availability.md) per ricevere un avviso se il sito è inattivo.


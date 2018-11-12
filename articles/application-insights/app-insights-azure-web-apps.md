---
title: Monitorare le prestazioni dell'app Web di Azure | Microsoft Docs
description: Monitoraggio delle prestazioni applicative per le app Web di Azure. Tempo di caricamento e risposta del grafico, informazioni sulle dipendenze e impostazione di avvisi sulle prestazioni.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: mbullwin
ms.openlocfilehash: e88d38e6d95459cc1c41adecee6b7ece659eac99
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957243"
---
# <a name="monitor-azure-web-app-performance"></a>Monitoraggio delle prestazioni dell'applicazione web di Azure
Nel [portale di Azure](https://portal.azure.com) è possibile configurare il monitoraggio delle prestazioni applicative per le [App Web di Azure](../app-service/app-service-web-overview.md). [Application Insights di Azure](app-insights-overview.md) consente di instrumentare l'app per inviare dati di telemetria sulle proprie attività al servizio Application Insights, in cui verranno archiviati e analizzati. Sarà quindi possibile usare grafici delle metriche e strumenti di ricerca per diagnosticare i problemi, migliorare le prestazioni e valutare l'utilizzo.

## <a name="run-time-or-build-time"></a>Fase di esecuzione o fase di compilazione
È possibile configurare il monitoraggio instrumentando l'app in uno dei due modi seguenti:

* **Fase di esecuzione** : è possibile selezionare un'estensione di monitoraggio delle prestazioni quando l'app Web è già attiva. Non è necessario ricompilarla o reinstallarla. Si ottiene un set di pacchetti standard che monitorano i tempi di risposta, le percentuali di riuscita, le eccezioni, le dipendenze e così via. 
* **Fase di compilazione** : è possibile installare un pacchetto nell'app durante lo sviluppo. Questa opzione è più versatile. Oltre agli stessi pacchetti standard, è possibile scrivere codice per personalizzare la telemetria o per inviare dati di telemetria personalizzati. È possibile registrare attività specifiche o registrare eventi in base alla semantica del dominio dell'app. 

## <a name="run-time-instrumentation-with-application-insights"></a>Strumentazione della fase di esecuzione con Application Insights
Se si esegue già un'App Web in Azure, vengono già visualizzati alcuni dati di monitoraggio, cioè la frequenza di esecuzione con errori e la frequenza delle richieste. Aggiungere Application Insights per usufruire di maggiori funzionalità, come i tempi di risposta, il monitoraggio delle chiamate alle dipendenze, il rilevamento intelligente e l'avanzato linguaggio di query di Log Analytics. 

1. **Selezionare Application Insights** nel pannello di controllo di Azure per l'App Web.

    ![In Impostazioni scegliere Application Insights](./media/app-insights-azure-web-apps/settings-app-insights.png)

   * Scegliere di creare una nuova risorsa, a meno che non sia già stata impostata una risorsa di Application Insights per l'applicazione. 

    > [!NOTE]
    > Quando si fa clic su **OK** per creare la nuova risorsa, viene visualizzata la richiesta **Applica impostazioni di monitoraggio**. Selezionando **Continua** si collegherà la nuova risorsa di Application Insights all'app Web. In questo modo viene anche **attivato un riavvio dell'app Web**. 

    ![Instrumentazione dell'App Web](./media/app-insights-azure-web-apps/create-resource.png)

2. Dopo aver creato la risorsa, selezionare **Controlla e aggiorna l'estensione Application Insights per l'app Web** per confermare l'aggiornamento dell'estensione.

     ![Controllare e aggiornare l'estensione del sito](./media/app-insights-azure-web-apps/check-and-update.png)

3. **Instrumentare l'App Web** dopo l'installazione di Application Insights.

   **Abilitare il monitoraggio lato client** per la visualizzazione delle pagine e la telemetria utente.

   * Selezionare Impostazioni > Impostazioni applicazione
   * In Impostazioni app aggiungere una nuova coppia chiave-valore:

    Chiave: `APPINSIGHTS_JAVASCRIPT_ENABLED`

    Valore: `true`
   * Salvare le impostazioni scegliendo **Salva** e quindi fare clic su **Riavvia** per riavviare l'app.
4. Esplorare i dati di monitoraggio dell'app selezionando **Impostazioni** > **Application Insights** > **Visualizza altro in Application Insights**.

In seguito, se necessario, sarà possibile creare l'app con Application Insights.

*Come è possibile rimuovere Application Insights o passare all'invio di un'altra risorsa?*

* In Azure aprire il pannello di controllo dell'App Web e aprire **Estensioni** in Strumenti di sviluppo. Eliminare l'estensione di Application Insights. Quindi in Monitoraggio scegliere Application Insights e creare o selezionare la risorsa desiderata.

## <a name="build-the-app-with-application-insights"></a>Compilare l'app con Application Insights
Application Insights può fornire ulteriori dati di telemetria installando un SDK nell'applicazione. In particolare, è possibile raccogliere i log di traccia, [scrivere dati di telemetria personalizzati](app-insights-api-custom-events-metrics.md) e ottenere report di eccezione più dettagliati.

1. **In Visual Studio** 2013 Update 2 o versione successiva configurare Application Insights per il progetto.

    Fare clic con il pulsante destro del mouse sul progetto Web e scegliere **Aggiungi > Application Insights** o **Progetto** > **Application Insights** > **Configura Application Insights**.

    ![Fare clic con il pulsante destro del mouse sul progetto Web e scegliere Aggiungi o Configura Application Insights](./media/app-insights-azure-web-apps/03-add.png)

    Se viene chiesto di effettuare l'accesso, usare le credenziali dell'account Azure.

    L'operazione ha due effetti:

   1. Crea una risorsa di Application Insights in Azure, in cui vengono archiviati, analizzati e visualizzati i dati di telemetria.
   2. Se non è già presente, aggiunge il pacchetto NuGet di Application Insights al codice e lo configura per l'invio della telemetria alla risorsa di Azure.
2. **Testare i dati di telemetria** eseguendo l'app nel computer di sviluppo (F5).
3. **Pubblicare l'app** in Azure nel modo consueto. 

*Come è possibile passare all'invio a un'altra risorsa di Application Insights?*

* In Visual Studio fare clic con il pulsante destro del mouse sul progetto, scegliere **Configura Application Insights** e scegliere la risorsa desiderata. Sarà possibile creare una nuova risorsa. Ricompilare e ridistribuire.

## <a name="more-telemetry"></a>Altri dati di telemetria

* [Dati di caricamento della pagina Web](app-insights-javascript.md)
* [Telemetria personalizzata](app-insights-api-custom-events-metrics.md)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire il profiler sull'app live](app-insights-profiler.md).
* [Funzioni di Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample): monitorare Funzioni di Azure con Application Insights
* [Abilitare l'invio dei dati di diagnostica di Azure](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md) ad Application Insights.
* [Monitorare le metriche di integrità del servizio](../monitoring/monitoring-data-collection.md) per assicurarsi che il servizio sia disponibile e reattivo.
* [Ricevere notifiche di avviso](../monitoring-and-diagnostics/monitoring-overview-alerts.md) ogni volta che si verificano eventi operativi o le metriche superano una soglia.
* Usare [Analisi dell'utilizzo per applicazioni Web con Application Insights](app-insights-javascript.md) per ottenere i dati di telemetria dei client dai browser che visitano una pagina Web.
* [Monitorare la disponibilità e la velocità di risposta dei siti Web](app-insights-monitor-web-app-availability.md) per ricevere un avviso se il sito è inattivo.


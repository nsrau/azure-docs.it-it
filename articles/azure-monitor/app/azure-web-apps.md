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
ms.date: 02/26/2019
ms.author: mbullwin
ms.openlocfilehash: 92a7c1a45655f8804aa1f81b1a77ebf7cd5197e8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122166"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorare le prestazioni del Servizio app di Azure
Nel [portale di Azure](https://portal.azure.com) è possibile configurare il monitoraggio delle prestazioni applicative per le app Web, i back-end per dispositivi mobili e le app per le API in [Servizio app di Azure](../../app-service/overview.md). [Application Insights di Azure](../../azure-monitor/app/app-insights-overview.md) consente di instrumentare l'app per inviare dati di telemetria sulle proprie attività al servizio Application Insights, in cui verranno archiviati e analizzati. Sarà quindi possibile usare grafici delle metriche e strumenti di ricerca per diagnosticare i problemi, migliorare le prestazioni e valutare l'utilizzo.

## <a name="runtime-or-build-time"></a>Fase di runtime o di compilazione
È possibile configurare il monitoraggio instrumentando l'app in uno dei due modi seguenti:

* **Runtime** -è possibile selezionare un'estensione di monitoraggio quando il servizio app è già attivo delle prestazioni. Non è necessario ricompilarla o reinstallarla. Si ottiene un set di pacchetti standard che monitorano i tempi di risposta, le percentuali di riuscita, le eccezioni, le dipendenze e così via.

* **Fase di compilazione** : è possibile installare un pacchetto nell'app durante lo sviluppo. Questa opzione è più versatile. Oltre agli stessi pacchetti standard, è possibile scrivere codice per personalizzare la telemetria o per inviare dati di telemetria personalizzati. È possibile registrare attività specifiche o registrare eventi in base alla semantica del dominio dell'app. Questo inoltre offre la possibilità di testare la versione più recente del SDK di Application Insights come è possibile scegliere di valutazione beta SDK, mentre il monitoraggio di runtime è limitato alla versione stabile più recente.

## <a name="runtime-instrumentation-with-application-insights"></a>Strumentazione di runtime con Application Insights
Se si sta eseguendo attualmente un servizio app in Azure, già visualizzato alcune attività di monitoraggio: velocità di richiesta e di errore per impostazione predefinita. Aggiungere Application Insights per ottenere più dati, ad esempio tempi di risposta, monitoraggio delle chiamate alle dipendenze, rilevamento intelligente e l'accesso al potente linguaggio di query Kusto. 

1. **Selezionare Application Insights** nel pannello di controllo di Azure per il servizio app.

    ![In Impostazioni scegliere Application Insights](./media/azure-web-apps/settings-app-insights.png)

   * Scegliere di creare una nuova risorsa, a meno che non sia già stata impostata una risorsa di Application Insights per l'applicazione. 

     > [!NOTE]
     > Quando si fa clic su **OK** per creare la nuova risorsa, viene visualizzata la richiesta **Applica impostazioni di monitoraggio**. Selezionando **Continua** si collegherà la nuova risorsa di Application Insights al servizio app; ciò consente anche di **attivare un riavvio del servizio app**. 

     ![Instrumentazione dell'App Web](./media/azure-web-apps/create-resource.png)

2. Dopo aver specificato la risorsa da usare, è possibile scegliere il modo in cui Application Insights deve raccogliere i dati per ogni piattaforma per l'applicazione. Monitoraggio delle app ASP.NET è attivato per impostazione predefinita con due livelli diversi della raccolta.

    ![Scegliere le opzioni per ogni piattaforma](./media/azure-web-apps/choose-options-new.png)

   * Il livello di raccolta **Di base** di .NET offre funzionalità essenziali di monitoraggio delle prestazioni applicative per singole istanze.
    
   * Il livello di raccolta **Consigliato** di .NET:
       * Aggiunge le tendenze di utilizzo della CPU, della memoria e delle operazioni di I/O.
       * Mette in correlazione i microservizi attraverso i limiti di richiesta/dipendenza.
       * Raccoglie le tendenze di utilizzo e consente la correlazione dei risultati di disponibilità con le transazioni.
       * Raccoglie le eccezioni non gestite dal processo host.
       * Migliora la precisione delle metriche di monitoraggio delle prestazioni applicative in condizioni di carico quando viene usato il campionamento.
    
     .NET core offra **consigliato collection** oppure **disabilitato** per .NET Core 2.0 e 2.1.

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

## <a name="automate-monitoring"></a>Automatizzare il monitoraggio

Per abilitare la raccolta di dati di telemetria con Application Insights è necessario impostare solo le impostazioni dell'applicazione:

   ![Impostazioni applicazione del servizio App con le impostazioni di Application Insights disponibili](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definizioni delle impostazioni dell'applicazione

|Nome impostazione app |  Definizione | Valore |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Estensione del principale, che controlla il monitoraggio di runtime. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Le funzionalità essenziali, solo in modalità predefinita sono abilitate per assicurare prestazioni ottimali. | `default` o `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Controlla se il motore di riscrittura di file binario `InstrumentationEngine` verrà attivata. Questa impostazione comporta implicazioni sulla prestazione e influisce sul tempo di avvio/avvio a freddo. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Controlli se il testo della tabella SQL e Azure verranno acquisiti con le chiamate di dipendenza. Avviso relativo alle prestazioni: è necessario il `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Impostazioni dell'applicazione del servizio App con Azure Resource Manager

Possono essere gestite e configurate con le impostazioni dell'applicazione per i servizi App [modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Questo metodo può essere utilizzato durante la distribuzione di nuove risorse del servizio App con automazione di Azure Resource Manager, o per modificare le impostazioni delle risorse esistenti.

Di seguito è riportata la struttura di base delle impostazioni dell'applicazione JSON per un servizio app:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]

```

Per un esempio di un Azure Resource Manager modello con le impostazioni dell'applicazione configurata per Application Insights ciò [modello](https://github.com/Andrew-MSFT/BasicImageGallery) può essere utile, in particolare la sezione a partire dal [riga 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatizzare la creazione di una risorsa di Application Insights e un collegamento al servizio App appena creato.

Per creare un modello Azure Resource Manager con tutte le impostazioni di Application Insights predefinite configurate, iniziare il processo come se si prevede di creare una nuova App Web con Application Insights abilitate.

Selezionare **opzioni di automazione**

   ![Dal menu la creazione di app web di servizio App](./media/azure-web-apps/create-web-app.png)

Viene generato l'ultimo modello di Azure Resource Manager con tutte le impostazioni necessarie configurate.

  ![Modello di app web di servizio App](./media/azure-web-apps/arm-template.png)

> [!NOTE]
> Il modello genererà le impostazioni dell'applicazione in modalità "default". Questa modalità è prestazioni ottimizzate, anche se è possibile modificare il modello per attivare qualunque funzionalità si preferisce.

## <a name="more-telemetry"></a>Altri dati di telemetria

* [Dati di caricamento della pagina Web](../../azure-monitor/app/javascript.md)
* [Telemetria personalizzata](../../azure-monitor/app/api-custom-events-metrics.md)

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="do-i-still-need-to-go-to-extensions---add---application-insights-extension-for-new-app-service-apps"></a>È comunque necessario passare a estensioni: Add - estensione Application Insights per la nuova App del servizio App?

No, è necessario non è più aggiungere l'estensione manualmente. Abilitazione di Application Insights usando il pannello impostazioni aggiungerà tutte le impostazioni dell'applicazione necessari per abilitare il monitoraggio. Ciò è ora possibile, perché i file precedentemente aggiunti dall'estensione sono ora [preinstallato](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) come parte dell'immagine del servizio App. I file si trovano in `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent`.

### <a name="if-runtime-and-build-time-monitoring-are-both-enabled-do-i-end-up-with-duplicate-data"></a>Se il runtime e il monitoraggio tempo di compilazione sono entrambi abilitati decide di duplicazione dei dati?

No, per impostazione predefinita se viene rilevato un monitoraggio tempo di compilazione tramite l'estensione di monitoraggio runtime interromperà l'invio di dati e verrà rispettata solo la fase di compilazione configurazione del monitoraggio. La determinazione della possibilità di disabilitare il monitoraggio di runtime è basata sul rilevamento di uno di questi tre file:

* Microsoft.ApplicationInsights dll
* Microsoft.ASP.NET.TelemetryCorrelation dll
* Dll di diagnosticsource

È importante tenere presente che in molte versioni di Visual Studio, alcuni o tutti questi file vengono aggiunti per impostazione predefinita per i file di modello di Visual Studio di ASP.NET Core e ASP.NET. Se il progetto è stato creato in base a uno dei modelli, anche se non è stata abilitata in modo esplicito Application Insights, la presenza della dipendenza file impedirebbe di monitoraggio in fase di attivazione.

### <a name="appinsightsjavascriptenabled-causes-incomplete-html-response-in-net-core-web-applications"></a>APPINSIGHTS_JAVASCRIPT_ENABLED causa risposte HTML incomplete in applicazioni Web NET CORE.

L'abilitazione di Javascript tramite i Servizi app può causare il troncamento delle risposte HTML.

* Soluzione alternativa n. 1: impostare l'impostazione applicazione APPINSIGHTS_JAVASCRIPT_ENABLED su false o rimuoverla completamente e riavviare
* Soluzione alternativa 2: aggiungere il sdk tramite il codice e rimuovere l'estensione (Profiler e Snapshot debugger non funzionerà con questa configurazione)

Per tenere traccia del problema, passare a [estensione di Azure causa incompleto risposta HTML](https://github.com/Microsoft/ApplicationInsights-Home/issues/277).

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
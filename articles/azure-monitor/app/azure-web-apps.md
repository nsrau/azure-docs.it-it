---
title: Monitorare le prestazioni dei servizi app di Azure | Microsoft Docs
description: Monitoraggio delle prestazioni applicative per i servizi app di Azure. Grafico di carico e il tempo di risposta, informazioni sulle dipendenze e impostare avvisi sulle prestazioni.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mbullwin
ms.openlocfilehash: ec5b3572cbf74bad9b82eb93a45d7a4664023b95
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408229"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorare le prestazioni del Servizio app di Azure

Abilitazione del monitoraggio in .NET e .NET Core basati su applicazioni web in esecuzione su [servizi App di Azure](https://docs.microsoft.com/azure/app-service/) è ora più facile che mai. Mentre in precedenza è necessario installare manualmente un'estensione del sito, l'estensione/agente più recente è ora incorporato nell'immagine del servizio app per impostazione predefinita. Questo articolo verrà consentono di eseguire l'abilitazione del monitoraggio di Application Insights, nonché fornire linee guida introduttive per l'automatizzazione del processo per le distribuzioni su larga scala.

> [!NOTE]
> Aggiunta manuale di un'estensione del sito Application Insights tramite **strumenti di sviluppo** > **estensioni** è deprecata. Questo metodo di installazione dell'estensione dipendeva aggiornamenti manuali per ogni nuova versione. La versione stabile più recente dell'estensione è ora [preinstallato](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) come parte dell'immagine del servizio App. I file si trovano in `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` e vengono aggiornati automaticamente con ogni versione stabile. Se si seguono le istruzioni basate su agenti per abilitare il monitoraggio di seguito rimuoverà automaticamente l'estensione deprecata per l'utente.

## <a name="enable-application-insights"></a>Abilita Application Insights

Esistono due modi per abilitare il monitoraggio delle applicazioni per le applicazioni di servizi App di Azure ospitate:

* **Monitoraggio dell'applicazione basata su agente** (ApplicationInsightsAgent).  
    * Questo metodo è più semplice da abilitare e non è necessaria alcuna configurazione avanzata. Si è noto anche come monitoraggio di "runtime". Per i servizi App di Azure è consigliabile almeno l'abilitazione di questo livello di monitoraggio e quindi in base uno scenario specifico che è possibile valutare se sono necessarie più avanzate per il monitoraggio tramite la strumentazione manuale.

* **Manualmente la strumentazione dell'applicazione tramite il codice** tramite l'installazione di Application Insights SDK.

    * Questo approccio è molto più personalizzabile, ma richiede [aggiunta di una dipendenza sui pacchetti NuGet di Application Insights SDK](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Questo metodo, significa anche che è necessario gestire manualmente gli aggiornamenti per la versione più recente dei pacchetti.

    * Se è necessario effettuare chiamate all'API personalizzate per tenere traccia degli eventi o le dipendenze non acquisite per impostazione predefinita basata su Agente monitoraggio, è necessario utilizzare questo metodo. Consultare il [API per l'articolo di metriche ed eventi personalizzato](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) per altre informazioni.

> [!NOTE]
> Se sia basato sull'agente di monitoraggio sia manuale SDK basato su strumentazione viene rilevato che verranno rispettate solo le impostazioni di strumentazione manuale. Per evitare dati duplicati da inviate. Per altre informazioni su questo, vedere la [risoluzione dei problemi di sezione](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) sotto.

## <a name="enable-agent-based-monitoring-net"></a>Abilitare basata su Agente monitoraggio .NET

> [!NOTE]
> la combinazione di APPINSIGHTS_JAVASCRIPT_ENABLED e urlCompression non è supportata. Per altre informazioni, vedere la spiegazione nel [risoluzione dei problemi di sezione](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).


1. **Selezionare Application Insights** nel pannello di controllo di Azure per il servizio app.

    ![In Impostazioni scegliere Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Scegliere di creare una nuova risorsa, a meno che non sia già stata impostata una risorsa di Application Insights per l'applicazione. 

     > [!NOTE]
     > Quando si fa clic su **OK** per creare la nuova risorsa, viene visualizzata la richiesta **Applica impostazioni di monitoraggio**. Selezionando **Continua** si collegherà la nuova risorsa di Application Insights al servizio app; ciò consente anche di **attivare un riavvio del servizio app**. 

     ![Instrumentazione dell'App Web](./media/azure-web-apps/create-resource-01.png)

2. Dopo aver specificato la risorsa da usare, è possibile scegliere il modo in cui Application Insights deve raccogliere i dati per ogni piattaforma per l'applicazione. Monitoraggio delle app ASP.NET è attivato per impostazione predefinita con due livelli diversi della raccolta.

    ![Scegliere le opzioni per ogni piattaforma](./media/azure-web-apps/choose-options-new.png)

   * Il livello di raccolta **Di base** di .NET offre funzionalità essenziali di monitoraggio delle prestazioni applicative per singole istanze.

   * Il livello di raccolta **Consigliato** di .NET:
       * Aggiunge le tendenze di utilizzo della CPU, della memoria e delle operazioni di I/O.
       * Mette in correlazione i microservizi attraverso i limiti di richiesta/dipendenza.
       * Raccoglie le tendenze di utilizzo e consente la correlazione dei risultati di disponibilità con le transazioni.
       * Raccoglie le eccezioni non gestite dal processo host.
       * Migliora la precisione delle metriche di monitoraggio delle prestazioni applicative in condizioni di carico quando viene usato il campionamento.

3. Per configurare le impostazioni, ad esempio il campionamento, è possibile controllare in precedenza tramite il file applicationinsights. config è ora possibile interagire con le stesse impostazioni tramite le impostazioni dell'applicazione con un prefisso corrispondente. 

    * Per modificare la percentuale di campionamento iniziale, ad esempio, è possibile creare un'impostazione dell'applicazione di: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` e il valore `100`.

    * Per l'elenco di impostazioni di processore dati di telemetria di campionamento adattivo supportati, è possibile consultare il [codice](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) e [documentazione associata](https://docs.microsoft.com/azure/azure-monitor/app/sampling).

## <a name="enable-agent-based-monitoring-net-core"></a>Abilitare basata su Agente monitoraggio .NET Core

Sono supportate le seguenti versioni di .NET Core: ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2

Destinazione il framework completo da .NET Core, distribuzione autonoma e ASP.NET Core 3.0 sono attualmente **non è supportato** con monitoraggio di base/estensione dell'agente. ([Strumentazione manuale](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) tramite il codice funzionerà in tutti gli scenari precedenti.)

1. **Selezionare Application Insights** nel pannello di controllo di Azure per il servizio app.

    ![In Impostazioni scegliere Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Scegliere di creare una nuova risorsa, a meno che non sia già stata impostata una risorsa di Application Insights per l'applicazione. 

     > [!NOTE]
     > Quando si fa clic su **OK** per creare la nuova risorsa, viene visualizzata la richiesta **Applica impostazioni di monitoraggio**. Selezionando **Continua** si collegherà la nuova risorsa di Application Insights al servizio app; ciò consente anche di **attivare un riavvio del servizio app**. 

     ![Instrumentazione dell'App Web](./media/azure-web-apps/create-resource-01.png)

2. Dopo aver specificato la risorsa da usare, è possibile scegliere la modalità di Application Insights per raccogliere i dati per ogni piattaforma per l'applicazione. .NET core offra **consigliato collection** oppure **disabilitato** per .NET Core 2.0, 2.1 e 2.2.

    ![Scegliere le opzioni per ogni piattaforma](./media/azure-web-apps/choose-options-new-net-core.png)

## <a name="enable-client-side-monitoring-net"></a>Abilitare .NET di monitoraggio lato client

Il monitoraggio lato client è acconsenti esplicitamente di ASP.NET. Per abilitare il monitoraggio lato client:

* Selezionare **impostazioni** > * * * * dell'applicazione delle impostazioni * * *
   * Nelle impostazioni dell'applicazione, aggiungere una nuova **nome dell'impostazione app** e **valore**:

     Nome: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valore: `true`

   * Salvare le impostazioni scegliendo **Salva** e quindi fare clic su **Riavvia** per riavviare l'app.

![Schermata dell'applicazione delle impostazioni dell'interfaccia utente](./media/azure-web-apps/appinsights-javascript-enabled.png)

Per disabilitare il monitoraggio di rimuovere la coppia valore chiave associata dalle impostazioni dell'applicazione sul lato client o impostare il valore su false.

## <a name="enable-client-side-monitoring-net-core"></a>Abilitare .NET Core monitoraggio lato client

Monitoraggio lato client viene **abilitata per impostazione predefinita** per le app .NET Core con **consigliato raccolta**, indipendentemente dal fatto che è presente l'impostazione 'APPINSIGHTS_JAVASCRIPT_ENABLED' dell'app.

Se per qualche motivo si vuole disabilitare il monitoraggio lato client:

* Selezionare **le impostazioni** > **le impostazioni dell'applicazione**
   * Nelle impostazioni dell'applicazione, aggiungere una nuova **nome dell'impostazione app** e **valore**:

     Nome: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valore: `false`

   * Salvare le impostazioni scegliendo **Salva** e quindi fare clic su **Riavvia** per riavviare l'app.

![Schermata dell'applicazione delle impostazioni dell'interfaccia utente](./media/azure-web-apps/appinsights-javascript-disabled.png)

## <a name="automate-monitoring"></a>Automatizzare il monitoraggio

Per abilitare la raccolta di dati di telemetria con Application Insights, è necessario impostare solo le impostazioni dell'applicazione:

   ![Impostazioni applicazione del servizio App con le impostazioni di Application Insights disponibili](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definizioni delle impostazioni dell'applicazione

|Nome impostazione app |  Definizione | Value |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Estensione del principale, che controlla il monitoraggio di runtime. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Le funzionalità essenziali, solo in modalità predefinita sono abilitate per assicurare prestazioni ottimali. | `default` o `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Controlla se il motore di riscrittura di file binario `InstrumentationEngine` verrà attivata. Questa impostazione comporta implicazioni sulla prestazione e influisce sul tempo di avvio/avvio a freddo. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Controlli se il testo della tabella SQL e Azure verranno acquisiti con le chiamate di dipendenza. Avviso relativo alle prestazioni: questa impostazione richiede la `InstrumentationEngine`. | `~1` |

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

Per un esempio di un modello di Azure Resource Manager con le impostazioni dell'applicazione configurata per Application Insights, ciò [modello](https://github.com/Andrew-MSFT/BasicImageGallery) può essere utile, in particolare la sezione a partire dal [riga 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatizzare la creazione di una risorsa di Application Insights e un collegamento al servizio App appena creato.

Per creare un modello Azure Resource Manager con tutte le impostazioni di Application Insights predefinite configurate, iniziare il processo come se si prevede di creare una nuova App Web con Application Insights abilitate.

Selezionare **opzioni di automazione**

   ![Dal menu la creazione di app web di servizio App](./media/azure-web-apps/create-web-app.png)

Questa opzione genera l'ultimo modello di Azure Resource Manager con tutte le impostazioni necessarie configurate.

  ![Modello di app web di servizio App](./media/azure-web-apps/arm-template.png)

Di seguito è riportato un esempio, sostituire tutte le istanze di `AppMonitoredSite` con il nome del sito:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

> [!NOTE]
> Il modello genererà le impostazioni dell'applicazione in modalità "default". Questa modalità è prestazioni ottimizzate, anche se è possibile modificare il modello per attivare qualunque funzionalità si preferisce.

### <a name="enabling-through-powershell"></a>Abilitazione tramite PowerShell

Per consentire l'applicazione di monitoraggio tramite PowerShell, è necessario essere modificata solo le impostazioni dell'applicazione sottostante. Di seguito è riportato un esempio, che consente il monitoraggio delle applicazioni per un sito Web denominato "AppMonitoredSite" nel gruppo di risorse "AppMonitoredRG", e configura i dati da inviare per la chiave di strumentazione "012345678-abcd-ef01-2345-6789abcd".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} #preserve non Application Insights Application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # enable the ApplicationInsightsAgent
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Eseguire l'aggiornamento di estensione/agente di monitoraggio

### <a name="upgrading-from-versions-289-and-up"></a>L'aggiornamento dalle versioni 2.8.9 e versioni successive

L'aggiornamento dalla versione 2.8.9 viene eseguita automaticamente, senza altre azioni. Le nuove funzionalità di monitoraggio vengono recapitate in background per il servizio app di destinazione e sul riavvio dell'applicazione verrà prelevati.

Per controllare quale versione dell'estensione è in esecuzione visita `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Screenshot del percorso url http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>L'aggiornamento dalle versioni 1.0.0 - 2.6.5

A partire dalla versione 2.8.9 viene usata l'estensione sito pre-installato. Se si ha una versione precedente, è possibile aggiornare tramite uno dei due modi:

* [Aggiornamento abilitando tramite il portale](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (Anche se si ha l'estensione Application Insights per servizio App di Azure installata, l'interfaccia utente mostra solo **abilitare** pulsante. Dietro le quinte, l'estensione sito privato precedente verrà rimossa.)

* [Aggiornamento tramite PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. Configurare le impostazioni dell'applicazione per abilitare l'estensione sito pre-installato ApplicationInsightsAgent. Visualizzare [abilitazione tramite powershell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Rimuovere manualmente l'estensione sito privato denominata estensione Application Insights per servizio App di Azure.

Se l'aggiornamento viene eseguita una versione precedente 2.5.1, verificare che le DLL ApplicationInsigths vengono rimosse dalla cartella bin dell'applicazione [vedere i passaggi di risoluzione dei problemi](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>risoluzione dei problemi

Ecco la nostra guida dettagliata sulla risoluzione dei problemi per il monitoraggio basato sull'estensione/agente per .NET e applicazioni in esecuzione in servizi App di Azure basate su .NET Core.

> [!NOTE]
> Le applicazioni Java e Node. js sono supportate solo nei servizi App di Azure tramite strumentazione di SDK basato su manuale e i passaggi seguenti non sono quindi applicabili a questi scenari.

1. Verificare che l'applicazione viene monitorata tramite `ApplicationInsightsAgent`.
    * Verificare che `ApplicationInsightsAgent_EXTENSION_VERSION` impostazione dell'app è impostato su un valore di "~ 2".
2. Assicurarsi che l'applicazione soddisfa i requisiti per il monitoraggio.
    * Passare a `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Screenshot della https://yoursitename.scm.azurewebsites/applicationinsights pagina risultati](./media/azure-web-apps/app-insights-sdk-status.png)

    * Verificare che il `Application Insights Extension Status` è `Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Se non è in esecuzione, seguire la [abilitare monitoraggio istruzioni di Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Verificare che lo stato esiste ed è simile a: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Se un valore simile non è presente, significa che l'applicazione non è attualmente in esecuzione o non è supportata. Per assicurarsi che l'applicazione sia in esecuzione, provare a visitare manualmente l'endpoint di url o dell'applicazione dell'applicazione, in modo da permettere le informazioni di runtime diventi disponibile.

    * Verificare che `IKeyExists` è `true`
        * Se è false, aggiungere ' APPINSIGHTS_INSTRUMENTATIONKEY con il guid della chiave di strumentazione alle impostazioni dell'applicazione.

    * Verificare che non sono presenti voci per `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly`, e `AppContainsAspNetTelemetryCorrelationAssembly`.
        * Se una di queste voci esiste, rimuovere i pacchetti seguenti dall'applicazione: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource`, e `Microsoft.AspNet.TelemetryCorrelation`.

La tabella seguente fornisce una spiegazione più dettagliata del significato di questi valori, le sottostante causa e le correzioni consigliate:

|Valore di problema|Spiegazione|Correggi
|---- |----|---|
| `AppAlreadyInstrumented:true` | Questo valore indica che l'estensione ha rilevato che alcuni aspetti del SDK sono già presenti nell'applicazione e verranno backoff. È possibile a causa di un riferimento a `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation`, o `Microsoft.ApplicationInsights`  | Rimuovere i riferimenti. Alcuni di questi riferimenti vengono aggiunti per impostazione predefinita di determinati modelli di Visual Studio e le versioni precedenti di Visual Studio possono aggiungere i riferimenti a `Microsoft.ApplicationInsights`.
|`AppAlreadyInstrumented:true` | Se l'applicazione è destinato a .NET Core 2.1 o 2.2 e si intende [Microsoft. aspnetcore](https://www.nuget.org/packages/Microsoft.AspNetCore.All) meta pacchetto, quindi lo riporta in Application Insights ed estensione verrà backoff. | I clienti in .NET Core 2.1,2.2 [consigliato](https://github.com/aspnet/Announcements/issues/287) usare invece Microsoft.AspNetCore.App meta pacchetto.|
|`AppAlreadyInstrumented:true` | Questo valore può essere causato anche dalla presenza delle DLL precedente nella cartella dell'app da una distribuzione precedente. | Eliminare la cartella dell'app per assicurarsi che queste DLL vengono rimossi.|
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Questo valore indica che l'estensione rilevato riferimenti a `Microsoft.AspNet.TelemetryCorrelation` nell'applicazione e verrà backoff. | Rimuovere il riferimento.
|`AppContainsDiagnosticSourceAssembly**:true`|Questo valore indica che l'estensione rilevato riferimenti a `System.Diagnostics.DiagnosticSource` nell'applicazione e verrà backoff.| Rimuovere il riferimento.
|`IKeyExists:false`|Questo valore indica che la chiave di strumentazione non è presente in AppSetting, `APPINSIGHTS_INSTRUMENTATIONKEY`. Possibili cause: I valori potrebbero essere stati rimossi per errore, sia dimenticato di impostare i valori in uno script di automazione e così via. | Assicurarsi che l'impostazione è presente nelle impostazioni dell'applicazione di servizio App.

### <a name="appinsightsjavascriptenabled-and-urlcompression-is-not-supported"></a>Non è supportato APPINSIGHTS_JAVASCRIPT_ENABLED e urlCompression

Se si usa APPINSIGHTS_JAVASCRIPT_ENABLED = true nei casi in cui il contenuto viene codificato, si potrebbero verificare errori, ad esempio: 

- Errore di riscrittura dell'URL 500
- 500.53 errore del modulo di riscrittura URL con messaggio di regole di riscrittura in uscita non può essere applicato quando il contenuto della risposta HTTP è codificato ('gzip'). 

Ciò a causa dell'impostazione applicazione APPINSIGHTS_JAVASCRIPT_ENABLED viene impostata su true e la codifica di contenuto sia presente nello stesso momento. Questo scenario non è ancora supportato. La soluzione alternativa consiste nel rimuovere APPINSIGHTS_JAVASCRIPT_ENABLED dalle impostazioni dell'applicazione. Sfortunatamente, ciò significa che se strumentazione JavaScript client/browser-side è ancora necessaria, riferimenti al SDK di manuali sono necessarie per le pagine Web. Seguire le [istruzioni](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) per la strumentazione manuale con JavaScript SDK.

Per informazioni aggiornate sull'estensione/agente di Application Insights, consultare il [note sulla versione](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire il profiler sull'app live](../app/profiler.md).
* [Funzioni di Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample): monitorare Funzioni di Azure con Application Insights
* [Abilitare l'invio dei dati di diagnostica di Azure](../platform/diagnostics-extension-to-application-insights.md) ad Application Insights.
* [Monitorare le metriche di integrità del servizio](../platform/data-platform.md) per assicurarsi che il servizio sia disponibile e reattivo.
* [Ricevere notifiche di avviso](../platform/alerts-overview.md) ogni volta che si verificano eventi operativi o le metriche superano una soglia.
* Usare [Analisi dell'utilizzo per applicazioni Web con Application Insights](javascript.md) per ottenere i dati di telemetria dei client dai browser che visitano una pagina Web.
* [Monitorare la disponibilità e la velocità di risposta dei siti Web](monitor-web-app-availability.md) per ricevere un avviso se il sito è inattivo.

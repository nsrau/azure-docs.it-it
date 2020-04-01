---
title: Monitorare le prestazioni dei servizi app di Azure | Microsoft Docs
description: Monitoraggio delle prestazioni applicative per i servizi app di Azure. Grafico del tempo di caricamento e di risposta, delle informazioni sulle dipendenze e dell'impostazione di avvisi sulle prestazioni.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: d2134e059a446c18108e8dd16bcc74504b42b15a
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437173"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorare le prestazioni del Servizio app di Azure

Abilitare il monitoraggio sulle applicazioni Web basate su ASP.NET e ASP.NET Core in esecuzione in [Servizi app](https://docs.microsoft.com/azure/app-service/) di Azure è ora più semplice che mai. Mentre in precedenza era necessario installare manualmente un'estensione del sito, l'estensione/agente più recente è ora incorporata nell'immagine del servizio app per impostazione predefinita. Questo articolo illustra come abilitare il monitoraggio di Application Insights e fornire indicazioni preliminari per l'automazione del processo per distribuzioni su larga scala.

> [!NOTE]
> L'aggiunta manuale di un'estensione del sito di Application Insights tramite**le estensioni** **degli strumenti** > di sviluppo è deprecata. Questo metodo di installazione dell'estensione dipendeva dagli aggiornamenti manuali per ogni nuova versione. L'ultima versione stabile dell'estensione è ora [preinstallata](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) come parte dell'immagine del servizio app. I file si `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` trovano in e vengono aggiornati automaticamente con ogni versione stabile. Se si seguono le istruzioni basate su agente per abilitare il monitoraggio di seguito, verrà rimossa automaticamente l'estensione deprecata.

## <a name="enable-application-insights"></a>Abilitare Application Insights

Esistono due modi per abilitare il monitoraggio delle applicazioni per le applicazioni ospitate dei servizi app di Azure:There are two ways to enable application monitoring for Azure App Services hosted applications:

* **Monitoraggio delle applicazioni basato su agente** (ApplicationInsightsAgent).  
    * Questo metodo è il più semplice da abilitare e non è necessaria alcuna configurazione avanzata. Viene spesso definito monitoraggio "di runtime". Per i servizi app di Azure è consigliabile abilitare almeno questo livello di monitoraggio e quindi, in base allo scenario specifico, è possibile valutare se è necessario un monitoraggio più avanzato tramite strumentazione manuale.

* **Instrumentare manualmente l'applicazione tramite codice** installando Application Insights SDK.

    * Questo approccio è molto più personalizzabile, ma richiede [l'aggiunta](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)di una dipendenza dai pacchetti NuGet di Application Insights SDK. Questo metodo, significa anche che è necessario gestire gli aggiornamenti alla versione più recente dei pacchetti da soli.

    * Se è necessario effettuare chiamate API personalizzate per tenere traccia di eventi/dipendenze non acquisiti per impostazione predefinita con il monitoraggio basato su agenti, è necessario utilizzare questo metodo. Per altre informazioni, consulta [l'articolo API per eventi e metriche personalizzati.](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) Questa è anche l'unica opzione supportata per i carichi di lavoro basati su Linux.This is also the only supported option for Linux based workloads.

> [!NOTE]
> Se vengono rilevati sia il monitoraggio basato su agenti che la strumentazione manuale basata su SDK, verranno rispettate solo le impostazioni di strumentazione manuale. In questo modo si evita l'invio di dati duplicati. Per ulteriori informazioni, consulta la [sezione relativa alla risoluzione dei problemi](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) riportata di seguito.

## <a name="enable-agent-based-monitoring"></a>Abilitare il monitoraggio basato su agentiEnable agent-based monitoring

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> La combinazione di APPINSIGHTS_JAVASCRIPT_ENABLED e urlCompression non è supportata. Per ulteriori informazioni, vedere la spiegazione nella sezione relativa alla [risoluzione dei problemi.](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)


1. **Selezionare Application Insights** nel pannello di controllo di Azure per il servizio app.

    ![In Impostazioni scegliere Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Scegliere di creare una nuova risorsa, a meno che non sia già stata impostata una risorsa di Application Insights per l'applicazione. 

     > [!NOTE]
     > Quando si fa clic su **OK** per creare la nuova risorsa, viene visualizzata la richiesta **Applica impostazioni di monitoraggio**. Selezionando **Continua** si collegherà la nuova risorsa di Application Insights al servizio app; ciò consente anche di **attivare un riavvio del servizio app**. 

     ![Instrumentazione dell'App Web](./media/azure-web-apps/create-resource-01.png)

2. Dopo aver specificato la risorsa da usare, è possibile scegliere il modo in cui Application Insights deve raccogliere i dati per ogni piattaforma per l'applicazione. ASP.NET monitoraggio delle app è attivato per impostazione predefinita con due diversi livelli di raccolta.

    ![Scegliere le opzioni per ogni piattaforma](./media/azure-web-apps/choose-options-new.png)

   * Il livello di raccolta di .NET **Basic** offre funzionalità APM a istanza singola essenziali.

   * Il livello di raccolta **Consigliato** di .NET:
       * Aggiunge le tendenze di utilizzo della CPU, della memoria e delle operazioni di I/O.
       * Mette in correlazione i microservizi attraverso i limiti di richiesta/dipendenza.
       * Raccoglie le tendenze di utilizzo e consente la correlazione dei risultati di disponibilità con le transazioni.
       * Raccoglie le eccezioni non gestite dal processo host.
       * Migliora la precisione delle metriche di monitoraggio delle prestazioni applicative in condizioni di carico quando viene usato il campionamento.

3. Per configurare impostazioni come il campionamento, che in precedenza era possibile controllare tramite il file applicationinsights.config, è ora possibile interagire con le stesse impostazioni tramite le impostazioni dell'applicazione con un prefisso corrispondente. 

    * Ad esempio, per modificare la percentuale di campionamento `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` iniziale, `100`è possibile creare un'impostazione Applicazione pari a: e un valore pari a .

    * Per l'elenco delle impostazioni del processore di telemetria di campionamento adattivo supportato, è possibile consultare il [codice](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) e la [documentazione associata.](https://docs.microsoft.com/azure/azure-monitor/app/sampling)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Sono supportate le seguenti versioni di .NET Core: ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2, ASP.NET Core 3.0

La destinazione del framework completo da .NET Core, distribuzione autonoma e applicazioni basate su Linux non sono attualmente **supportate** con il monitoraggio basato su agenti/estensioni. (La[strumentazione manuale](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) tramite codice funzionerà in tutti gli scenari precedenti.)

1. **Selezionare Application Insights** nel pannello di controllo di Azure per il servizio app.

    ![In Impostazioni scegliere Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Scegliere di creare una nuova risorsa, a meno che non sia già stata impostata una risorsa di Application Insights per l'applicazione. 

     > [!NOTE]
     > Quando si fa clic su **OK** per creare la nuova risorsa, viene visualizzata la richiesta **Applica impostazioni di monitoraggio**. Selezionando **Continua** si collegherà la nuova risorsa di Application Insights al servizio app; ciò consente anche di **attivare un riavvio del servizio app**. 

     ![Instrumentazione dell'App Web](./media/azure-web-apps/create-resource-01.png)

2. Dopo aver specificato la risorsa da usare, è possibile scegliere come usare Application Insights per raccogliere i dati per ogni piattaforma. .NET Core offre **la raccolta consigliata** o **disabilitato** per .NET Core 2.0, 2.1, 2.2 e 3.0.

    ![Scegliere le opzioni per ogni piattaforma](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Dall'app Web del servizio app in **Impostazioni** > **selezionare Abilita informazioni dettagliate** > **Enable**. Il monitoraggio basato su agente Node.js è attualmente in anteprima.

# <a name="java"></a>[Java](#tab/java)

Le applicazioni Web basate su Java App Service non supportano attualmente il monitoraggio automatico basato su agente/estensione. Per abilitare il monitoraggio per l'applicazione Java, è necessario [instrumentare manualmente l'applicazione](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started).

# <a name="python"></a>[Python](#tab/python)

Le applicazioni Web basate su Python App Service non supportano attualmente il monitoraggio automatico basato su agenti/estensioni. Per abilitare il monitoraggio per l'applicazione Python, è necessario [instrumentare manualmente l'applicazione](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

---

## <a name="enable-client-side-monitoring"></a>Abilitare il monitoraggio lato client

# <a name="net"></a>[.NET](#tab/net)

Il monitoraggio lato client è il consenso esplicito per ASP.NET. Per abilitare il monitoraggio lato client:

* Selezionare **Impostazioni** >
   * In Impostazioni applicazione aggiungere un nuovo nome e **valore**per **l'impostazione dell'app:**

     Nome: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valore: `true`

   * Salvare le impostazioni scegliendo **Salva** e quindi fare clic su **Riavvia** per riavviare l'app.

![Schermata dell'interfaccia utente delle impostazioni dell'applicazione](./media/azure-web-apps/appinsights-javascript-enabled.png)

Per disabilitare il monitoraggio lato client, rimuovere la coppia di valori di chiave associata dalle impostazioni dell'applicazione o impostare il valore su false.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Il monitoraggio lato client è **abilitato per impostazione predefinita** per le app .NET Core con raccolta **consigliata**, indipendentemente dal fatto che l'impostazione dell'app 'APPINSIGHTS_JAVASCRIPT_ENABLED' sia presente.

Se per qualche motivo si desidera disabilitare il monitoraggio lato client:

* Selezionare **Impostazioni** > **applicazione**
   * In Impostazioni applicazione aggiungere un nuovo nome e **valore**per **l'impostazione dell'app:**

     Nome:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valore: `false`

   * Salvare le impostazioni scegliendo **Salva** e quindi fare clic su **Riavvia** per riavviare l'app.

![Schermata dell'interfaccia utente delle impostazioni dell'applicazione](./media/azure-web-apps/appinsights-javascript-disabled.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Per abilitare il monitoraggio lato client per l'applicazione Node.js, è necessario [aggiungere manualmente l'SDK JavaScript lato client all'applicazione.](https://docs.microsoft.com/azure/azure-monitor/app/javascript)

# <a name="java"></a>[Java](#tab/java)

Per abilitare il monitoraggio lato client per l'applicazione Java, è necessario [aggiungere manualmente l'SDK JavaScript lato client all'applicazione.](https://docs.microsoft.com/azure/azure-monitor/app/javascript)

# <a name="python"></a>[Python](#tab/python)

Per abilitare il monitoraggio lato client per l'applicazione Python, è necessario [aggiungere manualmente l'SDK JavaScript lato client all'applicazione.](https://docs.microsoft.com/azure/azure-monitor/app/javascript)

---

## <a name="automate-monitoring"></a>Automatizzare il monitoraggio

Per abilitare la raccolta di dati di telemetria con Application Insights, è necessario impostare solo le impostazioni dell'applicazione:In order to enable telemetry collection with Application Insights, only the Application settings need to be set:

   ![Impostazioni dell'applicazione del servizio app con le impostazioni di Application Insights disponibili](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definizioni delle impostazioni dell'applicazione

|Nome impostazione app |  Definizione | valore |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Estensione principale, che controlla il monitoraggio di runtime. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Solo in modalità predefinita, le funzioni essenziali sono abilitate per garantire prestazioni ottimali. | `default` o `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Controlla se il motore `InstrumentationEngine` di riscrittura binaria verrà attivato. Questa impostazione ha implicazioni sulle prestazioni e influisce sul tempo di avvio/avvio a freddo. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Controlla se SQL & testo della tabella di Azure verrà acquisito insieme alle chiamate alle dipendenze. Avviso di prestazioni: il tempo di avvio a freddo dell'applicazione sarà influenzato. Questa impostazione `InstrumentationEngine`richiede il . | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Impostazioni dell'applicazione di servizio app con Azure Resource Manager

Le impostazioni dell'applicazione per i servizi app possono essere gestite e configurate con i modelli di [Azure Resource Manager.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) Questo metodo può essere usato quando si distribuiscono nuove risorse del servizio app con l'automazione di Azure Resource Manager o per modificare le impostazioni delle risorse esistenti.

La struttura di base delle impostazioni dell'applicazione JSON per un servizio app è riportata di seguito:The basic structure of the application settings JSON for an app service is below:

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

Per un esempio di modello di Azure Resource Manager con le impostazioni dell'applicazione configurate per Application Insights, questo [modello](https://github.com/Andrew-MSFT/BasicImageGallery) può essere utile, in particolare la sezione che inizia alla [riga 238.](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatizzare la creazione di una risorsa di Application Insights e collegarsi al servizio app appena creato.

Per creare un modello di Azure Resource Manager con tutte le impostazioni predefinite di Application Insights configurate, iniziare il processo come se si vostesse creare una nuova app Web con Application Insights abilitato.

Selezionare **Opzioni di automazione**

   ![Menu di creazione dell'app Web del servizio app](./media/azure-web-apps/create-web-app.png)

Questa opzione genera il modello di Azure Resource Manager più recente con tutte le impostazioni necessarie configurate.

  ![Modello di app Web del servizio app](./media/azure-web-apps/arm-template.png)

Di seguito è riportato `AppMonitoredSite` un esempio, sostituisci tutte le istanze di con il nome del sito:

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
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
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

### <a name="enabling-through-powershell"></a>Abilitazione tramite PowerShellEnabling through PowerShell

Per abilitare il monitoraggio dell'applicazione tramite PowerShell, è necessario modificare solo le impostazioni dell'applicazione sottostante. Di seguito è riportato un esempio, che consente il monitoraggio delle applicazioni per un sito Web denominato "AppMonitoredSite" nel gruppo di risorse "AppMonitoredRG" e configura i dati da inviare alla chiave di strumentazione "012345678-abcd-ef01-2345-6789abcd".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Estensione/agente di monitoraggio dell'aggiornamentoUpgrade monitoring extension/agent

### <a name="upgrading-from-versions-289-and-up"></a>Aggiornamento dalle versioni 2.8.9 e successive

L'aggiornamento dalla versione 2.8.9 avviene automaticamente, senza ulteriori azioni. I nuovi bit di monitoraggio vengono recapitati in background al servizio app di destinazione e al riavvio dell'applicazione verranno prelevati.

Per verificare la versione dell'estensione in esecuzione, visitare`http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Schermata del percorso urlhttp://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Aggiornamento dalle versioni 1.0.0 - 2.6.5

A partire dalla versione 2.8.9 viene utilizzata l'estensione del sito preinstallata. Se sei una versione precedente, puoi aggiornare in uno dei due modi seguenti:

* [Eseguire l'aggiornamento abilitando tramite il portale](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). Anche se è installata l'estensione Application Insights per il servizio app di Azure, l'interfaccia utente mostra solo il pulsante **Abilita.** Dietro le quinte, la vecchia estensione del sito privato verrà rimossa.)

* [Eseguire l'aggiornamento tramite PowerShell:](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell)

    1. Impostare le impostazioni dell'applicazione per abilitare l'estensione di sito preinstallata ApplicationInsightsAgent.Set the application settings to enable the pre-installed site extension ApplicationInsightsAgent. Vedere [Abilitazione tramite PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Rimuovere manualmente l'estensione del sito privato denominata Applicazione Insights extension per il servizio app di Azure.Manually remove the private site extension named Application Insights extension for Azure App Service.

Se l'aggiornamento viene eseguito da una versione precedente alla 2.5.1, verificare che le DLL ApplicationInsigths vengano rimosse dalla cartella del cestino dell'applicazione, [vedere la procedura di risoluzione dei problemi](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Di seguito è riportata la guida dettagliata alla risoluzione dei problemi per il monitoraggio basato su estensione/agente per le applicazioni basate su .NET e .NET Core in esecuzione nei servizi app di Azure.Below is our step-by-step troubleshooting guide for extension/agent based monitoring for .NET and .NET Core based applications running on Azure App Services.

> [!NOTE]
> Le applicazioni Java sono supportate solo in Servizi app di Azure tramite strumentazione manuale basata su SDK e pertanto i passaggi seguenti non si applicano a questi scenari.

1. Verificare che l'applicazione `ApplicationInsightsAgent`sia monitorata tramite .
    * Verificare `ApplicationInsightsAgent_EXTENSION_VERSION` che l'impostazione dell'app sia impostata su un valore pari a "2".
2. Assicurarsi che l'applicazione soddisfi i requisiti da monitorare.
    * Passare a `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`.

    ![Screenshot https://yoursitename.scm.azurewebsites/applicationinsights della pagina dei risultati](./media/azure-web-apps/app-insights-sdk-status.png)

    * Verificare `Application Insights Extension Status` che l'is`Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Se non è in esecuzione, seguire le istruzioni di [monitoraggio di Enable Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Verificare che l'origine dello stato esista e abbia l'aspetto:`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Se non è presente un valore simile, significa che l'applicazione non è attualmente in esecuzione o non è supportata. Per assicurarsi che l'applicazione sia in esecuzione, provare a visitare manualmente gli endpoint url/applicazione dell'applicazione, in modo da rendere disponibili le informazioni di runtime.

    * Verificare `IKeyExists` che sia`true`
        * Se è `false`, `APPINSIGHTS_INSTRUMENTATIONKEY` `APPLICATIONINSIGHTS_CONNECTION_STRING` aggiungere e con il guid ikey alle impostazioni dell'applicazione.

    * Verificare che non vi `AppAlreadyInstrumented` `AppContainsDiagnosticSourceAssembly`siano `AppContainsAspNetTelemetryCorrelationAssembly`voci per , , e .
        * Se esiste una di queste voci, rimuovere i `Microsoft.ApplicationInsights` `System.Diagnostics.DiagnosticSource`seguenti `Microsoft.AspNet.TelemetryCorrelation`pacchetti dall'applicazione: , e .

La tabella seguente fornisce una spiegazione più dettagliata del significato di questi valori, delle relative cause sottostanti e delle correzioni consigliate:

|Valore del problema|Spiegazione|Fix
|---- |----|---|
| `AppAlreadyInstrumented:true` | Questo valore indica che l'estensione ha rilevato che alcuni aspetti dell'SDK sono già presenti nell'applicazione e verrà eseguito il back-off. Può essere dovuto a `System.Diagnostics.DiagnosticSource`un `Microsoft.AspNet.TelemetryCorrelation`riferimento a , , o`Microsoft.ApplicationInsights`  | Rimuovere i riferimenti. Alcuni di questi riferimenti vengono aggiunti per impostazione predefinita da alcuni modelli `Microsoft.ApplicationInsights`di Visual Studio e le versioni precedenti di Visual Studio possono aggiungere riferimenti a .
|`AppAlreadyInstrumented:true` | Se l'applicazione è destinata a .NET Core 2.1 o 2.2 e fa riferimento a [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) meta-package, include Application Insights ed estensione verrà eseguito il back-off. | Si [consiglia](https://github.com/aspnet/Announcements/issues/287) ai clienti con .NET Core 2.1,2.2 di utilizzare invece il metapacchetto Microsoft.AspNetCore.App.|
|`AppAlreadyInstrumented:true` | Questo valore può essere causato anche dalla presenza delle DLL precedenti nella cartella dell'app da una distribuzione precedente. | Pulisci la cartella dell'app per assicurarti che queste DLL vengano rimosse. Controllare sia la directory bin dell'app locale che la directory wwwroot nel servizio app. (Per controllare la directory wwwroot dell'app Web del servizio app: Strumenti avanzati (Kudu) > console di debug > CMD > home,site/wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Questo valore indica che l'estensione ha rilevato riferimenti a `Microsoft.AspNet.TelemetryCorrelation` nell'applicazione e verrà eseguito il back-off. | Rimuovere il riferimento.
|`AppContainsDiagnosticSourceAssembly**:true`|Questo valore indica che l'estensione ha rilevato riferimenti a `System.Diagnostics.DiagnosticSource` nell'applicazione e verrà eseguito il back-off.| Rimuovere il riferimento.
|`IKeyExists:false`|Questo valore indica che la chiave di strumentazione `APPINSIGHTS_INSTRUMENTATIONKEY`non è presente in AppSetting, . Possibili cause: i valori potrebbero essere stati rimossi accidentalmente, dimenticati di impostare i valori nello script di automazione e così via. | Assicurarsi che l'impostazione sia presente nelle impostazioni dell'applicazione del servizio app.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED e urlCompression non sono supportati

Se si utilizza APPINSIGHTS_JAVASCRIPT_ENABLED, true nei casi in cui il contenuto è codificato, è possibile che vengano visualizzati errori come: 

- 500 Errore di riscrittura URL
- 500.53 Errore del modulo di riscrittura URL con messaggio Le regole di riscrittura in uscita non possono essere applicate quando il contenuto della risposta HTTP è codificato ('gzip'). 

Ciò è dovuto al fatto che l'impostazione dell'applicazione APPINSIGHTS_JAVASCRIPT_ENABLED è impostata su true e la codifica del contenuto è presente allo stesso tempo. Questo scenario non è ancora supportato. La soluzione consiste nel rimuovere APPINSIGHTS_JAVASCRIPT_ENABLED dalle impostazioni dell'applicazione. Purtroppo ciò significa che se la strumentazione JavaScript lato client/browser è ancora necessaria, sono necessari riferimenti SDK manuali per le pagine Web. Seguire le [istruzioni](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) per la strumentazione manuale con JavaScript SDK.

Per informazioni aggiornate sull'agente/estensione di Application Insights, vedere le [note sulla versione](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

### <a name="php-and-wordpress-are-not-supported"></a>PHP e WordPress non sono supportati

I siti PHP e WordPress non sono supportati. Attualmente non è disponibile alcun SDK/agente ufficialmente supportato per il monitoraggio lato server di questi carichi di lavoro. Tuttavia, la strumentazione manuale delle transazioni lato client su un sito PHP o WordPress aggiungendo il javascript lato client alle pagine Web può essere eseguita utilizzando [JavaScript SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript). 

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire il profiler sull'app live](../app/profiler.md).
* [Funzioni di Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample): monitorare Funzioni di Azure con Application Insights
* [Abilitare l'invio dei dati di diagnostica di Azure](../platform/diagnostics-extension-to-application-insights.md) ad Application Insights.
* [Monitorare le metriche di integrità del servizio](../platform/data-platform.md) per assicurarsi che il servizio sia disponibile e reattivo.
* [Ricevere notifiche di avviso](../platform/alerts-overview.md) ogni volta che si verificano eventi operativi o le metriche superano una soglia.
* Usare [Analisi dell'utilizzo per applicazioni Web con Application Insights](javascript.md) per ottenere i dati di telemetria dei client dai browser che visitano una pagina Web.
* [Monitorare la disponibilità e la velocità di risposta dei siti Web](monitor-web-app-availability.md) per ricevere un avviso se il sito è inattivo.

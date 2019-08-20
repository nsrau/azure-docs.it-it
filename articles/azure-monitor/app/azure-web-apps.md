---
title: Monitorare le prestazioni dei servizi app di Azure | Microsoft Docs
description: Monitoraggio delle prestazioni applicative per i servizi app di Azure. Tempo di caricamento e di risposta del grafico, informazioni sulle dipendenze e impostazione di avvisi sulle prestazioni.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mbullwin
ms.openlocfilehash: 4f296aae6c147b0d5209276dbd008a1207837cfd
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875210"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorare le prestazioni del Servizio app di Azure

L'abilitazione del monitoraggio sulle applicazioni Web basate su .NET e .NET Core in esecuzione in [app Azure Services](https://docs.microsoft.com/azure/app-service/) è ora più semplice che mai. Mentre in precedenza era necessario installare manualmente un'estensione del sito, la versione più recente dell'agente è ora incorporata nell'immagine del servizio app per impostazione predefinita. Questo articolo illustra l'abilitazione del monitoraggio Application Insights, oltre a fornire indicazioni preliminari per l'automazione del processo per le distribuzioni su larga scala.

> [!NOTE]
> L'aggiunta manuale di un'estensione del sito di Application Insights tramite le**estensioni** **degli strumenti** > di sviluppo è deprecata. Questo metodo di installazione dell'estensione dipende da aggiornamenti manuali per ogni nuova versione. La versione stabile più recente dell'estensione è ora [preinstallata](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) come parte dell'immagine del servizio app. I file si trovano in `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` e vengono aggiornati automaticamente con ogni versione stabile. Se si seguono le istruzioni basate sull'agente per abilitare il monitoraggio di seguito, verrà automaticamente rimossa l'estensione deprecata.

## <a name="enable-application-insights"></a>Abilitare Application Insights

Esistono due modi per abilitare il monitoraggio dell'applicazione per le applicazioni ospitate da app Azure Services:

* **Monitoraggio dell'applicazione basata su agenti** (ApplicationInsightsAgent).  
    * Questo metodo è il più semplice da abilitare e non è necessaria alcuna configurazione avanzata. Viene spesso definito monitoraggio "Runtime". Per i servizi app Azure si consiglia di abilitare almeno questo livello di monitoraggio, quindi, in base allo scenario specifico, è possibile valutare se è necessario un monitoraggio più avanzato tramite strumentazione manuale.

* **Strumentazione manuale dell'applicazione tramite il codice** tramite l'installazione di Application Insights SDK.

    * Questo approccio è molto più personalizzabile, ma richiede [l'aggiunta di una dipendenza dai pacchetti NuGet di Application Insights SDK](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Questo metodo consente inoltre di gestire gli aggiornamenti alla versione più recente dei pacchetti.

    * Se è necessario eseguire chiamate API personalizzate per tenere traccia degli eventi e delle dipendenze non acquisiti per impostazione predefinita con il monitoraggio basato su agenti, è necessario usare questo metodo. Per altre informazioni, vedere l' [articolo relativo all'API per eventi personalizzati e metriche](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) .

> [!NOTE]
> Se vengono rilevati sia il monitoraggio basato su agente che la strumentazione manuale basata su SDK, verranno rispettate solo le impostazioni di strumentazione manuale. Ciò consente di impedire l'invio di dati duplicati. Per ulteriori informazioni, vedere la [sezione relativa alla risoluzione dei problemi](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) di seguito.

## <a name="enable-agent-based-monitoring-for-net-applications"></a>Abilitare il monitoraggio basato su agenti per le applicazioni .NET

> [!NOTE]
> La combinazione di APPINSIGHTS_JAVASCRIPT_ENABLED e urlCompression non è supportata. Per ulteriori informazioni, vedere la spiegazione nella [sezione risoluzione dei problemi](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).


1. **Selezionare Application Insights** nel pannello di controllo di Azure per il servizio app.

    ![In Impostazioni scegliere Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Scegliere di creare una nuova risorsa, a meno che non sia già stata impostata una risorsa di Application Insights per l'applicazione. 

     > [!NOTE]
     > Quando si fa clic su **OK** per creare la nuova risorsa, viene visualizzata la richiesta **Applica impostazioni di monitoraggio**. Selezionando **Continua** si collegherà la nuova risorsa di Application Insights al servizio app; ciò consente anche di **attivare un riavvio del servizio app**. 

     ![Instrumentazione dell'App Web](./media/azure-web-apps/create-resource-01.png)

2. Dopo aver specificato la risorsa da usare, è possibile scegliere il modo in cui Application Insights deve raccogliere i dati per ogni piattaforma per l'applicazione. Il monitoraggio delle app ASP.NET è impostato per impostazione predefinita con due diversi livelli di raccolta.

    ![Scegliere le opzioni per ogni piattaforma](./media/azure-web-apps/choose-options-new.png)

   * Il livello di raccolta **Di base** di .NET offre funzionalità essenziali di monitoraggio delle prestazioni applicative per singole istanze.

   * Il livello di raccolta **Consigliato** di .NET:
       * Aggiunge le tendenze di utilizzo della CPU, della memoria e delle operazioni di I/O.
       * Mette in correlazione i microservizi attraverso i limiti di richiesta/dipendenza.
       * Raccoglie le tendenze di utilizzo e consente la correlazione dei risultati di disponibilità con le transazioni.
       * Raccoglie le eccezioni non gestite dal processo host.
       * Migliora la precisione delle metriche di monitoraggio delle prestazioni applicative in condizioni di carico quando viene usato il campionamento.

3. Per configurare impostazioni come il campionamento, che in precedenza era possibile controllare tramite il file applicationinsights. config, è ora possibile interagire con le stesse impostazioni tramite le impostazioni dell'applicazione con un prefisso corrispondente. 

    * Per modificare la percentuale di campionamento iniziale, ad esempio, è possibile creare un'impostazione dell' `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` applicazione: e un `100`valore di.

    * Per l'elenco delle impostazioni del processore di telemetria del campionamento adattivo supportato, è possibile consultare il [codice](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) e la [documentazione associata](https://docs.microsoft.com/azure/azure-monitor/app/sampling).

## <a name="enable-agent-based-monitoring-for-net-core-applications"></a>Abilitare il monitoraggio basato su agenti per le applicazioni .NET Core

Sono supportate le versioni seguenti di .NET Core: ASP.NET Core 2,0, ASP.NET Core 2,1, ASP.NET Core 2,2

La destinazione del Framework completo da .NET Core, la distribuzione autonoma e la ASP.NET Core 3,0 non sono attualmente **supportate** con il monitoraggio basato su agenti/estensioni. La[Strumentazione manuale](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) tramite codice funzionerà in tutti gli scenari precedenti.

1. **Selezionare Application Insights** nel pannello di controllo di Azure per il servizio app.

    ![In Impostazioni scegliere Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Scegliere di creare una nuova risorsa, a meno che non sia già stata impostata una risorsa di Application Insights per l'applicazione. 

     > [!NOTE]
     > Quando si fa clic su **OK** per creare la nuova risorsa, viene visualizzata la richiesta **Applica impostazioni di monitoraggio**. Selezionando **Continua** si collegherà la nuova risorsa di Application Insights al servizio app; ciò consente anche di **attivare un riavvio del servizio app**. 

     ![Instrumentazione dell'App Web](./media/azure-web-apps/create-resource-01.png)

2. Dopo aver specificato la risorsa da usare, è possibile scegliere come si vuole Application Insights raccogliere i dati per piattaforma per l'applicazione. .NET Core offre una **raccolta consigliata** o **disabilitata** per .net core 2,0, 2,1 e 2,2.

    ![Scegliere le opzioni per ogni piattaforma](./media/azure-web-apps/choose-options-new-net-core.png)

## <a name="enable-client-side-monitoring-for-net-applications"></a>Abilitare il monitoraggio lato client per le applicazioni .NET

Il monitoraggio lato client è il consenso esplicito per ASP.NET. Per abilitare il monitoraggio lato client:

* Selezionare **impostazioni** >** **dell'applicazione delle impostazioni****
   * In Impostazioni applicazione aggiungere un nuovo nome e **valore**per l' **impostazione dell'app** :

     Nome: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valore: `true`

   * Salvare le impostazioni scegliendo **Salva** e quindi fare clic su **Riavvia** per riavviare l'app.

![Screenshot dell'interfaccia utente delle impostazioni dell'applicazione](./media/azure-web-apps/appinsights-javascript-enabled.png)

Per disabilitare il monitoraggio lato client, rimuovere la coppia chiave/valore associata dalle impostazioni dell'applicazione oppure impostare il valore su false.

## <a name="enable-client-side-monitoring-for-net-core-applications"></a>Abilitare il monitoraggio lato client per le applicazioni .NET Core

Il monitoraggio lato client è **abilitato per impostazione predefinita** per le app .NET Core con la **raccolta consigliata**, indipendentemente dal fatto che sia presente l'impostazione dell'app "APPINSIGHTS_JAVASCRIPT_ENABLED".

Se per qualche motivo si desidera disabilitare il monitoraggio lato client:

* Selezionare **Impostazioni** > **Impostazioni applicazione**
   * In Impostazioni applicazione aggiungere un nuovo nome e **valore**per l' **impostazione dell'app** :

     nome`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valore: `false`

   * Salvare le impostazioni scegliendo **Salva** e quindi fare clic su **Riavvia** per riavviare l'app.

![Screenshot dell'interfaccia utente delle impostazioni dell'applicazione](./media/azure-web-apps/appinsights-javascript-disabled.png)

## <a name="automate-monitoring"></a>Automatizzare il monitoraggio

Per abilitare la raccolta di dati di telemetria con Application Insights, è necessario impostare solo le impostazioni dell'applicazione:

   ![Impostazioni dell'applicazione del servizio app con le impostazioni di Application Insights disponibili](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definizioni delle impostazioni dell'applicazione

|Nome impostazione app |  Definizione | Valore |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Estensione principale, che controlla il monitoraggio in fase di esecuzione. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Solo in modalità predefinita sono abilitate le funzionalità essenziali per garantire prestazioni ottimali. | `default` o `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Controlla se il motore `InstrumentationEngine` di riscrittura binaria verrà attivato. Questa impostazione presenta implicazioni sulle prestazioni e influisce sul tempo di avvio/avvio a freddo. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Controlla se SQL & il testo della tabella di Azure verrà acquisito insieme alle chiamate alle dipendenze. Avviso di prestazioni: questa impostazione richiede `InstrumentationEngine`il. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Impostazioni dell'applicazione del servizio app con Azure Resource Manager

Le impostazioni dell'applicazione per i servizi app possono essere gestite e configurate con i [modelli Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Questo metodo può essere usato quando si distribuiscono nuove risorse del servizio app con Azure Resource Manager automazione o per modificare le impostazioni delle risorse esistenti.

Di seguito è riportata la struttura di base del codice JSON delle impostazioni dell'applicazione per un servizio app:

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

Per un esempio di modello di Azure Resource Manager con le impostazioni dell'applicazione configurate per Application Insights, questo [modello](https://github.com/Andrew-MSFT/BasicImageGallery) può essere utile, in particolare la sezione che inizia alla [riga 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatizzare la creazione di una risorsa di Application Insights e il collegamento al servizio app appena creato.

Per creare un modello di Azure Resource Manager con tutte le impostazioni di Application Insights predefinite configurate, avviare il processo come se si creasse una nuova app Web con Application Insights abilitata.

Selezionare le **Opzioni di automazione**

   ![Menu di creazione dell'app Web del servizio app](./media/azure-web-apps/create-web-app.png)

Questa opzione genera il modello di Azure Resource Manager più recente con tutte le impostazioni necessarie configurate.

  ![Modello di app Web del servizio app](./media/azure-web-apps/arm-template.png)

Di seguito è riportato un esempio di sostituzione di `AppMonitoredSite` tutte le istanze di con il nome del sito:

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
> Il modello genererà le impostazioni dell'applicazione in modalità "predefinita". Questa modalità consente di ottimizzare le prestazioni, anche se è possibile modificare il modello per attivare qualsiasi funzionalità preferita.

### <a name="enabling-through-powershell"></a>Abilitazione tramite PowerShell

Per abilitare il monitoraggio dell'applicazione tramite PowerShell, è necessario modificare solo le impostazioni dell'applicazione sottostante. Di seguito è riportato un esempio che consente il monitoraggio delle applicazioni per un sito Web denominato "AppMonitoredSite" nel gruppo di risorse "AppMonitoredRG" e configura i dati da inviare alla chiave di strumentazione "012345678-ABCD-EF01-2345-6789abcd".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} #preserve non Application Insights Application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # enable the ApplicationInsightsAgent
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Aggiornare l'estensione di monitoraggio/Agent

### <a name="upgrading-from-versions-289-and-up"></a>Aggiornamento da versioni 2.8.9 e versioni precedenti

L'aggiornamento dalla versione 2.8.9 avviene automaticamente, senza alcuna azione aggiuntiva. I nuovi bit di monitoraggio vengono recapitati in background al servizio app di destinazione e, al riavvio dell'applicazione, verranno prelevati.

Per verificare quale versione dell'estensione in esecuzione visitare`http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Screenshot del percorso URL http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Aggiornamento dalla versione 1.0.0-2.6.5

A partire dalla versione 2.8.9 viene utilizzata l'estensione del sito preinstallata. Se si dispone di una versione precedente, è possibile aggiornare tramite uno dei due modi seguenti:

* [Eseguire l'aggiornamento abilitando tramite il portale](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (Anche se è stata installata l'estensione Application Insights per il servizio app Azure, l'interfaccia utente Mostra solo il pulsante **Abilita** . Dietro le quinte, l'estensione del sito privato precedente verrà rimossa.

* [Eseguire l'aggiornamento tramite PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. Impostare le impostazioni dell'applicazione per abilitare l'estensione del sito preinstallato ApplicationInsightsAgent. Vedere [Abilitazione tramite PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Rimuovere manualmente l'estensione del sito privato denominata Application Insights estensione per app Azure servizio.

Se l'aggiornamento viene eseguito da una versione precedente alla 2.5.1, verificare che le DLL di ApplicationInsigths vengano rimosse dalla cartella bin dell'applicazione, [vedere la procedura di risoluzione dei problemi](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>risoluzione dei problemi

Di seguito è riportata la guida dettagliata alla risoluzione dei problemi per il monitoraggio di estensioni/agenti per le applicazioni .NET e .NET Core in esecuzione in app Azure Services.

> [!NOTE]
> Le applicazioni Java e node. js sono supportate solo nei servizi app Azure tramite strumentazione manuale basata su SDK, quindi i passaggi seguenti non si applicano a questi scenari.

1. Verificare che l'applicazione venga monitorata `ApplicationInsightsAgent`tramite.
    * Verificare che `ApplicationInsightsAgent_EXTENSION_VERSION` l'impostazione dell'app sia impostata su un valore pari a "~ 2".
2. Verificare che l'applicazione soddisfi i requisiti da monitorare.
    * Passa a`https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Screenshot della https://yoursitename.scm.azurewebsites/applicationinsights pagina risultati](./media/azure-web-apps/app-insights-sdk-status.png)

    * Verificare che `Application Insights Extension Status` sia`Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Se non è in esecuzione, seguire le [istruzioni per l'abilitazione del monitoraggio Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Verificare che l'origine di stato esista e abbia un aspetto simile al seguente:`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Se non è presente un valore simile, significa che l'applicazione non è attualmente in esecuzione o non è supportata. Per assicurarsi che l'applicazione sia in esecuzione, provare a visitare manualmente gli endpoint dell'applicazione/URL dell'applicazione, in modo da rendere disponibili le informazioni di Runtime.

    * Confermare che `IKeyExists` è`true`
        * Se è false, aggiungere ' APPINSIGHTS_INSTRUMENTATIONKEY con il GUID iKey alle impostazioni dell'applicazione.

    * Verificare che non siano presenti voci per `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly`e `AppContainsAspNetTelemetryCorrelationAssembly`.
        * Se una di queste voci esiste, rimuovere i pacchetti seguenti dall'applicazione: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource`e `Microsoft.AspNet.TelemetryCorrelation`.

La tabella seguente fornisce una spiegazione più dettagliata del significato di questi valori, delle cause sottostanti e delle correzioni consigliate:

|Valore problema|Spiegazione|Correzione
|---- |----|---|
| `AppAlreadyInstrumented:true` | Questo valore indica che l'estensione ha rilevato che alcuni aspetti dell'SDK sono già presenti nell'applicazione e che verrà eseguito il backup. Può essere dovuto a un riferimento a `System.Diagnostics.DiagnosticSource`, o `Microsoft.AspNet.TelemetryCorrelation``Microsoft.ApplicationInsights`  | Rimuovere i riferimenti. Alcuni di questi riferimenti vengono aggiunti per impostazione predefinita da determinati modelli di Visual Studio e le versioni precedenti di Visual Studio possono aggiungere `Microsoft.ApplicationInsights`riferimenti a.
|`AppAlreadyInstrumented:true` | Se l'applicazione è destinata a .NET Core 2,1 o 2,2 e si riferisce al metapacchetto [Microsoft. AspNetCore. All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) , la porta Application Insights e l'estensione verrà riattivata. | Per i clienti di .NET Core 2.1, 2.2 è [consigliabile](https://github.com/aspnet/Announcements/issues/287) usare invece il meta pacchetto Microsoft. AspNetCore. app.|
|`AppAlreadyInstrumented:true` | Questo valore può anche essere causato dalla presenza delle dll precedenti nella cartella dell'app da una distribuzione precedente. | Pulire la cartella dell'app per assicurarsi che queste DLL vengano rimosse.|
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Questo valore indica che l'estensione ha rilevato `Microsoft.AspNet.TelemetryCorrelation` riferimenti a nell'applicazione e verrà riattivata. | Rimuovere il riferimento.
|`AppContainsDiagnosticSourceAssembly**:true`|Questo valore indica che l'estensione ha rilevato `System.Diagnostics.DiagnosticSource` riferimenti a nell'applicazione e verrà riattivata.| Rimuovere il riferimento.
|`IKeyExists:false`|Questo valore indica che la chiave di strumentazione non è presente in AppSetting `APPINSIGHTS_INSTRUMENTATIONKEY`. Possibili cause: È possibile che i valori siano stati rimossi accidentalmente, che non siano stati impostati in script di automazione e così via. | Assicurarsi che l'impostazione sia presente nelle impostazioni dell'applicazione del servizio app.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED e urlCompression non sono supportati

Se si usa APPINSIGHTS_JAVASCRIPT_ENABLED = true nei casi in cui il contenuto è codificato, è possibile che vengano visualizzati errori simili ai seguenti: 

- 500 errore di riscrittura URL
- 500,53 errore del modulo di riscrittura URL con le regole di riscrittura in uscita del messaggio non può essere applicato quando il contenuto della risposta HTTP è codificato (' gzip '). 

Ciò è dovuto al fatto che l'impostazione dell'applicazione APPINSIGHTS_JAVASCRIPT_ENABLED è impostata su true e la codifica del contenuto è presente nello stesso momento. Questo scenario non è ancora supportato. La soluzione alternativa consiste nel rimuovere APPINSIGHTS_JAVASCRIPT_ENABLED dalle impostazioni dell'applicazione. Sfortunatamente questo significa che se la strumentazione JavaScript lato client/browser è ancora richiesta, per le pagine Web sono necessarie le indicazioni manuali SDK. Seguire le [istruzioni](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) per la strumentazione manuale con JavaScript SDK.

Per informazioni aggiornate sull'Application Insights o sull'agente, vedere le [Note sulla versione](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire il profiler sull'app live](../app/profiler.md).
* [Funzioni di Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample): monitorare Funzioni di Azure con Application Insights
* [Abilitare l'invio dei dati di diagnostica di Azure](../platform/diagnostics-extension-to-application-insights.md) ad Application Insights.
* [Monitorare le metriche di integrità del servizio](../platform/data-platform.md) per assicurarsi che il servizio sia disponibile e reattivo.
* [Ricevere notifiche di avviso](../platform/alerts-overview.md) ogni volta che si verificano eventi operativi o le metriche superano una soglia.
* Usare [Analisi dell'utilizzo per applicazioni Web con Application Insights](javascript.md) per ottenere i dati di telemetria dei client dai browser che visitano una pagina Web.
* [Monitorare la disponibilità e la velocità di risposta dei siti Web](monitor-web-app-availability.md) per ricevere un avviso se il sito è inattivo.

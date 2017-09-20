---
title: Abilitare Azure Application Insights Profiler in una risorsa di Servizi cloud | Microsoft Docs
description: Informazioni su come configurare il profiler in un'applicazione ASP.NET ospitata da una risorsa di Servizi Cloud di Azure.
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: c2cae6129386260f2bf35f75d44fa001f7541d40
ms.contentlocale: it-it
ms.lasthandoff: 09/20/2017

---

# <a name="enable-application-insights-profiler-on-an-azure-cloud-services-resource"></a>Abilitare Application Insights Profiler in una risorsa di Servizi cloud di Azure

Questa procedura dettagliata illustra come abilitare Azure Application Insights Profiler in un'applicazione ASP.NET ospitata da una risorsa di Servizi cloud di Azure. Gli esempi includono il supporto per Macchine virtuali di Azure, set di scalabilità di macchine virtuali e Azure Service Fabric. Tutti gli esempi si basano su modelli che supportano il modello di distribuzione Azure Resource Manager. Per altre informazioni sul modello di distribuzione, vedere [Confronto tra distribuzione di Azure Resource Manager e classica: comprensione dei modelli di implementazione e dello stato delle risorse](/azure-resource-manager/resource-manager-deployment-model).

## <a name="overview"></a>Panoramica

Il diagramma seguente illustra come funziona il profiler per le risorse di Servizi cloud di Azure. Come esempio viene usata una macchina virtuale di Azure.

![Panoramica](./media/enable-profiler-compute/overview.png) Per raccogliere le informazioni per l'elaborazione e la visualizzazione nel portale di Azure, è necessario installare il componente Diagnostics Agent per le risorse di Servizi cloud di Azure. Il resto della procedura dettagliata contiene indicazioni su come installare e configurare Diagnostics Agent per abilitare Application Insights Profiler.

## <a name="prerequisites-for-the-walkthrough"></a>Prerequisiti per la procedura dettagliata

* Un modello di distribuzione Resource Manager che consente di installare gli agenti del profiler nelle VM ([WindowsVirtualMachine.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)) o nei set di scalabilità ([WindowsVirtualMachineScaleSet.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)).

* Istanza di Application Insights abilitata per la profilatura. Per istruzioni, vedere [Abilitare il profiler](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler).

* .NET Framework 4.6.1 o versione successiva installato nella risorsa di Servizi cloud di Azure di destinazione.

## <a name="create-a-resource-group-in-your-azure-subscription"></a>Creare un gruppo di risorse nella sottoscrizione di Azure
L'esempio seguente illustra come creare un gruppo di risorse usando uno script di PowerShell:

```
New-AzureRmResourceGroup -Name "Replace_With_Resource_Group_Name" -Location "Replace_With_Resource_Group_Location"
```

## <a name="create-an-application-insights-resource-in-the-resource-group"></a>Creare una risorsa di Application Insights nel gruppo di risorse
Nel pannello **Application Insights** immettere le informazioni per la risorsa, come illustrato nell'esempio: 

![Pannello Application Insights](./media/enable-profiler-compute/createai.png)

## <a name="apply-an-application-insights-instrumentation-key-in-the-azure-resource-manager-template"></a>Applicare una chiave di strumentazione di Application Insights nel modello di Azure Resource Manager

1. Se il modello non è ancora stato scaricato, scaricarlo da [GitHub](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json).

2. Trovare la chiave di Application Insights.
   
   ![Posizione della chiave](./media/enable-profiler-compute/copyaikey.png)

3. Sostituire il valore del modello.
   
   ![Valore sostituito nel modello](./media/enable-profiler-compute/copyaikeytotemplate.png)

## <a name="create-an-azure-vm-to-host-the-web-application"></a>Creare una VM di Azure per ospitare l'applicazione Web
1. Creare una stringa sicura per salvare la password.

   ```
   $password = ConvertTo-SecureString -String "Replace_With_Your_Password" -AsPlainText -Force
   ```

2. Distribuire il modello di Azure Resource Manager.

   Sostituire la directory nella console di PowerShell con la cartella contenente il modello di Resource Manager. Per distribuire il modello, eseguire il comando seguente:

   ```
   New-AzureRmResourceGroupDeployment -ResourceGroupName "Replace_With_Resource_Group_Name" -TemplateFile .\WindowsVirtualMachine.json -adminUsername "Replace_With_your_user_name" -adminPassword $password -dnsNameForPublicIP "Replace_WIth_your_DNS_Name" -Verbose
   ```

Al termine dell'esecuzione dello script, nel gruppo di risorse sarà presente una VM denominata **MyWindowsVM**.

## <a name="configure-web-deploy-on-the-vm"></a>Configurare Distribuzione Web nella VM
Assicurarsi che il servizio Distribuzione Web sia abilitato nella VM per poter pubblicare l'applicazione Web da Visual Studio.

Per installare Distribuzione Web in una VM manualmente tramite WebPI, vedere [Installing and Configuring Web Deploy on IIS 8.0 or Later](https://docs.microsoft.com/en-us/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later) (Installazione e configurazione di Distribuzione Web in IIS 8.0 e versioni successive). Per un esempio di come automatizzare l'installazione di Distribuzione Web usando un modello di Azure Resource Manager, vedere [Create, configure, and deploy Web Application to an Azure VM](https://azure.microsoft.com/en-us/resources/templates/201-web-app-vm-dsc/) (Creare, configurare e distribuire un'applicazione Web in una VM di Azure).

Per distribuire un'applicazione ASP.NET MVC, passare a Server Manager, selezionare **Aggiungi ruoli e funzionalità** > **Server Web (IIS)** > **Server Web** > **Sviluppo applicazioni** e abilitare ASP.NET 4.5 nel server.

![Aggiungere ASP.NET](./media/enable-profiler-compute/addaspnet45.png)

## <a name="install-the-azure-application-insights-sdk-for-your-project"></a>Installare Azure Application Insights SDK per il progetto
1. Aprire l'applicazione Web ASP.NET in Visual Studio.

2. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **Servizi connessi**.

3. Selezionare **Application Insights**.

4. Seguire le istruzioni visualizzate. Selezionare la risorsa di Application Insights creata in precedenza.

5. Fare clic sul pulsante **Registra**.


## <a name="publish-the-project-to-an-azure-vm"></a>Pubblicare il progetto in una VM di Azure
Esistono diversi modi per pubblicare un'applicazione in una macchina virtuale di Azure. Un modo consiste nell'usare Visual Studio 2017.

1. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**.

2. Selezionare **Macchine virtuali di Microsoft Azure** come destinazione di pubblicazione e seguire i passaggi.

   ![Pubblicare da VS](./media/enable-profiler-compute/publishtoVM.png)

3. Eseguire un test di carico sull'applicazione. I risultati dovrebbero venire visualizzati nella pagina Web del portale dell'istanza di Application Insights.


## <a name="enable-the-profiler"></a>Abilitare il profiler
1. Passare al pannello **Prestazioni** di Application Insights e selezionare **Configura**.
   
   ![Icona Configura](./media/enable-profiler-compute/enableprofiler1.png)
 
2. Selezionare **Abilita profiler**.
   
   ![Icona Abilita profiler](./media/enable-profiler-compute/enableprofiler2.png)

## <a name="add-a-performance-test-to-your-application"></a>Aggiungere un test delle prestazioni all'applicazione
Seguire questa procedura per poter raccogliere alcuni dati di esempio da visualizzare in Application Insights Profiler:

1. Passare alla risorsa di Application Insights creata in precedenza. 

2. Passare al pannello **Disponibilità** e aggiungere un test delle prestazioni che invia richieste Web all'URL dell'applicazione. 

   ![Aggiungere il test delle prestazioni](./media/enable-profiler-compute/AvailabilityTest.png)

## <a name="view-your-performance-data"></a>Visualizzare i dati sulle prestazioni

1. Attendere 10-15 minuti mentre il profiler raccoglie e analizza i dati. 

2. Passare al pannello **Prestazioni** nella risorsa di Application Insights e visualizzare le prestazioni dell'applicazione sotto carico.

   ![Visualizzazione delle prestazioni](./media/enable-profiler-compute/aiperformance.png)

3. Selezionare l'icona sotto **Esempi** per aprire il pannello **Trace View** (Visualizzazione traccia).

   ![Aprire il pannello di visualizzazione traccia](./media/enable-profiler-compute/traceview.png)


## <a name="work-with-an-existing-template"></a>Utilizzare un modello esistente

1. Individuare la dichiarazione della risorsa di Diagnostica di Azure nel modello di distribuzione.
   
   Se non si ha una dichiarazione, è possibile crearne una simile a quella nell'esempio seguente. È possibile aggiornare il modello dal [sito Web di Azure Resource Explorer](https://resources.azure.com).

2. Modificare il valore di publisher da `Microsoft.Azure.Diagnostics` a `AIP.Diagnostics.Test`.

3. Per `typeHandlerVersion`, usare `0.0`.

4. Assicurarsi che `autoUpgradeMinorVersion` sia impostato su `true`.

5. Aggiungere la nuova istanza del sink `ApplicationInsightsProfiler` nell'oggetto impostazioni `WadCfg`, come illustrato nell'esempio seguente:

```
"resources": [
        {
          "type": "extensions",
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "apiVersion": "2016-03-30",
          "properties": {
            "publisher": "AIP.Diagnostics.Test",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "WadCfg": {
                "SinksConfig": {
                  "Sink": [
                    {
                      "name": "Give a descriptive short name. E.g.: MyApplicationInsightsProfilerSink",
                      "ApplicationInsightsProfiler": "Enter the Application Insights instance instrumentation key guid here"
                    }
                  ]
                },
                "DiagnosticMonitorConfiguration": {
                    ...
                }
                ...
              }
              ...
            }
            ...
          }
          ...
]
```

## <a name="enable-the-profiler-on-virtual-machine-scale-sets"></a>Abilitare il profiler nei set di scalabilità di macchine virtuali
Per informazioni su come abilitare il profiler, scaricare il modello [WindowsVirtualMachineScaleSet.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json). Applicare le stesse modifiche di un modello di VM alla risorsa di estensione di diagnostica per il set di scalabilità di macchine virtuali.

Assicurarsi che ogni istanza nel set di scalabilità abbia accesso a Internet. L'agente del profiler può quindi inviare i campioni raccolti ad Application Insights per la visualizzazione e l'analisi.

## <a name="enable-the-profiler-on-service-fabric-applications"></a>Abilitare il profiler nelle applicazioni di Service Fabric
1. Effettuare il provisioning del cluster di Service Fabric in modo da avere l'estensione di Diagnostica di Azure che installa l'agente del profiler.

2. Installare Application Insights SDK nel progetto e configurare la chiave di Application Insights.

3. Aggiungere il codice dell'applicazione per instrumentare la telemetria.

### <a name="provision-the-service-fabric-cluster-to-have-the-azure-diagnostics-extension-that-installs-the-profiler-agent"></a>Effettuare il provisioning del cluster di Service Fabric in modo da avere l'estensione di Diagnostica di Azure che installa l'agente del profiler
Un cluster di Service Fabric può essere sicuro o non sicuro. È possibile impostare un cluster di gateway come non sicuro in modo che non richieda un certificato per l'accesso. I cluster che ospitano la logica di business e i dati devono essere sicuri. È possibile abilitare il profiler in cluster di Service Fabric sia sicuri che non sicuri. Questa procedura dettagliata usa un cluster non sicuro come esempio per illustrare le modifiche necessarie per abilitare il profiler. È possibile effettuare il provisioning di un cluster sicuro nello stesso modo.

1. Scaricare [ServiceFabricCluster.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json). Come per le VM e i set di scalabilità di macchine virtuali, sostituire `Application_Insights_Key` con la propria chiave di Application Insights:

   ```
   "publisher": "AIP.Diagnostics.Test",
                 "settings": {
                   "WadCfg": {
                     "SinksConfig": {
                       "Sink": [
                         {
                           "name": "MyApplicationInsightsProfilerSinkVMSS",
                           "ApplicationInsightsProfiler": "[Application_Insights_Key]"
                         }
                       ]
                     },
   ```

2. Distribuire il modello usando uno script di PowerShell:

   ```
   Login-AzureRmAccount
   New-AzureRmResourceGroup -Name [Your_Resource_Group_Name] -Location [Your_Resource_Group_Location] -Verbose -Force
   New-AzureRmResourceGroupDeployment -Name [Choose_An_Arbitrary_Name] -ResourceGroupName [Your_Resource_Group_Name] -TemplateFile [Path_To_Your_Template]

   ```

### <a name="install-the-application-insights-sdk-in-the-project-and-configure-the-application-insights-key"></a>Installare Application Insights SDK nel progetto e configurare la chiave di Application Insights
Installare Application Insights SDK dal [pacchetto NuGet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Assicurarsi di installare una versione stabile, 2.3 o successiva. 

Per informazioni sulla configurazione di Application Insights nei progetti, vedere [Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Uso di Service Fabric con Application Insights).

### <a name="add-application-code-to-instrument-telemetry"></a>Aggiungere il codice dell'applicazione per instrumentare la telemetria
1. Per ogni parte del codice che si vuole instrumentare, aggiungere un'istruzione using. 

   Nell'esempio seguente il metodo `RunAsync` esegue alcune operazioni e la classe `telemetryClient` acquisisce i dati di telemetria dopo l'avvio. È necessario un nome univoco per l'evento nell'applicazione.

   ```
   protected override async Task RunAsync(CancellationToken cancellationToken)
       {
           // TODO: Replace the following sample code with your own logic
           //       or remove this RunAsync override if it's not needed in your service.

           while (true)
           {
               using( var operation = telemetryClient.StartOperation<RequestTelemetry>("[Insert_Event_Unique_Name]"))
               {
                   cancellationToken.ThrowIfCancellationRequested();

                   ++this.iterations;

                   ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", this.iterations);

                   await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
               }

           }
       }
   ```

2. Distribuire l'applicazione nel cluster di Service Fabric. Lasciare l'app in esecuzione per 10 minuti. Per un risultato migliore, è possibile eseguire un test di carico sull'app. Passare al pannello **Prestazioni** del portale di Application Insights. Dovrebbero venire visualizzati gli esempi di tracce di profilatura.

<!---
Commenting out these sections for now
## Enable the Profiler on Cloud Services applications
[TODO]
## Enable the Profiler on classic Azure Virtual Machines
[TODO]
## Enable the Profiler on on-premise servers
[TODO]
--->

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come risolvere i problemi relativi al profiler, vedere [Risoluzione dei problemi del profiler](app-insights-profiler.md#troubleshooting).

- Per altre informazioni sul profiler, vedere [Application Insights Profiler](app-insights-profiler.md).


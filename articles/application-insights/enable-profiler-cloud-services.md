---
title: Abilitare Azure Application Insights Profiler per una risorsa di calcolo | Microsoft Docs
description: Informazioni su come configurare il profiler
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
ms.author: sewhee
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: cde0b0bce2e332243e555a72088b8e9eeb680bdb
ms.contentlocale: it-it
ms.lasthandoff: 07/28/2017

---

# <a name="how-to-enable-application-insights-profiler-on-azure-compute-resources"></a>Come abilitare Application Insights Profiler nelle risorse di calcolo di Azure

Questa procedura dettagliata illustra come abilitare Application Insights Profiler in un'applicazione ASP.NET ospitata dalle risorse di calcolo di Azure. Gli esempi includono il supporto per macchine virtuali, set di scalabilità di macchine virtuali e Services Fabric. Tutti gli esempi si basano su modelli che supportano il modello di distribuzione Azure Resource Manager. Per altre informazioni sul modello di distribuzione, vedere [Confronto tra distribuzione Azure Resource Manager e classica: comprensione dei modelli di distribuzione e dello stato delle risorse](/azure-resource-manager/resource-manager-deployment-model).

## <a name="overview"></a>Panoramica

Il diagramma seguente illustra come funziona il profiler per le risorse di calcolo di Azure. Usa una macchina virtuale di Azure come esempio.

![Panoramica](./media/enable-profiler-compute/overview.png) È necessario installare il componente agente di Diagnostica per le risorse di calcolo di Azure per raccogliere informazioni da elaborare e visualizzare nel portale di Azure. Il resto della procedura dettagliata contiene indicazioni su come installare e configurare l'agente di Diagnostica per abilitare Application Insights Profiler.

## <a name="prerequisites-for-the-walkthrough"></a>Prerequisiti per la procedura dettagliata

* Scaricare i modelli di distribuzione Resource Manager che installano gli agenti del profiler nelle VM o nei set di scalabilità.

    [WindowsVirtualMachine.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachine.json) | [WindowsVirtualMachineScaleSet.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachineScaleSet.json)
* Istanza di Application Insights abilitata per la profilatura. Per informazioni, vedere https://docs.microsoft.com/it-it/azure/application-insights/app-insights-profiler#enable-the-profiler.
* .NET framework >= 4.6.1 installato nella risorsa di calcolo di Azure di destinazione.

## <a name="create-a-resource-group-in-your-azure-subscription"></a>Creare un gruppo di risorse nella sottoscrizione di Azure
L'esempio seguente illustra come creare un gruppo di risorse usando uno script di PowerShell:

```
New-AzureRmResourceGroup -Name "Replace_With_Resource_Group_Name" -Location "Replace_With_Resource_Group_Location"
```

## <a name="create-an-application-insights-resource-in-the-resource-group"></a>Creare una risorsa di Application Insights nel gruppo di risorse

![Creare Application Insights](./media/enable-profiler-compute/createai.png)

## <a name="apply-application-insights-instrumentation-key-in-the-azure-resource-manager-template"></a>Applicare la chiave di strumentazione di Application Insights nel modello di Azure Resource Manager
Se non è ancora stato fatto, scaricare il modello da [WindowsVirtualMachine.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachine.json)

![Trovare la chiave di AI](./media/enable-profiler-compute/copyaikey.png)

![Sostituire il valore del modello](./media/enable-profiler-compute/copyaikeytotemplate.png)

## <a name="create-azure-vm-to-host-the-web-application"></a>Creare una VM di Azure per ospitare l'applicazione Web
* Creare una stringa sicura per salvare la password
```
$password = ConvertTo-SecureString -String "Replace_With_Your_Password" -AsPlainText -Force
```

* Distribuire il modello di Azure Resource Manager

Sostituire la directory nella console di PowerShell con la cartella contenente il modello di Resource Manager. Per distribuire il modello, usare il comando seguente:

```
New-AzureRmResourceGroupDeployment -ResourceGroupName "Replace_With_Resource_Group_Name" -TemplateFile .\WindowsVirtualMachine.json -adminUsername "Replace_With_your_user_name" -adminPassword $password -dnsNameForPublicIP "Replace_WIth_your_DNS_Name" -Verbose
```

Al termine dell'esecuzione dello script, nel gruppo di risorse sarà presente una VM denominata *MyWindowsVM*.

## <a name="configure-web-deploy-on-the-vm"></a>Configurare Distribuzione Web nella VM
Verificare che **Distribuzione Web** sia abilitato nella VM per poter pubblicare l'applicazione Web da Visual Studio.

È possibile installare manualmente Distribuzione Web in una VM tramite WebPI: [Installing and Configuring Web Deploy on IIS 8.0 or Later](https://docs.microsoft.com/en-us/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later) (Installazione e configurazione di Distribuzione Web in IIS 8.0 o versione successiva)

Ecco un esempio di come automatizzare l'installazione di Distribuzione Web usando il modello di Azure Resource Manager: [Create, configure, and deploy Web Application to an Azure VM](https://azure.microsoft.com/en-us/resources/templates/201-web-app-vm-dsc/) (Creare, configurare e distribuire l'applicazione Web in una VM di Azure)

Per distribuire un'applicazione ASP.NET MVC, è necessario andare a Server Manager, **Aggiungi ruoli e funzionalità | Server Web (IIS) | Server Web | Sviluppo applicazioni** e abilitare ASP.NET 4.5 nel server.
![Aggiungere ASP.NET](./media/enable-profiler-compute/addaspnet45.png)

## <a name="install-azure-application-insights-sdk-to-your-project"></a>Installare Azure Application Insights SDK nel progetto
* Aprire l'applicazione Web ASP.NET in Visual Studio
* Fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi | Servizi connessi**
* Scegliere Application Insights
* Seguire le istruzioni visualizzate. Selezionare la risorsa di Application Insights creata prima
* Fare clic sul pulsante **Registra**


## <a name="publish-the-project-to-azure-vm"></a>Pubblicare il progetto nella VM di Azure
Esistono diversi modi per pubblicare un'applicazione in una macchina virtuale di Azure. Uno consiste nell'eseguire tale operazione in Visual Studio 2017.
Per completare il processo di pubblicazione, fare clic con il pulsante destro sul progetto e scegliere "Pubblica". Selezionare Macchina virtuale di Azure come destinazione di pubblicazione e seguire i passaggi.

![Pubblicare da VS](./media/enable-profiler-compute/publishtoVM.png)

Eseguire alcuni test di carico sull'applicazione. I risultati verranno visualizzati nella pagina Web del portale dell'istanza di Application Insights.


## <a name="enable-the-profiler-in-application-insights"></a>Attivare il profiler in Application Insights
Andare al pannello delle prestazioni di Application Insights. Fare clic sull'icona Configura e abilitare il profiler

![Passaggio 1 di abilitazione del profiler](./media/enable-profiler-compute/enableprofiler1.png)

![Passaggio 2 di abilitazione del profiler](./media/enable-profiler-compute/enableprofiler2.png)

## <a name="add-an-availability-test-to-your-application"></a>Aggiungere un test di disponibilità per l'applicazione
Passare alla risorsa di Application Insights creata prima. Andare al pannello Disponibilità e aggiungere un test delle prestazioni che invia richieste Web all'URL dell'applicazione. In questo modo è possibile raccogliere alcuni dati di esempio da visualizzare in Application Insights Profiler

![Aggiungere il test delle prestazioni][./media/enable-profiler-compute/add-test.png]

## <a name="view-your-performance-data"></a>Visualizzare i dati sulle prestazioni

Attendere 10-15 minuti mentre il profiler raccoglie e analizza i dati. Andare quindi al pannello Prestazioni nella risorsa di AI e visualizzare le prestazioni dell'applicazione mentre era sotto carico.

![Visualizzare le prestazioni][./media/enable-profiler-compute/view-aiperformance.png]

Fare clic sull'icona sotto Esempi per aprire il pannello Trace View (Visualizzazione traccia).

![Trace View (Visualizzazione traccia)][./media/enable-profiler-compute/traceview.png]


## <a name="work-with-an-existing-template"></a>Utilizzare un modello esistente

1. Individuare la dichiarazione della risorsa di Diagnostica di Microsoft Azure nel modello di distribuzione.
  * Crearne una, se necessario. Vedere l'esempio completo come riferimento.
  * È possibile aggiornare il modello dal sito Web delle risorse di Azure (https://resources.azure.com).
2. Sostituire il server di pubblicazione "Microsoft.Azure.Diagnostics" con "AIP.Diagnostics.Test".
3. Usare typeHandlerVersion "0.0".
4. Verificare che autoUpgradeMinorVersion sia impostato su true.
5. Aggiungere la nuova istanza del sink ApplicationInsightsProfiler nell'oggetto impostazioni WadCfg, come illustrato nell'esempio seguente.

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

## <a name="enable-the-profiler-on-virtual-machine-scale-sets"></a>Attivare il profiler nei set di scalabilità di macchine virtuali
Scaricare il modello [WindowsVirtualMachineScaleSet.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachineScaleSet.json) per vedere come abilitare il profiler. Applicare le stesse modifiche di un modello di VM alla risorsa di estensione di diagnostica dei set di scalabilità di macchine virtuali.
Verificare che ogni istanza nel set di scalabilità abbia accesso a Internet, in modo che l'agente del profiler possa inviare ad Application Insights gli esempi raccolti da analizzare e visualizzare.

## <a name="enable-the-profiler-on-service-fabric-applications"></a>Abilitare il profiler nelle applicazioni di Service Fabric
Attualmente per abilitare il profiler nelle applicazioni di Service Fabric, è necessario:
1. Fornire al cluster di Service Fabric l'estensione di Diagnostica di Microsoft Azure che installa l'agente del profiler
2. Installare Application Insights SDK nel progetto e configurare la chiave di AI
3. Aggiungere il codice dell'applicazione per instrumentare la telemetria

## <a name="provision-the-service-fabric-cluster-have-the-wad-extension-that-installs-the-profiler-agent"></a>Fornire al cluster di Service Fabric l'estensione di Diagnostica di Microsoft Azure che installa l'agente del profiler
Un cluster di Service Fabric può essere sicuro o non sicuro. È possibile impostare un cluster di gateway come non sicuro in modo che non richieda un certificato per l'accesso. I cluster che ospitano la logica di business e i dati devono essere sicuri. È possibile abilitare il profiler in cluster di Service Fabric sia sicuri che non sicuri. Questa procedura dettagliata usa un cluster non sicuro come esempio per illustrare le modifiche necessarie per abilitare il profiler. È possibile effettuare il provisioning di un cluster sicuro nello stesso modo.

Scaricare [ServiceFabricCluster.json](https://github.com/CFreemanwa/samples/blob/master/ServiceFabricCluster.json). Come per le VM e il set di scalabilità di macchine virtuali, sostituire la chiave di Application Insights con la propria chiave di AI:

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

Distribuire il modello usando uno script di PowerShell:
```
Login-AzureRmAccount
New-AzureRmResourceGroup -Name [Your_Resource_Group_Name] -Location [Your_Resource_Group_Location] -Verbose -Force
New-AzureRmResourceGroupDeployment -Name [Choose_An_Arbitrary_Name] -ResourceGroupName [Your_Resource_Group_Name] -TemplateFile [Path_To_Your_Template]

```

## <a name="install-application-insights-sdk-in-the-project-and-configure-ai-key"></a>Installare Application Insights SDK nel progetto e configurare la chiave di AI
Installare Application Insights SDK dal pacchetto NuGet. Assicurarsi di installare una versione 2.3 o successiva stabile. [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) Per configurare Application Insights nei progetti, vedere [Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Uso di Service Fabric con Application Insights).

## <a name="add-application-code-to-instrument-telemetry"></a>Aggiungere il codice dell'applicazione per instrumentare la telemetria
Inserire ogni parte del codice che si vuole instrumentare all'interno di un'istruzione using. Nell'esempio seguente il metodo RunAsync esegue alcune operazioni e la classe telemetryClient acquisisce i dati di telemetria dopo l'avvio. È necessario un nome univoco per l'evento nell'applicazione.

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

Distribuire l'applicazione nel cluster di Service Fabric. Lasciare l'app in esecuzione per 10 minuti. Per un risultato migliore, è possibile eseguire un test di carico sull'app. Andare al pannello Prestazioni del portale di Application Insights. Verranno visualizzati gli esempi delle tracce di profilatura.

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

- Per risolvere i problemi relativi al profiler, vedere [Risoluzione dei problemi del profiler](app-insights-profiler.md#troubleshooting)

- Per altre informazioni sul profiler, vedere [Application Insights Profiler](app-insights-profiler.md).


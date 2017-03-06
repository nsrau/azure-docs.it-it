---
title: Come usare la diagnostica di Azure (.NET) con i servizi cloud | Documentazione Microsoft
description: Uso della diagnostica di Azure per raccogliere dati dai servizi cloud di Azure per debug, valutazione delle prestazioni, monitoraggio, analisi del traffico e altro ancora.
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: 
ms.assetid: 89623a0e-4e78-4b67-a446-7d19a35a44be
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/25/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: c3540d86a12935cea100248f7f6669df34ae2209
ms.openlocfilehash: cedc52b514eacb6cf7bc32634819573f5ee154c3
ms.lasthandoff: 02/16/2017


---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Abilitazione di Diagnostica di Azure in servizi cloud di Azure
Vedere [Cenni preliminari sulla diagnostica di Azure](../azure-diagnostics.md) per un background sulla diagnostica di Azure.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Come abilitare la diagnostica in un ruolo di lavoro
Questa procedura dettagliata descrive come implementare un ruolo di lavoro di Azure che emette i dati di telemetria con la classe EventSource .NET. Il modulo Diagnostica Azure viene usato per raccogliere i dati di telemetria e memorizzarli in un account di archiviazione di Azure. Quando si crea un ruolo di lavoro, Visual Studio abilita automaticamente Diagnostica 1.0 come parte della soluzione in Azure SDK per .NET 2.4 e versioni precedenti. Le seguenti istruzioni descrivono il processo per creare il ruolo di lavoro, disabilitare Diagnostica 1.0 dalla soluzione e implementare Diagnostica 1.2 o 1.3 nel ruolo di lavoro.

### <a name="prerequisites"></a>Prerequisiti
In questo articolo si presuppone che l'utente abbia una sottoscrizione di Azure e usi Visual Studio 2013 con Azure SDK. Se non si ha una sottoscrizione di Azure, è possibile ottenere una [versione di prova gratuita][Free Trial]. Assicurarsi di [installare e configurare Azure PowerShell versione 0.8.7 o successiva][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-worker-role"></a>Passaggio 1: Creare un ruolo di lavoro
1. Avviare **Visual Studio 2013**.
2. Creare un nuovo progetto **Servizio cloud di Azure** dal modello **Cloud** per .NET Framework 4.5.  Assegnare al progetto il nome "WadExample" e fare clic su OK.
3. Selezionare **Ruolo di lavoro** e fare clic su OK. Verrà creato il progetto.
4. In **Esplora soluzioni** fare doppio clic sul file delle proprietà **WorkerRole1**.
5. Nella scheda **Configurazione** deselezionare **Abilita diagnostica** per disabilitare Diagnostica 1.0 (Azure SDK 2.4 e versioni precedenti).
6. Compilare la soluzione per verificare che non ci siano errori.

### <a name="step-2-instrument-your-code"></a>Passaggio 2: Instrumentare il codice
Sostituire il contenuto del file WorkerRole.cs con il codice seguente. La classe SampleEventSourceWriter, ereditata dalla [classe EventSource][EventSource Class], implementa quattro metodi di registrazione: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. Il primo parametro per il metodo **WriteEvent** definisce l'ID per il rispettivo evento. Il metodo Run implementa un ciclo infinito che chiama ognuno dei metodi di registrazione implementati nella classe **SampleEventSourceWriter** ogni 10 secondi.

```csharp
using Microsoft.WindowsAzure.ServiceRuntime;
using System;
using System.Diagnostics;
using System.Diagnostics.Tracing;
using System.Net;
using System.Threading;

namespace WorkerRole1
{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
}
```


### <a name="step-3-deploy-your-worker-role"></a>Passaggio 3: Distribuire il ruolo di lavoro

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Distribuire il ruolo di lavoro in Azure da Visual Studio selezionando il progetto **WadExample** in Esplora soluzioni e quindi scegliendo **Pubblica** dal menu **Compila**.
2. Scegliere la propria sottoscrizione.
3. Nella finestra di dialogo **Impostazioni di pubblicazione Microsoft Azure** selezionare **Crea nuovo**.
4. Nella finestra di dialogo **Crea servizio cloud e account di archiviazione** immettere un valore in **Nome** (ad esempio, "WadExample") e selezionare un'area o un gruppo di affinità.
5. Impostare **Ambiente** su **Gestione temporanea**.
6. Modificare le altre **impostazioni** nel modo appropriato e fare clic su **Pubblica**.
7. Al termine della distribuzione, verificare nel portale di Azure classico che lo stato del servizio cloud sia **In esecuzione** .

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Passaggio 4: Creare il file di configurazione della diagnostica e installare l'estensione
1. Scaricare la definizione dello schema del file di configurazione pubblico eseguendo il comando PowerShell seguente:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Aggiungere un file XML al progetto **WorkerRole1** facendo clic con il pulsante destro del mouse sul progetto **WorkerRole1** e scegliendo **Aggiungi** -> **Nuovo elemento** -> **Elementi di Visual C#** -> **Dati** -> **File XML**. Assegnare al file il nome "WadExample.xml".

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Associare WadConfig.xsd al file di configurazione. Assicurarsi che la finestra dell'editor di WadExample.xml sia la finestra attiva. Premere **F4** per aprire la finestra **Proprietà**. Fare clic sulla proprietà **Schemi** nella finestra **Proprietà**. Fare clic su **...** in the **Schemi** . Fare clic su **Aggiungi** , passare al percorso in cui si è salvato il file XSD e selezionare il file WadConfig.xsd. Fare clic su **OK**.

4. Sostituire i contenuti del file di configurazione WadExample.xml con il codice XML seguente e salvare il file. Questo file di configurazione definisce due contatori delle prestazioni per la raccolta: uno per l'uso della CPU e uno per l'uso della memoria. La configurazione definisce quindi quattro eventi corrispondenti ai metodi della classe SampleEventSourceWriter.

```xml
<?xml version="1.0" encoding="utf-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <WadCfg>
    <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      <PerformanceCounters scheduledTransferPeriod="PT1M">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      </PerformanceCounters>
      <EtwProviders>
        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          <Event id="1" eventDestination="EnumsTable"/>
          <Event id="2" eventDestination="MessageTable"/>
          <Event id="3" eventDestination="SetOtherTable"/>
          <Event id="4" eventDestination="HighFreqTable"/>
          <DefaultEvents eventDestination="DefaultTable" />
        </EtwEventSourceProviderConfiguration>
      </EtwProviders>
    </DiagnosticMonitorConfiguration>
  </WadCfg>
</PublicConfig>
```

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Passaggio 5: Installare la diagnostica nel ruolo di lavoro
I cmdlet di PowerShell per la gestione della diagnostica in un ruolo Web o di lavoro sono: : Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension e Remove-AzureServiceDiagnosticsExtension.

1. Aprire Azure PowerShell.
2. Eseguire lo script per installare la diagnostica nel ruolo di lavoro (sostituire *StorageAccountKey* con la chiave dell'account di archiviazione di wadexample):

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Passaggio 6: Esaminare i dati di telemetria
In **Esplora server** di Visual Studio passare all'account di archiviazione di wadexample. Dopo 5 minuti dall'inizio dell'esecuzione del servizio cloud, dovrebbero essere visualizzate le tabelle **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Fare doppio clic su una delle tabelle per visualizzare i dati di telemetria raccolti.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Schema del file di configurazione
Il file di configurazione della diagnostica definisce i valori usati per inizializzare le impostazioni di diagnostica quando viene avviato il monitor di diagnostica. Vedere il [riferimento allo schema più recente](https://msdn.microsoft.com/library/azure/mt634524.aspx) per i valori validi ed alcuni esempi.

## <a name="troubleshooting"></a>Risoluzione dei problemi
Se si verificano problemi, vedere l'argomento relativo alla [risoluzione dei problemi di Diagnostica di Azure](../azure-diagnostics-troubleshooting.md) per informazioni sui problemi comuni.

## <a name="next-steps"></a>Passaggi successivi
[Vedere un elenco di articoli sulla diagnostica di Azure relativi a macchine virtuali](../azure-diagnostics.md#cloud-services-using-azure-diagnostics) per modificare i dati raccolti, risolvere i problemi o ottenere altre informazioni sulla diagnostica in generale.

[EventSource Class]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: http://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/


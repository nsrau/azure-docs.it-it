---
title: Come usare la diagnostica di Azure nelle macchine virtuali | Microsoft Docs
description: Uso della diagnostica di Azure per raccogliere dati dalle macchine virtuali di Azure per debug, valutazione delle prestazioni, monitoraggio, analisi del traffico e altro ancora.
services: virtual-machines
documentationcenter: .net
author: rboucher
manager: jwhit
editor: ''

ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: robb

---
# Abilitare la diagnostica nelle macchine virtuali di Azure
Vedere [Panoramica di Diagnostica di Azure](azure-diagnostics.md) per un'introduzione a Diagnostica di Azure.

## Come abilitare la diagnostica in una macchina virtuale
Questa procedura dettagliata descrive come installare in remoto la diagnostica in una macchina virtuale di Azure da un computer di sviluppo. Si apprenderà anche come implementare un'applicazione che viene eseguita in una macchina virtuale di Azure ed emette i dati di telemetria con la [classe EventSource][classe EventSource] .NET. Il modulo Diagnostica Azure viene usato per raccogliere la telemetria e memorizzarla in un account di archiviazione di Azure.

### Prerequisiti
In questa procedura si presuppone che l'utente abbia una sottoscrizione di Azure e usi Visual Studio 2013 con Azure SDK. Se non si dispone di una sottoscrizione di Azure, è possibile iscriversi alla [versione di prova gratuita][versione di prova gratuita]. Assicurarsi di [installare e configurare Azure PowerShell versione 0.8.7 o successiva][installare e configurare Azure PowerShell versione 0.8.7 o successiva].

### Passaggio 1: Creare una macchina virtuale
1. Nel computer di sviluppo avviare Visual Studio 2013.
2. In **Esplora server** di Visual Studio espandere **Azure**, fare clic con il pulsante destro del mouse su **Macchine virtuali**, quindi selezionare **Crea macchina virtuale**.
3. Selezionare la sottoscrizione di Azure nella finestra di dialogo **Scegli sottoscrizione** e fare clic su **Avanti**.
4. Selezionare **Windows Server 2012 R2 Datacenter, novembre 2014** nella finestra di dialogo **Selezionare un'immagine della macchina virtuale** e fare clic su **Avanti**.
5. In **Impostazioni di base della macchina virtuale** impostare il nome della macchina virtuale su "wadexample". Impostare il nome utente e la password dell'amministratore e fare clic su **Avanti**.
6. Nella finestra di dialogo **Impostazioni servizio cloud** creare un nuovo servizio cloud denominato "wadexampleVM". Creare un nuovo account di archiviazione denominato "wadexample" e fare clic su **Avanti**.
7. Fare clic su **Create**.

### Passaggio 2: Creare l'applicazione
1. Nel computer di sviluppo avviare Visual Studio 2013.
2. Creare una nuova applicazione console Visual C# per .NET Framework 4.5. Assegnare al progetto il nome "WadExampleVM". ![CloudServices\_diag\_new\_project](./media/virtual-machines-dotnet-diagnostics/NewProject.png)
3. Sostituire il contenuto del file Program.cs con il codice seguente. La classe **SampleEventSourceWriter** implementa quattro metodi di registrazione: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. Il primo parametro per il metodo WriteEvent definisce l'ID per il rispettivo evento. Il metodo Run implementa un ciclo infinito che chiama ognuno dei metodi di registrazione implementati nella classe **SampleEventSourceWriter** ogni 10 secondi.
   
     using System;
     using System.Diagnostics;
     using System.Diagnostics.Tracing;
     using System.Threading;
   
     namespace WadExampleVM
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
   
     class Program
     {
     static void Main(string[] args)
     {
   
         Trace.TraceInformation("My application entry point called");
   
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
     }
     }
4. Salvare il file e scegliere **Compila soluzione** dal menu **Compila** per compilare il codice.

### Passaggio 3: Distribuire l'applicazione
1. Fare clic con il pulsante destro del mouse sul progetto **WadExampleVM** in **Esplora soluzioni** e scegliere **Apri cartella in Esplora file**.
2. Passare alla cartella *bin\\Debug* e copiare tutti i file (WadExampleVM.*).
3. In **Esplora server** fare clic con il pulsante destro del mouse sulla macchina virtuale e scegliere **Connessione tramite desktop remoto**.
4. Una volta connessi alla macchina virtuale, creare una cartella denominata WadExampleVM e incollare i file dell'applicazione nella cartella.
5. Avviare l'applicazione WadExampleVM.exe. Verrà visualizzata una finestra della console vuota.

### Passaggio 4: Creare la configurazione per la diagnostica e installare l'estensione
1. Scaricare la definizione dello schema del file di configurazione pubblico nel computer di sviluppo eseguendo il comando PowerShell seguente:
   
     (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
2. Aprire un nuovo file XML in Visual Studio, in un progetto già aperto o in un'istanza di Visual Studio senza progetti aperti In Visual Studio selezionare **Aggiungi** -> **Nuovo elemento…** -> **Elementi Visual C# ** -> **Dati** -> **File XML**. Assegnare al file il nome "WadExample.xml".
3. Associare WadConfig.xsd al file di configurazione. Assicurarsi che la finestra dell'editor di WadExample.xml sia la finestra attiva. Premere **F4** per aprire la finestra **Proprietà**. Fare clic sulla proprietà **Schemi** nella finestra **Proprietà**. Fare clic su **…** nella proprietà **Schemi**. Fare clic sul pulsante **Aggiungi**, passare al percorso in cui si è salvato il file XSD e selezionare il file WadConfig.xsd. Fare clic su **OK**.
4. Sostituire i contenuti del file di configurazione WadExample.xml con il codice XML seguente e salvare il file. Questo file di configurazione definisce due contatori delle prestazioni per la raccolta: uno per l'uso della CPU e uno per l'uso della memoria. La configurazione definisce quindi quattro eventi corrispondenti ai metodi della classe SampleEventSourceWriter.

```
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

### Passaggio 5: Installare in remoto la diagnostica nella macchina virtuale di Azure
I cmdlet di PowerShell per la gestione della diagnostica in una macchina virtuale sono: Set-AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension e Remove-AzureVMDiagnosticsExtension.

1. Nel computer dello sviluppatore aprire Azure PowerShell.
2. Eseguire lo script e installare Diagnostica nella macchina virtuale (sostituire *StorageAccountKey* con la chiave dell'account di archiviazione wadexamplevm):
   
     $storage_name = "wadexamplevm"
     $key = "<StorageAccountKey>"
     $config_path="c:\users<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
     $service_name="wadexamplevm"
     $vm_name="WadExample"
     $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
     $VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
     $VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
     $VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM

### Passaggio 6: Esaminare i dati di telemetria
In **Esplora server** di Visual Studio passare all'account di archiviazione di wadexample. Dopo che la macchina virtuale è rimasta in esecuzione per circa 5 minuti, vengono visualizzate le tabelle **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Fare doppio clic su una delle tabelle per visualizzare i dati di telemetria raccolti.

![CloudServices\_diag\_wadexamplevm\_tables](./media/virtual-machines-dotnet-diagnostics/WadExampleVMTables.png)

## Schema del file di configurazione
Il file di configurazione della diagnostica definisce i valori usati per inizializzare le impostazioni di diagnostica quando viene avviato il monitor di diagnostica. Vedere il [riferimento allo schema più recente](https://msdn.microsoft.com/library/azure/mt634524.aspx) per i valori validi ed alcuni esempi.

## Risoluzione dei problemi
Per altre informazioni, vedere [Risoluzione dei problemi di Diagnostica di Azure](azure-diagnostics-troubleshooting.md)

## Passaggi successivi
[Vedere un elenco di articoli sulla diagnostica di Azure relativi a macchine virtuali ](azure-diagnostics.md#virtual-machines-using-azure-diagnostics) per modificare i dati raccolti, risolvere i problemi o ottenere altre informazioni sulla diagnostica in generale.

[classe EventSource]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[versione di prova gratuita]: http://azure.microsoft.com/pricing/free-trial/
[installare e configurare Azure PowerShell versione 0.8.7 o successiva]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

<!---HONumber=AcomDC_0302_2016-->
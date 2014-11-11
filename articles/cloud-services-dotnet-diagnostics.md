<properties linkid="dev-net-commons-tasks-diagnostics" urlDisplayName="Diagnostics" pageTitle="How to use diagnostics (.NET) - Azure feature guide" metaKeywords="Azure diagnostics monitoring,logs crash dumps C#" description="Learn how to use diagnostic data in Azure for debugging, measuring performance, monitoring, traffic analysis, and more." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Enabling Diagnostics in Azure" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi" />

# Abilitazione di Diagnostica in servizi cloud e macchine virtuali di Azure

Diagnostica Azure consente di raccogliere i dati di diagnostica da un ruolo di lavoro, da un ruolo Web o da una macchina virtuale in esecuzione in Azure. Questa guida descrive la modalità d'uso di Diagnostica Azure 1.2. Per altre linee guida dettagliate sulla creazione di una strategia di registrazione e traccia e sull'uso della diagnostica e di altre tecniche per risolvere i problemi e ottimizzare le applicazioni Azure, vedere [Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure][Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure].

## Sommario

-   [Panoramica][Panoramica]
-   [Come abilitare Diagnostica in un ruolo di lavoro][Come abilitare Diagnostica in un ruolo di lavoro]
-   [Come abilitare Diagnostica in una macchina virtuale][Come abilitare Diagnostica in una macchina virtuale]
-   [File e schema di configurazione di esempio][File e schema di configurazione di esempio]
-   [Risoluzione dei problemi][Risoluzione dei problemi]
-   [Domande frequenti][Domande frequenti]
-   [Confronto tra Diagnostica Azure 1.0 e 1.2][Confronto tra Diagnostica Azure 1.0 e 1.2]
-   [Risorse aggiuntive][Risorse aggiuntive]

## <a name="overview"></a><span class="short-header">Panoramica</span>Panoramica

Diagnostica Azure 1.2 è un'estensione di Azure che consente di raccogliere dati di telemetria di diagnostica da un ruolo di lavoro, da un ruolo Web o da una macchina virtuale in esecuzione in Azure. I dati di telemetria sono archiviati in un account di Azure e possono essere usati per il debug e la risoluzione dei problemi, per la misurazione delle prestazioni, per il monitoraggio dell'utilizzo delle risorse, per l'analisi del traffico e la pianificazione della capacità e per il controllo.

Rispetto alla precedente versione 1.0, Diagnostica 1.2 presenta tre importanti differenze:

1.  Diagnostica 1.2 può essere distribuita su macchine virtuali, oltre che su servizi cloud.
2.  Diagnostica 1.0 fa parte di Azure SDK e viene distribuita insieme al servizio cloud. Diagnostica 1.2 è un'estensione e viene distribuita separatamente.
3.  Diagnostica 1.2 abilita la raccolta di eventi ETW e EventSource .NET.

Per un confronto più dettagliato, vedere [Confronto tra Diagnostica Azure 1.0 e 1.2][Confronto tra Diagnostica Azure 1.0 e 1.2] al termine di questo articolo.

Diagnostica Azure è in grado di raccogliere i seguenti tipi di telemetria:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Origine dati</strong></td>
			<td><strong>Descrizione</strong></td>
	</tr>
	<tr>
		<td>Log IIS</td>
		<td>Informazioni sui siti Web IIS.</td>            
	</tr>
	<tr>
		<td>Log dell'infrastruttura diagnostica di Azure</td>
		<td>Informazioni su Diagnostica.</td>            
	</tr>
	<tr>
		<td>Log delle richieste non riuscite di IIS</td>
		<td>Informazioni sulle richieste non riuscite inviate a un'applicazione o a un sito IIS.</td>            
	</tr>
	<tr>
		<td>Log degli eventi di Windows</td>
		<td> Informazioni inviate al sistema di registrazione degli eventi di Windows.</td>        
	</tr>
	<tr>
		<td>Contatori delle prestazioni</td>
		<td>Contatori del sistema operativo e personalizzati delle prestazioni.</td>            
	</tr>
	<tr>
		<td>Dump di arresto anomalo del sistema</td>
		<td>Informazioni sullo stato del processo in caso di arresto anomalo di un'applicazione.</td>            
	</tr>
	<tr>
		<td>Log degli errori personalizzati</td>
		<td>Log creati dall'applicazione o dal servizio.</td>            
	</tr>
	<tr>
		<td>EventSource .NET</td>
		<td>Eventi generati dal codice mediante la <a href="http://msdn.microsoft.com/it-it/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">classe EventSource</a> .NET.</td>            
	</tr>
	<tr>
		<td>ETW basato su manifesto</td>
		<td> Eventi ETW generati da un processo qualsiasi.</td>            
	</tr>
</tbody>
</table>

## <a name="worker-role"></a><span class="short-header">Abilitazione di Diagnostica in un ruolo di lavoro</span>Come abilitare Diagnostica in un ruolo di lavoro

Questa procedura dettagliata illustra come implementare un ruolo di lavoro di Azure che emette dati di telemetria usando la classe EventSource .NET. Diagnostica Azure consente di raccogliere dati di telemetria e di memorizzarli in un account di archiviazione di Azure. Quando si crea un ruolo di lavoro, Visual Studio abilita automaticamente Diagnostica 1.0 come parte della soluzione. Le istruzioni seguenti descrivono i processi di creazione del ruolo di lavoro, di disabilitazione di Diagnostica 1.0 nella soluzione e di distribuzione di Diagnostica 1.2 al ruolo di lavoro.

### Prerequisiti

Questo articolo presuppone la disponibilità di una sottoscrizione Azure e l'uso di Visual Studio 2013 con Azure SDK. Se non si ha già una sottoscrizione Azure, è possibile iscriversi per accedere alla [versione di valutazione gratuita][versione di valutazione gratuita]. Assicurarsi di [installare e configurare Azure PowerShell versione 0.8.7 o successiva][installare e configurare Azure PowerShell versione 0.8.7 o successiva].

### Passaggio 1: Creare un ruolo di lavoro

1.  Avviare **Visual Studio 2013**.
2.  Creare un nuovo progetto di **servizio cloud di Windows Azure** a partire dal modello **Cloud** per .NET Framework 4.5. Assegnare al progetto il nome "WadExample".
3.  Selezionare **Ruolo di lavoro**.
4.  In **Esplora soluzioni** fare doppio clic sul file delle proprietà **WorkerRole1**.
5.  Nella scheda **Configurazione** deselezionare **Abilita diagnostica** per disabilitare Diagnostica 1.0.
6.  Compilare la soluzione per verificare che non siano presenti errori.

### Passaggio 2: Instrumentare il codice

Sostituire il contenuto del file WorkerRole.cs con il codice seguente. La classe SampleEventSourceWriter, ereditata dalla [classe EventSource][classe EventSource], implementa quattro metodi di registrazione: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. Il primo parametro del metodo **WriteEvent** definisce l'ID del rispettivo evento. Il metodo Run implementa un ciclo infinito che chiama ciascuno dei metodi di registrazione implementati nella classe **SampleEventSourceWriter** ogni 10 secondi.

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

### Passaggio 3: Distribuire il ruolo di lavoro

1.  Distribuire il ruolo di lavoro ad Azure da Visual Studio selezionando il progetto **WadExample** e scegliendo **Pubblica** dal menu **Compila**.
2.  Scegliere la propria sottoscrizione.
3.  Nella finestra di dialogo **Impostazioni di pubblicazione di Microsoft Azure** selezionare **<create new…>**.
4.  Nella finestra di dialogo relativa alla creazione di un servizio cloud e di un account di archiviazione immettere un nome (ad esempio "WadExample") e selezionare un'area geografica o un gruppo di affinità.
5.  Impostare **Ambiente** su **Gestione temporanea**.
6.  Modificare qualsiasi altro valore del campo **Impostazioni** nel modo appropriato, quindi fare clic su **Pubblica**.
7.  Una volta completata la distribuzione, verificare nel portale di Azure che lo stato del servizio cloud sia **In esecuzione**.

### Passaggio 4: Creare il file di configurazione di Diagnostica e installare l'estensione

1.  Scaricare la definizione dello schema del file di configurazione pubblico eseguendo il seguente comando di PowerShell:
2.  (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

3.  Aggiungere un file XML al progetto **WorkerRole1** facendo clic con il pulsante destro del mouse sul progetto **WorkerRole1**, selezionare **Aggiungi** -\> **Nuovo elemento** -\> **Elementi Visual C#** -\> **Dati** -\> **File XML**. Assegnare al file il nome "WadExample.xml".

    ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)

4.  Associare WadConfig.xsd al file di configurazione. Assicurarsi che la finestra dell'editor di WadExample.xml sia quella attiva. Premere **F4** per aprire la finestra **Proprietà**. Fare clic sulla proprietà **Schemas** nella finestra **Proprietà**. Fare clic sui puntini di sospensione (**…**) nella proprietà **Schemas**. Fare clic sul pulsante **Aggiungi** e spostarsi nella posizione in cui è stato salvato il file XSD, selezionare il file WadConfig.xsd, quindi fare clic su **OK**.
5.  Sostituire il contenuto del file di configurazione WadExample.xml con il codice XML seguente e salvare il file. Questo file di configurazione definisce due contatori delle prestazioni per la raccolta di dati relativi all'utilizzo della CPU e della memoria. La configurazione definisce quindi quattro eventi corrispondenti ai metodi della classe SampleEventSourceWriter.

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

### Passaggio 5: Installare Diagnostica nel ruolo di lavoro

I cmdlet di PowerShell per la gestione di Diagnostica su un ruolo Web o di lavoro sono: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension e Remove-AzureServiceDiagnosticsExtension.

1.  Aprire Windows Azure PowerShell.
2.  Eseguire lo script e installare Diagnostica nel ruolo di lavoro (sostituire *StorageAccountKey* con la chiave dell'account di archiviazione wadexample):

        $storage_name = "wadexample"
        $key = "<StorageAccountKey>"
        $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
        $service_name="wadexample"
        $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
        Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging

### Passaggio 6: Esaminare i dati di telemetria

In **Esplora server** di Visual Studio passare all'account di archiviazione wadexample. Dopo che il servizio cloud è rimasto in esecuzione per circa 5 minuti, vengono visualizzate le tabelle **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Fare doppio clic su una delle tabelle per visualizzare i dati di telemetria che sono stati raccolti.
 ![CloudServices\_diag\_tables][CloudServices\_diag\_tables]

## <a name="virtual-machine"></a><span class="short-header">Abilitazione di Diagnostica in una macchina virtuale</span>Come abilitare Diagnostica in una macchina virtuale

Questa procedura dettagliata illustra come installare Diagnostica in remoto su una macchina virtuale di Azure da un computer di sviluppo. Viene inoltre spiegato come implementare un'applicazione che viene eseguita sulla macchina virtuale di Azure ed emette dati di telemetria usando la [classe EventSource][classe EventSource] .NET. Diagnostica Azure consente di raccogliere dati di telemetria e di memorizzarli in un account di archiviazione di Azure.

### Prerequisiti

Questa procedura dettagliata presuppone la disponibilità di una sottoscrizione Azure e l'uso di Visual Studio 2013 con Azure SDK. Se non si ha già una sottoscrizione Azure, è possibile iscriversi per accedere alla [versione di valutazione gratuita][versione di valutazione gratuita]. Assicurarsi di [installare e configurare Azure PowerShell versione 0.8.7 o successiva][installare e configurare Azure PowerShell versione 0.8.7 o successiva].

### Passaggio 1: Creare una macchina virtuale

1.  Sul computer di sviluppo avviare Visual Studio 2013.
2.  In **Esplora server** di Visual Studio fare clic con il pulsante destro del mouse su **Windows Azure**, quindi selezionare **Macchina virtuale** -\> **Crea macchina virtuale**.
3.  Selezionare la propria sottoscrizione Azure nella finestra di dialogo **Scegli sottoscrizione** ,quindi fare clic su **Avanti**.
4.  Selezionare **Windows Server 2012 R2 Datacenter** nella finestra di dialogo **Selezionare un'immagine della macchina virtuale**, quindi fare clic su **Avanti**.
5.  In **Impostazioni di base della macchina virtuale** impostare il nome della macchina virtuale su "wadexample". Impostare il nome utente e la password dell'amministratore, quindi fare clic su **Avanti**.
6.  Nella finestra di dialogo **Impostazioni servizio cloud** creare un nuovo servizio cloud e assegnare al servizio il nome "wadexampleVM". Creare un nuovo account di archiviazione denominato "wadexample" e fare clic su **Avanti**.
7.  Fare clic su **Create**.

### Passaggio 2: Creare l'applicazione

1.  Sul computer di sviluppo avviare Visual Studio 2013.
2.  Creare una nuova applicazione console in Visual C# per .NET Framework 4.5. Assegnare al progetto il nome "WadExampleVM".
    ![CloudServices\_diag\_new\_project][CloudServices\_diag\_new\_project]
3.  Sostituire il contenuto del file Program.cs con il codice seguente. La classe **SampleEventSourceWriter** implementa quattro metodi di registrazione: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. Il primo parametro del metodo WriteEvent definisce l'ID del rispettivo evento. Il metodo Run implementa un ciclo infinito che chiama ciascuno dei metodi di registrazione implementati nella classe **SampleEventSourceWriter** ogni 10 secondi.

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

4.  Salvare il file e selezionare **Compila soluzione** dal menu **Compila** per compilare il codice.

### Passaggio 3: Distribuire l'applicazione

1.  Fare clic con il pulsante destro del mouse sul progetto **WadExampleVM** in **Esplora soluzioni**, quindi scegliere **Apri cartella** in Esplora file.
2.  Passare alla cartella *bin\\Debug* e copiare tutti i file (WadExampleVM\*).
3.  In **Esplora server** fare clic con il pulsante destro del mouse e scegliere **Connetti tramite Desktop remoto**.
4.  Una volta eseguita la connessione alla macchina virtuale, creare una cartella denominata WadExampleVM e incollare i file dell'applicazione in tale cartella.
5.  Avviare l'applicazione WadExampleVM.exe. Viene visualizzata una finestra della console vuota.

### Passaggio 4: Creare la configurazione di Diagnostica e installare l'estensione

1.  Scaricare la definizione dello schema del file di configurazione pubblico nel computer di sviluppo eseguendo il seguente comando di PowerShell:

        (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.  Aprire un nuovo file XML in Visual Studio, in un progetto già aperto o in un'istanza di Visual Studio senza progetti aperti. In Visual Studio selezionare **Aggiungi** -\> **Nuovo elemento** -\> **Elementi Visual C#** -\> **Dati** -\> **File XML**. Assegnare al file il nome "WadExample.xml".
3.  Associare WadConfig.xsd al file di configurazione. Assicurarsi che la finestra dell'editor di WadExample.xml sia quella attiva. Premere **F4** per aprire la finestra **Proprietà**. Fare clic sulla proprietà **Schemas** nella finestra **Proprietà**. Fare clic sui puntini di sospensione (**…**) nella proprietà **Schemas**. Fare clic sul pulsante **Aggiungi** e spostarsi nella posizione in cui è stato salvato il file XSD, selezionare il file WadConfig.xsd, quindi fare clic su **OK**.
4.  Sostituire il contenuto del file di configurazione WadExample.xml con il codice XML seguente e salvare il file. Questo file di configurazione definisce due contatori delle prestazioni per la raccolta di dati relativi all'utilizzo della CPU e della memoria. La configurazione definisce quindi quattro eventi corrispondenti ai metodi della classe SampleEventSourceWriter.

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

### Passaggio 5: Installare Diagnostica in remoto sulla macchina virtuale di Azure

I cmdlet di PowerShell per la gestione di Diagnostica su una macchina virtuale sono: Set-AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension e Remove-AzureVMDiagnosticsExtension.

1.  Sul computer di sviluppo aprire Windows Azure PowerShell.
2.  Eseguire lo script e installare Diagnostica nella macchina virtuale (sostituire *StorageAccountKey* con la chiave dell'account di archiviazione wadexamplevm):

        $storage_name = "wadexamplevm"
        $key = "<StorageAccountKey>"
        $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"
        $service_name="wadexamplevm"
        $vm_name="WadExample"
        $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
        $VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
        $VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
        $VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM

### Passaggio 6: Esaminare i dati di telemetria

In **Esplora server** di Visual Studio passare all'account di archiviazione wadexample. Dopo che la macchina virtuale è rimasta in esecuzione per circa 5 minuti, vengono visualizzate le tabelle **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Fare doppio clic su una delle tabelle per visualizzare i dati di telemetria che sono stati raccolti.
 ![CloudServices\_diag\_wadexamplevm\_tables][CloudServices\_diag\_wadexamplevm\_tables]

## <a name="configuration-file-schema"></a><span class="short-header"> File e schema di configurazione di esempio</span>Schema del file di configurazione

Il file di configurazione di Diagnostica definisce i valori usati per inizializzare le impostazioni di diagnostica quando viene avviato il monitor di diagnostica. Un file di configurazione di esempio e la documentazione dettagliata del relativo schema sono disponibili qui: [Schema di configurazione di Diagnostica Azure 1.2][Schema di configurazione di Diagnostica Azure 1.2].

## <a name="troubleshooting"></a><span class="short-header">Risoluzione dei problemi</span>Risoluzione dei problemi

### Mancato avvio di Diagnostica Azure

Diagnostica è composta da due componenti: un plug-in dell'agente guest e l'agente di monitoraggio. I file di log del plug-in dell'agente guest si trovano nel file seguente:

*%SystemDrive%\\ WindowsAzure\\Logs\\Plugins\\Microsoft.Azure.Diagnostics.PaaSDiagnostics\\<diagnosticsversion>*\\CommandExecution.log

Il plug-in restituisce i seguenti codici di errore:

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><strong>Codice di uscita</strong></td>
<td align="left"><strong>Descrizione</strong></td>
</tr>
<tr class="even">
<td align="left">0</td>
<td align="left">Completamento della procedura.</td>
</tr>
<tr class="odd">
<td align="left">-1</td>
<td align="left">Errore generico.</td>
</tr>
<tr class="even">
<td align="left">-2</td>
<td align="left"><p>Impossibile caricare il file rcf.</p>
<p>Questo è un errore interno che dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente, in modo non corretto, sulla macchina virtuale.</p></td>
</tr>
<tr class="odd">
<td align="left">-3</td>
<td align="left"><p>Impossibile caricare il file di configurazione di Diagnostica.</p>
<p>Soluzione: questo errore si verifica quando un file di configurazione non ha superato la convalida dello schema. La soluzione consiste nel fornire un file di configurazione conforme allo schema.</p></td>
</tr>
<tr class="even">
<td align="left">-4</td>
<td align="left"><p>La directory delle risorse locali è già usata da un'altra istanza dell'agente di monitoraggio di Diagnostica.</p>
<p>Soluzione: specificare un valore differente per <strong>LocalResourceDirectory</strong>.</p></td>
</tr>
<tr class="odd">
<td align="left">-6</td>
<td align="left"><p>L'utilità di avvio del plug-in dell'agente guest ha tentato di avviare Diagnostica con una riga di comando non valida.</p>
<p>Questo è un errore interno che dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente, in modo non corretto, sulla macchina virtuale.</p></td>
</tr>
<tr class="even">
<td align="left">-10</td>
<td align="left">Il plug-in di Diagnostica ha generato un'eccezione non gestita.</td>
</tr>
<tr class="odd">
<td align="left">-11</td>
<td align="left"><p>L'agente guest non è stato in grado di creare il processo responsabile dell'avvio e del monitoraggio dell'agente di monitoraggio.</p>
<p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare nuovi processi.</p></td>
</tr>
<tr class="even">
<td align="left">-101</td>
<td align="left"><p>Argomenti non validi durante la chiamata del plug-in di Diagnostica.</p>
<p>Questo è un errore interno che dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente, in modo non corretto, sulla macchina virtuale.</p></td>
</tr>
<tr class="odd">
<td align="left">-102</td>
<td align="left"><p>Il processo del plug-in non è in grado di inizializzarsi.</p>
<p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare nuovi processi.</p></td>
</tr>
<tr class="even">
<td align="left">-103</td>
<td align="left"><p>Il processo del plug-in non è in grado di inizializzarsi. In particolare, non è in grado di creare l'oggetto logger.</p>
<p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare nuovi processi.</p></td>
</tr>
<tr class="odd">
<td align="left">-104</td>
<td align="left"><p>Impossibile caricare il file rcf fornito dall'agente guest.</p>
<p>Questo è un errore interno che dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente, in modo non corretto, sulla macchina virtuale.</p></td>
</tr>
<tr class="even">
<td align="left">-105</td>
<td align="left"><p>Il plug-in di Diagnostica non è in grado di aprire il file di configurazione di Diagnostica.</p>
<p>Questo è un errore interno che dovrebbe verificarsi solo se il plug-in di Diagnostica viene richiamato manualmente, in modo non corretto, sulla macchina virtuale.</p></td>
</tr>
<tr class="odd">
<td align="left">-106</td>
<td align="left"><p>Impossibile leggere il file di configurazione di Diagnostica.</p>
<p>Soluzione: questo errore si verifica quando un file di configurazione non ha superato la convalida dello schema. La soluzione consiste nel fornire un file di configurazione conforme allo schema. Il file XML fornito all'estensione Diagnostica si trova nella cartella <em>%SystemDrive%\WindowsAzure\Config</em> sulla macchina virtuale. Aprire il file XML appropriato e cercare prima <strong>Microsoft.Azure.Diagnostics</strong>, quindi il campo <strong>xmlCfg</strong>. Poiché i dati hanno codifica base64, per visualizzare il file XML caricato da Diagnostica è necessario <a href="http://www.bing.com/search?q=base64+decoder">decodificarli</a>.</p></td>
</tr>
<tr class="even">
<td align="left">-107</td>
<td align="left"><p>La directory delle risorse passata all'agente di monitoraggio non è valida.</p>
<p>Questo è un errore interno che dovrebbe verificarsi solo se l'agente di monitoraggio viene richiamato manualmente, in modo non corretto, sulla macchina virtuale.</p></td>
</tr>
<tr class="odd">
<td align="left">-108</td>
<td align="left"><p>Impossibile convertire il file di configurazione di Diagnostica nel file di configurazione dell'agente di monitoraggio.</p>
<p>Questo è un errore interno che dovrebbe verificarsi solo se il plug-in di Diagnostica viene richiamato manualmente con un file di configurazione non valido.</p></td>
</tr>
<tr class="even">
<td align="left">-110</td>
<td align="left"><p>Errore di configurazione generale di Diagnostica.</p>
<p>Questo è un errore interno che dovrebbe verificarsi solo se il plug-in di Diagnostica viene richiamato manualmente con un file di configurazione non valido.</p></td>
</tr>
<tr class="odd">
<td align="left">-111</td>
<td align="left"><p>Impossibile avviare l'agente di monitoraggio.</p>
<p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti.</p></td>
</tr>
<tr class="even">
<td align="left">-112</td>
<td align="left">Errore generale:</td>
</tr>
</tbody>
</table>

### I dati di Diagnostica non vengono registrati nella risorsa di archiviazione

La causa più comune della mancata registrazione dei dati relativi agli eventi è la definizione non corretta delle informazioni sull'account di archiviazione.

Soluzione: correggere il file di configurazione e reinstallare Diagnostica.
Prima di essere caricati nell'account di archiviazione, i dati relativi agli eventi vengono archiviati nella cartella. Vedere sopra per informazioni dettagliate su **LocalResourceDirectory**.

Se nella cartella non sono presenti file, l'agente di monitoraggio non si avvia. Questo problema è causato in genere da un file di configurazione non valido e deve essere riportato nel file CommandExecution.log. Se l'agente di monitoraggio è in grado di raccogliere i dati relativi agli eventi, viene visualizzato un file .tsf per ciascuno degli eventi definiti nel file di configurazione.

L'agente di monitoraggio registra tutti gli errori rilevati nel file MaEventTable.tsf. Per esaminare il contenuto di questo file, eseguire il comando seguente:

        %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.[IaaS | PaaS]Diagnostics.2.0.0\Monitor\x64\table2csv maeventtable.tsf

Lo strumento genera un file denominato maeventtable.csv. È possibile aprire tale file ed esaminarne i log per verificare la presenza di errori.

## <a name="faq"></a><span class="short-header">Domande frequenti</span>Domande frequenti

Di seguito sono riportate alcune domande frequenti e le relative risposte:

**D.** Come è possibile aggiornare una soluzione Visual Studio da Diagnostica Azure 1.0 a Diagnostica Azure 1.1?

**R.** L'aggiornamento della soluzione Visual Studio da Diagnostica Azure 1.0 a Diagnostica Azure 1.1 (o versione successiva) è un processo manuale:

-   Disabilitare Diagnostica nella soluzione Visual Studio per evitare che Diagnostica 1.0 venga distribuita con il ruolo dell'utente.
-   Se il codice dell'utente usa un listener di traccia, modificarlo in modo che usi EventSource .NET. Diagnostica 1.1 e versioni successive non includono il supporto per un listener di traccia.
-   Modificare il processo di distribuzione per installare l'estensione Diagnostica 1.1.

**D.** Se l'estensione Diagnostica 1.1 è già installata sul ruolo o sulla macchina virtuale dell'utente, come si esegue l'aggiornamento a Diagnostica 1.2?

**R.** Se al momento dell'installazione di Diagnostica 1.1 è stato specificato –Version "1.", al prossimo riavvio il ruolo o la macchina virtuale verranno aggiornati automaticamente alla versione più recente che soddisfa l'espressione regolare "1.". Se al momento dell'installazione di Diagnostica 1.1 è stato specificato –Version "1.1", è possibile effettuare l'aggiornamento a una versione più recente eseguendo di nuovo il cmdlet Set- e specificando la versione che si desidera installare.

**D.** In che modo vengono assegnati i nomi alle tabelle?

**R.** I nomi delle tabelle vengono assegnati secondo lo schema seguente:

        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;

Di seguito è fornito un esempio:

        <EtwEventSourceProviderConfiguration provider=”prov1”>
          <Event id=”1” />
          <Event id=”2” eventDestination=”dest1” />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider=”prov2”>
          <DefaultEvents eventDestination=”dest2” />
        </EtwEventSourceProviderConfiguration>

Verranno generate quattro tabelle:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Evento</strong></td><td><strong>Nome tabella</strong></td></tr>
<tr>
<td>
provider=”prov1” \<Event id=”1” /\>

</td>
<td>
WADEvent+MD5(“prov1”)+”1”

</td>
</tr>
<tr>
<td>
provider=”prov1” \<Event id=”2” eventDestination=”dest1” /\>

</td>
<td>
WADdest1

</td>
</tr>
<tr>
<td>
provider=”prov1” \<DefaultEvents /\>

</td>
<td>
WADDefault+MD5(“prov1”)

</td>
</tr>
<tr>
<td>
provider=”prov2” \<DefaultEvents eventDestination=”dest2” /\>

</td>
<td>
WADdest2

</td>
</tr>
</table>
</tbody>
## <a name="comparing"></a><span class="short-header">Confronto tra Diagnostica Azure 1.0 e 1.2</span>Confronto tra Diagnostica Azure 1.0 e 1.2

La tabella seguente confronta le funzionalità supportate dalle versioni 1.0 e 1.1/1.2 di Diagnostica Azure:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Tipi di ruolo supportati</strong></td>
			<td><strong>Diagnostica 1.0</strong></td>
			<td><strong>Diagnostica 1.1/1.2</strong></td>
	</tr>

	<tr>
			<td>Ruolo Web</td>
			<td>Sì</td>
			<td>Sì</td>
	</tr>
	<tr>
			<td>Ruolo di lavoro</td>
			<td>Sì</td>
			<td>Sì</td>
	</tr>
	<tr>
			<td>IaaS</td>
			<td>No</td>
			<td>Sì</td>
	</tr>
</tbody>
</table>
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Configurazione e distribuzione</strong></td>
			<td><strong>Diagnostica 1.0</strong></td>
			<td><strong>Diagnostica 1.1/1.2</strong></td>
	</tr>

	<tr>
			<td>Integrazione con Visual Studio - Integrata nell'esperienza di sviluppo di ruoli Web o di lavoro di Azure.</td>
			<td>Sì</td>
			<td>No</td>
	</tr>
	<tr>
			<td>Script di PowerShell - Script per gestire l'installazione e la configurazione di Diagnostica nel ruolo.</td>
			<td>Sì</td>
			<td>Sì</td>
	</tr>
	
</tbody>
</table>


<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
	<tr>
			<td style="width: 100px;"><strong>Origine dati</strong></td>
			<td><strong>Raccolta predefinita</strong></td>
			<td><strong>Formato</strong></td>
			<td><strong>Descrizione</strong></td>
			<td><strong>Diagnostica 1.0</strong></td>
			<td><strong>Diagnostica 1.1/1.2</strong></td>
	</tr>
	<tr>
			<td>Log System.Diagnostics.Trace</td>
			<td>Sì</td>
			<td>Tabella</td>
			<td>Registra messaggi di traccia inviati dal codice al listener di traccia (è necessario aggiungere un listener di traccia al file web.config o app.config). I dati del log verranno trasferiti alla tabella di archiviazione WADLogsTable in base all'intervallo di trasferimento scheduledTransferPeriod. </td>
			<td>Sì</td>
			<td>No (usare EventSource</td>
	</tr>
	<tr>
			<td>IIS logs</td>
			<td>Sì</td>
			<td>BLOB</td>
			<td>Registra informazioni sui siti IIS. I dati del log verranno trasferiti al contenitore specificato in base all'intervallo di trasferimento scheduledTransferPeriod.</td>
			<td>Sì</td>
			<td>Sì</td>
	</tr>
	<tr>
			<td>Log dell'infrastruttura diagnostica di Azure</td>
			<td>Sì</td>
			<td>Tabella</td>
			<td>Registra informazioni sull'infrastruttura di diagnostica e sui moduli RemoteAccess e RemoteForwarder. I dati del log verranno trasferiti alla tabella di archiviazione WADDiagnosticInfrastructureLogsTable in base all'intervallo di trasferimento scheduledTransferPeriodtransfer.</td>
			<td>Sì</td>
			<td>Sì</td>
	</tr>
	<tr>
			<td>Log delle richieste non riuscite di IIS</td>
			<td>No</td>
			<td>BLOB</td>
			<td>Registra informazioni sulle richieste non riuscite inviate a un'applicazione o a un sito IIS. È inoltre necessario abilitare questa origine dati impostando le opzioni di traccia presenti in system.WebServer in Web.config. I dati del log verranno trasferiti al contenitore specificato in base all'intervallo di trasferimento scheduledTransferPeriod.</td>
			<td>Sì</td>
			<td>Sì</td>

	</tr>
	<tr>
			<td>Log degli eventi di Windows</td>
			<td>No</td>
			<td>Tabella</td>
			<td>Registra le informazioni sulle prestazioni del sistema operativo, dell'applicazione o del driver. I contatori delle prestazioni devono essere specificati in modo esplicito. Quando i contatori vengono aggiunti, i dati corrispondenti vengono trasferiti nella tabella di archiviazione WADPerformanceCountersTable in base all'intervallo di trasferimento scheduledTransferPeriod.</td>
			<td>Sì</td>
			<td>Sì</td>
	</tr>   
	<tr>
			<td>Contatori delle prestazioni</td>
			<td>No</td>
			<td>Tabella</td>
			<td>Registra le informazioni sulle prestazioni del sistema operativo, dell'applicazione o del driver. I contatori delle prestazioni devono essere specificati in modo esplicito. Quando i contatori vengono aggiunti, i dati corrispondenti vengono trasferiti nella tabella di archiviazione WADPerformanceCountersTable in base all'intervallo di trasferimento scheduledTransferPeriod.</td>
			<td>Sì</td>
			<td>Sì</td>
	</tr> 
	<tr>
			<td>Dump di arresto anomalo del sistema</td>
			<td>No</td>
			<td>BLOB</td>
			<td>Registra informazioni sullo stato del sistema operativo in caso di arresto anomalo del sistema. I minidump di arresto anomalo vengono raccolti localmente. È possibile abilitare i dump completi. I dati del log verranno trasferiti al contenitore specificato in base all'intervallo di trasferimento scheduledTransferPeriod. Poiché la maggior parte delle eccezioni è gestita da ASP.NET, questo è in genere utile solo per un ruolo di lavoro o una macchina virtuale.</td>
			<td>Sì</td>
			<td>Sì</td>
	</tr>
	<tr>
			<td>Log degli errori personalizzati</td>
			<td>No</td>
			<td>BLOB</td>
			<td>Grazie all'uso di risorse di archiviazione locali, i dati personalizzati possono essere registrati e trasferiti immediatamente al contenitore specificato.</td>
			<td>Sì</td>
			<td>Sì</td>
	</tr>
	<tr>
			<td>EventSource</td>
			<td>No</td>
			<td>Tabella</td>
			<td>Registra gli eventi generati dal codice usando la classe EventSource .NET.</td>
			<td>No</td>
			<td>Sì</td>
	</tr>      
	<tr>
			<td>ETW basato su manifesto</td>
			<td>No</td>
			<td>Tabella</td>
			<td>Eventi ETW generati da un processo qualsiasi.</td>
			<td>No</td>
			<td>Sì</td>
	</tr>
</tbody>
</table>

## <a name="additional"></a><span class="short-header">Risorse aggiuntive</span>Risorse aggiuntive

-   [Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure][Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure]
-   [Raccogliere dati di registrazione utilizzando Diagnostica Azure][Raccogliere dati di registrazione utilizzando Diagnostica Azure]
-   [Debug di un'applicazione Azure][Debug di un'applicazione Azure]
-   [Configurazione di Diagnostica Microsoft Azure][Configurazione di Diagnostica Microsoft Azure]

  [Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/hh771389.aspx
  [Panoramica]: #overview
  [Come abilitare Diagnostica in un ruolo di lavoro]: #worker-role
  [Come abilitare Diagnostica in una macchina virtuale]: #virtual-machine
  [File e schema di configurazione di esempio]: #configuration-file-schema
  [Risoluzione dei problemi]: #troubleshooting
  [Domande frequenti]: #faq
  [Confronto tra Diagnostica Azure 1.0 e 1.2]: #comparing
  [Risorse aggiuntive]: #additional
  [classe EventSource]: http://msdn.microsoft.com/it-it/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx
  [versione di valutazione gratuita]: http://azure.microsoft.com/it-it/pricing/free-trial/
  [installare e configurare Azure PowerShell versione 0.8.7 o successiva]: http://azure.microsoft.com/it-it/documentation/articles/install-configure-powershell/
  [Schema di configurazione di Diagnostica Azure 1.2]: http://msdn.microsoft.com/it-it/library/azure/dn782207.aspx
  [Raccogliere dati di registrazione utilizzando Diagnostica Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/gg433048.aspx
  [Debug di un'applicazione Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/ee405479.aspx
  [Configurazione di Diagnostica Microsoft Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/dn186185.aspx

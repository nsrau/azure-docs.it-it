<properties urlDisplayName="Diagnostics" pageTitle="Come usare la diagnostica (.NET) - Guida alle funzionalit&agrave; di Azure" metaKeywords="Azure diagnostics monitoring,logs crash dumps C#" description="Informazioni sull'uso dei dati di diagnostica in Azure per debug, valutazione delle prestazioni, monitoraggio, analisi del traffico e altro ancora." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Abilitazione della diagnostica in Azure" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/23/2014" ms.author="ryanwi" />

# Abilitazione della diagnostica nei servizi cloud e nelle macchine virtuali di Azure

Diagnostica Azure 1.2 consente di raccogliere i dati di diagnostica da un ruolo di lavoro, da un ruolo Web o da una macchina virtuale in esecuzione in Azure. Questa guida descrive come usare Diagnostica Azure 1.2. Per altre linee guida dettagliate sulla creazione di una strategia di registrazione e traccia e sull'utilizzo della diagnostica e di altre tecniche per risolvere i problemi e ottimizzare le applicazioni Azure, vedere [Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure][Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure].

## Sommario

-   [Panoramica][Panoramica]
-   [Come abilitare la diagnostica in un ruolo di lavoro][Come abilitare la diagnostica in un ruolo di lavoro]
-   [Come abilitare la diagnostica in una macchina virtuale][Come abilitare la diagnostica in una macchina virtuale]
-   [Schema e file di configurazione di esempio][Schema e file di configurazione di esempio]
-   [Risoluzione dei problemi][Risoluzione dei problemi]
-   [Domande frequenti][Domande frequenti]
-   [Confronto tra Diagnostica Azure 1.0 e 1.2][Confronto tra Diagnostica Azure 1.0 e 1.2]
-   [Risorse aggiuntive][Risorse aggiuntive]

## <a name="overview"></a><span class="short-header">Panoramica</span>Panoramica

Diagnostica Azure 1.2 è un'estensione di Azure che consente di raccogliere i dati di telemetria sulla diagnostica da un ruolo di lavoro, da un ruolo Web o da una macchina virtuale in esecuzione in Azure. I dati di telemetria vengono memorizzati in un account di archiviazione di Azure e possono essere usati per il debug e la risoluzione dei problemi, per la misurazione delle prestazioni, per il monitoraggio dell'uso delle risorse, per l'analisi del traffico e la pianificazione della capacità e per il controllo.

Se in passato si è usato Diagnostica versione 1.0, ci sono tre differenze sostanziali con Diagnostica 1.2:

1.  Il modulo Diagnostica 1.2 può essere implementato nelle macchine virtuali, oltre che nei servizi cloud.
2.  Il modulo Diagnostica 1.0 fa parte di Azure SDK e viene implementato insieme al servizio cloud. Il modulo Diagnostica 1.2 è un'estensione e viene implementato separatamente.
3.  Diagnostica 1.2 consente la raccolta di eventi EventSource .NET ed ETW.

Per un confronto più dettagliato, vedere [Confronto tra Diagnostica Azure 1.0 e 1.2][Confronto tra Diagnostica Azure 1.0 e 1.2] alla fine di questo articolo.

Diagnostica Azure può raccogliere i tipi di telemetria seguenti:

|----------------------------------------------|-------------------------------------------------------------------------------------|
| **Origine dati**                             | **Descrizione**                                                                     |
| Log di IIS                                   | Informazioni sui siti Web di IIS.                                                   |
| Log dell'infrastruttura diagnostica di Azure | Informazioni su Diagnostica.                                                        |
| Log di richieste non riuscite di IIS         | Informazioni sulle richieste non riuscite a un sito o a un'applicazione di IIS.     |
| Registri eventi di Windows                   | Informazioni inviate al sistema di registrazione eventi di Windows.                 |
| Contatori delle prestazioni                  | Contatori delle prestazioni personalizzati e del sistema operativo.                 |
| Dump di arresto anomalo del sistema          | Informazioni sullo stato del processo in caso di arresto anomalo dell'applicazione. |
| Log degli errori personalizzati              | Log creati dall'applicazione o dal servizio.                                        |
| EventSource .NET                             | Eventi generati dal codice con la [classe EventSource][classe EventSource] .NET.                      |
| ETW basato su manifesto                      | Eventi ETW generati da un processo.                                                 |

## <a name="worker-role"></a><span class="short-header">Abilitare la diagnostica in un ruolo di lavoro</span>Come abilitare la diagnostica in un ruolo di lavoro

Questa procedura dettagliata descrive come implementare un ruolo di lavoro di Azure che emette i dati di telemetria con la classe EventSource .NET. Il modulo Diagnostica Azure viene usato per raccogliere i dati di telemetria e memorizzarli in un account di archiviazione di Azure. Quando si crea un ruolo di lavoro, Visual Studio abilita automaticamente Diagnostica 1.0 come parte della soluzione. Le istruzioni seguenti descrivono il processo per creare il ruolo di lavoro, disabilitare Diagnostica 1.0 dalla soluzione e implementare Diagnostica 1.2 nel ruolo di lavoro.

### Prerequisiti

In questo articolo si presuppone che l'utente abbia una sottoscrizione di Azure e usi Visual Studio 2013 con Azure SDK. Se non si dispone di una sottoscrizione di Azure, è possibile iscriversi alla [versione di prova gratuita][versione di prova gratuita]. Assicurarsi di [installare e configurare Azure PowerShell versione 0.8.7 o successiva][installare e configurare Azure PowerShell versione 0.8.7 o successiva].

### Passaggio 1: Creare un ruolo di lavoro

1.  Avviare **Visual Studio 2013**.
2.  Creare un nuovo progetto **Servizio cloud di Windows Azure** dal modello **Cloud** per .NET Framework 4.5. Assegnare al progetto il nome "WadExample".
3.  Selezionare **Ruolo di lavoro**.
4.  In **Esplora soluzioni** fare doppio clic sul file delle proprietà **WorkerRole1**.
5.  Nella scheda **Configurazione** deselezionare **Abilita diagnostica** per disabilitare Diagnostica 1.0.
6.  Compilare la soluzione per verificare che non ci siano errori.

### Passaggio 2: Instrumentare il codice

Sostituire il contenuto del file WorkerRole.cs con il codice seguente. La classe SampleEventSourceWriter, ereditata dalla [classe EventSource][classe EventSource], implementa quattro metodi di registrazione: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. Il primo parametro per il metodo **WriteEvent** definisce l'ID per il rispettivo evento. Il metodo Run implementa un ciclo infinito che chiama ognuno dei metodi di registrazione implementati nella classe **SampleEventSourceWriter** ogni 10 secondi.

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

### Passaggio 3: Implementare il ruolo di lavoro

1.  Implementare il ruolo di lavoro in Azure da Visual Studio selezionando il progetto **WadExample**, quindi **Pubblica** nel menu **Compila**.
2.  Scegliere la propria sottoscrizione.
3.  Nella finestra di dialogo **Impostazioni di pubblicazione Microsoft Azure** selezionare **<create new…>**.
4.  Nella finestra di dialogo **Crea servizio cloud e account di archiviazione** immettere un **Nome** (ad esempio, "WadExample") e selezionare un'area o un gruppo di affinità.
5.  Impostare **Ambiente** su **Gestione temporanea**.
6.  Modificare le altre **Impostazioni** nel modo appropriato e fare clic su **Pubblica**.
7.  Al termine della distribuzione, verificare nel portale di Azure che lo stato del servizio cloud sia **In esecuzione**.

### Passaggio 4: Creare il file di configurazione della diagnostica e installare l'estensione

1.  Scaricare la definizione dello schema del file di configurazione pubblico eseguendo il comando PowerShell seguente:
2.  (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

3.  Aggiungere un file XML al progetto **WorkerRole1** facendo clic con il pulsante destro del mouse sul progetto **WorkerRole1** e scegliendo **Aggiungi** -\> **Nuovo elemento…** -\> **Elementi Visual C#** -\> **Dati** -\> **File XML**. Assegnare al file il nome "WadExample.xml".

    ![CloudServices\_diag\_add\_xml][CloudServices\_diag\_add\_xml]

4.  Associare WadConfig.xsd al file di configurazione. Assicurarsi che la finestra dell'editor di WadExample.xml sia la finestra attiva. Premere **F4** per aprire la finestra **Proprietà**. Fare clic sulla proprietà **Schemi** nella finestra **Proprietà**. Fare clic su **…** nella proprietà **Schemi**. Fare clic sul pulsante **Aggiungi…**, passare al percorso in cui si è salvato il file XSD e selezionare il file WadConfig.xsd. Fare clic su **OK**.
5.  Sostituire i contenuti del file di configurazione WadExample.xml con il codice XML seguente e salvare il file. Questo file di configurazione definisce due contatori delle prestazioni per la raccolta: uno per l'utilizzo della CPU e uno per l'utilizzo della memoria. La configurazione definisce quindi i quattro eventi corrispondenti ai metodi nella classe SampleEventSourceWriter.

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

### Passaggio 5: Installare la diagnostica nel ruolo di lavoro

I cmdlet di PowerShell per la gestione della diagnostica in un ruolo Web o di lavoro sono: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension e Remove-AzureServiceDiagnosticsExtension.

1.  Aprire Windows Azure PowerShell.
2.  Eseguire lo script per installare la diagnostica nel ruolo di lavoro (sostituire *StorageAccountKey* con la chiave dell'account di archiviazione di wadexample):

        $storage_name = "wadexample"
        $key = "<StorageAccountKey>"
        $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
        $service_name="wadexample"
        $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
        Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging

### Passaggio 6: Esaminare i dati di telemetria

In **Esplora server** di Visual Studio passare all'account di archiviazione di wadexample. Dopo 5 minuti dall'inizio dell'esecuzione del servizio cloud, si dovrebbero visualizzare le tabelle **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Fare doppio clic su una delle tabelle per visualizzare la telemetria raccolta.
 ![CloudServices\_diag\_tables][CloudServices\_diag\_tables]

## <a name="virtual-machine"></a><span class="short-header">Abilitare la diagnostica in una macchina virtuale</span>Come abilitare la diagnostica in una macchina virtuale

Questa procedura dettagliata descrive come installare in remoto la diagnostica in una macchina virtuale di Azure da un computer di sviluppo. Si apprenderà anche come implementare un'applicazione che viene eseguita in una macchina virtuale di Azure ed emette i dati di telemetria con la [classe EventSource][classe EventSource] .NET. Il modulo Diagnostica Azure viene usato per raccogliere la telemetria e memorizzarla in un account di archiviazione di Azure.

### Prerequisiti

In questa procedura si presuppone che l'utente abbia una sottoscrizione di Azure e usi Visual Studio 2013 con Azure SDK. Se non si dispone di una sottoscrizione di Azure, è possibile iscriversi alla [versione di prova gratuita][versione di prova gratuita]. Assicurarsi di [installare e configurare Azure PowerShell versione 0.8.7 o successiva][installare e configurare Azure PowerShell versione 0.8.7 o successiva].

### Passaggio 1: Creare una macchina virtuale

1.  Nel computer di sviluppo avviare Visual Studio 2013.
2.  In **Esplora server** di Visual Studio fare clic con il pulsante destro del mouse su **Windows Azure**, quindi scegliere **Macchina virtuale** -\> **Crea macchina virtuale**.
3.  Selezionare la sottoscrizione di Azure nella finestra di dialogo **Scegli sottoscrizione** e fare clic su **Avanti**.
4.  Selezionare **Windows Server 2012 R2 Datacenter** nella finestra di dialogo **Selezionare un'immagine della macchina virtuale** e fare clic su **Avanti**.
5.  In **Impostazioni di base della macchina virtuale** impostare il nome della macchina virtuale su "wadexample". Impostare il nome utente e la password dell'amministratore e fare clic su **Avanti**.
6.  Nella finestra di dialogo **Impostazioni servizio cloud** creare un nuovo servizio cloud denominato "wadexampleVM". Creare un nuovo account di archiviazione denominato "wadexample" e fare clic su **Avanti**.
7.  Fare clic su **Create**.

### Passaggio 2: Creare l'applicazione

1.  Nel computer di sviluppo avviare Visual Studio 2013.
2.  Creare una nuova applicazione console Visual C# per .NET Framework 4.5. Assegnare al progetto il nome "WadExampleVM".
    ![CloudServices\_diag\_new\_project][CloudServices\_diag\_new\_project]
3.  Sostituire il contenuto del file Program.cs con il codice seguente. La classe **SampleEventSourceWriter** implementa quattro metodi di registrazione: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. Il primo parametro per il metodo WriteEvent definisce l'ID per il rispettivo evento. Il metodo Run implementa un ciclo infinito che chiama ognuno dei metodi di registrazione implementati nella classe **SampleEventSourceWriter** ogni 10 secondi.

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

4.  Salvare il file e scegliere **Compila soluzione** dal menu **Compila** per compilare il codice.

### Passaggio 3: Distribuire l'applicazione

1.  Fare clic con il pulsante destro del mouse sul progetto **WadExampleVM** in **Esplora soluzioni** e scegliere **Apri cartella in Esplora file**.
2.  Passare alla cartella *bin\\Debug* e copiare tutti i file (WadExampleVM.\*).
3.  In **Esplora server** fare clic con il pulsante destro del mouse sulla macchina virtuale e scegliere **Connessione tramite desktop remoto**.
4.  Una volta connessi alla macchina virtuale, creare una cartella denominata WadExampleVM e incollare i file dell'applicazione nella cartella.
5.  Avviare l'applicazione WadExampleVM.exe. Verrà visualizzata una finestra della console vuota.

### Passaggio 4: Creare la configurazione per la diagnostica e installare l'estensione

1.  Scaricare la definizione dello schema del file di configurazione pubblico nel computer di sviluppo eseguendo il comando PowerShell seguente:

        (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.  Aprire un nuovo file XML in Visual Studio, in un progetto già aperto o in un'istanza di Visual Studio senza progetti aperti In Visual Studio selezionare **Aggiungi** -\> **Nuovo elemento…** -\> **Elementi Visual C#** -\> **Dati** -\> **File XML**. Assegnare al file il nome "WadExample.xml".
3.  Associare WadConfig.xsd al file di configurazione. Assicurarsi che la finestra dell'editor di WadExample.xml sia la finestra attiva. Premere **F4** per aprire la finestra **Proprietà**. Fare clic sulla proprietà **Schemi** nella finestra **Proprietà**. Fare clic su **…** nella proprietà **Schemi**. Fare clic sul pulsante **Aggiungi…**, passare al percorso in cui si è salvato il file XSD e selezionare il file WadConfig.xsd. Fare clic su **OK**.
4.  Sostituire i contenuti del file di configurazione WadExample.xml con il codice XML seguente e salvare il file. Questo file di configurazione definisce due contatori delle prestazioni per la raccolta: uno per l'utilizzo della CPU e uno per l'utilizzo della memoria. La configurazione definisce quindi i quattro eventi corrispondenti ai metodi nella classe SampleEventSourceWriter.

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

### Passaggio 5: Installare in remoto la diagnostica nella macchina virtuale di Azure

I cmdlet di PowerShell per la gestione della diagnostica in una macchina virtuale sono: Set-AzureVMDiagnosticsExtension, Get-AzureVMDiagnosticsExtension e Remove-AzureVMDiagnosticsExtension.

1.  Nel computer dello sviluppatore aprire Windows Azure PowerShell.
2.  Eseguire lo script per installare in remoto la diagnostica nella macchina virtuale (sostituire *StorageAccountKey* con la chiave dell'account di archiviazione di wadexamplevm):

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

In **Esplora server** di Visual Studio passare all'account di archiviazione di wadexample. Dopo 5 minuti dall'inizio dell'esecuzione della macchina virtuale, si dovrebbero visualizzare le tabelle **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Fare doppio clic su una delle tabelle per visualizzare la telemetria raccolta.
 ![CloudServices\_diag\_wadexamplevm\_tables][CloudServices\_diag\_wadexamplevm\_tables]

## <a name="configuration-file-schema"></a><span class="short-header">Schema e file di configurazione di esempio</span>Schema del file di configurazione

Il file di configurazione della diagnostica definisce i valori usati per inizializzare le impostazioni di configurazione della diagnostica quando viene avviato il monitor di diagnostica. Un file di configurazione di esempio e la documentazione dettagliata dello schema sono disponibili qui: [Schema di configurazione di Diagnostica di Azure 1.2][Schema di configurazione di Diagnostica di Azure 1.2].

## <a name="troubleshooting"></a><span class="short-header">Risoluzione dei problemi</span>Risoluzione dei problemi

### Diagnostica di Azure non si avvia

La diagnostica è costituita da due componenti: un plug-in agente guest e l'agente di monitoraggio. I file di log per il plug-in agente guest si trovano nel file:

*%SystemDrive%\\ WindowsAzure\\Logs\\Plugins\\Microsoft.Azure.Diagnostics.PaaSDiagnostics\\<diagnosticsversion>*\\CommandExecution.log

I seguenti codici errore vengono restituiti dal plug-in:

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
<td align="left">Esito positivo.</td>
</tr>
<tr class="odd">
<td align="left">-1</td>
<td align="left">Errore generico.</td>
</tr>
<tr class="even">
<td align="left">-2</td>
<td align="left"><p>Non è possibile caricare il file rcf.</p>
<p>È un errore interno che si dovrebbe verificare solo se il programma di avvio del plug-in agente guest viene richiamato manualmente, in modo non corretto, sulla macchina virtuale.</p></td>
</tr>
<tr class="odd">
<td align="left">-3</td>
<td align="left"><p>Non è possibile caricare il file di configurazione della diagnostica.</p>
<p>Soluzione: poiché questo errore è causato da un file di configurazione che non supera la convalida dello schema, specificare un file di configurazione conforme allo schema.</p></td>
</tr>
<tr class="even">
<td align="left">-4</td>
<td align="left"><p>Un'altra istanza della diagnostica dell'agente di monitoraggio sta già usando la directory delle risorse locali.</p>
<p>Soluzione: specificare un valore diverso per <strong>LocalResourceDirectory</strong>.</p></td>
</tr>
<tr class="odd">
<td align="left">-6</td>
<td align="left"><p>Il programma di avvio del plug-in agente guest ha tentato di avviare la diagnostica con una riga di comando non valida.</p>
<p>È un errore interno che si dovrebbe verificare solo se il programma di avvio del plug-in agente guest viene richiamato manualmente, in modo non corretto, sulla macchina virtuale.</p></td>
</tr>
<tr class="even">
<td align="left">-10</td>
<td align="left">Il plug-in Diagnostica è stato chiuso con un'eccezione non gestita.</td>
</tr>
<tr class="odd">
<td align="left">-11</td>
<td align="left"><p>L'agente guest non è stato in grado di creare il processo responsabile dell'avvio e del monitoraggio dell'agente di monitoraggio.</p>
<p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare i nuovi processi.</p></td>
</tr>
<tr class="even">
<td align="left">-101</td>
<td align="left"><p>Argomenti non validi quando si chiama il plug-in Diagnostica.</p>
<p>È un errore interno che si dovrebbe verificare solo se il programma di avvio del plug-in agente guest viene richiamato manualmente, in modo non corretto, sulla macchina virtuale.</p></td>
</tr>
<tr class="odd">
<td align="left">-102</td>
<td align="left"><p>Il processo del plug-in non è in grado di inizializzarsi.</p>
<p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare i nuovi processi.</p></td>
</tr>
<tr class="even">
<td align="left">-103</td>
<td align="left"><p>Il processo del plug-in non è in grado di inizializzarsi. In particolare, non è in grado di creare l'oggetto logger.</p>
<p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare i nuovi processi.</p></td>
</tr>
<tr class="odd">
<td align="left">-104</td>
<td align="left"><p>Non è possibile caricare il file rcf fornito dall'agente guest.</p>
<p>È un errore interno che si dovrebbe verificare solo se il programma di avvio del plug-in agente guest viene richiamato manualmente, in modo non corretto, sulla macchina virtuale.</p></td>
</tr>
<tr class="even">
<td align="left">-105</td>
<td align="left"><p>Il plug-in Diagnostica non può aprire il file di configurazione della diagnostica.</p>
<p>È un errore interno che si dovrebbe verificare solo se il plug-in Diagnostica viene richiamato manualmente, in modo non corretto, sulla macchina virtuale.</p></td>
</tr>
<tr class="odd">
<td align="left">-106</td>
<td align="left"><p>Non è possibile leggere il file di configurazione della diagnostica.</p>
<p>Soluzione: poiché questo errore è causato da un file di configurazione che non supera la convalida dello schema, specificare un file di configurazione conforme allo schema. È possibile trovare il file XML fornito all'estensione Diagnostica nella cartella <em>%SystemDrive%\WindowsAzure\Config</em> della macchina virtuale. Aprire il file XML appropriato e cercare <strong>Microsoft.Azure.Diagnostics</strong>, quindi cercare il campo <strong>xmlCfg</strong>. Poiché la codifica dei dati è Base 64, sarà necessario <a href="http://www.bing.com/search?q=base64+decoder">decodificarli</a> per visualizzare il file XML caricato dalla diagnostica.</p></td>
</tr>
<tr class="even">
<td align="left">-107</td>
<td align="left"><p>La directory delle risorse passata all'agente di monitoraggio non è valida.</p>
<p>È un errore interno che si dovrebbe verificare solo se l'agente di monitoraggio viene richiamato manualmente, in modo non corretto, sulla macchina virtuale.</p></td>
</tr>
<tr class="odd">
<td align="left">-108</td>
<td align="left"><p>Non è possibile convertire il file di configurazione della diagnostica nel file di configurazione dell'agente di monitoraggio.</p>
<p>È un errore interno che si dovrebbe verificare solo se il plug-in Diagnostica viene richiamato manualmente con un file di configurazione non valido.</p></td>
</tr>
<tr class="even">
<td align="left">-110</td>
<td align="left"><p>Errore di configurazione della diagnostica generale</p>
<p>È un errore interno che si dovrebbe verificare solo se il plug-in Diagnostica viene richiamato manualmente con un file di configurazione non valido.</p></td>
</tr>
<tr class="odd">
<td align="left">-111</td>
<td align="left"><p>Non è possibile avviare l'agente di monitoraggio.</p>
<p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti.</p></td>
</tr>
<tr class="even">
<td align="left">-112</td>
<td align="left">Errore generale</td>
</tr>
</tbody>
</table>

### I dati di diagnostica non vengono registrati nella memoria

La causa più comune della mancanza di dati degli eventi è la definizione non corretta delle informazioni sull'account di archiviazione.

Soluzione: correggere il file di configurazione della diagnostica e reinstallare la diagnostica.
I dati degli eventi, prima di essere caricati nell'account di archiviazione, vengono memorizzati nella cartella. Per i dettagli su **LocalResourceDirectory**, vedere sopra.

Se non ci sono file in questa cartella, non è possibile avviare l'agente di monitoraggio. Questo problema in genere è causato da un file di configurazione non valido e dovrebbe essere stato segnalato in CommandExecution.log. Se l'agente di monitoraggio sta raccogliendo correttamente i dati degli eventi, sarà presente un file tsf per ogni evento definito nel file di configurazione.

L'agente di monitoraggio registra gli errori individuati nel file MaEventTable.tsf. Per esaminare il contenuto di questo file, eseguire il comando seguente:

        %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.[IaaS | PaaS]Diagnostics.2.0.0\Monitor\x64\table2csv maeventtable.tsf

Lo strumento genera un file denominato maeventtable.csv che è possibile aprire per cercare gli errori nei log.

## <a name="faq"></a><span class="short-header">Domande frequenti</span>Domande frequenti

Ecco alcune domande frequenti e le relative risposte:

**D.** Come si aggiorna la soluzione Visual Studio da Diagnostica di Azure 1.0 a Diagnostica di Azure 1.1?

**R.** L'aggiornamento della soluzione Visual Studio da Diagnostica 1.0 a Diagnostica 1.1 (o versione successiva) è un processo manuale:

-   Disabilitare la diagnostica nella soluzione Visual Studio per evitare che Diagnostica 1.0 venga implementato con il ruolo in uso
-   Se il codice usa il listener di traccia, sarà necessario modificare il codice per poter usare EventSource .NET. Diagnostica 1.1 e versioni successive non supportano il listener di traccia.
-   Modificare il processo di distribuzione per installare l'estensione Diagnostica 1.1

**D.** Se l'estensione Diagnostica 1.1 è già installata nel ruolo o nella macchina virtuale, come si esegue l'aggiornamento a Diagnostica 1.2?

**R.** Se si è specificato "–Version "1.*"" quando si è installato Diagnostica 1.1, la prossima volta che il ruolo o la macchina virtuale verrà riavviata, verrà automaticamente aggiornata alla versione più recente corrispondente all'espressione regolare "1.*". Se si è specificato "–Version "1.1"" quando si è installato Diagnostica 1.1, è possibile aggiornare una versione più recente rieseguendo il cmdlet Set- e specificando la versione da installare.

**D.** Come vengono denominate le tabelle?

**R.** Le tabelle vengono denominate in base al codice seguente:

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

Verranno generate 4 tabelle:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
<td style="width: 100px;">
**Evento**

</td>
<td>
**Nome tabella**

</td>
</tr>
<tr>
<td>
provider=”prov1” \<Event id=”1” /\>

</td>
<td>
WADEvent+MD5("prov1")+"1"

</td>
</tr>
<tr>
<td>
provider="prov1" \<Event id="2" eventDestination="dest1" /\>

</td>
<td>
WADdest1

</td>
</tr>
<tr>
<td>
provider="prov1" \<DefaultEvents /\>

</td>
<td>
WADDefault+MD5("prov1")

</td>
</tr>
<tr>
<td>
provider="prov2" \<DefaultEvents eventDestination="dest2" /\>

</td>
<td>
WADdest2

</td>
</tr>
</table>
</tbody>
## <a name="comparing"></a><span class="short-header">Diagnostica di Azure 1.0 e 1.2</span>Confronto tra Diagnostica di Azure 1.0 e 1.2

La tabella seguente confronta le funzionalità supportate da Diagnostica di Azure versioni 1.0 e 1.1/1.2:

|------------------------------|---------------------|-------------------------|
| **Tipi di ruoli supportati** | **Diagnostica 1.0** | **Diagnostica 1.1/1.2** |
| Ruolo Web                    | Sì                  | Sì                      |
| Ruolo di lavoro              | Sì                  | Sì                      |
| IaaS                         | No                  | Sì                      |

|------------------------------------------------------------------------------------------------------------|---------------------|-------------------------|
| **Configurazione e distribuzione**                                                                         | **Diagnostica 1.0** | **Diagnostica 1.1/1.2** |
| Integrazione con Visual Studio - Integrato nell'esperienza di sviluppo di lavoro/Web di Azure.             | Sì                  | No                      |
| Script di PowerShell - Script per gestire l'installazione e la configurazione della diagnostica nel ruolo. | Sì                  | Sì                      |

|----------------------------------------------|--------------------------|-------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------|-------------------------|
| **Origine dati**                             | **Raccolta predefinita** | **Formato** | **Descrizione**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | **Diagnostica 1.0** | **Diagnostica 1.1/1.2** |
| System.Diagnostics.Trace Logs                | Sì                       | Tabella     | Registra i messaggi di traccia inviati dal codice al listener di traccia (un listener di traccia deve essere aggiunto al file web.config o app.config). I dati dei log verranno trasferiti nell'intervallo di trasferimento scheduledTransferPeriod alla tabella di archiviazione WADLogsTable.                                                                                                                                                                                                     | Sì                  | No (usare EventSource)  |
| Log di IIS                                   | Sì                       | BLOB        | Registra informazioni sui siti di IIS. I dati dei log verranno trasferiti nell'intervallo di trasferimento scheduledTransferPeriod al contenitore specificato.                                                                                                                                                                                                                                                                                                                                      | Sì                  | Sì                      |
| Log dell'infrastruttura diagnostica di Azure | Sì                       | Tabella     | Registra informazioni sull'infrastruttura di diagnostica, sul modulo RemoteAccess e sul modulo RemoteForwarder. I dati dei log verranno trasferiti nell'intervallo di trasferimento scheduledTransferPeriod alla tabella WADDiagnosticInfrastructureLogsTable.                                                                                                                                                                                                                                      | Sì                  | Sì                      |
| Log di richieste non riuscite di IIS         | No                       | BLOB        | Registra informazioni sulle richieste non riuscite a un sito o a un'applicazione di IIS. È anche necessario eseguire l'abilitazione impostando le opzioni di traccia sotto system.WebServer in Web.config. I dati dei log verranno trasferiti nell'intervallo di trasferimento scheduledTransferPeriod al contenitore specificato.                                                                                                                                                                  | Sì                  | Sì                      |
| Registri eventi di Windows                   | No                       | Tabella     | Registra informazioni sul funzionamento del sistema operativo, dell'applicazione o del driver. I contatori delle prestazioni devono essere specificati in modo esplicito. Quando verranno aggiunti, i dati dei contatori delle prestazioni verranno trasferiti nell'intervallo di trasferimento scheduledTransferPeriod alla tabella di archiviazione WADPerformanceCountersTable.                                                                                                                  | Sì                  | Sì                      |
| Contatori delle prestazioni                  | No                       | Tabella     | Registra informazioni sul funzionamento del sistema operativo, dell'applicazione o del driver. I contatori delle prestazioni devono essere specificati in modo esplicito. Quando verranno aggiunti, i dati dei contatori delle prestazioni verranno trasferiti nell'intervallo di trasferimento scheduledTransferPeriod alla tabella di archiviazione WADPerformanceCountersTable.                                                                                                                  | Sì                  | Sì                      |
| Dump di arresto anomalo del sistema          | No                       | BLOB        | Registra informazioni sullo stato del sistema operativo in caso di arresto anomalo dello stesso. I minidump di arresto anomalo del sistema vengono raccolti in locale. Possono essere abilitati dump completi. I dati dei log verranno trasferiti nell'intervallo di trasferimento scheduledTransferPeriod al contenitore specificato. Poiché la maggior parte delle eccezioni viene gestita in ASP.NET, questa funzionalità in genere è utile solo per un ruolo di lavoro o una macchina virtuale. | Sì                  | Sì                      |
| Log degli errori personalizzati              | No                       | BLOB        | Tramite le risorse di archiviazione locali, i dati personalizzati possono essere registrati e trasferiti immediatamente al contenitore specificato.                                                                                                                                                                                                                                                                                                                                                 | Sì                  | Sì                      |
| EventSource                                  | No                       | Tabella     | Registra eventi generati dal codice con la classe EventSource .NET.                                                                                                                                                                                                                                                                                                                                                                                                                                 | No                  | Sì                      |
| ETW basato su manifesto                      | No                       | Tabella     | Eventi ETW generati da un processo.                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | No                  | Sì                      |

## <a name="additional"></a><span class="short-header">Risorse aggiuntive</span>Risorse aggiuntive

-   [Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure][Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure]
-   [Raccogliere dati di registrazione usando Diagnostica Azure][Raccogliere dati di registrazione usando Diagnostica Azure]
-   [Debug di un'applicazione Azure][Debug di un'applicazione Azure]
-   [Configurazione di Diagnostica Microsoft Azure][Configurazione di Diagnostica Microsoft Azure]

  [Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/hh771389.aspx
  [Panoramica]: #overview
  [Come abilitare la diagnostica in un ruolo di lavoro]: #worker-role
  [Come abilitare la diagnostica in una macchina virtuale]: #virtual-machine
  [Schema e file di configurazione di esempio]: #configuration-file-schema
  [Risoluzione dei problemi]: #troubleshooting
  [Domande frequenti]: #faq
  [Confronto tra Diagnostica Azure 1.0 e 1.2]: #comparing
  [Risorse aggiuntive]: #additional
  [classe EventSource]: http://msdn.microsoft.com/it-it/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx
  [versione di prova gratuita]: http://azure.microsoft.com/it-it/pricing/free-trial/
  [installare e configurare Azure PowerShell versione 0.8.7 o successiva]: http://azure.microsoft.com/it-it/documentation/articles/install-configure-powershell/
  [CloudServices\_diag\_add\_xml]: ./media/cloud-services-dotnet-diagnostics/AddXmlFile.png
  [CloudServices\_diag\_tables]: ./media/cloud-services-dotnet-diagnostics/WadExampleTables.png
  [CloudServices\_diag\_new\_project]: ./media/cloud-services-dotnet-diagnostics/NewProject.png
  [CloudServices\_diag\_wadexamplevm\_tables]: ./media/cloud-services-dotnet-diagnostics/WadExampleVMTables.png
  [Schema di configurazione di Diagnostica di Azure 1.2]: http://msdn.microsoft.com/it-it/library/azure/dn782207.aspx
  [Raccogliere dati di registrazione usando Diagnostica Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/gg433048.aspx
  [Debug di un'applicazione Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/ee405479.aspx
  [Configurazione di Diagnostica Microsoft Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/dn186185.aspx

<properties 
	pageTitle="Come usare la diagnostica (.NET) - Guida alle funzionalità di Azure" 
	description="Informazioni sull'uso dei dati di diagnostica in Azure per debug, valutazione delle prestazioni, monitoraggio, analisi del traffico e altro ancora." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="rboucher" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="robb"/>



# Abilitazione di Diagnostica in servizi cloud e macchine virtuali di Azure

Diagnostica Azure 1.2 e 1.3 consentono di raccogliere dati diagnostici da una macchina virtuale in esecuzione in Azure, un ruolo web o un ruolo di lavoro. Questa guida descrive come usare Diagnostica Azure 1.2 e 1.3. Per altre linee guida dettagliate sulla creazione di una strategia di registrazione e traccia e sull'uso della diagnostica e di altre tecniche per risolvere i problemi e ottimizzare le applicazioni Azure, vedere [Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure][].

## Panoramica

Diagnostica Azure 1.2 e 1.3 sono estensioni di Azure che consentono di raccogliere dati di telemetria di diagnostica da un ruolo di lavoro, da un ruolo Web o da una macchina virtuale in esecuzione in Azure. I dati di telemetria vengono memorizzati in un account di archiviazione di Azure e possono essere usati per il debug e la risoluzione dei problemi, per la misurazione delle prestazioni, per il monitoraggio dell'uso delle risorse, per l'analisi del traffico e la pianificazione della capacità e per il controllo.

Diagnostica Azure 1.2 può essere usato con Azure SDK per .NET 2.4 e versioni precedenti. Diagnostica Azure 1.3 può essere usato con Azure SDK per .NET 2.5 e versioni successive.

Se in passato si è usato Diagnostica versione 1.0, ci sono tre differenze sostanziali con Diagnostica 1.2 e 1.3:

1.	I moduli Diagnostica 1.2 e 1.3 possono essere implementati nelle macchine virtuali, oltre che nei servizi cloud.
2.	Il modulo Diagnostica 1.0 fa parte di Azure SDK e viene implementato insieme al servizio cloud. Diagnostica 1.2 e 1.3 sono estensioni e sono distribuite separatamente dalla distribuzione del servizio cloud.
3.	Diagnostica 1.2 e 1.3 consentono la raccolta di eventi EventSource .NET ed ETW.

Per un confronto più dettagliato, vedere [Confronto tra le versioni di Diagnostica di Azure][] alla fine di questo articolo.

Diagnostica Azure può raccogliere i tipi di telemetria seguenti:

Origine dati|Descrizione
---|---
Log di IIS|Informazioni sui siti Web di IIS.
Log dell'infrastruttura diagnostica di Azure|Informazioni su Diagnostica.
Log delle richieste non riuscite di IIS|Informazioni sulle richieste non riuscite a un sito o a un'applicazione di IIS.
Registri eventi di Windows|Informazioni inviate al sistema di registrazione eventi di Windows.
Contatori delle prestazioni|Contatori delle prestazioni personalizzati e del sistema operativo.
Dump di arresto anomalo del sistema|Informazioni sullo stato del processo in caso di arresto anomalo dell'applicazione.
Log degli errori personalizzati|Log creati dall'applicazione o dal servizio.
EventSource .NET |Eventi generati dal codice con .NET <a href="http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">classe EventSource</a>
ETW basato su manifesto|Eventi ETW generati da un processo qualsiasi.

## Come abilitare la diagnostica in un ruolo di lavoro

Questa procedura dettagliata descrive come implementare un ruolo di lavoro di Azure che emette i dati di telemetria con la classe EventSource .NET. Il modulo Diagnostica Azure viene usato per raccogliere i dati di telemetria e memorizzarli in un account di archiviazione di Azure. Quando si crea un ruolo di lavoro, Visual Studio abilita automaticamente Diagnostica 1.0 come parte della soluzione in Azure SDK per .NET 2.4 e versioni precedenti. Le seguenti istruzioni descrivono il processo per creare il ruolo di lavoro, disabilitare Diagnostica 1.0 dalla soluzione e implementare Diagnostica 1.2 o 1.3 nel ruolo di lavoro.

### Prerequisiti
In questo articolo si presuppone che l'utente abbia una sottoscrizione di Azure e usi Visual Studio 2013 con Azure SDK. Se non si dispone di una sottoscrizione di Azure, è possibile iscriversi alla [versione di prova gratuita][]. Assicurarsi di [installare e configurare Azure PowerShell versione 0.8.7 o successiva][].

### Passaggio 1: Creare un ruolo di lavoro
1.	Avviare **Visual Studio 2013**.
2.	Creare un nuovo progetto **Servizio cloud di Azure** dal modello **Cloud** per .NET Framework 4.5. Assegnare al progetto il nome "WadExample" e fare clic su OK.
3.	Selezionare ** Ruolo di lavoro** e fare clic su OK. Verrà creato il progetto. 
4.	In **Esplora soluzioni** fare doppio clic sul file delle proprietà **WorkerRole1**.
5.	Nella scheda **Configurazione** deselezionare **Abilita diagnostica** per disabilitare Diagnostica 1.0 (Azure SDK 2.4 e versioni precedenti).
6.	Compilare la soluzione per verificare che non ci siano errori.

### Passaggio 2: Instrumentare il codice
Sostituire il contenuto del file WorkerRole.cs con il codice seguente. La classe SampleEventSourceWriter, ereditata dalla [classe EventSource][], implementa quattro metodi di registrazione: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. Il primo parametro per il metodo **WriteEvent** definisce l'ID per il rispettivo evento. Il metodo Run implementa un ciclo infinito che chiama ognuno dei metodi di registrazione implementati nella classe **SampleEventSourceWriter** ogni 10 secondi.

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
1.	Distribuire il ruolo di lavoro in Azure da Visual Studio selezionando il progetto **WadExample** in Esplora soluzioni, quindi **Pubblica** dal menu **Compila**.
2.	Scegliere la propria sottoscrizione.
3.	Nella finestra di dialogo **Impostazioni di pubblicazione di Microsoft Azure** selezionare **Crea nuovo**.
4.	Nella finestra di dialogo **Crea servizio cloud e account di archiviazione** immettere un valore in **Nome** (ad esempio, "WadExample") e selezionare un'area o un gruppo di affinità.
5.	Impostare **Ambiente** su **Gestione temporanea**.
6.	Modificare le altre **Impostazioni** nel modo appropriato e fare clic su **Pubblica**.
7.	Al termine della distribuzione, verificare nel portale di Azure che lo stato del servizio cloud sia **In esecuzione**.

### Passaggio 4: Creare il file di configurazione della diagnostica e installare l'estensione
1.	Scaricare la definizione dello schema del file di configurazione pubblico eseguendo il comando PowerShell seguente:
2.	
		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.	Aggiungere un file XML al progetto **WorkerRole1** facendo clic con il pulsante destro del mouse sul progetto **WorkerRole1** e scegliendo **Aggiungi** -> **Nuovo elemento…** -> **Elementi Visual C#** -> **Dati** -> **File XML**. Assegnare al file il nome "WadExample.xml".

	![CloudServices\_diag\_add\_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)

3.	Associare WadConfig.xsd al file di configurazione. Assicurarsi che la finestra dell'editor di WadExample.xml sia la finestra attiva. Premere **F4** per aprire la finestra **Proprietà**. Fare clic sulla proprietà **Schemi** nella finestra **Proprietà**. Fare clic su **…** nella proprietà **Schemi**. Fare clic sul pulsante **Aggiungi**, passare al percorso in cui si è salvato il file XSD e selezionare il file WadConfig.xsd. Fare clic su **OK**.
4.	Sostituire i contenuti del file di configurazione WadExample.xml con il codice XML seguente e salvare il file. Questo file di configurazione definisce due contatori delle prestazioni per la raccolta: uno per l'uso della CPU e uno per l'uso della memoria. La configurazione definisce quindi quattro eventi corrispondenti ai metodi della classe SampleEventSourceWriter.

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

### Passaggio 5: Installare la diagnostica nel ruolo di lavoro
I cmdlet di PowerShell per la gestione della diagnostica in un ruolo Web o di lavoro sono: : Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension e Remove-AzureServiceDiagnosticsExtension.

1.	Aprire Azure PowerShell.
2.	Eseguire lo script per installare la diagnostica nel ruolo di lavoro (sostituire *StorageAccountKey* con la chiave dell'account di archiviazione di wadexample):

		$storage_name = "wadexample"
		$key = "<StorageAccountKey>"
		$config_path="c:\users<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
		$service_name="wadexample"
		$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key 
		Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1


### Passaggio 6: Esaminare i dati di telemetria
In **Esplora server** di Visual Studio passare all'account di archiviazione di wadexample. Dopo 5 minuti dall'inizio dell'esecuzione del servizio cloud, si dovrebbero visualizzare le tabelle **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Fare doppio clic su una delle tabelle per visualizzare i dati di telemetria raccolti. ![CloudServices\_diag\_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## Come abilitare la diagnostica in una macchina virtuale

Questa procedura dettagliata descrive come installare in remoto la diagnostica in una macchina virtuale di Azure da un computer di sviluppo. Si apprenderà anche come implementare un'applicazione che viene eseguita in una macchina virtuale di Azure ed emette i dati di telemetria con la [classe EventSource][] .NET. Il modulo Diagnostica Azure viene usato per raccogliere la telemetria e memorizzarla in un account di archiviazione di Azure.

### Prerequisiti
In questa procedura si presuppone che l'utente abbia una sottoscrizione di Azure e usi Visual Studio 2013 con Azure SDK. Se non si dispone di una sottoscrizione di Azure, è possibile iscriversi alla [versione di prova gratuita][]. Assicurarsi di [installare e configurare Azure PowerShell versione 0.8.7 o successiva][].

### Passaggio 1: Creare una macchina virtuale
1.	Nel computer di sviluppo avviare Visual Studio 2013.
2.	In **Esplora server** di Visual Studio espandere **Azure**, fare clic con il pulsante destro del mouse su **Macchine virtuali**, quindi selezionare **Crea macchina virtuale**.
3.	Selezionare la sottoscrizione di Azure nella finestra di dialogo **Scegli sottoscrizione** e fare clic su **Avanti**.
4.	Selezionare **Windows Server 2012 R2 Datacenter, novembre 2014** nella finestra di dialogo **Selezionare un'immagine della macchina virtuale** e fare clic su **Avanti**.
5.	In **Impostazioni di base della macchina virtuale** impostare il nome della macchina virtuale su "wadexample". Impostare il nome utente e la password dell'amministratore e fare clic su **Avanti**.
6.	Nella finestra di dialogo **Impostazioni servizio cloud** creare un nuovo servizio cloud denominato "wadexampleVM". Creare un nuovo account di archiviazione denominato "wadexample" e fare clic su **Avanti**.
7.	Fare clic su **Create**.

### Passaggio 2: Creare l'applicazione
1.	Nel computer di sviluppo avviare Visual Studio 2013.
2.	Creare una nuova applicazione console Visual C# per .NET Framework 4.5. Assegnare al progetto il nome "WadExampleVM". ![CloudServices\_diag\_new\_project](./media/cloud-services-dotnet-diagnostics/NewProject.png)
3.	Sostituire il contenuto del file Program.cs con il codice seguente. La classe **SampleEventSourceWriter** implementa quattro metodi di registrazione: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. Il primo parametro per il metodo WriteEvent definisce l'ID per il rispettivo evento. Il metodo Run implementa un ciclo infinito che chiama ognuno dei metodi di registrazione implementati nella classe **SampleEventSourceWriter** ogni 10 secondi.

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


4.	Salvare il file e scegliere **Compila soluzione** dal menu **Compila** per compilare il codice.


### Passaggio 3: Distribuire l'applicazione
1.	Fare clic con il pulsante destro del mouse sul progetto **WadExampleVM** in **Esplora soluzioni** e scegliere **Apri cartella in Esplora file**.
2.	Passare alla cartella *bin\\Debug* e copiare tutti i file (WadExampleVM.*).
3.	In **Esplora server** fare clic con il pulsante destro del mouse sulla macchina virtuale e scegliere **Connessione tramite desktop remoto**.
4.	Una volta connessi alla macchina virtuale, creare una cartella denominata WadExampleVM e incollare i file dell'applicazione nella cartella.
5.	Avviare l'applicazione WadExampleVM.exe. Verrà visualizzata una finestra della console vuota.

### Passaggio 4: Creare la configurazione per la diagnostica e installare l'estensione
1.	Scaricare la definizione dello schema del file di configurazione pubblico nel computer di sviluppo eseguendo il comando PowerShell seguente:

		(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd' 

2.	Aprire un nuovo file XML in Visual Studio, in un progetto già aperto o in un'istanza di Visual Studio senza progetti aperti In Visual Studio selezionare **Aggiungi** -> **Nuovo elemento…** -> **Elementi Visual C# ** -> **Dati** -> **File XML**. Assegnare al file il nome "WadExample.xml".
3.	Associare WadConfig.xsd al file di configurazione. Assicurarsi che la finestra dell'editor di WadExample.xml sia la finestra attiva. Premere **F4** per aprire la finestra **Proprietà**. Fare clic sulla proprietà **Schemi** nella finestra **Proprietà**. Fare clic su **…** nella proprietà **Schemi**. Fare clic sul pulsante **Aggiungi**, passare al percorso in cui si è salvato il file XSD e selezionare il file WadConfig.xsd. Fare clic su **OK**.
4.	Sostituire i contenuti del file di configurazione WadExample.xml con il codice XML seguente e salvare il file. Questo file di configurazione definisce due contatori delle prestazioni per la raccolta: uno per l'uso della CPU e uno per l'uso della memoria. La configurazione definisce quindi quattro eventi corrispondenti ai metodi della classe SampleEventSourceWriter.

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

1.	Nel computer dello sviluppatore aprire Azure PowerShell.
2.	Eseguire lo script e installare Diagnostica nella macchina virtuale (sostituire *StorageAccountKey* con la chiave dell'account di archiviazione wadexamplevm):

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
In **Esplora server** di Visual Studio passare all'account di archiviazione di wadexample. Dopo che la macchina virtuale è rimasta in esecuzione per circa 5 minuti, vengono visualizzate le tabelle **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Fare doppio clic su una delle tabelle per visualizzare i dati di telemetria raccolti. ![CloudServices\_diag\_wadexamplevm\_tables](./media/cloud-services-dotnet-diagnostics/WadExampleVMTables.png)

## Schema del file di configurazione

Il file di configurazione di Diagnostica definisce i valori usati per inizializzare le impostazioni di diagnostica quando viene avviato il monitor di diagnostica. Un file di configurazione di esempio e la documentazione dettagliata dello schema sono disponibili nell'articolo che descrive lo [schema di configurazione di Diagnostica di Azure 1.2][].

## Risoluzione dei problemi

### Mancato avvio di Diagnostica Azure
La diagnostica è costituita da due componenti: un plug-in agente guest e l'agente di monitoraggio. I file di log del plug-in dell'agente guest si trovano nel file seguente:

*%SystemDrive%\\ WindowsAzure\\Logs\\Plugins\\Microsoft.Azure.Diagnostics.PaaSDiagnostics<VersioneDiagnostica>*\\CommandExecution.log

Il plug-in restituisce i seguenti codici di errore:

Codice di uscita|Descrizione
---|---
0|Completamento della procedura.
\-1|Errore generico.
\-2|Impossibile caricare il file rcf. <p>Questo è un errore interno che si dovrebbe verificare solo se il programma di avvio del plug-in agente guest viene richiamato manualmente, in modo non corretto, sulla macchina virtuale.
\-3|Impossibile caricare il file di configurazione di Diagnostica. <p> <p> Soluzione: questo errore è causato da un file di configurazione che non supera la convalida dello schema. La soluzione consiste nel fornire un file di configurazione conforme allo schema.
\-4|Un'altra istanza della diagnostica dell'agente di monitoraggio sta già usando la directory delle risorse locali. <p> <p> Soluzione: specificare un valore diverso per **LocalResourceDirectory**.
\-6|L’utilità di avvio del plug-in dell’agente guest ha tentato di avviare la Diagnostica con una riga di comando non valida. <p><p>Questo è un errore interno che dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente, in modo non corretto, sulla macchina virtuale.
\-10|Il plug-in di Diagnostica ha generato un'eccezione non gestita.
\-11|L'agente guest non è stato in grado di creare il processo responsabile dell'avvio e del monitoraggio dell'agente di monitoraggio. <p><p> Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare nuovi processi.<p>
\-101|Argomenti non validi durante la chiamata del plug-in di Diagnostica. <p> <p> Questo è un errore interno che dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente, in modo non corretto, sulla macchina virtuale.
\-102|Il processo del plug-in non è in grado di inizializzarsi. <p><p> Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare nuovi processi.
\-103|Il processo del plug-in non è in grado di inizializzarsi. In particolare, non è in grado di creare l'oggetto logger. <p> <p>Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare nuovi processi.
\-104|Impossibile caricare il file rcf fornito dall'agente guest. <p> <p> Questo è un errore interno che dovrebbe verificarsi solo se l'utilità di avvio del plug-in dell'agente guest viene richiamata manualmente, in modo non corretto, sulla macchina virtuale.
\-105|Il plug-in di Diagnostica non è in grado di aprire il file di configurazione di Diagnostica. <p> <p> Questo è un errore interno che dovrebbe verificarsi solo se il plug-in di Diagnostica viene richiamato manualmente, in modo non corretto, sulla macchina virtuale.
\-106|Impossibile caricare il file di configurazione di Diagnostica. <p><p> Soluzione: questo errore è causato da un file di configurazione che non supera la convalida dello schema. La soluzione consiste nel fornire un file di configurazione conforme allo schema. Il file XML fornito all'estensione Diagnostica si trova nella cartella *%SystemDrive%\\WindowsAzure\\Config* sulla macchina virtuale. Aprire il file XML appropriato e cercare prima **Microsoft.Azure.Diagnostics**, quindi il campo **xmlCfg**. I dati sono codificati in base64, pertanto sarà necessario [decodificarli](http://www.bing.com/search?q=base64+decoder) per visualizzare il codice XML che è stato caricato da Diagnostica. <p>
\-107|La directory delle risorse passata all'agente di monitoraggio non è valida. <p> <p> Questo è un errore interno che dovrebbe verificarsi solo se l'agente di monitoraggio viene richiamato manualmente, in modo non corretto, sulla macchina virtuale. </p>
\-108 |Impossibile convertire il file di configurazione di Diagnostica nel file di configurazione dell'agente di monitoraggio. <p><p> Questo è un errore interno che dovrebbe verificarsi solo se il plug-in di Diagnostica viene richiamato manualmente con un file di configurazione non valido.
\-110|Errore di configurazione generale di Diagnostica. <p> <p> Questo è un errore interno che dovrebbe verificarsi solo se il plug-in di Diagnostica viene richiamato manualmente con un file di configurazione non valido.
\-111|Impossibile avviare l'agente di monitoraggio. <p> <p> Soluzione: verificare che siano disponibili risorse di sistema sufficienti per avviare nuovi processi.
\-112|Errore generale:


### I dati di Diagnostica non vengono registrati nella risorsa di archiviazione
La causa più comune della mancanza di dati degli eventi è la definizione non corretta delle informazioni sull'account di archiviazione.

Soluzione: correggere il file di configurazione della diagnostica e reinstallare la diagnostica. I dati degli eventi, prima di essere caricati nell'account di archiviazione, vengono memorizzati nella cartella. Vedere sopra per informazioni dettagliate su **LocalResourceDirectory**.

Se nella cartella non sono presenti file, l'agente di monitoraggio non si avvia. Questo problema in genere è causato da un file di configurazione non valido e dovrebbe essere stato segnalato in CommandExecution.log Se l'agente di monitoraggio sta raccogliendo correttamente i dati degli eventi, sarà presente un file con estensione tsf per ogni evento definito nel file di configurazione.

L'agente di monitoraggio registra gli errori individuati nel file MaEventTable.tsf. Per esaminare il contenuto di questo file, eseguire il seguente comando:

		%SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.[IaaS | PaaS]Diagnostics\1.3.0.0\Monitor\x64\table2csv maeventtable.tsf

Lo strumento genera un file denominato maeventtable.csv. È possibile aprire tale file ed esaminarne i log per verificare la presenza di errori.


## Domande frequenti
Di seguito sono riportate alcune domande frequenti e le relative risposte:

**D.** Come si aggiorna la soluzione Visual Studio da Diagnostica di Azure 1.0 a Diagnostica di Azure 1.1?

**R.** L'aggiornamento della soluzione Visual Studio da Diagnostica 1.0 a Diagnostica 1.1 (o versione successiva) è un processo manuale: disabilitare la diagnostica nella soluzione Visual Studio per evitare che Diagnostica 1.0 venga implementato con il ruolo in uso. Se il codice usa il listener di traccia, sarà necessario modificare il codice per usare .NET EventSource. Diagnostica 1.1 e versioni successive non supportano il listener di traccia. Modificare il processo di distribuzione per installare l'estensione Diagnostica 1.1.

**D.** Se l'estensione Diagnostica 1.1 è già installata nel ruolo o nella macchina virtuale, come si esegue l'aggiornamento a Diagnostica 1.2 o 1.3?

**R.** Se si è specificato "–Version "1. *“” al momento dell'installazione di Diagnostica 1.1, la prossima volta che il ruolo o la macchina virtuale verrà riavviata, sarà aggiornata automaticamente alla versione più recente corrispondente all'espressione regolare "1.*" Se si è specificato "–Version "1.1"" al momento dell'installazione di Diagnostics 1.1, è possibile aggiornare una versione più recente rieseguendo il cmdlet Set- e specificando la versione da installare.

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

Verranno generate quattro tabelle:

Evento|Nome tabella
---|---
provider=”prov1” &lt;Event id=”1” /&gt;|WADEvent+MD5(“prov1”)+”1”
provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt;|WADdest1
provider=”prov1” &lt;DefaultEvents /&gt;|WADDefault+MD5(“prov1”)
provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt;|WADdest2

## Confronto tra versioni di Diagnostica Azure

La seguente tabella confronta le funzionalità supportate da Diagnostica Azure versioni 1.0 e 1.1/1.2/1.3:

Tipi di ruolo supportati|Diagnostica 1.0|Diagnostica 1.1/1.2/1.3
---|---
Ruolo Web|Sì|Sì
Ruolo di lavoro|Sì|Sì
IaaS|No|Sì

Configurazione e distribuzione|Diagnostica 1.0|Diagnostica 1.1/1.2/1.3
---|---|---
Integrazione con Visual Studio - Integrata nell'esperienza di sviluppo di ruoli Web o di lavoro di Azure.|Sì|No
Script di PowerShell - Script per gestire l'installazione e la configurazione di Diagnostica nel ruolo.|Sì|Sì

Origine dati|Raccolta predefinita|Formato|Descrizione|Diagnostica 1.0|Diagnostica 1.1/1.2|Diagnostica 1.3
---|---|---|---|---|---|---
Log System.Diagnostics.Trace|Sì|Tabella|Registra messaggi di traccia inviati dal codice al listener di traccia (è necessario aggiungere un listener di traccia al file web.config o app.config). I dati del log verranno trasferiti alla tabella di archiviazione WADLogsTable in base all'intervallo di trasferimento scheduledTransferPeriod.|Sì|No (usare EventSource)|Sì
Log di IIS|Sì|BLOB|Registra informazioni sui siti IIS. I dati del log verranno trasferiti al contenitore specificato in base all'intervallo di trasferimento scheduledTransferPeriod.|Sì|Sì|Sì
Log dell'infrastruttura diagnostica di Azure|Sì|Tabella|Registra informazioni sull'infrastruttura di diagnostica e sui moduli RemoteAccess e RemoteForwarder. I dati del log verranno trasferiti alla tabella di archiviazione WADDiagnosticInfrastructureLogsTable in base all'intervallo di trasferimento scheduledTransferPeriodtransfer.|Sì|Sì|Sì
Log delle richieste non riuscite di IIS|No|BLOB|Registra informazioni sulle richieste non riuscite inviate a un'applicazione o a un sito IIS. È anche necessario eseguire l'abilitazione impostando le opzioni di traccia sotto system.WebServer in Web.config. I dati del log verranno trasferiti al contenitore specificato in base all'intervallo di trasferimento scheduledTransferPeriod.|Sì|Sì|Sì
Registri eventi di Windows|No|Tabella|Registra le informazioni sulle prestazioni del sistema operativo, dell'applicazione o del driver. I contatori delle prestazioni devono essere specificati in modo esplicito. Quando i contatori vengono aggiunti, i dati corrispondenti vengono trasferiti nella tabella di archiviazione WADPerformanceCountersTable in base all'intervallo di trasferimento scheduledTransferPeriod.|Sì|Sì|Sì
Contatori delle prestazioni|No|Tabella|Registra le informazioni sulle prestazioni del sistema operativo, dell'applicazione o del driver. I contatori delle prestazioni devono essere specificati in modo esplicito. Quando i contatori vengono aggiunti, i dati corrispondenti vengono trasferiti nella tabella di archiviazione WADPerformanceCountersTable in base all'intervallo di trasferimento scheduledTransferPeriod.|Sì|Sì|Sì
Dump di arresto anomalo del sistema|No|BLOB|Registra informazioni sullo stato del sistema operativo in caso di arresto anomalo del sistema. I minidump di arresto anomalo vengono raccolti localmente. È possibile abilitare i dump completi. I dati del log verranno trasferiti al contenitore specificato in base all'intervallo di trasferimento scheduledTransferPeriod. Poiché la maggior parte delle eccezioni è gestita da ASP.NET, questo è in genere utile solo per un ruolo di lavoro o una macchina virtuale.|Sì|Sì|Sì
Log degli errori personalizzati|No|BLOB|Grazie all'uso di risorse di archiviazione locali, i dati personalizzati possono essere registrati e trasferiti immediatamente al contenitore specificato.|Sì|Sì|Sì
EventSource|No|Tabella|Registra gli eventi generati dal codice usando la classe EventSource .NET.|No|Sì|Sì
ETW basato su manifesto|No|Tabella|Eventi ETW generati da un processo qualsiasi.|No|Sì|Sì


## Risorse aggiuntive

- [Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure][]
- [Raccogliere dati di registrazione usando Diagnostica di Azure][]
- [Debug di un'applicazione Azure][]
- [Configurazione della diagnostica per i servizi cloud e le macchine virtuali di Azure][]

  

[Overview]: #overview
[How to Enable Diagnostics in a Worker Role]: #worker-role
[How to Enable Diagnostics in a Virtual Machine]: #virtual-machine
[Sample Configuration File and Schema]: #configuration-file-schema
[Troubleshooting]: #troubleshooting
[Frequently Asked Questions]: #faq
[Confronto tra le versioni di Diagnostica di Azure]: #comparing
[Additional Resources]: #additional
[classe EventSource]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx
  
[Configurazione della diagnostica per i servizi cloud e le macchine virtuali di Azure]: http://msdn.microsoft.com/library/windowsazure/dn186185.aspx
[Debug di un'applicazione Azure]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx
[Raccogliere dati di registrazione usando Diagnostica di Azure]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Procedure consigliate di risoluzione dei problemi per lo sviluppo di applicazioni Azure]: http://msdn.microsoft.com/library/windowsazure/hh771389.aspx
[versione di prova gratuita]: http://azure.microsoft.com/pricing/free-trial/
[installare e configurare Azure PowerShell versione 0.8.7 o successiva]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
[schema di configurazione di Diagnostica di Azure 1.2]: http://msdn.microsoft.com/library/azure/dn782207.aspx
[Set-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/library/dn495270.aspx
[Get-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/library/dn495145.aspx
[Remove-AzureServiceDiagnosticsExtension]: http://msdn.microsoft.com/library/dn495168.aspx
 

<!---HONumber=August15_HO6-->
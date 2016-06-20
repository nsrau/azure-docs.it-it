<properties 
   pageTitle="Trasmettere i dati di Diagnostica di Azure nel percorso critico tramite Hub eventi"
   description="L'articolo illustra come configurare Diagnostica di Azure con l'Hub eventi dall'inizio alla fine e include indicazioni sugli scenari comuni."
   services="event-hubs"
   documentationCenter="na"
   authors="tomarcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/01/2016"
   ms.author="tarcher" />

# Trasmettere i dati di Diagnostica di Azure nel percorso critico tramite Hub eventi

## Panoramica
Diagnostica di Azure fornisce metodi flessibili per raccogliere le metriche e i registri delle macchine virtuali di calcolo e trasferirli in Archiviazione di Azure. A partire da marzo 2016 (SDK 2.9), è possibile eseguire il sink di Diagnostica di Azure su origini dati completamente personalizzate e trasferire i dati del percorso critico in pochi secondi tramite Hub eventi di Azure.

Tipi di dati supportati:

- Eventi ETW
- Contatori delle prestazioni
- Registri eventi di Windows 
- Log applicazioni
- Registri dell'infrastruttura Diagnostica di Azure
 
In questo articolo verranno illustrate le modalità di configurazione di Diagnostica di Azure con Hub eventi dall'inizio alla fine. Vengono anche fornite linee guida per scenari comuni come ad esempio la personalizzazione di registri e metriche su cui effettuare il sink per Hub eventi, la modifica della configurazione in ogni ambiente, un esempio su come visualizzare i dati dei flussi di numerosi Hub eventi e come risolvere i problemi di connessione.

## Prerequisiti
Il sink dell'Hub eventi in Diagnostica di Azure è supportato su tutti i tipi di macchine di calcolo, tra cui servizio Cloud, macchine virtuali, set di scalabilità di macchine virtuali e Service Fabric, che supportano Diagnostica di Microsoft Azure a partire dall'SDK 2.9 di Azure e dai relativi strumenti di Azure per Visual Studio.
  
- Estensione di Diagnostica Azure 1.6 ([SDK di Azure per .NET 2.9 o versione successiva](https://azure.microsoft.com/downloads/) ha questa destinazione per impostazione predefinita)
- [Visual Studio 2013 o versione successiva](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- La configurazione di Diagnostica di Azure nell'applicazione precedente è stata completata usando un file con estensione *.wadcfgx* e uno dei seguenti metodi:
	- Visual Studio: [Configurazione della diagnostica per i servizi cloud e le macchine virtuali di Azure](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
	- Windows PowerShell: [Abilitare la diagnostica nei servizi Cloud di Azure tramite PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md)
- Spazio dei nomi dell'Hub eventi fornito per l'articolo: [Introduzione all'Hub eventi](./event-hubs-csharp-ephcs-getstarted.md)

## Collegare Diagnostica di Azure al sink dell'Hub eventi
Per impostazione predefinita, Diagnostica di Azure effettua sempre il sink dei registri e delle metriche a un account di archiviazione di Azure. Un'applicazione può anche effettuare il sink all'Hub eventi tramite l'aggiunta di una nuova sezione **Sinks** all'elemento **WadCfg** nella sezione **PublicConfig** del file *.wadcfgx*. In Visual Studio il file *.wadcfgx* viene archiviato in *Progetto servizio cloud > Ruoli > (NomeRuolo) > file diagnostics.wadcfgx*.
  
	  <SinksConfig>
	    <Sink name="HotPath">
	      <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
	    </Sink>
	  </SinksConfig>

In questo esempio, l'URL dell'Hub eventi è impostato sullo spazio dei nomi completo dell'Hub eventi (spazio dei nomi ServiceBus + "/" + nome Hub eventi).

L'URL dell'hub eventi viene visualizzato nel [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885) sul dashboard Hub eventi.

Il nome del **Sink** può essere impostato su qualsiasi stringa valida, purché lo stesso valore venga usato in modo coerente in tutto il file di configurazione.

**Nota:** questa sezione potrebbe contenere altri sink configurati, come "applicationInsights". Diagnostica di Azure consente di definire uno o più sink, purché ogni sink venga dichiarato anche nella sezione **PrivateConfig**.

Il sink dell'Hub eventi deve essere dichiarato e definito anche nella sezione **PrivateConfig** del file di configurazione *.wadcfgx*.

	  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <StorageAccount name="" key="" endpoint="" />
	    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="9B3SwghJOGEUvXigc6zHPInl2iYxrgsKHZoy4nm9CUI=" />
	  </PrivateConfig>

Il valore **SharedAccessKeyName** deve corrispondere a una chiave di firma di accesso condiviso e ai criteri definiti nello spazio dei nomi **ServiceBus/EventHub**. Questa operazione può essere eseguita tramite il dashboard di Hub eventi nel [portale di Azure classico](https://manage.windowsazure.com), selezionando la scheda **Configura** e impostando un criterio denominato (ad es. "SendRule") dotato delle autorizzazioni *Send*. Anche il valore **StorageAccount** viene dichiarato in **PrivateConfig**. Non è necessario modificare i valori, specialmente se funzionano. In questo esempio i valori verranno lasciati vuoti, poiché una risorsa downstream si occuperà di popolarli; ad esempio, il file di configurazione dell'ambiente *ServiceConfiguration.Cloud.cscfg* imposterà i nomi e le chiavi appropriate per l'ambiente.

>[AZURE.WARNING] Tenere presente che la chiave di firma di accesso condiviso di Hub eventi è archiviata in testo normale nel file *.wadcfgx*. Spesso questo file viene archiviato nel controllo del codice sorgente o in una risorsa del server di compilazione, pertanto è necessario proteggerlo adeguatamente. Si consiglia di usare una chiave SAS con autorizzazioni *Send only* in modo che qualsiasi utente malintenzionato possa al massimo solo scrivere nell'Hub eventi, ma mai ascoltarlo o gestirlo.

## Configurare registri e metriche di Diagnostica di Azure su cui effettuare il sink con gli Hub eventi.
Come illustrato in precedenza, il sink automatico viene effettuato per tutti i dati di diagnostica predefiniti e personalizzati (ad esempio, le metriche e i registri) su Archiviazione di Azure agli intervalli configurati. Con Hub eventi (e qualsiasi sink aggiuntivo), è possibile specificare qualsiasi nodo radice o foglia nella gerarchia per effettuare il sink con l'Hub eventi. Questi includono gli eventi ETW, i contatori delle prestazioni, i registri eventi di Windows e i registri dell'applicazione.

È importante valutare il numero di punti dati effettivi da trasferire all'Hub eventi. In genere gli sviluppatori usano questo numero per valutare i dati a bassa latenza del percorso critico che devono essere usati e interpretati rapidamente (ad esempio, monitorando i sistemi di avviso o le regole di scalabilità automatica). Il valore può essere anche usato per configurare un'analisi alternativa o per la ricerca nell'archivio; ad esempio, analisi di flusso, ElasticSearch, un sistema di monitoraggio personalizzato o un sistema di monitoraggio di terze parti preferito.

Di seguito alcuni esempi di configurazioni.

        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>

Nell'esempio che segue, il sink viene applicato al nodo padre **PerformanceCounters** nella gerarchia, che consente l'invio di tutti i nodi figlio **PerformanceCounters** all'Hub eventi.

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
        </PerformanceCounters>

Nell'esempio precedente, il sink è applicato solo a tre contatori: richieste in coda, richieste rifiutate e % tempo processore.

Nell'esempio seguente viene illustrato come uno sviluppatore può assumere il controllo e limitare la quantità di dati inviati come metriche importanti usate per l'integrità del servizio.

        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />

In questo esempio, il sink viene applicato ai registri e filtrato solo per l'analisi a livello di errore.
 
## Distribuzione e aggiornamento di un'applicazione del servizio cloud e della configurazione della diagnostica

Il modo più semplice per distribuire l'applicazione insieme alla configurazione sink dell'Hub eventi è Visual Studio. Per visualizzare e apportare le modifiche desiderate, aprire il file *.wadcfgx* in Visual Studio, archiviato in *Progetto servizio cloud > Ruoli > (NomeRuolo) > file diagnostics.wadcfgx* e salvare una volta terminata l'operazione.

A questo punto, tutte le distribuzioni e i relativi aggiornamenti in Visual Studio, Visual Studio Team System oppure i comandi e gli script basati su MSBUILD (tramite /t: destinazione di pubblicazione) includeranno il file *.wadcfgx* nel processo di creazione dei pacchetti. Assicurarsi che il file venga distribuito in Azure tramite l'estensione dell'agente WAD appropriato all'interno delle macchine virtuali in uso.
  
Al termine della distribuzione dell'applicazione e della configurazione di Diagnostica di Azure, l'attività verrà visualizzata immediatamente nel dashboard dell'Hub eventi. Si tratta di un'indicazione che si è pronti per passare alla visualizzazione dei dati del percorso critico nel client listener o nello strumento di analisi scelto.
 
Nella figura che segue, il dashboard dell'hub evento mostra l'invio corretto di dati di diagnostica all'Hub di eventi, con avvio dopo le 23, quando l'applicazione è stata distribuita con una versione aggiornata del file *.wadcfgx* e il sink è stato configurato correttamente.

![][0]
  
>[AZURE.NOTE] Quando si effettuano gli aggiornamenti del file di configurazione di Diagnostica di Azure (.wadcfgx), si consiglia di effettuare il push degli aggiornamenti per l'intera applicazione e la configurazione mediante la pubblicazione di Visual Studio o tramite script di Windows PowerShell.

## Visualizzazione dei dati del percorso critico

Come illustrato in precedenza, esistono molti casi d'uso per l'ascolto e l'elaborazione dei dati dell'Hub eventi.
  
Un approccio semplice consiste nel creare una piccola applicazione console di verifica per l'ascolto dell'Hub eventi e per la stampa del flusso di output. Il seguente codice (illustrato in dettaglio nell'articolo [Introduzione all'Hub eventi](./event-hubs-csharp-ephcs-getstarted.md)) può essere inserito in un'applicazione console.

L'applicazione console deve includere il [pacchetto EventProcessor Nuget](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).

Sostituire i valori in parentesi tonde nella funzione **Main** in basso con i valori per le proprie risorse.

	//Console application code for EventHub test client
	using System;
	using System.Collections.Generic;
	using System.Diagnostics;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.ServiceBus.Messaging;
	
	namespace EventHubListener
	{
	    class SimpleEventProcessor : IEventProcessor
	    {
	        Stopwatch checkpointStopWatch;
	
	        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
	        {
	            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
	            if (reason == CloseReason.Shutdown)
	            {
	                await context.CheckpointAsync();
	            }
	        }
	
	        Task IEventProcessor.OpenAsync(PartitionContext context)
	        {
	            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
	            this.checkpointStopWatch = new Stopwatch();
	            this.checkpointStopWatch.Start();
	            return Task.FromResult<object>(null);
	        }
	
	        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
	        {
	            foreach (EventData eventData in messages)
	            {
	                string data = Encoding.UTF8.GetString(eventData.GetBytes());
	
	                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
	                    context.Lease.PartitionId, data));
	
	                foreach (var x in eventData.Properties)
	                {
	                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
	                }
	            }
	
	            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
	            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
	            {
	                await context.CheckpointAsync();
	                this.checkpointStopWatch.Restart();
	            }
	        }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            string eventHubConnectionString = "Endpoint= <Your Connection String>”
	            string eventHubName = "<EventHub Name>";
	            string storageAccountName = "<Storage Account Name>";
	            string storageAccountKey = "<Storage Account Key>”;
	            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
	
	            string eventProcessorHostName = Guid.NewGuid().ToString();
	            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
	            Console.WriteLine("Registering EventProcessor...");
	            var options = new EventProcessorOptions();
	            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
	            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
	
	            Console.WriteLine("Receiving. Press enter key to stop worker.");
	            Console.ReadLine();
	            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
	        }
	    }
	}

## Risoluzione dei problemi del sink dell'Hub eventi

- L'Hub eventi non visualizza le attività dell'evento in ingresso o in uscita come previsto

	Verificare che l'Hub eventi esegua correttamente il provisioning. Tutte le informazioni di connessione nella sezione **PrivateConfig** del file *.wadcfgx* devono corrispondere ai valori della risorsa, come illustrato nel portale. Assicurarsi di avere definito un criterio SAS ("SendRule" nell'esempio) nel portale e che sia stata concessa l'autorizzazione Send.

- Dopo un aggiornamento, l'Hub eventi non visualizza più le attività dell'evento in ingresso o in uscita

	Innanzitutto, verificare che le informazioni di configurazione e dell'Hub eventi corrispondano come descritto in alto. Alcuni problemi sono causati dalla sezione **PrivateConfig** che viene reimpostata durante l'aggiornamento della distribuzione. La soluzione consigliata consiste nell'apportare tutte le modifiche nel file *.wadcfgx* del progetto e quindi effettuare il push di un aggiornamento completo dell'applicazione. Se non è possibile, verificare che l'aggiornamento della diagnostica effettui il push della sezione **PrivateConfig** completa, inclusa la chiave della firma di accesso condiviso.

- Ho provato la procedura riportata in alto, ma l'Hub eventi continua a non funzionare

	Effettuare una ricerca nella tabella di Archiviazione di Azure contenente i registri e gli errori della Diagnostica di Azure: **WADDiagnosticInfrastructureLogsTable**. Una possibilità consiste nell'usare uno strumento come [Esplora archivi Azure](http://www.storageexplorer.com) per connettersi a questo account di archiviazione, visualizzare la tabella e aggiungere una Query per il TimeStamp delle ultime 24 ore. È possibile usare lo strumento per esportare il file CSV e aprirlo in un'applicazione come Microsoft Excel, poiché Excel semplifica la ricerca di stringhe di presentazione come "EventHubs" per visualizzare l'errore segnalato.

## Passaggi successivi
• [Altre informazioni su Hub eventi](https://azure.microsoft.com/services/event-hubs/)

## Appendice: completare l'esempio di file (.wadcfgx) della configurazione di Diagnostica di Azure
	<?xml version="1.0" encoding="utf-8"?>
	<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <WadCfg>
	      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
	        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
	        <Directories scheduledTransferPeriod="PT1M">
	          <IISLogs containerName="wad-iis-logfiles" />
	          <FailedRequestLogs containerName="wad-failedrequestlogs" />
	        </Directories>
	        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
	          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
	          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
	        </PerformanceCounters>
	        <WindowsEventLog scheduledTransferPeriod="PT1M">
	          <DataSource name="Application!*" />
	        </WindowsEventLog>
	        <CrashDumps>
	          <CrashDumpConfiguration processName="WaIISHost.exe" />
	          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
	          <CrashDumpConfiguration processName="w3wp.exe" />
	        </CrashDumps>
	        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
	      </DiagnosticMonitorConfiguration>
	      <SinksConfig>
	        <Sink name="HotPath">
	          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
	        </Sink>
	        <Sink name="applicationInsights">
	          <ApplicationInsights />
	          <Channels>
	            <Channel logLevel="Error" name="errors" />
	          </Channels>
	        </Sink>
	      </SinksConfig>
	    </WadCfg>
	    <StorageAccount />
	  </PublicConfig>
	  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <StorageAccount name="" key="" endpoint="" />
	    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
	  </PrivateConfig>
	  <IsEnabled>true</IsEnabled>
	</DiagnosticsConfiguration>

Il file *ServiceConfiguration.Cloud.cscfg* complementare per questo esempio si presenta come segue.

	<?xml version="1.0" encoding="utf-8"?>
	<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
	  <Role name="MyFixIt.WorkerRole">
	    <Instances count="1" />
	    <ConfigurationSettings>
	      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
	    </ConfigurationSettings>
	  </Role>
	</ServiceConfiguration>

<!-- Images. -->
[0]: ./media/event-hubs-streaming-azure-diags-data/dashboard.png

<!---HONumber=AcomDC_0608_2016-->
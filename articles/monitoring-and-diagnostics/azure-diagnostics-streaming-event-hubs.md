---
title: Trasmettere i dati di Diagnostica di Azure nel percorso critico con Hub eventi | Microsoft Docs
description: Configurazione di Diagnostica di Azure con Hub eventi end-to-end, tra cui indicazioni per scenari comuni.
services: event-hubs
documentationcenter: na
author: rboucher
manager: carmonm
editor: 
ms.assetid: edeebaac-1c47-4b43-9687-f28e7e1e446a
ms.service: monitoring-and-diagnostics
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/13/2017
ms.author: robb
ms.openlocfilehash: 1c05bd6dc4c4d394aa043b9995de9c184e4f14c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Trasmettere i dati di Diagnostica di Azure nel percorso critico tramite Hub eventi
Diagnostica di Azure fornisce metodi flessibili per raccogliere le metriche e i log delle macchine virtuali (VM) di servizi cloud e trasferire i risultati in Archiviazione di Azure. A partire da marzo 2016 (SDK 2.9), è possibile eseguire inviare la diagnostica a origini dati completamente personalizzate e trasferire i dati del percorso critico in pochi secondi tramite [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/).

Tipi di dati supportati:

* Tracciamento degli eventi di Windows (ETW)
* Contatori delle prestazioni
* Registri eventi di Windows
* Log applicazioni
* Log dell'infrastruttura Diagnostica di Azure

Questo articolo illustra le modalità di configurazione di Diagnostica di Azure con Hub eventi dall'inizio alla fine. Vengono inoltre fornite delle linee guida per gli scenari comuni seguenti:

* Come personalizzare le metriche e i log inviati a Hub eventi
* Come modificare le configurazioni in ciascun ambiente
* Come visualizzare i dati di flusso di Hub eventi
* Come risolvere i problemi di connessione  

## <a name="prerequisites"></a>Prerequisiti
La ricezione di dati di Diagnostica di Azure in Hub eventi è supportata in Servizi cloud, macchine virtuali, set di scalabilità di macchine virtuali e Service Fabric a partire da Azure SDK 2.9 e dalla versione corrispondente di Strumenti di Azure per Visual Studio.

* Estensione di Diagnostica Azure 1.6 ([SDK di Azure per .NET 2.9 o versione successiva](https://azure.microsoft.com/downloads/) ha questa destinazione per impostazione predefinita)
* [Visual Studio 2013 o versione successiva](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* Configurazioni esistenti di Diagnostica di Azure in un'applicazione usando un file *.wadcfgx* e uno dei metodi seguenti:
  * Visual Studio: [Configurazione della diagnostica per i servizi cloud e le macchine virtuali di Azure](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
  * Windows PowerShell: [Abilitare la diagnostica nei servizi Cloud di Azure tramite PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md)
* Provisioning dello spazio dei nomi dell'Hub eventi eseguito in base all'articolo [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Collegare Diagnostica di Azure al sink dell'Hub eventi
Per impostazione predefinita, Diagnostica di Azure invia sempre log e metriche a un account di archiviazione di Azure. Un'applicazione può anche inviare dati agli Hub eventi aggiungendo una nuova sezione **Sinks** nell'elemento **PublicConfig** / **WadCfg** del file *.wadcfgx*. In Visual Studio il file *.wadcfgx* viene archiviato nel percorso di destinazione seguente: **Progetto servizio cloud** > **Ruoli** > **(NomeRuolo)** > **file diagnostics.wadcfgx**.

```xml
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "HotPath",
            "EventHub": {
                "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                "SharedAccessKeyName": "SendRule"
            }
        }
    ]
}
```

In questo esempio l'URL dell'Hub eventi è impostato sullo spazio dei nomi completo dell'Hub eventi: spazio dei nomi di Hub eventi + "/" + nome Hub eventi.  

L'URL dell'Hub eventi viene visualizzato nel [Portale di Azure](http://go.microsoft.com/fwlink/?LinkID=213885) sul dashboard di Hub eventi.  

Il nome del **Sink** può essere impostato su qualsiasi stringa valida, purché lo stesso valore venga usato in modo coerente in tutto il file di configurazione.

> [!NOTE]
> In questa sezione potrebbero esserci altri sink configurati, come ad esempio *applicationInsights* . Diagnostica di Azure consente di definire uno o più sink, ammesso che ciascun sink venga dichiarato anche nella sezione **PrivateConfig** .  
>
>

Il sink dell'Hub eventi deve essere dichiarato e definito anche nella sezione **PrivateConfig** del file di configurazione *.wadcfgx* .

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="{account name}" key="{account key}" endpoint="{optional storage endpoint}" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
</PrivateConfig>
```
```JSON
{
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{optional storage endpoint}",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```

Il valore `SharedAccessKeyName` deve corrispondere a una chiave di firma di accesso condiviso e ai criteri definiti nello spazio dei nomi di **Hub eventi** . Passare al dashboard Hub eventi nel [portale di Azure](https://manage.windowsazure.com), selezionare la scheda **Configura** e impostare un criterio denominato (ad esempio, "SendRule") che dispone delle autorizzazioni *di invio* . Anche il valore **StorageAccount** viene dichiarato in **PrivateConfig**. Non è necessario modificare i valori se funzionano. In questo esempio, i valori vengono lasciati vuoti, poiché verranno impostati da un asset downstream. Ad esempio, il file di configurazione dell'ambiente *ServiceConfiguration.Cloud.cscfg* consente l'impostazione delle chiavi e dei nomi appropriati per l'ambiente.  

> [!WARNING]
> La chiave di firma di accesso condiviso di Hub eventi è archiviata in testo normale nel file *.wadcfgx* . Spesso questa chiave viene archiviata nel controllo del codice sorgente o risulta disponibile in un asset del server di compilazione, pertanto è necessario proteggerla in maniera appropriata. Si consiglia di usare una chiave SAS con autorizzazioni *Send only* in modo che qualsiasi utente malintenzionato possa al massimo scrivere nell'Hub eventi, ma non ascoltarlo o gestirlo.
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>Configurare la Diagnostica di Azure per inviare log e metriche a Hub eventi
Come illustrato in precedenza, tutti i dati di diagnostica predefiniti e personalizzati (ovvero metriche e log) vengono inviati automaticamente ad Archiviazione di Azure agli intervalli configurati. Con Hub eventi (e qualsiasi sink aggiuntivo), è possibile specificare qualsiasi nodo radice o foglia nella gerarchia da inviare a Hub eventi. Questi includono gli eventi ETW, i contatori delle prestazioni, i registri eventi di Windows e i registri dell'applicazione.   

È importante valutare il numero di punti dati effettivi da trasferire all'Hub eventi. Di solito gli sviluppatori trasferiscono i dati del percorso critico a bassa latenza che devono essere utilizzati e interpretati rapidamente. I sistemi che monitorano gli avvisi o le regole di scalabilità automatica sono degli esempi. Uno sviluppatore può anche configurare un archivio di analisi o di ricerca alternativo; ad esempio, Analisi di flusso di Azure, ElasticSearch, un sistema di monitoraggio personalizzato o un sistema di monitoraggio preferito di terze parti.

Di seguito alcuni esempi di configurazioni.

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "HotPath",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```

Nell'esempio precedente poiché il sink viene applicato al nodo padre **PerformanceCounters** nella gerarchia, tutti i nodi figlio **PerformanceCounters** verranno inviati agli hub eventi.  

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        }
    ]
}
```

Nell'esempio precedente il sink è applicato solo a tre contatori: **richieste in coda**, **richieste rifiutate** e **% tempo processore**.  

Nell'esempio seguente viene illustrato come uno sviluppatore può limitare la quantità di dati inviati come metriche importanti che vengono usate per l'integrità del servizio.  

```XML
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```
```JSON
"Logs": {
    "scheduledTransferPeriod": "PT1M",
    "scheduledTransferLogLevelFilter": "Error",
    "sinks": "HotPath"
}
```

In questo esempio, il sink viene applicato ai registri e filtrato solo per l'analisi a livello di errore.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Distribuire e aggiornare un'applicazione dei servizi cloud e della configurazione della diagnostica
Visual Studio offre il modo più semplice per distribuire l'applicazione e la configurazione sink dell'Hub eventi. Per visualizzare e modificare il file, aprire il file *.wadcfgx* in Visual Studio, modificarlo e salvarlo. Il percorso è **Progetto servizio cloud** > **Ruoli** > **(NomeRuolo)** > **diagnostics.wadcfgx**.  

A questo punto, tutte le operazioni di distribuzione e di aggiornamento delle distribuzioni in Visual Studio, Visual Studio Team System e tutti i comandi o script che si basano su MSBuild e usano la destinazione **/t:publish** includeranno il file *.wadcfgx* nel processo di creazione dei pacchetti. Le distribuzioni e gli aggiornamenti distribuiscono anche il file in Azure tramite l'appropriata estensione agente di Diagnostica di Azure nelle macchine virtuali.

Al termine della distribuzione dell'applicazione e della configurazione di Diagnostica di Azure, l'attività verrà visualizzata immediatamente nel dashboard dell'Hub eventi. Ciò indica che si è pronti per passare alla visualizzazione dei dati del percorso critico nel client listener o nello strumento di analisi scelto.  

Nella figura seguente, il dashboard Hub eventi mostra l'invio integro dei dati di diagnostica all'Hub eventi a partire da un certo momento dopo le 23.00, ossia quando l'applicazione è stata distribuita con una versione aggiornata del file *.wadcfgx* e il sink è stato configurato correttamente.

![][0]  

> [!NOTE]
> Quando si eseguono gli aggiornamenti del file di configurazione di Diagnostica di Azure (.wadcfgx), si consiglia di eseguire il push degli aggiornamenti per l'intera applicazione e la configurazione tramite la pubblicazione di Visual Studio o tramite script di Windows PowerShell.  
>
>

## <a name="view-hot-path-data"></a>Visualizzare i dati del percorso critico
Come illustrato in precedenza, esistono molti casi d'uso per l'ascolto e l'elaborazione dei dati dell'Hub eventi.

Un approccio semplice consiste nel creare una piccola applicazione console di verifica per l'ascolto dell'Hub eventi e per la stampa del flusso di output. È possibile inserire il seguente codice (descritto dettagliatamente nell'articolo [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)) in un'applicazione console.  

L'applicazione console deve includere il [pacchetto Event Processor Host NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

Sostituire i valori in parentesi tonde nella funzione **Main** con i valori per le proprie risorse.   

```csharp
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
            string eventHubConnectionString = "Endpoint= <your connection string>”
            string eventHubName = "<Event hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>”;
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
```

## <a name="troubleshoot-event-hubs-sinks"></a>Risoluzione dei problemi relativi ai sink dell'Hub eventi
* L'Hub eventi non visualizza le attività dell'evento in ingresso o in uscita come previsto.

    Verificare che l'Hub eventi esegua correttamente il provisioning. Tutte le informazioni di connessione nella sezione **PrivateConfig** del file *.wadcfgx* devono corrispondere ai valori della risorsa, come illustrato nel portale. Assicurarsi di avere definito un criterio SAS ("SendRule" nell'esempio) nel portale e che sia stata concessa l'autorizzazione di *invio* .  
* Dopo un aggiornamento, l'Hub eventi non visualizza più le attività dell'evento in ingresso o in uscita.

    In primo luogo, assicurarsi che le informazioni di configurazione e dell'Hub eventi siano esatte, come spiegato in precedenza. Capita che a volte **PrivateConfig** venga reimpostato in un aggiornamento della distribuzione. La soluzione consigliata consiste nell'apportare tutte le modifiche nel file *.wadcfgx* del progetto e quindi eseguire il push di un aggiornamento completo dell'applicazione. Se non è possibile, verificare che l'aggiornamento della diagnostica esegua il push della sezione **PrivateConfig** completa, che include la chiave SAS.  
* Ho provato a seguire i suggerimenti, ma l'Hub eventi continua a non funzionare.

    Eseguire una ricerca nella tabella di Archiviazione di Azure contenente i registri e gli errori della Diagnostica di Azure: **WADDiagnosticInfrastructureLogsTable**. Una possibilità consiste nell'usare uno strumento come [Esplora archivi Azure](http://www.storageexplorer.com) per connettersi a questo account di archiviazione, visualizzare la tabella e aggiungere una query per il TimeStamp delle ultime 24 ore. È possibile utilizzare lo strumento per esportare un file .csv e aprirlo in un'applicazione come Microsoft Excel. Excel semplifica la ricerca di stringhe di presentazione come **EventHubs**per visualizzare l'errore segnalato.  

## <a name="next-steps"></a>Passaggi successivi
• [Altre informazioni su Hub eventi](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Appendice: completare l'esempio di file (.wadcfgx) della configurazione di Diagnostica di Azure
```xml
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
    <StorageAccount>ACCOUNT_NAME</StorageAccount>
  </PublicConfig>
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="{account name}" key="{account key}" endpoint="{storage endpoint}" />
    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
  </PrivateConfig>
  <IsEnabled>true</IsEnabled>
</DiagnosticsConfiguration>
```

Il file *ServiceConfiguration.Cloud.cscfg* complementare per questo esempio si presenta come segue.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

Le impostazioni basate su Json equivalenti per le macchine virtuali sono le seguenti:
```JSON
"settings": {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 4096,
            "sinks": "applicationInsights.errors",
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "Directories": {
                "scheduledTransferPeriod": "PT1M",
                "IISLogs": {
                    "containerName": "wad-iis-logfiles"
                },
                "FailedRequestLogs": {
                    "containerName": "wad-failedrequestlogs"
                }
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "HotPath",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Memory\\Available MBytes",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\Bytes Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Requests/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Errors Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Queued",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Rejected",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "DataSource": [
                    {
                        "name": "Application!*"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Error",
                "sinks": "HotPath"
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "HotPath",
                    "EventHub": {
                        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
                        "SharedAccessKeyName": "SendRule"
                    }
                },
                {
                    "name": "applicationInsights",
                    "ApplicationInsights": "",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "errors"
                            }
                        ]
                    }
                }
            ]
        }
    },
    "StorageAccount": "{account name}"
}


"protectedSettings": {
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{storage endpoint}",
    "EventHub": {
        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "YOUR_KEY_HERE"
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Creare un hub eventi](../event-hubs/event-hubs-create.md)
* [Domande frequenti su Hub eventi](../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png

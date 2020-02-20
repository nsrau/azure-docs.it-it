---
title: Inviare dati dall'estensione diagnostica di Microsoft Azure a hub eventi di Azure
description: Configurare l'estensione di diagnostica in monitoraggio di Azure per inviare i dati all'hub eventi di Azure in modo da poterli inoltrare ai percorsi esterni ad Azure.
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 573a56c537e48687e310acff8639c50d0d0c6e3d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467964"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Inviare dati dall'estensione diagnostica di Microsoft Azure a hub eventi di Azure
L'estensione diagnostica di Azure è un agente di monitoraggio di Azure che raccoglie i dati di monitoraggio dal sistema operativo guest e i carichi di lavoro delle macchine virtuali di Azure e di altre risorse di calcolo. Questo articolo descrive come inviare i dati dall'estensione di diagnostica di Azure (WAD) a [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) in modo da poter inoltrare a posizioni esterne ad Azure.

## <a name="supported-data"></a>Dati supportati

I dati raccolti dal sistema operativo guest che è possibile inviare a hub eventi includono quanto segue. Le altre origini dati raccolte da WAD, inclusi i log di IIS e i dump di arresto anomalo del sistema, non possono essere inviate a hub eventi.

* Tracciamento degli eventi di Windows (ETW)
* Contatori delle prestazioni
* Registri eventi di Windows, inclusi i log applicazioni nel registro eventi di Windows
* Log dell'infrastruttura Diagnostica di Azure

## <a name="prerequisites"></a>Prerequisiti

* Windows Diagnostics Extension 1,6 o versione successiva. Per una cronologia delle versioni e una [Panoramica delle estensioni diagnostica di Azure](diagnostics-extension-overview.md) per le risorse supportate, vedere [diagnostica di Azure cronologia e versioni dello schema di configurazione dell'estensione](diagnostics-extension-versions.md) .
* È necessario eseguire sempre il provisioning dello spazio dei nomi di hub eventi. Per informazioni dettagliate, vedere [Introduzione a hub eventi](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) .


## <a name="configuration-schema"></a>Schema di configurazione
Vedere [Install and Configure Windows Azure Diagnostics Extension (WAD)](diagnostics-extension-windows-install.md) per diverse opzioni per l'abilitazione e la configurazione dell'estensione di diagnostica e [diagnostica di Azure schema di configurazione](diagnostics-extension-schema-windows.md) per un riferimento allo schema di configurazione. Nella parte restante di questo articolo verrà descritto come usare questa configurazione per inviare dati a un hub eventi. 

Diagnostica di Azure invia sempre log e metriche a un account di archiviazione di Azure. È possibile configurare uno o più *sink di dati* che inviano dati a percorsi aggiuntivi. Ogni sink viene definito nell' [elemento SinksConfig](diagnostics-extension-schema-windows.md#sinksconfig-element) della configurazione pubblica con informazioni riservate nella configurazione privata. Questa configurazione per hub eventi USA i valori riportati nella tabella seguente.

| Proprietà | Descrizione |
|:---|:---|
| Name | Nome descrittivo per il sink. Utilizzato nella configurazione per specificare le origini dati da inviare al sink. |
| URL  | URL dell'hub eventi nel formato \<Hub eventi-spazio dei nomi\>. servicebus.windows.net/\<Event-Hub-Name\>.          |
| SharedAccessKeyName | Nome di un criterio di accesso condiviso per l'hub eventi con almeno autorità di **trasmissione** . |
| SharedAccessKey     | Chiave primaria o secondaria dal criterio di accesso condiviso per l'hub eventi. |

Di seguito sono elencate le configurazioni pubbliche e private di esempio. Si tratta di una configurazione minima con un singolo contatore delle prestazioni e registro eventi per illustrare come configurare e usare il sink di dati dell'hub eventi. Per un esempio più complesso, vedere [diagnostica di Azure schema di configurazione](diagnostics-extension-schema-windows.md) .

### <a name="public-configuration"></a>Configurazione pubblica

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120
        },
        "PerformanceCounters": {
            "scheduledTransferPeriod": "PT1M",
            "sinks": "myEventHub",
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "sinks": "myEventHub",
                "DataSource": [
                {
                    "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                }
            ]
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>Configurazione privata

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>Opzioni di configurazione
Per inviare dati a un sink di dati, è necessario specificare l'attributo **sinks** nel nodo dell'origine dati. Dove si posiziona l'attributo **sinks** determina l'ambito dell'assegnazione. Nell'esempio seguente, l'attributo **sinks** viene definito nel nodo **PerformanceCounters** che comporterà l'invio di tutti i contatori delle prestazioni figlio all'hub eventi.

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
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


Nell'esempio seguente l'attributo **sinks** viene applicato direttamente a tre contatori che determineranno l'invio solo dei contatori delle prestazioni all'hub eventi. 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
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
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>Convalida della configurazione
È possibile usare diversi metodi per convalidare l'invio dei dati all'hub eventi. il metodo semplice prevede l'uso dell'acquisizione di hub eventi, come descritto in [acquisizione di eventi tramite hub eventi di Azure in archiviazione BLOB di Azure o Azure Data Lake storage](../../event-hubs/event-hubs-capture-overview.md). 


## <a name="troubleshoot-event-hubs-sinks"></a>Risoluzione dei problemi relativi ai sink dell'Hub eventi

- Esaminare la tabella di archiviazione di Azure **WADDiagnosticInfrastructureLogsTable** che contiene log ed errori per diagnostica di Azure stesso. Una possibilità consiste nell'usare uno strumento come [Esplora archivi Azure](https://www.storageexplorer.com) per connettersi a questo account di archiviazione, visualizzare la tabella e aggiungere una query per il TimeStamp delle ultime 24 ore. È possibile utilizzare lo strumento per esportare un file .csv e aprirlo in un'applicazione come Microsoft Excel. Excel semplifica la ricerca di stringhe di presentazione come **EventHubs**per visualizzare l'errore segnalato.  

- Verificare che l'Hub eventi esegua correttamente il provisioning. Tutte le informazioni di connessione nella sezione **PrivateConfig** della configurazione devono corrispondere ai valori della risorsa come visualizzato nel portale. Assicurarsi di avere definito un criterio di firma di accesso condiviso (*SendRule* nell'esempio) nel portale e che sia concessa l'autorizzazione di *invio* .  

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Hub eventi](../../event-hubs/event-hubs-about.md)
* [Creare un hub eventi](../../event-hubs/event-hubs-create.md)
* [Domande frequenti su Hub eventi](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png




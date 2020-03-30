---
title: Inviare dati dall'estensione di diagnostica di Windows Azure agli hub eventi di AzureSend data from Windows Azure diagnostics extension to Azure Event Hubs
description: Configurare l'estensione di diagnostica in Monitoraggio di Azure per inviare dati all'hub eventi di Azure in modo da poterli inoltrare a posizioni esterne ad Azure.Configure diagnostics extension in Azure Monitor to send data to Azure Event Hub so you can forward it to locations outside of Azure.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 5e5034e99d37d3681192c2ad066f28acd1c4aeeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672532"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Inviare dati dall'estensione di diagnostica di Windows Azure agli hub eventi di AzureSend data from Windows Azure diagnostics extension to Azure Event Hubs
L'estensione di diagnostica di Azure è un agente in Monitoraggio di Azure che raccoglie i dati di monitoraggio dal sistema operativo guest e dai carichi di lavoro delle macchine virtuali di Azure e di altre risorse di calcolo. Questo articolo descrive come inviare dati dall'estensione diagnostica di Windows Azure agli hub eventi di Azure in modo da poter eseguire l'inoltro a posizioni esterne ad Azure.This article describes how to send data from the Windows Azure Diagnostic extension (WAD) to [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) so you can forward to locations outside of Azure.

## <a name="supported-data"></a>Dati supportati

I dati raccolti dal sistema operativo guest che possono essere inviati agli hub eventi includono quanto segue. Altre origini dati raccolte da WAD, inclusi i registri IIS e i dump di arresto anomalo del sistema, non possono essere inviate agli hub eventi.

* Tracciamento degli eventi di Windows (ETW)
* Contatori delle prestazioni
* Registri eventi di Windows, inclusi i registri applicazioni nel registro eventi di Windows
* Log dell'infrastruttura Diagnostica di Azure

## <a name="prerequisites"></a>Prerequisiti

* Estensione diagnostica Windows 1.6 o successiva. Vedere Versioni e cronologia dello schema di [configurazione dell'estensione di Diagnostica di Azure](diagnostics-extension-versions.md) per una cronologia delle versioni e [Panoramica dell'estensione Diagnostica di Azure](diagnostics-extension-overview.md) per le risorse supportate.
* È sempre necessario eseguire il provisioning dello spazio dei nomi Hub eventi. Per informazioni dettagliate, vedere [Introduzione agli hub eventi.](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)


## <a name="configuration-schema"></a>Schema di configurazione
Per informazioni di riferimento sullo schema di configurazione di Diagnostica e configurazione, vedere [Installare e configurare l'estensione di diagnostica di Windows Azure (WAD)](diagnostics-extension-windows-install.md) per diverse opzioni per l'abilitazione e la configurazione dell'estensione di diagnostica e dello schema di configurazione di Diagnostica di Azure.See Install and configure Windows Azure diagnostics extension (WAD) for different options for enabling and configuring the diagnostics extension and [Azure Diagnostics configuration schema](diagnostics-extension-schema-windows.md) for a reference of the configuration schema. Il resto di questo articolo descriverà come usare questa configurazione per inviare dati a un hub eventi. 

Diagnostica di Azure invia sempre log e metriche a un account di archiviazione di Azure.Azure Diagnostics always sends logs and metrics to an Azure Storage account. È possibile configurare uno o più *sink di dati* che inviano dati a posizioni aggiuntive. Ogni sink è definito [nell'elemento SinksConfig](diagnostics-extension-schema-windows.md#sinksconfig-element) della configurazione pubblica con informazioni riservate nella configurazione privata. Questa configurazione per gli hub eventi utilizza i valori nella tabella seguente.

| Proprietà | Descrizione |
|:---|:---|
| Nome | Nome descrittivo per il sink. Utilizzato nella configurazione per specificare le origini dati da inviare al sink. |
| URL  | URL dell'hub eventi \<nel formato event-hubs-namespace\>.servicebus.windows.net/\<nome-hub-evento\>.          |
| Nome Chiave Condivisa | Nome di un criterio di accesso condiviso per l'hub eventi con almeno l'autorità **Invia.** |
| SharedAccessKey     | Chiave primaria o secondaria dei criteri di accesso condiviso per l'hub eventi. |

Esempi di configurazioni pubbliche e private sono mostrate di seguito. Si tratta di una configurazione minima con un singolo contatore delle prestazioni e un log eventi per illustrare come configurare e usare il sink di dati dell'hub eventi. Vedere Schema di configurazione di [Diagnostica di Azure](diagnostics-extension-schema-windows.md) per un esempio più complesso.

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
Per inviare dati a un sink di dati, specificare l'attributo **sinks** nel nodo dell'origine dati. La posizione dell'attributo **sinks** determina l'ambito dell'assegnazione. Nell'esempio seguente, l'attributo **sinks** viene definito nel nodo **PerformanceCounters** che causerà l'invio di tutti i contatori delle prestazioni figlio all'hub eventi.

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


Nell'esempio seguente, l'attributo **sinks** viene applicato direttamente a tre contatori che causeranno solo l'invio di tali contatori delle prestazioni all'hub eventi. 

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
È possibile utilizzare diversi metodi per verificare che i dati vengano inviati all'hub eventi. Un metodo semplice consiste nell'usare l'acquisizione di hub eventi come descritto in [Acquisire eventi tramite Hub eventi](../../event-hubs/event-hubs-capture-overview.md)di Azure in Archiviazione BLOB di Azure o Archiviazione di Azure Data Lake . 


## <a name="troubleshoot-event-hubs-sinks"></a>Risoluzione dei problemi relativi ai sink dell'Hub eventi

- Esaminare la tabella di Archiviazione di Azure **WADDiagnosticInfrastructureLogsTable** che contiene log ed errori per Diagnostica di Azure. Una possibilità consiste nell'usare uno strumento come [Esplora archivi Azure](https://www.storageexplorer.com) per connettersi a questo account di archiviazione, visualizzare la tabella e aggiungere una query per il TimeStamp delle ultime 24 ore. È possibile utilizzare lo strumento per esportare un file .csv e aprirlo in un'applicazione come Microsoft Excel. Excel semplifica la ricerca di stringhe di presentazione come **EventHubs**per visualizzare l'errore segnalato.  

- Verificare che l'Hub eventi esegua correttamente il provisioning. Tutte le informazioni di connessione nella sezione **PrivateConfig** della configurazione devono corrispondere ai valori della risorsa visualizzati nel portale. Assicurarsi di avere un criterio di disponibilità di accesso utenti definito definito (*SendRule* nell'esempio) nel portale e che l'autorizzazione *di invio* è concessa.  

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Hub eventi](../../event-hubs/event-hubs-about.md)
* [Creare un hub eventi](../../event-hubs/event-hubs-create.md)
* [Domande frequenti su Hub eventi](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png




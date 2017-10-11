---
title: Aggregazione di eventi di Azure Service Fabric con Diagnostica di Microsoft Azure | Microsoft Docs
description: Informazioni sull'aggregazione e la raccolta di eventi con Diagnostica di Microsoft Azure per il monitoraggio e la diagnostica dei cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2017
ms.author: dekapur
ms.openlocfilehash: 5773361fdec4cb8ee54fa2856f6aa969d5dac4e9
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Aggregazione e raccolta di eventi con Diagnostica di Microsoft Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Quando si esegue un cluster Azure Service Fabric, è consigliabile raccogliere i log da tutti i nodi in una posizione centrale. Il salvataggio dei log in una posizione centrale semplifica l'analisi e la risoluzione di eventuali problemi nel cluster o nelle applicazioni e nei servizi in esecuzione nel cluster.

Un modo per caricare e raccogliere i log consiste nell'usare l'estensione Diagnostica di Microsoft Azure, che carica i log in Archiviazione di Azure e offre anche la possibilità di inviarli ad Azure Application Insights o Hub eventi. È anche possibile usare un processo esterno per leggere gli eventi dalla risorsa di archiviazione e inserirli in una piattaforma di analisi come [OMS Log Analytics](../log-analytics/log-analytics-service-fabric.md) o un'altra soluzione di analisi di log.

## <a name="prerequisites"></a>Prerequisiti
Per eseguire alcune delle operazioni descritte in questo documento vengono usati gli strumenti seguenti:

* [Diagnostica di Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (correlata ai Servizi cloud di Azure, include alcune informazioni ed esempi utili)
* [Gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Client di Azure Resource Manager](https://github.com/projectkudu/ARMClient)
* [Modello di Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="log-and-event-sources"></a>Origini di log ed eventi

### <a name="service-fabric-platform-events"></a>Eventi della piattaforma Service Fabric
Come illustrato in [questo articolo](service-fabric-diagnostics-event-generation-infra.md), Service Fabric offre alcuni canali di registrazione predefiniti. Tra questi, i canali seguenti sono facilmente configurabili con Diagnostica di Microsoft Azure per inviare i dati di monitoraggio e diagnostica a una tabella di archiviazione o un'altra posizione.
  * Eventi operativi: operazioni generali eseguite dalla piattaforma Service Fabric. Gli esempi includono la creazione di applicazioni e servizi, le modifiche allo stato dei nodi e informazioni sull'aggiornamento. Questi eventi vengono generati come log di Event Tracing for Windows (ETW)
  * [Eventi del modello di programmazione Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
  * [Eventi relativi al modello di programmazione Reliable Services](service-fabric-reliable-services-diagnostics.md)

### <a name="application-events"></a>Eventi dell'applicazione
 Eventi generati dal codice delle applicazioni e dei servizi e scritti con la classe helper EventSource disponibile nei modelli di Visual Studio. Per altre informazioni su come scrivere log EventSource dall'applicazione, vedere [Monitorare e diagnosticare servizi in una configurazione di sviluppo con computer locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

## <a name="deploy-the-diagnostics-extension"></a>Distribuire l'estensione Diagnostica
Il primo passaggio per la raccolta dei log consiste nel distribuire l'estensione Diagnostica in ogni VM del cluster Service Fabric. Questa estensione raccoglie i log in ogni VM e li carica nell'account di archiviazione specificato. La procedura varia a seconda che si usi il portale di Azure oppure Azure Resource Manager. Anche i passaggi variano se la distribuzione fa parte della creazione del cluster o è relativa a un cluster già esistente. Ecco la procedura per ogni scenario.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Distribuire l'estensione Diagnostica nell'ambito della creazione del cluster tramite il portale di Azure
Per distribuire l'estensione Diagnostica nelle VM del cluster nell'ambito della creazione del cluster, si usa il pannello delle impostazioni di diagnostica illustrato nell'immagine seguente. Verificare che l'opzione Diagnostica sia impostata su **Sì** (impostazione predefinita). Dopo aver creato il cluster, non è possibile modificare questa impostazione tramite il portale.

![Impostazioni di diagnostica di Azure nel portale per la creazione del cluster](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics.png)

Quando si crea un cluster con il portale, è consigliabile scaricare il modello **prima di fare clic su OK** per creare il cluster. Per informazioni dettagliate, vedere [Configurare un cluster di Service Fabric usando un modello di Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Il modello è necessario per apportare modifiche in un secondo momento perché non è possibile apportare alcune modifiche tramite il portale.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Distribuire l’estensione Diagnostica come parte della creazione di cluster tramite Gestione risorse di Azure
Per creare un cluster tramite Resource Manager, è necessario aggiungere il file JSON di configurazione di Diagnostica al modello di Resource Manager di tipo cluster completo prima di creare il cluster. Gli esempi relativi ai modelli di Gestione risorse includono un modello di cluster con 5 VM con aggiunta della configurazione di Diagnostica, disponibile nella raccolta di esempi di Azure nella pagina relativa all'[esempio di modello di Resource Manager di cluster con cinque nodi con Diagnostica](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad).

Per visualizzare l'impostazione di Diagnostica nel modello di Resource Manager, aprire il file azuredeploy.json e cercare **IaaSDiagnostics**. Per creare un cluster con questo modello, è sufficiente selezionare il pulsante di **distribuzione in Azure** disponibile nel collegamento precedente.

In alternativa, è possibile scaricare l'esempio di Resource Manager, modificarlo e creare un cluster con il modello modificato mediante il comando `New-AzureRmResourceGroupDeployment` in una finestra di Azure PowerShell. Per i parametri passati al comando, vedere il codice seguente. Per informazioni dettagliate sulla distribuzione di un gruppo di risorse con PowerShell, vedere l'articolo su come [distribuire un gruppo di risorse con un modello di Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>Distribuire l'estensione Diagnostica in un cluster esistente
Se in un cluster esistente non è stata distribuita l'estensione Diagnostica o se si vuole modificare una configurazione esistente, è possibile aggiungerla o aggiornarla. Modificare il modello di Resource Manager usato per creare il cluster esistente o scaricare il modello dal portale come descritto in precedenza. Modificare il file template.json eseguendo le attività seguenti.

Aggiungere una nuova risorsa di archiviazione al modello nella sezione risorse.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Aggiungere quindi alla sezione parametri subito dopo le definizioni dell'account di archiviazione, tra `supportLogStorageAccountName` e `vmNodeType0Name`. Sostituire il testo segnaposto *storage account name goes here* con il nome dell'account di archiviazione.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Aggiornare quindi la sezione `VirtualMachineProfile` del file template.json aggiungendo quanto segue all'interno della matrice delle estensioni. Assicurarsi di aggiungere una virgola all'inizio o alla fine, a seconda del punto di inserimento.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Dopo aver modificato il file template.json come descritto, pubblicare nuovamente il modello di Resource Manager. Se il modello è stato esportato, eseguire il file deploy.ps1 per pubblicarlo di nuovo. Dopo la distribuzione, assicurarsi che il valore di **ProvisioningState** sia **Succeeded**.

## <a name="collect-health-and-load-events"></a>Raccogliere gli eventi di integrità e di caricamento

A partire dalla versione 5.4 di Service Fabric è possibile raccogliere gli eventi di metrica di integrità e caricamento. Questi riflettono gli eventi generati dal sistema o dal codice usando le API di creazione di report di integrità o caricamento, ad esempio [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) o [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Ciò consente l'aggregazione e la visualizzazione dell'integrità del sistema nel tempo, nonché la generazione di avvisi in base a eventi di integrità o di caricamento. Per visualizzare tali eventi nel visualizzatore eventi di diagnostica di Visual Studio, aggiungere "Microsoft-ServiceFabric:4:0x4000000000000008" all'elenco di provider ETW.

Per raccogliere gli eventi, modificare il modello di Resource Manager in modo da includere

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387912",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

## <a name="collect-reverse-proxy-events"></a>Raccogliere eventi del proxy inverso

A partire dalla versione 5.7 di Service Fabric, è possibile raccogliere gli eventi del [proxy inverso](service-fabric-reverseproxy.md).
Il proxy inverso emette eventi in due canali, uno dei quali contiene gli eventi di errore corrispondenti agli errori di elaborazione delle richieste e l'altro contiene gli eventi dettagliati relativi a tutte le richieste elaborate nel proxy inverso. 

1. Raccogliere gli eventi di errore: per visualizzare questi eventi nel visualizzatore eventi di diagnostica di Visual Studio, aggiungere "Microsoft-ServiceFabric:4:0x4000000000000010" all'elenco di provider ETW.
Per raccogliere gli eventi dai cluster di Azure, modificare il modello di Resource Manager in modo da includere

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387920",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

2. Raccogliere tutti gli eventi di elaborazione delle richieste: nel visualizzatore eventi di diagnostica di Visual Studio aggiornare la voce Microsoft-ServiceFabric nell'elenco di provider ETW modificandola in "Microsoft-ServiceFabric:4:0x4000000000000020".
Per i cluster di Azure Service Fabric, modificare il modello di Resource Manager in modo da includere

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387936",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```
> È consigliabile abilitare la raccolta degli eventi in modo sensato da questo canale, in quanto raccoglie tutto il traffico attraverso il proxy inverso e può consumare rapidamente la capacità di archiviazione.

Per i cluster di Azure Service Fabric, vengono raccolti gli eventi da tutti i nodi, che vengono aggregati in SystemEventTable.
Per informazioni dettagliate sulla risoluzione dei problemi relativi agli eventi del proxy inverso, vedere la [guida alla diagnostica dei problemi relativi al proxy inverso](service-fabric-reverse-proxy-diagnostics.md).

## <a name="collect-from-new-eventsource-channels"></a>Eseguire la raccolta dai nuovi canali EventSource

Per aggiornare Diagnostica in modo da raccogliere log da nuovi canali EventSource che rappresentano una nuova applicazione da distribuire, eseguire gli stessi passaggi descritti in precedenza per la configurazione di Diagnostica per un cluster esistente.

Aggiornare la sezione `EtwEventSourceProviderConfiguration` nel file template.json per aggiungere voci per i nuovi canali EventSource prima di applicare l'aggiornamento della configurazione tramite il comando `New-AzureRmResourceGroupDeployment` di PowerShell. Il nome dell'origine evento è definito come parte del codice del file ServiceEventSource.cs generato da Visual Studio.

Ad esempio, se l'origine evento è denominato My Eventsource, aggiungere il codice seguente per inserire gli eventi da My Eventsource in una tabella denominata MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Per raccogliere i contatori delle prestazioni o i log eventi, modificare il modello di Resource Manager tramite gli esempi forniti in [Creare una macchina virtuale Windows con monitoraggio e diagnostica mediante i modelli di Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Pubblicare di nuovo il modello di Resource Manager.

## <a name="collect-performance-counters"></a>Raccogliere i contatori delle prestazioni

Per raccogliere metriche delle prestazioni dal cluster, aggiungere i contatori delle prestazioni a "WadCfg > DiagnosticMonitorConfiguration" nel modello di Resource Manager per il cluster. Per informazioni sui contatori delle prestazioni che è consigliabile raccogliere, vedere l'articolo relativo ai [contatori delle prestazioni in Service Fabric](service-fabric-diagnostics-event-generation-perf.md).

Di seguito, ad esempio, si imposta un contatore delle prestazioni con campionamento ogni 15 secondi e trasferimento nella tabella di archiviazione appropriata ogni minuto. La frequenza di campionamento può essere modificata seguendo il formato "PT\<tempo>\<unità>"; PT3M, ad esempio, determinerà il campionamento a intervalli di tre minuti.

  ```json
  "PerformanceCounters": {
      "scheduledTransferPeriod": "PT1M",
      "PerformanceCounterConfiguration": [
          {
              "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
              "sampleRate": "PT15S",
              "unit": "Percent",
              "annotation": [
              ],
              "sinks": ""
          }
      ]
  }
  ```
  
Se si usa un sink di Application Insights, come descritto nella sezione di seguito, e si vuole che queste metriche vengano visualizzate in Application Insights, aggiungere il nome del sink nella sezione "sinks" illustrata sopra. Valutare inoltre la possibilità di creare una tabella separata in cui inviare i contatori delle prestazioni, per non esaurire lo spazio a disposizione dei dati provenienti dagli altri canali di registrazione che sono stati abilitati.


## <a name="send-logs-to-application-insights"></a>Inviare i log ad Application Insights

L'invio dei dati di monitoraggio e diagnostica ad Application Insights può essere eseguito nell'ambito della configurazione di Diagnostica di Microsoft Azure. Se si decide di usare Application Insights per l'analisi e la visualizzazione degli eventi, vedere [Event Analysis and Visualization with Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) (Analisi e visualizzazione di eventi con Application Insights) per configurare un sink di Application Insights in "WadCfg".

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato correttamente Diagnostica di Azure, sarà possibile visualizzare i dati nelle tabelle di archiviazione dai log EventSource ed ETW. Se si sceglie di usare OMS, Kibana o qualsiasi altra piattaforma di analisi e visualizzazione dati non configurata direttamente nel modello di Resource Manager, assicurarsi di configurare la piattaforma scelta per la lettura dei dati da queste tabelle di archiviazione. Questa operazione in OMS è relativamente semplice ed è illustrata nell'articolo relativo all'[analisi di eventi e log tramite OMS](service-fabric-diagnostics-event-analysis-oms.md). Application Insights è un caso particolare sotto questo aspetto, perché può essere configurato nell'ambito della configurazione dell'estensione Diagnostica. Se si sceglie di usare Application Insights, vedere l'[articolo appropriato](service-fabric-diagnostics-event-analysis-appinsights.md).

>[!NOTE]
>Attualmente non è possibile filtrare o eliminare gli eventi inviati alla tabella. Se non si implementa un processo per rimuovere gli eventi dalla tabella, le dimensioni della tabella continueranno ad aumentare. È attualmente disponibile un esempio di servizio di eliminazione dati in esecuzione nel [watchdog di esempio](https://github.com/Azure-Samples/service-fabric-watchdog-service). È consigliabile scriverne uno personalizzato, a meno che non esista un motivo valido per archiviare i log per un intervallo di tempo superiore a 30 o 90 giorni.

* [Informazioni su come raccogliere i contatori delle prestazioni o i log mediante l'estensione Diagnostica](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Event Analysis and Visualization with Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) (Analisi e visualizzazione di eventi con Application Insights)
* [Event Analysis and Visualization with OMS](service-fabric-diagnostics-event-analysis-oms.md) (Analisi e visualizzazione di eventi con OMS)
---
title: Aggregazione di eventi di Azure Service Fabric con Diagnostica di Microsoft Azure | Microsoft Docs
description: Informazioni sull'aggregazione e la raccolta di eventi con Diagnostica di Microsoft Azure per il monitoraggio e la diagnostica dei cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/03/2018
ms.author: srrengar
ms.openlocfilehash: baa86fe70c394aaea31a6fa775073bb26d062c49
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2019
ms.locfileid: "54002400"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Aggregazione e raccolta di eventi con Diagnostica di Microsoft Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Quando si esegue un cluster Azure Service Fabric, è consigliabile raccogliere i log da tutti i nodi in una posizione centrale. Il salvataggio dei log in una posizione centrale semplifica l'analisi e la risoluzione di eventuali problemi nel cluster o nelle applicazioni e nei servizi in esecuzione nel cluster.

Un modo per caricare e raccogliere i log consiste nell'usare l'estensione Diagnostica di Microsoft Azure, che carica i log in Archiviazione di Azure e offre anche la possibilità di inviarli ad Azure Application Insights o Hub eventi. È anche possibile usare un processo esterno per leggere gli eventi dalla risorsa di archiviazione e inserirli in una piattaforma di analisi come [Log Analytics](../log-analytics/log-analytics-service-fabric.md) o in un'altra soluzione di analisi di log.

## <a name="prerequisites"></a>Prerequisiti
In questo articolo vengono usati gli strumenti seguenti:

* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Modello di Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-platform-events"></a>Eventi della piattaforma Service Fabric
Service Fabric offre alcuni [canali di registrazione predefiniti](service-fabric-diagnostics-event-generation-infra.md). Tra questi, i canali seguenti vengono preconfigurati con l'estensione per inviare i dati di monitoraggio e diagnostica a una tabella di archiviazione o un'altra posizione:
  * [Eventi operativi](service-fabric-diagnostics-event-generation-operational.md): operazioni generali eseguite dalla piattaforma Service Fabric. Gli esempi includono la creazione di applicazioni e servizi, le modifiche allo stato dei nodi e informazioni sull'aggiornamento. Questi eventi vengono generati come log di Event Tracing for Windows (ETW)
  * [Eventi del modello di programmazione Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
  * [Eventi relativi al modello di programmazione Reliable Services](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>Distribuire l'estensione Diagnostica tramite il portale
Il primo passaggio per la raccolta dei log consiste nel distribuire l'estensione Diagnostica nei nodi del set di scalabilità di macchine virtuali nel cluster Service Fabric. Questa estensione raccoglie i log in ogni VM e li carica nell'account di archiviazione specificato. La procedura seguente illustra come eseguire questa operazione per i cluster nuovi ed esistenti tramite il portale di Azure e i modelli di Azure Resource Manager.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Distribuire l'estensione Diagnostica nell'ambito della creazione del cluster tramite il portale di Azure
Quando si crea il cluster, nel passaggio di configurazione del cluster espandere le impostazioni facoltative e verificare che Diagnostica sia impostata su **Attivato** (impostazione predefinita).

![Impostazioni di diagnostica di Azure nel portale per la creazione del cluster](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

Si consiglia di scaricare il modello **prima di fare clic su Crea** nel passaggio finale. Per informazioni dettagliate, vedere [Configurare un cluster di Service Fabric usando un modello di Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Il modello è necessario per apportare modifiche ai canali (elencati in precedenza) da cui raccogliere i dati.

![Modello di cluster](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Dopo aver impostato l'aggregazione di eventi in Archiviazione di Azure, [configurare Log Analytics](service-fabric-diagnostics-oms-setup.md) per ottenere informazioni dettagliate ed eseguire query su tali informazioni nel portale di Log Analytics.

>[!NOTE]
>Attualmente non è possibile filtrare o eliminare gli eventi inviati alle tabelle. Se non si implementa un processo per rimuovere gli eventi dalla tabella, le dimensioni della tabella continueranno ad aumentare (il limite massimo è di 50 GB). Le istruzioni su come modificare questa impostazione sono disponibili [più avanti in questo articolo](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Inoltre, è disponibile un esempio di servizio di eliminazione dati in esecuzione nel [watchdog di esempio](https://github.com/Azure-Samples/service-fabric-watchdog-service). È consigliabile scriverne uno personalizzato, a meno che non esista un motivo valido per archiviare i log per un intervallo di tempo superiore a 30 o 90 giorni.

## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>Distribuire l'estensione Diagnostica tramite Azure Resource Manager

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Creare un cluster con l'estensione Diagnostica
Per creare un cluster tramite Resource Manager, è necessario aggiungere il file JSON di configurazione di Diagnostica al modello completo di Resource Manager. Gli esempi relativi ai modelli di Gestione risorse includono un modello di cluster con 5 VM con aggiunta della configurazione di Diagnostica, disponibile in questa posizione nella raccolta di esempi di Azure relativa all'[esempio di modello di Resource Manager con cluster a cinque nodi con Diagnostica](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Per visualizzare l'impostazione di Diagnostica nel modello di Resource Manager, aprire il file azuredeploy.json e cercare **IaaSDiagnostics**. Per creare un cluster con questo modello, è sufficiente selezionare il pulsante di **distribuzione in Azure** disponibile nel collegamento precedente.

In alternativa, è possibile scaricare l'esempio di Resource Manager, modificarlo e creare un cluster con il modello modificato mediante il comando `New-AzureRmResourceGroupDeployment` in una finestra di Azure PowerShell. Per i parametri passati al comando, vedere il codice seguente. Per informazioni dettagliate sulla distribuzione di un gruppo di risorse con PowerShell, vedere l'articolo su come [distribuire un gruppo di risorse con un modello di Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>Aggiungere l'estensione Diagnostica in un cluster esistente
Se in un cluster esistente non è stata distribuita l'estensione Diagnostica, è possibile aggiungerla o aggiornarla tramite il modello del cluster. Modificare il modello di Resource Manager usato per creare il cluster esistente o scaricare il modello dal portale come descritto in precedenza. Modificare il file template.json eseguendo le attività seguenti:

Aggiungere una nuova risorsa di archiviazione al modello nella sezione risorse.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Aggiungere quindi alla sezione parametri subito dopo le definizioni dell'account di archiviazione, tra `supportLogStorageAccountName`. Sostituire il testo segnaposto *storage account name goes here* con il nome dell'account di archiviazione preferito.

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
      "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
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

> [!TIP]
> Se si distribuiranno contenitori nel cluster, abilitare l'acquisizione delle statistiche Docker in Diagnostica di Microsoft Azure aggiungendo quanto segue alla sezione **WadCfg > DiagnosticMonitorConfiguration**.
>
>```json
>"DockerSources": {
>    "Stats": {
>        "enabled": true,
>        "sampleRate": "PT1M"
>    }
>},
>```

### <a name="update-storage-quota"></a>Aggiornare la quota di archiviazione

Dato che le tabelle popolate dall'estensione aumentano fino al raggiungimento della quota, è possibile valutare la possibilità di ridurre le dimensioni della quota. Il valore predefinito è 50 GB e può essere configurato nel modello nel campo `overallQuotainMB` in `DiagnosticMonitorConfiguration`.

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Configurazioni di raccolta log
Sono disponibili per la raccolta anche log da canali aggiuntivi; di seguito sono riportare alcune delle configurazioni più comuni che è possibile apportare nel modello per i cluster in esecuzione in Azure.

* Canale operativo - Base: Abilitato per impostazione predefinita. Operazioni di alto livello eseguite da Service Fabric e dal cluster, inclusi gli eventi per l'attivazione di un nodo, la distribuzione di una nuova applicazione, il ripristino dello stato precedente a un aggiornamento e così via. Per un elenco di eventi, consultare gli [Eventi del canale operativo](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-operational).
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Canale operativo - Dettagliato: include i report sull'integrità e le decisioni sul bilanciamento del carico, oltre a tutti gli elementi del canale operativo di base. Questi eventi sono generati dal sistema o dal codice usando le API di creazione di report di integrità o caricamento, ad esempio [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) o [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Per visualizzare tali eventi nel visualizzatore eventi di diagnostica di Visual Studio, aggiungere "Microsoft-ServiceFabric:4:0x4000000000000008" all'elenco di provider ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Canale dati e messaggistica - Base: log ed eventi critici generati nella messaggistica (attualmente solo ReverseProxy) e nel percorso dei dati, oltre ai log dettagliati del canale operativo. Questi eventi riguardano errori di elaborazione delle richieste e altri problemi critici nel ReverseProxy, oltre alle richieste elaborate. **Questa è l'indicazione per la registrazione completa**. Per visualizzare tali eventi nel visualizzatore eventi di diagnostica di Visual Studio, aggiungere "Microsoft-ServiceFabric:4:0x4000000000000010" all'elenco di provider ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Canale dati e messaggistica - Dettagliato: canale dettagliato che contiene tutti i log non critici di dati e messaggistica del cluster, oltre ai log dettagliati del canale operativo. Per informazioni dettagliate sulla risoluzione dei problemi relativi a tutti gli eventi del proxy inverso, vedere la [guida alla diagnostica dei problemi relativi al proxy inverso](service-fabric-reverse-proxy-diagnostics.md).  Per visualizzare tali eventi nel visualizzatore eventi di diagnostica di Visual Studio, aggiungere "Microsoft-ServiceFabric:4:0x4000000000000020" all'elenco di provider ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Questo canale ha un volume molto elevato di eventi; permettendo la raccolta di eventi da questo canale dettagliato vengono generate numerose tracce e pertanto ciò può comportare un maggiore uso della capacità di archiviazione. Attivare questa funzionalità solo quando è strettamente necessario.


Per abilitare il **canale operativo di base** è consigliabile la registrazione completa con la minor quantità di rumore possibile. `EtwManifestProviderConfiguration` in `WadCfg` del modello avrà un aspetto simile al seguente:

```json
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
```

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

Per raccogliere metriche delle prestazioni dal cluster, aggiungere i contatori delle prestazioni a "WadCfg > DiagnosticMonitorConfiguration" nel modello di Resource Manager per il cluster. Vedere [Monitoraggio delle prestazioni con Diagnostica di Microsoft Azure](service-fabric-diagnostics-perf-wad.md) per le istruzioni su come modificare `WadCfg` per raccogliere contatori delle prestazioni specifici. Per l'elenco dei contatori delle prestazioni che è consigliabile raccogliere, vedere l'articolo relativo ai [contatori delle prestazioni in Service Fabric](service-fabric-diagnostics-event-generation-perf.md).
  
Se si usa un sink di Application Insights, come descritto nella sezione di seguito, e si vuole che queste metriche vengano visualizzate in Application Insights, aggiungere il nome del sink nella sezione "sinks" illustrata sopra. I contatori delle prestazioni configurati singolarmente verranno inviati automaticamente alla risorsa di Application Insights.


## <a name="send-logs-to-application-insights"></a>Inviare i log ad Application Insights

L'invio dei dati di monitoraggio e diagnostica ad Application Insights può essere eseguito nell'ambito della configurazione di Diagnostica di Microsoft Azure. Se si decide di usare Application Insights per l'analisi e la visualizzazione degli eventi, vedere la [procedura per configurare un sink di Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-application-insights-sink-to-the-resource-manager-template) nel file di configurazione "WadCfg".

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato correttamente Diagnostica di Azure, sarà possibile visualizzare i dati nelle tabelle di archiviazione dai log EventSource ed ETW. Se si sceglie di usare Log Analytics, Kibana o qualsiasi altra piattaforma di analisi e visualizzazione dati non configurata direttamente nel modello di Resource Manager, assicurarsi di configurare la piattaforma scelta per la lettura dei dati da queste tabelle di archiviazione. Questa operazione, relativamente semplice per Log Analytics, è illustrata in [Analisi e visualizzazione degli eventi](service-fabric-diagnostics-event-analysis-oms.md). Application Insights è un caso particolare sotto questo aspetto, perché può essere configurato nell'ambito della configurazione dell'estensione Diagnostica. Se si sceglie di usare Application Insights, vedere l'[articolo appropriato](service-fabric-diagnostics-event-analysis-appinsights.md).

>[!NOTE]
>Attualmente non è possibile filtrare o eliminare gli eventi inviati alla tabella. Se non si implementa un processo per rimuovere gli eventi dalla tabella, le dimensioni della tabella continueranno ad aumentare. È attualmente disponibile un esempio di servizio di eliminazione dati in esecuzione nel [watchdog di esempio](https://github.com/Azure-Samples/service-fabric-watchdog-service). È consigliabile scriverne uno personalizzato, a meno che non esista un motivo valido per archiviare i log per un intervallo di tempo superiore a 30 o 90 giorni.

* [Informazioni su come raccogliere i contatori delle prestazioni o i log mediante l'estensione Diagnostica](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Event Analysis and Visualization with Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) (Analisi e visualizzazione degli eventi con Application Insights)
* [Analisi e visualizzazione degli eventi con OMS](service-fabric-diagnostics-event-analysis-oms.md)

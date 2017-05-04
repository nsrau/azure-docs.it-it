---
title: Raccogliere log con Diagnostica di Azure | Microsoft Docs
description: Questo articolo illustra come configurare Diagnostica di Azure per raccogliere log da un cluster di Service Fabric in esecuzione in Azure.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 9f7e1fa5-6543-4efd-b53f-39510f18df56
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/17/2017
ms.author: dekapur
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 7c3311800af27016b7f993b375055bbc65d9a727
ms.lasthandoff: 04/27/2017


---
# <a name="collect-logs-by-using-azure-diagnostics"></a>Raccogliere log con Diagnostica di Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
> * [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
> 
> 

Quando si esegue un cluster Azure Service Fabric, è consigliabile raccogliere i log da tutti i nodi in una posizione centrale. Il salvataggio dei log in una posizione centrale semplifica l'analisi e la risoluzione di eventuali problemi nel cluster o nelle applicazioni e nei servizi in esecuzione nel cluster.

Un modo per caricare e raccogliere i log consiste nell'usare l'estensione Diagnostica di Azure, che consente di caricare i log di archiviazione di Azure, Azure Application Insights o Hub eventi di Azure. I log non sono utili direttamente nell'archiviazione o in Hub eventi, ma è possibile usare un processo esterno per leggere gli eventi dalla risorsa di archiviazione e inserirli in un prodotto come [Log Analytics](../log-analytics/log-analytics-service-fabric.md) o un'altra soluzione di analisi di log. In [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) è integrato un servizio completo di analisi e di ricerca dei log.

## <a name="prerequisites"></a>Prerequisiti
Questi strumenti vengono usati per eseguire alcune operazioni nel documento:

* [Diagnostica di Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (correlata ai Servizi cloud di Azure, include alcune informazioni ed esempi utili)
* [Gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Client di Azure Resource Manager](https://github.com/projectkudu/ARMClient)
* [Modello di Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="log-sources-that-you-might-want-to-collect"></a>Origini di log da raccogliere
* **Log di Service Fabric:** generati dalla piattaforma in canali Event Tracing for Windows (ETW) ed EventSource standard. I log possono essere di diversi tipi:
  * Eventi operativi: log relativi a operazioni eseguite dalla piattaforma Service Fabric. Gli esempi includono la creazione di applicazioni e servizi, le modifiche allo stato dei nodi e informazioni sull'aggiornamento.
  * [Eventi del modello di programmazione Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
  * [Eventi relativi al modello di programmazione Reliable Services](service-fabric-reliable-services-diagnostics.md)
* **Eventi dell'applicazione:** eventi generati dal codice del servizio e scritti mediante la classe helper EventSource disponibile nei modelli di Visual Studio. Per altre informazioni su come scrivere i log dall'applicazione, vedere [Monitorare e diagnosticare servizi in una configurazione di sviluppo con computer locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

## <a name="deploy-the-diagnostics-extension"></a>Distribuire l'estensione Diagnostica
Il primo passaggio per la raccolta dei log consiste nel distribuire l'estensione Diagnostica in ogni VM del cluster Service Fabric. Questa estensione raccoglie i log in ogni VM e li carica nell'account di archiviazione specificato. La procedura varia a seconda che si usi il portale di Azure oppure Azure Resource Manager. Anche i passaggi variano se la distribuzione fa parte della creazione del cluster o è relativa a un cluster già esistente. Ecco la procedura per ogni scenario.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-the-portal"></a>Distribuire l’estensione Diagnostica come parte della creazione di cluster tramite il portale
Per distribuire l'estensione Diagnostica nelle VM del cluster come parte della creazione del cluster, si usa il pannello Impostazioni di diagnostica illustrato nella figura seguente. Per abilitare la raccolta di eventi Reliable Actors o Reliable Services, verificare che l'opzione Diagnostica sia impostata su **Sì**, che corrisponde all'impostazione predefinita. Dopo aver creato il cluster, non è possibile modificare questa impostazione tramite il portale.

![Impostazioni di diagnostica di Azure nel portale per la creazione del cluster](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

Il team di Supporto di Azure *necessita* dei log di supporto per gestire eventuali richieste di supporto create dall'utente. I log vengono raccolti in tempo reale e archiviati in uno degli account di archiviazione creati nel gruppo di risorse. Le impostazioni di diagnostica configurano gli eventi a livello di applicazione. Sono inclusi gli eventi [Reliable Actors](service-fabric-reliable-actors-diagnostics.md), [Reliable Services](service-fabric-reliable-services-diagnostics.md) e alcuni eventi di Service Fabric a livello di sistema da archiviare in Archiviazione di Azure.

Gli eventi possono essere ottenuti dall'account di archiviazione da prodotti come [ElasticSearch](https://www.elastic.co/guide/index.html) o da un processo personalizzato. Attualmente non è possibile filtrare o eliminare gli eventi inviati alla tabella. Se non si implementa un processo per rimuovere gli eventi dalla tabella, le dimensioni della tabella continueranno ad aumentare.

Quando si crea un cluster con il portale, è consigliabile scaricare il modello **prima di fare clic su OK** per creare il cluster. Per informazioni dettagliate, vedere [Configurare un cluster di Service Fabric usando un modello di Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Il modello è necessario per apportare modifiche in un secondo momento perché non è possibile apportare alcune modifiche tramite il portale.

È possibile esportare i modelli dal portale attenendosi alla procedura seguente. Tuttavia, questi modelli possono risultare più difficili da usare poiché potrebbero contenere valori Null in cui mancano informazioni necessarie.

1. Aprire il gruppo di risorse.
2. Selezionare **Impostazioni** per visualizzare il pannello Impostazioni.
3. Selezionare **Distribuzioni** per visualizzare il pannello Cronologia distribuzioni.
4. Selezionare una distribuzione per visualizzare i relativi dettagli.
5. Selezionare **Esporta modello** per visualizzare il pannello Modello.
6. Selezionare **Salva su file** per esportare un file con estensione zip contenente i file del modello, dei parametri e di PowerShell.

Dopo aver esportato i file, è necessario apportare una modifica. Modificare il file parameters.json e rimuovere l'elemento **adminPassword**. In questo modo viene richiesta la password quando viene eseguito lo script di distribuzione. Quando si esegue lo script di distribuzione, è necessario correggere i valori dei parametri Null.

Per usare il modello scaricato per aggiornare una configurazione:

1. Estrarre il contenuto in una cartella nel computer locale.
2. Modificare il contenuto in modo da riflettere la nuova configurazione.
3. Avviare PowerShell e passare alla cartella in cui è stato estratto il contenuto.
4. Eseguire **deploy.ps1** e immettere ID sottoscrizione, nome del gruppo di risorse (usare lo stesso nome per aggiornare la configurazione) e un nome di distribuzione univoco.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Distribuire l’estensione Diagnostica come parte della creazione di cluster tramite Gestione risorse di Azure
Per creare un cluster tramite Resource Manager, è necessario aggiungere il file JSON di configurazione di Diagnostica al modello di Resource Manager di tipo cluster completo prima di creare il cluster. Gli esempi relativi ai modelli di Gestione risorse includono un modello di cluster con 5 VM con aggiunta della configurazione di Diagnostica, disponibile nella raccolta di esempi di Azure nella pagina relativa all'[esempio di modello di Resource Manager di cluster con cinque nodi con Diagnostica](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad).

Per visualizzare l'impostazione di Diagnostica nel modello di Resource Manager, aprire il file azuredeploy.json e cercare **IaaSDiagnostics**. Per creare un cluster con questo modello, è sufficiente selezionare il pulsante di **distribuzione in Azure** disponibile nel collegamento precedente.

In alternativa, è possibile scaricare l'esempio di Resource Manager, modificarlo e creare un cluster con il modello modificato mediante il comando `New-AzureRmResourceGroupDeployment` in una finestra di Azure PowerShell. Per i parametri passati al comando, vedere il codice seguente. Per informazioni dettagliate sulla distribuzione di un gruppo di risorse con PowerShell, vedere l'articolo su come [distribuire un gruppo di risorse con un modello di Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

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

## <a name="update-diagnostics-to-collect-health-and-load-events"></a>Aggiornare i dati di diagnostica per raccogliere eventi di integrità e di caricamento

A partire dalla versione 5.4 di Service Fabric è possibile raccogliere gli eventi di metrica di integrità e caricamento. Questi riflettono gli eventi generati dal sistema o dal codice usando le API di creazione di report di integrità o caricamento, ad esempio [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) o [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Ciò consente l'aggregazione e la visualizzazione dell'integrità del sistema nel tempo, nonché la generazione di avvisi in base a eventi di integrità o di caricamento. Per visualizzare tali eventi nel visualizzatore eventi di diagnostica di Visual Studio, aggiungere "Microsoft-ServiceFabric:4:0x4000000000000008" all'elenco di provider ETW.

Per raccogliere gli eventi, modificare il modello di Resource Manager includendo quanto segue:

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

## <a name="update-diagnostics-to-collect-and-upload-logs-from-new-eventsource-channels"></a>Aggiornare Diagnostica per raccogliere e caricare log da nuovi canali EventSource
Per aggiornare Diagnostica in modo da raccogliere log da nuovi canali EventSource che rappresentano una nuova applicazione da distribuire, eseguire gli stessi passaggi illustrati nella [sezione precedente](#deploywadarm) per la configurazione di Diagnostica per un cluster esistente.

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

## <a name="next-steps"></a>Passaggi successivi
Per informazioni più dettagliate sugli eventi da esaminare durante la risoluzione dei problemi, vedere gli eventi di diagnostica emessi per [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) e [Reliable Services](service-fabric-reliable-services-diagnostics.md).

## <a name="related-articles"></a>Articoli correlati
* [Informazioni su come raccogliere i contatori delle prestazioni o i log mediante l'estensione Diagnostica](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Soluzione Service Fabric in Log Analytics](../log-analytics/log-analytics-service-fabric.md)



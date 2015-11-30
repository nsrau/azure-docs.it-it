<properties
   pageTitle="Come raccogliere log con Diagnostica di Microsoft Azure e Operational Insights | Microsoft Azure"
   description="Questo articolo illustra come configurare Diagnostica di Microsoft Azure e Operational Insights per raccogliere log da un cluster di infrastruttura di servizi in esecuzione in Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/20/2015"
   ms.author="kunalds"/>


# Raccolta di log da un cluster di infrastruttura di servizi in Azure mediante Diagnostica di Microsoft Azure e Operational Insights

Quando si esegue un cluster di infrastruttura di servizi in Azure, è consigliabile raccogliere i log da tutti i nodi in una posizione centrale. Il salvataggio dei log in una posizione centrale semplifica l'analisi e la risoluzione di eventuali problemi rilevati nel cluster o nelle applicazioni e nei servizi in esecuzione nel cluster. Uno dei modi per caricare e raccogliere i log consiste nell'usare l'estensione Diagnostica di Microsoft Azure, che carica i log nell'archiviazione tabelle di Azure. Operational Insights, incluso in Microsoft Operations Management Suite, è una soluzione SaaS che semplifica l'analisi e la ricerca nei log. La procedura seguente illustra come configurare Diagnostica di Microsoft Azure nelle VM nel cluster in modo da caricare i log in un archivio locale e quindi come configurare Operational Insights per eseguire il pull dei log, in modo da visualizzarli nel portale di Operational Insights. Operational Insights identifica le origini dei diversi tipi di log caricati da un cluster di infrastruttura di servizi in base ai nomi delle tabelle di archiviazione di Azure in cui sono archiviati. Sarà quindi necessario configurare Diagnostica di Microsoft Azure in modo che carichi i log nelle tabelle di archiviazione di Azure con nomi corrispondenti ai valori che verranno cercati da Operational Insights. Gli esempi di impostazioni di configurazione disponibili in questo documento indicano i nomi ottimali per le tabelle di archiviazione.

## Articoli utili
* [Diagnostica di Microsoft Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (correlato ai Servizi cloud, include alcune informazioni ed esempi utili)
* [Operational Insights](https://azure.microsoft.com/services/operational-insights/)
* [Gestione risorse di Azure](https://azure.microsoft.com/documentation/articles/resource-group-overview/)

## Prerequisiti
Questi strumenti verranno usati per eseguire alcune operazioni nel documento: * [Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) * [Client ARM](https://github.com/projectkudu/ARMClient)

## Diverse origini di log da raccogliere
1. Log di infrastruttura di servizi: emessi dalla piattaforma in canali ETW ed EventSource standard. Possono avere uno dei tipi seguenti:
  - Eventi operativi: si tratta di log relativi a operazioni eseguite dalla piattaforma di infrastruttura di servizi. Gli esempi includono la creazione di applicazioni e servizi, le modifiche allo stato dei nodi e informazioni sull'aggiornamento.
  - [Eventi relativi al modello di programmazione attore](https://azure.microsoft.com/documentation/articles/service-fabric-reliable-actors-diagnostics/)
  - [Eventi relativi al modello di programmazione Reliable Services](https://azure.microsoft.com/documentation/articles/service-fabric-reliable-services-diagnostics/)
2. Eventi dell'applicazione: si tratta di eventi emessi dal codice del servizio e scritti mediante la classe helper EventSource disponibile nei modelli di Visual Studio. Per altre informazioni su come scrivere i log dall'applicazione, vedere questo [articolo](https://azure.microsoft.com/documentation/articles/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/).


## Distribuire Diagnostica di Microsoft Azure in un cluster di infrastruttura di servizi per raccogliere e caricare i log
Il primo passaggio per la raccolta dei log consiste nel distribuire l'estensione Diagnostica di Microsoft Azure in ogni VM del cluster di infrastruttura di servizi. Diagnostica di Microsoft Azure raccoglierà i log in ogni VM e li caricherà nell'account di archiviazione specificato. La procedura dipende dall'uso del portale o di ARM e dall'esecuzione della distribuzione come parte della creazione di un cluster o per un cluster già esistente. Ecco la procedura per ogni scenario.

### Distribuire Diagnostica di Microsoft Azure come parte della creazione di cluster tramite il portale
Per distribuire Diagnostica di Microsoft Azure nelle VM nel cluster come parte della creazione di cluster, verrà usata l'impostazione di diagnostica illustrata nella figura seguente, attiva per impostazione predefinita. ![Impostazione di Diagnostica di Microsoft Azure nel portale per la creazione di cluster](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/portal-cluster-creation-diagnostics-setting.png)

### Distribuire Diagnostica di Microsoft Azure come parte della creazione di cluster tramite ARM
Per creare un cluster tramite ARM, è necessario aggiungere il file JSON di Diagnostica di Microsoft Azure al modello ARM di tipo cluster completo prima di creare il cluster. Gli esempi relativi ai modelli ARM includono un modello ARM di cluster con 5 VM con aggiunta della configurazione di Diagnostica di Microsoft Azure, disponibile nella pagina relativa all'[esempio di modello ARM di cluster con cinque nodi con Diagnostica di Microsoft Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype-wad) nella raccolta di esempi di Azure.

Per visualizzare l'impostazione di Diagnostica di Microsoft Azure nel modello ARM, cercare "WadCfg". Per creare un cluster con questo modello, è sufficiente premere il pulsante di distribuzione in Azure disponibile nel collegamento precedente. In alternativa, è possibile scaricare l'esempio ARM, modificarlo e creare un cluster con il modello modificato mediante il comando `New-AzureResourceGroupDeployment` in una finestra di Azure Powershell. Per informazioni sui parametri da passare al comando, vedere più avanti. Prima di chiamare questo comando di distribuzione, inoltre, potrebbe essere necessario eseguire operazioni di configurazione, ad esempio aggiungere l'account di Azure (`Add-AzureAccount`), scegliere una sottoscrizione (`Select-AzureSubscription`), passare alla modalità ARM (`Switch-AzureMode AzureResourceManager`) e creare il gruppo di risorse, se non è già disponibile (`New-AzureResourceGroup`).

```powershell
New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploywadarm"></a>Distribuire Diagnostica di Microsoft Azure in un cluster esistente
Se in un cluster non è stato distribuito Diagnostica di Microsoft Azure, sarà possibile aggiungere Diagnostica di Microsoft Azure seguendo questa procedura. Creare i due file WadConfigUpdate.json e WadConfigUpdateParams.json con il codice JSON seguente.

##### WadConfigUpdate.json
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",

  "parameters": {
        "vmNamePrefix": {
      "type": "string"
    },
        "applicationDiagnosticsStorageAccountName": {
      "type": "string"
    },
        "vmCount": {
            "type": "int"
    }
  },

  "resources": [
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(parameters('vmNamePrefix'),copyIndex(0),'/Microsoft.Insights.VMDiagnosticsSettings')]",
            "location": "[resourceGroup().location]",
      "properties": {
        "type": "IaaSDiagnostics",
                "typeHandlerVersion": "1.5",
        "publisher": "Microsoft.Azure.Diagnostics",
                "autoUpgradeMinorVersion": true,
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
                            "DefaultEvents": { "eventDestination": "ServiceFabricReliableActorEventTable" }
                        },
                        {
                            "provider": "Microsoft-ServiceFabric-Services",
                            "DefaultEvents": { "eventDestination": "ServiceFabricReliableServiceEventTable" },
                                        "scheduledTransferPeriod": "PT5M"
                        }
                    ],
                    "EtwManifestProviderConfiguration": [
                        {
                            "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                            "scheduledTransferKeywordFilter": "4611686018427387904",
                                        "scheduledTransferLogLevelFilter": "Information",
                                        "scheduledTransferPeriod": "PT5M",
                            "DefaultEvents": { "eventDestination": "ServiceFabricSystemEventTable" }
                        }
                    ]
                }
            }
    },
                    "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountNamee')]"
                },
                "protectedSettings": {
                    "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountNamee')]",
                    "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                    "storageAccountEndPoint": "https://core.windows.net/"
                }
            },
            "copy": {
                "name": "vmExtensionLoop",
                "count": "[parameters('vmCount')]"
            }
        }
    ],

    "outputs": {
    }
}
```

##### WadConfigUpdateParams.json
Sostituire il valore vmNamePrefix con il prefisso scelto per i nomi di VM durante la creazione del cluster e modificare il valore vmStorageAccountName in modo che corrisponda all'account di archiviazione in cui si vogliono caricare i log dalle VM. ```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmNamePrefix": {
            "value": "VM"
        },
        "applicationDiagnosticsStorageAccountName": {
            "value": "testdiagacc"
        },
        "vmCount": {
            "value": 5
        }
    }
}
``` Dopo la creazione dei file con estensione json, come illustrato in precedenza, e dopo la modifica dei file in base alle specifiche dell'ambiente, chiamare questo comando passando il nome del gruppo di risorse per il cluster di infrastruttura di servizi. Dopo l'esecuzione corretta del comando, Diagnostica di Microsoft Azure verrà distribuito in tutte le VM e inizierà a caricare i log dal cluster alle tabelle disponibili nell'account di archiviazione di Azure specificato. Prima di chiamare questo comando di distribuzione, inoltre, potrebbe essere necessario eseguire operazioni di configurazione, ad esempio aggiungere l'account di Azure (`Add-AzureAccount`), scegliere la sottoscrizione corretta (`Select-AzureSubscription`) e passare alla modalità ARM (`Switch-AzureMode AzureResourceManager`). ```powershell
New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToWADConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

## Configurare Operational Insights per visualizzare i log ed eseguire ricerche nei log del cluster
Quando Diagnostica di Microsoft Azure è configurato nel cluster e carica log in un account di archiviazione, occorre configurare Operational Insights in modo da potere visualizzare, eseguire ricerche e query in tutti i log di cluster nell'interfaccia utente del portale di Operational Insights.

### Creare un'area di lavoro di Operational Insights
Per visualizzare la procedura necessaria per creare un'area di lavoro di Operational Insights, vedere l'articolo seguente. Si noti che illustra due modi diversi per creare un'area di lavoro. Scegliere l'approccio basato sul portale di Azure e sulla sottoscrizione. Il nome dell'area di lavoro di Operational Insights sarà necessario nelle sezioni successive del documento. Creare l'area di lavoro di Operational Insights mediante la stessa sottoscrizione usata per creare tutte le risorse cluster, inclusi gli account di archiviazione.

[Onboarding di Operational Insights](https://technet.microsoft.com/library/mt484118.aspx)

### Configurare l'area di lavoro di Operational Insights in modo da visualizzare i log dei cluster
Dopo avere creato l'area di lavoro di Operational Insights, come illustrato in precedenza, sarà necessario configurare l'area di lavoro in modo che esegua il pull dei log dalle tabelle di Azure in cui vengono caricate dal cluster mediante Diagnostica di Microsoft Azure. Questa configurazione non è possibile tramite il portale di Operational Insights e può essere eseguita solo mediante comandi di Powershell. Eseguire questo script di Powershell. ```powershell <# Questo script configurerà un'area di lavoro di Operations Management Suite (definita anche area di lavoro di Operational Insights) in modo che legga i file di Diagnostica di Microsoft Azure da un account di archiviazione di Azure.

    It will enable all supported data types (currently Windows Event Logs, Syslog, Service Fabric Events, ETW Events and IIS Logs).

    It supports both classic and ARM storage accounts.

    If you have more than one OMS workspace you will be prompted for the workspace to configure.

    If you have more than one storage account you will be prompted for which storage account to configure.
#>
Add-AzureAccount

Switch-AzureMode -Name AzureResourceManager

$validTables = "WADServiceFabric*EventTable", "WADETWEventTable"

function Select-Workspace {

    $workspace = ""

    $allWorkspaces = Get-AzureOperationalInsightsWorkspace  

    switch ($allWorkspaces.Count) {
        0 {Write-Error "No Operations Management Suite workspaces found"}
        1 {return $allWorkspaces}
        default {
            $uiPrompt = "Enter the number corresponding to the workspace you want to configure.`n"

            $count = 1
            foreach ($workspace in $allWorkspaces) {
                $uiPrompt += "$count. " + $workspace.Name + " (" + $workspace.CustomerId + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt) - 1
            $workspace = $allWorkspaces[$answer]
        }  
    }
    return $workspace
}

function Select-StorageAccount {

    $storage = ""

    $allStorageAccounts = (get-azureresource -ResourceType Microsoft.ClassicStorage/storageAccounts) + (get-azureresource -ResourceType Microsoft.Storage/storageAccounts)

    switch ($allStorageAccounts.Count) {
        0 {Write-Error "No storage accounts found"}
        1 {$storage = $allStorageAccounts}
        default {

            $uiPrompt = "Enter the number corresponding to the storage account with diagnostics.`n"

            $count = 1
            foreach ($storageAccount in $allStorageAccounts) {
                $uiPrompt += "$count. " + $storageAccount.Name + " (" + $storageAccount.Location + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt)

            $storage = $allStorageAccounts[$answer - 1]
        }
    }
    return $storage
}

$workspace = Select-Workspace $storageAccount = Select-StorageAccount

$insightsName = $storageAccount.Name + $workspace.Name

$existingConfig = ""

try { $existingConfig = Get-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -ErrorAction Stop } catch [Hyak.Common.CloudException] { # HTTP Not Found is returned if the storage insight doesn't exist }

if ($existingConfig) { Set-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -Tables $validTables -Containers $validContainers

} else { if ($storageAccount.ResourceType -eq "Microsoft.ClassicStorage/storageAccounts") { Switch-AzureMode -Name AzureServiceManagement $key = (Get-AzureStorageKey -StorageAccountName $storageAccount.Name).Primary Switch-AzureMode -Name AzureResourceManager } else { $key = (Get-AzureStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.Name).Key1 } New-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -StorageAccountResourceId $storageAccount.ResourceId -StorageAccountKey $key -Tables $validTables -Containers $validContainers } ``` Dopo avere configurato l'area di lavoro di Operational Insights per la lettura dalle tabelle di Azure nell'account di archiviazione, è consigliabile accedere al portale di Azure e verificare la scheda Archiviazione per la risorsa di Operational Insights. Dovrebbe avere un aspetto analogo al seguente: ![Configurazione dell'archiviazione di Operational Insights nel portale di Azure](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/oi-connected-tables-list.png)

### Eseguire ricerche e visualizzare i log in Operational Insights
Dopo la configurazione dell'area di lavoro di Operational Insights per la lettura dei log dall'account di archiviazione specificato, la visualizzazione dei log nell'interfaccia utente di Operational Insights potrebbe richiedere fino a 10 minuti. Per assicurarsi che vengano generati nuovi log, è consigliabile distribuire un'applicazione di infrastruttura di servizi nel cluster, perché ciò genererà eventi operativi dalla piattaforma di infrastruttura di servizi.

1. Per visualizzare i log, selezionare LogSearch nella pagina principale del portale di Operational Insights. ![Opzione di ricerca log di Operational Insights](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/log-search-button-oi.png)

2. Nella pagina di ricerca dei log usare questa query "Type=ServiceFabricOperationalEvent". Dovrebbero essere visualizzati i log operativi del cluster, come illustrato di seguito. Per visualizzare tutti i log relativi ai modelli di programmazione attore, sarà necessario eseguire una query relativa a "Type=ServiceFabricReliableActorEvent”, mentre per visualizzare tutti i log del modello di programmazione Reliable Services sarà necessario specificare "Type=ServiceFabricReliableServiceEvent" ![Query e visualizzazione di log di Operational Insights](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/view-logs-oi.png)

### Esempi di query per semplificare la risoluzione dei problemi
Ecco gli esempi relativi ad alcuni scenari e le query che possono essere usate per risolvere i problemi correlati.

1. È possibile verificare se RunAsync è stato chiamato da infrastruttura di servizi per un servizio specifico. Ciò consente di scoprire se si verifica l'arresto anomalo di un servizio in fase di avvio. A questo scopo, eseguire una query analoga alla seguente, sostituendo il nome del servizio e dell'applicazione in modo che corrispondano ai valori distribuiti, e verificare se vengono restituiti risultati.

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateless1" AND "RunAsync has been invoked"
    ```

2. Se si esegue un servizio con stato e si vuole verificare se eventuali eccezioni generate dal servizio sono state contrassegnate come errori da infrastruttura di servizi, sarà possibile individuare questi eventi con una query analoga alla seguente.

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateful1" AND TaskName=StatefulRunAsyncFailure
    ```

3. Per individuare eventi corrispondenti a eventuali eccezioni generate dai metodi Actor in tutte le applicazioni e i servizi distribuiti, è possibile usare una query analoga alla seguente.

    ```
    Type=ServiceFabricReliableActorEvent AND TaskName=ActorMethodThrewException
    ```

## Aggiornare Diagnostica di Microsoft Azure per raccogliere e caricare log da nuovi canali EventSource
Per aggiornare Diagnostica di Microsoft Azure in modo da raccogliere log da un nuovo canale EventSource che rappresenta una nuova applicazione da distribuire, è sufficiente eseguire gli stessi passaggi illustrati nella [sezione precedente](#deploywadarm), che illustra la configurazione di Diagnostica di Microsoft Azure per un cluster esistente. Sarà necessario aggiornare la sezione EtwEventSourceProviderConfiguration nel file WadConfigUpdate.json per aggiungere voci relative al nuovo canale EventSources prima di applicare l'aggiornamento della configurazione mediante il comando ARM. La tabella per il caricamento sarà uguale (ETWEventTable), perché si tratta della tabella configurata per Operational Insights per la lettura degli eventi ETW dell'applicazione.


## Passaggi successivi
Verificare gli eventi di diagnostica emessi per [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) e [Reliable Services](service-fabric-reliable-services-diagnostics.md) per ottenere informazioni più dettagliate sugli eventi da esaminare durante la risoluzione dei problemi.

<!---HONumber=Nov15_HO4-->
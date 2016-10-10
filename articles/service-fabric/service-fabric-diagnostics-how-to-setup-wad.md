<properties
   pageTitle="Come raccogliere log con Diagnostica di Azure | Microsoft Azure"
   description="Questo articolo illustra come configurare Diagnostica di Azure per raccogliere log da un cluster di Service Fabric in esecuzione in Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="ms-toddabel"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="toddabel"/>


# Come raccogliere log con Diagnostica di Azure

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

Quando si esegue un cluster Azure Service Fabric, è consigliabile raccogliere i log da tutti i nodi in una posizione centrale. Il salvataggio dei log in una posizione centrale semplifica l'analisi e la risoluzione di eventuali problemi nel cluster o nelle applicazioni e nei servizi in esecuzione nel cluster. Uno dei modi per caricare e raccogliere i log consiste nell'usare l'estensione Diagnostica di Azure, che carica i log nell'archiviazione di Azure. Effettivamente i log non sono utili direttamente nell'archiviazione, ma è possibile usare un processo esterno per leggere gli eventi dalla risorsa di archiviazione e inserirli in un prodotto come [Log Analytics](../log-analytics/log-analytics-service-fabric.md), [ElasticSearch](service-fabric-diagnostic-how-to-use-elasticsearch.md) o un'altra soluzione di analisi di log.

## Prerequisiti
Questi strumenti verranno usati per eseguire alcune operazioni nel documento:

* [Diagnostica di Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (correlato ai Servizi cloud di Azure, include alcune informazioni ed esempi utili)
* [Gestione risorse di Azure](../resource-group-overview.md)
* [Azure PowerShell](../powershell-install-configure.md)
* [Client di Azure Resource Manager](https://github.com/projectkudu/ARMClient)
* [Creare una macchina virtuale Windows con monitoraggio e diagnostica mediante i modelli di Gestione risorse di Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)


## Diverse origini di log da raccogliere
1. **Log di Service Fabric:** emessi dalla piattaforma in canali ETW ed EventSource standard. I log possono essere di diversi tipi:
  - Eventi operativi: log relativi a operazioni eseguite dalla piattaforma Service Fabric. Gli esempi includono la creazione di applicazioni e servizi, le modifiche allo stato dei nodi e informazioni sull'aggiornamento.
  - [Eventi relativi al modello di programmazione attore](service-fabric-reliable-actors-diagnostics.md)
  - [Eventi relativi al modello di programmazione Reliable Services](service-fabric-reliable-services-diagnostics.md)
2. **Eventi dell'applicazione:** eventi emessi dal codice del servizio e scritti mediante la classe helper EventSource disponibile nei modelli di Visual Studio. Per altre informazioni su come scrivere i log dall'applicazione, vedere [l'articolo relativo al monitoraggio e alla diagnosi dei servizi in una configurazione con computer locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).


## Distribuzione dell'estensione di diagnostica
Il primo passaggio per la raccolta dei log consiste nel distribuire l'estensione Diagnostica in ogni VM del cluster Service Fabric. Tale estensione raccoglierà i log in ogni VM e li caricherà nell'account di archiviazione specificato. La procedura varia se si sceglie di usare il portale di Azure o Gestione risorse di Azure e se la distribuzione viene eseguita come parte della creazione di un cluster o per un cluster già esistente. Ecco la procedura per ogni scenario.

### Distribuire l'estensione di diagnostica come parte della creazione di cluster tramite il portale
Per distribuire l'estensione di diagnostica nelle VM del cluster come parte della creazione di cluster, verrà usato il pannello Impostazioni di diagnostica illustrato nella figura seguente. Per abilitare la raccolta di eventi Actor o Reliable Service, verificare che l'opzione Diagnostica sia impostata su **Sì**, che corrisponde all'impostazione predefinita. Dopo aver creato il cluster, queste impostazioni non possono essere modificate tramite il portale.

![Impostazione di Diagnostica di Azure nel portale per la creazione di cluster](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

I log di supporto sono **necessari** al team di supporto di Azure per gestire le richieste di supporto create dall'utente. Questi log vengono raccolti in tempo reale e verranno archiviati in uno degli account di archiviazione creato nel gruppo di risorse. L'impostazione di Diagnostica consente di configurare eventi a livello di applicazione, come eventi [Actor](service-fabric-reliable-actors-diagnostics.md), eventi [Reliable Service](service-fabric-reliable-services-diagnostics.md) e alcuni eventi di Service Fabric a livello di sistema da archiviare in Archiviazione di Azure. Gli eventi possono essere acquisiti dall'account di archiviazione da prodotti come [ElasticSearch](service-fabric-diagnostic-how-to-use-elasticsearch.md) o da un processo personalizzato. Attualmente non è possibile filtrare o eliminare gli eventi inviati alla tabella. Se non viene implementato un processo per rimuovere gli eventi dalla tabella, le dimensioni della tabella continueranno ad aumentare.

Quando si crea un cluster con il portale, è consigliabile scaricare il modello *prima di fare clic su OK* per creare il cluster. Per informazioni dettagliate, vedere [Configurare un cluster di Service Fabric usando un modello di Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Si otterrà un modello di Azure Resource Manager utilizzabile per il cluster che si intende creare. Questo modello è necessario per apportare modifiche in un secondo momento, perché non tutte possono essere fatte tramite il portale. I modelli possono essere esportati dal portale attenendosi alla procedura seguente, ma questi modelli possono risultare più difficili da usare, perché possono presentare un certo numero di valori Null, per cui si dovranno fornire i valori, o non mancare di tutte le informazioni necessarie.

1. Aprire il gruppo di risorse
2. Selezionare Impostazioni per visualizzare il pannello Impostazioni
3. Selezionare Distribuzioni per visualizzare il pannello Cronologia distribuzioni
4. Selezionare una distribuzione per visualizzare i relativi dettagli
5. Selezionare Esporta modello per visualizzare il pannello Modello
6. Selezionare Salva su file per esportare un file con estensione zip contenente i file del modello, dei parametri e di PowerShell.

Dopo l'esportazione dei file, è necessario apportare una modifica. Modificare il file **parameters.json** e rimuovere l'elemento **adminPassword**. In questo modo viene richiesta la password quando viene eseguito lo script di distribuzione. Quando si esegue lo script di distribuzione, è necessario correggere i valori dei parametri Null. Per usare il modello scaricato per aggiornare una configurazione

1. Estrarre il contenuto in una cartella nel computer locale
2. Modificare il contenuto in modo da riflettere la nuova configurazione
3. Avviare PowerShell e passare alla cartella in cui è stato estratto il contenuto
4. Eseguire **deploy.ps1** e immettere ID sottoscrizione, nome del gruppo di risorse (usare lo stesso nome per aggiornare la configurazione) e un nome di distribuzione univoco


### Distribuire l'estensione di diagnostica come parte della creazione di cluster tramite Azure Resource Manager
Per creare un cluster tramite Gestione risorse, è necessario aggiungere il file JSON di configurazione di Diagnostica al modello di Gestione risorse di tipo cluster completo prima di creare il cluster. Gli esempi relativi ai modelli di Gestione risorse includono un modello di cluster con 5 VM con aggiunta della configurazione di Diagnostica, disponibile nella raccolta di esempi di Azure nella pagina relativa all'[esempio di modello di Gestione risorse di cluster con cinque nodi con Diagnostica](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad). Per visualizzare l'impostazione di Diagnostica nel modello di Resource Manager, aprire il file **azuredeploy.json** e cercare **IaaSDiagnostics**. Per creare un cluster con questo modello, è sufficiente premere il pulsante di **distribuzione in Azure** disponibile nel collegamento precedente.

In alternativa, è possibile scaricare l'esempio di Gestione risorse, modificarlo e creare un cluster con il modello modificato mediante il comando `New-AzureRmResourceGroupDeployment` in una finestra di Azure PowerShell. Per informazioni sui parametri da passare al comando, vedere più avanti. Per informazioni dettagliate sulla distribuzione di un gruppo di risorse con PowerShell, vedere l'articolo su come [distribuire un gruppo di risorse con un modello di Azure Resource Manager](../resource-group-template-deploy.md).

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### Distribuire l'estensione di diagnostica in un cluster esistente
Se in un cluster esistente non è stata distribuita l'estensione di diagnostica o se si vuole modificare una configurazione esistente, è possibile aggiungerla o aggiornarla seguendo questa procedura. Modificare il modello di Azure Resource Manager usato per creare il cluster esistente o scaricare il modello dal portale come descritto in precedenza. Modificare il file **template.json** seguendo questa procedura:

Aggiungere una nuova risorsa di archiviazione al modello nella sezione risorse.

##### Aggiornare la sezione risorse
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

 Aggiungere quindi alla sezione parametri subito dopo le definizioni dell'account di archiviazione, tra "supportLogStorageAccountName" e "vmNodeType0Name". Sostituire il testo segnaposto *storage account name goes here* con il nome dell'account di archiviazione desiderato.

##### Aggiornare la sezione dei parametri
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
Aggiornare quindi la sezione *VirtualMachineProfile* del file **template.json** aggiungendo quanto segue all'interno della matrice "extensions". Assicurarsi di aggiungere una virgola all'inizio o alla fine, a seconda del punto di inserimento.

##### Aggiungere alla matrice di estensioni di VirtualMachineProfile
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

Dopo aver modificato il file **template.json** come descritto, pubblicare nuovamente il modello di Azure Resource Manager. Se il modello è stato esportato, eseguire il file **deploy.ps1** per pubblicarlo di nuovo. Dopo la distribuzione, assicurarsi che il valore di *ProvisioningState* sia *Succeeded*.


## Aggiornare Diagnostica per raccogliere e caricare log da nuovi canali EventSource
Per aggiornare la diagnostica in modo da raccogliere log da nuovi canali EventSource che rappresentano una nuova applicazione da distribuire, è sufficiente eseguire gli stessi passaggi illustrati nella [sezione precedente](#deploywadarm) che descrive la configurazione della diagnostica per un cluster esistente. Sarà necessario aggiornare la sezione *EtwEventSourceProviderConfiguration* nel file **template.json** per aggiungere voci relative ai nuovi canali EventSource prima di applicare l'aggiornamento della configurazione con il comando *New-AzureRmResourceGroupDeployment* di PowerShell. Il nome dell'origine evento è definito come parte del codice del file **ServiceEventSource.cs** generato da Visual Studio.


## Passaggi successivi
Verificare gli eventi di diagnostica emessi per [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) e [Reliable Services](service-fabric-reliable-services-diagnostics.md) per ottenere informazioni più dettagliate sugli eventi da esaminare durante la risoluzione dei problemi.


## Articoli correlati
* [Informazioni su come raccogliere i contatori delle prestazioni o i registri mediante le estensioni di diagnostica](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)
* [Soluzione Service Fabric in Log Analytics](../log-analytics/log-analytics-service-fabric.md)

<!---HONumber=AcomDC_0928_2016-->
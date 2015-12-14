<properties
	pageTitle="Creare una macchina virtuale Windows con monitoraggio e diagnostica mediante i modelli di Gestione risorse di Azure | Microsoft Azure"
	description="Usare un modello di Gestione risorse di Azure per creare una nuova macchina virtuale Windows con l'estensione Diagnostica di Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="sbtron"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/13/2015"
	ms.author="saurabh"/>

# Creare una macchina virtuale Windows con monitoraggio e diagnostica mediante i modelli di Gestione risorse di Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Questo articolo illustra l’utilizzo del modello di distribuzione Gestione risorse.

L'estensione Diagnostica di Azure offre le funzionalità di monitoraggio e diagnostica in una macchina virtuale Windows basata su Azure. È possibile abilitare queste funzionalità nella macchina virtuale includendo l'estensione come parte del modello di Gestione risorse di Azure. Per altre informazioni sull'inclusione di un'estensione come parte di un modello di macchina virtuale, vedere [Creazione di modelli di Gestione risorse di Azure con le estensioni di macchina virtuale](virtual-machines-extensions-authoring-templates.md). Questo articolo illustra come aggiungere l'estensione Diagnostica di Azure a un modello di macchina virtuale Windows.
  

## Aggiungere l'estensione Diagnostica di Azure alla definizione della risorsa della VM 

Per abilitare l'estensione Diagnostica in una macchina virtuale Windows, è necessario aggiungere l'estensione come risorsa della VM nel modello di Gestione risorse.

Per una semplice macchina virtuale basata su Gestione risorse, aggiungere la configurazione dell'estensione alla matrice *resources* per la macchina virtuale:

	"resources": [
                {
                    "name": "Microsoft.Insights.VMDiagnosticsSettings",
                    "type": "extensions",
                    "location": "[resourceGroup().location]",
                    "apiVersion": "2015-06-15",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
                    ],
                    "tags": {
                        "displayName": "AzureDiagnostics"
                    },
                    "properties": {
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "type": "IaaSDiagnostics",
                        "typeHandlerVersion": "1.5",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
                        },
                        "protectedSettings": {
                            "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                            "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                        }
                    }
                }
            ]


Un'altra convenzione comune consiste nell'aggiungere la configurazione dell'estensione al nodo delle risorse radice del modello, invece di definirla nel nodo delle risorse della macchina virtuale. Con questo approccio è necessario specificare esplicitamente una relazione gerarchica tra l'estensione e la macchina virtuale con i valori *name* e *type*. Ad esempio:
  
	"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
    "type": "Microsoft.Compute/virtualMachines/extensions",

L'estensione è sempre associata alla macchina virtuale. È quindi possibile definirla direttamente nel nodo delle risorse della macchina virtuale oppure è possibile definirla a livello di base e usare la convenzione di denominazione gerarchica per associarla alla macchina virtuale.

Per i set di scalabilità di macchine virtuali, la configurazione delle estensioni viene specificata nella proprietà *extensionProfile* di *VirtualMachineProfile*.
   
La proprietà *publisher* con valore **Microsoft.Azure.Diagnostics** e la proprietà *type* con valore **IaaSDiagnostics** identificano in modo univoco l'estensione Diagnostica di Azure.

Il valore della proprietà *name* può essere usato per fare riferimento all'estensione nel gruppo di risorse. Se lo si imposta esplicitamente su **Microsoft.Insights.VMDiagnosticsSettings**, se ne semplificherà l'identificazione da parte del portale di Azure classico, assicurando che i grafici di monitoraggio vengano visualizzati correttamente nel portale di Azure classico.

Il valore *typeHandlerVersion* specifica la versione dell'estensione da usare. Se si imposta la versione secondaria *autoUpgradeMinorVersion* su **true**, si assicurerà di ottenere la versione secondaria più recente disponibile dell'estensione. È consigliabile impostare sempre *autoUpgradeMinorVersion* su **true**, in modo che venga usata sempre l'estensione Diagnostica più recente disponibile con tutte le nuove funzionalità e correzioni di bug.

L'elemento *settings* contiene proprietà di configurazione per l'estensione che possono essere impostate e lette dall'estensione e sono spesso definite configurazione pubblica. La proprietà *xmlcfg* contiene configurazione basata su XML per i log di diagnostica e i contatori delle prestazioni che verranno raccolti dall'agente di diagnostica. Per altre informazioni sullo schema XML stesso, vedere [Schema di configurazione di diagnostica](https://msdn.microsoft.com/library/azure/dn782207.aspx). Una procedura comune consiste nell'archiviare la configurazione XML effettiva come variabile nel modello di Gestione risorse di Azure e quindi concatenare le variabili e codificarle in Base 64 per impostare il valore per *xmlcfg*. Per altre informazioni su come archiviare il codice XML nelle variabili, vedere la sezione relativa alle [variabili di configurazione diagnostica](#diagnostics-configuration-variables). La proprietà *storageAccount* specifica il nome dell'account di archiviazione in cui verranno trasferiti i dati di diagnostica.
 
Le proprietà in *protectedSettings*, a volte definite configurazione privata, possono essere impostate ma non possono essere lette dopo l'impostazione. La natura di sola scrittura di *protectedSettings* risulta utile per l'archiviazione di segreti quali la chiave dell'account di archiviazione in cui verranno scritti i dati di diagnostica.

## Specificare l'account di archiviazione di diagnostica come parametro 

Il frammento di codice JSON dell'estensione Diagnostica precedente presuppone due parametri *existingdiagnosticsStorageAccountName* e *existingdiagnosticsStorageAccountName* per specificare l'account di archiviazione di diagnostica in cui verranno archiviati i dati di diagnostica. Se si specifica l'account di archiviazione di diagnostica come parametro, sarà possibile modificare con maggiore facilità l'account di archiviazione di diagnostica in diversi ambienti. È ad esempio possibile usare un account di archiviazione di diagnostica per i test e un account di archiviazione diverso per la distribuzione di produzione.

        "existingdiagnosticsStorageAccountName": {
            "type": "string",
            "metadata": {
        "description": "The name of an existing storage account to which diagnostics data will be transfered."
			}        
		},
        "existingdiagnosticsStorageResourceGroup": {
            "type": "string",
            "metadata": {
        "description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
      		}
        }

È consigliabile specificare un account di archiviazione di diagnostica in un gruppo di risorse diverso rispetto al gruppo di risorse per la macchina virtuale. Un gruppo di risorse può essere considerato come un'unità di distribuzione con una durata specifica. È possibile distribuire e ridistribuire una macchina virtuale ogni volta che vengono applicati aggiornamenti della configurazione, ma è consigliabile continuare ad archiviare i dati di diagnostica nello stesso account di archiviazione nelle distribuzioni della macchina virtuale. La disponibilità dell'account di archiviazione in una risorsa diversa consente all'account di archiviazione di accettare i dati da diverse distribuzioni di macchina virtuale, semplificando la risoluzione dei problemi nelle diverse versioni.

>[AZURE.NOTE]Se si crea un modello di macchina virtuale Windows da Visual Studio, è possibile configurare l'account di archiviazione predefinito in modo che usi lo stesso account di archiviazione in cui è stato caricato il VHD della macchina virtuale. Ciò consente di semplificare la configurazione iniziale della VM. È consigliabile effettuare il refactoring del modello in modo che usi un account di archiviazione diverso, che possa essere passato come parametro.

## Variabili di configurazione della diagnostica
 
Il frammento di codice JSON dell'estensione Diagnostica precedente definisce una variabile *accountid* per semplificare il recupero della chiave dell'account di archiviazione per l'archiviazione dei dati di diagnostica:
	
	"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"


La proprietà *xmlcfg* per l'estensione Diagnostica viene definita mediante più variabili concatenate. I valori di questi variabili sono in XML, quindi è necessario inserire correttamente il carattere di escape durante la configurazione delle variabili JSON.

L'esempio seguente illustra il codice XML di configurazione di diagnostica che raccoglie i contatori delle prestazioni standard a livello di sistema, insieme ad alcuni registri eventi di Windows e log di infrastruttura di diagnostica. I caratteri di escape e la formattazione sono corretti, quindi è possibile incollare la configurazione direttamente nella sezione del modello relativa alle variabili. Per un esempio più facile da leggere del codice XML di configurazione, vedere [Schema di configurazione di diagnostica](https://msdn.microsoft.com/library/azure/dn782207.aspx).
    
        "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB="4096" xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/> <WindowsEventLog scheduledTransferPeriod="PT1M" > <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="Security!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="System!*[System[(Level = 1 or Level = 2)]]" /></WindowsEventLog>",
        "wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod="PT1M"><PerformanceCounterConfiguration counterSpecifier="\\Processor(_Total)\\% Processor Time" sampleRate="PT15S" unit="Percent"><annotation displayName="CPU utilization" locale="it-IT"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\Processor(_Total)\\% Privileged Time" sampleRate="PT15S" unit="Percent"><annotation displayName="CPU privileged time" locale="it-IT"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\Processor(_Total)\\% User Time" sampleRate="PT15S" unit="Percent"><annotation displayName="CPU user time" locale="it-IT"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\Processor Information(_Total)\\Processor Frequency" sampleRate="PT15S" unit="Count"><annotation displayName="CPU frequency" locale="it-IT"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\System\\Processes" sampleRate="PT15S" unit="Count"><annotation displayName="Processes" locale="it-IT"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\Process(_Total)\\Thread Count" sampleRate="PT15S" unit="Count"><annotation displayName="Threads" locale="it-IT"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\Process(_Total)\\Handle Count" sampleRate="PT15S" unit="Count"><annotation displayName="Handles" locale="it-IT"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\Memory\\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent"><annotation displayName="Memory usage" locale="it-IT"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\Memory\\Available Bytes" sampleRate="PT15S" unit="Bytes"><annotation displayName="Memory available" locale="it-IT"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\Memory\\Committed Bytes" sampleRate="PT15S" unit="Bytes"><annotation displayName="Memory committed" locale="it-IT"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\Memory\\Commit Limit" sampleRate="PT15S" unit="Bytes"><annotation displayName="Memory commit limit" locale="it-IT"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\PhysicalDisk(_Total)\\% Disk Time" sampleRate="PT15S" unit="Percent"><annotation displayName="Disk active time" locale="it-IT"/></PerformanceCounterConfiguration>",
        "wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier="\\PhysicalDisk(_Total)\\% Disk Read Time" sampleRate="PT15S" unit="Percent"><annotation displayName="Disk active read time" locale="it-IT"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\PhysicalDisk(_Total)\\% Disk Write Time" sampleRate="PT15S" unit="Percent"><annotation displayName="Disk active write time" locale="it-IT"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\PhysicalDisk(_Total)\\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond"><annotation displayName="Disk operations" locale="it-IT"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\PhysicalDisk(_Total)\\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond"><annotation displayName="Disk read operations" locale="it-IT"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\PhysicalDisk(_Total)\\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond"><annotation displayName="Disk write operations" locale="it-IT"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\PhysicalDisk(_Total)\\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond"><annotation displayName="Disk speed" locale="it-IT"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\PhysicalDisk(_Total)\\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond"><annotation displayName="Disk read speed" locale="it-IT"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\PhysicalDisk(_Total)\\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond"><annotation displayName="Disk write speed" locale="it-IT"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier="\\LogicalDisk(_Total)\\% Free Space" sampleRate="PT15S" unit="Percent"><annotation displayName="Disk free space (percentage)" locale="it-IT"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId="')]",
        "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
        "wadcfgxend": ""><MetricAggregation scheduledTransferPeriod="PT1H"/><MetricAggregation scheduledTransferPeriod="PT1M"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"

Il nodo XML della definizione Metrics nella configurazione precedente è un elemento di configurazione importante, perché definisce il modo in cui i contatori delle prestazioni specificati in precedenza nel codice XML nel nodo *PerformanceCounter* verranno aggregati e archiviati. Queste metriche sono alla base di grafici e avvisi del portale di Azure. È quindi importante includerle nella configurazione, se si vogliono visualizzare i dati di monitoraggio nel portale.

L'esempio seguente illustra il codice XML per le definizioni delle metriche:

		<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
			<MetricAggregation scheduledTransferPeriod="PT1H"/>
			<MetricAggregation scheduledTransferPeriod="PT1M"/>
		</Metrics>

L'attributo *resourceID* identifica in modo univoco la macchina virtuale nella sottoscrizione. Assicurarsi di usare le funzioni subscription() e resourceGroup(), in modo che il modello aggiorni automaticamente questi valori in base alla sottoscrizione e al gruppo di risorse in cui si effettua la distribuzione.

Se si creano più macchine virtuali in un ciclo, sarà necessario popolare il valore *resourceID* con una funzione copyIndex(), in modo da distinguere correttamente ogni singola VM. Il valore *xmlCfg* può essere aggiornato per supportare questa operazione, in modo analogo al seguente:

	"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 


Il valore MetricAggregation impostato su *PT1H* e *PT1M* indica un'aggregazione pari a un minuto e un'aggregazione pari a un'ora.

La configurazione precedente relativa a Metrics consentirà di generare tabelle nell'account di archiviazione di diagnostica con le convenzioni di denominazione seguenti:

- **WADMetrics**: prefisso standard per tutte le tabelle WADMetrics
- **PT1H** o **PT1M**: indica che la tabella contiene dati aggregati per un periodo pari a un'ora o 1 minuto.
- **P10D**: indica che la tabella conterrà dati per 10 giorni a partire dal momento in cui la tabella ha iniziato a raccogliere i dati.
- **V2S**: costante di tipo stringa.
- **yyyymmdd**: data a partire dalla quale la tabella ha iniziato a raccogliere i dati.

Esempio: *WADMetricsPT1HP10DV2S20151108* includerà i dati aggregati delle metriche per un periodo pari a un'ora per 10 giorni a partire dall'11 nov 2015

Ogni tabella WADMetrics includerà le colonne seguenti:

- **PartitionKey**: il valore partitionkey viene costruito in base al valore *resourceID* per identificare in modo univoco la risorsa della VM, ad esempio: 002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
- **RowKey**: usa il formato <Descending time tick>:<Performance Counter Name>. Il calcolo relativo ai tick temporali decrescenti corrisponde al numero massimo di tick temporali meno l'ora di inizio del periodo di aggregazione. Ad esempio, se il periodo di campionamento è iniziato il 10 nov 2015 alle 00:00Hrs UTC, il calcolo sarà il seguente: DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks). Per il contatore delle prestazioni dei byte disponibile della memoria, la riga avrà un aspetto analogo al seguente: 2519551871999999999\_\_:005CMemory:005CAvailable:0020Bytes
- **CounterName**: nome del contatore delle prestazioni. Corrisponde al valore *counterSpecifier* definito nel file di configurazione XML.
- **Maximum**: valore massimo del contatore delle prestazioni nel periodo di aggregazione.
- **Minimum**: valore minimo del contatore delle prestazioni nel periodo di aggregazione.
- **Total**: somma di tutti i valori del contatore delle prestazioni rilevati nel periodo di aggregazione.
- **Count**: numero totale di valori rilevati per il contatore delle prestazioni.
- **Average**: valore medio (totale/conteggio) del contatore delle prestazioni nel periodo di aggregazione.


## Passaggi successivi

- Per un modello di esempio completo di macchina virtuale Windows con estensione Diagnostica, vedere [201-vm-monitoring-diagnostics-extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
- Distribuire il modello di Gestione risorse mediante [Azure PowerShell](virtual-machines-deploy-rmtemplates-powershell.md) o la [riga di comando di Azure](virtual-machines-deploy-rmtemplates-powershell.md)
- Altre informazioni sulla [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md)

<!---HONumber=AcomDC_1203_2015-->
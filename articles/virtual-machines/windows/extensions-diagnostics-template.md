---
title: Aggiungere monitoraggio e diagnostica a una macchina virtuale di Azure | Documentazione Microsoft
description: Utilizzare un modello di gestione risorse di Azure per creare una nuova macchina virtuale Windows con estensione diagnostica di Azure.
services: virtual-machines-windows
documentationcenter: 
author: sbtron
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: saurabh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e3ea1687e7fb6cc7af00e03b85fb48b0d7911275
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Usare monitoraggio e diagnostica con una macchina virtuale Windows e modelli di Azure Resource Manager
L'estensione di Diagnostica di Azure offre le funzionalità di monitoraggio e diagnostica in una macchina virtuale di Azure basata su Windows. È possibile abilitare queste funzionalità nella macchina virtuale includendo l'estensione come parte del modello di gestione risorse di Azure. Per altre informazioni sull'inclusione di un'estensione come parte di un modello di macchina virtuale, vedere [Creazione di modelli di Gestione risorse di Azure con le estensioni di macchina virtuale](template-description.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#extensions) . Questo articolo illustra come aggiungere l'estensione Diagnostica di Azure a un modello di macchina virtuale Windows.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Aggiungere l'estensione Diagnostica di Azure alla definizione della risorsa della VM
Per abilitare l'estensione di diagnostica in una macchina virtuale di Windows, è necessario aggiungere l'estensione come una risorsa di macchina virtuale nel modello di gestione risorse.

Per una semplice macchina virtuale basata su Gestione risorse, aggiungere la configurazione dell'estensione alla matrice *resources* per la macchina virtuale: 

```json
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
```

Un'altra convenzione comune consiste nell'aggiungere la configurazione dell'estensione nel nodo risorse radice del modello anziché definirla in nodo risorse della macchina virtuale. Con questo approccio, è necessario specificare in modo esplicito una relazione gerarchica tra l'estensione e la macchina virtuale con il *nome* e *tipo* valori. Ad esempio:  

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

L'estensione è sempre associata alla macchina virtuale. È quindi possibile definirla direttamente nel nodo delle risorse della macchina virtuale oppure è possibile definirla a livello di base e usare la convenzione di denominazione gerarchica per associarla alla macchina virtuale.

Per i set di scalabilità di macchine virtuali, la configurazione delle estensioni viene specificata nella proprietà *extensionProfile* di *VirtualMachineProfile*.

La proprietà *publisher* con valore **Microsoft.Azure.Diagnostics** e la proprietà *type* con valore **IaaSDiagnostics** identificano in modo univoco l'estensione Diagnostica di Azure.

Il valore della proprietà *name* può essere usato per fare riferimento all'estensione nel gruppo di risorse. Impostarlo in modo specifico a **Microsoft.Insights.VMDiagnosticsSettings** consente di identificare facilmente dal portale di Azure, assicurando che il monitoraggio grafici Mostra backup correttamente nel portale di Azure.

Il valore *typeHandlerVersion* specifica la versione dell'estensione da usare. Impostazione *autoUpgradeMinorVersion* versione secondaria **true** consente di ottenere la versione secondaria più recente dell'estensione che è disponibile. È consigliabile impostare sempre *autoUpgradeMinorVersion* su **true** , in modo che venga usata sempre l'estensione Diagnostica più recente disponibile con tutte le nuove funzionalità e correzioni di bug. 

L'elemento *settings* contiene proprietà di configurazione per l'estensione che possono essere impostate e lette dall'estensione e sono spesso definite configurazione pubblica. Il *xmlcfg* proprietà contiene una configurazione basata su xml per i log di diagnostica, i contatori delle prestazioni e così via che vengono raccolti dall'agente di diagnostica. Per altre informazioni sullo schema XML stesso, vedere [Schema di configurazione di diagnostica](https://msdn.microsoft.com/library/azure/dn782207.aspx) . Una procedura comune consiste nell'archiviare la configurazione XML effettiva come variabile nel modello di Gestione risorse di Azure e quindi concatenare le variabili e codificarle in Base 64 per impostare il valore per *xmlcfg*. Per altre informazioni su come archiviare il codice XML nelle variabili, vedere la sezione relativa alle [variabili di configurazione diagnostica](#diagnostics-configuration-variables) . Il *storageAccount* proprietà specifica il nome dell'account di archiviazione per la diagnostica quali dati vengono trasferiti. 

Le proprietà in *protectedSettings* , a volte definite configurazione privata, possono essere impostate ma non possono essere lette dopo l'impostazione. La natura di sola scrittura di *protectedSettings* risulta utile per l'archiviazione di segreti, ad esempio la chiave di account di archiviazione in cui vengono scritti i dati di diagnostica.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Specificare l'account di archiviazione di diagnostica come parametro
Il frammento json estensione diagnostica precedente presuppone che i due parametri *existingdiagnosticsStorageAccountName* e *existingdiagnosticsStorageResourceGroup* per specificare lo spazio di archiviazione di diagnostica account di archiviazione dati di diagnostica. Specifica l'account di archiviazione di diagnostica come parametro consente di modificare l'account di archiviazione di diagnostica in ambienti diversi, ad esempio si consiglia di utilizzare un account di archiviazione di diagnostica diverso per il test e uno per il distribuzione di produzione.  

```json
"existingdiagnosticsStorageAccountName": {
    "type": "string",
    "metadata": {
"description": "The name of an existing storage account to which diagnostics data is transfered."
    }        
},
"existingdiagnosticsStorageResourceGroup": {
    "type": "string",
    "metadata": {
"description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
      }
}
```

È consigliabile specificare un account di archiviazione di diagnostica in un gruppo di risorse diverso rispetto al gruppo di risorse per la macchina virtuale. Un gruppo di risorse può essere considerato come un'unità di distribuzione con una durata specifica. È possibile distribuire e ridistribuire una macchina virtuale ogni volta che vengono applicati aggiornamenti della configurazione, ma è consigliabile continuare ad archiviare i dati di diagnostica nello stesso account di archiviazione nelle distribuzioni della macchina virtuale. La disponibilità dell'account di archiviazione in una risorsa diversa consente all'account di archiviazione di accettare i dati da diverse distribuzioni di macchina virtuale, semplificando la risoluzione dei problemi nelle diverse versioni.

> [!NOTE]
> Se si crea un modello di macchina virtuale windows da Visual Studio, è possibile impostare l'account di archiviazione predefinito per utilizzare lo stesso account di archiviazione in cui viene caricato il VHD della macchina virtuale. Ciò consente di semplificare la configurazione iniziale della VM. Refactoring del modello per utilizzare un account di archiviazione diversi che può essere passato come parametro. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>variabili di configurazione diagnostica
Il frammento di codice JSON dell'estensione Diagnostica precedente definisce una variabile *accountid* per semplificare il recupero della chiave dell'account di archiviazione per l'archiviazione dei dati di diagnostica:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

La proprietà *xmlcfg* per l'estensione Diagnostica viene definita mediante più variabili concatenate. I valori di questi variabili sono in XML, quindi è necessario inserire correttamente il carattere di escape durante la configurazione delle variabili JSON.

L'esempio seguente illustra il codice XML di configurazione di diagnostica che raccoglie i contatori delle prestazioni standard a livello di sistema, insieme ad alcuni registri eventi di Windows e log di infrastruttura di diagnostica. I caratteri di escape e la formattazione sono corretti, quindi è possibile incollare la configurazione direttamente nella sezione del modello relativa alle variabili. Per un esempio più facile da leggere del codice XML di configurazione, vedere [Schema di configurazione di diagnostica](https://msdn.microsoft.com/library/azure/dn782207.aspx) .

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

Il nodo xml di definizione delle metriche nella configurazione precedente è un elemento di configurazione importanti poiché consente di definire come i contatori delle prestazioni definito in precedenza nel codice xml in *PerformanceCounter* nodo vengono aggregati e archiviati. 

> [!IMPORTANT]
> Queste metriche guidano i grafici di monitoraggio e gli avvisi nel portale di Azure.  Il nodo **Metriche** con *resourceID* e **MetricAggregation** deve essere incluso nella configurazione di diagnostica per la macchina virtuale per visualizzare i dati di monitoraggio della macchina virtuale nel portale di Azure. 
> 
> 

L'esempio seguente illustra il codice XML per le definizioni delle metriche: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

L'attributo *resourceID* identifica in modo univoco la macchina virtuale nella sottoscrizione. Assicurarsi di usare le funzioni subscription() e resourceGroup(), in modo che il modello aggiorni automaticamente questi valori in base alla sottoscrizione e al gruppo di risorse in cui si effettua la distribuzione.

Se si siano creando più macchine virtuali in un ciclo, è necessario popolare il *resourceID* valore con una funzione copyIndex() per distinguere correttamente ogni singole macchine Virtuali. Il valore *xmlCfg* può essere aggiornato per supportare questa operazione, in modo analogo al seguente:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

Il valore MetricAggregation impostato su *PT1H* e *PT1M* indica un'aggregazione pari a un'ora e un'aggregazione pari a un minuto.

## <a name="wadmetrics-tables-in-storage"></a>Tabelle WADMetrics nell'archiviazione
La configurazione di metriche precedente genera tabelle nell'account di archiviazione di diagnostica con le convenzioni di denominazione seguente:

* **WADMetrics**: prefisso Standard per tutte le tabelle WADMetrics
* **PT1H** o **PT1M**: indica che la tabella contiene dati aggregati in 1 ora o 1 minuto
* **P10D**: indica la tabella conterrà i dati di 10 giorni da quando la tabella iniziato a raccogliere i dati
* **V2S**: costante di stringa
* **aaaammgg**: la data in cui è iniziata la raccolta dei dati della tabella

Esempio: *WADMetricsPT1HP10DV2S20151108* includerà i dati aggregati delle metriche per un periodo pari a un'ora per 10 giorni a partire dall'11 nov 2015    

Ogni tabella WADMetrics includerà le colonne seguenti:

* **PartitionKey**: la chiave di partizione viene costruita in base il *resourceID* per identificare in modo univoco la risorsa di macchina virtuale. Ad esempio: 002Fsubscriptions:<subscriptionID>: 002FresourceGroups:002F<ResourceGroupName>: 002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
* **RowKey**: viene indicato il formato `<Descending time tick>:<Performance Counter Name>`. Il calcolo relativo ai tick temporali decrescenti corrisponde al numero massimo di tick temporali meno l'ora di inizio del periodo di aggregazione. Se il periodo di campionamento avviato nel novembre-10/2015 e sarebbe 00:00Hrs UTC quindi il calcolo, ad esempio: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`. Il contatore delle prestazioni la chiave di riga sarà simile per i byte di memoria disponibile:`2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: È il nome del contatore delle prestazioni. Corrisponde al valore *counterSpecifier* definito nel file di configurazione XML.
* **Massimo**: il valore massimo del contatore delle prestazioni nel periodo di aggregazione.
* **Minimo**: il valore minimo del contatore delle prestazioni nel periodo di aggregazione.
* **Totale**: la somma di tutti i valori del contatore delle prestazioni segnalati nel periodo di aggregazione.
* **Conteggio**: il numero totale di valori segnalati per il contatore delle prestazioni.
* **Media**: il valore medio (totale) del contatore delle prestazioni nel periodo di aggregazione.

## <a name="next-steps"></a>Fasi successive
* Per un modello di esempio completo di una macchina virtuale di Windows con l'estensione di diagnostica, vedere [201-vm-monitoraggio--estensione di diagnostica](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Distribuire il modello di gestione risorse di Azure tramite [Azure PowerShell](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o [riga di comando di Azure](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Altre informazioni sulla [Creazione di modelli di Gestione risorse di Azure](../../resource-group-authoring-templates.md)

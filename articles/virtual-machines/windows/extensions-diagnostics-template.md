---
title: Aggiungere monitoraggio e diagnostica a una macchina virtuale di Azure | Documentazione Microsoft
description: Usare un modello di Azure Resource Manager per creare una nuova macchina virtuale Windows con l'estensione Diagnostica di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: sbtron
manager: timlt
editor: ''
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
ms.openlocfilehash: e205352ebf4eaf89627c268d78b69bb2d49c3f3e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Usare monitoraggio e diagnostica con una macchina virtuale Windows e modelli di Azure Resource Manager
L'estensione di Diagnostica di Azure offre le funzionalità di monitoraggio e diagnostica in una macchina virtuale di Azure basata su Windows. È possibile abilitare queste funzionalità nella macchina virtuale includendo l'estensione come parte del modello di Azure Resource Manager. Per altre informazioni sull'inclusione di un'estensione come parte di un modello di macchina virtuale, vedere [Creazione di modelli di Gestione risorse di Azure con le estensioni di macchina virtuale](template-description.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#extensions) . Questo articolo illustra come aggiungere l'estensione Diagnostica di Azure a un modello di macchina virtuale Windows.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Aggiungere l'estensione Diagnostica di Azure alla definizione della risorsa della VM
Per abilitare l'estensione Diagnostica in una macchina virtuale Windows, è necessario aggiungere l'estensione come risorsa della macchina virtuale nel modello di Resource Manager.

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

Un'altra convenzione comune consiste nell'aggiungere la configurazione dell'estensione al nodo delle risorse radice del modello, invece di definirla nel nodo delle risorse della macchina virtuale. Con questo approccio è necessario specificare esplicitamente una relazione gerarchica tra l'estensione e la macchina virtuale con i valori *name* e *type*. Ad esempio:  

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

L'estensione è sempre associata alla macchina virtuale. È quindi possibile definirla direttamente nel nodo delle risorse della macchina virtuale oppure è possibile definirla a livello di base e usare la convenzione di denominazione gerarchica per associarla alla macchina virtuale.

Per i set di scalabilità di macchine virtuali, la configurazione delle estensioni viene specificata nella proprietà *extensionProfile* di *VirtualMachineProfile*.

La proprietà *publisher* con valore **Microsoft.Azure.Diagnostics** e la proprietà *type* con valore **IaaSDiagnostics** identificano in modo univoco l'estensione Diagnostica di Azure.

Il valore della proprietà *name* può essere usato per fare riferimento all'estensione nel gruppo di risorse. Se viene impostato esattamente su **Microsoft.Insights.VMDiagnosticsSettings**, l'identificazione da parte del portale di Azure risulterà semplificata. I grafici di monitoraggio saranno quindi visualizzati correttamente nel portale di Azure.

Il valore *typeHandlerVersion* specifica la versione dell'estensione da usare. Se si imposta la versione secondaria *autoUpgradeMinorVersion* su **true**, si assicurerà di ottenere la versione secondaria più recente disponibile dell'estensione. È consigliabile impostare sempre *autoUpgradeMinorVersion* su **true** , in modo che venga usata sempre l'estensione Diagnostica più recente disponibile con tutte le nuove funzionalità e correzioni di bug. 

L'elemento *settings* contiene proprietà di configurazione per l'estensione che possono essere impostate e lette dall'estensione e sono spesso definite configurazione pubblica. La proprietà *xmlcfg* contiene configurazione basata su XML per i log di diagnostica e i contatori delle prestazioni che verranno raccolti dall'agente di diagnostica. Per altre informazioni sullo schema XML stesso, vedere [Schema di configurazione di diagnostica](https://msdn.microsoft.com/library/azure/dn782207.aspx) . Una procedura comune consiste nell'archiviare la configurazione XML effettiva come variabile nel modello di Gestione risorse di Azure e quindi concatenare le variabili e codificarle in Base 64 per impostare il valore per *xmlcfg*. Per altre informazioni su come archiviare il codice XML nelle variabili, vedere la sezione relativa alle [variabili di configurazione diagnostica](#diagnostics-configuration-variables) . La proprietà *storageAccount* specifica il nome dell'account di archiviazione in cui sono trasferiti i dati di diagnostica. 

Le proprietà in *protectedSettings* , a volte definite configurazione privata, possono essere impostate ma non possono essere lette dopo l'impostazione. La natura di sola scrittura di *protectedSettings* risulta utile per l'archiviazione di segreti quali la chiave dell'account di archiviazione in cui sono scritti i dati di diagnostica.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Specificare l'account di archiviazione di diagnostica come parametro
Il frammento di codice JSON dell'estensione Diagnostica precedente presuppone due parametri *existingdiagnosticsStorageAccountName* e *existingdiagnosticsStorageResourceGroup* per specificare l'account di archiviazione di diagnostica in cui sono archiviati i dati di diagnostica. Se si specifica l'account di archiviazione di diagnostica come parametro, sarà possibile modificare con maggiore facilità l'account di archiviazione di diagnostica in diversi ambienti. È ad esempio possibile usare un account di archiviazione di diagnostica per i test e un account di archiviazione diverso per la distribuzione di produzione.  

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
> Se si crea un modello di macchina virtuale Windows da Visual Studio, è possibile configurare l'account di archiviazione predefinito in modo che usi lo stesso account di archiviazione in cui è stato caricato il disco rigido virtuale della macchina virtuale. Ciò consente di semplificare la configurazione iniziale della VM. Effettuare il refactoring del modello in modo che usi un account di archiviazione diverso, che possa essere passato come parametro. 
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

Il nodo XML della definizione Metrics nella configurazione precedente è un elemento di configurazione importante, perché definisce il modo in cui i contatori delle prestazioni specificati in precedenza nel codice XML nel nodo *PerformanceCounter* sono aggregati e archiviati. 

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

Se si creano più macchine virtuali in un ciclo, è necessario popolare il valore *resourceID* con una funzione copyIndex(), in modo da distinguere correttamente ogni singola macchina virtuale. Il valore *xmlCfg* può essere aggiornato per supportare questa operazione, in modo analogo al seguente:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

Il valore MetricAggregation impostato su *PT1M* e *PT1H* indica rispettivamente un'aggregazione pari a un minuto e un'aggregazione pari a un'ora.

## <a name="wadmetrics-tables-in-storage"></a>Tabelle WADMetrics nell'archiviazione
La configurazione precedente relativa a Metrics consente di generare tabelle nell'account di archiviazione di diagnostica con le convenzioni di denominazione seguenti:

* **WADMetrics**: prefisso standard per tutte le tabelle WADMetrics
* **PT1H** o **PT1M**: indica che la tabella contiene dati aggregati per un periodo pari a un'ora o un minuto
* **P10D**: indica che la tabella conterrà dati per 10 giorni a partire dal momento in cui la tabella ha iniziato a raccogliere i dati
* **V2S**: costante di tipo stringa
* **yyyymmdd**: data a partire dalla quale la tabella ha iniziato a raccogliere i dati

Esempio: *WADMetricsPT1HP10DV2S20151108* includerà i dati aggregati delle metriche per un periodo pari a un'ora per 10 giorni a partire dall'11 nov 2015    

Ogni tabella WADMetrics includerà le colonne seguenti:

* **PartitionKey**: la chiave di partizione viene costruita in base al valore di *resourceID* per identificare in modo univoco la risorsa di macchina virtuale, ad esempio: 002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
* **RowKey**: usa il formato `<Descending time tick>:<Performance Counter Name>`. Il calcolo relativo ai tick temporali decrescenti corrisponde al numero massimo di tick temporali meno l'ora di inizio del periodo di aggregazione. Ad esempio, se il periodo di campionamento è stato avviato il 10 novembre 2015 alle 00:00 UTC, il calcolo sarebbe: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`. Per il contatore delle prestazioni dei byte di memoria disponibili, la chiave di riga avrà un aspetto simile al seguente: `2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: nome del contatore delle prestazioni. Corrisponde al valore *counterSpecifier* definito nel file di configurazione XML.
* **Maximum**: valore massimo del contatore delle prestazioni nel periodo di aggregazione.
* **Minimum**: valore minimo del contatore delle prestazioni nel periodo di aggregazione.
* **Total**: somma di tutti i valori del contatore delle prestazioni rilevati nel periodo di aggregazione.
* **Count**: numero totale di valori rilevati per il contatore delle prestazioni.
* **Average**: valore medio (totale/conteggio) del contatore delle prestazioni nel periodo di aggregazione.

## <a name="next-steps"></a>Passaggi successivi
* Per un modello di esempio completo di macchina virtuale Windows con estensione Diagnostica, vedere [201-vm-monitoring-diagnostics-extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Distribuire il modello di Azure Resource Manager con [Azure PowerShell](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o la [riga di comando di Azure](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Altre informazioni sulla [Creazione di modelli di Gestione risorse di Azure](../../resource-group-authoring-templates.md)

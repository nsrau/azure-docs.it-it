---
title: "Ridimensionamento automatico e set di scalabilità di macchine virtuali | Microsoft Docs"
description: "Informazioni sull'uso della diagnostica e delle risorse di scalabilità automatica per ridimensionare automaticamente le macchine virtuali in un set di scalabilità."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2017
ms.author: adegeo
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 06ff9d9ae1dd8256f0d22c1a60ed6a85554f1f17
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017


---
# <a name="how-to-use-automatic-scaling-and-virtual-machine-scale-sets"></a>Come usare ridimensionamento automatico e set di scalabilità di macchine virtuali
Il ridimensionamento automatico delle macchine virtuali in un set di scalabilità consiste nella creazione o nell'eliminazione di macchine nel set in base alle esigenze per soddisfare i requisiti a livello di prestazioni. Man mano che aumenta il volume di lavoro, è possibile che un'applicazione richieda risorse aggiuntive per poter eseguire le attività in modo efficace.

Il ridimensionamento automatico è un processo automatico che semplifica il sovraccarico di gestione. Riducendo il sovraccarico, non è necessario monitorare le prestazioni del sistema continuamente o decidere come gestire le risorse. Il ridimensionamento è un processo elastico. Con l'aumentare del carico è possibile aggiungere altre risorse e, man mano che la richiesta diminuisce, le risorse possono essere rimosse per ridurre al minimo i costi e mantenere i livelli di prestazioni.

Configurare il ridimensionamento automatico di un set di scalabilità tramite un modello di Azure Resource Manager, usando Azure PowerShell, l'interfaccia della riga di comando di Azure o il portale di Azure.

## <a name="set-up-scaling-by-using-resource-manager-templates"></a>Configurare il ridimensionamento usando modelli di Resource Manager
Invece di distribuire e gestire separatamente ogni risorsa dell'applicazione, usare un modello che distribuisce tutte le risorse in un'unica operazione coordinata. Nel modello vengono definite le risorse dell'applicazione e vengono specificati i parametri di distribuzione per diversi ambienti. Il modello è composto da JSON ed espressioni che è possibile usare per creare valori per la distribuzione. Per altre informazioni, vedere [Creazione di modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Nel modello si specifica l'elemento capacità:

```json
"sku": {
  "name": "Standard_A0",
  "tier": "Standard",
  "capacity": 3
},
```

La capacità identifica il numero di macchine virtuali nel set. È possibile modificare manualmente la capacità distribuendo un modello con un valore diverso. Se si distribuisce un modello per modificare solo la capacità, è possibile includere solo l'elemento SKU con la capacità aggiornata.

La capacità del set di scalabilità può essere regolata automaticamente usando una combinazione della risorsa **autoscaleSettings** e dell'estensione Diagnostica.

### <a name="configure-the-azure-diagnostics-extension"></a>Configurare l'estensione Diagnostica di Azure
Il ridimensionamento automatico può essere eseguito solo se la raccolta di metriche è riuscita in ogni macchina virtuale del set di scalabilità. L'estensione Diagnostica di Azure fornisce le funzionalità di monitoraggio e diagnostica che soddisfano le esigenze di raccolta delle metriche della risorsa di ridimensionamento automatico. È possibile installare l'estensione come parte del modello di Resource Manager.

Questo esempio mostra le variabili usate nel modello per configurare l'estensione di diagnostica:

```json
"diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
"accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Thread Count\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
"wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
```

I parametri vengono forniti quando si distribuisce il modello. In questo esempio vengono forniti il nome dell'account di archiviazione, in cui vengono archiviati i dati, e il nome del set di scalabilità, in cui vengono raccolti i dati. Anche in questo esempio di Windows Server vengono raccolti solo i dati del contatore delle prestazioni Conteggio dei thread. Tutti i contatori delle prestazioni disponibili in Windows o Linux possono essere usati per raccogliere informazioni di diagnostica e possono essere inclusi nella configurazione dell'estensione.

Questo esempio illustra la definizione dell'estensione nel modello:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "Microsoft.Insights.VMDiagnosticsSettings",
      "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
          "storageAccount": "[variables('diagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
          "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
          "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
          "storageAccountEndPoint": "https://core.windows.net"
        }
      }
    }
  ]
}
```

Quando viene eseguita l'estensione Diagnostica, i dati vengono archiviati e raccolti in una tabella, nell'account di archiviazione specificato. I dati raccolti sono disponibili nella tabella **WADPerformanceCounters**:

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### <a name="configure-the-autoscalesettings-resource"></a>Configurare la risorsa autoScaleSettings
La risorsa autoscaleSettings usa le informazioni raccolte dall'estensione di Diagnostica per prendere decisioni sull'aumento o la riduzione del numero di macchine virtuali nel set di scalabilità.

Questo esempio illustra la configurazione della risorsa nel modello:

```json
{
  "type": "Microsoft.Insights/autoscaleSettings",
  "apiVersion": "2015-04-01",
  "name": "[concat(parameters('resourcePrefix'),'as1')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
  ],
  "properties": {
    "enabled": true,
    "name": "[concat(parameters('resourcePrefix'),'as1')]",
    "profiles": [
      {
        "name": "Profile1",
        "capacity": {
          "minimum": "1",
          "maximum": "10",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "\\Processor(_Total)\\Thread Count",
              "metricNamespace": "",
              "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT5M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 650
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
          {
            "metricTrigger": {
              "metricName": "\\Processor(_Total)\\Thread Count",
              "metricNamespace": "",
              "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT5M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 550
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ],
    "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
  }
}
```

Nell'esempio precedente vengono create due regole per definire le azioni di ridimensionamento automatico. La prima regola definisce l'azione di aumento delle istanze e la seconda regola definisce l'azione di riduzione del numero di istanze. Questi valori vengono forniti nelle regole:

| Regola | Descrizione |
| ---- | ----------- |
| metricName        | Questo valore corrisponde al contatore delle prestazioni definito nella variabile wadperfcounter per l'estensione Diagnostica. Nell'esempio precedente viene usato il contatore Conteggio dei thread.    |
| metricResourceUri | Questo valore è l'identificatore di risorsa del set di scalabilità di macchine virtuali. Questo identificatore contiene il nome del gruppo di risorse, il nome del provider di risorse e il nome del set di scalabilità per il ridimensionamento. |
| timeGrain         | Questo valore corrisponde alla granularità delle metriche che vengono raccolte. Nell'esempio precedente i dati vengono raccolti con un intervallo di un minuto. Questo valore viene usato con timeWindow. |
| statistic         | Questo valore determina il modo in cui vengono combinate le metriche per consentire l'azione di ridimensionamento automatico. I valori possibili sono: Average, Min, Max. |
| timeWindow        | Questo valore rappresenta l'intervallo di tempo in cui vengono raccolti i dati dell'istanza. Deve essere compreso tra 5 minuti e 12 ore. |
| timeAggregation   | Questo valore definisce il modo in cui i dati raccolti devono essere combinati nel tempo. Il valore predefinito è "Average". I valori possibili sono: Average, Minimum, Maximum, Last, Total, Count. |
| operator          | Questo valore indica l'operatore usato per confrontare i dati della metrica e la soglia. I valori possibili sono: Equals, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual. |
| threshold         | Questo valore indica l'attivazione dell'azione di ridimensionamento. Assicurarsi di fornire una differenza sufficiente tra i valori delle soglie per le azioni di **aumento delle istanze** e di **riduzione delle istanze**. Se si impostano gli stessi valori per entrambe le azioni, il sistema prevede un'evoluzione costante che impedisce l'implementazione di un'azione di ridimensionamento. Ad esempio, impostando entrambi i valori su 600 thread, l'esempio precedente non funziona. |
| direction         | Questo valore determina l'azione da eseguire quando viene raggiunto il valore di soglia. I valori possibili sono Increase o Decrease. |
| type              | Questo valore indica il tipo di azione che deve verificarsi e deve essere impostato su ChangeCount. |
| value             | Questo valore indica il numero di macchine virtuali che vengono aggiunte o rimosse dal set di scalabilità. Questo valore deve essere uguale o maggiore di 1. |
| cooldown          | Questo valore indica il tempo di attesa dopo l'ultima azione di ridimensionamento prima che venga eseguita l'azione successiva. Questo valore deve essere compreso tra un minuto e una settimana. |

A seconda del contatore delle prestazioni scelto, alcuni elementi della configurazione del modello vengono usati in modo diverso. Nell'esempio precedente, il contatore delle prestazioni è Conteggio dei thread e il valore di soglia è impostato su 650 per un'azione di aumento delle istanze e su 550 per l'azione di riduzione delle istanze. Se si usa un contatore come%Processor Time, il valore di soglia è impostato sulla percentuale di utilizzo della CPU che determina un'azione di ridimensionamento.

Quando viene attivata un'azione di ridimensionamento, ad esempio un carico elevato, la capacità del set viene aumentata in base al valore presente nel modello. Ad esempio, in un set di scalabilità in cui la capacità è impostata su 3 e il valore dell'azione di ridimensionamento è impostato su 1:

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

Quando viene il carico corrente causa il superamento della soglia di 650 del conteggio medio dei thread:

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

Viene attivata un'azione di **aumento delle istanze** a causa della quale la capacità del set viene aumentata di uno:

```json
"sku": {
  "name": "Standard_A0",
  "tier": "Standard",
  "capacity": 4
},
```

Come conseguenza una macchina virtuale viene aggiunta al set di scalabilità:

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

Dopo un periodo di attesa di cinque minuti, se il numero medio di thread nelle macchine virtuali rimane oltre 600, viene aggiunta un'altra macchina virtuale al set. Se il numero medio di thread rimane sotto 550, la capacità del set di scalabilità viene ridotta di uno e una macchina virtuale viene rimossa dal set.

## <a name="set-up-scaling-using-azure-powershell"></a>Impostare la scalabilità con Azure PowerShell

Per visualizzare esempi d'uso di PowerShell per configurare il ridimensionamento automatico, vedere [Azure Monitor PowerShell quick start samples](../monitoring-and-diagnostics/insights-powershell-samples.md) (Esempi di avvio rapido di PowerShell per Monitoraggio di Azure).

## <a name="set-up-scaling-using-azure-cli"></a>Impostare la scalabilità con l'interfaccia della riga di comando di Azure

Per visualizzare esempi d'uso dell'interfaccia della riga di comando di Azure per configurare il ridimensionamento automatico, vedere [Azure Monitor Cross-platform CLI quick start samples](../monitoring-and-diagnostics/insights-cli-samples.md) (Esempi di avvio rapido dell'interfaccia della riga di comando multipiattaforma per Monitoraggio di Azure).

## <a name="set-up-scaling-using-the-azure-portal"></a>Impostare il ridimensionamento con il portale di Azure

Per visualizzare un esempio d'uso del portale di Azure per impostare il ridimensionamento automatico, vedere [Creare un set di scalabilità di macchine virtuali tramite il portale di Azure](virtual-machine-scale-sets-portal-create.md).

## <a name="investigate-scaling-actions"></a>Analisi delle azioni di ridimensionamento

* **Portale di Azure**  
Attualmente è possibile ottenere una quantità limitata di informazioni tramite il portale.

* **Esplora risorse di Azure**  
Si tratta dello strumento migliore per esaminare lo stato corrente del set di scalabilità. Seguire questo percorso per passare alla visualizzazione dell'istanza del set di scalabilità creato:  
**Subscriptions > {sottoscrizione dell'utente} > resourceGroups > {gruppo di risorse dell'utente} > providers > Microsoft.Compute > virtualMachineScaleSets > {set di scalabilità dell'utente} > virtualMachines**

* **Azure PowerShell**  
Usare questo comando per ottenere alcune informazioni:

  ```powershell
  Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15
  Get-Autoscalesetting -ResourceGroup rainvmss -DetailedOutput
  ```

* Connettersi alla macchina virtuale jumpbox esattamente come a qualsiasi altra macchina virtuale e quindi accedere in modalità remota alle macchine virtuali nel set di scalabilità per monitorare i singoli processi.

## <a name="next-steps"></a>Passaggi successivi
* Per un esempio sulla modalità di creazione di un set di scalabilità configurando il ridimensionamento automatico, vedere [Ridimensionare automaticamente le macchine virtuali in un set di scalabilità di macchine virtuali](virtual-machine-scale-sets-windows-autoscale.md) .

* Per alcuni esempi delle funzionalità di monitoraggio in Monitoraggio di Azure, vedere [Azure Monitor PowerShell quick start samples](../monitoring-and-diagnostics/insights-powershell-samples.md) (Esempi di avvio rapido di PowerShell per Monitoraggio di Azure).

* Per informazioni sulle funzionalità di notifica, vedere [Use autoscale actions to send email and webhook alert notifications in Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md) (Usare le azioni di ridimensionamento automatico per inviare notifiche di avviso di webhook ed e-mail in Monitoraggio di Azure).

* Per informazioni, vedere [Use audit logs to send email and webhook alert notifications in Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md) (Usare i log di controllo per inviare notifiche di avviso di webhook in Monitoraggio di Azure).

* Informazioni sugli [scenari di scalabilità automatica avanzata](virtual-machine-scale-sets-advanced-autoscale.md).


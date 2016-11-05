---
title: Ridimensionamento automatico e set di scalabilità di macchine virtuali | Microsoft Docs
description: Informazioni sull'uso della diagnostica e delle risorse di scalabilità automatica per ridimensionare automaticamente le macchine virtuali in un set di scalabilità.
services: virtual-machine-scale-sets
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: davidmu

---
# <a name="automatic-scaling-and-virtual-machine-scale-sets"></a>Ridimensionamento automatico e set di scalabilità di macchine virtuali
Il ridimensionamento automatico delle macchine virtuali in un set di scalabilità consiste nella creazione o nell'eliminazione di macchine nel set in base alle esigenze per soddisfare i requisiti a livello di prestazioni. Man mano che aumenta il volume di lavoro, è possibile che un'applicazione richieda risorse aggiuntive per poter eseguire le attività in modo efficace.

Il ridimensionamento automatico è un processo automatico che semplifica il sovraccarico di gestione. Riducendo il sovraccarico, non è necessario monitorare le prestazioni del sistema continuamente o decidere come gestire le risorse. Il ridimensionamento è un processo elastico. È possibile aggiungere ulteriori risorse con l'aumento del carico, tuttavia man mano che la richiesta diminuisce le risorse possono essere rimosse per ridurre al minimo i costi e mantenere i livelli di prestazioni.

Configurare il ridimensionamento automatico di un set di scalabilità tramite un modello di Azure Resource Manager, usando Azure PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="set-up-scaling-by-using-resource-manager-templates"></a>Configurare il ridimensionamento usando modelli di Resource Manager
Invece di distribuire e gestire separatamente ogni risorsa dell'applicazione, usare un modello che distribuisce tutte le risorse in un'unica operazione coordinata. Nel modello vengono definite le risorse dell'applicazione e vengono specificati i parametri di distribuzione per diversi ambienti. Il modello è composto da JSON ed espressioni che è possibile usare per creare valori per la distribuzione. Per altre informazioni, vedere [Creazione di modelli di Azure Resource Manager](../resource-group-authoring-templates.md).

Nel modello si specifica l'elemento capacità:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

La capacità identifica il numero di macchine virtuali nel set. È possibile modificare manualmente la capacità distribuendo un modello con un valore diverso. Se si distribuisce un modello per modificare solo la capacità, è possibile includere solo l'elemento SKU con la capacità aggiornata.

Modificare automaticamente la capacità del set di scalabilità impostata usando la combinazione della risorsa autoscaleSettings e dell'estensione di Diagnostica.

### <a name="configure-the-azure-diagnostics-extension"></a>Configurare l'estensione Diagnostica di Azure
Il ridimensionamento automatico può essere eseguito solo se la raccolta di metriche è riuscita in ogni macchina virtuale del set di scalabilità. L'estensione Diagnostica di Azure fornisce le funzionalità di monitoraggio e diagnostica che soddisfano le esigenze di raccolta delle metriche della risorsa di ridimensionamento automatico. È possibile installare l'estensione come parte del modello di Resource Manager.

Questo esempio mostra le variabili usate nel modello per configurare l'estensione di diagnostica:

    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
    "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Thread Count\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

I parametri vengono forniti quando si distribuisce il modello. In questo esempio, vengono forniti il nome dell'account di archiviazione in cui vengono archiviati i dati e il nome del set di scalabilità da cui vengono raccolti i dati. Anche in questo esempio di Windows Server vengono raccolti solo i dati del contatore delle prestazioni Conteggio dei thread. Tutti i contatori delle prestazioni disponibili in Windows o Linux possono essere usati per raccogliere informazioni di diagnostica e possono essere inclusi nella configurazione dell'estensione.

Questo esempio illustra la definizione dell'estensione nel modello:

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

Quando viene eseguita l'estensione della diagnostica, i dati vengono raccolti in una tabella inclusa nell'account di archiviazione specificato. I dati raccolti sono disponibili nella tabella WADPerformanceCounters:

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### <a name="configure-the-autoscalesettings-resource"></a>Configurare la risorsa autoScaleSettings
La risorsa autoscaleSettings usa le informazioni raccolte dall'estensione di Diagnostica per prendere decisioni sull'aumento o la riduzione del numero di macchine virtuali nel set di scalabilità.

Questo esempio illustra la configurazione della risorsa nel modello:

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
                  "metricName": "\\Process(_Total)\\Thread Count",
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
                  "metricName": "\\Process(_Total)\\Thread Count",
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

Nell'esempio precedente vengono create due regole per definire le azioni di ridimensionamento automatico. La prima regola definisce l'azione di aumento delle istanze e la seconda regola definisce l'azione di riduzione del numero di istanze. Questi valori vengono forniti nelle regole:

* **metricName** : questo valore corrisponde al contatore delle prestazioni definito nella variabile wadperfcounter per l'estensione di Diagnostica. Nell'esempio precedente viene usato il contatore Conteggio dei thread.  
* **metricResourceUri** : questo valore è l'identificatore di risorsa del set di scalabilità di macchine virtuali. Questo identificatore contiene il nome del gruppo di risorse, il nome del provider di risorse e il nome del set di scalabilità per il ridimensionamento.
* **timeGrain** : questo valore corrisponde alla granularità delle metriche che vengono raccolte. Nell'esempio precedente i dati vengono raccolti con un intervallo di un minuto. Questo valore viene usato con timeWindow.
* **statistic** : questo valore determina il modo in cui vengono combinate le metriche per consentire l'azione di ridimensionamento automatico. I valori possibili sono: Average, Min, Max.
* **timeWindow** : questo valore è l'intervallo di tempo in cui vengono raccolti i dati dell'istanza. Deve essere compreso tra 5 minuti e 12 ore.
* **timeAggregation** : questo valore definisce il modo in cui i dati raccolti devono essere combinati nel tempo. Il valore predefinito è "Average". I valori possibili sono: Average, Minimum, Maximum, Last, Total, Count.
* **operator** : questo valore indica l'operatore usato per confrontare i dati della metrica e la soglia. I valori possibili sono: Equals, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
* **threshold** : questo valore indica l'attivazione dell'azione di ridimensionamento. Assicurarsi di fornire una differenza sufficiente tra la soglia impostata per l'azione di aumento delle istanze e la soglia impostata per l'azione di riduzione delle istanze. Se si impostano i valori in modo che corrispondano, il sistema prevede un'evoluzione costante che impedisce l'implementazione di un'azione di ridimensionamento. Ad esempio, impostando entrambi i valori su 600 thread, l'esempio precedente non funziona.
* **direction** : questo valore determina l'azione da eseguire quando viene raggiunto il valore di soglia. I valori possibili sono Increase o Decrease.
* **type** : questo valore indica il tipo di azione che deve verificarsi e deve essere impostato su ChangeCount.
* **value** : questo valore indica il numero di macchine virtuali che vengono aggiunte o rimosse dal set di scalabilità. Questo valore deve essere uguale o maggiore di 1.
* **cooldown** : questo valore indica il tempo di attesa dopo l'ultima azione di ridimensionamento prima che venga eseguita l'azione successiva. Questo valore deve essere compreso tra un minuto e una settimana.

A seconda del contatore delle prestazioni scelto, alcuni elementi della configurazione del modello vengono usati in modo diverso. Nell'esempio precedente, il contatore delle prestazioni è Conteggio dei thread e il valore di soglia è impostato su 650 per un'azione di aumento delle istanze e su 550 per l'azione di riduzione delle istanze. Se si usa un contatore come%Processor Time, il valore di soglia è impostato sulla percentuale di utilizzo della CPU che determina un'azione di ridimensionamento.

Quando viene creato un carico nelle macchine virtuali che attiva un'azione di ridimensionamento, la capacità del set viene aumentata in base al valore presente nel modello. Ad esempio, in un set di scalabilità in cui la capacità è impostata su 3 e il valore dell'azione di ridimensionamento è impostato su 1:

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

Quando viene creato il carico che causa il superamento della soglia di 650 del conteggio medio dei thread:

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

Viene attivata un'azione di aumento delle istanze a causa della quale la capacità del set viene aumentata di uno:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

Inoltre, una macchina virtuale viene aggiunta al set di scalabilità:

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

Dopo un periodo di attesa di cinque minuti, se il numero medio di thread nelle macchine virtuali rimane oltre 600, viene aggiunta un'altra macchina virtuale al set. Se il numero medio di thread rimane sotto 550, la capacità del set di scalabilità viene ridotta di uno e una macchina virtuale viene rimossa dal set.

## <a name="set-up-scaling-using-azure-powershell"></a>Impostare la scalabilità con Azure PowerShell
Per visualizzare esempi d'uso di PowerShell per configurare la scalabilità automatica, vedere [Esempi di avvio rapido con PowerShell per Azure Insights](../monitoring-and-diagnostics/insights-powershell-samples.md).

## <a name="set-up-scaling-using-azure-cli"></a>Impostare la scalabilità con l'interfaccia della riga di comando di Azure
Per visualizzare esempi d'uso dell'interfaccia della riga di comando di Azure per configurare il ridimensionamento automatico, vedere [Esempi di avvio rapido dell'interfaccia della riga di comando multipiattaforma di Azure Insights](../monitoring-and-diagnostics/insights-cli-samples.md).

## <a name="investigate-scaling-actions"></a>Analisi delle azioni di ridimensionamento
* [Portale di Azure]() : attualmente è possibile ottenere una quantità limitata di informazioni tramite il portale.
* [Esplora risorse di Azure]() : si tratta dello strumento migliore per esaminare lo stato corrente del set di scalabilità. Seguire questo percorso per visualizzare l'istanza del set di scalabilità creato: sottoscrizioni > {sottoscrizione} > resourceGroups > {gruppo di risorse} > provider > Microsoft.Compute > virtualMachineScaleSets > {set di scalabilità} > virtualMachines
* Azure PowerShell: per ottenere alcune informazioni, usare il comando seguente:
  
        Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15
        Get-Autoscalesetting -ResourceGroup rainvmss -DetailedOutput
* Connettersi alla macchina virtuale jumpbox esattamente come a qualsiasi altra macchina virtuale e quindi accedere in modalità remota alle macchine virtuali nel set di scalabilità per monitorare i singoli processi.

## <a name="next-steps"></a>Passaggi successivi
* Per un esempio sulla modalità di creazione di un set di scalabilità configurando il ridimensionamento automatico, vedere [Ridimensionare automaticamente le macchine virtuali in un set di scalabilità di macchine virtuali](virtual-machine-scale-sets-windows-autoscale.md) .
* Per alcuni esempi delle funzionalità di monitoraggio di Azure Insights, vedere [Esempi di avvio rapido con PowerShell per Azure Insights](../monitoring-and-diagnostics/insights-powershell-samples.md)
* Per informazioni sulle funzionalità di notifica, vedere [Usare le azioni di scalabilità automatica per inviare notifiche di avviso di webhook e posta elettronica in Azure Insights](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md).
* Per informazioni, vedere [Usare i log di controllo per inviare notifiche di avviso di webhook e posta elettronica in Azure Insights](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
* Informazioni sugli [scenari di scalabilità automatica avanzata](virtual-machine-scale-sets-advanced-autoscale.md).

<!--HONumber=Oct16_HO2-->



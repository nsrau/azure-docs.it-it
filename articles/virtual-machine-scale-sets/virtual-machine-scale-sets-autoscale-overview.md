<properties
	pageTitle="Ridimensionamento automatico e set di scalabilità di macchine virtuali | Microsoft Azure"
	description="Informazioni sull'uso della diagnostica e delle risorse di ridimensionamento automatico per ridimensionare le macchine virtuali in un set di scalabilità."
	services="virtual-machine-scale-sets"
    documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# Ridimensionamento automatico e set di scalabilità di macchine virtuali

Il ridimensionamento automatico delle macchine virtuali in un set di scalabilità consiste nella creazione o nell'eliminazione di macchine nel set in base alle esigenze di un'applicazione per soddisfare i requisiti a livello di prestazioni e dei contratti di servizio. Man mano che aumenta il volume di lavoro, è possibile che un'applicazione richieda risorse aggiuntive per poter eseguire le attività in modo efficace.

Il ridimensionamento automatico è un processo automatico che semplifica il sovraccarico di gestione tramite attività di monitoraggio continuo delle prestazioni del sistema, nonché tramite l'assunzione di decisioni sull'aggiunta o la rimozione di risorse non necessarie. Il ridimensionamento è un processo elastico. Rende possibile il provisioning di più risorse con l'aumento del carico del sistema, ma con la riduzione della domanda consente di deallocare le risorse per ridurre i costi, mantenendo comunque un livello di prestazioni adeguato, e soddisfare i contratti di servizio.

Configurare il ridimensionamento automatico di un set di scalabilità tramite un modello di Azure Resource Manager, usando Azure PowerShell o l'interfaccia della riga di comando di Azure.

## Configurare il ridimensionamento usando modelli di Resource Manager

Invece di distribuire e gestire separatamente ogni risorsa dell'applicazione, usare un modello che distribuisce ed effettua il provisioning di tutte le risorse in un'unica operazione coordinata. Nel modello vengono definite le risorse dell'applicazione e vengono specificati i parametri di distribuzione per diversi ambienti. Il modello è composto da JSON ed espressioni che è possibile usare per creare valori per la distribuzione. Per altre informazioni, vedere [Creazione di modelli di Azure Resource Manager](../resource-group-authoring-templates.md)

Nel modello si specifica l'elemento capacità:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

La capacità identifica il numero di macchine virtuali nel set. È possibile modificare manualmente la capacità distribuendo un modello con un valore diverso. Se si distribuisce un modello per modificare solo la capacità, includere solo l'elemento SKU con la capacità aggiornata.

Modificare automaticamente la capacità del set di scalabilità usando una combinazione della risorsa autoscaleSettings fornita da Azure Resource Manager e l'estensione Diagnostica di Azure installata nelle macchine virtuali del set di scalabilità.

### Configurare l'estensione Diagnostica di Azure

Il ridimensionamento automatico può essere eseguito solo se la raccolta di metriche è riuscita in ogni macchina virtuale del set di scalabilità. L'estensione Diagnostica di Azure fornisce le funzionalità di monitoraggio e diagnostica che soddisfano le esigenze di raccolta delle metriche della risorsa di ridimensionamento automatico. È possibile installare l'estensione come parte del modello di Resource Manager. Per altre informazioni sull'uso dell'estensione, vedere [Creare una macchina virtuale Windows con monitoraggio e diagnostica mediante i modelli di Azure Resource Manager](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).

Questo esempio mostra le variabili usate nel modello per configurare l'estensione di diagnostica:

	"diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
	"accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
	"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB="4096" xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/> <WindowsEventLog scheduledTransferPeriod="PT1M" > <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="Security!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="System!*[System[(Level = 1 or Level = 2)]]" /></WindowsEventLog>",
	"wadperfcounter": "<PerformanceCounters scheduledTransferPeriod="PT1M"><PerformanceCounterConfiguration counterSpecifier="\\Processor(_Total)\\Thread Count" sampleRate="PT15S" unit="Percent"><annotation displayName="Thread Count" locale="it-IT"/></PerformanceCounterConfiguration>",
	"wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId="')]",
	"wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
	"wadcfgxend": "[concat('"><MetricAggregation scheduledTransferPeriod="PT1H"/><MetricAggregation scheduledTransferPeriod="PT1M"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

Quando si distribuisce il modello, vengono forniti i parametri per valori come il nome dell'account di archiviazione in cui vengono archiviati i dati e il nome del set di scalabilità da cui vengono raccolti i dati. In questo esempio di Windows Server vengono raccolti solo i dati del contatore delle prestazioni Conteggio dei thread, ma tutti i contatori delle prestazioni disponibili in Windows o Linux possono essere usati per raccogliere informazioni di diagnostica e possono essere inclusi nella configurazione dell'estensione.

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

Quando viene eseguita l'estensione della diagnostica, i dati vengono raccolti in una tabella inclusa nell'account di archiviazione specificato. I dati raccolti sono disponibili nella tabella WADPerformanceCounters. Ad esempio, questo è il conteggio dei thread raccolti quando sono state create le macchine virtuali in un set di scalabilità:

![Screenshot dei dati del contatore delle prestazioni di Windows Server prima dell'aggiunta del carico](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### Configurare la risorsa autoScaleSettings

La risorsa autoscaleSettings usa le informazioni raccolte dall'estensione di diagnostica per prendere decisioni sull'aumento o la riduzione del numero di macchine virtuali nel set di scalabilità.

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

- **metricName**: corrisponde al contatore delle prestazioni definito nella variabile wadperfcounter per l'estensione di diagnostica. Nell'esempio precedente viene usato il contatore Conteggio dei thread.  
- **metricResourceUri**: si tratta dell'identificatore di risorsa del set di scalabilità di macchine virtuali. Questo identificatore contiene il nome del gruppo di risorse, il nome del provider di risorse e il nome del set di scalabilità per il ridimensionamento.
- **timeGrain**: corrisponde alla granularità delle metriche che vengono raccolte. Nell'esempio precedente i dati vengono raccolti con un intervallo di un minuto. Questo valore viene usato in combinazione con timeWindow.
- **statistic**: determina il modo in cui vengono combinate le metriche per consentire l'azione di ridimensionamento automatico. I valori possibili sono: Average, Min, Max.
- **timeWindow**: si tratta dell'intervallo di tempo in cui vengono raccolti i dati dell'istanza. Deve essere compreso tra 5 minuti e 12 ore.
- **timeAggregation**: definisce il modo in cui i dati raccolti devono essere combinati nel tempo. Il valore predefinito è "Average". I valori possibili sono: Average, Minimum, Maximum, Last, Total, Count.
- **operator**: operatore usato per confrontare i dati della metrica e la soglia. I valori possibili sono: Equals, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
- **threshold**: indica il valore che attiva l'azione di ridimensionamento. È necessario assicurarsi di fornire una differenza sufficiente tra la soglia impostata per l'azione aumento delle istanze e la soglia impostata per l'azione di riduzione delle istanze. Se si impostano gli stessi valori, ad esempio entrambi su 600 thread nell'esempio precedente, il sistema prevede un aumento e una riduzione costanti delle dimensioni del set e non implementa un'azione di ridimensionamento.
- **direction**: determina l'azione da eseguire quando viene raggiunto il valore di soglia. I valori possibili sono Increase o Decrease.
- **type**: indica il tipo di azione che deve verificarsi e deve essere impostato su ChangeCount.
- **value**: indica il numero di macchine virtuali che vengono aggiunte o rimosse nel set di scalabilità. Questo valore deve essere uguale o maggiore di 1.
- **cooldown**: indica il tempo di attesa dopo l'ultima azione di ridimensionamento prima che venga eseguita l'azione successiva. Deve essere compreso tra un minuto e una settimana.

A seconda del contatore delle prestazioni scelto, alcuni elementi della configurazione del modello vengono usati in modo diverso. Nell'esempio illustrato il contatore delle prestazioni usato è Conteggio dei thread e il valore di soglia è impostato su 650 per un'azione di aumento delle istanze e su 550 per l'azione di riduzione delle istanze. Se si usa un contatore come % tempo processore, il valore di soglia sarà impostato sulla percentuale di utilizzo della CPU che determinerà un'azione di ridimensionamento.

Quando viene creato un carico nelle macchine virtuali nel set che attiva un'azione di ridimensionamento, il numero di macchine virtuali nel set viene aumentato in base all'elemento valore presente nel modello. Ad esempio, in un set di scalabilità in cui la capacità è impostata su 3 e il valore dell'azione di ridimensionamento è impostato su 1:

![Screenshot del numero di macchine virtuali in un set prima del ridimensionamento automatico](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

Quando viene creato il carico che causa il superamento della soglia di 650 del conteggio medio dei thread:

![Screenshot dei dati del contatore delle prestazioni di Windows Server dopo l'aggiunta del carico](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

Viene attivata un'azione di aumento delle istanze a causa della quale la capacità del set viene aumentata di uno:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

Inoltre, una macchina virtuale viene aggiunta al set di scalabilità:

![Screenshot del numero di macchine virtuali in un set dopo il ridimensionamento automatico](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

Dopo un periodo di attesa di cinque minuti, se il numero medio di thread nelle macchine virtuali rimane oltre 600, viene aggiunta un'altra macchina virtuale al set. Se il numero medio di thread rimane sotto 550, la capacità del set di scalabilità viene ridotta di uno e una macchina virtuale viene rimossa dal set.

## Analisi delle azioni di ridimensionamento

- [Portale di Azure](https://portal.azure.com/): attualmente è possibile ottenere una quantità limitata di informazioni tramite il portale.
- [Esplora risorse di Azure](https://resources.azure.com/): si tratta dello strumento migliore per esplorare lo stato corrente del set di scalabilità.
- [Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/): si possono usare cmdlet come**Get-AzureRmResource** o **Get-Autoscalesetting** per ottenere informazioni sui set di scalabilità.
- [Interfaccia della riga di comando di Azure](../xplat-cli-azure-resource-manager.md): usare il comando **azure resource show** per ottenere informazioni sul set.
- Connettersi alla macchina virtuale jumpbox esattamente come a qualsiasi altra macchina virtuale e quindi accedere in modalità remota alle macchine virtuali nel set di scalabilità per monitorare i singoli processi.

## Passaggi successivi

1. Introduzione alla creazione del primo set di scalabilità usando le informazioni disponibili nell'articolo che illustra come [creare un set di scalabilità di macchine virtuali Windows](virtual-machine-scale-sets-windows-create.md).
2. Per un esempio di come creare un set di scalabilità configurando il ridimensionamento automatico, vedere l'articolo che illustra come [ridimensionare automaticamente set di scalabilità di macchine virtuali Windows](virtual-machine-scale-sets-windows-autoscale.md) o [ridimensionare automaticamente set di scalabilità di macchine virtuali Linux](virtual-machine-scale-sets-linux-autoscale.md).

<!---HONumber=AcomDC_0525_2016-->
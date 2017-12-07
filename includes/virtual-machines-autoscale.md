È possibile [ridimensionare automaticamente](../articles/monitoring-and-diagnostics/insights-autoscale-best-practices.md) le [macchine virtuali (VM)](../articles/virtual-machines/windows/overview.md) con facilità usando [set di scalabilità di macchine virtuali](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) e la [funzionalità di ridimensionamento automatico di Monitoraggio di Azure](../articles/monitoring-and-diagnostics/monitoring-overview-autoscale.md). Per poter essere ridimensionate automaticamente, le VM devono essere membri di un set di scalabilità. Questo articolo contiene informazioni che consentono di comprendere meglio come ridimensionare sia orizzontalmente che verticalmente le VM usando metodi automatici e manuali.

## <a name="horizontal-or-vertical-scaling"></a>Ridimensionamento orizzontale o verticale

La funzionalità di ridimensionamento automatico di Monitoraggio di Azure supporta solo il ridimensionamento orizzontale, ossia un aumento o una riduzione del numero di istanze di VM. Il ridimensionamento orizzontale è più flessibile in una situazione cloud, perché consente di eseguire potenzialmente migliaia di VM per gestire il carico. Per ottenere il ridimensionamento orizzontale si modifica automaticamente o manualmente la capacità (ovvero il numero di istanze) del set di scalabilità. 

Il ridimensionamento verticale mantiene lo stesso numero di VM, ma le rende più o meno potenti. La potenza è misurata in termini di attributi come memoria, velocità di CPU o spazio su disco. Il ridimensionamento verticale dipende dalla disponibilità di hardware di maggiori dimensioni, che può raggiungere rapidamente il limite massimo e variare in base all'area. La scalabilità verticale richiede in genere l'arresto e il riavvio di una macchina virtuale. Per ottenere il ridimensionamento verticale si impostano nuove dimensioni nella configurazione delle VM del set di scalabilità.

Usando runbook in [Automazione di Azure](../articles/automation/automation-intro.md), è possibile [aumentare o ridurre facilmente le prestazioni delle VM di un set di scalabilità](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision.md).

## <a name="create-a-virtual-machine-scale-set"></a>Creare un set di scalabilità di macchine virtuali

I set di scalabilità semplificano la distribuzione e la gestione di VM identiche come un set. È possibile creare set di scalabilità Linux o Windows usando il [portale di Azure](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md), [Azure PowerShell](../articles/virtual-machines/windows/tutorial-create-vmss.md) o l'[interfaccia della riga di comando di Azure](../articles/virtual-machines/linux/tutorial-create-vmss.md). È anche possibile creare e gestire i set di scalabilità con SDK, ad esempio [Python](/develop/python) o [Node.js](/nodejs/azure), o direttamente con le [API REST](/rest/api/compute/virtualmachinescalesets). Il ridimensionamento automatico delle VM viene eseguito applicando metriche e regole al set di scalabilità.

## <a name="configure-autoscale-for-a-scale-set"></a>Configurare il ridimensionamento automatico per un set di scalabilità

Il ridimensionamento automatico garantisce il numero appropriato di VM per gestire il carico dell'applicazione. Consente di aggiungere VM per gestire gli incrementi di carico e di risparmiare denaro rimuovendo le VM inattive. È possibile specificare un numero minimo e massimo di VM da eseguire in base a un set di regole. La definizione di un minimo assicura che l'applicazione sia sempre in esecuzione anche in assenza di carico. La definizione di un valore massimo limita il costo orario totale possibile.

Il ridimensionamento automatico può essere abilitato quando si crea il set di scalabilità con [Azure PowerShell](../articles/monitoring-and-diagnostics/insights-powershell-samples.md#create-and-manage-autoscale-settings) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/monitor/autoscale-settings), nonché dopo la creazione del set di scalabilità. È possibile creare un set di scalabilità, installare l'estensione e configurare il ridimensionamento automatico usando un [modello di Azure Resource Manager](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md). Nel portale di Azure abilitare il ridimensionamento automatico da Monitoraggio di Azure oppure dalle impostazioni del set di scalabilità.

![Abilitare il ridimensionamento automatico](./media/virtual-machines-autoscale/virtual-machines-autoscale-enable.png)
 
### <a name="metrics"></a>Metrica

La funzionalità di ridimensionamento automatico di Monitoraggio di Azure consente di aumentare o ridurre le prestazioni delle VM in esecuzione in base a [metriche](../articles/monitoring-and-diagnostics/insights-autoscale-common-metrics.md). Per impostazione predefinita, le VM offrono metriche a livello di host di base per l'utilizzo di CPU, rete e dischi. Quando si configura la raccolta di dati di diagnostica usando l'estensione di diagnostica, diventano disponibili altri contatori delle prestazioni del sistema operativo guest relativi a disco, CPU e memoria.

![Criteri delle metriche](./media/virtual-machines-autoscale/virtual-machines-autoscale-criteria.png)

Se il ridimensionamento per l'applicazione deve essere basato su metriche non disponibili tramite l'host, è necessario che nelle VM del set di scalabilità sia installata l'[estensione di diagnostica per Linux](../articles/virtual-machines/linux/diagnostic-extension.md) o l'[estensione di diagnostica per Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md). Se si crea un set di scalabilità con il portale di Azure, si deve usare anche Azure PowerShell o l'interfaccia della riga di comando di Azure per installare l'estensione con la configurazione di diagnostica necessaria.
 
### <a name="rules"></a>Regole

Le [regole](../articles/monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) combinano una metrica con un'azione da eseguire. Quando vengono soddisfatte le condizioni delle regole, vengono attivate una o più azioni di ridimensionamento automatico. Potrebbe ad esempio essere definita una regola che aumenta di 1 il numero di VM se l'utilizzo medio della CPU supera l'85%.

![Azioni di ridimensionamento automatico](./media/virtual-machines-autoscale/virtual-machines-autoscale-actions.png)
 
### <a name="notifications"></a>Notifiche

È possibile [configurare trigger](../articles/monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md) per chiamare URL Web specifici o inviare messaggi di posta elettronica in base alle regole di ridimensionamento automatico create. I webhook consentono di instradare le notifiche di avviso di Azure ad altri sistemi per la post-elaborazione o le notifiche personalizzate.

## <a name="manually-scale-vms-in-a-scale-set"></a>Ridimensionare manualmente le VM di un set di scalabilità

### <a name="horizontal"></a>Ridimensionamento orizzontale

È possibile aggiungere o rimuovere VM modificando la capacità del set di scalabilità. Nel portale di Azure si può ridurre o aumentare il numero di VM (riportato come **numero di istanze**) nel set di scalabilità spostando verso sinistra o destra la barra del dispositivo di scorrimento in Override condition (Condizione di override) nella schermata Ridimensionamento.

Usando Azure PowerShell, è necessario ottenere l'oggetto set di scalabilità con [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss). Si imposta quindi la proprietà **sku.capacity** sul numero di VM desiderato e si aggiorna il set di scalabilità con [Update-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss). Usando l'interfaccia della riga di comando di Azure, si modifica la capacità con il parametro **--new-capacity** del comando [az vmss scale](https://docs.microsoft.com/cli/azure/vmss#az_vmss_scale).

### <a name="vertical"></a>Vertical

È possibile modificare manualmente le dimensioni delle VM nel portale di Azure nella schermata Dimensioni relativa al set di scalabilità. È possibile usare Azure PowerShell con Get-AzureRmVmss, impostando la proprietà dello SKU di riferimento dell'immagine, e quindi usare [Update-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) e [Update-AzureRmVmssInstance](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui set di scalabilità, vedere [Considerazioni sulla progettazione per i set di scalabilità](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md).


---
title: "Scalare automaticamente i set di scalabilità di macchine virtuali con Azure PowerShell | Microsoft Docs"
description: "Come creare regole di scalabilità automatica per i set di scalabilità di macchine virtuali con Azure PowerShell"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 8928e56f353858234db314714d411a9c2990eb4e
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Scalare automaticamente un set di scalabilità di una macchina virtuale con Azure PowerShell
Quando si crea un set di scalabilità, definire il numero di istanze di macchine virtuali da eseguire. È possibile aumentare o ridurre automaticamente il numero di istanze di macchine virtuali in base alle richieste dell'applicazione. La scalabilità automatica consente di adattarsi alle esigenze dei clienti o di rispondere alle prestazioni dell'applicazione durante il ciclo di vita dell'app.

Questo articolo illustra come creare regole di scalabilità automatica con Azure PowerShell per monitorare le prestazioni delle istanze di macchine virtuali nel set di scalabilità. Queste regole di scalabilità automatica aumentano o diminuiscono il numero di istanze di macchine virtuali in risposta a queste metriche delle prestazioni. È anche possibile completare questa procedura con l'[interfaccia della riga di comando di Azure 2.0](virtual-machine-scale-sets-autoscale-cli.md) o nel [portale di Azure](virtual-machine-scale-sets-autoscale-portal.md).


## <a name="prerequisites"></a>prerequisiti
Per creare regole di scalabilità automatica, è necessario un set di scalabilità di macchina virtuale esistente. È possibile creare un set di scalabilità con il [portale di Azure](virtual-machine-scale-sets-create-portal.md), [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) o l'[interfaccia della riga di comando di Azure 2.0](virtual-machine-scale-sets-create-cli.md).

Per rendere più semplice la creazione delle regole di scalabilità automatica, definire alcune variabili per il set di scalabilità. L'esempio seguente definisce le variabili per il set di scalabilità denominato *myScaleSet* nel gruppo di risorse denominato *myResourceGroup* nell'area *Stati Uniti orientali*. Ottenere l'ID della propria sottoscrizione con [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription). Se si hanno più sottoscrizioni associate all'account, viene restituita solo la prima sottoscrizione. Modificare i nomi e l'ID sottoscrizione nel modo seguente:

```powershell
$mySubscriptionId = (Get-AzureRmSubscription).Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```


## <a name="create-a-rule-to-automatically-scale-out"></a>Creare una regola per l'aumento automatico
Se aumenta la richiesta da parte dell'applicazione, aumenta il carico sulle istanze di macchine virtuali nel set di scalabilità. Se il carico aumenta in modo coerente e non momentaneamente, è possibile configurare regole di scalabilità automatica per aumentare il numero di istanze di macchine virtuali nel set di scalabilità. Quando sono state create le istanze di macchine virtuali e sono state distribuite le applicazioni, il set di scalabilità inizia a distribuire loro il traffico tramite il bilanciamento del carico. È possibile controllare le metriche da monitorare, ad esempio per la CPU o il disco, il tempo per cui il carico dell'applicazione deve soddisfare una determinata soglia e il numero di istanze di macchine virtuali da aggiungere al set di scalabilità.

È possibile creare una regola con [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) che aumenti il numero di istanze di macchine virtuali in un set di scalabilità quando il carico medio della CPU è superiore al 70% per un periodo di 5 minuti. Quando la regola viene attivata, il numero di istanze di macchine virtuali viene aumentato del 20%. Nei set di scalabilità con un numero minore di istanze di macchine virtuali, è possibile non specificare `-ScaleActionScaleType` e specificare solo `-ScaleActionValue` per aumentare di *1* o *2* istanze. Nei set di scalabilità con un numero elevato di istanze di macchine virtuali, potrebbe essere più appropriato un aumento del 10% o del 20% delle istanze di macchine virtuali.

Per questa regola vengono usati i parametri seguenti:

| Parametro               | Spiegazione                                                                                                         | Valore          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | La metrica delle prestazioni da monitorare e a cui applicare azioni dei set di scalabilità.                                                   | CPU percentuale |
| *-TimeGrain*            | La frequenza con cui le metriche vengono raccolte per l'analisi.                                                                   | 1 minuto       |
| *-MetricStatistic*      | Definisce la modalità di aggregazione delle metriche raccolte per l'analisi.                                                | Media        |
| *-TimeWindow*           | Il tempo monitorato prima che vengano confrontati i valori delle metriche e delle soglie.                                   | 10 minuti      |
| *-Operator*             | Operatore usato per confrontare i dati della metrica rispetto alla soglia.                                                     | Maggiore di   |
| *-Threshold*            | Il valore che determina l'attivazione di un'azione da parte della regola di scalabilità automatica.                                                      | 70%            |
| *-ScaleActionDirection* | Definisce se il set di scalabilità deve aumentare o diminuire quando si applica la regola.                                             | Aumento       |
| *–ScaleActionScaleType* | Indica che il numero di istanze di macchine virtuali deve essere modificato di un valore percentuale.                                 | Modifica percentuale |
| *-ScaleActionValue*     | La percentuale di istanze di macchine virtuali deve essere modificata quando viene attivata la regola.                                            | 20             |
| *-ScaleActionCooldown*  | Il tempo di attesa prima che la regola venga applicata nuovamente in modo che le azioni di scalabilità automatica diventino effettive. | 5 minuti      |

L'esempio seguente crea un oggetto denominato *myRuleScaleOut* che contiene questa regola di scalabilità. *-MetricResourceId* usa le variabili definite precedentemente per l'ID sottoscrizione, il nome del gruppo di risorse e il nome del set di scalabilità:

```powershell
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic Average `
  -TimeWindow 00:10:00 `
  -Operator GreaterThan `
  -Threshold 70 `
  -ScaleActionDirection Increase `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-automatically-scale-in"></a>Creare una regola per la riduzione automatica
Nelle ore serali o nel fine settimana è possibile che la richiesta delle applicazioni si riduca. Se il carico diminuisce in modo coerente nel tempo, è possibile configurare regole di scalabilità automatica per diminuire il numero di istanze di macchine virtuali nel set di scalabilità. Questa azione riduce i costi di esecuzione del set di scalabilità poiché si esegue solo il numero di istanze necessarie per soddisfare la richiesta corrente.

Creare una regola con [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) che diminuisca il numero di istanze di macchine virtuali in un set di scalabilità quando il carico medio della CPU scende sotto il 30% per un periodo di 10 minuti. Quando la regola viene attivata, il numero di istanze di macchine virtuali viene ridotto del 20%. L'esempio seguente crea un oggetto denominato *myRuleScaleDown* che contiene questa regola di scalabilità. *-MetricResourceId* usa le variabili definite precedentemente per l'ID sottoscrizione, il nome del gruppo di risorse e il nome del set di scalabilità:

```powershell
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:10:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20
```


## <a name="define-an-autoscale-profile"></a>Definire un profilo di scalabilità automatica
Per associare le regole di scalabilità automatica a un set di scalabilità, creare un profilo. Il profilo di scalabilità automatica definisce la capacità predefinita, minima e massima del set di scalabilità e associa le regole di scalabilità automatica. Creare un profilo di scalabilità automatica con [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile). L'esempio seguente imposta la capacità predefinita e minima di *2* istanze di macchine virtuali e la capacità massima di *10*. Le regole di aumento e riduzione create nei passaggi precedenti vengono quindi collegate:

```powershell
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Applicare le regole di scalabilità automatica a un set di scalabilità
Il passaggio finale consiste nell'applicare il profilo di scalabilità automatica al set di scalabilità. La scalabilità potrà quindi essere aumentata o diminuita automaticamente in base alla richiesta delle applicazioni. Applicare il profilo di scalabilità automatica con [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) come indicato di seguito:

```powershell
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitorare il numero di istanze in un set di scalabilità
Per visualizzare il numero e lo stato delle istanze di macchine virtuali, visualizzare un elenco di istanze del set di scalabilità con [Get-AzureRmVmssVM](/powershell/module/AzureRM.Compute/Get-AzureRmVmssVM). Lo stato indica se l'istanza della macchina virtuale esegue il provisioning quando il set di scalabilità viene aumentato automaticamente oppure esegue il deprovisioning quando il set di scalabilità viene ridotto automaticamente. L'esempio seguente visualizza lo stato dell'istanza della macchina virtuale per il set di scalabilità denominato *myScaleSet* nel gruppo di risorse denominato *myResourceGroup*:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="autoscale-based-on-a-schedule"></a>Ridimensionare automaticamente in base a una pianificazione
Negli esempi precedenti viene aumentato o ridotto automaticamente un set di scalabilità con le metriche host di base, ad esempio l'utilizzo della CPU. È possibile anche creare regole di scalabilità automatica in base a pianificazioni. Queste regole basate su pianificazione consentono di aumentare il numero di istanze di macchine virtuali in anticipo rispetto all'aumento previsto della richiesta delle applicazioni, ad esempio nelle ore di lavoro intenso, e quindi di ridurre automaticamente il numero di istanze quando si prevede una riduzione della richiesta, ad esempio nel fine settimana.

Per creare regole di scalabilità automatica basate su una pianificazione anziché su metriche host, usare il portale di Azure. Attualmente le regole basate su pianificazioni non possono essere create con Azure PowerShell.


## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come usare le regole di scalabilità automatica per scalare orizzontalmente e aumentare o diminuire il *numero* di istanze di macchine virtuali nel set di scalabilità. È possibile anche scalare verticalmente per aumentare o diminuire le *dimensioni* delle istanze di macchine virtuali. Per altre informazioni, vedere [Ridimensionamento automatico verticale con set di scalabilità di macchine virtuali](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Per informazioni su come gestire le istanze di macchine virtuali, vedere [Gestire set di scalabilità di macchine virtuali con Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Per informazioni su come generare avvisi all'attivazione delle regole di scalabilità automatica, vedere [Usare le azioni di scalabilità automatica per inviare notifiche di avviso di webhook e posta elettronica in Monitoraggio di Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Vedere anche [Use audit logs to send email and webhook alert notifications in Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md) (Usare i log di controllo per inviare notifiche di avviso tramite e-mail e webhook in Monitoraggio di Azure).

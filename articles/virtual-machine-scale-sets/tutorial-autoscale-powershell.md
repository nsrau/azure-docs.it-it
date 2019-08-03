---
title: Esercitazione - Ridimensionare automaticamente un set di scalabilità con Azure PowerShell | Microsoft Docs
description: Informazioni su come ridimensionare automaticamente con Azure PowerShell un set di scalabilità di macchine virtuali in base all'aumento o alla riduzione delle esigenze in termini di CPU
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 2d743b53f5ca74299c865d381f0832729fc956f4
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677601"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Esercitazione: Scalare automaticamente un set di scalabilità di una macchina virtuale con Azure PowerShell

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Quando si crea un set di scalabilità, definire il numero di istanze di macchine virtuali da eseguire. È possibile aumentare o ridurre automaticamente il numero di istanze di macchine virtuali in base alle richieste dell'applicazione. La scalabilità automatica consente di adattarsi alle esigenze dei clienti o di rispondere alle prestazioni dell'applicazione durante il ciclo di vita dell'app. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare la scalabilità automatica con un set di scalabilità
> * Creare e usare regole di scalabilità automatica
> * Sottoporre a test di stress le istanze di VM e attivare le regole di scalabilità automatica
> * Ridurre automaticamente il numero di istanze con la riduzione delle esigenze

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Si verifica un problema noto che influisce sul modulo Azure PowerShell versione 6.8.1 o versioni successive, inclusa la versione corrente di Azure Cloud Shell. Questa esercitazione può essere eseguita solo usando il modulo Azure PowerShell nelle versioni dalla 6.0.0 alla 6.8.0. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.


## <a name="create-a-scale-set"></a>Creare un set di scalabilità
Per rendere più semplice la creazione delle regole di scalabilità automatica, definire alcune variabili per il set di scalabilità. L'esempio seguente definisce le variabili per il set di scalabilità denominato *myScaleSet* nel gruppo di risorse denominato *myResourceGroup* nell'area *Stati Uniti orientali*. Ottenere l'ID della propria sottoscrizione con [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription). Se si hanno più sottoscrizioni associate all'account, viene restituita solo la prima sottoscrizione. Modificare i nomi e l'ID sottoscrizione nel modo seguente:

```azurepowershell-interactive
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroup"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```

Ora si può creare un set di scalabilità di macchine virtuali con [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Per distribuire il traffico alle singole istanze di macchine virtuali, viene creato anche un servizio di bilanciamento del carico. Il servizio di bilanciamento del carico include regole per la distribuzione del traffico sulla porta TCP 80, oltre che per consentire il traffico di Desktop remoto sulla porta TCP 3389 e la comunicazione remota di PowerShell sulla porta TCP 5985. Quando richiesto, fornire le proprie credenziali amministrative desiderate per le istanze di macchina virtuale nel set di scalabilità:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName $myResourceGroup `
  -VMScaleSetName $myScaleSet `
  -Location $myLocation `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer"
```

La creazione e la configurazione di tutte le macchine virtuali e risorse del set di scalabilità richiedono alcuni minuti.

## <a name="create-a-rule-to-autoscale-out"></a>Creare una regola per aumentare automaticamente il numero di istanze
Se aumenta la richiesta da parte dell'applicazione, aumenta il carico sulle istanze di macchine virtuali nel set di scalabilità. Se il carico aumenta in modo coerente e non momentaneamente, è possibile configurare regole di scalabilità automatica per aumentare il numero di istanze di macchine virtuali nel set di scalabilità. Quando sono state create le istanze di macchine virtuali e sono state distribuite le applicazioni, il set di scalabilità inizia a distribuire loro il traffico tramite il bilanciamento del carico. È possibile controllare le metriche da monitorare, ad esempio per la CPU o il disco, il tempo per cui il carico dell'applicazione deve soddisfare una determinata soglia e il numero di istanze di macchine virtuali da aggiungere al set di scalabilità.

È possibile creare una regola con [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) che aumenti il numero di istanze di macchine virtuali in un set di scalabilità quando il carico medio della CPU è superiore al 70% per un periodo di 5 minuti. Quando la regola viene attivata, il numero di istanze di VM viene incrementato di tre.

Per questa regola vengono usati i parametri seguenti:

| Parametro               | Spiegazione                                                                                                         | Valore          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | La metrica delle prestazioni da monitorare e a cui applicare azioni dei set di scalabilità.                                                   | CPU percentuale |
| *-TimeGrain*            | La frequenza con cui le metriche vengono raccolte per l'analisi.                                                                   | 1 minuto       |
| *-MetricStatistic*      | Definisce la modalità di aggregazione delle metriche raccolte per l'analisi.                                                | Media        |
| *-TimeWindow*           | Il tempo monitorato prima che vengano confrontati i valori delle metriche e delle soglie.                                   | 5 minuti      |
| *-Operator*             | Operatore usato per confrontare i dati della metrica rispetto alla soglia.                                                     | Maggiore di   |
| *-Threshold*            | Il valore che determina l'attivazione di un'azione da parte della regola di scalabilità automatica.                                                      | 70%            |
| *-ScaleActionDirection* | Definisce se il set di scalabilità deve aumentare o diminuire quando si applica la regola.                                             | Aumento       |
| *–ScaleActionScaleType* | Indica che il numero di istanze di VM dovrà essere modificato di un valore specifico.                                    | ChangeCount   |
| *-ScaleActionValue*     | La percentuale di istanze di macchine virtuali deve essere modificata quando viene attivata la regola.                                            | 3              |
| *-ScaleActionCooldown*  | Il tempo di attesa prima che la regola venga applicata nuovamente in modo che le azioni di scalabilità automatica diventino effettive. | 5 minuti      |

L'esempio seguente crea un oggetto denominato *myRuleScaleOut* che contiene questa regola di scalabilità. *-MetricResourceId* usa le variabili definite precedentemente per l'ID sottoscrizione, il nome del gruppo di risorse e il nome del set di scalabilità:

```azurepowershell-interactive
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic "Average" `
  -TimeWindow 00:05:00 `
  -Operator "GreaterThan" `
  -Threshold 70 `
  -ScaleActionDirection "Increase" `
  –ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 3 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-autoscale-in"></a>Creare una regola per ridurre automaticamente il numero di istanze
Nelle ore serali o nel fine settimana è possibile che la richiesta delle applicazioni si riduca. Se il carico diminuisce in modo coerente nel tempo, è possibile configurare regole di scalabilità automatica per diminuire il numero di istanze di macchine virtuali nel set di scalabilità. Questa azione riduce i costi di esecuzione del set di scalabilità poiché si esegue solo il numero di istanze necessarie per soddisfare la richiesta corrente.

Creare con [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) un'altra regola che riduca il numero di istanze di VM in un set di scalabilità quando il carico medio della CPU scende quindi al di sotto del 30% per un periodo di 5 minuti. Quando la regola viene attivata, il numero di istanze di VM viene ridotto di uno. L'esempio seguente crea un oggetto denominato *myRuleScaleDown* contenente questa regola di aumento delle prestazioni. *-MetricResourceId* usa le variabili definite precedentemente per l'ID sottoscrizione, il nome del gruppo di risorse e il nome del set di scalabilità:

```azurepowershell-interactive
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator "LessThan" `
  -MetricStatistic "Average" `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection "Decrease" `
  –ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 1
```


## <a name="define-an-autoscale-profile"></a>Definire un profilo di scalabilità automatica
Per associare le regole di scalabilità automatica a un set di scalabilità, creare un profilo. Il profilo di scalabilità automatica definisce la capacità predefinita, minima e massima del set di scalabilità e associa le regole di scalabilità automatica. Creare un profilo di scalabilità automatica con [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile). L'esempio seguente imposta la capacità predefinita e minima di *2* istanze di macchine virtuali e la capacità massima di *10*. Le regole di aumento e riduzione create nei passaggi precedenti vengono quindi collegate:

```azurepowershell-interactive
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-profile-to-a-scale-set"></a>Applicare il profilo di scalabilità automatica a un set di scalabilità
Il passaggio finale consiste nell'applicare il profilo di scalabilità automatica al set di scalabilità. Il numero di istanze nel set di scalabilità potrà quindi essere ridotto o aumentato automaticamente in base alle esigenze delle applicazioni. Applicare il profilo di scalabilità automatica con [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) come indicato di seguito:

```azurepowershell-interactive
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfile $myScaleProfile
```


## <a name="generate-cpu-load-on-scale-set"></a>Generare carico della CPU nel set di scalabilità
Per testare le regole di scalabilità automatica, generare una certa quantità di carico della CPU nelle istanze di VM del set di scalabilità. Questo carico della CPU simulato causa l'aumento del numero di istanze di VM in base alle regole di scalabilità automatica. Quando il carico della CPU simulato viene successivamente ridotto, le regole di scalabilità automatica determinano la riduzione del numero di istanze di VM.

Per visualizzare l'elenco delle porte NAT per la connessione alle istanze di VM di un set di scalabilità, recuperare per prima cosa l'oggetto servizio di bilanciamento del carico con [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer). Visualizzare quindi le regole NAT in ingresso con [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig):

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

L'output di esempio seguente mostra il nome dell'istanza, l'indirizzo IP pubblico del servizio di bilanciamento del carico e il numero di porta a cui le regole NAT inoltrano il traffico:

```powershell
Name        Protocol FrontendPort BackendPort
----        -------- ------------ -----------
myRDPRule.0 Tcp             50001        3389
myRDPRule.1 Tcp             50002        3389
```

Il valore di *Name* della regola è in linea con il nome dell'istanza di VM visualizzato con un precedente comando [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). Per connettersi all'istanza di VM *0*, ad esempio, si usa *myRDPRule.0* e si stabilisce la connessione alla porta *50001*. Per connettersi all'istanza di VM *1*, si usa il valore di *myRDPRule.1* e si stabilisce la connessione alla porta *50002*.

Visualizzare l'indirizzo IP pubblico del servizio di bilanciamento del carico con [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress):

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIP | Select IpAddress
```

Output di esempio:

```powershell
IpAddress
---------
52.168.121.216
```

Creare una connessione remota alla prima istanza di VM. Specificare l'indirizzo IP pubblico e il numero di porta dell'istanza di VM richiesta, in base a quanto visualizzato dai comandi precedenti. Quando richiesto, immettere le credenziali usate durante la creazione del set di scalabilità. Per impostazione predefinita, nei comandi di esempio le credenziali sono *azureuser* e *P\@ssw0rd!* . Se si usa Azure Cloud Shell, eseguire questo passaggio da un prompt di PowerShell in locale o da un client Desktop remoto. L'esempio seguente stabilisce la connessione all'istanza di VM *0*:

```powershell
mstsc /v 52.168.121.216:50001
```

Dopo aver effettuato l'accesso, aprire Internet Explorer dalla barra delle applicazioni.

- Selezionare **OK** per accettare la richiesta relativa a *Usa impostazioni di sicurezza, privacy e compatibilità consigliate*.
- Digitare *http://download.sysinternals.com/files/CPUSTRES.zip* nella barra degli indirizzi.
- Dato che la funzionalità Sicurezza avanzata di Internet Explorer è abilitata, scegliere **Aggiungi** per aggiungere il dominio *http://download.sysinternals.com* all'elenco dei siti attendibili.
- Quando viene visualizzata la richiesta per il download di file, selezionare **Apri** e quindi selezionare ed **eseguire** lo strumento *CPUSTRES.EXE*.

Per generare una certa quantità di carico della CPU, selezionare due caselle corrispondenti a thread con stato **Active** (Attivo). Per entrambi i thread, nel menu a discesa **Activity** (Attività) selezionare *Maximum* (Massima). È possibile aprire Gestione attività per verificare che il carico della CPU nella VM corrisponda al 100%.

![L'utilità CPU Stress genera carico nell'istanza di VM](media/tutorial-autoscale-powershell/cpu-stress-load.PNG)

Lasciare aperta la sessione Connessione desktop remoto e aprirne un'altra. Connettersi alla seconda istanza di VM con il numero di porta elencato dal cmdlet [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig) precedente:

```powershell
mstsc /v 52.168.121.216:50002
```

Dopo aver effettuato l'accesso alla seconda istanza di VM, ripetere i passaggi precedenti per scaricare ed eseguire *CPUSTRES.EXE*. Anche in questo caso avviare due thread con stato **Active** (Attivo) e impostare l'attività su *Maximum* (Massima).

Per mantenere lo strumento **CPU Stress** in esecuzione, lasciare aperte entrambe le sessioni Connessione desktop remoto.


## <a name="monitor-the-active-autoscale-rules"></a>Monitorare le regole di scalabilità automatica attive
Per monitorare il numero di istanze di VM nel set di scalabilità, usare **while**. Le regole di scalabilità automatica impiegano 5 minuti ad avviare il processo di aumento del numero di istanze in risposta al carico della CPU generato da *CPU Stress* in ognuna delle istanze di VM:

```azurepowershell-interactive
while (1) {Get-AzureRmVmssVM `
    -ResourceGroupName $myResourceGroup `
    -VMScaleSetName $myScaleSet; sleep 10}
```

Al raggiungimento della soglia relativa alla CPU, le regole di scalabilità automatica aumentano il numero di istanze di VM nel set di scalabilità. L'output seguente mostra tre VM create con l'aumento automatico del numero di istanze nel set di scalabilità:

```powershell
ResourceGroupName         Name Location          Sku Capacity InstanceID ProvisioningState
-----------------         ---- --------          --- -------- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_2   eastus Standard_DS2                   2         Succeeded
MYRESOURCEGROUP   myScaleSet_3   eastus Standard_DS2                   3         Succeeded
MYRESOURCEGROUP   myScaleSet_4   eastus Standard_DS2                   4          Creating
MYRESOURCEGROUP   myScaleSet_5   eastus Standard_DS2                   5          Creating
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Creating
```

Chiudere lo strumento **CPU Stress** nella sessione Connessione desktop remoto con ognuna delle istanze di VM. Il carico medio della CPU nel set di scalabilità torna alla normalità. Dopo altri 5 minuti, le regole di scalabilità automatica riducono quindi il numero di istanze di VM. Le azioni di riduzione del numero di istanze rimuovono per prime le istanze di VM con gli ID più elevati. Quando un set di scalabilità usa i set di disponibilità o le zone di disponibilità, le azioni di scalabilità vengono distribuite in modo uniforme tra le istanze di macchina virtuale. L'output di esempio seguente mostra l'eliminazione di un'istanza di VM con la riduzione automatica del numero di istanze nel set di scalabilità:

```powershell
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Deleting
```

Uscire da *while* con `Ctrl-c`. La riduzione del numero di istanze nel set di scalabilità continua ogni 5 minuti con la rimozione di un'istanza di VM fino al raggiungimento del numero minimo di due istanze.


## <a name="clean-up-resources"></a>Pulire le risorse
Per rimuovere il set di scalabilità e le risorse aggiuntive, eliminare il gruppo di risorse e tutte le relative risorse con [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Il parametro `-Force` conferma che si desidera eliminare le risorse senza un prompt aggiuntivo a tale scopo. Il parametro `-AsJob` restituisce il controllo al prompt senza attendere il completamento dell'operazione.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come ridurre o aumentare automaticamente il numero di istanze in un set di scalabilità con Azure PowerShell:

> [!div class="checklist"]
> * Usare la scalabilità automatica con un set di scalabilità
> * Creare e usare regole di scalabilità automatica
> * Sottoporre a test di stress le istanze di VM e attivare le regole di scalabilità automatica
> * Ridurre automaticamente il numero di istanze con la riduzione delle esigenze

Per altri esempi del funzionamento dei set di scalabilità di macchine virtuali, vedere gli script di esempio di Azure PowerShell riportati di seguito:

> [!div class="nextstepaction"]
> [Esempi di script di Azure PowerShell per i set di scalabilità](powershell-samples.md)

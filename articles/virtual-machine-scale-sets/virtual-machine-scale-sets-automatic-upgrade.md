---
title: "Aggiornamenti automatici del sistema operativo con i set di scalabilità di macchine virtuali di Azure | Microsoft Docs"
description: "Informazioni su come aggiornare automaticamente il sistema operativo nelle istanze di macchina virtuale in un set di scalabilità"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: negat
ms.openlocfilehash: 145f4ec92b142a1585ba17bf6e49c7824cc32529
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2018
---
# <a name="azure-virtual-machine-scale-set-automatic-os-upgrades"></a>Aggiornamenti automatici del sistema operativo per un set di scalabilità di macchine virtuali di Azure

L'aggiornamento automatico dell'immagine del sistema operativo è una funzionalità di anteprima per i set di scalabilità di macchine virtuali di Azure che consente di aggiornare automaticamente tutte le macchine virtuali all'immagine del sistema operativo più recente.

L'aggiornamento automatico del sistema operativo presenta le caratteristiche seguenti:

- Una volta configurato, l'immagine del sistema operativo più recente pubblicata dagli editori di immagini viene applicata automaticamente al set di scalabilità senza l'intervento dell'utente.
- Aggiorna batch di istanze in sequenza ogni volta che viene pubblicata una nuova immagine della piattaforma dall'editore.
- Si integra con i probe di integrità delle applicazioni (facoltativo, ma consigliato per motivi di sicurezza).
- È adatto per le macchine virtuali di qualsiasi dimensione.
- È adatto per le immagini della piattaforma Windows e Linux.
- È possibile rifiutare esplicitamente gli aggiornamenti automatici in qualsiasi momento (gli aggiornamenti del sistema operativo possono anche essere avviati manualmente).
- Il disco del sistema operativo di una macchina virtuale viene sostituito con il nuovo disco del sistema operativo creato con una versione più recente dell'immagine. Le estensioni configurate e gli script di dati personalizzati vengono eseguiti, mentre i dischi dati persistenti vengono mantenuti.


## <a name="preview-notes"></a>Note sull'anteprima 
Nella versione di anteprima si applicano le limitazioni e restrizioni seguenti:

- Automatica sistema operativo viene aggiornato solo il supporto [quattro SKU del sistema operativo](#supported-os-images). Non sono previsti contratti di servizio o garanzie. È consigliabile non usare gli aggiornamenti automatici sui carichi di lavoro critici di produzione durante l'anteprima.
- Il supporto per i set di scalabilità dei cluster di Service Fabric sarà presto disponibile.
- Crittografia dischi di Azure (attualmente in versione di anteprima) attualmente **non** è supportato con l'aggiornamento automatico del sistema operativo dei set di scalabilità di macchine virtuali.
- Un'esperienza del portale sarà presto disponibile.


## <a name="register-to-use-automatic-os-upgrade"></a>Eseguire la registrazione per usare l'aggiornamento automatico del sistema operativo
Per usare la funzionalità di aggiornamento automatico del sistema operativo, registrare il provider di anteprima con [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) come segue:

```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName AutoOSUpgradePreview
```

Occorrono circa 10 minuti perché lo stato di registrazione venga segnalato come *Registered* (Registrato). È possibile controllare lo stato di registrazione corrente con [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Dopo aver eseguito la registrazione, verificare che il provider *Microsoft.Compute* sia registrato con [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) come segue:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

È consigliabile usare probe di integrità per le applicazioni. Per registrare la funzionalità del provider per i probe di integrità, usare [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) come segue:

```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVmssHealthProbe
```

Anche in questo caso, occorrono circa 10 minuti perché lo stato di registrazione venga segnalato come *Registered* (Registrato). È possibile controllare lo stato di registrazione corrente con [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Dopo aver eseguito la registrazione, verificare che il provider *Microsoft.Network* sia registrato con [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) come segue:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```


## <a name="supported-os-images"></a>Immagini del sistema operativo supportate
Attualmente sono supportate solo alcune immagini della piattaforma del sistema operativo. Al momento non è possibile usare immagini personalizzate create autonomamente. La proprietà *version* dell'immagine della piattaforma deve essere impostata su *latest*.

Attualmente sono supportati i seguenti SKU (ne verranno aggiunti altri a breve):
    
| Editore               | Offerta         |  Sku               | Version  |
|-------------------------|---------------|--------------------|----------|
| Canonical               | UbuntuServer  | 16.04-LTS          | più recenti   |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter | più recenti   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    | più recenti   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Smalldisk | più recenti   |



## <a name="application-health"></a>Integrità dell'applicazione
Durante un aggiornamento del sistema operativo, le istanze di macchina virtuale in un set di scalabilità vengono aggiornate un batch alla volta. L'aggiornamento deve continuare solo se l'applicazione del cliente è integra nelle istanze di macchina virtuale aggiornate. È consigliabile che l'applicazione fornisca segnali di integrità al motore di aggiornamento del sistema operativo del set di scalabilità. Per impostazione predefinita, durante gli aggiornamenti del sistema operativo la piattaforma prende in considerazione lo stato di alimentazione della macchina virtuale e lo stato di provisioning dell'estensione per determinare se un'istanza di macchina virtuale è integra dopo un aggiornamento. Durante l'aggiornamento del sistema operativo di un'istanza di macchina virtuale, il disco del sistema operativo in un'istanza di macchina virtuale viene sostituito con un nuovo disco in base alla versione più recente dell'immagine. Una volta completato l'aggiornamento del sistema operativo, le estensioni configurate vengono eseguite in queste macchine virtuali. L'applicazione viene considerata integra solo dopo che è stato effettuato correttamente il provisioning di tutte le estensioni in una macchina virtuale. 

Un set di scalabilità può facoltativamente essere configurato con probe di integrità dell'applicazione per fornire alla piattaforma informazioni accurate sullo stato dell'applicazione. I probe di integrità delle applicazioni sono probe del servizio di bilanciamento del carico personalizzati usati come un segnale di integrità. L'applicazione in esecuzione in un'istanza di macchina virtuale del set di scalabilità può rispondere a richieste HTTP o TCP esterne per indicare se è integra. Per altre informazioni sul funzionamento dei probe di bilanciamento del carico personalizzati, vedere [Informazioni sui probe di bilanciamento del carico](../load-balancer/load-balancer-custom-probe-overview.md). Un probe di integrità dell'applicazione non è necessario per gli aggiornamenti automatici del sistema operativo, ma è vivamente consigliato.

Se il set di scalabilità è configurato per l'uso di più gruppi di posizionamento, è necessario usare probe con un [servizio di bilanciamento del carico standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

### <a name="important-keep-credentials-up-to-date"></a>Importante: Mantenere le credenziali aggiornate
Se il set di scalabilità utilizza le credenziali per accedere alle risorse esterne, ad esempio se è configurata un'estensione della macchina virtuale che utilizza un token di firma di accesso condiviso per l'account di archiviazione, è necessario assicurarsi che le credenziali vengono aggiornate. Se le credenziali, inclusi i certificati e i token scaduti, l'aggiornamento avrà esito negativo e il primo batch di macchine virtuali verrà lasciato in uno stato di errore.

Le procedure consigliate per ripristinare le macchine virtuali e abilitare di nuovo l'aggiornamento automatico del sistema operativo se si verifica un errore di autenticazione della risorsa sono:

* Rigenerare il token (o qualsiasi altra credenziale) passata l'estensione.
* Verificare che le credenziali utilizzate all'interno della macchina virtuale per comunicare con le entità esterne siano aggiornata.
* Aggiornare le estensioni del modello di set di scalabilità con eventuali nuovi token.
* Distribuire il set di scalabilità aggiornato che aggiornerà tutte le istanze di macchina virtuale, inclusi quelli non riusciti. 

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Configurazione di un probe di bilanciamento del carico personalizzato come probe di integrità dell'applicazione in un set di scalabilità
Come procedura consigliata, creare un probe di bilanciamento del carico in modo esplicito per l'integrità del set di scalabilità. Può essere usato lo stesso endpoint per un probe HTTP o TCP esistente, ma un probe di integrità può richiedere un comportamento diverso da un probe di bilanciamento del carico tradizionale. Ad esempio, un probe di bilanciamento del carico tradizionale può restituire uno stato non integro se il carico sull'istanza è troppo elevato, mentre questo potrebbe non essere appropriato per determinare l'integrità dell'istanza durante un aggiornamento automatico del sistema operativo. Configurare il probe con un tasso di probing elevato, inferiore a due minuti.

È possibile fare riferimento al probe di bilanciamento del carico nell'impostazione *networkProfile* del set di scalabilità e il probe può essere associato a un servizio di bilanciamento del carico interno o pubblico, come segue:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```


## <a name="enforce-an-os-image-upgrade-policy-across-your-subscription"></a>Applicare i criteri di aggiornamento dell'immagine del sistema operativo nella sottoscrizione
Per aggiornamenti sicuri, è consigliabile applicare criteri di aggiornamento. Questi criteri possono richiedere probe di integrità delle applicazioni nella sottoscrizione. I seguenti criteri di Azure Resource Manager rifiutano le distribuzioni per cui non sono configurate impostazioni automatizzate di aggiornamento dell'immagine del sistema operativo:

1. Ottenere la definizione dei criteri predefiniti di Azure Resource Manager con [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) come segue:

    ```powershell
    $policyDefinition = Get-AzureRmPolicyDefinition -Id "/providers/Microsoft.Authorization/policyDefinitions/465f0161-0087-490a-9ad9-ad6217f4f43a"
    ```

2. Assegnare i criteri a una sottoscrizione con [New-AzureRmPolicyAssignment](/powershell/module/AzureRM.Resources/New-AzureRmPolicyAssignment) come segue:

    ```powershell
    New-AzureRmPolicyAssignment `
        -Name "Enforce automatic OS upgrades with app health checks" `
        -Scope "/subscriptions/<SubscriptionId>" `
        -PolicyDefinition $policyDefinition
    ```


## <a name="configure-auto-updates"></a>Configurare gli aggiornamenti automatici
Per configurare gli aggiornamenti automatici, verificare che *automaticOSUpgrade* sia impostato su *true* nella definizione del modello del set di scalabilità. È possibile configurare questa proprietà con Azure PowerShell o l'interfaccia della riga di comando di Azure 2.0.

Nell'esempio seguente viene usato Azure PowerShell (4.4.1 o versione successiva) per configurare gli aggiornamenti automatici per il set di scalabilità denominato *myVMSS* nel gruppo di risorse denominato *myResourceGroup*:

```powershell
$rgname = myResourceGroup
$vmssname = myVMSS
$vmss = Get-AzureRmVMss -ResourceGroupName $rgname -VmScaleSetName $vmssname
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true
Update-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname -VirtualMachineScaleSet $vmss
```


Nell'esempio seguente viene usata l'interfaccia della riga di comando di Azure (2.0.20 o versione successiva) per configurare gli aggiornamenti automatici per il set di scalabilità denominato *myVMSS* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli
rgname="myResourceGroup"
vmssname="myVMSS"
az vmss update --name $vmssname --resource-group $rgname --set upgradePolicy.AutomaticOSUpgrade=true
```


## <a name="check-the-status-of-an-automatic-os-upgrade"></a>Controllare lo stato di un aggiornamento automatico del sistema operativo
È possibile controllare lo stato dell'aggiornamento del sistema operativo più recente eseguito nel set di scalabilità con Azure PowerShell, l'interfaccia della riga di comando di Azure 2.0 o le API REST.

### <a name="azure-powershell"></a>Azure PowerShell
Nell'esempio seguente viene usato Azure PowerShell (4.4.1 o versione successiva) per controllare lo stato per il set di scalabilità denominato *myVMSS* nel gruppo di risorse denominato *myResourceGroup*:

```powershell
Get-AzureRmVmssRollingUpgrade -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0
Nell'esempio seguente viene usata l'interfaccia della riga di comando di Azure (2.0.20 o versione successiva) per controllare lo stato per il set di scalabilità denominato *myVMSS* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli
az vmss rolling-upgrade get-latest --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>API REST
Nell'esempio seguente viene usata l'API REST per controllare lo stato per il set di scalabilità denominato *myVMSS* nel gruppo di risorse denominato *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/rollingUpgrades/latest?api-version=2017-03-30`
```

La chiamata GET restituisce proprietà simili all'output dell'esempio seguente:

```json
{
  "properties": {
    "policy": {
      "maxBatchInstancePercent": 20,
      "maxUnhealthyInstancePercent": 5,
      "maxUnhealthyUpgradedInstancePercent": 5,
      "pauseTimeBetweenBatches": "PT0S"
    },
    "runningStatus": {
      "code": "Completed",
      "startTime": "2017-06-16T03:40:14.0924763+00:00",
      "lastAction": "Start",
      "lastActionTime": "2017-06-22T08:45:43.1838042+00:00"
    },
    "progress": {
      "successfulInstanceCount": 3,
      "failedInstanceCount": 0,
      "inprogressInstanceCount": 0,
      "pendingInstanceCount": 0
    }
  },
  "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
  "location": "southcentralus"
}
```


## <a name="automatic-os-upgrade-execution"></a>Esecuzione dell'aggiornamento automatico del sistema operativo
Per espandere l'uso dei probe di integrità delle applicazioni, gli aggiornamenti del sistema operativo dei set di scalabilità eseguono le operazioni seguenti:

1. Se più del 20% delle istanze non è integro, interrompere l'aggiornamento; in caso contrario, procedere.
2. Identificare il batch successivo di istanze di macchina virtuale per l'aggiornamento, dove un batch che comprende al massimo il 20% del numero totale di istanze.
3. Aggiornare il sistema operativo del batch successivo di istanze di macchina virtuale.
4. Se più del 20% delle istanze aggiornate non è integro, interrompere l'aggiornamento; in caso contrario, procedere.
5. Se il cliente ha configurato probe di integrità delle applicazioni, l'aggiornamento attende fino a 5 minuti che i probe diventino integri, quindi continua immediatamente con il batch successivo; in caso contrario, attende 30 minuti prima di passare al batch successivo.
6. Se sono presenti ulteriori istanze da aggiornare, procedere al passaggio 1) per il batch successivo; in caso contrario, l'aggiornamento è stato completato.

Il motore di aggiornamento del sistema operativo del set di scalabilità verifica l'integrità complessiva delle istanze di macchina virtuale prima di eseguire l'aggiornamento di ogni batch. Durante l'aggiornamento di un batch, possono essere in corso altre attività di manutenzione pianificate o non pianificate nei data center di Azure che potrebbero influire sulla disponibilità delle macchine virtuali. Di conseguenza, è possibile che temporaneamente più del 20% delle istanze risulti inattivo. In questi casi, alla fine del batch corrente, l'aggiornamento del set di scalabilità si interrompe.


## <a name="deploy-with-a-template"></a>Eseguire la distribuzione con un modello

È possibile usare il modello seguente per distribuire un set di scalabilità che usa gli aggiornamenti automatici <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>Aggiornamenti automatici in sequenza - Ubuntu 16.04-LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## <a name="next-steps"></a>Passaggi successivi
Per altri esempi su come usare gli aggiornamenti automatici del sistema operativo con i set di scalabilità, vedere il [repository GitHub per le funzionalità di anteprima](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).

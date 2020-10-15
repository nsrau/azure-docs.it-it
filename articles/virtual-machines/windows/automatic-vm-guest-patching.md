---
title: Applicazione automatica delle patch Guest per VM Windows in Azure
description: Informazioni su come applicare automaticamente le patch alle macchine virtuali Windows in Azure
author: mayanknayar
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/09/2020
ms.author: manayar
ms.openlocfilehash: 0a777b9008864368a6d1731cae0374e55a4c585f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842870"
---
# <a name="preview-automatic-vm-guest-patching-for-windows-vms-in-azure"></a>Anteprima: applicazione automatica delle patch guest alle macchine virtuali Windows in Azure

L'abilitazione dell'applicazione automatica delle patch Guest per le macchine virtuali Windows consente di semplificare la gestione degli aggiornamenti con l'applicazione di patch alle macchine virtuali in modo sicuro e automatico per garantire la conformità

L'applicazione automatica di patch per guest VM presenta le caratteristiche seguenti:
- Le patch classificate come *critiche* o la *sicurezza* vengono scaricate e applicate automaticamente nella macchina virtuale.
- Le patch vengono applicate durante gli orari di minore traffico nel fuso orario della macchina virtuale.
- Patch Orchestration è gestito da Azure e le patch vengono applicate in base ai principi di disponibilità.
- L'integrità della macchina virtuale, come determinato dai segnali di integrità della piattaforma, viene monitorata per rilevare errori di applicazione delle patch.
- È adatto per le macchine virtuali di qualsiasi dimensione.

> [!IMPORTANT]
> L'applicazione automatica delle patch Guest per macchine virtuali è attualmente disponibile in anteprima pubblica. Per usare la funzionalità di anteprima pubblica descritta di seguito, è necessaria una procedura di consenso esplicito.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-automatic-vm-guest-patching-work"></a>Come funziona l'applicazione automatica delle patch per guest VM?

Se l'applicazione automatica delle patch Guest per le VM è abilitata in una macchina virtuale, le patch di *sicurezza* e *critiche* disponibili vengono scaricate e applicate automaticamente nella macchina virtuale. Questo processo viene automaticamente interrotto ogni mese quando vengono rilasciate nuove patch tramite Windows Update. La valutazione e l'installazione delle patch sono automatiche e il processo include il riavvio della macchina virtuale secondo le esigenze.

La macchina virtuale viene valutata periodicamente per determinare le patch applicabili per tale macchina virtuale. Le patch possono essere installate ogni giorno nella macchina virtuale durante gli orari di minore traffico per la macchina virtuale. Questa valutazione automatica garantisce che tutte le patch mancanti vengano individuate alla prima possibile opportunità.

Le patch vengono installate entro 30 giorni dalla versione di Windows Update mensile, seguendo la prima orchestrazione di disponibilità descritta di seguito. Le patch vengono installate solo durante gli orari di minore traffico per la macchina virtuale, a seconda del fuso orario della macchina virtuale. Per installare automaticamente le patch, la macchina virtuale deve essere in esecuzione durante gli orari di minore traffico. Se una macchina virtuale viene spenta durante una valutazione periodica, la macchina virtuale verrà automaticamente valutata e le patch applicabili verranno installate automaticamente durante la valutazione periodica successiva quando la macchina virtuale è accesa.

Per installare patch con altre classificazioni patch o pianificare l'installazione di patch all'interno della finestra di manutenzione personalizzata, è possibile usare [Gestione aggiornamenti](tutorial-config-management.md#manage-windows-updates).

### <a name="availability-first-patching"></a>Disponibilità-prima applicazione di patch

Il processo di installazione della patch viene orchestrato a livello globale da Azure per tutte le macchine virtuali in cui è abilitata l'applicazione automatica delle patch per guest VM. Questa orchestrazione segue i principi di disponibilità in diversi livelli di disponibilità offerti da Azure.

Per un gruppo di macchine virtuali in fase di aggiornamento, la piattaforma Azure eseguirà l'orchestrazione degli aggiornamenti:

**Tra le aree:**
- Un aggiornamento mensile viene orchestrato in Azure a livello globale in modo graduale per evitare errori di distribuzione globali.
- Una fase può avere una o più aree e un aggiornamento passa alle fasi successive solo se le VM idonee in una fase vengono aggiornate correttamente.
- Le aree geografiche abbinate non vengono aggiornate simultaneamente e non possono trovarsi nella stessa fase regionale.
- Il completamento di un aggiornamento viene misurato tenendo traccia dell'aggiornamento del post di integrità della macchina virtuale. L'integrità della macchina virtuale viene rilevata tramite indicatori di integrità della piattaforma per la macchina virtuale.

**All'interno di un'area:**
- Le macchine virtuali in zone di disponibilità diverse non vengono aggiornate simultaneamente.
- Le macchine virtuali che non fanno parte di un set di disponibilità vengono eseguite in batch in base al massimo sforzo per evitare aggiornamenti simultanei per tutte le macchine virtuali in una sottoscrizione.

**All'interno di un set di disponibilità:**
- Tutte le macchine virtuali in un set di disponibilità comune non vengono aggiornate simultaneamente.
-   Le macchine virtuali in un set di disponibilità comune vengono aggiornate all'interno dei limiti del dominio di aggiornamento e le macchine virtuali in più domini di aggiornamento non vengono aggiornate simultaneamente.

La data di installazione della patch per una determinata macchina virtuale può variare da un mese all'altro, perché una macchina virtuale specifica può essere prelevata in un altro batch tra cicli di patch mensili.

## <a name="supported-os-images"></a>Immagini del sistema operativo supportate
Nell'anteprima sono attualmente supportate solo le macchine virtuali create da determinate immagini della piattaforma del sistema operativo. Le immagini personalizzate non sono attualmente supportate nell'anteprima.

Gli SKU di piattaforma seguenti sono attualmente supportati e altri vengono aggiunti periodicamente:

| Publisher               | Offerta sistema operativo      |  Sku               |
|-------------------------|---------------|--------------------|
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Server-Core |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-Server-Core |

## <a name="patch-orchestration-modes"></a>Patch Orchestration Mode
Le macchine virtuali Windows in Azure supportano ora le modalità di orchestrazione delle patch seguenti:

**AutomaticByPlatform:**
- Questa modalità consente l'applicazione automatica delle patch Guest per la macchina virtuale Windows e l'installazione della patch successiva è orchestrata da Azure.
- L'impostazione di questa modalità Disabilita inoltre la Aggiornamenti automatici nativa nella macchina virtuale Windows per evitare la duplicazione.
- Questa modalità è supportata solo per le macchine virtuali create con le immagini della piattaforma del sistema operativo supportate sopra.
- Per usare questa modalità, impostare la proprietà `osProfile.windowsConfiguration.enableAutomaticUpdates=true` e impostare la proprietà  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatfom` nel modello di macchina virtuale.

**AutomaticByOS:**
- Questa modalità Abilita Aggiornamenti automatici nella macchina virtuale Windows e le patch vengono installate nella VM tramite Aggiornamenti automatici.
- Questa modalità è impostata per impostazione predefinita se non viene specificata nessun'altra modalità di patch.
- Per usare questa modalità, impostare la proprietà `osProfile.windowsConfiguration.enableAutomaticUpdates=true` e impostare la proprietà  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` nel modello di macchina virtuale.

**Manuale:**
- Questa modalità Disabilita Aggiornamenti automatici nella macchina virtuale Windows.
- Questa modalità deve essere impostata quando si utilizzano soluzioni di applicazione di patch personalizzate.
- Per usare questa modalità, impostare la proprietà `osProfile.windowsConfiguration.enableAutomaticUpdates=false` e impostare la proprietà  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` nel modello di macchina virtuale.

> [!NOTE]
>Attualmente la proprietà `osProfile.windowsConfiguration.enableAutomaticUpdates` può essere impostata solo quando la VM viene creata per la prima volta. Il cambio da manuale a modalità automatica o da una modalità automatica a una modalità manuale non è attualmente supportato. Il cambio dalla modalità AutomaticByOS alla modalità AutomaticByPlatfom è supportato

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>Requisiti per l'abilitazione dell'applicazione automatica delle patch per guest VM

- Per la macchina virtuale deve essere installato l' [agente VM di Azure](../extensions/agent-windows.md) .
- Il servizio di Windows Update deve essere in esecuzione nella macchina virtuale.
- La macchina virtuale deve essere in grado di accedere Windows Update endpoint. Se la macchina virtuale è configurata per l'utilizzo di Windows Server Update Services (WSUS), è necessario che gli endpoint server WSUS pertinenti siano accessibili.
- Usare l'API di calcolo versione 2020-06-01 o successiva.

Per abilitare la funzionalità di anteprima, è necessario un unico consenso esplicito per la funzionalità *InGuestAutoPatchVMPreview* per sottoscrizione, come descritto di seguito.

### <a name="rest-api"></a>API REST
Nell'esempio seguente viene descritto come abilitare l'anteprima per la sottoscrizione:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```

Una volta registrata la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Usare il cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) per abilitare l'anteprima per la sottoscrizione.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Una volta registrata la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0
Usare [AZ feature Register](/cli/azure/feature#az-feature-register) per abilitare l'anteprima per la sottoscrizione.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
```

Una volta registrata la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```
## <a name="enable-automatic-vm-guest-patching"></a>Abilitare l'applicazione automatica di patch guest alle macchine virtuali
Per abilitare l'applicazione automatica delle patch Guest per macchine virtuali, assicurarsi che la proprietà *osProfile. windowsConfiguration. enableAutomaticUpdates* sia impostata su *true* nella definizione del modello di macchina virtuale. Questa proprietà può essere impostata solo quando si crea la macchina virtuale.

### <a name="rest-api"></a>API REST
Nell'esempio seguente viene descritto come abilitare l'applicazione automatica di patch per guest VM:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-06-01`
```

```json
{
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Usare il cmdlet [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) per abilitare l'applicazione automatica delle patch per guest VM durante la creazione o l'aggiornamento di una macchina virtuale.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0
Usare [AZ VM create](/cli/azure/vm#az-vm-create) per abilitare l'applicazione automatica delle patch Guest per le VM quando si crea una nuova macchina virtuale. Nell'esempio seguente viene configurata l'applicazione automatica delle patch Guest per una macchina virtuale denominata *myVM* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

Per modificare una macchina virtuale esistente, usare il comando [AZ VM Update](/cli/azure/vm#az-vm-update)

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>Abilitazione e valutazione

> [!NOTE]
>Potrebbero essere necessarie più di tre ore per abilitare gli aggiornamenti automatici dei guest VM in una macchina virtuale, in quanto l'abilitazione viene completata durante le ore di minore traffico della macchina virtuale. Poiché la valutazione e l'installazione delle patch si verificano solo durante le ore non di punta, la macchina virtuale deve essere in esecuzione durante gli orari di minore traffico per applicare le patch.

Quando è abilitata l'applicazione automatica delle patch Guest per una macchina virtuale, viene installata un'estensione della macchina virtuale di tipo `Microsoft.CPlat.Core.WindowsPatchExtension` nella macchina virtuale. Questa estensione non deve essere installata o aggiornata manualmente, perché questa estensione viene gestita dalla piattaforma Azure come parte del processo di applicazione automatica di patch per i guest VM.

Potrebbero essere necessarie più di tre ore per abilitare gli aggiornamenti automatici dei guest VM in una macchina virtuale, in quanto l'abilitazione viene completata durante le ore di minore traffico della macchina virtuale. L'estensione viene inoltre installata e aggiornata durante gli orari di minore traffico per la macchina virtuale. Se le ore di minore traffico della macchina virtuale terminano prima del completamento dell'abilitazione, il processo di abilitazione verrà ripreso durante il successivo periodo di minore disponibilità. Se la macchina virtuale aveva in precedenza Windows Update attivata automaticamente tramite la modalità patch AutomaticByOS, il Windows Update automatico viene disattivato per la macchina virtuale al momento dell'installazione dell'estensione.

Per verificare se l'applicazione automatica delle patch Guest per le macchine virtuali è stata completata e l'estensione per l'applicazione di patch è installata nella macchina virtuale, è possibile esaminare la visualizzazione dell'istanza della macchina virtuale. Se il processo di abilitazione è completo, l'estensione verrà installata e i risultati della valutazione per la VM saranno disponibili con `patchStatus` . È possibile accedere alla visualizzazione dell'istanza della macchina virtuale in diversi modi, come descritto di seguito.

### <a name="rest-api"></a>API REST

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-06-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
Usare il cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) con il `-Status` parametro per accedere alla visualizzazione dell'istanza per la macchina virtuale.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

PowerShell fornisce attualmente solo informazioni sull'estensione della patch. Le informazioni su `patchStatus` saranno presto disponibili anche tramite PowerShell.

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0
Usare il comando [AZ VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view) per accedere alla visualizzazione dell'istanza della macchina virtuale.

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>Informazioni sullo stato delle patch per la macchina virtuale

La `patchStatus` sezione della risposta alla visualizzazione dell'istanza fornisce informazioni dettagliate sulla valutazione più recente e sull'ultima installazione della patch per la macchina virtuale.

I risultati della valutazione per la VM possono essere esaminati nella `availablePatchSummary` sezione. Una valutazione viene eseguita periodicamente per una macchina virtuale in cui è abilitata l'applicazione automatica delle patch per guest VM. Il numero di patch disponibili dopo una valutazione viene fornito con `criticalAndSecurityPatchCount` `otherPatchCount` i risultati. L'applicazione automatica delle patch Guest di VM installerà tutte le patch valutate in base alle classificazioni delle patch *critiche* e di *sicurezza* . Qualsiasi altra patch valutata verrà ignorata.

I risultati dell'installazione della patch per la macchina virtuale possono essere esaminati nella `lastPatchInstallationSummary` sezione. Questa sezione fornisce informazioni dettagliate sull'ultimo tentativo di installazione della patch nella VM, incluso il numero di patch installate, in sospeso, non riuscite o ignorate. Le patch vengono installate solo durante la finestra di manutenzione per le ore non di punta per la macchina virtuale. Le patch in sospeso e non riuscite vengono ritentate automaticamente durante la successiva finestra di manutenzione degli orari di minore traffico.

## <a name="on-demand-patch-assessment"></a>Valutazione patch su richiesta
Se l'applicazione automatica delle patch Guest per la VM è già abilitata per la macchina virtuale, viene eseguita una valutazione periodica delle patch sulla macchina virtuale durante le ore di minore traffico della macchina virtuale. Questo processo è automatico e i risultati della valutazione più recente possono essere esaminati tramite la visualizzazione dell'istanza della macchina virtuale, come descritto in precedenza in questo documento. È anche possibile attivare una valutazione delle patch su richiesta per la macchina virtuale in qualsiasi momento. La valutazione della patch può richiedere alcuni minuti per il completamento e lo stato dell'ultima valutazione viene aggiornato nella visualizzazione dell'istanza della macchina virtuale.

Per abilitare la funzionalità di anteprima, è necessario un unico consenso esplicito per la funzionalità *InGuestPatchVMPreview* per sottoscrizione. L'anteprima delle funzionalità per la valutazione delle patch su richiesta può essere abilitata seguendo il [processo di abilitazione dell'anteprima](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching) descritto in precedenza per l'applicazione automatica delle patch Guest per macchine virtuali.

> [!NOTE]
>La valutazione patch su richiesta non attiva automaticamente l'installazione della patch. Le patch valutate e applicabili per la macchina virtuale verranno installate solo durante le ore non di punta della macchina virtuale, in seguito al processo di applicazione delle patch prima della disponibilità descritto in precedenza in questo documento.

### <a name="rest-api"></a>API REST
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Usare il cmdlet [Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) per valutare le patch disponibili per la macchina virtuale.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0
Usare il comando [AZ VM valuti-patches](/cli/azure/vm#az-vm-assess-patches) per valutare le patch disponibili per la macchina virtuale.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Altre informazioni sulla creazione e la gestione di macchine virtuali Windows](tutorial-manage-vm.md)

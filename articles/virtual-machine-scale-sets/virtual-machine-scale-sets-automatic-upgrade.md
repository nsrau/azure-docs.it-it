---
title: Aggiornamenti automatici dell'immagine del sistema operativo con i set di scalabilità di macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come aggiornare automaticamente l'immagine del sistema operativo nelle istanze di macchina virtuale in un set di scalabilità
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: manayar
ms.openlocfilehash: 007f2801efed8da4964808056563418dec7f64d5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798509"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Aggiornamenti automatici dell'immagine del sistema operativo con i set di scalabilità di macchine virtuali di Azure

L'abilitazione degli aggiornamenti automatici per l'immagine del sistema operativo nel set di scalabilità è utile per facilitare la gestione degli aggiornamenti, grazie alla possibilità di aggiornare in modo sicuro e automatico il disco del sistema operativo per tutte le istanze nel set di scalabilità.

L'aggiornamento automatico del sistema operativo presenta le caratteristiche seguenti:

- Una volta configurato, l'immagine del sistema operativo più recente pubblicata dagli editori di immagini viene applicata automaticamente al set di scalabilità senza l'intervento dell'utente.
- Aggiorna batch di istanze in sequenza ogni volta che viene pubblicata una nuova immagine della piattaforma dall'editore.
- Si integra con i probe di integrità dell'applicazione e con l'[estensione Integrità applicazione](virtual-machine-scale-sets-health-extension.md).
- Funziona per le macchine virtuali di tutte le dimensioni e per le immagini di entrambe le piattaforme Windows e Linux.
- È possibile rifiutare esplicitamente gli aggiornamenti automatici in qualsiasi momento (gli aggiornamenti del sistema operativo possono anche essere avviati manualmente).
- Il disco del sistema operativo di una macchina virtuale viene sostituito con il nuovo disco del sistema operativo creato con una versione più recente dell'immagine. Le estensioni configurate e gli script di dati personalizzati vengono eseguiti, mentre i dischi dati persistenti vengono mantenuti.
- È supportata la [sequenziazione delle estensioni](virtual-machine-scale-sets-extension-sequencing.md).

## <a name="how-does-automatic-os-image-upgrade-work"></a>Come funziona l'aggiornamento dell'immagine del sistema operativo?

Un aggiornamento consiste nella sostituzione del disco del sistema operativo di una macchina virtuale con un nuovo disco creato usando la versione più recente dell'immagine. Le estensioni configurate e gli script di dati personalizzati vengono eseguiti nel disco del sistema operativo, mentre i dischi dati persistenti vengono mantenuti. Per ridurre al minimo il tempo di inattività delle applicazioni, gli aggiornamenti vengono eseguiti in batch, aggiornando ogni volta non più del 20% del set di scalabilità. È anche possibile integrare un probe di integrità dell'applicazione di Azure Load Balancer o l'[estensione Integrità applicazione](virtual-machine-scale-sets-health-extension.md). È consigliabile incorporare un heartbeat delle applicazioni e convalidare l'esito positivo per ogni batch nel processo di aggiornamento.

Il processo di aggiornamento funziona nel modo seguente:
1. Prima di iniziare il processo di aggiornamento, l'agente di orchestrazione verifica che il numero di istanze non integre (per qualsiasi motivo) non superi il 20% nell'intero set di scalabilità.
2. L'agente di orchestrazione dell'aggiornamento identifica il batch di istanze di macchine virtuali da aggiornare, con un batch che comprende al massimo il 20% del numero totale di istanze.
3. Il disco del sistema operativo del batch di istanze di macchine virtuali selezionato viene sostituito con un nuovo disco del sistema operativo creato dall'immagine più recente e tutte le estensioni e le configurazioni specificate nel modello del set di scalabilità vengono applicate all'istanza aggiornata.
4. Per i set di scalabilità in cui sono configurati probe di integrità dell'applicazione o l'estensione Integrità applicazione, l'aggiornamento attende fino a 5 minuti che l'istanza risulti integra prima di procedere con l'aggiornamento del batch successivo.
5. L'agente di orchestrazione dell'aggiornamento tiene anche traccia della percentuale di istanze che diventano non integre dopo un aggiornamento. L'aggiornamento verrà interrotto se più del 20% delle istanze aggiornate diventa non integro durante il processo di aggiornamento.
6. Questo processo continua fino a completare l'aggiornamento di tutte le istanze nel set di scalabilità.

L'agente di orchestrazione dell'aggiornamento del sistema operativo del set di scalabilità verifica l'integrità complessiva del set di scalabilità prima di aggiornare ogni batch. Durante l'aggiornamento di un batch, possono essere in corso altre attività di manutenzione pianificate o non pianificate che potrebbero influire sull'integrità delle istanze del set di scalabilità. In questi casi, se più del 20% delle istanze del set di scalabilità diventa non integro, l'aggiornamento del set di scalabilità si interrompe alla fine del batch corrente.

## <a name="supported-os-images"></a>Immagini del sistema operativo supportate
Attualmente sono supportate solo alcune immagini della piattaforma del sistema operativo. Le immagini personalizzate non sono attualmente supportate.

Sono attualmente supportati gli SKU seguenti e ne vengono aggiunti altri periodicamente:

| Editore               | Offerta sistema operativo      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| Rogue Wave (OpenLogic)  | CentOS        | 7.5                |
| CoreOS                  | CoreOS        | Stabile             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-with-Containers |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-with-Containers |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Requisiti per la configurazione dell'aggiornamento automatico dell'immagine del sistema operativo

- La proprietà *version* dell'immagine della piattaforma deve essere impostata su *latest*.
- Usare i probe di integrità dell'applicazione o l'[estensione Integrità applicazione](virtual-machine-scale-sets-health-extension.md) per i set di scalabilità non di Service Fabric.
- Assicurarsi che le risorse esterne specificate nel modello del set di scalabilità siano disponibili e aggiornate. Ad esempio, URI della firma di accesso condiviso per il payload di bootstrap nelle proprietà di estensione della macchina virtuale, payload nell'account di archiviazione, riferimento ai segreti nel modello e altro.

## <a name="configure-automatic-os-image-upgrade"></a>Configurare l'aggiornamento automatico dell'immagine del sistema operativo
Per configurare l'aggiornamento automatico dell'immagine del sistema operativo, verificare che nella definizione del modello del set di scalabilità la proprietà *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* sia impostata su *true*.

### <a name="rest-api"></a>API REST
L'esempio seguente descrive come impostare gli aggiornamenti automatici del sistema operativo in un modello del set di scalabilità:

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2018-10-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Usare il cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) per controllare la cronologia di aggiornamento del sistema operativo per il set di scalabilità. Nell'esempio seguente vengono configurati gli aggiornamenti automatici per il set di scalabilità denominato *myVMSS* nel gruppo di risorse denominato *myResourceGroup*:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0
Usare [az vmss update](/cli/azure/vmss#az-vmss-update) per controllare la cronologia di aggiornamento del sistema operativo per il set di scalabilità. Usare l'interfaccia della riga di comando di Azure versione 2.0.47 o successiva. Nell'esempio seguente vengono configurati gli aggiornamenti automatici per il set di scalabilità denominato *myVMSS* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
az vmss update --name myVMSS --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>Uso dei probe di integrità dell'applicazione

Durante un aggiornamento del sistema operativo, le istanze di macchina virtuale in un set di scalabilità vengono aggiornate un batch alla volta. L'aggiornamento deve continuare solo se l'applicazione del cliente è integra nelle istanze di macchina virtuale aggiornate. È consigliabile che l'applicazione fornisca segnali di integrità al motore di aggiornamento del sistema operativo del set di scalabilità. Per impostazione predefinita, durante gli aggiornamenti del sistema operativo la piattaforma prende in considerazione lo stato di alimentazione della macchina virtuale e lo stato di provisioning dell'estensione per determinare se un'istanza di macchina virtuale è integra dopo un aggiornamento. Durante l'aggiornamento del sistema operativo di un'istanza di macchina virtuale, il disco del sistema operativo in un'istanza di macchina virtuale viene sostituito con un nuovo disco in base alla versione più recente dell'immagine. Una volta completato l'aggiornamento del sistema operativo, le estensioni configurate vengono eseguite in queste macchine virtuali. L'applicazione viene considerata integra solo dopo che è stato completato correttamente il provisioning di tutte le estensioni nell'istanza.

Un set di scalabilità può facoltativamente essere configurato con probe di integrità dell'applicazione per fornire alla piattaforma informazioni accurate sullo stato dell'applicazione. I probe di integrità delle applicazioni sono probe del servizio di bilanciamento del carico personalizzati usati come un segnale di integrità. L'applicazione in esecuzione in un'istanza di macchina virtuale del set di scalabilità può rispondere a richieste HTTP o TCP esterne per indicare se è integra. Per altre informazioni sul funzionamento dei probe di bilanciamento del carico personalizzati, vedere [Informazioni sui probe di bilanciamento del carico](../load-balancer/load-balancer-custom-probe-overview.md). Un probe di integrità dell'applicazione non è necessario per i set di scalabilità di Service Fabric, ma è consigliabile. Per i set di scalabilità non di Service Fabric sono richiesti probe di integrità dell'applicazione Load Balancer oppure l'[estensione Integrità applicazione](virtual-machine-scale-sets-health-extension.md).

Se il set di scalabilità è configurato per l'uso di più gruppi di posizionamento, è necessario usare probe con un [servizio di bilanciamento del carico standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

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
}
```

> [!NOTE]
> Quando si usano gli aggiornamenti automatici del sistema operativo con Service Fabric, la nuova immagine del sistema operativo viene implementata in un dominio di aggiornamento alla volta per mantenere un'elevata disponibilità dei servizi in esecuzione in Service Fabric. Per usare gli aggiornamenti automatici del sistema operativo in Service Fabric, il cluster deve essere configurato per usare il livello di durabilità Silver o superiore. Per altre informazioni sulle caratteristiche di durabilità dei cluster di Service Fabric, vedere [questa documentazione](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Mantenere aggiornate le credenziali
Se il set di scalabilità usa credenziali per accedere alle risorse esterne, ad esempio se è configurata un'estensione della macchina virtuale che usa un token di firma di accesso condiviso per l'account di archiviazione, assicurarsi che le credenziali siano aggiornate. In caso di scadenza delle credenziali, inclusi i certificati e i token, l'aggiornamento avrà esito negativo e il primo batch di macchine virtuali verrà lasciato in uno stato di errore.

Le procedure consigliate per recuperare le macchine virtuali e abilitare di nuovo l'aggiornamento automatico del sistema operativo se si verifica un errore di autenticazione delle risorse sono:

* Rigenerare il token (o qualsiasi altra credenziale) passato alle estensioni.
* Verificare che le credenziali usate all'interno della macchina virtuale per comunicare con le entità esterne siano aggiornate.
* Aggiornare le estensioni nel modello di set di scalabilità con eventuali nuovi token.
* Distribuire il set di scalabilità aggiornato, che aggiornerà tutte le istanze di macchina virtuale, incluse quelle in errore.

## <a name="using-application-health-extension"></a>Uso dell'estensione Integrità applicazione
L'estensione Integrità applicazione viene distribuita in un'istanza di un set di scalabilità di macchine virtuali e segnala l'integrità della macchina virtuale dall'interno dell'istanza di set di scalabilità. È possibile configurare l'estensione per eseguire il probe su un endpoint dell'applicazione e aggiornare lo stato dell'applicazione in tale istanza. Questo stato dell'istanza viene verificato da Azure per determinare se un'istanza è idonea per le operazioni di aggiornamento.

Poiché l'estensione segnala l'integrità dall'interno di una macchina virtuale, può essere usata nelle situazioni in cui non è possibile usare probe esterni, ad esempio i probe di Integrità applicazione (che usano [probe](../load-balancer/load-balancer-custom-probe-overview.md) personalizzati di Azure Load Balancer).

Esistono diversi modi per distribuire l'estensione Integrità applicazione nei set di scalabilità, come descritto in dettaglio negli esempi in [questo articolo](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension).

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Ottenere la cronologia degli aggiornamenti automatici dell'immagine del sistema operativo
È possibile controllare la cronologia dell'aggiornamento del sistema operativo più recente eseguito nel set di scalabilità con Azure PowerShell, l'interfaccia della riga di comando di Azure 2.0 o le API REST. È possibile ottenere la cronologia degli ultimi cinque tentativi di aggiornamento del sistema operativo negli ultimi due mesi.

### <a name="rest-api"></a>API REST
Nell'esempio seguente viene usata l'[API REST](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) per controllare lo stato per il set di scalabilità denominato *myVMSS* nel gruppo di risorse denominato *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```

La chiamata GET restituisce proprietà simili all'output dell'esempio seguente:

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Usare il cmdlet [Get-AzVmss](/powershell/module/az.compute/get-azvmss) per controllare la cronologia di aggiornamento del sistema operativo per il set di scalabilità. Nell'esempio seguente viene dimostrato in dettaglio come verificare lo stato di aggiornamento del sistema operativo per un set di scalabilità denominato *myVMSS* nel gruppo di risorse denominato *myResourceGroup*:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0
Usare [az vmss get-os-upgrade-history](/cli/azure/vmss#az-vmss-get-os-upgrade-history) per controllare la cronologia di aggiornamento del sistema operativo per il set di scalabilità. Usare l'interfaccia della riga di comando di Azure versione 2.0.47 o successiva. Nell'esempio seguente viene dimostrato in dettaglio come verificare lo stato di aggiornamento del sistema operativo per un set di scalabilità denominato *myVMSS* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myVMSS
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Come ottenere la versione più recente di un'immagine del sistema operativo della piattaforma?

È possibile ottenere le versioni dell'immagine per gli SKU supportati tramite gli aggiornamenti automatici del sistema operativo usando gli esempi seguenti:

### <a name="rest-api"></a>API REST
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="deploy-with-a-template"></a>Eseguire la distribuzione con un modello

È possibile usare modelli per distribuire un set di scalabilità con aggiornamenti automatici del sistema operativo per le immagini supportate, ad esempio [Ubuntu 16.04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>Passaggi successivi
Per altri esempi su come usare gli aggiornamenti automatici del sistema operativo con i set di scalabilità, vedere il [repository GitHub ](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).

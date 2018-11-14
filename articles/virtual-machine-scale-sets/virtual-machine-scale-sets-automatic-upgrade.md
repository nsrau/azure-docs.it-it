---
title: Aggiornamenti automatici dell'immagine del sistema operativo con i set di scalabilità di macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come aggiornare automaticamente l'immagine del sistema operativo nelle istanze di macchina virtuale in un set di scalabilità
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: rajraj
ms.openlocfilehash: c8ba9ac3150b5a84b2902afaaefcf78c76764fed
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036191"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Aggiornamenti automatici dell'immagine del sistema operativo con i set di scalabilità di macchine virtuali di Azure

L'aggiornamento automatico dell'immagine del sistema operativo è una funzionalità dei set di scalabilità di macchine virtuali di Azure che consente di aggiornare automaticamente tutte le macchine virtuali all'immagine del sistema operativo più recente.

L'aggiornamento automatico del sistema operativo presenta le caratteristiche seguenti:

- Una volta configurato, l'immagine del sistema operativo più recente pubblicata dagli editori di immagini viene applicata automaticamente al set di scalabilità senza l'intervento dell'utente.
- Aggiorna batch di istanze in sequenza ogni volta che viene pubblicata una nuova immagine della piattaforma dall'editore.
- Si integra con un probe di integrità dell'applicazione.
- Funziona per le macchine virtuali di tutte le dimensioni e per le immagini di entrambe le piattaforme Windows e Linux.
- È possibile rifiutare esplicitamente gli aggiornamenti automatici in qualsiasi momento (gli aggiornamenti del sistema operativo possono anche essere avviati manualmente).
- Il disco del sistema operativo di una macchina virtuale viene sostituito con il nuovo disco del sistema operativo creato con una versione più recente dell'immagine. Le estensioni configurate e gli script di dati personalizzati vengono eseguiti, mentre i dischi dati persistenti vengono mantenuti.
- La crittografia dischi di Azure (in anteprima) non è attualmente supportata.  

## <a name="how-does-automatic-os-image-upgrade-work"></a>Come funziona l'aggiornamento dell'immagine del sistema operativo?

Un aggiornamento consiste nella sostituzione del disco del sistema operativo di una macchina virtuale con uno nuovo creato usando la versione più recente dell'immagine. Le estensioni configurate e gli script di dati personalizzati vengono eseguiti, mentre i dischi dati persistenti vengono mantenuti. Per ridurre al minimo il tempo di inattività delle applicazioni, gli aggiornamenti vengono eseguiti su batch di macchine, aggiornando ogni volta non più del 20% del set di scalabilità. È anche possibile integrare un probe di integrità dell'applicazione di Azure Load Balancer. È altamente consigliabile incorporare un heartbeat delle applicazioni e convalidare l'esito positivo per ogni batch nel processo di aggiornamento. Vengono eseguiti questi passaggi: 

1. Prima di iniziare il processo di aggiornamento, l'agente di orchestrazione verifica che il numero di istanze non integre non superi il 20%. 
2. Identificare il batch di istanze di macchina virtuale per l'aggiornamento, con un batch che comprende al massimo il 20% del numero totale di istanze.
3. Aggiornare l'immagine del sistema operativo di questo batch di istanze di macchina virtuale.
4. Se il cliente ha configurato probe di integrità dell'applicazione, l'aggiornamento attende fino a 5 minuti che i probe risultino integri prima di passare al batch successivo. 
5. Se sono presenti ulteriori istanze da aggiornare, procedere al passaggio 1) per il batch successivo; in caso contrario, l'aggiornamento è stato completato.

L'agente di orchestrazione dell'aggiornamento del sistema operativo del set di scalabilità verifica l'integrità complessiva delle istanze di macchina virtuale prima di aggiornare ogni batch. Durante l'aggiornamento di un batch, possono essere in corso altre attività di manutenzione pianificate o non pianificate nei data center di Azure che potrebbero influire sulla disponibilità delle macchine virtuali. Di conseguenza, è possibile che temporaneamente più del 20% delle istanze risulti inattivo. In questi casi, alla fine del batch corrente, l'aggiornamento del set di scalabilità si interrompe.

## <a name="supported-os-images"></a>Immagini del sistema operativo supportate
Attualmente sono supportate solo alcune immagini della piattaforma del sistema operativo. Al momento non è possibile usare immagini personalizzate create autonomamente. 

Attualmente sono supportati i seguenti SKU (ne verranno aggiunti altri in futuro):
    
| Editore               | Offerta sistema operativo      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS *        | 
| Rogue Wave (OpenLogic)  | CentOS        | 7.5 *              | 
| CoreOS                  | CoreOS        | Stabile             | 
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    | 
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-with-Containers |

* Il supporto per queste immagini è attualmente in fase di implementazione e sarà disponibile a breve in tutte le aree di Azure. 

## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Requisiti per la configurazione dell'aggiornamento automatico dell'immagine del sistema operativo

- La proprietà *version* dell'immagine della piattaforma deve essere impostata su *latest*.
- Usare i probe di integrità dell'applicazione per i set di scalabilità non di Service Fabric.
- Assicurarsi che le risorse a cui fa riferimento il modello del set di scalabilità siano disponibili e aggiornate. 
  Ad esempio, URI della firma di accesso condiviso per il payload di bootstrap nelle proprietà di estensione della macchina virtuale, payload nell'account di archiviazione, riferimento ai segreti nel modello. 

## <a name="configure-automatic-os-image-upgrade"></a>Configurare l'aggiornamento automatico dell'immagine del sistema operativo
Per configurare l'aggiornamento automatico dell'immagine del sistema operativo, verificare che nella definizione del modello del set di scalabilità la proprietà *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* sia impostata su *true*. 

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

Nell'esempio seguente viene usata l'interfaccia della riga di comando di Azure (2.0.47 o versione successiva) per configurare gli aggiornamenti automatici per il set di scalabilità denominato *myVMSS* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli
az vmss update --name myVMSS --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```
Il supporto per la configurazione di questa proprietà tramite Azure PowerShell sarà disponibile a breve.

## <a name="using-application-health-probes"></a>Uso dei probe di integrità dell'applicazione 

Durante un aggiornamento del sistema operativo, le istanze di macchina virtuale in un set di scalabilità vengono aggiornate un batch alla volta. L'aggiornamento deve continuare solo se l'applicazione del cliente è integra nelle istanze di macchina virtuale aggiornate. È consigliabile che l'applicazione fornisca segnali di integrità al motore di aggiornamento del sistema operativo del set di scalabilità. Per impostazione predefinita, durante gli aggiornamenti del sistema operativo la piattaforma prende in considerazione lo stato di alimentazione della macchina virtuale e lo stato di provisioning dell'estensione per determinare se un'istanza di macchina virtuale è integra dopo un aggiornamento. Durante l'aggiornamento del sistema operativo di un'istanza di macchina virtuale, il disco del sistema operativo in un'istanza di macchina virtuale viene sostituito con un nuovo disco in base alla versione più recente dell'immagine. Una volta completato l'aggiornamento del sistema operativo, le estensioni configurate vengono eseguite in queste macchine virtuali. L'applicazione viene considerata integra solo dopo che è stato effettuato correttamente il provisioning di tutte le estensioni in una macchina virtuale. 

Un set di scalabilità può facoltativamente essere configurato con probe di integrità dell'applicazione per fornire alla piattaforma informazioni accurate sullo stato dell'applicazione. I probe di integrità delle applicazioni sono probe del servizio di bilanciamento del carico personalizzati usati come un segnale di integrità. L'applicazione in esecuzione in un'istanza di macchina virtuale del set di scalabilità può rispondere a richieste HTTP o TCP esterne per indicare se è integra. Per altre informazioni sul funzionamento dei probe di bilanciamento del carico personalizzati, vedere [Informazioni sui probe di bilanciamento del carico](../load-balancer/load-balancer-custom-probe-overview.md). Un probe di integrità dell'applicazione non è necessario per gli aggiornamenti automatici del sistema operativo, ma è vivamente consigliato.

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
```
> [!NOTE]
> Quando si usano gli aggiornamenti automatici del sistema operativo con Service Fabric, la nuova immagine del sistema operativo viene implementata in un dominio di aggiornamento alla volta per mantenere un'elevata disponibilità dei servizi in esecuzione in Service Fabric. Per usare gli aggiornamenti automatici del sistema operativo in Service Fabric, il cluster deve essere configurato per usare il livello di durabilità Silver o superiore. Per altre informazioni sulle caratteristiche di durabilità dei cluster di Service Fabric, vedere [questa documentazione](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Mantenere aggiornate le credenziali
Se il set di scalabilità usa credenziali per accedere alle risorse esterne, ad esempio se è configurata un'estensione della macchina virtuale che usa un token di firma di accesso condiviso per l'account di archiviazione, è necessario assicurarsi che le credenziali siano mantenute aggiornate. In caso di scadenza delle credenziali, inclusi i certificati e i token, l'aggiornamento avrà esito negativo e il primo batch di macchine virtuali verrà lasciato in uno stato di errore.

Le procedure consigliate per ripristinare le macchine virtuali e abilitare di nuovo l'aggiornamento automatico del sistema operativo se si verifica un errore di autenticazione delle risorse sono:

* Rigenerare il token (o qualsiasi altra credenziale) passato alle estensioni.
* Verificare che le credenziali usate all'interno della macchina virtuale per comunicare con le entità esterne siano aggiornate.
* Aggiornare le estensioni nel modello di set di scalabilità con eventuali nuovi token.
* Distribuire il set di scalabilità aggiornato, che aggiornerà tutte le istanze di macchina virtuale, incluse quelle in errore. 

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Ottenere la cronologia degli aggiornamenti automatici dell'immagine del sistema operativo 
È possibile controllare la cronologia dell'aggiornamento del sistema operativo più recente eseguito nel set di scalabilità con Azure PowerShell, l'interfaccia della riga di comando di Azure 2.0 o le API REST. È possibile ottenere la cronologia degli ultimi cinque tentativi di aggiornamento del sistema operativo negli ultimi due mesi.

### <a name="azure-powershell"></a>Azure PowerShell
Nell'esempio seguente viene usato Azure PowerShell per controllare lo stato per il set di scalabilità denominato *myVMSS* nel gruppo di risorse denominato *myResourceGroup*:

```powershell
Get-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0
Nell'esempio seguente viene usata l'interfaccia della riga di comando di Azure (2.0.47 o versione successiva) per controllare lo stato per il set di scalabilità denominato *myVMSS* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>API REST
Nell'esempio seguente viene usata l'API REST per controllare lo stato per il set di scalabilità denominato *myVMSS* nel gruppo di risorse denominato *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```
La documentazione per questa API è disponibile qui: https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getosupgradehistory.

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

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Come ottenere la versione più recente di un'immagine del sistema operativo della piattaforma? 

È possibile ottenere le versioni dell'immagine per gli SKU supportati tramite gli aggiornamenti automatici del sistema operativo usando gli esempi seguenti: 

```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

```powershell
Get-AzureRmVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

```azurecli
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="deploy-with-a-template"></a>Eseguire la distribuzione con un modello

È possibile usare il modello seguente per distribuire un set di scalabilità che usa gli aggiornamenti automatici <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>Aggiornamenti automatici in sequenza - Ubuntu 16.04-LTS</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>Passaggi successivi
Per altri esempi su come usare gli aggiornamenti automatici del sistema operativo con i set di scalabilità, vedere il [repository GitHub per le funzionalità di anteprima](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).

---
title: Creare un set di scalabilità di Azure che usa le zone di disponibilità | Microsoft Docs
description: Informazioni su come creare set di scalabilità di macchine virtuali di Azure che usano le zone di disponibilità per aumentare la ridondanza in caso di interruzioni
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: cynthn
ms.openlocfilehash: 32679e37062fbf77ce6ab03b6ea708c0d5eeea30
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816238"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Creare un set di scalabilità di macchine virtuali che usa le zone di disponibilità

Per proteggere i set di scalabilità di macchine virtuali dagli errori che possono verificarsi a livello di data center, è possibile creare un set di scalabilità tra le zone di disponibilità. Le aree di Azure che supportano le zone di disponibilità includono almeno tre zone distinte, ognuna dotata di risorse di alimentazione, di rete e di raffreddamento indipendenti. Per ulteriori informazioni, vedere [Overview of Availability Zones](../availability-zones/az-overview.md) (Panoramica delle zone di disponibilità in Azure).

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Quando si distribuisce un set di scalabilità in una o più zone con la versione *2017-12-01* dell'API sono disponibili le opzioni di distribuzione massima e di distribuzione statica in 5 domini di errore. Con la distribuzione massima, il set di scalabilità distribuisce le macchine virtuali nel maggior numero di domini di errore possibile all'interno di ogni zona. Questa distribuzione potrebbe interessare più o meno di cinque domini di errore per zona. Con la distribuzione statica in 5 domini di errore il set di scalabilità distribuisce le macchine virtuali esattamente in cinque domini di errore per zona. Se il set di scalabilità non trova cinque domini di errore distinti per zona per soddisfare la richiesta di allocazione, la richiesta ha esito negativo.

**È consigliabile usare la distribuzione massima per la maggior parte dei carichi di lavoro** perché questo approccio offre la migliore distribuzione nella maggior parte dei casi. Se è necessario distribuire repliche in unità di isolamento hardware distinte, è consigliabile eseguire la distribuzione in zone di disponibilità e usare l'opzione di distribuzione massima all'interno di ogni zona.

Con la distribuzione massima si vede solo un dominio di errore nella visualizzazione dell'istanza di macchina virtuale del set di scalabilità e nei metadati dell'istanza, indipendentemente dal numero di domini di errore in cui le macchine virtuali sono distribuite. La distribuzione all'interno di ogni zona è implicita.

Per usare la distribuzione massima, impostare *platformFaultDomainCount* su *1*. Per usare la distribuzione statica in cinque domini di errore, impostare *platformFaultDomainCount* su *5*. Nell'API versione *2017-12-01* il valore predefinito di *platformFaultDomainCount* è *1* per i set di scalabilità a zona singola e tra zone. Attualmente è supportata solo la distribuzione statica in cinque domini di errore per i set di scalabilità a livello di area.

### <a name="placement-groups"></a>Gruppi di posizionamento

Quando si distribuisce un set di scalabilità, è anche possibile procedere con un singolo [gruppo di posizionamento](./virtual-machine-scale-sets-placement-groups.md) per zona di disponibilità o con più gruppi per zona. Per i set di scalabilità di area, è possibile scegliere tra un singolo gruppo di posizionamento o più gruppi nell'area. Per la maggior parte dei carichi di lavoro è consigliabile usare più gruppi di posizionamento, che consentono una maggiore scalabilità. Per impostazione predefinita, nell'API versione *2017-12-01* i set di scalabilità a zona singola e tra zone usano più gruppi di posizionamento, mentre i set di scalabilità a livello di area usano un solo gruppo di posizionamento.

> [!NOTE]
> Se si usa la distribuzione massima, è necessario usare più gruppi di posizionamento.

### <a name="zone-balancing"></a>Bilanciamento delle zone

Infine, per i set di scalabilità distribuiti in più zone è anche possibile scegliere tra il bilanciamento delle zone con massimo sforzo e il bilanciamento delle zone restrittivo. Un set di scalabilità viene considerato "bilanciato" se ogni zona del set ha lo stesso numero di macchine virtuali o +\\- 1 macchine virtuali in tutte le altre zone. Ad esempio: 

- Un set di scalabilità con 2 macchine virtuali nella zona 1, 3 macchine virtuali nella zona 2 e 3 macchine virtuali nella zona 3 è considerato bilanciato. C'è solo una zona con un numero di macchine virtuali diverso e la differenza è di 1 sola in meno rispetto alle altre zone. 
- Un set di scalabilità con 1 macchina virtuale nella zona 1, 3 nella zona 2 e 3 nella zona 3 è considerato sbilanciato. La zona 1 ha 2 macchine virtuali in meno rispetto alle zone 2 e 3.

È possibile che le macchine virtuali nel set di scalabilità vengano create correttamente, ma che non sia possibile distribuire le estensioni in tali macchine virtuali. Queste macchine virtuali con errori di estensione vengono comunque conteggiate per determinare se un set di scalabilità è bilanciato. Ad esempio, un set di scalabilità con 3 macchine virtuali nella zona 1, 3 nella zona 2 e 3 nella zona 3 viene considerato bilanciato anche se tutte le estensioni non sono riuscite nella zona 1 e tutte le estensioni sono riuscite nelle zone 2 e 3.

Con il bilanciamento delle zone con massimo sforzo, il set di scalabilità tenta di aumentare e ridurre il numero di macchine virtuali mantenendo il bilanciamento. Tuttavia, se per qualche motivo questo non è possibile (ad esempio se una zona diventa inattiva, impedendo al set di scalabilità di creare una nuova macchina virtuale in tale zona), il set di scalabilità consente uno squilibrio temporaneo per poter aumentare o ridurre il numero di macchine virtuali correttamente. Con i successivi tentativi di aumento, il set di scalabilità aggiunge macchine virtuali alle zone che ne richiedono di più per bilanciare il set di scalabilità. Allo stesso modo, con i successivi tentativi di riduzione, il set di scalabilità rimuove macchine virtuali dalle zone che ne richiedono di meno per bilanciare il set di scalabilità. Con il bilanciamento delle zone restrittivo, qualsiasi tentativo di aumento o riduzione da parte del set di scalabilità ha esito negativo, in quanto così facendo si creerebbe uno squilibrio.

Per usare il bilanciamento delle zone con massimo sforzo, impostare *zoneBalance* su *false*. Questa è l'impostazione predefinita nell'API versione *2017-12-01*. Per usare il bilanciamento delle zone restrittivo, impostare *zoneBalance* su *true*.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Set di scalabilità a zona singola o con ridondanza della zona

Quando si distribuisce un set di scalabilità di macchine virtuali, è possibile scegliere di usare una sola zona di disponibilità in un'area o più zone.

Quando si crea un set di scalabilità in una sola zona, la zona in cui tutte le istanze delle macchine virtuali vengono eseguite è sotto controllo e il set di scalabilità viene gestito e ridimensionato automaticamente solo all'interno di questa zona. Un set di scalabilità con ridondanza della zona consente di creare un solo set di scalabilità che include più zone. Quando vengono create le istanze delle macchine virtuali, per impostazione predefinita vengono bilanciate equamente tra le diverse zone. Se si verifica un'interruzione in una di queste zone, un set di scalabilità non aumenta automaticamente le istanze per incrementare la capacità. Una procedura consigliata è quella di configurare regole di scalabilità automatica in base alla CPU o all'utilizzo della memoria. Le regole di scalabilità automatica consentono al set di scalabilità di rispondere a una perdita delle istanze delle macchine virtuali in una zona aumentando il numero di istanze nelle restanti zone operative.

Per usare le zone di disponibilità, è necessario creare il set di scalabilità in un'[area di Azure supportata](../availability-zones/az-overview.md#regions-that-support-availability-zones). È possibile creare un set di scalabilità che usa le zone di disponibilità in uno dei modi seguenti:

- [Portale di Azure](#use-the-azure-portal)
- Interfaccia della riga di comando di Azure
- [Azure PowerShell](#use-azure-powershell)
- [Modelli di Gestione risorse di Azure](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

La procedura per creare un set di scalabilità che usa una zona di disponibilità è identica a quella descritta in dettaglio nell'[articolo introduttivo](quick-create-portal.md). Quando si seleziona un'area di Azure supportata, è possibile creare un set di scalabilità in una o più zone tra quelle disponibili, come illustrato nell'esempio seguente:

![Creare un set di scalabilità in una sola zona di disponibilità](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

Il set di scalabilità e le risorse di supporto, ad esempio Azure Load Balancer e l'indirizzo IP pubblico, vengono creati nella sola zona specificata.

## <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

La procedura per creare un set di scalabilità che usa una zona di disponibilità è identica a quella descritta in dettaglio nell'[articolo introduttivo](quick-create-cli.md). Per usare le zone di disponibilità, è necessario creare il set di scalabilità in un'area di Azure supportata.

Aggiungere il parametro `--zones` al comando [az vmss create](/cli/azure/vmss) e specificare quale zona usare, ad esempio la zona *1*, *2* o *3*. L'esempio seguente crea un set di scalabilità a zona singola denominato *myScaleSet* nella zona *1*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

Per un esempio completo di un set di scalabilità a zona singola e delle relative risorse di rete, vedere [questo script di esempio dell'interfaccia della riga di comando](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh.).

### <a name="zone-redundant-scale-set"></a>Set di scalabilità con ridondanza della zona

Per creare un set di scalabilità con ridondanza della zona, si usano un indirizzo IP pubblico e un servizio di bilanciamento del carico dello SKU *Standard*. Per una ridondanza ottimizzata, lo SKU *Standard* crea risorse di rete con ridondanza della zona. Per altre informazioni, vedere [Panoramica di Azure Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md) e [Load Balancer Standard e zone di disponibilità](../load-balancer/load-balancer-standard-availability-zones.md).

Per creare un set di scalabilità con ridondanza della zona, specificare più zone nel parametro `--zones`. L'esempio seguente crea un set di scalabilità con ridondanza della zona denominato *myScaleSet* nelle zone *1,2,3*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1 2 3
```

Le operazioni da eseguire per creare e configurare tutte le macchine virtuali e le risorse del set di scalabilità nelle zone specificate richiedono alcuni minuti. Per un esempio completo di un set di scalabilità con ridondanza della zona e delle relative risorse di rete, vedere [questo script di esempio dell'interfaccia della riga di comando](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh).

## <a name="use-azure-powershell"></a>Usare Azure PowerShell

Per usare le zone di disponibilità, è necessario creare il set di scalabilità in un'area di Azure supportata. Aggiungere il parametro `-Zone` al comando [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) e specificare quale zona usare, ad esempio la zona *1*, *2* o *3*.

L'esempio seguente crea un set di scalabilità a zona singola denominato *myScaleSet* nella zona *1* dell'area *East US 2*. Vengono create automaticamente le risorse di rete di Azure per la rete virtuale, l'indirizzo IP pubblico e il bilanciamento del carico. Quando richiesto, fornire le proprie credenziali amministrative desiderate per le istanze di macchina virtuale nel set di scalabilità:

```powershell
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1"
```

### <a name="zone-redundant-scale-set"></a>Set di scalabilità con ridondanza della zona

Per creare un set di scalabilità con ridondanza della zona, specificare più zone nel parametro `-Zone`. L'esempio seguente crea un set di scalabilità con ridondanza della zona denominato *myScaleSet* nelle zone *1, 2, 3* dell'area *East US 2*. Vengono create automaticamente le risorse di rete di Azure con ridondanza della zona per la rete virtuale, l'indirizzo IP pubblico e il bilanciamento del carico. Quando richiesto, fornire le proprie credenziali amministrative desiderate per le istanze di macchina virtuale nel set di scalabilità:

```powershell
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1", "2", "3"
```

## <a name="use-azure-resource-manager-templates"></a>Usare i modelli di Gestione risorse di Azure

La procedura per creare un set di scalabilità che usa una zona di disponibilità è identica a quella descritta in dettaglio nell'articolo introduttivo per [Linux](quick-create-template-linux.md) o [Windows](quick-create-template-windows.md). Per usare le zone di disponibilità, è necessario creare il set di scalabilità in un'area di Azure supportata. Aggiungere la proprietà `zones` al tipo di risorsa *Microsoft.Compute/virtualMachineScaleSets* nel modello e specificare quale zona usare, ad esempio la zona *1*, *2* o *3*.

L'esempio seguente crea un set di scalabilità a zona singola per Linux denominato *myScaleSet* nella zona *1* dell'area *East US 2*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

Per un esempio completo di un set di scalabilità a zona singola e delle relative risorse di rete, vedere [questo modello di esempio di Resource Manager](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json).

### <a name="zone-redundant-scale-set"></a>Set di scalabilità con ridondanza della zona

Per creare un set di scalabilità con ridondanza della zona, specificare più valori nella proprietà `zones` del tipo di risorsa *Microsoft.Compute/virtualMachineScaleSets*. L'esempio seguente crea un set di scalabilità con ridondanza della zona denominato *myScaleSet* nelle zone *1,2,3* dell'area *East US 2*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

Se si crea un indirizzo IP pubblico o un servizio di bilanciamento del carico, specificare la proprietà *"sku": {"name": "Standard"} "* per creare risorse di rete con ridondanza della zona. È anche necessario creare un gruppo di sicurezza di rete e le relative regole per consentire ogni tipo di traffico. Per altre informazioni, vedere [Panoramica di Azure Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md) e [Load Balancer Standard e zone di disponibilità](../load-balancer/load-balancer-standard-availability-zones.md).

Per un esempio completo di un set di scalabilità con ridondanza della zona e delle relative risorse di rete, vedere [questo modello di esempio di Resource Manager](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un set di scalabilità in una zona di disponibilità, è possibile visualizzare informazioni su come [distribuire le applicazioni nei set di scalabilità di macchine virtuali](tutorial-install-apps-cli.md) o come [usare la scalabilità automatica con i set di scalabilità di macchine virtuali](tutorial-autoscale-cli.md).

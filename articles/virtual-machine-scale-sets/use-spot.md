---
title: Creare un set di scalabilità che usa VM di Azure spot (anteprima)
description: Informazioni su come creare set di scalabilità di macchine virtuali di Azure che usano macchine virtuali spot per risparmiare sui costi.
services: virtual-machine-scale-sets
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/23/2019
ms.author: cynthn
ms.openlocfilehash: 68315b1b0d290b107fe2d28a9e3b49be009b78b8
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782242"
---
# <a name="preview-azure-spot-vms-for-virtual-machine-scale-sets"></a>Anteprima: VM di Azure spot per i set di scalabilità di macchine virtuali 

L'uso di Azure spot nei set di scalabilità consente di sfruttare i vantaggi della capacità inutilizzata con un notevole risparmio sui costi. In qualsiasi momento, quando Azure necessita della capacità, l'infrastruttura di Azure eliminerà le istanze di spot. Le istanze di spot sono quindi ottime per i carichi di lavoro in grado di gestire le interruzioni, ad esempio processi di elaborazione batch, ambienti di sviluppo/test, carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

La quantità di capacità disponibile può variare in base alle dimensioni, all'area, all'ora del giorno e così via. Quando si distribuiscono istanze di spot nei set di scalabilità, Azure lo alloca solo se è disponibile una capacità, ma non esiste alcun contratto di contratto per queste istanze. Un set di scalabilità spot viene distribuito in un singolo dominio di errore e non offre garanzie di disponibilità elevata.

> [!IMPORTANT]
> Le istanze di spot sono attualmente in anteprima pubblica.
> Questa versione di anteprima non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Per la prima parte dell'anteprima pubblica, le istanze di spot avranno un prezzo fisso, quindi non vi saranno eliminazioni basate sul prezzo.

## <a name="pricing"></a>Prezzi

I prezzi per le istanze di spot sono variabili in base all'area e allo SKU. Per altre informazioni, vedere prezzi per [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


Con i prezzi variabili è possibile impostare un prezzo massimo, in dollari statunitensi (USD), usando un massimo di 5 cifre decimali. Ad esempio, il valore `0.98765`sarebbe un prezzo massimo di $0,98765 USD all'ora. Se si imposta il prezzo massimo da `-1`, l'istanza non verrà rimossa in base al prezzo. Il prezzo per l'istanza sarà il prezzo corrente per spot o il prezzo di un'istanza standard, che sempre è inferiore, purché siano disponibili capacità e quota.

## <a name="eviction-policy"></a>Criteri di rimozione

Quando si creano set di scalabilità spot, è possibile impostare i criteri di rimozione per *deallocare* (impostazione predefinita) o *eliminare*. 

Il criterio *deallocate* sposta le istanze eliminate nello stato arrestato-deallocato, consentendo di ridistribuire le istanze eliminate. Tuttavia, non è garantito che l'allocazione avrà esito positivo. Le macchine virtuali deallocate verranno incluse nel conteggio per la quota delle istanze del set di scalabilità e verranno addebitati i costi dei dischi sottostanti. 

Se si desidera che le istanze nel set di scalabilità spot vengano eliminate al momento della rimozione, è possibile impostare i criteri di rimozione da *eliminare*. Con i criteri di rimozione impostati per l'eliminazione è quindi possibile creare nuove macchine virtuali aumentando il valore della proprietà del numero di istanze di set di scalabilità. Le macchine virtuali rimosse vengono eliminate insieme ai relativi dischi sottostanti, quindi non verrà addebitato alcun costo per l'archiviazione. Si può anche usare la funzionalità di ridimensionamento automatico dei set di scalabilità per cercare di compensare automaticamente le macchine virtuali rimosse, tuttavia non esiste alcuna garanzia di successo dell'allocazione. Si consiglia di usare la funzionalità di scalabilità automatica solo nei set di scalabilità di punti quando si impostano i criteri di rimozione da eliminare per evitare il costo dei dischi e raggiungere i limiti di quota. 

Gli utenti possono acconsentire esplicitamente a ricevere notifiche in-VM tramite [Azure eventi pianificati](../virtual-machines/linux/scheduled-events.md). In questo modo si riceverà una notifica se le macchine virtuali vengono eliminate e saranno disponibili 30 secondi per completare i processi ed eseguire le attività di arresto prima dell'eliminazione. 


## <a name="deploying-spot-vms-in-scale-sets"></a>Distribuzione di VM spot nei set di scalabilità

Per distribuire le VM spot nei set di scalabilità, è possibile impostare il nuovo flag di *priorità* su *spot*. Tutte le macchine virtuali nel set di scalabilità verranno impostate su spot. Per creare un set di scalabilità con macchine virtuali spot, usare uno dei metodi seguenti:
- [Azure portal](#portal)
- [interfaccia della riga di comando di Azure](#azure-cli)
- [Azure PowerShell](#powershell)
- [Modelli di Gestione risorse di Azure](#resource-manager-templates)

## <a name="portal"></a>di Microsoft Azure

Il processo per creare un set di scalabilità che usa le VM spot è identico a quello descritto nell' [articolo introduttivo](quick-create-portal.md). Quando si distribuisce un set di scalabilità, è possibile scegliere di impostare il flag spot e i criteri di rimozione: ![creare un set di scalabilità con macchine virtuali spot](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Il processo di creazione di un set di scalabilità con macchine virtuali spot è identico a quello descritto nell' [articolo introduttivo](quick-create-cli.md). È sufficiente aggiungere "--Priority spot" e aggiungere `--max-price`. In questo esempio si usa `-1` per `--max-price` in modo che l'istanza non venga rimossa in base al prezzo.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

Il processo di creazione di un set di scalabilità con macchine virtuali spot è identico a quello descritto nell' [articolo introduttivo](quick-create-powershell.md).
È sufficiente aggiungere "-Priority spot" e fornire un `-max-price` al [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Modelli di Resource Manager

Il processo per creare un set di scalabilità che usa le VM spot è identico a quello descritto nell'articolo introduttivo per [Linux](quick-create-template-linux.md) o [Windows](quick-create-template-windows.md). Aggiungere la proprietà' priority ' al tipo di risorsa *Microsoft. Compute/virtualMachineScaleSets/virtualMachineProfile* nel modello e specificare *spot* come valore. Assicurarsi di usare la versione dell'API *2019-03-01* o successiva. 

Per impostare i criteri di rimozione per l'eliminazione, aggiungere il parametro "evictionPolicy" e impostarlo su *delete*.

Nell'esempio seguente viene creato un set di scalabilità di *Linux denominato set* di scalabilità in *Stati Uniti centro-occidentali*, che *eliminerà* le VM nel set di scalabilità durante la rimozione:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2019-03-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Spot",
       "evictionPolicy": "delete",
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
## <a name="faq"></a>FAQ

**D:** Una volta creato, è un'istanza di spot uguale all'istanza standard?

**R:** Sì, ad eccezione del fatto che non esiste alcun contratto di contratto per le macchine virtuali spot e che possono essere rimossi in qualsiasi momento.


**D:** Cosa fare quando si viene eliminati, ma è ancora necessaria una capacità?

**R:** È consigliabile usare VM standard anziché macchine virtuali spot se è necessaria immediatamente la capacità.


**D:** Come viene gestita la quota per spot?

**R:** Le istanze di spot e le istanze standard avranno pool di quote distinti. La quota spot verrà condivisa tra le macchine virtuali e le istanze del set di scalabilità. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-subscription-service-limits).


**D:** È possibile richiedere una quota aggiuntiva per spot?

**R:** Sì, sarà possibile inviare la richiesta per aumentare la quota per le VM spot tramite il [processo standard di richiesta di quota](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).


**D:** È possibile convertire I set di scalabilità esistenti in set di scalabilità spot?

**R:** No, l'impostazione del flag di `Spot` è supportata solo in fase di creazione.


**D:** Se si usa `low` per i set di scalabilità con priorità bassa, è necessario iniziare a usare invece `Spot`?

**R:** Per ora, sia `low` che `Spot` funzioneranno, ma è necessario iniziare a eseguire la transizione a utilizzando `Spot`.


**D:** È possibile creare un set di scalabilità con macchine virtuali normali e macchine virtuali spot?

**R:** No, un set di scalabilità non può supportare più di un tipo di priorità.


**D:**  È possibile usare la scalabilità automatica con i set di scalabilità spot?

**R:** Sì, è possibile impostare le regole di scalabilità automatica per il set di scalabilità di spot. Se le macchine virtuali vengono eliminate, la scalabilità automatica può tentare di creare nuove VM spot. Tuttavia, tenere presente che questa capacità potrebbe non funzionare. 


**D:**  La scalabilità automatica funziona con entrambi i criteri di rimozione (deallocazione ed eliminazione)?

**R:** È consigliabile impostare i criteri di rimozione per l'eliminazione quando si usa la scalabilità automatica. Questo avviene perché le istanze appena deallocate sono incluse nel calcolo delle capacità nel set di scalabilità. Quando si usa la scalabilità automatica, probabilmente verrà raggiunto rapidamente il numero di istanze di destinazione a causa delle istanze deallocate e rimosse. 


**D:** Quali canali supportano le VM spot?

**R:** Vedere la tabella seguente per la disponibilità della macchina virtuale spot.

<a name="channel"></a>

| Canali di Azure               | Disponibilità di macchine virtuali di Azure spot       |
|------------------------------|-----------------------------------|
| Contratto Enterprise Agreement         | SÌ                               |
| Pagamento in base al consumo                | SÌ                               |
| Provider di servizi cloud | [Contatta il tuo partner](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Vantaggi                     | Non disponibile                     |
| Eventi sponsorizzati                    | Non disponibile                     |
| Versione di valutazione gratuita                   | Non disponibile                     |


**D:** Dove è possibile pubblicare domande?

**R:** È possibile pubblicare e contrassegnare la domanda con `azure-spot` a [Q & a](https://docs.microsoft.com/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Passaggi successivi
Ora che è stato creato un set di scalabilità con le VM spot, provare a distribuire il [modello di scalabilità automatica usando Spot](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Per informazioni sui prezzi, vedere la [pagina dei prezzi dei set di scalabilità di macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/).

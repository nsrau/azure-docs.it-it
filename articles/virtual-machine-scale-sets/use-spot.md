---
title: Creare un set di scalabilità che usa VM di Azure spot
description: Informazioni su come creare set di scalabilità di macchine virtuali di Azure che usano macchine virtuali spot per risparmiare sui costi.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 03/25/2020
ms.reviewer: jagaveer
ms.custom: jagaveer, devx-track-azurecli
ms.openlocfilehash: de8cfa66d6d52fe16cc40c5df0f41a39fff134fd
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832638"
---
# <a name="azure-spot-vms-for-virtual-machine-scale-sets"></a>VM di Azure spot per i set di scalabilità di macchine virtuali 

L'uso di Azure spot nei set di scalabilità consente di sfruttare i vantaggi della capacità inutilizzata con un notevole risparmio sui costi. In qualsiasi momento, quando Azure necessita della capacità, l'infrastruttura di Azure eliminerà le istanze di spot. Le istanze di spot sono quindi ottime per i carichi di lavoro in grado di gestire le interruzioni, ad esempio processi di elaborazione batch, ambienti di sviluppo/test, carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

La quantità di capacità disponibile dipende dalle dimensioni, dall'area, dal momento della giornata e da altri fattori. Quando si distribuiscono istanze di spot nei set di scalabilità, Azure lo alloca solo se è disponibile una capacità, ma non esiste alcun contratto di contratto per queste istanze. Un set di scalabilità spot viene distribuito in un singolo dominio di errore e non offre garanzie di disponibilità elevata.


## <a name="pricing"></a>Prezzi

I prezzi per le istanze di spot sono variabili in base all'area e allo SKU. Per altre informazioni, vedere prezzi per [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


Con i prezzi variabili è possibile impostare un prezzo massimo, in dollari statunitensi (USD), usando al massimo 5 cifre decimali. Ad esempio, il valore `0.98765`sarebbe un prezzo massimo di 0,98765 USD all'ora. Se si imposta il prezzo massimo `-1` , l'istanza non verrà rimossa in base al prezzo. Il prezzo per l'istanza sarà il prezzo corrente per spot o il prezzo di un'istanza standard, che sempre è inferiore, purché siano disponibili capacità e quota.

## <a name="eviction-policy"></a>Criteri di rimozione

Quando si creano set di scalabilità spot, è possibile impostare i criteri di rimozione per *deallocare* (impostazione predefinita) o *eliminare*. 

Il criterio *deallocate* sposta le istanze eliminate nello stato arrestato-deallocato, consentendo di ridistribuire le istanze eliminate. Tuttavia, non è garantito che l'allocazione avrà esito positivo. Le macchine virtuali deallocate verranno incluse nel conteggio per la quota delle istanze del set di scalabilità e verranno addebitati i costi dei dischi sottostanti. 

Se si desidera che le istanze nel set di scalabilità spot vengano eliminate al momento della rimozione, è possibile impostare i criteri di rimozione da *eliminare*. Con i criteri di rimozione impostati per l'eliminazione è quindi possibile creare nuove macchine virtuali aumentando il valore della proprietà del numero di istanze di set di scalabilità. Le macchine virtuali rimosse vengono eliminate insieme ai relativi dischi sottostanti, quindi non verrà addebitato alcun costo per l'archiviazione. Si può anche usare la funzionalità di ridimensionamento automatico dei set di scalabilità per cercare di compensare automaticamente le macchine virtuali rimosse, tuttavia non esiste alcuna garanzia di successo dell'allocazione. Si consiglia di usare la funzionalità di scalabilità automatica solo nei set di scalabilità di punti quando si impostano i criteri di rimozione da eliminare per evitare il costo dei dischi e raggiungere i limiti di quota. 

Gli utenti possono acconsentire esplicitamente a ricevere notifiche nelle macchine virtuali con [Eventi pianificati di Azure](../virtual-machines/linux/scheduled-events.md). In questo modo riceveranno una notifica se le macchine virtuali vengono eliminate e avranno a disposizione 30 secondi per completare i processi ed eseguire le attività di arresto prima dell'eliminazione. 

## <a name="placement-groups"></a>Gruppi di posizionamento
Il gruppo di posizionamento è un costrutto simile a un set di disponibilità di Azure, con domini di errore e domini di aggiornamento propri. Per impostazione predefinita, un set di scalabilità è costituito da un singolo gruppo di posizionamento con una dimensione massima di 100 VM. Se la proprietà del set di scalabilità denominata `singlePlacementGroup` è impostata su *false*, il set di scalabilità può essere composto da più gruppi di posizionamento e presenta un intervallo di 0-1000 VM. 

> [!IMPORTANT]
> A meno che non si usi InfiniBand con HPC, si consiglia vivamente di impostare la proprietà del set `singlePlacementGroup` di scalabilità su *false* per abilitare più gruppi di posizionamento per una migliore scalabilità nell'area o nella zona. 

## <a name="deploying-spot-vms-in-scale-sets"></a>Distribuzione di VM spot nei set di scalabilità

Per distribuire le VM spot nei set di scalabilità, è possibile impostare il nuovo flag di *priorità* su *spot*. Tutte le macchine virtuali nel set di scalabilità verranno impostate su spot. Per creare un set di scalabilità con macchine virtuali spot, usare uno dei metodi seguenti:
- [Azure portal](#portal)
- [Interfaccia della riga di comando di Azure](#azure-cli)
- [Azure PowerShell](#powershell)
- [Modelli di Gestione risorse di Azure](#resource-manager-templates)

## <a name="portal"></a>Portale

Il processo per creare un set di scalabilità che usa le VM spot è identico a quello descritto nell' [articolo introduttivo](quick-create-portal.md). Quando si distribuisce un set di scalabilità, è possibile scegliere di impostare il flag spot e i criteri di rimozione: ![ creare un set di scalabilità con le VM spot](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Il processo di creazione di un set di scalabilità con macchine virtuali spot è identico a quello descritto nell' [articolo introduttivo](quick-create-cli.md). È sufficiente aggiungere "--Priority spot" e aggiungere `--max-price` . In questo esempio viene usato `-1` per, `--max-price` quindi l'istanza non verrà rimossa in base al prezzo.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --single-placement-group false \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

Il processo di creazione di un set di scalabilità con macchine virtuali spot è identico a quello descritto nell' [articolo introduttivo](quick-create-powershell.md).
È sufficiente aggiungere "-Priority spot" e fornire un oggetto `-max-price` a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Modelli di Gestione risorse

Il processo per creare un set di scalabilità che usa le VM spot è identico a quello descritto nell'articolo introduttivo per [Linux](quick-create-template-linux.md) o [Windows](quick-create-template-windows.md). 

Per le distribuzioni di modelli di spot, usare `"apiVersion": "2019-03-01"` o versione successiva. 

Aggiungere le `priority` `evictionPolicy` proprietà, e `billingProfile` alla sezione `"virtualMachineProfile":` e la `"singlePlacementGroup": false,` proprietà alla `"Microsoft.Compute/virtualMachineScaleSets"` sezione nel modello:

```json

{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  },
  "properties": {
    "singlePlacementGroup": false,
    }

        "virtualMachineProfile": {
              "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            },
```

Per eliminare l'istanza dopo che è stata rimossa, impostare il `evictionPolicy` parametro su `Delete` .

## <a name="faq"></a>Domande frequenti

**D:** Una volta creato, è un'istanza di spot uguale all'istanza standard?

**R:** Sì, ad eccezione del fatto che per le macchine virtuali spot non esiste un contratto di servizio e che possono essere eliminate in qualsiasi momento.


**D:** Cosa si può fare se la VM viene eliminata, ma è ancora necessaria capacità?

**R:** È consigliabile usare le macchine virtuali standard anziché le macchine virtuali spot se si ha immediatamente bisogno di capacità.


**D:** Come viene gestita la quota per spot?

**R:** Le istanze di spot e le istanze standard avranno pool di quote distinti. La quota spot verrà condivisa tra le macchine virtuali e le istanze del set di scalabilità. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md).


**D:** È possibile richiedere una quota aggiuntiva per le macchine virtuali spot?

**R:** Sì, è possibile inviare la richiesta di aumento della quota per le macchine virtuali spot attraverso il [processo di richiesta di quota standard](../azure-portal/supportability/per-vm-quota-requests.md).


**D:** È possibile convertire I set di scalabilità esistenti in set di scalabilità spot?

**R:** No, l'impostazione del `Spot` flag è supportata solo in fase di creazione.


**D:** Se si usa `low` per i set di scalabilità con priorità bassa, è necessario iniziare a usare `Spot` invece?

**R:** Per il momento, sia che `low` `Spot` funzioneranno, ma è necessario iniziare a eseguire la transizione a usando `Spot` .


**D:** È possibile creare un set di scalabilità con macchine virtuali normali e macchine virtuali spot?

**R:** No, un set di scalabilità non può supportare più di un tipo di priorità.


**D:**  È possibile usare la scalabilità automatica con i set di scalabilità spot?

**R:** Sì, è possibile impostare le regole di scalabilità automatica per il set di scalabilità di spot. Se le macchine virtuali vengono eliminate, la scalabilità automatica può tentare di creare nuove VM spot. Tuttavia, tenere presente che questa capacità potrebbe non funzionare. 


**D:**  La scalabilità automatica funziona con entrambi i criteri di rimozione (deallocazione ed eliminazione)?

**R:** È consigliabile impostare i criteri di rimozione per l'eliminazione quando si usa la scalabilità automatica. Questo avviene perché le istanze appena deallocate sono incluse nel calcolo delle capacità nel set di scalabilità. Quando si usa la scalabilità automatica, probabilmente verrà raggiunto rapidamente il numero di istanze di destinazione a causa delle istanze deallocate e rimosse. 


**D:** Quali canali supportano le macchine virtuali spot?

**R:** Vedere la tabella seguente per la disponibilità di macchine virtuali spot.

<a name="channel"></a>

| Canali di Azure               | Disponibilità di macchine virtuali spot di Azure       |
|------------------------------|-----------------------------------|
| Enterprise Agreement         | Sì                               |
| Pagamento in base al consumo                | Sì                               |
| Provider di servizi cloud | [Contattare il partner](/partner-center/azure-plan-get-started) |
| Vantaggi                     | Non disponibile                     |
| Sponsorizzato                    | Sì                               |
| Versione di valutazione gratuita                   | Non disponibile                     |


**D:** Dove è possibile pubblicare le domande?

**R:** È possibile pubblicare e contrassegnare la domanda con `azure-spot` in [Domande e risposte](/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui prezzi, vedere la [pagina dei prezzi dei set di scalabilità di macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/).

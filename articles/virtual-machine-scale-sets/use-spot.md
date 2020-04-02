---
title: Creare un set di scalabilità che usa le macchine virtuali di Azure SpotCreate a scale set that uses Azure Spot VMs
description: Informazioni su come creare set di scalabilità di macchine virtuali di Azure che usano le macchine virtuali Spot per risparmiare sui costi.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: a7bd22032a554c83a2ea2323ffdb3ae52dfe4faf
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545945"
---
# <a name="azure-spot-vms-for-virtual-machine-scale-sets"></a>Macchine virtuali di Azure Spot per set di scalabilità di macchine virtualiAzure Spot VMs for virtual machine scale sets 

L'uso di Azure Spot su set di scalabilità consente di sfruttare la capacità inutilizzata con un notevole risparmio sui costi. In qualsiasi momento in cui Azure richiede la capacità, l'infrastruttura di Azure rimuovere le istanze Spot.At any in time when Azure needs the capacity back, the Azure infrastructure will evict Spot instances. Di conseguenza, le istanze Spot sono ideali per i carichi di lavoro in grado di gestire interruzioni come processi di elaborazione batch, ambienti di sviluppo/test, carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

La quantità di capacità disponibile può variare in base alle dimensioni, all'area, all'ora del giorno e altro ancora. Quando si distribuiscono istanze Spot nei set di scalabilità, Azure allocherà l'istanza solo se è disponibile capacità, ma non è disponibile alcun servizio di archiviazione per queste istanze. Un set di scalabilità Spot viene distribuito in un singolo dominio di errore e non offre garanzie di disponibilità elevata.


## <a name="pricing"></a>Prezzi

Il prezzo per le istanze Spot è variabile, in base all'area e all'SKU. Per ulteriori informazioni, vedere Prezzi per [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


Con prezzi variabili, hai la possibilità di impostare un prezzo massimo, in dollari USA (USD), utilizzando fino a 5 cifre decimali. Ad esempio, `0.98765`il valore sarebbe un prezzo massimo di 0,98765 USD all'ora. Se si imposta il `-1`prezzo massimo su , l'istanza non verrà rimossa in base al prezzo. Il prezzo per l'istanza sarà il prezzo corrente per Spot o il prezzo per un'istanza standard, che è sempre inferiore, purché vi siano capacità e quote disponibili.

## <a name="eviction-policy"></a>Politica di sfratto

Quando si creano set di scale Spot, è possibile impostare i criteri di rimozione su *Deal(impostazione* predefinita) o *Elimina*. 

Il criterio *Deallocate* sposta le istanze rimosse allo stato di arresto deallocato consentendo di ridistribuire le istanze rimosse. Tuttavia, non è garantito che l'allocazione avrà esito positivo. Le macchine virtuali deallocate verranno incluse nel conteggio per la quota delle istanze del set di scalabilità e verranno addebitati i costi dei dischi sottostanti. 

Se si desidera che le istanze nel set di scalabilità Spot vengano eliminate quando vengono rimosse, è possibile impostare i criteri di rimozione per *l'eliminazione*. Con i criteri di rimozione impostati per l'eliminazione è quindi possibile creare nuove macchine virtuali aumentando il valore della proprietà del numero di istanze di set di scalabilità. Le macchine virtuali rimosse vengono eliminate insieme ai relativi dischi sottostanti, quindi non verrà addebitato alcun costo per l'archiviazione. Si può anche usare la funzionalità di ridimensionamento automatico dei set di scalabilità per cercare di compensare automaticamente le macchine virtuali rimosse, tuttavia non esiste alcuna garanzia di successo dell'allocazione. Si consiglia di utilizzare la funzionalità di scalabilità automatica nei set di scalabilità Spot solo quando si imposta il criterio di rimozione da eliminare per evitare il costo dei dischi e il limite di quota. 

Gli utenti possono acconsentire esplicitamente a ricevere notifiche nella macchina virtuale tramite [gli eventi pianificati](../virtual-machines/linux/scheduled-events.md)di Azure. In questo modo verrà notificato se le macchine virtuali vengono rimosse e si avranno 30 secondi per completare i processi ed eseguire le attività di arresto prima dello sfratto. 


## <a name="deploying-spot-vms-in-scale-sets"></a>Distribuzione di macchine virtuali Spot in set di scalabilitàDeploying Spot VMs in scale sets

Per distribuire macchine virtuali Spot nei set di scalabilità, è possibile impostare il nuovo flag *Priorità* su *Spot*. Tutte le macchine virtuali nel set di scalabilità verranno impostate su Individua.All VMs in your scale set will be set to Spot. Per creare un set di scalabilità con macchine virtuali Spot, usare uno dei metodi seguenti:To create a scale set with Spot VMs, use one of the following methods:
- [Portale di Azure](#portal)
- [Interfaccia della riga di comando di Azure](#azure-cli)
- [Azure PowerShell](#powershell)
- [Modelli di Gestione risorse di Azure](#resource-manager-templates)

## <a name="portal"></a>Portale

Il processo per creare un set di scalabilità che usa macchine virtuali Spot è lo stesso descritto [nell'articolo introduttivo.](quick-create-portal.md) Quando si distribuisce un set di scalabilità, è possibile scegliere di ![impostare il flag Spot e il criterio di rimozione: Creare un set di scalabilità con macchine virtuali SpotWhen you are deploying a scale set, you can choose to set the Spot flag, and the sviction policy: Create a scale set with Spot VMs](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Il processo per creare un set di scalabilità con macchine virtuali Spot è lo stesso descritto [nell'articolo introduttivo](quick-create-cli.md). Basta aggiungere il '--Priority Spot' e aggiungere `--max-price`. In questo esempio, `-1` `--max-price` usiamo per in modo che l'istanza non venga rimossa in base al prezzo.

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

Il processo per creare un set di scalabilità con macchine virtuali Spot è lo stesso descritto [nell'articolo introduttivo](quick-create-powershell.md).
È sufficiente aggiungere '-Priority Spot' e fornire a `-max-price` a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

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

Il processo per creare un set di scalabilità che usa macchine virtuali Spot è lo stesso descritto nell'articolo introduttivo per [Linux](quick-create-template-linux.md) o [Windows.](quick-create-template-windows.md) 

Per le distribuzioni`"apiVersion": "2019-03-01"` di modelli Spot, usare o versione successiva. Aggiungere `priority`le `evictionPolicy` `billingProfile` proprietà e `"virtualMachineProfile":` alla sezione del modello: 

```json
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
```

Per eliminare l'istanza dopo che è `evictionPolicy` stata `Delete`rimossa, modificare il parametro in .

## <a name="faq"></a>Domande frequenti

**D:** Una volta creata, un'istanza Spot è uguale a un'istanza standard?

**A:** Sì, ad eccezione del fatto che non esiste alcun servizio di controllo di servizio per le macchine virtuali Spot e che possono essere rimosse in qualsiasi momento.


**D:** Cosa fare quando si viene sfrattati, ma si ha ancora bisogno di capacità?

**A:** È consigliabile usare macchine virtuali standard anziché macchine virtuali Spot se è necessaria immediatamente la capacità.


**D:** Come viene gestita la quota per Spot?

**A:** Le istanze spot e standard diranno pool di quote separati. La quota spot verrà condivisa tra le macchine virtuali e le istanze con set di scalabilità. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**D:** Posso richiedere una quota aggiuntiva per Spot?

**A:** Sì, sarà possibile inviare la richiesta per aumentare la quota per le macchine virtuali Spot tramite il processo di richiesta di [quota standard.](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)


**D:** È possibile convertire i set di scale esistenti in set di scale Spot?

**A:** No, l'impostazione del `Spot` flag è supportata solo al momento della creazione.


**D:** Se si `low` usava per set di scalabilità con `Spot` priorità bassa, è necessario iniziare a usare?

**A:** Per ora, `low` `Spot` entrambi e funzionerà, ma si `Spot`dovrebbe iniziare la transizione all'utilizzo di .


**D:** È possibile creare un set di scalabilità sia con macchine virtuali normali che con macchine virtuali Spot?

**A:** No, un set di scalabilità non può supportare più di un tipo di priorità.


**D:**  È possibile usare la scalabilità automatica con i set di scalabilità Spot?

**A:** Sì, è possibile impostare le regole di scalabilità automatica nel set di scalabilità Spot.Yes, you can set autoscaling rules on your Spot scale set. Se le macchine virtuali vengono rimosse, la scalabilità automatica può tentare di creare nuove macchine virtuali Spot.If your VMs are evicted, autoscale can try to create new Spot VMs. Tuttavia, tenere presente che questa capacità potrebbe non funzionare. 


**D:**  La scalabilità automatica funziona con entrambi i criteri di rimozione (deallocazione ed eliminazione)?

**A:** Si consiglia di impostare i criteri di rimozione per l'eliminazione quando si utilizza la scalabilità automatica. Questo avviene perché le istanze appena deallocate sono incluse nel calcolo delle capacità nel set di scalabilità. Quando si usa la scalabilità automatica, probabilmente verrà raggiunto rapidamente il numero di istanze di destinazione a causa delle istanze deallocate e rimosse. 


**D:** Quali canali supportano le macchine virtuali Spot?

**A:** Vedere la tabella seguente per la disponibilità di VM Spot.See the table below for Spot VM availability.

<a name="channel"></a>

| Canali di AzureAzure Channels               | Azure Spot VMs Availability       |
|------------------------------|-----------------------------------|
| Enterprise Agreement         | Sì                               |
| Pagamento in base al consumo                | Sì                               |
| Provider di servizi cloud | [Contatta il tuo partner](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Vantaggi                     | Non disponibile                     |
| Sponsorizzato                    | Non disponibile                     |
| Versione di valutazione gratuita                   | Non disponibile                     |


**D:** Dove posso pubblicare le domande?

**A:** Puoi pubblicare e taggare la tua domanda su `azure-spot` Q&[A](https://docs.microsoft.com/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui prezzi, vedere la [pagina dei prezzi dei set di scalabilità di macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/).

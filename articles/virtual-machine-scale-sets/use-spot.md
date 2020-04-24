---
title: Creare un set di scalabilità che usa VM di Azure spot
description: Informazioni su come creare set di scalabilità di macchine virtuali di Azure che usano macchine virtuali spot per risparmiare sui costi.
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
# <a name="azure-spot-vms-for-virtual-machine-scale-sets"></a>VM di Azure spot per i set di scalabilità di macchine virtuali 

L'uso di Azure spot nei set di scalabilità consente di sfruttare i vantaggi della capacità inutilizzata con un notevole risparmio sui costi. In qualsiasi momento, quando Azure necessita della capacità, l'infrastruttura di Azure eliminerà le istanze di spot. Le istanze di spot sono quindi ottime per i carichi di lavoro in grado di gestire le interruzioni, ad esempio processi di elaborazione batch, ambienti di sviluppo/test, carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

La quantità di capacità disponibile può variare in base alle dimensioni, all'area, all'ora del giorno e così via. Quando si distribuiscono istanze di spot nei set di scalabilità, Azure lo alloca solo se è disponibile una capacità, ma non esiste alcun contratto di contratto per queste istanze. Un set di scalabilità spot viene distribuito in un singolo dominio di errore e non offre garanzie di disponibilità elevata.


## <a name="pricing"></a>Prezzi

I prezzi per le istanze di spot sono variabili in base all'area e allo SKU. Per altre informazioni, vedere prezzi per [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


Con i prezzi variabili è possibile impostare un prezzo massimo, in dollari statunitensi (USD), usando un massimo di 5 cifre decimali. Ad esempio, il valore `0.98765`è un prezzo massimo di $0,98765 USD all'ora. Se si imposta il prezzo massimo `-1`, l'istanza non verrà rimossa in base al prezzo. Il prezzo per l'istanza sarà il prezzo corrente per spot o il prezzo di un'istanza standard, che sempre è inferiore, purché siano disponibili capacità e quota.

## <a name="eviction-policy"></a>Criteri di rimozione

Quando si creano set di scalabilità spot, è possibile impostare i criteri di rimozione per *deallocare* (impostazione predefinita) o *eliminare*. 

Il criterio *deallocate* sposta le istanze eliminate nello stato arrestato-deallocato, consentendo di ridistribuire le istanze eliminate. Tuttavia, non è garantito che l'allocazione avrà esito positivo. Le macchine virtuali deallocate verranno incluse nel conteggio per la quota delle istanze del set di scalabilità e verranno addebitati i costi dei dischi sottostanti. 

Se si desidera che le istanze nel set di scalabilità spot vengano eliminate al momento della rimozione, è possibile impostare i criteri di rimozione da *eliminare*. Con i criteri di rimozione impostati per l'eliminazione è quindi possibile creare nuove macchine virtuali aumentando il valore della proprietà del numero di istanze di set di scalabilità. Le macchine virtuali rimosse vengono eliminate insieme ai relativi dischi sottostanti, quindi non verrà addebitato alcun costo per l'archiviazione. Si può anche usare la funzionalità di ridimensionamento automatico dei set di scalabilità per cercare di compensare automaticamente le macchine virtuali rimosse, tuttavia non esiste alcuna garanzia di successo dell'allocazione. Si consiglia di usare la funzionalità di scalabilità automatica solo nei set di scalabilità di punti quando si impostano i criteri di rimozione da eliminare per evitare il costo dei dischi e raggiungere i limiti di quota. 

Gli utenti possono acconsentire esplicitamente a ricevere notifiche in-VM tramite [Azure eventi pianificati](../virtual-machines/linux/scheduled-events.md). In questo modo si riceverà una notifica se le macchine virtuali vengono eliminate e saranno disponibili 30 secondi per completare i processi ed eseguire le attività di arresto prima dell'eliminazione. 


## <a name="deploying-spot-vms-in-scale-sets"></a>Distribuzione di VM spot nei set di scalabilità

Per distribuire le VM spot nei set di scalabilità, è possibile impostare il nuovo flag di *priorità* su *spot*. Tutte le macchine virtuali nel set di scalabilità verranno impostate su spot. Per creare un set di scalabilità con macchine virtuali spot, usare uno dei metodi seguenti:
- [Azure portal](#portal)
- [Interfaccia della riga di comando di Azure](#azure-cli)
- [Azure PowerShell](#powershell)
- [Modelli di Gestione risorse di Azure](#resource-manager-templates)

## <a name="portal"></a>Portale

Il processo per creare un set di scalabilità che usa le VM spot è identico a quello descritto nell' [articolo introduttivo](quick-create-portal.md). Quando si distribuisce un set di scalabilità, è possibile scegliere di impostare il flag spot e i criteri di ![rimozione: creare un set di scalabilità con le VM spot](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Il processo di creazione di un set di scalabilità con macchine virtuali spot è identico a quello descritto nell' [articolo introduttivo](quick-create-cli.md). È sufficiente aggiungere "--Priority spot" e aggiungere `--max-price`. In questo esempio viene usato `-1` per `--max-price` , quindi l'istanza non verrà rimossa in base al prezzo.

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
È sufficiente aggiungere "-Priority spot" e fornire un `-max-price` oggetto a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

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

Per le distribuzioni di modelli di`"apiVersion": "2019-03-01"` spot, usare o versione successiva. Aggiungere le `priority`proprietà `evictionPolicy` , `billingProfile` e alla `"virtualMachineProfile":` sezione nel modello: 

```json
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
```

Per eliminare l'istanza dopo che è stata rimossa, impostare il `evictionPolicy` parametro su `Delete`.

## <a name="faq"></a>Domande frequenti

**D:** Una volta creato, è un'istanza di spot uguale all'istanza standard?

**R:** Sì, ad eccezione del fatto che non esiste alcun contratto di contratto per le macchine virtuali spot e che possono essere rimossi in qualsiasi momento.


**D:** Cosa fare quando si viene eliminati, ma è ancora necessaria una capacità?

**R:** È consigliabile usare VM standard anziché macchine virtuali spot se è necessaria immediatamente la capacità.


**D:** Come viene gestita la quota per spot?

**R:** Le istanze di spot e le istanze standard avranno pool di quote distinti. La quota spot verrà condivisa tra le macchine virtuali e le istanze del set di scalabilità. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**D:** È possibile richiedere una quota aggiuntiva per spot?

**R:** Sì, sarà possibile inviare la richiesta per aumentare la quota per le VM spot tramite il [processo standard di richiesta di quota](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).


**D:** È possibile convertire I set di scalabilità esistenti in set di scalabilità spot?

**R:** No, l'impostazione `Spot` del flag è supportata solo in fase di creazione.


**D:** Se si usa per `low` i set di scalabilità con priorità bassa, è necessario iniziare a `Spot` usare invece?

**R:** Per il momento, `low` sia `Spot` che funzioneranno, ma è necessario iniziare a eseguire la `Spot`transizione a usando.


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
| Enterprise Agreement         | Sì                               |
| Pagamento in base al consumo                | Sì                               |
| Provider di servizi cloud | [Contatta il tuo partner](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Vantaggi                     | Non disponibile                     |
| Sponsorizzato                    | Non disponibile                     |
| Versione di valutazione gratuita                   | Non disponibile                     |


**D:** Dove è possibile pubblicare domande?

**R:** È possibile pubblicare e contrassegnare la domanda `azure-spot` con [Q&a](https://docs.microsoft.com/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui prezzi, vedere la [pagina dei prezzi dei set di scalabilità di macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/).
